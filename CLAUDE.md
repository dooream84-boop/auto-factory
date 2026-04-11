# Auto-Factory — Claude Code 자동화 가이드

## 개요

이 저장소는 [cocoflow.xyz](https://cocoflow.xyz)에서 서비스 중인 한국어 웹 계산기 모음입니다.
GitHub Issues에 **`claude`** 라벨을 붙이면 Claude Code가 자동으로 작업을 수행합니다.

---

## 이슈 작성 방법

1. [New Issue](../../issues/new) 생성
2. 본문에 작업 내용을 구체적으로 작성
3. 오른쪽 **Labels** 에서 **`claude`** 선택 후 제출

라벨이 붙는 순간 GitHub Actions가 트리거되어 Claude Code가 자동으로 실행됩니다.

---

## 이슈 작성 예시

### 새 계산기 추가
```
products/loan-calculator/ 에 대출이자 계산기를 만들어줘.

- 대출금액, 연이율, 기간(개월) 입력
- 원리금균등상환 공식: M = P × r(1+r)^n / ((1+r)^n - 1)
- 총 납입액, 총 이자 결과 표시
- 기존 계산기와 동일한 파란색 디자인 (#2563eb)
- index.html 카드 링크 및 sitemap.xml 업데이트
- git push까지 완료
```

### 버그 수정
```
products/bmi-calculator/index.html 에서
BMI 결과가 소수점 5자리로 표시됨. 소수점 1자리로 수정해줘.
```

### 디자인 수정
```
모든 계산기의 결과 카드 배경색을 #eff6ff 로 통일해줘.
```

---

## 프로젝트 규칙

- **호스팅**: GitHub Pages → cocoflow.xyz
- **디자인**: #2563eb / #1d4ed8 파란색 테마, Noto Sans KR, 배경 #f0f4f8
- **AdSense**: ca-pub-8616886611208474 (모든 페이지 `<head>` 포함)
- **SEO**: meta description, og: 태그, Schema.org JSON-LD, canonical URL 필수
- **새 계산기 추가 시**: `sitemap.xml` URL 추가 + `index.html` 카드 링크 업데이트 필수
- **단일 HTML 파일**: 프레임워크 없이 순수 HTML/CSS/JS
