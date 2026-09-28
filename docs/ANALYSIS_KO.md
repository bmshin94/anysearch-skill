# 📦 AnySearch Skill 전수조사 & 활용 분석 리포트 (한국어)

> 작성일: 2026-09-28
> 대상 버전: **v3.1.1** (Apache License 2.0)
> 작성: Claude Code 세션 대화 정리본

---

## 🔗 관련 GitHub 주소

| 구분 | URL |
|---|---|
| **원본 레포지토리 (upstream)** | https://github.com/anysearch-ai/anysearch-skill |
| **본 포크 (this repo)** | https://github.com/bmshin94/anysearch-skill |
| 조직 페이지 | https://github.com/anysearch-ai |
| 릴리스 목록 | https://github.com/anysearch-ai/anysearch-skill/releases |
| v3.1.1 배포본 | https://github.com/anysearch-ai/anysearch-skill/archive/refs/tags/v3.1.1.zip |
| SKILL.md (원본) | https://github.com/anysearch-ai/anysearch-skill/blob/main/SKILL.md |
| README.md (원본) | https://github.com/anysearch-ai/anysearch-skill/blob/main/README.md |
| README_zh.md (중문) | https://github.com/anysearch-ai/anysearch-skill/blob/main/README_zh.md |
| CI Actions | https://github.com/anysearch-ai/anysearch-skill/actions |
| 서드파티 포크 (원샷 설치판) | https://github.com/lvusyy/anysearch-skill |
| 스킬 디렉터리 소개 | https://skillsllm.com/skill/anysearch-skill |
| SourceForge 미러 | https://sourceforge.net/projects/anysearch-skill.mirror/ |

### 서비스 / API 주소

| 구분 | URL |
|---|---|
| API 베이스 | `https://api.anysearch.com` |
| 검색 엔드포인트 | `POST https://api.anysearch.com/v1/search` |
| 서브도메인 조회 | `GET  https://api.anysearch.com/v1/sub-domains` |
| 본문 추출 | `POST https://api.anysearch.com/v1/extract` |
| 이메일 자동 등록 | `POST https://api.anysearch.com/v1/auth/email/register` |
| 콘솔 (API 키 발급) | https://anysearch.com/console/api-keys |
| 로그인 | https://www.anysearch.com/login |
| 보안 신고 | security@anysearch.com |

### 📊 인기도 (2026-09-28 확인 기준)

| 지표 | 값 |
|---|---|
| ⭐ Stars | **약 6.3k** |
| 🍴 Forks | 약 363 |
| 👀 Watchers | 약 46 |
| 📝 Commits (main) | 약 65 |
| 📜 License | Apache-2.0 |

---

## 1. 한 줄 요약

> **AI 에이전트(Claude Code 등)에게 "실시간 검색 능력"을 붙여주는 Agent Skill 패키지.**
> 일반 웹검색 + 17개 전문(버티컬) 도메인 검색 + 최대 5개 병렬 배치검색 + URL 본문 마크다운 추출을 제공한다.

---

## 2. 저장소 전체 구조 (23개 파일)

```
anysearch-skill/                 # 설치 시 "anysearch"로 이름 변경
├── SKILL.md                     ⭐ AI가 읽는 스킬 정의서 (YAML frontmatter)
├── README.md / README_zh.md     사람이 읽는 설치 가이드 (영문/중문)
├── CLAUDE.md                    (본 포크에서 추가된 페르소나 설정)
├── LICENSE / NOTICE             Apache License 2.0
├── SECURITY.md                  취약점 신고 정책 (security@anysearch.com)
├── SHA256SUMS.txt               🔐 CLI 스크립트 4종 무결성 해시
├── .env.example                 API 키 템플릿
├── requirements.txt             requests>=2.20 (Python CLI 전용)
├── .gitignore                   .env / runtime.conf / __pycache__ / .idea
├── .gitattributes               *.sh text eol=lf
├── .github/
│   ├── workflows/ci.yml         드리프트 검사 + 계약 테스트 + 플레이스홀더 검사
│   └── ISSUE_TEMPLATE/          bug-report.yml / feature-request.yml
└── scripts/
    ├── anysearch_cli.py   (723줄) 🐍 Python CLI  — 1순위
    ├── anysearch_cli.js   (587줄) 🟢 Node.js CLI — 2순위, 외부 의존성 0
    ├── anysearch_cli.sh   (610줄) 🐚 Bash CLI    — jq + curl 필요
    ├── anysearch_cli.ps1  (769줄) 🪟 PowerShell CLI
    ├── generate.py        (243줄) 🏭 4개 CLI 공통 블록 자동 생성기
    ├── test_cli.py        (333줄) 🧪 로컬 HTTP 스텁 기반 크로스런타임 계약 테스트
    └── shared/
        ├── constants.json       API 주소 + 17개 도메인 목록 (단일 진실 공급원)
        └── doc_spec.md          AI용 인터페이스 명세 템플릿 ({{LANG_*}} 치환)
```

