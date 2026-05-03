# Change Log

위키의 모든 변경 내역. 최신이 위.

형식:
```
## YYYY-MM-DD — <workflow>: <요약>
- created: wiki/concepts/foo.md
- updated: wiki/entities/bar.md, wiki/index.md
- source: <source-id>
```

---

## [2026-05-03] ingest | GeekNews — LLM-Wiki 토픽 (본문 + 댓글)
- source: `2026-geeknews-llm-wiki-topic` (`sources/articles/2026-geeknews-llm-wiki-topic.md`)
- created: wiki/summaries/geeknews-llm-wiki-topic.md
- created: wiki/concepts/model-collapse.md
- created: wiki/entities/farzapedia.md
- updated: wiki/concepts/llm-wiki-pattern.md (4가지 장점, 비판 5종, Licklider 계보, 실구현 사례)
- updated: wiki/concepts/rag.md ("사실상 RAG임" 논쟁, Farzapedia 전환 사례)
- updated: wiki/entities/karpathy.md (4가지 장점, "토큰 더 씀" 발언, "21세기 핵심 스킬" 발언)
- updated: wiki/index.md, sources/INDEX.md
- note: Phase 2 검증 — 같은 주제 두 번째 원문이 들어오면서 기존 페이지 3개가 갱신되고 신규 3개가 추가되는 흐름 확인. 비판·실구현·사상 계보 등 새 축이 기존 패턴 페이지에 layering 됨.

## [2026-05-03] ingest | Karpathy — LLM Wiki gist
- source: `2026-karpathy-llm-wiki` (`sources/articles/2026-karpathy-llm-wiki.md`)
- created: wiki/summaries/karpathy-llm-wiki.md
- created: wiki/concepts/llm-wiki-pattern.md
- created: wiki/concepts/rag.md
- created: wiki/entities/karpathy.md
- updated: wiki/index.md, sources/INDEX.md
- note: 본 저장소의 원형 아이디어 자체를 첫 인제스트로 기록 (자기참조적 시작)

## 2026-05-03 — bootstrap: Phase 0 스켈레톤
- created: CLAUDE.md, wiki/index.md, wiki/log.md, sources/INDEX.md
- created: 폴더 구조 (sources/{articles,papers,notes}, wiki/{concepts,entities,topics,summaries}, scripts/)
