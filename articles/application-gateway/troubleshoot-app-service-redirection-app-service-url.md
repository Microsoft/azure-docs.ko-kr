---
title: App Service URL로 리디렉션 문제 해결
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure 애플리케이션 Gateway를 사용할 때 리디렉션 문제를 해결 하는 방법에 대한 정보를 제공 Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: d43efd6dbd344f666c23b1ad4414ceb29992e996
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074489"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>App Service 문제 해결 Application Gateway

Azure 애플리케이션 게이트웨이의 백 엔드 대상으로 Azure App Service 사용 되는 경우 발생할 수 있는 문제를 진단 하 고 해결 하는 방법을 알아봅니다.

## <a name="overview"></a>개요

이 문서에서는 다음과 같은 문제를 해결 하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 리디렉션 시 app service URL은 브라우저에 노출 됩니다.
> * App service ARRAffinity cookie 도메인은 원래 호스트 대신 app service 호스트 이름인 example.azurewebsites.net로 설정 됩니다.

백 엔드 응용 프로그램에서 리디렉션 응답을 보내는 경우 백 엔드 응용 프로그램에서 지정한 URL과 다른 URL로 클라이언트를 리디렉션할 수 있습니다. App service가 응용 프로그램 게이트웨이 뒤에서 호스트 되는 경우이 작업을 수행할 수 있으며, 클라이언트가 상대 경로에 대한 리디렉션을 수행 해야 할 수 있습니다. 예를 들어 contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로의 리디렉션이 있습니다. 

App service는 리디렉션 응답을 보낼 때 응용 프로그램 게이트웨이에서 수신 하는 요청의 응답 위치 헤더에 있는 것과 동일한 호스트 이름을 사용 합니다. 예를 들어 클라이언트는 application gateway contoso.com/path2를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청을 만듭니다. 응용 프로그램 게이트웨이를 무시 하지 않으려고 합니다.

이 문제는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

- 앱 서비스에 리디렉션이 구성 되어 있습니다. 리디렉션은 요청에 후행 슬래시를 추가 하는 것 처럼 간단할 수 있습니다.
- 리디렉션을 유발 하는 Azure Active Directory 인증이 있습니다.

또한 응용 프로그램 게이트웨이 뒤에 있는 app services를 사용 하는 경우 응용 프로그램 게이트웨이 (example.com)와 연결 된 도메인 이름은 app service의 도메인 이름 (예 example.azurewebsites.net)과 다릅니다. App service에 의해 설정 된 ARRAffinity 쿠키의 도메인 값에 example.azurewebsites.net 도메인 이름이 전달 됩니다 .이는 바람직하지 않습니다. 원래 호스트 이름인 example.com은 쿠키의 도메인 이름 값 이어야 합니다.

## <a name="sample-configuration"></a>샘플 구성

- HTTP 수신기: 기본 또는 다중 사이트
- 백 엔드 주소 풀: App Service
- HTTP 설정: **백 엔드 주소에서 호스트 이름 선택**
- 프로브: **HTTP 설정에서 호스트 이름 선택**

## <a name="cause"></a>원인

App Service은 다중 테넌트 서비스 이므로 요청에서 호스트 헤더를 사용 하 여 요청을 올바른 엔드포인트으로 라우팅합니다. App Services *. azurewebsites.net (contoso.azurewebsites.net)의 기본 도메인 이름은 application gateway의 도메인 이름 (예를 들어 contoso.com)과 다릅니다. 

클라이언트의 원래 요청에는 호스트 이름으로 응용 프로그램 게이트웨이의 도메인 이름 contoso.com이 있습니다. 앱 서비스 백 엔드에 요청을 라우팅하는 경우 원래 요청의 호스트 이름을 app service의 호스트 이름으로 변경 하도록 응용 프로그램 게이트웨이를 구성 해야 합니다. Application gateway의 HTTP 설정 구성에서 **백 엔드 주소의 백 호스트 선택** 스위치를 사용 합니다. 상태 프로브 구성의 **백 엔드에서 호스트 선택 HTTP 설정** 스위치를 사용 합니다.



