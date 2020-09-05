---
title: 프리미엄 파일 공유를 사용 하 여 FCI 만들기
description: PFS (프리미엄 파일 공유)를 사용 하 여 Azure 가상 컴퓨터에서 SQL Server를 사용 하 여 FCI (장애 조치 (failover) 클러스터 인스턴스)를 만듭니다.
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
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: cbc6b2af98905a09324a58c92cafca0075d8a01d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055144"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>프리미엄 파일 공유를 사용 하 여 FCI 만들기 (Azure Vm에서 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 [프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md)를 사용 하 여 Azure Virtual Machines (vm)에서 SQL Server를 사용 하 여 fci (장애 조치 (failover) 클러스터 인스턴스)를 만드는 방법을 설명 합니다.

프리미엄 파일 공유는 Windows Server 2012 이상에서 2012 이상 장애 조치 (failover) 클러스터 SQL Server 인스턴스와 함께 사용할 수 있도록 완전히 지원 되는 스토리지 공간 다이렉트 (SSD) 지원 되 고 대기 시간이 짧은 파일 공유입니다. 프리미엄 파일 공유는 뛰어난 유연성을 제공하여 가동 중지 시간 없이 파일 공유 크기를 조정하고 크기를 조정할 수 있습니다.

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 지침을 완료 하기 전에 다음이 이미 있어야 합니다.

- Azure 구독
- Azure 가상 머신과 Active Directory 모두에서 개체를 만들 수 있는 권한이 있는 계정
- [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 또는 다른 [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)에 [준비 된 두 개 이상의 Windows Azure 가상 머신](failover-cluster-instance-prepare-vm.md)
- 데이터 파일용 데이터베이스의 스토리지 할당량을 기준으로 클러스터형 드라이브로 [프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md)를 사용합니다.
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)입니다. 

## <a name="mount-premium-file-share"></a>프리미엄 파일 공유 탑재

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 저장소 계정으로 이동 합니다.
1. **파일 서비스**에서 **파일 공유** 로 이동한 다음 SQL 저장소에 사용할 프리미엄 파일 공유를 선택 합니다.
1. **연결**을 선택하여 파일 공유에 대한 연결 문자열을 가져옵니다.
1. 드롭다운 목록에서 사용 하려는 드라이브 문자를 선택 하 고 두 코드 블록을 메모장에 복사 합니다.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="파일 공유 연결 포털에서 두 PowerShell 명령 복사":::

