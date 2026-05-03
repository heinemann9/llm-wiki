---
title: 모델 붕괴 (Model Collapse)
type: concept
sources: [2026-geeknews-llm-wiki-topic]
updated: 2026-05-03
---

# 모델 붕괴 (Model Collapse)

LLM이 자신이 생성한 출력으로 다시 학습되는 과정이 반복되면서 **품질이 누적적으로 저하되는 현상**[^geeknews]. Nature 논문에서 관측된 패턴으로 인용됨[^geeknews] (정확한 논문 메타는 본 원문 범위 밖).

추론: 일반적으로 알려진 phenomenon은 Shumailov et al. "The Curse of Recursion" 계열 — 합성 데이터를 다시 학습 데이터로 쓰는 재귀가 분포 꼬리(rare events)를 잃게 만든다는 결과. 이 위키에 별도 출처가 들어오면 그때 페이지 확장.

## [LLM Wiki 패턴](llm-wiki-pattern.md)에서의 적용 논쟁

[GeekNews 토픽](../summaries/geeknews-llm-wiki-topic.md)의 핵심 비판 중 하나는 LLM Wiki 패턴이 모델 붕괴를 야기할 것이라는 우려[^geeknews]:

> "LLM이 문서를 작성할수록 기존의 정확한 정보를 점점 덜 간결하게 재작성하는 식으로 품질이 누적 저하됨"[^geeknews]

이에 대한 **반박**[^geeknews]:
> 그 글은 LLM을 **훈련하는** 게 아니라, 이미 학습된 모델을 이용해 **개인용 위키를 작성**하는 내용임. 모델 가중치는 변하지 않음.

추론: 엄밀히 말해 모델 붕괴는 **학습 루프**에서 정의되는 현상이고, [LLM Wiki 패턴](llm-wiki-pattern.md)은 가중치를 건드리지 않는다 → 정의상 동일 현상은 아님.

다만 **유사한 우려는 남는다**:
- 위키 자체가 LLM의 입력 컨텍스트로 반복 투입됨 → 위키에 부정확·과추상화된 진술이 들어가면, 다음 인제스트 시 LLM이 그 진술을 사실처럼 다루며 **2차 오류 누적** 가능[^geeknews]
- 이는 가중치 수준의 model collapse는 아니지만, **artifact 수준의 drift**라 부를 만함

이 우려를 완화하기 위해 본 저장소가 채택한 장치 (CLAUDE.md):
- 모든 사실 진술에 출처 표시 의무 (`[^source-id]`)
- 추측·해석은 명시적 "추론:" 표시
- 원문(`sources/`)은 불변 → 항상 1차 진실로 되돌아갈 수 있음
- Lint 워크플로에서 모순·노후 진술 점검

[^geeknews]: [`sources/articles/2026-geeknews-llm-wiki-topic.md`](../../sources/articles/2026-geeknews-llm-wiki-topic.md)
