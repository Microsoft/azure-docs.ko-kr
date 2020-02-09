---
title: 다중 테넌트 백 엔드
titleSuffix: Azure Application Gateway
description: 이 페이지에서는 다중 테넌트 백 엔드에 대한 Application Gateway 지원의 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075187"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>App service와 같은 다중 테넌트 백 엔드에 대한 Application Gateway 지원

웹 서버의 다중 테넌트 아키텍처 디자인에서는 여러 웹 사이트가 동일한 웹 서버 인스턴스에서 실행 되 고 있습니다. 호스트 이름은 호스트 되는 여러 응용 프로그램을 구별 하는 데 사용 됩니다. 기본적으로 애플리케이션 게이트웨이는 클라이언트에서 들어오는 HTTP 호스트 헤더를 변경하지 않으며 변경되지 않은 헤더를 백 엔드로 보냅니다. 이는 Nic, 가상 머신 확장 집합, 공용 IP 주소, 내부 IP 주소 및 FQDN과 같은 백 엔드 풀 멤버에 적합 합니다 .이는 특정 호스트 헤더 또는 SNI 확장을 사용 하 여 올바른 엔드포인트을 확인 하지 않습니다. 그러나 Azure 앱 서비스 웹 앱 및 Azure API management와 같은 많은 서비스가 있으며,이는 본질적으로 다중 테넌트이 고 특정 호스트 헤더 또는 SNI 확장을 사용 하 여 올바른 엔드포인트을 확인 하는 것입니다. 일반적으로 응용 프로그램 게이트웨이와 연결 된 DNS 이름으로 사용 되는 응용 프로그램의 DNS 이름은 백 엔드 서비스의 도메인 이름과 다릅니다. 따라서 application gateway에서 받은 원래 요청의 호스트 헤더는 백 엔드 서비스의 호스트 이름과 동일 하지 않습니다. 이 때문에 응용 프로그램 게이트웨이에서 백 엔드에 대한 요청에 있는 호스트 헤더를 백 엔드 서비스의 호스트 이름으로 변경 하지 않는 한 다중 테넌트 백 엔드는 해당 요청을 올바른 엔드포인트으로 확인할 수 없습니다. 

Application Gateway는 사용자가 백 엔드의 호스트 이름에 따라 요청의 HTTP 호스트 헤더를 재정의할 수 있는 기능을 제공합니다. 이 기능을 사용하면 Azure 앱 서비스 웹앱 및 API 관리 같은 다중 테넌트 백 엔드를 지원할 수 있습니다. 이 기능은 v1/v2 표준 및 WAF SKU에 모두 사용할 수 있습니다. 

