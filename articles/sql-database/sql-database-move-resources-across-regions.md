---
title: 리소스를 다른 지역으로 이동 하는 방법
description: Azure SQL Database, Azure SQL 탄력적 풀 또는 Azure SQL 관리 되는 인스턴스를 다른 지역으로 이동 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821427"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Azure SQL 리소스를 다른 지역으로 이동 하는 방법

이 문서에서는 Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스를 새 지역으로 이동 하는 방법에 대 한 일반적인 워크플로에 대해 설명 합니다. 

## <a name="overview"></a>개요

기존 Azure SQL 리소스를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 비즈니스를 새 지역으로 확장 하 고 새 고객 기반에 맞게 최적화 하려고 합니다. 또는 준수 이유 때문에 작업을 다른 지역으로 이동 해야 합니다. 또는 Azure는 더 나은 근접성을 제공 하 고 고객 환경을 개선 하는 새 지역을 출시 했습니다.  

이 문서에서는 리소스를 다른 지역으로 이동 하는 일반적인 워크플로를 제공 합니다. 워크플로는 다음 단계로 구성 됩니다. 

- 이동에 대 한 필수 구성 요소 확인 
- 범위에서 리소스 이동 준비
- 준비 프로세스 모니터링
- 이동 프로세스 테스트
- 실제 이동 시작 
- 원본 영역에서 리소스를 제거 합니다. 


> [!NOTE]
> 이 문서는 Azure 공용 클라우드 내에서 또는 동일한 소 버린 클라우드 내에서의 마이그레이션에 적용 됩니다. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>단일 데이터베이스 이동

### <a name="verify-prerequisites"></a>필수 조건 확인 

1. 각 원본 서버에 대 한 대상 논리 서버를 만듭니다. 
1. [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)을 사용 하 여 적절 한 예외를 사용 하 여 방화벽을 구성 합니다.  
1. 올바른 로그인을 사용 하 여 논리 서버를 구성 합니다. 구독 관리자 또는 SQL server 관리자가 아닌 경우 관리자와 협력 하 여 필요한 사용 권한을 할당 합니다. 자세한 내용은 [재해 복구 후에 AZURE SQL database 보안을 관리 하는 방법](sql-database-geo-replication-security-config.md)을 참조 하세요. 
1. 데이터베이스가 TDE를 사용 하 여 암호화 되 고 Azure key vault에서 사용자 고유의 암호화 키를 사용 하는 경우 올바른 암호화 자료가 대상 지역에 프로 비전 되었는지 확인 합니다. 자세한 내용은 [에서 고객 관리 키를 사용 하는 AZURE SQL 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md) 를 참조 하세요 Azure Key Vault
1. 데이터베이스 수준 감사를 사용 하는 경우 사용 하지 않도록 설정 하 고 대신 서버 수준 감사를 사용 하도록 설정 합니다. 장애 조치 (failover) 후 데이터베이스 수준 감사에는 지역 간 트래픽이 필요 하며,이는 이동 후에는 필요 하지 않습니다. 
1. 서버 수준 감사에 대해 다음을 확인 합니다.
   - 기존 감사 로그가 포함 된 저장소 컨테이너, Log Analytics 또는 이벤트 허브가 대상 지역으로 이동 됩니다. 
   - 감사는 대상 서버에서 구성 됩니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing.md)을 참조하세요. 
1. 인스턴스에서 LTR (장기 보존 정책)을 사용 하는 경우 기존 LTR 백업은 현재 서버와 연결 된 상태로 유지 됩니다. 대상 서버가 다르므로 원본 서버를 사용 하 여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다 (서버가 삭제 된 경우에도). 

  > [!NOTE]
  > 소 버린 클라우드와 공용 지역 간에 이동 하는 데 충분 하지 않습니다. 이러한 마이그레이션을 수행 하려면 LTR 백업을 대상 서버로 이동 해야 합니다 .이는 현재 지원 되지 않습니다. 

### <a name="prepare-resources"></a>리소스 준비

1. 원본의 논리 서버와 대상의 논리 서버 간에 [장애 조치 (failover) 그룹](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) 을 만듭니다.  
1. 장애 조치 (failover) 그룹으로 이동 하려는 데이터베이스를 추가 합니다. 
    - 추가 된 모든 데이터베이스의 복제는 자동으로 시작 됩니다. 자세한 내용은 [단일 데이터베이스에서 장애 조치 (failover) 그룹 사용에 대 한 모범 사례](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)를 참조 하세요. 
 
### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

