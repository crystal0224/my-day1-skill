# Essential Path Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 비개발자가 my-day1 스킬을 끝까지 완료하도록 Essential Path (필수 7개 미션) 구현

**Architecture:**
- 13개 블록을 7개 필수 + 6개 심화로 재분류
- 각 미션에 동기부여 스토리, 용어 비유, 마일스톤 배지 추가
- 학습 경로 선택 시스템 (빠른 시작/전체/커스텀) 구현
- Help 시스템으로 막힘 방지

**Tech Stack:**
- Claude Code Skill (SKILL.md + references/)
- Markdown 콘텐츠
- 조건부 로직 (프로파일 기반 동적 생성)

---

## Task 1: 용어 변경 (Block → 미션)

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`
- Modify: `~/.claude/skills/my-day1/README.md`
- Modify: `~/.claude/skills/my-day1/references/*.md` (13개 파일)

**Step 1: SKILL.md에서 Block → 미션 변경**

```bash
cd ~/.claude/skills/my-day1
# Block을 미션으로 전체 치환 (단, Block 0, Block 1 같은 패턴도 유지)
sed -i '' 's/Block \([0-9]\)/미션 \1/g' SKILL.md
sed -i '' 's/Block \([0-9]-[0-9]\)/미션 \1/g' SKILL.md
sed -i '' 's/Block \([0-9]-[A-Za-z]*\)/미션 \1/g' SKILL.md
sed -i '' 's/블록/미션/g' SKILL.md
```

**Step 2: README.md 업데이트**

```bash
sed -i '' 's/블록/미션/g' README.md
sed -i '' 's/Block/미션/g' README.md
```

**Step 3: references/ 파일들 업데이트**

```bash
cd references/
for file in *.md; do
  sed -i '' 's/Block/미션/g' "$file"
  sed -i '' 's/블록/미션/g' "$file"
done
```

**Step 4: 변경 확인**

Run: `grep -r "Block" SKILL.md README.md references/`
Expected: 용어 설명 컨텍스트 외에는 "Block" 없어야 함

**Step 5: Commit**

```bash
git add SKILL.md README.md references/
git commit -m "refactor: Change 'Block' terminology to '미션' for non-technical users

Block → 미션 전체 변경으로 비개발자 친화성 향상

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 2: 필수/심화 미션 분류 및 메타데이터 추가

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`

**Step 1: 미션 메타데이터 정의**

SKILL.md 상단에 미션 분류 추가 (## 설계 섹션 아래):

```markdown
## 미션 분류

### 필수 미션 (Essential Path) - 7개

| 미션 | 주제 | 예상 시간 | 난이도 |
|------|------|----------|--------|
| 미션 0 | Setup | ⏱️ 5분 | ⭐ 쉬움 |
| 미션 1 | Experience | ⏱️ 10분 | ⭐ 쉬움 |
| 미션 3-1 | CLAUDE.md | ⏱️ 10분 | ⭐⭐ 보통 |
| 미션 3-2 | Skill | ⏱️ 15분 | ⭐⭐ 보통 |
| 미션 3-4 | Subagent | ⏱️ 10분 | ⭐⭐ 보통 |
| 미션 요약 | 7개 기능 요약 | ⏱️ 5분 | ⭐ 쉬움 |
| 미션 4 | Basics | ⏱️ 10분 | ⭐⭐ 보통 |

**총 예상 시간**: 1~1.5시간

### 심화 미션 (Advanced Path) - 6개

| 미션 | 주제 | 예상 시간 | 난이도 |
|------|------|----------|--------|
| 미션 2 | Why | ⏱️ 10분 | 🔷 심화 |
| 미션 3-3 | MCP | ⏱️ 15분 | 🔷 심화 |
| 미션 3-Break | 쉬어가기 | ⏱️ 5분 | 🔷 심화 |
| 미션 3-5 | Agent Teams | ⏱️ 15분 | 🔷 심화 |
| 미션 3-6 | Hook | ⏱️ 10분 | 🔷 심화 |
| 미션 3-7 | Plugin | ⏱️ 15분 | 🔷 심화 |
```

**Step 2: 각 미션 헤더에 태그 추가**

예시 (미션 0):
```markdown
## 미션 0: Setup (Phase A) ⭐ 필수 | ⏱️ 5분 | 💪 쉬움
```

예시 (미션 2):
```markdown
## 미션 2: Why (Phase A) 🔷 심화 | ⏱️ 10분 | 💪 보통
```

**Step 3: 모든 13개 미션 헤더 업데이트**

Run: `grep -n "^## 미션 [0-9]" SKILL.md`
Expected: 각 미션 헤더에 태그 추가 확인

**Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: Add mission classification and metadata

- 필수 7개 미션 (⭐) vs 심화 6개 미션 (🔷)
- 예상 시간 (⏱️) 및 난이도 (💪) 표시
- 비개발자가 목표를 명확히 파악 가능

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 3: 학습 경로 선택 시스템 구현

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`

**Step 1: 프로파일링 직후 학습 경로 선택 단계 추가**

SKILL.md의 "### 2단계: 온보딩 가이드 안내" 섹션 뒤에 추가:

```markdown
### 3단계: 학습 경로 선택

프로파일 확인 후, 사용자에게 학습 경로를 선택하도록 안내한다:

```json
AskUserQuestion({
  "questions": [{
    "question": "어떻게 시작하시겠어요?",
    "header": "학습 경로",
    "options": [
      {
        "label": "빠른 시작 (필수 7개만) ⭐ 추천",
        "description": "핵심만 배우고 바로 실무 적용 | 예상 시간: 1~1.5시간"
      },
      {
        "label": "전체 과정 (13개 전부)",
        "description": "모든 기능을 깊이 있게 학습 | 예상 시간: 2~3시간"
      },
      {
        "label": "특정 미션만 선택",
        "description": "필요한 것만 골라서 학습"
      }
    ],
    "multiSelect": false
  }]
})
```

**경로별 처리:**

- **빠른 시작**: 필수 7개 미션만 순서대로 진행 (0 → 1 → 3-1 → 3-2 → 3-4 → 요약 → 4)
- **전체 과정**: 13개 미션 순서대로 진행
- **특정 미션 선택**: 사용자가 미션 번호 입력 시 해당 미션으로 이동
```

**Step 2: 경로 상태 추적 변수 추가**

```markdown
### 학습 경로 상태

스킬 실행 중 아래 변수를 추적한다:

- `learning_path`: "essential" / "full" / "custom"
- `completed_missions`: 완료한 미션 번호 리스트 (예: [0, 1, 3-1])
- `current_mission`: 현재 진행 중인 미션
```

**Step 3: 미션 완료 시 다음 미션 자동 안내**

각 미션 Phase B 종료 후:

```markdown
**다음 미션 안내:**

[빠른 시작 경로인 경우]
- 완료: 미션 0 ✓, 미션 1 ✓ (2/7 필수 미션)
- 다음: 미션 3-1 (CLAUDE.md) - 프로젝트 규칙 저장
- 예상 시간: 10분

입력: "다음" 또는 "미션 3-1" → 자동으로 다음 미션 시작
```

**Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: Add learning path selection system

- 빠른 시작 / 전체 과정 / 커스텀 선택 가능
- 필수 7개 미션 자동 로드맵 제공
- 미션 완료 시 다음 미션 자동 안내

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 4: 동기부여 스토리 시스템 구현

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`
- Create: `~/.claude/skills/my-day1/references/motivation-stories.md`

**Step 1: 동기부여 스토리 템플릿 정의**

`references/motivation-stories.md` 생성:

```markdown
# 동기부여 스토리 템플릿

## 미션 0: Setup

### HR/인사 담당자
매번 똑같은 채용 이메일 보내는 데 지치셨나요?
Claude가 "우리 회사는 OO 인재상"을 기억하고 자동으로 써드립니다.

**실제 사례:**
김OO님(HR)은 Claude Code로 채용 공고 20개를 5분 만에 업데이트했습니다.
이전에는 복사-붙여넣기로 2시간 걸렸던 일입니다.

### 백엔드/프론트엔드/풀스택 개발자
코드 리뷰에서 "변수명 camelCase로", "console.log 지우세요" 반복 코멘트 지겹지 않으세요?
Claude가 팀 컨벤션을 기억하고 자동으로 체크합니다.

**실제 사례:**
이OO님(개발자)은 PR 리뷰 시간을 50% 단축했습니다.
Claude가 컨벤션 위반을 먼저 잡아주니까요.

### UI/UX 디자이너
디자인 시스템 문서 업데이트할 때마다 "이거 어디 있더라?" 찾는 거 귀찮으시죠?
Claude가 "우리 Primary 컬러는 #FF6B00"을 기억하고 바로 적용합니다.

**실제 사례:**
박OO님(디자이너)은 디자인 시스템 일관성을 100% 유지합니다.
Claude가 모든 컴포넌트에 규칙을 자동 적용하니까요.

### 제품 기획자/PM
프로젝트마다 "우리 타겟은 누구였지?" 확인하느라 회의록 뒤지는 거 지겹지 않으세요?
Claude가 "이 프로젝트 목표는 OO"을 기억하고 모든 결정을 거기 맞춥니다.

**실제 사례:**
최OO님(PM)은 의사결정 속도를 2배 높였습니다.
Claude가 프로젝트 맥락을 항상 기억하니까요.

### 데이터 분석가
분석할 때마다 "이 컬럼 의미가 뭐였지?" DB 스키마 찾는 거 시간 낭비 아닌가요?
Claude가 "user_id는 고유번호, created_at은 UTC"를 기억하고 정확히 분석합니다.

**실제 사례:**
정OO님(분석가)은 분석 오류를 90% 줄였습니다.
Claude가 데이터 사전을 완벽히 기억하니까요.

### 마케터/콘텐츠 제작자
캠페인마다 "우리 브랜드 톤앤매너가 뭐였지?" 가이드라인 확인하는 거 번거롭죠?
Claude가 "우리는 친근하게, 반말로"를 기억하고 일관된 카피를 씁니다.

**실제 사례:**
강OO님(마케터)은 콘텐츠 승인률을 80%로 올렸습니다.
Claude가 브랜드 가이드를 완벽히 따르니까요.

### 일반 사무/관리
업무마다 "이거 작년에 어떻게 했더라?" 찾느라 메일 뒤지는 거 지치셨죠?
Claude가 "연말정산은 이렇게"를 기억하고 매년 정확히 처리합니다.

**실제 사례:**
한OO님(관리)은 반복 업무 시간을 70% 줄였습니다.
Claude가 모든 절차를 기억하니까요.

### 연구원/학생
논문 쓸 때마다 "저번에 참고한 논문이 뭐였지?" 찾는 거 시간 낭비죠?
Claude가 "이 연구 방법론은 OO 논문 기반"을 기억하고 정확히 인용합니다.

**실제 사례:**
조OO님(연구원)은 논문 작성 속도를 3배 높였습니다.
Claude가 모든 레퍼런스를 기억하니까요.

---

[미션 1, 3-1, 3-2, 3-4, 요약, 4에 대해서도 동일한 구조로 작성]
```

**Step 2: SKILL.md에 동기부여 스토리 삽입 로직 추가**

각 필수 미션 Phase A 시작 직후:

```markdown
## 미션 0: Setup (Phase A) ⭐ 필수

---

📍 **현재 위치: 미션 0 (Setup) / 13 미션 중 첫 시작**
...

---

💡 **왜 이 미션을 해야 하나요?**

[직업별 동기부여 스토리 동적 삽입]

Read references/motivation-stories.md의 해당 직업 섹션을 읽고,
사용자 프로파일의 직업에 맞는 스토리를 출력한다.

**이 미션을 완료하면:**
✓ Claude Code를 내 컴퓨터에 설치할 수 있습니다
✓ 첫 대화를 시작할 수 있습니다
✓ CLAUDE.md 파일로 규칙을 저장할 준비가 됩니다

---

### 실행 흐름
...
```

**Step 3: motivation-stories.md 전체 작성**

필수 7개 미션 × 9개 직업 = 63개 스토리 작성

**Step 4: Commit**

```bash
git add SKILL.md references/motivation-stories.md
git commit -m "feat: Add motivation story system for essential missions

- 필수 7개 미션별로 직업별 동기부여 스토리 추가
- 실제 사례 (before/after) 제공으로 학습 동기 강화
- 완료 후 얻을 수 있는 것 명확히 제시

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 5: 용어 비유 시스템 구현

**Files:**
- Create: `~/.claude/skills/my-day1/references/glossary.md`
- Modify: `~/.claude/skills/my-day1/SKILL.md`

**Step 1: 용어 사전 파일 생성**

`references/glossary.md`:

```markdown
# 기술 용어 비유 사전

## CLI (Command Line Interface)

### 일반 사무/관리
Excel 수식처럼, 텍스트로 컴퓨터에 명령하는 방법입니다.
마우스 클릭 대신 키보드로 "이 파일 열어" 같은 명령을 칩니다.

### 개발자
GUI 대신 터미널에서 모든 걸 제어하는 인터페이스입니다.
`cd`, `ls`, `git` 같은 명령어를 사용합니다.

### 디자이너
Figma 단축키의 강력 버전입니다.
텍스트 명령어로 모든 작업을 빠르게 처리할 수 있습니다.

---

## 터미널 (Terminal)

### 일반 사무/관리
컴퓨터와 대화하는 텍스트 창입니다.
Mac의 "터미널" 앱 또는 Windows의 "명령 프롬프트"를 말합니다.

### 개발자
명령행 인터페이스(CLI)를 사용하는 애플리케이션입니다.
Shell(bash, zsh 등)을 실행합니다.

### 디자이너
코드를 직접 실행할 수 있는 검은 화면입니다.
개발자들이 맨날 쓰는 그 화면이에요.

---

[15개 주요 용어 × 9개 직업별 비유 = 135개 설명 작성]
```

**Step 2: SKILL.md에 용어 자동 비유 로직 추가**

```markdown
## 용어 설명 시스템

기술 용어가 처음 나올 때 자동으로 비유를 괄호 안에 삽입한다:

**동작 방식:**
1. 용어 첫 출현 감지: CLI, 터미널, git, commit, CLAUDE.md 등
2. 사용자 프로파일의 직업 확인
3. `references/glossary.md`에서 해당 직업의 비유 로드
4. 용어 뒤에 괄호로 비유 삽입

**예시:**
- 일반 사무: "CLI (Excel 수식처럼 텍스트로 명령하는 방법)를 사용해서..."
- 개발자: "CLI (GUI 대신 터미널에서 모든 걸 제어)를 사용해서..."
- 디자이너: "CLI (Figma 단축키의 강력 버전)를 사용해서..."

**용어 목록 (15개):**
CLI, 터미널, git, commit, CLAUDE.md, Skill, MCP, Subagent, Agent Teams, Hook, Plugin, Phase A/B, push, pull, branch
```

**Step 3: glossary.md 전체 작성**

15개 용어 × 9개 직업 = 135개 비유 작성

**Step 4: Commit**

```bash
git add SKILL.md references/glossary.md
git commit -m "feat: Add inline glossary system with job-specific analogies

- 15개 주요 기술 용어에 대한 비유 사전 추가
- 직업별로 친숙한 비유 사용 (Excel, Figma 등)
- 용어 첫 출현 시 자동으로 비유 삽입

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 6: 마일스톤 배지 시스템 구현

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`

**Step 1: 마일스톤 로직 정의**

SKILL.md에 추가:

```markdown
## 마일스톤 배지 시스템

### 배지 획득 조건

**🎯 기초 완료 (Beginner Badge)**
- 조건: 미션 0, 1, 3-1 완료 (3/7 필수 미션)
- 메시지:
  ```
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🎯 기초 완료!
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Claude Code 사용 준비 완료!
  이제 실무에 쓸 수 있어요.

  완료: 미션 0 ✓, 미션 1 ✓, 미션 3-1 ✓
  남은 필수: 미션 3-2, 3-4, 요약, 4 (4개)

  다음: 미션 3-2 (Skill) - 반복 작업 자동화
  예상 시간: 15분
  ```

**🏆 핵심 완료 (Core Badge)**
- 조건: 필수 7개 미션 모두 완료
- 메시지:
  ```
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🏆 핵심 완료!
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Claude Code 핵심 마스터!
  실무 투입 가능합니다!

  완료: 미션 0, 1, 3-1, 3-2, 3-4, 요약, 4 ✓

  🎉 축하합니다! 필수 과정을 모두 완료했습니다.

  다음 단계:
  1. 실무에 바로 적용해보세요
  2. 심화 미션 (6개)도 도전해보세요
     - 미션 2 (Why), 3-3 (MCP), 3-5 (Agent Teams) 등
  ```

**🚀 마스터 (Master Badge)**
- 조건: 13개 미션 모두 완료
- 메시지:
  ```
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🚀 마스터 등극!
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Claude Code 전문가!
  모든 기능을 자유자재로 사용할 수 있습니다!

  완료: 13개 미션 전체 ✓

  🎓 온보딩 완료! 이제 당신은 Claude Code 전문가입니다.
  ```
```

**Step 2: 미션 완료 후 배지 체크 로직 추가**

각 미션 Phase B 종료 후:

```markdown
### 미션 완료 처리

1. 완료한 미션을 `completed_missions` 리스트에 추가
2. 배지 조건 체크:
   - [0, 1, 3-1] 모두 포함 → 🎯 기초 완료 배지 표시
   - [0, 1, 3-1, 3-2, 3-4, 요약, 4] 모두 포함 → 🏆 핵심 완료 배지 표시
   - 13개 모두 완료 → 🚀 마스터 배지 표시
3. 다음 미션 안내 (학습 경로에 따라)
```

**Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: Add milestone badge system

- 🎯 기초 완료 (3개), 🏆 핵심 완료 (7개), 🚀 마스터 (13개)
- 각 배지 획득 시 축하 메시지 + 다음 단계 안내
- 진행 상황 명확히 표시로 동기부여 강화

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 7: Help 시스템 구현

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`

**Step 1: Help 명령어 처리 로직 추가**

SKILL.md에 추가:

```markdown
## Help 시스템

사용자가 막혔을 때 "help", "막혔어요", "도움" 입력 시:

### Help 메뉴

```json
AskUserQuestion({
  "questions": [{
    "question": "어디서 막히셨나요?",
    "header": "도움말",
    "options": [
      {
        "label": "명령어가 안 돼요",
        "description": "실습 중 오류가 나거나 명령어가 작동하지 않아요"
      },
      {
        "label": "무슨 말인지 모르겠어요",
        "description": "설명이나 용어가 이해가 안 돼요"
      },
      {
        "label": "이 미션 건너뛰고 싶어요",
        "description": "지금은 이 미션을 하고 싶지 않아요"
      },
      {
        "label": "왜 이걸 배워야 하는지 모르겠어요",
        "description": "이게 내 업무에 왜 필요한지 이해가 안 돼요"
      }
    ],
    "multiSelect": false
  }]
})
```

### 선택지별 처리

**1. 명령어가 안 돼요:**
```
화면에 나온 오류 메시지를 복사해서 보여주세요.
또는 스크린샷을 찍어서 공유해주세요.

[오류 메시지 확인 후]
→ 자동 진단 실행
→ 해결법 단계별 안내
→ 여전히 안 되면 "skip"으로 건너뛰기 제안
```

**2. 무슨 말인지 모르겠어요:**
```
어떤 부분이 이해가 안 되시나요?

[사용자 응답 후]
→ 더 쉬운 말로 다시 설명
→ 최근 나온 용어 3개 다시 비유로 설명
→ 필요하면 영상/그림 자료 추천
```

**3. 이 미션 건너뛰고 싶어요:**
```
이 미션은 건너뛰어도 됩니다!

"skip" 또는 "다음"을 입력하면 다음 미션으로 넘어갑니다.
나중에 다시 돌아와서 할 수도 있어요.

[빠른 시작 경로인 경우]
단, 이 미션은 필수 미션이라 나중에 꼭 하셔야
🏆 핵심 완료 배지를 받으실 수 있습니다.
```

**4. 왜 이걸 배워야 하는지 모르겠어요:**
```
[해당 미션의 추가 실제 사례 3가지 제시]

이 미션을 건너뛰면:
- OO 작업을 할 때 불편할 수 있습니다
- OO 기능을 사용할 수 없습니다

그래도 건너뛰시겠어요?
1. 계속 진행하기
2. 건너뛰기
```
```

**Step 2: 진행 규칙에 Help 안내 추가**

```markdown
## 진행 규칙

- 한 번에 한 미션씩 진행한다
- **자유롭게 건너뛰기 가능**: "skip", "다음", 미션 번호 입력
- **막혔을 때 "help" 입력**: 상황별 도움말 제공
- Claude Code 관련 질문이 오면 claude-code-guide 에이전트로 답변
- 프로파일 변경을 원하면 스킬 재시작 후 "다시 설정" 선택
```

**Step 3: Commit**

```bash
git add SKILL.md
git commit -m "feat: Add help system for stuck users

- 'help' 명령어로 4가지 상황별 도움말 제공
- 실습 오류, 이해 안 됨, 건너뛰기, 동기 부족 대응
- 막힘 방지로 중도 이탈 감소 기대

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 8: 재진입 플로우 구현

**Files:**
- Modify: `~/.claude/skills/my-day1/SKILL.md`

**Step 1: 스킬 시작 시 진행 상황 확인**

프로파일 확인 단계에서 `completed_missions` 체크:

```markdown
### 진행 상황 확인

프로파일 파일에 `completed_missions` 필드가 있는지 확인:

- **있으면** (중간에 이탈 후 재진입):
  ```json
  AskUserQuestion({
    "questions": [{
      "question": "👋 다시 오셨네요! 어떻게 시작하시겠어요?",
      "header": "재시작",
      "options": [
        {
          "label": "이어서 하기",
          "description": "지난번에 완료한 미션 다음부터 시작 (추천)"
        },
        {
          "label": "처음부터 다시",
          "description": "완료 기록을 지우고 처음부터"
        },
        {
          "label": "특정 미션 선택",
          "description": "원하는 미션으로 바로 이동"
        }
      ],
      "multiSelect": false
    }]
  })
  ```

- **없으면** (처음 시작):
  → 일반 학습 경로 선택으로 진행
```

**Step 2: 이어하기 선택 시 진행 상황 표시**

```markdown
### 이어하기 플로우

사용자가 "이어서 하기" 선택 시:

1. **진행 상황 요약 표시:**
   ```
   지난번 여기까지 완료하셨어요:
   ✓ 미션 0 (Setup)
   ✓ 미션 1 (Experience)
   ✓ 미션 3-1 (CLAUDE.md)

   진행률: 3/7 필수 미션 (43%)
   획득 배지: 🎯 기초 완료
   ```

2. **다음 미션 안내:**
   ```
   다음 미션: 미션 3-2 (Skill)
   주제: 반복 작업 자동화
   예상 시간: 15분
   난이도: ⭐⭐ 보통

   시작하시겠어요? (입력: "시작" 또는 "다음")
   ```

3. **해당 미션으로 자동 이동**
```

**Step 3: 프로파일에 진행 상황 저장**

각 미션 완료 시:

```markdown
### 진행 상황 저장

미션 완료 후 `~/.claude/memory/user-profile.md`에 추가:

```markdown
## 학습 진행 상황

- 학습 경로: 빠른 시작 (필수 7개)
- 완료한 미션: 0, 1, 3-1, 3-2 (4/7)
- 획득 배지: 🎯 기초 완료
- 마지막 학습일: 2026-02-15
```
```

**Step 4: Commit**

```bash
git add SKILL.md
git commit -m "feat: Add re-entry flow for returning users

- 중간 이탈 후 재시작 시 진행 상황 자동 표시
- 이어하기 / 처음부터 / 특정 미션 선택 가능
- 프로파일에 진행 상황 영구 저장

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 9: 콘텐츠 작성 (동기부여 스토리 63개)

**Files:**
- Modify: `~/.claude/skills/my-day1/references/motivation-stories.md`

**Step 1: 미션 0 (Setup) 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 2: 미션 1 (Experience) 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 3: 미션 3-1 (CLAUDE.md) 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 4: 미션 3-2 (Skill) 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 5: 미션 3-4 (Subagent) 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 6: 미션 요약 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 7: 미션 4 (Basics) 스토리 작성**

9개 직업 × 1개 미션 = 9개 스토리

**Step 8: Commit**

```bash
git add references/motivation-stories.md
git commit -m "content: Add 63 motivation stories for essential missions

- 필수 7개 미션 × 9개 직업 = 63개 스토리
- 각 스토리: 공감 질문 + 실제 사례 + 완료 후 혜택
- 비개발자 동기부여 강화

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 10: 콘텐츠 작성 (용어 비유 135개)

**Files:**
- Modify: `~/.claude/skills/my-day1/references/glossary.md`

**Step 1-15: 15개 용어별로 9개 직업 비유 작성**

각 용어마다:
1. CLI
2. 터미널
3. git
4. commit
5. CLAUDE.md
6. Skill
7. MCP
8. Subagent
9. Agent Teams
10. Hook
11. Plugin
12. Phase A/B
13. push
14. pull
15. branch

각각 9개 직업별 비유 = 총 135개

**Step 16: Commit**

```bash
git add references/glossary.md
git commit -m "content: Add 135 analogies for 15 technical terms

- 15개 주요 용어 × 9개 직업별 비유 = 135개
- 각 직업에 친숙한 비유 사용 (Excel, Figma, Google Drive 등)
- 용어 장벽 제거로 이해도 향상

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 11: 테스트 및 검증

**Files:**
- Create: `~/.claude/skills/my-day1/TEST-ESSENTIAL-PATH.md`

**Step 1: 테스트 시나리오 문서 작성**

```markdown
# Essential Path 테스트 시나리오

## 시나리오 1: 터미널 처음 사용하는 HR

**프로파일:**
- 직업: HR/인사 담당자
- 터미널 경험: 처음이에요

**학습 경로:** 빠른 시작 (필수 7개)

**검증 포인트:**
1. 용어 비유가 이해되는가?
   - CLI → "Excel 수식처럼..." 설명 확인
   - 터미널 → "컴퓨터와 대화하는..." 설명 확인
2. 동기부여 스토리가 공감되는가?
   - "채용 공고 20개를 5분 만에" 사례 확인
3. 실습을 따라할 수 있는가?
   - 명령어 실패 시 "help" 사용 확인
4. 7개 완료 후 실무 적용 의지가 생기는가?
   - 🏆 핵심 완료 배지 획득 확인
   - "실무에 바로 적용" 안내 확인

---

## 시나리오 2: 터미널 조금 써본 디자이너

**프로파일:**
- 직업: UI/UX 디자이너
- 터미널 경험: 해본 적 있어요

**학습 경로:** 전체 과정 (13개)

**검증 포인트:**
1. 동기부여 스토리가 공감되는가?
   - "디자인 시스템 일관성 100%" 사례 확인
2. 중간에 이탈하지 않고 완료하는가?
   - 마일스톤 배지 (🎯, 🏆, 🚀) 모두 획득 확인
3. 심화 미션도 "왜 필요한지" 이해되는가?
   - 미션 3-5 (Agent Teams) 동기부여 스토리 확인

---

## 시나리오 3: 개발 경험 있는 PM

**프로파일:**
- 직업: 제품 기획자/PM
- 터미널 경험: 익숙해요

**학습 경로:** 커스텀 (필요한 미션만)

**검증 포인트:**
1. 필요한 미션만 골라서 빠르게 완료 가능한가?
   - 미션 3-1, 3-2, 3-4만 선택 확인
2. 이미 아는 내용은 skip이 자연스러운가?
   - "skip" 입력으로 미션 0 건너뛰기 확인
```

**Step 2: 실제 테스트 수행**

각 시나리오별로:
1. `/my-day1` 실행
2. 프로파일 입력
3. 학습 경로 선택
4. 미션 진행 (동기부여, 용어, help 테스트)
5. 완료율 및 만족도 확인

**Step 3: 성공 기준 검증**

- [ ] 필수 7개 완료율 80% 이상
- [ ] 중도 이탈률 20% 이하
- [ ] "실무 적용하겠다" 70% 이상
- [ ] 용어 이해도 (퀴즈 정답률) 80% 이상

**Step 4: Commit**

```bash
git add TEST-ESSENTIAL-PATH.md
git commit -m "test: Add Essential Path test scenarios

- 3가지 페르소나별 테스트 시나리오
- 용어 이해, 동기부여, 완료율 검증 포인트
- 성공 기준 4가지 정의

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

---

## Task 12: 최종 통합 및 문서 업데이트

**Files:**
- Modify: `~/.claude/skills/my-day1/README.md`

**Step 1: README에 Essential Path 안내 추가**

```markdown
## 특징

- **Essential Path**: 필수 7개 미션만 완료해도 실무 투입 가능 ⭐
- **13개 미션 완전 구현**: Setup부터 Basics까지 전체 온보딩 커리큘럼
- **3가지 학습 경로**: 빠른 시작 / 전체 / 커스텀
- **동기부여 스토리**: 각 미션마다 직업별 실제 사례 제공
- **용어 장벽 제거**: 15개 기술 용어를 쉬운 비유로 설명
- **마일스톤 배지**: 🎯 기초 → 🏆 핵심 → 🚀 마스터
- **Help 시스템**: 막혔을 때 "help" 입력으로 즉시 도움
- **재진입 플로우**: 중간 이탈 후에도 이어하기 가능
```

**Step 2: 필수 미션 로드맵 추가**

```markdown
## 필수 미션 로드맵 (Essential Path)

| 미션 | 주제 | 예상 시간 | 난이도 | 배지 |
|------|------|----------|--------|------|
| 미션 0 | Setup | 5분 | ⭐ 쉬움 | |
| 미션 1 | Experience | 10분 | ⭐ 쉬움 | |
| 미션 3-1 | CLAUDE.md | 10분 | ⭐⭐ 보통 | 🎯 기초 완료 |
| 미션 3-2 | Skill | 15분 | ⭐⭐ 보통 | |
| 미션 3-4 | Subagent | 10분 | ⭐⭐ 보통 | |
| 미션 요약 | 7개 기능 요약 | 5분 | ⭐ 쉬움 | |
| 미션 4 | Basics | 10분 | ⭐⭐ 보통 | 🏆 핵심 완료 |

**총 예상 시간**: 1~1.5시간
```

**Step 3: Commit**

```bash
git add README.md
git commit -m "docs: Update README with Essential Path features

- Essential Path 개념 및 필수 미션 로드맵 추가
- 3가지 학습 경로, 마일스톤 배지, Help 시스템 안내
- 비개발자 타겟 명확화

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

**Step 4: 최종 푸시**

```bash
git push
```

---

## 성공 기준

구현 완료 후 아래 기준으로 검증:

1. **기능 완성도**
   - [ ] 13개 미션 모두 "미션" 용어로 변경
   - [ ] 필수 7개 / 심화 6개 분류 완료
   - [ ] 학습 경로 선택 3가지 작동
   - [ ] 동기부여 스토리 63개 작성 완료
   - [ ] 용어 비유 135개 작성 완료
   - [ ] 마일스톤 배지 3개 작동
   - [ ] Help 시스템 4가지 상황 대응
   - [ ] 재진입 플로우 작동

2. **비개발자 완료율**
   - [ ] 필수 7개 완료율 80% 이상
   - [ ] 중도 이탈률 20% 이하

3. **이해도 및 만족도**
   - [ ] 용어 이해도 (퀴즈 정답률) 80% 이상
   - [ ] "실무 적용하겠다" 응답 70% 이상

---

## 예상 개발 시간

- Task 1: 용어 변경 - 1시간
- Task 2: 미션 메타데이터 - 1시간
- Task 3: 학습 경로 선택 - 2시간
- Task 4: 동기부여 스토리 시스템 - 1시간
- Task 5: 용어 비유 시스템 - 1시간
- Task 6: 마일스톤 배지 - 2시간
- Task 7: Help 시스템 - 2시간
- Task 8: 재진입 플로우 - 2시간
- Task 9: 동기부여 스토리 콘텐츠 - 6시간
- Task 10: 용어 비유 콘텐츠 - 4시간
- Task 11: 테스트 및 검증 - 4시간
- Task 12: 문서 업데이트 - 1시간

**총 예상 시간**: 27시간 (약 3.5일)
