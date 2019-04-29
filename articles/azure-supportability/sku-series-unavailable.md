---
title: SKU 시리즈 사용할 수 없음 | Microsoft Docs
description: 이 지역의 선택한 구독에 대해 일부 SKU 시리즈를 사용할 수 없습니다.
services: Azure Supportability
documentationcenter: ''
author: stevendotwang
manager: rajatk
editor: ''
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: a57899e36a6716a6fd59cb018119c225b7396c0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649131"
---
# <a name="region-or-sku-unavailable"></a>지역 또는 SKU 사용할 수 없음
이 문서에서는 Azure 구독에 지역 또는 VM SKU에 대한 액세스 권한이 없는 문제를 해결하는 방법을 설명합니다.

## <a name="symptoms"></a>증상

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>가상 머신을 배포할 때 다음 메시지 중 하나가 표시됩니다.
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>예약 가상 머신 인스턴스를 구입할 때 다음 메시지 중 하나가 표시됩니다.

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>계산 코어 할당량을 늘리도록 지원을 요청할 때 지역 또는 SKU 제품군을 선택할 수 없습니다.

## <a name="solution"></a>해결 방법
먼저 비즈니스 요구를 충족하는 대체 지역 또는 SKU를 사용하는 것이 좋습니다. 적합한 지역 또는 SKU를 찾을 수 없다면 다음 단계를 수행하여 "구독 관리" [지원을 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)합니다.


- 기본 페이지에서 문제 유형을 "구독 관리"로 선택하고, 구독을 선택한 후 "다음"을 클릭합니다.

![기본 사항 블레이드](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   문제 페이지에서 문제 유형을 "기타 일반적인 질문"으로 선택합니다.
- 세부 정보 섹션에서:
  - 가상 머신을 배포하거나 예약 Virtual Machine 인스턴스를 구입하려고 하는지를 알려주세요.
  - 배포하거나 구입하려고 계획 중인 지역, SKU 및 가상 머신 인스턴스 수를 지정하세요


![문제](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   연락처 정보를 입력하고 "만들기"를 클릭합니다.

![연락처 정보](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>사용자 의견
Microsoft는 사용자 의견 및 제안을 항상 환영합니다! [제안 사항](https://feedback.azure.com/forums/266794-support-feedback)을 보내 주세요. [Twitter](https://twitter.com/azuresupport)나 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure)을 통해 참여하실 수도 있습니다.

## <a name="learn-more"></a>자세한 정보
[Azure 지원 FAQ](https://azure.microsoft.com/support/faq)

