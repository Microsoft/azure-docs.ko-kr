---
title: Azure SQL Database 하이퍼스케일 FAQ | Microsoft Docs
description: 일반적으로 하이퍼스케일 데이터베이스라고 하는 하이퍼스케일 서비스 계층의 Azure SQL 데이터베이스에 대해 고객이 하는 일반적인 질문에 대한 답변입니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: 7746d7256add185be0c67123edf63ea09b6b05a4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111835"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Azure SQL 하이퍼스케일 데이터베이스에 대한 FAQ

이 문서에서는 일반적으로 하이퍼스케일 데이터베이스(현재 공개 미리 보기로 제공됨)라고 하는 Azure SQL Database 하이퍼스케일 서비스 계층의 데이터베이스를 고려하는 고객을 위해 FAQ에 대한 답변을 제공합니다. 이 문서에서는 하이퍼스케일 지원 및 교차 기능 서비스가 SQL Database 하이퍼스케일과 일반적으로 호환되는 시나리오를 설명합니다.

- 이 FAQ는 하이퍼스케일 서비스 계층에 대해 간략히 이해하고 있으며 특정 질문 및 문제에 대한 답변을 원하는 독자를 대상으로 합니다.
- 이 FAQ는 가이드 북이 아니며, SQL Database 하이퍼스케일 데이터베이스를 사용하는 방법에 대한 질문에 답변을 제공하도록 준비된 문서가 아닙니다. 이를 위해서는 [Azure SQL Database 하이퍼스케일](sql-database-service-tier-hyperscale.md) 설명서를 참조하는 것이 좋습니다.

## <a name="general-questions"></a>일반적인 질문

### <a name="what-is-a-hyperscale-database"></a>하이퍼스케일 데이터베이스란?

하이퍼스케일 데이터베이스 하이퍼스케일 스케일 아웃 저장소 기술에 기반하는 하이퍼스케일 서비스 계층에 있는 Azure SQL 데이터베이스입니다. 하이퍼스케일 데이터베이스는 최대 100TB의 데이터를 지원하며 높은 처리량과 성능을 제공할 뿐만 아니라 워크로드 요구 사항에 맞게 신속한 확장이 가능합니다. 크기 조정은 애플리케이션에 대해 투명하고 연결, 쿼리 처리 등은 다른 SQL 데이터베이스와 동일하게 작동합니다.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>하이퍼스케일을 지원하는 리소스 종류 및 구매 모델

하이퍼스케일 서비스 계층은 Azure SQL Database의 vCore 기반 구매 모델을 사용하는 단일 데이터베이스에만 사용할 수 있습니다.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>하이퍼스케일 서비스 계층과 범용 및 중요 비즈니스용 서비스 계층의 차이점

vCore 기반 서비스 계층은 주로 가용성, 저장소 형식 및 IOPs를 기반으로 차별화됩니다.

- 범용 서비스 계층은 대부분의 비즈니스 워크로드에 적합하며 IO 대기 시간 또는 장애 조치(failover) 시간이 최우선 순위가 아니고 균형 잡힌 계산 및 스토리지 옵션 세트를 제공합니다.
- 하이퍼스케일 서비스 계층은 매우 큰 데이터베이스 워크로드에 최적화되어 있습니다.
- 중요 비즈니스용 서비스 계층은 IO 대기 시간이 최우선인 비즈니스 워크로드에 적합합니다.