![호스트 재정의](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> ASE가 다중 테넌트 리소스인 Azure 앱 서비스와 달리 전용 리소스 이기 때문에이는 ASE (Azure 앱 service environment)에는 적용 되지 않습니다.

## <a name="override-host-header-in-the-request"></a>요청에서 호스트 헤더 재정의

호스트 재정의를 지정 하는 기능은 [HTTP 설정](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 에서 정의 되며 규칙을 만드는 동안 모든 백 엔드 풀에 적용할 수 있습니다. 다중 테넌트 백 엔드에 대한 호스트 헤더 및 SNI 확장을 재정의 하는 다음 두 가지 방법이 지원 됩니다.

- 호스트 이름을 HTTP 설정에 명시적으로 입력 한 고정 값으로 설정할 수 있습니다. 이 기능을 사용 하면 특정 HTTP 설정이 적용 된 백 엔드 풀에 대한 모든 트래픽에 대해 호스트 헤더가이 값으로 재정의 됩니다. 종단 간 SSL을 사용하면 이 재정의된 호스트 이름이 SNI 확장에 사용됩니다. 이 기능은 백 엔드 풀 팜이 들어오는 고객 호스트 헤더와 다른 호스트 헤더를 예상하는 시나리오를 지원할 수 있습니다.

- 백 엔드 풀 멤버의 IP 또는 FQDN에서 호스트 이름을 파생하는 기능. 또한 HTTP 설정은 개별 백 엔드 풀 멤버에서 호스트 이름을 파생 하는 옵션을 사용 하 여 구성 된 경우 백 엔드 풀 멤버의 FQDN에서 호스트 이름을 동적으로 선택 하는 옵션을 제공 합니다. 종단 간 SSL을 사용하면 이 호스트 이름이 FQDN에서 파생되며 SNI 확장에 사용됩니다. 이 기능을 사용하면 백 엔드 풀이 Azure 웹앱 같은 다중 테넌트 PaaS 서비스를 두 개 이상 가질 수 있고 각 멤버에 대한 요청의 호스트 헤더에 FQDN에서 파생된 호스트 이름이 포함되는 시나리오를 지원할 수 있습니다. 이 시나리오를 구현 하기 위해 백 엔드 풀에서 언급 한 것으로 원래 요청의 호스트 헤더를 동적으로 재정의 하는 [백 엔드 주소에서 호스트 이름 선택](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) 이라는 HTTP 설정에서 스위치를 사용 합니다.  예를 들어 백 엔드 풀 FQDN에 "contoso11.azurewebsites.net" 및 "contoso22.azurewebsites.net"가 포함 된 경우 원래 요청의 호스트 헤더 contoso.com는 contoso11.azurewebsites.net 또는 contoso22.azurewebsites.net로 재정의 됩니다. 요청이 적절 한 백 엔드 서버에 전송 되는 경우 

  ![웹앱 시나리오](./media/application-gateway-web-app-overview/scenario.png)

이 기능을 사용하여 고객이 HTTP 설정 및 사용자 지정 프로브의 옵션을 적절한 구성으로 지정합니다. 그러면 규칙을 사용하여 이 설정이 수신기와 백 엔드 풀에 연결됩니다.

## <a name="special-considerations"></a>특별 고려 사항

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>다중 테넌트 서비스를 사용 하는 SSL 종료 및 종단 간 SSL

다중 테넌트 서비스에서 SSL 종료 및 종단 간 SSL 암호화가 지원 됩니다. Application gateway에서 SSL을 종료 하는 경우에는 application gateway 수신기에 SSL 인증서를 계속 추가 해야 합니다. 그러나 종단 간 SSL의 경우 Azure 앱 서비스 웹 앱과 같은 신뢰할 수 있는 Azure 서비스는 응용 프로그램 게이트웨이에서 백 엔드를 허용 목록 필요가 없습니다. 따라서 인증 인증서를 추가할 필요가 없습니다. 

![종단 간 SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

위의 이미지에서는 App service를 백 엔드로 선택할 때 인증 인증서를 추가 해야 하는 요구 사항이 없습니다.

### <a name="health-probe"></a>상태 프로브

**HTTP 설정** 에서 호스트 헤더를 재정의 하면 요청 및 라우팅에만 영향을 줍니다. 상태 프로브 동작에는 영향을 주지 않습니다. 종단 간 기능이 작동하려면 올바른 구성을 반영하도록 프로브와 HTTP 설정을 모두 수정해야 합니다. 사용자 지정 프로브는 프로브 구성에서 호스트 헤더를 지정 하는 기능을 제공 하는 것 외에도 현재 구성 된 HTTP 설정에서 호스트 헤더를 파생 하는 기능을 지원 합니다. 이 구성은 프로브 구성의 `PickHostNameFromBackendHttpSettings` 매개 변수를 사용하여 지정할 수 있습니다.

### <a name="redirection-to-app-services-url-scenario"></a>App Service의 URL 시나리오로 리디렉션

앱 서비스의 응답에 있는 호스트 이름이 Application Gateway와 연결 된 도메인 대신 *. azurewebsites.net 호스트 이름으로 전달 될 수 있는 시나리오가 있을 수 있습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

- App Service에 리디렉션이 구성 되어 있습니다. 리디렉션은 요청에 후행 슬래시를 추가 하는 것 처럼 간단할 수 있습니다.
- 리디렉션을 발생 시키는 Azure AD 인증이 있습니다.

이러한 경우를 해결 하려면 [App service의 URL로 리디렉션 문제 해결](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Application Gateway로 App Service 웹 앱 구성](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal) 을 방문 하 여 Azure 앱 service web app과 같은 다중 테넌트 앱을 백 엔드 풀 멤버로 사용 하 여 응용 프로그램 게이트웨이를 설정 하는 방법에 대해 알아봅니다
