
# NEURALTWIN 관리자 콘솔 통합 아키텍처

- **버전**: v9
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
  <Route path="/admin/3d-assets" element={<DigitalTwinAssetLibraryPage />} />

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

> 각 섹션은 공통적으로
> - 목적
> - 화면 구조
> - 주요 화면(목록/상세 등)의 노출 데이터/필터/관리 액션
> 구조로 정리한다.

---

### 3.1 Tenants & Users

#### 3.1.1 목적

- 테넌트(조직), 매장, 사용자 계정, 권한 및 플랜 정보를 중앙에서 관리한다.
- 특정 테넌트에서 발생하는 문제를 탐색하기 위한 Entry Point 역할을 한다.

#### 3.1.2 화면 구조

1. **Tenant List**
   - 라우트: `/admin/tenants`
   - 전체 테넌트 현황을 요약해서 보여주는 목록 화면.

2. **Tenant Detail**
   - 라우트: `/admin/tenants/:tenantId`
   - 단일 테넌트의 매장/사용자/플랜/최근 활동을 통합해서 보여주는 상세 화면.

#### 3.1.3 Tenant List 뷰

**노출 데이터**

- tenant_id
- 조직명, 브랜드명
- 플랜 타입 (Free/Pro/Enterprise)
- 상태(Trial/Active/Suspended)
- 매장 개수, 활성 사용자 수
- 최근 활동 일시(마지막 로그인/마지막 데이터 업로드 등)
- Data Health Score (요약 지표, 3.3 Data Pipeline & Quality와 연동)

**필터/정렬**

- 플랜 타입별 필터
- 상태별 필터 (Active/Suspended 등)
- 최근 활동 기준 정렬 (최신 활동 순)
- 브랜드/조직명 검색

**관리 액션 (목록에서의 단축 액션)**

- 테넌트 상태 변경 (Active ↔ Suspended)
- 상세 화면으로 이동
- 테넌트별 3D 프로젝트 목록 바로가기
- 테넌트별 Data Health 상세 바로가기

#### 3.1.4 Tenant Detail 뷰

**노출 데이터**

1. **기본 정보 패널**
   - 조직명, 브랜드명
   - 국가/타임존, 통화
   - 플랜 정보 (타입, 시작/종료일 등)
   - 라이선스 한도 (`license_management`)

2. **매장 정보 패널**
   - 매장 리스트 (`stores`, `hq_store_master`, `store_mappings`)
   - 매장별 상태(오픈/휴점), 개장일, 면적 등 기본 메타데이터

3. **사용자 계정 패널**
   - 이메일
   - 역할(Role) – admin, manager, viewer 등
   - 상태(Active/Disabled)
   - 마지막 접속일
   - (선택) SSO/IDP 연동 정보

4. **최근 활동 타임라인**
   - 데이터 임포트(성공/실패)
   - 시뮬레이션 실행 기록
   - 3D 씬 배포 이벤트
   - 주요 설정 변경(플랜 변경, 기능 플래그 변경 등)

**관리 액션**

- 테넌트 상태 변경 (예: Suspended 설정)
- 플랜/라이선스 변경
- 사용자 계정 잠금/해제
- 비밀번호 초기화 링크 발송
- 테넌트 단위 3D 디지털 트윈 요청 현황 확인
  - 해당 테넌트와 연관된 3D 제작 요청(프로젝트) 목록 조회
  - 클릭 시 3.5 3D Digital Twin Production 상세 화면으로 이동
- 데이터 재집계 관련 액션 (핵심 링크 역할)
  - 해당 테넌트에 대한 재집계 Job 실행 이력 조회
  - 3.3 Data Pipeline & Quality 또는 3.8 Admin Tools & Settings로 이동하여
    특정 스토어/기간에 대한 재집계 실행

---

### 3.2 Usage & Billing

#### 3.2.1 목적

- 테넌트별/전체 사용량을 모니터링하고, 기능·플랜 설계 및 향후 과금 정책 수립에 참고할 수 있는 데이터를 제공한다.
- 현 단계에서는 한도 설정·강제 차단 등 제한 기능은 제공하지 않고,
  사용량 추세를 파악하기 위한 관찰·리포트 용도에 초점을 둔다.

