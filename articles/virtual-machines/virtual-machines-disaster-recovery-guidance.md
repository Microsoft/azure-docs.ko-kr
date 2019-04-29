---
title: Azure VM용 재해 복구 시나리오 | Microsoft Docs
description: Azure 가상 머신에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업을 알아본다.
services: virtual-machines
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dc71e8564b35f4fdd4153a04c66a3d8c5df88c30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478847"
---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Azure VM에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업
Microsoft에서는 서비스가 필요할 때 서비스를 항상 사용할 수 있도록 하기 위해 많은 노력을 기울입니다. 다만 경우에 따라 계획되지 않은 서비스 중단이 발생하여 강제적으로 제어 영향을 벗어날 때가 있습니다.

Microsoft는 작동 시간 및 연결에 대한 약정으로 해당 서비스에 대한 서비스 수준 약정(SLA)을 제공합니다. 개별 Azure 서비스에 대한 SLA는 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)에서 찾을 수 있습니다.

Azure에는 항상 사용 가능한 애플리케이션을 지원하는 많은 기본 제공 플랫폼 기능이 있습니다. 이러한 서비스에 대한 자세한 내용은 [Azure 애플리케이션에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

이 문서에서는 주요 자연 재해 또는 광범위한 서비스 중단으로 인해 전체 지역에 중단이 발생할 때의 실제 재해 복구 시나리오를 다룹니다. 이러한 경우는 드물게 발생하지만 전체 지역의 중단이 발생될 가능성에 대해 준비해야 합니다. 전체 지역에 서비스 중단이 발생하는 경우 데이터의 로컬 중복 복사본을 일시적으로 사용할 수 없게 됩니다. 지역에서 복제를 사용하는 경우 Azure Storage Blob 및 테이블의 추가 사본 3개는 다른 지역에 저장됩니다. 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해의 경우 Azure는 지역 복제된 지역에 모든 DNS 항목을 다시 매핑합니다.

이러한 드문 경우를 처리할 수 있도록 Azure 가상 머신 애플리케이션이 배포되는 전체 영역의 서비스가 중단될 경우에 대비해서 Azure 가상 머신에 대한 다음 지침을 제공합니다.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>옵션 1: Azure Site Recovery를 사용 하 여 장애 조치 시작
비교적 짧은 시간에 한 번의 클릭으로 애플리케이션을 복구할 수 있도록 VM에 Azure Site Recovery를 구성할 수 있습니다. 사용자가 선택한 Azure 지역에 복제할 수 있고 쌍을 이루는 지역으로 제한되지 않습니다. [가상 머신을 복제](https://aka.ms/a2a-getting-started)하여 시작할 수 있습니다. [복구 계획을 만들어](../site-recovery/site-recovery-create-recovery-plans.md) 애플리케이션에 대한 전체 장애 조치(failover) 프로세스를 자동화할 수 있습니다. 프로덕션 애플리케이션 또는 진행 중인 복제에 영향을 주지 않고 미리 [장애 조치(failover)를 테스트](../site-recovery/site-recovery-test-failover-to-azure.md)할 수 있습니다. 주 지역에 중단이 발생한 경우 [장애 조치(failover)를 시작](../site-recovery/site-recovery-failover.md)하고 대상 지역에 애플리케이션을 가져옵니다.


## <a name="option-2-wait-for-recovery"></a>옵션 2: 복구 대기
이 경우에 사용자의 조치가 필요하지 않습니다. 서비스 가용성을 복원하기 위해 열심히 노력 중입니다. 서비스의 현재 상태를 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 확인할 수 있습니다.

이 옵션은 가동 중단 이전에 Azure Site Recovery, 읽기 액세스 지역 중복 저장소 또는 지역 중복 저장소를 설치하지 않은 경우 최선의 옵션입니다. VM VHD(가상 하드 드라이브)가 저장되는 저장소 계정으로 지역 중복 저장소 또는 읽기 액세스 지역 중복 저장소를 설치한 경우 기본 이미지 VHD 복구를 볼 수 있으며 새 VM 프로비전을 시도합니다. 이 옵션은 데이터 동기화를 보장할 수 없기 때문에 권장되는 옵션이 아닙니다. 따라서, 이 옵션은 작동한다는 보장이 없습니다.


> [!NOTE]
> 이 프로세스는 사용자가 제어할 수 없으며 지역 전체 서비스 중단에 대해서만 발생합니다. 이 때문에 가장 높은 수준의 가용성을 달성하기 위해 다른 애플리케이션별 백업 전략에 의존해야 합니다. 자세한 내용은 [재해 복구를 위한 데이터 전략](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery)의 섹션을 참조하세요.
>
>

## <a name="next-steps"></a>다음 단계

- Azure Site Recovery를 사용하여 [Azure 가상 머신에서 실행 중인 애플리케이션 보호](https://aka.ms/a2a-getting-started)를 시작합니다.

- 재해 복구 및 고가용성 전략을 구현하는 방법에 관해 자세히 알아보려면 [Azure 애플리케이션에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

- 클라우드 플랫폼의 기능에 대한 자세한 기술적 이해를 높이려면 [Azure 복원력 기술 지침](../resiliency/resiliency-technical-guidance.md)을 참조하세요.


- 지침이 명확하지 않거나 사용자를 대신해 Microsoft가 작업을 수행하도록 하고 싶은 경우 [고객 지원팀](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.
