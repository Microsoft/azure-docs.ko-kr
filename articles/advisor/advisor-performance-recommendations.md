---
title: Advisor를 사용 하 여 Azure 앱의 성능 향상
description: Azure Advisor의 성능 권장 사항을 사용 하 여 비즈니스에 중요 한 응용 프로그램의 속도 및 응답성을 향상 시킬 수 있습니다.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0112e94e7652026e020e99ca82ad757c236a0c53
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653310"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Azure Advisor를 사용 하 여 Azure 응용 프로그램의 성능 향상

Azure Advisor의 성능 권장 사항은 업무상 중요 한 응용 프로그램의 속도 및 응답성을 향상 시키는 데 도움이 될 수 있습니다. Advisor 대시보드의 **성능** 탭에서 Advisor를 사용하여 성능 권장 사항을 볼 수 있습니다.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Traffic Manager 프로필에 대 한 DNS ttl (time to live)을 줄여 정상 끝점으로 빠르게 장애 조치 (failover)

Azure Traffic Manager 프로필에 대 한 [ttl (time-to-live) 설정을](../traffic-manager/traffic-manager-performance-considerations.md) 사용 하 여 지정 된 끝점이 쿼리에 응답 하지 않는 경우 끝점을 전환 하는 속도를 지정할 수 있습니다. TTL 값을 줄이면 클라이언트는 작동 하는 끝점에 더 빠르게 라우팅됩니다.

Azure Advisor는 더 긴 TTL이 구성 된 Traffic Manager 프로필을 식별 합니다. [빠른 장애 조치(Failover)](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)에 대해 프로필이 구성 되었는지 여부에 따라 TTL을 20 초 또는 60 초로 구성 하는 것이 좋습니다.

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>SQL Database Advisor를 사용 하 여 데이터베이스 성능 향상 (일시적으로 사용 안 함)

Azure Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. SQL Database Advisor와 통합 되어 데이터베이스의 성능을 향상 시키기 위한 권장 사항을 제공 합니다.SQL Database Advisor은 사용 기록을 분석 하 여 데이터베이스의 성능을 평가 합니다. 그런 다음 데이터베이스의 일반적인 워크 로드를 실행 하는 데 가장 적합 한 권장 사항을 제공 합니다.

> [!NOTE]
> 권장 사항을 얻기 전에 데이터베이스를 일주일 동안 사용 해야 하며, 해당 주 내에는 일정 한 작업을 수행 해야 합니다. SQL Database Advisor는 일관성 있는 쿼리 패턴을 임의 활동 버스트보다 더욱 쉽게 최적화할 수 있습니다.

자세한 내용은 [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md)를 참조 하세요.

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>안정성 및 성능 향상을 위해 저장소 클라이언트 라이브러리를 최신 버전으로 업그레이드

최신 버전의 Storage 클라이언트 라이브러리 SDK에는 고객이 보고 한 문제에 대 한 수정 사항이 포함 되어 있으며 QA 프로세스를 통해 사전에 식별 됩니다. 최신 버전은 Azure Storage를 사용 하 여 전반적인 환경을 개선할 수 있는 새로운 기능과 함께 안정성 및 성능 최적화를 제공 합니다. Advisor는 최신 버전의 SDK를 사용 하는 경우 최신 버전의 SDK로 업그레이드 하는 데 필요한 권장 사항 및 단계를 제공 합니다. 권장 사항은 c + + 및 .NET의 지원 되는 언어에 대 한 것입니다.

## <a name="improve-app-service-performance-and-reliability"></a>App Service 성능 및 안정성 향상

Azure Advisor는 App Service 환경을 개선 하 고 관련 플랫폼 기능을 검색 하기 위한 권장 사항을 통합 합니다. App Service 권장 사항의 예는 다음과 같습니다.
* 완화 옵션을 사용 하 여 메모리 또는 CPU 리소스가 앱 런타임에서 소모 되는 인스턴스를 검색 합니다.
* 웹 앱 및 데이터베이스와 같은 리소스를 공동 배치 하 여 성능을 향상 시키고 비용을 절감할 수 있는 인스턴스를 검색 합니다.

자세한 내용은 [Azure App Service에 대 한 모범 사례](../app-service/app-service-best-practices.md)를 참조 하세요.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>관리 디스크를 사용 하 여 디스크 i/o 제한 방지

