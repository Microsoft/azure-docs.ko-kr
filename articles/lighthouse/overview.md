---
title: Azure Lighthouse란?
description: Azure Lighthouse에서 서비스 공급자는 더 높은 자동화 및 효율성으로 고객을 위한 관리형 서비스를 제공할 수 있습니다.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: d21d11d1316b177f38b6bce197dec4f2c3d3fac8
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660736"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse란?

Azure Lighthouse는 교차 및 다중 테넌트 관리를 지원하여 리소스 및 테넌트 간에 더 높은 자동화, 확장성 및 향상된 거버넌스를 허용합니다. Azure Lighthouse를 통해 서비스 공급자는 Azure 플랫폼에 기본 제공되는 포괄적이고 강력한 관리 도구를 사용하여 관리형 서비스를 제공할 수 있습니다. 이 제품은 여러 테넌트 간에 리소스를 관리하는 엔터프라이즈 IT 조직에도 유용할 수 있습니다.

![Azure Lighthouse의 개요 다이어그램](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>이점

Azure Lighthouse를 사용하면 관리형 서비스를 효과적이면서 효율적으로 빌드하고 제공할 수 있습니다. 이점은 다음과 같습니다.

- **대규모 관리**: 고객 참여 및 수명 주기 작업을 통해 고객 리소스를 보다 쉽게 관리하고 확장할 수 있습니다. 기존 API, 관리 도구 및 워크플로는 배치된 지역에 관계없이 위임된 리소스와 함께 사용할 수 있습니다.
- **고객을 위한 더 높은 가시성 및 정밀도**: 고객은 IP가 유지되는 동안 액세스를 완전히 제거하는 기능을 포함하여 사용자 작업을 보다 잘 볼 수 있고, 관리를 위해 위임하는 범위를 보다 정밀하게 제어할 수 있습니다.
- **포괄적이고 통합된 플랫폼 도구**: Microsoft의 도구 환경은 EA, CSP 및 종량제 등의 여러 라이선스 모델을 포함하는 주요 서비스 공급자 시나리오를 다룹니다. 새로운 기능은 기존 도구와 API, 라이선스 모델 및 CSP([클라우드 솔루션 공급자 프로그램](/partner-center/csp-overview))와 같은 파트너 프로그램에서 작동합니다. Azure Lighthouse는 기존 워크플로 및 애플리케이션에 연결될 수 있으며, [파트너 ID를 연결](../cost-management-billing/manage/link-partner-id.md)하여 고객의 계약에 대한 영향을 추적할 수 있습니다.

Azure Lighthouse를 사용하여 Azure 리소스를 관리하는 것과 관련된 추가 비용은 없습니다. 모든 Azure 고객 또는 파트너는 Azure Lighthouse를 사용할 수 있습니다.

## <a name="capabilities"></a>기능

Azure Lighthouse에는 참여 및 관리를 간소화하는 데 도움이 되는 여러 방법이 포함되어 있습니다.

- **Azure 위임 리소스 관리**: 컨텍스트 및 제어 평면을 전환할 필요 없이, 고유한 테넌트 내에서 고객의 Azure 리소스를 안전하게 관리할 수 있습니다. 필요에 따라 액세스 권한을 제거하는 기능을 통해 구독 및 리소스 그룹을 관리 테넌트의 지정된 사용자와 역할에 위임할 수 있습니다. 자세한 내용은 [Azure 위임 리소스 관리](concepts/azure-delegated-resource-management.md)를 참조하세요.
- **새로운 Azure Portal 환경**: Azure Portal의 [**내 고객** 페이지](./how-to/view-manage-customers.md)에서 테넌트 간 정보를 확인합니다. 고객은 해당 [**서비스 공급자** 페이지](how-to/view-manage-service-providers.md)에서 해당 서비스 공급자 액세스 권한을 보고 관리할 수 있습니다.
- **Azure Resource Manager 템플릿**: 이 템플릿을 통해 테넌트 간 관리 작업을 수행하고 위임된 고객 리소스를 등록할 수 있습니다. 자세한 내용은 [샘플 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) 및 [Azure Lighthouse에 고객 온보딩](how-to/onboard-customer.md)을 참조하세요.
- **Azure Marketplace의 관리형 서비스 제품**: 프라이빗 또는 퍼블릭 제안을 통해 고객에게 서비스를 제공하고 Azure Lighthouse에 자동으로 등록합니다. 자세한 내용은 [Azure Marketplace의 관리형 서비스 제품](concepts/managed-services-offers.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 위임 리소스 관리](concepts/azure-delegated-resource-management.md)에 대해 알아봅니다.
- [테넌트 간 관리 환경](concepts/cross-tenant-management-experience.md)을 살펴봅니다.
- [엔터프라이즈 내에서 Azure Lighthouse를 사용](concepts/enterprise.md)하는 방법을 참조하세요.
