---
title: Azure Advisor로 Azure 애플리케이션의 성능 향상
description: Advisor를 사용하여 Azure 배포 성능을 최적화합니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443051"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Azure Advisor로 Azure 애플리케이션의 성능 향상

Azure Advisor 성능 권장 사항은 업무에 중요한 애플리케이션의 속도 및 응답성을 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **성능** 탭에서 Advisor를 사용하여 성능 권장 사항을 볼 수 있습니다.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>정상 엔드포인트로 더 빠르게 장애 조치(failover)할 수 있게 Traffic Manager의 DNS TTL(Time To Live) 단축

Traffic Manager의 [TTL(Time to Live) 설정](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)을 사용하면 특정 엔드포인트가 쿼리에 응답을 멈추었을 때 엔드포인트로 전환하는 속도를 지정할 수 있습니다. TTL 값을 줄면 클라이언트가 작동하는 엔드포인트로 더 빠르게 라우팅됩니다.

Azure Advisor는 긴 TTL이 구성된 Traffic Manager 프로필을 식별하여 [빠른 장애 조치(Failover)](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)를 위해 프로필이 구성되었는지 여부에 따라 20초 또는 60초로 TTL을 구성하도록 권장합니다.

## <a name="improve-database-performance-with-sql-db-advisor"></a>SQL DB Advisor로 데이터베이스 성능 개선

Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. SQL Database Azure와 통합되므로 SQL Azure Database의 성능을 향상시키기 위한 권장 사항이 표시됩니다. SQL Database Advisor은 사용 기록을 분석 하 여 SQL Azure 데이터베이스의 성능을 평가 합니다. 그런 후 데이터베이스의 일반적인 워크로드를 실행하는 데 가장 적합한 권장 사항을 제공합니다.

> [!NOTE]
> 권장 사항을 가져오려면 데이터베이스에 일주일의 사용 기간이 필요하고, 그 기간 내에 일관된 활동이 필요합니다. SQL Database Advisor는 일관성 있는 쿼리 패턴을 임의 활동 버스트보다 더욱 쉽게 최적화할 수 있습니다.

SQL Database Advisor에 대한 자세한 내용은 [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)를 참조하세요.

## <a name="improve-app-service-performance-and-reliability"></a>App Service 성능 및 안정성 향상

Azure Advisor는 App Services 환경을 개선하고 관련 플랫폼 기능을 검색하기 위한 모범 사례 권장 사항을 통합합니다. App Services 권장 사항 예제:
* 완화 옵션을 사용하여 메모리나 CPU 리소스가 앱 런타임에서 소모되는 인스턴스 검색
* Web Apps 및 데이터베이스를 함께 배치할 때 성능을 향상시키고 비용을 절감할 수 있는 인스턴스 검색

App Services 권장 사항에 대한 자세한 내용은 [Azure App Service에 대한 모범 사례](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)를 참조하세요.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Managed Disks를 사용하여 디스크 I/O 제한 방지

Advisor는 해당 확장성 목표에 도달하는 스토리지 계정에 속한 가상 머신을 식별합니다. 이 조건으로 해당 VM이 I/O 제한에 걸리기 쉽습니다. Advisor는 성능 저하를 방지하기 위해 Managed Disks를 사용하도록 추천합니다.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Storage를 사용하여 가상 머신 디스크의 성능 및 안정성 향상

Advisor는 스토리지 계정에 많은 양의 트랜잭션이 있는 표준 디스크를 갖춘 가상 머신을 식별하고 프리미엄 디스크로 업그레이드하도록 추천합니다. 

Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 머신에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Premium Storage 계정을 사용하는 가상 머신 디스크는 SSD(솔리드 스테이트 드라이브)에 데이터를 저장합니다. 애플리케이션의 성능을 최상으로 높이려면 높은 IOPS가 필요한 모든 가상 머신 디스크를 프리미엄 스토리지로 마이그레이션하는 것이 좋습니다.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>쿼리 성능을 향상시키려면 SQL 데이터 웨어하우스 테이블에서 데이터 기울이기 제거

데이터 기울이기는 워크 로드를 실행하는 경우 불필요한 데이터 이동이나 리소스 병목 현상을 일으킬 수 있습니다. Advisor는 15%를 초과한 배포 데이터 기울이기를 감지하면 데이터를 다시 배포하고 테이블 배포 키를 다시 선택하도록 권고합니다. 기울이기 식별 및 제거에 대한 자세한 정보는 [기울이기 문제 해결](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)을 참조합니다.

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>쿼리 성능을 향상시키려면 SQL 데이터 웨어하우스 테이블에서 오래된 테이블 통계 업데이트 또는 만들기

