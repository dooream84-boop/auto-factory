# 법령 변경 대응 로직 업데이트 방안 (2026-04-19)

> 목적: 26개 계산기의 세율·요율·공제 하드코딩 구조를 지속 가능하게 전환
> 대상: cocoflow.xyz (정적 사이트, GitHub Pages)

---

## 1. 현황 진단

### 1.1 하드코딩 상수 인벤토리 요약

| 변경 주기 | 상수 건수 | 영향 페이지 수 | 최악 중복 횟수 |
|---|---|---|---|
| 매년 1월 (확정) | ~35건 | 7개 | 종합소득세 누진표: 6개 파일 |
| 매년 7월 (국민연금) | 2건 | 2개 | 연금 상한: 2개 파일 |
| 수시 (한전·금융위) | ~10건 | 2개 | 전기요금: 1개 파일 |
| 3~5년 (세율 기본) | ~15건 | 5개 | 증여/상속세: 2개 파일 |
| 변경 없음 (순수 수학) | 0건 | 7개 | - |
| **합계** | **~95건** | **19개** | - |

### 1.2 상수별 상세 위치 (파일·줄번호·현재값)

#### A. 종합소득세 누진표 (6개 파일 중복 — 최우선 통합 대상)

```
products/salary-calculator/index.html          줄 820~827  if-else 체인
products/income-tax-calculator/index.html       줄 652~661  BRACKETS 배열
products/capital-gains-tax-calculator/index.html 줄 587~596  BRACKETS 배열
products/financial-income-tax-calculator/index.html 줄 573~582  brackets 배열
products/tax-refund-calculator/index.html       줄 555~561  if-else 체인
```

현재값 (2026년):
| 과세표준 | 세율 | 누진공제 |
|---|---|---|
| ~1,400만 | 6% | 0 |
| ~5,000만 | 15% | 126만 |
| ~8,800만 | 24% | 576만 |
| ~1.5억 | 35% | 1,544만 |
| ~3억 | 38% | 1,994만 |
| ~5억 | 40% | 2,594만 |
| ~10억 | 42% | 3,594만 |
| 10억 초과 | 45% | 6,594만 |

#### B. 4대보험 요율 (2개 파일)

| 항목 | 현재값 | 파일1: salary | 파일2: insurance |
|---|---|---|---|
| 국민연금 | 4.5% | 줄 778 | 줄 505~506 |
| 국민연금 상한 | 617만원 | 줄 777 | 줄 505, 558 |
| 건강보험 | 3.545% | 줄 781 | 줄 508~509 |
| 장기요양 | 12.95% | 줄 784 | 줄 510~511 |
| 고용보험(근로자) | 0.9% | 줄 787 | 줄 512 |
| 고용보험(사업주) | 1.15% | - | 줄 562 |

#### C. 최저임금 (1개 파일)

```
products/hourly-wage-calculator/index.html  줄 481  MIN_WAGE = 10030
```

#### D. 근로소득공제 (3개 파일)

```
products/salary-calculator/index.html       줄 806~810
products/income-tax-calculator/index.html    줄 672~677
products/tax-refund-calculator/index.html    줄 522~526
```

#### E. 근로소득세액공제 (3개 파일)

```
products/salary-calculator/index.html       줄 830~836
products/income-tax-calculator/index.html    줄 712~713
products/tax-refund-calculator/index.html    줄 565~567
```

#### F. 증여세/상속세 세율표 (2개 파일)

```
products/gift-tax-calculator/index.html      줄 564~569
products/inheritance-tax-calculator/index.html 줄 526~531
```

#### G. 증여세 공제한도 (1개 파일)

```
products/gift-tax-calculator/index.html      줄 584~586
배우자 6억, 직계존속 5천만, 미성년 2천만, 직계비속 5천만, 기타친족 1천만
```

#### H. 상속세 공제 (2개 파일)

```
products/gift-tax-calculator/index.html        줄 655~656  기초 2억, 일괄 5억
products/inheritance-tax-calculator/index.html  줄 571~575, 553, 563, 590
```