#### 3.2.2 화면 구조

1. **Usage Overview (전역 뷰)**
   - 라우트: `/admin/usage`
   - 전체 시스템 차원의 사용량/트래픽/시뮬레이션 사용 현황 요약.

2. **Tenant Usage Detail (테넌트별 상세)**
   - 라우트: `/admin/usage/:tenantId`
   - 단일 테넌트에 대한 기간별 사용량/추세/이상 패턴 확인.

#### 3.2.3 Usage Overview 뷰

**노출 데이터**

- 전역 사용량 요약
  - 전체 API 호출량 (AI, Edge Functions, 외부 API 프록시 등)
  - 스토리지 사용량 (store-data, 3D assets, logs)
  - 시뮬레이션 호출 횟수 및 성공률
  - 대시보드 조회/집계 호출량 (대략적인 사용 강도 지표)

- 플랜/세그먼트별 요약
  - 플랜 타입별 평균/총 사용량
  - 상위 N개 테넌트(사용량 기준) 리스트

**필터/정렬**

- 기간 필터 (최근 7일, 30일, 특정 월)
- 기능 타입별 필터 (AI, Simulation, Dashboard 등)
- 플랜 타입별 필터

**관리 액션**

- 특정 테넌트 클릭 시 Tenant Usage Detail로 이동
- 요약 리포트(전역) 다운로드 (CSV/PDF)

#### 3.2.4 Tenant Usage Detail 뷰

**노출 데이터**

- 월간/기간별 API 호출량 (요청 수, 에러 비율)
- 스토리지 사용량 (GB)
- 시뮬레이션 실행 수 (`scenarios` 카운트, 타입별 분포)
- `simulation_results` 수, 실패율
- 대시보드 집계·조회 호출량
- (선택) 플랜 정보/라이선스 정보 요약
  - 현재 플랜 타입 (Free / Pro / Enterprise 등)
  - 등록 매장 수, 활성 사용자 수

**필터/정렬**

- 기간 필터 (커스텀 날짜 범위)
- 기능 타입별 필터 (API, Simulation, Dashboard 등)
- 스토어별 필터 (특정 매장 사용량만 분리해서 보기)

**관리 액션**

- 사용량 리포트 다운로드 (해당 테넌트 전용 CSV/PDF)
- 특정 기능(예: 과도한 Simulation 사용)에 메모/태그 추가
- 이상 패턴 감지 시 AI & Simulation Monitoring, System & Error Console로 바로가기

---

### 3.3 Data Pipeline & Quality

#### 3.3.1 목적

- 데이터 임포트/동기화/집계 파이프라인의 상태를 한눈에 파악한다.
- 각 테넌트의 데이터 품질(Data Health)을 평가하고 문제 지점을 빠르게 찾을 수 있도록 한다.

#### 3.3.2 화면 구조

1. **Data Pipeline Overview (전역 뷰)**
   - 라우트: `/admin/data-pipeline`
   - 전체 Import/Sync/집계 파이프라인 상태 요약.

2. **Tenant Data Health Detail**
   - 라우트: `/admin/data-pipeline/:tenantId`
   - 단일 테넌트에 대한 Data Health 상세/에러 로그/재시도 액션 제공.

#### 3.3.3 Data Pipeline Overview 뷰

**노출 데이터**

- 최신 `user_data_imports` 성공/실패 로그 요약
- 외부 데이터 동기화 (`data_sync_schedules`, `data_sync_logs`) 상태
- WiFi/IoT 데이터 수신 상태
  - 디바이스 마지막 수신 시간
  - offline 여부

- 전역 Data Health 지표
  - 최근 N일 평균 Import 성공률
  - 주요 테이블별 평균 데이터 볼륨 변화(전 테넌트 합산)
  - 실패 Job 상위 유형

**필터/정렬**

- 기간 필터 (최근 24시간/7일/30일 등)
- Job 타입 필터 (Import/Sync/Aggregation)
- 상태 필터 (성공/실패/진행 중)

**관리 액션**

