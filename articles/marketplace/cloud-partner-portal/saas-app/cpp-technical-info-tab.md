---
title: Azure SaaS 애플리케이션 제품 기술 구성 | Microsoft Docs
description: Azure Marketplace의 SaaS 애플리케이션 제품에 대한 기술 정보를 구성합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 891d9b7b34e3d30efb46b69ef1aa75566fe634c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594325"
---
# <a name="saas-application-technical-info-tab"></a>SaaS 애플리케이션 기술 정보 탭

기술 정보 탭은 기술 구성 양식을 제공합니다. 이 양식을 사용하여 만들려는 SaaS 애플리케이션(앱)의 유형을 선택하고 앱이 고객에게 제공되는 방식을 구성할 수 있습니다.

![기술 구성 양식](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>기술 구성 양식

이 양식에는 제품 및 활용 방안의 두 개 필드가 있습니다.

### <a name="product-field"></a>제품 필드

다음 상점 둘 다에 SaaS 앱을 제공할 수 있습니다.
- **목록** 옵션을 선택하여 비즈니스 사용자에게 제공
- **Microsoft를 통해 판매**를 선택하여 IT 관리자에게 제공
빌드할 SaaS 앱의 유형을 결정하는 데 도움이 되도록 [상점 선택 이해](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection)를 읽어 보세요.

#### <a name="sell-through-microsoft"></a>Microsoft를 통해 판매
이 환경을 빌드하려면 다음을 구성해야 합니다.

- SaaS 서비스 웹 사이트를 Microsoft SaaS API에 연결합니다. [Azure를 통해 SaaS 판매 - API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) 문서에서 이 연결을 만드는 방법을 설명합니다.
- Cloud 파트너 포털의 기술 구성 양식에서 Azure를 통해 판매를 사용하도록 설정하고 필요한 정보를 제공합니다. 이 청구 모델 및 구현 방식에 대한 자세한 내용은 [SaaS - Azure를 통해 판매](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions)를 참조하세요.

  ![Microsoft를 통해 판매 양식](./media/saas-techinfo-sellthrough-ms.png)

다음 표에서는 Microsoft를 통해 판매의 필수 필드에 대해 설명합니다.

|  **필드 이름**   |  **설명**  |
|  ---------------  |  ---------------  |
|    미리 보기 구독 ID               |    제안이 공개적으로 사용 가능해지기 전에 미리 보기에서 제안을 테스트하는 데 사용되는 모든 Azure 구독 식별자입니다.               |
|     시작하기 지침              |   SaaS 앱에 연결하도록 지원하기 위해 고객과 공유하는 지침입니다. &lt;p&gt;, &lt;h1&gt;, &lt;li&gt; 등의 기본 HTML 태그가 허용됩니다.                |
|    방문 페이지 URL  |   Azure Portal로부터 획득한 후 고객이 방문하도록 설정하는 사이트 URL입니다. 이 URL은 Microsoft와의 거래를 용이하게 하기 위한 연결 API를 수신할 엔드포인트로도 사용됩니다.                |
|  연결 웹후크    |  Microsoft에서 고객을 대신해서 보내야 하는 모든 비동기 이벤트(예: Azure 구독이 더 이상 유효하지 않음)를 위해 연결 웹후크를 제공해야 합니다. 웹후크 시스템이 아직 준비되지 않은 경우 가장 단순한 구성은 게시되는 모든 이벤트를 수신 대기한 후 적절히 처리하는 HTTP 엔드포인트 논리 앱을 유지하는 것입니다. 자세한 내용은 <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩</a>을 참조하세요.                |
|  Azure AD 테넌트 ID 및 앱 ID      |   사용자는 두 서비스 간 연결이 인증된 통신을 통해 설정되는지 검증할 수 있도록 Azure Portal 내에서 Active Directory 앱을 만들어야 합니다. 이러한 필드에 대해, AD 앱을 만들고, 필요한 해당 테넌트 ID 및 앱 ID를 붙여 넣습니다. 앱 ID는 publisherID와 연결되어 있습니다. 따라서 모든 제안에서 앱 ID가 동일한지 확인합니다.             |


마지막으로, **Microsoft를 통해 판매**를 선택하는 경우 **플랜**이라는 다른 새 제안 탭이 있습니다. 

[플랜 탭](./cpp-plans-tab.md)에는 SaaS 앱이 지원하는 특정 플랜 및 해당 가격이 나열됩니다. 현재, 1개월 또는 3개월 동안 무료 액세스를 허용하는 월별 가격 책정을 사용하고 있습니다. 이러한 요금제 및 가격은 사용자 고유의 SaaS 앱 사이트의 정확한 요금제 및 가격과 일치해야 합니다.

>[!NOTE] 
>플랜은 Microsoft를 통해 판매를 선택한 경우에만 필요합니다.

### <a name="call-to-action-field"></a>활용 방안 필드

활용 방안 필드를 사용하여 제안의 획득 단추에 표시되는 메시지를 선택할 수 있습니다. 다음 옵션을 사용할 수 있습니다.

- 무료 - 이 옵션을 선택하면 고객이 SaaS 앱에 액세스할 수 있는 평가판 URL을 입력하라는 메시지가 표시됩니다. 예: https://contoso.com/trial
- 평가판- 이 옵션을 선택하면 고객이 SaaS 앱에 액세스할 수 있는 평가판 URL을 입력하라는 메시지가 표시됩니다. 예: https://contoso.com/trial
- 연락처

활용 방안 옵션에 대한 자세한 내용은 게시 옵션 선택을 참조하세요.

>[!Note]
>클라우드 솔루션 공급자 (CSP) 파트너 채널 옵트인 이제 사용할 수 있습니다.  참조 하세요 [클라우드 솔루션 공급자](../../cloud-solution-providers.md) Microsoft CSP를 통해 제품을 마케팅 하는 방법은 채널 파트너입니다.

## <a name="next-steps"></a>다음 단계

- [플랜 탭(선택 사항)](./cpp-plans-tab.md)
- [채널 정보 탭](./cpp-channel-info-tab.md)