Advisor는 확장성 목표에 도달 하는 저장소 계정에 속한 가상 컴퓨터를 식별 합니다. 이 조건으로 해당 VM이 I/O 제한에 걸리기 쉽습니다. Advisor는 관리 디스크를 사용 하 여 성능 저하를 방지 하는 것이 좋습니다.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Premium Storage를 사용하여 가상 머신 디스크의 성능 및 안정성 향상

Advisor는 저장소 계정에 많은 양의 트랜잭션이 있는 표준 디스크를 사용 하 여 가상 컴퓨터를 식별 합니다. 프리미엄 디스크로 업그레이드할 것을 권장 합니다. 

Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 머신에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Premium Storage 계정을 사용 하는 가상 머신 디스크는 Ssd (반도체 드라이브)에 데이터를 저장 합니다. 응용 프로그램에 대 한 최상의 성능을 위해 높은 IOPS가 필요한 모든 가상 머신 디스크를 Premium Storage 마이그레이션하는 것이 좋습니다.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블에서 데이터 오차를 제거 하 여 쿼리 성능 향상

데이터 오차는 작업을 실행할 때 불필요 한 데이터 이동 또는 리소스 병목 현상을 유발할 수 있습니다. Advisor가 15% 이상의 배포 데이터 오차를 검색 합니다. 데이터를 재배포 하 고 테이블 배포 키 선택 항목을 다시 방문 하는 것이 좋습니다. 기울이기 식별 및 제거에 대한 자세한 정보는 [기울이기 문제 해결](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)을 참조합니다.

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블에서 오래 된 테이블 통계를 만들거나 업데이트 하 여 쿼리 성능을 향상 시킵니다.

Advisor는 최신 [테이블 통계가](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) 없는 테이블을 식별 하 고 통계를 만들거나 업데이트 하는 것을 권장 합니다. Azure Synapse Analytics의 쿼리 최적화 프로그램은 최신 통계를 사용 하 여 쿼리 결과의 카디널리티 또는 행 수를 계산 합니다. 이러한 예상치를 통해 쿼리 최적화 프로그램은 가장 빠른 성능을 제공 하는 쿼리 계획을 만들 수 있습니다.

## <a name="improve-mysql-connection-management"></a>MySQL 연결 관리 개선

Advisor 분석은 MySQL 서버에 연결 하는 응용 프로그램이 연결을 효율적으로 관리 하지 않을 수 있음을 나타낼 수 있습니다. 이로 인해 불필요 한 리소스 소비 및 전반적인 응용 프로그램 대기 시간이 발생할 수 있습니다. 연결 관리를 향상시키려면 수명이 짧은 연결 수를 줄이고 불필요한 유휴 연결을 제거하는 것이 좋습니다. ProxySQL과 같은 서버 쪽 연결 풀을 구성 하 여 이러한 개선을 수행할 수 있습니다.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블에서 캐시 사용률을 최적화 하 여 쿼리 성능 향상을 위해 확장

Azure Advisor는 Azure Synapse Analytics 테이블에 높은 수준의 캐시가 사용 되는 비율과 낮은 적중률이 있는지 검색 합니다. 이 상태는 Azure Synapse Analytics 인스턴스의 성능에 영향을 줄 수 있는 높은 캐시 제거를 나타냅니다. Advisor는 워크 로드에 충분 한 캐시 용량을 할당할 수 있도록 Azure Synapse Analytics 인스턴스를 확장 하는 것을 권장 합니다.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Azure Synapse Analytics 테이블을 복제 된 테이블로 변환 하 여 쿼리 성능 향상

Advisor는 복제 된 테이블이 아닌 변환을 통해 이점을 누릴 수 있는 테이블을 식별 합니다. 이러한 테이블을 변환 하는 것을 제안 합니다. 권장 사항은 다음을 기준으로 합니다.
- 복제 된 테이블의 크기입니다. 
- 열의 수입니다. 
- 테이블 배포 유형입니다. 
- Azure Synapse Analytics 테이블의 파티션 수입니다. 

컨텍스트 권장 사항에 추가 추론을 제공할 수 있습니다. 이 권장 사항을 결정 하는 방법에 대 한 자세한 내용은 [Azure Synapse Analytics 권장 사항](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables)을 참조 하세요. 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>저장소 계정을 Azure Resource Manager로 마이그레이션하여 최신 Azure 기능을 받으세요.

저장소 계정 배포 모델을 Azure Resource Manager로 마이그레이션하여 다음과 같은 이점을 활용할 수 있습니다.
- 템플릿 배포.
- 추가 보안 옵션입니다.
- 최신 Azure Storage 기능을 사용할 수 있도록 GPv2 계정으로 업그레이드할 수 있습니다. 