1. 원격 데스크톱 프로토콜 (RDP)를 사용 하 여 SQL Server FCI가 서비스 계정에 사용할 계정을 사용 하 여 SQL Server VM에 연결 합니다.
1. 관리 PowerShell 명령 콘솔을 엽니다.
1. 앞서 포털에서 작업하면서 저장한 명령을 실행합니다.
1. 파일 탐색기 또는 **실행** 대화 상자를 사용 하 여 공유로 이동 합니다 (Windows + R 선택). 네트워크 경로 `\\storageaccountname.file.core.windows.net\filesharename`을 사용합니다. 예를 들어 `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 새로 연결 된 파일 공유에 SQL 데이터 파일을 저장할 폴더를 하나 이상 만듭니다.
1. 클러스터에 참여하는 각 SQL Server VM에 대해 이러한 단계를 반복합니다.

  > [!IMPORTANT]
  > - 백업 파일에 대 한 별도의 파일 공유를 사용 하 여 데이터 및 로그 파일에 대 한이 공유의 IOPS (초당 입력/출력 작업 수) 및 공간 용량을 저장 하는 것이 좋습니다. 백업 파일에 프리미엄 또는 표준 파일 공유 중 하나를 사용할 수 있습니다.
  > - Windows 2012 R2이 하를 사용 하는 경우 다음 단계를 수행 하 여 파일 공유 감시로 사용할 파일 공유를 탑재 합니다. 
  > 


## <a name="add-windows-cluster-feature"></a>Windows 클러스터 기능 추가

1. 로컬 관리자의 구성원이며 Active Directory에 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용하여 RDP로 첫 번째 가상 머신에 연결합니다. 구성의 나머지 부분에서는 이 계정을 사용합니다.

1. [장애 조치 (failover) 클러스터링을 각 가상 컴퓨터에 추가](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)합니다.

   UI에서 장애 조치 (failover) 클러스터링을 설치 하려면 두 가상 컴퓨터에서 다음을 수행 합니다.
   1. **서버 관리자**에서 **관리**를 선택한 다음, **역할 및 기능 추가**를 선택합니다.
   1. **역할 및 기능 추가** 마법사에서 **다음** 을 선택 하 여 **기능을 선택**합니다.
   1. **기능 선택**에서 **장애 조치(failover) 클러스터링**을 선택합니다. 필요한 모든 기능 및 관리 도구를 포함합니다. 
   1. **기능 추가**를 선택합니다.
   1. **다음**을 선택하고 **마침**을 선택하여 기능을 설치합니다.

   PowerShell을 사용 하 여 장애 조치 (failover) 클러스터링을 설치 하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행 합니다.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>클러스터 유효성 검사

UI에서 또는 PowerShell을 사용하여 클러스터의 유효성을 검사합니다.

UI를 사용 하 여 클러스터의 유효성을 검사 하려면 가상 머신 중 하나에서 다음을 수행 합니다.

1. **서버 관리자**에서 **도구**를 선택한 다음 **장애 조치(failover) 클러스터 관리자**를 선택합니다.
1. **장애 조치(failover) 클러스터 관리자**에서 **작업**을 선택한 다음 **구성 유효성 검사**를 선택합니다.
1. **다음**을 선택합니다.
1. **서버 또는 클러스터 선택**에서 두 가상 머신의 이름을 입력합니다.
1. **테스트 옵션**에서 **선택한 테스트만 실행**을 선택합니다. 
1. **다음**을 선택합니다.
1. **테스트 선택**에서 다음과 같이 **스토리지** 및 **스토리지 공간 다이렉트**를 선택합니다.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="클러스터 유효성 검사 테스트 선택":::

1. **다음**을 선택합니다.
1. **확인**에서 **다음**을 선택합니다.

**구성 유효성 검사** 마법사는 유효성 검사 테스트를 실행 합니다.

PowerShell을 사용하여 클러스터의 유효성을 검사하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

클러스터의 유효성을 검사한 후에 장애 조치 클러스터를 만듭니다.


## <a name="create-failover-cluster"></a>장애 조치 (failover) 클러스터 만들기

장애 조치 클러스터를 만들려면 다음이 필요합니다.

- 클러스터 노드가 될 가상 머신의 이름
- 장애 조치 (failover) 클러스터의 이름입니다.
- 장애 조치 클러스터의 IP 주소 클러스터 노드와 동일한 Azure 가상 네트워크 및 서브넷에 사용되지 않는 IP 주소를 사용할 수 있습니다.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

다음 PowerShell 스크립트는 Windows Server 2012~Windows Server 2016에 대한 장애 조치(failover) 클러스터를 만듭니다. 스크립트를 노드의 이름(가상 머신 이름) 및 Azure 가상 네트워크에서 사용 가능한 IP 주소 이름으로 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

다음 PowerShell 스크립트는 Windows Server 2019에 대한 장애 조치(failover) 클러스터를 만듭니다.  스크립트를 노드의 이름(가상 머신 이름) 및 Azure 가상 네트워크에서 사용 가능한 IP 주소 이름으로 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

자세한 내용은 [장애 조치(failover) 클러스터: 클러스터 네트워크 개체](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)를 참조하세요.

---


## <a name="configure-quorum"></a>쿼럼 구성

비즈니스 요구에 가장 적합 한 쿼럼 솔루션을 구성 합니다. [디스크 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), [클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness)또는 [파일 공유 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)를 구성할 수 있습니다. 자세한 내용은 [SQL Server vm을 사용 하 여 쿼럼](hadr-cluster-best-practices.md#quorum)을 참조 하세요. 


## <a name="test-cluster-failover"></a>클러스터 장애 조치 테스트

클러스터의 장애 조치 (failover)를 테스트 합니다. **장애 조치(Failover) 클러스터 관리자**에서 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **추가 작업**  >  **코어 클러스터 리소스 이동**  >  **노드**를 선택한 후 클러스터의 다른 노드를 선택 합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 다시 기본 노드로 이동합니다. 클러스터를 각 노드로 성공적으로 이동할 수 있는 경우 SQL Server를 설치할 준비가 된 것입니다.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="코어 리소스를 다른 노드로 이동하여 클러스터 장애 조치(failover) 테스트":::


## <a name="create-sql-server-fci"></a>SQL Server FCI 만들기

장애 조치(failover) 클러스터를 구성한 후에는 SQL Server FCI를 만들 수 있습니다.

1. RDP를 사용하여 첫 번째 가상 머신에 연결합니다.

1. **장애 조치(Failover) 클러스터 관리자**에서 모든 코어 클러스터 리소스가 첫 번째 가상 컴퓨터에 있는지 확인 합니다. 필요한 경우 모든 리소스를 이 가상 컴퓨터로 이동합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. 

1. **Setup**을 선택합니다.

1. **SQL Server 설치 센터**에서 **설치**를 선택합니다.

1. **새로 만들기 SQL Server 장애 조치 (failover) 클러스터 설치**를 선택한 다음 마법사의 지침에 따라 SQL Server fci를 설치 합니다.

   FCI 데이터 디렉터리는 프리미엄 파일 공유에 있어야 합니다. 공유의 전체 경로를 형식으로 입력 `\\storageaccountname.file.core.windows.net\filesharename\foldername` 합니다. 파일 서버를 데이터 디렉터리로 지정했음을 알려주는 경고가 표시됩니다. 이 경고는 예상된 것입니다. 파일 공유를 보관할 때 RDP를 통해 VM에 액세스 하는 데 사용한 사용자 계정이 SQL Server 서비스에서 가능한 오류를 방지 하는 데 사용 하는 것과 동일한 계정 인지 확인 합니다.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="파일 공유를 SQL 데이터 디렉터리로 사용":::

1. 마법사의 단계를 완료하면 설치 프로그램이 첫 번째 노드에 SQL Server FCI를 설치합니다.

1. 설치 프로그램이 첫 번째 노드에 FCI를 설치하면 RDP를 사용하여 두 번째 노드에 연결합니다.

1. **SQL Server 설치 센터**를 연 다음 **설치**를 선택 합니다.

1. **SQL Server 장애 조치(failover) 클러스터에 노드 추가**를 선택합니다. 마법사의 지침에 따라 SQL Server를 설치하고 이 서버를 FCI에 추가합니다.

   >[!NOTE]
   >SQL Server와 함께 Azure Marketplace 갤러리 이미지를 사용한 경우 SQL Server 도구는 이미지에 포함되었습니다. 이러한 이미지 중 하나를 사용하지 않은 경우 SQL Server 도구를 별도로 설치합니다. 자세한 내용은 [SSMS(SQL Server Management Studio) 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)를 참조하세요.

1. SQL Server 장애 조치 (failover) 클러스터 인스턴스에 추가 하려는 다른 노드에서 이러한 단계를 반복 합니다. 

## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP에 등록

포털에서 SQL Server VM를 관리 하려면 SQL VM 리소스 공급자 (RP)를 [경량 관리 모드로](sql-vm-resource-provider-register.md#lightweight-management-mode)등록 하 고, 현재는 fci에서 지원 되는 유일한 모드 이며, Azure vm에서 SQL Server 합니다. 

PowerShell을 사용 하 여 경량 모드의 SQL Server VM 등록 합니다 (-LicenseType은 또는 일 수 있음 `PAYG` `AHUB` ).

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>연결 구성 

현재 주 노드로 트래픽을 적절 하 게 라우팅하려면 사용자 환경에 적합 한 연결 옵션을 구성 합니다. [Azure 부하 분산 장치](hadr-vnn-azure-load-balancer-configure.md) 를 만들 수도 있고, SQL Server 2019 및 Windows Server 2016를 사용 하는 경우에는 [분산 네트워크 이름](hadr-distributed-network-name-dnn-configure.md) 기능을 미리 볼 수 있습니다. 

## <a name="limitations"></a>제한 사항

- MSDTC (Microsoft DTC(Distributed Transaction Coordinator))는 Windows Server 2016 이전 버전에서는 지원 되지 않습니다. 
- Filestream은 프리미엄 파일 공유를 사용하는 장애 조치(failover) 클러스터에 대해 지원되지 않습니다. Filestream을 사용 하려면 대신 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 또는 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 를 사용 하 여 클러스터를 배포 하세요.
- [경량 관리 모드](sql-vm-resource-provider-register.md#management-modes) 에서는 SQL VM 리소스 공급자에 등록만 지원 됩니다. 

## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 [가상 네트워크 이름 및 Azure 부하 분산 장치](hadr-vnn-azure-load-balancer-configure.md) 또는 [DNN (분산 네트워크 이름)](hadr-distributed-network-name-dnn-configure.md)을 사용 하 여 fci에 대 한 연결을 구성 합니다. 

프리미엄 파일 공유가 적합 한 FCI 저장소 솔루션이 아닌 경우 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 를 사용 하 여 fci를 만들거나 대신 [스토리지 공간 다이렉트](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 합니다. 

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 구성 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요. 

자세한 내용은 다음을 참조하세요. 
- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 장애 조치 (failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
