# NEURALTWIN 3D Product Placement 메타데이터 가이드

> 버전: v3  
> 상태: Draft (내부 스펙)

---

## 목차

- [1 개념 및 범위](#1-개념-및-범위)
- [2 파일 구성](#2-파일-구성)
- [3 ID와 네이밍 규칙](#3-id와-네이밍-규칙)
  - [3.1 Furniture 카테고리와 ID](#31-furniture-카테고리와-id)
  - [3.2 Product 카테고리와 ID](#32-product-카테고리와-id)
  - [3.3 GLB 파일 네이밍](#33-glb-파일-네이밍)

---


## 1 개념 및 범위

3D 에셋은 크게 세 가지 타입으로 나눕니다:

1. **Store**  
   - 매장 쉘(Shell) 3D 모델 (벽, 바닥, 천장 등)  
   - 보통 매장당 1개

2. **Furniture**  
   - 매장 환경을 구성하는 모든 집기 및 오브젝트
     - 선반(Shelf), 랙(Rack), 행거(Hanger), 테이블(Table)
     - 마네킹(Mannequin), 디스플레이 플랫폼(Display Platform), 스탠드(Display Stand)
     - 계산대(Cashier), 피팅룸(Fitting Room)
     - 거울, 벤치/소파, 로고 오브제, 기타 데코

3. **Product**  
   - 실제 브랜드가 판매하는 상품
   - 의류, 신발, 가방, 액세서리 등

이 문서는 **Product Placement를 위한 메타데이터**에 집중합니다:

- 각 Furniture(집기)가 매장 안에서 어디에 놓여 있는지 (position, rotation)
- 해당 Furniture가 어떤 Product 카테고리를 DP할 수 있는지
- 실제로 어떤 Product가 어떤 Furniture 인스턴스 위에 DP되어 있는지

---

## 2 파일 구성

다음 3개의 CSV 파일을 사용합니다:

1. `store_master.csv`  
   - 스토어(매장) 단위 마스터 데이터

2. `furniture_layout.csv`  
   - 각 매장에 배치된 모든 Furniture 인스턴스
   - 포함 정보:
     - 모델 ID
     - glb 파일명
     - 위치/회전 정보
     - 이동 가능 여부
     - DP 가능 여부 및 DP 가능 카테고리

3. `product_placement.csv`  
   - Product–Furniture 관계 데이터
   - 포함 정보:
     - 어떤 Product가
     - 어떤 Furniture 인스턴스 위에
     - 몇 개 정도 DP되어 있는지

공통 CSV 규칙:

- 포맷: CSV
- 인코딩: UTF-8
- 첫 줄: 헤더(컬럼 이름)
- 구분자: `,`
- 소수점: `.` (예: `2.5`)

---

## 3 ID와 네이밍 규칙

### 3.1 Furniture 카테고리와 ID

#### 3.1.1 Furniture 카테고리 (논리적 분류)

NEURALTWIN에서 사용하는 **Furniture 카테고리**는 다음과 같습니다:

- Shelf
- Rack
- Hanger
- Table
- Mannequin
- Display Platform
- Display Stand
- Cashier
- Fitting Room
- Decor
- Other

#### 3.1.2 `furniture_category` 코드 값 (CSV에 들어가는 값)

`furniture_layout.csv`의 `furniture_category` 컬럼에는 아래와 같은 **대문자 코드**를 사용합니다:

| Category Name   | `furniture_category` 값  |
| --------------- | ------------------------ |
| Shelf           | `SHELF`                  |
| Rack            | `RACK`                   |
| Hanger          | `HANGER`                 |
| Table           | `TABLE`                  |
| Mannequin       | `MANNEQUIN`              |
| Display Platform| `DISPLAY_PLATFORM`       |
| Display Stand   | `DISPLAY_STAND`          |
| Cashier         | `CASHIER`                |
| Fitting Room    | `FITTING_ROOM`           |
| Decor           | `DECOR`                  |
| Other           | `OTHER`                  |

> 예시:
> - 거울 → `DECOR`
> - 벤치 / 소파 → `DECOR`
> - 브랜드 로고 오브제 → `DECOR`
> - 순수 진열용 선반 → `SHELF`
> - 옷걸이로 거는 fixture → `HANGER`
> - 선반+행거가 섞인 복합 구조 → `RACK`

#### 3.1.3 Furniture 모델 ID (`furniture_model_id`)

`furniture_model_id`는 각 Furniture 모델 타입을 식별하는 **엔티티/모델 ID**입니다.

- 형식: `<2글자 prefix><3자리 숫자>`

카테고리별 prefix:

| `furniture_category` | Prefix | 예시 `furniture_model_id` |
| -------------------- | ------ | ------------------------- |
| `SHELF`              | `SF`   | `SF001`                  |
| `RACK`               | `RK`   | `RK001`                  |
| `HANGER`             | `HG`   | `HG001`                  |
| `TABLE`              | `TB`   | `TB001`                  |
| `MANNEQUIN`          | `MN`   | `MN001`                  |
| `DISPLAY_PLATFORM`   | `PF`   | `PF001`                  |
| `DISPLAY_STAND`      | `DS`   | `DS001`                  |
| `CASHIER`            | `CS`   | `CS001`                  |
| `FITTING_ROOM`       | `FR`   | `FR001`                  |
| `DECOR`              | `DC`   | `DC001`                  |
| `OTHER`              | `OT`   | `OT001`                  |

예시:

- 라운드 디스플레이 테이블 → `TABLE` → `TB001`
- 더블 레일 행거 → `HANGER` → `HG001`
- 벽 선반 타입 1 → `SHELF` → `SF001`
- 입구 피데스탈 → `DISPLAY_PLATFORM` → `PF001`
- 피팅룸 옆 거울 → `DECOR` → `DC001`

#### 3.1.4 Furniture 인스턴스 ID (`furniture_instance_id`)

`furniture_instance_id`는 각 매장 안에 실제로 배치된 **Furniture 개별 인스턴스**를 나타냅니다.

- 형식: `<furniture_model_id>_<2자리 인스턴스 번호>`
  - 예시:
    - `TB001_01`, `TB001_02`
    - `HG001_01`
    - `DC001_01`

의미:

- `TB001` = 하나의 테이블 모델 타입
- `TB001_01`, `TB001_02` = 같은 모델(`TB001`)을 사용하는 두 개의 실제 테이블 인스턴스

---

### 3.2 Product 카테고리와 ID

#### 3.2.1 Product 카테고리 (`product_category`)

Product 카테고리는 다음 코드들을 사용합니다:

- `TOPS`
- `BOTTOMS`
- `OUTER`
- `DRESS`
- `SHOES`
- `BAGS`
- `ACCESSORY`
- `HAT`
- `SCARF`
- `GLOVES`

이 값들은:

- `product_placement.csv` → `product_category`
- `furniture_layout.csv` → `dp_allowed_categories` (해당 집기 위에 DP 가능한 상품 카테고리)

에 사용됩니다.

#### 3.2.2 Product ID (`product_id`)

`product_id`는 실제 리테일 SKU가 아니라, NEURALTWIN 내부에서 사용하는 **Product 모델 ID**입니다.

- 형식: `<2글자 prefix><3자리 숫자>`

추천 prefix:

| `product_category` | Prefix | 예시 `product_id` |
| ------------------ | ------ | ----------------- |
| `TOPS`             | `TP`   | `TP001`           |
| `BOTTOMS`          | `BT`   | `BT001`           |
| `OUTER`            | `OT`   | `OT001`           |
| `DRESS`            | `DR`   | `DR001`           |
| `SHOES`            | `SH`   | `SH001`           |
| `BAGS`             | `BG`   | `BG001`           |
| `ACCESSORY`        | `AC`   | `AC001`           |
| `HAT`              | `HT`   | `HT001`           |
| `SCARF`            | `SC`   | `SC001`           |
| `GLOVES`           | `GV`   | `GV001`           |

예시:

- 화이트 스니커즈 → `SH001`
- 블랙 토트백 → `BG001`
- 스트라이프 티셔츠 → `TP001`
- 트렌치 코트 → `OT001`

> 실제 브랜드 SKU(예: `NIKE-ABC-123`)가 필요하면  
> 추후 `brand_sku` 컬럼을 별도로 추가하면 됩니다.  
> 현재 스펙에서는 위와 같은 internal ID만 사용합니다.

---

### 3.3 GLB 파일 네이밍

GLB 파일명은 CSV에 **명시적으로 저장**합니다.

#### 3.3.1 Furniture GLB 파일 (`furniture_glb_file`)

- 컬럼: `furniture_glb_file` (`furniture_layout.csv` 안에 위치)
- 추천 형식:  
  `<furniture_model_id>_<사람이 읽기 좋은 이름>.glb`

예시:

- `TB001_Round_display_table.glb`
- `HG001_Double_rail_hanger.glb`
- `DC001_Mirror.glb`

런타임 로딩 예시 (TypeScript):

```ts
const FURNITURE_BASE_URL = "https://storage/nt/furniture/";

function getFurnitureGlbUrl(filename: string) {
  return `${FURNITURE_BASE_URL}${filename}`;
}