정기적으로 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 를 호출 하 여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. `Get-AzSqlDatabaseFailoverGroup`의 출력 개체는 **ReplicationState**에 대 한 속성을 포함 합니다. 
   - **ReplicationState = 2** (CATCH_UP)는 데이터베이스가 동기화 되었으며 안전 하 게 장애 조치 (failover) 할 수 있음을 나타냅니다. 
   - **ReplicationState = 0** (시드)은 데이터베이스가 아직 시드 되지 않아 장애 조치 (failover) 시도가 실패 했음을 나타냅니다. 

### <a name="test-synchronization"></a>동기화 테스트

**ReplicationState** 가 `2`되 면 `<fog-name>.secondary.database.windows.net` 보조 엔드포인트을 사용 하 여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결 하 고 데이터베이스에 대 한 쿼리를 수행 하 여 연결, 적절 한 보안 구성 및 데이터 복제를 보장 합니다. 

### <a name="initiate-the-move"></a>이동 시작

1. `<fog-name>.secondary.database.windows.net`보조 엔드포인트을 사용 하 여 대상 서버에 연결 합니다.
1. [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 를 사용 하 여 보조 관리 되는 인스턴스를 전체 동기화를 사용 하는 기본으로 전환 합니다. 이 작업은 성공 하거나 롤백될 예정입니다. 
1. `nslook up <fog-name>.secondary.database.windows.net` 사용 하 여 명령이 성공적으로 완료 되었는지 확인 하 고 DNS CNAME 항목이 대상 지역 IP 주소를 가리키는지 확인 합니다. 스위치 명령이 실패 하면 CNAME은 업데이트 되지 않습니다. 

### <a name="remove-the-source-databases"></a>원본 데이터베이스 제거

이동이 완료 되 면 불필요 한 요금을 방지 하기 위해 원본 지역의 리소스를 제거 합니다. 

1. [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)를 사용 하 여 장애 조치 (failover) 그룹을 삭제 합니다. 
1. 원본 서버의 각 데이터베이스에 대해 [AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) 를 사용 하 여 각 원본 데이터베이스를 삭제 합니다. 그러면 지역에서 복제 링크가 자동으로 종료 됩니다. 
1. [AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)를 사용 하 여 원본 서버를 삭제 합니다. 
1. 주요 자격 증명 모음, 감사 저장소 컨테이너, 이벤트 허브, AAD 인스턴스 및 기타 종속 리소스를 제거 하 여 요금 청구를 중지 합니다. 

## <a name="move-elastic-pools"></a>탄력적 풀 이동

### <a name="verify-prerequisites"></a>필수 조건 확인 

1. 각 원본 서버에 대 한 대상 논리 서버를 만듭니다. 
1. [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)을 사용 하 여 적절 한 예외를 사용 하 여 방화벽을 구성 합니다. 
1. 올바른 로그인을 사용 하 여 논리 서버를 구성 합니다. 구독 관리자 또는 SQL server 관리자가 아닌 경우 관리자와 협력 하 여 필요한 사용 권한을 할당 합니다. 자세한 내용은 [재해 복구 후에 AZURE SQL database 보안을 관리 하는 방법](sql-database-geo-replication-security-config.md)을 참조 하세요. 
1. 데이터베이스가 TDE를 사용 하 여 암호화 되 고 Azure key vault에서 사용자 고유의 암호화 키를 사용 하는 경우 올바른 암호화 자료가 대상 지역에 프로 비전 되었는지 확인 합니다.
1. 각 원본 탄력적 풀에 대 한 대상 탄력적 풀을 만들어 동일한 서비스 계층에서 동일한 이름과 크기를 가진 풀이 만들어졌는지 확인 합니다. 
1. 데이터베이스 수준 감사를 사용 하는 경우 사용 하지 않도록 설정 하 고 대신 서버 수준 감사를 사용 하도록 설정 합니다. 장애 조치 (failover) 후 데이터베이스 수준 감사에는 지역 간 트래픽이 필요 하거나 이동 후에는 가능 하지 않습니다. 
1. 서버 수준 감사에 대해 다음을 확인 합니다.
    - 기존 감사 로그가 포함 된 저장소 컨테이너, Log Analytics 또는 이벤트 허브가 대상 지역으로 이동 됩니다.
    - 감사 구성은 대상 서버에서 구성 됩니다. 자세한 내용은 [SQL database 감사](sql-database-auditing.md)를 참조 하세요.
1. 인스턴스에서 LTR (장기 보존 정책)을 사용 하는 경우 기존 LTR 백업은 현재 서버와 연결 된 상태로 유지 됩니다. 대상 서버가 다르므로 원본 서버를 사용 하 여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다 (서버가 삭제 된 경우에도). 

  > [!NOTE]
  > 소 버린 클라우드와 공용 지역 간에 이동 하는 데 충분 하지 않습니다. 이러한 마이그레이션을 수행 하려면 LTR 백업을 대상 서버로 이동 해야 합니다 .이는 현재 지원 되지 않습니다. 

### <a name="prepare-to-move"></a>이동할 준비
 
1.  원본 논리 서버의 각 탄력적 풀과 대상 서버에서 해당 하는 탄력적 풀 사이에 별도의 [장애 조치 (failover) 그룹](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) 을 만듭니다. 
1.  풀의 모든 데이터베이스를 장애 조치 (failover) 그룹에 추가 합니다. 
    - 추가 된 데이터베이스의 복제는 자동으로 시작 됩니다. 자세한 내용은 [탄력적 풀을 사용 하는 장애 조치 (failover) 그룹에 대 한 모범 사례](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)를 참조 하세요. 

  > [!NOTE]
  > 여러 탄력적 풀을 포함 하는 장애 조치 (failover) 그룹을 만들 수 있지만 각 풀에 대해 별도의 장애 조치 (failover) 그룹을 만드는 것이 좋습니다. 이동 해야 하는 여러 탄력적 풀에 데이터베이스 수가 많은 경우 준비 단계를 병렬로 실행 한 다음 이동 단계를 병렬로 시작할 수 있습니다. 이 프로세스는 더 효율적으로 확장 되며 동일한 장애 조치 (failover) 그룹에 여러 탄력적 풀을 보유 하는 것에 비해 시간이 더 짧습니다. 

### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

정기적으로 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 를 호출 하 여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. `Get-AzSqlDatabaseFailoverGroup`의 출력 개체는 **ReplicationState**에 대 한 속성을 포함 합니다. 
   - **ReplicationState = 2** (CATCH_UP)는 데이터베이스가 동기화 되었으며 안전 하 게 장애 조치 (failover) 할 수 있음을 나타냅니다. 
   - **ReplicationState = 0** (시드)은 데이터베이스가 아직 시드 되지 않아 장애 조치 (failover) 시도가 실패 했음을 나타냅니다. 

### <a name="test-synchronization"></a>동기화 테스트
 
**ReplicationState** 가 `2`되 면 `<fog-name>.secondary.database.windows.net` 보조 엔드포인트을 사용 하 여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결 하 고 데이터베이스에 대 한 쿼리를 수행 하 여 연결, 적절 한 보안 구성 및 데이터 복제를 보장 합니다. 

### <a name="initiate-the-move"></a>이동 시작
 
1. `<fog-name>.secondary.database.windows.net`보조 엔드포인트을 사용 하 여 대상 서버에 연결 합니다.
1. [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 를 사용 하 여 보조 관리 되는 인스턴스를 전체 동기화를 사용 하는 기본으로 전환 합니다. 이 작업은 성공 하거나 롤백될 예정입니다. 
1. `nslook up <fog-name>.secondary.database.windows.net` 사용 하 여 명령이 성공적으로 완료 되었는지 확인 하 고 DNS CNAME 항목이 대상 지역 IP 주소를 가리키는지 확인 합니다. 스위치 명령이 실패 하면 CNAME은 업데이트 되지 않습니다. 

### <a name="remove-the-source-elastic-pools"></a>원본 탄력적 풀 제거
 
이동이 완료 되 면 불필요 한 요금을 방지 하기 위해 원본 지역의 리소스를 제거 합니다. 

1. [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)를 사용 하 여 장애 조치 (failover) 그룹을 삭제 합니다.
1. [AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)를 사용 하 여 원본 서버에서 각 원본 탄력적 풀을 삭제 합니다. 
1. [AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)를 사용 하 여 원본 서버를 삭제 합니다. 
1. 주요 자격 증명 모음, 감사 저장소 컨테이너, 이벤트 허브, AAD 인스턴스 및 기타 종속 리소스를 제거 하 여 요금 청구를 중지 합니다. 

## <a name="move-managed-instance"></a>관리 되는 인스턴스 이동

### <a name="verify-prerequisites"></a>필수 조건 확인
 
1. 각 원본 관리 되는 인스턴스에 대해 대상 지역에 동일한 크기의 대상 관리 되는 인스턴스를 만듭니다.  
1. 관리 되는 인스턴스의 네트워크를 구성 합니다. 자세한 내용은 [네트워크 구성](sql-database-howto-managed-instance.md#network-configuration)을 참조 하세요.
1. 올바른 로그인을 사용 하 여 대상 master 데이터베이스를 구성 합니다. 구독 관리자 또는 SQL server 관리자가 아닌 경우 관리자와 협력 하 여 필요한 사용 권한을 할당 합니다. 
1. 데이터베이스가 TDE를 사용 하 여 암호화 되 고 Azure key vault에서 사용자 고유의 암호화 키를 사용 하는 경우 동일한 암호화 키를 가진 AKV 원본 및 대상 지역에 모두 존재 하는지 확인 합니다. 자세한 내용은 [Azure Key Vault에서 고객 관리 키를 사용 하는 Tde](transparent-data-encryption-byok-azure-sql.md)를 참조 하세요.
1. 인스턴스에 대해 감사가 설정 된 경우 다음을 확인 합니다.
    - 기존 로그가 포함 된 저장소 컨테이너 또는 이벤트 허브가 대상 지역으로 이동 됩니다. 
    - 감사는 대상 인스턴스에 구성 됩니다. 자세한 내용은 [관리 되는 인스턴스로 감사](sql-database-managed-instance-auditing.md)를 참조 하세요.
1. 인스턴스에서 LTR (장기 보존 정책)을 사용 하는 경우 기존 LTR 백업은 현재 서버와 연결 된 상태로 유지 됩니다. 대상 서버가 다르므로 원본 서버를 사용 하 여 원본 지역의 이전 LTR 백업에 액세스할 수 있습니다 (서버가 삭제 된 경우에도). 

  > [!NOTE]
  > 소 버린 클라우드와 공용 지역 간에 이동 하는 데 충분 하지 않습니다. 이러한 마이그레이션을 수행 하려면 LTR 백업을 대상 서버로 이동 해야 합니다 .이는 현재 지원 되지 않습니다. 

### <a name="prepare-resources"></a>리소스 준비

각 원본 인스턴스와 해당 대상 인스턴스 간에 장애 조치 (failover) 그룹을 만듭니다.
    - 각 인스턴스에 있는 모든 데이터베이스의 복제는 자동으로 시작 됩니다. 자세한 내용은 [자동 장애 조치 그룹](sql-database-auto-failover-group.md) 을 참조 하세요.

 
### <a name="monitor-the-preparation-process"></a>준비 프로세스 모니터링

정기적으로 [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) 를 호출 하 여 원본에서 대상으로 데이터베이스의 복제를 모니터링할 수 있습니다. `Get-AzSqlDatabaseFailoverGroup`의 출력 개체는 **ReplicationState**에 대 한 속성을 포함 합니다. 
   - **ReplicationState = 2** (CATCH_UP)는 데이터베이스가 동기화 되었으며 안전 하 게 장애 조치 (failover) 할 수 있음을 나타냅니다. 
   - **ReplicationState = 0** (시드)은 데이터베이스가 아직 시드 되지 않아 장애 조치 (failover) 시도가 실패 했음을 나타냅니다. 

### <a name="test-synchronization"></a>동기화 테스트

**ReplicationState** 가 `2`되 면 `<fog-name>.secondary.database.windows.net` 보조 엔드포인트을 사용 하 여 각 데이터베이스 또는 데이터베이스의 하위 집합에 연결 하 고 데이터베이스에 대 한 쿼리를 수행 하 여 연결, 적절 한 보안 구성 및 데이터 복제를 보장 합니다. 

### <a name="initiate-the-move"></a>이동 시작 

1. `<fog-name>.secondary.database.windows.net`보조 엔드포인트을 사용 하 여 대상 서버에 연결 합니다.
1. [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) 를 사용 하 여 보조 관리 되는 인스턴스를 전체 동기화를 사용 하는 기본으로 전환 합니다. 이 작업은 성공 하거나 롤백될 예정입니다. 
1. `nslook up <fog-name>.secondary.database.windows.net` 사용 하 여 명령이 성공적으로 완료 되었는지 확인 하 고 DNS CNAME 항목이 대상 지역 IP 주소를 가리키는지 확인 합니다. 스위치 명령이 실패 하면 CNAME은 업데이트 되지 않습니다. 

### <a name="remove-the-source-managed-instances"></a>원본 관리 되는 인스턴스 제거
이동이 완료 되 면 불필요 한 요금을 방지 하기 위해 원본 지역의 리소스를 제거 합니다. 

1. [AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)를 사용 하 여 장애 조치 (failover) 그룹을 삭제 합니다. 장애 조치 (failover) 그룹 구성을 삭제 하 고 두 인스턴스 간의 지역에서 복제 링크를 종료 합니다. 
1. [AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)를 사용 하 여 원본 관리 되는 인스턴스를 삭제 합니다. 
1. 리소스 그룹에서 가상 클러스터, 가상 네트워크 및 보안 그룹과 같은 추가 리소스를 제거 합니다. 

## <a name="next-steps"></a>다음 단계 

Azure SQL Database 마이그레이션된 후에 [관리](sql-database-manage-after-migration.md) 합니다. 