![Application gateway에서 호스트 이름 변경](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

App service는 리디렉션을 수행 하는 경우, 달리 구성 되지 않은 한 원래 호스트 이름 contoso.com 대신 location 헤더에 재정의 된 호스트 이름 contoso.azurewebsites.net을 사용 합니다. 다음 예제 요청 및 응답 헤더를 확인 합니다.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
이전 예제에서 응답 헤더의 상태 코드는 리디렉션에 대해 301입니다. Location 헤더에는 `www.contoso.com`원래 호스트 이름 대신 app service의 호스트 이름이 있습니다.

## <a name="solution-rewrite-the-location-header"></a>해결 방법: 위치 헤더 다시 작성

Location 헤더의 호스트 이름을 application gateway의 도메인 이름으로 설정 합니다. 이렇게 하려면 응답의 location 헤더에 azurewebsites.net가 포함 되어 있는지 여부를 평가 하는 [다시 쓰기 규칙](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) 을 만듭니다. 또한 응용 프로그램 게이트웨이의 호스트 이름을 갖도록 location 헤더를 다시 작성 하는 작업을 수행 해야 합니다. 자세한 내용은 [location 헤더를 다시 작성 하는 방법](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)에 대한 지침을 참조 하세요.

> [!NOTE]
> HTTP 헤더 재작성 지원은 Application Gateway의 [Standard_v2 및 WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 에서만 사용할 수 있습니다. V1 SKU를 사용 하는 경우 v 1에서 v 2 [로 마이그레이션하](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)는 것이 좋습니다. V2 SKU에서 사용할 수 있는 재작성 및 기타 [고급 기능](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) 을 사용 하려고 합니다.

## <a name="alternate-solution-use-a-custom-domain-name"></a>대체 솔루션: 사용자 지정 도메인 이름 사용

V1 SKU를 사용 하는 경우에는 location 헤더를 다시 작성할 수 없습니다. 이 기능은 v2 SKU에만 사용할 수 있습니다. 리디렉션 문제를 해결 하려면 호스트 재정의를 수행 하는 대신 응용 프로그램 게이트웨이에서 수신 하는 것과 동일한 호스트 이름을 app service에 전달 합니다.

이제 app service는 동일한 원래 호스트 헤더에 대한 리디렉션 (있는 경우)을 수행 합니다 .이는 응용 프로그램 게이트웨이를 가리키지만 자체는 그렇지 않습니다.

사용자 지정 도메인을 소유 하 고이 프로세스를 수행 해야 합니다.

- App service의 사용자 지정 도메인 목록에 도메인을 등록 합니다. 사용자 지정 도메인에 app service의 FQDN을 가리키는 CNAME이 있어야 합니다. 자세한 내용은 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)을 참조 하세요.

    ![App service 사용자 지정 도메인 목록](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- App service에서 호스트 이름 `www.contoso.com`를 받아들일 준비가 되었습니다. DNS에서 CNAME 항목을 변경 하 여 응용 프로그램 게이트웨이의 FQDN (예: `appgw.eastus.cloudapp.azure.com`)으로 다시 가리키도록 합니다.

- DNS 쿼리를 수행할 때 도메인 `www.contoso.com` 응용 프로그램 게이트웨이의 FQDN으로 확인 되는지 확인 합니다.

- 사용자 지정 프로브를 설정 하 여 **백 엔드 HTTP 설정에서 호스트 이름 선택**을 사용 하지 않도록 설정 합니다. Azure Portal에서 프로브 설정의 확인란을 선택 취소 합니다. PowerShell에서 **AzApplicationGatewayProbeConfig** 명령에 **-PickHostNameFromBackendHttpSettings** 스위치를 사용 하지 마세요. 프로브의 호스트 이름 필드에 app service의 FQDN, example.azurewebsites.net를 입력 합니다. Application gateway에서 전송 된 프로브 요청은 호스트 헤더에이 FQDN을 포함 합니다.

  > [!NOTE]
  > 다음 단계에서 사용자 지정 프로브가 백 엔드 HTTP 설정에 연결 되지 않았는지 확인 합니다. 현재 HTTP 설정에는이 시점에서 사용 가능한 **백 엔드 주소에서 호스트 선택** 스위치가 있습니다.

- **백 엔드 주소에서 호스트 이름 선택**을 사용 하지 않도록 응용 프로그램 게이트웨이의 HTTP 설정을 설정 합니다. Azure Portal에서 확인란의 선택을 취소 합니다. PowerShell에서 **AzApplicationGatewayBackendHttpSettings** 명령에 **-PickHostNameFromBackendAddress** 스위치를 사용 하지 마세요.

- 사용자 지정 프로브를 백 엔드 HTTP 설정에 다시 연결 하 고 백 엔드가 정상 상태 인지 확인 합니다.

- 이제 응용 프로그램 게이트웨이에서 앱 서비스에 동일한 호스트 이름 `www.contoso.com`를 전달 해야 합니다. 리디렉션이 동일한 호스트 이름에서 발생 합니다. 다음 예제 요청 및 응답 헤더를 확인 합니다.

기존 설치를 위해 PowerShell을 사용 하 여 이전 단계를 구현 하려면 다음에 나오는 샘플 PowerShell 스크립트를 사용 합니다. 프로브 및 HTTP 설정 구성에서 **-PickHostname** 스위치를 사용 하지 않은 경우를 확인 합니다.

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

위의 단계를 수행 해도 문제가 해결 되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