Advisor는 최신 [테이블 통계](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)가 없는 테이블을 식별하고 테이블 통계를 만들거나 업데이트할 것을 권고합니다. SQL 데이터 웨어하우스 쿼리 최적화 프로그램은 최신 통계를 사용하여 쿼리 결과의 행 수 또는 카디널리티를 예상하며, 이를 통해 쿼리 최적화 프로그램이 가장 빠른 성능에 대한 고품질 쿼리 계획을 만들 수 있습니다.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>SQL Data Warehouse 테이블에서 캐시 사용률을 최적화하도록 확장하여 쿼리 성능 향상

Azure Advisor는 SQL Data Warehouse에서 캐시 사용률이 높고 적중 비율이 낮은지를 감지합니다. 이 조건은 SQL Data Warehouse의 성능에 영향을 줄 수 있는 높은 캐시의 제거를 나타냅니다. 관리자는 SQL Data Warehouse를 확장하여 워크로드에 충분한 캐시 용량을 할당하도록 제안합니다.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>SQL Data Warehouse 테이블을 복제된 테이블로 변환하여 쿼리 성능 향상

Advisor는 복제된 테이블이 아니지만 변환을 통해 이득을 얻을 수 있는 테이블을 식별하고 이러한 테이블을 변환하도록 제안합니다. 추천 사항은 SQL Data Warehouse 테이블의 복제된 테이블 크기, 열 수, 테이블 배포 유형 및 파티션 수를 기반으로 합니다. 컨텍스트에 대한 추천 사항에 추가적인 추론이 제공될 수 있습니다. 이 추천 사항을 확인하는 방법에 대한 자세한 내용은 [SQL Data Warehouse 추천 사항](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)을 참조하세요. 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>스토리지 계정을 Azure Resource Manager로 마이그레이션하여 최신 Azure 기능 모두 이용하기

스토리지 계정 배포 모델을 Azure Resource Manager(Resource Manager)로 마이그레이션하여 템플릿 배포, 추가 보안 옵션, Azure Storage의 최신 기능 활용을 위해 GPv2 계정으로 업그레이드하는 기능을 활용하세요. Advisor는 Classic 배포 모델을 사용 중인 독립형 스토리지 계정을 식별하고 Resource Manager 배포 모델로 마이그레이션할 것을 권장합니다.

> [!NOTE]
> Azure Monitor의 클래식 경고는 2019 년 8 월에 사용이 중지 되었습니다. 새 플랫폼에서 경고 기능을 유지하려면 Resource Manager를 사용하도록 클래식 스토리지 계정을 업그레이드하는 것이 좋습니다. 자세한 내용은 [클래식 경고 사용 중지](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)를 참조하세요.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>최대 구독 제한에 도달 하지 않도록 저장소 계정 디자인

