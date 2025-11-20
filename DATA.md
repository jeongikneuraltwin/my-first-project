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

---

### 4.2 furniture_layout.csv

#### 4.2.1 역할

각 매장에 배치된 **모든 Furniture 인스턴스**를 정의합니다.

- 한 행(row) = 하나의 **Furniture Instance**
- 매장 내 좌표계(미터 단위)를 기준으로 위치/회전, 사용 가능한 상품 카테고리 등을 포함합니다.
- 3D 클라이언트는 이 정보를 기반으로 Furniture GLB를 실제 공간에 배치합니다.

#### 4.2.2 컬럼 정의

| 컬럼명                | 타입   | 필수 | 설명                                                                 | 예시                                      |
|-----------------------|--------|------|----------------------------------------------------------------------|-------------------------------------------|
| store_id              | string | ✅   | 매장 ID. `store_master.csv`의 `store_id`를 참조                     | `store_gangnam_flagship`                  |
| furniture_instance_id | string | ✅   | Furniture 인스턴스 ID. 매장 내에서 고유해야 함                       | `furinst_gangnam_entrance_wall01`         |
| furniture_type_id     | string | ✅   | Furniture 타입 ID. `fur_` 접두사 사용, GLB 파일명과 1:1 매핑        | `fur_wall_2bay`                           |
| pos_x                 | float  | ✅   | 매장 좌표계 기준 X 위치 (미터, m)                                   | `3.2`                                     |
| pos_y                 | float  | ✅   | 매장 좌표계 기준 Y 위치 (높이, 미터, m)                             | `0.0`                                     |
| pos_z                 | float  | ✅   | 매장 좌표계 기준 Z 위치 (미터, m)                                   | `1.5`                                     |
| rot_y                 | float  | ✅   | Yaw 회전 각도 (도, degree). 상단에서 내려다본 수평 회전             | `90`                                      |
| glb_override          | string | ❌   | 기본 GLB 대신 사용할 개별 GLB 경로 (없으면 빈 값)                   | `custom/fur_wall_2bay_variantA.glb`       |
| dp_allowed_categories | string | ❌   | 이 Furniture에 진열 가능한 상품 카테고리 목록(세미콜론 구분)        | `outer_coat;outer_jacket`                 |
| is_enabled            | bool   | ✅   | 이 인스턴스 사용 여부. 미사용/숨김 시 `false`                        | `true`                                    |
| note                  | string | ❌   | 운영/머천다이징용 메모, 주석                                        | `입구 오른쪽 메인 월랙`                   |

> 좌표계의 원점/축 방향은 별도 문서 **「좌표계 가이드」**를 기준으로 하며,  
> 여기서는 **단위(m)** 와 회전 단위(degree)만 고정합니다.

#### 4.2.3 값 규칙

- **store_id**
  - 반드시 `store_master.csv`에 존재하는 값만 사용합니다.

- **furniture_instance_id**
  - 같은 `store_id` 내에서 **중복 불가**입니다.
  - 추천 네이밍 패턴:
    - `furinst_{매장약어}_{영역/용도}`
    - 예: `furinst_gangnam_entrance_wall01`, `furinst_gangnam_center_table01`

- **furniture_type_id**
  - `fur_` 접두사 필수 (예: `fur_wall_2bay`)
  - `/assets/furniture_glb/`에 다음 규칙의 GLB가 존재해야 합니다.
    - 기본 규칙: `{furniture_type_id}.glb`
    - 예: `fur_wall_2bay` → `/assets/furniture_glb/fur_wall_2bay.glb`

- **pos_x / pos_y / pos_z**
  - 단위: **미터(m)**
  - 실수(float) 사용, 소수점은 `.` 사용 (`3.5`, `0.25` 등)
  - 매장 내 유효 범위(예: `0.0 ≤ x ≤ 100.0`)는 프로젝트 내 별도 정의를 따릅니다.

