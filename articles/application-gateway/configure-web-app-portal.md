---
title: 포털을 사용 하 여 다중 테넌트 앱에 대한 트래픽 관리
titleSuffix: Azure Application Gateway
description: 이 문서에서는 기존 또는 새 응용 프로그램 게이트웨이에서 백 엔드 풀의 멤버로 Azure 앱 서비스 웹 앱을 구성 하는 방법에 대한 지침을 제공 합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075174"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway를 사용하여 App Service 구성

App service는 전용 배포가 아닌 다중 테넌트 서비스 이므로 들어오는 요청의 호스트 헤더를 사용 하 여 올바른 app service 엔드포인트에 대한 요청을 확인 합니다. 일반적으로 응용 프로그램의 DNS 이름은 app service 앞단에 application gateway와 연결 된 DNS 이름으로, 백엔드 app service의 도메인 이름과 다릅니다. 따라서 application gateway에서 받은 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 동일 하지 않습니다. 이 때문에 응용 프로그램 게이트웨이에서 백 엔드에 대한 요청에 있는 호스트 헤더를 백 엔드 서비스의 호스트 이름으로 변경 하지 않는 한 다중 테넌트 백 엔드는 해당 요청을 올바른 엔드포인트으로 확인할 수 없습니다.

Application Gateway은 요청을 Application Gateway에서 백 엔드로 라우팅하는 경우 요청의 호스트 헤더를 백 엔드의 호스트 이름과 재정의 하는 `Pick host name from backend address` 라는 스위치를 제공 합니다. 이 기능을 사용 하면 Azure app service 및 API management와 같은 다중 테넌트 백 엔드를 지원할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> - 백 엔드 풀을 만들고 여기에 App Service 추가
> - "호스트 선택" 스위치를 사용 하 여 HTTP 설정 및 사용자 지정 프로브 만들기

## <a name="prerequisites"></a>선행 조건