---

## 3. 제공 기능 (CLI 명령어 5종)

| 명령어 | 설명 | 제한 |
|---|---|---|
| `search` | 웹 검색 1건 (일반 또는 버티컬) | `--max_results` 1–10, 기본 10 |
| `batch_search` | 검색 1–5건 **병렬** 실행 | 최대 5개, 개별 실패 무관 / 부분 성공 가능 |
| `extract` | URL 전체 본문을 Markdown으로 추출 | HTML/텍스트 5만자 절단, PDF·이미지 미지원 |
| `get_sub_domains` | 버티컬 검색 가능 목록 + 필수 파라미터 조회 | 최대 5개 도메인 동시 조회 |
| `doc` | 오프라인 인터페이스 명세 출력 (네트워크 미사용) | 복구·디버깅용 |

### 🌐 지원 버티컬 도메인 17종

```
general        resource       social_media   finance        academic
legal          health         business       security       ip
code           energy         environment    agriculture    travel
film           gaming
```

| 도메인 | 활용 예 |
|---|---|
| `finance` 💰 | 주가, 환율, 시장 동향 (`finance.quote`, `type=stock,symbol=AAPL,cn_code=`) |
| `academic` 🎓 | 논문 검색, DOI 조회 |
| `security` 🛡️ | CVE 취약점 조회 |
| `code` 💻 | 공식 개발 문서 |
| `legal` ⚖️ | 판례·법률 |
| `health` 🏥 | 의학 정보 |
| `travel` ✈️ | 항공(IATA) |
| `ip` 📜 | 특허 |

---

## 4. 동작 흐름

```
[사용자 질문]
      ↓
[AI 에이전트] SKILL.md 로드 → runtime.conf 확인 (없으면 런타임 탐지)
      ↓
[CLI 실행]  python3 scripts/anysearch_cli.py search "..." --tag finance.quote
      ↓
[HTTP 요청] POST https://api.anysearch.com/v1/search
            Content-Type: application/json
            X-Anysearch-Client: skill/3.1.1
            Authorization: Bearer as_sk_xxx   (선택 — 없으면 익명)
      ↓
[JSON 응답] code / message / data / request_id 봉투 구조
      ↓
[CLI가 Markdown으로 렌더링] → AI가 읽고 답변 생성
```

### 런타임 우선순위

```
Python (>=3.6, requests 필요)  >  Node.js (>=12, 의존성 없음)
  >  PowerShell (>=5.1, Windows)  >  Bash (>=3.2, jq + curl)
```

> macOS는 `python`이 없고 `python3`만 있는 경우가 많으므로 **둘 다 확인** 필요.
> `anysearch_cli.sh`는 Bash 전용 (POSIX `sh` 비호환) — 반드시 `bash`로 실행.

---

## 5. 검색 전략 (SKILL.md가 AI에게 강제하는 규칙)

이 스킬의 진짜 가치는 API 래핑이 아니라 **"AI가 검색을 잘하도록 유도하는 프롬프트 설계"**에 있다.

```
사용자 질문
  |
  +-- 순수 백과사전/상식 + 도메인 중첩 ZERO?
  |     YES → Path 1: search "query"           (드문 예외)
  |
  +-- 애매함 / 도메인 소스가 도움될 수 있음?
  |     YES → HYBRID: batch_search (일반 1 + 버티컬 N 동시 발사)
  |
  +-- 명확히 도메인 특화 / 구조화된 식별자 보유?
        YES → Path 2: get_sub_domains → search  (기본 경로)
```

핵심 규칙 요약:

1. **기본값은 Path 2 (버티컬)**. 일반 검색은 예외다.
2. **버티컬 검색 전 `get_sub_domains` 필수 호출** — 서브도메인과 필수 파라미터 확인.
3. `(required)` 표시 파라미터는 **값이 없어도 빈 값으로 반드시 전달** (`cn_code=`). 누락 시 백엔드 검증 에러.
4. **애매하면 `batch_search`로 하이브리드** — "커버리지가 추측을 이긴다(Coverage beats guessing)".
5. `get_sub_domains` 결과는 **세션 내 캐싱**, 반복 호출 금지 (토큰 절약).
6. AnySearch 불가 시(키 없음/쿼터 소진/서비스 오류/네트워크 실패) **사용자에게 알리고**, 승인 시 다른 검색 수단으로 폴백.

