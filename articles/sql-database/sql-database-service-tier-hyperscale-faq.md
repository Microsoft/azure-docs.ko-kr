---
title: Azure SQL Database Hyperscale FAQ
description: 일반적으로 하이퍼스케일 데이터베이스라고 하는 하이퍼스케일 서비스 계층의 Azure SQL 데이터베이스에 대해 고객이 하는 일반적인 질문에 대한 답변입니다.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/12/2019
ms.openlocfilehash: 6a25d5197746e04ffa25ee397e6d8451e24ae176
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614996"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database Hyperscale FAQ

이 문서에서는이 FAQ의 나머지 부분에서 Hyperscale 이라고 하는 Azure SQL Database Hyperscale 서비스 계층에서 데이터베이스를 고려 하는 고객을 위한 질문과 대답을 제공 합니다. 이 문서에서는 Hyperscale에서 지 원하는 시나리오와 Hyperscale과 호환 되는 기능에 대해 설명 합니다.

- 이 FAQ는 하이퍼스케일 서비스 계층에 대해 간략히 이해하고 있으며 특정 질문 및 문제에 대한 답변을 원하는 독자를 대상으로 합니다.
- 이 FAQ는 가이드 서적 또는 하이퍼 확장 데이터베이스를 사용 하는 방법에 대 한 질문에 대 한 대답이 아닙니다. 하이퍼 확장에 대 한 소개는 [Azure SQL Database hyperscale](sql-database-service-tier-hyperscale.md) 설명서를 참조 하는 것이 좋습니다.

## <a name="general-questions"></a>일반적인 질문

### <a name="what-is-a-hyperscale-database"></a>하이퍼스케일 데이터베이스란?

하이퍼스케일 데이터베이스 하이퍼스케일 스케일 아웃 스토리지 기술에 기반하는 하이퍼스케일 서비스 계층에 있는 Azure SQL 데이터베이스입니다. 하이퍼스케일 데이터베이스는 최대 100TB의 데이터를 지원하며 높은 처리량과 성능을 제공할 뿐만 아니라 워크로드 요구 사항에 맞게 신속한 확장이 가능합니다. 확장은 연결, 쿼리 처리 등의 응용 프로그램에 투명 하 게 적용 됩니다. 다른 Azure SQL database와 같은 작업을 수행 합니다.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>하이퍼스케일을 지원하는 리소스 종류 및 구매 모델

하이퍼스케일 서비스 계층은 Azure SQL Database의 vCore 기반 구매 모델을 사용하는 단일 데이터베이스에만 사용할 수 있습니다.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>하이퍼스케일 서비스 계층과 범용 및 중요 비즈니스용 서비스 계층의 차이점

VCore 기반 서비스 계층은 다음 표에 설명 된 것 처럼 데이터베이스 가용성 및 저장소 유형, 성능 및 최대 크기에 따라 구분 됩니다.

| | 리소스 유형 | 일반적인 용도 |  하이퍼스케일 | 중요 비즈니스용 |
|:---:|:---:|:---:|:---:|:---:|
| **적합한 대상** |전체|예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다.|대부분의 비즈니스 워크로드. 저장소 크기를 최대 100 TB, 빠른 수직 및 수평 계산 크기 조정, 빠른 데이터베이스 복원으로 자동 크기 조정 합니다.|트랜잭션 속도가 높고 IO 대기 시간이 낮은 OLTP 응용 프로그램 는 동시에 업데이트 된 여러 복제본을 사용 하 여 오류 및 빠른 장애 조치에 가장 높은 복원 력을 제공 합니다|
|  **리소스 종류** ||단일 데이터베이스/탄력적 풀/관리되는 인스턴스 | 단일 데이터베이스 | 단일 데이터베이스/탄력적 풀/관리되는 인스턴스 |
| **컴퓨팅 크기**|단일 데이터베이스/탄력적 풀* | vCore 1~80개 | vCore 1~80개* | vCore 1~80개 |
| |Managed Instance | vCore 8, 16, 24, 32, 40, 64, 80개 | N/A | vCore 8, 16, 24, 32, 40, 64, 80개 |
| **스토리지 유형** | 전체 |프리미엄 원격 스토리지(인스턴스별) | 로컬 SSD 캐시를 사용한 분리형 스토리지(인스턴스별) | 초고속 로컬 SSD 스토리지(인스턴스별) |
| **스토리지 크기** | 단일 데이터베이스/탄력적 풀*| 5GB~4TB | 최대 100TB | 5GB~4TB |
| | Managed Instance  | 32GB~8TB | N/A | 32GB~4TB |
| **IOPS** | 단일 데이터베이스 | vCore당 500 IOPS(최대 7,000 IOPS) | Hyperscale은 여러 수준에서 캐싱을 사용 하는 다중 계층 아키텍처입니다. 유효 IOPS는 워크 로드에 따라 달라 집니다. | 5000 IOPS(최대 200,000 IOPS)|
| | Managed Instance | 파일 크기에 따라 달라 집니다. | N/A | 1375 IOPS/vCore |
|**가용성**|전체|복제본 1 개, 읽기 확장 없음, 로컬 캐시 없음 | 여러 복제본, 최대 4 개의 읽기 확장, 부분 로컬 캐시 | 복제본 3 개, 읽기 확장, 영역 중복 HA, 전체 로컬 저장소 |
|**백업**|전체|RA-GRS, 7-35 일 보존 (기본적으로 7 일)| RA-GRS, 7 일 보존, 일정 시간 지정 시간 복구 (PITR) | RA-GRS, 7-35 일 보존 (기본적으로 7 일) |

