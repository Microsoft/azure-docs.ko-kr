---
title: Azure Lighthouse 및 Azure 관리형 애플리케이션
description: Azure Lighthouse 및 Azure 관리 되는 응용 프로그램을 사용 하 여 다양 한 시나리오를 지원 하 고 함께 사용할 수 있는 방법을 이해 합니다.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 81ff61a6d1b7487d3da0643bac3987589de46c1e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163427"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 및 Azure 관리형 애플리케이션

Azure 관리 되는 응용 프로그램 및 Azure Lighthouse는 서비스 공급자가 고객의 테 넌 트에 있는 리소스에 액세스할 수 있도록 하 여 작동 합니다. 작동 방식 및 사용 하도록 설정 하는 데 도움이 되는 시나리오와 함께 사용할 수 있는 방법에 대 한 차이점을 이해 하는 것이 도움이 될 수 있습니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만 [여러 테 넌 트를 관리](enterprise.md) 하는 기업은 동일한 프로세스와 도구를 사용할 수 있습니다.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 및 Azure 관리 되는 응용 프로그램 비교

### <a name="azure-lighthouse"></a>Azure Lighthouse

[Azure Lighthouse](../overview.md)를 사용 하 여 서비스 공급자는 고객의 구독 (또는 리소스 그룹)에서 직접 광범위 한 관리 작업을 수행할 수 있습니다. 이 액세스는 논리 프로젝션을 통해 수행 되며, 서비스 공급자는 자신의 테 넌 트에 로그인 하 여 고객의 테 넌 트에 속한 리소스에 액세스할 수 있습니다. 고객은 서비스 공급자에 게 위임할 구독 또는 리소스 그룹을 결정할 수 있으며, 고객은 해당 리소스에 대 한 모든 권한을 유지 합니다. 또한 언제 든 지 서비스 공급자의 액세스를 제거할 수 있습니다.

Azure Lighthouse를 사용 하기 위해 고객은 [ARM 템플릿을 배포](../how-to/onboard-customer.md) 하거나 [Azure Marketplace의 관리 서비스 제품](managed-services-offers.md)을 통해 [azure에서 위임 된 리소스 관리](azure-delegated-resource-management.md) 를 등록 합니다. [파트너 ID를 연결](../../cost-management-billing/manage/link-partner-id.md)하 여 고객의 계약에 대 한 영향을 추적할 수 있습니다.

Azure Lighthouse는 서비스 공급자가 지속적으로 고객에 대 한 관리 작업을 수행 하는 경우에 일반적으로 사용 됩니다.

### <a name="azure-managed-applications"></a>Azure 관리되는 애플리케이션

[Azure 관리 되는 응용 프로그램](../../azure-resource-manager/managed-applications/overview.md) 을 통해 서비스 공급자 또는 ISV는 고객이 자신의 구독에서 쉽게 배포 하 고 사용할 수 있는 클라우드 솔루션을 제공할 수 있습니다.

관리 되는 응용 프로그램에서 응용 프로그램에 사용 되는 리소스는 함께 번들로 묶여 게시자가 관리 하는 리소스 그룹에 배포 됩니다. 이 리소스 그룹은 고객의 구독에 있지만 게시자의 테 넌 트에 있는 id는이 리소스 그룹에 액세스할 수 있습니다. ISV는 관리 되는 응용 프로그램을 계속 관리 및 유지 관리 하는 반면, 고객은 해당 리소스 그룹에서 작업에 대 한 직접 액세스 권한이 없거나 해당 리소스에 대 한 액세스 권한이 없습니다.

관리 되는 응용 프로그램은 사용자 지정 [Azure Portal 환경](../../azure-resource-manager/managed-applications/concepts-view-definition.md) 및 [사용자 지정 공급자와의 통합](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)을 지원 합니다. 이러한 옵션을 사용 하면 보다 사용자 지정 및 통합 된 환경을 제공 하 여 고객이 일부 관리 작업을 쉽게 수행할 수 있습니다.

관리 되는 응용 프로그램은 특정 고객 사용을 위한 개인 제품 또는 여러 고객이 구매할 수 있는 공개 제품으로 [Azure Marketplace에 게시할](../../azure-resource-manager/managed-applications/publish-marketplace-app.md)수 있습니다. [관리 되는 응용 프로그램을 서비스 카탈로그에 게시](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)하 여 조직 내 사용자에 게 제공할 수도 있습니다. ARM 템플릿을 사용 하 여 서비스 카탈로그와 Marketplace 인스턴스를 모두 배포할 수 있습니다. 여기에는 상용 마켓플레이스 파트너의 고유한 식별자를 포함 하 여 [고객 사용 특성](../../marketplace/azure-partner-customer-usage-attribution.md)을 추적할 수 있습니다.

Azure 관리 되는 응용 프로그램은 일반적으로 서비스 공급자에 의해 완전히 관리 되는 턴키 솔루션을 통해 달성할 수 있는 특정 고객 요구에 사용 됩니다.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Azure Lighthouse 및 Azure 관리 되는 응용 프로그램 함께 사용

Azure Lighthouse 및 Azure 관리 되는 응용 프로그램은 다른 액세스 메커니즘을 사용 하 여 서로 다른 목표를 달성 하는 반면, 서비스 공급자가 동일한 고객을 사용 하 여 둘 다를 사용 하는 것이 적합 한 시나리오가 있을 수 있습니다.

예를 들어 고객은 Azure Lighthouse를 통해 서비스 공급자가 제공 하는 관리 서비스를 사용 하 여 위임 된 구독을 지속적으로 제어 하는 것과 함께 파트너의 작업을 볼 수 있습니다. 그러나 서비스 공급자는 고객이 고객의 테 넌 트에 저장 되는 특정 리소스에 액세스 하거나 해당 리소스에 대해 사용자 지정 된 작업을 허용 하지 않도록 할 수도 있습니다. 이러한 목표를 달성 하기 위해 서비스 공급자는 개인 제품을 관리 되는 응용 프로그램으로 게시할 수 있습니다. 관리 되는 응용 프로그램에는 고객의 테 넌 트에 배포 된 리소스 그룹이 포함 될 수 있지만 고객이 직접 액세스할 수는 없습니다.

또한 고객은 이러한 서비스 공급자의 Azure Lighthouse를 통해 관리 되는 서비스를 사용 하는지 여부에 관계 없이 여러 서비스 공급자의 관리 되는 응용 프로그램에 관심이 있을 수 있습니다. 또한 CSP (클라우드 솔루션 공급자) 프로그램의 파트너는 다른 Isv가 게시 한 특정 관리 되는 응용 프로그램을 Azure Lighthouse를 통해 지 원하는 고객에 게 재판매 수 있습니다. 광범위 한 옵션을 사용 하는 경우 서비스 공급자는 적절 한 리소스에 대 한 액세스를 제한 하면서 고객의 요구에 맞게 적절 한 균형을 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 관리 되는 응용 프로그램](../../azure-resource-manager/managed-applications/overview.md)에 대해 알아봅니다.
- [Azure Lighthouse에 구독을 등록](../how-to/onboard-customer.md)하는 방법에 대해 알아봅니다.
