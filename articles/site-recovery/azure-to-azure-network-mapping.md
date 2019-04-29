---
title: Azure Site Recovery에서 두 Azure 지역 간 가상 네트워크 매핑 | Microsoft Docs
description: Azure Site Recovery는 가상 머신 및 실제 서버의 복제, 장애 조치 및 복구를 조정합니다. Azure로 또는 보조 데이터 센터로 장애 조치에 대해 알아봅니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: d08715b1b3e0db4dfcf31bb4c020ab44ed3916e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791101"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>VNet의 네트워크 매핑 및 IP 주소 설정

이 문서에서는 서로 다른 Azure 지역에 있는 Azure VNet(가상 네트워크)의 두 인스턴스를 매핑하는 방법과 네트워크 간에 IP 주소를 설정하는 방법을 설명합니다. 네트워크 매핑 복제 시 원본 네트워크에 따라 대상 네트워크 선택에 대 한 기본 동작을 제공 합니다.

## <a name="prerequisites"></a>필수 조건

네트워크를 매핑하려면 원본 및 대상 Azure 지역에 [Azure VNet](../virtual-network/virtual-networks-overview.md)이 있어야 합니다. 

## <a name="set-up-network-mapping-manually-optional"></a>네트워크 매핑을 수동으로 (선택 사항) 설정

다음과 같이 네트워크를 매핑합니다.