- Application gateway: 기존 application gateway가 없는 경우 [응용 프로그램 게이트웨이를 만드는](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) 방법을 참조 하세요.
- App service: 기존 App service가 없는 경우 [app service 설명서](https://docs.microsoft.com/azure/app-service/)를 참조 하세요.

## <a name="add-app-service-as-backend-pool"></a>백 엔드 풀로 App service 추가

1. Azure Portal에서 application gateway의 구성 뷰를 엽니다.

2. **백 엔드 풀**에서 **추가** 를 클릭 하 여 새 백 엔드 풀을 만듭니다.

3. 백 엔드 풀에 적절 한 이름을 제공 합니다. 

4. **대상**에서 드롭다운을 클릭 하 고 옵션으로 **App Services** 를 선택 합니다.

5. **대상** 드롭다운 바로 아래에 App Services 목록이 포함 된 드롭다운이 표시 됩니다. 이 드롭다운에서 백 엔드 풀 멤버로 추가 하려는 App Service를 선택 하 고 추가를 클릭 합니다.

   ![App service 백 엔드](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 드롭다운은 Application Gateway와 동일한 구독에 있는 앱 서비스만 채웁니다. Application Gateway 아닌 다른 구독에 있는 앱 서비스를 사용 하려는 경우 **대상** 드롭다운에서 **App Services** 를 선택 하는 대신 **IP 주소 또는 호스트 이름** 옵션을 선택 하 고 호스트 이름 (예:)을 입력 합니다. azurewebsites.net)를 다운로드 합니다.

## <a name="create-http-settings-for-app-service"></a>App service에 대한 HTTP 설정 만들기

1. **Http 설정**에서 **추가** 를 클릭 하 여 새 http 설정을 만듭니다.

2. HTTP 설정의 이름을 입력 하 고 요구 사항에 따라 쿠키 기반 선호도를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

3. 사용 사례에 따라 프로토콜을 HTTP 또는 HTTPS로 선택 합니다. 

   > [!NOTE]
   > HTTPS를 선택 하는 경우 app service가 신뢰할 수 있는 Azure 서비스 이므로 app service 백 엔드를 허용 목록 인증 인증서 또는 신뢰할 수 있는 루트 인증서를 업로드할 필요가 없습니다.

4. **App Service에 사용할** 확인란을 선택 합니다. 스위치 `Create a probe with pick host name from backend address` 및 `Pick host name from backend address`는 자동으로 사용 하도록 설정 됩니다.`Pick host name from backend address` 는 요청이 Application Gateway에서 백 엔드로 전달 될 때 백 엔드의 호스트 이름을 사용 하 여 요청의 호스트 헤더를 재정의 합니다.  

   `Create a probe with pick host name from backend address`에서 자동으로 상태 프로브를 만들고이를이 HTTP 설정에 연결 합니다. 이 HTTP 설정에 대해 다른 상태 프로브를 만들 필요가 없습니다. <HTTP Setting name><Unique GUID> 이름이 있는 새 프로브는 상태 프로브 목록에 추가 되었으며 이미 스위치 `Pick host name from backend http settings enabled`있는지 확인할 수 있습니다.

   이미 하나 이상의 HTTP 설정이 App service에 사용 되 고 있는 경우 해당 HTTP 설정이 사용자가 만드는 항목에서 사용 중인 것과 동일한 프로토콜을 사용 하는 경우 `Create a probe with pick host name from backend address` 스위치 대신 사용자 지정 프로브 중 하나를 선택할 수 있는 드롭다운이 표시 됩니다. 이는 app service를 사용 하 여 HTTP 설정이 이미 있으므로 스위치 `Pick host name from backend http settings enabled` 있는 상태 프로브도 존재 하기 때문입니다. 드롭다운에서 해당 사용자 지정 프로브를 선택 합니다.

5. **확인** 을 클릭 하 여 HTTP 설정을 만듭니다.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>수신기, 백 엔드 풀 및 HTTP 설정을 연결 하는 규칙 만들기

1. **규칙**에서 **기본** 을 클릭 하 여 새 기본 규칙을 만듭니다.

2. 적절 한 이름을 제공 하 고 App service에 대한 들어오는 요청을 수락 하는 수신기를 선택 합니다.

3. **백 엔드 풀** 드롭다운에서 위에서 만든 백 엔드 풀을 선택 합니다.

4. **Http 설정** 드롭다운에서 위에서 만든 http 설정을 선택 합니다.

5. **확인** 을 클릭 하 여이 규칙을 저장 합니다.

   ![규칙](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>App service의 상대 경로로 리디렉션하는 경우 추가 구성

App service가 상대 경로 (예: contoso.azurewebsites.net/path1에서 contoso.azurewebsites.net/path2로 리디렉션)로 리디렉션하는 리디렉션 응답을 클라이언트에 보낼 때 응용 프로그램 게이트웨이에서 받은 요청의 위치 헤더에 있는 것과 동일한 호스트 이름을 사용 합니다. 따라서 클라이언트는 application gateway (contoso.com/path2)를 통하지 않고 contoso.azurewebsites.net/path2에 직접 요청을 만듭니다. Application gateway를 무시 하는 것은 바람직하지 않습니다.

사용 사례에서 응용 프로그램 서비스가 클라이언트에 리디렉션 응답을 보내야 하는 경우에는 추가 단계를 수행 하 여 [위치 헤더를 다시 작성](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)해야 합니다.

## <a name="restrict-access"></a>액세스 제한

이 예제에서 배포된 웹앱은 인터넷에서 직접 액세스할 수 있는 공용 IP 주소를 사용합니다. 따라서 새로운 기능을 배우고 새로운 것을 시도할 때 문제를 해결하는 데 도움이 됩니다. 하지만 프로덕션 환경에 기능을 배포하려는 경우 더 많은 제한을 추가해야 합니다.

웹앱에 대한 액세스를 제한할 수 있는 한 가지 방법은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 사용하는 것입니다. 웹앱이 애플리케이션 게이트웨이의 트래픽만 수신하도록 웹앱을 제한할 수 있습니다. 앱 서비스 IP 제한 기능을 사용하여 액세스할 수 있는 유일한 주소로 애플리케이션 게이트웨이 VIP를 나열할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Application gateway를 사용 하 여 App service 및 기타 다중 테넌트 지원에 대한 자세한 내용은 [application gateway를 사용 하 여 다중 테넌트 서비스 지원](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)을 참조 하세요.
