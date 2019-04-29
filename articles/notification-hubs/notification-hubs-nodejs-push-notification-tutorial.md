---
title: Azure Notification Hubs 및 Node.js를 사용하여 푸시 알림 보내기
description: Notification Hubs를 사용하여 Node.js 애플리케이션에 푸시 알림을 보내는 방법에 대해 알아봅니다.
keywords: 푸시 알림, 푸시 알림, node.js 푸시, ios 푸시
services: notification-hubs
documentationcenter: nodejs
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bdeba401e99ad16555b9f6ea00017fc525302983
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457877"
---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Azure Notification Hubs 및 Node.js를 사용하여 푸시 알림 보내기

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>개요

> [!IMPORTANT]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs)을 통해 몇 분 이내에 평가판 계정을 만들 수 있습니다.

이 가이드에서는 [Node.js](https://nodejs.org) 애플리케이션에서 직접 Azure Notification Hubs를 통해 푸시 알림을 보내는 방법을 보여 줍니다.

시나리오는 다음 플랫폼에서 애플리케이션에 푸시 알림을 보내기를 포함합니다.

- Android
- iOS
- 범용 Windows 플랫폼
- Windows Phone

## <a name="notification-hubs"></a>Notification Hubs

Azure Notification Hubs는 모바일 디바이스에 푸시 알림을 보내는 사용하기 쉽고 확장성 있는 다중 플랫폼 인프라를 제공합니다. 서비스 인프라에 대한 세부 정보는 [Azure Notification Hubs](https://msdn.microsoft.com/library/windowsazure/jj927170.aspx) 페이지를 참조하세요.

## <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

이 자습서의 첫 번째 단계는 새로운 빈 Node.js 애플리케이션을 만드는 것입니다. Node.js 애플리케이션을 만드는 방법에 대한 지침은 [Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포][nodejswebsite], Windows PowerShell을 사용한 [Node.js Cloud Service][Node.js Cloud Service] 또는 [WebMatrix를 사용하는 웹 사이트][webmatrix]를 참조하세요.

## <a name="configure-your-application-to-use-notification-hubs"></a>Notification Hubs를 사용하도록 애플리케이션 구성

Azure Notification Hubs를 사용하려면 푸시 알림 REST 라이브러리와 통신하는 일련의 기본 제공 도우미 라이브러리가 포함되어 있는 Node.js [Azure 패키지](https://www.npmjs.com/package/azure)를 다운로드하여 사용해야 합니다.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 가져오기

1. **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Linux) 등과 같은 명령줄 인터페이스를 사용하여 빈 애플리케이션을 만든 폴더로 이동합니다.
2. 명령 창에서 `npm install azure-sb`를 실행합니다.
3. `ls` 또는 `dir` 명령을 수동으로 실행하여 `node_modules` 폴더가 만들어졌는지 확인할 수 있습니다.
4. 이 폴더에서 알림 허브에 액세스하는 데 필요한 라이브러리가 들어 있는 **Azure** 패키지를 찾습니다.

> [!NOTE]
> 자세한 내용은 공식 [NPM 블로그](https://blog.npmjs.org/post/85484771375/how-to-install-npm)에서 NPM 설치에 대해 자세히 알아볼 수 있습니다.

### <a name="import-the-module"></a>모듈 가져오기
텍스트 편집기를 사용하여 다음을 애플리케이션의 `server.js` 파일 맨 위에 추가합니다.

```javascript
var azure = require('azure-sb');
```

### <a name="set-up-an-azure-notification-hub-connection"></a>Azure 알림 허브 연결 설정

`NotificationHubService` 개체를 사용하면 알림 허브에서 작업할 수 있습니다. 다음 코드는 `hubname`이라는 알림 허브에 대한 `NotificationHubService` 개체를 만듭니다. 이 코드를 `server.js` 파일의 위쪽에 있는 Azure 모듈을 가져오는 명령문 뒤에 추가합니다.

```javascript
var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');
```

다음 단계를 수행하여 [Azure Portal]에서 `connectionstring` 연결 값을 가져옵니다.

1. 왼쪽 탐색 창에서 **찾아보기**를 클릭합니다.
2. **Notification Hubs**를 선택한 다음 샘플로 사용하려는 허브를 찾습니다. 참조할 수 있습니다 합니다 [Windows 스토어 시작 자습서](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 를 새 알림 허브를 만드는 데 도움이 필요한 경우.
3. **설정**을 선택합니다.
4. **액세스 정책**을 클릭합니다. 공유 및 전체 액세스 연결 문자열이 모두 표시됩니다.

![Azure Portal - Notification Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> [Azure PowerShell](/powershell/azureps-cmdlets-docs)에서 제공하는 **Get-AzureSbNamespace** cmdlet 또는 [Azure CLI(Azure 명령줄 인터페이스)](../cli-install-nodejs.md)로 **azure sb namespace show** 명령을 사용하여 연결 문자열을 검색할 수도 있습니다.

## <a name="general-architecture"></a>일반 아키텍처

`NotificationHubService` 개체는 특정 디바이스와 애플리케이션에 푸시 알림을 보내는 다음 개체 인스턴스를 공개합니다.

- **Android** - `notificationHubService.gcm`에서 사용할 수 있는 `GcmService` 개체를 사용합니다.
- **iOS** - `notificationHubService.apns`에서 액세스할 수 있는 `ApnsService` 개체를 사용합니다.
- **Windows Phone** - `notificationHubService.mpns`에서 사용할 수 있는 `MpnsService` 개체를 사용합니다.
- **유니버설 Windows 플랫폼** - `notificationHubService.wns`에서 사용할 수 있는 `WnsService` 개체를 사용합니다.

### <a name="how-to-send-push-notifications-to-android-applications"></a>방법: Android 애플리케이션에 푸시 알림 보내기

`GcmService` 개체는 Android 애플리케이션에 푸시 알림을 보내는 데 사용할 수 있는 `send` 메서드를 제공합니다. `send` 메서드에서 허용하는 매개 변수는 다음과 같습니다.

- **Tags** - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
- **Payload** - 메시지의 JSON 또는 원시 문자열 페이로드
- **Callback** - 콜백 함수.

페이로드 형식에 대한 자세한 내용은 **GCM 서버 구현** 문서의 [페이로드](https://developer.android.com/google/gcm/server.html#payload) 섹션을 참조하세요.

다음 코드는 `NotificationHubService`에서 공개하는 `GcmService` 인스턴스를 사용하여 등록된 모든 클라이언트에 푸시 알림을 보냅니다.

```javascript
var payload = {
  data: {
    message: 'Hello!'
  }
};
notificationHubService.gcm.send(null, payload, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-ios-applications"></a>방법: iOS 애플리케이션에 푸시 알림 보내기

위에서 설명한 Android 애플리케이션과 마찬가지로 `ApnsService` 개체는 iOS 애플리케이션에 알림을 보내는 데 사용할 수 있는 `send` 메서드를 제공합니다. `send` 메서드에서 허용하는 매개 변수는 다음과 같습니다.

- **Tags** - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
- **Payload** - 메시지의 JSON 또는 문자열 페이로드
- **Callback** - 콜백 함수.

페이로드 형식에 대한 자세한 내용은 **로컬 및 푸시 알림 프로그래밍 가이드** 문서의 [알림 페이로드](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) 섹션을 참조하세요.

다음 코드는 `NotificationHubService`에서 공개하는 `ApnsService` 인스턴스를 사용하여 모든 클라이언트에 경고 메시지를 보냅니다.

```javascript
var payload={
    alert: 'Hello!'
  };
notificationHubService.apns.send(null, payload, function(error){
  if(!error){
      // notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>방법: Windows Phone 애플리케이션에 푸시 알림 보내기

`MpnsService` 개체는 Windows Phone 애플리케이션에 푸시 알림을 보내는 데 사용할 수 있는 `send` 메서드를 제공합니다. `send` 메서드에서 허용하는 매개 변수는 다음과 같습니다.

- **Tags** - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
- **Payload** - 메시지의 XML 페이로드
- **TargetName** - `toast` - 알림 메시지인 경우. `token` - 타일 알림 메시지인 경우.
- **NotificationClass** - 알림 우선 순위. 유효한 값은 **서버에서 푸시 알림** 문서의 [HTTP 헤더 요소](https://msdn.microsoft.com/library/hh221551.aspx) 섹션을 참조하세요.
- **Options** - 선택적 요청 헤더
- **Callback** - 콜백 함수.

유효한 `TargetName`, `NotificationClass` 및 헤더 옵션에 대한 목록은 [서버의 푸시 알림](https://msdn.microsoft.com/library/hh221551.aspx) 페이지를 확인하세요.

다음 샘플 코드는 `NotificationHubService`에서 공개하는 `MpnsService` 인스턴스를 사용하여 알림 푸시 알림을 보냅니다.

```javascript
var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
  if(!error){
    //notification sent
  }
});
```

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>방법: UWP(유니버설 Windows 플랫폼) 애플리케이션에 푸시 알림 보내기

`WnsService` 개체는 유니버설 Windows 플랫폼 애플리케이션에 푸시 알림을 보내는 데 사용할 수 있는 `send` 메서드를 제공합니다.  `send` 메서드에서 허용하는 매개 변수는 다음과 같습니다.

- **Tags** - 태그 식별자. 태그를 제공하지 않은 경우 모든 클라이언트에게 알림이 전송됩니다.
- **Payload** - XML 메시지 페이로드
- **Type** - 알림 유형
- **Options** - 선택적 요청 헤더
- **Callback** - 콜백 함수.

유효한 유형 및 요청 헤더 목록은 [푸시 알림 서비스 요청 및 응답 헤더](https://msdn.microsoft.com/library/windows/apps/hh465435.aspx)를 참조하세요.

다음 코드는 `NotificationHubService`에서 공개하는 `WnsService` 인스턴스를 사용하여 UWP 앱에 알림 푸시 알림을 보냅니다.

```javascript
var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
  if(!error){
      // notification sent
  }
});
```

## <a name="next-steps"></a>다음 단계

위의 샘플 코드 조각을 사용하면 다양한 디바이스에 푸시 알림을 전달하는 서비스 인프라를 쉽게 작성할 수 있습니다. 이제 node.js가 있는 Notification Hubs를 사용하는 기본 사항을 배웠으므로 다음 링크를 따라서 이러한 기능을 더욱 확장할 수 있는 방법에 대해 자세히 알아봅니다.

- [Azure Notification Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx)는 MSDN 참조를 참조하세요.
- 추가 샘플 및 구현 세부 정보는 GitHub에서 [Node용 Azure SDK] 리포지토리를 방문합니다.

[Node용 Azure SDK]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
[Create a Service Namespace]: #create-a-service-namespace
[Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
[Create a Node.js Application]: #Create_a_Nodejs_Application
[Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[How to: Create a Topic]: #How_to_Create_a_Topic
[How to: Create Subscriptions]: #How_to_Create_Subscriptions
[How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
[How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
[How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: https://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: https://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Web Site with WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs
[webmatrix]: https://docs.microsoft.com/aspnet/web-pages/videos/introduction/create-a-website-using-webmatrix
[Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Azure Portal]: https://portal.azure.com
