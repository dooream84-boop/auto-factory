# Auto-Factory

AI가 웹 계산기/도구를 자동 생산하여 애드센스 수익을 만드는 자가발전 시스템.

## 라이브 도구 목록

| 도구 | 경로 | 상태 |
|------|------|------|
| 퇴직금 계산기 | [/products/severance-calculator/](./products/severance-calculator/) | ✅ 배포 준비 완료 |

## GitHub Pages 배포 방법

1. GitHub에서 새 저장소 생성 (예: `auto-factory`)
2. 이 폴더를 push

```bash
git remote add origin https://github.com/YOUR-USERNAME/auto-factory.git
git branch -M main
git push -u origin main
```

3. GitHub 저장소 → **Settings → Pages**
4. Source: **Deploy from a branch** → Branch: `main` / `/ (root)` → Save
5. 몇 분 후 `https://YOUR-USERNAME.github.io/auto-factory/products/severance-calculator/` 로 접근 가능

## 도메인 확정 후 해야 할 일

각 `products/*/index.html` 파일에서 아래 TODO 주석을 찾아 URL 교체:

```html
<!-- TODO: 도메인 확정 후 아래 두 줄 주석 해제 및 URL 교체 -->
<!-- <meta property="og:image" content="https://YOUR-DOMAIN/..."> -->
<!-- <link rel="canonical" href="https://YOUR-DOMAIN/..."> -->
```

## 폴더 구조

```
auto-factory/
├── pipeline/       # 자동화 스크립트
├── products/       # 배포 준비 완료 계산기
│   └── severance-calculator/
│       └── index.html
├── templates/      # 재사용 가능한 원본 템플릿
├── monitoring/     # 트래픽·수익 데이터 (git 제외)
└── docs/
    └── SYSTEM.md   # 시스템 설계 문서
```

## 애드센스 적용 방법

각 `index.html`에서 `ca-pub-XXXXXXXXXXXXXXXX` 를 실제 퍼블리셔 ID로 교체하고,
주석 처리된 AdSense `<script>` 및 `<ins>` 태그를 주석 해제하면 됩니다.