| | 리소스 종류 | 범용 |  하이퍼스케일 | 중요 비즈니스용 |
|:---|:---:|:---:|:---:|:---:|
| **적합한 대상** |모두|  대부분의 비즈니스 워크로드. 예산 중심의 균형 잡힌 계산 및 스토리지 옵션을 제공합니다. | 대용량 데이터 요구 사항 및 유동적인 스토리지 크기 자동 조정 및 계산 규모 조정 기능이 있는 데이터 애플리케이션. | 트랜잭션 속도가 높고 지연 시간이 적은 OLTP 애플리케이션. 다수의 격리된 복제본을 사용하여 장애에 대해 최고의 복원력을 제공합니다.|
|  **리소스 종류** ||단일 데이터베이스/탄력적 풀/관리되는 인스턴스 | 단일 데이터베이스 | 단일 데이터베이스/탄력적 풀/관리되는 인스턴스 |
| **계산 크기**|단일 데이터베이스/탄력적 풀* | vCore 1~80개 | vCore 1~80개* | vCore 1~80개 |
| |Managed Instance | vCore 8, 16, 24, 32, 40, 64, 80개 | N/A | vCore 8, 16, 24, 32, 40, 64, 80개 |
| **저장소 유형** | 모두 |프리미엄 원격 저장소(인스턴스별) | 로컬 SSD 캐시를 사용한 분리형 저장소(인스턴스별) | 초고속 로컬 SSD 저장소(인스턴스별) |
| **저장소 크기** | 단일 데이터베이스/탄력적 풀 | 5GB~4TB | 최대 100TB | 5GB~4TB |
| | Managed Instance  | 32GB~8TB | N/A | 32GB~4TB |
| **IO 처리량** | 단일 데이터베이스** | vCore당 500 IOPS(최대 7,000 IOPS) | 아직 알 수 없음 | 5000 IOPS(최대 200,000 IOPS)|
| | Managed Instance | 파일의 크기에 따라 다름 | N/A | Managed Instance: 파일의 크기에 따라 다름|
|**가용성**|모두|복제본 1개, 읽기 확장 없음, 로컬 캐시 없음 | 복제본 여러 개, 읽기 확장 최대 15, 부분 로컬 캐시 | 복제본 3개, 읽기 확장 1개, 영역 중복 HA, 전체 로컬 캐시 |
|**백업**|모두|RA-GRS, 7-35일(기본값: 7일)| RA-GRS, 7-35일(기본값: 7일), 일정 시간 PITR(point-in-time recovery) | RA-GRS, 7-35일(기본값: 7일) |

\* 하이퍼스케일 서비스 계층에는 탄력적 풀이 지원되지 않습니다.

### <a name="who-should-use-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층을 사용해야 하는 경우

하이퍼스케일 서비스 계층은 주로 대규모 온-프레미스 SQL Server 데이터베이스를 보유하고 있으며 클라우드로 전환하여 애플리케이션을 최신화하려는 고객 또는 Azure SQL Database를 이미 사용하고 있으며 데이터 증가에 대한 잠재력을 상당히 확대시키려는 고객에게 적합합니다. 하이퍼스케일은 고성능과 고가용성을 모두 원하는 고객에게도 적합합니다. 하이퍼스케일을 통해 얻을 수 있는 사항:

- 최대 100TB의 데이터베이스 크기 지원
- 데이터베이스 크기에 관계없이 빠른 데이터베이스 백업(백업은 파일 스냅숏을 기반으로 함)
- 데이터베이스 크기에 관계없이 빠른 데이터베이스 복원(파일 스냅숏을 통해 복원이 수행됨)
- 데이터베이스 크기에 관계없이 로그 처리량이 높아서 트랜잭션 커밋 시간이 빨라짐
- 읽기 규모가 하나 이상의 읽기 전용 노드로 확장되어 읽기 워크로드가 감소되고 상시 대기가 가능합니다.
- 일정 시간에 계산 규모가 신속하게 확장되어, 강력한 기능으로 과도한 워크로드를 수용하고 일정 시간에 규모를 축소할 수 있습니다. 이것은 P6과 P11 사이의 강화 및 축소와 유사하지만 데이터 작업의 규모가 아니므로 훨씬 빠릅니다.

### <a name="what-regions-currently-support-hyperscale"></a>현재 하이퍼스케일이 지원되는 지역

현재 하이퍼스케일은 다음 지역의 단일 데이터베이스에 사용할 수 있습니다.  서 부 US1, 미국 서 부 2, 동부 US1, 미국 중부, 유럽 서 부, 북유럽, 동남 아시아, 일본 동부, 한국 중부, 오스트레일리아 남동부 및 오스트레일리아 동부입니다.

