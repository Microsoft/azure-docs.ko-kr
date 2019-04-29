---
title: Azure Application Gateway-포털을 사용 하 여 앱 서비스 웹 앱 같은 다중 테 넌 트 앱으로 트래픽 관리
description: 이 문서에서는 Azure App service web apps를 기존 또는 새 application gateway에서 백 엔드 풀의 구성원으로 구성 하는 방법에 지침을 제공 합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831223"
---
# <a name="configure-app-service-with-application-gateway"></a>Application Gateway를 사용하여 App Service 구성

응용 프로그램 게이트웨이 사용 하는 Azure App service 웹 앱 또는 기타 다중 테 넌 트 서비스는 백 엔드 풀 구성원으로 가질 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> - 백 엔드 풀 만들기 및 App Service에 추가
> - 사용 하도록 설정 하는 "Hostname 선택" 스위치를 사용 하 여 HTTP 설정 및 사용자 지정 프로브 만들기

## <a name="prerequisites"></a>필수 조건

- 응용 프로그램 게이트웨이: 기존 application gateway가 없는 경우 참조 하는 방법을 [응용 프로그램 게이트웨이 만들기](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 앱 서비스: 기존 App service가 없는 경우 [App service 설명서](https://docs.microsoft.com/azure/app-service/)합니다.

## <a name="add-app-service-as-backend-pool"></a>App service 백 엔드 풀 추가

1. Azure portal에서 application gateway의 구성 보기를 엽니다.

2. 아래 **백 엔드 풀**, 클릭 **추가** 새 백 엔드 풀 만들기.

3. 백 엔드 풀에 적합 한 이름을 제공 합니다. 

4. 아래 **대상**드롭다운 목록을 클릭 하 고 선택 **App Services** 옵션으로 제공 합니다.

5. 바로 아래 드롭다운을 **대상** 에 App Services 목록이 있는 드롭다운이 나타납니다. 이 드롭다운 목록에서 추가 클릭 하 고 백 엔드 풀 멤버로 추가 하려는 App Service를 선택 합니다.

   ![App service 백 엔드](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>App service 용 HTTP 설정 만들기

1. 아래 **Http-settings**, 클릭 **추가** 새 HTTP 설정을 만들어야 합니다.

2. HTTP 설정에 대 한 이름을 입력 하 고 사용자의 요구 사항에 따라 쿠키 기반 선호도 사용 하지 않도록 설정 하거나 설정할 수 있습니다.

3. 사용 사례에 따라 HTTP 또는 HTTPS 프로토콜을 선택 합니다. 

4. 확인란 **App Service에 대해 사용 하 여** 켜 집니다 및 합니다 **선택 호스트 이름에서 백 엔드 주소를 사용 하 여 프로브를 만듭니다** 및 **백 엔드 주소에서 호스트 이름을 선택** 옵션입니다. 이 옵션은 또한 사용 하도록 설정 하는 스위치를 사용 하 여 프로브를 자동으로 만들기 하 고이 HTTP 설정에 연결 합니다.

5. 클릭 **확인** HTTP 설정을 만들어야 합니다.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>수신기, 백 엔드 풀 및 HTTP 설정을 연결 하는 규칙 만들기

1. 아래 **규칙**, 클릭 **기본** 새 기본 규칙을 만들려고 합니다.

2. 적절 한 이름을 제공 하 고 App service에 대 한 들어오는 요청을 수락 됩니다 하는 수신기를 선택 합니다.

3. 에 **백 엔드 풀** 드롭다운에서 위에서 만든 백 엔드 풀을 선택 합니다.

4. 에 **HTTP 설정** 드롭다운에서 위에서 만든를 설정 하는 HTTP를 선택 합니다.

5. 클릭 **확인** 이 규칙을 저장 합니다.

   ![규칙](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>액세스 제한

이 예제에서 배포된 웹앱은 인터넷에서 직접 액세스할 수 있는 공용 IP 주소를 사용합니다. 따라서 새로운 기능을 배우고 새로운 것을 시도할 때 문제를 해결하는 데 도움이 됩니다. 하지만 프로덕션 환경에 기능을 배포하려는 경우 더 많은 제한을 추가해야 합니다.

웹앱에 대한 액세스를 제한할 수 있는 한 가지 방법은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 사용하는 것입니다. 웹앱이 애플리케이션 게이트웨이의 트래픽만 수신하도록 웹앱을 제한할 수 있습니다. 앱 서비스 IP 제한 기능을 사용하여 액세스할 수 있는 유일한 주소로 응용 프로그램 게이트웨이 VIP를 나열할 수 있습니다.

## <a name="next-steps"></a>다음 단계

App service 및 application gateway와 다른 다중 테 넌 트 지원에 대 한 자세한 내용은 참조 하세요 [응용 프로그램 게이트웨이 사용 하 여 다중 테 넌 트 서비스 지원](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)합니다.

App service의 URL로 리디렉션됩니다. App service에서 응답을 하는 경우 참조 하는 방법 [App service의 URL 문제에 대 한 리디렉션을 문제 해결](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)합니다.