---

## 6. 설치 및 사용법

### 6-1. 설치

```bash
# 1) 고정 릴리스 다운로드 (권장)
curl -L -o anysearch-skill.zip \
  https://github.com/anysearch-ai/anysearch-skill/archive/refs/tags/v3.1.1.zip

# 2) 압축 해제
unzip anysearch-skill.zip

# 3) 에이전트 스킬 디렉터리로 이동 (이름은 "anysearch")
mv anysearch-skill-3.1.1 ~/.claude/skills/anysearch
```

| 에이전트 | 설치 경로 |
|---|---|
| Claude Code | `~/.claude/skills/anysearch` |
| OpenCode | `~/.config/opencode/skills/anysearch` |
| Cursor / Windsurf | `<project>/.skills/anysearch` |
| 공용 (Codex, OpenClaw 등) | `~/.agents/skills/anysearch` |

> 스킬 마켓플레이스가 있는 플랫폼이면 **"anysearch" 검색 후 설치**가 가장 간단하다.

### 6-2. API 키 설정 (선택)

**이메일 한 줄로 자동 등록 (인증코드 불필요):**

```bash
curl -s -X POST "https://api.anysearch.com/v1/auth/email/register" \
  -H "Content-Type: application/json" \
  -d '{"email": "you@example.com"}'
```

성공 시 `data.api_key.key` 에 `as_sk_...` 형태의 평문 키가 **단 한 번만** 반환된다.

```bash
cp .env.example .env
# .env 에 작성
ANYSEARCH_API_KEY=as_sk_xxxxxxxxxxxx
```

**키 우선순위:** `--api_key` 플래그 > `.env` 파일 > 시스템 환경변수 > 익명 접속

**등록 에러 대응표:**

| message | 대처 |
|---|---|
| `Invalid email address.` | 이메일 재입력 |
| `email_already_registered` | 이미 가입됨 → 로그인 안내, **재시도 금지** |
| `Rate limited, retry after N seconds.` | N초 대기 후 재시도 |
| `Key creation failed. ...` | 계정만 생성됨 → 콘솔에서 수동 발급 |
| `Internal server error.` | 나중에 재시도 또는 익명 폴백 |

### 6-3. 설치 검증 (4단계)

```bash
# Step 1 — 런타임 탐지
python --version ; python3 --version ; node --version
bash --version ; jq --version ; curl --version

# Step 2 — 엔트리 테스트 (오프라인)
python3 <skill_dir>/scripts/anysearch_cli.py doc

# Step 3 — runtime.conf 저장 (매번 탐지 방지)
echo "Runtime: Python" > <skill_dir>/runtime.conf
echo "Command: python3 <skill_dir>/scripts/anysearch_cli.py" >> <skill_dir>/runtime.conf

# Step 4 — 실검색 테스트
python3 <skill_dir>/scripts/anysearch_cli.py search "hello world" --max_results 1
```

> 설치/업데이트 후 `SHA256SUMS.txt`로 스크립트 무결성 검증 권장.

### 6-4. 실전 명령어 모음

```bash
# 일반 검색
<cmd> search "양자컴퓨팅 2026" --max_results 5

# 버티컬 검색 (2단계)
<cmd> get_sub_domains --domain finance
<cmd> search "AAPL" --tag finance.quote --params type=stock,symbol=AAPL,cn_code=

# 호환 별칭 형태
<cmd> search "latest trends" --domain finance --sub_domain finance.market --sdp region=US,timeframe=2025Q1

# 다중 도메인 조회 (최대 5개)
<cmd> get_sub_domains --domains finance,health,code

# 병렬 배치 검색
<cmd> batch_search --query "AAPL" --query "TSLA" --query "GOOG" --max_results 3

# 하이브리드 (일반 + 버티컬 혼합)
<cmd> batch_search --queries '[{"query":"quantum computing"},{"query":"QBTS","domain":"finance","sub_domain":"finance.quote","sub_domain_params":"type=stock,symbol=QBTS,cn_code="}]'

# 파일에서 읽기
<cmd> batch_search --queries @queries.json

# URL 본문 추출 (출력이 이미 Markdown)
<cmd> extract "https://example.com/article"
<cmd> extract --url "https://example.com/article"

# 서브커맨드 도움말
<cmd> search --help
```

