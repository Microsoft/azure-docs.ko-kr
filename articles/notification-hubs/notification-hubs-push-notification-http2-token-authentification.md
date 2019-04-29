---
title: Azure Notification Hubs의 APNS에 대한 토큰 기반(HTTP/2) 인증 | Microsoft Docs
description: 이 항목에서는 APNS에 대한 새로운 토큰 인증을 활용하는 방법을 설명합니다.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: jowargo
ms.openlocfilehash: 890577c013a96fc06acf3b05881649ad8202a083
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872372"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS에 대한 토큰 기반(HTTP/2) 인증

## <a name="overview"></a>개요

이 문서에서는 토큰 기반 인증을 지원하는 새로운 APNS HTTP/2 프로토콜을 사용하는 방법을 자세히 설명합니다.

새 프로토콜을 사용할 경우의 주요 이점은 다음과 같습니다.

* 인증서에 비해 토큰 생성이 비교적 쉽습니다.
* 만료 날짜가 없습니다. 인증 토큰과 해당 해지를 제어할 수 있습니다.
* 이제 페이로드가 최대 4KB입니다.
* 피드백이 동기화됩니다.
* Apple의 최신 프로토콜을 사용합니다. 인증서에서는 여전히 이진 프로토콜을 사용하며 이는 사용 중단된 것으로 표시됩니다.

두 단계를 통해 몇 분 만에 이 새로운 메커니즘을 사용할 수 있습니다.

1. Apple 개발자 계정 포털에서 필요한 정보를 가져옵니다.
2. 새로운 정보로 알림 허브를 구성합니다.

이제 Notification Hubs는 모두 APNS에서 새로운 인증 시스템을 사용하도록 설정됩니다.

APNS에 인증서 자격 증명을 사용하는 것에서 마이그레이션한 경우

* 토큰 속성이 시스템의 인증서를 덮어씁니다.
* 그러나 애플리케이션에서는 계속 원활하게 알림을 수신합니다.

## <a name="obtaining-authentication-information-from-apple"></a>Apple에서 인증 정보 가져오기

토큰 기반 인증을 사용하려면 Apple 개발자 계정에서 다음 속성을 가져와야 합니다.

### <a name="key-identifier"></a>키 식별자

Apple 개발자 계정의 "키" 페이지에서 키 식별자를 가져올 수 있습니다.

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>애플리케이션 식별자 및 애플리케이션 이름

애플리케이션 이름은 개발자 계정의 앱 ID 페이지를 통해 제공됩니다.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

애플리케이션 식별자는 개발자 계정의 멤버 자격 세부 정보 페이지를 통해 제공됩니다.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>인증 토큰

애플리케이션에 대한 토큰을 생성한 후 인증 토큰을 다운로드할 수 있습니다. 이 토큰을 생성하는 방법에 대한 자세한 내용은 [Apple 개발자 설명서](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1)를 참조하세요.

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>토큰 기반 인증을 사용하도록 알림 허브 구성

### <a name="configure-via-the-azure-portal"></a>Azure Portal을 통해 구성

포털에서 토큰 기반 인증을 활성화하려면 Azure Portal에 로그인하여 알림 허브 > Notification Services > APNS 패널로 이동합니다.

*인증 모드*라는 새로운 속성이 있습니다. 토큰을 선택하면 관련된 모든 토큰 속성으로 허브를 업데이트할 수 있습니다.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple 개발자 계정에서 검색한 속성을 입력합니다.
* 애플리케이션 모드(프로덕션 또는 샌드박스)를 선택합니다.
* **저장** 단추를 클릭하여 APNS 자격 증명을 업데이트합니다.

### <a name="configure-via-management-api-rest"></a>관리 API(REST)을 통해 구성

[관리 API](https://msdn.microsoft.com/library/azure/dn495827.aspx)를 사용하여 토큰 기반 인증을 사용하도록 알림 허브를 업데이트할 수 있습니다.
구성 중인 애플리케이션이 샌드박스 앱인지 또는 프로덕션 앱인지에 따라(Apple 개발자 계정에서 지정) 해당 엔드포인트 중 하나를 사용합니다.

* 샌드박스 엔드포인트: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* 프로덕션 엔드포인트: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> 토큰 기반 인증에는 API 버전 **2017-04 이상**이 필요합니다.

토큰 기반 인증으로 허브를 업데이트하기 위한 PUT 요청 예제는 다음과 같습니다.

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>.NET SDK를 통해 구성

[최신 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8)를 사용하여 토큰 기반 인증을 사용하도록 허브를 구성할 수 있습니다.

올바른 사용법을 보여 주는 코드 샘플은 다음과 같습니다.

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>인증서 기반 인증 사용으로 되돌리기

위 방법을 사용하고 토큰 속성 대신 인증서를 전달하여 언제든지 인증서 기반 인증 사용으로 되돌릴 수 있습니다. 이 작업은 이전에 저장된 자격 증명을 덮어씁니다.
