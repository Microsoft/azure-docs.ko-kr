---
title: SQL Server FCI - Azure Virtual Machines | Microsoft Docs
description: 이 문서에서는 Azure Virtual Machines에 SQL Server 장애 조치(Failover) 클러스터 인스턴스를 만드는 방법을 설명합니다.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: a758cce85645e72bfd9434a69393133d3da6b57d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591560"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Azure Virtual Machines에 SQL Server 장애 조치(Failover) 클러스터 인스턴스 구성

이 문서에서는 리소스 관리자 모델에서 Azure 가상 머신에 SQL Server FCI(장애 조치(Failover) 클러스터 인스턴스)를 만드는 방법을 설명합니다. 이 솔루션에서는 Windows 클러스터에서 노드(Azure VM) 간 저장소(데이터 디스크)를 동기화하는 소프트웨어 기반 가상 SAN으로 [Windows Server 2016 Datacenter 버전 저장소 공간 다이렉트 \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)를 사용합니다. S2D는 Windows Server 2016의 새로운 기능입니다.

다음 다이어그램에서는 Azure 가상 머신에 완전한 솔루션을 보여 줍니다.

![가용성 그룹](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

위의 다이어그램은 다음을 보여 줍니다.

- Windows 장애 조치(Failover) 클러스터에 있는 두 개의 Azure Virtual Machines 가상 머신이 장애 조치 클러스터에 있는 경우 *클러스터 노드* 또는 *노드*라고도 합니다.
- 각 가상 머신에는 두 개 이상의 데이터 디스크가 있습니다.
- S2D는 데이터 디스크의 데이터를 동기화하고 저장소 풀로 동기화된 저장소를 제공합니다.
- 저장소 풀은 장애 조치 클러스터에 CSV(클러스터 공유 볼륨)를 제공합니다.
- SQL Server FCI 클러스터 역할은 데이터 드라이브에 CSV를 사용합니다.
- SQL Server FCI에 대한 IP 주소를 저장하는 Azure 부하 분산 장치.
- Azure 가용성 집합은 모든 리소스를 보유합니다.

   >[!NOTE]
   >다이어그램의 모든 Azure 리소스는 동일한 리소스 그룹에 있습니다.

S2D에 대한 자세한 내용은 [Windows Server 2016 Datacenter 버전 저장소 공간 다이렉트 \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)를 참조하세요.

S2D는 두 가지 유형의 아키텍처 수렴형 및 하이퍼 수렴형을 지원합니다. 이 문서의 아키텍처는 하이퍼 수렴형입니다. 하이퍼 수렴형 인프라는 클러스터형 애플리케이션을 호스트하는 동일한 서버에 저장소를 배치합니다. 이 아키텍처에서 저장소는 각 SQL Server FCI 노드에 있습니다.

## <a name="licensing-and-pricing"></a>라이선싱 및 가격 책정

Azure Virtual Machines에서 PAYG(용량제 통화 요금) 또는 BYOL(사용자 라이선스 필요) VM 이미지를 사용하여 SQL Server를 라이선싱할 수 있습니다. 선택하는 이미지의 유형은 청구 방식에 영향을 줍니다.

PAYG 라이선싱을 사용하면 Azure Virtual Machines에서 SQL Server의 FCI(장애 조치 클러스터 인스턴스)는 수동 노드를 포함한 모든 FCI의 노드에 대한 요금을 부과합니다. 자세한 내용은 [SQL Server Enterprise Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)을 참조하세요. 

Software Assurance로 기업계약을 한 고객은 각 활성 노드에 대해 하나의 무료 수동 FCI 노드를 사용할 권리가 있습니다. Azure에서 이 이점을 활용하려면 BYOL VM 이미지를 사용한 다음, FCI의 활성 노드와 수동 노드 둘 다에 동일한 라이선스를 사용합니다. 자세한 내용은 [기업계약](https://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx)을 참조하세요.

Azure Virtual Machines의 SQL Server에 대한 PAYG와 BYOL 라이선싱을 비교하려면 [SQL VM 시작](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)을 참조하세요.

SQL Server 라이선싱에 대한 자세한 내용은 [가격 책정](https://www.microsoft.com/sql-server/sql-server-2017-pricing)을 참조하세요.

### <a name="example-azure-template"></a>예제 Azure 템플릿

템플릿에서 Azure에 전체 솔루션을 만들 수 있습니다. 템플릿의 예제는 GitHub [Azure 빠른 시작 템플릿](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)에서 사용 가능합니다. 이 예제는 특정 작업에 대해 설계되거나 테스트되지 않았습니다. 템플릿을 실행하여 도메인에 연결된 S2D 저장소를 사용하여 SQL Server FCI를 만들 수 있습니다. 템플릿을 평가하고 용도에 맞게 수정할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

진행하기 전에 알아야 할 몇 가지 사항이 있으며 준비해야 할 몇 가지 사항이 있습니다.

### <a name="what-to-know"></a>알아야 할 사항
다음 기술의 작동을 이해해야 합니다.

- [Windows 클러스터 기술](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 장애 조치(Failover) 클러스터 인스턴스](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

하나의 중요 한 차이점은 권장 되는 Azure IaaS VM 게스트 장애 조치 클러스터에서 단일 NIC 서버 (클러스터 노드) 및 단일 서브넷입니다. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다. 

또한 다음 기술에 대한 기본적인 지식이 있어야 합니다.

- [Windows Server 2016의 저장소 공간 다이렉트를 사용하는 하이퍼 수렴형 솔루션](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure 리소스 그룹](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 이번에 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)은 Azure에서 SQL Server FCI에 대해 지원되지 않습니다. FCI에 참여하는 VM에서 확장을 제거하는 것이 좋습니다. 이 확장은 자동화된 Backup 및 패칭 같은 기능 및 SQL용 일부 포털 기능을 지원합니다. 이 기능은 에이전트를 제거한 후 SQL VM에 대해 작동하지 않습니다.

### <a name="what-to-have"></a>가지고 있어야 할 사항

이 문서의 지침을 수행하기 전에 다음이 있어야 합니다.

- Microsoft Azure 구독
- Azure 가상 머신의 Windows 도메인
- Azure 가상 컴퓨터에 개체를 만들 수 있는 권한이 있는 계정
- 다음 구성 요소에 대한 충분한 IP 주소 공간이 있는 Azure 가상 네트워크 및 서브넷
   - 두 가상 머신
   - 장애 조치 클러스터 IP 주소
   - 각 FCI에 대한 IP 주소
- 도메인 컨트롤러를 가리키는 Azure 네트워크에 구성된 DNS

이러한 필수 구성 요소가 준비되면 장애 조치 클러스터 빌드를 진행할 수 있습니다. 첫 번째 단계는 가상 머신을 만드는 것입니다.

## <a name="step-1-create-virtual-machines"></a>1단계: 가상 머신 만들기

1. 사용자의 구독으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [Azure 가용성 집합을 만듭니다](../tutorial-availability-sets.md).

   가용성 집합은 장애 도메인 및 업데이트 도메인에 대해 가상 머신을 그룹화합니다. 가용성 집합을 사용하면 애플리케이션은 네트워크 스위치 또는 서버 랙의 전원 장치와 같은 단일 지점의 오류에 영향을 받지 않습니다.

   가상 머신에 대한 리소스 그룹을 만들지 않은 경우 Azure 가용성 집합을 만들 때 만듭니다. 가용성 집합을 만드는 데 Azure Portal을 사용하는 경우 다음 단계를 수행합니다.

   - Azure Portal에서 **+** 를 클릭하여 Azure Marketplace를 엽니다. **가용성 집합**을 검색합니다.
   - **가용성 집합**을 클릭합니다.
   - **만들기**를 클릭합니다.
   - **가용성 집합 만들기** 블레이드에서 다음 값을 설정합니다.
      - **이름**: 가용성 집합의 이름입니다.
      - **구독**: Azure 구독.
      - **리소스 그룹**: 기존 그룹을 사용하려는 경우 **기존 항목 사용**을 클릭하고 드롭다운 목록에서 그룹을 선택합니다. 그렇지 않으면 **새로 만들기**를 선택하고 그룹에 대한 이름을 입력합니다.
      - **Location**: 가상 머신을 만들 위치를 설정합니다.
      - **장애 도메인**: 기본값(3)을 사용하세요.
      - **업데이트 도메인**: 기본값(5)을 사용하세요.
   - **만들기**를 클릭하여 가용성 집합을 만듭니다.

1. 가용성 집합에 가상 머신을 만듭니다.

   Azure 가용성 집합에서 두 개의 SQL Server 가상 머신을 프로비전합니다. 지침은 [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

   두 가상 머신을 다음에 배치합니다.

   - 가용성 집합이 있는 동일한 Azure 리소스 그룹에.
   - 도메인 컨트롤러와 동일한 네트워크에.
   - 두 가상 머신에 대한 충분한 IP 주소 공간이 있는 서브넷 및 이 클러스터에서 사용할 수 있는 모든 FCI에.
   - Azure 가용성 집합에.   

      >[!IMPORTANT]
      >가상 머신을 만든 후에 가용성 집합을 설정 또는 변경할 수 없습니다.

   Azure Marketplace에서 이미지를 선택합니다. Windows Server 및 SQL Server 또는 Windows Server만 포함하는 Marketplace 이미지를 사용할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

   Azure Gallery의 공식 SQL Server 이미지에는 설치된 SQL Server 인스턴스와 SQL Server 설치 소프트웨어 및 필요한 키가 포함됩니다.

   SQL Server 라이선스에 대한 요금을 지불하려는 방법에 따라 올바른 이미지를 선택합니다.

   - **사용 라이선스당 지불**: 이러한 이미지의 초당 비용은 SQL Server 라이선스를 포함합니다.
      - **Windows Server Datacenter 2016의 SQL Server 2016 Enterprise**
      - **Windows Server Datacenter 2016의 SQL Server 2016 Standard**
      - **Windows Server Datacenter 2016의 SQL Server 2016 Developer**

   - **BYOL(사용자 라이선스 필요)**

      - **{BYOL} Windows Server Datacenter 2016의 SQL Server 2016 Enterprise**
      - **{BYOL} Windows Server Datacenter 2016의 SQL Server 2016 Standard**

   >[!IMPORTANT]
   >가상 머신을 만든 후에 사전 설치된 독립 실행형 SQL Server 인스턴스를 제거합니다. 장애 조치 클러스터 및 S2D를 구성한 후 사전 설치된 SQL Server 미디어를 사용하여 SQL Server FCI를 만듭니다.

   또는 운영 체제와 함께 Azure Marketplace 이미지를 사용할 수 있습니다. **Windows Server 2016 Datacenter** 이미지를 선택하고 장애 조치 클러스터와 S2D를 구성한 후 SQL Server FCI를 설치합니다. 이 이미지는 SQL Server 설치 이미지를 포함하지 않습니다. 각 서버에 대한 SQL Server 설치를 실행할 수 있는 위치에 설치 미디어를 배치합니다.

1. Azure에서 가상 머신을 만든 후에 RDP로 각 가상 머신에 연결합니다.

   RDP로 가상 머신에 처음 연결하면 컴퓨터는 이 PC를 네트워크에서 검색할 수 있도록 허용하는지 묻습니다. **예**를 클릭합니다.

1. SQL Server 기반 가상 머신 이미지 중 하나를 사용하고 있는 경우 SQL Server 인스턴스를 제거합니다.

   - **프로그램 및 기능**에서 **Microsoft SQL Server 2016(64비트)** 을 마우스 오른쪽 단추로 클릭하고 **제거/변경**을 클릭합니다.
   - **제거**를 클릭합니다.
   - 기본 인스턴스를 선택합니다.
   - **데이터베이스 엔진 서비스**에서 모든 기능을 제거합니다. **공유 기능**을 제거하지 마십시오. 다음 그림을 참조하세요.

      ![기능 제거](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - **다음**을 클릭한 후 **제거**를 클릭합니다.

1. <a name="ports"></a>방화벽 포트를 엽니다.

   각 가상 머신에서 Windows 방화벽의 다음 포트를 엽니다.

   | 목적 | TCP 포트 | 메모
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server의 기본 인스턴스에 대한 표준 포트입니다. 갤러리에서 이미지를 사용한 경우 이 포트는 자동으로 열립니다.
   | 상태 프로브 | 59999 | 모든 공개 TCP 포트입니다. 이후 단계에서 이 포트를 사용하려면 부하 분산 장치 [상태 프로브](#probe) 및 클러스터를 구성합니다.  

1. 가상 컴퓨터에 저장소를 추가합니다. 자세한 내용은 [저장소 추가](../disks-types.md)를 참조하세요.

   두 가상 머신에 두 개 이상의 데이터 디스크가 필요합니다.

   NTFS 포맷 디스크가 아닌 원시 디스크를 연결합니다.
      >[!NOTE]
      >NTFS 포맷 디스크를 연결하는 경우 디스크 자격 확인 없이 S2D를 사용할 수 있습니다.  

   각 VM에 최소 두 개의 프리미엄 SSD를 연결합니다. 적어도 P30(1TB) 디스크가 좋습니다.

   호스트 캐싱을 **읽기 전용**으로 설정합니다.

   프로덕션 환경에서 사용하는 저장소 용량은 작업에 따라 달라집니다. 이 문서에서 설명하는 값은 데모 및 테스트용입니다.

1. [기존 도메인에 가상 머신을 추가합니다](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

가상 머신을 만들고 구성한 후에 장애 조치 클러스터를 구성할 수 있습니다.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>2단계: S2D로 Windows 장애 조치(Failover) 클러스터 구성

다음 단계는 S2D로 장애 조치 클러스터를 구성하는 것입니다. 이 단계에서는 다음 하위 단계를 수행합니다.

1. Windows 장애 조치(Failover) 클러스터링 기능 추가
1. 클러스터 유효성 검사
1. 장애 조치 클러스터 만들기
1. 클라우드 감시 만들기
1. 저장소 추가

### <a name="add-windows-failover-clustering-feature"></a>Windows 장애 조치(Failover) 클러스터링 기능 추가

1. 시작하려면 로컬 관리자의 구성원이며 Active Directory에 개체를 만들 수 있는 권한이 있는 도메인 계정을 사용하여 RDP로 첫 번째 가상 머신에 연결합니다. 구성의 나머지 부분에서는 이 계정을 사용합니다.

1. [장애 조치(Failover) 클러스터링 기능을 각 가상 머신에 추가합니다](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   UI에서 장애 조치(Failover) 클러스터링 기능을 설치하려면 두 가상 머신에서 다음 단계를 수행합니다.
   - **서버 관리자**에서 **관리**를 클릭한 다음 **역할 및 기능 추가**를 클릭합니다.
   - **역할 및 기능 추가 마법사**에서 **기능 선택**이 표시될 때까지 **다음**을 클릭합니다.
   - **기능 선택**에서 **장애 조치(Failover) 클러스터링**을 클릭합니다. 필요한 모든 기능 및 관리 도구를 포함합니다. **기능 추가**를 클릭합니다.
   - **다음**을 클릭한 다음 **마침**을 클릭하여 기능을 설치합니다.

   PowerShell로 장애 조치(Failover) 클러스터링 기능을 설치하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

참고로 다음 단계는 [Windows Server 2016의 저장소 공간 다이렉트를 사용하는 하이퍼 수렴형 솔루션](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)의 3단계 지침을 따릅니다.

### <a name="validate-the-cluster"></a>클러스터 유효성 검사

이 가이드는 [클러스터 유효성 검사](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation)의 지침을 설명합니다.

UI에서 또는 PowerShell을 사용하여 클러스터의 유효성을 검사합니다.

UI를 사용하여 클러스터의 유효성을 검사하려면 가상 머신 중 하나에서 다음 단계를 수행합니다.

1. **서버 관리자**에서 **도구**를 클릭한 다음 **장애 조치(Failover) 클러스터 관리자**를 클릭합니다.
1. **장애 조치(Failover) 클러스터 관리자**에서 **작업**을 클릭한 다음 **구성 유효성 검사...** 를 클릭합니다.
1. **다음**을 클릭합니다.
1. **서버 또는 클러스터 선택**에서 두 가상 머신의 이름을 입력합니다.
1. **테스트 옵션**에서 **선택한 테스트만 실행**을 선택합니다. **다음**을 클릭합니다.
1. **테스트 선택**에서 **저장소**를 제외한 모든 테스트를 포함합니다. 다음 그림을 참조하세요.

   ![유효성 검사 테스트](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. **다음**을 클릭합니다.
1. **확인**에서 **다음**을 클릭합니다.

**구성 유효성 검사 마법사**가 유효성 검사 테스트를 실행합니다.

PowerShell로 클러스터의 유효성을 검사하려면 가상 머신 중 하나의 관리자 PowerShell 세션에서 다음 스크립트를 실행합니다.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

클러스터의 유효성을 검사한 후에 장애 조치 클러스터를 만듭니다.

### <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기

이 가이드에서는 [장애 조치 클러스터 만들기](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster)를 설명합니다.

장애 조치 클러스터를 만들려면 다음이 필요합니다.
- 클러스터 노드가 되는 가상 머신의 이름.
- 장애 조치 클러스터의 이름
- 장애 조치 클러스터의 IP 주소 클러스터 노드와 동일한 Azure 가상 네트워크 및 서브넷에 사용되지 않는 IP 주소를 사용할 수 있습니다.

다음 PowerShell은 장애 조치 클러스터를 만듭니다. 스크립트를 노드의 이름(가상 머신 이름) 및 Azure VNET에서 사용 가능한 IP 주소 이름으로 업데이트합니다.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>클라우드 감시 만들기

클라우드 감시는 Azure Storage Blob에 저장된 새로운 유형의 클러스터 쿼럼 감시입니다. 감시 공유를 호스트하는 별도 VM의 필요성을 제거합니다.

1. [장애 조치 클러스터에 대한 클라우드 감시를 만듭니다](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Blob 컨테이너를 만듭니다.

1. 액세스 키 및 컨테이너 URL을 저장합니다.

1. 장애 조치(Failover) 클러스터 쿼럼 감시를 구성합니다. UI에서 [사용자 인터페이스에서 쿼럼 감시 구성](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)을 참조하세요.

### <a name="add-storage"></a>저장소 추가

S2D용 디스크는 비어 있고 파티션 또는 기타 데이터가 없어야 합니다. 디스크를 정리하려면 [이 가이드의 단계](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks)를 따릅니다.

1. [저장소 공간 다이렉트 \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)를 활성화합니다.

   다음 PowerShell은 저장소 공간 다이렉트를 활성화합니다.  

   ```powershell
   Enable-ClusterS2D
   ```

   **장애 조치(Failover) 클러스터 관리자**에서 이제 저장소 풀을 볼 수 있습니다.

1. [볼륨을 만듭니다](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   S2D의 기능 중 하나는 이를 활성화할 때 저장소 풀을 자동으로 만드는 것입니다. 이제 볼륨을 만들 준비가 되었습니다. PowerShell commandlet `New-Volume`은 서식 지정, 클러스터에 추가 및 CSV(클러스터 공유 볼륨) 만들기를 포함하는 볼륨 생성 프로세스를 자동화합니다. 다음 예제에서는 800GB(기가바이트) CSV를 만듭니다.

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   이 명령이 완료되면 클러스터 리소스로 800GB 볼륨이 탑재됩니다. 볼륨은 `C:\ClusterStorage\Volume1\`에 있습니다.

   다음 다이어그램에서는 S2D로 클러스터 공유 볼륨을 보여 줍니다.

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>3단계: 장애 조치(failover) 클러스터의 장애 조치(failover) 테스트

장애 조치(Failover) 클러스터 관리자에서 저장소 리소스를 다른 클러스터 노드로 이동할 수 있는지 확인합니다. **장애 조치(Failover) 클러스터 관리자**를 사용하여 장애 조치 클러스터에 연결하고 한 노드에서 다른 노드로 저장소를 이동할 수 있는 경우 이제 FCI를 구성할 준비가 되었습니다.

## <a name="step-4-create-sql-server-fci"></a>4단계: SQL Server FCI 만들기

장애 조치 클러스터 및 저장소를 포함한 모든 클러스터 구성 요소를 구성한 후 SQL Server FCI를 만들 수 있습니다.

1. RDP를 사용하여 첫 번째 가상 컴퓨터에 연결합니다.

1. **장애 조치(Failover) 클러스터 관리자**에서 모든 클러스터 코어 리소스가 첫 번째 가상 머신에 있는지 확인합니다. 필요한 경우 모든 리소스를 이 가상 컴퓨터로 이동합니다.

1. 설치 미디어를 찾습니다. 가상 머신이 Azure Marketplace 이미지 중 하나를 사용하는 경우 미디어는 `C:\SQLServer_<version number>_Full`에 있습니다. **설치**를 클릭합니다.

1. **SQL Server 설치 센터**에서 **설치**를 클릭합니다.

1. **새 SQL Server 장애 조치(failover) 클러스터 설치**를 클릭합니다. 마법사의 지침에 따라 SQL Server FCI를 설치합니다.

   FCI 데이터 디렉터리는 클러스터형 저장소에 있어야 합니다. S2D를 사용하는 경우 공유 디스크가 아니고 각 서버의 볼륨에 대한 탑재 지점입니다. S2D는 두 노드 간의 볼륨을 동기화합니다. 볼륨은 클러스터 공유 볼륨으로 클러스터에 표시됩니다. 데이터 디렉터리에 CSV 탑재 지점을 사용합니다.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. 마법사를 완료한 후 설정은 첫 번째 노드에 SQL Server FCI를 설치합니다.

1. 설정이 첫 번째 노드에 FCI를 성공적으로 설치한 후 RDP를 사용하여 두 번째 노드에 연결합니다.

1. **SQL Server 설치 센터**를 엽니다. **설치**를 클릭합니다.

1. **SQL Server 장애 조치(failover) 클러스터에 노드 추가**를 클릭합니다. 마법사의 지침에 따라 SQL Server를 설치하고 이 서버를 FCI에 추가합니다.

   >[!NOTE]
   >SQL Server와 함께 Azure Marketplace 갤러리 이미지를 사용한 경우 SQL Server 도구는 이미지에 포함되었습니다. 이 이미지를 사용하지 않은 경우 SQL Server 도구를 개별적으로 설치합니다. [SSMS(SQL Server Management Studio) 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)를 참조하세요.

## <a name="step-5-create-azure-load-balancer"></a>5단계: Azure Load Balancer 만들기

Azure 가상 머신에서 클러스터는 한 번에 하나의 클러스터 노드에 있어야 하는 IP 주소를 저장하는 부하 분산 장치를 사용합니다. 이 솔루션에서 부하 분산 장치는 SQL Server FCI에 대한 IP 주소를 저장합니다.

[Azure Load Balancer를 만들고 구성합니다](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 만들기

부하 분산 장치를 만들려면:

1. Azure Portal에서 가상 머신을 사용하여 리소스 그룹으로 이동합니다.

1. **+ 추가**를 클릭합니다. **부하 분산 장치**에 대한 Marketplace를 검색합니다. **부하 분산 장치**를 클릭합니다.

1. **만들기**를 클릭합니다.

1. 다음으로 부하 분산 장치를 구성합니다.

   - **이름**: 부하 분산 장치를 식별하는 이름입니다.
   - **유형**: 부하 분산 장치는 공개 또는 개인일 수 있습니다. 동일한 VNET 내에서 개인 부하 분산 장치에 액세스할 수 있습니다. 대부분의 Azure 애플리케이션은 개인 부하 분산 장치를 사용할 수 있습니다. 애플리케이션에 인터넷을 통해 직접 SQL Server에 대한 액세스가 필요한 경우 공개 부하 분산 장치를 사용합니다.
   - **Virtual Network**: 가상 머신과 동일한 네트워크입니다.
   - **서브넷**: 가상 머신과 동일한 서브넷입니다.
   - **개인 IP 주소**: SQL Server FCI 클러스터 네트워크 리소스에 할당한 동일한 IP 주소입니다.
   - **구독**: Azure 구독.
   - **리소스 그룹**: 가상 머신과 동일한 리소스 그룹을 사용합니다.
   - **Location**: 가상 머신과 동일한 Azure 위치를 사용합니다.
   다음 그림을 참조하세요.

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀 구성

1. 가상 머신을 사용하여 Azure 리소스 그룹으로 돌아가고 새 부하 분산 장치를 찾습니다. 리소스 그룹의 보기를 새로 고쳐야 할 수도 있습니다. 부하 분산 장치를 클릭합니다.

1. **백 엔드 풀**을 클릭하고 **+ 추가**를 클릭하여 백 엔드 풀을 추가합니다.

1. VM이 포함된 가용성 집합과 백 엔드 풀을 연결합니다.

1. **대상 네트워크 IP 구성**에서 **가상 머신**을 확인하고, 클러스터 노드로 참여하는 가상 머신을 선택합니다. FCI를 호스팅하는 모든 가상 머신을 포함해야 합니다. 

1. **확인**을 클릭하여 백엔드 풀을 만듭니다.

### <a name="configure-a-load-balancer-health-probe"></a>부하 분산 장치 상태 프로브 구성

1. 부하 분산 장치 블레이드에서 **상태 프로브**를 클릭합니다.

1. **+ 추가**를 클릭합니다.

1. **상태 프로브 추가** 블레이드에서 <a name="probe"></a>상태 프로브 매개 변수를 설정합니다.

   - **이름**: 상태 프로브의 이름입니다.
   - **프로토콜**: TCP입니다.
   - **포트**: 상태 프로브에 대 한 방화벽에서 만든 포트에 설정할 [이 단계](#ports)합니다. 이 문서의 예제에서는 TCP 포트 `59999`합니다.
   - **간격**: 5초입니다.
   - **비정상 임계값**: 2번 연속 실패입니다.

1. 확인을 클릭합니다.

### <a name="set-load-balancing-rules"></a>부하 분산 규칙 설정

1. 부하 분산 장치 블레이드에서 **부하 분산 규칙**을 클릭합니다.

1. **+ 추가**를 클릭합니다.

1. 부하 분산 규칙 매개 변수를 설정합니다.

   - **이름**: 부하 분산 규칙의 이름입니다.
   - **프런트 엔드 IP 주소**: SQL Server FCI 클러스터 네트워크 리소스에 대한 IP 주소를 사용합니다.
   - **포트**: SQL Server FCI TCP 포트에 대해 설정합니다. 기본 인스턴스 포트는 1433입니다.
   - **백 엔드 포트**: 이 값은 **부동 IP(Direct Server Return)** 를 활성화할 때 **포트** 값과 동일한 포트를 사용합니다.
   - **백 엔드 풀**: 이전에 구성한 백 엔드 풀 이름을 사용합니다.
   - **상태 프로브**: 이전에 구성한 상태 프로브를 사용합니다.
   - **세션 지속성**: 없음.
   - **유휴 제한 시간(분)**: 4.
   - **부동 IP(Direct Server Return)**: Enabled

1. **확인**을 클릭합니다.

## <a name="step-6-configure-cluster-for-probe"></a>6단계: 프로브에 대한 클러스터 구성

PowerShell에서 클러스터 프로브 포트 매개 변수를 설정합니다.

클러스터 프로브 포트 매개 변수를 설정하려면 다음 스크립트의 변수를 사용자 환경의 값으로 업데이트합니다. 스크립트에서 꺾쇠 괄호 `<>`를 제거합니다. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

이전 스크립트에서 사용자 환경에 대한 값을 설정합니다. 다음 목록에서는 값을 설명합니다.

   - `<Cluster Network Name>`: 네트워크의 Windows Server 장애 조치(failover) 클러스터 이름입니다. **장애 조치(Failover) 클러스터 관리자** > **네트워크**에서 네트워크를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다. 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP 주소 리소스 이름입니다. **장애 조치(Failover) 클러스터 관리자** > **역할**의 SQL Server FCI 역할 아래에 있는 **서버 이름**에서 IP 주소 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 올바른 값은 **일반** 탭의 **이름** 아래에 있습니다. 

   - `<ILBIP>`: ILB IP 주소입니다. 이 주소는 Azure Portal에서 ILB 프런트 엔드 주소로 구성됩니다. 또한 SQL Server FCI IP 주소입니다. 이 주소는 `<SQL Server FCI IP Address Resource Name>`이 있는 동일한 속성 페이지의 **장애 조치(Failover) 클러스터 관리자**에서 확인할 수 있습니다.  

   - `<nnnnn>`: 부하 분산 장치 상태 프로브에서 구성한 프로브 포트입니다. 사용하지 않는 모든 TCP 포트는 유효합니다. 

>[!IMPORTANT]
>클러스터 매개 변수에 대한 서브넷 마스크는 TCP IP 브로드캐스트 주소여야 합니다(`255.255.255.255`).

클러스터 프로브를 설정한 후에는 PowerShell에서 모든 클러스터 매개 변수를 볼 수 있습니다. 다음 스크립트를 실행합니다.

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>7단계: FCI 장애 조치(failover) 테스트

FCI의 장애 조치(failover)를 테스트하여 클러스터 기능의 유효성을 검사합니다. 다음 단계를 수행합니다.

1. RDP를 사용하여 SQL Server FCI 클러스터 노드 중 하나에 연결합니다.

1. **장애 조치(Failover) 클러스터 관리자**를 엽니다. **역할**을 클릭합니다. SQL Server FCI 역할을 소유하는 노드를 살펴봅니다.

1. SQL Server FCI 역할을 마우스 오른쪽 단추로 클릭합니다.

1. **이동**을 클릭하고 **가장 적합한 노드**를 클릭합니다.

**장애 조치(Failover) 클러스터 관리자**는 역할을 보여 주고 해당 리소스는 오프라인으로 전환합니다. 그런 다음 리소스는 이동하고 다른 노드에서 온라인 상태로 전환합니다.

### <a name="test-connectivity"></a>연결 테스트

연결을 테스트하려면 동일한 가상 네트워크의 다른 가상 머신에 로그인합니다. **SQL Server Management Studio**를 열고 SQL Server FCI 이름에 연결합니다.

>[!NOTE]
>필요한 경우 [SQL Server Management Studio를 다운로드](https://msdn.microsoft.com/library/mt238290.aspx)할 수 있습니다.

## <a name="limitations"></a>제한 사항

Azure Virtual Machines는 가상 머신 CSV(클러스터형 공유 볼륨)의 저장소와 [표준 Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)가 있는 Windows Server 2019에서 MSDTC(Microsoft Distributed Transaction Coordinator)를 지원합니다.

Azure Virtual Machines의 Windows Server 2016 및 이전 버전에서는 다음과 같은 이유로 MSDTC가 지원되지 않습니다.

- 클러스터형 MSDTC 리소스는 공유 저장소를 사용하도록 구성할 수 없습니다. Windows Server 2016에서 MSDTC 리소스를 만드는 경우 공유 저장소가 있더라도 사용 가능한 공유 저장소가 표시되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 Load Balancer는 RPC 포트를 처리하지 않습니다.

## <a name="see-also"></a>관련 항목

[원격 데스크톱(Azure)을 사용하여 S2D 설치](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[저장소 공간 다이렉트를 사용하는 하이퍼 수렴형 솔루션](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[저장소 공간 다이렉트 개요](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[S2D에 대한 SQL Server 지원](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