**❌ 잘못된 사용:** `extract --format markdown`, `extract --format json`, `extract --markdown`
→ `extract` 에는 포맷 옵션이 **존재하지 않는다**. URL 위치인자 또는 `--url`/`-u` 만 허용.

---

## 7. 플러그인 / 스킬 / MCP 구분

### 결론: **Agent Skill 이다.** (MCP 아님, 플러그인 아님)

근거:
- 루트에 `SKILL.md` + YAML frontmatter (`name`, `description`, `version`, `authors`, `credentials`)
- 설치 경로가 `~/.claude/skills/`
- README/SKILL.md에 *"no MCP server installation or JSON-RPC wrapper is required"* 명시
- 커밋 이력에 `feat: migrate skill CLI to direct HTTP` — 과거 JSON-RPC 방식에서 직접 HTTP로 **의도적으로 전환**

| 구분 | 🎨 Skill | 🔌 MCP | 🧩 Plugin |
|---|---|---|---|
| 정체 | Markdown 설명서 + 스크립트 | 상시 실행 서버 프로세스 | Skill+MCP+명령어 묶음 |
| 통신 | AI가 터미널에서 CLI 실행 | JSON-RPC (stdio/HTTP) | — |
| 설치 | 폴더 복사 | 설정파일에 서버 등록 | 마켓플레이스 |
| 상주 | 필요할 때만 실행 | 세션 내내 상주 | — |
| 토큰 | 필요 시에만 로드 | 툴 스키마 상시 점유 | — |

**스킬 방식을 택한 전략적 이유:** 설치 마찰 제거, 프로세스 상주 불필요, 컨텍스트 절약,
Claude Code / Cursor / Windsurf / OpenCode / Codex 등 **플랫폼 중립 호환**.

---

## 8. API 토큰 필요 여부

### 결론: **필수 아님.** 키 없이도 모든 기능 사용 가능.

```yaml
credentials:
  - name: ANYSEARCH_API_KEY
    required: false      # ← 명시적으로 false
```

| | 🆓 익명 | 🔑 API 키 |
|---|---|---|
| 기능 | 전부 사용 가능 | 전부 사용 가능 |
| Rate limit | 낮음 | 높음 (`rate_limit: 100`) |
| 쿼터 | 낮음 | 높음 |
| 인증 | 없음 | `Authorization: Bearer as_sk_...` |
| 발급 | — | 이메일 1개, 약 30초 |

### 키 소진 시 자동 복구 흐름

1. 응답에 `auto_registered` 필드로 새 키가 포함될 수 있음
2. 에이전트는 키를 추출
3. **사용자에게 명시적 확인을 받은 뒤에만** `.env`에 저장
4. 실패한 호출 재시도

### 보안 권고

- 채팅창에 키 붙여넣기 금지 → `.env` 또는 환경변수 사용
- `.env`, `runtime.conf` 는 `.gitignore` 등록 완료
- 키 주기적 로테이션
- **민감정보(비밀번호·개인정보·영업기밀) 검색 금지** — 쿼리/URL/키가 `api.anysearch.com` 으로 전송됨
- 제공사의 "zero retention / zero-knowledge / no logging" 은 **업체의 주장(claimed)** 이며 독립 검증된 사실이 아님

---

## 9. GitHub에서 인기 있는 이유 분석

⭐ 약 6.3k stars / 🍴 363 forks / 👀 46 watchers

1. **타이밍** — Agent Skills 생태계 초기에 나온 "제대로 만든 실사용 스킬". 선점 효과.
2. **수요 1순위** — 무슨 에이전트를 만들든 "검색"은 반드시 필요하다.
3. **진입장벽 0** — API 키 불필요, 카드 불필요, 설정파일 편집 불필요, 의존성 불필요(Node 기준).
4. **플랫폼 중립** — MCP였다면 플랫폼별 지원 편차로 확산이 제한됐을 것.
5. **코드 품질** — 4런타임 동등 구현, `generate.py` 코드 생성기, 로컬 HTTP 스텁 계약 테스트,
   CI의 SIGPIPE 레이스 컨디션까지 주석으로 설명, SHA256 체크섬, 프롬프트 인젝션 방어.
6. **레퍼런스 가치** — 자기 스킬을 만들려는 개발자들이 "SKILL.md 작성법 교과서"로 참고.
7. **중화권 공략** — `README_zh.md`, `zone: cn/intl`, 중국 주식 `cn_code` 파라미터.
8. **PLG 마케팅** — README 상단 등록 안내, "30초 시작", **AI가 사용자 대신 회원가입 자동 수행**.