Advisor는 클래식 배포 모델을 사용 하는 독립 실행형 저장소 계정을 식별 하 고 리소스 관리자 배포 모델로 마이그레이션하는 것을 권장 합니다.

> [!NOTE]
> Azure Monitor의 클래식 경고는 2019 년 8 월에 사용이 중지 되었습니다. 새 플랫폼에서 경고 기능을 유지하려면 Resource Manager를 사용하도록 클래식 스토리지 계정을 업그레이드하는 것이 좋습니다. 자세한 내용은 [클래식 경고](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)사용 중지를 참조 하세요.

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>최대 구독 제한에 도달 하지 않도록 저장소 계정 디자인

Azure 지역은 구독 당 최대 250 개의 저장소 계정을 지원 합니다. 이 한도에 도달 하면 해당 지역/구독 조합에서 저장소 계정을 만들 수 없습니다. Advisor는 구독을 확인 하 고 최대 한도에 도달 하는 데 가까운 구독/지역에 대 한 저장소 계정 수를 줄일 수 있는 권장 사항을 제공 합니다.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>높은 P2S 사용을 해결 하기 위해 VPN Gateway SKU의 크기를 높이는 것이 좋습니다.

각 Azure VPN Gateway SKU는 지정 된 수의 동시 P2S 연결만 지원할 수 있습니다. 연결 수가 게이트웨이 제한에 근접 한 경우 추가 연결 시도가 실패할 수 있습니다. 게이트웨이의 크기를 늘리면 더 많은 동시 P2S 사용자를 지원할 수 있습니다. Advisor는 게이트웨이의 크기를 높이기 위한 권장 사항 및 지침을 제공 합니다.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>높은 CPU를 처리 하기 위해 VPN Gateway SKU 크기를 늘립니다.

트래픽 부하가 높으면 VPN gateway는 높은 CPU로 인해 패킷을 삭제할 수 있습니다. VPN Gateway SKU를 업그레이드 하는 것이 좋습니다. VPN 게이트웨이의 크기를 높이면 높은 CPU로 인해 연결이 끊어진 것을 확인할 수 있습니다. Advisor는이 문제를 사전에 해결 하기 위한 권장 사항을 제공 합니다. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>로드 처리량, 데이터 압축 및 쿼리 성능을 최대화하기 위해 로드할 때 일괄 처리 크기를 늘립니다.

Advisor는 데이터베이스에 로드할 때 일괄 처리 크기를 늘려 로드 성능 및 처리량을 늘릴 수 있는지 여부를 검색 합니다. COPY 문을 사용 하는 것을 고려할 수 있습니다. COPY 문을 사용할 수 없는 경우 SQLBulkCopy API 또는 BCP와 같은 로드 유틸리티를 사용할 때 일괄 처리 크기를 늘려야 합니다. 좋은 일반적인 규칙은 10만에서 100만 행 사이의 일괄 처리 크기를 사용 하는 것입니다. 일괄 처리 크기를 늘리면 로드 처리량, 데이터 압축 및 쿼리 성능이 향상 됩니다.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>로드할 때 대기 시간을 최소화 하기 위해 동일한 지역에 저장소 계정 배치

Advisor는 SQL 풀과 다른 지역에서 로드 하 고 있는지 검색 합니다. 데이터를 로드할 때 대기 시간을 최소화 하기 위해 SQL 풀과 동일한 지역에 있는 저장소 계정에서 로드 하는 것이 좋습니다. 이렇게 변경 하면 대기 시간을 최소화 하 고 로드 성능을 향상 시킬 수 있습니다.

## <a name="use-a-supported-kubernetes-version"></a>지원 되는 Kubernetes 버전 사용

