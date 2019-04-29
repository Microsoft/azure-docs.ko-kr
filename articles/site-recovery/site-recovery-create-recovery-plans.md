---
title: Azure Site Recovery를 사용한 재해 복구를 위한 복구 계획 만들기 및 사용자 지정 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용한 재해 복구를 위한 복구 계획을 만들고 사용자 지정하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: d52aa3b39a17c42c0f0e0cb669c69d336b41ba48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035825"
---
# <a name="create-and-customize-recovery-plans"></a>복구 계획 만들기 및 사용자 지정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)에서 복구 계획을 만들고 사용자 지정하는 방법을 설명합니다. 시작하기 전에 복구 계획에 대해 [자세히 알아봅니다](recovery-plan-overview.md).

## <a name="create-a-recovery-plan"></a>복구 계획 만들기

1. Recovery Services 자격 증명 모음에서 **복구 계획(Site Recovery)** > **+복구 계획**을 선택합니다.
2. **복구 계획 만들기**에서 계획의 이름을 지정합니다.
3. 계획의 머신을 기반으로 하여 원본 및 대상을 선택하고 배포 모델에 대한 **리소스 관리자**를 선택합니다. 원본 위치에는 장애 조치(failover) 및 복구를 사용하도록 설정한 머신이 있어야 합니다. 

   **장애 조치(Failover)** | **원본** | **대상** 
   --- | --- | ---
   Azure 간 | Azure 지역 |Azure 지역
   VMware에서 Azure로 | 구성 서버 | Azure
   물리적 컴퓨터에서 Azure로 | 구성 서버 | Azure   
   VMM에서 관리하는 Hyper-V에서 Azure로  | VMM 표시 이름 | Azure
   VMM을 사용하지 않는 Hyper-V에서 Azure로 | Hyper-v 사이트 이름 | Azure
   VMM에서 VMM으로 |VMM 식별 이름 | VMM 표시 이름 

   > [!NOTE]
   > 복구 계획에는 원본 및 대상이 동일한 머신이 포함될 수 있습니다. VMM에서 관리하는 VMware 및 Hyper-V VM을 동일한 계획에 포함할 수 없습니다. VMware VM 및 물리적 서버는 원본이 구성 서버인 동일한 계획에 포함할 수 있습니다.

2. **항목 가상 머신 선택**에서 계획에 추가할 머신(또는 복제 그룹)을 선택합니다. 그런 후 **OK**를 클릭합니다.
    - 머신이 계획의 기본 그룹(그룹 1)에 추가됩니다. 장애 조치(failover) 후 이 그룹의 모든 머신은 동시에 시작됩니다.
    - 지정된 원본 및 대상 위치에 있는 머신만 선택할 수 있습니다. 
1. **확인**을 클릭하여 계획을 만듭니다.

## <a name="add-a-group-to-a-plan"></a>계획에 그룹 추가

추가 그룹을 만들고 여러 그룹에 머신을 추가하여 그룹 단위로 다른 동작을 지정할 수 있습니다. 예를 들어 장애 조치(failover) 후 그룹의 머신이 시작되어야 하는 시기를 지정하거나 그룹별로 사용자 지정 작업을 설정할 수 있습니다.

1. **복구 계획**에서 계획을 마우스 오른쪽 단추로 클릭하고 **사용자 지정**을 선택합니다. 기본적으로 계획을 만든 후 계획에 추가한 모든 머신은 기본 그룹 1에 있습니다.
2. **+그룹**을 클릭합니다. 기본적으로 추가된 순서대로 새 그룹에 번호가 지정됩니다. 최대 7개의 그룹을 사용할 수 있습니다.
3. 새 그룹으로 이동할 머신을 선택하고 **그룹 변경**을 클릭한 다음, 새 그룹을 선택합니다. 또는 그룹 이름을 마우스 오른쪽 단추로 클릭하고 **보호된 항목**을 선택한 다음, 그룹에 머신을 추가합니다. 머신 또는 복제 그룹은 복구 계획의 한 그룹에만 속할 수 있습니다.


## <a name="add-a-script-or-manual-action"></a>스크립트 또는 수동 작업 추가

