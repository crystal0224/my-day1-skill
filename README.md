# my-day1: 개인화 Claude Code 온보딩

사용자의 직업, 전공, 터미널 경험에 따라 예시와 설명을 자동으로 맞춤화하는 Claude Code 온보딩 스킬입니다.

## 사용법

```bash
/my-day1
```

처음 실행 시 3가지 질문에 답하면 자동으로 프로파일이 저장되고, 다음부터는 저장된 프로파일로 바로 시작됩니다.

## 특징

- **13개 미션 완전 구현**: Setup부터 Basics까지 전체 온보딩 커리큘럼
- **명령어 1번만**: `/my-day1` 입력 후 모든 게 자연스럽게 진행
- **3가지 질문**: 직업/전공/터미널 경험 (한 번에 답변)
- **맞춤 예시**: HR이면 인사 관련, 개발자면 코드 관련 예시
- **실무 실험 과제**: 각 미션마다 본인 업무로 실습
- **세션 간 유지**: 프로파일 영구 저장 (`~/.claude/memory/user-profile.md`)

## 구현된 미션

| 미션 | 주제 | 내용 |
|-------|------|------|
| 0 | Setup | Claude Code 설치 + 첫 대화 |
| 1 | Experience | Working Backward 데모 3가지 |
| 2 | Why | 왜 CLI? 왜 터미널? |
| 3-1 | CLAUDE.md | 프로젝트별 기억 저장 |
| 3-2 | Skill | 반복 작업 자동화 |
| 3-3 | MCP | 외부 도구 연동 |
| 3-4 | Subagent | 작업 위임 |
| 3-Break | 쉬어가기 | 터미널 소개 + Status Line |
| 3-5 | Agent Teams | 팀 협업 |
| 3-6 | Hook | 이벤트 자동화 |
| 3-7 | Plugin | 확장 기능 |
| 3-Summary | 요약 | 7개 기능 관계도 |
| 4 | Basics | CLI + Git + GitHub |

## 동작 흐름

1. `/my-day1` 실행
2. 프로파일 확인 (있으면 확인 질문, 없으면 프로파일링)
3. 미션 선택 (13개 미션 중 자유 선택)
4. Phase A: 개인화된 설명 + 실습 안내 (STOP)
5. 사용자 실습 후 "완료" 입력
6. Phase B: 퀴즈 + 실무 실험 과제

## 지원 직업군

| 직업군 | 비유 예시 | 실무 과제 예시 |
|--------|-----------|---------------|
| HR/인사 담당자 | "회사 인사 규정집" | 인사 업무 규칙을 CLAUDE.md에 추가 |
| 백엔드 개발자 | ".gitignore, 설정 파일" | 코딩 컨벤션을 CLAUDE.md에 저장 |
| UI/UX 디자이너 | "디자인 시스템 가이드" | 디자인 시스템 항목을 CLAUDE.md에 저장 |
| 제품 기획자/PM | "프로젝트 킥오프 문서" | 프로젝트 규칙을 CLAUDE.md에 저장 |
| 데이터 분석가 | "데이터 사전" | 분석 규칙을 CLAUDE.md에 저장 |
| 마케터/콘텐츠 제작자 | "브랜드 가이드라인" | 콘텐츠 규칙을 CLAUDE.md에 저장 |
| 일반 사무/관리 | "업무 매뉴얼" | 업무 규칙을 CLAUDE.md에 저장 |
| 연구원/학생 | "연구 노트 표지" | 공부 규칙을 CLAUDE.md에 저장 |

## 터미널 경험별 차이

| 경험 수준 | 차이점 |
|-----------|--------|
| 익숙해요 | 터미널 설명 생략, 기술적 비유 사용 |
| 해본 적 있어요 | 간단한 안내만 제공 |
| 처음이에요 | 터미널 여는 법부터 단계별 상세 설명 |

## 파일 구조

```
~/.claude/skills/my-day1/
├── SKILL.md              # 메인 스킬 파일 (프로파일링 + 미션 로직)
├── README.md             # 이 파일
├── TEST.md               # 테스트 시나리오
├── references/           # 미션별 콘텐츠 파일 (13개)
│   ├── block0-setup.md
│   ├── block1-experience.md
│   ├── block2-why.md
│   ├── block3-1-claude-md.md
│   ├── block3-2-skill.md
│   ├── block3-3-mcp.md
│   ├── block3-4-subagent.md
│   ├── block3-break.md
│   ├── block3-5-agent-teams.md
│   ├── block3-6-hook.md
│   ├── block3-7-plugin.md
│   ├── block3-summary.md
│   └── block4-basics.md
└── prompts/              # 동적 생성용 프롬프트 (선택)
```

## 테스트 직업군

- HR/인사 담당자 (터미널 처음)
- 백엔드 개발자 (터미널 익숙)
- UI/UX 디자이너 (터미널 해본 적 있음)

테스트 시나리오 상세: `TEST.md` 참조

## 참고

- 원본 스킬: `day1-onboarding`
- 설계 문서: `~/docs/plans/2026-02-15-personalized-onboarding-design.md`
- 구현 계획: `~/docs/plans/2026-02-15-my-day1.md` (Phase 1), `~/docs/plans/2026-02-15-my-day1-phase2.md` (Phase 2)
