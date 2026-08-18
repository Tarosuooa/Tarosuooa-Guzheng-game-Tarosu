# 타로당 고쟁 — 아리랑 연주 게임 & 곡 편집기

가야금(고쟁) 리듬 게임 프로토타입입니다.

- **`index.html`** (저장소 루트) — 아리랑 연주하기. 사이트 주소로 바로 들어가면 이 화면이 나와요.
- **`editor/index.html`** — 곡 편집기. 그리드에 음을 찍어 곡을 만들고, 게임에 붙여넣을 코드를 내보냅니다.

## GitHub Pages로 배포하기

1. GitHub 저장소 **Settings → Pages**로 이동합니다.
2. **Source**를 `Deploy from a branch`로 설정하고, 브랜치는 `main`, 폴더는 `/ (root)`로 선택합니다.
3. 저장 후 1~2분 기다리면 아래 주소로 접속할 수 있어요.
   - 연주 화면(첫 화면): `https://<사용자명>.github.io/<저장소명>/`
   - 곡 편집기: `https://<사용자명>.github.io/<저장소명>/editor/`

## 폴더 구조

```
(저장소 루트)
├── index.html          ← 아리랑 연주하기 (첫 화면)
└── editor/
    └── index.html      ← 곡 편집기
```

⚠️ **폴더가 이 구조 그대로 저장소 최상위에 있어야 해요.** `tarodang-guzheng` 같은 폴더로 한 번 더 감싸져 있으면 루트 주소에서 404가 나요. zip을 풀었을 때 `index.html`과 `editor` 폴더가 바로 보이는지 확인한 뒤, 그 안의 내용물만 저장소에 올려주세요.

## ⚠️ 지금은 프로토타입 단계예요

지금은 Firebase 같은 서버가 연결되어 있지 않아서, **곡 등록은 이 브라우저(이 기기)에만 저장**됩니다 (`localStorage` 사용). 다른 사람의 브라우저나 다른 기기에서는 보이지 않아요.

곡 목록 관련 코드는 `index.html` 안의 아래 부분입니다.

```js
const SONG_STORE_KEY = 'tarodang_guzheng_songs';

function loadSongList(){
  const raw = localStorage.getItem(SONG_STORE_KEY);
  return raw ? JSON.parse(raw) : [];
}
function saveSongList(list){
  localStorage.setItem(SONG_STORE_KEY, JSON.stringify(list));
}
```

### 나중에 실제 '게시판'으로 만들려면

`localStorage.getItem` / `localStorage.setItem` 두 곳만 Firestore 읽기/쓰기로 바꾸면 다른 사람들과도 곡을 진짜로 공유할 수 있습니다. UI/기능 흐름(등록, 목록, 삭제, 적용)은 이미 다 만들어져 있으므로 저장소 전환 작업만 하면 됩니다.

1. Firebase 프로젝트를 만들고 Firestore를 활성화합니다.
2. `index.html`의 `<head>`에 Firebase SDK(모듈 방식)를 추가합니다.
3. `loadSongList()` → Firestore `getDocs(collection(db, 'songs'))` 호출로 교체합니다.
4. `saveSongList(list)` → 곡 등록 시점에 `addDoc`/`setDoc`으로, 삭제 시점에 `deleteDoc`으로 교체합니다.
5. Firestore 보안 규칙으로 쓰기 권한(스팸 방지, 글자 수 제한 등)을 설정합니다.

## 편집기 사용법

- 칸을 클릭하면 음이 찍혀요. 다시 클릭하면 지워져요.
- 음 길이: `0.5박 · 1박 · 2박` 중 선택 후 클릭.
- **Shift + 클릭**: 점 대신 `///` 슬라이드 노트 표시.
- '재생'으로 미리듣기, '테스트 플레이'로 실제 게임처럼 플레이해볼 수 있어요.
- '코드 내보내기'로 나온 코드를 복사해서 `index.html`의 `const SONG = [ ... ];` 부분을 통째로 바꿔치기하면 새 곡이 반영돼요.
