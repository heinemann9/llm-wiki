---
title: Karpathy — LLM Wiki (gist 요약)
type: summary
sources: [2026-karpathy-llm-wiki]
updated: 2026-05-03
---

# Karpathy — LLM Wiki

Andrej [Karpathy](../entities/karpathy.md)가 공개한 gist. 개인 지식 베이스를 LLM으로 구축하는 패턴을 제안한다[^src]. 본 위키 저장소의 원형 아이디어.

## 핵심 주장

대다수 사람들의 LLM+문서 사용 경험은 [RAG](../concepts/rag.md)다. 질의 시점에 청크를 검색해서 답을 생성하는 방식이지만, 매 질문마다 LLM이 지식을 처음부터 재발견하므로 **누적이 없다**[^src]. NotebookLM, ChatGPT 파일 업로드 등이 이 방식이다[^src].

대안으로 제시되는 것이 [LLM Wiki 패턴](../concepts/llm-wiki-pattern.md): LLM이 원문과 사용자 사이에 위치한 영속적·구조화된 마크다운 위키를 **점진적으로 작성·유지**한다[^src]. 새 원문이 들어오면 LLM이 읽고 핵심을 추출해 기존 위키에 통합 — 개체 페이지 갱신, 주제 요약 수정, 모순 표시 등[^src]. 지식은 한 번 컴파일되어 **계속 최신 상태로 유지**된다[^src].

## 적용 사례

저자가 든 예시[^src]:

- **Personal**: 목표·건강·자기계발 추적, 저널/팟캐스트 노트 정리
- **Research**: 한 주제를 수개월 깊게 파고들며 가설 진화 추적
- **Reading a book**: 챕터별 정리 + 인물·테마·플롯 페이지 (Tolkien Gateway 같은 팬위키를 개인용으로)
- **Business/team**: Slack·미팅·고객 콜로 자동 유지되는 내부 위키
- **Competitive analysis, due diligence, trip planning** 등 누적형 지식 작업 전반

## 아키텍처

3계층[^src]:

1. **Raw sources** — 불변. LLM은 읽기만 함.
2. **The wiki** — LLM 소유. 요약·개체·개념·비교·종합 페이지.
3. **The schema** — `CLAUDE.md`/`AGENTS.md`. 위키 구조·관례·워크플로 정의. 사람과 LLM이 함께 진화시킴.

→ 본 저장소의 [3계층 구조](../../PLAN.md)와 [CLAUDE.md](../../CLAUDE.md)는 이 설계를 그대로 채택.

## 운영 (Operations)

세 가지 작업[^src]:

- **Ingest**: 원문 한 건이 보통 10~15개 위키 페이지를 건드림[^src]. 저자는 1건씩 사람이 개입하는 방식 선호.
- **Query**: 답변이 가치 있으면 위키 페이지로 다시 파일링 → 탐구 자체가 누적된다[^src].
- **Lint**: 모순·노후 진술·고아 페이지·누락된 교차참조·빈 영역(웹서치 후보)을 점검[^src].

## 인덱싱과 로깅

두 특수 파일[^src]:

- **`index.md`**: 콘텐츠 지향. 카테고리별 페이지 카탈로그. 질의 시 LLM이 먼저 읽음. 약 100 sources / 수백 페이지 규모까지는 **임베딩 RAG 인프라 없이도 충분**[^src].
- **`log.md`**: 시간순 append-only. `## [YYYY-MM-DD] ingest | Title` 형식이면 `grep`/`tail` 같은 unix 도구로 파싱 가능[^src].

## 도구·팁

- **Obsidian Web Clipper**: 웹 → 마크다운 변환[^src]
- **로컬 이미지 다운로드**: `Settings → Files and links` 에서 attachment 폴더 고정 + 핫키 바인딩[^src]
  - LLM이 inline 이미지가 있는 마크다운을 한 번에 못 읽음 → 텍스트 먼저, 이미지는 별도 조회[^src]
- **Obsidian graph view**: 허브/고아 페이지 시각화[^src]
- **Marp**: 마크다운 슬라이드[^src]
- **Dataview**: frontmatter 기반 동적 테이블[^src]
- **검색 도구**: 위키가 커지면 [qmd](https://github.com/tobi/qmd) 같은 BM25/벡터 하이브리드 + LLM 재순위 로컬 검색 도입 가능[^src]
- 위키 = git 레포 → 버전 관리·브랜칭 무료[^src]

## 왜 이게 작동하는가

지식 베이스 유지의 어려운 부분은 읽기·사고가 아니라 **북키핑**이다[^src]. 교차참조 갱신, 요약 최신화, 모순 표시, 일관성 유지 — 사람은 유지비용 증가가 가치 증가보다 빨라서 위키를 포기한다[^src]. LLM은 지루해하지 않고, 한 번에 15개 파일을 건드릴 수 있다[^src]. **유지비용이 0에 수렴**하므로 위키가 살아남는다[^src].

사람의 역할: 출처 큐레이션, 분석 방향 지시, 좋은 질문, 의미 해석. LLM의 역할: **그 외 전부**[^src].

## 사상적 계보

추론: Vannevar Bush의 [Memex](https://en.wikipedia.org/wiki/Memex) (1945)와 정신적으로 가깝다고 저자가 언급[^src]. 개인적·능동적 큐레이션, 문서 간 연결을 문서 자체만큼 중요하게 다루는 비전. Bush가 풀지 못한 부분이 "누가 유지할 것인가"였는데, LLM이 그 답[^src].

## 메타 노트

문서는 의도적으로 추상적이다[^src]. 디렉토리 구조·스키마·페이지 포맷·도구는 도메인과 LLM에 맞춰 구체화하라고 명시[^src]. 본 저장소가 그 instantiation 중 하나.

[^src]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
