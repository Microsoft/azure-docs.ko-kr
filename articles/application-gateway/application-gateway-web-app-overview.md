---
title: Azure Application Gateway를 사용 하 여 Azure App service와 같은 다중 테 넌 트 백 엔드 개요
description: 이 페이지에서는 다중 테넌트 백 엔드에 대한 Application Gateway 지원의 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 8434340bb7ed95cc36115c05048b2b67682b5796
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831323"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>App service와 같은 다중 테 넌 트 백에 대 한 응용 프로그램 게이트웨이 지원은 종료 됩니다.

웹 서버에서 다중 테 넌 트 아키텍처 설계에서 여러 웹 사이트는 동일한 웹 서버 인스턴스가에서 실행 됩니다. 호스트는 호스팅되는 다른 응용 프로그램을 구분 하는 데 사용 됩니다. 기본적으로 애플리케이션 게이트웨이는 클라이언트에서 들어오는 HTTP 호스트 헤더를 변경하지 않으며 변경되지 않은 헤더를 백 엔드로 보냅니다. Nic와 같은 백 엔드 풀 멤버에이 적합 합니다, 가상 머신 확장 집합, 공용 IP 주소, 내부 IP 주소 및 FQDN으로 이러한 특정 호스트 헤더 또는 SNI 확장에 올바른 끝점으로 해석에 의존 하지 마십시오. 그러나는 본질적으로 다중 테 넌 트 및 특정 호스트 헤더 또는 SNI 확장 올바른 끝점으로 해석에 의존 하는 Azure App service web apps 및 Azure API management와 같은 여러 서비스가 있습니다. 일반적으로는 응용 프로그램에는 응용 프로그램 게이트웨이에 연결 된 DNS 이름, DNS 이름은 백 엔드 서비스의 도메인 이름과에서 다릅니다. 따라서 응용 프로그램 게이트웨이에서 수신 하는 원래 요청에 호스트 헤더가 아닙니다 백 엔드 서비스의 호스트 이름과 동일 합니다. 이 인해 응용 프로그램 게이트웨이에서 백 엔드에 요청에 호스트 헤더를 백 엔드 서비스의 호스트 이름 변경 하지 않으면 다중 테 넌 트 백 엔드 수 없는 올바른 끝점으로 요청을 확인 합니다. 

Application gateway는 백 엔드의 호스트 이름에 따라 요청에 HTTP 호스트 헤더를 재정의 하는 사용자를 허용 하는 기능을 제공 합니다. 이 기능을 사용 하면 Azure App service web apps 및 API management와 같은 다중 테 넌 트 백 엔드에 대 한 지원. 이 기능은 v1 및 v2 표준 및 WAF Sku에 대해 사용할 수 있습니다. 

