# 나끌 디자인 시스템

> 오디오 + 텍스트 동시 독서 앱 **나끌** 의 디자인 시스템 명세.
> 원본: Figma `나끌_디자인시스템` · 기준일 2026-11-20

## 0. 기본 전제

| 항목 | 내용 |
|---|---|
| 플랫폼 | iOS · Android 모바일 앱 |
| 기준 화면 | iPhone 14 (390 × 844) |
| 단위 | Figma px = iOS pt = Android dp (글자는 sp), 1:1 대응 |
| 테마 | 기본(Light) 모드만 지원. 고대비 모드는 다음 단계 예정, 다크 모드는 지원하지 않음 |
| 글꼴 | Pretendard (`font/family`) |
| 브랜드 색 | 버밀리온 `primary/40` #FF4030 |
| 시스템 아이콘 | Lucide 기반 24px 선(line), 선 두께 2px |
| 접근성 기준 | WCAG 2.1 AA — 본문 4.5:1, 큰 글자·그래픽 3:1, 터치 영역 최소 48×48 |

### 토큰 구조

- **primitive** → 원본 색 팔레트. 화면에 직접 쓰지 않는다.
- **semantic** → 화면에서 쓰는 의미 토큰(`color/text/*`, `color/icon/*` …). primitive를 참조한다.
- 컴포넌트 전용 토큰(`color/button/*` 등)이 있으면 primitive보다 우선 적용한다.
- 표에 적힌 수치는 iPhone 14 기준 예시이며, 세이프 영역·기기 폭에 따라 달라지는 런타임 고정값이 아니다.

---

## 1. 공통 규칙

### 터치 영역
누를 수 있는 요소는 보이는 크기와 별도로 최소 48×48의 터치 영역을 확보한다(iOS 44pt·Android 48dp 기준을 함께 충족). 아이콘 버튼은 `icon-button` 컴포넌트를 사용하고, 텍스트 버튼(높이 20~40)·40/32 버튼·요약 듣기(높이 20)처럼 보이는 크기가 작은 요소는 구현에서 위아래로 터치 영역을 넓힌다. 이웃한 버튼끼리 터치 영역이 겹치지 않도록 간격을 둔다.

### 버튼 상태
hover는 마우스 같은 포인터 환경에서만 사용하고, 모바일 기본 상태는 default·pressed·disabled다. 저장·결제처럼 결과를 기다리는 버튼은 누른 뒤 loading으로 바꾸고 완료 전까지 다시 누를 수 없게 한다. 키보드·스위치 같은 보조 입력에는 focus 상태를 primary/40 2px 테두리로 표시한다. 모든 상태(비활성 제외)의 라벨은 배경과 4.5:1 이상을 유지하며, hover·pressed에서 배경이 한 단계 진해지면 글자도 `color/text/primary-hover`·`primary-pressed`로 한 단계 진하게 바꾼다.

**loading** — 라벨 자리에 16px 스피너(라벨과 같은 색)를 가운데 두고, 버튼 폭은 누르기 전 폭을 유지한다. 배경·색은 default와 같고, loading 동안 탭은 무시한다. 스크린리더에는 "처리 중"을 전달한다.
**focus** — 보조 입력에서만 표시. 버튼 바깥 2px 간격에 `color/focus/ring`(primary/40) 2px 테두리, 모서리는 버튼 radius + 2. 어두운 배경 위에서는 white 2px 테두리.

### 탭바
컴포넌트 전체 높이 70px은 탭 영역 56px(`layout/tab-bar`)과 바깥 패딩 위아래 7px로 이루어진다. 그 아래 홈 인디케이터 영역(34px, 기기별 Safe Area)은 탭바 높이에 포함하지 않는다. 미니 플레이어는 탭바 위 16px(`layout/item-gap`) 간격에 둔다. 선택된 탭은 스크린리더에 탭 이름과 함께 "선택됨"을 전달한다.

### 투명도
투명하게 만드는 방법은 두 가지로 나눠 쓴다.
1. **색상 알파** — 배경·딤·글래스 표면처럼 특정 색 하나만 비쳐야 할 때는 `color/overlay/*`·`color/surface/glass` 같은 색상 변수를 쓴다.
2. **레이어 투명도** — 아이콘·버튼·묶음 전체를 흐리게 할 때는 `opacity/*` 변수를 쓴다(Figma 투명도 변수는 0~100 단위).

본문과 필수 정보에는 `opacity/subtle`(60%) 이하를 쓰지 않으며, 비활성 전용 색(gray/40 등)을 이미 쓴 요소에 `opacity/disabled`를 겹쳐 적용하지 않는다.

---

## 2. 색상

Primary 색상은 나끌의 브랜드 컬러인 버밀리온으로, 재생·구매 같은 주요 액션과 현재 진행 상태를 강조하는 데 사용한다. 흰 텍스트와 함께 쓰는 버튼 배경에는 명도 대비를 위해 `primary/50`을 사용한다.

**대비 판정** — AA/AAA 등급은 색 하나가 아니라 글자색·배경색·글자 크기의 조합으로 판정한다.

| 조합 | 대비 | 사용 |
|---|---|---|
| 흰 글자 / primary-50 | 4.72:1 | AA, 본문 가능 |
| 흰 글자 / primary-40 | 3.48:1 | 본문 불가 — 18.66px Bold·24px 이상 큰 글자, 아이콘, 장식에만 |
| color/text/tertiary(gray/70) / 흰 배경 | 5.17:1 | 보조 정보 텍스트 |
| color/text/tertiary / primary-5 배경 | 4.54:1 | 선택 행의 보조 텍스트 |

