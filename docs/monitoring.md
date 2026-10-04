# 모니터링 · 배포된 앱의 지표와 로그

[← 프로젝트 소개](../profile/README.md)

PieckPick은 배포된 고객 앱의 Amazon CloudWatch 지표·실행 로그를 애플리케이션 상세 화면에 모읍니다. 배포 과정의 진행 이벤트와, 배포 후 서비스 지표·로그는 서로 다른 데이터 흐름입니다.

![PieckPick 모니터링 아키텍처](../assets/architecture/monitoring.svg)

[모니터링 구조 상세 보기](../assets/architecture/monitoring.html)

## 서비스 지표와 로그

로그·모니터링 탭을 열면 즉시 요청하고, 요청이 끝난 뒤 **약 15초 후 다시 조회**합니다. Backend도 결과를 **15초 캐시**합니다. 지표는 최근 10분 범위에서 가장 최근의 1분 집계값을 고릅니다. 로그는 최대 3개 스트림에서 최근 7일의 로그를 모아 요청한 줄 수만큼 시간순으로 보여줍니다. 로그 화면에는 일시정지·재개 기능이 있습니다. [지표 화면](https://github.com/softbank-hackathon-2026/Freesia-Frontend/blob/28cd1d78ff56da033231768ad06405bc23dfbbfd/src/components/ApplicationMetrics.tsx#L44), [로그 화면](https://github.com/softbank-hackathon-2026/Freesia-Frontend/blob/28cd1d78ff56da033231768ad06405bc23dfbbfd/src/components/ApplicationLogs.tsx#L72), [조회·캐시 구현](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/monitoring.py).

| 실행 환경 | 현재 조회하는 지표 | 실행 로그 |
|---|---|---|
| ECS Fargate | CPU·메모리 사용률, 앱 전용 ALB를 찾을 수 있을 때 응답 시간·요청 수·target 5xx 수 | CloudWatch Logs |
| Lambda | 평균 처리 시간·호출 수·오류 수 | CloudWatch Logs |
| EC2 | CPU 사용률 | Docker `awslogs` 로그, 현재 deployment의 스트림 |
| 온프레미스 소스·컨테이너 | 현재 미지원 | 현재 미지원 |

EC2 메모리 지표는 현재 수집하지 않습니다. Fargate의 ALB 지표는 앱 전용 ALB 이름을 조회하는 구현이므로, 공용 ALB의 앱별 요청·오류·응답 시간을 제공한다고 보장하지 않습니다. 존재하지 않는 지표는 `null`로 반환합니다. [컴퓨팅별 질의](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/monitoring.py#L144), [지원 범위](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/routers/monitoring.py#L14).

## 0과 측정값 없음 구분

숫자 `0`은 수집된 측정값입니다. 지표가 없거나 해당 환경에서 수집하지 않는 항목은 `null`이며, 화면에는 `—` 또는 측정값 없음으로 표시합니다. Backend와 화면은 다음 상태를 구분합니다.

| 상태 | 의미 |
|---|---|
| `ok` | 조회한 지표 또는 로그가 있음 |
| `waiting` | 아직 지표가 수집되지 않았거나 최근 로그가 없음 |
| `not_deployed` | 모니터링 대상으로 판단할 실제 성공 배포가 없음·내리기 요청 후 대상에서 제외됨 |
| `unsupported` | 해당 컴퓨팅의 모니터링 미지원 |
| `error` | 자격증명 설정 또는 AWS 조회 오류 |

Lambda는 요청이 들어와야 지표가 생성됩니다. Backend는 성공 상태와 실제 workflow `run_id`가 있는 최신 배포를 대상으로 삼고, 이후 내리기가 요청되거나 완료된 앱은 제외합니다. 이 기준은 DB 기록에 따른 판단이며 매 조회마다 고객 앱의 health를 검사하는 것은 아닙니다. [배포 대상 판정](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/monitoring.py#L81), [API 상태 처리](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/routers/monitoring.py#L36), [화면의 null 처리](https://github.com/softbank-hackathon-2026/Freesia-Frontend/blob/28cd1d78ff56da033231768ad06405bc23dfbbfd/src/components/ApplicationMetrics.tsx#L86).

## 배포 진행 이벤트는 SSE

GitHub Actions의 단계·자원별 callback을 Backend DB에 기록하고 `/api/deployments/{id}/events`에서 Server-Sent Events(SSE)로 전달합니다. 재연결은 `Last-Event-ID` 이후의 이벤트를 이어서 받고, 이벤트가 없는 동안에는 15초 간격의 연결 유지 신호를 보냅니다. 이 SSE는 배포 진행 표시용이며, CloudWatch 서비스 로그를 실시간으로 스트리밍하는 경로는 아닙니다. [Backend SSE](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/routers/deployments.py#L65), [Frontend EventSource](https://github.com/softbank-hackathon-2026/Freesia-Frontend/blob/28cd1d78ff56da033231768ad06405bc23dfbbfd/src/lib/api.ts#L240).

Callback URL 제한, HMAC 서명 검증, 끝난 배포·이전 단계의 보고 거절로 상태 기록을 보호합니다. 자세한 실행 흐름과 callback 유실의 의미는 [CI/CD 문서](cicd.md)에서 설명합니다.

## 자격증명과 로그 전송 역할

Backend는 Infra Space의 계정 정보에 따라 Workload·Sandbox 계정별 읽기 키로 CloudWatch를 조회합니다. Bedrock 호출에 사용하는 기본 자격증명·ECS 작업 역할과 구분합니다. AWS 자원을 생성하는 권한은 고객 앱 배포 workflow가 사용합니다. [계정별 client](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/aws.py), [설정 분리](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/config.py#L36).

고객 앱의 로그 전송은 실행 환경별 역할을 사용합니다. ECS는 Task Execution Role과 `awslogs`, Lambda는 함수 실행 역할, EC2는 인스턴스 역할과 Docker `awslogs`를 사용합니다. 템플릿의 로그 그룹 보관 기간은 7일입니다. [ECS 템플릿](https://github.com/softbank-hackathon-2026/workload-deploy/blob/4852093f2f7e76a8014bef96d0acbc0c7384b39f/templates/ecs-fargate/basic/main.tf), [Lambda 템플릿](https://github.com/softbank-hackathon-2026/workload-deploy/blob/4852093f2f7e76a8014bef96d0acbc0c7384b39f/templates/lambda/basic/main.tf), [EC2 템플릿](https://github.com/softbank-hackathon-2026/workload-deploy/blob/4852093f2f7e76a8014bef96d0acbc0c7384b39f/templates/ec2/basic/main.tf).

## 확인 범위

현재 기능은 앱 지표·로그 조회입니다. 알람 생성·알림 전송, 온프레미스 지표 수집, TGW 연결 전환은 완료된 기능으로 표시하지 않습니다. 소스 검증은 실제 CloudWatch 데이터의 수집 여부나 현재 AWS 운영 상태를 확인한 결과와 구분합니다. [모니터링 API 범위](https://github.com/softbank-hackathon-2026/Freesia-backend/blob/880e7da78f2fcb9100f3bfc354eceaa990c476b1/app/routers/monitoring.py#L1).
