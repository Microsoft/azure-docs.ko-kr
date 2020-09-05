---
title: Azure Notification Hubs의 APNS에 대 한 토큰 기반 (HTTP/2) 인증 | Microsoft Docs
description: APNS에 대 한 새 토큰 인증을 사용 하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: e8258aeef33be9192608f0d4cb29b46f08da64e2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010633"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS에 대 한 토큰 기반 (HTTP/2) 인증

## <a name="overview"></a>개요

이 문서에서는 토큰 기반 인증에 새 APNS HTTP/2 프로토콜을 사용 하는 방법을 설명 합니다.

새 프로토콜을 사용할 경우의 주요 이점은 다음과 같습니다.

* 토큰 생성은 인증서와 비교 하 여 비교적 간단 합니다.
* 만료 날짜가 없습니다. 인증 토큰과 해당 해지를 제어할 수 있습니다.
* 이제 페이로드가 최대 4KB입니다.
* 피드백이 동기화됩니다.
* Apple의 최신 프로토콜을 사용 하 고 있습니다. 인증서는 사용 중단으로 표시 된 이진 프로토콜을 계속 사용 합니다.

이 새로운 메커니즘 사용은 다음 두 단계로 수행할 수 있습니다.

* Apple 개발자 계정 포털에서 필요한 정보를 가져옵니다.
* 새 정보를 사용 하 여 알림 허브를 구성 합니다.

이제 Notification Hubs은 APNS와 함께 새 인증 시스템을 사용 하도록 설정 됩니다.

APNS에 대 한 인증서 자격 증명을 사용 하 여 마이그레이션한 경우 토큰 속성은 시스템에서 인증서를 덮어쓰므로 응용 프로그램은 계속 해 서 알림을 계속 받습니다.

## <a name="obtaining-authentication-information-from-apple"></a>Apple에서 인증 정보 가져오기

토큰 기반 인증을 사용 하도록 설정 하려면 Apple 개발자 계정에서 다음 속성이 필요 합니다.

### <a name="key-identifier"></a>키 식별자

키 식별자는 Apple 개발자 계정의 **인증서, 식별자 & 프로필**아래에 있는 **키** 페이지에서 가져올 수 있습니다.

![인증서](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![식별자](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>응용 프로그램 식별자 및 응용 프로그램 이름

개발자 계정의 **인증서, 식별자 & 프로필** 페이지 에서도 응용 프로그램 이름 및 식별자를 사용할 수 있습니다.

![인증서 및 Id](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>.NET SDK 또는 Azure Portal를 통해 구성

[최신 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)를 사용 하 여 또는 Azure Portal에서 토큰 기반 인증을 사용 하도록 허브를 구성할 수 있습니다. 포털에서 토큰 기반 인증을 사용 하도록 설정 하려면 Azure Portal에 로그인 하 고 알림 허브의 **설정 > Apple (APNS)** 패널로 이동 합니다. **인증 모드** 속성에서 **토큰** 을 선택 하 여 모든 관련 토큰 속성으로 허브를 업데이트 합니다.

![토큰 구성](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple 개발자 계정에서 검색 한 속성을 입력 합니다.
* 응용 프로그램 모드 (**프로덕션** 또는 **샌드박스**)를 선택 합니다.
* **저장** 단추를 클릭 하 여 APNS 자격 증명을 업데이트 합니다.

토큰 기반 자격 증명은 다음 필드로 구성 됩니다.

* **키 ID**: Apple 개발자 포털에서 생성 된 개인 키의 식별자입니다. 예를 들면 `2USFGKSKLT` 입니다.
* **팀 ID**: "접두사" 또는 "앱 접두사" 라고도 합니다. Apple 개발자 포털의 조직에 대 한 식별자입니다. 예를 들면 `S4V3D7CHJR` 입니다.
* **번들 id**: "앱 id" 라고도 합니다. 응용 프로그램에 대 한 번들 식별자입니다. 예를 들면 `com.example.myapp` 입니다. 많은 앱에 하나의 키를 사용할 수 있습니다. 이 값 `apns-topic` 은 알림을 보낼 때 HTTP 헤더에 매핑되고 특정 응용 프로그램을 대상으로 지정 하는 데 사용 됩니다. 값은 명시적으로 설정할 수 없습니다 `apns-topic` .
* **Token**: "키" 또는 "개인 키" 라고도 합니다. 이는 Apple 개발자 포털에서 생성 된 p8 파일에서 가져옵니다. 키에 APNS를 사용 하도록 설정 되어 있어야 합니다 (키를 생성할 때 Apple 개발자 포털에서 선택 됨). NH 포털/API에 값을 제공 하는 경우 값은 PEM 헤더/바닥글을 제거 해야 합니다.
* **끝점**: Notification Hubs 포털 블레이드의 토글 이며 API의 문자열 필드입니다. 유효한 값은 `https://api.development.push.apple.com:443/3/device` 또는 `https://api.sandbox.push.apple.com:443/3/device`입니다. Notification Hubs는 프로덕션 또는 샌드박스 환경에 대해이 값을 사용 하 여 알림을 보냅니다. 이는 앱의 자격과 일치 해야 합니다 `aps-environment` . 그렇지 않으면 생성 된 APNS 장치 토큰이 환경과 일치 하지 않고 알림이 전송 되지 않습니다.

올바른 사용법을 보여 주는 코드 샘플은 다음과 같습니다.

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 Azure 알림 허브 만들기](create-notification-hub-portal.md)
* [Azure Portal에서 알림 허브 구성](create-notification-hub-portal.md)