#### I. 양도소득세 특수세율 (1개 파일)

```
products/capital-gains-tax-calculator/index.html
  줄 632  12억 비과세 한도
  줄 659  기본공제 250만원
  줄 663  1년미만 50%, 1~2년 40%
  줄 699  다주택 중과 +20%p, +30%p
  줄 608  장기보유공제 연 2%
```

#### J. 취득세 (1개 파일)

```
products/acquisition-tax-calculator/index.html
  줄 442~454  1주택/다주택 세율
  줄 471, 477  교육세·농특세
```

#### K. 자동차세 (1개 파일)

```
products/automobile-tax-calculator/index.html
  줄 459~461  배기량 단가 (80/140/200원)
  줄 467      연납할인율
  줄 475, 477~478  교육세, 전기차
```

#### L. 전기요금 (1개 파일)

```
products/electricity-calculator/index.html
  줄 409  기본요금 [910, 1600, 7300]
  줄 410  전력량요금 [88.3, 182.9, 275.6]
  줄 453  기후환경요금 9.0원/kWh
  줄 454  연료비조정액 5.0원/kWh
  줄 457  전력산업기반기금 3.7%
```

#### M. 기타 (각 1개 파일)

```
해외주식: 기본공제 250만, 세율 20%     overseas-stock-tax 줄 556~559
금융소득: 분리과세 15.4%, 2000만 기준  financial-income-tax 줄 519~536
적금이자: 이자소득세 15.4%             savings 줄 432
부가세: 10%                           vat 줄 473
비과세: 식대 20만, 차량 20만, 기타 10만 salary 줄 846~848
연차: 월 소정근로 209시간              annual-leave 줄 395, midterm-leave 줄 439
```

### 1.3 변경 시 영향 규모

| 시나리오 | 수정 파일 수 | 수정 위치 수 |
|---|---|---|
| 소득세 누진표 개정 | 5~6개 | 48줄+ |
| 4대보험 요율 변경 | 2개 | 12줄 |
| 최저임금 변경 | 1개 | 1줄 |
| 전기요금 변경 | 1개 | 5줄 |
| 증여/상속세 개정 | 2개 | 20줄+ |
| **전체 연초 업데이트** | **최대 15개** | **~80줄** |

---

## 2. 공통 상수 추출 설계

### 2.1 제안 구조: `assets/data/tax-constants.js`

GitHub Pages는 정적 사이트이므로 서버 사이드 빌드가 없음. 가장 실용적인 방법은 **단일 JS 파일에 상수 객체를 정의**하고 각 페이지에서 `<script src>` 로 로드하는 방식.

```
assets/
└── data/
    └── tax-constants-2026.js    ← 현재 연도 상수
    └── tax-constants-2025.js    ← 이전 연도 (아카이브)
```

**`tax-constants-2026.js` 구조안:**

