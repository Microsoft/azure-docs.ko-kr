---
title: 온-프레미스와 Azure 간 재해 복구를 위한 Azure Site Recovery의 네트워크 인터페이스 관리 | Microsoft Docs
description: Azure Site Recovery를 사용한 온-프레미스와 Azure 간 재해 복구를 위해 네트워크 인터페이스를 관리하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 5d5dd7bc3f6b60c2f9d7c2179f2bd356ca101dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471777"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>온-프레미스와 Azure 간 재해 복구를 위한 가상 머신 네트워크 인터페이스 관리
Azure의 VM(가상 머신)에는 하나 이상의 네트워크 인터페이스가 연결되어 있어야 합니다. 네트워크 인터페이스 수는 VM 크기에서 지원되는 수만큼 연결할 수 있습니다.

기본적으로 Azure 가상 머신에 연결된 첫 번째 네트워크 인터페이스는 주 네트워크 인터페이스로 정의됩니다. 가상 머신의 다른 모든 네트워크 인터페이스는 보조 네트워크 인터페이스입니다. 또한 기본적으로 가상 머신의 모든 아웃바운드 트래픽은 주 네트워크 인터페이스의 기본 IP 구성에 할당된 IP 주소로 보내집니다.

온-프레미스 환경에서 가상 머신이나 서버에는 환경 내 여러 네트워크에 대해 서로 다른 네트워크 인터페이스가 있을 수 있습니다. 서로 다른 네트워크는 일반적으로 업그레이드, 유지 관리 및 인터넷 액세스와 같은 특정 작업을 수행하는 데 사용됩니다. 온-프레미스 환경에서 Azure로 마이그레이션하거나 장애 조치(failover)하는 경우, 동일한 가상 머신의 네트워크 인터페이스는 모두 동일한 가상 네트워크에 연결되어야 합니다.

기본적으로 Azure Site Recovery는 네트워크 인터페이스를 온-프레미스 서버에 연결된 수만큼 Azure 가상 머신에 만듭니다. 복제된 가상 머신의 설정에서 네트워크 인터페이스 설정을 편집하여 마이그레이션 또는 장애 조치(failover) 중 중복 네트워크 인터페이스가 생성되는 것을 방지할 수 있습니다.

## <a name="select-the-target-network"></a>대상 네트워크 선택

VMware와 물리적 컴퓨터 및 Hyper-V(System Center Virtual Machine Manager 없음) 가상 머신의 경우 개별 가상 머신에 대해 대상 가상 네트워크를 지정할 수 있습니다. Virtual Machine Manager로 관리되는 Hyper-V 가상 머신의 경우 [네트워크 매핑](site-recovery-network-mapping.md)을 사용하여 원본 Virtual Machine Manager 서버와 대상 Azure 네트워크에서 VM 네트워크를 매핑합니다.

1. Recovery Services 자격 증명 모음의 **복제된 항목**에서 복제된 항목을 선택하여 복제된 항목의 설정에 액세스합니다.

2. **계산 및 네트워크** 탭을 선택하여 복제된 항목의 네트워크 설정에 액세스합니다.

3. **네트워크 속성** 아래 사용 가능한 네트워크 인터페이스 목록에서 가상 네트워크를 선택합니다.

    ![네트워크 설정](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

대상 네트워크를 수정하면 해당하는 특정 가상 머신의 모든 네트워크 인터페이스에 영향을 줍니다.

Virtual Machine Manager 클라우드의 경우 네트워크 매핑을 수정하면 모든 가상 머신과 해당 네트워크 인터페이스에 영향을 줍니다.

## <a name="select-the-target-interface-type"></a>대상 인터페이스 유형 선택

**계산 및 네트워크** 창의 **네트워크 인터페이스** 섹션에서 네트워크 인터페이스 설정을 볼 수 있고 편집할 수 있습니다. 대상 네트워크 인터페이스 유형을 지정할 수도 있습니다.

- 장애 조치(failover)에는 **주** 네트워크 인터페이스가 필요합니다.
- 선택된 다른 모든 네트워크 인터페이스는 **보조** 네트워크 인터페이스입니다.
- 장애 조치(failover) 시 네트워크 인터페이스를 생성에서 제외하려면 **사용 안 함**을 선택합니다.

기본적으로 복제를 사용하도록 설정하면 Site Recovery는 온-프레미스 서버에서 감지된 모든 네트워크 인터페이스를 선택합니다. 하나는 **주**로 표시하고 다른 모든 항목은 **보조**로 표시합니다. 온-프레미스 서버에 추가된 모든 후속 인터페이스는 기본적으로 **사용 안 함**으로 표시됩니다. 네트워크 인터페이스를 더 추가하는 경우, 필요한 네트워크 인터페이스를 모두 수용할 수 있도록 올바른 Azure 가상 머신 대상 크기를 선택해야 합니다.

## <a name="modify-network-interface-settings"></a>네트워크 인터페이스 설정 수정

복제된 항목의 네트워크 인터페이스에 대한 서브넷 및 IP 주소를 수정할 수 있습니다. IP 주소가 지정되지 않으면 Site Recovery는 장애 조치(failover) 시 서브넷에서 다음으로 사용할 수 있는 IP 주소를 네트워크 인터페이스에 할당합니다.

1. 사용 가능한 네트워크 인터페이스를 선택하여 네트워크 인터페이스 설정을 엽니다.

2. 사용 가능한 서브넷 목록에서 원하는 서브넷을 선택합니다.

3. 원하는 IP 주소를 입력합니다(필요에 맞게).

    ![네트워크 인터페이스 설정](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. **확인**을 선택하여 편집을 끝내고 **계산 및 네트워크** 창으로 돌아갑니다.

5. 다른 네트워크 인터페이스에 대해 1~4단계를 반복합니다.

6. **저장**을 선택하여 변경 내용을 모두 저장합니다.

## <a name="next-steps"></a>다음 단계
  Azure 가상 머신의 네트워크 인터페이스에 대해 [자세히 알아봅니다](../virtual-network/virtual-network-network-interface-vm.md).
