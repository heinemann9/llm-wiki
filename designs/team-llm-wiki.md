# Team LLM Wiki — 설계 문서

> Redmine + Google Docs + 코드 레포를 종합하는 팀 내부 LLM Wiki를 위한 설계 템플릿.
> 다른 프로젝트에 가져가서 가미해 쓰는 것을 전제로 작성.
> 본 문서는 [llm-wiki](https://github.com/heinemann9/llm-wiki) 레포의 PLAN.md/CLAUDE.md 패턴을 팀 환경에 확장한 것.

---

## 1. 컨셉

> 팀이 운영 중인 **살아있는 시스템**들(Redmine·Google Docs·코드 레포)을 LLM이 인제스트해, 영속적·구조화된 마크다운 위키를 점진적으로 작성·유지한다.

질의 시점에 매번 검색하는 RAG와 달리, **한 번 정리한 지식이 누적**된다. 단발 검색이 아니라 *팀의 누적 합의·결정·맥락*을 담는 것이 목표.

## 2. 적용 시나리오

이 패턴이 가치 있는 상황:

- **온보딩**: 새 멤버가 위키만 읽으면 도메인·결정 히스토리·핵심 인물을 파악
- **회고·의사결정 추적**: "왜 이 결정을 내렸지?" 가 이슈/문서/커밋에 흩어져 있을 때 한 곳에 합성
- **고객·도메인 컨텍스트**: Redmine 이슈에 등장하는 고객/시나리오/제약을 entity 페이지로 누적
- **문서 표류 방지**: GDocs는 만든 사람만 알고 검색이 약함 → 위키 인덱스가 진입점 역할

이 패턴이 **부적절한** 상황:
- 데이터가 자주 바뀌고 단발 조회만 필요 → 그냥 Redmine/GDocs 직접 검색이 나음
- 한 사람만 쓰는 컨텍스트 → CLAUDE.md 하나면 충분

## 3. 아키텍처 (3계층 + 출처 분리)

```
┌───────────────────────────────────────────────┐
│ 3) Schema (사람이 정의)                       │
│    CLAUDE.md, 출처별 ingest 규칙              │
└───────────────────────────────────────────────┘
                  ↓
┌───────────────────────────────────────────────┐
│ 2) Wiki (LLM이 작성·유지)                     │
│    concepts / entities / topics / summaries   │
│    + index.md + log.md                        │
└───────────────────────────────────────────────┘
                  ↑ 읽기 전용
┌───────────────────────────────────────────────┐
│ 1) Raw Sources (스냅샷 단위로 불변)           │
│    sources/redmine/  + sources/gdocs/         │
│    + sources/code/                            │
└───────────────────────────────────────────────┘
```

**핵심 원칙**: 원본은 살아있지만, **fetch한 스냅샷은 불변**. 같은 자료의 새 버전은 새 스냅샷으로 추가하고, 위키 페이지가 두 버전을 합성·diff 표시한다.

## 4. 폴더 구조

```
<team>-llm-wiki/                 ← 보통 private repo
├── PLAN.md                       이 문서를 복사·도메인화
├── CLAUDE.md                     LLM 운영 규칙 (§9 템플릿 참조)
├── README.md
├── .gitignore                    .env, *.token 등 차단
├── sources/                      Raw (스냅샷 단위 불변)
│   ├── INDEX.md                  전체 source 메타
│   ├── redmine/
│   │   ├── issue-1234@2026-05-03.md
│   │   ├── wiki-page-foo@2026-05-03.md
│   │   └── INDEX.md              redmine 전용 인덱스 (선택)
│   ├── gdocs/
│   │   ├── design-auth-v3@2026-05-03.md
│   │   └── meeting-2026-04-30@2026-05-03.md
│   └── code/
│       ├── api-handler@abc123.md  (커밋 SHA 단위)
│       └── README-snapshot@abc123.md
├── wiki/
│   ├── index.md                  카테고리별 페이지 카탈로그
│   ├── log.md                    시간순 변경 이력
│   ├── concepts/                 도메인 개념 (Auth flow, Billing model …)
│   ├── entities/                 사람·팀·서비스·고객·외부 시스템
│   ├── topics/                   "Q3 OKR 진행", "결제 모듈 history"
│   └── summaries/                source 1:1 요약
└── scripts/                      자동화 (Phase 2 이후)
    ├── sync_redmine.py
    ├── sync_gdocs.py
    ├── sync_code.py
    └── lint_stale.py             원본 변경 감지 → 영향 페이지 표시
```

## 5. 출처별 수집·동기화 전략

### 5.1 Redmine
- **API**: REST `/issues.json`, `/wiki/<project>/<page>.json`
- **단위**: 이슈 1개 = source 1개. 위키 페이지 1개 = source 1개.
- **스냅샷 명명**: `issue-<id>@YYYY-MM-DD.md`
- **포함**: 본문 + 모든 코멘트 + 상태 history + 첨부 메타
- **Delta sync**: `updated_on >= 마지막_sync` 필터로 변경분만 재인제스트
- **상태 변화 추적**: 이슈 entity 페이지에 *상태 전이 history* 기록 (Open → In Progress → Closed)

### 5.2 Google Docs
- **API**: Drive API (목록) + Docs API (본문 export)
- **단위**: 문서 1개 = source 1개. 큰 문서면 섹션 단위 분할 고려.
- **스냅샷 명명**: `<slug>@YYYY-MM-DD.md`
- **포함**: 텍스트 + 표 + 댓글(권한 허용 시). 이미지는 본문에 placeholder + 별도 다운로드.
- **버전 비교**: 새 fetch 시 이전 스냅샷과 diff → CLAUDE.md 워크플로에서 *변경 요약*을 위키 페이지에 반영
- **OAuth**: workspace 도메인 한정 토큰 권장. read-only scope.

### 5.3 코드 레포
- **수집 단위**: 파일 단위가 아닌 **커밋 SHA 단위 스냅샷**. 또는 README/ADR/주요 모듈 docstring만 추출.
- **스냅샷 명명**: `<path-slug>@<short-sha>.md`
- **무엇을 위키화할지 결정**:
  - ✅ 위키화: 아키텍처 결정, 도메인 개념, 외부 인터페이스 계약, 운영 절차
  - ❌ 위키화 X: 함수 구현 자체, 테스트 픽스처, 자동 생성 코드
- **변경 감지**: `git log <last_sync>..HEAD --name-only` → 영향받는 위키 페이지 목록 산출

### 5.4 공통 원칙
- 모든 source는 frontmatter에 `fetched_at`, `source_type`, `source_url`, `source_version` 명시
- API 토큰은 `.env`로 분리, `.gitignore` 필수
- private repo 또는 self-hosted git 권장

## 6. 페이지 작성 규칙

### 6.1 Frontmatter
```yaml
---
title: <페이지 제목>
type: concept | entity | topic | summary
sources: [<source-id>, ...]
updated: YYYY-MM-DD
sensitive: true | false      # 민감 정보 포함 여부 (선택)
owners: [<team-or-person>]   # 책임자 (선택)
---
```

### 6.2 본문 규칙
- 모든 사실 진술 끝에 `[^source-id]` 출처 표시
- 다른 페이지 언급 시 마크다운 링크
- 추측·해석은 명시적 `추론:` 표시
- **익명화**: 외부 공유 가능성 있는 페이지는 고객명·개인정보를 가명·역할로 치환
- **민감 정보 격리**: `sensitive: true` 페이지는 wiki/index.md에 표시하되 별도 prefix (`🔒`)
- 날짜는 절대 표기 (`YYYY-MM-DD`)

## 7. 워크플로

### 7.1 Ingest
1. (자동 또는 수동) 원본을 fetch → `sources/<type>/<slug>@<date>.md` 저장
2. LLM이 읽고 `wiki/summaries/<slug>.md` 요약 생성
3. 추출된 개념/개체에 대해 기존 페이지 갱신, 신규면 생성
4. `wiki/index.md`, `wiki/log.md`, `sources/INDEX.md` 갱신
5. 민감 정보 익명화 점검

### 7.2 Sync (이 use case 추가 워크플로)
원본이 살아있는 시스템이라 정기 sync 필요:
1. `scripts/sync_*.py` 가 변경된 source 재 fetch → 새 스냅샷 (`@<new-date>.md`)
2. 이전 스냅샷과 diff 산출
3. LLM이 diff 기반으로 영향 위키 페이지 갱신 (덮어쓰기 X, *변경 사실*을 페이지에 추가)
4. 노후 진술 발견 시 페이지에 `⚠️ 2026-05-03 갱신 필요` 마커

### 7.3 Query
1. `wiki/index.md` 에서 관련 페이지 후보 식별
2. 합성 답변 + 출처 인용
3. 재사용 가치 있으면 `wiki/topics/` 승격

### 7.4 Lint (정기)
- 모순·고아·누락 교차참조 (기본)
- + **노후 source**: 마지막 fetch 후 N일 경과
- + **변경 미반영**: source 새 스냅샷 있으나 위키 미갱신
- + **민감 정보 누출**: anonymization 규칙 위반 후보

## 8. 보안·접근 통제

- **레포 가시성**: private GitHub 또는 self-hosted git (Gitea, GitLab CE)
- **토큰**: `.env` + `.gitignore`. 절대 frontmatter나 본문에 포함 금지
- **민감 정보 분류**:
  - L0 공개 가능
  - L1 사내 한정
  - L2 팀 한정 (별도 폴더 또는 별도 레포)
- **공유 시 sanitize**: L1 페이지를 외부에 보여줄 때 자동 redact하는 스크립트 (선택)
- **감사 추적**: git history 자체가 audit log. force-push 금지.

## 9. CLAUDE.md 템플릿 (이 use case 전용 추가 섹션)

기존 [llm-wiki CLAUDE.md](https://github.com/heinemann9/llm-wiki/blob/main/CLAUDE.md) 에 다음을 **추가**:

```markdown
## N. 출처별 ingest 규칙

### Redmine 이슈
- 본문 + 코멘트 + 상태 history 모두 포함
- 이슈 entity 페이지에 상태 전이 timeline 유지
- 담당자/리포터는 entities/ 페이지로 분리

### Google Docs
- 새 스냅샷 fetch 시 이전 버전과 diff
- diff 요약을 위키 페이지에 추가 (덮어쓰기 X)
- 댓글이 결정에 영향을 줬으면 그 사실 명시

### 코드 레포
- 코드 자체를 위키화하지 말 것
- "이 코드가 구현하는 결정·개념"만 추출
- ADR/README 변경은 우선 갱신, 함수 변경은 무시

## N+1. 민감 정보 처리
- 외부 고객명: 가명 (예: 고객A, 고객B) 또는 역할 (예: "결제 PSP 파트너")
- 개인 식별자: 이메일·전화·실명은 위키에 작성 금지 (이슈 ID로만 참조)
- 내부 metric/매출: `sensitive: true` 페이지에만 작성, index에서 🔒 표시

## N+2. Sync 워크플로
- 정기 sync 후 발견된 변경은 매 sync마다 별도 commit ("sync: ...")
- 변경 미반영 페이지는 ⚠️ 마커 + log.md에 todo 등록
```

## 10. 자동화 단계

| 단계 | 인제스트 | Sync | Lint |
|---|---|---|---|
| **Phase 0** | 수동 (1건씩) | — | — |
| **Phase 1** | 수동, 패턴 정착 | — | 주 1회 수동 |
| **Phase 2** | 수동 | 주 1회 수동 sync 스크립트 | 주 1회 |
| **Phase 3** | 수동 또는 selective auto | cron sync (일 1회) | cron lint |
| **Phase 4** | full auto + 알림 | full auto | full auto + 대시보드 |

**중요**: Phase 0~1에서 *수동으로 패턴을 정착*시키지 않고 자동화부터 하면 위키가 쓰레기로 채워짐. CLAUDE.md 규칙을 LLM이 안정적으로 따르는 걸 확인한 후에 자동화.

## 11. MVP 단계 (실제 시작 시)

**Phase 0 — 셋업** (1일)
- [ ] private repo 생성
- [ ] 이 문서를 복사 → `PLAN.md`로 도메인 적합화
- [ ] `CLAUDE.md` 작성 (§9 템플릿 + 도메인 규칙)
- [ ] 폴더 구조 + 빈 인덱스
- [ ] `.env`, `.gitignore` 준비

**Phase 1 — 첫 인제스트** (1주)
- [ ] 출처 1개 선정 (예: 핵심 Redmine 프로젝트 1개의 이슈 5건)
- [ ] 손으로 fetch → wiki 페이지 생성
- [ ] 요약·개념·개체 페이지 형식 정착
- [ ] LLM이 CLAUDE.md 규칙을 따르는지 검증

**Phase 2 — 출처 확장** (2주)
- [ ] Google Docs 1개 카테고리 추가 (예: 설계 문서)
- [ ] 코드 레포 1개의 ADR/README 추가
- [ ] 출처가 섞일 때 entity 페이지가 제대로 합성되는지 확인

**Phase 3 — 동기화 도입** (선택, 2주~)
- [ ] `scripts/sync_redmine.py` (주 1회 수동 실행)
- [ ] 변경 감지 → 위키 갱신 워크플로
- [ ] Lint 자동화

**Phase 4 — 운영 정착** (지속)
- [ ] cron + 알림
- [ ] 팀 멤버에게 query 인터페이스 제공 (Claude Code, Slack bot 등)
- [ ] 분기별 대규모 lint + 구조 재정비

## 12. 결정해야 할 것 (시작 전 체크리스트)

- [ ] **레포 위치**: GitHub private / GitLab self-hosted / 사내 Gitea
- [ ] **범위**: 한 팀 / 한 프로젝트 / 회사 전체 (작게 시작 권장)
- [ ] **언어 정책**: 위키 한국어 / 영어 / 혼용
- [ ] **출처 우선순위**: 셋 중 어디부터 시작할지 (보통 가장 정보 밀도 높은 곳)
- [ ] **민감 정보 분류 기준**: L0/L1/L2 정의
- [ ] **자동화 시점**: 언제부터 sync 자동화 도입할지 (Phase 2~3 권장)
- [ ] **운영 책임자**: 위키 lint·구조 정비를 정기적으로 할 사람 1명 (없으면 무너짐)

## 13. 흔한 함정

1. **자동화 조급증** — Phase 0에서 수동 패턴 정착 없이 cron부터 돌리면 LLM이 잘못된 패턴을 대규모로 박아넣음
2. **모든 source를 위키화하려는 욕심** — 코드 함수 단위까지 위키화하면 노이즈만 늘어남. ADR/결정만.
3. **민감 정보 사후 정리** — 한 번 들어간 sensitive 정보는 git history에서 지우기 어려움. 처음부터 익명화 규칙 강제.
4. **운영 책임자 없음** — 팀 위키는 누구도 책임 안 지면 6개월 안에 무너짐. LLM이 북키핑은 해도 *방향성·구조 정비*는 사람이 해야 함.
5. **Query를 안 함** — 위키만 만들고 안 쓰면 가치 없음. 정기적으로 wiki 기반 질의를 돌려서 부족한 곳을 발견해야 위키가 자란다.

## 14. 함께 보면 좋은 것

- [Karpathy LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — 원형 아이디어
- [llm-wiki 레퍼런스 구현](https://github.com/heinemann9/llm-wiki) — 본 패턴의 정적 자료 instantiation
- [llm-wiki/wiki/concepts/llm-wiki-pattern.md](https://github.com/heinemann9/llm-wiki/blob/main/wiki/concepts/llm-wiki-pattern.md) — 패턴 자체에 대한 위키 페이지 (비판·실구현 사례 포함)
- [llm-wiki/CLAUDE.md](https://github.com/heinemann9/llm-wiki/blob/main/CLAUDE.md) — 운영 규칙 베이스