- 실패 Job 상세 화면 이동 (Tenant Data Health Detail)
- 전체 Import/Sync 재시도 트리거(제한적, 운영자에게 경고 표시)
- 에러 유형별 메모/태그 추가

#### 3.3.4 Tenant Data Health Detail 뷰

**노출 데이터**

- 최근 N일 임포트/동기화 성공율
- 주요 테이블별 데이터 볼륨 변화 추이
  - 예: visits, sales, wifi_tracking 등
- 에러 유형 분포
  - 형식 오류, 누락 컬럼, 타임아웃, 인증 실패 등
- Data Health Score (내부 기준에 따른 스코어링)
  - 예: A/B/C 등급 또는 0–100 점수

- 실패 Job 리스트
  - Job ID, Job 타입
  - 실행 시각, 대상 스토어/기간
  - 에러 메시지 요약
  - (선택) 예시 에러 레코드 샘플

**필터/정렬**

- 기간 필터
- Job 타입 필터
- 에러 유형 필터

**관리 액션**

- 실패 Job 재시도
- 대시보드 KPI 재집계 요청 (해당 스토어/기간 대상)
- Data Health 리포트 출력 (CSV/PDF)
- Ontology & Graph Governance로 이동하여 스키마 관련 이슈 확인

---

### 3.4 Ontology & Graph Governance

> 중요
> - 고객(테넌트)용 애플리케이션에서는 Graph 구조가 직접 노출되지 않는다.
> - 고객은 임포트 → 분석 결과/시뮬레이션 결과만 보며,
>   Ontology 스키마 및 Graph Entities/Relations는 관리자 전용 뷰에서만 조회·관리된다.

#### 3.4.1 목적

- Ontology 스키마(Entity/Relation 타입) 정의·버전·변경 이력을 관리한다.
- Graph 인스턴스(`graph_entities`, `graph_relations`)의 상태·규모·품질을 모니터링한다.
- 스키마 변경이 각 테넌트 데이터에 미치는 영향을 관리한다.

#### 3.4.2 화면 구조

1. **Ontology Schema**
   - 라우트: `/admin/ontology`
   - 엔티티/관계 타입 정의, 속성, 버전 이력 관리.

2. **Global Graph Monitoring**
   - 라우트: `/admin/graph`
   - 전역 Graph 개요 및 품질 지표 확인.

3. **Tenant Graph Detail**
   - 라우트: `/admin/graph/:tenantId`
   - 테넌트별 Graph 인스턴스 상태, 품질 문제 점검.

#### 3.4.3 Ontology Schema 뷰

**노출 데이터**

- `ontology_entity_types`
  - 엔티티 타입명, 레이블
  - 속성 목록 (필수/선택, 타입, 단위 등)
  - 3D 연관 메타데이터 (3D Asset 타입, Placement 규칙 등)

- `ontology_relation_types`
  - 관계 타입명, 레이블
  - Source/Target 엔티티 타입
  - 방향성 정보 (단방향/양방향)
  - 제약 조건 (카디널리티 등)

- 스키마 버전/변경 이력
  - 버전 ID, 적용 일시
  - 변경 요약, 영향 범위

**관리 액션**

- 엔티티/관계 타입 추가/수정/비활성화
- 스키마 변경 초안 작성 및 저장(임시 상태)
- 변경 승인 및 배포(버전 증가)
- 특정 버전 기준 스키마 diff 보기

#### 3.4.4 Global Graph Monitoring 뷰

**노출 데이터**

- 전역 Graph 개요
  - 전체 노드 수, 관계 수
  - 엔티티 타입별 분포 (노드 수)
  - 관계 타입별 분포 (엣지 수)

- 품질 메트릭
  - 관계 없는 고립 노드 비율
  - 필수 속성 누락 비율
  - 스키마 위반(예: 정의되지 않은 타입 간 관계) 카운트

- 테넌트별 요약
  - 각 테넌트 노드/엣지 수
  - 각 테넌트의 Graph Health Score

**관리 액션**

- 특정 테넌트 Graph 상세로 이동
- 품질 메트릭 기준 상위 이슈 테넌트 필터링
- Graph 정합성 검사 Job 실행 (전역/부분)

#### 3.4.5 Tenant Graph Detail 뷰

