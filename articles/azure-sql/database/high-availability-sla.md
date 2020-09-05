---
title: 고가용성
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance 서비스의 고가용성 기능 및 기능에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 08/12/2020
ms.openlocfilehash: 62dfa3214b86139a8f836b3d9bd72585653b7fa2
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88189933"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance에 대 한 고가용성
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 SQL Managed Instance에서 고가용성 아키텍처의 목표는 데이터베이스가 가동 중 이며 최소 99.99%의 시간 동안 실행 되도록 보장 하는 것입니다. 다른 계층의 특정 SLA에 대 한 자세한 내용은 유지 관리 작업 및 중단의 영향을 걱정 하지 않고 [Azure SQL Database 및 SQL Managed Instance에 대 한 sla](https://azure.microsoft.com/support/legal/sla/sql-database/)를 참조 하세요. Azure는 패치, 백업, Windows 및 Azure SQL 업그레이드와 같은 중요 한 서비스 작업 뿐만 아니라 기본 하드웨어, 소프트웨어, 네트워크 오류 등의 계획 되지 않은 이벤트도 자동으로 처리 합니다.  Azure SQL Database의 기본 데이터베이스가 패치 되거나 장애 조치 (failover) 되 면 앱에서 [재시도 논리](develop-overview.md#resiliency) 를 사용 하는 경우 가동 중지 시간이 눈에 띄지 않습니다. SQL Database 및 SQL Managed Instance는 데이터를 항상 사용할 수 있도록 하는 가장 중요 한 상황 에서도 신속 하 게 복구할 수 있습니다.

고가용성 솔루션은 커밋된 데이터가 오류로 인해 손실 되지 않도록 하 고, 유지 관리 작업이 작업에 영향을 주지 않으며, 데이터베이스가 소프트웨어 아키텍처에서 단일 실패 지점이 되지 않도록 설계 되었습니다. 데이터베이스를 업그레이드하거나 유지 관리하는 동안 워크로드를 중지해야 하는 유지 관리 기간이나 가동 중지 시간이 없습니다.

고가용성 아키텍처 모델에는 다음 두 가지가 있습니다.

- 계산 및 저장소의 분리를 기반으로 하는 **표준 가용성 모델** 입니다.  원격 저장소 계층의 고가용성 및 안정성을 기반으로 합니다. 이 아키텍처는 유지 관리 작업 중에 일부 성능 저하를 허용할 수 있는 예산 기반 비즈니스 응용 프로그램을 대상으로 합니다.
- 데이터베이스 엔진 프로세스의 클러스터를 기반으로 하는 **프리미엄 가용성 모델** 입니다. 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 항상 존재 한다는 사실에 의존 합니다. 이 아키텍처는 높은 IO 성능을 가진 중요 업무용 응용 프로그램을 대상으로 하며, 트랜잭션 속도는 유지 관리 작업 중에 작업에 대 한 최소 성능 영향을 보장 합니다.

SQL Database 및 SQL Managed Instance은 안정적인 최신 버전의 SQL Server 데이터베이스 엔진 및 Windows 운영 체제에서 실행 되며, 대부분의 사용자는 업그레이드가 지속적으로 수행 되는 것을 알 수 없습니다.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>기본, 표준 및 범용 서비스 계층 가용성

Basic, Standard 및 범용 서비스 계층에서는 서버 리스 및 프로 비전 된 계산에 대 한 표준 가용성 아키텍처를 활용 합니다. 다음 그림은 분리 된 계산 및 저장소 계층을 포함 하는 네 가지 노드를 보여 줍니다.

![컴퓨팅과 스토리지의 분리](./media/high-availability-sla/general-purpose-service-tier.png)

표준 가용성 모델에는 두 개의 계층이 있습니다.

- 프로세스를 실행 하 고 임시 및 캐시 된 데이터만 포함 하는 상태 비저장 계산 계층 ( `sqlservr.exe` 예: TempDB, 연결 된 SSD의 모델 데이터베이스 및 메모리에 계획 캐시, 버퍼 풀 및 columnstore 풀)을 포함 합니다. 이 상태 비저장 노드는 `sqlservr.exe` 노드의 상태를 초기화 하 고 제어 하며 필요한 경우 다른 노드로 장애 조치 (failover)를 수행 하는 Azure Service Fabric에 의해 작동 됩니다.
- Azure Blob 저장소에 저장 된 데이터베이스 파일 (.mdf/.ldf)이 포함 된 상태 저장 데이터 계층입니다. Azure blob storage에는 기본 제공 되는 데이터 가용성 및 중복성 기능이 있습니다. 프로세스가 충돌 하는 경우에도 데이터 파일에 있는 로그 파일 또는 페이지의 모든 레코드가 유지 되도록 보장 `sqlservr.exe` 합니다.

데이터베이스 엔진 또는 운영 체제가 업그레이드 되거나 오류가 검색 될 때마다 Azure Service Fabric는 `sqlservr.exe` 사용 가능한 용량이 충분 한 상태 비저장 프로세스를 다른 상태 비저장 계산 노드로 이동 합니다. Azure Blob storage의 데이터는 이동의 영향을 받지 않으며 데이터/로그 파일이 새로 초기화 된 프로세스에 연결 됩니다 `sqlservr.exe` . 이 프로세스는 99.99%의 가용성을 보장 하지만, 새 `sqlservr.exe` 프로세스가 콜드 캐시로 시작 되기 때문에 많은 워크 로드에서 성능 저하가 발생할 수 있습니다.

## <a name="premium-and-business-critical-service-tier-availability"></a>프리미엄 및 중요 비즈니스용 서비스 계층 가용성

프리미엄 및 중요 비즈니스용 서비스 계층은 단일 노드에 계산 리소스 ( `sqlservr.exe` 프로세스)와 저장소 (로컬에 연결 된 SSD)를 통합 하는 프리미엄 가용성 모델을 활용 합니다. 고가용성은 3 ~ 4 개 노드 클러스터를 만드는 추가 노드에 계산과 저장소를 복제 하 여 수행 됩니다.

![데이터베이스 엔진 노드의 클러스터](./media/high-availability-sla/business-critical-service-tier.png)

기본 데이터베이스 파일 (.mdf/.ldf)은 연결 된 SSD 저장소에 배치 되어 워크 로드에 매우 짧은 대기 시간 IO를 제공 합니다. 고가용성은 SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)와 비슷한 기술을 사용 하 여 구현 됩니다. 클러스터에는 읽기/쓰기 고객 작업에 액세스할 수 있는 단일 주 복제본과 데이터 복사본을 포함 하는 최대 3 개의 보조 복제본 (계산 및 저장소)이 포함 되어 있습니다. 주 노드는 계속 해 서 보조 노드에 대 한 변경 내용을 적용 하 고 각 트랜잭션을 커밋하기 전에 데이터를 하나 이상의 보조 복제본에 동기화 되도록 합니다. 이 프로세스를 통해 어떤 이유로 든 주 노드가 충돌 하는 경우 항상 완전히 동기화 된 노드는로 장애 조치 (failover) 됩니다. 장애 조치 (failover)는 Azure Service Fabric에 의해 시작 됩니다. 보조 복제본이 새 주 노드가 되 면 클러스터에 충분 한 노드 (쿼럼 집합)가 있는지 확인 하기 위해 다른 보조 복제본이 생성 됩니다. 장애 조치 (failover)가 완료 되 면 Azure SQL 연결이 자동으로 새 주 노드로 리디렉션됩니다.

