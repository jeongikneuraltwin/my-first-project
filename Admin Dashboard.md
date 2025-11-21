# NEURALTWIN 관리자 콘솔 통합 아키텍처

- **버전**: 1.0  
- **상태**: Draft (내부 설계용)  
- **대상 시스템**: NEURALTWIN Admin Web (운영·관리자용 콘솔)  

---

## 0. 문서 개요

### 0.1 목적

본 문서는 NEURALTWIN 플랫폼의 **관리자용 웹 콘솔(Admin Console)** 아키텍처를 정의한다.  
고객(테넌트)이 사용하는 분석·시뮬레이션·3D 디지털 트윈 UI와는 별도의 채널로, 다음 목적을 가진다.

- 테넌트/사용자/플랜/사용량 관리
- 데이터 파이프라인 및 품질 모니터링
- Ontology & Graph 스키마 및 인스턴스 거버넌스
- 3D Digital Twin 제작·배포 파이프라인 관리
- AI/시뮬레이션 상태 및 오류 모니터링
- 시스템/에러/성능 모니터링
- 운영자가 고객 요청·문제를 효율적으로 처리할 수 있는 프로세스 제공

### 0.2 범위

- **포함**
  - Admin 전용 웹 UI 정보 구조(IA)
  - 주요 페이지 및 노출 데이터 정의
  - 운영 플로우 및 대응 시나리오
  - 권한/보안 개요
  - 관리자용 메타 데이터 구조(고수준)

- **제외**
  - 고객용(테넌트) 애플리케이션 상세 아키텍처  
  - 개별 컴포넌트 수준 UI/UX 상세 디자인  
  - 인프라/네트워크 레벨 배포 구성 세부사항

---

## 1. 관리자 콘솔의 역할 및 페르소나

### 1.1 주요 역할

관리자 콘솔은 다음 역할을 수행한다.

1. **비즈니스 운영 관리**
   - 테넌트(고객사)·플랜·사용량·라이선스 관리
   - CS/영업 관점에서의 고객 상태 파악 및 조치

2. **데이터 품질 및 파이프라인 관리**
   - 임포트/동기화/집계 파이프라인 모니터링
   - 데이터 건강도(Data Health) 관리 및 재처리

3. **Ontology & Graph 거버넌스**
   - 온톨로지(Entity/Relation 타입) 정의 및 변경
   - Graph 인스턴스(Entities & Relations) 모니터링 및 품질 관리  
   - **Graph는 관리자 전용**이며, 고객 UI에서는 직접 노출되지 않음

4. **3D Digital Twin 제작·배포**
   - 고객이 자체 3D 모델이 없는 경우,  
     내부/외주 팀을 통한 3D 모델링·텍스처링·베이크·glb 추출 및 메타데이터 생성 관리
   - 완성된 SceneRecipe 및 3D Assets 를 고객 테넌트로 배포

5. **AI & Simulation/Recommendation 모니터링**
   - 각 테넌트의 시뮬레이션 실행 현황, 실패율, 응답 품질 모니터링
   - 이상 패턴(특정 테넌트의 비정상적인 실패 증가 등) 탐지

6. **시스템 안정성 및 보안 관리**
   - Edge Function/API 에러 모니터링
   - 기본 성능 트렌드 모니터링
   - RLS/권한 오작동 감시

### 1.2 내부 페르소나

- **운영/CS 담당자**
  - 테넌트 및 사용자 현황 파악
  - 고객 문의에 대한 상황 확인 및 간단한 조치 수행

- **데이터/AI 담당자**
  - 데이터 파이프라인 상태 및 품질 관리
  - Ontology/Graph 스키마 및 인스턴스 품질 점검
  - AI/시뮬레이션 이상 패턴 분석

- **시스템/플랫폼 엔지니어**
  - Edge Functions 및 주요 API 상태 모니터링
  - 성능 이슈 및 에러 핫스팟 파악
  - 운영 자동화 도입

