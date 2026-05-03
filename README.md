# llm-wiki

LLM이 직접 작성·유지하는 영속적 마크다운 위키. [Andrej Karpathy의 LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)에서 제안된 패턴을 한국어 환경에 instantiation 한 것.

매번 원문에서 정보를 재추출하는 [RAG](wiki/concepts/rag.md) 대신, **한 번 정리한 지식을 누적·교차참조하여 재사용**한다. 위키 유지의 가장 큰 장벽이었던 북키핑(bookkeeping) 비용을 LLM이 거의 0으로 만든다.

## 아키텍처 (3계층)

```
sources/   ← 원본 (불변, LLM은 읽기만)
wiki/      ← LLM이 작성·갱신
CLAUDE.md  ← 사람이 정의한 운영 규칙 (스키마)
```

자세한 설계는 [PLAN.md](PLAN.md), 운영 규칙은 [CLAUDE.md](CLAUDE.md) 참고.

## 폴더 구조

```
llm-wiki/
├── PLAN.md                  설계 문서
├── CLAUDE.md                LLM 운영 규칙
├── README.md                이 문서
├── sources/                 원본 (불변)
│   ├── INDEX.md             원문 메타데이터
│   ├── articles/
│   ├── papers/
│   └── notes/
└── wiki/                    LLM 작성
    ├── index.md             전체 페이지 카탈로그 (질의 진입점)
    ├── log.md               변경 이력 (시간순 append-only)
    ├── concepts/            개념 페이지
    ├── entities/            인물·기업·제품 페이지
    ├── topics/              종합·합성 페이지
    └── summaries/           원문별 1:1 요약
```

## 핵심 작업

- **Ingest** — `sources/` 에 원문 추가 → LLM이 읽고 요약 + 관련 개념/개체 페이지 갱신 + 인덱스/로그 갱신
- **Query** — 위키 기반 질의응답. 답변이 재사용 가치 있으면 `wiki/topics/` 페이지로 승격
- **Lint** — 모순·고아 페이지·누락 교차참조·노후 진술 점검 (보고만, 자동 수정 없음)

각 워크플로 상세는 [CLAUDE.md §4](CLAUDE.md) 참고.

## 페이지 규약

모든 위키 페이지는 frontmatter로 시작:

```yaml
---
title: <페이지 제목>
type: concept | entity | topic | summary
sources: [<source-id>, ...]
updated: YYYY-MM-DD
---
```

본문 규칙:
- 사실 진술 끝에 `[^source-id]` 출처 표시
- 다른 페이지 언급 시 마크다운 링크
- 추측·해석은 명시적 `추론:` 표시
- 날짜는 절대 표기 (`YYYY-MM-DD`)

## 시작하기

이 저장소는 Claude Code로 운영하도록 설계됨. 다른 LLM 에이전트(Codex, OpenCode 등)도 `CLAUDE.md`를 `AGENTS.md`로 복제·조정해 사용 가능.

새 원문 인제스트 예시:
```
1. sources/articles/ 에 원문 파일 추가
2. Claude Code에 "이거 인제스트해줘" 요청
3. LLM이 CLAUDE.md §4.1 워크플로에 따라 처리
```

## 진행 단계

- [x] **Phase 0** — 스켈레톤 (폴더 구조 + 운영 규칙)
- [x] **Phase 1** — 첫 인제스트 (Karpathy gist)
- [x] **Phase 2** — 누적 검증 (GeekNews 토픽 — 갱신 패턴 확인)
- [x] **Phase 3** — Lint + Query→Topic 승격 정착
- [ ] 다른 도메인 원문 추가, 100 페이지 규모 운영 검증

상세는 [wiki/log.md](wiki/log.md) 참고.

## 라이선스

원본 아이디어: [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
이 저장소의 위키 콘텐츠: 인용된 원문의 라이선스 각각 따름. 원문은 `sources/` 에 보존.
