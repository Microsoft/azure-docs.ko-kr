---
title: Azure Site Recovery 서비스를 사용하여 재해 복구를 위해 보조 Azure 지역에 복제된 Azure VM에 대한 장애 조치(failover) 및 다시 보호를 수행합니다.
description: Azure Site Recovery 서비스를 사용하여 재해 복구를 위해 보조 Azure 지역에 복제된 Azure VM에 대한 장애 조치(failover) 및 다시 보호를 수행하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 836b6bb76ff608fed0f34e40fe450d593fec540a
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514162"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>지역 간 Azure VM의 장애 조치(failover) 및 다시 보호

이 자습서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 보조 Azure 지역으로 Azure VM(가상 머신)을 장애 조치(failover)하는 방법을 설명합니다. 장애 조치한 후 VM을 다시 보호합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure VM 장애 조치(failover)
> * 주 지역에 복제되도록 보조 Azure VM을 다시 보호합니다.

> [!NOTE]
> 이 자습서에는 기본 설정과 최소 사용자 지정을 포함하는 가장 간단한 경로가 포함되어 있습니다. 더 복잡한 시나리오의 경우 Azure VM에 대한 ‘방법’에 있는 문서를 사용합니다.


## <a name="prerequisites"></a>필수 조건

- 시작하기 전에 장애 조치(failover)에 대한 [질문과 대답](site-recovery-faq.md#failover)을 검토합니다.
- [재해 복구 ](azure-to-azure-tutorial-dr-drill.md)을 완료하여 모든 항목이 예상대로 작동하는지 확인합니다.
- 테스트 장애 조치(failover)를 실행하기 전에 VM 속성을 확인합니다. VM은 [Azure 요구 사항](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)을 준수해야 합니다.

## <a name="run-a-failover-to-the-secondary-region"></a>보조 지역으로 장애 조치(failover) 실행

1. **복제된 항목**에서 장애 조치(failover)할 VM > **장애 조치(Failover)** 를 선택합니다.

   ![장애 조치(failover)](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. **장애 조치(Failover)** 에서 장애 조치(failover)할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.

   * **최신**(기본값): Site Recovery 서비스의 모든 데이터를 처리하고 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
   * **가장 최근에 처리됨**: Site Recovery 서비스에서 처리한 최근 복구 지점으로 가상 머신을 되돌립니다.
   * **사용자 지정**: 특정 복구 지점으로 장애 조치(failover)합니다. 이 옵션은 테스트 장애 조치(failover)를 수행하는 데 유용합니다.

3. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 VM을 종료하려고 시도하는 경우 **장애 조치(Failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 종료하여 데이터 손실을 방지할 수 있습니다. 종료가 실패하더라도 장애 조치는 계속됩니다. Site Recovery는 장애 조치(failover) 후 원본을 정리하지 않습니다.

4. **작업** 페이지에서 장애 조치(failover) 진행 상황을 확인합니다.

5. 장애 조치 후에 가상 컴퓨터에 로그인하여 해당 가상 컴퓨터의 유효성을 검사합니다. 가상 머신에 대한 다른 복구 지점으로 이동하려면 **복구 지점 변경** 옵션을 사용할 수 있습니다.

6. 장애 조치된 가상 머신에 만족하면 장애 조치를 **커밋**할 수 있습니다.
   커밋하면 서비스와 함께 사용할 수 있는 모든 복구 지점이 삭제됩니다. 이제 복구 지점을 변경할 수 없습니다.

> [!NOTE]
> VM에 대한 복제를 설정한 후 추가할 VM을 장애 조치(failover)할 때 복제 지점은 복구에 사용할 수 있는 디스크를 표시합니다. 예를 들어, VM에 단일 디스크가 있고 새로운 디스크를 추가하는 경우, 디스크를 추가하기 전에 생성한 복제 지점은 “2개 디스크 중 하나”로 구성된 복제 지점을 표시합니다.

![추가된 디스크를 사용하여 장애 조치(failover)](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>보조 VM 다시 보호

VM의 장애 조치(failover) 후 주 지역에 다시 복제되도록 다시 보호해야 합니다.

1. VM이 **장애 조치(Failover) 커밋** 상태이고 주 지역을 사용할 수 있는지 확인하면 새 리소스를 만들고 액세스할 수 있습니다.
2. **자격 증명 모음** > **복제된 항목**에서 장애 조치(failover)된 VM을 마우스 오른쪽 단추로 클릭한 다음 **다시 보호**를 선택합니다.

   ![마우스 오른쪽 단추를 클릭하여 다시 보호](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. 보조 지역에서 주 지역으로의 보호 방향이 이미 선택되어 있는지 확인합니다.
3. **리소스 그룹, 네트워크, 저장소 및 가용성 집합** 정보를 검토합니다. 신규로 표시된 리소스가 다시 보호 작업의 일부로 생성됩니다.
4. **확인**을 클릭하여 다시 보호 작업을 트리거합니다. 이 작업은 최신 데이터로 대상 사이트를 시드합니다. 그런 다음 델타를 주 지역에 복제합니다. 이제 VM이 보호된 상태입니다.

## <a name="next-steps"></a>다음 단계
- 다시 보호를 수행한 후, 가능한 경우 주 지역으로 장애 복구(failback)하는 [방법을 알아봅니다](azure-to-azure-tutorial-failback.md).
- 다시 보호 흐름에 대해 [자세히 알아봅니다](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).
