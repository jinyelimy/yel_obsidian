---
type: moc
tags: [moc]
---
# 🗄 Archive

> **`04_Archive/` 폴더의 입구 노트입니다.** 끝났거나 중단된 프로젝트를 보관합니다.

← [[🏠 Home]]

## 📂 보관 정책

다음 경우 여기로 이동:
- ✅ 완료된 프로젝트
- ⏸ 6개월 이상 손대지 않은 프로젝트
- ❌ 더이상 필요 없지만 기록은 남기고 싶은 노트

## 🔄 이동 방법

1. 해당 프로젝트 폴더를 통째로 `04_Archive/` 안으로 드래그
2. MOC 노트의 `status: active` → `status: archived`로 변경
3. [[Projects]] 입구 노트에서 해당 링크를 "완료" 섹션으로 이동

## 📋 보관된 프로젝트 (자동)

```dataview
TABLE status, file.folder
FROM "04_Archive"
WHERE type = "project"
SORT file.mtime DESC
```

---

## 💡 왜 삭제하지 않나?

- 나중에 비슷한 프로젝트 할 때 **참고자료**가 됨
- 1년 뒤 회고할 때 **"내가 뭘 했지?"**를 확인 가능
- 옵시디언 .md는 가벼우니 **삭제할 이유가 거의 없음**
