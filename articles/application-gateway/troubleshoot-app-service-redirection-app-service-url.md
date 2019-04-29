---
title: App Service-App Service의 URL로 리디렉션을 사용 하 여 Azure Application Gateway 문제 해결
description: 이 문서에서는 Azure Application Gateway는 Azure App Service를 사용 하는 경우 리디렉션 문제를 해결 하는 방법에 대해 설명
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: f456cfec82a315a2be877a52e4f3f1850b992736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715200"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>App Service를 사용 하 여 응용 프로그램 게이트웨이 문제 해결

진단 및 응용 프로그램 게이트웨이와 백 엔드 서버와 App Service를 사용 하 여 발생 하는 문제를 해결 하는 방법에 알아봅니다.

## <a name="overview"></a>개요

이 문서에서는 다음과 같은 문제를 해결 하는 방법에 배웁니다.

> [!div class="checklist"]
> * App Service의 URL 리디렉션 때 브라우저에 노출 하기
> * App Service 호스트 이름 대신 원래 호스트 (example.azurewebsites.net)로 설정 하는 app Service의 ARRAffinity 쿠키 도메인

공용 응용 프로그램 게이트웨이의 백 엔드 풀에서 App Service를 구성 하 고 리디렉션을 응용 프로그램 코드에 구성 된 경우 표시 될 수 있습니다 하는 Application Gateway를 액세스 하는 경우, 리디렉션됩니다 브라우저에서 앱에 직접 서비스 URL입니다.

이 문제는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

- 리디렉션 App Service에서 구성 해야 합니다. 리디렉션 요청에 후행 슬래시를 추가 하는 것으로 간단할 수 있습니다.
- 리디렉션되는 Azure AD 인증을 해야 합니다.
- Application Gateway의 HTTP 설정에서 "백 엔드 주소에서 호스트 이름을 선택" 스위치를 설정한 경우.
- 앱 서비스에 등록 된 사용자 지정 도메인이 필요는 없습니다.

또한 Application Gateway 뒤의 App Services를 사용 하는 사용자 지정 도메인을 사용 하 여 응용 프로그램 게이트웨이에 액세스 하는 경우 App Service에서 설정한 ARRAffinity 쿠키에 대 한 도메인 값 "example.azurewebsites.net" 도메인 이름에 전달 됩니다 표시 될 수 있습니다. 쿠키 도메인도 되도록 원래 호스트 이름, 원하는 경우이 문서에서 솔루션을 수행 합니다.

## <a name="sample-configuration"></a>샘플 구성

- HTTP 수신기: 기본 또는 다중 사이트
- 백 엔드 주소 풀: App Service
- HTTP 설정: "백 엔드 주소에서 호스트 이름 선택"이 사용 하도록 설정
- 프로브: "HTTP 설정에서 호스트 이름 선택"이 사용 하도록 설정

## <a name="cause"></a>원인

App Service만 액세스할 수 있으며 사용자 지정 도메인 설정에 구성 된 호스트 이름을 사용 하 여 기본적으로, "example.azurewebsites.net" 고 또는 App Service에 등록 되지 않은 호스트를 사용 하 여 Application Gateway를 사용 하 여 App Service에 액세스 하려는 경우 Application Gateway의 App Service의 호스트 이름 원래 요청에 호스트 이름을 재정의 해야 하는 FQDN입니다.

Application Gateway를 사용 하 여이 위해,를 사용 하 여 스위치 "백 엔드 주소에서 선택 호스트 이름" HTTP 설정에 프로브에 대 한 작업, 프로브 구성의 "선택 호스트 이름에서 백 엔드 HTTP 설정"을 사용 했습니다.

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

이 인해 App Service는 리디렉션을 수행 하는 경우 사용 하 여 호스트 이름 "example.azurewebsites.net" Location 헤더의 원본 호스트 이름 대신 별도로 구성 하지. 아래 예제 요청 및 응답 헤더를 확인할 수 있습니다.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
위의 예제에서는 응답 헤더에는 상태 코드 of 301 리디렉션 위한을 location 헤더에는 App Service의 호스트 이름을 원본 호스트 이름 대신 "www.contoso.com"을 확인할 수 있습니다.

## <a name="solution"></a>해결 방법

호스트 재정의 수행 하는 대신도 App Service에 응용 프로그램 게이트웨이 수신 하는 동일한 호스트 헤더를 전달 해야에서는 가능 하지 않은 경우 단, 응용 프로그램 쪽에서 리디렉션 없이이 문제를 해결할 수 있습니다.

그 후 App Service는 리디렉션을 수행 (있는 경우) Application Gateway를 가리키는 동일한 원래 호스트 헤더에 아니라 자체입니다.

이 위해 사용자 지정 도메인을 소유 하며 아래에 설명 된 프로세스를 따릅니다.

- App Service의 사용자 지정 도메인 목록에 도메인을 등록 합니다. 이 위해 App Service의 FQDN을 가리키는 사용자 지정 도메인에서 CNAME을 해야 합니다. 자세한 내용은 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)합니다.

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 완료 되 면 App Service가 "www.contoso.com" 호스트를 받아들일 준비가 되었습니다. 이제 Application Gateway의 FQDN으로 다시 가리키도록 DNS의 CNAME 항목을 변경 합니다. 예를 들어, "appgw.eastus.cloudapp.azure.com"가 있습니다.

- 도메인 "www.contoso.com" Application Gateway의 FQDN에 DNS 쿼리를 수행할 때 확인 해야 합니다.

- "선택 호스트 이름에서 백 엔드 HTTP 설정"을 사용 하지 않도록 설정 하 여 사용자 지정 프로브를 설정 합니다. 이 프로브 설정에 대 한 확인란의 선택을 취소 하 여 포털에서 수행할 수 있습니다 하 고-PickHostNameFromBackendHttpSettings를 사용 하지 않으면 PowerShell에서 집합 AzApplicationGatewayProbeConfig 명령에서 전환 합니다. 검색의 호스트 이름 필드에 입력에 App Service의 FQDN "example.azurewebsites.net" Application Gateway에서 전송 된 프로브 요청이 호스트 헤더에 전달 됩니다.

  > [!NOTE]
  > 다음 단계를 수행 하는 동안 아닌지 사용자 지정 프로브가 백 엔드 HTTP 설정에 관련 HTTP 설정을 계속 하는 "백 엔드 주소에서 선택 호스트 이름" 스위치가 시점에서 설정 되어 있어을 확인 하세요.

- Application Gateway의 HTTP 설정 "선택 호스트 이름에서 백 엔드 주소"를 사용 하지 않도록 설정 합니다. 이 확인란을 선택 취소 하 여 포털에서 수행할 수 있습니다 하 고 사용 하지 않으면 PowerShell에서-PickHostNameFromBackendAddress 집합 AzApplicationGatewayBackendHttpSettings 명령에서 전환 합니다.

- 사용자 지정 프로브는 백 엔드 HTTP 설정으로 다시 연결 하 고 정상 상태 이면 백 엔드 상태를 확인 합니다.

- 이 작업이 완료 되 면 Application Gateway App Service에 이제 동일한 호스트 이름 "www.contoso.com"을 전달 해야 및 동일한 호스트에서 리디렉션이 발생 합니다. 아래 예제 요청 및 응답 헤더를 확인할 수 있습니다.

기존 설치에 대 한 PowerShell을 사용 하 여 위에서 언급 한 단계를 구현 하려면 아래 샘플 PowerShell 스크립트를 수행 합니다. Note 방법에서는 사용 하지 않은-PickHostname 스위치 프로브 및 HTTP 설정을 구성 합니다.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