### <a name="can-i-create-multiple-hyperscale-databases-per-sql-database-server"></a>SQL Database 서버에 대해 여러 개의 하이퍼스케일 데이터베이스를 만들 수 있나요?

예. SQL Database 서버별 하이퍼스케일 데이터베이스 수에 대한 제한과 자세한 내용은 [SQL Database 서버의 단일 및 풀링된 데이터베이스에 대한 SQL Database 리소스 제한](sql-database-resource-limits-database-server.md)을 참조하세요.

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 성능 특성은 무엇인가요?

SQL Database 하이퍼스케일 아키텍처는 높은 성능과 처리량을 제공하면서 대용량 데이터베이스를 지원합니다. 공개 미리 보기 중에는 정확한 성능 프로필과 특징이 제공되지 않습니다.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>하이퍼스케일 데이터베이스의 확장성은 어떻게 되나요?

SQL Database 하이퍼스케일은 워크로드 요구 사항에 따라 신속한 확장성을 제공합니다.

- **강화/축소**

  하이퍼스케일을 사용하면 CPU, 메모리 등의 리소스 측면에서 기본 계산 규모를 강화한 다음, 일정 시간 내에 축소할 수 있습니다. 저장소가 공유되기 때문에 강화와 축소는 데이터 작업의 크기가 아닙니다.  
- **규모 감축/확장**

  하이퍼스케일을 사용하면 읽기 요청을 처리하는 데 사용할 수 있는 추가 계산 노드를 하나 이상 프로비전할 수 있습니다. 즉, 추가 계산 노드를 읽기 전용 노드로 사용하여 기본 계산의 읽기 워크로드를 오프로드할 수 있습니다. 읽기 전용 외에도 이 노드는 기본 노드에서 장애 조치(failover)가 발생할 경우 상시 대기 노드 역할을 합니다.

  이러한 추가 계산 노드 각각에 대한 프로비전을 일정 시간 내에 온라인으로 수행할 수 있습니다. 연결 문자열의 `ApplicationIntent` 인수를 `read_only`로 설정하면 이러한 추가 읽기 전용 계산 노드에 연결할 수 있습니다. `read-only`로 표시된 모든 연결은 추가 읽기 전용 계산 노드 중 하나에 자동으로 라우팅됩니다.

## <a name="deep-dive-questions"></a>심층적인 질문

### <a name="can-i-mix-hyperscale-and-single-databases-a-my-sql-database-server"></a>SQL Database 서버에 하이퍼스케일과 단일 데이터베이스를 혼합할 수 있나요?

 예, 할 수 있습니다.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>하이퍼스케일에서 내 애플리케이션 프로그래밍 모델을 변경해야 하나요?

아니요, 애플리케이션 프로그래밍 모델은 그대로 유지됩니다. 평소대로 연결 문자열을 사용하고 다른 일반 모드가 Azure SQL 데이터베이스와 상호 작용합니다.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>SQL Database 하이퍼스케일 데이터베이스에 어떤 트랜잭션 격리 수준이 기본값이 되나요?

주 노드의 트랜잭션 격리 수준은 RCSI(Read Committed 스냅숏 격리)입니다. 읽기 확장 보조 노드의 격리 수준은 스냅숏입니다.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>SQL Database 하이퍼스케일에 온-프레미스 또는 IaaS SQL Server 라이선스를 가져올 수 있나요?