추가 혜택으로 프리미엄 가용성 모델에는 읽기 전용 Azure SQL 연결을 보조 복제본 중 하나로 리디렉션하는 기능이 포함 되어 있습니다. 이 기능을 [읽기 확장](read-scale-out.md)이라고 합니다. 주 복제본에서 분석 워크 로드와 같은 읽기 전용 작업을 오프 로드 하기 위해 추가 요금 없이 100% 추가 계산 용량을 제공 합니다.

## <a name="hyperscale-service-tier-availability"></a>분산 서비스 계층 가용성

하이퍼 확장 서비스 계층 아키텍처는 [분산 함수 아키텍처](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture) 에 설명 되어 있으며 현재 SQL Managed Instance가 아닌 SQL Database에만 사용할 수 있습니다.

![하이퍼 확장 기능 아키텍처](./media/high-availability-sla/hyperscale-architecture.png)

Hyperscale의 가용성 모델에는 다음 4 개의 계층이 포함 됩니다.

- 프로세스를 실행 하 `sqlservr.exe` 고, 연결 된 SSD에서 포함 되지 않은 RBPEX cache, TempDB, model 데이터베이스 등과 같은 임시 및 캐시 된 데이터만 포함 하 고 메모리의 계획 캐시, 버퍼 풀 및 columnstore 풀을 포함 하는 상태 비저장 계산 계층입니다. 이 상태 비저장 계층에는 기본 계산 복제본과 장애 조치 (failover) 대상으로 사용할 수 있는 선택적 개수의 보조 계산 복제본이 포함 됩니다.
- 페이지 서버에서 구성 된 상태 비저장 저장소 계층입니다. 이 계층은 `sqlservr.exe` 계산 복제본에서 실행 되는 프로세스에 대 한 분산 저장소 엔진입니다. 각 페이지 서버에는 연결 된 SSD의 RBPEX cache 및 메모리에 캐시 된 데이터 페이지와 같은 임시 및 캐시 된 데이터만 포함 됩니다. 각 페이지 서버에는 부하 분산, 중복성 및 고가용성을 제공 하기 위해 활성-활성 구성의 쌍을 이루는 페이지 서버가 있습니다.
- 로그 서비스 프로세스를 실행 하는 계산 노드, 트랜잭션 로그 방문 영역 및 트랜잭션 로그 장기 저장소로 구성 된 상태 저장 트랜잭션 로그 저장소 계층입니다. 방문 영역 및 장기 저장소는 트랜잭션 로그에 대 한 가용성 및 [중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 을 제공 하는 Azure Storage을 사용 하 여 커밋된 트랜잭션에 대 한 데이터 내 구성을 보장 합니다.
- Azure Storage에 저장 되 고 페이지 서버에서 업데이트 되는 데이터베이스 파일 (.mdf/.ndf)이 포함 된 상태 저장 데이터 저장소 계층입니다. 이 계층은 Azure Storage의 데이터 가용성 및 [중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 기능을 사용 합니다. 이를 통해 데이터 파일의 모든 페이지가 하이퍼 확장 아키텍처 충돌의 다른 계층에 있는 프로세스 또는 계산 노드가 실패 하는 경우에도 유지 됩니다.

모든 하이퍼 규모 계층의 계산 노드는 Azure Service Fabric에서 실행 되며, 각 노드의 상태를 제어 하 고 필요에 따라 사용 가능한 정상 노드로 장애 조치 (failover)를 수행 합니다.

하이퍼 규모의 고가용성에 대 한 자세한 내용은 [hyperscale의 데이터베이스 고가용성](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)을 참조 하세요.

## <a name="zone-redundant-configuration"></a>영역 중복 구성

기본적으로 프리미엄 가용성 모델에 대 한 노드 클러스터는 동일한 데이터 센터에 만들어집니다. [Azure 가용성 영역](../../availability-zones/az-overview.md)도입 된 SQL Database 중요 비즈니스용 데이터베이스의 여러 복제본을 동일한 지역의 다른 가용성 영역에 저장할 수 있습니다. 단일 실패 지점을 제거하기 위해 제어 링은 세 개의 GW(게이트웨이 링)로 여러 영역에 걸쳐 복제됩니다. 특정 게이트웨이 링에 대한 라우팅은 [ATM(Azure Traffic Manager)](../../traffic-manager/traffic-manager-overview.md)에서 제어됩니다. 프리미엄 또는 중요 비즈니스용 서비스 계층의 영역 중복 구성은 추가 데이터베이스 중복성을 만들지 않으므로 추가 비용 없이 사용 하도록 설정할 수 있습니다. 영역 중복 구성을 선택 하면 응용 프로그램 논리를 변경 하지 않고도 심각한 데이터 센터 중단을 포함 하 여 훨씬 더 큰 오류 집합에 대해 프리미엄 또는 중요 비즈니스용 데이터베이스를 탄력적으로 복원할 수 있습니다. 기존 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 풀을 영역 중복 구성으로 변환할 수도 있습니다.

영역 중복 데이터베이스에는 서로 다른 데이터 센터의 복제본이 있기 때문에 네트워크 대기 시간이 늘어나면 커밋 시간이 늘어날 수 있으므로 일부 OLTP 작업의 성능에 영향을 줄 수 있습니다. 언제든지 영역 중복 설정을 사용하지 않도록 설정하여 단일 영역 구성으로 돌아갈 수 있습니다. 이 프로세스는 일반 서비스 계층 업그레이드와 비슷한 온라인 작업입니다. 프로세스가 완료되면 데이터베이스 또는 풀이 영역 중복 링에서 단일 영역 링으로 또는 그 반대로 마이그레이션됩니다.

> [!IMPORTANT]
> 영역 중복 데이터베이스 및 탄력적 풀은 현재 선택 영역에서 프리미엄 및 중요 비즈니스용 서비스 계층 에서만 지원 됩니다. 중요 비즈니스용 계층을 사용할 때 영역 중복 구성은 Gen5 계산 하드웨어를 선택한 경우에만 사용할 수 있습니다. 영역 중복 데이터베이스를 지 원하는 지역에 대 한 최신 정보는 [지역별 서비스 지원](../../availability-zones/az-region.md)을 참조 하세요.

> [!NOTE]
> 이 기능은 SQL Managed Instance에서 사용할 수 없습니다.

다음 다이어그램에서는 고가용성 아키텍처의 영역 중복 버전을 보여 줍니다.

![고가용성 아키텍처 영역 중복](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>ADR(가속 데이터베이스 복구)

[ADR (가속화 된 데이터베이스 복구)](../accelerated-database-recovery.md) 는 특히 장기 실행 트랜잭션이 있을 때 데이터베이스 가용성을 크게 향상 시키는 새로운 데이터베이스 엔진 기능입니다. ADR는 현재 Azure SQL Database, Azure SQL Managed Instance 및 Azure SQL Data Warehouse에 사용할 수 있습니다.

## <a name="testing-application-fault-resiliency"></a>응용 프로그램 오류 복원 력 테스트

고가용성은 데이터베이스 응용 프로그램에 대해 투명 하 게 작동 하는 SQL Database 및 SQL Managed Instance 플랫폼의 기본 부분입니다. 그러나 계획 되거나 계획 되지 않은 이벤트 중에 시작 된 자동 장애 조치 (failover) 작업이 프로덕션 환경에 배포 되기 전에 응용 프로그램에 영향을 주는지 확인 하는 것을 알 수 있습니다. 특수 API를 호출 하 여 데이터베이스, 탄력적 풀 또는 관리 되는 인스턴스를 다시 시작 하 여 장애 조치 (failover)를 수동으로 트리거할 수 있습니다. 영역 중복 데이터베이스 또는 탄력적 풀의 경우 API 호출로 인해 이전 주 데이터베이스의 가용성 영역과 다른 가용성 영역에서 새 주 데이터베이스로 클라이언트 연결이 리디렉션됩니다. 따라서 장애 조치 (failover)가 기존 데이터베이스 세션에 미치는 영향을 테스트 하는 것 외에도 네트워크 지연 시간이 변경 되어 종단 간 성능이 변경 되는지 확인할 수 있습니다. 다시 시작 작업은 개입 하지 않으며 많은 수의 플랫폼에서 스트레스를 발생 시킬 수 있기 때문에 각 데이터베이스, 탄력적 풀 또는 관리 되는 인스턴스에 대해 30 분 마다 하나의 장애 조치 (failover) 호출만 허용 됩니다.

장애 조치 (failover)는 PowerShell, REST API 또는 Azure CLI를 사용 하 여 시작할 수 있습니다.

|배포 유형|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|데이터베이스|[AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover)|[데이터베이스 장애 조치](/rest/api/sql/databases(failover)/failover/)|[az rest](https://docs.microsoft.com/cli/azure/reference-index#az-rest) 를 사용 하 여에서 REST API 호출을 호출할 수 있습니다 Azure CLI|
|탄력적 풀|[AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[탄력적 풀 장애 조치 (failover)](/rest/api/sql/elasticpools(failover)/failover/)|[az rest](https://docs.microsoft.com/cli/azure/reference-index#az-rest) 를 사용 하 여에서 REST API 호출을 호출할 수 있습니다 Azure CLI|
|관리되는 인스턴스|[AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[관리 되는 인스턴스-장애 조치](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi 장애 조치](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> 장애 조치 (Failover) 명령은 Hyperscale 데이터베이스의 읽기 가능한 보조 복제본에 사용할 수 없습니다.

## <a name="conclusion"></a>결론

Azure SQL Database 및 Azure SQL Managed Instance 기능은 Azure 플랫폼과 긴밀 하 게 통합 되는 기본 제공 고가용성 솔루션입니다. 이는 장애 검색 및 복구에 대 한 Service Fabric, 데이터 가용성 영역 보호를 위한 Azure Blob storage, 높은 내결함성 (Azure SQL Managed Instance 아직 적용 되지 않은 문서에서 설명한 대로)에 따라 달라 집니다. 또한 SQL Database 및 SQL Managed Instance는 복제 및 장애 조치 (failover)를 위해 SQL Server 인스턴스에서 Always On 가용성 그룹 기술을 활용 합니다. 이러한 기술을 조합 하 여 응용 프로그램에서 혼합 된 저장소 모델의 이점을 완전히 실현 하 고 가장 까다로운 Sla를 지원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 가용성 영역](../../availability-zones/az-overview.md) 알아보기
- [Service Fabric](../../service-fabric/service-fabric-overview.md) 에 대해 알아보기
- [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) 알아보기
- [SQL Managed Instance에서 수동 장애 조치 (failover)를 시작 하는 방법을](../managed-instance/user-initiated-failover.md) 알아봅니다.
- 고가용성 및 재해 복구의 추가 옵션은 [비즈니스 연속성](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조하세요.
