---
title: Azure Site Recovery를 사용하여 Azure로 Hyper-V VM 재해 복구 준비
description: Azure Site Recovery를 사용하여 Azure로 재해 복구하기 위해 온-프레미스 Hyper-V VM을 준비하는 방법을 알아봅니다.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f24a259d2d71aa6599f6dd417d5e9fc99734e99
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86135663"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Azure로의 재해 복구용으로 온-프레미스 Hyper-V 서버 준비

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure로 Hyper-VM 재해 복구를 설정하려는 경우 온-프레미스 Hyper-V 인프라를 준비하는 방법을 설명합니다.


이는 온-프레미스 Hyper-V VM에 대한 재해 복구를 Azure에 설정하는 방법을 보여주는 자습서 시리즈 중 두 번째 자습서입니다. 첫 번째 자습서에서는 Hyper-V 재해 복구에 필요한 [Azure 구성 요소를 설정](tutorial-prepare-azure.md)했습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Hyper-V 호스트가 System Center VMM에서 관리되는 경우 Hyper-V 요구 사항 및 VMM 요구 사항을 검토합니다.
> * 해당되는 경우 VMM을 준비합니다.
> * Azure 위치에 대한 인터넷 액세스를 확인합니다.
> * Azure로 장애 조치(failover)한 후 액세스할 수 있도록 VM을 준비합니다.

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 자세한 내용은 Site Recovery 목차의 방법 섹션에 있는 문서를 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

[이 시리즈의 첫 번째 자습서](tutorial-prepare-azure.md)에 설명된 대로 Azure가 준비되었는지 확인합니다.

## <a name="review-requirements-and-prerequisites"></a>검토 요구 사항 및 필수 구성 요소

Hyper-V 호스트 및 VM이 요구 사항을 준수하는지 확인합니다.

1. 온-프레미스 서버 요구 사항을 [확인](hyper-v-azure-support-matrix.md#on-premises-servers)합니다.
2. Azure로 복제하려는 Hyper-V VM에 대한 [요구 사항을 확인](hyper-v-azure-support-matrix.md#replicated-vms)합니다.
3. 온-프레미스 Hyper-V 호스트에 대한 Hyper-V 호스트 [네트워킹](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) 및 호스트와 게스트 [스토리지](hyper-v-azure-support-matrix.md#hyper-v-host-storage) 지원을 확인합니다.
4. 장애 조치(failover) 후 [Azure 네트워킹](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [스토리지](hyper-v-azure-support-matrix.md#azure-storage) 및 [컴퓨팅](hyper-v-azure-support-matrix.md#azure-compute-features)에 대해 지원되는 기능을 확인합니다.
5. Azure에 복제하려는 온-프레미스 VM은 [Azure VM 요구 사항](hyper-v-azure-support-matrix.md#azure-vm-requirements)을 준수해야 합니다.


## <a name="prepare-vmm-optional"></a>VMM 준비(선택 사항)

VMM에서 Hyper-V 호스트를 관리하는 경우 온-프레미스 VMM 서버를 준비해야 합니다. 

- VMM 서버에 하나 이상의 클라우드가 있고 호스트 그룹이 하나 이상 있는지 확인합니다. VM이 실행되는 Hyper-V 호스트가 클라우드에 있어야 합니다.
- 네트워크 매핑을 위해 VMM 서버를 준비합니다.

### <a name="prepare-vmm-for-network-mapping"></a>네트워크 매핑용 VMM 준비

VMM을 사용하는 경우 [네트워크 매핑](./hyper-v-vmm-network-mapping.md)은 온-프레미스 VMM VM 네트워크와 Azure 가상 네트워크 간을 매핑합니다. 매핑은 장애 조치(failover) 후에 만들어지는 Azure VM이 올바른 네트워크에 연결되도록 합니다.

다음과 같이 네트워크 매핑용 VMM을 준비합니다.

1. Hyper-V 호스트가 있는 클라우드와 연결된 [VMM 논리 네트워크](/system-center/vmm/network-logical)를 준비합니다.
2. [VM 네트워크](/system-center/vmm/network-virtual)를 논리 네트워크에 연결합니다.
3. VMM에서 VM을 VM 네트워크에 연결합니다.

## <a name="verify-internet-access"></a>인터넷 액세스 확인

1. 이 자습서에서 가장 단순한 구성은 Hyper-V 호스트와 VMM 서버가 프록시를 사용하지 않고 인터넷에 직접 액세스하도록 설정하는 것입니다. 
2. Hyper-V 호스트와 VMM 서버(관련된 경우)가 아래의 필요한 URL에 액세스할 수 있는지 확인합니다.   
3. IP 주소로 액세스를 제어하는 경우 다음을 확인합니다.
    - IP 주소 기준 방화벽 규칙은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 HTTPS(443) 포트에 연결할 수 있습니다.
    - 구독의 Azure 지역에 대해 IP 주소 범위를 허용합니다.
    
### <a name="required-urls"></a>필요한 URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(failover) 시나리오에서는 복제된 온-프레미스 네트워크에 연결하는 것이 좋습니다.

장애 조치(failover) 후 RDP를 사용하여 Windows VM에 연결하려면 다음과 같이 액세스를 허용합니다.

1. 인터넷을 통해 액세스하려면 장애 조치 전에 온-프레미스 VM에서 RDP를 활성화합니다. **공용** 프로필에 대한 TCP 및 UDP 규칙이 추가되었는지와 해당 RDP가 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 허용되는지 확인합니다.
2. 사이트 간 VPN을 통해 액세스하려면 온-프레미스 컴퓨터에서 RDP를 활성화합니다. RDP가 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 **도메인 또는 프라이빗** 네트워크에 대해 허용되어야 합니다.
   운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아봅니다](https://support.microsoft.com/kb/3031135). 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없어야 합니다. 있는 경우 업데이트가 완료될 때까지 가상 머신에 로그인할 수 없습니다.
3. 장애 조치 후 Microsoft Azure VM에서 **부트 진단**을 확인하여 VM의 스크린샷을 검토합니다. 연결할 수 없는 경우 VM이 실행 중인지 확인하고 해당 [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)(영문)을 검토합니다.

장애 조치(failover) 후 복제된 온-프레미스 VM과 동일한 IP 주소 또는 다른 IP 주소를 사용하여 Azure VM에 액세스할 수 있습니다. 장애 조치(failover)에 대한 IP 주소 설정에 관해 [자세히 알아보세요](concepts-on-premises-to-azure-networking.md).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Hyper-V VM에 대해 Azure로의 재해 복구 설정](./hyper-v-azure-tutorial.md)
> [VMM 클라우드의 Hyper-V VM에 대해 Azure로의 재해 복구 설정](./hyper-v-vmm-azure-tutorial.md)