- **rot_y**
  - 단위: **도(degree)**
  - 상단에서 내려다봤을 때의 수평 회전(Yaw)에 해당합니다.
  - 일반적으로 `0`, `90`, `180`, `270` 단위로 회전하되, 필요 시 임의 각도 사용 가능

- **glb_override**
  - 비워두면: `furniture_type_id` 기반 **기본 GLB** 사용
  - 채워두면: 해당 경로의 GLB를 우선 사용  
    (예: A/B 테스트, 특별한 프로토타입 매장 등)

- **dp_allowed_categories**
  - 비워두면: “**모든 카테고리 허용**”으로 간주합니다.
  - 값이 있는 경우: **해당 카테고리 목록만 진열 가능**  
    (카테고리 ID는 3.2에서 정의한 것 사용)

- **is_enabled**
  - 문자열 `true` / `false` (소문자) 사용
  - 비활성화/숨김 처리하려는 인스턴스는 `false`로 명시

- **note**
  - 자유 서술식 텍스트
  - 콤마(,) 포함 시 전체를 `"`로 감싸야 합니다.

#### 4.2.4 dp_allowed_categories 작성 규칙

- 여러 카테고리는 **세미콜론(;)** 으로 구분합니다.
  - 예시
    - `outer_coat;outer_jacket`
    - `top_tshirt;top_knit;bottom_jeans`

- 공백(스페이스) 사용 금지
  - ❌ `outer_coat; outer_jacket`  
  - ✅ `outer_coat;outer_jacket`

- 카테고리 ID는 **3.2 Product 카테고리와 ID**에서 정의한 값만 사용합니다.

- **비워둔 경우(빈 문자열)** 의 의미
  - 해당 Furniture에는 **카테고리 제한 없음**으로 간주합니다.

- 와일드카드/패턴 매칭은 사용하지 않습니다.
  - ❌ `outer_*`  
  - 항상 **완전한 카테고리 ID**만 나열합니다.

#### 4.2.5 예시

