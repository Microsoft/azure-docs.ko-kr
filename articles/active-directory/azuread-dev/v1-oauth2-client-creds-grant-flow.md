---
title: OAuth 2.0을 사용한 Azure AD 서비스 간 인증 | Microsoft Docs
description: 이 문서는 OAuth 2.0 클라이언트 자격 증명 부여 흐름을 사용하여 서비스 간 인증을 구현하기 위해 HTTP 메시지를 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 24c9c4385f23b68e9a3efb65d2582457219fa10d
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164124"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>클라이언트 자격 증명을 사용하여 서비스를 호출하는 서비스(공유 암호 또는 인증서)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2.0 클라이언트 자격 증명 부여 흐름은 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스( *기밀 클라이언트*)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Azure AD는 호출 서비스가 자격 증명으로 인증서(공유 암호 대신)를 사용할 수 있도록 합니다.

## <a name="client-credentials-grant-flow-diagram"></a>클라이언트 자격 증명 부여 흐름 다이어그램
다음 다이어그램에서는 Azure Active Directory(Azure AD)에서 클라이언트 자격 증명 부여 흐름이 작동하는 방법에 대해 설명합니다.

![OAuth 2.0 클라이언트 자격 증명 부여 흐름](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. 클라이언트 애플리케이션은 Azure AD 토큰 발급 엔드포인트 인증하고 액세스 토큰을 요청합니다.
2. Azure AD 토큰 발급 엔드포인트가 액세스 토큰을 발급합니다.
3. 보안 리소스에 인증하는 데 액세스 토큰이 사용됩니다.
4. 보안 리소스의 데이터는 클라이언트 애플리케이션에 반환됩니다.

## <a name="register-the-services-in-azure-ad"></a>Azure AD에서 서비스 등록
Azure AD(Azure Active Directory)에서 호출 서비스와 수신 서비스를 등록합니다. 자세한 지침은 [Azure Active Directory와 애플리케이션 통합](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.

## <a name="request-an-access-token"></a>액세스 토큰 요청
액세스 토큰을 요청하려면 테넌트별 Azure AD 엔드포인트에 HTTP POST를 사용합니다.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>서비스 간 액세스 토큰 요청
클라이언트 애플리케이션이 공유 암호 또는 인증서 중에서 어떤 방식으로 보호되도록 선택되는지에 따라 두 가지 사례가 있습니다.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>첫 번째 사례: 공유 암호를 사용한 액세스 토큰 요청
공유 암호를 사용할 경우 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | Description |
| --- | --- | --- |
| grant_type |required |요청된 부여 유형을 지정합니다. 클라이언트 자격 증명 부여 흐름에서 값은 **client_credentials**이어야 합니다. |
| client_id |required |호출 웹 서비스의 Azure AD 클라이언트 ID를 지정합니다. 호출하는 애플리케이션의 클라이언트 ID를 찾으려면 [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**, **앱 등록** 및 애플리케이션을 차례로 클릭합니다. client_id는 *애플리케이션 ID*입니다. |
| client_secret |required |Azure AD에서 호출 웹 서비스 또는 디먼 애플리케이션에 대해 등록된 키를 입력합니다. 키를 만들려면 Azure Portal에서 **Azure Active Directory**, **앱 등록** 및 애플리케이션, **설정** 및 **키**를 차례로 클릭하고, 키를 추가합니다.  이 비밀을 제공하는 경우 URL로 인코딩합니다. |
| resource |required |수신 웹 서비스의 앱 ID URI를 입력합니다. 앱 ID URI를 찾으려면 Azure Portal에서 **Azure Active Directory**, **앱 등록**, 서비스 애플리케이션 및 **설정**과 **속성**을 차례로 클릭합니다. |

#### <a name="example"></a>예제
다음 HTTP POST는 [ 웹 서비스에 대한 ](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)액세스 토큰 https://service.contoso.com/을 요청합니다. `client_id` 은(는) 액세스 토큰을 요청하는 웹 서비스를 식별합니다.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>두 번째 사례: 인증서를 사용한 액세스 토큰 요청
인증서를 사용한 서비스 간 액세스 토큰 요청에는 다음 매개 변수가 있습니다.

| 매개 변수 |  | Description |
| --- | --- | --- |
| grant_type |required |요청된 응답 유형을 지정합니다. 클라이언트 자격 증명 부여 흐름에서 값은 **client_credentials**이어야 합니다. |
| client_id |required |호출 웹 서비스의 Azure AD 클라이언트 ID를 지정합니다. 호출하는 애플리케이션의 클라이언트 ID를 찾으려면 [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**, **앱 등록** 및 애플리케이션을 차례로 클릭합니다. client_id는 *애플리케이션 ID*입니다. |
| client_assertion_type |required |값은 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`이어야 합니다. |
| client_assertion |required | 애플리케이션의 자격 증명으로 등록한 인증서를 사용하여 만들고 서명해야 하는 어설션(JSON Web Token)입니다. 인증서 등록 방법 및 어설션 형식에 대한 자세한 내용은 [인증서 자격 증명](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.|
| resource | required |수신 웹 서비스의 앱 ID URI를 입력합니다. 앱 ID URI를 찾으려면 Azure Portal에서 **Azure Active Directory**, **앱 등록**, 서비스 애플리케이션 및 **설정**과 **속성**을 차례로 클릭합니다. |

client_secret 매개 변수가 두 개의 매개 변수 client_assertion_type 및 client_assertion으로 바뀐다는 것을 제외하고 공유 비밀에 따른 요청 사례와 매개 변수는 거의 동일합니다.

#### <a name="example"></a>예제
다음 HTTP POST는 인증서를 사용하여 https://service.contoso.com/ 웹 서비스에 대한 액세스 토큰을 요청합니다. `client_id` 은(는) 액세스 토큰을 요청하는 웹 서비스를 식별합니다.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>서비스 간 액세스 토큰 응답

성공 응답에는 다음 매개 변수가 있는 JSON OAuth 2.0 응답이 있습니다.

| 매개 변수 | Description |
| --- | --- |
| access_token |요청된 액세스 토큰입니다. 호출 웹 서비스는 이 토큰을 사용하여 수신 웹 서비스에 인증할 수 있습니다. |
| token_type |토큰 유형 값을 나타냅니다. Azure AD는 **전달자**유형만 지원합니다. 전달자 토큰에 대한 자세한 내용은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)을 참조하세요. |
| expires_in |액세스 토큰이 유효한 기간(초)입니다. |
| expires_on |액세스 토큰이 만료되는 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 만료 시간까지 기간(초)으로 표시됩니다. 이 값은 캐시된 토큰의 수명을 결정하는 데 사용됩니다. |
| not_before |액세스 토큰은 사용할 수 있게 되는 시작 시간입니다. 날짜는 1970-01-01T0:0:0Z UTC부터 토큰 유효 기간까지의 기간(초)으로 표시됩니다.|
| resource |수신 웹 서비스의 앱 ID URI입니다. |

#### <a name="example-of-response"></a>응답 예제
다음 예제는 웹 서비스에 액세스 토큰 요청에 대한 성공 응답을 보여줍니다.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>참고 항목
* [Azure AD의 OAuth 2.0](v1-protocols-oauth-code.md)
* [공유 암호를 사용한 서비스 간 호출의 C# 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon) 및 [인증서를 사용한 서비스 간 호출의 C# 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
