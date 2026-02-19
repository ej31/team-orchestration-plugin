---
description: "검증/QA용 Agent Team 생성. 보안, 성능, 테스트 커버리지, 코드 품질을 병렬로 검증한다."
allowed-tools: Teammate, Task, TaskCreate, TaskUpdate, Read, Bash, Grep, Glob
---

# 🔍 Verification & QA Team

## 목표
$ARGUMENTS 에 대해 다각도 검증을 수행한다.

## 대상 파악
1. 검증 대상 코드/PR/브랜치를 파악한다
2. 변경된 파일 목록을 추출한다 (`git diff --name-only` 또는 지정된 범위)
3. 프로젝트의 테스트 프레임워크와 린트 설정을 파악한다

## 팀 구성 (4명 - 각자 독립적 관점)

```
Create an agent team for verification: "$ARGUMENTS"

Spawn four reviewers, each with a distinct lens. They should NOT overlap.

1. **security-reviewer** (Sonnet): 보안 관점 검증
   - SQL injection, XSS, CSRF 등 취약점 확인
   - 인증/인가 로직 검증
   - 민감 데이터 노출 여부 (환경변수, 시크릿, 하드코딩된 값)
   - 의존성 취약점 확인 (npm audit, pip audit 등)

2. **performance-reviewer** (Sonnet): 성능 관점 검증
   - N+1 쿼리, 불필요한 DB 호출
   - 메모리 누수 패턴
   - 비동기 처리 적절성
   - 캐싱 전략 검토
   - 대량 데이터 처리 시 병목

3. **test-reviewer** (Sonnet): 테스트 커버리지 검증
   - 기존 테스트 실행 및 통과 확인
   - 엣지 케이스 테스트 누락 확인
   - 에러 핸들링 경로 테스트 여부
   - 모킹이 적절한지 확인
   - 통합 테스트 vs 유닛 테스트 균형

4. **quality-reviewer** (Sonnet): 코드 품질 검증
   - SOLID 원칙 준수
   - 네이밍 컨벤션 일관성
   - 중복 코드 탐지
   - 에러 핸들링 패턴 일관성
   - 로깅/모니터링 적절성
   - 문서화 (docstring, JSDoc 등)

All four work in parallel — no dependencies between them.
Each reviewer writes findings to a structured format.
```

## 리더 역할
- 4명의 리뷰어가 모두 완료될 때까지 대기
- 결과를 종합해서 심각도(Critical/High/Medium/Low)별로 분류
- 충돌하는 의견이 있으면 양쪽 리뷰어에게 메시지 보내서 토론 유도
- 최종 리뷰 리포트를 markdown으로 작성

## 출력 포맷
```markdown
# Verification Report: [대상]
## Summary
- Critical: N개 | High: N개 | Medium: N개 | Low: N개

## Critical Issues
### [C-001] 제목
- **카테고리**: Security | Performance | Test | Quality
- **위치**: 파일:라인
- **설명**: ...
- **권장 수정**: ...

## High Issues
...

## Recommendations
- 즉시 수정 필요: ...
- 다음 스프린트: ...
- 기술 부채로 등록: ...
```