### 2-1. primitive — `primitive` (Value)

| 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `gray/0` | — | #FFFFFF | 회색 0단계 |
| `gray/5` | — | #FCFCFC | 회색 5단계 |
| `gray/10` | — | #E8E8E8 | 회색 10단계 |
| `gray/20` | — | #D3D3D3 | 회색 20단계 |
| `gray/30` | — | #BFBFBF | 회색 30단계 |
| `gray/40` | — | #ABABAB | 회색 40단계 |
| `gray/50` | — | #969696 | 회색 50단계 |
| `gray/60` | — | #828282 | 회색 60단계 |
| `gray/70` | — | #6D6D6D | 회색 70단계 |
| `gray/80` | — | #595959 | 회색 80단계 |
| `gray/90` | — | #454545 | 회색 90단계 |
| `gray/95` | — | #1F1F1F | 회색 95단계 |
| `gray/100` | — | #0F0F0F | 회색 100단계 |
| `primary/0` | — | #FFFFFF | primary 0단계 |
| `primary/5` | — | #FFECEA | primary 5단계 |
| `primary/10` | — | #FFC1BC | primary 10단계 |
| `primary/20` | — | #FF968D | primary 20단계 |
| `primary/30` | — | #FF6B5E | primary 30단계 |
| `primary/40` | — | #FF4030 | **brand base** |
| `primary/50` | — | #DD2C1E | primary 50단계 |
| `primary/60` | — | #BB1C0F | primary 60단계 |
| `primary/70` | — | #990F04 | primary 70단계 |
| `primary/80` | — | #770900 | primary 80단계 |
| `primary/90` | — | #550700 | primary 90단계 |
| `primary/95` | — | #310400 | primary 95단계 |
| `primary/100` | — | #000000 | primary 100단계 |
| `danger/0` | — | #FFFFFF | 오류 0단계 |
| `danger/5` | — | #FFEAEC | 오류 5단계 |
| `danger/10` | — | #FFBCC2 | 오류 10단계 |
| `danger/20` | — | #FF8D99 | 오류 20단계 |
| `danger/30` | — | #FF5E6F | 오류 30단계 |
| `danger/40` | — | #FF384C | **danger base** |
| `danger/50` | — | #DD1E31 | 오류 50단계 |
| `danger/60` | — | #BB0F20 | 오류 60단계 |
| `danger/70` | — | #990413 | 오류 70단계 |
| `danger/80` | — | #77000C | 오류 80단계 |
| `danger/90` | — | #550009 | 오류 90단계 |
| `danger/95` | — | #340005 | 오류 95단계 |
| `danger/100` | — | #1D0003 | 오류 100단계 |
| `success/0` | — | #FFFFFF | 성공 0단계 |
| `success/5` | — | #ECFFF1 | 성공 5단계 |
| `success/10` | — | #C3FFD2 | 성공 10단계 |
| `success/20` | — | #99FFB3 | 성공 20단계 |
| `success/30` | — | #70FF94 | 성공 30단계 |
| `success/40` | — | #43F26F | **success base** |
| `success/50` | — | #2FD058 | 성공 50단계 |
| `success/60` | — | #1FAE43 | 성공 60단계 |
| `success/70` | — | #118830 | 성공 70단계 |
| `success/80` | — | #086A21 | 성공 80단계 |
| `success/90` | — | #024814 | 성공 90단계 |
| `success/95` | — | #00300C | 성공 95단계 |
| `success/100` | — | #001907 | 성공 100단계 |
| `graphic/gray/1` | — | #EFEFEF | 그래픽·일러스트 전용 |
| `graphic/gray/2` | — | #CDC8C8 | 그래픽·일러스트 전용 |
| `graphic/gray/3` | — | #ABA2A2 | 그래픽·일러스트 전용 |
| `graphic/gray/4` | — | #897E7E | 그래픽·일러스트 전용 |
| `graphic/gray/5` | — | #675C5C | 그래픽·일러스트 전용 |
| `graphic/red/1` | `primary/10` | #FFC1BC | 그래픽·일러스트 전용 |
| `graphic/red/2` | `primary/20` | #FF968D | 그래픽·일러스트 전용 |
| `graphic/red/3` | `primary/30` | #FF6B5E | 그래픽·일러스트 전용 |
| `graphic/red/4` | `primary/40` | #FF4030 | 그래픽·일러스트 전용 |
| `graphic/red/5` | `primary/50` | #DD2C1E | 그래픽·일러스트 전용 |
| `alpha/black-5` | — | #000000 5% | 투명 원본 |
| `alpha/black-8` | — | #000000 8% | 투명 원본 |
| `alpha/black-12` | — | #000000 12% | 투명 원본 |
| `alpha/black-20` | — | #000000 20% | 투명 원본 |
| `alpha/black-30` | — | #000000 30% | 투명 원본 |
| `alpha/black-40` | — | #000000 40% | 투명 원본 |
| `alpha/white-20` | — | #FFFFFF 20% | 투명 원본 |
| `alpha/white-60` | — | #FFFFFF 60% | 투명 원본 |
| `alpha/gray70-70` | — | #707070 70% | 투명 원본 |
| `alpha/primary10-75` | — | #FFC1BC 75% | 투명 원본(글래스 그래픽) |
| `alpha/primary40-85` | — | #FF4030 85% | 투명 원본(글래스 그래픽) |

### 2-2. semantic — `semantic` (Light)

**텍스트**

