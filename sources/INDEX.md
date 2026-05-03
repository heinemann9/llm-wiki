# Sources Index

`sources/` 에 들어온 모든 원문의 메타데이터.

형식:
```
## <source-id>
- title: <원문 제목>
- type: article | paper | note
- url: <원문 URL>
- ingested: YYYY-MM-DD
- path: sources/articles/<file>
- summary: wiki/summaries/<slug>.md
```

> source-id 규칙: `<연도>-<짧은-슬러그>` (예: `2025-karpathy-llm-wiki`)

---

## 2026-geeknews-llm-wiki-topic
- title: LLM-Wiki - LLM을 활용하여 개인 지식저장소 구축 하기
- type: article (community discussion)
- url: https://news.hada.io/topic?id=28208
- posted_by: GN+ (neo)
- posted_at: ~2026-04-04 (29일전, 2026-05-03 fetch 기준)
- ingested: 2026-05-03
- path: sources/articles/2026-geeknews-llm-wiki-topic.md
- summary: wiki/summaries/geeknews-llm-wiki-topic.md
- note: Karpathy gist (`2026-karpathy-llm-wiki`)의 한국어 정리 + 댓글(한국 + HN 번역). 본문은 원 gist와 거의 동일하나, Karpathy 후속 발언·비판·실구현 사례·사상 계보 추가가 새 정보.

## 2026-karpathy-llm-wiki
- title: LLM Wiki
- type: article (gist)
- url: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- author: Andrej Karpathy
- ingested: 2026-05-03
- path: sources/articles/2026-karpathy-llm-wiki.md
- summary: wiki/summaries/karpathy-llm-wiki.md