예, [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 하이퍼스케일에 사용할 수 있습니다. 모든 SQL Server Standard 코어를 하이퍼스케일 vCore 하나에 매핑할 수 있습니다. 모든 SQL Server Enterprise 코어를 하이퍼스케일 vCore 4개에 매핑할 수 있습니다. 보조 복제본에 대해 SQL 라이선스가 필요하지 않습니다. Azure 하이브리드 혜택 가격이 읽기 확장(보조) 복제본에 자동으로 적용됩니다.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>어떤 종류의 워크로드에 맞게 SQL Database 하이퍼스케일이 설계되었나요?

SQL Database 하이퍼스케일은 모든 SQL Server 워크로드를 지원하지만 주로 OLTP에 최적화되어 있습니다. 하이브리드 및 분석(데이터 마트) 워크로드를 가져올 수도 있습니다.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Azure SQL Data Warehouse와 SQL Database 하이퍼스케일 중에 무엇을 선택해야 하나요?

현재 SQL Server를 데이터 웨어하우스로 사용하여 대화형 분석 쿼리를 실행하는 경우 SQL Database 하이퍼스케일이 가장 적합한 옵션입니다. 낮은 비용으로 상대적으로 작은 데이터 웨어하우스(예: 몇 TB~최대 10TB)를 호스트할 수 있으며 T-SQL 코드를 변경하지 않고 데이터 웨어하우스 워크로드를 SQL Database 하이퍼스케일로 마이그레이션할 수 있기 때문입니다.

PDW(병렬 데이터 웨어하우스), Teradata 또는 기타 MPP(Massively Parallel Processor)를 사용하여 복잡한 쿼리로 대규모 데이터 분석을 실행하는 경우에는 SQL Data Warehouse가 최고의 옵션입니다.
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database 하이퍼스케일 계산 질문

### <a name="can-i-pause-my-compute-at-any-time"></a>계산을 아무 때나 일시 중지할 수 있나요?

아니요.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>메모리 사용량이 많은 워크로드를 위해 계산에 추가 RAM을 프로비전할 수 있나요?

아니요. RAM을 더 확보하려면 더 큰 계산 크기로 업그레이드해야 합니다. Gen4 하드웨어는 Gen5 하드웨어에 비해 더 많은 RAM을 제공합니다. 자세한 내용은 [하이퍼스케일 스토리지 및 계산 크기](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-preview-for-provisioned-compute-tier)를 참조하세요.

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>크기가 다른 계산 노드를 여러 개 프로비저닝할 수 있나요?

아니요.

### <a name="how-many-read-scale-replicas-are-supported"></a>읽기-확장 복제본이 몇 개나 지원되나요?

공개 미리 보기에서 하이퍼스케일 데이터베이스는 읽기-확장 복제본 하나(총 2개의 복제본)로 생성됩니다. 읽기-확장 복제본을 추가 또는 제거하려면 Azure Portal을 사용하여 지원 요청을 제출하세요.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>고가용성을 위해 계산 노드를 추가로 프로비저닝해야 하나요?

하이퍼스케일 데이터베이스에서는 고가용성이 저장소 수준에서 제공됩니다. 고가용성을 제공하려면 복제본이 하나만 있으면 됩니다. 계산 복제본이 다운되면 데이터가 손실되지 않고 새로운 복제본이 자동으로 생성됩니다.

단, 복제본이 하나만 있으면 장애 조치(failover) 후 새 복제본에 로컬 캐시를 구축하는 데 약간의 시간이 걸릴 수 있습니다. 캐시를 재구축하는 단계에서 데이터베이스가 페이지 서버에서 데이터를 직접 가져오기 때문에 IOPS 및 쿼리 성능이 저하됩니다.

고가용성이 필요한 중용 업무용 앱의 경우 기본 계산 노드(기본값)를 포함하여 최소 2개의 계산 노드를 프로비전해야 합니다. 이렇게 하면 장애 조치(failover)가 발생할 경우 상시 대기가 가능합니다.

## <a name="data-size-and-storage-questions"></a>데이터 크기 및 저장소 질문

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>SQL Database 하이퍼스케일에 지원되는 최대 DB 크기는 얼마인가요?

100TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>하이퍼스케일의 트랜잭션 로그 크기는 얼마인가요?

하이퍼스케일의 트랜잭션 로그는 사실상 무한합니다. 로그 처리량이 많은 시스템에서 로그 공간 부족을 걱정할 필요가 없습니다. 단, 지속적이고 공격적인 워크로드에 대해서는 로그 생성 속도가 제한될 수 있습니다. 최대 및 평균 로그 생성 속도는 아직 알 수 없습니다(아직 미리 보기 상태임).

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>데이터베이스가 커지면 임시 DB 규모가 커지나요?

`tempdb` 데이터베이스는 로컬 SSD 저장소에 위치하며 사용자가 프로비전한 계산 규모를 기반으로 구성됩니다. `tempdb`는 최대 성능 이점을 제공하도록 최적화되고 배치됩니다. `tempdb` 크기는 구성할 수 없으며 저장소 하위 시스템에 의해 관리됩니다.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>데이터베이스 크기가 자동으로 커지나요? 아니면 데이터 파일의 크기를 관리해야 하나요?

데이터베이스 크기는 데이터를 추가로 삽입/수집하면 자동으로 커집니다.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>SQL Database 하이퍼스케일을 사용할 수 있는 또는 지원되는 최소 데이터베이스 크기는 얼마인가요?

5GB

### <a name="in-what-increments-does-my-database-size-grow"></a>데이터베이스가 증가하는 증분의 크기는 얼마인가요?

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>SQL Database 하이퍼스케일의 저장소는 로컬인가요 아니면 원격인가요?

하이퍼스케일에서 데이터 파일은 Azure 표준 저장소에 저장됩니다. 계산 노드와 가까운 머신의 로컬 SSD 스토리지에 데이터가 많이 캐시됩니다. 또한, 원격 노드에서 데이터를 가져 오는 빈도를 줄이기 위해 계산 노드는 로컬 SSD 및 메모리(버퍼 풀 등)에 캐시를 포함합니다.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>하이퍼스케일로 파일이나 파일 그룹을 관리하거나 정의할 수 있나요?

아닙니다.
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>데이터베이스의 데이터 증가에 대해 하드 캡을 프로비전할 수 있나요?

아닙니다.

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>데이터 파일이 SQL Database 하이퍼스케일을 통해 어떻게 배치되나요?

데이터 파일은 페이지 서버에 의해 제어됩니다. 데이터 크기가 커지면 데이터 파일 및 연결된 페이지 서버 노드가 추가됩니다.

### <a name="is-database-shrink-supported"></a>데이터베이스 축소가 지원되나요?

아닙니다.

### <a name="is-database-compression-supported"></a>데이터베이스 압축이 지원되나요?

예

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>거대한 테이블이 있는 경우 테이블 데이터가 여러 데이터 파일에 분산되나요?

예. 주어진 테이블과 연결된 데이터 페이지는 모두 동일한 파일 그룹에 속하는 여러 데이터 파일에 분산될 수 있습니다. SQL Server는 [비례하여 채우기 전략](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)을 사용하여 여러 데이터 파일에 데이터를 분산합니다.

## <a name="data-migration-questions"></a>데이터 마이그레이션 질문

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>기존 Azure SQL 데이터베이스를 하이퍼스케일 서비스 계층으로 이동할 수 있나요?

예. 기존 Azure SQL 데이터베이스를 하이퍼스케일로 이동할 수 있습니다. 공개 미리 보기에서는 단방향 마이그레이션입니다. 하이퍼스케일에서 또 다른 서비스 계층으로 데이터베이스를 이동할 수는 없습니다. 프로덕션 데이터베이스의 복사본을 만들어서 POC(개념 증명)용으로 하이퍼스케일에 마이그레이션하는 것이 좋습니다.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>하이퍼스케일 데이터베이스를 다른 버전으로 이동할 수 있나요?

아니요. 공개 미리 보기에서는 하이퍼스케일 데이터베이스를 다른 서비스 계층으로 이동할 수 없습니다.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층으로 마이그레이션한 후에 손실되는 기능이 있나요?

예. 공개 미리 보기 중 Azure SQL Database의 장기 보존 백업은 대규모에서 지원 되지 않습니다. 대규모로 데이터베이스를 마이그레이션한 후이 기능에 작동이 중지 됩니다.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>온-프레미스 SQL Server 데이터베이스나 SQL Server 가상 머신 데이터베이스를 하이퍼스케일로 이동할 수 있나요?

예. 기존의 모든 마이그레이션 기술(예: BACPAC, 트랜잭션 복제, 논리 데이터 로딩)을 사용하여 하이퍼스케일로 마이그레이션할 수 있습니다. [Azure Database Migration Service](../dms/dms-overview.md)도 참조하세요.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>온-프레미스 또는 가상 머신 환경에서 하이퍼스케일로 마이그레이션하는 동안 가동 중지 시간은 얼마나 되며 어떻게 최소화할 수 있나요?

가동 중지 시간은 Azure SQL 데이터베이스에서 데이터베이스를 단일 데이터베이스로 마이그레이션하는 경우 가동 중지 시간과 같습니다. [트랜잭션 복제](replication-to-sql-database.md#data-migration-scenario
)를 사용하면 크기가 최대 몇 TB에 이르는 데이터베이스의 마이그레이션 가동 중지 시간을 최소화할 수 있습니다. 초대형 데이터베이스(10TB 초과)의 경우 ADF, Spark 또는 기타 데이터 이동 기술을 사용하여 데이터를 마이그레이션하는 것이 좋습니다.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>양이 X인 데이터를 SQL Database 하이퍼스케일로 가져오는 데 시간이 얼마나 걸리나요?

아직 알려지지 않음(아직 미리 보기 중임)

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Blob Storage의 데이터를 읽을 수 있고 빠른 로딩(Polybase 및 SQL Data Warehouse와 같은)이 가능한가요?

Azure Storage의 데이터를 읽을 수 있고 하이퍼스케일 데이터베이스에 데이터를 로드할 수 있습니다(일반 단일 데이터베이스를 사용하는 것처럼). Azure SQL Database에서는 현재 Polybase가 지원되지 않습니다. [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)를 사용하거나 [SQL용 Spark 커넥터](sql-database-spark-connector.md)를 사용하여 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)에서 Spark 작업을 실행하여 Polybase를 수행할 수 있습니다. SQL에 대한 Spark 커넥터는 대량 삽입을 지원합니다.

