---
title: 장애 조치(failover) 그룹
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
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116200"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치(failover)를 사용할 수 있습니다.

자동 장애 조치 그룹은 SQL Database 서버 또는 관리 되는 인스턴스의 모든 데이터베이스에 대 한 복제 및 장애 조치 (failover)를 관리 하는 데 사용할 수 있는 SQL Database 기능으로, 다른 지역에 있습니다. 지리적으로 복제 된 데이터베이스를 대규모로 배포 하 고 관리할 수 있도록 설계 된 기존 [활성 지역 복제](sql-database-active-geo-replication.md) 기능을 기반으로 하는 선언적 추상화입니다. 수동으로 장애 조치(failover)를 시작하거나, 사용자 정의 정책에 따라 SQL Database 서비스에 위임할 수 있습니다. 사용자 정의 정책 옵션을 사용하면 치명적인 오류 또는 계획되지 않은 다른 이벤트가 발생하여 주 지역에서 SQL Database 서비스의 가용성이 완전히 또는 부분적으로 상실될 경우 보조 지역에서 여러 관련 데이터베이스를 자동으로 복구할 수 있습니다. 장애 조치 (failover) 그룹은 하나 또는 여러 데이터베이스를 포함할 수 있으며, 일반적으로 동일한 응용 프로그램에서 사용 됩니다. 또한 읽을 수 있는 보조 데이터베이스를 사용하여 읽기 전용 쿼리 워크로드를 오프로드할 수 있습니다. 자동 장애 조치 그룹에 여러 데이터베이스가 포함되기 때문에 주 서버에서 이러한 데이터베이스를 구성해야 합니다. 자동 장애 조치 그룹은 그룹의 모든 데이터베이스를 다른 지역에 있는 하나의 보조 서버로만 복제할 수 있도록 지원합니다.

> [!NOTE]
> SQL Database 서버에서 단일 또는 풀링된 데이터베이스를 사용 중이며 동일하거나 다른 지역에 여러 개의 보조 데이터베이스를 만들려는 경우 [활성 지역 복제](sql-database-active-geo-replication.md)를 사용합니다. 

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 그룹 내 데이터베이스 중 하나 이상에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 일반적으로 이러한 인시던트는 기본 제공 되는 자동 고가용성 작업으로 자체적으로 완화 될 수 없습니다. 장애 조치 (failover) 트리거의 예에는 여러 계산 노드의 OS 커널 메모리 누수로 인 한 SQL 테 넌 트 링 또는 제어 링으로 인 한 인시던트가 포함 되어 있거나, ro 중 잘못 된 네트워크 케이블이 잘렸습니다. 하나 이상의 테 넌 트 링으로 인해 발생 한 인시던트가 포함 됩니다. 하드웨어 서비스 해제.  자세한 내용은 [SQL Database 고가용성](sql-database-high-availability.md)을 참조 하세요.

또한 자동 장애 조치 그룹은 장애 조치하는 동안 변경되지 않는 읽기-쓰기 및 읽기 전용 수신기 끝점을 제공합니다. 수동 또는 자동 장애 조치 활성화를 사용하는지 여부에 관계 없이 장애 조치는 그룹의 모든 보조 데이터베이스를 주 데이터베이스로 전환합니다. 데이터베이스 장애 조치(failover)가 완료되면 엔드포인트를 새 지역으로 리디렉션하도록 DNS 레코드가 자동으로 업데이트됩니다. 특정 RPO 및 RTO 데이터에 대해서는 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

자동 장애 조치(failover) 정책과 함께 자동 장애 조치(failover) 그룹을 사용하는 경우 SQL Database 서버 또는 관리되는 인스턴스의 데이터베이스에 영향을 미치는 중단이 발생하면 자동 장애 조치(failover)가 수행됩니다. 다음을 사용하여 자동 장애 조치(failover) 그룹을 관리할 수 있습니다.

- [Azure Portal](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: 장애 조치 (Failover) 그룹](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: 그룹 장애 조치](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API: 장애 조치(failover) 그룹](/rest/api/sql/failovergroups)

장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.

