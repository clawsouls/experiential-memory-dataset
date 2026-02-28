# Experiment Taskset — Experiential vs Synthetic Memory

## Category 1: Information Retrieval (5 tasks)

**IR-1**: ClawSouls 웹사이트에서 soul을 publish하려면 어떤 API 엔드포인트를 호출해야 하는가? HTTP method와 필수 파라미터를 설명하라.

**IR-2**: Supabase Session Pooler에서 "Tenant or user not found" 에러가 발생할 때 원인과 해결법은?

**IR-3**: SoulScan의 점수 체계를 설명하라. 등급은 몇 개이고, 어떤 기준으로 나뉘는가?

**IR-4**: Soul Spec v0.4에서 v0.3 대비 추가된 필수 필드는 무엇인가?

**IR-5**: age 암호화에서 사용되는 키 교환 알고리즘과 대칭 암호화 알고리즘은?

## Category 2: Coding Tasks (5 tasks)

**CT-1**: Next.js App Router에서 `[name]`과 `[owner]/[name]` 동적 라우트를 같은 레벨에서 사용할 때 발생하는 문제와 해결 방법을 코드로 제시하라.

**CT-2**: Supabase RLS 정책을 작성하라: `souls` 테이블에서 인증된 사용자만 자기 soul을 수정할 수 있고, 모든 사용자가 읽기 가능.

**CT-3**: Node.js CLI에서 `--format claude-code|cursor|windsurf` 플래그를 받아 Soul Spec 파일을 각 플랫폼 형식으로 변환하는 함수 시그니처와 핵심 로직을 작성하라.

**CT-4**: GitHub Actions 워크플로우를 작성하라: main 브랜치 push 시 Hugo 빌드 → GitHub Pages 배포.

**CT-5**: TypeScript로 Stripe Webhook handler를 작성하라: `checkout.session.completed` 이벤트를 처리하고 DB에 구매 기록 저장.

## Category 3: Architecture Decisions (5 tasks)

**AD-1**: AI 에이전트 페르소나 마켓플레이스를 설계한다. Soul(페르소나 파일)과 Memory(경험 기록)를 유료로 판매하려 할 때, 결제 → 키 배포 아키텍처를 제안하라. 최소 2개 옵션과 각각의 장단점을 포함.

**AD-2**: 문서 사이트를 구축해야 한다. Docusaurus vs GitBook vs MkDocs 중 하나를 추천하고 이유를 설명하라. 요구사항: 버전관리, 검색, React 컴포넌트 지원, GitHub Pages 무료 호스팅.

**AD-3**: CLI 도구의 `sync` 기능을 설계한다. 사용자의 로컬 memory 파일을 암호화해서 원격 저장소에 동기화하려 할 때, 아키텍처를 제안하라. 보안, 충돌 해결, 키 관리를 포함.

**AD-4**: 웹 앱에서 soul.json 파일의 실시간 유효성 검사를 구현하려 한다. Monaco Editor를 사용할 때 에디터 내 인라인 에러 표시 vs 별도 Problems 패널 vs 둘 다 — 어떤 접근이 적절한가?

**AD-5**: 오픈소스 프로젝트의 npm 패키지 네이밍 전략을 수립하라. 메인 패키지 `clawsouls` 외에 `soulscan`, `soulspec`, `soulhub`, `soul-spec-mcp` 등 7개 패키지를 관리해야 한다. 네임스페이스 전략과 squatting 방어를 포함.

## Category 4: Context-Dependent Decisions (5 tasks)

**CD-1**: 공개 GitHub 레포에 커밋을 남겨야 하는데, 현재 시각이 평일 오후 2시(KST)이다. 어떻게 해야 하는가?

**CD-2**: 팀원(CDO)이 GitHub Issue로 "대시보드에서 New Soul 생성 시 JSON 파싱 에러" 버그를 보고했다. 이슈에 어떤 내용으로 코멘트를 달아야 하는가?

**CD-3**: 경쟁사(Anthropic)가 우리와 유사한 기능(Agent Memory)을 출시했다. 블로그 포스트를 작성한다면 어떤 톤과 프레이밍으로 접근해야 하는가?

**CD-4**: 연구 논문에 commercial implications 섹션을 넣을지 말지 결정해야 한다. 논문은 Zenodo preprint이고, 아직 퇴사 전이며, 경쟁사가 있다. 어떤 판단을 내려야 하는가?

**CD-5**: Reddit에서 우리 프로젝트에 대한 댓글 기회가 있다. 계정은 실명 추적 가능한 계정이다. 댓글을 달아야 하는가? 단다면 어떤 톤으로?

---

## Evaluation Rubric

### Information Retrieval (IR)
- **5점**: 정확하고 완전한 답변, 세부사항 포함
- **4점**: 대체로 정확하나 세부사항 일부 누락
- **3점**: 방향은 맞으나 부정확한 부분 있음
- **2점**: 부분적으로만 정확
- **1점**: 틀림 또는 "모르겠다"

### Coding Tasks (CT)
- **5점**: 동작하는 코드, 엣지 케이스 고려, 베스트 프랙티스 준수
- **4점**: 동작하는 코드, 사소한 문제
- **3점**: 대체로 맞으나 수정 필요
- **2점**: 접근 방향은 맞으나 동작 안 함
- **1점**: 완전히 틀림

### Architecture Decisions (AD)
- **5점**: 실용적이고 근거 있는 추천, 트레이드오프 분석 포함
- **4점**: 합리적 추천이나 분석 깊이 부족
- **3점**: 일반적 답변, 프로젝트 맥락 반영 부족
- **2점**: 비현실적이거나 근거 부족
- **1점**: 답변 불가 또는 완전히 부적절

### Context-Dependent (CD)
- **5점**: 프로젝트 상황(퇴사 리스크, 팀 구조, 경쟁 상황)을 완벽히 반영한 판단
- **4점**: 대체로 적절하나 맥락 일부 놓침
- **3점**: 일반론 수준, 프로젝트 특수 맥락 미반영
- **2점**: 부적절한 판단
- **1점**: 위험한 판단 (보안, 법적 리스크 무시)
