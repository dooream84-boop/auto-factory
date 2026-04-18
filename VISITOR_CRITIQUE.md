# 접속자 관점 날카로운 비판 (2026-04-19)

> 전제: 블로그·네이버 검색에서 처음 들어온 모바일 방문자 시점. 칭찬 0, 단점만.
> 분석 대상: 26개 계산기 전체 HTML/CSS/JS 소스 코드

---

## 1. TOP 10 가장 아픈 비판 (사이트 전체 공통, 영향도 순)

### #1. 금액 입력에 쉼표(천 단위 구분) 없음 — 24/26개 페이지

- **문제**: DSR·취득세 계산기 2개만 `formatNumber()` 적용. 나머지 24개는 `<input type="number">`에 쉼표 없이 8~9자리 숫자를 타이핑해야 함
- **방문자 인상**: "100000000을 입력하다 0을 하나 잘못 세면 퇴직금이 10배 차이. 이 사이트 못 믿겠다"
- **권장 수정안**: DSR 계산기의 `formatNumber()`/`toNumber()` 헬퍼를 `assets/js/format.js`로 추출 → 24개 페이지 `<input type="text" inputmode="numeric">` + `oninput="formatNumber(this)"` 적용
- **우선순위**: **P0 즉시** (가장 높은 사용성 영향)

### #2. 증여세 계산기에 존재하지 않는 지방소득세 10% 부과

- **문제**: `gift-tax-calculator/index.html` 증여세 탭에서 `localTax = Math.round(tax * 0.1)` (약 617줄). 증여세는 국세이며 지방소득세 대상이 아님
- **방문자 인상**: "국세청 계산기랑 결과가 다르다. 10% 더 나오는데?" → 신뢰 완전 상실
- **권장 수정안**: 증여세 탭의 `localTax` 계산 라인 삭제, 결과 표시에서 "지방소득세" 행 제거. 상속세 탭은 정상(상속세도 국세이므로 동일하게 확인 필요)
- **우선순위**: **P0 즉시** (계산 오류)

### #3. 양도소득세 장기보유특별공제 공식 오류 가능성

- **문제**: `capital-gains-tax-calculator/index.html` 606~609줄 `getLTHoldRate` 함수: `(years - 2) * 0.02`. 3년 보유 시 2% 반환하지만, 실제 법(소득세법 제95조)은 3년 보유 시 6%(연 2% × 3년)
- **방문자 인상**: "양도세가 경쟁 사이트보다 수백만 원 높게 나온다" → 이탈
- **권장 수정안**: 정확한 공식 검증 후 수정. 현행법 기준 일반자산 보유기간별 공제율표와 대조 필요
- **우선순위**: **P0 즉시** (금액 오류)

### #4. 모든 에러 메시지가 `alert()` 팝업 — 26/26개 페이지

- **문제**: 빈 입력·음수·잘못된 값 → 네이티브 `alert()` 팝업. 모바일에서 화면 전체를 가리고, 확인 탭 후 입력 포커스 유실
- **방문자 인상**: "옛날 사이트 같다. 다른 계산기 찾아야겠다"
- **권장 수정안**: `alert()` → 해당 입력 필드 바로 아래 빨간 텍스트 `.error-msg` 인라인 표시. CSS: `color: #dc2626; font-size: 0.82rem;`
- **우선순위**: **P1 이번 주**

### #5. 12개 계산기에서 Enter 키 미지원

- **문제**: 퇴직금·실수령액·대출이자·4대보험·BMI·전기요금·출산예정일·D-Day·상속세·만나이·중도퇴사·해외주식 양도세 — Enter 누르면 아무 반응 없음
- **방문자 인상**: 숫자 입력 후 자연스럽게 Enter → 아무 일도 안 일어남 → 계산 버튼 찾아 스크롤
- **권장 수정안**: 각 페이지 `<script>` 말미에 `document.addEventListener('keydown', e => { if(e.key === 'Enter') { e.preventDefault(); calculate(); } });` 추가 (이미 14개 페이지에 있는 패턴)
- **우선순위**: **P1 이번 주**