\* 탄력적 풀은 Hyperscale 서비스 계층에서 지원 되지 않습니다.

### <a name="who-should-use-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 사용해야 하는 경우

하이퍼 크기 조정 서비스 계층은 대규모 온-프레미스 SQL Server 데이터베이스를 사용 하 고 클라우드로 이동 하거나 이미 Azure SQL Database를 사용 하 고 있는 고객을 위해 응용 프로그램을 현대화 하는 고객을 위한 것입니다. 데이터베이스 증가가 발생할 수 있습니다. 하이퍼스케일은 고성능과 고가용성을 모두 원하는 고객에게도 적합합니다. 하이퍼스케일을 통해 얻을 수 있는 사항:

- 최대 100 TB의 데이터베이스 크기
- 데이터베이스 크기에 관계 없이 빠른 데이터베이스 백업 (백업은 저장소 스냅숏에 기반)
- 데이터베이스 크기에 관계 없이 빠른 데이터베이스 복원 (저장소 스냅숏에서 복원)
- 데이터베이스 크기 및 vCores 수에 관계 없이 로그 처리량 높음
- 읽기 오프 로딩에 사용 되 고 핫으로 처리 되는 읽기 전용 복제본을 하나 이상 사용 하 여 확장을 읽습니다.
- 일정 시간에 컴퓨팅 규모가 신속하게 확장되어, 강력한 기능으로 과도한 워크로드를 수용하고 일정 시간에 규모를 축소할 수 있습니다. 이는 예를 들어 P6과 P11 사이에서 확장 및 축소 하는 것과 유사 하지만 데이터 작업의 크기가 아니라 훨씬 더 빠릅니다.

### <a name="what-regions-currently-support-hyperscale"></a>현재 하이퍼스케일이 지원되는 지역