**노출 데이터**

- 테넌트별 Graph 개요
  - 노드 수, 관계 수
  - 엔티티 타입별 분포(예: Product, Zone, Fixture 등)
- 특정 엔티티 타입 중심 부분 그래프 조회
  - 예: 특정 매장 Zone 주변 관계 구조
- 품질 메트릭
  - 고립 노드 리스트
  - 필수 속성 누락 엔티티 리스트
  - 스키마 위반 관계 리스트

**관리 액션**

- 정합성 검사 실행(해당 테넌트만)
- 비정상 인스턴스 목록 Export
- 자동 정리 Job 트리거(또는 후보만 마크)
- 3D Digital Twin Production으로 이동하여 Scene/Graph 매핑 상태 확인

---

### 3.5 3D Digital Twin Production

> 고객이 자체 3D 매장 모델을 보유하지 않은 경우,
> 관리자 콘솔을 통해 3D 디지털 트윈 제작·배포 파이프라인을 관리한다.

#### 3.5.1 목적

- 3D 모델링/텍스처링/베이크/`glb` 추출 작업을 요청·검수·배포까지 관리한다.
- 완성된 SceneRecipe 및 glb Assets 를 고객 테넌트의 `store_scenes` 및 3D 자산 스토리지로 안전하게 전달한다.
- 테넌트가 직접 디지털 트윈을 편집할 수 있도록, 초기 베이스 씬을 제공하는 역할을 수행한다.

#### 3.5.2 화면 구조

1. **3D Project List**
   - 라우트: `/admin/3d-projects`
   - 테넌트/스토어별 3D 디지털 트윈 제작 프로젝트 목록.

2. **3D Project Detail**
   - 라우트: `/admin/3d-projects/:projectId`
   - 단일 프로젝트에 대한 요청 정보, 참고 자료, 3D 작업 결과(glb),
     Scene Preview, 배포 상태 등을 종합적으로 보여주는 상세 화면.
   - 관리자가 glb/gltf 파일을 업로드하는 모든 작업은 이 화면에서만 수행.

3. **3D Asset Library**
   - 라우트: `/admin/3d-assets`
   - 재사용 가능한 3D 컴포넌트(glb) 목록과 범위(scope), 사용처, 스토리지 위치를 관리.

#### 3.5.3 3D Project List 뷰

**목적**

- 3D 제작 요청의 전체 현황을 한눈에 파악하고, 우선순위를 정해 처리할 수 있도록 한다.

**노출 데이터**

- Project ID
- 테넌트, 스토어
- 프로젝트 이름/설명 (요청 시 입력한 제목)
- 상태 (`Requested` / `InProduction` / `InReview` / `Approved` / `Deployed`)
- 생성일, 최근 업데이트일
- 담당자(내부 3D 아티스트/외주사)
- 최근 배포 여부 (해당 프로젝트가 실제로 테넌트 `store_scenes` 에 연결되어 있는지 여부)

**필터/정렬**

- 상태별 필터 (예: Requested 만 보기, InReview 이상만 보기 등)
- 테넌트/스토어별 필터
- 담당자별 필터
- 최근 생성/업데이트 순서 정렬

**관리 액션**

- 프로젝트 상태 일괄 변경(선택된 항목)
- 프로젝트 상세 화면으로 이동
- 관련 테넌트 상세(Tenants & Users)로 이동

#### 3.5.4 3D Project Detail 뷰

**목적**

- 단일 프로젝트 단위로, 요청 정보부터 최종 배포까지의 흐름을 한 화면에서 관리한다.

**섹션 구성**

1. **기본 정보 패널**
   - 테넌트, 스토어, 프로젝트 이름/설명
   - 상태, 담당자, 생성일/업데이트일

2. **요청 및 참고 자료 패널**
   - 사용자(테넌트)가 공식 웹사이트/문의 폼을 통해 제출한 정보
     - 매장 유형, 면적, 층수, 주요 존(입구/핫존/카운터 등) 설명
     - 특이사항(브랜드 톤, 강조 구역 등)
   - 첨부 파일 목록
     - 도면(PDF/이미지), 매장 사진, 스케치 이미지 등
     - 각 파일의 다운로드 링크 제공 (외부 3D 툴에서 참조용)

