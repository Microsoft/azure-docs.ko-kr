---
title: Azure Marketplace에 관리 서비스 제품 게시
description: Azure Lighthouse에 고객에 게 제공 하는 관리 서비스 제품을 게시 하는 방법에 대해 알아봅니다.
ms.date: 08/18/2020
ms.topic: how-to
ms.openlocfilehash: 11fb0073bcc3ba4437f78de8b263d165f0069db8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589727"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Azure Marketplace에 관리 서비스 제품 게시

이 문서에서는 파트너 센터에서 [상업적 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) 프로그램을 사용 하 여 [Azure Marketplace](https://azuremarketplace.microsoft.com) 에 공용 또는 개인 관리 서비스 제품을 게시 하는 방법을 알아봅니다. 제품을 구매한 고객은 구독 또는 리소스 그룹을 위임 하 여 [Azure Lighthouse](../overview.md)를 통해 관리할 수 있습니다.

## <a name="publishing-requirements"></a>게시 요구 사항

제품을 만들고 게시 하려면 [파트너 센터에 유효한 계정이](../../marketplace/partner-center-portal/create-account.md) 있어야 합니다. 계정이 아직 없는 경우 [등록 프로세스](https://aka.ms/joinmarketplace) 는 파트너 센터에서 계정을 만들고 상업적 Marketplace 프로그램에 등록 하는 단계를 안내 합니다.

관리 서비스 제공 [인증 요구 사항](/legal/marketplace/certification-policies#7004-business-requirements)에 따라 [실버 또는 골드 클라우드 플랫폼 역량 수준이](/partner-center/learn-about-competencies) 있거나 관리 서비스 제품을 게시 하기 위해 [Azure 전문가 MSP](https://partner.microsoft.com/membership/azure-expert-msp) 여야 합니다.

MPN(Microsoft 파트너 네트워크) ID는 게시하는 제품에 [자동으로 연결되어](../../cost-management-billing/manage/link-partner-id.md) 고객 계약에 미치는 영향을 추적합니다.

Azure Marketplace에 제품을 게시 하지 않거나 모든 요구 사항을 충족 하지 않으려는 경우 Azure Resource Manager 템플릿을 사용 하 여 수동으로 고객을 등록할 수 있습니다. 자세한 내용은 [Azure Lighthouse에 고객](onboard-customer.md)등록을 참조 하세요.

다음 표에서는 관리 서비스 제품을 게시 하거나 Azure Resource Manager 템플릿을 사용 하 여 고객을 등록 하는지 여부를 결정 하는 데 도움이 될 수 있습니다.

|**고려 사항**  |**관리 서비스 제안**  |**ARM 템플릿**  |
|---------|---------|---------|
|[파트너 센터 계정](../../marketplace/partner-center-portal/create-account.md) 필요   |예         |예        |
|[실버 또는 골드 클라우드 플랫폼 역량 수준](/partner-center/learn-about-competencies) 또는 [Azure 전문가 MSP](https://partner.microsoft.com/membership/azure-expert-msp) 가 필요 합니다.      |예         |예         |
|Azure Marketplace를 통해 신규 고객에 게 제공     |예     |예       |
|특정 고객에 게 제품을 제한할 수 있습니다.     |예 (CSP (클라우드 솔루션 공급자) 프로그램의 대리점을 통해 설정 된 구독과 함께 사용할 수 없는 개인 제공만 해당)         |예         |
|Azure Portal에서 고객 동의가 필요 합니다.     |예     |예   |
|Automation을 사용 하 여 여러 구독, 리소스 그룹 또는 고객을 등록할 수 있습니다. |예     |예    |
|새로운 기본 제공 역할 및 Azure Lighthouse 기능에 즉시 액세스     |항상 그렇지 않음 (약간의 지연 후 일반적으로 사용 가능)         |예         |

## <a name="create-your-offer"></a>제품 만들기

제공 해야 하는 모든 정보 및 자산을 포함 하 여 제품을 만드는 방법에 대 한 자세한 지침은 [관리 서비스 제안 만들기](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)를 참조 하세요.

일반 게시 프로세스에 대해 알아보려면 [Azure Marketplace 및 AppSource 게시 가이드](../../marketplace/marketplace-publishers-guide.md)를 참조 하세요. [상업 마켓플레이스 인증 정책](/legal/marketplace/certification-policies), 특히 [관리 서비스](/legal/marketplace/certification-policies#700-managed-services) 섹션도 검토해야 합니다.

고객이 제품을 추가한 후에는 하나 이상의 구독 또는 리소스 그룹을 위임할 수 있으며, 그러면 [Azure Lighthouse에 등록](#the-customer-onboarding-process)됩니다.

> [!IMPORTANT]
> 관리 서비스 제안의 각 계획에는 **매니페스트 세부 정보** 섹션이 포함 되어 있습니다. 여기에는 테 넌 트에서 위임 된 리소스 그룹 및/또는 해당 요금제를 구매한 고객의 구독에 대 한 액세스 권한이 있는 Azure Active Directory (Azure AD) 엔터티를 정의 합니다. 포함 하는 모든 그룹 (또는 사용자 또는 서비스 주체)은 계획을 구입 하는 모든 고객에 대해 동일한 권한을 갖게 됩니다. 각 고객에 대해 사용할 다른 그룹을 할당 하기 위해 각 고객에 게 단독으로 사용 되는 별도의 [비공개 요금제](../../marketplace/private-offers.md) 를 게시할 수 있습니다. 사설 요금제는 CSP (클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정 된 구독에서 지원 되지 않는다는 점에 유의 하세요.

## <a name="publish-your-offer"></a>제품 게시

모든 섹션을 완료한 후, 다음 단계는 Azure Marketplace에 제품을 게시하는 것입니다. **게시** 단추를 선택하여 제품을 라이브로 전환하는 프로세스를 시작합니다. 이 프로세스에 대 한 자세한 정보는 [여기](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)에서 확인할 수 있습니다. 

언제든지 [제품의 업데이트된 버전을 게시](../..//marketplace/partner-center-portal/update-existing-offer.md)할 수 있습니다. 예를 들어 이전에 게시된 제품에 새 역할 정의를 추가하려고 할 수 있습니다. 이렇게 하면 제품을 이미 추가한 고객은 Azure Portal의 [**서비스 공급자**](view-manage-service-providers.md) 페이지에 업데이트를 사용할 수 있음을 알려 주는 아이콘이 표시됩니다. 각 고객은 [변경 내용을 검토](view-manage-service-providers.md#update-service-provider-offers) 하 고 새 버전으로 업데이트할지 여부를 결정할 수 있습니다. 

## <a name="the-customer-onboarding-process"></a>고객 온보딩 프로세스

고객이 제품을 추가한 후에는 [하나 이상의 특정 구독 또는 리소스 그룹을 위임할](view-manage-service-providers.md#delegate-resources)수 있습니다. 그러면 Azure Lighthouse에 등록 됩니다. 고객이 제품을 수락했지만 아직 리소스를 위임하지 않은 경우 Azure Portal에서 [**서비스 공급자**](view-manage-service-providers.md) 페이지의 **공급자 제품** 섹션 맨 위에 메모가 표시됩니다.

> [!IMPORTANT]
> 위임은 등록 중인 구독에 대 한 [소유자 기본 제공 역할](../../role-based-access-control/built-in-roles.md#owner) 을 가진 고객 테 넌 트의 비 게스트 계정 (또는 등록 되는 리소스 그룹을 포함 하는)에 의해 수행 되어야 합니다. 구독을 위임할 수 있는 모든 사용자를 보기 위해 고객 테넌트의 사용자는 Azure Portal에서 구독을 선택하고, **IAM(액세스 제어)** 을 열고, [소유자 역할이 있는 모든 소유자를 볼 수 있습니다](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

고객이 구독 또는 구독 내에서 하나 이상의 리소스 그룹을 위임한 후에는 해당 구독에 대해 **Microsoft ManagedServices** 리소스 공급자가 등록 되 고 테 넌 트의 사용자는 제품의 권한 부여에 따라 위임 된 리소스에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업적 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)에 대해 알아보세요.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객**으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
