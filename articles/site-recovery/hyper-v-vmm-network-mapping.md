---
title: Site Recovery를 사용하여 Azure로의 Hyper-V VM(VMM 사용) 재해 복구를 위한 네트워크 매핑 정보 | Microsoft Docs
description: Azure Site Recovery를 사용하여 Azure로의 VMM 클라우드에서 관리되는 Hyper-V VM의 재해 복구를 위한 네트워크 매핑을 설정하는 방법을 설명합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: cefde79cf8c544a6900b1efa5dbcefbc43638d40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679332"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Azure로 Hyper-V VM 재해 복구를 수행하기 위해 네트워크 매핑 준비


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에서 Azure 또는 보조 사이트로 Hyper-V VM을 복제할 때 네트워크 매핑을 이해하고 준비하도록 도와줍니다.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Azure로의 복제를 위한 네트워크 매핑 준비

Azure로 복제할 때 네트워크 매핑은 원본 VMM 서버의 VM 네트워크와 대상 Azure 가상 네트워크 사이를 매핑합니다. 매핑은 다음을 수행합니다.
-  **네트워크 연결** - 복제된 Azure VM이 매핑된 네트워크에 연결되도록 합니다. 동일한 네트워크에서 장애 조치되는 모든 컴퓨터가 서로 연결될 수 있으며, 이는 서로 다른 복구 계획에서 장애 조치된 경우에도 마찬가지입니다.
- **네트워크 게이트웨이**- 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 VM이 다른 온-프레미스 가상 머신에 연결할 수 있습니다.

네트워크 매핑은 다음과 같이 작동합니다.

- 원본 VMM VM 네트워크를 Azure 가상 네트워크에 매핑합니다.
- 장애 조치 후 원본 네트워크의 Azure VM이 매핑된 대상 가상 네트워크에 연결됩니다.
- 원본 VM 네트워크에 추가된 새 VM은 복제된 후에 매핑된 Azure 네트워크에 연결됩니다.
- 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 머신이 있는 서브넷과 같으면 복제 가상 머신이 장애 조치(failover) 후에 대상 서브넷에 연결됩니다.
- 일치하는 이름을 가진 대상 서브넷이 없으면 가상 머신이 네트워크의 첫 번째 서브넷에 연결됩니다.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>보조 사이트로의 복제를 위한 네트워크 매핑 준비

보조 사이트로 복제할 때 네트워크 매핑은 원본 VMM 서버의 VM 네트워크와 대상 VMM 서버의 VM 네트워크 사이를 매핑합니다. 매핑은 다음을 수행합니다.

- **네트워크 연결** - 장애 조치 후 VM을 적절한 네트워크에 연결합니다. 복제본 VM은 원본 네트워크에 매핑되는 대상 네트워크에 연결됩니다.
- **최적의 VM 배치** - Hyper-V 호스트 서버에 복제본 VM을 가장 적합하게 배치합니다. 복제본 VM은 매핑된 VM 네트워크에 액세스할 수 있는 호스트에 배치됩니다.
- **네트워크 매핑 안 함**- 네트워크 매핑을 구성하지 않으면 장애 조치 후 복제본 VM이 VM 네트워크에 연결되지 않습니다.

네트워크 매핑은 다음과 같이 작동합니다.

- 두 VMM 서버에서 또는 두 사이트가 동일한 서버를 통해 관리되는 경우 단일 VMM 서버의 VM 네트워크 간에 네트워크 매핑을 구성할 수 있습니다.
- 매핑이 올바르게 구성되었고 복제를 설정한 경우 기본 위치의 VM이 네트워크에 연결되고 대상 위치의 복제본이 매핑된 네트워크에 연결됩니다.
- Site Recovery에서 네트워크 매핑 중에 대상 VM 네트워크를 선택하면 보호에 사용되는 대상 클라우드의 사용 가능한 대상 VM 네트워크와 함께 원본 VM 네트워크를 사용하는 VMM 원본 클라우드가 표시됩니다.
- 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 머신이 있는 서브넷과 같으면 복제 VM이 장애 조치(failover) 후에 대상 서브넷에 연결됩니다. 이름이 일치하는 대상 서브넷이 없으면 VM은 네트워크의 첫 번째 서브넷에 연결됩니다.

## <a name="example"></a>예

이 메커니즘을 설명하는 예는 다음과 같습니다. 뉴욕과 시카고 두 위치에 있는 조직을 보겠습니다.