3. **3D 작업 결과 / Asset 패널**
   - 이 프로젝트에서 업로드된 glb Asset 목록
     - 파일명, 버전/업데이트 일시
     - 범위(scope): `global` / `tenant` / `store`
   - 기본 메타데이터
     - 예상 용도(베이스 구조, 가구, 존 마커 등), 폴리곤 수 등(선택)

4. **Scene Preview 패널**
   - 현재 프로젝트에 연결된 SceneRecipe 기반 3D 뷰어
   - 관리자용 Preview 이며,
     테넌트 대시보드의 디지털 트윈 뷰어와 동일한 Three.js 기반 렌더링을 사용
   - 카메라 조작, 레이어 On/Off, 간단한 정보 표시(존 이름, 주요 지점 등) 제공

5. **배포 상태 패널**
   - 연결된 `store_scenes` 레코드 여부 및 ID
   - 현행 활성 SceneRecipe 버전 정보 (예: v1, v2 등 내부 버전)
   - 최근 배포 일시, 배포자(관리자 계정)
   - 테넌트 앱에서의 사용 여부 요약 (최근 N일간 3D 뷰 방문 수 등)

**관리 액션**

- 프로젝트 상태 변경 (Requested → InProduction → InReview → Approved → Deployed)
- 담당자 지정/변경
- 첨부 도면/사진 파일 다운로드
- glb/gltf 업로드/수정/삭제
- SceneRecipe 생성/수정/검수
- SceneRecipe 배포 및 롤백

#### 3.5.5 3D Asset Library 뷰

**목적**

- 재사용 가능한 3D Asset을 카탈로그 형태로 관리하고,
  여러 프로젝트/테넌트에서 재활용할 수 있도록 한다.

**노출 데이터**

- Asset ID
- 파일명, 스토리지 경로
- 범위(scope): `global` / `tenant` / `store`
- 연관 테넌트/스토어(tenant scope 이상인 경우)
- Asset 타입 (예: Fixture, Furniture, ZoneMarker 등)
- 폴리곤 수, 텍스처 해상도 등 기본 품질 정보(선택)
- 마지막 업데이트 일시
- 사용 중인 Scene 수 (참조 카운트)

**필터/정렬**

- 범위(scope) 필터
- 테넌트/스토어 필터
- Asset 타입 필터
- 사용 중/미사용 필터
- 최근 업데이트 순 정렬

**관리 액션**

- 신규 Asset 메타데이터 등록(파일 업로드는 Project Detail에서 수행)
- Asset 범위(scope) 조정 (예: store → tenant 범위 확대)
- 사용 중인 Scene 리스트 확인
- Asset 메타데이터 수정/비활성화

#### 3.5.6 데이터 연동 & 저장소 구조 (요약)

**스토리지**

- 3D 전용 버킷 예시: `3d-assets`
  - 공용 Asset: `3d-assets/global/...`
  - 테넌트 전용 Asset: `3d-assets/tenant/{tenant_id}/...`
  - 매장 전용 Asset: `3d-assets/tenant/{tenant_id}/store/{store_id}/...`
- 테넌트 대시보드의 데이터 임포트(`user_data_imports`)에 사용되는
  CSV/Excel 파일 스토리지(`store-data` 등)와는 논리적으로 분리.

**Scene & Graph 연동**

- 완성된 SceneRecipe 는 `store_scenes.recipe_data` 에 저장되며,
  테넌트 앱의 디지털 트윈 뷰어는 이 레시피를 기반으로 씬을 렌더링한다.
- 별도의 동기화 Job(예: `sync_scene_to_graph`) 또는 Scene 저장 시점에,
  SceneRecipe 내 인스턴스 정보(tenant/store, 비즈니스 키, 위치/스케일 등)를 이용해
  `graph_entities` 를 생성/업데이트한다.
- 테넌트가 user data import 로 업로드한 데이터가 만든 Graph 노드와,
  관리자가 3D 씬을 통해 만든 Graph 노드는
  동일한 Ontology 스키마 및 tenant 기준으로 하나의 Graph 에서 통합 관리된다.

---

