
# 🧱 전체 프로세스 개요

PRD → FSD → UI/UX Wireframe (v0.dev 등) → 디자인 시안 확정 → 프론트엔드 코드 생성

이 흐름을 기준으로, Claude는 “무엇을 만들지” 정의하고 v0.dev는 “어떻게 보여줄지” 시각화하는 역할을 맡게 됩니다.

---

## ① PRD/FSD 정리 단계 – Claude에서의 준비

**목표:** v0.dev가 이해할 수 있는 수준으로 기능과 레이아웃 정보를 구조화하기

**Claude에게 해야 할 지시:**
- PRD와 FSD를 바탕으로 “UI 설계용 데이터 구조”를 JSON 또는 리스트 형태로 출력하게 한다.

**예:**
```json
{
  "pages": [
    {
      "name": "Login Page",
      "components": [
        {"type": "Form", "fields": ["email", "password"]},
        {"type": "Button", "label": "로그인"},
        {"type": "Link", "label": "회원가입"}
      ]
    },
    {
      "name": "Dashboard",
      "components": [
        {"type": "Navbar"},
        {"type": "Card", "title": "최근 활동"},
        {"type": "Table", "dataSource": "userLogs"}
      ]
    }
  ]
}
```

**출력 가이드:**
- 화면 단위로 페이지를 구분
- 각 컴포넌트를 명시적으로 타입과 목적까지 포함
- 입력 필드, 버튼, 데이터 테이블 등은 기능 명세에 매칭되게 기술

이 구조를 v0.dev나 비슷한 UI Agent가 입력으로 사용하면, 바로 와이어프레임이 자동 생성됩니다.

---

## ② v0.dev로 와이어프레임 생성 단계

**목표:** Claude의 명세를 시각적 와이어프레임으로 구체화하기

**사용 방법:**
1.  **Claude의 결과(JSON 또는 기능별 구조 요약)**를 v0.dev의 “Prompt Input” 영역에 그대로 붙여넣기
2.  프롬프트 시작부에 다음과 같은 시스템 지침을 추가하면 좋습니다:
    ```
    You are a UI/UX wireframe generator.
    Follow atomic design principles.
    Create clear layout hierarchy with reusable components.
    ```
3.  Claude의 UI 설계 지침 중 주요 원칙을 함께 전달:
    - “8px spacing grid”
    - “반응형 레이아웃 기준”
    - “명확한 시각 대비 및 접근성 확보”
    - “컴포넌트는 재사용 가능 구조로 구성”

**Tip:**
Claude에서 “UI 설계 지침”의 핵심 5줄 요약을 v0.dev prompt 맨 아래에 붙이면 결과물의 일관성이 높아집니다.

---

## ③ 피드백 및 수정 단계

**자동화 루프 구조:**
1.  v0.dev가 생성한 와이어프레임의 결과(이미지 or 코드)를 Claude에 다시 전달
2.  Claude에게 다음과 같이 요청:
    “이 와이어프레임이 FSD와 UI 설계 지침에 부합하는지 검토하고 개선사항을 제안해줘.”

Claude가 “UI 설계 원칙 기반 피드백”을 주면, 그걸 다시 v0.dev에 반영해서 반복 개선하는 구조로 완성도를 높일 수 있습니다.

---

## ④ 산출물 연결 흐름 예시

Claude (PRD/FSD 작성)
   ↓
Claude (UI 구조 JSON 생성)
   ↓
v0.dev (Wireframe 제작)
   ↓
Claude (디자인 피드백 및 개선)
   ↓
v0.dev (최종 와이어프레임 수정)
   ↓
Claude (UI 컴포넌트 구조 분석 → React 코드 설계)

---

## 💡 베스트 프랙티스

| 항목 | 권장 설정 |
|---|---|
| UI 구조 데이터 형식 | JSON (페이지, 컴포넌트, 액션 정의) |
| 디자인 기준 | Atomic Design / 8px Grid / 반응형 |
| AI 간 역할 분리 | Claude: 논리적 구조 / v0.dev: 시각화 |
| 프롬프트 문체 | 명령형, 구체적 속성 지정 |
| 피드백 루프 | Claude ↔ v0.dev 반복 (최대 3회 이내) |

---

## 🧱 Claude와 v0.dev를 함께 사용할 때의 프롬프트 예시

### Claude에게
PRD와 FSD를 기반으로 각 화면의 컴포넌트 구조를 JSON으로 작성해줘.  
각 페이지별 목적, 주요 UI 구성요소, 사용자 행동 트리거를 포함해야 해.

---

### v0.dev에게
You are a UI/UX design AI.
Create a wireframe based on the following JSON structure.
Follow minimal, modern, and responsive layout with 8px spacing grid.
Ensure visual hierarchy and accessibility.

{Claude가 생성한 JSON 붙여넣기}

---
