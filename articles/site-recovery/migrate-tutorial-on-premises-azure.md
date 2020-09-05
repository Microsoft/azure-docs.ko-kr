---
title: Azure Migrate를 사용하여 온-프레미스 머신 마이그레이션
description: 이 문서에서는 온-프레미스 머신을 Azure로 마이그레이션하는 방법을 요약하고 Azure Migrate를 권장합니다.
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847315"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Azure로 온-프레미스 컴퓨터 마이그레이션

이 문서에서는 온-프레미스 머신을 Azure로 마이그레이션하기 위한 옵션을 설명합니다. 

## <a name="migrate-with-azure-migrate"></a>Azure Migrate로 마이그레이션

[Azure Migrate](../migrate/migrate-services-overview.md) 서비스를 사용하여 머신을 Azure로 마이그레이션하는 것이 좋습니다. Azure Migrate는 서버 마이그레이션을 위해 특별히 빌드되었습니다. Azure Migrate는 온-프레미스 머신을 Azure로 검색, 평가 및 마이그레이션하기 위한 중앙 허브를 제공합니다.

다음 링크를 따라 Azure Migrate로 마이그레이션합니다.

- VMware VM에 대한 마이그레이션 옵션에 대해 [알아](../migrate/server-migrate-overview.md)보고 [에이전트 없는](../migrate/tutorial-migrate-vmware.md) 또는 [에이전트 기반](../migrate/tutorial-migrate-vmware-agent.md) 마이그레이션을 사용하여 VM을 Azure로 마이그레이션합니다.
- Azure로 [Hyper-V VM을 마이그레이션](../migrate/tutorial-migrate-hyper-v.md)합니다.
- [AWS 인스턴스](../migrate/tutorial-migrate-aws-virtual-machines.md)를 포함하여 [물리적 서버 또는 다른 VM](../migrate/tutorial-migrate-physical-virtual-machines.md)을 Azure로 마이그레이션합니다.

## <a name="migrate-with-site-recovery"></a>Site Recovery로 마이그레이션
Site Recovery는 마이그레이션이 아닌 재해 복구에만 사용해야 합니다.

이미 Azure Site Recovery를 사용하고 있고 마이그레이션에 계속 사용하려는 경우 재해 복구에 사용하는 것과 동일한 단계를 수행합니다.

- VMware VM: [Azure 준비](tutorial-prepare-azure.md) 및 [VMware](vmware-azure-tutorial-prepare-on-premises.md), [복제 머신](vmware-azure-tutorial.md) 시작, 모든 작업이 작동하는지 [확인](tutorial-dr-drill-azure.md)하고 [장애 조치(failover)를 실행](vmware-azure-tutorial-failover-failback.md)합니다.
- Hyper-V VM: [Azure 준비](tutorial-prepare-azure-for-hyperv.md) 및 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md), [복제 머신](hyper-v-azure-tutorial.md) 시작, 모든 작업이 작동하는지 [확인](tutorial-dr-drill-azure.md)하고 [장애 조치(failover)를 실행](hyper-v-azure-failover-failback-tutorial.md)합니다.
- 실제 서버: [연습에 따라](physical-azure-disaster-recovery.md) Azure를 준비하고, 재해 복구를 위한 머신을 준비하고, 복제를 설정합니다.

> [!NOTE]
> 재해 복구를 위해 장애 조치(failover)를 실행하는 경우 마지막 단계로 장애 조치(failover)를 커밋합니다. 온-프레미스 머신을 마이그레이션하는 경우 **커밋** 옵션은 관련이 없습니다. 대신 **마이그레이션 완료** 옵션을 선택합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> Azure Migrate에 대한 [일반적인 질문을 검토](../migrate/resources-faq.md)합니다.

  
