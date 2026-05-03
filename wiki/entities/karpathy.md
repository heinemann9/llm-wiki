---
title: Andrej Karpathy
type: entity
sources: [2026-karpathy-llm-wiki, 2026-geeknews-llm-wiki-topic]
updated: 2026-05-03
---

# Andrej Karpathy

본 위키 저장소의 원형 아이디어인 [LLM Wiki 패턴](../concepts/llm-wiki-pattern.md)을 gist로 공개한 인물[^gist].

## 본 저장소와의 관계

2026년경 작성된 것으로 추정되는 gist "[LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)"에서 패턴 제안 → 본 저장소가 그 instantiation 중 하나[^gist].

gist 공개 시점에 본인은 "**코드보다 개인 지식 저장소 구축에 토큰을 더 많이 쓰고 있다**"고 언급[^geeknews] — 본인이 이 패턴을 실제로 메인 워크로드로 돌리고 있음을 시사.

## 작업 방식 (gist에 명시)

LLM 에이전트와 [Obsidian](https://obsidian.md)을 양옆에 띄워두고, LLM이 대화에 따라 편집하는 동안 본인은 결과를 실시간으로 브라우징(링크 따라가기, graph view 확인, 갱신된 페이지 읽기)[^gist]. 본인 표현으로 "Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase"[^gist].

원문 인제스트는 1건씩 사람이 개입하는 방식 선호 — 요약을 읽고 갱신을 확인하며 LLM에게 강조점을 지시[^gist].

## 4가지 장점 (Karpathy 후속 발언)

[Farzapedia](farzapedia.md)를 LLM Wiki 트윗의 좋은 실사례로 인용하면서, "사용할수록 알아서 좋아진다"는 **암묵적 AI 개인화**와 비교해 본 패턴의 장점을 4가지로 정리[^geeknews]:

| 항목 | 의미 |
|---|---|
| **명시성 (Explicit)** | 메모리 결과물이 위키 형태로 명확히 존재. AI가 무엇을 알고 모르는지 직접 확인·관리 가능. 지식이 불투명한 시스템 내부에 묻히지 않고 눈에 보이는 형태. |
| **데이터 소유권 (Yours)** | 데이터가 특정 AI 제공업체 시스템이 아닌 로컬 컴퓨터에 저장. 추출 불가능한 형태로 잠기지 않음. 정보에 대한 완전한 통제권 유지. |
| **파일 우선 (File over app)** | 메모리가 마크다운·이미지 등 범용 포맷의 파일 모음. 다양한 도구·CLI와 호환. 에이전트가 Unix 툴킷 전체 적용 가능. Obsidian 등 원하는 인터페이스로 열람. |
| **AI 선택 자유 (BYOAI)** | Claude, Codex, OpenCode 등 원하는 AI 자유 연결. 오픈소스 AI를 위키로 파인튜닝해 데이터 참조하는 것을 넘어, **가중치 자체에 개인 지식을 내재화하는 것도 원칙적으로 가능**. |

## 인용할 만한 발언

> "에이전트 활용 능력(agent proficiency)은 21세기의 핵심 스킬"[^geeknews]

영어로 지시하면 컴퓨터 작업을 대신 처리하는 도구를 직접 경험해볼 것을 권장[^geeknews].

## Karpathy의 GeekNews 댓글 논란

GeekNews 토픽에 인용된 HN 댓글 중[^geeknews]:
> Karpathy의 댓글이 플래그로 사라졌는데, Claude가 쓴 장문의 조롱 섞인 문단을 그대로 붙여넣은 형태였음

추론: HN 측 일화로, 본 위키 컨텍스트와는 직접 연관 없음. 다만 "AI 출력을 인간 발화로 그대로 사용하는 것의 사회적 마찰"이라는 본 패턴과 인접한 주제라 기록.

## 메타

추론: 일반 정보(소속·약력·다른 작업 — Stanford, OpenAI, Tesla 등 — 은 학습 데이터로 알고 있지만 본 위키 출처에 등장하지 않음)는 추후 다른 출처가 인제스트되면 확장.

[^gist]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
[^geeknews]: [`sources/articles/2026-geeknews-llm-wiki-topic.md`](../../sources/articles/2026-geeknews-llm-wiki-topic.md)