- **3D 콘텐츠/디지털 트윈 담당자**
  - 매장 3D 모델링 제작 요청 관리
  - 3D Asset/Scene 품질 검수 및 배포

---

## 2. 전체 IA 구조 (관리자 콘솔 메뉴)

### 2.1 상위 IA 개요

관리자 콘솔은 **4개의 상위 도메인 탭**과 그 아래 **기능 단위 하위 섹션**으로 구성된다.

```text
NEURALTWIN ADMIN CONSOLE
├─ A. 운영/CS 관리
│  ├─ Tenants & Users
│  └─ Usage & Billing
│
├─ B. 데이터 & AI 관리
│  ├─ Data Pipeline & Quality
│  ├─ Ontology & Graph Governance
│  └─ AI & Simulation Monitoring
│
├─ C. 시스템 관리
│  ├─ System & Error Console
│  └─ Admin Tools & Settings
│
└─ D. 3D 디지털 트윈 관리
   └─ 3D Digital Twin Production
       ├─ 3D 제작 프로젝트
       ├─ 3D Asset 라이브러리
       └─ 씬 배포 현황
```

- 상위 탭(도메인): 운영/CS 관리, 데이터 & AI 관리, 시스템 관리, 3D 디지털 트윈 관리  
- 하위 섹션: Tenants & Users, Usage & Billing, Data Pipeline & Quality 등 기능 단위 화면들로 구성된다.  
- 라우팅(URL) 구조는 기능 단위 하위 섹션 기준으로 정의하고, UI 상에서 상위 탭별로 그룹화한다.

### 2.2 라우팅 구조 예시

(예시, 실제 구현 시 도메인/Prefix는 조정 가능.  
상위 탭은 UI 그룹이며, URL은 기능 단위 페이지에 직접 매핑한다.)

```tsx
<Routes>
  {/* Dashboard (공통 Admin 홈) */}
  <Route path="/admin" element={<AdminHome />} />

  {/* A. 운영/CS 관리 */}
  {/* 1. Tenants & Users */}
  <Route path="/admin/tenants" element={<TenantListPage />} />
  <Route path="/admin/tenants/:tenantId" element={<TenantDetailPage />} />

  {/* 2. Usage & Billing */}
  <Route path="/admin/usage" element={<UsageOverviewPage />} />
  <Route path="/admin/usage/:tenantId" element={<TenantUsageDetailPage />} />

  {/* B. 데이터 & AI 관리 */}
  {/* 3. Data Pipeline & Quality */}
  <Route path="/admin/data-pipeline" element={<DataPipelineOverviewPage />} />
  <Route path="/admin/data-pipeline/:tenantId" element={<TenantDataHealthPage />} />

  {/* 4. Ontology & Graph Governance */}
  <Route path="/admin/ontology" element={<OntologySchemaPage />} />
  <Route path="/admin/graph" element={<GlobalGraphMonitoringPage />} />
  <Route path="/admin/graph/:tenantId" element={<TenantGraphDetailPage />} />

  {/* 5. AI & Simulation Monitoring */}
  <Route path="/admin/ai-sim" element={<AISimOverviewPage />} />
  <Route path="/admin/ai-sim/:tenantId" element={<TenantAISimDetailPage />} />

  {/* D. 3D 디지털 트윈 관리 */}
  {/* 6. 3D Digital Twin Production */}
  <Route path="/admin/3d-projects" element={<DigitalTwinProjectsPage />} />
  <Route path="/admin/3d-projects/:projectId" element={<DigitalTwinProjectDetailPage />} />
  <Route path="/admin/3d-assets" element={<AssetLibraryPage />} />

  {/* C. 시스템 관리 */}
  {/* 7. System & Error Console */}
  <Route path="/admin/system/errors" element={<ErrorConsolePage />} />
  <Route path="/admin/system/functions" element={<EdgeFunctionsStatusPage />} />

  {/* 8. Admin Tools & Settings */}
  <Route path="/admin/tools" element={<AdminToolsPage />} />
  <Route path="/admin/settings" element={<AdminSettingsPage />} />
</Routes>
```

