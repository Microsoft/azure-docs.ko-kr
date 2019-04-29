---
title: Azure Site Recovery를 사용하여 파일 서버 보호
description: 이 문서는 Azure Site Recovery를 사용하여 파일 서버를 보호하는 방법에 대해 설명합니다.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 51754021f5029a751be90bfc4194ac6347c1e278
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772158"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 파일 서버 보호 

[Azure Site Recovery](site-recovery-overview.md)는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 재해 복구를 관리 및 오케스트레이션합니다. 재해 복구는 복제, 장애 조치(failover) 및 다양한 작업의 복구를 포함합니다.

이 문서에서는 Site Recovery를 사용하여 파일 서버를 보호하는 방법을 설명하고 다양한 환경에 맞도록 기타 권장 사항을 제공합니다. 

- [Azure IaaS 파일 서버 컴퓨터 복제](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Site Recovery를 사용하여 온-프레미스 파일 서버 복제](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>파일 서버 아키텍처
열려 있는 분산된 파일 공유 시스템의 목적은 지리적으로 분산된 사용자 그룹이 협력하여 효율적으로 파일을 사용하고 무결성 요구 사항이 적용되도록 보장할 수 있는 환경을 제공하는 것입니다. 수많은 동시 사용자 및 콘텐츠 항목을 지원하는 일반적인 온-프레미스 파일 서버 에코시스템은 복제 일정 및 대역폭 제한에 DFSR(분산 파일 시스템 복제)을 사용합니다. 

DFSR은 RDC(원격 차등 압축)라는 압축 알고리즘을 사용합니다. 이 기능은 제한된 대역폭 네트워크를 통해 효율적으로 파일을 업데이트하는 데 사용할 수 있습니다. 파일에서 데이터의 삽입, 제거 및 재배열을 검색합니다. 파일이 업데이트될 때 변경된 파일 블록만 복제하도록 DFSR이 활성화됩니다. 사용량이 많지 않은 시간에 매일 백업을 수행하는 파일 서버 환경도 있습니다. 여기서는 재해 요구 사항을 충족합니다. DFSR이 구현되지 않습니다.

다음과 같은 다이어그램은 DFSR을 구현한 파일 서버 환경을 보여줍니다.
                
![DFSR 아키텍처](media/site-recovery-file-server/dfsr-architecture.JPG)

위의 다이어그램에서 여러 파일 서버는 복제 그룹 간에 파일을 복제하는 데 적극적으로 참여하는 멤버를 호출했습니다. 복제된 폴더의 콘텐츠는 멤버가 오프라인으로 전환되는 경우에도 멤버 중 하나에 요청을 전송하는 모든 클라이언트에 사용할 수 있습니다.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>파일 서버의 재해 복구 권장 사항

* **Site Recovery를 사용하여 파일 서버 복제**: Site Recovery를 사용하여 파일 서버를 Azure에 복제할 수 있습니다. 하나 이상의 온-프레미스 파일 서버에 액세스할 수 없는 경우 Azure에서 복구 VM을 가져올 수 있습니다. 그런 다음, VM은 클라이언트, 온-프레미스에서 요청을 처리할 수 있습니다. 여기에서 제공되는 사이트 간 VPN 연결 및 Active Directory는 Azure에서 구성됩니다. DFSR이 구성된 환경 또는 DFSR이 없는 단순한 파일 서버 환경의 경우에 이 메서드를 사용할 수 있습니다. 

* **DFSR을 Azure IaaS VM으로 확장**: DFSR이 구현된 클러스터된 파일 서버 환경에서 Azure로 온-프레미스 DFSR을 확장할 수 있습니다. Azure VM은 파일 서버 역할을 수행하도록 설정됩니다. 

    * 사이트 간 VPN 연결 및 Active Directory의 종속성을 처리하고 DFSR이 설정되면 하나 이상의 온-프레미스 파일 서버에 액세스할 수 없는 경우에도 클라이언트는 Azure VM에 연결할 수 있으므로 요청을 처리합니다.

    * VM에 Site Recovery에서 지원되지 않는 구성이 있는 경우 이 방법을 사용할 수 있습니다. 예는 경우에 따라 일반적으로 파일 서버 환경에서 사용되는 공유 클러스터 디스크입니다. DFSR은 중간 변동률을 사용하여 대역폭이 낮은 환경에서도 잘 작동합니다. Azure VM을 사용하고 항상 실행하는 추가 비용을 고려해야 합니다. 

* **Azure File Sync를 사용하여 파일 복제**: 클라우드를 사용하려는 경우 또는 Azure VM을 이미 사용하는 경우 Azure 파일 동기화를 사용할 수 있습니다. Azure 파일 동기화는 산업 표준 SMB([서버 메시지 블록](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)) 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유의 동기화를 제공합니다. Azure 파일 공유는 Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포를 통해 동시에 탑재될 수 있습니다. 

다음 다이어그램을 사용하여 파일 서버 환경에 사용할 전략을 결정할 수 있습니다.

![의사 결정 트리](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Azure로 재해 복구에 대한 결정 사항에 고려해야 할 요소

|Environment  |권장 사항  |고려할 항목 |
|---------|---------|---------|
|DFSR 포함 또는 제외 파일 서버 환경|   [복제를 위해 Site Recovery 사용](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery는 공유 디스크 클러스터 또는 NAS(Network Attached Storage)를 지원하지 않습니다. 환경에서 이러한 구성을 사용하는 경우 적절한 다른 인증 방법 중 하나를 사용합니다. <br> Site Recovery는 SMB 3.0을 지원하지 않습니다. 복제된 VM은 파일에 대한 변경 내용이 파일의 원래 위치에서 업데이트되는 경우에만 변경 내용을 통합합니다.
|DFSR 포함 파일 서버 환경     |  [Azure IaaS 가상 머신으로 확장된 DFSR](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR은 대역폭 환경에서 잘 작동합니다. 이 방법에서는 Azure VM을 항상 실행해야 합니다. 계획에서 VM의 비용을 고려해야 합니다.         |
|Azure IaaS VM     |     파일 동기화    |     재해 복구 시나리오에서 파일 동기화를 사용하는 경우 장애 조치(failover) 중 파일 공유가 투명한 방식으로 클라이언트 컴퓨터에 액세스할 수 있는지 확인하기 위해 수동 작업을 수행해야 합니다. 파일 동기화에는 클라이언트 컴퓨터에서 포트 445가 열려 있어야 합니다.     |


### <a name="site-recovery-support"></a>Site Recovery 지원
Site Recovery 복제는 애플리케이션을 제한하지 않으므로 권장 사항은 다음 시나리오에서도 유지됩니다.

| 원본    |보조 사이트 대상    |Azure 대상
|---------|---------|---------|
|Azure| -|예|
|Hyper-V|   예 |예
|VMware |예|   예
|물리적 서버|   예 |예
 

> [!IMPORTANT]
> 다음 세 가지 방법을 계속 진행하기 전에 해당 종속성이 처리되는지를 확인합니다.

**사이트 간 연결**: 서버 간 통신을 허용하도록 온-프레미스 사이트와 Azure 네트워크 간에 직접 연결을 설정해야 합니다. 재해 복구 사이트로 사용되는 Azure 가상 네트워크에 안전한 사이트 간 VPN 연결을 사용합니다. 자세한 내용은 [온-프레미스 사이트와 Azure 가상 네트워크 간의 사이트 간 VPN 연결 설정](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)을 참조하세요.

**Active Directory**: DFSR은 Active Directory에 따라 달라집니다. 즉, 로컬 도메인 컨트롤러와 Active Directory 포리스트를 Azure의 재해 복구 사이트로 확장합니다. DFSR을 사용하지 않더라도 대상 사용자가 액세스를 부여 받거나 액세스에 대한 확인이 필요한 경우 이러한 단계를 수행해야 합니다. 자세한 내용은 [Azure로 온-프레미스 Active Directory 확장](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory)을 참조하세요.

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Azure IaaS 가상 머신에 대한 재해 복구 권장 사항

Azure IaaS VM에 호스팅되는 파일 서버의 재해 복구를 관리하는 경우 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)로 이동할지 여부에 따라 두 가지 옵션 중에서 선택할 수 있습니다.

* [파일 동기화 사용](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Site Recovery 사용](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>파일 동기화를 사용하여 IaaS 가상 머신에서 호스팅되는 파일 복제

Azure Files는 기존의 온-프레미스 파일 서버 또는 NAS 디바이스를 완전히 바꾸거나 보완하는 데 사용할 수 있습니다. Azure 파일 공유는 사용되는 데이터의 성능 및 분산 캐싱을 위해 파일 동기화를 사용하여 온-프레미스 또는 클라우드의 Windows 서버에 복제될 수도 있습니다. 다음 단계에서는 기존의 파일 서버와 동일한 기능을 수행하는 Azure VM에 대한 재해 복구 권장 사항을 설명합니다.
* Site Recovery를 사용하여 컴퓨터를 보호합니다. [다른 Azure 지역에 Azure VM 복제](azure-to-azure-quickstart.md)의 단계를 따릅니다.
* 파일 동기화를 사용하여 파일 서버 역할을 하는 VM에서 클라우드로 파일을 복제합니다.
* Site Recovery의 [복구 계획](site-recovery-create-recovery-plans.md) 기능을 사용하여 [Azure 파일 공유를 탑재](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)하고 가상 머신에서 공유 권한에 액세스하는 스크립트를 추가합니다.

다음 단계는 파일 동기화를 사용하는 방법을 간단하게 설명합니다.

1. [Azure에서 저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 저장소 계정에 대해 읽기 액세스 지역 중복 저장소를 선택한 경우 재해 발생 시 보조 지역의 데이터에 대한 읽기 권한을 가집니다. 자세한 내용은 [Azure Storage에서 재해 복구 및 강제 장애 조치(Failover)(미리 보기)](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)를 참조하세요.
2. [파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. Azure 파일 서버에서 [파일 동기화를 시작합니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. 동기화 그룹을 만듭니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나 이상의 클라우드 엔드포인트를 포함해야 합니다. 또한 동기화 그룹은 Windows 서버의 경로를 나타내는 하나의 서버 엔드포인트를 포함해야 합니다.
5. 파일은 이제 Azure 파일 공유 및 온-프레미스서버와 동기화된 상태를 유지합니다.
6. 온-프레미스 환경에서 재해 발생 시 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 장애 조치(failover)를 수행합니다. 스크립트를 추가하여 [Azure 파일 공유를 탑재](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)하고 가상 머신에서 공유에 액세스합니다.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Site Recovery를 사용하여 IaaS 파일 서버 가상 머신 복제

IaaS 파일 서버 가상 머신에 액세스하는 온-프레미스 클라이언트가 있는 경우 다음 모든 단계를 수행합니다. 그렇지 않은 경우, 3단계로 건너뜁니다.

1. 온-프레미스 사이트와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다.
2. 온-프레미스 Active Directory를 확장합니다.
3. 보조 지역에 IaaS 파일 서버 컴퓨터에 대한 [재해 복구를 설정합니다](azure-to-azure-tutorial-enable-replication.md).


보조 지역에 대한 재해 복구의 자세한 내용은 [이 문서](concepts-azure-to-azure-architecture.md)를 참조하세요.


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Site Recovery를 사용하여 온-프레미스 파일 서버 복제

다음 단계에서는 VMware VM에 대한 복제를 설명합니다. Hyper-V VM을 복제하는 단계는 [이 자습서](tutorial-hyper-v-to-azure.md)를 참조하세요.

1. 온-프레미스 컴퓨터 복제를 위한 [Azure 리소스 준비](tutorial-prepare-azure.md)
2. 온-프레미스 사이트와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다. 
3. 온-프레미스 Active Directory를 확장합니다.
4. [온-프레미스 VMware 서버를 준비합니다](tutorial-prepare-on-premises-vmware.md).
5. Azure에 온-프레미스 VM에 대한 [재해 복구를 설정합니다](tutorial-vmware-to-azure.md).

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Azure IaaS 가상 머신으로 DFSR 확장

1. 온-프레미스 사이트와 Azure 네트워크 간에 사이트 간 VPN 연결을 설정합니다. 
2. 온-프레미스 Active Directory를 확장합니다.
3. Azure 가상 네트워크에서 [파일 서버 VM을 만들고 프로비전합니다](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
가상 머신을 온-프레미스 환경과 교차 연결한 동일한 Azure 가상 네트워크에 추가했는지 확인합니다. 
4. Windows Server에서 [DFSR을 설치하고 구성합니다](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx).
5. [DFS 네임스페이스를 구현합니다](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. DFS 네임스페이스를 구현하면 프로덕션에서 재해 복구 사이트로 공유 폴더의 장애 조치는 DFS 네임스페이스 폴더 대상을 업데이트하여 수행할 수 있습니다. 이러한 DFS 네임스페이스 변경 내용이 Active Directory를 통해 복제되면 사용자가 적절한 폴더 대상에 투명하게 연결됩니다.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>파일 동기화를 사용하여 온-프레미스 파일 복제
파일 동기화를 사용하여 클라우드로 파일을 복제할 수 있습니다. 온-프레미스 파일 서버에 재해 및 불가용성이 발생하는 경우 클라우드의 원하는 파일 위치를 탑재하고 클라이언트 컴퓨터의 서비스 요청을 진행할 수 있습니다.
Site Recovery와 파일 동기화를 통합하려면:

* Site Recovery를 사용하여 파일 서버 컴퓨터를 보호합니다. [이 자습서](tutorial-vmware-to-azure.md)의 단계를 따릅니다.
* 파일 동기화를 사용하여 파일 서버 역할을 하는 컴퓨터에서 클라우드로 파일을 복제합니다.
* Site Recovery에서 복구 계획 기능을 사용하여 Azure에서 장애 조치된 파일 서버 VM에서 Azure 파일 공유를 탑재하는 스크립트를 추가합니다.

파일 동기화를 사용하려면 다음 단계를 수행합니다.

1. [Azure에서 저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 저장소 계정에 대해 읽기 액세스 지역 중복 저장소(권장)를 선택한 경우 재해 발생 시 보조 지역의 데이터에 대한 읽기 권한을 가집니다. 자세한 내용은 [Azure Storage에서 재해 복구 및 강제 장애 조치(Failover)(미리 보기)](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json)를 참조하세요.
2. [파일 공유 만들기](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. 온-프레미스 파일 서버에서 [파일 동기화를 배포합니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide).
4. 동기화 그룹을 만듭니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나 이상의 클라우드 엔드포인트를 포함해야 합니다. 또한 동기화 그룹은 온-프레미스 Windows 서버의 경로를 나타내는 하나의 서버 엔드포인트를 포함해야 합니다.
5. 파일은 이제 Azure 파일 공유 및 온-프레미스서버와 동기화된 상태를 유지합니다.
6. 온-프레미스 환경에서 재해 발생 시 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 장애 조치(failover)를 수행합니다. 스크립트를 추가하여 Azure 파일 공유를 탑재하고 가상 머신에서 공유에 액세스합니다.

> [!NOTE]
> 포트 445가 열려 있는지 확인합니다. Azure Files는 SMB 프로토콜을 사용합니다. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 TCP 포트 445를 차단하고 있지 않은지 확인합니다.


## <a name="do-a-test-failover"></a>테스트 장애 조치(failover) 실행

1. Azure Portal로 이동하고, 복구 서비스 자격 증명 모음을 선택합니다.
2. 파일 서버 환경에서 만든 복구 계획을 선택합니다.
3. **테스트 장애 조치**를 선택합니다.
4. 복구 지점과 Azure 가상 네트워크를 선택하여 테스트 장애 조치(failover) 프로세스를 시작합니다.
5. 보조 환경이 가동되면 유효성 검사를 수행합니다.
6. 유효성 검사가 완료되면 복구 계획에서 **테스트 장애 조치(failover) 정리**를 선택하고, 테스트 장애 조치(failover) 환경이 정리됩니다.

테스트 장애 조치(failover)를 수행하는 방법에 대한 자세한 내용은 [Site Recovery로 테스트 장애 조치(failover)](site-recovery-test-failover-to-azure.md)를 참조하세요.

Active Directory 및 DNS에 대한 테스트 장애 조치(failover) 수행에 관한 지침은 [Active Directory 및 DNS에 대한 테스트 장애 조치(failover) 고려 사항](site-recovery-active-directory.md)을 참조하세요.

## <a name="do-a-failover"></a>장애 조치(failover) 수행

1. Azure Portal로 이동하고, Recovery Services 자격 증명 모음을 선택합니다.
2. 파일 서버 환경에서 만든 복구 계획을 선택합니다.
3. **장애 조치(failover)** 를 선택합니다.
4. 복구 지점을 선택하여 장애 조치(failover) 프로세스를 시작합니다.

장애 조치(failover)를 수행하는 방법에 대한 자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.