### 주의할 점

- 스타 수 ≠ 실사용자 수 (AI 도구 카테고리 특유의 거품 존재)
- 여러 포크가 유통 중 (예: `lvusyy/anysearch-skill` "one-shot install edition") → 출처 확인 필요
- 커밋 이력에 `docs: remove third-party promo content injected via external PRs` 존재
  → **외부 PR을 통한 광고 삽입 시도가 실제로 있었음**. 포크 사용 시 SHA256 검증 필수.

---

## 10. 로컬 에이전트 구축 관점

### A. 부품으로서

| 항목 | 평가 |
|---|---|
| 검색 기능 | ⭐⭐⭐⭐⭐ 즉시 사용 가능 |
| 설치 난이도 | ⭐⭐⭐⭐⭐ 폴더 복사 |
| 완전 오프라인 | ❌ 외부 API 의존 — 폐쇄망 요건이면 부적합 |

> 💡 **중요 발견:** `ANYSEARCH_API_BASE_URL` 환경변수로 엔드포인트 교체가 가능하다.
> ```bash
> export ANYSEARCH_API_BASE_URL=http://localhost:8080
> ```
> → 동일 인터페이스를 구현한 **자체 게이트웨이/검색 백엔드**를 붙일 수 있다는 뜻.

### B. 설계 교본으로서 (실질 가치가 더 큼)

| 패턴 | 배울 점 |
|---|---|
| SKILL.md 4단 구성 | Overview / Trigger / Recommended Entry Point / Command Cheat Sheet |
| Trigger 섹션 | "언제 쓸지"를 명시 → 스킬이 많아도 오발동 방지 |
| Decision Flow | 판단 순서도를 마크다운으로 제공 → AI 판단 품질 상승 |
| `runtime.conf` 캐싱 | 탐지 결과 저장 → 토큰·시간 절약 |
| `doc` 오프라인 명령 | 네트워크 없이 자기 사용법 출력 → 복구 경로 확보 |
| 멀티 런타임 폴백 | 사용자 환경 다양성 대응 |
| `_repair_json()` | **AI/셸이 망가뜨린 JSON 복구** — AI 도구 제작 시 필수 패턴 |
| untrusted 경고 주입 | `extract` 결과 상단에 프롬프트 인젝션 방어 문구 자동 삽입 |
| 키 우선순위 체인 | 플래그 > .env > 환경변수 > 익명 |
| `generate.py` + CI `--check` | 다중 구현 드리프트 방지 |

### 권장 아키텍처

```
[로컬 에이전트]
  └── skills/
      ├── anysearch/     ← 검색 (그대로 사용)
      ├── my-db/         ← 사내 DB 조회 (직접 제작)
      ├── my-crawler/    ← 특정 사이트 크롤러
      └── my-report/     ← 리포트 생성
      ※ 전부 SKILL.md + CLI 패턴으로 통일
```

### 제약 체크리스트

- ✅ Apache 2.0 → 상업적 이용/수정/재배포 자유 (NOTICE 유지, 변경사항 명시)
- ✅ Node CLI 의존성 0 → 컨테이너 경량화 유리
- ⚠️ `batch_search` 최대 5개, `max_results` 최대 10 → 대량 수집엔 부적합
- ⚠️ `extract` HTML/텍스트 5만자 절단 → 긴 문서는 청킹 필요
- ⚠️ PDF/DOC/이미지/오디오/비디오/아카이브 미지원

---

## 11. React / PHP 구현 가능성

### 결론: **가능하다.** AnySearch는 단순 HTTP + JSON API이므로 언어 무관.

```
POST /v1/search        {query, tag?, params?, zone?, language?, max_results?}
GET  /v1/sub-domains   ?domain=finance&domain=health
POST /v1/extract       {url}
Header: Authorization: Bearer <key>   (선택)
        X-Anysearch-Client: <client>/<version>
```

응답 봉투: `{ code, message, data, request_id }` — `code !== 0` 이면 에러.

### PHP 클라이언트 (요지)

