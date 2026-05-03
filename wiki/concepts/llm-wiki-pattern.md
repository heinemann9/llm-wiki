---
title: LLM Wiki 패턴
type: concept
sources: [2026-karpathy-llm-wiki, 2026-geeknews-llm-wiki-topic]
updated: 2026-05-03
---

# LLM Wiki 패턴

LLM이 원본 문서와 사용자 사이에 위치한 **영속적·구조화된 마크다운 위키**를 점진적으로 작성·유지하는 지식 관리 패턴[^gist]. [Karpathy](../entities/karpathy.md)가 gist 형태로 제안.

## 핵심 정의

> "the LLM **incrementally builds and maintains a persistent wiki** — a structured, interlinked collection of markdown files that sits between you and the raw sources"[^gist]

지식이 **한 번 컴파일되어 계속 최신 상태로 유지**된다는 점이 정의적 특징[^gist].

## [RAG](rag.md)와의 차이

| 측면 | RAG | LLM Wiki 패턴 |
|---|---|---|
| 지식 누적 | 없음 (질의마다 재발견) | 있음 (위키에 영속) |
| 답변 생성 | 청크 검색 → 합성 | 위키 페이지 검색 → 합성 |
| 교차참조 | 질의 시점에 재구성 | 위키에 미리 박힘 |
| 모순 처리 | 보통 무시/충돌 | 위키에 명시적으로 표시 |
| 비용 시점 | 질의 시 | 인제스트 시 (선불) |

> "The wiki is a persistent, compounding artifact. The cross-references are already there. The contradictions have already been flagged."[^gist]

## 구성 요소 (3계층)

1. **Raw sources** — 불변. LLM은 읽기만[^gist]
2. **The wiki** — LLM 소유. 요약·개체·개념·비교·종합 페이지[^gist]
3. **The schema** — `CLAUDE.md`/`AGENTS.md`. 위키 구조·관례·워크플로 정의. 사람과 LLM이 함께 진화시킴[^gist]

## 핵심 작업 (Operations)

- **Ingest**: 원문 1건 → 보통 10~15개 위키 페이지 갱신[^gist]
  - 추론: [Farzapedia](../entities/farzapedia.md)는 "2~3개 갱신"으로 보고 → 도메인(개인 일상 vs 기술 문서)에 따라 fan-out 다를 수 있음
- **Query**: 답변이 가치 있으면 위키 페이지로 다시 파일링 (탐구 자체도 누적)[^gist]
- **Lint**: 모순·노후·고아·누락 교차참조·공백 점검[^gist]

## 작동 원리: 북키핑 비용 → 0

추론: 이 패턴이 작동하는 본질적 이유는 위키 유지의 가장 큰 장벽이 "북키핑 비용"이라는 인식. 사람은 교차참조 갱신·요약 최신화·일관성 유지 같은 단순 반복 작업의 부담이 가치 증가보다 빨라서 위키를 포기한다[^gist]. LLM은 지루해하지 않고 한 번에 다수 파일을 건드릴 수 있어 유지비용이 0에 수렴 → 위키가 살아남는다[^gist].

## 4가지 장점 (Karpathy 후속 발언)

[Karpathy](../entities/karpathy.md)가 [Farzapedia](../entities/farzapedia.md)를 인용하며 정리한 본 패턴의 차별점[^geeknews]:

- **명시성 (Explicit)** — 메모리가 위키 형태로 가시화되어, AI가 무엇을 알고 모르는지 직접 확인·관리 가능[^geeknews]
- **데이터 소유권 (Yours)** — 로컬 저장, 특정 AI 제공업체에 잠기지 않음[^geeknews]
- **파일 우선 (File over app)** — 마크다운/이미지 등 범용 포맷 → Unix 툴킷 전체 적용 가능[^geeknews]
- **AI 선택 자유 (BYOAI)** — 모델 자유 교체. 위키로 파인튜닝하거나 가중치에 내재화도 가능[^geeknews]

