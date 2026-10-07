# 현지거점 업무관리 — Claude 운영 규칙

한-베 스마트공장 ODA 하노이 현지거점 사무소의 업무 이력을 대시보드 하나로 관리한다.

- 대시보드: https://claude.ai/artifact/9qHu5Badqg4r8QQiruvDe3
- 페이지 소스: `office/index.html` (수정 후 같은 경로로 Artifact 재게시 → URL 유지)
- 데이터 구조: `office/README.md`
- 기록 추가·수정은 페이지 재게시가 아니라 `ArtifactData` 도구로 db에 쓴다.

## 데이터 쓰기 규칙

1. **과업 매핑**: `task` 값은 `tasks` 컬렉션의 id(rd, sati, office, mou, pd, training …)를 쓴다. 애매하면 비워 두고(미분류) 사용자에게 묻는다. 새 과업은 사용자 확인 후에만 만든다.
2. **중복 방지**: 출처 id로 문서 id를 정한다. 같은 id가 이미 있으면 쓰지 않는다.
   - Gmail 스레드 `gm-<threadId>`, Drive 파일 `drv-<fileId>`, Calendar 일정 `cal-<eventId>`
   - 사용자가 직접 말한 항목은 `m-<YYYYMMDD>-<짧은영문키>`
3. **자동 수집 표시**: 메일·드라이브·캘린더에서 Claude가 스스로 가져온 항목은 `source: "auto", reviewed: false`를 붙인다. 페이지에서 "검토 필요"로 보이고 사용자가 "확인"을 누르면 `reviewed: true`가 된다. 사용자가 직접 불러 준 항목에는 붙이지 않는다.
4. **덮어쓰기 금지**: `reviewed: true`이거나 `source`가 없는(사람이 쓴) 문서는 사용자 요청 없이 고치지 않는다. 고칠 때는 읽은 `version`을 `if_version`으로 붙인다.
5. **원문 링크**: `url`에는 Gmail `viewUrl`, Drive `viewUrl`, Calendar `htmlLink`를 그대로 넣는다.
6. **날짜**: `YYYY-MM-DD`, Asia/Seoul 기준.
7. **개인정보**: 관계자 연락처는 메일 서명 등에 실제로 적힌 것만 옮긴다. 추측해서 채우지 않는다.
8. **외부 발송 금지**: 메일 발송, 일정 생성, 파일 공유는 사용자가 명시적으로 요청할 때만 한다.

## 컬렉션별 판단 기준

| 원천 | 들어갈 곳 |
|---|---|
| 기관과 주고받은 메일 (요청·회신·협의) | `actions` (type: 연락) |
| 회의·면담·행사 일정 (지난 일정) | `actions` (type: 행사 또는 실행) |
| 다가오는 중요 일정 (워킹그룹, 서명식 등) | `milestones` |
| MOM·MOU·RD·PD·공문·회의록 파일 | `documents` |
| 발표자료·사진·참고자료·번역본 | `library` |
| 메일 서명에 나온 기관 담당자 | `contacts` |
| 주간·월간·결과 보고 파일 | `reports` |

## 자주 쓰는 요청

- "이번 주 정리해줘": 지난 7일 Gmail·Drive·Calendar를 훑어 위 규칙대로 db에 추가하고, 추가한 목록과 애매한 항목을 보고한다.
- "주간보고 초안 써줘": 지난 7일 `actions`를 과업별로 묶어 `reports`에 `kind: 주간보고, source: auto` 초안을 만든다. Drive 문서 작성은 요청할 때만 한다.
- "○○ 액션 추가해줘 / 문서 등록해줘": 해당 컬렉션에 한 건 쓴다.
- "과업 상태 바꿔줘": `tasks/<id>`의 status·tone·summary를 update한다.
- "이정표 추가해줘": `milestones`에 date·label을 쓴다.

## 정기 루틴

매주 월요일 08:47(서울)에 "현지거점 주간 정리" 루틴이 새 세션에서 실행된다. 위 "이번 주 정리해줘"와 "주간보고 초안 써줘"를 자동으로 수행하고, 결과는 대시보드의 "검토 필요" 배너로 확인한다.
