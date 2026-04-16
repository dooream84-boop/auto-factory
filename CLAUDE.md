# CocoFlow 프로젝트 컨텍스트

## 프로젝트 개요
- 사이트: https://cocoflow.xyz
- 목적: 한국어 웹 계산기 모음 + Google AdSense 수익화
- 저장소: github.com/dooream84-boop/auto-factory
- 로컬 경로: /Users/jeongsanghyeon/auto-factory
- 배포: GitHub Pages + Gabia 도메인 (HTTPS 완료)

## 디자인 스펙 (모든 계산기 공통)
- 메인 컬러: #2563eb / 다크: #1d4ed8 / 배경: #f0f4f8
- 폰트: Noto Sans KR (Google Fonts)
- 페이지 구조: header → hero(파란 그라데이션) → ad-banner → 입력카드 → 결과카드 → 설명카드 → footer
- AdSense 코드 (<head>에 필수):
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-8616886611208474" crossorigin="anonymous"></script>

## 배포된 계산기 23개

### 금융·노무 (14개)
- 퇴직금: /products/severance-calculator/
- 실수령액: /products/salary-calculator/
- 대출이자: /products/loan-calculator/
- 부가세: /products/vat-calculator/
- 연차수당: /products/annual-leave-calculator/
- 중도퇴사연차: /products/midterm-leave-calculator/
- 복리: /products/compound-interest-calculator/
- 양도소득세: /products/capital-gains-tax-calculator/
- 종합소득세: /products/income-tax-calculator/
- 4대보험: /products/insurance-calculator/
- 적금이자: /products/savings-calculator/
- 상속세: /products/inheritance-tax-calculator/
- 시급: /products/hourly-wage-calculator/
- 연말정산: /products/tax-refund-calculator/

### 건강·생활 (6개)
- BMI: /products/bmi-calculator/
- 전기요금: /products/electricity-calculator/
- 출산예정일: /products/due-date-calculator/
- D-Day: /products/dday-calculator/
- 칼로리: /products/calorie-calculator/
- 만나이: /products/korean-age-calculator/

### 세금 통합 (1개)
- 증여세·상속세 통합탭: /products/gift-tax-calculator/

### 금융 소득세·해외 (2개)
- 금융소득세: /products/financial-income-tax-calculator/
- 해외주식 양도세: /products/overseas-stock-tax-calculator/

## 주요 계산 로직 (검증 완료)
- 연차수당: 통상임금 ÷ 209 × 8 × 잔여일수
- 4대보험: 국민연금 4.5%, 건강보험 3.545%, 장기요양 건강보험료×12.95%, 고용보험 0.9%
- 2026 최저시급: 10,030원
- 전기요금: 누진 3단계 (200이하 88.3원/kWh, 201~400 182.9원, 초과 275.6원)
- 증여세 공제: 배우자 6억, 직계존속 5천만(미성년 2천만), 직계비속 5천만, 기타친족 1천만
- 상속세: 일괄공제 5억 vs 기초공제 2억+인적공제 비교, 배우자공제 최소 5억, 신고세액공제 3%
- 칼로리: Mifflin-St Jeor BMR + 활동계수 TDEE
- 복리: A = P(1 + r/n)^(nt)

## 인프라 현황
- HTTPS: 완료
- Search Console: 등록 완료, 색인요청 7개 완료 / 13개 미완료
- AdSense: 심사 진행 중 (ca-pub-8616886611208474)
- GitHub Actions: claude-code-issue.yml 세팅 완료

## 미완료 작업
1. ~~Search Console 색인 요청 15개~~ ✅ 완료 (2026-04-14)
2. ~~GitHub Secrets에 ANTHROPIC_API_KEY 등록~~ ❌ 미진행 (GitHub 자동화 사용 안 함)
3. ~~Issues에 claude 라벨 생성~~ ❌ 미진행 (GitHub 자동화 사용 안 함)
4. AdSense 승인 후 광고 단위 설정

## 새 계산기 추가 시 체크리스트
1. products/[name]-calculator/index.html 생성
2. 공통 디자인 스펙 적용
3. AdSense 코드 <head>에 삽입
4. index.html 홈페이지 카드 추가 (✅ 이용 가능)
5. sitemap.xml에 URL 추가
6. git add . && git commit -m "feat: [이름] 계산기 추가" && git push

## 기타
- 문의 이메일: cocoflow.contact@gmail.com
- 개인정보처리방침: /privacy/
- 문의하기: /contact/