### #6. 전기요금 계산기 "여름" 탭이 일반 탭과 동일 결과

- **문제**: `electricity-calculator/index.html` 407~417줄. 여름 요금 배열이 일반과 완전 동일. 사용자가 탭 전환 → 같은 결과 → "여름엔 더 비싼 거 아니었나?"
- **방문자 인상**: "탭이 있어서 눌러봤더니 같은 결과. 이 사이트 제대로 만든 건가?"
- **권장 수정안**: (1) 2026년 기준 여름 요금이 실제로 동일하면 여름 탭 제거하고 안내 문구 추가 "2026년 기준 계절별 차등 요금 미적용", (2) 다르다면 실제 여름 단가 반영
- **우선순위**: **P1 이번 주**

### #7. 4개 계산기 footer 링크 404 오류

- **문제**: 대출이자·BMI·전기요금·출산예정일 계산기의 footer가 `../privacy-policy.html`, `../index.html`로 링크 → 404. 나머지 22개는 `/privacy/` 정상
- **방문자 인상**: "개인정보처리방침 링크가 깨져 있다" → 사이트 관리 안 하는 느낌
- **권장 수정안**: 4개 파일 footer를 나머지 22개와 동일한 형식으로 교체 (`/privacy/`, `/disclosure/`, `mailto:cocoflow.contact@gmail.com`)
- **우선순위**: **P0 즉시** (5분 소요)

### #8. 자동차세 연납할인율 구버전 가능성

- **문제**: `automobile-tax-calculator/index.html` 467줄 `prepayMap = {0:0, 1:7, 2:5, 3:3, 4:2}`. 2023년부터 연납할인율 축소 (1월 기준 실질 ~4.58%), 7%는 2022년 이전 값
- **방문자 인상**: "1월 연납 시 7% 할인이라 해서 납부했는데 실제 4.58%밖에 안 됐다" → 항의
- **권장 수정안**: 2026년 실제 연납할인율(지방세법 제128조 개정분) 확인 후 반영. 2025~2027년은 5%→3% 단계적 축소 중
- **우선순위**: **P0 즉시** (수치 정확성)

### #9. 플로팅 공유 바가 360px 모바일에서 하단 콘텐츠 가림

- **문제**: 26개 전체에 `position:fixed; bottom:16px; right:16px` 공유 바(카톡·링크복사·인쇄). 360px 폰에서 계산 버튼이나 결과 카드 하단과 겹침
- **방문자 인상**: "계산하기 버튼 누르려는데 공유 버튼이 눌린다"
- **권장 수정안**: `@media (max-width: 480px)` 에서 `bottom: 70px`으로 올리거나, 스크롤 시에만 표시 (scroll event + opacity 전환)
- **우선순위**: **P2 이번 달**

### #10. 붙여넣기한 쉼표 포함 숫자가 조용히 잘못된 결과 생성

