# Issue 의존성(Blocked by) 사용 안내

여러 Repository에 걸친 작업의 선후 관계를 GitHub의 Issue 의존성 기능으로 연결합니다. 이 문서는 기능이 무엇을 하고, 팀에서 어떻게 사용하는지 정리합니다.

## 1. 기능 개요

"이 Issue는 저 Issue가 끝나야 진행할 수 있다"는 관계를 Issue에 기록하는 기능입니다.

|관계|의미|예시|
|---|---|---|
|**Blocked by**|이 Issue보다 먼저 끝나야 하는 Issue|`orbit-gitops#9`는 `orbit-infrastructure#43`(NFS 구축)에 Blocked by|
|**Blocking**|이 Issue가 막고 있는 Issue|`orbit-infrastructure#43`은 `orbit-gitops#9`를 Blocking|

- 한쪽에 연결하면 반대쪽에도 자동으로 표시됩니다.
- 다른 Repository의 Issue와도 연결할 수 있습니다.

## 2. 확인 위치

- **Issue 화면**: 오른쪽 사이드바 **Relationships**에 Blocked by / Blocking 목록이 표시됩니다.
- **Issue 목록·Project 보드**: 선행 Issue가 아직 열려 있으면 막혀 있다는 표시가 붙습니다. 선행 Issue가 모두 닫히면 표시가 사라집니다.

## 3. 검색 필터

Issue 검색창에서 아래 조건을 사용할 수 있습니다.

|필터|결과|
|---|---|
|`is:blocked`|아직 끝나지 않은 선행 Issue가 있는 Issue|
|`is:blocking`|다른 Issue를 막고 있는 Issue|
|`assignee:@me -is:blocked`|내 Issue 중 지금 바로 시작할 수 있는 Issue|
|`assignee:@me is:blocking`|내 Issue 중 다른 사람이 기다리고 있는 Issue|

웹 Issue 검색에서 동작하며, REST 검색 API(`gh api search/issues`)에서는 결과가 나오지 않습니다.

## 4. 기능이 하지 않는 것

- **작업을 강제로 막지 않습니다.** 선행 Issue가 열려 있어도 막힌 Issue를 진행하거나 닫을 수 있습니다. 순서를 알려 주는 표시입니다.
- **Project Status를 바꾸지 않습니다.** Status는 Branch·Pull Request 워크플로우가 바꿉니다.
- **넘겨줄 값을 전달하지 않습니다.** 이미지 tag, 설정 이름 같은 값은 코멘트로 남겨야 합니다.

## 5. 사용 규칙

### 연결할 때

- 실제로 결과물(이미지, 설정 값, 생성된 자원)을 받아야 시작할 수 있는 경우에만 연결합니다. 단순히 관련 있는 Issue는 본문의 `References`에 적습니다.
- 선행 관계를 연결하면 상위 Issue(로드맵·작업 묶음)의 표도 함께 수정합니다.

### 작업을 시작할 때

1. 내 Issue에 막혀 있다는 표시가 있는지 확인합니다.
2. 있으면 Relationships의 **Blocked by**에서 누구의 어떤 작업을 기다리는지 확인합니다.
3. 기다리는 동안 미리 할 수 있는 작업(설정 초안, 스크립트 작성)은 진행합니다.

### 작업을 끝냈을 때

1. 내 Issue의 **Blocking** 목록을 확인합니다.
2. 그 Issue 담당자가 받아야 할 값을 내 Issue에 코멘트로 남기고 담당자를 멘션합니다.
   - 예: `@<담당자> 새 서버 이미지 ghcr.io/orbit-cloud-labs/orbit-server:<커밋SHA>를 발행했습니다.`
   - Secret, Credential 원문은 코멘트에 남기지 않습니다.
3. Pull Request 머지로 Issue가 닫히면 막혀 있던 Issue의 표시가 자동으로 해제됩니다.

## 6. CLI 사용

```bash
# 선행 Issue 목록 조회
gh api repos/ORBIT-CLOUD-LABS/<repo>/issues/<번호>/dependencies/blocked_by \
  --jq '.[] | "\(.repository_url | split("/") | last)#\(.number) \(.title)"'

# 선행 Issue 추가 (issue_id는 Issue 번호가 아니라 내부 id)
BLOCKER_ID=$(gh api repos/ORBIT-CLOUD-LABS/<선행 repo>/issues/<선행 번호> --jq .id)
gh api -X POST repos/ORBIT-CLOUD-LABS/<repo>/issues/<번호>/dependencies/blocked_by -F issue_id=$BLOCKER_ID

# 선행 Issue 삭제
gh api -X DELETE repos/ORBIT-CLOUD-LABS/<repo>/issues/<번호>/dependencies/blocked_by/$BLOCKER_ID
```

## 7. 사용 예

Phase1 로드맵([orbit#25](https://github.com/ORBIT-CLOUD-LABS/orbit/issues/25))은 역할별 작업 묶음 Issue 아래 세부 Issue에 선행 관계를 연결해 두었습니다. 현재 선행 관계와 바로 시작할 수 있는 Issue는 각 작업 묶음 Issue에서 확인합니다.