### 3.6 AI & Simulation Monitoring

#### 3.6.1 목적

- 각 테넌트의 시뮬레이션/AI 기능 사용 현황과 실패율을 모니터링한다.
- 모델/파이프라인 수준에서의 문제를 조기에 감지한다.

#### 3.6.2 화면 구조

1. **AI & Simulation Overview**
   - 라우트: `/admin/ai-sim`
   - 전역 시뮬레이션/AI 실행 현황 요약.

2. **Tenant AI & Simulation Detail**
   - 라우트: `/admin/ai-sim/:tenantId`
   - 테넌트별 시뮬레이션/AI 실행 이력, 실패율, 응답 시간 상세.

#### 3.6.3 AI & Simulation Overview 뷰

**노출 데이터**

- 전체 시뮬레이션 실행 수 (기간별)
- 실패율 (에러 코드/타입 별)
- 평균 응답 시간
- 시뮬레이션 타입별 분포
  - layout / pricing / demand / recommendation 등
- AI 관련 호출 요약
  - `ai_recommendations`, `ai_scene_analysis` 등 카운트

**필터/정렬**

- 기간 필터
- 시뮬레이션 타입 필터
- 에러 코드/타입 필터

**관리 액션**

- 실패율 상위 테넌트 목록 확인
- 특정 테넌트/타입 선택 시 Tenant Detail로 이동
- 에러 코드/타입에 메모/태그 추가

#### 3.6.4 Tenant AI & Simulation Detail 뷰

**노출 데이터**

- 해당 테넌트의 시뮬레이션 실행 이력
  - `scenarios` (타입별: layout/pricing/demand/recommendation)
  - 실행 시각, 입력 파라미터 요약, 상태(성공/실패)
- `simulation_results` 요약
  - 결과 생성 여부, 주요 KPI 변화량 등
- AI 관련 호출
  - `ai_recommendations`, `ai_scene_analysis` 횟수/실패율
- 대표 지표
  - 시뮬레이션 타입별 실패율
  - 글로벌 평균 대비 실패율/응답시간 이상 여부

**관리 액션**

- 실패한 시나리오 상세 로그 열람
- 특정 시나리오 재실행 요청
- 특정 테넌트/기능에 대한 임시 제한 설정 (예: Pricing Simulation 임시 중단)
- 모델 버전/설정 변경 이력 조회 (외부 모델 관리 시스템과 연동 시)

---

### 3.7 System & Error Console

#### 3.7.1 목적

- Edge Functions, API, Frontend 오류 등 시스템 전반의 에러를 중앙에서 모니터링한다.
- 운영자가 문제의 위치(테넌트별/기능별/시간대별)를 빠르게 추적할 수 있도록 한다.

#### 3.7.2 화면 구조

1. **Error & Performance Overview**
   - 라우트: `/admin/system/errors`
   - 에러/HTTP 상태 코드/응답 시간 등 요약.

2. **Edge Functions Status**
   - 라우트: `/admin/system/functions`
   - Edge Functions별 호출 수/실패율/응답 시간 모니터링.

#### 3.7.3 Error & Performance Overview 뷰

**노출 데이터**

- HTTP 에러 로그 요약
  - 4xx/5xx 비율, 엔드포인트별 분포
- 성능 트렌드
  - 평균 응답 시간, p95, 특정 시간대 부하 집중 여부
- 에러 Hotspot
  - 특정 테넌트/엔드포인트/기능 조합에서 에러 집중 여부

**필터/정렬**

- 기간 필터
- HTTP 상태 코드 필터
- 테넌트/엔드포인트 필터

**관리 액션**

- 에러 로그 필터링 (테넌트/함수/HTTP 코드/시간대)
- 에러 세부 정보 확인 (Request/Response 요약, Trace ID, 관련 테넌트)
- 반복되는 에러 유형에 태그/메모 추가

#### 3.7.4 Edge Functions Status 뷰

**노출 데이터**

- Edge Function별 호출 수
- 실패율
- 평균/최대 응답 시간
- 최근 배포/버전 변경 이력(선택)

**관리 액션**