1. **Site Recovery 인프라**에서 **+네트워크 매핑**을 클릭합니다.

    ![ 네트워크 매핑 만들기](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. **네트워크 매핑 추가**에서 원본 및 대상 위치를 선택합니다. 이 예제에서는 원본 VM이 동아시아 지역에서 실행되고 동남 아시아 지역에 복제됩니다.

    ![원본 및 대상 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. 이제 반대쪽 디렉터리에 네트워크 매핑을 만듭니다. 이 예제에서는 원본이 동남 아시아이고 대상이 동아시아입니다.

    ![네트워크 매핑 추가 창 - 대상 네트워크의 원본 및 대상 위치 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>복제를 사용하도록 설정할 때 네트워크 매핑

Azure VM에 대한 재해 복구를 구성하기 전에 네트워크 매핑을 준비하지 않은 경우 [복제를 설정하고 활성화](azure-to-azure-how-to-enable-replication.md)할 때 대상 네트워크를 지정할 수 있습니다. 이렇게 하면 다음과 같은 상황이 발생합니다.

- 선택하는 대상에 따라 Site Recovery가 자동으로 원본 지역에서 대상 지역으로 그리고 대상 지역에서 원본 지역으로 네트워크 매핑을 만듭니다.
- 기본적으로 Site Recovery는 원본 네트워크와 동일한 대상 지역에 네트워크를 만듭니다. Site Recovery는 원본 네트워크의 이름에 **-asr**을 접두사로 추가합니다. 대상 네트워크를 사용자 지정할 수 있습니다.
- 원본 네트워크에 대 한 네트워크 매핑이 이미 수행, 매핑된 대상 네트워크는 항상 기본 시 더 많은 vm 복제를 사용 하도록 설정 됩니다. 드롭다운 목록에서 사용 가능한 다른 옵션을 선택 하 여 대상 가상 네트워크를 변경할 수 있습니다. 
- 새 복제에 대 한 기본 대상 가상 네트워크를 변경 하려면 기존 네트워크 매핑을 수정 해야 합니다.
- 지역 B에는 지역 간에 네트워크 매핑을 수정, 확인 하려는 경우 먼저 삭제 네트워크 매핑을 지역 B에서에서를 지역 A로 역방향 매핑 삭제 후 지역 B에는 지역 간에 네트워크 매핑을 수정 하 고 관련 역방향 매핑을 만듭니다.

>[!NOTE]
>* 새 VM 복제에 대 한 기본값을 변경만 네트워크 매핑을 수정 합니다. 기존 복제의 대상 가상 네트워크 선택 영향을 주지 않습니다. 
>* 기존 복제에 대 한 대상 네트워크를 수정 하려는 경우 Compute 및 네트워크 설정 복제 된 항목의 이동 합니다.

## <a name="specify-a-subnet"></a>서브넷 지정

원본 VM의 서브넷 이름에 따라 대상 VM의 서브넷이 선택됩니다.

- 원본 VM 서브넷과 이름이 같은 서브넷을 대상 네트워크에서 사용할 수 있는 경우 해당 서브넷은 대상 VM에 대해 설정됩니다.
- 대상 네트워크에 동일한 이름의 서브넷이 없는 경우 사전순으로 첫 번째 서브넷이 대상 서브넷으로 설정됩니다.
- VM의 **계산 및 네트워크** 설정에서 수정할 수 있습니다.

    ![계산 및 네트워크 계산 속성 창](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>대상 VM의 IP 주소 설정

대상 가상 머신에 있는 각 NIC의 IP 주소는 다음과 같이 구성됩니다.

- **DHCP**: 원본 VM의 NIC에서 DHCP를 사용하는 경우 대상 VM의 NIC 역시 DHCP를 사용하도록 설정됩니다.
- **고정 IP 주소**: 원본 VM의 NIC에서 고정 IP 주소를 사용하는 경우 대상 VM NIC 역시 고정 IP 주소를 사용합니다.


## <a name="ip-address-assignment-during-failover"></a>장애 조치(failover) 중에 IP 주소 할당

**원본 및 대상 서브넷** | **세부 정보**
--- | ---
동일한 주소 공간 | 원본 VM NIC의 IP 주소는 대상 VM NIC IP 주소로 설정됩니다.<br/><br/> 해당 주소를 사용할 수 없는 경우 그 다음으로 사용 가능한 IP 주소가 대상으로 설정됩니다.

다른 주소 공간<br/><br/> 대상 서브넷에서 그 다음으로 사용 가능한 IP 주소가 대상 VM NIC 주소로 설정됩니다.



## <a name="ip-address-assignment-during-test-failover"></a>테스트 장애 조치(failover) 중에 IP 주소 할당

**대상 네트워크** | **세부 정보**
--- | ---
대상 네트워크는 장애 조치(failover) VNet | - 대상 IP 주소는 고정 주소이지만, 장애 조치(failover)를 위해 예약된다는 점에서 동일한 IP 주소가 아닙니다.<br/><br/>  - 서브넷 범위의 끝에서부터 사용 가능한 그 다음 IP가 주소로 할당됩니다.<br/><br/> 예를 들면 다음과 같습니다. 원본 IP 주소가 10.0.0.19이고 장애 조치(failover) 네트워크에서 10.0.0.0/24 범위를 사용하는 경우 대상 VM에 할당되는 그 다음 IP 주소는 10.0.0.254입니다.
대상 네트워크는 장애 조치(failover) VNet이 아님 | - 대상 IP 주소는 장애 조치(failover)에 예약된 동일한 IP 주소를 사용하는 고정 주소입니다.<br/><br/>  -이미 동일한 IP 주소에 할당 하는 경우 IP 주소는 그 다음 서브넷 범위 끝에 사용할 수 있습니다.<br/><br/> 예를 들면 다음과 같습니다. 원본 고정 IP 주소가 10.0.0.19이고 장애 조치(failover) 네트워크가 아니며 범위가 10.0.0.0/24인 네트워크에서 장애 조치(failover)되는 경우 대상 고정 IP 주소는 10.0.0.0.19이고(사용 가능한 경우), 그렇지 않으면 10.0.0.254입니다.

- 장애 조치(failover) VNet은 재해 복구를 설정할 때 선택한 대상 네트워크입니다.
- 테스트 장애 조치(failover)에는 항상 비 프로덕션 네트워크를 사용하는 것이 좋습니다.
- VM의 **계산 및 네트워크** 설정에서 대상 IP 주소를 수정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- Azure VM 재해 복구와 관련된 [네트워킹 가이드](site-recovery-azure-to-azure-networking-guidance.md)를 검토합니다.
- 장애 조치(failover) 후 IP 주소 유지에 대해 [자세히 알아봅니다](site-recovery-retain-ip-azure-vm-failover.md).

선택한 대상 네트워크가 장애 조치(failover) vnet이면 그 다음으로 할 일은 "선택한 대상 네트워크가 장애 조치(failover) vnet과 다르지만 서브넷 범위는 장애 조치(failover) vnet과 같은지" 확인하는 것입니다.
