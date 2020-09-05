---
title: 스토리지 공간 다이렉트를 사용 하 여 FCI 만들기
description: 스토리지 공간 다이렉트를 사용 하 여 Azure virtual machines에서 SQL Server를 사용 하 여 FCI (장애 조치 (failover) 클러스터 인스턴스)를 만듭니다.
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
ms.openlocfilehash: 5f2fa1f33fe57b0d96ad3d6faa334b98d21c5f4a
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055178"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>스토리지 공간 다이렉트를 사용 하 여 FCI 만들기 (Azure Vm에서 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server와 함께 [스토리지 공간 다이렉트](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 를 사용 하 여 fci (장애 조치 (failover) 클러스터 인스턴스)를 만드는 방법을 설명 합니다. 스토리지 공간 다이렉트는 Windows 클러스터의 노드 (Azure Vm) 간에 저장소 (데이터 디스크)를 동기화 하는 소프트웨어 기반 VSAN (가상 저장 영역 네트워크) 역할을 합니다. 

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요. 


## <a name="overview"></a>개요 

[S2D (스토리지 공간 다이렉트)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 는 수렴 형 및 하이퍼 수렴 형의 두 가지 아키텍처 유형을 지원 합니다. 하이퍼 수렴 형 인프라는 저장소를 클러스터 된 응용 프로그램을 호스트 하는 동일한 서버에 배치 하므로 저장소는 각 SQL Server FCI 노드에 있습니다. 

다음 다이어그램에서는 Azure Vm에서 SQL Server와 함께 하이퍼 수렴 형 스토리지 공간 다이렉트를 사용 하는 전체 솔루션을 보여 줍니다. 

![하이퍼 수렴 형 스토리지 공간 다이렉트를 사용 하는 전체 솔루션의 다이어그램](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

위의 다이어그램은 동일한 리소스 그룹에서 다음 리소스를 보여 줍니다.

- Windows Server 장애 조치 (failover) 클러스터의 두 가상 머신 가상 머신이 장애 조치(failover) 클러스터에 있는 경우 클러스터 노드 또는 노드라고도 합니다.
- 각 가상 머신에는 두 개 이상의 데이터 디스크가 있습니다.
- 스토리지 공간 다이렉트는 데이터 디스크의 데이터를 동기화하고 스토리지 풀로 동기화된 스토리지를 제공합니다.
- 스토리지 풀은 장애 조치(failover) 클러스터에 CSV(클러스터 공유 볼륨)를 제공합니다.
- SQL Server FCI 클러스터 역할은 데이터 드라이브에 CSV를 사용합니다.
- SQL Server FCI에 대한 IP 주소를 저장하는 Azure 부하 분산 장치.
- Azure 가용성 집합은 모든 리소스를 보유합니다.

   > [!NOTE]
   > 템플릿을 사용하여 Azure에 전체 솔루션을 만들 수 있습니다. 템플릿의 예는 GitHub [Azure 빠른 시작 템플릿](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) 페이지에서 사용할 수 있습니다. 이 예제는 특정 워크로드에 대해 설계되거나 테스트되지 않았습니다. 템플릿을 실행하여 도메인에 연결된 스토리지 공간 다이렉트 스토리지를 사용하여 SQL Server FCI를 만들 수 있습니다. 템플릿을 평가하고 용도에 맞게 수정할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

이 문서의 지침을 완료 하기 전에 다음이 이미 있어야 합니다.

- Azure 구독 [무료로](https://azure.microsoft.com/free/)시작 하세요. 
- [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)에서 두 개 이상의 [준비 된 Windows Azure 가상 컴퓨터](failover-cluster-instance-prepare-vm.md) .
- Azure 가상 머신과 Active Directory 모두에서 개체를 만들 수 있는 권한이 있는 계정
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0)입니다. 


## <a name="add-the-windows-cluster-feature"></a>Windows 클러스터 기능 추가

1. 로컬 관리자의 구성원이 고 Active Directory에서 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용 하 여 원격 데스크톱 프로토콜 (RDP)를 사용 하 여 첫 번째 가상 컴퓨터에 연결 합니다. 구성의 나머지 부분에서는 이 계정을 사용합니다.

1. 장애 조치 (failover) 클러스터링을 각 가상 컴퓨터에 추가 합니다.

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

다음 단계에 대 한 자세한 내용은 [Windows Server 2016의 스토리지 공간 다이렉트를 사용 하 여 하이퍼 수렴 형 솔루션](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)의 "3 단계: 스토리지 공간 다이렉트 구성" 섹션의 지침을 참조 하세요.


## <a name="validate-the-cluster"></a>클러스터 유효성 검사

UI에서 또는 PowerShell을 사용하여 클러스터의 유효성을 검사합니다.

UI를 사용 하 여 클러스터의 유효성을 검사 하려면 가상 머신 중 하나에서 다음을 수행 합니다.

1. **서버 관리자**에서 **도구**를 선택한 다음 **장애 조치(failover) 클러스터 관리자**를 선택합니다.
1. **장애 조치(failover) 클러스터 관리자**에서 **작업**을 선택한 다음 **구성 유효성 검사**를 선택합니다.
1. **다음**을 선택합니다.
1. **서버 또는 클러스터 선택**에서 두 가상 머신의 이름을 입력합니다.
1. **테스트 옵션**에서 **선택한 테스트만 실행**을 선택합니다. 
1. **다음**을 선택합니다.
1. **테스트 선택**에서 다음과 같이 **스토리지**를 제외한 모든 테스트를 선택합니다.

   ![클러스터 유효성 검사 테스트 선택](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. **다음**을 선택합니다.
1. **확인**에서 **다음**을 선택합니다.

    **구성 유효성 검사** 마법사는 유효성 검사 테스트를 실행 합니다.

PowerShell을 사용하여 클러스터의 유효성을 검사하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
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

## <a name="add-storage"></a>스토리지 추가

스토리지 공간 다이렉트용 디스크는 비어 있어야 하며 파티션 또는 다른 데이터를 포함할 수 없습니다. 디스크를 정리 하려면 [스토리지 공간 다이렉트 배포](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)의 지침을 따르세요.

1. [스토리지 공간 다이렉트를 사용 하도록 설정](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)합니다.

   다음 PowerShell 스크립트는 스토리지 공간 다이렉트를 활성화합니다.  

   ```powershell
   Enable-ClusterS2D
   ```

   **장애 조치(Failover) 클러스터 관리자**에서 이제 스토리지 풀을 볼 수 있습니다.

1. [볼륨을 만듭니다](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   스토리지 공간 다이렉트를 활성화하면 스토리지 풀이 자동으로 만들어집니다. 이제 볼륨을 만들 준비가 되었습니다. PowerShell cmdlet `New-Volume`은 볼륨 만들기 프로세스를 자동화합니다. 이 프로세스에는 형식을 포함 하며 클러스터에 볼륨을 추가 하 고 CSV를 만듭니다. 이 예제에서는 800GB CSV를 만듭니다.

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   이전 명령을 실행 한 후 800 GB 볼륨이 클러스터 리소스로 탑재 됩니다. 볼륨은 `C:\ClusterStorage\Volume1\`에 있습니다.

   이 스크린샷에서는 스토리지 공간 다이렉트 포함 된 CSV를 보여줍니다.

   ![스토리지 공간 다이렉트 포함 된 클러스터 공유 볼륨의 스크린샷](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>클러스터 장애 조치 테스트

클러스터의 장애 조치 (failover)를 테스트 합니다. **장애 조치(Failover) 클러스터 관리자**에서 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **추가 작업**  >  **코어 클러스터 리소스 이동**  >  **노드**를 선택한 후 클러스터의 다른 노드를 선택 합니다. 코어 클러스터 리소스를 클러스터의 모든 노드로 이동한 다음 다시 기본 노드로 이동합니다. 클러스터를 각 노드로 성공적으로 이동할 수 있는 경우 SQL Server를 설치할 준비가 된 것입니다.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="코어 리소스를 다른 노드로 이동하여 클러스터 장애 조치(failover) 테스트":::

## <a name="create-sql-server-fci"></a>SQL Server FCI 만들기

장애 조치(failover) 클러스터 및 스토리지를 포함한 모든 클러스터 구성 요소를 구성한 후에는 SQL Server FCI를 만들 수 있습니다.

1. RDP를 사용하여 첫 번째 가상 머신에 연결합니다.

1. **장애 조치(Failover) 클러스터 관리자**에서 모든 핵심 클러스터 리소스가 첫 번째 가상 컴퓨터에 있는지 확인 합니다. 필요한 경우 모든 리소스를 이 가상 머신으로 이동합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. **Setup**을 선택합니다.

1. **SQL Server 설치 센터**에서 **설치**를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터 새로 설치**를 선택합니다. 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

   FCI 데이터 디렉터리는 클러스터형 스토리지에 있어야 합니다. 스토리지 공간 다이렉트를 사용 하는 경우 공유 디스크가 아니라 각 서버의 볼륨에 대 한 탑재 지점입니다. 스토리지 공간 다이렉트는 두 노드 간에 볼륨을 동기화합니다. 볼륨은 CSV로 클러스터에 표시 됩니다. 데이터 디렉터리에 CSV 탑재 지점을 사용합니다.

   ![데이터 디렉터리](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. 마법사의 지침을 완료 한 후 설치 프로그램은 첫 번째 노드에 SQL Server FCI를 설치 합니다.

1. 설치 프로그램이 첫 번째 노드에 FCI를 설치하면 RDP를 사용하여 두 번째 노드에 연결합니다.

1. **SQL Server 설치 센터**를 엽니다. **설치**를 선택합니다.

1. **SQL Server 장애 조치(failover) 클러스터에 노드 추가**를 선택합니다. 마법사의 지침에 따라 SQL Server를 설치하고 이 서버를 FCI에 추가합니다.

   >[!NOTE]
   >SQL Server가 포함된 Azure Marketplace 갤러리 이미지를 사용한 경우 SQL Server 도구는 이미지에 포함되었습니다. 이러한 이미지 중 하나를 사용하지 않은 경우 SQL Server 도구를 별도로 설치합니다. 자세한 내용은 [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.
   >


## <a name="register-with-the-sql-vm-rp"></a>SQL VM RP에 등록

포털에서 SQL Server VM를 관리 하려면 SQL VM 리소스 공급자 (RP)를 [경량 관리 모드로](sql-vm-resource-provider-register.md#lightweight-management-mode)등록 하 고, 현재는 fci에서 지원 되는 유일한 모드 이며, Azure vm에서 SQL Server 합니다. 


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

- Azure virtual machines는 Csv의 저장소와 [표준 부하 분산 장치](../../../load-balancer/load-balancer-standard-overview.md)를 사용 하는 Windows Server 2019에서 MSDTC (Microsoft DTC(Distributed Transaction Coordinator))를 지원 합니다.
- NTFS 형식 디스크로 연결 된 디스크는 클러스터에 저장소를 추가 하는 경우 디스크 자격 옵션을 선택 취소 하거나 선택 취소 하는 경우에만 스토리지 공간 다이렉트와 함께 사용할 수 있습니다. 
- [경량 관리 모드](sql-vm-resource-provider-register.md#management-modes) 에서는 SQL VM 리소스 공급자에 등록만 지원 됩니다.

## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 [가상 네트워크 이름 및 Azure 부하 분산 장치](hadr-vnn-azure-load-balancer-configure.md) 또는 [DNN (분산 네트워크 이름)](hadr-distributed-network-name-dnn-configure.md)을 사용 하 여 fci에 대 한 연결을 구성 합니다. 

스토리지 공간 다이렉트 적절 한 FCI 저장소 솔루션이 아니면 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 또는 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md) 를 대신 사용 하 여 fci를 만드는 것이 좋습니다. 

자세한 내용은 [Azure vm의 SQL Server를 사용 하는 Fci](failover-cluster-instance-overview.md) 개요 및 [클러스터 구성 모범 사례](hadr-cluster-best-practices.md)를 참조 하세요. 

자세한 내용은 다음을 참조하세요. 
- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 장애 조치 (failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
