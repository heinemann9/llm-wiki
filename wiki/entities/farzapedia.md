---
title: Farzapedia
type: entity
sources: [2026-geeknews-llm-wiki-topic]
updated: 2026-05-03
---

# Farzapedia

[LLM Wiki 패턴](../concepts/llm-wiki-pattern.md)의 대표적 실제 구현 사례. 일기·메모·메시지 2,500건을 입력으로 LLM이 400개의 상세 위키 문서를 자동 생성한 개인 위키[^src]. [Karpathy](karpathy.md)가 본인의 LLM Wiki 트윗에서 좋은 실사례로 인용[^src].

## 데이터 소스

입력으로 사용된 항목 ~2,500개[^src]:
- 일기
- Apple Notes
- iMessage 대화

## 구조

- 출력: 약 400개의 상세 위키 문서[^src]
- 다루는 범위: 친구, 스타트업, 관심 연구 분야, 좋아하는 애니메이션과 그 영향[^src]
- 백링크(backlink)로 상호 연결[^src]
- **위키는 개인 열람용이 아닌 에이전트가 활용하는 지식 베이스로 설계** — 파일 구조와 백링크가 에이전트가 크롤링하기 용이한 형태[^src]
- Claude Code를 위키에 연결, `index.md`를 진입점으로 삼아 쿼리 시 에이전트가 필요한 페이지를 직접 탐색[^src]

추론: "사람이 보는 위키"가 아니라 "에이전트가 읽는 위키"라는 설계 결정은 [LLM Wiki 패턴](../concepts/llm-wiki-pattern.md) 원안에서는 명시되지 않은 비틀기. Karpathy 원안은 "사람이 Obsidian으로 본다"가 전제였음.

## 활용 예시 [^src]

새 랜딩 페이지 작업 시 "최근 영감을 받은 이미지와 영화를 참고해 카피와 디자인 아이디어를 줘"라고 요청하면, 에이전트가 다음을 종합해 답변:
- Studio Ghibli 다큐 기반 "철학" 문서
- YC 기업 랜딩 페이지 스크린샷이 담긴 "경쟁사" 문서
- 저장해둔 1970년대 Beatles 굿즈 이미지

## RAG에서 위키로 전환한 경험 [^src]

> 1년 전 RAG 기반으로 유사 시스템을 구축했으나 성능이 좋지 않았고, 에이전트가 파일 시스템을 통해 직접 탐색하는 방식이 훨씬 효과적

→ [RAG 페이지](../concepts/rag.md)에 실증 사례로 인용 가능.

## 자동 갱신 동작 [^src]

새 항목(기사, 영감 이미지, 미팅 노트 등) 추가 시:
- 관련 2~3개 기존 문서를 자동 업데이트
- 또는 새 문서 생성

추론: Karpathy 원안의 "1 source → 10~15 페이지 갱신"보다 보수적. 데이터 도메인(개인 일상 vs 기술 문서)에 따라 페이지 fan-out이 다를 수 있다는 시사점.

## 메타

추론: "Farzapedia"는 운영자 개인 이름(Farza?)에서 따온 듯. 외부 공개 URL이나 코드 저장소는 본 원문에서 확인되지 않음 — 트위터 게시물 기반 인용으로 보임. 추후 출처 인제스트 시 보강.

[^src]: [`sources/articles/2026-geeknews-llm-wiki-topic.md`](../../sources/articles/2026-geeknews-llm-wiki-topic.md) (xguru 댓글 인용)
