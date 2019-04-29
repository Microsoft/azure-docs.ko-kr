---
title: 장애 조치(failover) 그룹 - Azure SQL Database | Microsoft Docs
description: 자동 장애 조치(failover) 그룹은 관리되는 인스턴스의 모든 데이터베이스 또는 SQL Database 서버에서 데이터베이스 그룹의 복제 및 자동/조정된 를 관리할 수 있도록 하는 SQL Database 기능입니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: f382cc547640969f934b94405b635c9e84f10791
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417361"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치(failover)를 사용할 수 있습니다.

자동 장애 조치(failover) 그룹은 Managed Instance의 모든 데이터베이스 또는 SQL Database 서버에 있는 데이터베이스 그룹을 다른 Azure 지역으로 복제 및 장애 조치(failover)하는 작업을 관리할 수 있도록 하는 SQL Database 기능입니다(현재 Managed Instance에 대해 공개 미리 보기로 제공되고 있음). [활성 지역 복제](sql-database-active-geo-replication.md)와 동일한 기본 기술을 사용합니다. 수동으로 장애 조치(failover)를 시작하거나, 사용자 정의 정책에 따라 SQL Database 서비스에 위임할 수 있습니다. 사용자 정의 정책 옵션을 사용하면 치명적인 오류 또는 계획되지 않은 다른 이벤트가 발생하여 주 지역에서 SQL Database 서비스의 가용성이 완전히 또는 부분적으로 상실될 경우 보조 지역에서 여러 관련 데이터베이스를 자동으로 복구할 수 있습니다. 또한 읽을 수 있는 보조 데이터베이스를 사용하여 읽기 전용 쿼리 워크로드를 오프로드할 수 있습니다. 자동 장애 조치 그룹에 여러 데이터베이스가 포함되기 때문에 주 서버에서 이러한 데이터베이스를 구성해야 합니다. 장애 조치 그룹에서 데이터베이스에 대한 기본 및 보조 서버는 모두 동일한 구독에 위치해야 합니다. 자동 장애 조치 그룹은 그룹의 모든 데이터베이스를 다른 지역에 있는 하나의 보조 서버로만 복제할 수 있도록 지원합니다.