| 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `color/text/bolder` | `gray/100` | #0F0F0F | 가장 진한 제목 |
| `color/text/basic` | `gray/95` | #1F1F1F | 기본 본문 |
| `color/text/secondary` | `gray/95` | #1F1F1F | 보조 본문 |
| `color/text/subtle` | `gray/90` | #454545 | 설명 글 |
| `color/text/tertiary` | `gray/70` | #6D6D6D | 보조 정보(작가명·시간·미선택 탭·보조 링크). 흰 배경 5.17:1, primary/5 배경 4.54:1 |
| `color/text/primary` | `primary/60` | #BB1C0F | 강조 글자 |
| `color/text/primary-hover` | `primary/70` | #990F04 | 버튼 hover 라벨 (primary/10 배경 위 5.61:1) |
| `color/text/primary-pressed` | `primary/80` | #770900 | 버튼 pressed 라벨 (primary/20 배경 위 5.48:1) |
| `color/text/danger` | `danger/50` | #DD1E31 | 오류 메시지 |
| `color/text/success` | `success/70` | #118830 | 성공 메시지 |
| `color/text/disabled` | `gray/60` | #828282 | 비활성 글자 |
| `color/text/disabled-on` | `gray/70` | #6D6D6D | 비활성 배경 위 글자 |
| `color/text/inverse-static` | `gray/0` | #FFFFFF | 어두운 배경 위 글자 |
| `color/text/basic-inverse` | `gray/0` | #FFFFFF | 어두운 배경 위 글자 |

**아이콘**

| 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `color/icon/basic` | `gray/100` | #0F0F0F | 시스템 아이콘 기본 |
| `color/icon/primary` | `primary/40` | #FF4030 | 선택·강조 (흰 배경 3.48:1, 그래픽 3:1 충족) |
| `color/icon/subtle` | `gray/60` | #828282 | 보조 아이콘 (흰 배경 3.84:1) |
| `color/icon/disabled` | `gray/40` | #ABABAB | 비활성 (대비 기준 제외) |
| `color/icon/danger` | `danger/50` | #DD1E31 | 오류·재시도 |
| `color/icon/inverse-static` | `gray/0` | #FFFFFF | 어두운 배경 위 아이콘 |

**배경·면·선**

| 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `color/background/white` | `gray/0` | #FFFFFF | 화면 배경 |
| `color/surface/white-subtler` | `gray/0` | #FFFFFF | 카드·패널 면 |
| `color/surface/gray-subtler` | `gray/5` | #FCFCFC | 회색 면 |
| `color/surface/glass` | `alpha/white-60` | #FFFFFF 60% | 글래스 표면(+배경 블러) — 탭바. 미선택 라벨 대비를 위해 55% 이상 유지 |
| `color/border/gray` | `gray/30` | #BFBFBF | 기본 테두리 |
| `color/border/glass` | `alpha/white-20` | #FFFFFF 20% | 글래스 표면 테두리 — 탭바 |
| `color/border/transparency` | — | 투명 | 자리만 잡는 투명 테두리 |
| `color/divider/gray-light` | `gray/20` | #D3D3D3 | 연한 구분선 |
| `color/divider/gray` | `gray/50` | #969696 | 구분선 |
| `color/divider/secondary` | `gray/90` | #454545 | 진한 구분선 |
| `color/focus/ring` | `primary/40` | #FF4030 | 포커스 링 (`border-width/focus` 2px, 바깥 2px) |

**진행률·오버레이**

| 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `color/progress/track` | `gray/10` | #E8E8E8 | 진행 바 남은 구간 (밝은 면 위) |
| `color/progress/fill` | `primary/40` | #FF4030 | 진행 바 진행한 구간 |
| `color/overlay/scrim` | `alpha/black-40` | #000000 40% | 모달·바텀시트 뒤 딤 |
| `color/overlay/on-image` | `alpha/black-30` | #000000 30% | 이미지 위 반투명 배경 (표지 위 진도 바 트랙 등) |
| `color/overlay/on-image-subtle` | `alpha/black-20` | #000000 20% | 이미지 위 약한 배경 (책 카드 오디오북 배지) |
| `color/overlay/pressed` | `alpha/black-8` | #000000 8% | 눌림 덧씌움 (icon-button pressed) |
| `color/overlay/time-backdrop` | `alpha/gray70-70` | #707070 70% | 웨이브폼 시간 표시 뒤 배경(+블러) |

**버튼**

| 토큰 | 참조 | 값 |
|---|---|---|
| `color/button/primary-fill` | `primary/50` | #DD2C1E |
| `color/button/primary-fill-hover` | `primary/60` | #BB1C0F |
| `color/button/primary-fill-pressed` | `primary/70` | #990F04 |
| `color/button/secondary-fill` | `primary/5` | #FFECEA |
| `color/button/secondary-fill-hover` | `primary/10` | #FFC1BC |
| `color/button/secondary-fill-pressed` | `primary/20` | #FF968D |
| `color/button/secondary-border` | `primary/50` | #DD2C1E |
| `color/button/tertiary-fill` | — | 투명 |
| `color/button/tertiary-fill-hover` | `gray/10` | #E8E8E8 |
| `color/button/tertiary-fill-pressed` | `gray/20` | #D3D3D3 |
| `color/button/tertiary-border` | `gray/80` | #595959 |
| `color/button/gray-fill` | `gray/100` | #0F0F0F |
| `color/button/gray-fill-hover` | `gray/95` | #1F1F1F |
| `color/button/gray-fill-pressed` | `gray/90` | #454545 |
| `color/button/text-fill` | — | 투명 |
| `color/button/text-fill-hover` | `primary/5` | #FFECEA |
| `color/button/text-fill-pressed` | `primary/10` | #FFC1BC |
| `color/button/disabled-fill` | `gray/20` | #D3D3D3 |

