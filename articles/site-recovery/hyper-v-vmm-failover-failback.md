---
title: Azure Site Recovery를 사용하여 재해 복구하는 동안 복제된 Hyper-V VM을 보조 데이터 센터로 장애 조치(Failover) 및 장애 복구(failback) | Microsoft Docs
description: Azure Site Recovery를 사용하여 재해 복구하는 동안 Hyper-V VM을 보조 온-프레미스 사이트로 장애 조치(Failover)하고, 주 사이트로 장애 복구(failback)하는 방법에 대해 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 3890283f70bfd922d0c436f76b38d7f81d7b8c6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471522"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>복제된 Hyper-V VM을 보조 온-프레미스 사이트로 장애 조치(Failover) 및 장애 복구(failback)

[Azure Site Recovery](site-recovery-overview.md) 서비스는 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 복제, 장애 조치(Failover) 및 장애 복구(failback)를 관리하고 오케스트레이션합니다.

이 문서에서는 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 Hyper-V VM을 보조 VMM 사이트로 장애 조치(Failover)하는 방법을 설명합니다. 장애 조치(failover)한 후에 온-프레미스 사이트가 사용 가능해지면 장애 복구(failback)합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 주 VMM 클라우드에서 보조 VMM 클라우드로 Hyper-V VM 장애 조치(Failover)
> * 보조 사이트에서 주 사이트로 다시 보호 및 장애 복구(failback)
> * 필요에 따라 주 사이트에서 보조 사이트로 다시 복제 시작

## <a name="failover-and-failback"></a>장애 조치 및 장애 복구

장애 조치(Failover) 및 장애 복구(failback)는 다음 3단계로 진행됩니다.

1. **보조 사이트로 장애 조치(failover)**: 주 사이트에서 보조 사이트로 머신을 장애 조치(failover)합니다.
2. **보조 사이트에서 장애 복구(failback)**: VM을 보조 사이트에서 주 사이트로 복제하고, 예정된 장애 조치(failover)를 실행하여 장애 복구(failback)합니다.
3. 예정된 장애 조치(Failover) 후에는 선택적으로 주 사이트에서 보조 사이트로 복제가 다시 시작됩니다.


## <a name="prerequisites"></a>필수 조건

- [재해 복구 드릴](hyper-v-vmm-test-failover.md)을 완료하여 모든 항목이 예상대로 작동하는지 확인합니다.
- 장애 복구(Failback)를 완료하려면 주 및 보조 VMM 서버가 Site Recovery에 연결되어 있는지 확인합니다.



## <a name="run-a-failover-from-primary-to-secondary"></a>주 사이트에서 보조 사이트로의 장애 조치(Failover) 실행

Hyper-V VM에 대해 주기적이거나 예정된 장애 조치(Failover)를 실행할 수 있습니다.

- 예기치 않은 중단에 대비해서 주기적인 장애 조치(Failover)를 사용합니다. 이 장애 조치(Failover)를 실행하면 Site Recovery에서 보조 사이트에 VM을 만들고 작동시킵니다. 동기화되지 않은 보류 중인 데이터에 따라 데이터 손실이 발생할 수 있습니다.
- 유지 관리 또는 예정된 중단에 대해서는 계획된 장애 조치(failover)를 사용할 수 있습니다. 이 옵션은 데이터 손실을 발생하지 않습니다. 계획된 장애 조치(failover)가 트리거되면 원본 VM이 종료됩니다. 동기화되지 않은 데이터가 동기화되고 장애 조치(failover)가 트리거됩니다. 
- 
  이 절차에서는 주기적 장애 조치(failover)를 실행하는 방법을 설명합니다.


1. **설정** > **복제된 항목**에서 VM > **장애 조치(Failover)** 를 클릭합니다.
1. 장애 조치를 트리거하기 전에 Site Recovery에서 원본 VM을 종료하려고 시도하는 경우 **장애 조치(Failover)를 시작하기 전에 컴퓨터를 종료합니다**를 선택합니다. 또한 Site Recovery는 장애 조치(Failover)를 트리거하기 전에 보조 사이트로 아직 전송되지 않은 온-프레미스 데이터를 동기화하려고 합니다. 종료가 실패하더라도 장애 조치(failover)는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
2. 이제 보조 VMM 클라우드의 VM을 볼 수 있습니다.
3. VM을 확인 한 후 장애 조치(Failover)를 **커밋**합니다. 그러면 사용 가능한 복구 지점이 모두 삭제됩니다.

> [!WARNING]
> **진행 중인 장애 조치(failover)는 취소하지 마세요**. 장애 조치(failover)를 시작하기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.  


## <a name="reverse-replicate-and-failover"></a>역방향 복제 및 장애 조치

보조 사이트에서 주 사이트로의 복제를 시작하고 주 사이트로 장애 복구(Failback)합니다. 주 사이트에서 VM을 다시 실행하면 보조 사이트로 복제할 수 있습니다.  

 
1. VM을 클릭하고 **역방향 복제**를 클릭합니다.
2. 작업이 완료되면 VM을 클릭하고, **장애 조치**에서 보조 VMM 클라우드의 장애 조치 방향을 확인하고, 원본 및 대상 위치를 선택합니다. 
4. 장애 조치를 시작합니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
5. 주 VMM 클라우드에서 VM를 사용할 수 있는지 확인합니다.
6. 주 VM에서 보조 사이트로 다시 복제하기 시작하려면 **역방향 복제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
Hyper-V VM을 보조 사이트에 복제하는 [단계를 검토](hyper-v-vmm-disaster-recovery.md)합니다.