Advisor가 지원 되지 않는 버전의 Kubernetes를 검색 합니다.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Database for MariaDB 서버의 성능 최적화

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>CPU 병목 상태를 사용 하 여 Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Database for MariaDB 서버의 CPU 압력을 수정 합니다.
장기간 CPU 사용률이 높으면 작업에 쿼리 성능이 저하 될 수 있습니다. CPU 크기를 높이면 데이터베이스 쿼리의 런타임 최적화 및 전반적인 성능 향상에 도움이 됩니다. Advisor는 CPU 제한 작업을 실행할 가능성이 높은 CPU 사용률이 높은 서버를 식별 하 고 계산 크기를 조정 하는 것이 좋습니다.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Database for MariaDB 서버에서 메모리 제약 조건을 줄이거나 메모리 액세스에 최적화 된 SKU로 이동 합니다.
낮은 캐시 적중률으로 인해 쿼리 성능이 느려지고 IOPS가 증가할 수 있습니다. 이 문제는 잘못 된 쿼리 계획 또는 메모리 집약적 작업으로 인해 발생할 수 있습니다. 쿼리 계획을 수정 하거나 Azure Database for PostgreSQL, Azure Database for MySQL 또는 Azure Database for MariaDB 서버의 [메모리를 늘려서](../postgresql/concepts-pricing-tiers.md) 데이터베이스 작업의 실행을 최적화 하는 데 도움이 됩니다. Azure Advisor는이 높은 버퍼 풀 변동의 영향을 받는 서버를 식별 합니다. 다음 작업 중 하나를 수행 하는 것이 좋습니다. 
- 쿼리 계획 수정
- 더 많은 메모리가 있는 SKU로 이동 
- 더 많은 IOPS를 얻기 위해 저장소 크기를 늘립니다.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>읽기/쓰기 작업에 대 한 읽기를 확장 하려면 Azure Database for MySQL 또는 Azure Database for PostgreSQL 복제본 읽기를 사용 합니다.
Advisor는 지난 7 일간 서버에서 쓰기에 대 한 읽기 비율 같은 작업 기반 추론을 사용 하 여 읽기 집약적인 작업을 식별 합니다. 읽기/쓰기 비율이 높은 Azure Database for PostgreSQL 또는 Azure Database for MySQL 리소스는 CPU 또는 메모리 경합이 발생할 수 있으며 쿼리 성능이 느려질 수 있습니다.  [복제본](../postgresql/howto-read-replicas-portal.md) 을 추가 하면 복제본 서버에 대 한 읽기를 확장 하 고 주 서버에서 CPU 또는 메모리 제약 조건을 방지할 수 있습니다. Advisor는 읽기 집약적인 작업을 사용 하는 서버를 식별 하 고 읽기 [복제본](../postgresql/concepts-read-replicas.md)을 추가 하 여 읽기   작업의 일부를 오프 로드 하는 것을 권장 합니다.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>연결 제약 조건을 방지 하기 위해 Azure Database for MySQL, Azure Database for PostgreSQL 또는 Azure Database for MariaDB 서버를 상위 SKU로 크기 조정
데이터베이스 서버에 대 한 새 연결은 메모리를 차지 합니다. 서버에 대 한 연결이 메모리의 [상한](../postgresql/concepts-limits.md) 으로 인해 실패 하는 경우 데이터베이스 서버의 성능이 저하 됩니다. Azure Advisor는 여러 연결 오류가 발생 하 여 실행 되는 서버를 식별 합니다. 다음 작업 중 하나를 수행 하 여 서버에 더 많은 메모리를 제공 하도록 서버 연결 제한을 업그레이드 하는 것이 좋습니다.
- 계산을 확장 합니다. 
- 코어 당 계산을 많이 사용 하는 메모리 액세스에 최적화 된 Sku를 사용 합니다.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>캐시와 응용 프로그램 성능을 개선 하기 위해 다른 크기나 SKU로 캐시 크기 조정

캐시 인스턴스는 높은 메모리 부족, 높은 서버 부하 또는 높은 네트워크 대역폭에서 실행 되지 않는 경우에 가장 잘 작동 합니다. 이러한 조건으로 인해 응답 하지 않거나 데이터 손실이 발생 하거나 사용할 수 없게 될 수 있습니다. Advisor는 이러한 조건에서 캐시 인스턴스를 식별 합니다. 다음 작업 중 하나를 수행 하는 것이 좋습니다.
- 모범 사례를 적용 하 여 메모리 부족, 서버 부하 또는 네트워크 대역폭을 줄입니다.
- 용량이 더 많은 다른 크기나 SKU로 크기를 조정 합니다.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>트래픽이 있는 지역을 Azure Cosmos DB 계정에 추가