```csv
store_id,furniture_instance_id,furniture_type_id,pos_x,pos_y,pos_z,rot_y,glb_override,dp_allowed_categories,is_enabled,note
store_gangnam_flagship,furinst_gangnam_entrance_wall01,fur_wall_2bay,3.2,0.0,1.5,90,,outer_coat;outer_jacket,true,"입구 오른쪽 메인 월랙"
store_gangnam_flagship,furinst_gangnam_center_table01,fur_table_3tier,5.8,0.0,2.4,0,,top_tshirt;top_knit,true,"센터 테이블"
store_hongdae_pop,furinst_hongdae_gondola01,fur_gondola_2way,2.0,0.0,3.0,180,,bottom_jeans;bottom_pants,true,"데님 곤돌라"

---

### 4.3 product_placement.csv

`product_placement.csv` 는 특정 매장의 특정 Furniture Instance 안에  
**어떤 상품을 어떤 슬롯에, 몇 페이싱으로 배치했는지**를 정의합니다.

- 한 행(row) = **상품 1종의 배치 정보**
- “슬롯(slot) 순서 + 페이싱(facing) 수” 개념을 기본으로 합니다.

#### 4.3.1 컬럼 정의

| 컬럼명               | 타입   | 필수 | 설명                                                                 | 예시                                      |
|----------------------|--------|------|----------------------------------------------------------------------|-------------------------------------------|
| store_id             | string | ✅   | 매장 ID. `store_master.csv`의 `store_id` 참조                        | `store_gangnam_flagship`                  |
| furniture_instance_id| string | ✅   | Furniture 인스턴스 ID. `furniture_layout.csv`의 `furniture_instance_id` 참조 | `furinst_gangnam_entrance_wall01` |
| slot_index           | int    | ✅   | 해당 Furniture 내에서의 슬롯/베이/위치 순서 (1부터 시작)            | `1`                                       |
| product_id           | string | ✅   | 상품 ID (SKU 또는 스타일/컬러 단위). 외부 MD 시스템 PK              | `prod_10000001`                           |
| facing_count         | int    | ✅   | 전면에 보이는 페이싱 개수(같은 상품을 연속으로 몇 개 두는지)        | `4`                                       |
| is_primary           | bool   | ❌   | 주요 진열(메인 페이싱) 여부. 기본값은 `false`                        | `true`                                    |
| offset_x             | float  | ❌   | 슬롯 기준 X 오프셋 (m). 0이면 기본 템플릿 위치                       | `0.0`                                     |
| offset_y             | float  | ❌   | 슬롯 기준 Y 오프셋 (m)                                               | `0.05`                                    |
| offset_z             | float  | ❌   | 슬롯 기준 Z 오프셋 (m)                                               | `0.0`                                     |
| note                 | string | ❌   | 운영/머천다이징 메모                                                 | `입구 메인 코트`                          |

> `offset_*` 컬럼은 초기에는 모두 `0`으로 두고,  
> 나중에 세밀한 위치 튜닝이 필요해질 때만 사용하는 것을 권장합니다.

#### 4.3.2 값 규칙

- **store_id**
  - 반드시 `store_master.csv`에 존재하는 값만 사용합니다.

- **furniture_instance_id**
  - 반드시 `furniture_layout.csv`에 존재하는 값만 사용합니다.
  - `(store_id, furniture_instance_id)` 조합이 실제 매장에 배치된 인스턴스를 가리켜야 합니다.

- **slot_index**
  - 1 이상 정수, 기본적으로 **1부터 시작**합니다.
  - 동일 `(store_id, furniture_instance_id, slot_index)` 조합 안에서는  
    하나의 `product_id`만 사용하는 것을 권장합니다.
    - 슬롯 하나에 여러 상품을 혼합 진열해야 하는 특별 케이스는  
      팀 합의 후 스키마 확장으로 처리하는 것을 권장합니다.

- **product_id**
  - 실제 MD 시스템에서 유효한 상품 ID여야 합니다.
  - `/assets/product_glb/`에 대응 GLB가 존재하는지 별도 검증이 필요합니다.
    - 예) 규칙: `/assets/product_glb/prod_{product_id}.glb`

- **facing_count**
  - 1 이상 정수.
  - 실제 재고 수량이 다르더라도, **화면상에서 보이고 싶은 페이싱 개수** 기준으로 설정합니다.
  - 너무 큰 값(예: 999 등)은 사용하지 않도록 합니다.

- **is_primary**
  - 문자열 `true` / `false` (소문자) 사용.
  - 메인 룩, 집중 노출 상품 등은 `true` 로 설정합니다.
  - 지정하지 않을 경우(빈 값) 기본적으로 `false`로 취급합니다.

- **offset_x / offset_y / offset_z**
  - 단위: **미터(m)**.
  - 기본값: `0.0`.
  - 템플릿 기준 위치에서의 미세 조정이 필요한 경우에만 사용합니다.
    - 예: 살짝 위로 올려야 할 때 `offset_y = 0.05` 등.

- **note**
  - 자유 입력 텍스트.
  - 콤마(,)가 포함될 경우 전체를 `"`로 감싸야 합니다.

#### 4.3.3 예시

```csv
store_id,furniture_instance_id,slot_index,product_id,facing_count,is_primary,offset_x,offset_y,offset_z,note
store_gangnam_flagship,furinst_gangnam_entrance_wall01,1,prod_10000001,5,true,0.0,0.0,0.0,"입구 메인 코트"
store_gangnam_flagship,furinst_gangnam_entrance_wall01,2,prod_10000002,4,false,0.0,0.0,0.0,"코디용 니트"
store_gangnam_flagship,furinst_gangnam_center_table01,1,prod_20000001,6,true,0.0,0.05,0.0,"테이블 상단 접어서 진열"
store_hongdae_pop,furinst_hongdae_gondola01,1,prod_30000001,4,false,0.0,0.0,0.0,"데님 메인 상품"





