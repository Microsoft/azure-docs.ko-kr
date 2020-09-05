---
title: Azure Site Recovery에서 Hyper-V VM의 Azure에 대한 장애 조치(failover) 설정
description: Azure Site Recovery를 사용하여 Hyper-V VM을 Azure로 장애 조치(failover)하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86132459"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Hyper-V VM을 Azure로 장애 조치(failover)

이 자습서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Hyper-V VM을 Azure로 장애 조치(failover)하는 방법을 설명합니다. 장애 조치(failover)한 후에 온-프레미스 사이트가 사용 가능해지면 장애 복구(failback)합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Hyper-V VM 속성을 확인하여 Azure 요구 사항을 준수하는지 확인합니다.
> * 특정 VM을 Azure로 장애 조치(failover)합니다.


이 자습서는 시리즈의 다섯 번째 자습서입니다. 여기에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.    

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 Hyper-V 준비](./hyper-v-prepare-on-premises-tutorial.md)
3. [Hyper-V VM](./hyper-v-azure-tutorial.md) 또는 [System Center VMM 클라우드에서 관리되는 Hyper-V VM](./hyper-v-vmm-azure-tutorial.md)용 재해 복구 설정
4. [재해 복구 훈련 실행](tutorial-dr-drill-azure.md)

다양한 유형의 장애 조치(failover)에 [대해 알아봅니다](failover-failback-overview.md#types-of-failover). 복구 계획에서 여러 VM을 장애 조치(failover)하려는 경우 [이 문서](site-recovery-failover.md)를 검토하세요.

## <a name="prepare-for-failover"></a>장애 조치(Failover) 준비 
VM에서 스냅샷이 없으며 장애 복구(failback) 시 온-프레미스 VM이 꺼져 있는지 확인해야 합니다. 이렇게 하면 복제 중 데이터 일관성을 보장할 수 있습니다. 장애 복구(failback) 시 온-프레미스 VM을 켜지 마십시오. 

장애 조치(failover)및 장애 복구(failback)는 다음 3단계로 진행됩니다.

1. **Azure에 장애 조치(failover)** : Hyper-V VM을 온-프레미스에서 Azure로 장애 조치(failover)합니다.
2. **온-프레미스로 장애 복구(failback)** : 온-프레미스 사이트를 사용할 수 있는 경우, Azure VM을 온-프레미스 사이트로 장애 조치(failover)합니다. Azure에서 온-프레미스로 데이터 동기화를 시작하고, 완료되면 온-프레미스에서 VM을 실행합니다.  
3. **온-프레미스 VM에 역방향 복제**: 온-프레미스로 장애 복구(failback)된 후에 온-프레미스 VM을 역방향 복제하여 Azure로 복제를 시작합니다.

## <a name="verify-vm-properties"></a>VM 속성 확인

장애 조치(failover) 전에 VM 속성을 확인하고 VM이 [Azure 요구 사항](hyper-v-azure-support-matrix.md#replicated-vms)을 충족하는지 확인합니다.

**보호된 항목**에서 **복제된 항목** > VM을 클릭합니다.

1. **복제된 항목** 창에 VM 정보, 상태 및 최신 사용 가능한 복구 지점의 요약이 제공됩니다. 자세한 내용을 보려면 **속성**을 클릭합니다.

1. **컴퓨팅 및 네트워크**에서 Azure 이름, 리소스 그룹, 대상 크기, [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md) 및 관리 디스크 설정을 수정할 수 있습니다.

1. 장애 조치(failover) 후 Azure VM이 배치될 네트워크/서브넷 및 할당되는 IP 주소를 포함한 네트워크 설정을 보고 수정할 수 있습니다.

1. **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.

## <a name="fail-over-to-azure"></a>Azure로 장애 조치(failover)

1. **설정** > **복제된 항목**에서 VM > **장애 조치(failover)** 를 클릭합니다.
2. **장애 조치(failover)** 에서 **최신** 복구 지점을 선택합니다. 
3. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM 종료를 시도합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
4. 장애 조치(failover)를 확인하고 **커밋**을 클릭합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> **진행 중인 장애 조치(failover)를 취소하지 마십시오.** : 진행 중에 취소하면 장애 조치(failover)는 중지되지만 VM이 다시 복제되지 않습니다.

## <a name="connect-to-failed-over-vm"></a>장애 조치(failover)된 VM에 연결

1. 장애 조치(failover) 후에 RDP(원격 데스크톱 프로토콜) 및 SSH(보안 셸)를 사용하여 Azure VM에 연결하려는 경우 [요구 사항이 충족되었는지 확인](failover-failback-overview.md#connect-to-azure-after-failover)합니다.
2. 장애 조치(failover) 후 VM으로 이동하고 [연결](../virtual-machines/windows/connect-logon.md)하여 유효성을 검사합니다.
3. 장애 조치(failover) 후 다른 복구 지점을 사용하려면 **복구 지점 변경**을 사용합니다. 다음 단계에서 장애 조치(failover)를 커밋하면 이 옵션을 더 이상 사용할 수 없습니다.
4. 유효성 검사 후 **커밋**을 선택하여 장애 조치(failover) 후 VM의 복구 지점을 마무리합니다.
5. 커밋 후 다른 사용 가능한 복구 지점을 모두 삭제합니다. 이 단계를 수행하면 장애 조치(failover)가 완료됩니다.

>[!TIP]
> 장애 조치(failover) 후 연결 문제가 발생하는 경우 [문제 해결 가이드](site-recovery-failover-to-azure-troubleshoot.md)를 따릅니다.


## <a name="next-steps"></a>다음 단계

장애 조치(failover) 후 Azure에서 온-프레미스로 복제되도록 Azure VM을 다시 보호합니다. 그러면 VM이 다시 보호되고 온-프레미스 사이트로 복제된 후 준비가 되었을 때 Azure에서 장애 복구(failback)가 수행됩니다.