스크립트 또는 수동 작업을 추가하여 복구 계획을 사용자 지정할 수 있습니다. 다음 사항에 유의하세요.

- Azure에 복제하는 경우, Azure Automation Runbook을 복구 계획에 통합할 수 있습니다. [자세히 알아보기](site-recovery-runbook-automation.md).
- System Center VMM에서 관리하는 Hyper-V VM을 복제하는 경우, 온-프레미스 VMM 서버에서 스크립트를 만들고 복구 계획에 포함할 수 있습니다.
- 스크립트를 추가하면 해당 그룹에 대해 새로운 작업 집합이 추가됩니다. 예를 들어, 그룹 1에 대한 사전 단계 집합이 *Group 1: pre-steps*라는 이름으로 생성됩니다. 모든 사전 단계가 이 집합 내에 나열됩니다. VMM 서버가 배포된 경우에만 주 사이트에 스크립트를 추가할 수 있습니다.
- 수동 작업을 추가하는 경우, 복구 계획을 실행할 때 수동 작업을 삽입한 지점에서 중지됩니다. 대화 상자는 수동 작업이 완료되도록 지정하라는 메시지를 표시합니다.
- VMM 서버에서 스크립트를 만들려면 [이 문서](hyper-v-vmm-recovery-script.md)의 지침을 따르세요.
- 보조 사이트로 장애 조치(failover) 중 및 보조 사이트에서 기본 사이트로 장애 복구(failback) 중 스크립트를 적용할 수 있습니다. 지원은 복제 시나리오에 따라 다릅니다.
    
    **시나리오** | **장애 조치(Failover)** | **장애 복구**
    --- | --- | --- 
    Azure 간  | Runbook | Runbook
    VMware에서 Azure로 | Runbook | 해당 없음 
    VMM을 사용하는 Hyper-V에서 Azure로 | Runbook | 스크립트
    Azure에 Hyper-V 사이트 | Runbook | 해당 없음
    VMM에서 보조 VMM으로 | 스크립트 | 스크립트

1. 복구 계획에서 작업을 추가해야 하는 단계를 클릭하고 작업이 발생해야 하는 시기를 지정합니다. 가. 장애 조치(failover) 후 그룹의 머신이 시작되기 전에 작업을 수행하려면 **사전 작업 추가**를 선택합니다.
    나. 장애 조치(failover) 후 그룹의 머신이 시작된 후 작업을 수행하려면 **사후 작업 추가**를 선택합니다. 작업 위치를 이동하려면 **위로 이동** 및 **아래로 이동** 단추를 선택합니다.
2. **작업 삽입**에서 **스크립트** 또는 **수동 작업**을 선택합니다.
3. 수동 작업을 추가하려면 다음을 수행합니다. 가. 작업 이름과 작업 지침을 입력합니다. 장애 조치(failover)를 실행하는 사용자에게 이러한 지침이 표시됩니다.
    나. 모든 장애 조치(failover) 유형(테스트, 장애 조치(failover), 계획된 장애 조치(failover)(관련된 경우))에 대해 수동 작업을 추가할지 여부를 지정합니다. 그런 후 **OK**를 클릭합니다.
4. 스크립트를 추가하려면 다음을 수행합니다. 가. VMM 스크립트를 추가하는 경우 **VMM 스크립트에 대한 장애 조치**를 선택하고 **스크립트 경로** 유형에서 공유에 대한 상대 경로를 입력합니다. 예를 들어 공유가 \\<VMMServerName>\MSSCVMMLibrary\RPScripts에 있는 경우 경로를 다음과 같이 지정합니다. \RPScripts\RPScript.PS1.
    나. Azure Automation Runbook을 추가하는 경우 Runbook이 위치한 **Azure Automation 계정**을 지정하고 적절한 **Azure Runbook 스크립트**를 선택합니다.
5. 복구 계획의 테스트 장애 조치(failover)를 실행하여 스크립트가 예상대로 작동하는지 확인합니다.

## <a name="watch-a-video"></a>비디오 보기

복구 계획을 작성하는 방법을 보여 주는 비디오를 시청합니다.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>다음 단계

[장애 조치(failover) 실행](site-recovery-failover.md)에 대해 자세히 알아보세요.  

    
