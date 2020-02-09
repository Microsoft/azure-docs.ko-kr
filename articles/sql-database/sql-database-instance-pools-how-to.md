---
title: 인스턴스 풀 방법 가이드 (미리 보기)
description: 이 문서에서는 Azure SQL Database 인스턴스 풀 (미리 보기)을 만들고 관리 하는 방법을 설명 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 5a45b9e3ba59a91f580ce0f2dc180adf5d20c87d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754047"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL Database instance 풀 (미리 보기) 방법 가이드

이 문서에서는 [인스턴스 풀](sql-database-instance-pools.md)을 만들고 관리 하는 방법에 대 한 세부 정보를 제공 합니다.

## <a name="instance-pool-operations"></a>인스턴스 풀 작업

다음 표에서는 Azure Portal 및 PowerShell에서 인스턴스 풀 및 해당 가용성과 관련 된 사용 가능한 작업을 보여 줍니다.

|명령|Azure Portal|PowerShell|
|:---|:---|:---|
|인스턴스 풀 만들기|아닙니다.|예|
|인스턴스 풀 업데이트 (제한 된 속성 수)|아닙니다. |예 |
|인스턴스 풀 사용 및 속성 확인|아닙니다.|예 |
|인스턴스 풀 삭제|아닙니다.|예|
|인스턴스 풀 내에서 관리 되는 인스턴스 만들기|아닙니다.|예|
|관리 되는 인스턴스 리소스 사용 현황 업데이트|예 |예|
|관리 되는 인스턴스 사용 및 속성 확인|예|예|
|풀에서 관리 되는 인스턴스 삭제|예|예|
|풀에 배치 된 관리 되는 인스턴스에서 데이터베이스 만들기|예|예|
|관리 되는 인스턴스에서 데이터베이스 삭제|예|예|

