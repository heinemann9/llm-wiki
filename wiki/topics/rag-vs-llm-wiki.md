---
title: RAG vs LLM Wiki 패턴 — 선택 가이드
type: topic
sources: [2026-karpathy-llm-wiki, 2026-geeknews-llm-wiki-topic]
updated: 2026-05-03
---

# RAG vs LLM Wiki 패턴 — 선택 가이드

가상 질의 "둘 중 언제 무엇을 써야 하나?"에 답하기 위해 [RAG](../concepts/rag.md), [LLM Wiki 패턴](../concepts/llm-wiki-pattern.md), [Farzapedia](../entities/farzapedia.md) 사례를 합성한 종합 페이지.

## 한 줄 결론

추론: **비용을 어디로 옮길 것인가의 선택**. 단발 질의·자주 바뀌는 코퍼스에는 [RAG](../concepts/rag.md), 같은 코퍼스에 반복적·누적적으로 깊게 들어가는 작업에는 [LLM Wiki 패턴](../concepts/llm-wiki-pattern.md).

## 핵심 차이 (한눈)

| 측면 | [RAG](../concepts/rag.md) | [LLM Wiki 패턴](../concepts/llm-wiki-pattern.md) |
|---|---|---|
| 비용 시점 | 질의 시 | 인제스트 시 (선불) |
| 지식 누적 | 없음 (매번 재발견) | 영속 (위키에 박힘) |
| 교차참조 | 질의 시점에 즉석 구성 | 미리 구축됨 |
| 모순 처리 | 무시·충돌 | 명시적 표시 |
| 답변 품질 한계 | 청크 검색 정확도 | 위키 페이지 품질 |
| 인프라 | 보통 벡터 DB | 마크다운 + git (~수백 페이지까지)[^gist] |
| 사람 부담 | 낮음 (한 번 업로드) | 인제스트 워크플로 운영 |

## 선택 기준

### RAG가 유리한 경우
- 코퍼스가 **빠르게 바뀜** (인제스트 비용을 회수하기 전에 폐기됨)
- **단발성 질의** 위주, 같은 자료를 반복 깊이 파지 않음
- 사용자가 **위키 운영에 시간을 쓸 의지/필요가 없음**
- 답이 보통 한두 청크 안에 들어 있음 (합성 깊이 낮음)

### LLM Wiki 패턴이 유리한 경우
- 같은 도메인을 **수개월~수년 누적**해서 깊이 파고듦 (리서치, 책, 회사 KB)
- 5개 이상 문서를 **합성**해야 풀리는 미묘한 질문이 자주 나옴[^gist]
- **모순 추적·교차참조**가 시간 갈수록 가치 있어지는 영역
- 결과물을 **사람이 읽거나 다른 도구로 쓰고 싶음** (마크다운/git 호환)
- 데이터 **소유권·이식성**이 중요 (BYOAI)[^geeknews]

## 실증 사례

[Farzapedia](../entities/farzapedia.md) 운영자 보고[^geeknews]: 1년 전 동일 도메인(개인 일기·노트)에서 RAG 기반으로 시도했으나 성능 미흡 → 에이전트가 파일 시스템을 직접 탐색하는 LLM Wiki 방식이 훨씬 효과적이었다. 새 항목 추가 시 관련 2~3개 기존 문서 자동 갱신·신규 생성.

추론: n=1 사례지만 "동일 도메인에서 두 방식을 직접 비교"한 보고이므로 가치 있음. 도메인이 "고유명사·관계·취향이 누적되는 개인 일상"이라 합성 깊이가 깊은 영역 — LLM Wiki 패턴의 강점이 잘 드러나는 케이스.

## 흔한 오해 정리

### "LLM Wiki는 사실상 RAG 아닌가?"
[GeekNews 토픽](../summaries/geeknews-llm-wiki-topic.md#사실상-rag임-논쟁) 댓글에서 제기[^geeknews]:
- RAG의 정의를 좁게(벡터 검색) 잡으면 LLM Wiki는 RAG 아님
- 넓게(외부 문서를 LLM 컨텍스트로 끌어오는 모든 방식) 잡으면 한 변종

추론: 차이의 본질은 **검색 대상의 성질**이다. RAG의 검색 대상은 원문 청크, LLM Wiki의 검색 대상은 LLM이 사전 합성·구조화한 페이지. "검색"이라는 동작은 같지만 검색되는 것이 다르다.

### "RAG는 임베딩이 필수다"
한 댓글이 명시적으로 반박[^geeknews]: grep만으로도 RAG는 가능. 임베딩은 RAG의 일반적 구현일 뿐 본질이 아님.

추론: 본 위키도 `index.md`를 LLM이 먼저 읽고 관련 페이지를 결정한다는 점에서 "텍스트 기반 retrieval-then-generate" — 넓은 의미의 RAG로 분류 가능.

## 둘을 섞는 전략

추론: 본 출처들에서는 명시적으로 다루지 않지만 자연스러운 하이브리드:
- **위키 우선, 부족하면 원문 RAG** — Lint에서 발견된 "데이터 공백"을 원문 검색으로 채움 (Karpathy도 lint 점검 항목에 "웹 검색으로 채울 수 있는 데이터 공백" 포함[^gist])
- **위키가 작을 때는 RAG, 임계 규모 넘으면 위키화** — 초기에 RAG로 빠르게 답하다가 같은 질문이 반복되면 그 답을 topic 페이지로 승격 (본 페이지가 그 예시)

## 본 저장소에서의 입장

본 저장소는 명시적으로 LLM Wiki 패턴 채택 ([PLAN.md](../../PLAN.md), [CLAUDE.md](../../CLAUDE.md)). 단, 패턴의 한계·비판은 [llm-wiki-pattern.md#비판들](../concepts/llm-wiki-pattern.md#비판들), [model-collapse.md](../concepts/model-collapse.md)에 정리해 두었으니 같이 참고.

[^gist]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
[^geeknews]: [`sources/articles/2026-geeknews-llm-wiki-topic.md`](../../sources/articles/2026-geeknews-llm-wiki-topic.md)
