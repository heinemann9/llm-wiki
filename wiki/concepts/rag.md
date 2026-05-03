---
title: RAG (Retrieval-Augmented Generation)
type: concept
sources: [2026-karpathy-llm-wiki]
updated: 2026-05-03
---

# RAG (Retrieval-Augmented Generation)

질의 시점에 원문 컬렉션에서 관련 청크를 검색해 LLM에 컨텍스트로 주입하고 답변을 생성하는 방식[^src].

## 전형적 흐름

문서 컬렉션 업로드 → 질의 시 관련 청크 검색 → LLM이 청크를 읽고 답변 생성[^src]. NotebookLM, ChatGPT 파일 업로드 등 대다수 "LLM + 문서" 제품이 이 구조[^src].

## [Karpathy](../entities/karpathy.md)가 지적한 한계

> "the LLM is rediscovering knowledge from scratch on every question. There's no accumulation."[^src]

- 매 질의마다 LLM이 지식을 처음부터 재발견 → **누적 없음**[^src]
- 5개 문서를 합성해야 풀리는 미묘한 질문이면, 매번 관련 조각을 다시 찾고 짜맞춰야 함[^src]
- 교차참조·모순 표시·종합 — 어느 것도 영속화되지 않음[^src]

## 대안 — [LLM Wiki 패턴](llm-wiki-pattern.md)

Karpathy는 RAG의 대안으로 LLM이 영속적 위키를 점진적으로 작성·유지하는 패턴을 제안[^src]. 비용을 **질의 시점**(RAG)에서 **인제스트 시점**(Wiki)으로 옮기는 것이 핵심.

추론: RAG가 틀렸다는 주장은 아님. 단발성 질의에는 RAG도 충분하다. 차이는 "같은 코퍼스에 반복적·누적적으로 깊이 들어가는" 사용 패턴에서 드러난다.

[^src]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