```js
const COCOFLOW = {
  year: 2026,
  lastUpdated: '2026-01-15',

  // ── 종합소득세 누진표 ──
  incomeTaxBrackets: [
    { limit: 14000000, rate: 0.06, deduction: 0 },
    { limit: 50000000, rate: 0.15, deduction: 1260000 },
    { limit: 88000000, rate: 0.24, deduction: 5760000 },
    { limit: 150000000, rate: 0.35, deduction: 15440000 },
    { limit: 300000000, rate: 0.38, deduction: 19940000 },
    { limit: 500000000, rate: 0.40, deduction: 25940000 },
    { limit: 1000000000, rate: 0.42, deduction: 35940000 },
    { limit: Infinity, rate: 0.45, deduction: 65940000 }
  ],

  // ── 4대보험 ──
  insurance: {
    nationalPension: { rate: 0.045, capMonthly: 6170000, floorMonthly: 390000 },
    healthInsurance: { rate: 0.03545 },
    longTermCare: { rateOfHealth: 0.1295 },
    employment: { employeeRate: 0.009, employerBase: 0.009, employerStabilization: 0.0025 }
  },

  // ── 최저임금 ──
  minimumWage: { hourly: 10030 },

  // ── 근로소득공제 ──
  earnedIncomeDeduction: [
    { limit: 5000000, rate: 0.70, base: 0 },
    { limit: 15000000, rate: 0.40, base: 3500000 },
    { limit: 45000000, rate: 0.15, base: 7500000 },
    { limit: 100000000, rate: 0.05, base: 12000000 },
    { limit: Infinity, rate: 0.02, base: 14750000 }
  ],

  // ── 근로소득세액공제 ──
  earnedIncomeTaxCredit: {
    threshold: 1300000, belowRate: 0.55,
    aboveBase: 715000, aboveRate: 0.30,
    limitHigh: 740000, limitLow: 660000,
    limitIncomeThreshold: 33000000, limitReductionRate: 0.008
  },

  // ── 인적공제 ──
  personalDeduction: 1500000,

  // ── 자녀세액공제 ──
  childTaxCredit: { first: 150000, second: 200000, additional: 300000 },

  // ── 증여·상속세 ──
  giftInheritanceBrackets: [
    { limit: 100000000, rate: 0.10, deduction: 0 },
    { limit: 500000000, rate: 0.20, deduction: 10000000 },
    { limit: 1000000000, rate: 0.30, deduction: 60000000 },
    { limit: 3000000000, rate: 0.40, deduction: 160000000 },
    { limit: Infinity, rate: 0.50, deduction: 460000000 }
  ],
  giftExemptions: {
    spouse: 600000000, directAscendant: 50000000,
    minorFromAscendant: 20000000, directDescendant: 50000000,
    otherRelative: 10000000, nonRelative: 0
  },
  inheritanceDeductions: {
    basic: 200000000, lumpSum: 500000000,
    perChild: 50000000, minorPerYear: 5000000,
    spouseMin: 500000000, funeralCap: 15000000,
    filingCredit: 0.03
  },

  // ── 양도소득세 ──
  capitalGains: {
    basicDeduction: 2500000,
    oneHouseExemptLimit: 1200000000,
    shortTermRates: { under1yr: 0.50, under2yr: 0.40 },
    multiHomeSurcharge: { twoHomes: 0.20, threeOrMore: 0.30 },
    longTermHoldRate: 0.02, longTermHoldMax: 0.30,
    longTermResideRate: 0.04, longTermResideMax: 0.40
  },

  // ── 전기요금 ──
  electricity: {
    baseFee: [910, 1600, 7300],
    energyRate: [88.3, 182.9, 275.6],
    tiers: [200, 400],
    climateEnvFee: 9.0,
    fuelAdjustment: 5.0,
    vat: 0.10,
    industryFund: 0.037
  },

  // ── 취득세 ──
  acquisitionTax: {
    oneHome: { under6: 0.01, over9: 0.03 },
    multiHome: { twoRegulated: 0.08, threeRegulated: 0.12, nonRegulated: 0.08 },
    educationTax: { normal: 0.10, heavy: 0.004 },
    ruralTax: { over85sqm: 0.002 }
  },

  // ── 자동차세 ──
  automobileTax: {
    ccRates: [
      { limit: 1000, rate: 80 },
      { limit: 1600, rate: 140 },
      { limit: Infinity, rate: 200 }
    ],
    evFixedTax: 100000, evEducationTax: 30000,
    educationTaxRate: 0.30,
    prepayDiscount: { 1: 0.0458, 2: 0.0342, 3: 0.0225, 4: 0.0117 }
    // ↑ 2026년 기준 확인 필요 (지방세법 제128조)
  },

  // ── 비과세 한도 ──
  nonTaxable: { meal: 200000, carAllowance: 200000, other: 100000 },

  // ── 기타 ──
  vatRate: 0.10,
  interestIncomeTax: 0.154,
  overseasStockTax: { rate: 0.20, localRate: 0.10, deduction: 2500000 },
  financialIncome: { separationThreshold: 20000000, withholdingRate: 0.14 },
  workHoursMonthly: 209
};
```

