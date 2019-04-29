---
title: Azure API Management에서 구독 만들기 | Microsoft Docs
description: Azure API Management에서 구독을 만드는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: bc791fea1dfd184749e84cb7b7a912972c6a9f12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657629"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Azure API Management에서 구독 만들기

Azure API Management를 통해 API를 게시하면 구독 키를 사용하여 이러한 API에 쉽고 안전하게 액세스할 수 있습니다. 게시된 API를 사용해야 하는 클라이언트 애플리케이션은 이러한 API에 호출할 때 HTTP 요청에 유효한 구독 키를 포함해야 합니다. API에 액세스하기 위해 구독 키를 얻으려면 구독이 필요합니다. 구독에 대한 자세한 내용은 [Azure API Management의 구독](api-management-subscriptions.md)을 참조하세요.

이 문서에서는 Azure Portal에서 구독을 만들기 위한 단계를 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 수행하기 위한 필수 구성 요소는 다음과 같습니다.

+ [API Management 인스턴스를 만듭니다](get-started-create-service-instance.md).
+ [API Management에서 구독](api-management-subscriptions.md)을 이해합니다.

## <a name="create-a-new-subscription"></a>새 구독 만들기

1. 왼쪽 메뉴에서 **구독**을 선택합니다.
2. **구독 추가**를 선택합니다.
3. 구독의 이름을 입력하고 범위를 선택합니다.
4. 필요에 따라 구독 사용자와 연결 해야 하는 경우 선택 합니다.
5. **저장**을 선택합니다.

![유연성 있는 구독](./media/api-management-subscriptions/flexible-subscription.png)

구독을 만들면 API에 액세스하기 위한 두 개의 API 키가 제공됩니다. 하나는 기본 키고 하나는 보조 키입니다. 

## <a name="next-steps"></a>다음 단계
API Management에 대한 자세한 내용 확인:

+ API Management의 기타 [개념](api-management-terminology.md)에 대해 알아보기
+ [자습서](import-and-publish.md)에 따라 API Management에 대해 자세히 알아보기
+ 일반적인 질문은 [FAQ 페이지](api-management-faq.md) 확인