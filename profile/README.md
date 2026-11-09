<!-- GitHub organization profiles resolve relative links against the organization URL. Keep repository files and images absolute. -->
<p align="center">
  <img src="https://raw.githubusercontent.com/softbank-hackathon-2026/.github/main/assets/banner.png" alt="Team Freesia · PieckPick — SoftBank Hackathon 2026 in Korea 예선 프로젝트" width="100%">
</p>

<h1 align="center">PieckPick</h1>
<p align="center"><strong>인프라 담당자는 환경을 준비하고, 개발자는 앱 배포에 집중합니다.</strong></p>
<p align="center">저장소와 배포 환경을 선택하면 AI가 실행 구성을 추천하고,<br>배포 과정과 결과를 한곳에서 확인하는 배포 관리 플랫폼입니다.</p>

<p align="center">
  <a href="https://sbh.howon.me/">서비스 콘솔</a> ·
  <a href="https://app.notion.com/p/3ed8bee9ada4809eb9bcc5fdd1f07082">최종 발표</a> ·
  <a href="#purpose">기획 의도</a> ·
  <a href="#features">PieckPick의 제안</a> ·
  <a href="#architecture">아키텍처</a> ·
  <a href="#team">팀 구성원</a>
</p>

> **SoftBank Hackathon 2026 in Korea · 예선 · Team Freesia**<br>
> 서비스 이름은 **PieckPick**, 소스 저장소에는 기존 **Freesia** 이름을 유지합니다.

<a name="purpose"></a>

## 기획 의도

앱 하나를 배포하는 데에도 개발자와 인프라 담당자는 같은 질문을 반복합니다.

> “어느 서브넷에 넣을까요?” “포트는요?” “CPU와 메모리는 얼마나 필요하죠?”

개발자는 인프라 설정을 파악해야 하고, 인프라 담당자는 앱마다 환경과 실행 조건을 확인합니다. PieckPick은 이 커뮤니케이션 병목을 **준비된 환경 선택 → AI 추천 → 설정 검토 → 배포 → 관찰**의 흐름으로 줄이고자 했습니다.

| 기존 배포 과정의 어려움 | PieckPick의 접근 |
|---|---|
| 앱마다 네트워크와 실행 환경을 다시 설명해야 함 | 기반 환경을 **Infra Space**로 제공하고 서비스명으로 선택 |
| 코드에 맞는 실행 방식과 설정을 직접 판단해야 함 | 저장소를 분석해 **실행 후보와 배포 설정**을 추천 |
| 배포 상태와 자원, 로그를 여러 도구에서 확인해야 함 | **애플리케이션 단위로 진행·구성·로그·지표**를 모아 확인 |

<a name="features"></a>

## 서비스 기능 · PieckPick의 제안

**환경을 준비하는 일과 앱을 배포하는 일을 분리합니다.** 인프라 담당자는 공통 환경을 준비하고, 개발자는 등록한 저장소를 해당 환경에 연결합니다.

| 기능 | 사용자가 하는 일 | PieckPick이 제공하는 것 |
|---|---|---|
| **서비스별 환경 선택** | 준비된 Infra Space 선택 | 환경 특성과 연결된 앱을 같은 기준으로 확인 |
| **저장소 연결과 AI 추천** | 공개 GitHub 저장소와 앱 연결 | 코드 분석, 실행 환경 후보와 근거, 설정값 추천 |
| **배포 구성 검토** | 실행 방식과 설정값 확인 | 선택한 템플릿에 들어갈 구성을 배포 전에 검토 |
| **AWS·온프레미스 배포** | 선택한 환경에 배포 요청 | GitHub Actions를 통해 AWS는 Terraform, 온프레미스는 Ansible 실행 |
| **배포 결과 관찰** | 진행·전체 구성·로그·지표 확인 | SSE 진행 상태, 보고된 자원 트리, 지원되는 AWS 앱의 CloudWatch 데이터 |

**기본 사용 흐름**

1. **통합**에서 공개 GitHub 저장소를 등록합니다.
2. **애플리케이션**에 저장소와 준비된 Infra Space를 연결합니다.
3. **AI 분석 → 실행 환경 선택 → 구성안 검토**를 진행합니다.
4. **배포 진행 → 전체 구성**에서 결과를 확인합니다.
5. **로그·모니터링** 탭에서 실행 이후의 데이터를 조회합니다.

[화면과 사용 흐름 보기](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/frontend-flow.md) · [AI 추천 과정 보기](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/ai-recommendation.md)

<a name="architecture"></a>

## 전체 시스템 아키텍처

<img src="https://raw.githubusercontent.com/softbank-hackathon-2026/.github/main/assets/architecture/system.svg" alt="React 관리 콘솔 → FastAPI 제어 서버 → GitHub Actions → AWS·온프레미스 고객 앱. Bedrock 추천, PostgreSQL 상태 저장, CloudWatch 조회 연결" width="100%">