```php
<?php
class AnySearch {
    private string $base = 'https://api.anysearch.com';
    public function __construct(private ?string $apiKey = null) {}

    private function call(string $method, string $path, ?array $payload = null, array $query = []) {
        $url = $this->base . $path . ($query ? '?' . http_build_query($query) : '');
        $headers = ['Content-Type: application/json', 'X-Anysearch-Client: php/1.0'];
        if ($this->apiKey) $headers[] = 'Authorization: Bearer ' . $this->apiKey;

        $ch = curl_init($url);
        curl_setopt_array($ch, [
            CURLOPT_RETURNTRANSFER => true,
            CURLOPT_CUSTOMREQUEST  => $method,
            CURLOPT_HTTPHEADER     => $headers,
            CURLOPT_TIMEOUT        => 30,
        ]);
        if ($payload !== null) {
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($payload, JSON_UNESCAPED_UNICODE));
        }
        $res  = curl_exec($ch);
        $code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);

        $body = json_decode($res, true);
        if ($code >= 400 || ($body['code'] ?? 0) !== 0) {
            throw new RuntimeException($body['message'] ?? "HTTP $code");
        }
        return $body['data'] ?? [];
    }

    public function search(string $q, ?string $tag = null, array $params = [], int $max = 10) {
        $payload = ['query' => $q, 'max_results' => max(1, min($max, 10))];
        if ($tag)    $payload['tag']    = $tag;
        if ($params) $payload['params'] = $params;
        return $this->call('POST', '/v1/search', $payload);
    }

    public function subDomains(array $domains) {
        return $this->call('GET', '/v1/sub-domains', null, ['domain' => $domains]);
    }

    public function extract(string $url) {
        return $this->call('POST', '/v1/extract', ['url' => $url]);
    }
}
```

활용처: 워드프레스 플러그인, 라라벨 패키지, 그누보드 모듈, 관리자 대시보드.

### React 구현 시 **필수 주의사항**

🚨 **브라우저에서 API 키를 직접 사용하면 안 된다.** (개발자도구로 노출 + CORS 문제)

```
❌ React(브라우저) ──[API Key 노출]──> api.anysearch.com
✅ React ──> 내 서버(BFF) ──[키는 서버에만]──> api.anysearch.com
```

Next.js Route Handler 예 (`app/api/search/route.ts`):

```ts
import { NextRequest, NextResponse } from 'next/server';

export async function POST(req: NextRequest) {
  const { query, tag, params, max_results = 5 } = await req.json();

  const res = await fetch('https://api.anysearch.com/v1/search', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Anysearch-Client': 'web/1.0',
      ...(process.env.ANYSEARCH_API_KEY && {
        Authorization: `Bearer ${process.env.ANYSEARCH_API_KEY}`,
      }),
    },
    body: JSON.stringify({
      query,
      ...(tag && { tag }),
      ...(params && { params }),
      max_results: Math.max(1, Math.min(max_results, 10)),
    }),
  });

  const body = await res.json();
  if (!res.ok || body.code !== 0) {
    return NextResponse.json({ error: body.message ?? 'search failed' }, { status: 502 });
  }
  return NextResponse.json(body.data);
}
```

### 만들 수 있는 것 (난이도별)

| 난이도 | 산출물 | 스택 |
|---|---|---|
| 🟢 | PHP/JS 5번째 CLI → upstream에 PR (컨트리뷰터 등재) | PHP |
| 🟢 | 워드프레스 "AI 검색" 플러그인 | PHP |
| 🟡 | 17개 도메인 UI화한 검색 대시보드 | React + Next.js |
| 🟡 | 라라벨 / Composer 패키지 배포 | PHP |
| 🟠 | 키워드 모니터링 SaaS (크론 + 알림) | Next.js + DB |
| 🔴 | 셀프호스팅 게이트웨이 (`ANYSEARCH_API_BASE_URL` 교체) | 자유 |

---

## 12. 수익화 아이디어

### 대전제 3원칙

1. **재료를 팔지 말고 요리를 팔아라** — AnySearch 자체는 무료+익명 가능. 리셀링은 성립하지 않는다.
2. **한국어 시장은 무주공산** — 공식 문서는 영문·중문뿐. 한글 생태계 콘텐츠가 사실상 없음.
3. **AI 스킬 생태계는 초기 단계** — 선점 프리미엄이 존재하는 시기.

### 🥇 티어 1 — 즉시 실행 가능 (1~4주, 초기비용 0)

**1. 한국어 스킬 번들 "K-Skills Pack"**
- 구성: `anysearch-ko`(한글 SKILL.md), `naver-search`, `krx-stock`, `law-kr`, `dart-finance`, `gov-data`
- 모델: 무료 3종 공개 + Pro 번들 ₩29,000(평생) / 팀 라이선스 ₩199,000
- 기간 2~4주 · 난이도 🟢 · 예상 월 ₩50~300만