**그래픽**

| 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `color/graphic/glass-fill` | `alpha/primary10-75` | #FFC1BC 75% | 카테고리 글래스 아이콘 앞면 |
| `color/graphic/glass-fill-strong` | `alpha/primary40-85` | #FF4030 85% | 글래스 아이콘 앞면 강조 (new) |
| `color/graphic/glass-edge` | `alpha/white-60` | #FFFFFF 60% | 글래스 아이콘 테두리 하이라이트 |
| `color/light/graphic/90` | `graphic/gray/5` | #675C5C | 그래픽 진한 회색 |

---

## 3. 타이포그래피

헤딩 스타일 6종(xlarge~xxsmall)은 시각적 크기 단계이고, 의미상 제목 단계(h1~h6)와는 따로 정한다. 화면 구조에 맞는 제목 단계를 먼저 정한 뒤 맥락에 맞는 크기의 스타일을 고른다.

본문 굵기는 Regular(400)·Medium(500)·SemiBold(600)·Bold(700). 크기별 지원 굵기는 large·medium이 400·500·700, small·xsmall이 400·500·600·700, xxsmall(11px)이 400·500이다. xxsmall은 작은 카드의 보조 정보·탭바 라벨처럼 공간이 좁은 곳에만 쓴다.

행간과 자간은 글자 크기에 대한 비율(**행간 150%, 자간 -2%**)로 정의하고 개발 전달 시 절대값으로 환산한다(예: 17px → 행간 25.5px, 자간 -0.34px). 시스템 글자 크기 조절(Dynamic Type·글꼴 크기) 시에도 비율이 유지되도록 pt/sp 기준으로 확대한다.

### 3-1. 텍스트 스타일

| 스타일 | 크기 | 굵기 | 행간 | 자간 |
|---|---|---|---|---|
| `heading/xlarge` | 28 | Bold | 42 (150%) | -0.56 (-2%) |
| `heading/large` | 24 | Bold | 36 | -0.48 |
| `heading/medium` | 22 | Bold | 33 | -0.44 |
| `heading/small` | 19 | Bold | 28.5 | -0.38 |
| `heading/xsmall` | 17 | Bold | 25.5 | -0.34 |
| `heading/xxsmall` | 15 | Bold | 22.5 | -0.30 |
| `body/large-400` · `-500` · `-700` | 19 | 400 · 500 · 700 | 28.5 | -0.38 |
| `body/medium-400` · `-500` · `-700` | 17 | 400 · 500 · 700 | 25.5 | -0.34 |
| `body/small-400` · `-500` · `-600` · `-700` | 15 | 400 · 500 · 600 · 700 | 22.5 | -0.30 |
| `body/xsmall-400` · `-500` · `-600` · `-700` | 13 | 400 · 500 · 600 · 700 | 19.5 | -0.26 |
| `body/xxsmall-400` · `-500` | 11 | 400 · 500 | 16.5 | -0.22 |
| `label/large` | 19 | Regular | 28.5 | -0.38 |
| `label/medium` | 17 | Regular | 25.5 | -0.34 |
| `label/small` | 15 | Regular | 22.5 | -0.30 |
| `label/xsmall` | 13 | Regular | 19.5 | -0.26 |

### 3-2. 타이포 변수 — `typography` (Mobile)

| 토큰 | 값 |
|---|---|
| `font/family` | Pretendard |
| `font/weight/regular` · `medium` · `semibold` · `bold` | 400 · 500 · 600 · 700 |
| `{heading\|body\|label}/{size}/font-size` | 위 표의 크기 |
| `{heading\|body\|label}/{size}/line-height` | 크기 × 150% |
| `{heading\|body\|label}/{size}/letter-spacing` | 크기 × -2% |

---

## 4. 간격·크기·레이아웃 — `spacing` (Mobile)

4의 배수를 기본 단위로 사용한다(2·10은 작은 요소용 예외).

| gap (요소 사이) | 값 | padding (안쪽 여백) | 값 |
|---|---|---|---|
| `gap/1` | 2 | `padding/1` | 2 |
| `gap/2` | 4 | `padding/2` | 4 |
| `gap/3` | 8 | `padding/3` | 8 |
| `gap/4` | 12 | `padding/4` | 10 |
| `gap/5` | 16 | `padding/5` | 12 |
| `gap/6` | 20 | `padding/6` | 16 |
| `gap/7` | 24 | `padding/7` | 20 |
| `gap/8` | 32 | `padding/8` | 24 |
| `gap/9` | 40 | `padding/9` | 32 |
| `gap/10` | 48 | `padding/10` | 40 |
| `gap/11` | 64 | | |
| `gap/12` | 80 | | |

| 레이아웃 토큰 | 참조 | 값 | 설명 |
|---|---|---|---|
| `layout/screen-width` | — | 390 | iPhone 14 기준 너비 |
| `layout/screen-height` | — | 844 | iPhone 14 기준 높이 |
| `layout/screen-margin` | `padding/7` | 20 | 화면 좌우 여백 |
| `layout/section-gap` | `gap/8` | 32 | 섹션 사이 간격 |
| `layout/item-gap` | `gap/5` | 16 | 리스트·카드 사이 간격 |
| `layout/status-bar` | — | 47 | 상태바(세이프 영역 상단) |
| `layout/home-indicator` | — | 34 | 홈 인디케이터(세이프 영역 하단) |
| `layout/header` | — | 56 | 상단 헤더 높이 |
| `layout/tab-bar` | — | 56 | 탭 영역 높이(홈 인디케이터 제외) |