### 2.2 각 페이지 적용 방법

**Before (현재):**
```html
<script>
  // salary-calculator 내부
  const pension = Math.min(taxable, 6170000) * 0.045;
  const health = taxable * 0.03545;
</script>
```

**After (개선):**
```html
<script src="/assets/data/tax-constants-2026.js"></script>
<script>
  const C = COCOFLOW.insurance;
  const pension = Math.min(taxable, C.nationalPension.capMonthly) * C.nationalPension.rate;
  const health = taxable * C.healthInsurance.rate;
</script>
```

### 2.3 방식 비교

| 방식 | 장점 | 단점 | 추천 |
|---|---|---|---|
| **A. 공통 JS 파일** | 간단, 빌드 불필요, GitHub Pages 호환 | 추가 HTTP 요청 1건 | **✅ 추천** |
| B. JSON + fetch() | 데이터/로직 분리 | async 처리 필요, CORS 설정 | △ |
| C. 빌드 타임 치환 (GitHub Actions) | 완전 자동 | CI/CD 설정 복잡, 비전문자 유지보수 어려움 | ✗ |
| D. 각 페이지 인라인 유지 | 변경 없음 | 현 상태 문제 지속 | ✗ |

**결론**: 방식 A (공통 JS 파일) 채택. 1인 운영자가 연 1회 JS 파일 1개만 수정하면 19개 페이지가 자동 반영.

---

## 3. 변경 감지·알림 체계

### 3.1 모니터링 대상 사이트

| 기관 | URL | 확인 항목 | 시기 |
|---|---|---|---|
| 최저임금위원회 | minimumwage.go.kr | 최저시급 | 매년 8월 고시, 1월 시행 |
| 국민건강보험공단 | nhis.or.kr | 건강보험·장기요양 요율 | 매년 12월 고시, 1월 시행 |
| 국민연금공단 | nps.or.kr | 연금 요율·상하한 | 요율: 매년 1월, 상한: 매년 7월 |
| 고용노동부 | moel.go.kr | 고용보험 요율 | 매년 12월 고시 |
| 국세청 | nts.go.kr | 소득세·증여세·상속세 세율, 간이세액표 | 매년 12월 세법개정안 국회 통과 후 |
| 기획재정부 | moef.go.kr | 세법 개정안 원문 | 매년 7월 발표, 12월 국회 |
| 행정안전부 | mois.go.kr | 취득세·자동차세 지방세법 | 수시 |
| 한국전력 | kepco.co.kr | 전기요금표 | 수시 (분기별 연료비 조정) |
| 금융위원회 | fsc.go.kr | DSR 규제, 금투세 | 수시 |

### 3.2 정기 점검 캘린더

| 시기 | 작업 |
|---|---|
| **매년 8월 둘째 주** | 최저임금 고시 확인 → `minimumWage.hourly` 예비 업데이트 |
| **매년 12월 셋째 주** | 세법개정안 국회 통과 확인 → 소득세 누진표·공제 변경 여부 체크 |
| **매년 12월 넷째 주** | 건강보험·장기요양·고용보험 요율 고시 확인 |
| **매년 1월 1~2주** | `tax-constants-2027.js` 확정 → 각 페이지 script src 경로 변경 → 테스트 → 배포 |
| **매년 7월 첫 주** | 국민연금 기준소득월액 상·하한 확인 → `insurance.nationalPension` 업데이트 |
| **매 분기 첫 주** | 한전 연료비조정액 확인 → `electricity.fuelAdjustment` 업데이트 |

### 3.3 변경 시 체크리스트

변경이 확인되면:

- [ ] `tax-constants-20XX.js`에서 해당 값 수정
- [ ] 변경된 상수를 사용하는 페이지 목록 확인 (이 문서 §1.2 참조)
- [ ] 각 페이지의 E-E-A-T 박스 "마지막 업데이트" 날짜 갱신
- [ ] 대표 테스트 케이스 3건 이상 수동 검증 (§4.2 참조)
- [ ] `git commit -m "chore: 2027년 세율 반영"` → `git push`
- [ ] Search Console에서 해당 URL 색인 재요청

