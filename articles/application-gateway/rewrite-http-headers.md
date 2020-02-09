---
title: Azure 애플리케이션 Gateway를 사용 하 여 HTTP 헤더 재작성 | Microsoft Docs
description: 이 문서에서는 Azure 애플리케이션 게이트웨이에서 HTTP 헤더를 다시 작성 하는 방법에 대한 개요를 제공 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: b6f26eca0592017306eaefd3f5fecb544dc6fb36
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932200"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Application Gateway를 사용 하 여 HTTP 헤더 재작성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 헤더를 사용 하면 클라이언트와 서버에서 요청 또는 응답과 함께 추가 정보를 전달할 수 있습니다. 이러한 헤더를 다시 작성 하 여 HSTS/X-XSS 보호와 같은 보안 관련 헤더 필드 추가, 중요 한 정보를 표시할 수 있는 응답 헤더 필드 제거,에서 포트 정보 제거와 같은 중요 한 작업을 수행할 수 있습니다. X 전달-헤더의 경우입니다.

Application Gateway는 요청 및 응답 패킷이 클라이언트와 백 엔드 풀 사이를 이동하는 동안 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다. 뿐만 아니라 특정 조건이 충족되는 경우에만 지정된 헤더를 다시 쓸 수 있도록 조건을 추가할 수 있습니다.

또한 Application Gateway은 요청 및 응답에 대한 추가 정보를 저장 하는 데 도움이 되는 여러 [서버 변수](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) 를 지원 합니다. 이렇게 하면 더 쉽게 강력한 재작성 규칙을 만들 수 있습니다.

> [!NOTE]
>
> HTTP 헤더 재작성 지원은 [Standard_V2 및 WAF_V2 SKU](application-gateway-autoscaling-zone-redundant.md)에서만 사용할 수 있습니다.

