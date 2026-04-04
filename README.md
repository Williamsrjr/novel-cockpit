# Novel Cockpit
### [디버깅 및 개선사항 로그](https://docs.google.com/spreadsheets/d/1iqu3m0_fnIk2_VcCk9qBh1i8xoawz9-39JIjSSNh9BE/edit?gid=0#gid=0)
## 동기화
1분 자동 동기화 시행중(2026/4/5

소설 프로젝트 관리 대시보드 — 작품별 진행 상태, 일정, 문서 링크를 한눈에..
자동 동기화 (10초 폴링) — loadFromSheets(true)만 호출합니다. 시트에서 데이터를 읽어오기만 합니다. 로컬 → 시트 저장은 하지 않습니다.
수동 동기화 — saveToSheets() → loadFromSheets(true) 순서로 호출합니다. 로컬 데이터를 시트에 먼저 저장한 다음, 시트에서 다시 읽어옵니다. 양방향 전체 동기화입니다.
정리하면, 자동 폴링은 pull only, 수동 동기화는 push + pull입니다. 수동 동기화가 필요한 상황은 예를 들어 persist()의 debounced save가 네트워크 오류로 실패했을 때, 수동 버튼을 누르면 밀린 로컬 변경분을 강제로 시트에 올리고 최신 상태를 다시 받아오는 용도입니다.

## 화면 업데이트 시
순서가 이렇습니다:

사용자가 조작 → 로컬 N 배열이 즉시 수정됨
R() 호출 → 화면에 즉시 반영됨
800ms 뒤 시트에 push → 시트에서 pull → R() 한 번 더
## 기능

- **전체보기**: 모든 작품 × 9단계 파이프라인. 클릭→문서이동, 더블클릭→편집, 우클릭→빠른메뉴
- **작품별**: 카드형 뷰. 진행률, 일정 소화율, 마감일 경고
- **우클릭 메뉴**: 상태 전환(대기/진행중/완료/막힘), 메모 작성, URL 복사
- **작품 추가/편집/삭제**: 제목, 분류 태그(복수 선택), 분량, 일정
- **마감 경고**: 마감일 접근 시 깜박임(긴급도에 따라 속도 변화)
- **Docs/NC 색상 구분**: 초록=Google Docs, 보라=NovelCrafter
- **다크/라이트 모드**: 설정에서 전환, 브라우저에 저장
- **웹/모바일 반응형**: 768px 기준 별도 레이아웃
- **localStorage 자동 저장**: 모든 수정사항이 브라우저에 유지

## 분류 태그

SF, 문학, 엽편, 단편, 중편, 장편, 야설, 에로, 판타지 (복수 선택 가능)

## 단계 (9단계)

착상 → 구조설계 → 스토리 정교화 → 씬 구조 → 씬 비트 → 초고 → 퇴고 → 완성 → 발행

## GitHub Pages 배포

1. [github.com/new](https://github.com/new) → 저장소 생성 (`novel-cockpit`, Public, Add README 체크)
2. Add file → Upload files → `index.html` 업로드 → Commit
3. Settings → Pages → Branch: main → Save
4. 1~2분 후 `https://[아이디].github.io/novel-cockpit/` 에서 접속

### 아이폰 홈 화면 추가

Safari 접속 → 공유(□↑) → 홈 화면에 추가

## 향후 계획: Google Sheets 연동

현재 데이터는 localStorage(기기별 저장). 크로스 디바이스 동기화를 위해 Google Sheets를 백엔드로 연결 예정.

```
Google Sheets (데이터)
  ↕ Google Apps Script (API)
    ↕ index.html (프론트엔드)
```

시트 구성:
- **novels**: id, title, tags, currentStage, length, startDate, endDate, dirUrl
- **stages**: novelId, stageId, status, note, dueDate, docUrl, tool
- **refs**: novelId, icon, name, desc, url
