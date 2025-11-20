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
- [4 CSV 스펙](#4-csv-스펙)
  - [4.1 store_mastercsv](#41-store_mastercsv)
  - [4.2 furniture_layoutcsv](#42-furniture_layoutcsv)
  - [4.3 product_placementcsv](#43-product_placementcsv)


---

## 1 개념 및 범위

3D 에셋은 크게 세 가지 타입으로 나눕니다.

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

다음 3개의 CSV 파일을 사용합니다.

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

NEURALTWIN에서 사용하는 **Furniture 카테고리**는 다음과 같습니다.

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

`furniture_layout.csv`의 `furniture_category` 컬럼에는 아래와 같은 **대문자 코드**를 사용합니다.

| Category Name   | `furniture_category` 값 |
|-----------------|-------------------------|
| Shelf           | `SHELF`                 |
| Rack            | `RACK`                  |
| Hanger          | `HANGER`                |
| Table           | `TABLE`                 |
| Mannequin       | `MANNEQUIN`             |
| Display Platform| `DISPLAY_PLATFORM`      |
| Display Stand   | `DISPLAY_STAND`         |
| Cashier         | `CASHIER`               |
| Fitting Room    | `FITTING_ROOM`          |
| Decor           | `DECOR`                 |
| Other           | `OTHER`                 |

> 예시  
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
|----------------------|--------|---------------------------|
| `SHELF`              | `SF`   | `SF001`                   |
| `RACK`               | `RK`   | `RK001`                   |
| `HANGER`             | `HG`   | `HG001`                   |
| `TABLE`              | `TB`   | `TB001`                   |
| `MANNEQUIN`          | `MN`   | `MN001`                   |
| `DISPLAY_PLATFORM`   | `PF`   | `PF001`                   |
| `DISPLAY_STAND`      | `DS`   | `DS001`                   |
| `CASHIER`            | `CS`   | `CS001`                   |
| `FITTING_ROOM`       | `FR`   | `FR001`                   |
| `DECOR`              | `DC`   | `DC001`                   |
| `OTHER`              | `OT`   | `OT001`                   |

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

Product 카테고리는 다음 코드들을 사용합니다.

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
|--------------------|--------|-------------------|
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
```


## 4 CSV 스펙

NeuralTwin 데이터셋은 기본적으로 **쉼표(,)** 구분 CSV를 사용합니다.  
문자열 컬럼 중 콤마(,)가 포함될 수 있는 경우는 `"`로 감싸서 사용합니다.

타입 표기:
- `string` : 따옴표 없는 일반 문자열
- `int` : 정수
- `float` : 실수(소수점)
- `bool` : `true` / `false` (소문자)

---

### 4.1 store_master.csv

#### 4.1.1 역할

매장 정보를 담는 **스토어 마스터 테이블**입니다.  
다른 CSV(`furniture_layout.csv`, `product_placement.csv`)에서 `store_id`로 참조됩니다.

#### 4.1.2 컬럼 정의

| 컬럼명     | 타입   | 필수 | 설명                                  | 예시                    |
|-----------|--------|------|---------------------------------------|-------------------------|
| store_id  | string | ✅   | 매장 ID (고유값)                      | `store_gangnam_flagship` |
| store_name| string | ✅   | 매장 이름 (표시용, 한/영 자유)        | `Gangnam Flagship`     |

> 필요 시 층 정보, 지역 코드 등의 추가 컬럼은 **프로젝트 합의 후** 확장합니다.

#### 4.1.3 예시

```csv
store_id,store_name
store_gangnam_flagship,Gangnam Flagship
store_hongdae_pop,홍대 팝업스토어
```

---

### 4.2 furniture_layout.csv

#### 4.2.1 역할

각 매장에 배치된 **모든 Furniture 인스턴스**를 정의합니다.

각 행(row)은 하나의 **Furniture 인스턴스**입니다.

- 어떤 매장(`store_id`)에  
- 어떤 모델(`furniture_model_id`, `furniture_glb_file`)을 사용한  
- 어떤 이름의 가구(`furniture_name`)가  
- 어느 위치(`pos_x`, `pos_y`, `pos_z`)에  
- 어떤 회전(`rot_x_deg`, `rot_y_deg`, `rot_z_deg`) 상태로  
- 이동 가능한지(`mobility`)  
- Product DP가 가능한지(`dp_possible`)  
- 가능한 경우 어떤 카테고리를 허용하는지(`dp_allowed_categories`)  
- 대략 몇 개까지 DP 가능한지(`dp_capacity`)  

를 정의합니다.

---

#### 4.2.2 컬럼 정의

| 컬럼명                 | 타입    | 필수 | 설명 |
|------------------------|---------|------|------|
| store_id               | string  | ✅   | 매장 ID (`store_master.csv` 참조) |
| furniture_instance_id  | string  | ✅   | 가구 인스턴스 ID (예: `TB001_01`) |
| furniture_model_id     | string  | ✅   | 가구 모델 ID (예: `TB001`) |
| furniture_glb_file     | string  | ✅   | 가구 GLB 파일명 (예: `TB001_Round_display_table.glb`) |
| furniture_name         | string  | ✅   | 사람이 이해하기 쉬운 가구 이름 |
| furniture_category     | string  | ✅   | `SHELF`, `RACK`, `HANGER`, … |
| pos_x                  | number  | ✅   | 월드 좌표 X (미터) |
| pos_y                  | number  | ✅   | 월드 좌표 Y (미터) |
| pos_z                  | number  | ✅   | 월드 좌표 Z (미터) |
| rot_x_deg              | number  | ✅   | 회전 X (도, degree) |
| rot_y_deg              | number  | ✅   | 회전 Y (도, degree) |
| rot_z_deg              | number  | ✅   | 회전 Z (도, degree) |
| mobility               | string  | ✅   | `MOVABLE` 또는 `STATIC` |
| dp_possible            | string  | ✅   | `TRUE` 또는 `FALSE` |
| dp_allowed_categories  | string  | ⬜   | DP 가능한 Product 카테고리 목록 (형식은 아래 [4.2.4](#424-dp_allowed_categories-작성-규칙)) |
| dp_capacity            | integer | ⬜   | 대략적인 DP 가능 최대 수량 |

---

#### 4.2.3 값 규칙

**`mobility`**

- `MOVABLE`  
  - 레이아웃 시뮬레이션에서 위치를 변경할 수 있는 가구  
  - 예: 이동 가능한 테이블, 행거, 독립형 랙, 벤치 등
- `STATIC`  
  - 구조적으로 고정된 가구  
  - 예: 벽에 고정된 선반, 고정형 계산대, 피팅룸 벽체 등

**`dp_possible`**

- `TRUE`  
  - Product DP용으로 사용하는 가구  
  - 예: 선반, 행거, 랙, 디스플레이 테이블, 디스플레이 플랫폼/스탠드 등
- `FALSE`  
  - DP를 하지 않는 가구/오브젝트  
  - 예: 벤치, 소파, 거울, 브랜드 로고 구조물, 통로 데코 등

**`furniture_category`**

아래 값 중 하나를 사용합니다.

- `SHELF`
- `RACK`
- `HANGER`
- `TABLE`
- `MANNEQUIN`
- `DISPLAY_PLATFORM`
- `DISPLAY_STAND`
- `CASHIER`
- `FITTING_ROOM`
- `DECOR`
- `OTHER`

---

#### 4.2.4 dp_allowed_categories 작성 규칙

`dp_allowed_categories`는 **해당 Furniture 위에 어떤 Product 카테고리들이 DP될 수 있는지**를 나타내는 필드입니다.

##### (1) 사람이 CSV에 입력하는 형식 (Authoring format)

`furniture_layout.csv`에서 Excel / Sheets에서 작성할 때:

- 단일 카테고리 예시:
  - `Tops`
- 여러 카테고리 예시:
  - `Tops,Bottoms`
  - `Shoes,Bags,Accessory`

즉, **쉼표(,)로 구분된 문자열**을 한 셀에 입력하는 것을 기본 패턴으로 합니다.  

> 엑셀에서 저장 시 자동으로 `"Tops,Bottoms"`처럼 따옴표가 들어갈 수 있지만,  
> 파서에서 문제 없이 처리된다고 가정합니다.

추가로, `|`(파이프)를 구분자로 사용해도 됩니다.

- `Tops|Bottoms`  
- `Shoes|Bags|Accessory`  

##### (2) 엔진 내부 정규화 규칙 (Internal normalization)

임포트 파이프라인에서 `dp_allowed_categories`는 다음 규칙으로 정규화됩니다.

1. 셀 값을 문자열로 읽습니다.  
   - 예: `"Tops,Bottoms"` → `Tops,Bottoms`
2. 구분자: **`,`와 `|`를 모두 허용**합니다.
   - `Tops,Bottoms`  
   - `Tops|Bottoms`  
   - `Tops, Bottoms`  
   - `Shoes|Bags,Accessory`  
   → 모두 동일한 로직으로 처리
3. 구분자로 split 후, 각 항목에 대해:
   - 앞뒤 공백 제거 (trim)
   - 대문자로 변환
   - 다음 매핑 룰 적용:
     - `TOP`, `TOPS` → `TOPS`
     - `BOTTOM`, `BOTTOMS` → `BOTTOMS`
     - `OUTER` → `OUTER`
     - `DRESS`, `DRESSES` → `DRESS`
     - `SHOE`, `SHOES` → `SHOES`
     - `BAG`, `BAGS` → `BAGS`
     - `ACCESSORY`, `ACCESSORIES` → `ACCESSORY`
     - `HAT`, `HATS` → `HAT`
     - `SCARF`, `SCARVES` → `SCARF`
     - `GLOVE`, `GLOVES` → `GLOVES`
4. 비어 있는 항목은 제거합니다.
5. 최종적으로 내부에서는 배열 형태로 사용합니다.
   - `"Tops,Bottoms"` → `["TOPS", "BOTTOMS"]`
   - `"Shoes,Bags,Accessory"` → `["SHOES", "BAGS", "ACCESSORY"]`

##### (3) DP 불가 가구의 규칙

- `dp_possible = FALSE` 인 경우:
  - `dp_allowed_categories`는 **빈 값**으로 둡니다.
  - 엔진에서는 해당 가구를 “DP 불가 가구”로 인식하고, 어떤 카테고리도 허용하지 않습니다.

---

#### 4.2.5 예시

```csv
store_id,furniture_instance_id,furniture_model_id,furniture_glb_file,furniture_name,furniture_category,pos_x,pos_y,pos_z,rot_x_deg,rot_y_deg,rot_z_deg,mobility,dp_possible,dp_allowed_categories,dp_capacity
store_gangnam_flagship,TB001_01,TB001,TB001_Round_display_table.glb,Entrance Round Table A,TABLE,2.0,0.0,1.5,0,30,0,MOVABLE,TRUE,"Shoes,Bags,Accessory",12
store_gangnam_flagship,TB001_02,TB001,TB001_Round_display_table.glb,Entrance Round Table B,TABLE,4.0,0.0,1.5,0,0,0,MOVABLE,TRUE,"Shoes,Bags,Accessory",10
store_gangnam_flagship,HG001_01,HG001,HG001_Double_rail_hanger.glb,Main Aisle Hanger 1,HANGER,6.0,0.0,3.0,0,90,0,MOVABLE,TRUE,"Tops,Outer",40
store_gangnam_flagship,SF001_01,SF001,SF001_Back_wall_shelf.glb,Back Wall Shelf 1,SHELF,0.0,0.0,8.0,0,180,0,STATIC,TRUE,"Shoes,Bags",30
store_gangnam_flagship,CS001_01,CS001,CS001_Main_cashier.glb,Main Cashier,CASHIER,8.0,0.0,0.5,0,270,0,STATIC,FALSE,,
store_gangnam_flagship,FR001_01,FR001,FR001_Fitting_room.glb,Fitting Room 1,FITTING_ROOM,9.0,0.0,5.0,0,0,0,STATIC,FALSE,,
store_gangnam_flagship,DC001_01,DC001,DC001_Mirror.glb,Mirror Near Fitting Room,DECOR,8.5,0.0,5.2,0,0,0,MOVABLE,FALSE,,
store_gangnam_flagship,DC002_01,DC002,DC002_Bench.glb,Waiting Bench,DECOR,3.5,0.0,4.5,0,0,0,MOVABLE,FALSE,,
store_gangnam_flagship,DC003_01,DC003,DC003_Brand_logo_object.glb,Brand Logo Object,DECOR,1.0,0.0,0.8,0,15,0,STATIC,FALSE,,
```

---

### 4.3 product_placement.csv

#### 4.3.1 역할

`product_placement.csv`는 **어떤 Product가 어떤 Furniture 인스턴스 위에 진열되어 있는지**를 정의하는 테이블입니다.

각 행(row)은 하나의 **Product × Furniture 인스턴스 관계**를 의미합니다.

- 하나의 Product가 여러 가구에 DP될 수 있음  
  → 같은 `product_id`로 여러 행이 존재할 수 있음
- 하나의 가구가 여러 Product를 DP할 수 있음  
  → 같은 `furniture_instance_id`로 여러 행이 존재할 수 있음

이 CSV는 주로 다음 용도로 사용됩니다.

- UI에서 “이 테이블/행거에 어떤 상품들이 DP되어 있는지” 보여주기
- AI 시뮬레이션에서 “DP 구성을 바꾸면 KPI가 어떻게 달라지는지” 계산하기
- Product–Fixture 관계를 그래프/온톨로지로 확장하기 위한 베이스 데이터

---

#### 4.3.2 컬럼 정의

| 컬럼명                | 타입    | 필수 | 설명 |
|-----------------------|---------|------|------|
| store_id              | string  | ✅   | 매장 ID (`store_master.csv` 참조) |
| product_id            | string  | ✅   | Product ID (예: `SH001`) |
| product_name          | string  | ✅   | 상품 이름 (표시용 텍스트) |
| product_category      | string  | ✅   | 상품 카테고리 (`TOPS`, `SHOES`, `BAGS` 등) |
| product_glb_file      | string  | ⬜   | Product GLB 파일명 (예: `SH001_White_sneakers.glb`) |
| furniture_instance_id | string  | ✅   | 진열된 Furniture 인스턴스 ID (`furniture_layout.csv` 참조) |
| dp_quantity           | integer | ⬜   | 해당 가구 위에 DP된 대략적인 수량 |

**필드별 설명**

- `store_id`  
  - 이 진열 관계가 속한 매장을 나타냅니다.  
  - 반드시 `store_master.csv`의 `store_id`와 일치해야 합니다.

- `product_id`  
  - NEURALTWIN 내부에서 사용하는 Product 모델 ID입니다.  
  - 예: `SH001`, `TP001`, `BG001` (카테고리별 prefix 규칙 준수).

- `product_name`  
  - 사람이 읽기 쉬운 상품 이름입니다.  
  - 예: `White Sneakers`, `Striped T-Shirt`, `Black Tote Bag`.

- `product_category`  
  - 상품의 카테고리입니다.  
  - 허용 값(예시): `TOPS`, `BOTTOMS`, `OUTER`, `DRESS`, `SHOES`, `BAGS`, `ACCESSORY`, `HAT`, `SCARF`, `GLOVES`  
  - `furniture_layout.csv`의 `dp_allowed_categories`와 논리적으로 매칭될 수 있어야 합니다.

- `product_glb_file` (선택)  
  - 3D Product 에셋을 사용하는 경우, 해당 GLB 파일명을 적습니다.  
  - 예: `SH001_White_sneakers.glb`  
  - Product를 3D로 렌더링하지 않고 이미지/텍스트 UI만 사용할 경우, 비워두어도 됩니다.

- `furniture_instance_id`  
  - 이 Product가 진열된 Furniture 인스턴스를 가리킵니다.  
  - 반드시 `furniture_layout.csv`의 `furniture_instance_id` 중 하나여야 합니다.

- `dp_quantity` (선택)  
  - 해당 Furniture 위에 진열된 대략적인 수량입니다.  
  - 정확한 재고 수량이 아니라, DP 상태를 표현할 정도의 대략적인 값이면 충분합니다.

---

#### 4.3.3 예시

```csv
store_id,product_id,product_name,product_category,product_glb_file,furniture_instance_id,dp_quantity
store_gangnam_flagship,SH001,White Sneakers,SHOES,SH001_White_sneakers.glb,TB001_01,6
store_gangnam_flagship,SH001,White Sneakers,SHOES,SH001_White_sneakers.glb,TB001_02,4
store_gangnam_flagship,BG001,Black Tote Bag,BAGS,BG001_Black_tote_bag.glb,TB001_01,3
store_gangnam_flagship,TP001,Striped T-Shirt,TOPS,TP001_Striped_tshirt.glb,HG001_01,15
store_gangnam_flagship,OT001,Trench Coat,OUTER,OT001_Trench_coat.glb,HG001_01,10