![헤더 다시 작성](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>지원 되는 헤더

호스트, 연결 및 업그레이드 헤더를 제외 하 고 요청 및 응답의 모든 헤더를 다시 작성할 수 있습니다. 응용 프로그램 게이트웨이를 사용 하 여 사용자 지정 헤더를 만들어이 헤더를 통해 라우팅되는 요청 및 응답에 추가할 수도 있습니다.

## <a name="rewrite-conditions"></a>다시 쓰기 조건

다시 쓰기 조건을 사용 하 여 HTTP 요청 및 응답의 내용을 평가 하 고 하나 이상의 조건이 충족 될 때만 헤더 재작성을 수행할 수 있습니다. 응용 프로그램 게이트웨이는 이러한 형식의 변수를 사용 하 여 HTTP 요청 및 응답의 콘텐츠를 평가 합니다.

- 요청에 있는 HTTP 헤더입니다.
- 응답의 HTTP 헤더입니다.
- 서버 변수를 Application Gateway 합니다.

조건을 사용 하 여 지정 된 변수가 있는지, 지정 된 변수가 특정 값과 일치 하는지 여부 또는 지정 된 변수가 특정 패턴과 일치 하는지 여부를 평가할 수 있습니다. [Perl 호환 정규식 (PCRE) 라이브러리](https://www.pcre.org/) 를 사용 하 여 조건에서 정규식 패턴 일치를 설정 합니다. 정규식 구문에 대한 자세한 내용은 [Perl 정규식 기본 페이지](https://perldoc.perl.org/perlre.html)를 참조 하세요.

## <a name="rewrite-actions"></a>작업 다시 작성

다시 작성 작업을 사용 하 여 재작성 하려는 요청 및 응답 헤더와 헤더의 새 값을 지정 합니다. 새 헤더를 만들거나 기존 헤더의 값을 수정 하거나 기존 헤더를 삭제할 수 있습니다. 새 헤더 또는 기존 헤더의 값은 다음과 같은 형식 값으로 설정할 수 있습니다.

- 텍스트입니다.
- 요청 헤더. 요청 헤더를 지정 하려면 {http_req_*헤드 ername*} 구문을 사용 해야 합니다.
- 응답 헤더입니다. 응답 헤더를 지정 하려면 {http_resp_*헤드 ername*} 구문을 사용 해야 합니다.
- 서버 변수입니다. 서버 변수를 지정 하려면 {var_*Servervariable*} 구문을 사용 해야 합니다.
- 텍스트, 요청 헤더, 응답 헤더 및 서버 변수의 조합입니다.

## <a name="server-variables"></a>서버 변수

Application Gateway 서버 변수를 사용 하 여 서버에 대한 유용한 정보, 클라이언트에 대한 연결 및 현재 연결 요청을 저장 합니다. 저장 되는 정보의 예로는 클라이언트의 IP 주소와 웹 브라우저 유형이 있습니다. 서버 변수는 새 페이지가 로드 될 때 또는 양식이 게시 될 때와 같이 동적으로 변경 됩니다. 이러한 변수를 사용 하 여 재작성 조건을 평가 하 고 헤더를 다시 작성할 수 있습니다.

Application gateway는 다음 서버 변수를 지원 합니다.

| 변수 이름 | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | I p 1, IP2, p 3 등의 형식으로 추가 된 ( `client_ip` 이 표의 뒷부분에 나오는 설명 참조) 변수와 함께 X로 전달 된 클라이언트 요청 헤더 필드입니다. X 전달-For 필드가 클라이언트 요청 헤더 `add_x_forwarded_for_proxy` 에 없으면 변수는 `$client_ip` 변수와 같습니다. 이 변수는 헤더에 포트 정보가 없는 IP 주소만 포함 되도록 Application Gateway에 의해 설정 된 X 전달 된 헤더를 다시 작성 하려는 경우에 특히 유용 합니다. |
| ciphers_supported          | 클라이언트에서 지 원하는 암호화 목록입니다.          |
| ciphers_used               | 설정 된 SSL 연결에 사용 되는 암호화 문자열입니다. |
| client_ip                  | 응용 프로그램 게이트웨이에서 요청을 받은 클라이언트의 IP 주소입니다. 응용 프로그램 게이트웨이와 원래 클라이언트 앞에 역방향 프록시가 있는 경우 *client_ip* 은 역방향 프록시의 ip 주소를 반환 합니다. |
| client_port                | 클라이언트 포트입니다.                                                  |
| client_tcp_rtt             | 클라이언트 TCP 연결에 대한 정보입니다. TCP_INFO 소켓 옵션을 지 원하는 시스템에서 사용할 수 있습니다. |
| client_user                | HTTP 인증을 사용 하는 경우 인증을 위해 제공 되는 사용자 이름입니다. |
| 호스트                       | 이 우선 순위 순서 대로: 요청 줄의 호스트 이름, 호스트 요청 헤더 필드의 호스트 이름 또는 요청과 일치 하는 서버 이름입니다. |
| cookie_*name*              | *이름* 쿠키입니다.                                            |
| http_method                | URL 요청을 만드는 데 사용 되는 메서드입니다. 예를 들어 GET 또는 POST입니다. |
| http_status                | 세션 상태입니다. 예: 200, 400 또는 403.                       |
| http_version               | 요청 프로토콜입니다. 일반적으로 HTTP/1.0, HTTP/1.1 또는 HTTP/2.0입니다. |
| query_string               | 요청 된 URL에서 "?" 뒤에 오는 변수/값 쌍의 목록입니다. |
| received_bytes             | 요청 (요청 줄, 헤더 및 요청 본문 포함)의 길이입니다. |
| request_query              | 요청 줄의 인수입니다.                                |
| request_scheme             | 요청 체계: http 또는 https.                            |
| request_uri                | 전체 원래 요청 URI (인수 포함)입니다.                   |
| sent_bytes                 | 클라이언트에 전송 된 바이트 수입니다.                             |
| server_port                | 요청을 수락한 서버 포트입니다.                 |
| ssl_connection_protocol    | 설정 된 SSL 연결의 프로토콜입니다.        |
| ssl_enabled                | 연결이 SSL 모드에서 작동 하는 경우 "On"입니다. 그렇지 않으면 빈 문자열입니다. |

## <a name="rewrite-configuration"></a>구성 재작성

HTTP 헤더 재작성을 구성 하려면 다음 단계를 완료 해야 합니다.

1. HTTP 헤더 재작성에 필요한 개체를 만듭니다.

   - **다시 작성 작업**: 다시 작성 하려는 요청 및 요청 헤더 필드와 헤더의 새 값을 지정 하는 데 사용 됩니다. 재작성 작업과 하나 이상의 재작성 조건을 연결할 수 있습니다.

   - **다시 작성 조건**: 선택적 구성입니다. 다시 쓰기 조건은 HTTP 요청 및 응답의 콘텐츠를 평가 합니다. HTTP (S) 요청 또는 응답이 재작성 조건과 일치 하면 재작성 작업이 수행 됩니다.

     작업에 둘 이상의 조건을 연결 하는 경우 모든 조건이 충족 되는 경우에만 작업이 수행 됩니다. 즉, 작업은 논리적 AND 연산입니다.

   - **재작성 규칙**: 여러 재작성 작업/재작성 조건 조합이 포함 됩니다.

   - **규칙 시퀀스**: 재작성 규칙의 실행 순서를 결정 하는 데 도움이 됩니다. 이 구성은 재작성 집합에 다시 쓰기 규칙이 여러 개 있는 경우에 유용 합니다. 규칙 시퀀스 값이 낮은 재작성 규칙이 먼저 실행 됩니다. 두 재작성 규칙에 동일한 규칙 시퀀스를 할당 하는 경우 실행 순서는 비결 정적입니다.

   - **재작성 집합**: 에는 요청 라우팅 규칙과 연결 되는 재작성 규칙이 여러 개 포함 되어 있습니다.

2. 재작성 집합 (*rewriteRuleSet*)을 라우팅 규칙에 연결 합니다. 재작성 구성은 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 원본 수신기와 연결 되며 전역 헤더 재작성입니다. 경로 기반 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다.
   > [!NOTE]
   > URL 다시 쓰기는 헤더를 변경 합니다. 경로에 대한 URL을 변경 하지 않습니다.

여러 HTTP 헤더 재작성 집합을 만들고 각 재작성 집합을 여러 수신기에 적용할 수 있습니다. 그러나 특정 수신기에 재작성 집합을 하나만 적용할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

헤더 재작성 사용에 대한 몇 가지 일반적인 시나리오는 다음과 같습니다.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>X 전달 된 헤더에서 포트 정보를 제거 합니다.

Application Gateway는 백 엔드에 요청을 전달 하기 전에 모든 요청에 X 전달 된 헤더를 삽입 합니다. 이 헤더는 IP 포트를 쉼표로 구분한 목록입니다. 백 엔드 서버에는 IP 주소를 포함 하는 머리글만 필요 합니다. 헤더 재작성을 사용 하 여 X 전달 된 헤더에서 포트 정보를 제거할 수 있습니다. 이 작업을 수행 하는 한 가지 방법은 add_x_forwarded_for_proxy 서버 변수로 헤더를 설정 하는 것입니다.

![포트 제거](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>리디렉션 URL 수정

백 엔드 응용 프로그램에서 리디렉션 응답을 보내는 경우 백 엔드 응용 프로그램에서 지정한 URL과 다른 URL로 클라이언트를 리디렉션할 수 있습니다. 예를 들어 app service가 응용 프로그램 게이트웨이 뒤에서 호스트 되는 경우이 작업을 수행 하 고 클라이언트가 상대 경로에 대한 리디렉션을 수행 해야 할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로의 리디렉션이 있습니다.

App Service은 다중 테넌트 서비스 이므로 요청에서 호스트 헤더를 사용 하 여 요청을 올바른 엔드포인트으로 라우팅합니다. App services의 기본 도메인 이름은 application gateway의 도메인 이름과 다른 *. azurewebsites.net (contoso.azurewebsites.net)입니다 (예를 들어 contoso.com). 클라이언트의 원래 요청에는 호스트 이름으로 응용 프로그램 게이트웨이의 도메인 이름 (contoso.com)이 있으므로 application gateway는 호스트 이름을 contoso.azurewebsites.net로 변경 합니다. App service가 요청을 올바른 엔드포인트으로 라우팅할 수 있도록이 변경을 수행 합니다.

App service는 리디렉션 응답을 보낼 때 응용 프로그램 게이트웨이에서 수신 하는 요청의 위치 헤더에 있는 것과 동일한 호스트 이름을 사용 합니다. 따라서 클라이언트는 application gateway (contoso.com/path2)를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청을 만듭니다. Application gateway를 무시 하는 것은 바람직하지 않습니다.

Location 헤더의 호스트 이름을 application gateway의 도메인 이름으로 설정 하 여이 문제를 해결할 수 있습니다.

호스트 이름을 교체 하는 단계는 다음과 같습니다.

1. 응답의 location 헤더에 azurewebsites.net가 포함 되어 있는지 여부를 평가 하는 다시 쓰기 규칙을 만듭니다. 패턴 `(https?):\/\/.*azurewebsites\.net(.*)$`을 입력 합니다.
1. 응용 프로그램 게이트웨이의 호스트 이름을 포함 하도록 위치 헤더를 다시 작성 하는 작업을 수행 합니다. 헤더 값으로를 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 입력 하 여이 작업을 수행 합니다.

![위치 헤더 수정](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>취약성을 방지 하기 위해 보안 HTTP 헤더 구현

응용 프로그램 응답에 필요한 헤더를 구현 하 여 여러 보안 취약점을 해결할 수 있습니다. 이러한 보안 헤더에는 X-XSS 보호, 엄격한 전송 보안 및 콘텐츠 보안 정책이 포함 됩니다. Application Gateway를 사용 하 여 모든 응답에 대해 이러한 헤더를 설정할 수 있습니다.

![보안 헤더](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>원치 않는 머리글 삭제

HTTP 응답에서 중요 한 정보를 표시 하는 헤더를 제거 하는 것이 좋습니다. 예를 들어 백 엔드 서버 이름, 운영 체제 또는 라이브러리 세부 정보와 같은 정보를 제거 하는 것이 좋습니다. Application gateway를 사용 하 여 이러한 헤더를 제거할 수 있습니다.

![헤더 삭제](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>헤더가 있는지 확인 합니다.

헤더 또는 서버 변수가 있는지에 대한 HTTP 요청 또는 응답 헤더를 평가할 수 있습니다. 이 계산은 특정 헤더가 있는 경우에만 헤더 재작성을 수행 하려는 경우에 유용 합니다.

![헤더가 있는지 확인 하는 중](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>제한 사항

- 응답에 이름이 같은 헤더가 두 개 이상 있는 경우 해당 헤더 중 하나의 값을 다시 작성 하면 응답의 다른 헤더가 삭제 됩니다. 이는 일반적으로 응답에 두 개 이상의 쿠키 헤더를 사용할 수 있으므로 설정-쿠키 헤더에서 발생할 수 있습니다. 이러한 시나리오 중 하나는 응용 프로그램 게이트웨이와 함께 app service를 사용 하 고 응용 프로그램 게이트웨이에서 쿠키 기반 세션 선호도를 구성 하는 경우입니다. 이 경우 응답에는 app service에서 사용 하는 두 개의 설정-쿠키 헤더 (예:)와 application `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` gateway 선호도 ( `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`예:)가 포함 됩니다. 이 시나리오에서 Set 쿠키 헤더 중 하나를 다시 작성 하면 응답에서 다른 Set 쿠키 헤더가 제거 될 수 있습니다.

- 연결, 업그레이드 및 호스트 헤더 재작성은 현재 지원 되지 않습니다.

- 헤더 이름에는 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)에 정의 된 대로 영숫자 문자와 특정 기호가 포함 될 수 있습니다. 현재 헤더 이름에 밑줄 (\_) 특수 문자를 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

HTTP 헤더를 다시 작성 하는 방법에 대한 자세한 내용은 다음을 참조 하세요.

- [Azure Portal를 사용 하 여 HTTP 헤더 재작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Azure PowerShell를 사용 하 여 HTTP 헤더 재작성](add-http-header-rewrite-rule-powershell.md)
