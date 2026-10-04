# Contributing to ORBIT

ORBIT Organization의 Repository는 공개되어 있지만 현재 외부 Contribution은 받지 않습니다. 모든 변경은 ORBIT 팀원이 Issue와 Pull Request를 통해 진행합니다.

## 작업 Repository

작업 내용에 맞는 Repository에서 Issue를 생성합니다.

|Repository|관리 범위|
|---|---|
|`.github`|Organization 프로필과 공통 Community Health 파일|
|`orbit`|프로젝트 문서, 아키텍처, ADR, Runbook, 시험 결과|
|`orbit-server`|OTA Campaign, 차량 상태, SSE 및 Backend API|
|`orbit-gitops`|Kubernetes 배포 선언과 Desired State|
|`orbit-infrastructure`|Terraform, Ansible, 서버, 네트워크 및 Kubernetes 인프라|
|`orbit-vehicle-agent`|가상 차량의 OTA 다운로드, 설치 및 상태 보고|
|`orbit-observability`|Metrics, Logs, Traces 수집·저장 및 Monitoring 구성|
|`orbit-visualization`|OTA Campaign과 차량 상태 Dashboard|

여러 Repository를 변경하는 작업은 Repository별 Issue와 Pull Request로 분리합니다.

## 작업 흐름

```text
Issue → Branch → Commit → Push → Pull Request → Review → Merge
```

### 1. Issue

- 모든 작업은 Issue를 먼저 생성한 후 시작합니다.
- Issue 하나에는 하나의 명확한 결과만 포함합니다.
- Issue Type은 `Feature`, `Bug`, `Task` 중 하나를 선택합니다.
- Repository별 `area:*` Label을 기본으로 하나 선택합니다.
- 필요한 경우에만 `work:*`, `needs:decision`, `cross-repo`, `risk:security` Label을 추가합니다.

### 2. Branch

Issue 번호가 확정된 후 Branch를 생성합니다.

```text
<type>/<issue-number>-<short-description>
```

```text
feature/12-add-campaign-api
bug/23-fix-manifest-validation
task/31-add-issue-forms
```

- Branch type은 Issue Type에 맞춰 `feature`, `bug`, `task`를 사용합니다.
- 설명은 영문 소문자와 하이픈으로 작성합니다.
- `main`에 직접 Commit하거나 Push하지 않습니다.
- 하나의 Branch에 관련 없는 Issue 작업을 포함하지 않습니다.

### 3. Commit

```text
<type>: <summary>
```

사용 가능한 type은 다음과 같습니다.

- `feat`: 새로운 기능이나 동작
- `fix`: 잘못된 동작 수정
- `docs`: 문서 변경
- `refactor`: 외부 동작 변경 없는 구조 개선
- `chore`: 설정, 의존성 및 도구 유지보수
- `test`: 테스트 추가 또는 수정
- `ci`: CI/CD Workflow 변경

하나의 Commit에는 하나의 논리적 변경만 포함합니다. 파일 단위로 불필요하게 나누거나 관련 없는 변경을 함께 포함하지 않습니다.

### 4. Push

작업 Branch만 원격 Repository에 Push합니다.

```bash
git push -u origin <branch-name>
```

최초 Push 이후에는 같은 Branch에 `git push`를 사용합니다.

### 5. Pull Request

Issue 하나당 Pull Request 하나를 생성합니다.

```text
[<Issue Type>/#<issue-number>] <summary>
```

```text
[Task/#31] 공통 Issue Form 추가
[Feature/#41] Campaign 생성 API 구현
[Bug/#52] Package checksum 검증 오류 수정
```

Repository에 등록된 Pull Request Template을 사용하고 다음 항목을 작성합니다.

- `Related Issue`: `Closes #<issue-number>`
- `Summary`: 무엇을 왜 변경했는지 작성합니다.
- `Verification`: 실제로 수행한 검증과 결과를 작성합니다.

실행하지 않은 검증을 완료한 것으로 표시하지 않습니다. Secret, Credential 또는 개인정보를 Commit과 Pull Request에 포함하지 않습니다.

## Review와 Merge

- Pull Request는 최소 한 명의 Approve를 받아야 합니다.
- Review 중 발생한 Conversation을 모두 해결해야 합니다.
- Approve 이후 Commit이 추가되면 변경 내용을 다시 Review합니다.
- Merge 방식은 각 Repository에 설정된 전략을 따릅니다.
- Pull Request가 Merge되면 `Closes`로 연결된 Issue가 종료됩니다.
