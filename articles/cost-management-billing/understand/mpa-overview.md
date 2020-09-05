---
title: Microsoft 파트너 계약 청구 계정 시작 - Azure CSP
description: Microsoft 파트너 계약 청구 계정 이해(CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 98941530ba0483e82f43af222e12df7439b6625e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688753"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Microsoft 파트너 계약 청구 계정 시작

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 여러 청구 계정에 액세스할 수 있습니다. 예를 들어 개인 프로젝트를 위해 Azure에 가입한 경우가 있을 수 있습니다. 조직의 기업계약, Microsoft 고객 계약 또는 Microsoft 파트너 계약을 통해 Azure에 액세스할 수도 있습니다. 이러한 각 시나리오의 경우 별도의 청구 계정을 가질 수 있습니다.

이 문서는 Microsoft 파트너 계약에 대한 청구 계정에 적용됩니다. 이러한 계정은 새 상거래 환경에서 고객에 대한 청구를 관리하기 위해 CSP(클라우드 솔루션 공급자)에 대해 만들어집니다. 새 환경은 Microsoft 고객 계약을 수락하고 Azure 플랜이 있는 고객을 한 명 이상 보유한 파트너에만 사용할 수 있습니다. [Microsoft 파트너 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-partner-agreement). [Azure 플랜](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)에서는 Microsoft 고객 계약에 따라 고객이 Azure 서비스를 종량제 요금으로 이용할 수 있습니다.

## <a name="your-billing-account"></a>청구 계정

Microsoft 파트너 계약에 대한 청구 계정에는 비즈니스에서 수행하는 각 통화에 대한 청구 프로필이 포함됩니다. 청구 프로필을 사용하여 해당 통화에 대한 청구서를 관리할 수 있습니다. 고객과의 관계를 설정하는 경우 해당 통화에 따라 Azure 구독 및 기타 구매는 해당 청구 프로필로 청구됩니다.

다음 다이어그램은 청구 계정, 청구 프로필, 고객 및 재판매인 간의 관계를 보여 줍니다.

![Microsoft 파트너 계약 청구 계층을 보여 주는 다이어그램](./media/mpa-overview/mpa-hierarchy.svg)

조직에서 **글로벌 관리** 및 **관리 에이전트** 역할을 가진 사용자는 청구 계정, 청구 프로필 및 고객을 관리할 수 있습니다. 자세히 알아보려면 [파트너 센터 - 사용자 역할 및 사용 권한 할당](https://docs.microsoft.com/partner-center/permissions-overview)을 참조하세요.

## <a name="billing-profiles"></a>청구 프로필

청구 프로필을 사용하여 통화에 대한 청구서를 관리할 수 있습니다. 월초에 사용자 계정에서 각 청구 프로필의 월별 청구서가 생성됩니다. 청구서에는 이전 달의 모든 Azure 구독 및 기타 구매에 대한 청구 프로필의 통화 요금이 포함됩니다.

Azure Portal에서 청구서를 보고 사용량 파일 및 가격표와 같은 관련 문서를 다운로드할 수 있습니다. 자세한 내용은 [Microsoft 파트너 계약 청구서 다운로드](download-azure-invoice.md)를 참조하세요.

> [!IMPORTANT]
>
> 청구 프로필에 대한 청구서에는 Azure 플랜 뿐만 아니라 SaaS, Azure 마켓플레이스가 있는 고객에 대한 요금 및 Microsoft 고객 계약을 수락하지 않고 Azure 플랜이 없는 고객에 대한 예약 구매가 포함됩니다.

## <a name="customers"></a>고객

Microsoft 고객 계약을 수락하고 Azure Portal에 Azure 플랜이 있는 고객을 보고 관리할 수 있습니다. 이러한 고객에 대한 Azure 구독을 만들고 관리하는 것 뿐만 아니라 요금 및 트랜잭션도 볼 수 있습니다.

### <a name="enable-policy-to-give-visibility-into-cost"></a>정책을 활성화하여 비용에 대한 가시성 제공

고객 조직의 사용자가 Azure 사용량에 대한 종량제 요금으로 비용을 보고 분석할 수 있는지 여부를 제어하는 정책을 적용합니다. 기본적으로 이 정책은 해제되어 있으며 사용자가 비용을 볼 수 없습니다. 이 기능이 활성화되면 구독에 대한 적절한 [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 액세스 권한이 있는 사용자는 구독에 대한 비용을 보고 분석할 수 있습니다.

정책을 켜려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Cost Management + 청구**에서 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/mpa-overview/search-cmb.png)

1. 왼쪽에서 **고객**을 선택한 다음, 목록에서 고객을 선택합니다.

   ![고객 선택을 보여 주는 스크린샷](./media/mpa-overview/mpa-customers.png)

1. 왼쪽에서 **정책**을 선택합니다.

   ![정책을 보여 주는 스크린샷](./media/mpa-overview/mpa-change-policy.png)

1. **예**를 선택합니다.

## <a name="resellers"></a>재판매인

CSP [2계층 모델](https://docs.microsoft.com/partner-center)의 간접 공급자는 Azure Portal에서 고객에 대한 구독을 만드는 동안 재판매인을 선택할 수 있습니다. 만든 후에는 재판매인에 의해 필터링된 구독 목록을 보고, Azure 비용 분석에서 재판매인을 통해 고객에 대한 비용을 분석할 수 있습니다.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft 파트너 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

청구 계정에 대해 알아보려면 다음 문서를 참조하세요.

- [Microsoft 파트너 계약에 대한 추가 Azure 구독 만들기](../manage/create-subscription.md)
- [Azure 청구 API](https://docs.microsoft.com/rest/api/billing/)를 사용하여 청구 데이터를 사용자 고유의 보고 시스템과 통합합니다.
- [파트너를 위한 Azure Cost Management 빠른 시작 가이드](https://go.microsoft.com/fwlink/?linkid=2106482)