- **문제**: 급여명세서에서 "3,000,000"을 복사해 붙여넣으면 `parseFloat("3,000,000")` → `3` 반환. 에러 메시지 없이 퇴직금 100원 같은 황당한 결과 출력
- **방문자 인상**: "퇴직금이 100원? 사기 사이트인가?" → 즉시 이탈
- **권장 수정안**: #1과 동일. `toNumber()` 헬퍼로 쉼표 제거 후 파싱. 또는 `input type="text"` + 입력 시 자동 포매팅
- **우선순위**: **P0 즉시** (#1과 동시 해결)

---

## 2. 계산기별 개별 이슈 (문제 있는 것만)

### 퇴직금 계산기 (`severance-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 885 | `parseFloat(value)` 쉼표 미처리 | `toNumber()` 적용 | P0 |
| 861 | 3개월 급여 0 입력 시 경고 없이 진행 | `if(s1 === 0 \|\| s2 === 0 \|\| s3 === 0) alert()` | P1 |
| - | Enter 키 미지원 | keydown 리스너 추가 | P1 |

### 실수령액 계산기 (`salary-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 813 | `parseInt(dependents)` NaN 미처리 | `parseInt(dependents) \|\| 1` 기본값 | P1 |
| - | Enter 키 미지원 | keydown 리스너 추가 | P1 |

### 대출이자 계산기 (`loan-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 466 | 금리 0% 거부 (무이자 대출 불가) | 조건을 `annualRate < 0`으로 변경 | P2 |
| 449 | footer "All rights reserved." 브랜드 불일치 | 타 페이지 footer 복사 | P0 |
| - | Enter 키 미지원 | keydown 리스너 추가 | P1 |

### 4대보험 계산기 (`insurance-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 505 | 국민연금 하한 39만원 미적용 | `Math.max(390000, Math.min(sal, 6170000))` | P1 |
| 562 | 사업주 고용보험 1.15% 고정 (규모별 차등 미반영) | "150인 이상 사업장은 0.25%p 추가" 안내 문구 | P2 |

### BMI 계산기 (`bmi-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 405~406 | footer `../privacy-policy.html` 404 | `/privacy/` 로 변경 | P0 |
| 437 | 표준체중과 정확히 같을 때 빈 문자열 | `diff === 0` 분기 추가 | P2 |

### 전기요금 계산기 (`electricity-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 407~417 | 여름 탭 = 일반 탭 동일 결과 | 탭 제거 또는 안내 문구 | P1 |
| 397~398 | footer 404 링크 | `/privacy/` 로 변경 | P0 |

### 양도소득세 계산기 (`capital-gains-tax-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 606~609 | 장기보유공제 `(years-2)*0.02` 오류 의심 | 법 대조 후 수정 | P0 |
| - | 유일하게 `scrollIntoView` 없음 → 모바일 결과 안 보임 | `scrollIntoView({behavior:'smooth'})` 추가 | P0 |

### 증여세·상속세 통합 (`gift-tax-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 617 | 증여세에 지방소득세 10% 잘못 부과 | `localTax` 라인 삭제 | P0 |
| 655~656 | 상속세 인적공제에 배우자 미포함 | 자녀+배우자 합산 로직 확인 | P1 |

### 출산예정일 계산기 (`due-date-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 398~399 | footer 404 링크 | `/privacy/` 로 변경 | P0 |

### 적금이자 계산기 (`savings-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 377 부근 | E-E-A-T 박스 "세전 기준" 표기 vs 실제 세후 계산 | "이자소득세(15.4%) 차감 후 세후 금액 표시" 문구로 변경 | P1 |

### 자동차세 계산기 (`automobile-tax-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 467 | 1월 연납할인 7% → 2026년 실제값 확인 필요 | 지방세법 제128조 2026년 기준 적용 | P0 |

### 칼로리 계산기 (`calorie-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 434 | 나이 0·음수·200 입력 허용 | `age < 10 \|\| age > 120` 범위 검증 | P2 |
| 442~443 | TDEE-500이 1200kcal 미만일 수 있음 (위험) | `Math.max(1200, tdee-500)` + 경고 문구 | P1 |

### 시급 계산기 (`hourly-wage-calculator/index.html`)
| 줄 | 문제 | 수정안 | 우선순위 |
|---|---|---|---|
| 514 | 주 40시간 초과 시 주휴수당 과다 계산 | 40시간 cap 적용 | P1 |

---

## 3. 즉시 수정 가능한 5건 (1시간 내 패치)

### Fix 1: footer 404 링크 4개 파일 수정 (10분)

**대상**: `loan-calculator`, `bmi-calculator`, `electricity-calculator`, `due-date-calculator`

각 파일 `<footer>` 영역을 아래로 교체:
```html
<footer>
  <p><a href="/">COCOFLOW</a></p>
  <p><a href="/privacy/">개인정보처리방침</a> · <a href="/disclosure/">광고·제휴 고지</a> · <a href="mailto:cocoflow.contact@gmail.com">문의하기</a></p>
  <p>&copy; 2026 CocoFlow. All rights reserved.</p>
</footer>
```

### Fix 2: Enter 키 지원 12개 파일 추가 (15분)

**대상**: severance, salary, loan, insurance, bmi, electricity, due-date, dday, inheritance-tax, korean-age, midterm-leave, overseas-stock-tax

각 파일 `</script>` 직전에 추가:
```js
document.addEventListener('keydown', function(e) {
  if (e.key === 'Enter') { e.preventDefault(); /* 해당 페이지 계산 함수명 */ }
});
```

### Fix 3: 증여세 지방소득세 오류 제거 (5분)

**파일**: `gift-tax-calculator/index.html`
- 617줄 `const localTax = Math.round(tax * 0.1);` → 삭제 또는 `const localTax = 0;`
- 결과 표시의 "지방소득세" 행 제거
- 총 납부세액에서 `localTax` 제외

### Fix 4: 양도소득세 결과 scrollIntoView 추가 (3분)

**파일**: `capital-gains-tax-calculator/index.html`

각 탭의 계산 함수 결과 표시 직후에 추가:
```js
document.getElementById('결과카드ID').scrollIntoView({ behavior: 'smooth', block: 'start' });
```

### Fix 5: 적금이자 E-E-A-T 박스 문구 수정 (2분)

**파일**: `savings-calculator/index.html`

"세전 기준" → "이자소득세(15.4%) 반영 세후 기준" 으로 변경

---

## 4. 구조적 개선 3건 (1~2주 단위)

### 구조개선 1: 공통 입력 포매팅 시스템 (1주)

**문제**: 24개 페이지에서 금액 입력에 쉼표 없음 + 붙여넣기 시 `parseFloat` 오작동

**작업 내용**:
1. `assets/js/input-format.js` 생성:
   ```js
   function formatNumber(el) {
     let v = el.value.replace(/[^0-9]/g, '');
     el.value = v ? Number(v).toLocaleString('ko-KR') : '';
   }
   function toNumber(el) {
     return Number(el.value.replace(/[^0-9]/g, '')) || 0;
   }
   ```
2. 24개 파일의 금액 `<input>` 변경:
   - `type="number"` → `type="text" inputmode="numeric"`
   - `oninput="formatNumber(this)"` 추가
3. 각 계산 함수의 `parseFloat(el.value)` → `toNumber(el)` 교체
4. 테스트: 빈 입력, 0, 1, 999,999,999,999, 붙여넣기 "3,000,000"

**기대 효과**: 사용성 대폭 개선, 입력 실수 90% 감소

### 구조개선 2: 인라인 에러 메시지 시스템 (1주)

**문제**: 26개 페이지 모두 `alert()` 사용

**작업 내용**:
1. 공통 CSS 추가:
   ```css
   .error-msg { color: #dc2626; font-size: 0.82rem; margin-top: 4px; display: none; }
   .error-msg.show { display: block; }
   .input-wrap.error input { border-color: #dc2626; }
   ```
2. 각 `<input>` 하위에 `<p class="error-msg" id="err-필드명"></p>` 추가
3. `alert()` 호출부를:
   ```js
   function showError(fieldId, msg) {
     document.getElementById('err-' + fieldId).textContent = msg;
     document.getElementById('err-' + fieldId).classList.add('show');
     document.getElementById(fieldId).closest('.input-wrap').classList.add('error');
   }
   ```
4. 입력 변경 시 에러 자동 제거: `oninput="clearError(this)"`

### 구조개선 3: 세율·요율 상수 중앙 관리 (2주)

**문제**: 종합소득세 누진표가 6개 파일에 중복 하드코딩. 매년 1월 세법 개정 시 6곳 수동 수정

**작업 내용**: → LAW_UPDATE_PLAN.md에서 상세 설계

---

> **작성일**: 2026-04-19
> **분석 범위**: 26개 계산기 HTML/CSS/JS 전체 소스
> **분석 관점**: 블로그·검색 유입 모바일 첫 방문자
