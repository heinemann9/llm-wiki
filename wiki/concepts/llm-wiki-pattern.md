---
title: LLM Wiki 패턴
type: concept
sources: [2026-karpathy-llm-wiki]
updated: 2026-05-03
---

# LLM Wiki 패턴

LLM이 원본 문서와 사용자 사이에 위치한 **영속적·구조화된 마크다운 위키**를 점진적으로 작성·유지하는 지식 관리 패턴[^src]. [Karpathy](../entities/karpathy.md)가 gist 형태로 제안.

## 핵심 정의

> "the LLM **incrementally builds and maintains a persistent wiki** — a structured, interlinked collection of markdown files that sits between you and the raw sources"[^src]

지식이 **한 번 컴파일되어 계속 최신 상태로 유지**된다는 점이 정의적 특징[^src].

## [RAG](rag.md)와의 차이

| 측면 | RAG | LLM Wiki 패턴 |
|---|---|---|
| 지식 누적 | 없음 (질의마다 재발견) | 있음 (위키에 영속) |
| 답변 생성 | 청크 검색 → 합성 | 위키 페이지 검색 → 합성 |
| 교차참조 | 질의 시점에 재구성 | 위키에 미리 박힘 |
| 모순 처리 | 보통 무시/충돌 | 위키에 명시적으로 표시 |
| 비용 시점 | 질의 시 | 인제스트 시 (선불) |

> "The wiki is a persistent, compounding artifact. The cross-references are already there. The contradictions have already been flagged."[^src]

## 구성 요소 (3계층)

1. **Raw sources** — 불변. LLM은 읽기만[^src]
2. **The wiki** — LLM 소유. 요약·개체·개념·비교·종합 페이지[^src]
3. **The schema** — `CLAUDE.md`/`AGENTS.md`. 위키 구조·관례·워크플로 정의. 사람과 LLM이 함께 진화시킴[^src]

## 핵심 작업 (Operations)

- **Ingest**: 원문 1건 → 보통 10~15개 위키 페이지 갱신[^src]
- **Query**: 답변이 가치 있으면 위키 페이지로 다시 파일링 (탐구 자체도 누적)[^src]
- **Lint**: 모순·노후·고아·누락 교차참조·공백 점검[^src]

## 작동 원리: 북키핑 비용 → 0

추론: 이 패턴이 작동하는 본질적 이유는 위키 유지의 가장 큰 장벽이 "북키핑 비용"이라는 인식. 사람은 교차참조 갱신·요약 최신화·일관성 유지 같은 단순 반복 작업의 부담이 가치 증가보다 빨라서 위키를 포기한다[^src]. LLM은 지루해하지 않고 한 번에 다수 파일을 건드릴 수 있어 유지비용이 0에 수렴 → 위키가 살아남는다[^src].

## 인덱싱 전략

- `index.md`: 콘텐츠 카탈로그 (질의 시 1차 진입점)[^src]
- `log.md`: 시간순 append-only (`## [YYYY-MM-DD] ingest | Title` 형식이면 grep 가능)[^src]
- 약 100 sources / 수백 페이지 규모까지는 **임베딩 RAG 인프라 불필요**[^src]
- 그 이상에서는 [qmd](https://github.com/tobi/qmd) 같은 BM25/벡터 하이브리드 검색 도입[^src]

## 사상적 계보

추론: Vannevar Bush의 [Memex](https://en.wikipedia.org/wiki/Memex)(1945)와 정신적 연속선상[^src]. Bush가 풀지 못한 "누가 유지하는가" 문제의 LLM 시대 해답.

## 적용 가능 영역

저자가 명시한 예시[^src]:
- Personal knowledge base (목표·건강·자기계발)
- 장기 리서치 (수개월짜리 주제 심화)
- 책 읽기 동반 위키 (인물·테마·플롯)
- 팀 내부 위키 (Slack·미팅 자동 통합)
- 경쟁 분석, due diligence, trip planning 등

## 본 저장소에서의 instantiation

이 위키 저장소가 패턴의 한 구체화. [PLAN.md](../../PLAN.md)와 [CLAUDE.md](../../CLAUDE.md)에 구조·규칙 명시.

[^src]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
