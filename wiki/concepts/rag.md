---
title: RAG (Retrieval-Augmented Generation)
type: concept
sources: [2026-karpathy-llm-wiki, 2026-geeknews-llm-wiki-topic]
updated: 2026-05-03
---

# RAG (Retrieval-Augmented Generation)

질의 시점에 원문 컬렉션에서 관련 청크를 검색해 LLM에 컨텍스트로 주입하고 답변을 생성하는 방식[^gist].

## 전형적 흐름

문서 컬렉션 업로드 → 질의 시 관련 청크 검색 → LLM이 청크를 읽고 답변 생성[^gist]. NotebookLM, ChatGPT 파일 업로드 등 대다수 "LLM + 문서" 제품이 이 구조[^gist].

## [Karpathy](../entities/karpathy.md)가 지적한 한계

> "the LLM is rediscovering knowledge from scratch on every question. There's no accumulation."[^gist]

- 매 질의마다 LLM이 지식을 처음부터 재발견 → **누적 없음**[^gist]
- 5개 문서를 합성해야 풀리는 미묘한 질문이면, 매번 관련 조각을 다시 찾고 짜맞춰야 함[^gist]
- 교차참조·모순 표시·종합 — 어느 것도 영속화되지 않음[^gist]

## 실증 사례 — RAG에서 [LLM Wiki 패턴](llm-wiki-pattern.md)으로 전환

[Farzapedia](../entities/farzapedia.md) 운영자 보고[^geeknews]:
> 1년 전 RAG 기반으로 유사 시스템을 구축했으나 성능이 좋지 않았고, 에이전트가 파일 시스템을 통해 직접 탐색하는 방식이 훨씬 효과적

추론: n=1 사례지만 동일 도메인(개인 노트)에서 두 방식을 직접 비교한 보고라 가치 있음.

## "사실상 RAG임" 논쟁

[GeekNews 토픽](../summaries/geeknews-llm-wiki-topic.md#사실상-rag임-논쟁)에서 제기된 논쟁[^geeknews]:

**RAG로 보는 입장**:
> 벡터 DB는 없지만, 의미적 연결 인덱스를 만들고 계층적 구조를 구성해 검색을 돕는 점에서 동일

**구분되어야 한다는 반박**:
> LLM이 위키를 직접 작성·유지하며 백링크를 만들고 불일치를 검사함. 이는 검색이 아니라 **지식 합성**에 가까움. 마치 LLM이 스스로 [Zettelkasten](https://en.wikipedia.org/wiki/Zettelkasten)을 관리하는 느낌

추론: 이 논쟁은 RAG의 정의 폭에 달림.
- 좁게(벡터 검색 기반 retrieval-then-generate)로 정의하면 [LLM Wiki 패턴](llm-wiki-pattern.md)은 RAG가 아님
- 넓게(외부 문서를 LLM 컨텍스트로 끌어오는 모든 방식)로 정의하면 LLM Wiki도 RAG의 한 변종 — `index.md`를 읽고 관련 페이지를 selectively 끌어오는 구조이므로
- 핵심 차이는 **인덱스 구조와 페이지 자체가 LLM이 만든 합성물이라는 점** → "검색 대상이 LLM 사전 합성된 결과"

추가로:
> RAG는 임베딩이 필수는 아님. 단순히 grep 검색으로도 충분히 구현 가능함[^geeknews]

→ "RAG = 벡터 DB"라는 좁은 통념도 흔하지만, 본질은 retrieval + generation이지 임베딩이 본질은 아니라는 지적.

## 정리: RAG와 LLM Wiki는 어떻게 봐야 하나

추론: 두 패턴은 배타적이라기보다 **비용을 어디로 옮길 것인가**의 선택에 가깝다 — RAG는 질의 시점에, LLM Wiki는 인제스트 시점에 비용을 청구. 단발성 질의·자주 바뀌는 코퍼스에는 RAG가, 같은 코퍼스에 반복적·누적적으로 깊게 들어가는 작업에는 LLM Wiki가 적합.

[^gist]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
[^geeknews]: [`sources/articles/2026-geeknews-llm-wiki-topic.md`](../../sources/articles/2026-geeknews-llm-wiki-topic.md)