하이퍼 크기 조정 서비스 계층은 현재 [Azure SQL Database Hyperscale 개요](sql-database-service-tier-hyperscale.md#regions)에 나열 된 지역에서 사용할 수 있습니다.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>논리 서버에 대해 여러 개의 하이퍼스케일 데이터베이스를 만들 수 있나요?

예. 논리 서버별 하이퍼스케일 데이터베이스 수에 대한 제한과 자세한 내용은 [논리 서버의 단일 및 풀링된 데이터베이스에 대한 SQL Database 리소스 제한](sql-database-resource-limits-logical-server.md)을 참조하세요.

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>하이퍼 확장 데이터베이스의 성능 특징

하이퍼 확장 아키텍처는 큰 데이터베이스 크기를 지 원하는 동시에 고성능 및 처리량을 제공 합니다. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 확장성은 어떻게 되나요?

Hyperscale은 워크 로드 요구 사항에 따라 신속한 확장성을 제공 합니다.

- **강화/축소**

  Hyperscale을 사용 하면 CPU 및 메모리와 같은 리소스를 기준으로 기본 계산 크기를 확장 한 다음 일정 한 시간에 확장할 수 있습니다. 스토리지가 공유되기 때문에 강화와 축소는 데이터 작업의 크기가 아닙니다.  
- **규모 감축/확장**

  Hyperscale을 사용 하면 읽기 요청을 처리 하는 데 사용할 수 있는 하나 이상의 추가 계산 복제본을 프로 비전 할 수도 있습니다. 즉, 이러한 추가 계산 복제본을 읽기 전용 복제본으로 사용 하 여 기본 계산에서 읽기 작업을 오프 로드할 수 있습니다. 이러한 복제본은 읽기 전용 외에도 주 복제본에서 장애 조치 (failover)를 수행 하는 경우에도 핫으로 제공 됩니다.

  이러한 추가 계산 복제본 각각의 프로 비전은 일정 한 시간에 수행할 수 있으며 온라인 작업입니다. 연결 문자열에 `ApplicationIntent` 인수를 `ReadOnly`설정 하 여 이러한 추가 읽기 전용 계산 복제본에 연결할 수 있습니다. `ReadOnly` 응용 프로그램 의도를 가진 연결은 추가 읽기 전용 계산 복제본 중 하나로 자동 라우팅됩니다.

## <a name="deep-dive-questions"></a>심층 이해 질문

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>단일 논리 서버에서 하이퍼 크기 조정 및 단일 데이터베이스를 혼합할 수 있나요?

예, 할 수 있습니다.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>하이퍼스케일에서 내 애플리케이션 프로그래밍 모델을 변경해야 하나요?

아니요, 애플리케이션 프로그래밍 모델은 그대로 유지됩니다. 일반적인 방법으로 연결 문자열을 사용 하 고 하이퍼 크기 조정 데이터베이스와 상호 작용 하는 다른 일반적인 방법을 사용 합니다.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>하이퍼 확장 데이터베이스의 기본값은 트랜잭션 격리 수준입니다.

주 복제본에서 기본 트랜잭션 격리 수준은 RCSI (읽기 커밋된 스냅숏 격리)입니다. 읽기 확장 보조 복제본에서 기본 격리 수준은 Snapshot입니다.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>온-프레미스 또는 IaaS SQL Server 라이선스를 Hyperscale으로 가져올 수 있나요?

예, [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 하이퍼스케일에 사용할 수 있습니다. 모든 SQL Server Standard 코어를 하이퍼스케일 vCore 하나에 매핑할 수 있습니다. 모든 SQL Server Enterprise 코어를 하이퍼스케일 vCore 4개에 매핑할 수 있습니다. 보조 복제본에 대해 SQL 라이선스가 필요하지 않습니다. 수평 확장 (보조) 복제본을 읽기 위해 Azure 하이브리드 혜택 가격이 자동으로 적용 됩니다.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>어떤 종류의 작업을 위해 설계 된 하이퍼 확장

Hyperscale은 모든 SQL Server 작업을 지원 하지만 주로 OLTP에 최적화 되어 있습니다. 하이브리드 (HTAP) 및 분석 (데이터 마트) 워크 로드도 가져올 수 있습니다.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Azure SQL Data Warehouse 및 Azure SQL Database Hyperscale 중에서 선택 하는 방법

현재 데이터 웨어하우스로 SQL Server를 사용 하 여 대화형 분석 쿼리를 실행 하 고 있는 경우 더 낮은 비용으로 중소 규모의 데이터 웨어하우스 (예: 몇 TB에서 100 TB)를 호스팅하고 SQL Server 데이터 전쟁을 마이그레이션할 수 있기 때문에 Hyperscale은 좋은 옵션입니다. T-sql 코드를 최소한으로 변경 하 여 Hyperscale 확장을 위한 작업입니다.

복잡 한 쿼리를 사용 하 여 대규모 데이터 분석을 실행 하 고 있거나 100 m b/초 보다 높은 수집 속도를 유지 하거나 PDW (병렬 데이터 웨어하우스), Teradata 또는 다른 대규모 병렬 처리 (MPP) 데이터 웨어하우스를 사용 하는 경우에는 SQL Data Warehouse 하는 것이 가장 적합할 수 있습니다.
  
## <a name="hyperscale-compute-questions"></a>분산 계산 질문

### <a name="can-i-pause-my-compute-at-any-time"></a>컴퓨팅을 아무 때나 일시 중지할 수 있나요?

그러나 현재는 불가능 한 시간 동안 계산 및 복제본 수를 축소 하 여 비용을 줄일 수 있습니다.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>메모리를 많이 사용 하는 워크 로드의 추가 RAM을 사용 하 여 계산 복제본을 프로 비전 할 수 있음

아닙니다. RAM을 더 확보하려면 더 큰 컴퓨팅 크기로 업그레이드해야 합니다. 자세한 내용은 [하이퍼스케일 스토리지 및 컴퓨팅 크기](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)를 참조하세요.

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>다른 크기의 여러 계산 복제본을 프로 비전 할 수 있습니다.

아닙니다.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>지원 되는 읽기 확장 복제본 수

Hyperscale 데이터베이스는 기본적으로 하나의 읽기 스케일 아웃 복제본 (주 복제본을 포함 하 여 두 개의 복제본)으로 만들어집니다. [Azure Portal](https://portal.azure.com) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 사용 하 여 0과 4 사이의 읽기 전용 복제본 수를 조정할 수 있습니다.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>고가용성을 위해 추가 계산 복제본을 프로 비전 해야 합니다.

Hyperscale 데이터베이스에서 데이터 복원 력이 저장소 수준에서 제공 됩니다. 복원 력을 제공 하는 복제본이 하나만 필요 합니다. 컴퓨팅 복제본이 다운되면 데이터가 손실되지 않고 새로운 복제본이 자동으로 생성됩니다.

단, 복제본이 하나만 있으면 장애 조치(failover) 후 새 복제본에 로컬 캐시를 구축하는 데 약간의 시간이 걸릴 수 있습니다. 캐시 다시 작성 단계 중에 데이터베이스는 페이지 서버에서 직접 데이터를 인출 하므로 저장소 대기 시간이 더 높고 쿼리 성능이 저하 됩니다.

장애 조치 (failover) 영향을 최소화 하면서 고가용성이 필요한 중요 업무용 앱의 경우 기본 계산 복제본을 포함 하 여 2 개 이상의 계산 복제본을 프로 비전 해야 합니다. 이 값이 기본 구성입니다. 이러한 방식으로 장애 조치 (failover) 대상으로 사용 되는 상시 대기 복제본이 있습니다.

## <a name="data-size-and-storage-questions"></a>데이터 크기 및 저장소 질문

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Hyperscale에서 지원 되는 최대 데이터베이스 크기

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>하이퍼스케일의 트랜잭션 로그 크기는 얼마인가요?

하이퍼스케일의 트랜잭션 로그는 사실상 무한합니다. 로그 처리량이 많은 시스템에서 로그 공간 부족을 걱정할 필요가 없습니다. 그러나 작업 부하를 지속적으로 기록 하기 위해 로그 생성 비율이 제한 될 수 있습니다. 지속 되는 최대 로그 생성 비율은 100 m b/초입니다.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>내 데이터베이스가 증가 함에 따라 내 `tempdb` 크기 조정

`tempdb` 데이터베이스는 로컬 SSD 저장소에 있으며 프로 비전 하는 계산 크기에 비례하여 크기가 지정 됩니다. `tempdb`은 최대 성능 이점을 제공 하도록 최적화 되었습니다. `tempdb` 크기는 구성할 수 없으며 사용자를 위해 관리 됩니다.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>내 데이터베이스 크기가 자동으로 증가 하거나 데이터 파일의 크기를 관리 해야 하나요?

데이터베이스 크기는 데이터를 추가로 삽입/수집하면 자동으로 커집니다.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Hyperscale에서 지원 하거나 시작 하는 가장 작은 데이터베이스 크기는

40 GB 10 GB의 시작 크기를 사용 하 여 Hyperscale 데이터베이스를 만듭니다. 그런 다음 크기가 40 GB에 도달할 때까지 10 분 마다 10gb의 증가를 시작 합니다. 더 많은 IOPS 및 더 높은 i/o 병렬 처리를 제공 하기 위해 이러한 10gb chucks는 각각 다른 페이지 서버에 할당 됩니다. 이러한 최적화로 인해 40 GB 보다 작은 초기 데이터베이스 크기를 선택 하는 경우에도 데이터베이스는 최소 40 GB까지 자동으로 확장 됩니다.

### <a name="in-what-increments-does-my-database-size-grow"></a>데이터베이스가 증가하는 증분의 크기는 얼마인가요?

각 데이터 파일은 10gb 씩 증가 합니다. 여러 데이터 파일이 동시에 증가할 수 있습니다.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>은 (는) 로컬 또는 원격으로 분산 된 확장의 저장소입니다.

하이퍼스케일에서 데이터 파일은 Azure 표준 스토리지에 저장됩니다. 데이터는 계산 복제본에 가까운 페이지 서버에서 로컬 SSD 저장소에 완전히 캐시 됩니다. 또한 계산 복제본은 원격 페이지 서버에서 데이터를 가져오는 빈도를 줄이기 위해 로컬 SSD 및 메모리에 데이터 캐시를 포함 합니다.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>하이퍼스케일로 파일이나 파일 그룹을 관리하거나 정의할 수 있나요?

아닙니다. 데이터 파일은 자동으로 추가 됩니다. 추가 파일 그룹을 만드는 일반적인 이유는 Hyperscale storage 아키텍처에서 적용 되지 않습니다.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>데이터베이스의 데이터 증가에 대해 하드 캡을 프로비전할 수 있나요?

아닙니다.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>데이터 파일이 Hyperscale으로 배치 되는 방법

데이터 파일은 페이지당 하나의 페이지 서버를 사용 하 여 페이지 서버에 의해 제어 됩니다. 데이터 크기가 증가 하면 데이터 파일 및 관련 페이지 서버가 추가 됩니다.

### <a name="is-database-shrink-supported"></a>데이터베이스 축소가 지원되나요?

아닙니다.

### <a name="is-data-compression-supported"></a>데이터 압축이 지원 됨

예 (행, 페이지 및 columnstore 압축 포함)

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>거대한 테이블이 있는 경우 테이블 데이터가 여러 데이터 파일에 분산되나요?

예. 주어진 테이블과 연결된 데이터 페이지는 모두 동일한 파일 그룹에 속하는 여러 데이터 파일에 분산될 수 있습니다. SQL Server는 [비례 채우기 전략](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) 을 사용 하 여 데이터 파일에 데이터를 배포 합니다.

## <a name="data-migration-questions"></a>데이터 마이그레이션 관련 질문

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>기존 Azure SQL 데이터베이스를 하이퍼스케일 서비스 계층으로 이동할 수 있나요?

예. 기존 Azure SQL 데이터베이스를 하이퍼스케일로 이동할 수 있습니다. 이는 단방향 마이그레이션입니다. 하이퍼스케일에서 또 다른 서비스 계층으로 데이터베이스를 이동할 수는 없습니다. POCs (개념 증명)의 경우 데이터베이스의 복사본을 만들고 복사본을 Hyperscale으로 마이그레이션하는 것이 좋습니다.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Hyperscale 데이터베이스를 다른 서비스 계층으로 이동할 수 있나요?

아닙니다. 이번에는 Hyperscale 데이터베이스를 다른 서비스 계층으로 이동할 수 없습니다.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층으로 마이그레이션한 후에 손실되는 기능이 있나요?

예. 일부 Azure SQL Database 기능은 하이퍼 규모에서 아직 지원 되지 않습니다 (장기 백업 보존을 포함 하지만이에 제한 되지 않음). 하이퍼스케일로 데이터베이스를 마이그레이션한 후에는 해당 기능이 작동을 중지합니다.  이러한 제한 사항은 일시적일 것으로 간주 됩니다.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>온-프레미스 SQL Server 데이터베이스 또는 클라우드 가상 컴퓨터의 SQL Server 데이터베이스를 Hyperscale으로 이동할 수 있나요?

예. 모든 기존 마이그레이션 기술을 사용 하 여 트랜잭션 복제 및 기타 데이터 이동 기술 (대량 복사, Azure Data Factory, Azure Databricks, SSIS)을 비롯 한 하이퍼 규모로 마이그레이션할 수 있습니다. 여러 마이그레이션 시나리오를 지 원하는 [Azure Database Migration Service](../dms/dms-overview.md)를 참조 하세요.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>온-프레미스 또는 가상 머신 환경에서 Hyperscale으로 마이그레이션하는 동안 가동 중지 시간이 발생 하 고이를 최소화 하려면 어떻게 해야 하나요?

Hyperscale으로의 마이그레이션 가동 중지 시간은 데이터베이스를 다른 Azure SQL Database 서비스 계층으로 마이그레이션할 때 가동 중지 시간과 동일 합니다. [트랜잭션 복제](replication-to-sql-database.md#data-migration-scenario
)를 사용하면 크기가 최대 몇 TB에 이르는 데이터베이스의 마이그레이션 가동 중지 시간을 최소화할 수 있습니다. 매우 큰 데이터베이스 (10 + TB)의 경우 ADF, Spark 또는 기타 데이터 이동 기술을 사용 하 여 데이터를 마이그레이션하는 것을 고려할 수 있습니다.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>데이터의 크기를 Hyperscale에 가져오는 데 걸리는 시간

Hyperscale은 새로운/변경 된 데이터의 100 m b/초를 사용할 수 있지만, 데이터를 Azure SQL 데이터베이스로 이동 하는 데 필요한 시간은 사용 가능한 네트워크 처리량, 원본 읽기 속도 및 대상 데이터베이스 서비스 수준 목표의 영향도 받습니다.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Blob 저장소에서 데이터를 읽고 빠른 로드를 수행할 수 있음 (예: Polybase SQL Data Warehouse)

클라이언트 응용 프로그램이 Azure Storage에서 데이터를 읽고 데이터 로드를 하이퍼 규모의 데이터베이스에 로드할 수 있습니다 (다른 Azure SQL database와 마찬가지로). Polybase는 현재 Azure SQL Database에서 지원 되지 않습니다. 빠른 로드를 제공 하는 대신 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)를 사용 하거나 [SQL 용 spark 커넥터](sql-database-spark-connector.md)를 사용 하 여 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) 에서 spark 작업을 사용할 수 있습니다. SQL에 대한 Spark 커넥터는 대량 삽입을 지원합니다.

BULK INSERT 또는 OPENROWSET: [Azure Blob Storage 데이터에 대 한 대량 액세스 예제](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)를 사용 하 여 Azure Blob 저장소에서 데이터를 대량으로 읽을 수도 있습니다.

하이퍼스케일에서는 단순 복구 또는 대량 로깅 모델이 지원되지 않습니다. 고가용성 및 지정 시간 복구를 제공 하려면 전체 복구 모델이 필요 합니다. 그러나 Hyperscale 로그 아키텍처는 다른 Azure SQL Database 서비스 계층에 비해 더 나은 데이터 수집 비율을 제공 합니다.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Hyperscale을 사용 하면 대량의 데이터를 병렬 수집 하는 여러 노드를 프로 비전 할 수 있습니다.

아닙니다. Hyperscale은 SMP (대칭적 다중 처리) 아키텍처 이며 MPP (대규모 parallel processing) 또는 다중 마스터 아키텍처가 아닙니다. 여러 개의 복제본을 만들어서 읽기 전용 워크로드 규모 확장할 수만 있습니다.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Hyperscale으로의 마이그레이션에 지원 되는 가장 오래 된 SQL Server 버전은 무엇 인가요?

SQL Server 2005입니다. 자세한 내용은 [단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)을 참조하세요. 호환성 문제는 [데이터베이스 마이그레이션 호환성 문제 해결](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)을 참조하세요.

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Hyperscale은 Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 및 기타 데이터베이스 플랫폼과 같은 다른 데이터 원본에서의 마이그레이션을 지원 합니다.

예. [Azure Database Migration Service](../dms/dms-overview.md) 는 여러 마이그레이션 시나리오를 지원 합니다.

## <a name="business-continuity-and-disaster-recovery-questions"></a>비즈니스 연속성 및 재해 복구 질문

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>하이퍼 확장 데이터베이스에 제공 되는 Sla

[Azure SQL Database에 대 한 SLA를](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)참조 하세요. 보조 계산 복제본이 두 개 이상인 데이터베이스의 경우 추가 보조 계산 복제본은 가용성을 최대 99.99%까지 늘립니다.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database 서비스에서 데이터베이스 백업이 관리되나요?

예.

### <a name="how-often-are-the-database-backups-taken"></a>데이터베이스 백업이 얼마나 자주 수행되나요?

Hyperscale 데이터베이스에 대 한 기존의 전체, 차등 및 로그 백업이 없습니다. 대신 데이터 파일의 일반 저장소 스냅숏이 있습니다. 생성 되는 로그는 구성 된 보존 기간 동안 그대로 유지 되므로 보존 기간 내의 특정 시점으로 복원할 수 있습니다.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Hyperscale은 지정 시간 복원을 지원 합니다.

예.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Hyperscale의 데이터베이스 복원에 대 한 RPO (복구 지점 목표)/RTO (복구 시간 목표)는 무엇 인가요?

RPO는 0 분입니다. RTO 목표는 데이터베이스 크기에 관계 없이 10 분 미만입니다. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>데이터베이스 백업이 주 복제본 또는 보조 복제본의 계산 성능에 영향을 미칩니까?

아닙니다. 백업은 저장소 하위 시스템에 의해 관리 되며 저장소 스냅숏을 활용 합니다. 사용자 작업에는 영향을 주지 않습니다.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>하이퍼 확장 데이터베이스를 사용 하 여 지역 복원을 수행할 수 있습니다.

예.  지역 복원은 완전히 지원 됩니다. 지정 시간 복원과 달리 지역 복원에는 장기 실행 데이터 크기 작업이 필요할 수 있습니다.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Hyperscale 데이터베이스를 사용 하 여 지역에서 복제를 설정할 수 있나요?

현재는 사용할 수 없습니다.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>하이퍼 규모의 데이터베이스 백업을 사용 하 여 온-프레미스 서버 또는 VM의 SQL Server에 복원할 수 있나요?

아닙니다. 하이퍼 규모의 데이터베이스에 대 한 저장소 형식은 SQL Server의 릴리스 버전과 다르며 백업을 제어 하거나 액세스할 수 없습니다. 하이퍼 규모의 데이터베이스에서 데이터를 가져오기 위해 데이터 이동 기술 (예: Azure Data Factory, Azure Databricks, SSIS 등)을 사용 하 여 데이터를 추출할 수 있습니다.

## <a name="cross-feature-questions"></a>기능 간 질문

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층으로 마이그레이션한 후에 손실되는 기능이 있나요?

예. 일부 Azure SQL Database 기능은 하이퍼 규모에서 지원 되지 않습니다 (장기 백업 보존을 포함 하지만이에 제한 되지 않음). 하이퍼스케일로 데이터베이스를 마이그레이션한 후에는 해당 기능이 작동을 중지합니다.

### <a name="will-polybase-work-with-hyperscale"></a>Polybase가 Hyperscale에서 작동 합니까?

아닙니다. Polybase는 Azure SQL Database에서 지원 되지 않습니다.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Hyperscale은 R 및 Python을 지원 합니다.

현재는 사용할 수 없습니다.

### <a name="are-compute-nodes-containerized"></a>계산 노드 컨테이너 화 된

아닙니다. Hyperscale 프로세스는 컨테이너에 없는 Vm ( [Service Fabric](https://azure.microsoft.com/services/service-fabric/) 노드)에서 실행 됩니다.

## <a name="performance-questions"></a>성능 질문

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>하이퍼 확장 데이터베이스에 푸시할 수 있는 쓰기 처리량 크기

트랜잭션 로그 처리량 상한은 모든 하이퍼 크기 조정 계산 크기에 대해 100 m b/초로 설정 됩니다. 이 속도를 달성할 수 있는 기능은 작업 유형, 클라이언트 구성 등을 비롯 하 여 여러 요인에 따라 달라 지 며 기본 계산 복제본에 충분 한 계산 용량이 있으므로이 속도로 로그를 생성할 수 있습니다.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>가장 큰 계산에서 얻을 수 있는 IOPS 수

IOPS 및 IO 대기 시간은 워크 로드 패턴에 따라 달라 집니다. 액세스 되는 데이터를 계산 복제본에 캐시 하면 로컬 SSD와 마찬가지로 유사한 IO 성능이 표시 됩니다.

### <a name="does-my-throughput-get-affected-by-backups"></a>처리량이 백업의 영향을 받나요?

아닙니다. 계산은 저장소 계층에서 분리 됩니다. 이렇게 하면 백업의 성능 영향을 제거 합니다.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>추가 계산 복제본을 프로 비전 하면 처리량이 영향을 받습니다.

저장소가 공유 되 고 주 및 보조 계산 복제본 간에 직접 물리적 복제가 발생 하지 않으므로 보조 복제본을 추가 하 여 주 복제본에 대 한 처리량이 직접적인 영향을 받지 않습니다. 하지만 보조 복제본에 대 한 읽기 성능이 저하 되는 것을 방지 하기 위해 보조 복제본 및 페이지 서버에서 로그를 적용 하 여 보조 복제본에 대 한 로그를 적용 하는 것이 좋습니다.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>어떻게 할까요? 하이퍼 규모 데이터베이스의 성능 문제를 진단 하 고 해결 합니다.

특히 저장소 성능에 속하지 않는 대부분의 성능 문제, 일반적인 SQL Server 진단 및 문제 해결 단계가 적용 됩니다. 하이퍼 확장 별 저장소 진단의 경우 [SQL hyperscale 성능 문제 해결 진단](sql-database-hyperscale-performance-diagnostics.md)을 참조 하세요.

## <a name="scalability-questions"></a>확장성 질문

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>계산 복제본을 확장 및 축소 하는 데 소요 되는 시간

계산 크기를 늘리거나 줄일 때 데이터 크기에 관계 없이 5-10 분이 소요 됩니다.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>강화 및 축소 작업을 진행하는 동안 데이터베이스는 오프라인 상태인가요?

아닙니다. 강화 및 축소는 온라인 상태가 됩니다.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>크기 조정 작업을 진행하는 동안 연결이 끊어질 수 있나요?

크기를 확장 하거나 축소 하면 크기 조정 작업의 끝에 장애 조치가 발생할 때 기존 연결이 삭제 됩니다. 보조 복제본을 추가 해도 연결 삭제는 발생 하지 않습니다.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>계산 복제본 자동 또는 최종 사용자 트리거 작업의 확장 및 축소 여부

최종 사용자가 트리거합니다. 자동이 아닙니다.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>계산이 확장 됨에 따라 `tempdb` 데이터베이스의 크기도 증가 합니다.

예. `tempdb` 데이터베이스는 계산이 증가 함에 따라 자동으로 확장 됩니다.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>다중 마스터 시스템과 같은 여러 기본 계산 복제본을 프로 비전 할 수 있습니다. 여러 기본 계산 헤드는 더 높은 수준의 동시성을 제공할 수 있습니다.

아닙니다. 기본 계산 복제본만 읽기/쓰기 요청을 허용 합니다. 보조 계산 복제본은 읽기 전용 요청만 허용 합니다.

## <a name="read-scale-out-questions"></a>수평 확장 질문 읽기

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>프로 비전 할 수 있는 보조 계산 복제본의 수

기본적으로 Hyperscale 데이터베이스에 대해 하나의 보조 복제본을 만듭니다. 복제본 수를 조정 하려는 경우 [Azure Portal](https://portal.azure.com) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)를 사용 하 여 수행할 수 있습니다.

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>이러한 보조 계산 복제본에 연결 어떻게 할까요?

연결 문자열에 `ApplicationIntent` 인수를 `ReadOnly`설정 하 여 이러한 추가 읽기 전용 계산 복제본에 연결할 수 있습니다. `ReadOnly` 표시 된 모든 연결은 추가 읽기 전용 계산 복제본 중 하나로 자동 라우팅됩니다.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>SSMS 또는 다른 클라이언트 도구를 사용 하 여 보조 계산 복제본에 성공적으로 연결 된 경우 유효성 검사를 어떻게 할까요? 합니다.

다음 T-sql 쿼리를 실행할 수 있습니다. `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
읽기 전용 보조 복제본에 연결 된 경우에는 결과가 `READ_ONLY` 되 고 주 복제본에 연결 된 경우에는 `READ_WRITE`. 데이터베이스 컨텍스트는 `master` 데이터베이스가 아닌 Hyperscale 데이터베이스의 이름으로 설정 되어야 합니다.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>읽기 확장 복제본의 전용 엔드포인트을 만들 수 있습니다.

아닙니다. `ApplicationIntent=ReadOnly`를 지정 하 여 스케일 아웃 복제본 읽기에만 연결할 수 있습니다.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>시스템에서 읽기 워크로드에 대한 지능적인 부하 분산이 수행되나요?

아닙니다. 읽기 전용 의도를 사용 하는 새 연결은 임의의 읽기 스케일 아웃 복제본으로 리디렉션됩니다.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>주 복제본과 독립적으로 보조 계산 복제본을 확장/축소할 수 있습니다.

아닙니다. 보조 계산 복제본은 고가용성 장애 조치 (failover) 대상으로도 사용 되므로 장애 조치 (failover) 후 예상 되는 성능을 제공 하기 위해 주 복제본과 동일한 구성을 사용 해야 합니다.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>기본 계산 및 추가 보조 계산 복제본에 대해 다양 한 `tempdb` 크기 조정 수행

아닙니다. `tempdb` 데이터베이스는 계산 크기 프로 비전에 따라 구성 되 고, 보조 계산 복제본은 기본 계산과 크기가 동일 합니다.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>보조 계산 복제본에 인덱스 및 뷰를 추가할 수 있습니다.

아닙니다. Hyperscale 데이터베이스는 공유 저장소를 가집니다. 즉, 모든 계산 복제본이 동일한 테이블, 인덱스 및 보기를 볼 수 있습니다. 보조 복제본에 대 한 읽기에 최적화 된 추가 인덱스를 원하는 경우 주 데이터베이스에 추가 해야 합니다.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>기본 및 보조 계산 복제본 사이에 있는 지연 시간

주 복제본에서 트랜잭션이 커밋된 시간부터 데이터 대기 시간은 현재 로그 생성 률에 따라 달라 집니다. 일반적인 데이터 대기 시간은 낮은 밀리초입니다.

## <a name="next-steps"></a>다음 단계

하이퍼 크기 조정 서비스 계층에 대 한 자세한 내용은 [Hyperscale service 계층](sql-database-service-tier-hyperscale.md)을 참조 하세요.