사용 가능한 [PowerShell 명령](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |Description |
|:---|:---|
|[AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Azure SQL Database 인스턴스 풀을 만듭니다. |
|[AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Azure SQL 인스턴스 풀에 대 한 정보를 반환 합니다. |
|[AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Azure SQL Database 인스턴스 풀의 속성을 설정 합니다. |
|[AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Azure SQL Database 인스턴스 풀을 제거 합니다. |
|[AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Azure SQL 인스턴스 풀 사용에 대 한 정보를 반환 합니다. |


PowerShell을 사용 하려면 [최신 버전의 Powershell Core를 설치](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)하 고 지침에 따라 [Azure PowerShell 모듈을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)합니다.

풀과 단일 인스턴스 모두의 인스턴스와 관련 된 작업의 경우 표준 [관리 되는 인스턴스 명령을](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)사용 하지만 풀의 인스턴스에 대해 이러한 명령을 사용 하는 경우 *인스턴스 풀 이름* 속성을 채워야 합니다.

## <a name="how-to-deploy-managed-instances-into-pools"></a>풀에 관리 되는 인스턴스를 배포 하는 방법

풀에 인스턴스를 배포 하는 프로세스는 다음 두 단계로 구성 됩니다.

1. 일회용 인스턴스 풀 배포. 이 작업은 장기 실행 작업입니다. 여기서 기간은 [빈 서브넷에서 만든 단일 인스턴스](sql-database-managed-instance.md#managed-instance-management-operations)를 배포 하는 것과 같습니다.

2. 인스턴스 풀의 반복적인 인스턴스 배포 이 작업의 일부로 instance pool 매개 변수를 명시적으로 지정 해야 합니다. 일반적으로 최대 5 분이 걸리는 비교적 빠른 작업입니다.

공개 미리 보기에서는 PowerShell 및 리소스 관리자 템플릿만 사용 하 여 두 단계를 모두 지원 합니다. Azure Portal 환경을 현재 사용할 수 없습니다.

관리 되는 인스턴스를 풀에 배포한 후에는 Azure Portal를 사용 하 여 가격 책정 계층 페이지에서 해당 속성을 변경할 *수* 있습니다.


## <a name="create-an-instance-pool"></a>인스턴스 풀 만들기

인스턴스 풀을 만들려면 다음을 수행 합니다.

1. [서브넷을 사용 하 여 가상 네트워크를 만듭니다](#create-a-virtual-network-with-a-subnet).
2. [인스턴스 풀을 만듭니다](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>서브넷을 사용하여 가상 네트워크 만들기 

동일한 가상 네트워크 내에 여러 인스턴스 풀을 놓으려면 다음 문서를 참조 하세요.

- [Azure SQL Database 관리 되는 인스턴스의 VNet 서브넷 크기를 확인](sql-database-managed-instance-determine-size-vnet-subnet.md)합니다.
- [Azure Portal 템플릿을](sql-database-managed-instance-create-vnet-subnet.md) 사용 하 여 새 가상 네트워크 및 서브넷을 만들거나 지침에 따라 [기존 가상 네트워크를 준비](sql-database-managed-instance-configure-vnet-subnet.md)합니다.
 


### <a name="create-an-instance-pool"></a>인스턴스 풀 만들기 

이전 단계를 완료 한 후에는 인스턴스 풀을 만들 준비가 된 것입니다.

인스턴스 풀에는 다음과 같은 제한 사항이 적용 됩니다.

- 공개 미리 보기에서는 범용 및 Gen5 사용할 수 있습니다.
- 풀 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 하이픈으로 시작할 수 없습니다.
- AHB (Azure 하이브리드 혜택)를 사용 하려는 경우 인스턴스 풀 수준에서 적용 됩니다. 풀을 만드는 동안 라이선스 유형을 설정 하거나 생성 후 언제 든 지 업데이트할 수 있습니다.

> [!IMPORTANT]
> 인스턴스 풀을 배포 하는 작업은 약 4.5 시간이 소요 되는 장기 실행 작업입니다.

네트워크 매개 변수를 가져오려면:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

인스턴스 풀을 만들려면 다음을 수행 합니다.

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> 인스턴스 풀을 배포 하는 작업은 장기 실행 작업 이므로이 문서의 다음 단계 중 하나를 실행 하기 전에 완료 될 때까지 기다려야 합니다.

## <a name="create-a-managed-instance-inside-the-pool"></a>풀 내에서 관리 되는 인스턴스 만들기 

인스턴스 풀을 성공적으로 배포한 후에는 그 안에 인스턴스를 만들 시간입니다.

관리 되는 인스턴스를 만들려면 다음 명령을 실행 합니다.

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

풀 내에 인스턴스를 배포 하는 데 몇 분 정도 걸립니다. 첫 번째 인스턴스를 만든 후에는 추가 인스턴스를 만들 수 있습니다.

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>인스턴스 내에 데이터베이스 만들기 

풀 내에 있는 관리 되는 인스턴스에서 데이터베이스를 만들고 관리 하려면 단일 인스턴스 명령을 사용 합니다.

관리 되는 인스턴스 내에 데이터베이스를 만들려면 다음을 수행 합니다.

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>인스턴스 풀 사용 가져오기 
 
풀 내의 인스턴스 목록을 가져오려면:

```powershell
$instancePool | Get-AzSqlInstance
```


풀 리소스 사용을 가져오려면 다음을 수행 합니다.

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


풀 및 해당 풀의 인스턴스에 대 한 자세한 사용 개요를 보려면 다음을 수행 합니다.

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

인스턴스의 데이터베이스를 나열 하려면 다음을 수행 합니다.

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 풀 당 데이터베이스 수는 100 개로 제한 됩니다 (인스턴스당이 아님).


## <a name="scale-a-managed-instance-inside-a-pool"></a>풀 내에서 관리 되는 인스턴스 크기 조정 


관리 되는 인스턴스를 데이터베이스로 채운 후에는 저장소 또는 성능에 대 한 인스턴스 제한에 도달할 수 있습니다. 이 경우 풀 사용을 초과 하지 않은 경우 인스턴스를 확장할 수 있습니다.
풀 내에서 관리 되는 인스턴스의 크기를 조정 하는 작업은 몇 분 정도 소요 됩니다. 크기를 조정 하기 위한 필수 구성 요소는 인스턴스 풀 수준에서 vCores 및 storage를 사용할 수 있습니다.

VCores 및 저장소 크기의 수를 업데이트 하려면 다음을 수행 합니다.

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


저장소 크기만 업데이트 하려면 다음을 수행 합니다.

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>풀 내의 관리 되는 인스턴스에 연결

풀의 관리 되는 인스턴스에 연결 하려면 다음 두 단계가 필요 합니다.

1. [인스턴스에 대 한 공용 엔드포인트을 사용 하도록 설정](#enable-the-public-endpoint-for-the-instance)합니다.
2. [네트워크 보안 그룹 (NSG)에 인바운드 규칙을 추가](#add-an-inbound-rule-to-the-network-security-group)합니다.

두 단계가 모두 완료 된 후에는 인스턴스를 만드는 동안 제공 된 공용 엔드포인트 주소, 포트 및 자격 증명을 사용 하 여 인스턴스에 연결할 수 있습니다. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>인스턴스에 대 한 공용 엔드포인트 사용

인스턴스에 대 한 공용 엔드포인트을 사용 하도록 설정 하는 작업은 Azure Portal 또는 다음 PowerShell 명령을 사용 하 여 수행할 수 있습니다.


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

이 매개 변수는 인스턴스를 만드는 동안에도 설정할 수 있습니다.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>네트워크 보안 그룹에 인바운드 규칙 추가 

이 단계는 Azure Portal 또는 PowerShell 명령을 사용 하 여 수행할 수 있으며, 관리 되는 인스턴스에 대해 서브넷이 준비 된 후 언제 든 지 수행할 수 있습니다.

자세한 내용은 [네트워크 보안 그룹에서 공용 엔드포인트 트래픽 허용](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)을 참조 하세요.


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>인스턴스 풀 내에서 기존 단일 인스턴스 이동 
 
풀에서 인스턴스를 이동 하는 것은 공개 미리 보기 제한 사항 중 하나입니다. 사용할 수 있는 해결 방법은 풀 외부의 인스턴스에서 이미 풀에 있는 인스턴스로 데이터베이스의 지정 시간 복원에 의존 합니다. 

두 인스턴스는 동일한 구독 및 지역에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원 되지 않습니다.

이 프로세스의 가동 중지 시간이 발생 합니다.

기존 데이터베이스를 이동 하려면:

1. 마이그레이션하는 관리 되는 인스턴스에서 작업을 일시 중지 합니다.
2. 시스템 데이터베이스를 생성 하는 스크립트를 생성 하 여 인스턴스 풀 내에 있는 인스턴스에서이를 실행 합니다.
3. 단일 인스턴스에서 풀의 인스턴스로 각 데이터베이스의 지정 시간 복원을 수행 합니다.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. 응용 프로그램이 새 인스턴스를 가리키고 워크 로드를 다시 시작 합니다.

데이터베이스가 여러 개 있는 경우 각 데이터베이스에 대해이 프로세스를 반복 합니다.


## <a name="next-steps"></a>다음 단계

- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [Managed Instance VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요.
- 백업 파일에서 Managed Instance를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 Managed Instance 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- 기본 제공 되는 문제 해결 인텔리전스를 사용 하 여 관리 되는 인스턴스 데이터베이스 성능의 고급 모니터링은 [Azure SQL 분석를 사용 하 여 Azure SQL Database 모니터링](../azure-monitor/insights/azure-sql.md)을 참조 하세요.
- 가격 정보는 [SQL Database Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
