---
title: Azure API Managment 정책 샘플 - 백 엔드 서비스로 요청 컨텍스트 정보 보내기 | Microsoft Docs
description: Azure API Management 정책 샘플 - 백 엔드 서비스에 요청 컨텍스트 정보를 보내는 방법을 보여줍니다.
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
ms.openlocfilehash: 3369f3b3349e8daf9ff540b824c10bbd618a1147
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859956"
---
# <a name="send-request-context-information-to-the-backend-service"></a>백 엔드 서비스에 요청 컨텍스트 정보 보내기

이 문서에서는 백 엔드 서비스에 요청 컨텍스트 정보를 보내는 방법을 보여 주는 Azure API Management 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-samples.md)

