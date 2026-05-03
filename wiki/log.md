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

## [2026-05-03] query→topic | RAG vs LLM Wiki 선택 가이드
- created: wiki/topics/rag-vs-llm-wiki.md
- updated: wiki/index.md
- query: "둘 중 언제 무엇을 써야 하나?"
- synthesis: rag.md + llm-wiki-pattern.md + farzapedia.md + 두 summary
- note: Phase 3 — 가상 질의의 답변을 재사용 가치가 있다고 판단해 topic 페이지로 승격. 이로써 첫 topic 페이지 등장.

## [2026-05-03] lint | 첫 정합성 점검 (8 페이지 대상)
- 점검 범위: wiki/concepts/*.md (3) + wiki/entities/*.md (2) + wiki/summaries/*.md (2) + wiki/topics/*.md (1)
- ✅ orphans: 없음 (모든 페이지 inbound 4~8개)
- ✅ broken internal links: 없음 (스크립트 점검)
- ✅ contradictions: 없음 (10~15 vs 2~3 fan-out 차이는 이미 추론으로 명시됨)
- ✅ 노후 진술: 없음
- ⚠️ 한국어 anchor 링크 (예: `#4가지-장점-karpathy-후속-발언`): 마크다운 렌더러에 따라 슬러그화가 다를 수 있음. 현재는 보고만.
- 📌 신규 페이지 후보:
  - `concepts/memex.md` 또는 `entities/vannevar-bush.md`: 두 출처 모두에서 핵심 사상 계보로 언급 (현재 외부 wikipedia 링크만)
  - `entities/licklider.md`: GeekNews 출처에서 언급. 현재 외부 링크만.
- action: 보고만. 자동 수정 없음 (CLAUDE.md §4.3 정책).

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
