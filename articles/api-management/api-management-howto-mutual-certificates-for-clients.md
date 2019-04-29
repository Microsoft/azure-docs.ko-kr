---
title: API Management에서 클라이언트 인증서 인증을 사용하여 API 보호 - Azure API Management | Microsoft Docs
description: 클라이언트 인증서를 사용하여 API에 대한 액세스의 보안을 유지하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 450ebc621758363c5ea9ab6d631cd6c7df38794b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657716"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법

API Management에서는 클라이언트 인증서를 사용하여 API에 대한 액세스(예: API Management에 대한 클라이언트)를 보호하는 기능을 제공합니다. 현재, 클라이언트 인증서의 지문을 원하는 값과 비교해서 확인할 수 있습니다. API Management에 업로드된 기존 인증서와 지문을 비교해서 확인할 수도 있습니다.  

클라이언트 인증서를 사용하여 API의 백 엔드 서비스(예: 백 엔드에 대한 API Management)에 대한 액세스를 보호하는 방법에 대한 자세한 내용은 [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)을 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>만료 날짜 확인

인증서 만료 여부를 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>발급자 및 주체 확인

클라이언트 인증서의 발급자 및 주체를 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>지문 확인

클라이언트 인증서의 지문을 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management에 업로드된 인증서에 대해 지문 확인

다음 예제에서는 API Management에 업로드된 인증서에 대해 클라이언트 인증서의 지문을 확인하는 방법을 보여 줍니다. 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>다음 단계

*  [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [인증서 업로드 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

