---
description: "리팩토링용 Agent Team 생성. 모듈 분리, 의존성 정리, 테스트 보강을 병렬 Wave로 진행한다."
allowed-tools: Teammate, Task, TaskCreate, TaskUpdate, Read, Write, Bash, Grep, Glob
---

# ♻️ Refactoring Team

## 목표
$ARGUMENTS 대상을 안전하게 리팩토링한다.

## 사전 분석
1. 리팩토링 대상의 현재 구조를 파악한다
2. 의존성 그래프를 분석한다 (누가 이 코드를 참조하는지)
3. 기존 테스트 커버리지를 확인한다
4. 리팩토링 계획을 Wave 단위로 수립한다

## 팀 구성 (3명 - Wave 기반 실행)

```
Create an agent team for refactoring: "$ARGUMENTS"

This team follows a WAVE EXECUTION pattern with safety gates.

1. **extractor** (Sonnet): 코드 분리/추출 담당
   - 모듈, 함수, 클래스 추출
   - 인터페이스/타입 분리
   - 중복 코드 통합
   - 각 추출 단위마다 기존 테스트가 깨지지 않는지 확인

2. **migrator** (Sonnet): 참조 업데이트 담당
   - import 경로 업데이트
   - 호출부 시그니처 변경
   - deprecated 마킹
   - 점진적 마이그레이션 전략 (한 번에 다 바꾸지 않음)

3. **guardian** (Sonnet): 안전성 검증 담당
   - 매 Wave 완료 후 전체 테스트 실행
   - 타입 체크 / 린트 실행
   - 동작 변경이 없는지 확인 (behavioral equivalence)
   - 문제 발견 시 즉시 extractor/migrator에게 알림

Task dependencies (Wave pattern):
Wave 1: "현재 상태 스냅샷" (guardian) → 의존성 없음
Wave 2: "인터페이스 추출" (extractor) → blocked by Wave 1
Wave 2: "테스트 보강" (guardian) → blocked by Wave 1
Wave 3: "구현 이동" (extractor) → blocked by Wave 2
Wave 3: "참조 업데이트" (migrator) → blocked by "인터페이스 추출"
Wave 4: "전체 검증" (guardian) → blocked by Wave 3
Wave 4: "cleanup" (extractor) → blocked by Wave 3

SAFETY RULE: guardian can BLOCK any wave from proceeding 
if tests fail. The lead must not advance until guardian approves.
```

## 리더 역할
- Wave 진행을 관리 (다음 Wave로 넘어가기 전 guardian 승인 필수)
- git commit을 Wave 단위로 수행 (롤백 가능하게)
- 충돌 발생 시 중재
- 전체 리팩토링 완료 후 before/after 비교 리포트 작성

## 완료 조건
- [ ] 모든 Wave가 순서대로 완료됨
- [ ] guardian이 매 Wave를 승인함
- [ ] 전체 테스트 스위트 통과
- [ ] 타입 체크 / 린트 통과
- [ ] Wave별 커밋이 깔끔하게 정리됨
