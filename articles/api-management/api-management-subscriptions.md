---
title: Azure API Management의 구독 | Microsoft Docs
description: Azure API Management의 구독 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 6f577530c42952c6340a15110bcd37383a5fca57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693560"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management의 구독

구독은 Azure API Management에서 중요한 개념입니다. 구독은 API 소비자가 API Management 인스턴스를 통해 게시된 API에 대한 액세스 권한을 획득하는 가장 일반적인 방법입니다. 이 문서는 개념에 대한 개요를 제공합니다.

## <a name="what-are-subscriptions"></a>구독이란?

API Management를 통해 API를 게시하면 구독 키를 사용하여 이러한 API에 쉽고 안전하게 액세스할 수 있습니다. 게시된 API를 사용해야 하는 개발자는 이러한 API에 호출 시 HTTP 요청에 유효한 구독 키를 포함해야 합니다. 그렇지 않으면 API Management 게이트웨이에서 호출을 즉시 거부합니다. 그리고 백 엔드 서비스에 호출이 전달되지 않습니다.

API에 액세스하기 위해 구독 키를 얻으려면 구독이 필요합니다. 구독은 일반적으로 구독 키 쌍의 이름을 지정한 컨테이너입니다. 게시된 API를 사용해야 하는 개발자는 구독을 가져올 수 있습니다. API 게시자에게 승인을 받을 필요는 없습니다. 또한 API 게시자는 API 소비자를 위해 직접 구독을 만들 수도 있습니다.

> [!TIP]
> API Management는 또한 다음 예를 포함한 기타 API 액세스 보호 메커니즘도 지원합니다.
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [클라이언트 인증서](api-management-howto-mutual-certificates-for-clients.md)
> - [IP 허용 목록](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>구독 범위

구독은 다양한 범위, 즉 제품, 모든 API 또는 개별 API와 연결할 수 있습니다.

### <a name="subscriptions-for-a-product"></a>제품에 대한 범위

일반적으로 API Management의 구독은 항상 단일 [API 제품](api-management-terminology.md) 범위와 연결되어 있습니다. 개발자가 개발자 포털에서 제품 목록을 찾았다고 가정해 보겠습니다. 그런 다음에는 사용할 제품에 대한 구독 요청을 제출할 것입니다. 구독 요청이 승인되면(자동으로 또는 API 게시자를 통해) 개발자는 포함된 키를 사용하여 제품의 모든 API에 게시할 수 있습니다.

![제품 구독](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> 특정 시나리오에서는 API 게시자가 구독 요건 없이 API 제품을 대중에 공개하길 원할 수 있습니다. Azure Portal에서 제품의 **설정** 페이지에서 **구독 필요** 옵션을 선택 취소할 수 있습니다. 결과적으로, 제품의 모든 API에 API 키 없이 액세스할 수 있습니다.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>모든 API 또는 개별 API에 대한 구독

API Management의 [소비](https://aka.ms/apimconsumptionblog) 계층이 도입될 때, 키 관리를 간소화하기 위해 몇 가지 변경 사항이 적용되었습니다.
- 첫째, 구독 범위가 두 개 더 추가되었습니다(모든 API와 단일 API). 구독의 범위는 더 이상 API 제품으로 한정되지 않습니다. 이제 제품을 만들고 먼저 API에 추가하지 않아도 API(또는 API Management 인스턴스 내의 모든 API)에 액세스 권한을 부여하는 키를 만들 수 있습니다. 또한 각 API Management 인스턴스는 이제 변경 불가능한 모든 API 구독과 함께 제공됩니다. 이 구독을 사용하면 테스트 콘솔 내에서 더욱 쉽고 간단하게 API를 테스트하고 디버그할 수 있습니다.

- 둘째, API Management는 이제 **독립 실행형** 구독을 허용합니다. 개발자 계정과 연결하는 데 더 이상 구독이 필요하지 않습니다. 이 기능은 여러 개발자 또는 팀에서 구독을 공유하는 경우와 같은 시나리오에서 유용합니다.

- 마지막으로, 이제 API 게시자가 Azure Portal에서 직접 [구독을 생성](api-management-howto-create-subscriptions.md)할 수 있습니다.

    ![유연성 있는 구독](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>다음 단계
API Management에 대한 자세한 내용 확인:

+ API Management의 기타 [개념](api-management-terminology.md)에 대해 알아보기
+ [자습서](import-and-publish.md)에 따라 API Management에 대해 자세히 알아보기
+ 일반적인 질문은 [FAQ 페이지](api-management-faq.md) 확인