---

## 3. 섹션별 상세 설계

### 3.1 Tenants & Users

#### 3.1.1 목적

- 테넌트(조직), 매장, 사용자 계정, 권한 및 플랜 정보를 중앙에서 관리한다.  
- 특정 테넌트에서 발생하는 문제를 탐색하기 위한 Entry Point 역할을 한다.

#### 3.1.2 노출 데이터

1. **테넌트 목록 뷰**
   - tenant_id  
   - 조직명, 브랜드명  
   - 플랜 타입 (Free/Pro/Enterprise)  
   - 상태(Trial/Active/Suspended)  
   - 매장 개수, 활성 사용자 수  
   - 최근 활동 일시(마지막 로그인/마지막 데이터 업로드 등)  
   - Data Health Score (요약 지표, 3.3과 연동)  

2. **테넌트 상세 뷰**
   - 기본 정보  
     - 조직명, 국가/타임존, 통화  
     - 플랜 정보, 라이선스 한도 (`license_management`)  
   - 매장 정보 (`stores`, `hq_store_master`, `store_mappings`)  
   - 사용자 계정 목록  
     - 이메일, 역할(Role), 상태(Active/Disabled), 마지막 접속일  
   - 최근 활동 타임라인  
     - 데이터 임포트, 시뮬레이션 실행, 3D 씬 배포 등 주요 이벤트  

#### 3.1.3 관리 액션

- 테넌트 상태 변경 (예: Suspended 설정)  
- 플랜/라이선스 변경  
- 사용자 계정 잠금/해제  
- 비밀번호 초기화 링크 발송  
- “3D 디지털 트윈 제작 요청 생성” (5.1과 연계)  
- “데이터 재집계 요청” (3.3, 6.1과 연계)  

---

### 3.2 Usage & Billing

#### 3.2.1 목적

- 테넌트별/전체 사용량과 플랜 대비 사용률을 모니터링한다.  
- 요금제 설계·과금·한도 관리의 기반 데이터를 제공한다.

#### 3.2.2 노출 데이터

- 전역 사용량 요약  
  - 전체 API 호출량 (AI, Edge Functions, 외부 API 프록시 등)  
  - 스토리지 사용량 (store-data, 3D assets, logs)  
  - 시뮬레이션 호출 횟수 및 성공율  
- 테넌트별 사용량 상세  
  - 월간 API 호출량  
  - 스토리지 사용량 (GB)  
  - 시뮬레이션 실행 수 (`scenarios` 카운트)  
  - 대시보드 집계 호출량  
- 플랜 한도 대비 사용률 (라이선스)  
  - `license_management.api_calls_limit`, `storage_limit_gb`, `max_stores` 등과 비교  

#### 3.2.3 관리 액션

- 특정 테넌트의 한도 상향/하향 조정  
- 한도 초과 시 플래그 설정(자동 차단 vs 경고 모드 등)  
- 사용량 리포트 다운로드 (CSV/PDF)  

---

### 3.3 Data Pipeline & Quality

#### 3.3.1 목적

- 데이터 임포트/동기화/집계 파이프라인의 상태를 한눈에 파악한다.  
- 각 테넌트의 데이터 품질(Data Health)을 평가하고 문제 지점을 빠르게 찾을 수 있도록 한다.

#### 3.3.2 노출 데이터

1. **파이프라인 전역 뷰**
   - 최신 `user_data_imports` 성공/실패 로그  
   - 외부 데이터 동기화 (`data_sync_schedules`, `data_sync_logs`) 상태  
   - WiFi/IoT 데이터 수신 상태 (디바이스 마지막 수신 시간, offline 여부)  

2. **테넌트별 Data Health 뷰**
   - 최근 N일 임포트/동기화 성공율  
   - 주요 테이블별 데이터 볼륨 변화 추이 (예: visits/sales/wifi_tracking)  
   - 에러 유형 분포 (형식 오류, 누락 컬럼, 타임아웃 등)  
   - Data Health Score (내부 기준에 따른 스코어링)  

