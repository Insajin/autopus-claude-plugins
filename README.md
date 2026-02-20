# Autopus Claude Plugins

Autopus 플랫폼을 Claude Code와 통합하는 플러그인 마켓플레이스입니다.

## 빠른 시작

### 1. Marketplace 등록

```bash
/plugin marketplace add insajin/autopus-claude-plugins
```

### 2. 플러그인 설치

```bash
/plugin install autopus@autopus-plugins
```

### 3. Bridge 설정

```bash
autopus-bridge up
```

이 명령어 하나로 로그인, 워크스페이스 선택, AI 도구 설정, 서버 연결이 완료됩니다.

### 4. 사용 시작

```bash
# 슬래시 커맨드로 태스크 실행
/autopus:execute 로그인 API 보안 취약점 분석

# 상태 확인
/autopus:status
```

---

## 플러그인 구성

### autopus

Autopus 플랫폼 통합 플러그인. Bridge CLI를 통해 플랫폼과 소통합니다.

| 구성 요소 | 설명 |
|-----------|------|
| **Commands** | `/autopus:execute` - 태스크 실행, `/autopus:status` - 상태 확인 |
| **Agent** | `autopus-orchestrator` - 플랫폼 작업 오케스트레이터 |
| **Skill** | `autopus-platform` - Bridge CLI 사용법, 워크플로우 패턴 |
| **Hooks** | 자동 연결 확인, 파일 변경 보고, 세션 종료 보고 |

### 아키텍처

```
Claude Code (Worker Agent)
  │
  ├── [Hooks] 자동 보고 (워커 무의식)
  │   ├── SessionStart → 연결 상태 확인
  │   ├── PostToolUse  → 파일 변경 시 보고
  │   └── Stop         → 세션 종료 보고
  │
  ├── [Skills] 능동 소통 (필요할 때)
  │   ├── autopus-bridge execute "..."
  │   ├── autopus-bridge status --json
  │   └── autopus-bridge tools list
  │
  └── [Agent] 본업에 집중
      └── 코드 작성, 버그 수정, 테스트 등
          │
          ▼
     autopus-bridge CLI
          │
          ▼
     Autopus Platform
```

---

## 설치 방법

### 방법 1: Marketplace를 통한 설치 (권장)

```bash
# Step 1: Marketplace 등록
/plugin marketplace add insajin/autopus-claude-plugins

# Step 2: 플러그인 설치
/plugin install autopus@autopus-plugins
```

### 방법 2: GitHub에서 직접 설치

```bash
/plugin install insajin/autopus-claude-plugins
```

### 방법 3: 로컬 설치 (개발/테스트)

```bash
/plugin install /path/to/autopus-claude-plugins/plugins/autopus
```

### 방법 4: autopus-bridge up (자동 설치)

```bash
# bridge up 명령어가 플러그인도 자동 설치합니다
autopus-bridge up
```

---

## 팀 배포

`.claude/settings.json`에 추가하여 팀 전체에 자동 적용:

```json
{
  "extraKnownMarketplaces": [
    {
      "source": "https://github.com/insajin/autopus-claude-plugins",
      "name": "autopus-plugins"
    }
  ],
  "enabledPlugins": [
    {
      "name": "autopus",
      "marketplaceId": "autopus-plugins",
      "scope": "project"
    }
  ]
}
```

---

## 사전 요구 사항

- **Claude Code** v1.0.33 이상
- **autopus-bridge** CLI 설치 및 인증 완료
  ```bash
  # 설치
  # GitHub Releases에서 다운로드하거나:
  go install github.com/autopus/autopus-bridge@latest

  # 설정
  autopus-bridge up
  ```

---

## 문제 해결

### 플러그인이 로드되지 않음

```bash
# 플러그인 검증
/plugin validate .

# 디버그 모드로 확인
claude --debug
```

### Bridge 연결 안 됨

```bash
# 상태 확인
autopus-bridge status --json

# 재연결
autopus-bridge connect

# 전체 재설정
autopus-bridge up --force
```

### 커맨드가 보이지 않음

```bash
# 플러그인 목록 확인
/plugin list

# 플러그인이 비활성화된 경우
/plugin enable autopus@autopus-plugins
```

---

## 개발

### 로컬 테스트

```bash
# 플러그인 디렉토리에서 테스트
claude --plugin-dir ./plugins/autopus

# 검증
claude plugin validate ./plugins/autopus
```

### 구조

```
autopus-claude-plugins/
├── .claude-plugin/
│   └── marketplace.json        # Marketplace 매니페스트
├── plugins/
│   └── autopus/
│       ├── .claude-plugin/
│       │   └── plugin.json     # 플러그인 매니페스트
│       ├── commands/
│       │   ├── execute.md      # /autopus:execute
│       │   └── status.md       # /autopus:status
│       ├── agents/
│       │   └── orchestrator.md # autopus-orchestrator 에이전트
│       ├── skills/
│       │   └── autopus-platform/
│       │       └── SKILL.md    # Bridge CLI 레퍼런스 스킬
│       └── hooks/
│           └── hooks.json      # 자동 보고 hooks
└── README.md
```

---

## 라이선스

MIT