**2. 설치 자동화 CLI (`npx k-skills init`)**
- 런타임 탐지 → SHA256 검증 → runtime.conf 생성 → 이메일 키 자동발급 → 엔트리테스트
- 모델: 무료(리드 수집) → Pro 관리 대시보드 월 ₩9,900
- 기간 1~2주 · 난이도 🟢
- 근거: `lvusyy/anysearch-skill` 의 "one-shot install edition" 포크가 인기 → **수요 검증됨**

**3. 콘텐츠·교육 (가장 빠른 현금화)**

| 상품 | 가격 |
|---|---|
| 유튜브 "AI 에이전트 스킬 만들기" | 광고 + 제휴 |
| 인프런/클래스101 강의 | ₩55,000 |
| 전자책 "Claude Code 실전 스킬 개발" | ₩19,900 |
| 유료 뉴스레터 (주간 AI 스킬) | 월 ₩5,900 |
| 기업 사내교육 | 일 ₩150~300만 |

### 🥈 티어 2 — SaaS (3~6개월)

**4. CVE 보안 모니터링 SaaS ★ 최우선 추천**
```
의존성 파일 업로드 → 라이브러리 파싱
→ 매일 batch_search --domain security
→ 신규 CVE 발견 시 Slack/메일 알림
→ 대시보드(위험도/영향범위/패치버전/조치 가이드)
```
- 모델: Free(1저장소) / Pro $29·월(10개) / Team $99·월(무제한+Slack) / Enterprise
- 기간 3~4개월 · 난이도 🟡 · 예상 100개사 × $29 = **월 $2,900**
- 강점: 보안은 예산이 배정된 영역, 구독 유지율 최상, ISMS-P·ISO27001 대응 수요

**5. 경쟁사/브랜드 모니터링 SaaS**
- 키워드 등록 → 매일 배치검색(일반+business+social_media) → `extract` 본문 수집 → AI 요약·감성분석 → 브리핑 발송
- 모델: Starter ₩49,000 / Pro ₩149,000 / Agency ₩399,000 (월)
- 차별점: 기존 알리미류는 링크만 제공 → 우리는 "읽을 필요 없는 요약" 제공

**6. 논문·연구 리서치 어시스턴트**
- `academic` 도메인 + DOI + `extract` + 한글 요약 + APA/MLA/IEEE 인용 생성 + 연구동향 타임라인
- 모델: 학생 ₩9,900 / 연구자 ₩29,000 (월) / **대학 기관 라이선스 연 ₩1,000만+**

**7. 금융 리서치 봇**
- 포트폴리오 종목 최대 5개 → `batch_search` 로 시세·뉴스·공시 요약 → 텔레그램 알림
- 모델: 월 ₩19,900 / API 종량제
- ⚠️ **투자자문업 규제 확인 필수** — 정보 제공에 한정, 매매 추천 금지

### 🥉 티어 3 — 고수익·고난도 (B2B)

**8. 기업용 AI 에이전트 구축 컨설팅 ★ 단가 최고**
- 현황 분석 → 스킬 설계 → 사내 데이터 연동 스킬 개발 → 보안 게이트웨이 → 교육 → 유지보수
- 모델: 구축 ₩2,000~8,000만 / 유지보수 월 ₩200~500만
- AnySearch 구조를 익히면 사내 DB·문서·ERP 스킬을 동일 패턴으로 양산 가능

**9. 셀프호스팅 검색 게이트웨이 (보안 시장)**
```
[사내 AI 에이전트] → [자체 게이트웨이] → 외부 API 또는 사내 SearxNG
                         ├── 쿼리 감사 로그
                         ├── 민감정보 필터 (주민번호·계좌번호 차단)
                         ├── 결과 캐싱 (비용 절감)
                         └── 부서별 쿼터 관리
```
- 기술 근거: `ANYSEARCH_API_BASE_URL` 로 엔드포인트 교체 가능
- 시장 근거: SKILL.md 자체가 "민감정보 검색 금지"를 경고 → **그 경고가 곧 페인포인트**
- 모델: 온프레미스 라이선스 연 ₩3,000만~ / 서버당 과금
- 타겟: 금융·공공·의료 등 데이터 반출 제한 조직

**10. 스킬 마켓플레이스 플랫폼**
- 모델: 거래 수수료 20~30% / 프리미엄 입점비
- 난이도 🔴🔴 (네트워크 효과·자본 필요). 하이리스크 하이리턴.

### 종합 비교표