#### 3.3.3 관리 액션

- 실패한 Import Job 상세 보기 (에러 메시지, 예시 행)  
- 임포트/동기화 재시도  
- “대시보드 KPI 재집계” 요청 (해당 스토어/기간 대상)  
- 특정 테넌트에 대한 Data Health 리포트 출력  

---

### 3.4 Ontology & Graph Governance

> **중요**  
> - 고객(테넌트)용 애플리케이션에서는 Graph 구조가 직접 노출되지 않는다.  
> - 고객은 임포트 → 분석 결과/시뮬레이션 결과만 보며,  
>   **Ontology 스키마 및 Graph Entities/Relations는 관리자 전용 뷰에서만 조회·관리**된다.

#### 3.4.1 목적

- Ontology 스키마(Entity/Relation 타입) 정의·버전·변경 이력을 관리한다.  
- Graph 인스턴스(`graph_entities`, `graph_relations`)의 상태·규모·품질을 모니터링한다.  
- 스키마 변경이 각 테넌트 데이터에 미치는 영향을 관리한다.

#### 3.4.2 노출 데이터

1. **Ontology 스키마 뷰**
   - `ontology_entity_types`  
     - 엔티티 타입명, 레이블, 속성 목록, 3D 연관 메타데이터  
   - `ontology_relation_types`  
     - 관계 타입명, Source/Target 타입, 방향성 정보  
   - 스키마 버전/변경 이력 (내부 버전 테이블 또는 audit 로그)  

2. **Graph 모니터링 뷰 (Admin 전용 Graph 뷰어)**
   - 전역 Graph 개요  
     - 노드 수, 관계 수, 엔티티 타입별 분포  
   - 테넌트별 Graph 뷰  
     - 해당 테넌트/스토어의 `graph_entities`/`graph_relations` 구조  
     - 특정 엔티티 타입(예: Product, Zone 등) 중심의 부분 그래프 조회  
   - 품질 메트릭  
     - 관계 없는 고립 노드 비율  
     - 필수 속성 누락 비율  
     - 스키마 위반(예: 정의되지 않은 타입 간 관계) 카운트  

#### 3.4.3 관리 액션

- 엔티티/관계 타입 추가/수정/비활성화  
- 스키마 변경 시 “마이그레이션 플랜” 작성 및 단계적 적용  
- 특정 테넌트 Graph에 대한 정합성 검사 실행  
- 비정상 인스턴스 자동 정리(또는 후보 목록 생성)  

---

### 3.5 3D Digital Twin Production

> 고객이 자체 3D 매장 모델을 보유하지 않은 경우,  
> 관리자 콘솔을 통해 **3D 디지털 트윈 제작·배포 파이프라인**을 관리한다.

#### 3.5.1 목적

- 3D 모델링/텍스처링/베이크/`glb` 추출 작업을 요청·진행·검수·배포까지 관리한다.  
- 완성된 SceneRecipe 및 glb Assets 를 고객 테넌트의 `store_scenes` 및 3D 자산 스토리지로 안전하게 전달한다.

#### 3.5.2 주요 개념

- **3D Project**  
  - 특정 테넌트의 특정 매장을 위한 3D 제작 단위  
  - 상태 예: `Requested` → `InProduction` → `InReview` → `Approved` → `Deployed`  

- **3D Asset**  
  - `glb` 파일 및 관련 텍스처/메타데이터  
  - 예: 기본 구조(Base), 가구(Furniture), 집기/Fixture, 존/Zone 마커, Product Display  

- **SceneRecipe**  
  - 최종적으로 `store_scenes.recipe_data` 에 저장되는 JSON 구조  
  - 어떤 Asset 을 어떤 위치/스케일/회전으로 배치할지, 조명/카메라 설정 등을 포함  