---

## 4. 연도별 마이그레이션 SOP

### 4.1 "2027년 1월 세율 개정 시" 작업 순서

```
[12월 중순] 세법개정안 국회 통과
    ↓
[12월 말] tax-constants-2027.js 초안 작성
    ↓    (2026 파일 복사 → 변경값만 수정)
    ↓
[1월 1주] 각 페이지 <script src> 경로를 2027.js로 변경
    ↓    (sed -i 's/2026/2027/g' products/*/index.html)
    ↓
[1월 1주] 테스트 케이스 실행 (§4.2)
    ↓
[1월 1주] E-E-A-T 박스 일괄 날짜 갱신
    ↓    (sed -i 's/2026-01-15/2027-01-10/g' ...)
    ↓
[1월 1주] git commit → git push → GitHub Pages 배포
    ↓
[1월 2주] Search Console 색인 재요청 (19개 URL)
    ↓
[1월 2주] 블로그 "2027년 세율 업데이트" 공지 포스트
```

### 4.2 표준 테스트 케이스

각 계산기별 대표 입력 → 기대 출력. 연초 갱신 시 이 표로 검증:

| 계산기 | 입력 예시 | 2026 기대 출력 | 검증 대상 상수 |
|---|---|---|---|
| 실수령액 | 월급 300만, 부양 1인 | 세전3,000,000 → 실수령 ~2,640,000 | 4대보험·소득세 |
| 4대보험 | 월급 300만 | 국민연금 135,000, 건강 106,350, 장기요양 13,772, 고용 27,000 | 보험 요율 |
| 시급 | 시급 10,030 | 주급 562,880, 월급 ~2,096,270 | 최저임금 |
| 종합소득세 | 사업소득 5,000만 | 계산세액 ~582만 | 누진표 |
| 증여세 | 자녀에 1억 증여 | 과세표준 5천만, 세액 500만 | 증여공제·세율 |
| 전기요금 | 350kWh | ~56,000원대 | 전기단가 |
| 양도소득세 | 1주택 12억 초과 | 비과세 12억 차감 후 과세 | 비과세한도 |
| 취득세 | 1주택 7억 | 세율 ~1.67% | 취득세 구간 |

### 4.3 롤백 절차

문제 발견 시:
```bash
# 직전 커밋으로 롤백
git revert HEAD
git push

# 또는 특정 파일만 복원
git checkout HEAD~1 -- assets/data/tax-constants-2027.js
git commit -m "fix: 2027 상수 롤백"
git push
```

GitHub Pages는 push 후 1~2분 내 반영. 긴급 시 이전 연도 파일(2026.js)로 script src 복원.

---

## 5. 콘텐츠 신뢰 신호 강화

### 5.1 현재 상태

모든 26개 페이지에 E-E-A-T 박스가 있으나:
- 업데이트일이 정적 텍스트로 하드코딩
- 법령 근거가 일부 페이지에서 불명확 ("관련 법령 기준")
- 결과 카드 하단에는 업데이트일 미표시

### 5.2 일관화 방안

**결과 카드 하단 신뢰 배지 추가** (26개 페이지 공통):

```html
<div class="trust-badge">
  <span class="trust-date">마지막 업데이트: <span id="lastUpdated"></span></span>
  <span class="trust-source">근거: <span id="legalSource"></span></span>
</div>
<script>
  // tax-constants에서 자동 로드
  document.getElementById('lastUpdated').textContent = COCOFLOW.lastUpdated;
</script>
```

**페이지별 법령 근거 매핑** (`tax-constants.js`에 추가):

```js
COCOFLOW.sources = {
  'severance': '근로자퇴직급여 보장법, 근로기준법 제34조',
  'salary': '소득세법, 국민건강보험법, 국민연금법, 고용보험법',
  'insurance': '국민연금법, 국민건강보험법, 노인장기요양보험법, 고용보험법',
  'income-tax': '소득세법 제55조(세율), 제47조(공제)',
  'gift-tax': '상속세 및 증여세법 제26조, 제53조',
  'electricity': '한국전력 전기요금표 (2026년 기준)',
  // ... 19개 전체
};
```