실제 비즈니스 연속성을 달성하기 위해 데이터 센터 간에 데이터베이스 중복을 추가하는 것은 솔루션의 일부입니다. 치명적인 오류 후 애플리케이션(서비스) 엔드투엔드 복구에는 서비스 및 모든 종속성 서비스를 구성하는 모든 구성 요소의 복구가 필요합니다. 이러한 구성 요소의 예에는 클라이언트 소프트웨어(예: 사용자 지정 JavaScript를 사용한 브라우저), 웹 프런트 엔드, 스토리지 및 DNS가 포함됩니다. 모든 구성 요소는 동일한 오류에 탄력적이며 애플리케이션의 복구 시간 목표(RTO) 내에서 사용할 수 있는 것이 중요합니다. 따라서 모든 종속성 서비스를 확인하고 제고하는 보장 사항 및 기능을 이해해야 합니다. 그런 다음 의존하는 서비스 장애 조치 중 서비스 기능을 확인하도록 적절한 단계를 수행해야 합니다. 재해 복구를 위한 솔루션 설계에 대한 자세한 내용은 [활성 지역 복제를 사용하여 재해 복구를 위한 클라우드 솔루션 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.

## <a name="auto-failover-group-terminology-and-capabilities"></a>자동 장애 조치(failover) 그룹 용어 및 기능

- **장애 조치 (Failover) 그룹 (안개)**

  장애 조치 (failover) 그룹은 단일 SQL Database 서버 또는 단일 관리 되는 인스턴스 내에서 관리 되는 데이터베이스의 명명 된 그룹으로, 주 지역의 가동 중단으로 인해 모든 또는 주 데이터베이스를 사용할 수 없게 되는 경우 다른 지역에 대 한 단위로 장애 조치 (failover) 할 수 있습니다. 관리 되는 인스턴스에 대해 만들어진 장애 조치 (failover) 그룹에는 인스턴스에 있는 모든 사용자 데이터베이스가 포함 되므로 하나의 장애 조치 (failover) 그룹만 인스턴스에 구성할 수 있습니다.
  
  > [!IMPORTANT]
  > 장애 조치 (failover) 그룹의 이름은 `.database.windows.net` 도메인 내에서 전역적으로 고유 해야 합니다.

- **SQL Database 서버**

     SQL Database 서버를 사용하면 단일 SQL Database 서버의 사용자 데이터베이스를 일부 또는 모두 장애 조치(failover) 그룹에 배치할 수 있습니다. 또한 SQL Database 서버는 단일 SQL Database 서버에서 여러 개의 장애 조치(failover) 그룹을 지원합니다.

- **주**

  장애 조치 (failover) 그룹의 주 데이터베이스를 호스팅하는 SQL Database 서버 또는 관리 되는 인스턴스입니다.

- **보조**

  장애 조치 (failover) 그룹의 보조 데이터베이스를 호스팅하는 SQL Database 서버 또는 관리 되는 인스턴스입니다. 보조 서버는 주 서버와 동일한 지역에 있을 수 없습니다.

- **장애 조치(failover) 그룹에 단일 데이터베이스 추가**

  동일한 SQL Database 서버에 있는 여러 개의 단일 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 장애 조치(failover) 그룹에 단일 데이터베이스를 추가하면 동일한 버전과 컴퓨팅 크기를 사용하는 보조 데이터베이스가 보조 서버에 자동으로 만들어집니다.  장애 조치(failover) 그룹을 만들 때 해당 서버를 지정했습니다. 보조 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 서버에 보조 데이터베이스가 이미 있는 데이터베이스를 추가하면 새 보조 데이터베이스가 보조 서버에 만들어집니다.

  > [!IMPORTANT]
  > 기존 보조 데이터베이스가 아닌 경우 보조 서버에 동일한 이름의 데이터베이스가 없는지 확인 합니다. 관리 되는 인스턴스의 장애 조치 (failover) 그룹에서 모든 사용자 데이터베이스가 복제 됩니다. 장애 조치(failover) 그룹에서 복제를 위해 사용자 데이터베이스 하위 집합을 선택할 수 없습니다.

- **장애 조치(failover) 그룹에 탄력적 풀의 데이터베이스 추가**

  탄력적 풀에 있는 전체 또는 여러 개의 데이터베이스를 동일한 장애 조치(failover) 그룹에 포함할 수 있습니다. 탄력적 풀에 주 데이터베이스가 있는 경우 동일한 이름을 가진 보조 데이터베이스가 탄력적 풀에 자동으로 만들어집니다(보조 풀). 장애 조치(failover) 그룹에서 만들 보조 데이터베이스를 호스트하기에 충분한 여유 용량과 정확히 동일한 이름을 가진 탄력적 풀이 보조 서버에 있는지 확인해야 합니다. 보조 풀에 보조 데이터베이스가 이미 있는 데이터베이스를 풀에 추가하는 경우 해당 지역 복제 링크가 그룹에 상속됩니다. 장애 조치(failover) 그룹에 속하지 않는 보조 데이터베이스가 서버에 이미 있는 데이터베이스를 추가하는 경우 새 보조 데이터베이스가 보조 풀에 만들어집니다.
  
- **초기 시드** 

  장애 조치 (failover) 그룹에 데이터베이스, 탄력적 풀 또는 관리 되는 인스턴스를 추가 하는 경우 데이터 복제를 시작 하기 전에 초기 시드 단계가 수행 됩니다. 초기 시드 단계는 가장 오래 되 고 비용이 많이 드는 작업입니다. 초기 시드가 완료 되 면 데이터가 동기화 되 고 이후의 데이터 변경 내용만 복제 됩니다. 초기 초기값을 완료 하는 데 걸리는 시간은 데이터의 크기, 복제 된 데이터베이스 수 및 장애 조치 (failover) 그룹에 있는 엔터티 간의 링크 속도에 따라 다릅니다. 일반적인 상황에서 일반적인 시드 속도는 단일 데이터베이스 또는 탄력적 풀의 경우 50-500 g b이 고, 관리 되는 인스턴스의 경우 18-35 GB는 1 시간입니다. 모든 데이터베이스에 대 한 시드는 병렬로 수행 됩니다. 데이터 복제를 시작 하기 전에 데이터베이스 수 및 데이터의 전체 크기와 함께 명시 된 시드 속도를 사용 하 여 초기 시드 단계가 소요 되는 기간을 예측할 수 있습니다.

  관리 되는 인스턴스의 경우 두 인스턴스 간의 Express 경로 링크 속도는 초기 시드 단계의 시간을 예측할 때 고려해 야 합니다. 두 인스턴스 간의 링크 속도가 필요한 것 보다 느린 경우 시드 시간이 특히 영향을 받을 수 있습니다. 설명 된 시드 속도, 데이터베이스 수, 총 데이터 크기 및 링크 속도를 사용 하 여 데이터 복제가 시작 되기 전에 초기 시드 단계가 소요 되는 기간을 예측할 수 있습니다. 예를 들어 단일 100 GB 데이터베이스의 경우 링크에서 시간당 35 GB를 푸시할 수 있는 경우 초기 초기값 단계는 2.8-5.5 시간에서 아무 곳 이나 사용 하지 않습니다. 링크에서 시간당 10gb만 전송할 수 있는 경우 100 g b 데이터베이스를 시드해야 하면 약 10 시간이 소요 됩니다. 복제할 데이터베이스가 여러 개 있는 경우 시드는 병렬로 실행 되며, 저속 링크 속도와 함께 사용 하는 경우, 특히 모든 데이터베이스의 데이터에 대 한 병렬 시드가 사용 가능한을 초과 하는 경우 초기 시드 단계가 훨씬 더 오래 걸릴 수 있습니다. 링크 대역폭. 두 인스턴스 간의 네트워크 대역폭이 제한 되어 있고 여러 관리 되는 인스턴스를 장애 조치 (failover) 그룹에 추가 하는 경우 여러 개의 관리 되는 인스턴스를 장애 조치 그룹에 하나씩 순차적으로 추가 하는 것이 좋습니다.

  
- **DNS 영역**

  새 인스턴스를 만들 때 자동으로 생성 되는 고유 ID입니다. 이 인스턴스에 대 한 SAN (다중 도메인) 인증서는 동일한 DNS 영역에 있는 모든 인스턴스에 대 한 클라이언트 연결을 인증 하기 위해 프로 비전 됩니다. 동일한 장애 조치 (failover) 그룹의 두 관리 되는 인스턴스는 DNS 영역을 공유 해야 합니다.
  
  > [!NOTE]
  > SQL Database 서버에 대해 만들어진 장애 조치 (failover) 그룹에는 DNS 영역 ID가 필요 하지 않습니다.

- **장애 조치 그룹 읽기-쓰기 수신기**

  현재 주 URL을 가리키는 DNS CNAME 레코드입니다. 장애 조치 그룹을 만들 때 자동으로 생성 되며 장애 조치 (failover) 후 주 복제본이 변경 되 면 읽기-쓰기 SQL 작업을 주 데이터베이스에 투명 하 게 다시 연결할 수 있습니다. SQL Database 서버에서 장애 조치 (failover) 그룹을 만들 때 수신기 URL에 대 한 DNS CNAME 레코드는 `<fog-name>.database.windows.net`로 형성 됩니다. 관리 되는 인스턴스에서 장애 조치 (failover) 그룹을 만들 때 수신기 URL에 대 한 DNS CNAME 레코드는 `<fog-name>.zone_id.database.windows.net`으로 구성 됩니다.

- **장애 조치 그룹 읽기 전용 수신기**

  보조 데이터베이스의 URL을 가리키는 읽기 전용 수신기를 가리키는 DNS CNAME 레코드가 생성됩니다. 이 파일은 장애 조치 (failover) 그룹을 만들 때 자동으로 생성 되며, 지정 된 부하 분산 규칙을 사용 하 여 읽기 전용 SQL 워크 로드를 보조 복제본에 투명 하 게 연결할 수 있도록 허용 합니다. SQL Database 서버에서 장애 조치 (failover) 그룹을 만들 때 수신기 URL에 대 한 DNS CNAME 레코드는 `<fog-name>.secondary.database.windows.net`로 형성 됩니다. 관리 되는 인스턴스에서 장애 조치 (failover) 그룹을 만들 때 수신기 URL에 대 한 DNS CNAME 레코드는 `<fog-name>.zone_id.secondary.database.windows.net`으로 구성 됩니다.

- **자동 장애 조치 정책**

  기본적으로 장애 조치(failover) 그룹은 자동 장애 조치(failover) 정책을 사용하여 구성됩니다. SQL Database 서비스는 오류가 검색되고 유예 기간이 만료된 후에 장애 조치(failover)를 트리거합니다. 영향의 규모로 인해 [SQL Database 서비스의 기본 제공 고가용성 인프라](sql-database-high-availability.md)를 통해 중단을 완화할 수 없음을 시스템에서 확인해야 합니다. 애플리케이션에서 장애 조치 워크플로를 제어하려는 경우 자동 장애 조치를 해제할 수 있습니다.
  
  > [!NOTE]
  > 가동 중단의 규모를 확인 하 고 해당 규모를 완화할 수 있는 속도를 확인 하는 작업은 운영 팀의 인적 작업에 따라 결정 됩니다. 유예 기간은 1 시간 이하로 설정할 수 없습니다.  이러한 제한은 데이터 동기화 상태에 관계 없이 장애 조치 (failover) 그룹의 모든 데이터베이스에 적용 됩니다. 

- **읽기 전용 장애 조치 정책**

  기본적으로 읽기 전용 수신기에 대한 장애 조치가 사용되지 않습니다. 이렇게 하면 보조 서버가 오프라인 상태일 때 주 서버의 성능이 영향을 받지 않습니다. 그러나 보조 서버가 복구될 때까지 읽기 전용 세션이 연결할 수 없음을 의미합니다. 읽기 전용 세션의 가동 중지 시간을 허용할 수 없고 주 복제본의 잠재적인 성능 저하를 방지 하기 위해 읽기 전용 및 읽기-쓰기 트래픽에 대해 주 복제본을 일시적으로 사용할 수 없는 경우 `AllowReadOnlyFailoverToPrimary` 속성을 구성 하 여 읽기 전용 수신기에 대 한 장애 조치 (failover)를 사용 하도록 설정할 수 있습니다. 이 경우 보조 데이터베이스를 사용할 수 없는 경우 읽기 전용 트래픽이 자동으로 주 데이터베이스로 리디렉션됩니다.

- **계획된 장애 조치**

   계획된 장애 조치(failover)는 보조 역할이 주 역할로 전환되기 전에 주 데이터베이스와 보조 데이터베이스 간에 전체 동기화를 수행합니다. 이렇게 하면 데이터 손실이 발생하지 않습니다. 계획된 장애 조치(failover)는 다음과 같은 시나리오에서 사용합니다.

  - 데이터 손실이 허용되지 않을 때 프로덕션에서 DR(재해 복구) 드릴 수행
  - 데이터베이스를 다른 지역으로 재배치
  - 중단이 완화된 후 데이터베이스를 주 지역으로 반환합니다(장애 복구(failback)).

- **계획되지 않은 장애 조치**

   계획되지 않은 장애 조치 또는 강제 장애 조치(failover)는 주 데이터베이스와의 동기화 없이 보조 역할을 주 역할로 즉시 전환합니다. 이 작업으로 인해 데이터가 손실됩니다. 계획되지 않은 장애 조치(failover)는 주 데이터베이스에 액세스할 수 없는 중단 중에 복구 방법으로 사용됩니다. 원래 주 복제본이 다시 온라인 상태가 되 면 동기화 없이 자동으로 다시 연결 되 고 새 보조 데이터베이스가 됩니다.

- **수동 장애 조치**

  자동 장애 조치 구성에 관계 없이 언제든지 장애 조치를 수동으로 시작할 수 있습니다. 자동 장애 조치(failover) 정책이 구성되지 않은 경우 장애 조치 그룹의 데이터베이스를 보조 데이터베이스에 복구하려면 수동 장애 조치(failover)를 수행해야 합니다. 강제 장애 조치 또는 친숙한 장애 조치(전체 데이터 동기화 사용)를 시작할 수 있습니다. 친숙한 장애 조치를 사용하면 주 데이터베이스를 보조 지역에 재배치할 수 있습니다. 장애 조치(failover)가 완료되면 새로운 주 데이터베이스에 대한 연결을 보장하기 위해 DNS 레코드가 자동으로 업데이트됩니다.

- **데이터 손실이 있는 유예 기간**

  주 및 보조 데이터베이스가 비동기 복제를 사용하여 동기화되기 때문에 장애 조치로 인해 데이터가 손실될 수 있습니다. 애플리케이션의 데이터 손실 허용 오차를 반영하도록 자동 장애 조치 정책을 사용자 지정할 수 있습니다. `GracePeriodWithDataLossHours`를 구성 하면 데이터 손실이 발생할 수 있는 장애 조치 (failover)를 시작 하기 전에 시스템에서 대기 하는 시간을 제어할 수 있습니다.

- **여러 장애 조치 그룹**

  동일한 서버 쌍에 대해 여러 장애 조치 그룹을 구성하여 장애 조치의 크기를 제어할 수 있습니다. 각 그룹은 독립적으로 장애 조치됩니다. 다중 테넌트 애플리케이션에서 탄력적 풀을 사용하는 경우 이 기능을 사용하여 각 풀에 주 및 보조 데이터베이스를 혼합할 수 있습니다. 이렇게 하면 테넌트의 절반에 대해서만 가동 중단에 따른 영향을 줄일 수 있습니다.

  > [!NOTE]
  > Managed Instance는 여러 개의 장애 조치(failover) 그룹을 지원하지 않습니다.
  
## <a name="permissions"></a>사용 권한

장애 조치 (failover) 그룹에 대 한 권한은 [RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 관리 됩니다. [SQL Server 참여자](../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할에는 장애 조치 (failover) 그룹을 관리 하는 데 필요한 모든 권한이 있습니다.

### <a name="create-failover-group"></a>장애 조치 (failover) 그룹 만들기

장애 조치 (failover) 그룹을 만들려면 주 서버와 보조 서버 및 장애 조치 (failover) 그룹의 모든 데이터베이스에 대 한 RBAC 쓰기 권한이 필요 합니다. 관리 되는 인스턴스의 경우 기본 및 보조 관리 되는 인스턴스에 대해 RBAC 쓰기 권한이 필요 하지만 개별 데이터베이스에 대 한 사용 권한은 장애 조치 (failover) 그룹에 추가 되거나 장애 조치 (failover) 그룹에서 제거 될 수 없으므로 관련 되지 않습니다. 

### <a name="update-a-failover-group"></a>장애 조치 (failover) 그룹 업데이트

장애 조치 (failover) 그룹을 업데이트 하려면 장애 조치 (failover) 그룹 및 현재 주 서버 또는 관리 되는 인스턴스의 모든 데이터베이스에 대 한 RBAC 쓰기 권한이 필요 합니다.  

### <a name="failover-a-failover-group"></a>장애 조치 (failover) 그룹 장애 조치

장애 조치 (failover) 그룹을 장애 조치 (failover) 하려면 새 주 서버 또는 관리 되는 인스턴스에서 장애 조치 (failover) 그룹에 대 한 RBAC 쓰기 권한이 필요 합니다.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀로 장애 조치(failover) 그룹을 사용하는 방법의 모범 사례

자동 장애 조치(failover) 그룹은 주 SQL Database 서버에서 구성되어야 하며 다른 Azure 지역의 보조 SQL Database 서버에 연결됩니다. 그룹에는 이러한 서버에 있는 데이터베이스가 모두 또는 일부 포함될 수 있습니다. 다음 다이어그램은 여러 데이터베이스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> 장애 조치 (failover) 그룹에 단일 데이터베이스를 추가 하는 자세한 단계별 자습서는 [장애 조치 (failover) 그룹에 단일 데이터베이스 추가](sql-database-single-database-failover-group-tutorial.md) 를 참조 하세요.

비즈니스 연속성을 고려하여 서비스를 설계하는 경우 다음과 같은 일반 지침을 따르세요.

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>하나 또는 여러 장애 조치 그룹을 사용 하 여 여러 데이터베이스의 장애 조치 (failover) 관리

다른 하위 지역의 두 서버(기본 및 보조 서버) 간에 하나 이상의 장애 조치 그룹을 만들 수 있습니다. 주 지역의 가동 중단으로 인해 주 데이터베이스 전체 또는 일부를 사용할 수 없게 되는 경우를 대비하여 각 그룹에 한 단위로 복구되는 하나 또는 여러 개의 데이터베이스가 포함될 수 있습니다. 장애 조치(failover) 그룹을 사용하는 경우 주 데이터베이스와 서비스 목표가 동일한 지역 보조 데이터베이스가 작성됩니다. 장애 조치(failover) 그룹에 기존의 지역 복제 관계를 추가하는 경우 보조 데이터베이스가 주 데이터베이스와 동일한 서비스 계층 및 컴퓨팅 크기로 구성되었는지 확인합니다.
  
> [!IMPORTANT]
> 다른 구독에 있는 두 서버 간에 장애 조치 (failover) 그룹을 만드는 것은 단일 데이터베이스 및 탄력적 풀에 대해 현재 지원 되지 않습니다. 장애 조치 (failover) 그룹을 만든 후 주 서버 또는 보조 서버를 다른 구독으로 이동 하면 장애 조치 (failover) 요청 및 기타 작업이 실패할 수 있습니다.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP 워크 로드에 대 한 읽기-쓰기 수신기 사용

OLTP 작업을 수행할 때 `<fog-name>.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새 주 데이터베이스로 리디렉션됩니다.

### <a name="using-read-only-listener-for-read-only-workload"></a>읽기 전용 작업에 읽기 전용 수신기 사용

특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 읽기 전용 세션의 경우 `<fog-name>.secondary.database.windows.net`을 서버 URL로 사용하면 자동으로 보조 데이터베이스에 연결됩니다. 또한 `ApplicationIntent=ReadOnly`를 사용 하 여 연결 문자열 읽기 의도를 표시 하는 것이 좋습니다. 장애 조치 (failover) 후 또는 보조 서버가 오프 라인 상태가 된 경우에 읽기 전용 작업을 다시 연결할 수 있는지 확인 하려면 장애 조치 정책의 `AllowReadOnlyFailoverToPrimary` 속성을 구성 해야 합니다.

### <a name="preparing-for-performance-degradation"></a>성능 저하 준비

일반적인 Azure 응용 프로그램은 여러 Azure 서비스를 사용 하며 여러 구성 요소로 구성 됩니다. 장애 조치 (failover) 그룹의 자동 장애 조치 (failover)는 Azure SQL 구성 요소에만 해당 하는 상태에 따라 트리거됩니다. 주 지역의 다른 Azure 서비스는 중단의 영향을 받지 않을 수 있으며 해당 지역에서 해당 구성 요소를 계속 사용할 수 있습니다. 주 데이터베이스가 DR 지역으로 전환 되 면 종속 된 구성 요소 사이의 대기 시간이 늘어날 수 있습니다. 응용 프로그램의 성능에 대 한 대기 시간이 길수록 응용 프로그램의 모든 구성 요소에 대 한 중복성을 유지 하 고 다음 [네트워크 보안 지침](#failover-groups-and-network-security)을 따라야 합니다.

### <a name="preparing-for-data-loss"></a>데이터 손실 준비

중단이 감지 되 면 SQL은 `GracePeriodWithDataLossHours`에 지정 된 기간 동안 대기 합니다. 기본값은 1시간입니다. 데이터 손실을 방지할 수 없는 경우 24 시간 등의 충분히 큰 숫자로 `GracePeriodWithDataLossHours` 설정 해야 합니다. 수동 그룹 장애 조치(failover)를 사용하여 보조 데이터베이스에서 주 데이터베이스로 장애 복구할 수 있습니다.

> [!IMPORTANT]
> 지역 복제를 사용하는 800개 이하의 DTU 및 250개 초과의 데이터베이스가 있는 탄력적 풀에는 계획된 장애 조치 지연 및 성능 저하가 포함된 문제가 발생할 수 있습니다.  이러한 문제는 지역에서 복제 엔드포인트가 지리적으로 광범위하게 분리되어 있거나 여러 보조 엔드포인트가 각 데이터베이스에 대해 사용되는 경우 쓰기 집약적 작업에 발생할 가능성이 높습니다.  이러한 문제의 증상은 지역에서 복제 지연이 시간에 따라 증가하는 경우에 표시됩니다.  이러한 지연은 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)를 사용하여 모니터링할 수 있습니다.  이러한 문제가 발생하는 경우 완화 방안에는 풀 DTU의 수를 늘리거나 동일한 풀에 지역에서 복제된 데이터베이스 수를 줄이는 방법이 있습니다.

### <a name="changing-secondary-region-of-the-failover-group"></a>장애 조치 (failover) 그룹의 보조 지역 변경

변경 순서를 설명 하기 위해 서버 A는 주 서버이 고 서버 B는 기존 보조 서버 이며, 서버 C는 세 번째 지역의 새로운 보조 서버입니다.  전환을 수행 하려면 다음 단계를 수행 합니다.

1.  [활성 지역 복제](sql-database-active-geo-replication.md)를 사용 하 여 서버 A에서 서버 C에 대 한 각 데이터베이스의 추가 보조 데이터베이스를 만듭니다. 서버 A의 각 데이터베이스에는 서버 B와 서버 C에 각각 하나씩 두 개의 보조 데이터베이스가 있습니다. 이렇게 하면 전환 하는 동안 주 데이터베이스가 보호 된 상태로 유지 됩니다.
2.  장애 조치 (failover) 그룹을 삭제 합니다. 이 시점에서 로그인에 실패 하 게 됩니다. 이는 장애 조치 (failover) 그룹 수신기에 대 한 SQL 별칭이 삭제 되 고 게이트웨이에서 장애 조치 (failover) 그룹 이름을 인식 하지 못하기 때문입니다.
3.  서버 A와 C 간에 이름이 같은 장애 조치 (failover) 그룹을 다시 만듭니다. 이 시점에서 로그인은 실패 하 게 됩니다.
4.  서버 A의 모든 주 데이터베이스를 새 장애 조치 (failover) 그룹에 추가 합니다.
5.  서버 B를 삭제 합니다. B의 모든 데이터베이스는 자동으로 삭제 됩니다. 


### <a name="changing-primary-region-of-the-failover-group"></a>장애 조치 (failover) 그룹의 주 지역 변경

변경 순서를 설명 하기 위해 서버 A는 주 서버, 서버 B는 기존 보조 서버, 서버 C는 세 번째 지역의 새로운 주 서버 라고 가정 하겠습니다.  전환을 수행 하려면 다음 단계를 수행 합니다.

1.  계획 된 장애 조치 (failover)를 수행 하 여 주 서버를 B로 전환 합니다. 서버 A가 새 보조 서버가 됩니다. 장애 조치 (failover)로 인해 몇 분의 가동 중지 시간이 발생할 수 있습니다. 실제 시간은 장애 조치 (failover) 그룹의 크기에 따라 달라 집니다.
2.  [활성 지역 복제](sql-database-active-geo-replication.md)를 사용 하 여 server B에서 server C로 각 데이터베이스의 추가 보조 데이터베이스를 만듭니다. 서버 B의 각 데이터베이스에는 서버 A와 서버 C에 각각 하나씩 두 개의 보조 데이터베이스가 있습니다. 이렇게 하면 전환 하는 동안 주 데이터베이스가 보호 된 상태로 유지 됩니다.
3.  장애 조치 (failover) 그룹을 삭제 합니다. 이 시점에서 로그인에 실패 하 게 됩니다. 이는 장애 조치 (failover) 그룹 수신기에 대 한 SQL 별칭이 삭제 되 고 게이트웨이에서 장애 조치 (failover) 그룹 이름을 인식 하지 못하기 때문입니다.
4.  서버 A와 C 간에 이름이 같은 장애 조치 (failover) 그룹을 다시 만듭니다. 이 시점에서 로그인은 실패 하 게 됩니다.
5.  새 장애 조치 (failover) 그룹에 B의 모든 주 데이터베이스를 추가 합니다. 
6.  장애 조치 그룹의 계획 된 장애 조치 (failover)를 수행 하 여 B 및 C로 전환 합니다. 이제 server C가 기본이 되 고 B-보조 데이터베이스가 됩니다. 서버 A의 모든 보조 데이터베이스는 C의 기본 데이터베이스에 자동으로 연결 됩니다. 1 단계와 마찬가지로 장애 조치 (failover)로 인해 몇 분의 가동 중지 시간이 발생할 수 있습니다.
6.  서버 A를 삭제 합니다. 의 모든 데이터베이스는 자동으로 삭제 됩니다.

> [!IMPORTANT]
> 장애 조치 (failover) 그룹을 삭제 하면 수신기 끝점에 대 한 DNS 레코드도 삭제 됩니다. 이 시점에서 다른 사람이 동일한 이름으로 장애 조치 (failover) 그룹 또는 서버 별칭을 만들 수 있습니다 .이로 인해 다시 사용할 수 없습니다. 위험을 최소화 하려면 일반 장애 조치 (failover) 그룹 이름을 사용 하지 마세요.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>관리 되는 인스턴스에서 장애 조치 (failover) 그룹 사용에 대 한 모범 사례

자동 장애 조치(failover) 그룹은 주 인스턴스에서 구성되어야 하며 다른 Azure 지역의 보조 인스턴스에 연결됩니다.  인스턴스의 모든 데이터베이스가 보조 인스턴스에 복제됩니다.

다음 다이어그램은 관리되는 인스턴스와 자동 장애 조치(failover) 그룹을 사용하는 지역 중복 클라우드 애플리케이션의 일반적인 구성을 보여 줍니다.

![자동 장애 조치(failover)](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> 장애 조치 (failover) 그룹을 사용 하도록 관리 되는 인스턴스를 추가 하는 자세한 단계별 자습서는 [장애 조치 (failover) 그룹에 관리 되는 인스턴스 추가](sql-database-managed-instance-failover-group-tutorial.md) 를 참조 하세요.

응용 프로그램에서 관리 되는 인스턴스를 데이터 계층으로 사용 하는 경우 비즈니스 연속성을 위해 설계할 때 다음 일반 지침을 따르십시오.

### <a name="creating-the-secondary-instance"></a>보조 인스턴스 만들기 

장애 조치(failover) 후 주 인스턴스에 대한 무중단 연결을 보장하려면 주 인스턴스와 보조 인스턴스가 동일한 DNS 영역에 있어야 합니다. 이는 동일한 SAN (다중 도메인) 인증서를 사용 하 여 장애 조치 (failover) 그룹의 두 인스턴스 중 하나에 대 한 클라이언트 연결을 인증할 수 있도록 보장 합니다. 애플리케이션이 프로덕션 배포에 사용할 준비가 되면 다른 지역에 보조 인스턴스를 만들고 주 인스턴스와 DNS 영역을 공유하는지 확인합니다. Azure Portal, PowerShell 또는 REST API를 사용 하 여 선택적 매개 변수 `DNS Zone Partner` 지정 하 여이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 서브넷에서 만든 첫 번째 인스턴스는 동일한 서브넷에 있는 모든 후속 인스턴스의 DNS 영역을 결정 합니다. 즉, 동일한 서브넷의 두 인스턴스는 서로 다른 DNS 영역에 속할 수 없습니다.

주 인스턴스와 동일한 DNS 영역에 보조 인스턴스를 만드는 방법에 대 한 자세한 내용은 [보조 관리 되는 인스턴스 만들기](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance)를 참조 하세요.

### <a name="enabling-replication-traffic-between-two-instances"></a>두 인스턴스 간의 복제 트래픽 활성화

각 인스턴스가 자체 VNet에 격리되므로 이러한 VNet 간의 양방향 트래픽을 허용해야 합니다. [Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요.

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>다른 구독의 관리 되는 인스턴스 간에 장애 조치 (failover) 그룹 만들기

서로 다른 두 구독에서 관리 되는 인스턴스 간에 장애 조치 (failover) 그룹을 만들 수 있습니다. PowerShell API를 사용 하는 경우 보조 인스턴스에 대 한 `PartnerSubscriptionId` 매개 변수를 지정 하 여 수행할 수 있습니다. REST API를 사용 하는 경우 `properties.managedInstancePairs` 매개 변수에 포함 된 각 인스턴스 ID에는 고유한 subscriptionID가 있을 수 있습니다.
  
> [!IMPORTANT]
> Azure Portal은 다른 구독에서 장애 조치 (failover) 그룹 만들기를 지원 하지 않습니다. 또한 다른 구독 및/또는 리소스 그룹에 걸친 기존 장애 조치 (failover) 그룹의 경우 주 인스턴스에서 포털을 통해 수동으로 장애 조치 (failover)를 시작할 수 없습니다. 대신 지역 보조 인스턴스에서 시작 합니다.

### <a name="managing-failover-to-secondary-instance"></a>보조 인스턴스로 장애 조치 (failover) 관리

장애 조치(failover) 그룹은 인스턴스에 있는 모든 데이터베이스의 장애 조치(failover)를 관리합니다. 그룹을 만들면 인스턴스의 각 데이터베이스가 자동으로 보조 인스턴스에 지역 복제됩니다. 장애 조치(failover) 그룹을 사용하여 데이터베이스 하위 집합의 부분 장애 조치(failover)를 시작할 수 없습니다.

> [!IMPORTANT]
> 주 인스턴스에서 데이터베이스를 제거하면 지역 보조 인스턴스에서도 자동으로 삭제됩니다.

### <a name="using-read-write-listener-for-oltp-workload"></a>OLTP 워크 로드에 대 한 읽기-쓰기 수신기 사용

OLTP 작업을 수행할 때 `<fog-name>.zone_id.database.windows.net`을 서버 URL로 사용하면 자동으로 주 데이터베이스에 연결됩니다. 이 URL은 장애 조치(failover) 후에 변경되지 않습니다. 장애 조치(failover) 시에는 DNS 레코드가 업데이트되므로 클라이언트 DNS 캐시를 새로 고친 후에만 클라이언트 연결이 새로운 주 데이터베이스로 리디렉션됩니다. 보조 인스턴스는 주 복제본과 DNS 영역을 공유 하므로 클라이언트 응용 프로그램은 동일한 SAN 인증서를 사용 하 여 해당 영역에 다시 연결할 수 있습니다.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>읽기 전용 수신기를 사용 하 여 보조 인스턴스에 연결

특정 데이터가 부실해도 정상적으로 수행 가능한 논리적으로 격리된 읽기 전용 작업이 있는 경우에는 애플리케이션에서 보조 데이터베이스를 사용할 수 있습니다. 지역 복제된 보조 데이터베이스에 직접 연결하려는 경우 `server.secondary.zone_id.database.windows.net`을 서버 URL로 사용하면 지역 복제된 보조 데이터베이스에 직접 연결됩니다.

> [!NOTE]
> 특정 서비스 계층에서 Azure SQL Database는 [읽기 전용 복제본](sql-database-read-scale-out.md)을 사용하여 읽기 전용 복제본의 용량 및 연결 문자열의 `ApplicationIntent=ReadOnly` 매개 변수를 통해 읽기 전용 쿼리 워크로드를 부하를 분산할 수 있도록 지원합니다. 지역 복제된 보조 데이터베이스를 구성한 경우 이 기능을 사용하여 주 위치 또는 지역 복제된 위치에 있는 읽기 전용 복제본에 연결할 수 있습니다.
> - 주 위치의 읽기 전용 복제본에 연결하려면 `<fog-name>.zone_id.database.windows.net`을 사용합니다.
> - 보조 위치의 읽기 전용 복제본에 연결 하려면 `<fog-name>.secondary.zone_id.database.windows.net`을 사용 합니다.

### <a name="preparing-for-performance-degradation"></a>성능 저하 준비

일반적인 Azure 응용 프로그램은 여러 Azure 서비스를 사용 하며 여러 구성 요소로 구성 됩니다. 장애 조치 (failover) 그룹의 자동 장애 조치 (failover)는 Azure SQL 구성 요소에만 해당 하는 상태에 따라 트리거됩니다. 주 지역의 다른 Azure 서비스는 중단의 영향을 받지 않을 수 있으며 해당 지역에서 해당 구성 요소를 계속 사용할 수 있습니다. 주 데이터베이스가 DR 지역으로 전환 되 면 종속 된 구성 요소 사이의 대기 시간이 늘어날 수 있습니다. 응용 프로그램의 성능에 대 한 대기 시간이 길수록 응용 프로그램의 모든 구성 요소에 대 한 중복성을 유지 하 고 다음 [네트워크 보안 지침](#failover-groups-and-network-security)을 따라야 합니다.

### <a name="preparing-for-data-loss"></a>데이터 손실 준비

가동 중단이 감지되는 경우 SQL은 중요한 지식에 대한 데이터 손실이 없으면 읽기 쓰기 장애 조치를 자동으로 트리거합니다. 그렇지 않으면 `GracePeriodWithDataLossHours`에서 지정한 기간 동안 대기합니다. `GracePeriodWithDataLossHours`를 지정한 경우 데이터 손실을 준비합니다. 일반적으로 중단 시에 Azure에서는 가용성을 우선으로 합니다. 데이터 손실을 방지하려는 경우 GracePeriodWithDataLossHours를 24시간과 같은 충분히 큰 숫자로 설정해야 합니다.

읽기/쓰기 수신기의 DNS 업데이트는 장애 조치(failover)가 시작된 후 즉시 발생합니다. 이 작업으로 인한 데이터 손실은 없습니다. 그러나 일반적인 조건에서 데이터베이스 역할을 전환하는 데 최대 5분 정도 걸릴 수 있습니다. 완료될 때까지 새로운 주 인스턴스의 일부 데이터베이스는 계속 읽기 전용입니다. PowerShell을 사용 하 여 장애 조치 (failover)를 시작 하는 경우 전체 작업이 동기화 됩니다. Azure Portal를 사용 하 여 시작 된 경우 UI는 완료 상태를 표시 합니다. REST API를 사용하여 시작하는 경우, 표준 Azure Resource Manager의 폴링 메커니즘을 사용하여 완료되었는지 모니터링합니다.

> [!IMPORTANT]
> 수동 그룹 장애 조치(failover)를 사용하여 주 데이터베이스를 원래 위치로 다시 이동할 수 있습니다. 장애 조치(failover)를 유발한 중단이 완화되면 주 데이터베이스를 원래 위치로 이동할 수 있습니다. 이렇게 하려면 그룹의 수동 장애 조치(failover)를 시작해야 합니다.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>장애 조치 (failover) 그룹의 보조 지역 변경

인스턴스 A는 주 인스턴스이고 인스턴스 B는 기존 보조 인스턴스이고 인스턴스 C는 세 번째 지역의 새 보조 인스턴스인 것으로 가정 하겠습니다.  전환을 수행 하려면 다음 단계를 수행 합니다.

1.  동일한 DNS 영역에서 및와 크기가 같은 인스턴스 C를 만듭니다. 
2.  인스턴스 A와 B 간의 장애 조치 (failover) 그룹을 삭제 합니다. 이 시점에서 장애 조치 (failover) 그룹 수신기에 대 한 SQL 별칭이 삭제 되 고 게이트웨이에서 장애 조치 (failover) 그룹 이름을 인식 하지 못하기 때문에 로그인에 실패 하 게 됩니다. 보조 데이터베이스는 주 데이터베이스에서 연결을 끊고 읽기/쓰기 데이터베이스가 됩니다. 
3.  인스턴스 A와 C 사이에 동일한 이름으로 장애 조치 (failover) 그룹을 만듭니다. [관리 되는 인스턴스로 장애 조치 (failover) 그룹 자습서](sql-database-managed-instance-failover-group-tutorial.md)의 지침을 따르세요. 이는 데이터 크기의 작업 이며, 인스턴스 A의 모든 데이터베이스가 시드 되 고 동기화 되 면 완료 됩니다.
4.  불필요 한 요금을 방지 하기 위해 필요 하지 않은 경우 인스턴스 B를 삭제 합니다.

> [!NOTE]
> 2 단계 이후부터 3 단계가 완료 될 때까지 인스턴스 a의 데이터베이스는 인스턴스 A의 치명적인 오류 로부터 보호 되지 않는 상태로 유지 됩니다.

### <a name="changing-primary-region-of-the-failover-group"></a>장애 조치 (failover) 그룹의 주 지역 변경

인스턴스 A는 주 인스턴스이고 인스턴스 B는 기존 보조 인스턴스이고 인스턴스 C는 세 번째 지역의 새 주 인스턴스인 것으로 가정 하겠습니다.  전환을 수행 하려면 다음 단계를 수행 합니다.

1.  동일한 DNS 영역에서 B와 크기가 같은 인스턴스 C를 만듭니다. 
2.  인스턴스 B에 연결 하 여 수동으로 장애 조치 (failover) 하 여 주 인스턴스를 B로 전환 합니다. 인스턴스 A는 새 보조 인스턴스가 자동으로 만들어집니다.
3.  인스턴스 A와 B 간의 장애 조치 (failover) 그룹을 삭제 합니다. 이 시점에서 장애 조치 (failover) 그룹 수신기에 대 한 SQL 별칭이 삭제 되 고 게이트웨이에서 장애 조치 (failover) 그룹 이름을 인식 하지 못하기 때문에 로그인에 실패 하 게 됩니다. 보조 데이터베이스는 주 데이터베이스에서 연결을 끊고 읽기/쓰기 데이터베이스가 됩니다. 
4.  인스턴스 A와 C 사이에 동일한 이름으로 장애 조치 (failover) 그룹을 만듭니다. [관리 되는 인스턴스로 장애 조치 (failover) 그룹 자습서](sql-database-managed-instance-failover-group-tutorial.md)의 지침을 따르세요. 이는 데이터 크기의 작업 이며, 인스턴스 A의 모든 데이터베이스가 시드 되 고 동기화 되 면 완료 됩니다.
5.  불필요 한 요금을 방지 하기 위해 필요 하지 않은 경우 인스턴스 A를 삭제 합니다.

> [!NOTE] 
> 3 단계 후 4 단계가 완료 될 때까지 인스턴스 a의 데이터베이스는 인스턴스 A의 치명적인 오류 로부터 보호 되지 않는 상태로 유지 됩니다.

> [!IMPORTANT]
> 장애 조치 (failover) 그룹을 삭제 하면 수신기 끝점에 대 한 DNS 레코드도 삭제 됩니다. 이 시점에서 다른 사람이 동일한 이름으로 장애 조치 (failover) 그룹 또는 서버 별칭을 만들 수 있습니다 .이로 인해 다시 사용할 수 없습니다. 위험을 최소화 하려면 일반 장애 조치 (failover) 그룹 이름을 사용 하지 마세요.

## <a name="failover-groups-and-network-security"></a>장애 조치 그룹 및 네트워크 보안

일부 응용 프로그램의 경우 보안 규칙에서는 데이터 계층에 대 한 네트워크 액세스가 특정 구성 요소나 VM, 웹 서비스 등의 구성 요소로 제한 되어야 합니다. 이 요구 사항은 비즈니스 연속성 디자인 및 장애 조치 (failover) 그룹 사용에 대 한 몇 가지 문제를 제공 합니다. 이러한 제한 된 액세스를 구현 하는 경우 다음 옵션을 고려 하십시오.

### <a name="using-failover-groups-and-virtual-network-rules"></a>장애 조치 그룹 및 가상 네트워크 규칙 사용

[Virtual Network 서비스 엔드포인트 및 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 사용하여 SQL 데이터베이스에 대한 액세스를 제한하는 경우 각 Virtual Network 서비스 엔드포인트가 하나의 Azure 지역에만 적용됩니다. 엔드포인트를 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다. 따라서 동일한 지역에 배포된 클라이언트 애플리케이션만 주 데이터베이스에 연결할 수 있습니다. 장애 조치가 SQL 클라이언트 세션을 다른 (보조) 지역의 서버로 다시 라우팅하는 결과를 발생시키므로 해당 지역 외부에 있는 클라이언트에서 시작된 경우 이러한 세션에 실패합니다. 이런 이유로 참여하는 서버가 Virtual Network 규칙에 포함된 경우 자동 장애 조치(failover) 정책을 사용할 수 없습니다. 수동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. 보조 지역에서 애플리케이션(웹 서비스, 가상 머신 등) 프런트 엔드 구성 요소의 중복 복사본을 프로비전합니다.
2. 기본 및 보조 서버에 대한 [가상 네트워크 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 개별적으로 구성합니다.
3. [트래픽 관리자 구성을 사용하여 프런트 엔드 장애 조치](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)를 사용하도록 설정합니다.
4. 중단이 검색되면 수동 장애 조치(failover)를 시작합니다. 이 옵션은 프런트 엔드와 데이터 계층 간에 일관된 대기 시간을 필요로 하는 애플리케이션에 대해 최적화되고 프런트 엔드, 데이터 계층 또는 둘 모두가 중단의 영향을 받는 경우 복구를 지원합니다.

> [!NOTE]
> **읽기 전용 수신기**를 사용하여 읽기 전용 워크로드의 부하를 분산하는 경우 이 워크로드가 보조 데이터베이스에 연결할 수 있도록 보조 지역의 VM 또는 다른 리소스에서 실행되는지 확인합니다.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>장애 조치 그룹 및 SQL 데이터베이스 방화벽 규칙 사용

비즈니스 연속성 계획이 자동 장애 조치에서 그룹을 사용하는 장애 조치가 필요한 경우 기존 방화벽 규칙을 사용하여 SQL 데이터베이스에 대한 액세스를 제한할 수 있습니다. 자동 장애 조치를 지원하려면 다음 단계를 수행합니다.

1. [공용 IP를 만듭니다.](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [공용 부하 분산 장치를 만들고](../load-balancer/quickstart-load-balancer-standard-public-portal.md) 여기에 공용 IP를 할당합니다.
3. 프런트 엔드 구성 요소에 대한 [가상 네트워크 및 가상 머신을 만듭니다.](../load-balancer/quickstart-load-balancer-standard-public-portal.md)
4. [네트워크 보안 그룹을 만들고](../virtual-network/security-overview.md) 인바운드 연결을 구성합니다.
5. 'Sql' [서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하여 아웃바운드 연결이 Azure SQL 데이터베이스에 대해 열려 있는지 확인합니다.
6. [SQL 데이터베이스 방화벽 규칙](sql-database-firewall-configure.md)을 만들어서 1단계에서 만든 공용 IP 주소에서 인바운드 트래픽을 허용합니다.

아웃바운드 액세스를 구성하는 방법 및 방화벽 규칙에서 사용할 IP에 대한 자세한 내용은 [부하 분산 장치 아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md)을 참조하세요.

위의 구성에서는 자동 장애 조치가 프런트 엔드 구성 요소에서 연결을 차단하지 않는지 확인하고 애플리케이션이 프런트 엔드와 데이터 계층 간의 긴 대기 시간을 허용할 수 있다고 가정합니다.

> [!IMPORTANT]
> 지역 중단에 대한 비즈니스 연속성을 보장하기 위해 프런트 엔드 구성 요소와 데이터베이스 모두에 대한 지리적 복제를 확인해야 합니다.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>관리 되는 인스턴스와 해당 Vnet 간의 지역에서 복제 사용

서로 다른 두 지역에서 주 관리 인스턴스와 보조 관리 되는 인스턴스 간에 장애 조치 (failover) 그룹을 설정 하는 경우 각 인스턴스는 독립 된 가상 네트워크를 사용 하 여 격리 됩니다. 이러한 Vnet 간의 복제 트래픽을 허용 하려면 다음 필수 구성 요소가 충족 되는지 확인 합니다.

1. 두 개의 관리 되는 인스턴스는 서로 다른 Azure 지역에 있어야 합니다.
2. 두 개의 관리 되는 인스턴스는 동일한 서비스 계층 이어야 하 고 저장소 크기는 동일 해야 합니다.
3. 보조 관리 되는 인스턴스는 비어 있어야 합니다 (사용자 데이터베이스 없음).
4. 관리 되는 인스턴스에서 사용 되는 가상 네트워크는 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [Express 경로](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 통해 연결 해야 합니다. 두 가상 네트워크가 온-프레미스 네트워크를 통해 연결 되는 경우 포트 5022 및 11000-11999을 차단 하는 방화벽 규칙이 없어야 합니다. 전역 VNet 피어링은 지원되지 않습니다.
5. 두 개의 관리 되는 인스턴스 Vnet는 겹치는 IP 주소를 가질 수 없습니다.
6. 포트 5022 및 11000 ~ 12000 범위는 다른 관리 되는 인스턴스의 서브넷에서 연결에 대 한 인바운드 및 아웃 바운드 연결에 대 한 오픈 인바운드 및 아웃 바운드를 설정 하는 NSG (네트워크 보안 그룹)를 설정 해야 합니다. 이는 인스턴스 간의 복제 트래픽을 허용 하기 위한 것입니다.

   > [!IMPORTANT]
   > NSG 보안 규칙을 잘못 구성하면 데이터베이스 복사 작업이 중단됩니다.

7. 보조 인스턴스는 올바른 DNS 영역 ID로 구성 됩니다. DNS 영역은 관리 되는 인스턴스 및 가상 클러스터의 속성 이며 해당 ID는 호스트 이름 주소에 포함 됩니다. 각 VNet에서 첫 번째 관리 되는 인스턴스를 만들 때 영역 ID가 임의의 문자열로 생성 되며 동일한 서브넷의 다른 모든 인스턴스에 동일한 ID가 할당 됩니다. 할당 되 면 DNS 영역을 수정할 수 없습니다. 동일한 장애 조치 (failover) 그룹에 포함 된 관리 되는 인스턴스는 DNS 영역을 공유 해야 합니다. 보조 인스턴스를 만들 때 기본 인스턴스의 영역 ID를 DnsZonePartner 매개 변수 값으로 전달 하 여이를 수행 합니다. 

   > [!NOTE]
   > 관리 되는 인스턴스로 장애 조치 그룹을 구성 하는 방법에 대 한 자세한 자습서는 [장애 조치 (failover) 그룹에 관리 되는 인스턴스 추가](sql-database-managed-instance-failover-group-tutorial.md)

## <a name="upgrading-or-downgrading-a-primary-database"></a>주 데이터베이스 업그레이드 또는 다운그레이드

보조 데이터베이스와의 연결을 끊지 않고도 주 데이터베이스를 다른 컴퓨팅 크기(동일한 서비스 계층 내, 범용 및 중요 비즈니스용 사이 아님)로 업그레이드하거나 다운그레이드할 수 있습니다. 업그레이드할 때 모든 보조 데이터베이스를 먼저 업그레이드 한 다음 주 데이터베이스를 업그레이드 하는 것이 좋습니다. 다운 그레이드 하는 경우 순서를 반대로 바꿉니다. 먼저 주 복제본을 다운 그레이드 한 다음 모든 보조 데이터베이스를 다운 그레이드 합니다. 데이터베이스를 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 때 이 권장 사항이 적용됩니다.

이 순서는 더 낮은 SKU의 보조 데이터베이스가 오버 로드 되 고 업그레이드 또는 다운 그레이드 프로세스 중에 다시 시드 되어야 하는 문제를 방지 하기 위해 특별히 권장 됩니다. 주 복제본에 대 한 모든 읽기-쓰기 작업에 영향을 미칠 수 있으므로 주 읽기 전용으로 설정 하 여 문제를 방지할 수도 있습니다.

> [!NOTE]
> 장애 조치 그룹 구성의 일부로 보조 데이터베이스를 만든 경우 보조 데이터베이스를 다운그레이드하지 않는 것이 좋습니다. 이렇게 하면 장애 조치가 활성화된 후 데이터 계층에서 일반 워크로드를 처리할 수 있을 만큼 충분한 용량을 갖출 수 있습니다.

## <a name="preventing-the-loss-of-critical-data"></a>중요한 데이터 손실 방지

광역 네트워크의 높은 대기 시간으로 인해 연속 복사는 비동기 복제 메커니즘을 사용합니다. 비동기 복제를 수행하면 오류가 발생하는 경우에 일부 데이터 손실은 불가피합니다. 그러나 일부 애플리케이션은 데이터 손실이 없어야 합니다. 이러한 중요한 업데이트를 보호하기 위해 애플리케이션 개발자는 트랜잭션을 커밋한 후 즉시 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 시스템 프로시저를 호출할 수 있습니다. `sp_wait_for_database_copy_sync`를 호출 하면 마지막으로 커밋된 트랜잭션이 보조 데이터베이스로 전송 될 때까지 호출 스레드가 차단 됩니다. 그러나 전송된 트랜잭션이 보조 데이터베이스에서 재생 및 커밋될 때까지 기다리지 않습니다. `sp_wait_for_database_copy_sync`는 특정 연속 복사 링크로 범위가 지정 됩니다. 주 데이터베이스에 대한 연결 권한이 있는 모든 사용자는 이 프로시저를 호출할 수 있습니다.

> [!NOTE]
> `sp_wait_for_database_copy_sync`는 장애 조치 (failover) 후 데이터 손실을 방지 하지만 읽기 액세스를 위해 전체 동기화를 보장 하지는 않습니다. `sp_wait_for_database_copy_sync` 프로시저 호출로 인해 발생 하는 지연은 매우 중요할 수 있으며 호출 시 트랜잭션 로그의 크기에 따라 달라 집니다.

## <a name="failover-groups-and-point-in-time-restore"></a>장애 조치(failover) 그룹 및 지정 시간 복원

장애 조치(failover) 그룹과 함께 지정 시간 복원을 사용하는 방법에 대한 자세한 내용은 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

## <a name="limitations-of-failover-groups"></a>장애 조치 (failover) 그룹의 제한 사항

다음과 같은 제한 사항을 고려해야 합니다.

- 동일한 Azure 지역에 있는 두 서버 또는 인스턴스 간에 장애 조치 (Failover) 그룹을 만들 수 없습니다.
- 장애 조치 (Failover) 그룹 이름을 바꿀 수 없습니다. 그룹을 삭제 하 고 다른 이름으로 다시 만들어야 합니다. 
- 장애 조치 (failover) 그룹의 인스턴스에 대해서는 데이터베이스 이름 바꾸기가 지원 되지 않습니다. 데이터베이스의 이름을 바꾸려면 장애 조치 (failover) 그룹을 일시적으로 삭제 해야 합니다.

## <a name="programmatically-managing-failover-groups"></a>프로그래밍 방식으로 장애 조치(failover) 그룹 관리

앞에서 설명한 대로, 자동 장애 조치(failover) 그룹과 활성 지역 복제는 Azure PowerShell 및 REST API를 사용하여 프로그래밍 방식으로 관리할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다. 활성 지역 복제는 관리를 위해 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 및 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 비롯한 Azure Resource Manager API 세트를 포함합니다. 이러한 API는 리소스 그룹을 사용해야 하며 RBAC(역할 기반 보안)를 지원합니다. 액세스 역할을 구현하는 방법에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)을 참조하세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| Cmdlet | Description |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 서버에서 장애 조치 (failover) 그룹을 제거 합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 장애 조치 (failover) 그룹의 구성을 검색 합니다. |
| [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |장애 조치 (failover) 그룹의 구성을 수정 합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 장애 조치 (failover) 그룹의 장애 조치 (failover)를 보조 서버로 트리거합니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|장애 조치 (failover) 그룹에 하나 이상의 데이터베이스를 추가 합니다.|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>관리 되는 인스턴스로 SQL database 장애 조치 (failover) 그룹 관리

| Cmdlet | Description |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |이 명령은 장애 조치 (failover) 그룹을 만들고 주 및 보조 인스턴스에 등록 합니다.|
| [AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |장애 조치 (failover) 그룹의 구성을 수정 합니다.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |장애 조치 (failover) 그룹의 구성을 검색 합니다.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |장애 조치 (failover) 그룹의 장애 조치를 보조 인스턴스로 트리거합니다.|
| [AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 장애 조치(failover) 그룹을 제거합니다.|

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>단일 데이터베이스 및 탄력적 풀을 사용하여 SQL Database 장애 조치(failover) 관리

| 명령 | Description |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |장애 조치 그룹을 만들고 주 및 보조 서버 모두에 등록합니다|
| [az sql 장애 조치 (failover) 그룹 삭제](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 서버에서 장애 조치 (failover) 그룹을 제거 합니다. |
| [az sql 장애 조치 (failover)-그룹 표시](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 장애 조치 (failover) 그룹 구성을 검색 합니다. |
| [az sql 장애 조치 (failover)-그룹 업데이트](/cli/azure/sql/failover-group#az-sql-failover-group-update) |장애 조치 (failover) 그룹의 구성을 수정 하거나 하나 이상의 데이터베이스를 장애 조치 (failover) 그룹에 추가 합니다.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 장애 조치 (failover) 그룹의 장애 조치 (failover)를 보조 서버로 트리거합니다. |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>관리 되는 인스턴스로 SQL database 장애 조치 (failover) 그룹 관리

| 명령 | Description |
| --- | --- |
| [az sql instance-장애 조치 (failover) 그룹 만들기](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | 이 명령은 장애 조치 (failover) 그룹을 만들고 주 및 보조 인스턴스에 등록 합니다. |
| [az sql instance-장애 조치 (failover)-그룹 업데이트](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | 장애 조치 (failover) 그룹의 구성을 수정 합니다.|
| [az sql instance-장애 조치 (failover)-그룹 표시](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | 장애 조치 (failover) 그룹의 구성을 검색 합니다.|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | 장애 조치 (failover) 그룹의 장애 조치를 보조 인스턴스로 트리거합니다.|
| [az sql instance-장애 조치 (failover) 그룹 삭제](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | 장애 조치(failover) 그룹을 제거합니다. |

* * *

> [!IMPORTANT]
> 샘플 스크립트는 [단일 데이터베이스에 대한 장애 조치(failover) 그룹 구성 및 장애 조치(failover)](scripts/sql-database-add-single-db-to-failover-group-powershell.md)를 참조하세요.

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: 단일 및 풀링된 데이터베이스를 사용 하 여 SQL database 장애 조치 (failover) 그룹 관리

| API | Description |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 장애 조치(failover) 그룹을 만들거나 업데이트합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 서버에서 장애 조치 (failover) 그룹을 제거 합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 전체 데이터 동기화를 사용 하 여 현재 주 서버에서 보조 서버로 장애 조치 (failover)를 트리거합니다.|
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | 데이터를 동기화 하지 않고 현재 주 서버에서 보조 서버로 장애 조치 (failover)를 트리거합니다. 이 작업으로 인해 데이터가 손실 될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 장애 조치 (failover) 그룹의 구성을 검색 합니다. |
| [서버별 장애 조치(failover) 그룹 나열](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 서버의 장애 조치 (failover) 그룹을 나열 합니다. |
| [장애 조치(failover) 그룹 업데이트](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 장애 조치 (failover) 그룹의 구성을 업데이트 합니다. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: 관리 되는 인스턴스를 사용 하 여 장애 조치 그룹 관리

| API | Description |
| --- | --- |
| [장애 조치(failover) 그룹 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 장애 조치 (failover) 그룹의 구성을 만들거나 업데이트 합니다. |
| [장애 조치(failover) 그룹 삭제](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 인스턴스에서 장애 조치 (failover) 그룹을 제거 합니다. |
| [장애 조치(failover)(계획됨)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 전체 데이터 동기화를 사용 하 여 현재 주 인스턴스에서이 인스턴스로 장애 조치 (failover)를 트리거합니다. |
| [장애 조치(failover)로 인한 데이터 손실 허용](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | 데이터를 동기화 하지 않고 현재 주 인스턴스에서 보조 인스턴스로 장애 조치 (failover)를 트리거합니다. 이 작업으로 인해 데이터가 손실 될 수 있습니다. |
| [장애 조치 그룹 가져오기](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 장애 조치 (failover) 그룹의 구성을 검색 합니다. |
| [장애 조치(failover) 그룹 나열 - 위치별 목록](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 위치에 있는 장애 조치(failover) 그룹을 나열합니다. |

## <a name="next-steps"></a>다음 단계

- 자세한 자습서는를 참조 하세요.
    - [장애 조치 (failover) 그룹에 단일 데이터베이스 추가](sql-database-single-database-failover-group-tutorial.md)
    - [장애 조치 (failover) 그룹에 탄력적 풀 추가](sql-database-elastic-pool-failover-group-tutorial.md)
    - [장애 조치 (failover) 그룹에 관리 되는 인스턴스 추가](sql-database-managed-instance-failover-group-tutorial.md)
- 샘플 스크립트에 대해서는 다음을 참조하세요.
  - [PowerShell을 사용 하 여 Azure SQL Database에서 단일 데이터베이스에 대 한 활성 지역 복제 구성](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [PowerShell을 사용 하 여 Azure SQL Database에서 풀링된 데이터베이스에 대 한 활성 지역 복제 구성](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [PowerShell을 사용 하 여 Azure SQL Database 단일 데이터베이스를 장애 조치 (failover) 그룹에 추가](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 새로운 주 서버 및 데이터베이스의 인증 요구 사항에 대해 알아보려면 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
