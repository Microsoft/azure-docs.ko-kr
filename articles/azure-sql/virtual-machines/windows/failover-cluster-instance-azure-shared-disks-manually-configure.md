---
title: Azure 공유 디스크를 사용 하 여 FCI 만들기 (미리 보기)
description: Azure 공유 디스크를 사용 하 여 Azure Virtual Machines에서 SQL Server를 사용 하 여 FCI (장애 조치 (failover) 클러스터 인스턴스)를 만듭니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: ffb739affac68898f6ed5ff1d972d3fd4a70df2f
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055263"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Azure 공유 디스크를 사용 하 여 FCI 만들기 (Azure Vm에서 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server와 함께 Azure 공유 디스크를 사용 하 여 FCI (장애 조치 (failover) 클러스터 인스턴스)를 만드는 방법을 설명 합니다. 

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요. 


## <a name="prerequisites"></a>필수 구성 요소 

이 문서의 지침을 완료 하기 전에 다음이 이미 있어야 합니다.

- Azure 구독 [무료로](https://azure.microsoft.com/free/)시작 하세요. 
- 장애 도메인 및 업데이트 도메인을 **1**로 설정 하 여 만든 가용성 집합을 사용 하는 동일한 [가용성 집합](../../../virtual-machines/linux/tutorial-availability-sets.md) 및 [근접 배치 그룹](../../../virtual-machines/windows/co-location.md#proximity-placement-groups)에서 두 개 이상의 [미국 중부-준비 된 Windows Azure 가상 컴퓨터](failover-cluster-instance-prepare-vm.md) 
- Azure 가상 머신과 Active Directory 모두에서 개체를 만들 수 있는 권한이 있는 계정
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)입니다. 


## <a name="add-azure-shared-disk"></a>Azure 공유 디스크 추가
공유 디스크 기능이 사용 하도록 설정 된 관리 되는 프리미엄 SSD 디스크를 배포 합니다. `maxShares`두 FCI 노드 간에 디스크를 공유할 수 있도록 설정 하려면 **2** 로 설정 합니다. 

다음을 수행 하 여 Azure 공유 디스크를 추가 합니다. 


1. *SharedDiskConfig.js에*다음 스크립트를 저장 합니다. 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. PowerShell을 사용 하 여 *SharedDiskConfig.js를* 실행 합니다. 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. 각 VM에 대해 연결 된 공유 디스크를 GPT (GUID 파티션 테이블)로 초기화 하 고 다음 명령을 실행 하 여 NTFS (새 기술 파일 시스템)로 포맷 합니다. 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>장애 조치 (failover) 클러스터 만들기

장애 조치 클러스터를 만들려면 다음이 필요합니다.

- 클러스터 노드가 될 가상 머신의 이름
- 장애 조치 (failover) 클러스터의 이름입니다.
- 장애 조치 클러스터의 IP 주소 클러스터 노드와 동일한 Azure 가상 네트워크 및 서브넷에 사용되지 않는 IP 주소를 사용할 수 있습니다.


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

다음 PowerShell 스크립트는 장애 조치 (failover) 클러스터를 만듭니다. 스크립트를 노드의 이름(가상 머신 이름) 및 Azure 가상 네트워크에서 사용 가능한 IP 주소 이름으로 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

다음 PowerShell 스크립트는 장애 조치 (failover) 클러스터를 만듭니다. 스크립트를 노드의 이름(가상 머신 이름) 및 Azure 가상 네트워크에서 사용 가능한 IP 주소 이름으로 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

자세한 내용은 [장애 조치(failover) 클러스터: 클러스터 네트워크 개체](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)를 참조하세요.

---


## <a name="configure-quorum"></a>쿼럼 구성

비즈니스 요구에 가장 적합 한 쿼럼 솔루션을 구성 합니다. [디스크 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), [클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness)또는 [파일 공유 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)를 구성할 수 있습니다. 자세한 내용은 [SQL Server vm을 사용 하 여 쿼럼](hadr-cluster-best-practices.md#quorum)을 참조 하세요. 

## <a name="validate-cluster"></a>클러스터 유효성 검사
UI에서 또는 PowerShell을 사용하여 클러스터의 유효성을 검사합니다.

UI를 사용 하 여 클러스터의 유효성을 검사 하려면 가상 머신 중 하나에서 다음을 수행 합니다.

1. **서버 관리자**에서 **도구**를 선택한 다음 **장애 조치(failover) 클러스터 관리자**를 선택합니다.
1. **장애 조치(failover) 클러스터 관리자**에서 **작업**을 선택한 다음 **구성 유효성 검사**를 선택합니다.
1. **다음**을 선택합니다.
1. **서버 또는 클러스터 선택**에서 두 가상 머신의 이름을 입력합니다.
1. **테스트 옵션**에서 **선택한 테스트만 실행**을 선택합니다. 
1. **다음**을 선택합니다.
1. **테스트 선택**에서 **저장소** 를 *제외한* 모든 테스트를 선택 합니다.

## <a name="test-cluster-failover"></a>클러스터 장애 조치 테스트

클러스터의 장애 조치 (failover)를 테스트 합니다. **장애 조치(Failover) 클러스터 관리자**에서 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **추가 작업**  >  **코어 클러스터 리소스 이동**  >  **노드**를 선택한 후 클러스터의 다른 노드를 선택 합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 다시 기본 노드로 이동합니다. 클러스터를 각 노드로 성공적으로 이동할 수 있는 경우 SQL Server를 설치할 준비가 된 것입니다.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="코어 리소스를 다른 노드로 이동하여 클러스터 장애 조치(failover) 테스트":::

## <a name="create-sql-server-fci"></a>SQL Server FCI 만들기

장애 조치(failover) 클러스터 및 스토리지를 포함한 모든 클러스터 구성 요소를 구성한 후에는 SQL Server FCI를 만들 수 있습니다.

1. 원격 데스크톱 프로토콜 (RDP)를 사용 하 여 첫 번째 가상 컴퓨터에 연결 합니다.

1. **장애 조치(Failover) 클러스터 관리자**에서 모든 핵심 클러스터 리소스가 첫 번째 가상 컴퓨터에 있는지 확인 합니다. 필요한 경우 모든 리소스를 이 가상 머신으로 이동합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. 

1. **Setup**을 선택합니다.

1. **SQL Server 설치 센터**에서 **설치**를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터 새로 설치**를 선택합니다. 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

FCI 데이터 디렉터리는 Azure 공유 디스크에 있어야 합니다. 

1. 마법사의 지침을 완료하면 설치 프로그램이 첫 번째 노드에 SQL Server FCI를 설치합니다.

1. 설치 프로그램이 첫 번째 노드에 FCI를 설치하면 RDP를 사용하여 두 번째 노드에 연결합니다.

1. **SQL Server 설치 센터**를 연 다음 **설치**를 선택 합니다.

1. **SQL Server 장애 조치(failover) 클러스터에 노드 추가**를 선택합니다. 마법사의 지침에 따라 SQL Server를 설치하고 이 서버를 FCI에 추가합니다.

   >[!NOTE]
   >SQL Server가 포함된 Azure Marketplace 갤러리 이미지를 사용한 경우 SQL Server 도구는 이미지에 포함되었습니다. 이러한 이미지 중 하나를 사용하지 않은 경우 SQL Server 도구를 별도로 설치합니다. 자세한 내용은 [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.
   >

## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP에 등록

포털에서 SQL Server VM를 관리 하려면 SQL VM 리소스 공급자 (RP)를 [경량 관리 모드로](sql-vm-resource-provider-register.md#lightweight-management-mode)등록 합니다. 현재는 fci에서 지원 되는 유일한 모드 이며 Azure vm의 SQL Server입니다. 


PowerShell을 사용 하 여 SQL Server VM를 경량 모드로 등록 합니다.  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>연결 구성 

현재 주 노드로 트래픽을 적절 하 게 라우팅하려면 사용자 환경에 적합 한 연결 옵션을 구성 합니다. [Azure 부하 분산 장치](hadr-vnn-azure-load-balancer-configure.md) 를 만들 수도 있고, SQL Server 2019 및 Windows Server 2016를 사용 하는 경우에는 [분산 네트워크 이름](hadr-distributed-network-name-dnn-configure.md) 기능을 미리 볼 수 있습니다. 

## <a name="limitations"></a>제한 사항

- Windows Server 2019에서는 SQL Server 2019만 지원 됩니다. 
- [경량 관리 모드](sql-vm-resource-provider-register.md#management-modes) 에서는 SQL VM 리소스 공급자에 등록만 지원 됩니다.

## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 [가상 네트워크 이름 및 Azure 부하 분산 장치](hadr-vnn-azure-load-balancer-configure.md) 또는 [DNN (분산 네트워크 이름)](hadr-distributed-network-name-dnn-configure.md)을 사용 하 여 fci에 대 한 연결을 구성 합니다. 

Azure 공유 디스크가 적합 한 FCI 저장소 솔루션이 아니면 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md) 를 사용 하 여 fci를 만들거나 대신 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 하는 것이 좋습니다. 

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 구성 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요.

자세한 내용은 다음을 참조하세요. 
- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 장애 조치 (failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