| 크기 토큰 | 값 | 설명 |
|---|---|---|
| `size-height/3` ~ `/8` | 20 · 24 · 32 · 40 · 48 · 56 | 컴포넌트 높이 |
| `size/icon/small` · `medium` · `large` | 16 · 20 · 24 | 아이콘 외형 크기 (터치 영역과 별개). 시스템 아이콘 기본 24 |
| `size/touch-target/min` | 48 | 최소 터치 영역 |
| `size/progress/height` | 4 | 진행 바 높이(표지 진도 바·미니 플레이어 공통) |
| `border-width/regular` | 1 | 기본 테두리 |
| `border-width/focus` | 2 | 포커스 링 |
| `elevation/level-3/y` · `blur` | 8 · 16 | 그림자 level 3 |
| `elevation/level-4/y` · `blur` | 16 · 24 | 그림자 level 4 |

---

## 5. 모서리 — `radius`

글을 오래 읽고 듣는 서비스인 만큼 과하게 둥근 형태 대신 단정하고 차분한 곡률을 쓴다. 일반 요소의 최대 값은 12px. 아바타·토글·캡슐형 탭바·미니 플레이어처럼 완전한 원형이 필요한 요소만 `radius/max`를 쓴다.

| 토큰 | 값 | 사용 가이드 |
|---|---|---|
| `radius/2` | 2 | xsmall — 인디케이터·배지·진행 바 (컨테이너 8~16) |
| `radius/4` | 4 | small — 작은 요소 (컨테이너 20~32), xsmall 버튼 |
| `radius/6` | 6 | medium — 컨테이너 40~48, small 버튼 |
| `radius/8` | 8 | medium — 컨테이너 56~64, medium 버튼 |
| `radius/10` | 10 | large — 컨테이너 72~80 |
| `radius/12` | 12 | xlarge — 컨테이너 96 이상, large 버튼·카드 |
| `radius/max` | 9999 | 완전한 원형 (pill, 아바타) |

---

## 6. 투명도 — `opacity` (Value, 0~100)

| 토큰 | 값 | 용도 |
|---|---|---|
| `opacity/full` | 100 | 기본(불투명) |
| `opacity/subtle` | 60 | 보조 그래픽·장식. 본문·필수 정보에는 쓰지 않음 |
| `opacity/disabled` | 40 | 비활성·사용할 수 없는 상태 (예: 불러오는 중 웨이브의 남은 구간) |
| `opacity/dim` | 30 | 강하게 흐리게 (예: 재생 오류 웨이브, 장식용 빛 번짐) |
| `opacity/graphic-blur` | 90 | 장식 전용 — 글래스 아이콘 블러 레이어 |
| `opacity/waveform-bar` | 80 | 장식 전용 — 웨이브폼 막대 |

---

## 7. 그림자 (Effect style)

| 스타일 | 값 | 사용 |
|---|---|---|
| `shadow/level-1` | y0 blur2 검정5% + y1 blur2 검정5% | 책 카드 |
| `shadow/level-2` | y4 blur8 검정8% + y0 blur2 검정5% | 드롭다운 |
| `shadow/level-3` | y8 blur16 검정12% + y0 blur2 검정8% | 미니 플레이어·바텀시트 |
| `shadow/level-4` | y16 blur24 검정12% + y0 blur2 검정8% | 모달 |

> `shadow/high-contrast/level-1~4`는 고대비 모드(예정)용 참고 값으로, 이 문서의 적용 범위에서 제외한다.

---

## 8. 아이콘

1. **시스템 아이콘** — Lucide 기반 24px 선 스타일, 선 두께 2px·둥근 끝/모서리. 24×24 하나만 쓰고 크기를 바꾸지 않는다. 색은 `color/icon/basic`(gray/100)·`disabled`(gray/40)·`primary`(primary/40)·버튼 글자색만 허용. 누를 수 있는 아이콘은 `icon-button`으로 감싸 의미별 접근성 이름을 주고, 장식 아이콘은 스크린리더에서 숨긴다. 개발 전달은 SVG(선 유지, 24 프레임 포함).
2. **하단 탭바 아이콘** — Phosphor fill 스타일로 선택/미선택을 구분. 검색 탭만 예외로 Lucide 검색 아이콘을 쓰고 색과 굵기로 상태를 구분한다.
3. **카테고리 아이콘** — 48px 글래스 스타일 장식 아이콘. 밝은 배경(white, gray-subtler) 위에서만, 항상 라벨과 함께 쓰며 스크린리더에서는 숨긴다. 배경 블러를 지원하지 않는 환경에서는 블러 없이 반투명 도형만 보여준다.

| 시스템 아이콘 (24) | 용도 |
|---|---|
| `icon/close` · `icon/close-circle` | 닫기 |
| `icon/alert-circle` | 경고·오류 |
| `icon/checkbox` · `icon/checkbox-checked` | 체크 |
| `icon/settings` · `icon/share` · `icon/download` | 설정·공유·다운로드 |
| `icon/more-vertical` | 더보기 |
| `icon/timer` | 수면 타이머 |
| `icon/bookmark` | 담기 |
| `icon/list` · `icon/toc` | 목록 · 목차 보기 |
| `icon/user` · `icon/book` · `icon/home` · `icon/search` | 사용자·책·홈·검색 |
| `icon/chevron-left` · `-right` · `-down` | 이동·펼치기 |
| `icon/reading-settings` | 읽기 설정(글자 크기·줄 간격·배경) |
| `icon/playback-settings` | 재생 설정(배속·수면 타이머) |
| `icon/speed` | 재생 속도 |
| `icon/headphones` | 오디오 듣기 / 오디오북 표시 |
| `icon/bell` | 알림 |
| `icon/rotate-ccw` | 다시 시도 |