#### 3.5.3 노출 데이터

1. **3D 프로젝트 목록 뷰**
   - Project ID  
   - 테넌트, 스토어  
   - 생성일, 최근 업데이트일  
   - 상태 (Requested / InProduction / InReview / Approved / Deployed)  
   - 담당자(내부 3D 아티스트/외주사)  

2. **3D 프로젝트 상세 뷰**
   - 요청 정보  
     - 매장 도면(PDF/Image), 사진, 스케치 등 첨부파일  
     - 면적, 층수, 주요 존 정의(입구, 핫존, 카운터 등)  
   - 작업 진척도  
     - 베이스 구조, 가구, 상품 디스플레이, 조명 등 작업 단계 체크리스트  
   - Asset 목록  
     - 생성된 `glb` 파일 목록 및 메타데이터  
   - Preview  
     - 내부용 3D 뷰어 (QA용)  
   - 배포 상태  
     - 연결된 `store_scenes` 레코드 여부  
     - 테넌트 앱에서 실제로 사용 중인지 사용률(접속 수) 등  

3. **3D Asset Library 뷰**
   - 재사용 가능한 가구/Fixture/Zone/장치 등 컴포넌트화된 Asset 목록  
   - Ontology 엔티티 타입과 연결 정보 (어떤 EntityType 에 사용되는지)  

#### 3.5.4 관리 액션

- 테넌트 상세 화면(3.1)에서 “3D 디지털 트윈 제작 요청” 생성  
- 3D 프로젝트 상태 변경 (Requested→InProduction→…)  
- Asset 업로드/수정/삭제  
- SceneRecipe 생성/수정/검수 후 **테넌트 `store_scenes` 로 배포**  
- 배포 롤백 (기존 Scene 으로 되돌리기)  

---

### 3.6 AI & Simulation Monitoring

#### 3.6.1 목적

- 각 테넌트의 시뮬레이션/AI 기능 사용 현황과 실패율을 모니터링한다.  
- 모델/파이프라인 수준에서의 문제를 조기에 감지한다.

#### 3.6.2 노출 데이터

- 전역 요약  
  - 전체 시뮬레이션 실행 수 (기간별)  
  - 실패율 (에러 코드/타입 별)  
  - 평균 응답 시간  
- 테넌트별 상세  
  - `scenarios` (타입별: layout/pricing/demand/recommendation)  
  - `simulation_results`  
  - `ai_recommendations`  
  - `ai_scene_analysis` (3D 분석 관련)  
- 대표 지표  
  - 시뮬레이션 타입별 실패율  
  - 특정 테넌트의 실패율이 글로벌 평균 대비 이상치인지 여부  

#### 3.6.3 관리 액션

- 실패한 시나리오 상세 로그 열람  
- 재실행 요청  
- 특정 테넌트/기능에 대한 임시 제한 설정 (예: Pricing Simulation 임시 중단)  
- 모델 버전/설정 변경 시점 기록 (향후 A/B 비교용)  

---

### 3.7 System & Error Console

#### 3.7.1 목적

- Edge Functions, API, Frontend 오류 등 시스템 전반의 에러를 중앙에서 모니터링한다.  
- 운영자가 문제의 위치(테넌트별/기능별/시간대별)를 빠르게 추적할 수 있도록 한다.

#### 3.7.2 노출 데이터

- Edge Function 호출 로그 요약  
  - 함수명, 호출 수, 실패율, 평균 응답 시간  
- HTTP 에러 로그 요약  
  - 4xx/5xx 비율, 엔드포인트별 분포  
- 성능 트렌드  
  - 대략적인 응답 시간, 특정 시간대의 부하 집중 여부  

#### 3.7.3 관리 액션

- 에러 로그 필터링 (테넌트/함수/HTTP 코드/시간대)  
- 에러 세부 정보 확인 (Request/Response 요약, Trace ID, 관련 테넌트)  
- 반복되는 에러 유형에 태그/메모 추가 (내부 운영 문서화와 연결)  

