---
title: Azure Site Recovery를 사용한 Azure로의 물리적 서버 복제 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 서비스를 사용하여 온-프레미스 물리적 서버를 Azure로 재해 복구할 때 사용되는 구성 요소 및 아키텍처 개요를 제공합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 99aec3be893693e523dffefbb3c422222ac19a2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947574"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>물리적 서버에서 Azure로의 재해 복구 아키텍처

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 사이트와 Azure 간에 물리적 Windows 및 Linux 서버를 복제, 장애 조치 및 복구할 때 사용되는 아키텍처와 프로세스에 대해 설명합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

다음 표와 그림은 Azure로 물리적 서버를 복제하는 데 사용되는 구성 요소를 개략적으로 보여 줍니다.  

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독 및 Azure 네트워크입니다. | 온-프레미스에서 물리적 컴퓨터는 Azure에 저장 된 복제 된 데이터 디스크를 관리 합니다. 온-프레미스에서 Azure로의 장애 조치가 발생한 경우 복제된 데이터를 사용하여 Azure VM을 만듭니다. Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다.
**구성 서버** | 모든 온-프레미스 Site Recovery 구성 요소를 실행하도록 단일 온-프레미스 물리적 컴퓨터나 VMware VM이 배포됩니다. VM은 구성 서버, 프로세스 서버 및 마스터 대상 서버를 실행합니다. | 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
 **프로세스 서버**:  | 기본적으로 구성 서버와 함께 설치됩니다. | 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure Storage로 전송합니다.<br/><br/> 프로세스 서버도 복제하려는 서버에 모바일 서비스를 설치합니다.<br/><br/> 배포가 늘어나면 프로세스 서버로 실행하는 별도의 프로세스 서버를 추가하여 더 큰 복제 트래픽을 처리할 수 있습니다.
 **마스터 대상 서버** | 기본적으로 구성 서버와 함께 설치됩니다. | Azure에서 장애 복구 중에 복제 데이터를 처리합니다.<br/><br/> 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.
**복제된 서버** | 복제하는 각 서버에 모바일 서비스가 설치됩니다. | 프로세스 서버에서 자동 설치를 허용하는 것이 좋습니다. 또는 서비스를 수동으로 설치하거나 System Center Configuration Manager와 같은 자동화된 배포 방법을 사용할 수 있습니다.

**물리적 서버에서 Azure로 아키텍처**

![구성 요소](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>복제 프로세스

1. 온-프레미스 및 Azure 구성 요소를 포함하여 배포를 설정합니다. Recovery Services 자격 증명 모음에서 복제 원본 및 대상을 지정하고 구성 서버를 설정하며 복제 정책을 만들고 모바일 서비스를 배포하며 복제를 사용하도록 설정합니다.
2. 컴퓨터는 복제 정책에 따라 복제를 시작하고 서버 데이터의 초기 복사본은 Azure Storage로 복제됩니다.
3. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. .hrl 파일에는 컴퓨터에 대한 추적된 변경 내용이 유지됩니다.
    - 컴퓨터는 복제 관리를 위해 HTTPS 443 인바운드 포트에 대한 구성 서버와 통신합니다.
    - 컴퓨터는 복제 데이터를 HTTPS 9443 인바운드 포트(수정될 수 있음)에 대한 프로세스 서버로 전달합니다.
    - 구성 서버는 HTTPS 443 아웃바운드 포트를 통해 Azure를 사용하는 복제 관리를 오케스트레이션합니다.
    - 프로세스 서버가 원본 컴퓨터에서 데이터를 수신하고 이를 최적화 및 암호화하며 443 아웃바운드 포트를 통해 Azure Storage로 전송합니다.
    - 다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 다중 VM은 장애 조치 시(failover) 크래시 일관성 및 앱 일관성 복구 지점을 공유하는 복제 그룹에 여러 컴퓨터를 그룹화하는 경우 사용됩니다. 이 기능은 컴퓨터가 동일한 워크로드를 실행하고 일관성을 유지해야 할 경우에 유용합니다.
4. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. Azure ExpressRoute [공용 피어링](../expressroute/expressroute-circuit-peerings.md#publicpeering)을 사용할 수도 있습니다. 온-프레미스 사이트에서 Azure로의 사이트 간 VPN을 통한 트래픽 복제는 지원되지 않습니다.


**물리적 구성 요소에서 Azure로 복제 프로세스**

![복제 프로세스](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

복제가 설정되고 재해 복구 훈련(테스트 장애 조치)를 실행하여 모든 것이 예상대로 돌아가는 것을 확인한 후에는 필요에 따라 장애 조치와 장애 복구를 실행할 수 있습니다. 다음 사항에 유의하세요.

- 계획된 장애 조치는 지원되지 않습니다.
- 온-프레미스 VMware VM으로 장애 복구해야 합니다. 즉, 온-프레미스 물리적 서버를 Azure로 복제하는 경우에도 온-프레미스 VMware 인프라가 필요합니다.
- 단일 컴퓨터에 장애 조치를 수행하거나 복구 계획을 만들어서 여러 컴퓨터의 장애 조치합니다.
- 장애 조치를 실행하면 복제된 데이터에서 Azure VM이 Azure Storage에 만들어집니다.
- 초기 장애 조치를 트리거한 후 이를 커밋하여 Azure VM에서 워크로드 액세스를 시작합니다.
- 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 장애 복구를 수행할 수 있습니다.
- 다음을 포함한 장애 복구 인프라를 설정해야 합니다.
    - **Azure의 임시 프로세스 서버**: Azure에서 장애 복구하려면 Azure에서 복제를 처리하는 프로세스 서버로 작동하도록 Azure VM을 설정합니다. 장애 복구를 완료한 후 이 VM을 삭제할 수 있습니다.
    - **VPN 연결**: 장애 복구(failback)하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 Azure ExpressRoute)이 필요합니다.
    - **개별 마스터 대상 서버**: 기본적으로 구성 서버와 함께 온-프레미스 VMware VM에 설치된 마스터 대상 서버에서 장애 복구를 처리합니다. 그러나 대용량 트래픽을 장애 복구하는 경우 이 용도로 별도의 온-프레미스 마스터 대상 서버를 설정해야 합니다.
    - **장애 복구 정책**: 온-프레미스 사이트에 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 온-프레미스에서 Azure로의 복제 정책을 만들 때 자동으로 생성됩니다.
    - **VMware 인프라**: 장애 복구에는 VMware 인프라가 필요합니다. 실제 서버로 장애 복구할 수 없습니다.
- 구성 요소를 배치한 후 다음 3단계로 장애 복구가 발생합니다.
    - 1단계: Azure에서 다시 온-프레미스 VMware VM으로 복제하도록 Azure VM을 다시 보호합니다.
    - 2단계: 온-프레미스 사이트로 장애 조치를 실행합니다.
    - 3단계: 워크로드가 장애 복구된 후 다시 복제를 활성화합니다.

**Azure로부터 VMware 장애 복구**

![장애 복구](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>다음 단계

[이 자습서](physical-azure-disaster-recovery.md)를 따라 물리적 서버와 Azure 간의 복제를 활성화합니다.