하이퍼스케일에서는 단순 복구 또는 대량 로깅 모델이 지원되지 않습니다. 고가용성을 제공하려면 전체 복구 모델이 필요합니다. 하지만 하이퍼스케일은 새로운 로그 아키텍처 때문에 단일 데이터베이스에 비해 빠른 데이터 수집 속도를 제공합니다.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>SQL Database 하이퍼스케일로 대량의 데이터를 수집하기 위해 여러 노드를 프로비전할 수 있나요?

아니요. SQL Database 하이퍼스케일은 SMP 아키텍처이며 비대칭 다중 처리 또는 다중 마스터 아키텍처가 아닙니다. 여러 개의 복제본을 만들어서 읽기 전용 워크로드 규모 확장할 수만 있습니다.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>SQL Database 하이퍼스케일에서 마이그레이션이 지원되는 가장 오래된 SQL Server 버전은 무엇인가요?

SQL Server 2005입니다. 자세한 내용은 [단일 데이터베이스 또는 풀링된 데이터베이스로 마이그레이션](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)을 참조하세요. 호환성 문제는 [데이터베이스 마이그레이션 호환성 문제 해결](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)을 참조하세요.

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Aurora, MySQL, Oracle, DB2 등의 다른 데이터 원본 및 기타 데이터베이스 플랫폼에서 마이그레이션을 SQL Database 하이퍼스케일에서 지원하나요?

