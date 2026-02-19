---
description: "디버깅용 Agent Team 생성. 여러 가설을 병렬로 조사하고 서로 반박하며 근본 원인을 찾는다."
allowed-tools: Teammate, Task, TaskCreate, TaskUpdate, Read, Bash, Grep, Glob
---

# 🐛 Debug Investigation Team

## 목표
$ARGUMENTS 버그/이슈의 근본 원인을 찾고 수정한다.

## 초기 정보 수집
1. 에러 메시지, 스택 트레이스, 로그를 수집한다
2. 재현 조건을 파악한다
3. 최근 변경사항을 확인한다 (`git log --oneline -20`)

## 팀 구성 (3명 - 경쟁적 가설 조사)

```
Create an agent team for debugging: "$ARGUMENTS"

This team uses a COMPETING HYPOTHESES pattern. Each investigator 
pursues a different theory AND actively tries to disprove the others.

1. **log-analyst** (Sonnet): 로그/에러 트레이스 기반 조사
   - 에러 로그에서 패턴 찾기
   - 타임라인 구성 (언제부터 발생했는지)
   - 관련 서비스/컴포넌트 간 호출 흐름 추적
   - 다른 조사관의 가설에 반박할 증거도 찾기

2. **code-archaeologist** (Sonnet): 코드 변경 이력 기반 조사
   - git blame, git log로 관련 코드 변경 추적
   - 최근 변경이 문제를 유발했을 가능성 조사
   - 의존성 변경 확인 (lock file diff)
   - 다른 조사관의 가설에 반박할 증거도 찾기

3. **reproducer** (Sonnet): 재현 및 실험 기반 조사
   - 버그를 최소 재현 케이스로 축소
   - 환경 차이(dev vs prod) 조사
   - bisect 방식으로 원인 범위 좁히기
   - 다른 조사관이 제시한 가설을 실험으로 검증/반증

IMPORTANT: Teammates should MESSAGE EACH OTHER with findings.
When one finds evidence, they should share it AND challenge 
the others' hypotheses. The lead synthesizes when consensus 
emerges or when a clear root cause is identified.
```

## 리더 역할
- 각 조사관의 발견을 실시간 추적
- 가설이 수렴하면 "유력 원인"으로 판정
- 의견이 갈리면 추가 실험을 reproducer에게 요청
- 근본 원인 확정 후 수정 방안 도출

## 완료 조건
- [ ] 근본 원인이 식별됨 (2/3 이상 동의)
- [ ] 재현 테스트가 작성됨 (수정 전 실패 확인)
- [ ] 수정 패치가 적용됨
- [ ] 재현 테스트가 통과됨
- [ ] 회귀 테스트 추가됨
