---
title: Azure API Management 정책 샘플 - 게이트웨이 및 백 엔드 간의 권한 부여를 위해 OAuth2 사용 | Microsoft Docs
description: Azure API Management 정책 샘플 - 게이트웨이와 백 엔드 간의 권한 부여를 위해 OAuth2를 사용하는 방법을 보여줍니다. AAD에서 액세스 토큰을 가져오고 백 엔드로 전달하는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859093"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>게이트웨이와 백 엔드 간의 권한 부여에 OAuth2 사용

이 문서에서는 게이트웨이와 백 엔드 간의 권한 부여를 위해 OAuth2를 사용하는 방법을 보여 주는 Azure API Management 정책 샘플을 제공합니다. AAD에서 액세스 토큰을 가져오고 백 엔드로 전달하는 방법을 보여 줍니다. 

정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

다음 스크립트는 {{property}}에 나타나는 속성을 사용합니다. 속성 및 API Management 정책을 사용하는 방법에 대해 자세히 알아보려면 [이](../api-management-howto-properties.md) 항목을 참조하세요.
 
## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-samples.md)

