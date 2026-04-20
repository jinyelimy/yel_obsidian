---
type: moc
tags: [moc]
---
# 🤝 Meetings

> **`05_Meetings/` 폴더의 입구 노트입니다.** 모든 회의록이 여기에 모입니다.

← [[🏠 Home]]

## 📁 폴더 구조

연/월별로 자동 분류:
```
05_Meetings/
  └─ 2026/
       └─ 04/
            └─ 2026-04-20_AI-TF-킥오프.md
```

## 📝 회의록 만들기

### 빠른 방법
1. `Ctrl+P` → "새 노트 만들기"
2. 파일명: `2026-04-20_회의주제.md`
3. `Ctrl+P` → "Templater: Insert template" → `Meeting` 선택
4. 본문에 관련 프로젝트 링크 (`[[AI-TF]]` 등)를 꼭 적기 → 백링크 자동 연결!

### 회의록 작성 5원칙
1. **결정사항** 먼저 적기
2. **액션 아이템**은 담당자/마감일 명시
3. **관련 프로젝트 링크** 빼먹지 않기
4. 회의 직후 5분만 정리 (시간 지나면 잊음)
5. **민감 정보는 별도 노트로** (이 Vault에 무엇이 들어가는지 항상 의식)

---

## 📅 최근 회의 (자동)

```dataview
TABLE date, project as "프로젝트", attendees as "참석자"
FROM "05_Meetings"
WHERE type = "meeting"
SORT date DESC
LIMIT 15
```

## 🎯 미완료 액션 아이템 (자동)

```dataview
TASK
FROM "05_Meetings"
WHERE !completed
GROUP BY file.link
LIMIT 30
```