![호스트 재정](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> 이 ASE는 다중 테 넌 트 리소스는 Azure App service와 달리 전용된 리소스 이므로 Azure App service environment (ASE)에 적용 되지 않습니다.

## <a name="override-host-header-in-the-request"></a>요청의 호스트 헤더를 재정의 합니다.

호스트 재정의 지정 하는 기능에 정의 되어는 [http-settings](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) 및 규칙을 만드는 동안 모든 백 엔드 풀에 적용할 수 있습니다. 다음 두 가지 호스트 헤더 및 SNI 확장 다중 테 넌 트 백 엔드에 대 한 재정의 사용할 수 있습니다.

- 호스트 이름을 HTTP 설정에 명시적으로 입력 한 고정된 값으로 설정 하는 기능. 이 기능은 호스트 헤더는 특정 HTTP 설정이 적용 되는 백 엔드 풀에 대 한 모든 트래픽에 대해이 값으로 재정의 되는 않도록 합니다. 종단 간 SSL을 사용하면 이 재정의된 호스트 이름이 SNI 확장에 사용됩니다. 이 기능은 백 엔드 풀 팜이 들어오는 고객 호스트 헤더와 다른 호스트 헤더를 예상하는 시나리오를 지원할 수 있습니다.

- 백 엔드 풀 멤버의 IP 또는 FQDN에서 호스트 이름을 파생하는 기능. HTTP 설정에는 또한 동적으로 개별 백 엔드 풀 멤버에서 호스트 이름을 파생 하는 옵션을 사용 하 여 구성 하는 경우 백 엔드 풀 멤버의 FQDN에서 호스트 이름을 선택 하는 옵션도 제공 합니다. 종단 간 SSL을 사용하면 이 호스트 이름이 FQDN에서 파생되며 SNI 확장에 사용됩니다. 이 기능을 사용하면 백 엔드 풀이 Azure 웹앱 같은 다중 테넌트 PaaS 서비스를 두 개 이상 가질 수 있고 각 멤버에 대한 요청의 호스트 헤더에 FQDN에서 파생된 호스트 이름이 포함되는 시나리오를 지원할 수 있습니다. 이 시나리오를 구현, 호출 하는 HTTP 설정에서 스위치를 사용 [백 엔드 주소에서 호스트 이름 선택](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-backend-address) 는 동적으로 백 엔드 풀에서 언급 한 원래 요청에 호스트 헤더를 재정의 합니다.  예를 들어, 백 엔드 풀 FQDN "contoso11.azurewebsites.net" 및 "contoso22.azurewebsites.net"가 포함 된 경우 원래 요청의 호스트 헤더를 contoso.com 재정의 됩니다 contoso11.azurewebsites.net 또는 contoso22.azurewebsites.net 경우 요청이 적절 한 백 엔드 서버에 보내집니다. 

  ![웹앱 시나리오](./media/application-gateway-web-app-overview/scenario.png)

이 기능을 사용하여 고객이 HTTP 설정 및 사용자 지정 프로브의 옵션을 적절한 구성으로 지정합니다. 그러면 규칙을 사용하여 이 설정이 수신기와 백 엔드 풀에 연결됩니다.

## <a name="special-considerations"></a>특별 고려 사항

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>다중 테 넌 트 서비스를 사용 하 여 종단 간 SSL 및 SSL 종료

SSL 종료 및 SSL 암호화를 종단 간 다중 테 넌 트 서비스를 사용 하 여 지원 됩니다. Application gateway에서 SSL 종료에 대 한 SSL 인증서 계속 응용 프로그램 게이트웨이 수신기에 추가 해야 합니다. 그러나 종단 간 SSL의 경우 신뢰할 수 있는 Azure 같은 서비스 Azure App service 웹 앱에 application gateway에 백 엔드를 허용 목록 작성 필요 하지 않습니다. 따라서 모든 인증 인증서를 추가할 필요가 없습니다 있습니다. 

![종단 간 SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

위의 그림에서, 백 엔드 App service를 선택 하는 경우 인증 인증서를 추가할 필요가 없습니다.

### <a name="health-probe"></a>상태 프로브

호스트 헤더를 재정의 합니다 **http-settings** 요청과 해당 라우팅에 영향을 줍니다. 상태 프로브 동작을 영향을 주지 않습니다. 종단 간 기능이 작동하려면 올바른 구성을 반영하도록 프로브와 HTTP 설정을 모두 수정해야 합니다. 프로브 구성의 호스트 헤더를 지정 하는 기능을 제공 하는 것 외에도 사용자 지정 프로브는 또한 현재 구성 된 HTTP 설정에서 호스트 헤더를 파생 하는 기능도 지원 합니다. 이 구성은 프로브 구성의 `PickHostNameFromBackendHttpSettings` 매개 변수를 사용하여 지정할 수 있습니다.

### <a name="redirection-to-app-services-url-scenario"></a>App Service의 URL 시나리오에 리디렉션

응답 App service에서 호스트 이름에 최종 사용자 브라우저를 전달할 수 있습니다 위치 하는 시나리오가 있을 수는 *. azurewebsites.net 호스트 응용 프로그램 게이트웨이에 연결 된 도메인 대신 합니다. 이 문제가 발생할 수 있습니다 때:

- 리디렉션 App Service에서 구성 해야 합니다. 리디렉션 요청에 후행 슬래시를 추가 하는 것으로 간단할 수 있습니다.
- 리디렉션되는 Azure AD 인증을 해야 합니다.

이러한 사례를 해결 하려면 참조 [App service의 URL 문제에 대 한 리디렉션을 해결](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)합니다.

## <a name="next-steps"></a>다음 단계

방문 하 여 Azure App service web apps와 같은 다중 테 넌 트 앱을 사용 하 여 응용 프로그램 게이트웨이 백 엔드 풀 멤버로 설정 하는 방법을 알아봅니다 [Application Gateway를 사용 하 여 웹 앱 App Service 구성](https://docs.microsoft.com/azure/application-gateway/create-web-app)