Advisor는 현재 구성 되지 않은 지역에서 트래픽을 포함 하는 Azure Cosmos DB 계정을 검색 합니다. 해당 지역을 추가 하는 것이 좋습니다. 이렇게 하면 해당 지역에서 들어오는 요청에 대 한 대기 시간이 향상 되 고 지역 중단이 발생 한 경우 가용성이 보장 됩니다. [Azure Cosmos DB를 사용 하 여 글로벌 데이터 배포에 대해 자세히 알아보세요.](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>사용자 지정 포함 또는 제외 경로를 사용 하 여 Azure Cosmos DB 인덱싱 정책 구성

Advisor는 기본 인덱싱 정책을 사용 하는 Azure Cosmos DB 컨테이너를 식별 하지만 사용자 지정 인덱싱 정책의 이점을 누릴 수 있습니다. 이러한 결정은 워크 로드 패턴을 기반으로 합니다. 기본 인덱싱 정책은 모든 속성을 인덱싱합니다. 쿼리 필터에 사용 되는 명시적 포함 또는 제외 경로를 사용 하는 사용자 지정 인덱싱 정책은 인덱싱에 사용 된 RUs 및 저장소를 줄일 수 있습니다. [인덱스 정책 수정에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB 쿼리 페이지 크기 (MaxItemCount)를-1로 설정 

Azure Advisor 쿼리 페이지 크기 100를 사용 하는 Azure Cosmos DB 컨테이너를 식별 합니다. 빠른 검색을 위해 페이지 크기-1을 사용 하는 것이 좋습니다. [MaxItemCount에 대해 자세히 알아보세요.](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>클러스터 성능을 향상 시키기 위해 HBase 클러스터에서 가속 쓰기 기능을 사용 하는 것이 좋습니다.
Azure Advisor는 지난 7 일간 시스템 로그를 분석 하 고 클러스터에 다음 시나리오가 발생 했는지 확인 합니다.
1. 긴 WAL 동기화 대기 시간 
2. 많은 쓰기 요청 수(1시간에 avg_write_requests/second/node 1000건 초과가 3번 이상)

이러한 상태는 클러스터의 쓰기 대기 시간이 길다는 것을 나타냅니다. 이는 클러스터에서 많은 작업을 수행 했기 때문일 수 있습니다. 클러스터의 성능을 향상 시키기 위해 Azure HDInsight HBase에서 제공 하는 가속화 된 쓰기 기능을 활용 하는 것을 고려할 수 있습니다. HDInsight Apache HBase 클러스터의 가속 쓰기 기능은 클라우드 스토리지를 사용하는 대신 모든 RegionServer(작업자 노드)에 프리미엄 SSD 관리 디스크를 연결합니다. 따라서 애플리케이션의 쓰기 대기 시간이 단축되고 복원력이 향상됩니다. 이 기능에 대 한 자세한 내용을 보려면 자세히 [알아보세요](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes#how-to-enable-accelerated-writes-for-hbase-in-hdinsight) .

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>성능 향상을 위해 Azure 데이터 탐색기 테이블 캐시 기간 (정책) 검토 (미리 보기)
이 권장 사항은 구성된 캐시 기간(정책)을 초과하여 가장 많은 수의 쿼리를 포함하는 Azure Data Explorer 테이블을 표시합니다(캐시 외부 데이터에 액세스하는 쿼리 비율별로 상위 10개의 테이블이 표시됨). 클러스터의 성능을 향상시키기 위한 권장 조치: 이 테이블에 대한 쿼리를 필요한 최소 시간 범위(정의된 정책 내)로 제한합니다. 또는 전체 시간 범위의 데이터가 필요한 경우 캐시 기간을 권장 값으로 늘립니다.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>MySQL 임시 테이블 크기 조정을 최적화하여 성능 향상
Advisor 분석은 낮은 임시 테이블 매개 변수 설정으로 인해 MySQL 서버가 불필요 한 i/o 오버 헤드를 발생 시킬 수 있음을 나타냅니다. 이로 인해 불필요한 디스크 기반 트랜잭션이 발생하고 성능이 저하될 수 있습니다. 디스크 기반 트랜잭션 수를 줄이려면 'tmp_table_size' 및 'max_heap_table_size' 매개 변수 값을 늘리는 것이 좋습니다. [자세한 정보](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>서버 그룹에 데이터를 배포 하 여 노드 간에 작업 분산
Advisor는 데이터가 배포 되지 않았지만 코디네이터에 유지 되는 서버 그룹을 식별 합니다. 이를 기반으로 하는 관리자는 Citus (전체 Hyperscale) 혜택을 통해 서버 그룹의 작업자 노드에 데이터를 배포할 것을 권장 합니다. 이렇게 하면 서버 그룹에서 각 노드의 리소스를 활용 하 여 쿼리 성능을 향상 시킬 수 있습니다. [자세한 정보](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Advisor의 성능 권장 사항에 액세스 하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **성능** 탭을 선택 합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 안정성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