예. SQL Server 이외의 다른 데이터 원본에서 가져오려면 논리 마이그레이션이 필요합니다. 논리 마이그레이션에는 [Azure Database Migration Service](../dms/dms-overview.md)를 사용할 수 있습니다.

## <a name="business-continuity-and-disaster-recovery-questions"></a>비즈니스 연속성 및 재해 복구 질문

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>하이퍼스케일 데이터베이스에 어떤 SLA가 지원되나요?

일반적으로 공개 미리 보기 중에는 SLA가 제공되지 않습니다. 단 하이퍼스케일은 현재 SQL DB와 동일한 수준의 고 가용성을 제공합니다. [SLA](https://azure.microsoft.com/support/legal/sla/)를 참조하세요.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database 서비스에서 데이터베이스 백업이 관리되나요?

예

### <a name="how-often-are-the-database-backups-taken"></a>데이터베이스 백업이 얼마나 자주 수행되나요?

SQL Database 하이퍼스케일 데이터베이스에는 기존의 전체, 차등 및 로그 백업이 없습니다. 대신 데이터 파일의 일반 스냅숏이 생성되며 생성된 로그는 구성했거나 제공되는 보존 기간 동안 그대로 보존됩니다.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database 하이퍼스케일에서 지정 시간 복원을 지원하나요?

예

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>SQL Database 하이퍼스케일에서 백업/복원의 RPO(복구 지점 목표)/RTO(복구 시간 목표)는 무엇인가요?

RPO는 0분입니다. RTO 목표는 데이터베이스 크기에 관계없이 10분 미만입니다. 단, 공개 미리 보기 중에는 복원 시간이 더 오래 걸릴 수 있습니다.

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>큰 데이터베이스를 백업하면 주 노드의 계산 성능에 영향을 주나요?

아니요. 백업은 저장소 하위 시스템에 의해 관리되며 파일 스냅숏이 활용됩니다. 주 노드의 사용자 워크로드에 영향을 주지 않습니다.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>SQL Database 하이퍼스케일 데이터베이스로 지역 복원을 수행할 수 있나요?

아니요, 공개 미리 보기 중에는 가능하지 않습니다.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>SQL Database 하이퍼스케일 데이터베이스로 지역에서 복제를 설정할 수 있나요?

아니요, 공개 미리 보기 중에는 가능하지 않습니다.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>보조 계산 노드가 SQL Database 하이퍼스케일을 통해 지역에서 복제되나요?

아니요, 공개 미리 보기 중에는 가능하지 않습니다.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>SQL Database 하이퍼스케일 데이터베이스 백업을 가져와서 온-프레미스 서버나 VM의 SQL Server에 복원할 수 있나요?

아니요. 하이퍼스케일 데이터베이스의 저장소 형식은 기존 SQL Server와 다르며 백업을 제어하거나 액세스할 수 없습니다. SQL Database 하이퍼스케일 데이터베이스에서 데이터를 가져오려면 내보내기 서비스를 사용하거나 스크립팅과 BCP를 사용합니다.

## <a name="cross-feature-questions"></a>교차 기능 질문

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>하이퍼스케일 서비스 계층으로 마이그레이션한 후에 손실되는 기능이 있나요?

예. 공개 미리 보기 중 Azure SQL Database의 장기 보존 백업은 대규모에서 지원 되지 않습니다. 대규모로 데이터베이스를 마이그레이션한 후이 기능에 작동이 중지 됩니다.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Polybase가 SQL Database 하이퍼스케일과 작동이 되나요?

아니요. Azure SQL Database에서는 Polybase가 지원되지 않습니다.

### <a name="does-the-compute-have-support-for-r-and-python"></a>계산에 R 및 Python이 지원되나요?

아니요. Azure SQL Database에서는 R 및 Python이 지원되지 않습니다.

### <a name="are-the-compute-nodes-containerized"></a>계산 노드가 컨테이너화되나요?

아니요. 데이터베이스는 컨테이너가 아닌 컴퓨팅 VM에 상주합니다.

## <a name="performance-questions"></a>성능 질문

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>최대 SQL Database 하이퍼스케일 계산에 푸시할 수 있는 처리량은 얼마인가요?

아직 알려지지 않음(아직 미리 보기 중임)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>최대 SQL Database 하이퍼스케일 계산에 지원되는 IOPS는 얼마나 되나요?

아직 알려지지 않음(아직 미리 보기 중임)

### <a name="does-my-throughput-get-affected-by-backups"></a>처리량이 백업의 영향을 받나요?

아니요. 계산 계층에 영향을 주지 않도록 계산은 스토리지 계층에서 분리됩니다.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>계산 노드를 추가로 프로비저닝할 때 처리량이 영향을 받나요?

스토리지가 공유되고 기본 계산 노드와 보조 계산 노드 간에 직접적인 물리적 복제가 발생하지 않기 때문에 기술적으로, 읽기-확장 노드를 추가하면 기본 노드의 처리량이 영향을 받습니다. 하지만 지속적이며 공격적인 워크로드를 제한하여 보조 노드와 페이지 서버에서 로그 적용을 따라 잡아서 보조 노드의 잘못된 읽기 성능을 방지할 수 있습니다.

## <a name="scalability-questions"></a>확장성 질문

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>계산 노드를 강화 및 축소하는 데 시간이 얼마나 걸리나요?

몇 분 정도

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>강화 및 축소 작업을 진행하는 동안 데이터베이스는 오프라인 상태인가요?

아니요. 강화 및 축소는 온라인 상태가 됩니다.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>크기 조정 작업을 진행하는 동안 연결이 끊어질 수 있나요?

강화 또는 축소 작업을 수행하는 경우 대상 크기의 계산 노드에 장애 조치(failover)가 발생하면 기존 연결이 끊어집니다. 읽기 복제본을 추가하면 연결이 끊어지지 않습니다.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>계산 노드 강화 및 축소는 자동인가요? 아니면 최종 사용자가 트리거하는 작업인가요?

최종 사용자가 트리거합니다. 자동이 아닙니다.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>계산이 강화되면 `tempb`도 증가하나요?

예. 계산이 증가하면 Temp db가 자동으로 강화됩니다.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>기본 계산 헤드가 여러 개여서 높은 수준의 동시성이 가능한 다중 마스터 시스템과 같이 여러 개의 기본 계산을 프로비저닝할 수 있나요?

아니요. 기본 계산 노드만 읽기/쓰기 요청을 수락합니다. 보조 계산 노드는 읽기 전용 요청만 수락합니다.

## <a name="read-scale-questions"></a>읽기 확장 질문

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>프로비전이 가능한 보조 계산 노드의 수는 얼마나 되나요?

공개 미리 보기에서는 하이퍼스케일 데이터베이스에 대해 기본적으로 2개의 복제본이 생성됩니다. 복제본의 수를 조정하려면 Azure Portal을 사용하여 지원 요청을 제출하세요.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>보조 계산 노드에 어떻게 연결하나요?

연결 문자열의 `ApplicationIntent` 인수를 `read_only`로 설정하면 이러한 추가 읽기 전용 계산 노드에 연결할 수 있습니다. `read-only`로 표시된 모든 연결은 추가 읽기 전용 계산 노드 중 하나에 자동으로 라우팅됩니다.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>읽기-확장 복제본 전용 엔드포인트를 만들 수 있나요?

아니요. 공개 미리 보기에서는 `ApplicationIntent=ReadOnly`를 지정하여 읽기-확장 복제본에 연결만 가능합니다.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>시스템에서 읽기 워크로드에 대한 지능적인 부하 분산이 수행되나요?

아니요. 미리 보기에서는 읽기 전용 워크로드가 임의의 읽기-확장 복제본으로 리디렉션됩니다.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>보조 계산 노드를 기본 계산 노드와 독립적으로 강화/축소할 수 있나요?

아니요, 공개 미리 보기 중에는 가능하지 않습니다.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>기본 계산 노드와 추가적인 보조 계산 노드에 대해 temp db 크기가 달라지나요?

아니요. `tempdb`는 계산 크기 프로비전을 기반으로 구성되며 공개 미리 중에 보조 계산 노드는 기본 계산 노드와 크기가 같습니다.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>보조 계산 노드에 인덱스와 보기를 추가할 수 있나요?

아니요. 하이퍼스케일 데이터베이스에는 공유 스토리지가 포함됩니다. 즉, 모든 계산 노드에는 동일한 테이블, 인덱스, 보기가 표시됩니다. 보조 노드 읽기에 최적화된 추가 인덱스가 필요하면 먼저 기본 노드에 인덱스를 추가해야 합니다.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>기본 및 보조 계산 노드 사이의 지연 시간은 얼마나 되나요?

기본 노드에서 트랜잭션이 커밋된 시간부터, 로그 생성 속도에 따라, 즉시 또는 짧은 시간(밀리초)이 지연됩니다.

## <a name="next-steps"></a>다음 단계

하이퍼스케일 서비스 계층에 대한 자세한 내용은 [하이퍼스케일 서비스 계층(미리 보기)](sql-database-service-tier-hyperscale.md)을 참조하세요.