| 구성 요소 | 책임 |
|---|---|
| **관리 콘솔 · React** | 환경·저장소·앱 선택, 구성 검토, 진행과 결과 표시 |
| **제어 서버 · FastAPI** | 입력 검증, 분석과 구성안 관리, 워크플로 실행 요청, 상태 저장·조회 |
| **AI · Amazon Bedrock** | 저장소와 제공된 실행 후보를 바탕으로 구성 추천 |
| **실행기 · GitHub Actions** | 소스 빌드와 템플릿 기반 배포, 결과 콜백 |
| **관찰 · CloudWatch** | 지원되는 AWS 고객 앱의 지표·로그를 백엔드가 읽어 콘솔에 전달 |

프론트엔드는 배포를 요청하고 결과를 표시합니다. 실제 자원 구성과 앱 실행은 워크플로가 담당하며, **AI의 추천과 배포 실행을 구분**합니다. AI 분석 결과는 기존 템플릿과 설정값에 연결됩니다.

[상세 시스템 구조도](https://github.com/softbank-hackathon-2026/.github/blob/main/assets/architecture/system-detail.svg) · [CI/CD 실행 흐름](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/cicd.md) · [모니터링 구성](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/monitoring.md)

## 인프라 아키텍처

<img src="https://raw.githubusercontent.com/softbank-hackathon-2026/.github/main/assets/architecture/infrastructure.png" alt="Notion 최종 발표의 인프라 아키텍처 원본: Cloudflare·CloudFront, Private VPC의 Internal ALB·ECS Fargate·RDS, Regional NAT와 온프레미스 연결" width="100%">

*[최종 발표](https://app.notion.com/p/3ed8bee9ada4809eb9bcc5fdd1f07082)의 인프라 아키텍처 원본 이미지입니다.*

**플랫폼 자체의 진입점은 CloudFront로 모으고, 내부 자원은 Private 영역에 둡니다.**

- **화면:** CloudFront → OAC → 비공개 S3.
- **API:** CloudFront → VPC Origin → internal ALB → ECS Fargate.
- **데이터:** ECS Fargate → Private RDS PostgreSQL.
- **외부 조회:** 플랫폼의 Regional NAT를 통한 GitHub·Bedrock·Cloudflare 연결.
- **하이브리드:** 백엔드의 Proxmox VM 조회와 GitHub Actions의 온프레미스 앱 배포 경로를 분리.

플랫폼을 운영하는 계정과 고객 앱을 실행하는 계정을 구분합니다. **플랫폼의 Private 구조와 고객 앱 템플릿의 네트워크 구조는 각각 다릅니다.** 고객 앱은 선택한 실행 환경에 따라 ECS Fargate·EC2·Lambda 또는 온프레미스 VM·컨테이너로 배포하는 코드 경로를 갖습니다.

[인프라 구성과 네트워크 상세](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/infrastructure.md) · [AWS Organizations와 계정 역할](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/aws-organizations.md)

## 기술 스택

<img src="https://raw.githubusercontent.com/softbank-hackathon-2026/.github/main/assets/technology-stack.svg?v=20261005-slack" alt="AWS 인프라, React·TypeScript·Vite·Python·FastAPI·PostgreSQL, GitHub Actions·Docker·Terraform·Ansible, Cloudflare·Proxmox, CloudWatch·pytest, GitHub·Notion·Slack을 그룹별로 표시" width="100%">

| 영역 | 주요 기술 |
|---|---|
| **Frontend / Backend** | React, TypeScript, Vite / Python, FastAPI, SQLAlchemy, PostgreSQL |
| **AWS 플랫폼과 앱 실행** | CloudFront, S3, ALB, ECS Fargate, ECR, RDS, EC2, Lambda, IAM, ACM, Parameter Store, Secrets Manager |
| **AI / 관찰** | Amazon Bedrock / Amazon CloudWatch |
| **자동화 / 하이브리드** | GitHub Actions, Docker, Terraform, Ansible, Cloudflare Tunnel·Access, Proxmox |
| **테스트 / 협업** | pytest, Playwright / GitHub, Notion, Slack |

<a name="team"></a>

## 팀 구성원

**Team Freesia · 6명**

<table>
  <thead>
    <tr>
      <th align="center">김동윤</th>
      <th align="center">박태원</th>
      <th align="center">강효승</th>
      <th align="center">정호원</th>
      <th align="center">박준서</th>
      <th align="center">박소정</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center"><strong>Frontend<br>Monitoring</strong></td>
      <td align="center"><strong>Backend</strong></td>
      <td align="center"><strong>AI</strong></td>
      <td align="center"><strong>Infra</strong></td>
      <td align="center"><strong>Infra</strong></td>
      <td align="center"><strong>DevOps<br>CI/CD</strong></td>
    </tr>
    <tr>
      <td align="center"><a href="https://github.com/BusanGukbap"><img src="https://avatars.githubusercontent.com/u/77170214?v=4" alt="김동윤 GitHub 프로필" width="112" height="112"></a></td>
      <td align="center"><a href="https://github.com/BokJaeSung"><img src="https://avatars.githubusercontent.com/u/87299396?v=4" alt="박태원 GitHub 프로필" width="112" height="112"></a></td>
      <td align="center"><a href="https://github.com/Recklehs"><img src="https://avatars.githubusercontent.com/u/127021195?v=4" alt="강효승 GitHub 프로필" width="112" height="112"></a></td>
      <td align="center"><a href="https://github.com/ONE0x393"><img src="https://avatars.githubusercontent.com/u/76539118?v=4" alt="정호원 GitHub 프로필" width="112" height="112"></a></td>
      <td align="center"><a href="https://github.com/Junseo-tech"><img src="https://avatars.githubusercontent.com/u/91211740?v=4" alt="박준서 GitHub 프로필" width="112" height="112"></a></td>
      <td align="center"><a href="https://github.com/sojjeoi"><img src="https://avatars.githubusercontent.com/u/276283504?v=4" alt="박소정 GitHub 프로필" width="112" height="112"></a></td>
    </tr>
    <tr>
      <td align="center"><a href="https://github.com/BusanGukbap">@BusanGukbap</a></td>
      <td align="center"><a href="https://github.com/BokJaeSung">@BokJaeSung</a></td>
      <td align="center"><a href="https://github.com/Recklehs">@Recklehs</a></td>
      <td align="center"><a href="https://github.com/ONE0x393">@ONE0x393</a></td>
      <td align="center"><a href="https://github.com/Junseo-tech">@Junseo-tech</a></td>
      <td align="center"><a href="https://github.com/sojjeoi">@sojjeoi</a></td>
    </tr>
  </tbody>
</table>

역할은 [최종 발표 자료](https://app.notion.com/p/3ed8bee9ada4809eb9bcc5fdd1f07082)의 분담을 따릅니다.

## 소스 저장소

| 저장소 | 포함하는 내용 |
|---|---|
| [Freesia-Frontend](https://github.com/softbank-hackathon-2026/Freesia-Frontend) | 관리 콘솔, API 연동, 화면 검증 |
| [Freesia-backend](https://github.com/softbank-hackathon-2026/Freesia-backend) | 앱·인프라·분석·배포·모니터링 API |
| [platform-terraform](https://github.com/softbank-hackathon-2026/platform-terraform) | 플랫폼의 AWS 기반 인프라 |
| [workload-terraform](https://github.com/softbank-hackathon-2026/workload-terraform) | 고객 앱을 위한 기반 환경 |
| [workload-deploy](https://github.com/softbank-hackathon-2026/workload-deploy) | AWS·온프레미스 앱 실행·정리 워크플로와 템플릿 |

샘플 앱: [sample-shop](https://github.com/softbank-hackathon-2026/sample-shop) · [shop-api](https://github.com/softbank-hackathon-2026/shop-api)

## 상세 문서

| 문서 | 확인할 내용 |
|---|---|
| [AWS Organizations](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/aws-organizations.md) · [인프라 상세](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/infrastructure.md) | 계정·OU·권한 경계, 플랫폼과 워크로드의 네트워크 |
| [CI/CD](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/cicd.md) | 콘솔·백엔드 자체 배포와 고객 앱 배포의 차이 |
| [프론트 화면 흐름](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/frontend-flow.md) | 저장소 등록부터 배포·전체 구성·관찰까지 |
| [AI 추천 과정](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/ai-recommendation.md) | 분석 입력, 실행 후보, 설정 추천과 검토 |
| [모니터링](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/monitoring.md) | 실행 환경별 지표·로그, 조회 방식과 지원 상태 |

## 현재 범위와 다음 목표

| 구분 | 내용 |
|---|---|
| **코드에서 확인한 범위** | 저장소 등록, 앱 분석·구성 검토, 워크플로 실행·콜백, 진행·자원 표시, 지원되는 AWS 앱의 로그·지표 조회 |
| **환경 확인이 필요한 범위** | 실행 환경별 실제 배포와 앱 접속, 공유 ALB의 앱별 지표, 온프레미스 SSH·배포 연결 |
| **확장 목표** | 기반 인프라 변경에 따른 AI 재추천, Cloudflare Tunnel 연결을 TGW 중심의 통합 네트워크로 발전 |

인프라 생성 화면의 AI 대화·Terraform Apply는 데모 흐름입니다. 현재 문서는 **2026-10-05에 확인한 고정 GitHub 소스와 최종 발표**를 바탕으로 작성했으며, 운영 중인 모든 경로의 성공을 보장하는 자료는 아닙니다. 상세 구현 범위와 각 문서의 코드 근거를 함께 확인해 주세요.

[확인한 소스와 이미지 출처](https://github.com/softbank-hackathon-2026/.github/blob/main/docs/sources.md)
