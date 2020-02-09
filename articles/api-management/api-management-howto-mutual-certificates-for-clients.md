---
title: API Management에서 클라이언트 인증서 인증을 사용 하 여 Api 보안
titleSuffix: Azure API Management
description: 클라이언트 인증서를 사용하여 API에 대한 액세스의 보안을 유지하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713140"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management에서 클라이언트 인증서 인증을 사용하여 API를 보호하는 방법

API Management에서는 클라이언트 인증서를 사용하여 API에 대한 액세스(예: API Management에 대한 클라이언트)를 보호하는 기능을 제공합니다. 정책 식을 사용 하 여 들어오는 인증서의 유효성을 검사 하 고 원하는 값에 대해 인증서 속성을 확인할 수 있습니다.

클라이언트 인증서 (예: 백 엔드에 API Management)를 사용 하 여 API의 백 엔드 서비스에 대한 액세스를 보호 하는 방법에 대한 자세한 내용은 [클라이언트 인증서 인증을 사용 하 여 백 엔드 서비스를 보호 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) 을 참조 하세요.

> [!IMPORTANT]
> 개발자, 기본, 표준 또는 프리미엄 계층에서 HTTP/2를 통해 클라이언트 인증서를 수신 하 고 확인 하려면 아래와 같이 "사용자 지정 도메인" 블레이드에서 "클라이언트 인증서 협상" 설정을 켜야 합니다.

![클라이언트 인증서 협상](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 소비 계층에서 클라이언트 인증서를 수신 하 고 확인 하려면 아래와 같이 "사용자 지정 도메인" 블레이드에서 "클라이언트 인증서 요청" 설정을 켜야 합니다.

![클라이언트 인증서 요청](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>발급자 및 주체 확인

클라이언트 인증서의 발급자 및 주체를 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 인증서 해지 목록 확인을 사용 하지 않도록 설정 하려면 `context.Request.Certificate.Verify()`대신 `context.Request.Certificate.VerifyNoRevocation()`를 사용 합니다.
> 클라이언트 인증서가 자체 서명 된 경우에는 `context.Request.Certificate.Verify()`에 대한 루트 또는 중간 CA 인증서를 API Management [업로드](api-management-howto-ca-certificates.md) 하 고 `context.Request.Certificate.VerifyNoRevocation()` 합니다.

## <a name="checking-the-thumbprint"></a>지문 확인

클라이언트 인증서의 지문을 확인하도록 아래 정책을 구성할 수 있습니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 인증서 해지 목록 확인을 사용 하지 않도록 설정 하려면 `context.Request.Certificate.Verify()`대신 `context.Request.Certificate.VerifyNoRevocation()`를 사용 합니다.
> 클라이언트 인증서가 자체 서명 된 경우에는 `context.Request.Certificate.Verify()`에 대한 루트 또는 중간 CA 인증서를 API Management [업로드](api-management-howto-ca-certificates.md) 하 고 `context.Request.Certificate.VerifyNoRevocation()` 합니다.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management에 업로드된 인증서에 대해 지문 확인

다음 예제에서는 API Management에 업로드된 인증서에 대해 클라이언트 인증서의 지문을 확인하는 방법을 보여 줍니다.

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 인증서 해지 목록 확인을 사용 하지 않도록 설정 하려면 `context.Request.Certificate.Verify()`대신 `context.Request.Certificate.VerifyNoRevocation()`를 사용 합니다.
> 클라이언트 인증서가 자체 서명 된 경우에는 `context.Request.Certificate.Verify()`에 대한 루트 또는 중간 CA 인증서를 API Management [업로드](api-management-howto-ca-certificates.md) 하 고 `context.Request.Certificate.VerifyNoRevocation()` 합니다.

> [!TIP]
> 이 [문서](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) 에서 설명 하는 클라이언트 인증서 교착 상태 문제는 여러 가지 방법으로 매니페스트 될 수 있습니다. 예를 들어, 요청을 중지 하 고, 시간이 초과 되 면 `403 Forbidden` 상태 코드를 반환 `context.Request.Certificate` `null`. 이 문제는 일반적으로 콘텐츠 길이가 약 60KB 이상인 `POST` 및 `PUT` 요청에 영향을 줍니다.
> 이 문제가 발생 하지 않도록 하려면 아래와 같이 "사용자 지정 도메인" 블레이드에서 원하는 호스트 이름에 대해 "클라이언트 인증서 협상" 설정을 설정 합니다. 소비 계층에서는이 기능을 사용할 수 없습니다.

![클라이언트 인증서 협상](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>다음 단계

-   [클라이언트 인증서 인증을 사용하여 백 엔드 서비스를 보호하는 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [인증서 업로드 방법](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
