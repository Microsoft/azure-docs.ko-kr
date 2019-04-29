---
title: Azure CDN를 사용한 중국 콘텐츠 전송 | Microsoft Docs
description: Azure CDN(콘텐츠 전송 네트워크)을 사용하여 중국 사용자에게 콘텐츠를 배달하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: ce136a341809b1a485e86337f428b4685eba22f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681511"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Azure CDN를 사용한 중국 콘텐츠 전송

Azure CDN(콘텐츠 전송 네트워크) 글로벌은 중국에서 오는 요청에 최상의 성능을 제공하는 모든 POP(point-of-presence) 또는 중국 부근의 POP 위치를 사용하여 중국 사용자에게 콘텐츠를 서비스할 수 있습니다. 그러나 중국이 고객에게 중요한 시장이고 고객이 빠른 성능을 필요로 하는 경우 대신 Azure CDN을 사용하는 것을 고려합니다.

Azure CDN 중국은 많은 지역 공급자와 협력하여 중국 내 POP에서 콘텐츠를 배달한다는 점에서 Azure CDN 글로벌과 다릅니다. 중국의 규정 준수 및 규제로 인해 Azure CDN 중국을 사용하려면 별도 구독을 등록해야 하며, 웹 사이트는 ICP 라이선스가 있어야 합니다. 콘텐츠 전송을 사용하고 관리하는 포털 및 API 환경은 Azure CDN 글로벌 및 Azure CDN 중국 간에 동일합니다.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Azure CDN 글로벌 및 Azure CDN 중국의 비교

Azure CDN 글로벌 및 Azure CDN 중국은 다음의 특징이 있습니다.

- Azure CDN 글로벌:

     - 포털: https://portal.azure.com  

     - 중국 외부에서 콘텐츠 전송 수행

     - 네 가지 가격 책정 계층: Microsoft 표준, Verizon 표준, Verizon premium 및 Akamai 표준의

     - [설명서](https://docs.microsoft.com/azure/cdn/)

- Azure CDN 중국:

     - 포털: https://portal.azure.cn

     - 중국 내에서 콘텐츠 전송 수행

     - 두 가지 가격 책정 계층: Standard 및 premium

     - [설명서](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>다음 단계

Azure CDN 중국에 대한 자세한 정보는 다음을 참조합니다.

- [Content Delivery Network 기능](https://www.azure.cn/en-us/home/features/cdn/)

- [Azure Content Delivery Network 개요](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Azure Content Delivery Network 사용](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [중국 내 Azure 서비스 제공 여부](https://docs.microsoft.com/azure/china/china-get-started-service-availability)