- 특정 함수 에러 로그 상세로 이동
- 특정 함수 호출 제한/비활성화(정책에 따라)
- 배포 이력/릴리즈 노트 링크

---

### 3.8 Admin Tools & Settings

#### 3.8.1 목적

- 운영자가 반복적으로 수행하는 관리 작업을 쉽게 실행할 수 있도록 도구화한다.
- 기능 플래그/실험 설정을 중앙에서 관리한다.

#### 3.8.2 화면 구조

1. **Admin Tools**
   - 라우트: `/admin/tools`
   - 재집계/리빌드/테스트용 유틸리티 모음.

2. **Admin Settings**
   - 라우트: `/admin/settings`
   - Admin 계정/권한, Feature Flag, Impersonation 정책 등의 설정.

#### 3.8.3 Admin Tools 뷰

**노출 기능**

- Feature Flag 상태 요약(읽기 전용 링크)
- 강제 재집계/리빌드
  - 대시보드 KPI 재집계 트리거
  - 특정 기간/스토어에 대한 Graph 재생성(재임포트 기반) 요청
- 테스트 유틸리티
  - 외부 API 연결 상태 점검
  - 특정 테넌트/스토어에 대한 Sanity Check 실행

**관리 액션**

- 재집계 Job 실행
- Graph 재생성 Job 실행
- 테스트 결과 로그 확인

#### 3.8.4 Admin Settings 뷰

**노출 데이터**

- Admin 계정 목록
  - 계정, 역할(Role: `platform_admin`, `ops_admin`, `read_only_admin` 등)
  - 상태(Active/Disabled)
- Feature Flags 설정
  - 플래그 키, 설명
  - 기본값, 테넌트/플랜별 Override 설정
- Impersonation 정책
  - 허용 여부
  - 읽기 전용/쓰기 허용 범위

**관리 액션**

- Admin 계정 생성/수정/비활성화
- 기능 플래그 On/Off (전역/테넌트별)
- Impersonation 권한 범위 설정

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
   - 공식 웹사이트의 3D 디지털 트윈 문의/의뢰 폼(또는 유사한 외부 채널)을 통해
     요청을 제출하면, 백엔드에서 해당 테넌트/스토어 기준의 3D 프로젝트가
     상태 `Requested` 로 생성된다.
   - 이때 업로드된 도면/사진/스케치 파일은 3D 제작 참고 자료로 함께 저장된다.

2. **관리자 플로우**
   - 3D Digital Twin Production → 3D 프로젝트 목록에서 상태 `Requested` 인 항목 확인
   - 해당 프로젝트 상세 화면 진입
     - 매장 도면/사진/필수 정보 검수
     - 누락 정보가 있을 경우 테넌트 측에 보완 요청
     - 필요한 참고 자료(도면/사진)를 로컬로 다운로드
   - 내부/외주 3D 팀이 외부 3D 툴에서 모델링/텍스처링/베이크 작업 수행
     - 완성된 3D 모델을 `glb` 형식으로 추출
   - 관리자 콘솔의 동일 프로젝트 상세 화면에서
      - 완성된 `glb` Asset 을 업로드하고 대상 테넌트/스토어 및 Asset 카테고리만 지정하면,
        정의된 매핑 규칙에 따라 적절한 Ontology 엔티티 타입/Graph 엔티티와 연결되도록 구성한다.
   - SceneRecipe 및 관련 Graph/Ontology 매핑이 정상적으로 동작하는지 확인 후
     상태를 `Approved` 로 변경하고 “배포” 실행
     → 해당 테넌트 `store_scenes` 에 SceneRecipe 등록
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

- 고객 테넌트 데이터는 관리자에 의해 조회 가능하되,
  - Audit 로그에 모든 조회 내역 기록 (누가, 언제, 어떤 테넌트/스토어/데이터를 조회했는지)
- Impersonation 기능 사용 시
  - 실제 조작 권한(쓰기/삭제)은 제한하거나 별도 경고 표시
  - 필요한 경우 “읽기 전용” 미러링 모드 우선 도입

### 5.3 Graph/Ontology 접근 정책

- Graph 구조(`graph_entities`, `graph_relations`)는 기본적으로 관리자 콘솔에서만 조회/관리 가능
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