| 탭바 아이콘 | 출처 |
|---|---|
| `tab/home/house-fill` | Phosphor house (fill) |
| `tab/search/search` | Lucide search (선, 예외) |
| `tab/library/book-bookmark-fill` | Phosphor book-bookmark (fill) |
| `tab/my/user-circle-fill` | Phosphor user-circle (fill) |

카테고리 아이콘: `category/glass/today-book` · `audio-text` · `most-reviewed` · `new`

---

## 9. 컴포넌트

속성 이름과 값은 모두 영어 소문자(kebab-case)로 코드와 1:1 대응한다.

### button
기본 버튼. 64개 변형.

| 속성 | 타입 | 값 (기본) |
|---|---|---|
| `type` | variant | primary · secondary · tertiary · gray (primary) |
| `size` | variant | large · medium · small · xsmall (large) — 높이 56 · 48 · 40 · 32, radius 12 · 8 · 6 · 4 |
| `state` | variant | default · hover · pressed · disabled (default) |
| `label` | text | "버튼" |
| `show-leading-icon` · `leading-icon` | boolean · instance | false · 아이콘 교체 |
| `show-trailing-icon` · `trailing-icon` | boolean · instance | false · 아이콘 교체 |

- 40/32 높이는 구현에서 터치 영역을 48 이상으로 넓힌다.
- 긴 라벨·글자 확대: 외곽 높이는 size별 최소 높이로 두고, 라벨은 최대 2줄까지 줄바꿈하며 높이는 내용에 맞춰 늘어난다(3줄 이상은 말줄임). 너비는 부모 폭을 넘지 않고, 좁은 화면에서 양쪽 아이콘과 라벨이 겹치면 오른쪽 아이콘을 먼저 숨긴다.
- Secondary 상태 색: default primary/5 + `color/text/primary` 5.61:1 · hover primary/10 + `primary-hover` 5.61:1 · pressed primary/20 + `primary-pressed` 5.48:1.
- loading·focus는 [1. 공통 규칙 > 버튼 상태](#버튼-상태) 참고.

### button-text
텍스트 버튼. 16개 변형. 속성: `size`(large · medium · small · xsmall) · `state`(default · hover · pressed · disabled) · `label` · `show-leading-icon` + `leading-icon` · `show-trailing-icon` + `trailing-icon`.
- 보이는 높이(40/32/24/20)와 별도로 터치 영역 48 이상, 이웃 버튼과 터치 영역이 겹치지 않게 한다.
- 상태 색: default 투명 + primary/60 6.39:1 · hover primary/5 + primary/60 5.61:1 · pressed primary/10 + `color/text/primary-hover` 5.61:1.
- focus·긴 라벨 정책은 button과 같다.

### icon-button
공통 아이콘 버튼. 48×48 (`size/touch-target/min`), 아이콘 24. 6개 변형.

| 속성 | 타입 | 값 (기본) |
|---|---|---|
| `icon` | instance | 아이콘 교체 |
| `state` | variant | default · pressed(`color/overlay/pressed`) · disabled(gray/40) (default) |
| `selected` | variant | off · on(primary/40, 토글 켜짐) (off) |

- state와 selected는 독립 — "켜진 채로 눌림", "켜진 채로 비활성"도 표현한다.
- 보조 입력 시 포커스 링(primary/40 2px, 버튼 바깥 2px).
- 버튼마다 접근성 이름(예: "뒤로 가기", "알림")을 지정하고, 토글은 선택 상태도 전달한다. 누를 수 없는 장식 아이콘에는 쓰지 않는다.

### app-bar
리더 화면 앱바. 390×56. 속성: `role`(reader) · `theme`(light).
- 왼쪽 뒤로가기, 가운데 제목, 오른쪽 더보기(icon-button). 제목은 양쪽 버튼 사이 남은 너비를 채우고 넘치면 1줄 말줄임.
- 다른 역할·테마가 생기면 같은 속성에 값을 추가한다.

### app-bar/home
홈 앱바. 390×56. 속성: `unread`(boolean).
- 왼쪽 로고(`logo/symbol-small`), 오른쪽 알림 버튼(icon-button, 접근성 이름 "알림" / 새 알림이 있으면 "알림, 새 알림 있음").
- `unread`를 켜면 종 아이콘 오른쪽 위에 primary/40 점. 개수는 표시하지 않고 여부만 알린다. 알림 화면에서 목록을 본 시점에 읽음 처리.
- 내부 행은 좌우 늘어남으로 화면 폭에 맞춰 가변.

### tab-bar
하단 탭바. 350×70. 속성: `selected`(home · search · library · my).
- 높이 70 = 탭 영역 56(`layout/tab-bar`) + 바깥 패딩 위아래 7. 홈 인디케이터(34)는 포함하지 않는다. 미니 플레이어는 탭바 위 16 간격.
- 표면 `color/surface/glass`(흰색 60%) + 배경 블러, 테두리 `color/border/glass`. 미선택 탭은 별도 배경 없이 표면 위에 놓이므로 표면은 최소 55% 이상 유지(뒤가 검은색이어도 gray/95 라벨 약 5.8:1).
- 선택 탭: 흰색 100% 배경 + primary/50 라벨(4.72:1), 아이콘 primary/40. 스크린리더에 "선택됨" 전달.
- 라벨 `body/xxsmall-500`(11px Medium). 시스템 글자 크기를 키우면 탭바 높이는 고정한 채 라벨은 최대 1.3배까지만 키우고, 그 이상은 아이콘만 남기고 길게 누르면 라벨을 보여준다.

### mini-player
미니 플레이어. 350×64(최소 높이). 8개 변형.

| 속성 | 타입 | 값 (기본) |
|---|---|---|
| `state` | variant | paused · playing · buffering · error (paused) |
| `bookmarked` | variant | off · on (off) |

- 탭 동작: 카드 전체 → 플레이어 열기("<책 제목>, 플레이어 열기") · 북마크 버튼 → 담기 토글("담기"/"담기 취소") · 재생 버튼 → 재생/일시정지. 버튼 영역이 카드 탭보다 우선.
- 제목·부제 각 1줄 말줄임. 부제는 `color/text/tertiary`. 글자를 키우면 높이가 늘어난다.
- 진행 바: `color/progress/track` · `color/progress/fill`, 높이 `size/progress/height`.
- 오류 복구: error에서는 오른쪽 버튼이 "다시 시도"(`icon/rotate-ccw`)로 바뀐다. 누르면 buffering으로 전환되고 완료 전까지 다시 누를 수 없다. 재시도 실패 시 error와 담기 상태를 유지한다. 오류 문구는 `color/text/danger`.

### player-button
리딩뷰 재생 버튼. 84×84 원형(`radius/max`). 속성: `state`(paused · playing).

| state | 아이콘 | 누르면 | 접근성 이름 |
|---|---|---|---|
| `paused` | 재생 ▶ | 재생 시작 → playing | "재생" |
| `playing` | 일시정지 ❚❚ | 멈춤 → paused | "일시정지" |

- 배경 `color/background/white`, 아이콘 `color/icon/basic`.
- 재생 삼각형은 무게중심이 원의 중심에 오도록 오른쪽으로 약 4px 옮긴 광학 중앙 정렬.
- 상태는 mini-player·waveform의 재생 상태와 항상 같게 맞춘다.
- 터치 영역은 원 전체(84, 최소 48 충족).

### waveform
오디오 진행 웨이브폼. 36개 변형.

| 속성 | 타입 | 값 (기본) |
|---|---|---|
| `color` | variant | black · white (black) |
| `playback` | variant | paused · playing · buffering · error · seeking (paused) |
| `progress` | variant | 0 ~ 100, 10 단위 (0) |

- 크기: paused 396×79, playing(black) 578×151, playing(white) 578×175.
- buffering·error·seeking은 progress=30 변형을 대표로 두며, 구현에서는 progress와 독립적으로 적용한다.
- 모바일에서는 콘텐츠 너비(350)를 채우도록 너비를 가변으로 하고, 막대 수는 너비 ÷ 3px(막대 2 + 간격 1)로 다시 계산한다. 막대 투명도 `opacity/waveform-bar`.
- seeking: 손가락 위치에 세로선(primary/40)과 이동할 시간을 표시한다.

### summary-audio
요약 듣기. 101×48. 속성: `state`(ready · playing · paused).
- ready "요약 듣기 - 전체 길이" → playing "듣는 중 - 현재/전체" → paused "이어 듣기 - 현재/전체". 전체 길이는 세 상태에서 같은 값.
- 보이는 높이 20 → 구현에서 터치 영역 48×48.
- 로딩: ready 라벨 유지 + 아이콘 자리에 로딩, 중복 탭 무시. 실패: "요약 음성을 불러오지 못했어요 · 다시 시도".
- 본문 오디오 재생 중 요약을 재생하면 본문은 일시정지, 요약이 끝나도 자동 이어 재생하지 않는다.
- pressed 70%, disabled는 요약 음성이 없을 때 gray/40.

### summary
요약 펼침/접힘. 폭 350. 속성: `state`(expanded · collapsed).
- collapsed는 3줄까지 + 말줄임, expanded는 전체. 3줄 이하면 더보기 버튼 숨김.
- 로딩 3줄 스켈레톤 · 빈 상태는 컴포넌트 숨김 · 실패 시 "요약을 불러오지 못했어요" + 다시 시도.
- 헤더 행은 좌우 패딩 14 안에서 텍스트 영역이 가변, 화살표 24 고정. 펼침/접힘 버튼은 "요약 펼치기 / 접기"와 확장 상태를 전달.
- "들으러 가기" 링크는 `color/text/tertiary`.

### tab/summary
요약 화면 상단 탭. 390×44. 속성: `selected`(recent · chapter · all).
- 미선택 탭도 누를 수 있는 활성 요소라 `color/text/tertiary`(5.17:1). 선택 탭은 gray/100 글자 + 밑줄, "선택됨" 전달.

### reading-status
독서 진행 배지. 53×26. 속성: `state`(not-started · in-progress · completed).
- not-started 진도 0% · in-progress 1~99%(마지막 위치 저장) · completed 100% 또는 사용자가 완독 처리.
- 진도는 텍스트·오디오 중 더 많이 진행한 위치 기준. completed 이후 다시 읽어도 completed 유지.
- 스크린리더: "읽기 전 / 읽는 중 / 다 읽음".

### table-of-contents
목차 항목. 390×68. 속성: `current`(on · off).
- on = 현재 읽는 장(primary 강조). 탭하면 해당 장 시작 위치로 이동하고 목차를 닫는다.
- 현재 장은 "현재 위치"를 함께 전달, 목차를 열면 현재 장이 보이도록 자동 스크롤.
- 시간 정보 `color/text/tertiary`.

### reader/more-menu
리더 더보기 메뉴. 행 390×48.
- 앱바 더보기 버튼 아래에 열린다. 항목은 자주 쓰는 순서, 행 최소 높이 48.
- 바깥 탭·뒤로가기로 닫힘. 열리면 첫 항목에 포커스, 닫히면 더보기 버튼으로 포커스 복귀.

### tooltip
말풍선 툴팁. 속성: `theme`(dark · light).
- 모바일에는 호버가 없으므로 첫 진입 코치마크·길게 누르기 안내처럼 직접 보여줄 때만 쓴다.
- 한 화면에 하나만, 대상을 가리지 않게 위/아래 배치, 화면 여백 20 안으로 꼬리 위치 조정. 탭하거나 3~5초 뒤 사라지며 중요한 정보는 툴팁에만 두지 않는다.
- 밝은 화면엔 dark, 어두운 화면엔 light.

### book-card
책 카드. 126 고정폭.

| 속성 | 타입 | 기본값 |
|---|---|---|
| `title` | text | "오늘도 조금은 좋아지기로 했다" |
| `author` | text | "서은진" |
| `audiobook` | boolean | true |

- 표지는 cover 안 이미지 채우기로 교체. 진도는 내부 `cover-progress` 0~100(10 단위), 0이면 숨김.
- `audiobook`을 켜면 표지 오른쪽 아래에 헤드폰 원(`color/overlay/on-image-subtle` + 블러).
- 제목 `body/small-500`(15) · `color/text/basic`, 작가 `body/xsmall-400`(13) · `color/text/tertiary`, 각 1줄 말줄임. 표지 로드 실패 시 gray 배경에 책 제목을 가운데 표시하는 대체 표지.
- 접근성 이름: "제목, 작가, 오디오북(해당 시), 진도 n%".

### book-card/small
내 서재용 작은 책 카드. 106 고정폭. 속성(`title` · `author` · `audiobook`)과 동작·접근성 규칙은 book-card와 같다.

| 항목 | book-card | book-card/small |
|---|---|---|
| 카드 폭 | 126 | 106 |
| 표지 | 126×182, `radius/8` | 106×153, `radius/6` (같은 비율 1:1.44) |
| 제목 | `body/small-500` 15 | `body/xsmall-500` 13 |
| 작가 | `body/xsmall-400` 13 | `body/xxsmall-400` 11 |
| 오디오북 배지 | 32 · 아이콘 20 | 28 · 아이콘 16 (`size/icon/small`) |
| 표지–정보 간격 | `gap/3` 8 | `gap/3` 8 |

- 3열 그리드 기준: 콘텐츠 폭 350 = 106 × 3 + 열 간격 16(`gap/5`) × 2.

### cover-progress
책 표지 하단 진도 바. 126×4. 속성: `progress`(0 ~ 100, 10 단위).
- 표지 프레임(clip content) 안 맨 아래에 놓으면 표지 모서리를 따라 잘린다. 0은 숨김.
- 트랙 `color/overlay/on-image`, 채움 `color/progress/fill`, 높이 `size/progress/height`.
- 실제 진도는 10% 단위로 내림(47% → 40), 0~100으로 제한, 1~9%는 10으로 올려 읽은 흔적을 보여준다.
- 장식 요소이므로 진도는 book-card 접근성 이름에서 전달.

### book/3d
3D 책 표지. 142×188. cover 레이어 채우기를 표지 이미지로 교체, 크기를 바꾸면 비율대로 커진다.

### return-welcome-card
복귀 환영 카드. 폭 290, 높이는 내용에 맞춰 늘어남(세로 오토 레이아웃).
- 오랜만에 돌아온 사용자에게 홈 하단에 띄운다.
- 데이터: 마지막으로 읽은 책 표지·제목, 마지막 접속 후 지난 일수(예: "47일만이에요"), 마지막 위치(장·진도).
- CTA "이어 듣기" → 마지막 위치의 플레이어 화면으로 바로 이동.
- 긴 제목·사용자명·글자 확대 시 카드가 길어지고, 화면 높이를 넘으면 카드 안쪽을 스크롤(버튼 영역은 하단 고정).
- 버튼 높이 40, 실제 터치 영역 48×48 확보, 두 버튼 간격 8 안에서 터치 영역이 겹치지 않게.
- 모달로 뜨면 첫 포커스는 카드 제목, 닫기 버튼(48×48)·바깥 탭으로 닫힘, 닫히면 이전 포커스로 복귀.

### system (시안 배치용)
iOS 시스템 영역. 속성: `type`(status-bar 47 · home-indicator 34). 화면 시안에서 Safe Area 확인용이며 앱에서 구현하지 않는다.

---

## 10. 로고

| 컴포넌트 | 크기 | 설명 |
|---|---|---|
| `logo/symbol` | 1024×1024 | 로고 심볼(구름 말풍선 + 음파), 그라데이션 원본, 배경 없음 |
| `logo/app-icon` | 1024×1024 | 런처 아이콘 원본 |
| `logo/symbol-small` | 28×28 | 40px 이하(앱바·알림 등)용 단순화 심볼 — 단색 primary/40 + 음파 4개 |

- 40px 이상은 그라데이션 원본 심볼, 그보다 작으면 `logo/symbol-small`.
- 출시용 에셋
  - iOS: 1024×1024 PNG, 알파 채널 없음, 모서리 마스크 없이 정사각형(라운드는 OS가 적용)
  - Android: 108dp 적응형 아이콘(전경/배경 레이어 분리, 중앙 66dp 세이프 존 안에 심볼) + 모노크롬 레이어
  - Google Play: 512×512 PNG