| # | 아이디어 | 난이도 | 기간 | 초기비용 | 예상수익 | 추천도 |
|---|---|---|---|---|---|---|
| 1 | 한국어 스킬 번들 | 🟢 | 2-4주 | 0 | 월 50~300만 | ⭐⭐⭐⭐⭐ |
| 2 | 설치 자동화 CLI | 🟢 | 1-2주 | 0 | 리드 수집 | ⭐⭐⭐⭐ |
| 3 | 콘텐츠·교육 | 🟢 | 즉시 | 0 | 월 100~500만 | ⭐⭐⭐⭐⭐ |
| 4 | CVE 모니터링 | 🟡 | 3-4개월 | 소 | 월 $3,000+ | ⭐⭐⭐⭐⭐ |
| 5 | 브랜드 모니터링 | 🟡 | 2-3개월 | 소 | 월 300~1,000만 | ⭐⭐⭐⭐ |
| 6 | 논문 어시스턴트 | 🟡 | 3개월 | 소 | 기관 계약 | ⭐⭐⭐⭐ |
| 7 | 금융 리서치봇 | 🟠 | 2개월 | 소 | 월 200만 | ⭐⭐⭐ |
| 8 | B2B 컨설팅 | 🔴 | 건별 | 0 | 건당 2천~8천만 | ⭐⭐⭐⭐⭐ |
| 9 | 보안 게이트웨이 | 🔴 | 6개월 | 중 | 연 3,000만+ | ⭐⭐⭐⭐ |
| 10 | 마켓플레이스 | 🔴🔴 | 1년+ | 대 | 변동성 큼 | ⭐⭐ |

### 추천 로드맵

```
【0~1개월】 씨앗 뿌리기
├── 한국어 SKILL.md 번역 → GitHub 공개 (무료)
├── PHP CLI 제작 → upstream PR (컨트리뷰터 등재)
├── 블로그/유튜브 "AI 스킬" 시리즈 시작
└── 목표: GitHub 스타 + 개인 브랜딩

【1~4개월】 첫 수익
├── 스킬 번들 Pro 판매
├── 전자책/강의 출시
├── CVE 모니터링 MVP 개발 착수
└── 목표: 월 100만원 + 고객 리스트 확보

【4~12개월】 스케일
├── CVE 모니터링 SaaS 정식 출시
├── 축적된 브랜드로 B2B 컨설팅 수주
└── 목표: 월 1,000만원 + 기업 고객
```

### 리스크 관리

| 리스크 | 대응 |
|---|---|
| AnySearch 서비스 종료 | 추상화 레이어 설계 → 검색 백엔드 교체 가능하게 |
| 가격 정책 변경 | 자체 키 + 결과 캐싱으로 비용 통제 |
| 대형 업체 진입 | 한국 특화 니치로 방어 |
| 투자자문 규제 (아이디어 7) | 사전 법률 검토 필수 |

---

## 13. 최종 요약 (TL;DR)

| 질문 | 답 |
|---|---|
| **뭐하는 거야?** | AI 에이전트에 실시간 검색·버티컬 검색·병렬 배치검색·URL 본문추출을 붙여주는 Agent Skill |
| **언제 써?** | 최신 정보 조회, 팩트체크, 문서 정독, 전문 식별자(주식·CVE·DOI·IATA) 조회, 다중 질의 |
| **스킬? MCP? 플러그인?** | **Agent Skill** (MCP 아님 — 직접 HTTP 호출) |
| **API 키 필요해?** | 선택. 없어도 전 기능 동작(익명, 낮은 한도). 이메일만으로 30초 발급 |
| **왜 유명해?** | 타이밍 + 진입장벽 0 + 플랫폼 중립 + 높은 코드 품질 + 스킬 작성 교본 + 중화권 공략 + PLG |
| **로컬 에이전트에 도움?** | 부품으로도 쓰이지만, **설계 패턴 교본**으로서의 가치가 더 큼 |
| **React/PHP 가능?** | 가능. 단순 HTTP+JSON. 단 **React는 반드시 BFF 프록시**로 키 보호 |
| **수익화?** | 한국어 번들 · 교육 콘텐츠 → CVE 모니터링 SaaS → B2B 컨설팅 순 |
| **주의점?** | 서드파티 API 의존 / 민감정보 검색 금지 / PDF 미지원 / 결과 10건·배치 5건 제한 / 포크 무결성 검증 |

---

*이 문서는 저장소 전체 파일(23개) 정독과 대화 내용을 정리한 결과물입니다.*
*원본: https://github.com/anysearch-ai/anysearch-skill · 본 포크: https://github.com/bmshin94/anysearch-skill*
