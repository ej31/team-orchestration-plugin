---
description: "PR 리뷰용 Agent Team 생성. 보안/성능/테스트/변경이력을 병렬로 검토하고 종합 리뷰를 작성한다."
allowed-tools: Teammate, Task, TaskCreate, TaskUpdate, Read, Bash, Grep, Glob
---

# 📋 PR Review Team

## 목표
$ARGUMENTS PR을 다각도로 리뷰한다.

## PR 정보 수집
1. PR diff를 가져온다 (`gh pr diff` 또는 `git diff`)
2. PR 설명과 연관 이슈를 확인한다
3. 변경된 파일의 기존 히스토리를 파악한다

## 팀 구성 (3~4명 - 독립 병렬 리뷰)

```
Create an agent team to review: "$ARGUMENTS"

Spawn reviewers with distinct, non-overlapping focuses:

1. **security-check** (Sonnet): 보안 관점
   - 인증/인가 변경 확인
   - 입력 검증 누락
   - 시크릿/민감정보 노출
   - SQL injection, XSS 패턴

2. **logic-check** (Sonnet): 비즈니스 로직 관점
   - 요구사항 대비 구현 정확성
   - 엣지 케이스 처리
   - 에러 핸들링 적절성
   - 레이스 컨디션, 동시성 이슈

3. **history-check** (Sonnet): 변경 이력 관점
   - 왜 이전에 이렇게 만들었는지 git blame으로 맥락 파악
   - 이전 PR에서 논의된 결정 사항과 충돌하는지
   - 관련 이슈/티켓과의 일관성
   - 불필요한 변경이 섞여있는지

4. **test-check** (Sonnet): 테스트 관점
   - 변경에 대한 테스트가 충분한지
   - 기존 테스트가 깨지지 않는지 (실제 실행)
   - 모킹이 과도하지 않은지
   - 경계값 테스트 존재 여부

All reviewers work in parallel — no dependencies.
```

## 리더 역할
- 모든 리뷰어 완료 대기
- 중복되는 피드백 통합
- 심각도 기준으로 정렬
- 최종 리뷰 코멘트를 작성

## 출력 포맷
리뷰 결과를 아래 형식으로 종합:

```markdown
# PR Review: [PR 제목]

## 판정: ✅ Approve | ⚠️ Request Changes | ❌ Block

## Must Fix (머지 전 필수 수정)
- [ ] [파일:라인] 설명 (@카테고리)

## Should Fix (강력 권장)
- [ ] [파일:라인] 설명 (@카테고리)

## Nice to Have (개선 제안)
- [ ] [파일:라인] 설명 (@카테고리)

## Positive Highlights 👍
- 잘한 부분도 언급
```