> [!NOTE]
> SQL Database 서버에서 단일 또는 풀링된 데이터베이스를 사용 중이며 동일하거나 다른 지역에 여러 개의 보조 데이터베이스를 만들려는 경우 [활성 지역 복제](sql-database-active-geo-replication.md)를 사용합니다.

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 그룹 내 데이터베이스 중 하나 이상에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 또한 자동 장애 조치 그룹은 장애 조치하는 동안 변경되지 않는 읽기-쓰기 및 읽기 전용 수신기 끝점을 제공합니다. 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계 없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치(failover)가 완료되면 엔드포인트를 새 지역으로 리디렉션하도록 DNS 레코드가 자동으로 업데이트됩니다. 특정 RPO 및 RTO 데이터에 대해서는 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 SQL Database 서버 또는 관리되는 인스턴스의 데이터베이스에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 다음을 사용하여 자동 장애 조치(failover) 그룹을 관리할 수 있습니다.

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [PowerShell: 장애 조치(failover) 그룹](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- [REST API: 장애 조치(failover) 그룹](https://docs.microsoft.com/rest/api/sql/failovergroups)

장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 종단 간 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 저장소 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="auto-failover-group-terminology-and-capabilities"></a>자동 장애 조치(failover) 그룹 용어 및 기능

- **장애 조치 그룹 (안개)**

  장애 조치(failover) 그룹은 주 지역의 중단으로 인해 주 데이터베이스를 모두 또는 일부 사용할 수 없게 될 경우 한 단위로 다른 지역에 장애 조치(failover)될 수 있는, 관리되는 단일 인스턴스 내에 있거나 단일 SQL Database 서버에 의해 관리되는 데이터베이스 그룹입니다.

  - **SQL Database 서버**

     SQL Database 서버를 사용하면 단일 SQL Database 서버의 사용자 데이터베이스를 일부 또는 모두 장애 조치(failover) 그룹에 배치할 수 있습니다. 또한 SQL Database 서버는 단일 SQL Database 서버에서 여러 개의 장애 조치(failover) 그룹을 지원합니다.

  - **관리되는 인스턴스**
  
     관리되는 인스턴스를 사용하면 장애 조치(failover) 그룹에 Managed Instance의 모든 사용자 데이터베이스가 포함되므로 Managed Instance는 단일 장애 조치(failover) 그룹만 지원합니다.

- **주**

  장애 조치(failover) 그룹의 주 데이터베이스를 호스트하는 SQL Database 서버 또는 Managed Instance입니다.

- **보조**

  장애 조치(failover) 그룹의 보조 데이터베이스를 호스트하는 SQL Database 서버 또는 Managed Instance입니다. 보조 서버는 주 서버와 동일한 지역에 있을 수 없습니다.

- **장애 조치(failover) 그룹에 단일 데이터베이스 추가**

  동일한 SQL Database 서버에 있는 여러 개의 단일 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 장애 조치(failover) 그룹에 단일 데이터베이스를 추가하면 동일한 버전과 컴퓨팅 크기를 사용하는 보조 데이터베이스가 보조 서버에 자동으로 만들어집니다.  장애 조치(failover) 그룹을 만들 때 해당 서버를 지정했습니다. 보조 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 새 보조 데이터베이스가 보조 서버에 만들어집니다.
  
> [!IMPORTANT]
  > Managed Instance에서 모든 사용자 데이터베이스가 복제됩니다. 장애 조치(failover) 그룹에서 복제를 위해 사용자 데이터베이스 하위 집합을 선택할 수 없습니다.

- **장애 조치(failover) 그룹에 탄력적 풀의 데이터베이스 추가**

  탄력적 풀에 있는 전체 또는 여러 개의 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 탄력적 풀에 주 데이터베이스가 있는 경우 동일한 이름을 가진 보조 데이터베이스가 탄력적 풀에 자동으로 만들어집니다(보조 풀). 장애 조치(failover) 그룹에서 만들 보조 데이터베이스를 호스트하기에 충분한 여유 용량과 정확히 동일한 이름을 가진 탄력적 풀이 보조 서버에 있는지 확인해야 합니다. 보조 풀에 보조 데이터베이스가 이미 있는 데이터베이스를 풀에 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 보조 데이터베이스가 서버에 이미 있는 데이터베이스를 추가하는 경우 새 보조 데이터베이스가 보조 풀에 만들어집니다.
  
  - **장애 조치 그룹 읽기-쓰기 수신기**

  현재 주 데이터베이스의 URL을 가리키는 DNS CNAME 레코드가 생성됩니다. 장애 조치 후 주 데이터베이스가 변경되면 읽기-쓰기 SQL 애플리케이션이 해당 주 데이터베이스에 투명하게 다시 연결할 수 있습니다.

  - **읽기/쓰기 수신기에 대한 SQL Database 서버 DNS CNAME 레코드**

     SQL Database 서버에서 현재 주 데이터베이스의 URL을 가리키는 장애 조치(failover) 그룹에 대한 DNS CNAME 레코드가 `<fog-name>.database.windows.net`으로 생성됩니다.

  - **읽기/쓰기 수신기에 대한 Managed Instance DNS CNAME 레코드**

     Managed Instance에서 현재 주 데이터베이스의 URL을 가리키는 장애 조치(failover) 그룹에 대한 DNS CNAME 레코드가 `<fog-name>.zone_id.database.windows.net`으로 생성됩니다.

- **장애 조치 그룹 읽기 전용 수신기**

  보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기를 가리키는 DNS CNAME 레코드가 생성됩니다. 읽기 전용 SQL 애플리케이션이 지정된 부하 분산 규칙을 사용하여 보조 데이터베이스에 투명하게 연결할 수 있도록 합니다.

  - **읽기 전용 수신기에 대한 SQL Database 서버 DNS CNAME 레코드**

     SQL Database 서버에서 보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기에 대한 DNS CNAME 레코드가 `'.secondary.database.windows.net`으로 생성됩니다.

  - **읽기 전용 수신기에 대한 Managed Instance DNS CNAME 레코드**

     Managed Instance에서 보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기에 대한 DNS CNAME 레코드가 `<fog-name>.zone_id.database.windows.net`으로 생성됩니다.

- **자동 장애 조치 정책**

  기본적으로 장애 조치(failover) 그룹은 자동 장애 조치(failover) 정책을 사용하여 구성됩니다. SQL Database 서비스는 오류가 검색되고 유예 기간이 만료된 후에 장애 조치(failover)를 트리거합니다. 영향의 규모로 인해 [SQL Database 서비스의 기본 제공 고가용성 인프라](sql-database-high-availability.md)를 통해 중단을 완화할 수 없음을 시스템에서 확인해야 합니다. 애플리케이션에서 장애 조치 워크플로를 제어하려는 경우 자동 장애 조치를 해제할 수 있습니다.

- **읽기 전용 장애 조치 정책**

  기본적으로 읽기 전용 수신기에 대한 장애 조치가 사용되지 않습니다. 이렇게 하면 보조 서버가 오프라인 상태일 때 주 서버의 성능이 영향을 받지 않습니다. 그러나 보조 서버가 복구될 때까지 읽기 전용 세션이 연결할 수 없음을 의미합니다. 읽기 전용 세션의 가동 중지 시간을 허용할 수 없고 주 데이터베이스의 성능이 잠재적으로 저하되더라도 읽기 전용 및 읽기/쓰기 트래픽 둘 다에 주 데이터베이스를 일시적으로 사용해도 되는 경우, 읽기 전용 수신기에 대해 장애 조치(failover)를 사용하도록 설정할 수 있습니다. 이 경우, 보조 데이터베이스를 사용할 수 없으면 읽기 전용 트래픽이 주 데이터베이스로 자동으로 리디렉션됩니다.

- **계획된 장애 조치**

   계획된 장애 조치(failover)는 보조 역할이 주 역할로 전환되기 전에 주 데이터베이스와 보조 데이터베이스 간에 전체 동기화를 수행합니다. 이렇게 하면 데이터 손실이 발생하지 않습니다. 계획된 장애 조치(failover)는 다음과 같은 시나리오에서 사용합니다.

  - 데이터 손실이 허용되지 않을 때 프로덕션에서 DR(재해 복구) 드릴 수행
  - 데이터베이스를 다른 지역으로 재배치
  - 중단이 완화된 후 데이터베이스를 주 지역으로 반환합니다(장애 복구(failback)).

- **계획되지 않은 장애 조치**

   계획되지 않은 장애 조치 또는 강제 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 보조 역할을 주 역할로 즉시 전환합니다. 이 작업으로 인해 데이터가 손실됩니다. 계획되지 않은 장애 조치(failover)는 주 데이터베이스에 액세스할 수 없는 중단 중에 복구 방법으로 사용됩니다. 원래 주 데이터베이스가 다시 온라인 상태가 되면 동기화 없이 자동으로 다시 연결되고 새 보조 데이터베이스가 됩니다.

- **수동 장애 조치**

  자동 장애 조치 구성에 관계 없이 언제든지 장애 조치를 수동으로 시작할 수 있습니다. 자동 장애 조치(failover) 정책이 구성되지 않은 경우 장애 조치 그룹의 데이터베이스를 보조 데이터베이스에 복구하려면 수동 장애 조치(failover)를 수행해야 합니다. 강제 장애 조치 또는 친숙한 장애 조치(전체 데이터 동기화 사용)를 시작할 수 있습니다. 친숙한 장애 조치를 사용하면 주 데이터베이스를 보조 지역에 재배치할 수 있습니다. 장애 조치(failover)가 완료되면 새로운 주 데이터베이스에 대한 연결을 보장하기 위해 DNS 레코드가 자동으로 업데이트됩니다.

- **데이터 손실이 있는 유예 기간**

  주 및 보조 데이터베이스가 비동기 복제를 사용하여 동기화되기 때문에 장애 조치로 인해 데이터가 손실될 수 있습니다. 애플리케이션의 데이터 손실 허용 오차를 반영하도록 자동 장애 조치 정책을 사용자 지정할 수 있습니다. **GracePeriodWithDataLossHours**를 구성하여 데이터 손실이 발생할 수 있는 장애 조치를 시작하기 전에 시스템에서 대기하는 시간을 제어할 수 있습니다.

- **여러 장애 조치 그룹**

  동일한 서버 쌍에 대해 여러 장애 조치 그룹을 구성하여 장애 조치의 크기를 제어할 수 있습니다. 각 그룹은 독립적으로 장애 조치됩니다. 다중 테넌트 애플리케이션에서 탄력적 풀을 사용하는 경우 이 기능을 사용하여 각 풀에 주 및 보조 데이터베이스를 혼합할 수 있습니다. 이렇게 하면 테넌트의 절반에 대해서만 가동 중단에 따른 영향을 줄일 수 있습니다.

  > [!IMPORTANT]
  > Managed Instance는 여러 개의 장애 조치(failover) 그룹을 지원하지 않습니다.
  
## <a name="permissions"></a>권한
장애 조치 그룹에 대 한 권한을 통해 관리 됩니다 [역할 기반 액세스 제어 (RBAC)](../role-based-access-control/overview.md)합니다. 합니다 [SQL Server 참여자](../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할에 장애 조치 그룹을 관리 하는 데 필요한 모든 권한이 있습니다. 

### <a name="create-failover-group"></a>장애 조치 그룹 만들기
장애 조치 그룹을 만들려면 RBAC 쓰기 권한을 모두 기본 및 보조 서버를 장애 조치 그룹의 모든 데이터베이스에 필요 합니다. 관리 되는 인스턴스에 대 한 기본 및 보조 관리 되는 인스턴스는 모두에 대 한 RBAC 쓰기 액세스 필요 하지만 개별 데이터베이스에 대 한 권한을 관련이 없는 개별 관리 되는 인스턴스 데이터베이스에 추가 하거나 장애 조치 그룹에서 제거할 수 있으므로. 

### <a name="update-a-failover-group"></a>장애 조치 그룹 업데이트
RBAC를 해야 장애 조치 그룹을 업데이트 하려면 장애 조치 그룹 및 현재 주 서버 또는 관리 되는 인스턴스에서 모든 데이터베이스에 대 한 쓰기 액세스 합니다.  

### <a name="failover-a-failover-group"></a>장애 조치 그룹을 장애 조치
장애 조치 그룹을 장애 조치할를 새 주 서버에서 장애 조치 그룹에 대 한 RBAC 쓰기 액세스 필요 하거나 관리 되는 인스턴스. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀로 장애 조치(failover) 그룹을 사용하는 방법의 모범 사례

자동 장애 조치(failover) 그룹은 주 SQL Database 서버에서 구성되어야 하며 다른 Azure 지역의 보조 SQL Database 서버에 연결됩니다.  그룹에는 이러한 서버에 있는 데이터베이스가 모두 또는 일부 포함될 수 있습니다. 다음 다이어그램은 여러 데이터베이스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group.png)

비즈니스 연속성을 고려하여 서비스를 설계하는 경우 다음과 같은 일반 지침을 따르세요.

- **하나 이상의 장애 조치 그룹을 사용하여 여러 데이터베이스의 장애 조치 관리**

  다른 하위 지역의 두 서버(기본 및 보조 서버) 간에 하나 이상의 장애 조치 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다. 장애 조치(failover) 그룹을 사용하는 경우 주 데이터베이스와 서비스 목표가 동일한 지역 보조 데이터베이스가 작성됩니다. 장애 조치(failover) 그룹에 기존의 지역 복제 관계를 추가하는 경우 보조 데이터베이스가 주 데이터베이스와 동일한 서비스 계층 및 계산 크기로 구성되었는지 확인합니다.

- **OLTP 워크로드에 읽기-쓰기 수신기 사용**

  OLTP 작업을 수행할 때 `<fog-name>.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새 주 데이터베이스로 리디렉션됩니다.

- **읽기 전용 워크로드에 읽기 전용 수신기 사용**

  특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 읽기 전용 세션의 경우 `<fog-name>.secondary.database.windows.net`을 서버 URL로 사용하면 자동으로 보조 데이터베이스에 연결됩니다. 또한 **ApplicationIntent=ReadOnly**를 사용하여 연결 문자열 읽기 의도를 표시하는 것이 좋습니다.

- **성능 저하에 대한 대비**

  SQL 장애 조치 결정은 애플리케이션의 나머지 부분 또는 사용되는 다른 서비스에서 독립적입니다. 애플리케이션은 한 지역의 일부 구성 요소 및 나머지 지역의 일부 구성 요소와 "혼합"될 수 있습니다. 성능 저하를 방지하려면 DR 지역에서 중복 애플리케이션 배포를 확인하고 이러한 [네트워크 보안 지침](#failover-groups-and-network-security)을 따르세요.

  > [!NOTE]
  > DR 지역의 애플리케이션이 다른 연결 문자열을 사용하지 않아도 됩니다.  

- **데이터 손실에 대비**

  중단이 검색되면 SQL은 **GracePeriodWithDataLossHours**에서 지정한 기간 동안 대기합니다. 기본값은 1시간입니다. 데이터 손실을 방지하려는 경우 **GracePeriodWithDataLossHours**를 24시간과 같은 충분히 큰 숫자로 설정해야 합니다. 수동 그룹 장애 조치(failover)를 사용하여 보조 데이터베이스에서 주 데이터베이스로 장애 복구할 수 있습니다.

> [!IMPORTANT]
> 지역 복제를 사용하는 800개 이하의 DTU 및 250개 초과의 데이터베이스가 있는 탄력적 풀에는 계획된 장애 조치 지연 및 성능 저하가 포함된 문제가 발생할 수 있습니다.  이러한 문제는 지역에서 복제 엔드포인트가 지리적으로 광범위하게 분리되어 있거나 여러 보조 엔드포인트가 각 데이터베이스에 대해 사용되는 경우 쓰기 집약적 작업에 발생할 가능성이 높습니다.  이러한 문제의 증상은 지역에서 복제 지연이 시간에 따라 증가하는 경우에 표시됩니다.  이러한 지연은 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 사용하여 모니터링할 수 있습니다.  이러한 문제가 발생하는 경우 완화 방안에는 풀 DTU의 수를 늘리거나 동일한 풀에 지역에서 복제된 데이터베이스 수를 줄이는 방법이 있습니다.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Managed Instance로 장애 조치(failover) 그룹을 사용하는 방법의 모범 사례

자동 장애 조치(failover) 그룹은 주 인스턴스에서 구성되어야 하며 다른 Azure 지역의 보조 인스턴스에 연결됩니다.  인스턴스의 모든 데이터베이스가 보조 인스턴스에 복제됩니다. 다음 다이어그램은 관리되는 인스턴스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Managed Instance에 대한 자동 장애 조치(failover) 그룹은 공개 미리 보기로 제공되고 있습니다.

애플리케이션이 Managed Instance를 데이터 계층으로 사용하는 경우 비즈니스 연속성을 설계할 때 다음과 같은 일반 지침을 따르세요.

- **주 인스턴스와 동일한 DNS 영역에 보조 인스턴스 만들기**

  새 인스턴스를 만들면 고유 ID가 DNS 영역으로 자동 생성되고 인스턴스 DNS 이름에 포함됩니다. 이 인스턴스의 다중 도메인(SAN) 인증서는 SAN 필드를 사용하여 `zone_id.database.windows.net` 형식으로 프로비전됩니다. 이 인증서를 사용하여 동일한 DNS 영역의 인스턴스에 클라이언트 연결을 인증할 수 있습니다. 장애 조치(failover) 후 주 인스턴스에 대한 무중단 연결을 보장하려면 주 인스턴스와 보조 인스턴스가 동일한 DNS 영역에 있어야 합니다. 애플리케이션이 프로덕션 배포에 사용할 준비가 되면 다른 지역에 보조 인스턴스를 만들고 주 인스턴스와 DNS 영역을 공유하는지 확인합니다. 이렇게 하려면 Azure Portal, PowerShell 또는 REST API를 사용하여 `DNS Zone Partner` 선택적 매개 변수를 지정합니다.

  주 인스턴스와 동일한 DNS 영역에 보조 인스턴스를 만드는 방법에 대한 자세한 내용은 [Managed Instances(미리 보기)를 사용하여 장애 조치(failover) 그룹 관리](#powershell-managing-failover-groups-with-managed-instances-preview)를 참조하세요.

- **두 인스턴스 간에 복제 트래픽 사용**

  각 인스턴스가 자체 VNet에 격리되므로 이러한 VNet 간의 양방향 트래픽을 허용해야 합니다. [Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

- **장애 조치(failover) 그룹을 구성하여 전체 인스턴스의 장애 조치(failover) 관리**

  장애 조치(failover) 그룹은 인스턴스에 있는 모든 데이터베이스의 장애 조치(failover)를 관리합니다. 그룹을 만들면 인스턴스의 각 데이터베이스가 자동으로 보조 인스턴스에 지역 복제됩니다. 장애 조치(failover) 그룹을 사용하여 데이터베이스 하위 집합의 부분 장애 조치(failover)를 시작할 수 없습니다.

  > [!IMPORTANT]
  > 주 인스턴스에서 데이터베이스를 제거하면 지역 보조 인스턴스에서도 자동으로 삭제됩니다.

- **OLTP 워크로드에 읽기-쓰기 수신기 사용**

  OLTP 작업을 수행할 때 `<fog-name>.zone_id.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새로운 주 데이터베이스로 리디렉션됩니다. 보조 인스턴스는 주 인스턴스와 DNS 영역을 공유하므로 클라이언트 애플리케이션이 동일한 SAN 인증서를 사용하여 다시 연결할 수 있습니다.

- **읽기 전용 쿼리를 위해 지역 복제된 보조 데이터베이스에 직접 연결**

  특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 지역 복제된 보조 데이터베이스에 직접 연결하려는 경우 `server.secondary.zone_id.database.windows.net`을 서버 URL로 사용하면 지역 복제된 보조 데이터베이스에 직접 연결됩니다.

  > [!NOTE]
  > 특정 서비스 계층에서 Azure SQL Database는 [읽기 전용 복제본](sql-database-read-scale-out.md)을 사용하여 읽기 전용 복제본의 용량 및 연결 문자열의 `ApplicationIntent=ReadOnly` 매개 변수를 통해 읽기 전용 쿼리 워크로드를 부하를 분산할 수 있도록 지원합니다. 지역 복제된 보조 데이터베이스를 구성한 경우 이 기능을 사용하여 주 위치 또는 지역 복제된 위치에 있는 읽기 전용 복제본에 연결할 수 있습니다.
  > - 주 위치의 읽기 전용 복제본에 연결하려면 `<fog-name>.zone_id.database.windows.net`을 사용합니다.
  > - 보조 위치의 읽기 전용 복제본에 연결 하려면 사용 하 여 `<fog-name>.secondary.zone_id.database.windows.net`입니다.

- **성능 저하에 대한 대비**

  SQL 장애 조치 결정은 애플리케이션의 나머지 부분 또는 사용되는 다른 서비스에서 독립적입니다. 애플리케이션은 한 지역의 일부 구성 요소 및 나머지 지역의 일부 구성 요소와 "혼합"될 수 있습니다. 성능 저하를 방지하려면 DR 지역에서 중복 애플리케이션 배포를 확인하고 이러한 [네트워크 보안 지침](#failover-groups-and-network-security)을 따르세요.

- **데이터 손실에 대비**

  가동 중단이 감지되는 경우 SQL은 중요한 지식에 대한 데이터 손실이 없으면 읽기 쓰기 장애 조치를 자동으로 트리거합니다. 그렇지 않으면 `GracePeriodWithDataLossHours`에서 지정한 기간 동안 대기합니다. `GracePeriodWithDataLossHours`를 지정한 경우 데이터 손실을 준비합니다. 일반적으로 중단 시에 Azure에서는 가용성을 우선으로 합니다. 데이터 손실을 방지하려는 경우 GracePeriodWithDataLossHours를 24시간과 같은 충분히 큰 숫자로 설정해야 합니다.

  읽기/쓰기 수신기의 DNS 업데이트는 장애 조치(failover)가 시작된 후 즉시 발생합니다. 이 작업으로 인한 데이터 손실은 없습니다. 그러나 일반적인 조건에서 데이터베이스 역할을 전환하는 데 최대 5분 정도 걸릴 수 있습니다. 완료될 때까지 새로운 주 인스턴스의 일부 데이터베이스는 계속 읽기 전용입니다. PowerShell을 사용하여 장애 조치(failover)를 시작하는 경우 전체 작업이 동기입니다. Azure Portal을 사용하여 시작하는 경우 UI에 완료 상태가 표시됩니다. REST API를 사용하여 시작하는 경우, 표준 Azure Resource Manager의 폴링 메커니즘을 사용하여 완료되었는지 모니터링합니다.

  > [!IMPORTANT]
  > 수동 그룹 장애 조치(failover)를 사용하여 주 데이터베이스를 원래 위치로 다시 이동할 수 있습니다. 장애 조치(failover)를 유발한 중단이 완화되면 주 데이터베이스를 원래 위치로 이동할 수 있습니다. 이렇게 하려면 그룹의 수동 장애 조치(failover)를 시작해야 합니다.

## <a name="failover-groups-and-network-security"></a>장애 조치 그룹 및 네트워크 보안

일부 애플리케이션의 경우 보안 규칙에서는 특정 구성 요소 또는 VM, 웹 서비스 등과 같은 구성 요소로 데이터 계층에 대한 네트워크 액세스가 제한되어야 합니다. 이 요구 사항은 비즈니스 연속성 디자인에 대한 일부 과제 및 장애 조치 그룹의 사용량을 나타냅니다. 이러한 제한된 액세스를 구현하는 경우 다음 옵션을 고려해야 합니다.

### <a name="using-failover-groups-and-virtual-network-rules"></a>장애 조치 그룹 및 가상 네트워크 규칙 사용

[Virtual Network 서비스 엔드포인트 및 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 사용하여 SQL 데이터베이스에 대한 액세스를 제한하는 경우 각 Virtual Network 서비스 엔드포인트가 하나의 Azure 지역에만 적용됩니다. 엔드포인트를 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다. 따라서 동일한 지역에 배포된 클라이언트 애플리케이션만 주 데이터베이스에 연결할 수 있습니다. 장애 조치가 SQL 클라이언트 세션을 다른 (보조) 지역의 서버로 다시 라우팅하는 결과를 발생시키므로 해당 지역 외부에 있는 클라이언트에서 시작된 경우 이러한 세션에 실패합니다. 이런 이유로 참여하는 서버가 Virtual Network 규칙에 포함된 경우 자동 장애 조치 정책을 사용할 수 없습니다. 수동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. 보조 지역에서 애플리케이션(웹 서비스, 가상 머신 등) 프런트 엔드 구성 요소의 중복 복사본을 프로비전합니다.
2. 기본 및 보조 서버에 대한 [가상 네트워크 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 개별적으로 구성합니다.
3. [트래픽 관리자 구성을 사용하여 프런트 엔드 장애 조치](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)를 사용하도록 설정합니다.
4. 중단이 검색되면 수동 장애 조치(failover)를 시작합니다. 이 옵션은 프런트 엔드와 데이터 계층 간에 일관된 대기 시간을 필요로 하는 애플리케이션에 대해 최적화되고 프런트 엔드, 데이터 계층 또는 둘 모두가 중단의 영향을 받는 경우 복구를 지원합니다.

> [!NOTE]
> **읽기 전용 수신기**를 사용하여 읽기 전용 워크로드의 부하를 분산하는 경우 이 워크로드가 보조 데이터베이스에 연결할 수 있도록 보조 지역의 VM 또는 다른 리소스에서 실행되는지 확인합니다.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>장애 조치 그룹 및 SQL 데이터베이스 방화벽 규칙 사용

비즈니스 연속성 계획이 자동 장애 조치에서 그룹을 사용하는 장애 조치가 필요한 경우 기존 방화벽 규칙을 사용하여 SQL 데이터베이스에 대한 액세스를 제한할 수 있습니다.  자동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. [공용 IP를 만듭니다.](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [공용 부하 분산 장치를 만들고](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) 여기에 공용 IP를 할당합니다.
3. 프런트 엔드 구성 요소에 대한 [가상 네트워크 및 가상 머신을 만듭니다.](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers)
4. [네트워크 보안 그룹을 만들고](../virtual-network/security-overview.md) 인바운드 연결을 구성합니다.
5. 'Sql' [서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하여 아웃바운드 연결이 Azure SQL 데이터베이스에 대해 열려 있는지 확인합니다.
6. [SQL 데이터베이스 방화벽 규칙](sql-database-firewall-configure.md)을 만들어서 1단계에서 만든 공용 IP 주소에서 인바운드 트래픽을 허용합니다.

아웃바운드 액세스를 구성하는 방법 및 방화벽 규칙에서 사용할 IP에 대한 자세한 내용은 [부하 분산 장치 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md)을 참조하세요.

위의 구성에서는 자동 장애 조치가 프런트 엔드 구성 요소에서 연결을 차단하지 않는지 확인하고 애플리케이션이 프런트 엔드와 데이터 계층 간의 긴 대기 시간을 허용할 수 있다고 가정합니다.

> [!IMPORTANT]
> 지역 중단에 대한 비즈니스 연속성을 보장하기 위해 프런트 엔드 구성 요소와 데이터베이스 모두에 대한 지리적 복제를 확인해야 합니다.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Managed Instance와 해당 VNet 간에 지역 복제 사용

두 지역에 있는 주 관리되는 인스턴스와 보조 관리되는 인스턴스 간에 장애 조치(failover) 그룹을 설정하는 경우 각 인스턴스가 독립 VNet을 사용하여 격리됩니다. 이러한 VNet 간에 복제 트래픽을 허용하려면 다음 필수 조건을 충족하는지 확인합니다.

1. 두 개의 Managed Instance가 서로 다른 Azure 지역에 있어야 합니다.
2. 보조 인스턴스는 비어 있어야 합니다(사용자 데이터베이스 없음).
3. 주 Managed Instance와 보조 Managed Instance가 동일한 리소스 그룹에 있어야 합니다.
4. Managed Instance가 속해 있는 VNet이 [VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 통해 연결되어야 합니다. 전역 VNet 피어링은 지원되지 않습니다.
5. 두 개의 Managed Instance VNet에 겹치는 IP 주소가 있으면 안 됩니다.
6. 포트 5022, 범위 11000~12000이 다른 Managed Instance 서브넷의 연결에 대해 인바운드 및 아웃바운드로 열려 있도록 NSG(네트워크 보안 그룹)를 설정해야 합니다. 이는 인스턴스 간의 복제 트래픽을 허용하기 위한 것입니다.

    > [!IMPORTANT]
    > NSG 보안 규칙을 잘못 구성하면 데이터베이스 복사 작업이 중단됩니다.

7. 보조 인스턴스에서 DNS 영역 파트너를 구성해야 합니다. DNS 영역은 Managed Instance의 속성입니다. Managed Instance 이름 뒤, `.database.windows.net` 접두사 앞에 오는 호스트 이름의 일부를 나타냅니다. 각 VNet에서 첫 번째 Managed Instance를 만드는 동안 임의 문자열로 생성됩니다. 관리되는 인스턴스를 만든 후에는 DNS 영역을 수정할 수 없으며, 동일한 서브넷 내의 모든 Managed Instance가 동일한 DNS 영역 값을 공유합니다. Managed Instance 장애 조치(failover) 그룹 설정의 경우 주 Managed Instance와 보조 Managed Instance가 동일한 DNS 영역 값을 공유해야 합니다. 이렇게 하려면 보조 Managed Instance를 만들 때 DnsZonePartner 매개 변수를 지정합니다. DNS 영역 파트너 속성은 인스턴스 장애 조치(failover) 그룹을 공유할 Managed Instance를 정의합니다. DnsZonePartner의 입력으로 다른 관리되는 인스턴스의 리소스 ID를 전달하면 현재 생성 중인 Managed Instance가 파트너 Managed Instance의 동일한 DNS 영역 값을 상속 받습니다.

## <a name="upgrading-or-downgrading-a-primary-database"></a>주 데이터베이스 업그레이드 또는 다운그레이드

보조 데이터베이스와의 연결을 끊지 않고도 주 데이터베이스를 다른 계산 크기(동일한 서비스 계층 내, 범용 및 중요 비즈니스용 사이 아님)로 업그레이드하거나 다운그레이드할 수 있습니다. 업그레이드 하는 경우 모든 보조 데이터베이스를 먼저 업그레이드 한 다음 주 데이터베이스를 업그레이드 하는 것이 좋습니다. 으로 다운 그레이드 하는 경우 순서를 반대로: 주 데이터베이스를 먼저 다운 그레이드 하 고 모든 보조 데이터베이스를 다운 그레이드 합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

더 낮은 SKU에서 보조 흘려 보내면 보낼수록 않았고 다시 시드해야 업그레이드 또는 다운 그레이드 프로세스 중 문제를 방지 하려면 특히이 시퀀스를 사용 하는 것이 좋습니다. 또한 주에 대 한 모든 읽기 / 쓰기 워크 로드에 영향을 주는 대신 읽기 전용 기본 지정 하 여 문제를 방지할 수 있습니다. 

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. **sp_wait_for_database_copy_sync** 호출은 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송될 때까지 호출 스레드를 차단합니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. **sp_wait_for_database_copy_sync**는 그 범위가 특정 연속 복사 링크로 한정됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> **sp_wait_for_database_copy_sync**는 장애 조치 후 데이터 손실을 방지하지만 읽기 액세스를 위한 전체 동기화를 보장하지 않습니다. **sp_wait_for_database_copy_sync** 프로시저 호출로 인한 지연은 심각할 수 있으며 호출 시 트랜잭션 로그의 크기에 따라 달라집니다.

## <a name="failover-groups-and-point-in-time-restore"></a>장애 조치(failover) 그룹 및 지정 시간 복원

장애 조치(failover) 그룹과 함께 지정 시간 복원을 사용하는 방법에 대한 자세한 내용은 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="programmatically-managing-failover-groups"></a>프로그래밍 방식으로 장애 조치(failover) 그룹 관리

앞에서 설명한 대로, 자동 장애 조치(failover) 그룹과 활성 지역 복제는 Azure PowerShell 및 REST API를 사용하여 프로그래밍 방식으로 관리할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)을 참조하세요.

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: 단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| Cmdlet | 설명 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 서버에서 장애 조치 그룹을 제거하고 그룹에 포함된 모든 보조 데이터베이스를 삭제합니다. |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 장애 조치 그룹 구성을 검색합니다. |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |장애 조치 그룹의 구성을 수정합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다. |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Azure SQL Database 장애 조치(failover) 그룹에 하나 이상의 데이터베이스 추가|
|  | |

> [!IMPORTANT]
> 샘플 스크립트는 [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)를 참조하세요.
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Managed Instance를 사용하여 장애 조치(failover) 그룹 관리(미리 보기)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>PowerShell의 최신 시험판 버전을 설치

1. PowerShellGet 모듈을 1.6.5(또는 최신 미리 보기 버전)로 업데이트합니다. [PowerShell 미리 보기 사이트](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview)를 참조하세요.

   ```powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. 새 PowerShell 창에서 다음 명령을 실행합니다.

   ```powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>인스턴스가 장애 조치 그룹을 만드는 PowerShell commandlet

| API | 설명 |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |장애 조치 그룹의 구성을 수정합니다.|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |장애 조치 그룹 구성을 검색합니다.|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |장애 조치 그룹의 장애 조치를 보조 서버로 트리거합니다.|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | 장애 조치(failover) 그룹을 제거합니다.|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: 단일 및 풀링된 데이터베이스를 사용하여 SQL Database 장애 조치(failover) 그룹 관리

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 현재 주 서버에서 이 서버로 장애 조치(failover)합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |현재 주 서버에서 이 서버로 장애 조치(failover)합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 장애 조치(failover) 그룹을 가져옵니다. |
| [서버별 장애 조치(failover) 그룹 나열](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 서버에 있는 장애 조치(failover) 그룹을 나열합니다. |
| [장애 조치(failover) 그룹 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 장애 조치(failover) 그룹을 업데이트합니다. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>REST API: Managed Instance를 사용하여 장애 조치(failover) 그룹 관리(미리 보기)

| API | 설명 |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 서버에서 장애 조치 그룹을 제거합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 현재 주 서버에서 이 서버로 장애 조치(failover)합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |현재 주 서버에서 이 서버로 장애 조치(failover)합니다. 이 작업으로 인해 데이터가 손실될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 장애 조치(failover) 그룹을 가져옵니다. |
| [장애 조치(failover) 그룹 나열 - 위치별 목록](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 위치에 있는 장애 조치(failover) 그룹을 나열합니다. |

## <a name="next-steps"></a>다음 단계

- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [활성 지역 복제를 사용하여 풀된 데이터베이스 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md)
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
