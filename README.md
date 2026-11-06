# prompt-refiner

사용자의 거친 요청, 메모, 질문을 다른 AI가 바로 처리할 수 있는 프롬프트로 다듬는 에이전트 스킬입니다. Claude Code, Codex CLI, Antigravity, Gemini CLI, Copilot CLI에서 같은 파일로 동작합니다.

## 무엇을 하나

- 요청에서 목표, 대상 AI, 채널, 독자, 입력 자료, 출력 형식을 추출합니다.
- 답에 따라 결과물이 달라지는 것만 확인 질문합니다(최대 3개, 0개도 가능).
- 짧은 요청은 사용자 말을 그대로 둔 산문형 프롬프트로, 긴 요청은 슬롯형 프롬프트로 만듭니다.
- 이미지·영상 모델은 positive / negative / 설정 형식으로, 스타일 미지정 시 2~3종을 냅니다.

## 설치

원본은 한 곳에 두고 각 도구의 스킬 폴더에서 심볼릭 링크로 연결하는 방식을 권합니다.

```bash
git clone https://github.com/ts-ha/prompt-refiner.git ~/.claude/skills/prompt-refiner

# Codex CLI
mkdir -p ~/.codex/skills && ln -s ~/.claude/skills/prompt-refiner ~/.codex/skills/prompt-refiner
# 크로스 런타임 (Codex, Copilot CLI, Gemini CLI)
mkdir -p ~/.agents/skills && ln -s ~/.claude/skills/prompt-refiner ~/.agents/skills/prompt-refiner
# Antigravity
mkdir -p ~/.gemini/config/skills && ln -s ~/.claude/skills/prompt-refiner ~/.gemini/config/skills/prompt-refiner
```

Claude Code는 `~/.claude/skills/` 아래 폴더를 자동 인식합니다. `/prompt-refiner`로 호출하거나 "프롬프트 써줘", "질문 다듬어줘"처럼 요청하면 됩니다.

## 사용 예

```
재택근무 집중 어떻게 해? 이거 AI한테 물어볼 프롬프트로 만들어줘
```

응답은 네 부분입니다. 의도 요약과 보완 포인트, 복사해 쓸 정제 프롬프트, 가정, 확인 질문. 질문에 답하면 바뀐 부분만 요약하고 프롬프트 전체를 다시 출력합니다.

## 파일

- `SKILL.md` — 스킬 본문
- `references/example.md` — 출력 모양 예시(형식 참고용)
- `tests/cross-tool-test-prompt.md` — 도구별 형식 준수 테스트 지시문
- `tests/vague-set.md` — 애매한 질문 8개와 의도 카드(결과물 A/B용)
- `tests/ab-results.md` — 블라인드 A/B 누적 기록

## 검증 방식

실행 모델(Sonnet)이 원문 요청과 정제 프롬프트로 각각 결과물을 만들고, 판정 모델(Opus)이 조건을 모르는 상태에서 5항목 25점으로 채점합니다. 애매한 질문 세트에서는 "원문 + 사용자 답변 그대로"를 대조군으로 추가해, 효과가 질문에서 오는지 구조화에서 오는지 분리합니다. 기록은 `tests/ab-results.md`에 있습니다.
