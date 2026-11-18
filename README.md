# Gemini Console

Google 로그인 → API 키·모델·시스템 프롬프트와 대화 내역을 Firestore에 저장하는 개인용 Gemini 채팅 페이지.

## Firebase 설정 (처음 한 번)

1. [Firebase 콘솔](https://console.firebase.google.com)에서 프로젝트 만들기
2. **Authentication → 시작하기 → 로그인 방법 → Google** 사용 설정
3. **Authentication → 설정 → 승인된 도메인**에 `pswego.github.io` 추가
4. **Firestore Database → 데이터베이스 만들기** (프로덕션 모드, 위치는 `asia-northeast3`(서울) 추천)
5. **Firestore → 규칙** 탭에 `firestore.rules` 내용을 붙여넣고 **게시**
6. **프로젝트 설정 → 내 앱 → 웹 앱 추가(</>)** 후 나오는 `firebaseConfig` 값을 `firebase-config.js`에 붙여넣기
7. 커밋·푸시 → `https://pswego.github.io/gemini/` 접속

> 로컬에서 `file://`로 열면 모듈 로딩이 막힙니다. `python3 -m http.server` 등으로 띄워서 `http://localhost:8000`으로 여세요 (`localhost`는 기본 승인 도메인).

## 저장 구조

```
users/{uid}                       apiKey, models, sys, model
users/{uid}/convos/{id}           title, model, createdAt, updatedAt, count
users/{uid}/convos/{id}/messages/{m000000}   role, parts, idx
users/{uid}/blobs/{id}/chunks/{n} 큰 첨부·생성 이미지 (1MB 문서 제한 때문에 분할 저장)
```

- 보안 규칙상 본인 계정만 읽고 쓸 수 있습니다.
- 대화 목록은 실시간 동기화되어 다른 기기에서도 바로 보입니다.
- 예전 버전에서 브라우저에 저장했던 대화·API 키는 첫 로그인 때 계정으로 옮길지 물어봅니다.
- 로그아웃하면 이 기기의 Firestore 캐시도 지웁니다.