---

### 3.8 Admin Tools & Settings

#### 3.8.1 목적

- 운영자가 반복적으로 수행하는 관리 작업을 쉽게 실행할 수 있도록 도구화한다.  
- 기능 플래그/실험 설정을 중앙에서 관리한다.

#### 3.8.2 예시 기능

- Feature Flag 관리  
  - `FEATURE_FLAGS` 스타일의 기능 On/Off (테넌트/플랜별)  
- 강제 재집계/리빌드  
  - 대시보드 KPI 재집계 트리거  
  - 특정 기간/스토어에 대한 Graph 재생성(재임포트 기반) 요청  
- Impersonation (선택 사항)  
  - 운영자가 특정 테넌트 유저 뷰를 “읽기 전용”으로 미러링  
    (실제 행동 발생 여부에 대한 보안 정책 필요)  
- Admin 계정/역할 관리  
  - 슈퍼관리자/일반운영자/읽기전용 계정 등  

---

## 4. 운영 시나리오 & 대응 플로우

### 4.1 시나리오 1 – 데이터 임포트 실패 문의

1. **사용자 증상**
   - “CSV를 업로드했는데 계속 실패합니다.”

2. **관리자 플로우**
   - Tenants & Users → 해당 테넌트 검색 → 상세 화면 진입  
   - “최근 데이터 임포트” 위젯에서 실패 건 클릭  
   - Data Pipeline & Quality → 해당 Job 상세 보기  
     - 오류 유형(필수 컬럼 누락/형식 오류/타임아웃 등) 확인  
   - 필요 시 “재시도” 실행  
   - 문제가 스키마/Ontology와 관련된 경우  
     → Ontology & Graph Governance 에서 해당 데이터 타입 스키마 점검  
   - 처리 결과를 CS 시스템 또는 내부 메모에 기록  

---

### 4.2 시나리오 2 – 대시보드 숫자 이상 문의

1. **사용자 증상**
   - “전날보다 유입이 갑자기 0으로 나오는데, 실제로는 매장이 열려 있었습니다.”

2. **관리자 플로우**
   - Tenants & Users → 해당 테넌트 선택  
   - Data Pipeline & Quality → 해당 날짜 데이터 Health 확인  
     - WiFi/Visits 데이터 누락/지연 여부 확인  
   - 외부 API(공휴일/이벤트) 동기화 상태 확인  
   - 경우에 따라 KPI 집계 함수 에러 여부를 System & Error Console 에서 추가 확인  
   - 문제가 파악되면 “해당 기간 대시보드 재집계” 실행  
   - 재집계 이후, 수치 정상화 여부를 직접 확인 후 사용자에게 안내  

---

### 4.3 시나리오 3 – 시뮬레이션 실패 문의

1. **사용자 증상**
   - “어제부터 레이아웃 시뮬레이션이 계속 에러가 납니다.”

2. **관리자 플로우**
   - AI & Simulation Monitoring → 해당 테넌트 필터  
   - 실패 시나리오 목록에서 최근 레코드 확인  
   - 관련 Edge Function 로그/오류 메시지 확인  
   - 파라미터/데이터/모델 응답 중 어느 영역 문제인지 분류  
   - 단기 조치:  
     - 특정 시나리오 재실행  
     - 동일 유형 시뮬레이션 임시 제한(문제 확산 방지)  
   - 장기 조치:  
     - 데이터/모델/코드 수정 후 릴리즈  
     - 재테스트 및 모니터링  

---

### 4.4 시나리오 4 – 3D 디지털 트윈 제작 요청 및 배포

1. **사용자 상황**
   - “우리 매장 3D 모델이 없는데, 디지털 트윈을 사용하고 싶습니다.”