Azure 지역은 구독 당 최대 250 개의 저장소 계정을 지원할 수 있습니다. 제한에 도달 하면 해당 지역/구독 조합에서 저장소 계정을 더 이상 만들 수 없습니다. Advisor는 최대한도에 도달 하는 데 가까운 모든 저장소 계정에 대한 설계를 위해 구독과 surface 권장 사항을 확인 합니다.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Azure MySQL, Azure PostgreSQL 및 Azure MariaDB 서버의 성능 최적화 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>CPU 병목 상태를 사용 하 여 Azure MySQL, Azure PostgreSQL 및 Azure MariaDB 서버의 CPU 압력을 수정 합니다.
장기간 CPU 사용률이 높으면 작업에 쿼리 성능이 저하 될 수 있습니다. CPU 크기를 높이면 데이터베이스 쿼리의 런타임 최적화 및 전반적인 성능 향상에 도움이 됩니다. Azure Advisor는 CPU 사용률이 높은 cpu 사용률이 높은 서버를 식별 하 고 계산 크기를 조정 하는 것이 좋습니다.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Azure MySQL, Azure PostgreSQL 및 Azure MariaDB 서버에서 메모리 제약 조건을 줄이고 메모리 액세스에 최적화 된 SKU로 이동 합니다.
낮은 캐시 적중률으로 인해 쿼리 성능이 느려지고 IOPS가 증가할 수 있습니다. 이는 잘못 된 쿼리 계획 이거나 메모리 집약적 작업 실행으로 인해 발생할 수 있습니다. 쿼리 계획을 수정 하거나 Azure Database for PostgreSQL 데이터베이스 서버, Azure MySQL 데이터베이스 서버 또는 Azure MariaDB 서버의 [메모리를 늘려서](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) 데이터베이스 작업의 실행을 최적화 하는 데 도움이 됩니다. Azure Advisor는이 높은 버퍼 풀 변동으로 인해 영향을 받는 서버를 식별 하 고 쿼리 계획을 수정 하거나 더 많은 메모리를 사용 하 여 상위 SKU로 이동 하거나 저장소 크기를 늘려서 더 많은 IOPS를 가져옵니다.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Azure MySQL 또는 Azure PostgreSQL 읽기 복제본을 사용 하 여 읽기 집약적 워크 로드에 대한 읽기 확장
Azure Advisor는 지난 7 일간 서버에서 쓰기에 대한 읽기의 비율과 같은 작업 기반 추론을 활용 하 여 읽기 집약적인 작업을 식별 합니다. 읽기/쓰기 비율이 매우 높은 azure database for PostgreSQL 리소스 또는 Azure database for MySQL 리소스는 CPU 및/또는 메모리 경합이 증가 하 여 쿼리 성능이 저하 될 수 있습니다.  [복제본](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) 을 추가 하면 복제본 서버에 대한 읽기를 확장 하 여 주 서버에서 CPU 및/또는 메모리 제약 조건을 방지할 수 있습니다. Advisor는 이러한 높은 읽기 집약적인 작업을 사용 하 여 서버를 식별 하 고 읽기 [복제본](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 추가 하 여 일부 읽기 작업을 오프 로드 하는 것이 좋습니다.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>연결 제약 조건을 방지 하기 위해 Azure MySQL, Azure PostgreSQL 또는 Azure MariaDB 서버를 상위 SKU로 크기 조정
데이터베이스 서버에 대한 새 연결은 일부 메모리를 차지 합니다. 서버에 대한 연결이 메모리의 [상한](https://docs.microsoft.com/azure/postgresql/concepts-limits) 으로 인해 실패 하는 경우 데이터베이스 서버의 성능이 저하 됩니다. Azure Advisor는 많은 연결 오류가 발생 하 여 실행 되는 서버를 식별 하 고, 계산을 확장 하 여 서버에 더 많은 메모리를 제공 하 고, 코어 당 계산을 많이 사용 하는 메모리 최적화 Sku를 사용 하 여 서버에 더 많은 메모리를 제공

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>캐시와 응용 프로그램 성능을 개선 하기 위해 다른 크기나 SKU로 캐시 크기 조정

캐시 인스턴스는 높은 메모리 부족, 높은 서버 부하 또는 높은 네트워크 대역폭으로 실행 되지 않을 때 최상의 성능을 제공 합니다 .이로 인해 응답 하지 않거나, 데이터 손실이 발생 하거나, 사용할 수 없게 될 수 있습니다. Advisor는 이러한 상황에서 캐시 인스턴스를 식별 하 고, 메모리 부족, 서버 부하 또는 네트워크 대역폭을 줄이기 위해 모범 사례를 적용 하거나 용량이 더 많은 다른 크기나 SKU로 크기를 조정 하는 것이 좋습니다.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>트래픽이 있는 지역을 Azure Cosmos DB 계정에 추가

Advisor는 현재 구성 되지 않은 지역에서 트래픽을 포함 하는 Azure Cosmos DB 계정을 검색 하 고 해당 지역을 추가 하는 것이 좋습니다. 이렇게 하면 해당 지역에서 들어오는 요청에 대한 대기 시간이 향상 되 고, 지역 가동 중단이 발생 하는 경우 가용성을 보장 합니다. [Azure Cosmos DB와의 글로벌 데이터 배포에 대한 자세한 정보](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>고객 포함 또는 제외 경로를 사용 하 여 Azure Cosmos DB 인덱싱 정책 구성

Azure Advisor는 기본 인덱싱 정책을 사용 하는 Cosmos DB 컨테이너를 식별 하지만 워크 로드 패턴을 기반으로 사용자 지정 인덱싱 정책의 이점을 누릴 수 있습니다. 기본 인덱싱 정책은 모든 속성을 인덱싱합니다. 하지만 쿼리 필터에 사용 되는 명시적으로 포함 되거나 제외 된 경로를 사용 하 여 사용자 지정 인덱싱 정책을 사용 하는 경우에는 인덱싱에 사용 된 RUs와 저장소를 줄일 수 [인덱스 정책 수정에 대한 자세한 정보](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB 쿼리 페이지 크기(MaxItemCount)를 -1로 구성 

Azure Advisor은 쿼리 페이지 크기 100를 사용 하는 Azure Cosmos DB 컨테이너를 식별 하 고, 빠른 검색을 위해 페이지 크기-1을 사용 하는 것이 좋습니다. [최대 항목 수에 대한 자세한 정보](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor에서 성능 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **성능** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