### 5.3 업데이트일 자동 갱신 스크립트

`tax-constants-20XX.js` 수정 시 E-E-A-T 박스 날짜를 일괄 갱신하는 쉘 스크립트:

```bash
#!/bin/bash
# update-dates.sh — 상수 파일 변경 후 실행
NEW_DATE=$(date +%Y-%m-%d)

for f in products/*/index.html; do
  # E-E-A-T 박스의 날짜 패턴 교체
  sed -i "s/마지막 업데이트: [0-9]\{4\}-[0-9]\{2\}-[0-9]\{2\}/마지막 업데이트: $NEW_DATE/g" "$f"
done

echo "✅ $NEW_DATE 로 갱신 완료 ($(ls products/*/index.html | wc -l)개 파일)"
```

---

## 6. 로드맵

### P0: 1주 내 Quick Win

| # | 작업 | 소요 | 효과 |
|---|---|---|---|
| 1 | `assets/data/tax-constants-2026.js` 파일 생성 (§2.1 구조) | 2시간 | 상수 중앙 관리 기반 |
| 2 | 가장 중복 심한 **종합소득세 누진표** 6개 파일 → 공통 상수 참조로 전환 | 3시간 | 6개 파일 중복 제거 |
| 3 | **4대보험 요율** 2개 파일 → 공통 상수 참조 전환 | 1시간 | 2개 파일 중복 제거 |
| 4 | `update-dates.sh` 스크립트 생성 | 30분 | 날짜 갱신 자동화 |
| 5 | 표준 테스트 케이스 표 작성 (§4.2 확장) → `TEST_CASES.md` | 1시간 | 연초 검증 기반 |

### P1: 1개월 내 구조 개선

| # | 작업 | 소요 | 효과 |
|---|---|---|---|
| 6 | 나머지 13개 계산기의 하드코딩 상수 → 공통 파일 참조 전환 | 8시간 | 19개 전체 통합 |
| 7 | 결과 카드 하단 신뢰 배지 26개 페이지 일괄 삽입 | 3시간 | 신뢰도 UP |
| 8 | 정기 점검 캘린더를 Google Calendar에 반복 일정으로 등록 | 30분 | 놓침 방지 |
| 9 | `CHANGELOG.md` 생성 — 세율 변경 이력 기록 시작 | 30분 | 투명성 |

### P2: 3개월 이상 장기 리팩터

| # | 작업 | 소요 | 효과 |
|---|---|---|---|
| 10 | GitHub Actions 자동화: `tax-constants` 변경 시 → 테스트 케이스 자동 실행 | 4시간 | 실수 방지 |
| 11 | 연도 전환 자동화 스크립트 (§4.1 SOP를 쉘 스크립트화) | 2시간 | 연초 30분 완료 |
| 12 | 법령 변경 RSS/뉴스 알림 설정 (국세청·한전 등) | 2시간 | 조기 감지 |
| 13 | 각 계산기에 "이전 연도 기준으로 계산" 토글 추가 | 8시간+ | 사용자 편의 |

---

## 부록: 상수 없는 계산기 (변경 불필요)

다음 7개 계산기는 순수 수학/날짜 로직만 사용하며, 법령 변경 영향 없음:

1. BMI 계산기 (WHO 기준, 10년+ 불변)
2. 칼로리 계산기 (Mifflin-St Jeor 공식)
3. D-Day 계산기 (날짜 연산)
4. 출산예정일 계산기 (280일)
5. 만나이 계산기 (날짜 연산)
6. 복리 계산기 (수학 공식)
7. 대출이자 계산기 (사용자가 금리 입력)

---

> **작성일**: 2026-04-19
> **다음 점검 예정**: 2026-07-01 (국민연금 상한 변경 시)
> **연초 대비 시작**: 2026-12-15 (세법개정안 통과 후)
