---
title: 가용성 그룹 구성 (Azure Portal)
description: Azure Portal를 사용 하 여 Azure에서 Windows 장애 조치 (failover) 클러스터, 가용성 그룹 수신기 및 내부 부하 분산 SQL Server VM 장치를 만들 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 46e2563b0d1c26c984616b523a367c8b2cff7aaa
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038574"
---
# <a name="configure-an-availability-group-for-sql-server-on-azure-vm-azure-portal---preview"></a>Azure VM에서 SQL Server에 대 한 가용성 그룹 구성 (Azure Portal-미리 보기)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 [Azure Portal](https://portal.azure.com) 를 사용 하 여 Azure vm에서 SQL Server에 대 한 가용성 그룹을 구성 하는 방법을 설명 합니다. 

Azure Portal를 사용 하 여 새 클러스터를 만들거나 기존 클러스터를 온보드 한 다음 가용성 그룹, 수신기 및 내부 부하 분산 장치를 만듭니다. 

   > [!NOTE]
   > 이 기능은 현재 미리 보기로 제공 되며 원하는 지역을 사용할 수 없는 경우 잠시 후에 다시 확인 하세요. 


## <a name="prerequisites"></a>필수 구성 요소

Azure Portal를 사용 하 여 Always On 가용성 그룹을 구성 하려면 다음 필수 구성 요소가 있어야 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- Azure에서 하나 이상의 도메인에 가입 된 Vm은 *동일한* 가용성 집합 또는 [전체 관리 모드에서 SQL vm 리소스 공급자에 등록](sql-vm-resource-provider-register.md) 되어 있고 각 vm의 SQL Server 서비스에 대해 동일한 도메인 계정을 사용 하는 *다른* 가용성 영역에서 [SQL Server 2016 이상 Enterprise edition을 실행](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 합니다.
- 2개의 사용 가능한(엔터티에서 사용하지 않음) IP 주소. 하나는 내부 부하 분산 장치용입니다. 다른 하나는 가용성 그룹과 동일한 서브넷 내의 가용성 그룹 수신기용입니다. 기존 부하 분산 장치를 사용 하는 경우 가용성 그룹 수신기에 대해 사용 가능한 IP 주소가 하나만 필요 합니다. 

## <a name="permissions"></a>사용 권한

Azure Portal를 사용 하 여 가용성 그룹을 구성 하려면 다음 계정 권한이 필요 합니다. 

- 도메인에서 **컴퓨터 개체 만들기** 권한이 있는 기존 도메인 사용자 계정. 예를 들어 도메인 관리자 계정에는 일반적으로 충분한 권한이 있습니다(예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server를 제어하는 도메인 사용자 계정. 이 계정은 가용성 그룹에 추가 하려는 모든 SQL Server VM 동일한 계정 이어야 합니다. 

## <a name="configure-cluster"></a>클러스터 구성

Azure Portal를 사용 하 여 클러스터를 구성 합니다. 새 클러스터를 만들거나 기존 클러스터가 이미 있는 경우 포털 관리 효율성을 위해 SQL VM 리소스 공급자에이를 등록할 수 있습니다.


### <a name="create-a-new-cluster"></a>새 클러스터 만들기

클러스터가 이미 있는 경우이 섹션을 건너뛰고 대신 [기존 클러스터 온보드](#onboard-existing-cluster) 로 이동 합니다. 

기존 클러스터가 아직 없는 경우 다음 단계에 Azure Portal를 사용 하 여 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 컴퓨터](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동 합니다. 
1. **설정**아래에서 **고가용성** 을 선택 합니다. 
1. **+ 새 Windows Server 장애 조치 클러스터** 를 선택 하 여 **Windows 장애 조치 (Failover) 클러스터 구성** 페이지를 엽니다.  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="포털에서 + 새 클러스터를 선택 하 여 새 클러스터 만들기":::

1. 클러스터 이름을 지정 하 고 클라우드 감시로 사용할 저장소 계정을 제공 합니다. 기존 저장소 계정을 사용 하거나 **새로 만들기** 를 선택 하 여 새 저장소 계정을 만듭니다. 스토리지 계정 이름은 3자에서 24자 사이여야 하며 숫자와 소문자만 사용해야 합니다.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="클러스터에 대 한 이름, 저장소 계정 및 자격 증명을 제공 합니다.":::

1. **Windows Server 장애 조치 (Failover) 클러스터 자격 증명** 을 확장 하 여 SQL Server 서비스 계정에 대 한 [자격 증명](https://docs.microsoft.com/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) 을 제공 하 고, SQL Server 서비스에 사용 되는 계정과 다른 경우 클러스터 운영자 및 부트스트랩 계정을 제공 합니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="SQL 서비스 계정, 클러스터 운영자 계정 및 클러스터 부트스트랩 계정에 대 한 자격 증명을 제공 합니다.":::

1. 클러스터에 추가 하려는 SQL Server Vm을 선택 합니다. 다시 시작 해야 하는지 여부를 확인 하 고 주의 해 서 진행 합니다. 모든 관리 효율성 모드로 SQL VM 리소스 공급자에 등록 되어 있고 기본 SQL Server VM와 동일한 가상 네트워크에 있는 Vm만 표시 됩니다. 
1. **적용** 을 선택 하 여 클러스터를 만듭니다. 

위쪽 탐색 모음의 종 모양 아이콘에서 액세스할 수 있는 **활동 로그** 에서 배포 상태를 확인할 수 있습니다. 

### <a name="onboard-existing-cluster"></a>기존 클러스터 온보드

SQL Server VM 환경에 구성 된 클러스터가 이미 있는 경우 Azure Portal에서 등록할 수 있습니다.

이렇게 하려면 다음 단계를 따르십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 컴퓨터](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동 합니다. 
1. **설정**아래에서 **고가용성** 을 선택 합니다. 
1. **기존 Windows Server 장애 조치 (Failover) 클러스터 온보드** 를 선택 하 여 **Windows Server 장애 조치 (failover) 클러스터** 등록 페이지를 엽니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="SQL 가상 컴퓨터 리소스의 고가용성 페이지에서 기존 클러스터를 등록 합니다.":::

1. 클러스터에 대 한 설정을 검토 합니다. 
1. 클러스터를 등록 하려면 [ **적용** ]을 선택한 다음 계속 하려면 [ **예]** 를 선택 합니다.


## <a name="create-availability-group"></a>가용성 그룹 만들기

클러스터가 만들어지거나 등록 된 후 Azure Portal를 사용 하 여 가용성 그룹을 만듭니다. 이렇게 하려면 다음 단계를 따르십시오.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 컴퓨터](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동 합니다. 
1. **설정**아래에서 **고가용성** 을 선택 합니다. 
1. **+ 새로 만들기 Always On 가용성 그룹** 을 선택 하 여 **가용성 그룹 만들기** 페이지를 엽니다.

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="새 always on 가용성 그룹을 선택 하 여 가용성 그룹 만들기 페이지를 엽니다.":::

1. 가용성 그룹의 이름을 입력 합니다. 
1. **수신기 구성** 을 선택 하 여 **가용성 그룹 수신기 구성** 페이지를 엽니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="가용성 그룹의 이름을 제공 하 고 수신기를 구성 합니다.":::

1. 값을 입력 하 고 기존 부하 분산 장치를 사용 하거나 **새로 만들기** 를 선택 하 여 새 부하 분산 장치를 만듭니다.  **적용** 을 선택 하 여 설정을 저장 하 고 수신기 및 부하 분산 장치를 만듭니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="양식의 값을 입력 하 여 새 수신기 및 부하 분산 장치를 만듭니다.":::

1. **+ 복제본 선택** 을 선택 하 여 **가용성 그룹 복제본 구성** 페이지를 엽니다.
1. 가용성 그룹에 추가 하려는 가상 머신을 선택 하 고 비즈니스 요구에 가장 적합 한 가용성 그룹 설정을 선택 합니다. **적용** 을 선택 하 여 설정을 저장 합니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Vm을 선택 하 여 가용성 그룹에 추가 하 고 비즈니스에 적합 한 설정을 구성 합니다.":::

1. 가용성 그룹 설정을 확인 한 다음 **적용** 을 선택 하 여 가용성 그룹을 만듭니다. 

위쪽 탐색 모음의 종 모양 아이콘에서 액세스할 수 있는 **활동 로그** 에서 배포 상태를 확인할 수 있습니다. 

  > [!NOTE]
  > 가용성 그룹에 데이터베이스를 추가할 때까지 Azure Portal의 **고가용성 페이지에서** **동기화 상태가** 정상으로 표시 **되지** 않습니다. 


## <a name="add-database-to-availability-group"></a>가용성 그룹에 데이터베이스 추가

배포가 완료 된 후 가용성 그룹에 데이터베이스를 추가 합니다. 아래 단계에서는 SSMS (SQL Server Management Studio)를 사용 하지만 [transact-sql 또는 PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) 을 사용할 수도 있습니다. 

SQL Server Management Studio를 사용 하 여 가용성 그룹에 데이터베이스를 추가 하려면 다음 단계를 수행 합니다.

1. RDP (원격 데스크톱 연결)와 같은 선호 하는 방법을 사용 하 여 SQL Server Vm 중 하나에 연결 합니다. 
1. SSMS(SQL Server Management Studio)를 엽니다.
1. SQL Server 인스턴스에 연결 합니다. 
1. **개체 탐색기**에서 **Always On 고가용성** 을 확장 합니다.
1. **가용성 그룹**을 확장 하 고 가용성 그룹을 마우스 오른쪽 단추로 클릭 한 다음 **데이터베이스 추가**...를 선택 합니다.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="개체 탐색기에서 가용성 그룹을 마우스 오른쪽 단추로 클릭 하 고 데이터베이스 추가를 선택 합니다.":::

1. 표시 되는 메시지에 따라 가용성 그룹에 추가 하려는 데이터베이스를 선택 합니다. 
1. **확인** 을 선택 하 여 설정을 저장 하 고 데이터베이스를 가용성 그룹에 추가 합니다. 
1. 데이터베이스가 추가 된 후 **개체 탐색기** 를 새로 고쳐 데이터베이스 상태를으로 확인 `synchronized` 합니다. 

데이터베이스가 추가 된 후 Azure Portal에서 가용성 그룹의 상태를 확인할 수 있습니다. 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="데이터베이스가 동기화 된 후 Azure Portal의 고가용성 페이지에서 가용성 그룹의 상태를 확인 합니다.":::

## <a name="add-more-vms"></a>Vm 추가

클러스터에 SQL Server Vm을 더 추가 하려면 다음 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL 가상 컴퓨터](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스로 이동 합니다. 
1. **설정**아래에서 **고가용성** 을 선택 합니다. 
1. Windows **Server 장애 조치 (Failover) 클러스터 구성** 을 선택 하 여 **Windows Server 장애 조치 클러스터 구성** 페이지를 엽니다. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="클러스터에 Vm을 추가 하려면 Windows Server 장애 조치 (Failover) 클러스터 구성을 선택 합니다.":::

1. **Windows Server 장애 조치 (Failover) 클러스터 자격 증명** 을 확장 하 고 SQL Server 서비스, 클러스터 운영자 및 클러스터 부트스트랩 계정에 사용 되는 계정을 입력 합니다. 
1. 클러스터에 추가 하려는 SQL Server Vm을 선택 합니다. 
1. **적용**을 선택합니다. 

위쪽 탐색 모음의 종 모양 아이콘에서 액세스할 수 있는 **활동 로그** 에서 배포 상태를 확인할 수 있습니다. 


## <a name="modify-availability-group"></a>가용성 그룹 수정 


가용성 그룹 **에 복제본을 추가** 하거나, 수신기를 **구성**하거나, 가용성 그룹 옆에 있는 줄임표 (...)를 선택 하 여 Azure Portal의 **고가용성** 페이지에서 수신기를 **삭제할** 수 있습니다. 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="가용성 그룹 옆에 있는 줄임표를 선택한 다음 복제본 추가를 선택 하 여 가용성 그룹에 더 많은 복제본을 추가 합니다.":::

## <a name="remove-cluster"></a>클러스터 제거

클러스터에서 모든 SQL Server Vm을 제거 하 여 삭제 한 다음 SQL VM 리소스 공급자에서 클러스터 메타 데이터를 제거 합니다. 최신 버전의 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 PowerShell을 사용 하 여이 작업을 수행할 수 있습니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 클러스터에서 모든 SQL Server Vm을 제거 합니다. 이렇게 하면 클러스터에서 노드를 물리적으로 제거 하 고 클러스터를 제거 합니다.  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

클러스터의 유일한 Vm 인 경우 클러스터가 제거 됩니다. 클러스터의 다른 Vm이 제거 된 SQL Server Vm과 분리 되어 있으면 다른 Vm은 제거 되지 않고 클러스터가 제거 되지 않습니다. 

다음으로 SQL VM 리소스 공급자에서 클러스터 메타 데이터를 제거 합니다. 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 클러스터에서 모든 SQL Server Vm을 제거 합니다. 이렇게 하면 클러스터에서 노드를 물리적으로 제거 하 고 클러스터를 제거 합니다. 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

클러스터의 유일한 Vm 인 경우 클러스터가 제거 됩니다. 클러스터의 다른 Vm이 제거 된 SQL Server Vm과 분리 되어 있으면 다른 Vm은 제거 되지 않고 클러스터가 제거 되지 않습니다. 


다음으로 SQL VM 리소스 공급자에서 클러스터 메타 데이터를 제거 합니다. 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="troubleshooting"></a>문제 해결

문제가 발생 하는 경우 배포 기록을 확인 하 고 일반적인 오류 및 해결 방법을 검토할 수 있습니다. 

### <a name="check-deployment-history"></a>배포 기록 확인

포털을 통해 클러스터 및 가용성 그룹에 대 한 변경 내용은 배포를 통해 수행 됩니다. 클러스터를 만들거나 온 보 딩 하거나 가용성 그룹을 만드는 데 문제가 있는 경우 배포 기록은 더 자세한 정보를 제공할 수 있습니다.

배포에 대 한 로그를 확인 하 고 배포 기록을 확인 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 리소스 그룹으로 이동 합니다.
1. **설정** 아래에서 **배포**를 선택합니다.
1. 배포에 대 한 자세한 내용을 보려면 관심 있는 배포를 선택 합니다. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="에 대해 자세히 알아보려면 원하는 배포를 선택 합니다." :::

### <a name="common-errors"></a>일반 오류

다음과 같은 일반적인 오류 및 해결 방법을 검토 합니다. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>Sql 서비스를 시작 하는 데 사용 되는 계정이 도메인 계정이 아닙니다.

이는 리소스 공급자가 제공 된 자격 증명을 사용 하 여 SQL Server 서비스에 액세스할 수 없음을 나타냅니다. 몇 가지 일반적인 해결 방법은 다음과 같습니다.
- 도메인 컨트롤러가 실행 중인지 확인 합니다.
- 포털에 제공 된 자격 증명이 SQL Server 서비스의 자격 증명과 일치 하는지 확인 합니다. 


## <a name="next-steps"></a>다음 단계


가용성 그룹에 대 한 자세한 내용은 다음을 참조 하세요.

- [가용성 그룹 개요](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [가용성 그룹 관리](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [가용성 그룹 모니터링&#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Availability group Transact-sql 문 ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [가용성 그룹 PowerShell 명령](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

SQL Server Vm에 대 한 자세한 내용은 다음을 참조 하세요. 

* [SQL Server VM 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM 릴리스 정보](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM에 대한 FAQ](frequently-asked-questions-faq.md)
