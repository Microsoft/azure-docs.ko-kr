---
title: Azure Cloud Services에 영향을 주는 Azure 서비스 중단 처리
titleSuffix: Azure Cloud Services
description: Azure Cloud Services에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업에 대해 알아봅니다.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.workload: cloud-services
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: e3f0fd88eb302dac208f43d0622ae28b31dcddc2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157510"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Azure Cloud Services에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업
Microsoft에서는 서비스가 필요할 때 서비스를 항상 사용할 수 있도록 하기 위해 많은 노력을 기울입니다. 다만 경우에 따라 계획되지 않은 서비스 중단이 발생하여 강제적으로 제어 영향을 벗어날 때가 있습니다.

Microsoft는 작동 시간 및 연결에 대한 약정으로 해당 서비스에 대한 서비스 수준 약정(SLA)을 제공합니다. 개별 Azure 서비스에 대한 SLA는 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)에서 찾을 수 있습니다.

Azure에는 항상 사용 가능한 애플리케이션을 지원하는 많은 기본 제공 플랫폼 기능이 있습니다. 이러한 서비스에 대한 자세한 내용은 [Azure 애플리케이션에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

이 문서에서는 주요 자연 재해 또는 광범위한 서비스 중단으로 인해 전체 지역에 중단이 발생할 때의 실제 재해 복구 시나리오를 다룹니다. 이러한 경우는 드물게 발생하지만 전체 지역의 중단이 발생될 가능성에 대해 준비해야 합니다. 전체 지역에 서비스 중단이 발생하는 경우 데이터의 로컬 중복 복사본을 일시적으로 사용할 수 없게 됩니다. 지역에서 복제를 사용하는 경우 Azure Storage Blob 및 테이블의 추가 사본 3개는 다른 지역에 저장됩니다. 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해의 경우 Azure는 지역 복제된 지역에 모든 DNS 항목을 다시 매핑합니다.

> [!NOTE]
> 이 프로세스는 사용자가 제어할 수 없으며 데이터 센터 전체 서비스 중단에 대해서만 발생합니다. 이 때문에 가장 높은 수준의 가용성을 달성하기 위해 다른 애플리케이션별 백업 전략에 의존해야 합니다. 자세한 내용은 [Microsoft Azure에서 빌드된 애플리케이션에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요. 자체 장애 조치(failover)에 영향을 줄 수 있으려면 다른 지역에 데이터의 읽기 전용 복사본을 만드는 [RA-GRS(읽기 액세스 지역 중복 스토리지)](../storage/common/storage-redundancy.md)를 참조하세요.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>옵션 1: Azure Traffic Manager를 통해 백업 배포 사용
가장 강력한 재해 복구 솔루션은 여러 다른 하위 지역에 애플리케이션의 여러 배포를 유지한 다음, [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 사용하여 배포 간에 트래픽을 전송하는 작업과 관련됩니다. Azure Traffic Manager는 여러 [라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)을 제공하므로 기본/백업 모델을 사용하여 배포를 관리할지 또는 두 모델 간에 트래픽을 분할할지를 선택할 수 있습니다.

![Azure Traffic Manager를 사용하여 지역 간에 Azure Cloud Services 분산](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

하위 지역의 손실에 가장 빠르게 대처하기 위해서는 Traffic Manager의 [엔드포인트 모니터링](../traffic-manager/traffic-manager-monitoring.md)을 구성하는 것이 중요합니다.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>옵션 2: 새 하위 지역에 애플리케이션 배포
이전 옵션에 설명된 대로 여러 활성 배포를 유지 관리하면 추가적인 비용이 지속적으로 발생합니다. RTO(복구 시간 목표)가 충분히 유연하고 원래 코드 또는 컴파일된 Cloud Services 패키지가 있는 경우에는 다른 하위 지역에 애플리케이션의 새 인스턴스를 만든 후 새 배포를 가리키도록 DNS 레코드를 업데이트할 수 있습니다.

클라우드 서비스 애플리케이션을 만들고 배포하는 방법에 대한 자세한 내용은 [클라우드 서비스를 만들고 배포하는 방법](cloud-services-how-to-create-deploy-portal.md)을 참조하세요.

애플리케이션 데이터 원본에 따라 애플리케이션 데이터 원본에 대한 복구 절차를 확인해야 할 수 있습니다.

* Azure Storage 데이터 원본의 경우 응용 프로그램에 대해 선택한 중복성 모델에 따라 사용할 수 있는 옵션을 확인 하려면 [중복성 Azure Storage](../storage/common/storage-redundancy.md) 를 참조 하세요.
* SQL Database 원본에 대해서는 [개요: SQL Database를 사용한 클라우드 비즈니스 연속성 및 데이터베이스 재해 복구](../sql-database/sql-database-business-continuity.md) 를 참조하여 애플리케이션에 대해 선택한 복제 모델에 따라 사용할 수 있는 옵션을 확인합니다.


## <a name="option-3-wait-for-recovery"></a>옵션 3: 복구 대기
이 경우 사용자의 조치는 필요하지 않지만 해당 하위 지역이 복원될 때까지 서비스를 사용할 수 없습니다. 서비스의 현재 상태를 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
재해 복구 및 고가용성 전략을 구현하는 방법에 관해 자세히 알아보려면 [Azure 애플리케이션에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)을 참조하세요.

클라우드 플랫폼의 기능에 대한 자세한 기술적 이해를 높이려면 [Azure 복원력 기술 지침](/azure/architecture/checklist/resiliency-per-service)을 참조하세요.