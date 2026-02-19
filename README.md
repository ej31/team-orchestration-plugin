# 🎯 Team Orchestration Plugin

개발 라이프사이클 단계별로 최적화된 Agent Teams 프리셋을 제공하는 Claude Code 플러그인.

## Prerequisites

```bash
# Agent Teams 실험 기능 활성화
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1

# tmux 설치 (split-pane 모드용)
# macOS
brew install tmux
# Ubuntu
sudo apt install tmux
```

## 설치

```bash
# 로컬 테스트 (개발 중)
claude --plugin-dir /path/to/team-orchestration-plugin

# 플러그인으로 설치 (배포 후)
/plugin marketplace add https://github.com/your-username/team-orchestration-plugin
/plugin install team-orchestration
```

## 슬래시 커맨드

| 커맨드 | 상황 | 팀 패턴 | 팀원 수 |
|--------|------|---------|---------|
| `/team:develop` | 기능 개발 | Leader-Workers | 3 (architect, implementer, tester) |
| `/team:verify` | 검증/QA | Parallel Reviewers | 4 (security, performance, test, quality) |
| `/team:debug` | 디버깅 | Competing Hypotheses | 3 (log-analyst, code-archaeologist, reproducer) |
| `/team:refactor` | 리팩토링 | Wave Execution | 3 (extractor, migrator, guardian) |
| `/team:review-pr` | PR 리뷰 | Parallel Reviewers | 3-4 (security, logic, history, test) |
| `/team:status` | 팀 상태 확인 | - | - |

## 사용 예시

### 기능 개발
```
/team:develop OAuth2 인증 with JWT 토큰 리프레시
```

### 코드 검증
```
/team:verify feature/auth 브랜치의 변경사항
```

### 버그 디버깅
```
/team:debug 결제 API가 간헐적으로 타임아웃 발생
```

### 리팩토링
```
/team:refactor src/services/user.py 모듈을 auth, profile, notification으로 분리
```

### PR 리뷰
```
/team:review-pr #142
```

## 팀 패턴 설명

### 🏗️ Leader-Workers
리더가 계획을 수립하고 팀원에게 분배. 개발/리팩토링에 적합.
```
Leader → [Task Queue] → Worker A (implements)
                      → Worker B (implements)
                      → Worker C (tests)
```

### 👁️ Parallel Reviewers
같은 대상을 다른 관점에서 동시 검토. 검증/리뷰에 적합.
```
Code → Reviewer A (security)
     → Reviewer B (performance)
     → Reviewer C (tests)
     → Leader (synthesize)
```

### ⚔️ Competing Hypotheses
서로 다른 가설을 추구하며 반박. 디버깅에 적합.
```
Bug → Investigator A (logs) ←→ challenges ←→ Investigator B (code)
                              ←→ challenges ←→ Investigator C (repro)
     → Leader (judge when consensus)
```

### 🌊 Wave Execution
의존성 기반으로 단계적 실행. 안전한 리팩토링에 적합.
```
Wave 1: [snapshot] → guardian approves
Wave 2: [extract interfaces] + [add tests] → guardian approves
Wave 3: [move code] + [update refs] → guardian approves
Wave 4: [final verify] + [cleanup]
```

## 비용 가이드

| 시나리오 | 예상 토큰 | 비용 감소 팁 |
|----------|-----------|-------------|
| 단순 기능 | ~500K | 서브에이전트(Task)로 충분할 수 있음 |
| 중간 기능 | ~1-2M | 팀원 3명 + Sonnet |
| 대규모 리팩토링 | ~3-5M | Wave별 커밋으로 중간 중단 가능 |
| PR 리뷰 | ~500K-1M | 변경 파일이 적으면 서브에이전트로 |

**원칙**: 팀원끼리 소통이 필요 없으면 → 서브에이전트. 소통이 필요하면 → Agent Teams.

## 커스터마이징

### 팀 구성 변경
각 커맨드의 `.md` 파일에서 팀원 역할과 수를 조정할 수 있음.

### 프로젝트별 설정
프로젝트 `.claude/commands/team/` 에 복사해서 프로젝트에 맞게 수정:
```bash
cp -r team-orchestration-plugin/commands/team .claude/commands/
```

### Hook 커스터마이징
`hooks/hooks.json`에서 `TeammateIdle`, `TaskCompleted` 이벤트에 
커스텀 검증 스크립트를 연결할 수 있음.

## 디렉토리 구조

```
team-orchestration-plugin/
├── .claude-plugin/
│   └── plugin.json          # 플러그인 매니페스트
├── commands/
│   └── team/
│       ├── develop.md       # /team:develop - 기능 개발
│       ├── verify.md        # /team:verify - 검증/QA
│       ├── debug.md         # /team:debug - 디버깅
│       ├── refactor.md      # /team:refactor - 리팩토링
│       ├── review-pr.md     # /team:review-pr - PR 리뷰
│       └── status.md        # /team:status - 상태 확인
├── skills/
│   └── team-orchestration/
│       └── SKILL.md         # 자동 로드 베스트 프랙티스
├── hooks/
│   └── hooks.json           # 팀 이벤트 훅
└── README.md
```

## License
MIT
