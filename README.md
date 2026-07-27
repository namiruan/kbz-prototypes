# KBZ 3.0 Prototypes

기획 프로토타입을 관리하는 전용 repo. 화면(HTML)은 [KBZ 3.0 디자인 시스템](https://github.com/namiruan/kbz_3.0_ui_stytem)을 GitHub Pages URL로 참조하므로, 디자인 시스템이 갱신되면 프로토타입에도 자동 반영된다.

> 프로토타입 작성 규칙·컴포넌트 마크업의 단일 원본은 디자인 시스템 repo의 `design-system/workflow/planner.md`다. 이 README는 **이 repo에서의 저장·빌드 절차**만 다룬다.

## 구조

```
kbz-prototypes/
  build-prototype.py     # 공용 파셜 주입(@include) — .src.html → .html
  build-flow-hub.py      # @flow 스캔 → flow-hub.html
  index.html             # 랜딩(기능별 허브 링크)
  <기능>/                 # 예: insurance-report/
    _shared/             # 공용 오버레이 파셜(@include 대상)
    6-1-list.src.html    # 소스(@include·@flow 마커 포함)
    6-1-list.html        # 빌드 산출물(서빙용, 커밋)
    ...
    flow-hub.html        # 플로우 허브(생성물)
```

- `_shared/`(repo 루트)는 여러 기능이 공유하는 파셜, `<기능>/_shared/`는 그 기능 전용 파셜.
- 서빙용 `.html`과 `flow-hub.html`은 **커밋한다**(Pages가 서빙). `.src.html`도 커밋해 소스를 남긴다.

## 워크플로우

1. `<기능>/` 폴더에 화면을 `*.src.html`로 작성한다.
   - 공용 오버레이는 `<!-- @include: _shared/site-select.html -->`로 include.
   - 각 화면 `<body>` 상단에 관계 메타를 임베드:
     ```html
     <!-- @flow
     title: 취득 대상자 목록
     exits: 6-2-detail.html?worker, 6-3-report.html
     -->
     ```
2. 공용 파셜 주입 → 서빙용 HTML 생성:
   ```
   python3 build-prototype.py 기능/*.src.html
   ```
3. 플로우 허브 생성:
   ```
   python3 build-flow-hub.py 기능 --title "기능명"
   ```
4. 커밋 & push → GitHub Pages가 URL로 서빙.

## 미리보기 (GitHub Pages)

`Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: main / (root)` 로 켠다.

- 랜딩: `https://namiruan.github.io/kbz-prototypes/`
- 허브: `https://namiruan.github.io/kbz-prototypes/<기능>/flow-hub.html`
- 화면: `https://namiruan.github.io/kbz-prototypes/<기능>/6-1-list.html`

## 참고

- 디자인 시스템 리소스(자동 참조): `https://namiruan.github.io/kbz_3.0_ui_stytem/{tokens.css, components.css, components.js}`
- 빌드 스크립트 원본은 디자인 시스템 repo(`build-prototype.py`·`build-flow-hub.py`)이며, 이 repo에도 같은 파일을 둔다. 스크립트가 갱신되면 두 곳을 동기화한다.