2. **관리자 플로우**
   - Tenants & Users → 해당 테넌트 상세 → “3D 디지털 트윈 제작 요청” 버튼  
   - 3D Digital Twin Production → 새 프로젝트 생성 페이지로 이동  
   - 매장 도면/사진/필수 정보를 첨부 후 Project 생성  
   - 내부/외주 3D 팀이 작업 진행, 상태를 `InProduction` → `InReview` 로 변경  
   - QA 담당자가 SceneRecipe 및 Assets 품질 확인 (Preview)  
   - `Approved` 상태로 변경 후 “배포” 실행 → 해당 테넌트 `store_scenes` 에 SceneRecipe 등록  
   - 고객 앱에서 `/digital-twin/3d` 접근 시 생성된 씬 사용 가능  

---

### 4.5 시나리오 5 – Ontology 스키마 변경 필요

1. **내부 요구사항**
   - 새로운 엔티티 타입/속성을 추가해야 하거나  
     기존 타입 구조가 비즈니스 요구에 맞지 않는 상황.

2. **관리자 플로우**
   - Ontology & Graph Governance → 스키마 설계 변경  
   - 영향 받는 테넌트/데이터 유형 파악  
   - 제한된 범위에서 사전 테스트용 테넌트/스토어 선정  
   - 변경 적용 후 Data Pipeline & Quality, Graph Monitoring 을 통해 품질 점검  
   - 문제 없을 시 전체 테넌트에 순차 적용  
   - 스키마 버전 및 변경 이력 기록  

---

## 5. 권한 & 보안 아키텍처 (Admin 관점)

### 5.1 Admin 권한 모델

- Admin 전용 Role (예: `platform_admin`, `ops_admin`, `read_only_admin`)  
- 고객 테넌트의 일반 사용자 Role 과는 분리  
- Supabase RLS 및 `has_role()`, `is_admin()` 함수 기반 접근 제어  

### 5.2 데이터 접근 원칙

- 고객 테넌트 데이터는 **관리자에 의해 조회 가능**하되,  
  - Audit 로그에 모든 조회 내역 기록 (누가, 언제, 어떤 테넌트/스토어/데이터를 조회했는지)  
- Impersonation 기능 사용 시  
  - 실제 조작 권한(쓰기/삭제)은 제한하거나 별도 경고 표시  
  - 필요한 경우 “읽기 전용” 미러링 모드 우선 도입  

### 5.3 Graph/Ontology 접근 정책

- Graph 구조(`graph_entities`, `graph_relations`)는 기본적으로 **관리자 콘솔에서만 조회/관리 가능**  
  (고객 앱에서는 직접 노드/엣지를 볼 수 없음)  
- 고객 앱의 Graph 관련 기능 요청은 반드시 Admin 측에서 검토 후 단계적 제공  

---

## 6. 데이터/로그 구조 개요 (Admin용 메타 데이터)

아래는 관리자 콘솔을 위해 추가될 수 있는 예시적인 메타 데이터 구조(고수준)이다.

- `admin_incidents`  
  - 고객 문의/이슈 티켓과 시스템 상태를 연결  
- `admin_actions`  
  - 관리자 수행 행위 (재집계, 배포, 강제 제한 설정 등) Audit 로그  
- `admin_scene_projects`  
  - 3D Digital Twin 제작 프로젝트 메타데이터  
- `admin_feature_flags`  
  - 기능 플래그/실험 설정 정보  

구체적인 스키마는 향후 구현 단계에서 세부 설계한다.

---

## 7. 향후 확장 포인트

- 에러/데이터 이슈 발생 시 Slack/이메일 등으로 자동 알림  
- 특정 패턴(예: 연속 Import 실패, 시뮬레이션 실패 급증)에 대한 룰 기반 자동 대응  
- 관리자 콘솔 내 검색 기능 (테넌트/스토어/유저/이슈를 한 번에 찾는 Global Search)  
- Admin용 KPI 대시보드 (전체 테넌트 활성도, 이슈 처리 SLA 등)  
- 3D 디지털 트윈 제작 리드 타임/품질 지표 관리 대시보드  
- Ontology/Graph 변경에 대한 자동 영향 분석 리포트  