자세한 표는 [Karpathy 페이지](../entities/karpathy.md#4가지-장점-karpathy-후속-발언) 참조.

## 인덱싱 전략

- `index.md`: 콘텐츠 카탈로그 (질의 시 1차 진입점)[^gist]
- `log.md`: 시간순 append-only (`## [YYYY-MM-DD] ingest | Title` 형식이면 grep 가능)[^gist]
- 약 100 sources / 수백 페이지 규모까지는 **임베딩 RAG 인프라 불필요**[^gist]
- 그 이상에서는 [qmd](https://github.com/tobi/qmd) 같은 BM25/벡터 하이브리드 검색 도입[^gist]
  - 한국어 환경에서는 BM25 약점 보완 필요 ([seCall](../summaries/geeknews-llm-wiki-topic.md#secall--kurthong-댓글) 사례)[^geeknews]

## 사상적 계보

본 패턴은 두 개의 정신적 선조와 정렬됨:

- **Vannevar Bush — [Memex](https://en.wikipedia.org/wiki/Memex) (1945)**[^gist]: 개인적·능동적으로 큐레이션되며, 문서 간 연결이 문서 자체만큼 중요한 지식 저장소. Bush가 풀지 못한 "누가 유지하는가" 문제의 LLM 시대 해답.
- **J.C.R. Licklider — "Man-Computer Symbiosis" (1960)**[^geeknews]: 지능 증폭(Intelligence Amplification) 개념. 인간이 목표 설정, 컴퓨터가 가설 검증·반복 계산 담당. 본 패턴의 사용자/LLM 역할 분담과 정확히 정렬.

## 적용 가능 영역

저자가 명시한 예시[^gist]:
- Personal knowledge base (목표·건강·자기계발)
- 장기 리서치 (수개월짜리 주제 심화)
- 책 읽기 동반 위키 (인물·테마·플롯)
- 팀 내부 위키 (Slack·미팅 자동 통합)
- 경쟁 분석, due diligence, trip planning 등

실제 구현 사례:
- [Farzapedia](../entities/farzapedia.md) — 개인 일기·메모·메시지 → 자기 위키[^geeknews]
- [seCall](../summaries/geeknews-llm-wiki-topic.md#secall--kurthong-댓글) — 한국어 환경 + Obsidian/GitHub 동기화[^geeknews]

## 비판들

본 패턴에 제기된 주요 이의[^geeknews]:

### 1. [모델 붕괴](model-collapse.md) 우려
LLM이 만든 위키를 다시 LLM이 읽는 루프가 품질 저하를 일으킬 것이라는 우려. 엄밀히는 학습 루프가 아니므로 정의상 동일하지 않지만, **artifact 수준의 drift**는 가능. 자세한 분석은 [model-collapse.md](model-collapse.md).

### 2. "문제를 미루는 것"
- LLM이 매번 원본 대신 위키를 읽으면서 **2차 오류 누적**[^geeknews]
- 차세대 컨텍스트 (10M토큰)가 나오면 무의미해질 것
  - 반론: 1M 컨텍스트도 20만~30만 토큰에서 기억 손실 시작 → 컨텍스트 확장만으로 해결되지 않음[^geeknews]

### 3. LLM의 위키 유지 능력 자체에 대한 회의
- "LLM은 claude.md 하나도 제대로 유지 못함"[^geeknews]
- 코딩 프로젝트에서는 명확한 요구사항·반복 개선·잘 문서화된 코드가 더 효과적이고, 기억이 너무 많아지면 오류 증가[^geeknews]

### 4. "사고 정리 가치" 비판 ⭐
> 문서 작성의 진짜 가치는 결과물이 아니라, 작성 과정에서 사고가 정리되는 것임. LLM이 아무리 좋은 결과를 내도, 개인 위키에서는 그 과정이 더 중요함.[^geeknews]

추론: 이는 본 패턴의 가장 본질적 이의제기. "북키핑 비용 → 0"이 가치 그 자체였는지, 아니면 그 비용이 사고를 강제하는 메커니즘이었는지 묻는다. 패턴 옹호자라면 "쓰기 행위는 사람이, 정리는 LLM이"라는 분업이 가능하다고 답할 수 있지만, 비판자는 "분리할 수 없다"고 말함.

### 5. 인간 개입의 필수성
> 완전히 자율적인 자기참조형 시스템은 가치가 없다고 봄. 진짜 가치는 인간이 "이건 이렇게 작동해야 한다"고 개입할 수 있는 구조에 있음[^geeknews]

→ 본 저장소도 이 입장과 정렬: CLAUDE.md에 페이지 삭제 금지·승인제, 출처 표시 의무 등 인간 개입 지점 명시.

## 관련 시스템·도구

[GeekNews 토픽 요약](../summaries/geeknews-llm-wiki-topic.md#실제-구현-사례)의 "기타 언급된 시스템" 섹션 참조 — hmem, commonplace, llmdoc, atomic, DocMason, Binder, AS Notes, WUPHF 등.

추론: Copilot의 `copilot-instructions.md`, AGENTS.md 등 "에이전트 지침 문서" 일반화의 한 갈래로 볼 수도 있음 — 한 댓글이 이 비교를 제시[^geeknews].

## 본 저장소에서의 instantiation

이 위키 저장소가 패턴의 한 구체화. [PLAN.md](../../PLAN.md)와 [CLAUDE.md](../../CLAUDE.md)에 구조·규칙 명시.

[^gist]: [`sources/articles/2026-karpathy-llm-wiki.md`](../../sources/articles/2026-karpathy-llm-wiki.md)
[^geeknews]: [`sources/articles/2026-geeknews-llm-wiki-topic.md`](../../sources/articles/2026-geeknews-llm-wiki-topic.md)
