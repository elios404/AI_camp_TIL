---
layout: post
title: "9월 12일 금요일 TIL(FastAPI, Git)"
date: 2025-09-12 18:00:00 +0900
categories: July_week11
---

# 9월 12일 금요일 TIL 작성

## 1. 학습 주제
- FastAPI
- Git


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### fastAPI 시작하기

```powershell
conda install -c conda-forge fastapi uvicorn
```

fastAPI와 서버 실행을 위한 uvicorn 설치

`uvicorn main:app` 이런식으로 실행. `uvicorn main:app --reload` 코드 변화시 알아서 재실행하도록

url에서 뒤에 `/docs` 를 붙이면 swagger 확인 가능

### git 사용

git reset —hard commit코드

git revert commit코드

### git revert, reset의 차이

`git revert`는 이전 커밋을 **되돌리는 새로운 커밋을 생성**하는 명령어입니다.

---

#### 작동 방식

`git revert`는 삭제하려는 커밋의 변경사항을 **역으로 적용**하여, 그 결과물을 새로운 커밋으로 만듭니다. 즉, 특정 커밋에서 추가된 내용은 제거하고, 제거된 내용은 다시 추가하는 방식으로 작동합니다.

예를 들어, 커밋 `C`가 파일에 'Hello'라는 내용을 추가했다고 가정해 보겠습니다. `git revert C` 명령을 실행하면, 'Hello'를 제거하는 새로운 커밋 `C'`가 생성됩니다.

!

이 방법은 기존 커밋 기록을 변경하지 않고 보존하기 때문에, 팀원들과 협업하는 프로젝트에서 안전하게 변경사항을 취소할 때 유용합니다.

`git reset`과 비교하면 `git revert`의 특징이 더 명확해집니다.

---

#### `git revert` vs. `git reset`

| 특징 | `git revert` | `git reset` |
| --- | --- | --- |
| **커밋 기록** | **변경하지 않고 보존** (새로운 커밋 생성) | **삭제하거나 되돌림** (기록을 변경) |
| **안전성** | 안전함. 협업 시 주로 사용 | 위험함. 로컬 환경에서 주로 사용 |
| **용도** | 특정 커밋의 변경사항을 취소 | 커밋 기록을 완전히 되돌리고 싶을 때 |

협업 환경에서는 이미 공유된 커밋을 `git reset`으로 삭제하면 다른 팀원들의 저장소와 충돌을 일으킬 수 있습니다. 따라서 **기록을 유지하며 안전하게 되돌리는 `git revert`**가 협업 프로젝트에 훨씬 적합합니다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크
https://github.com/elios404/LLM_agent_study_GJ_class/tree/main/spring_fast_work
코드 참고 링크

## 6. 느낀 점 / 회고 
이전에 8월 미니프로젝트에서 활용한 FastAPI를 좀 더 정확하게 학습할 수 있어서 좋았다.