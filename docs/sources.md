# 작성 기준과 출처

[메인 소개](../profile/README.md)

문서는 **2026-10-05**에 확인한 아래 소스와 팀의 최종 발표를 기준으로 작성했습니다. 발표의 기획 의도·역할 분담과 코드의 구현 범위를 대조했고, 운영 AWS 계정이나 모든 배포 경로의 동작을 직접 검사하지는 않았습니다.

## 기준 소스

| 자료 | 고정 기준 |
|---|---|
| [최종 발표](https://app.notion.com/p/3ed8bee9ada4809eb9bcc5fdd1f07082) | 기획 의도, PieckPick의 제안, 역할, 플랫폼 설계, Organizations 구성과 확장 목표 |
| [Frontend](https://github.com/softbank-hackathon-2026/Freesia-Frontend/tree/28cd1d78ff56da033231768ad06405bc23dfbbfd) | `28cd1d78ff56da033231768ad06405bc23dfbbfd` |
| [Backend](https://github.com/softbank-hackathon-2026/Freesia-backend/tree/880e7da78f2fcb9100f3bfc354eceaa990c476b1) | `880e7da78f2fcb9100f3bfc354eceaa990c476b1` |
| [플랫폼 IaC](https://github.com/softbank-hackathon-2026/platform-terraform/tree/b46bc8ba0031778eefff0c65eed3424a36d24676) | `b46bc8ba0031778eefff0c65eed3424a36d24676` |
| [워크로드 기반 IaC](https://github.com/softbank-hackathon-2026/workload-terraform/tree/3c828c5aa6a5c8fb82fbd4724dc50304e6d0ba6e) | `3c828c5aa6a5c8fb82fbd4724dc50304e6d0ba6e` |
| [앱 실행 워크플로](https://github.com/softbank-hackathon-2026/workload-deploy/tree/4852093f2f7e76a8014bef96d0acbc0c7384b39f) | `4852093f2f7e76a8014bef96d0acbc0c7384b39f` |

기능별 근거 파일과줄 번호는 각 상세 문서에 연결했습니다. GitHub `main`은 이후 변경될 수 있으므로 기술 설명의 기준은 위 커밋입니다.

## 이미지와 다이어그램

| 자산 | 제작·재사용 기준 |
|---|---|
| [배너](../assets/banner.png) | README 배너의 예시 디자인과 기존 PieckPick 마스코트를 참고해 **OpenAI imagegen**으로 새로 제작. |
| [기술 스택](../assets/technology-stack.svg) | 실제 사용한 기술만 분류해 제작한 SVG. AWS 로고는 [AWS Labs 아이콘](https://github.com/awslabs/aws-icons-for-plantuml/tree/e26e2c05daf8b6bc4c764669fc2be04c314ccb8c), 개발 도구 로고는 [Simple Icons](https://github.com/simple-icons/simple-icons/tree/98820a4dc8c363ca72fa2c0d294ea4a0a9bba75d)와 archify의 Simple Icons 16.28.0 벡터 사용 |
| [전체 시스템 개요](../assets/architecture/system.svg) · [인프라 개요](../assets/architecture/infrastructure.svg) | 발표 내용과 고정 코드 경로를 대조해 제작 |
| [상세 시스템](../assets/architecture/system-detail.svg) · [모니터링](../assets/architecture/monitoring.svg) | 팀의 기존 archify HTML 다이어그램에서 내보낸 SVG 재사용. 기존 상세 그림의 기준 커밋은 2026-10-04 소스이며, 최신 설명은 본 저장소의 상세 Markdown 문서를 따름 |
| 프론트엔드 캡처 | 현행 코드의 자동 브라우저 검사에서 만든 화면. 캡처별 데이터 종류와 검증 범위는 [화면 흐름](frontend-flow.md)에 표시 |
| 팀원 사진 | 각 팀원의 GitHub 프로필 이미지. 역할은 최종 발표, 계정과 이름은 프로필 및 팀 확인 기준 |

AWS 서비스·각 기술의 이름과 로고는 해당 권리자의 식별 목적으로 사용합니다.

## 상세 HTML 보기

[시스템 HTML](../assets/architecture/system.html) · [모니터링 HTML](../assets/architecture/monitoring.html)

GitHub는 HTML 파일을 실행하지 않고 소스로 보여줍니다. 저장소를 내려받아 파일을 브라우저로 열면 기존 다이어그램의 확대·검색·노드 설명 기능을 사용할 수 있습니다.

## 배너 생성 지시

- 형식: 가로로 긴 README 배너, 어두운 navy 배경과 cyan·mint 네트워크.
- 표시 문구: `TEAM FREESIA · PIECKPICK`, `SoftBank`, `Hackathon 2026 in Korea`, `예선에서 진행한 프로젝트입니다.`
- 오른쪽: 기존 PieckPick 마스코트의 집·장갑·주황 지붕·노란 Freesia 꽃·노란 반바지 특징을 유지.
- 사용 도구: 기본 제공 `imagegen`. 생성 결과를 본 저장소의 배너로 복사해 사용.
