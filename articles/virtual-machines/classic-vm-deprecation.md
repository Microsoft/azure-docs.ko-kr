---
title: 2023 년 3 월 1 일에 Azure 클래식 Vm을 사용 중지 하 고 있습니다.
description: 문서는 클래식 VM 사용 중지에 대 한 개략적인 개요를 제공 합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754806"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023 년 3 월 1 일 Azure Resource Manager IaaS 리소스 마이그레이션 

2014에서는 Azure Resource Manager에서 IaaS를 실행 했으며 이후 기능을 개선 했습니다. [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 이제 전체 iaas 기능 및 기타 고급 기능을 제공 하기 때문에 2020 년 2 월 28 일에 [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) 를 통해 iaas vm의 관리를 사용 하지 않으며,이 기능은 2023 년 3 월 1 일에 완전히 사용 중지 됩니다. 

현재 IaaS Vm의 약 90%가 Azure Resource Manager를 사용 하 고 있습니다. ASM (Azure Service Manager)을 통해 IaaS 리소스를 사용 하는 경우 지금 마이그레이션 계획을 시작 하 고 2023 년 3 월 1 일까 지 [Azure Resource Manager](../azure-resource-manager/management/index.yml)를 활용 하 여 완료 합니다.

클래식 Vm은 [최신 수명 주기 정책](https://support.microsoft.com/help/30881/modern-lifecycle-policy) 에 따라 만료 됩니다.

## <a name="how-does-this-affect-me"></a>이 변경 사항은 어떤 영향을 미치나요? 

- 2020 년 2 월 28 일부 터 Azure Service Manager (ASM 2020)를 통해 IaaS Vm을 사용 하지 않는 고객은 더 이상 클래식 Vm을 만들 수 없습니다. 
- 2023 년 3 월 1 일부 터 고객은 더 이상 Azure Service Manager를 사용 하 여 IaaS Vm을 시작할 수 없으며 계속 실행 중이거나 할당 된이 중지 되 고 할당이 취소 됩니다. 
- 2023 년 3 월 1 일부 터 Azure Resource Manager로 마이그레이션되지 않은 구독은 남은 클래식 Vm을 삭제 하는 일정에 대 한 정보가 표시 됩니다.  

다음 Azure 서비스 및 기능은이 사용 중지에 영향을 **받지 않습니다** . 
- Cloud Services 
- 클래식 Vm에서 사용 **하지 않는** 저장소 계정 
- Vnet (가상 네트워크)는 클래식 Vm에서 사용 **되지 않습니다** . 
- 기타 클래식 리소스

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행 해야 하나요? 

- 현재 Azure Resource Manager로의 마이그레이션 계획을 시작 합니다. 

- 영향을 받는 모든 Vm 목록을 만듭니다. [Azure PORTAL Vm 블레이드의](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) vm 유형 "Virtual Machines (클래식)"은 구독 내에서 영향을 받는 모든 vm입니다. 

- 클래식 [Linux](./linux/migration-classic-resource-manager-plan.md) 및 [Windows](./windows/migration-classic-resource-manager-plan.md) vm을 Azure Resource Manager로 마이그레이션하는 방법에 [대해 자세히 알아보세요](./windows/migration-classic-resource-manager-overview.md) .

- 자세한 내용은 [클래식에서 Azure Resource Manager로 마이그레이션에 대 한 질문과 대답](./migration-classic-resource-manager-faq.md) 을 참조 하세요.

- 기술 관련 질문을 하 고 허용 목록에 구독을 추가 하려면 [지원 담당자에 게 문의 하세요](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- FAQ 및 피드백의 일부가 아닌 다른 질문은 아래에 설명 되어 있습니다.

- 가능한 한 빨리 마이그레이션을 완료 하 여 비즈니스 영향을 방지 하 고 향상 된 성능, 보안 & Azure Resource Manager에서 제공 하는 새로운 기능을 활용 하세요. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>이 마이그레이션을 위해 어떤 리소스를 제공 하나요?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): 마이그레이션에 대 한 microsoft & 커뮤니티 지원

- [Azure 마이그레이션 지원](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): 마이그레이션 중 기술 지원을 위한 전용 지원 팀

- [Microsoft Fast track](https://www.microsoft.com/fasttrack): Microsoft fast track 팀은 적격 고객으로 마이그레이션하는 동안 기술 지원을 제공할 수 있습니다. 

- 회사/조직이 Microsoft와 파트너 관계를 맺고 있거나 (CSA (클라우드 솔루션 설계자), TAMs (기술 계정 관리자) 등의 Microsoft 전문가와 함께 작업 하는 경우 마이그레이션에 대 한 추가 리소스를 확인 하세요. 