**위치**: | **VMM 서버** | **VM 네트워크** | **다음으로 매핑**
---|---|---|---
뉴욕 | VMM-뉴욕| VMNetwork1-뉴욕 | VMNetwork1-시카고로 매핑
 |  | VMNetwork2-뉴욕 | 매핑되지 않음
시카코 | VMM-시카고| VMNetwork1-시카고 | VMNetwork1-뉴욕으로 매핑
 | | VMNetwork2-시카고 | 매핑되지 않음

이 예제에서:

- VMNetwork1-뉴욕에 연결된 모든 VM에 대한 복제 VM을 만들면 VMNetwork1-시카고에 연결됩니다.
- VMNetwork2-뉴욕 또는 VMNetwork2-시카고에 대한 복제 VM을 만들면 어떤 네트워크에도 연결되지 않습니다.

VMM 클라우드가 예제 조직 및 클라우드와 연결된 논리 네트워크에서 설정된 방식은 다음과 같습니다.

### <a name="cloud-protection-settings"></a>클라우드 보호 설정

**보호된 클라우드** | **클라우드 보호** | **논리 네트워크(뉴욕)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>해당 없음</p><p></p> | <p>LogicalNetwork1-뉴욕</p><p>LogicalNetwork1-시카고</p>
SilverCloud2 | <p>해당 없음</p><p></p> | <p>LogicalNetwork1-뉴욕</p><p>LogicalNetwork1-시카고</p>

### <a name="logical-and-vm-network-settings"></a>논리 및 VM 네트워크 설정

**위치**: | **논리 네트워크** | **연결된 VM 네트워크**
---|---|---
뉴욕 | LogicalNetwork1-뉴욕 | VMNetwork1-뉴욕
시카코 | LogicalNetwork1-시카고 | VMNetwork1-시카고
 | LogicalNetwork2Chicago | VMNetwork2-시카고

### <a name="target-network-settings"></a>대상 네트워크 설정

이러한 설정에 따라 대상 VM 네트워크를 선택하면 다음 표에 사용 가능한 선택 항목이 보입니다.

**선택** | **보호된 클라우드** | **클라우드 보호** | **사용 가능한 대상 네트워크**
---|---|---|---
VMNetwork1-시카고 | SilverCloud1 | SilverCloud2 | 사용 가능
 | GoldCloud1 | GoldCloud2 | 사용 가능
VMNetwork2-시카고 | SilverCloud1 | SilverCloud2 | 사용할 수 없음
 | GoldCloud1 | GoldCloud2 | 사용 가능


대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 머신이 있는 서브넷과 같으면 복제본 가상 머신이 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.


### <a name="failback-behavior"></a>장애 복구 동작

장애 복구(역방향 복제)의 경우 수행되는 작업을 보려면 다음 설정을 사용하여 VMNetwork1-뉴욕이 VMNetwork1-시카고에 매핑되어 있다고 가정해 보겠습니다.


**VM** | **VM 네트워크에 연결**
---|---
VM1 | VMNetwork1-네트워크
VM2(VM1의 복제) | VMNetwork1-시카고

이러한 설정을 사용하여 몇 가지 가능한 시나리오에서 발생하는 결과를 검토해 보겠습니다.

**시나리오** | **결과**
---|---
장애 조치(failover) 후 VM-2의 네트워크 속성이 변경되지 않음 | VM-1에 원본 네트워크에 연결된 상태로 유지됩니다.
장애 조치(failover) 후 VM-2의 네트워크 속성이 변경되고 연결이 끊김 | VM-1의 연결이 끊김
장애 조치(failover) 후 VM-2의 네트워크 속성이 변경되고 VMNetwork2-시카고에 연결됨 | VMNetwork2-시카고가 매핑되지 않는 경우 VM-1의 연결이 끊김
VMNetwork1-시카고의 네트워크 매핑이 변경됨 | VM-1이 현재 VMNetwork1-시카고에 매핑된 네트워크에 연결됨



## <a name="next-steps"></a>다음 단계

- 보조 VMM 사이트로 장애 조치(failover)한 후 IP 주소 지정에 대해 [자세히 알아봅니다](hyper-v-vmm-networking.md).
- Azure로의 장애 조치(Failover) 후 IP 주소 지정에 대해 [자세히 알아봅니다](concepts-on-premises-to-azure-networking.md).
