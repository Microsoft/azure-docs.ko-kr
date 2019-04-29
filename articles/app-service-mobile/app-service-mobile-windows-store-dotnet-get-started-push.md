---
title: UWP(유니버설 Windows 플랫폼) 앱에 푸시 알림 추가 | Microsoft Docs
description: Azure App Service Mobile Apps 및 Azure Notification Hubs를 사용하여 UWP(유니버설 Windows 플랫폼) 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다.
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 7efd853e7b66933cac811625d7510139864f41f3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128035"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Windows 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>개요

이 자습서에서는 푸시 알림을 [Windows 빠른 시작](app-service-mobile-windows-store-dotnet-get-started.md) 프로젝트에 추가하여 레코드가 삽입될 때마다 디바이스에 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="configure-hub"></a>알림 허브 구성

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>푸시 알림에 대해 앱 등록

Microsoft Store에 앱을 제출한 다음, 푸시를 전송하는 [WNS(Windows Notification Services)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview)를 통합하도록 서버 프로젝트를 구성해야 합니다.

1. Visual Studio 솔루션 탐색기에서 UWP 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어** > **스토어와 앱을 연결...** 을 클릭합니다.

    ![Microsoft Store와 앱 연결](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. 마법사에서 **다음**을 클릭하고, Microsoft 계정으로 로그인하고, **새로운 앱 이름 예약**에서 앱 이름을 입력한 후 **예약**을 클릭합니다.
3. 앱을 성공적으로 등록한 후에 새로운 앱 이름을 선택하고 **다음** 및 **연결**을 차례로 클릭합니다. 이렇게 하면 필요한 Microsoft Store 등록 정보가 애플리케이션 매니페스트에 추가됩니다.
4. [애플리케이션 등록 포털](https://apps.dev.microsoft.com/)로 이동하고 Microsoft 계정으로 로그인합니다. 이전 단계에서 연결된 Windows 스토어 앱을 클릭합니다.
5. 등록 페이지에서 **애플리케이션 암호** 및 **패키지 SID**에 있는 값을 기록합니다. 이 값은 모바일 앱 백 엔드를 구성하는 데 사용합니다.

    ![Microsoft Store와 앱 연결](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > 클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마세요. **애플리케이션 ID**는 Microsoft 계정 인증을 구성하는 암호와 함게 사용됩니다.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns)에는 푸시 알림용 UWP 앱 구성에 대한 지침도 포함되어 있습니다.

## <a name="configure-the-backend-to-send-push-notifications"></a>푸시 알림을 전송하도록 백 엔드 구성

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>푸시 알림을 전송하도록 서버 업데이트

백 엔드 프로젝트 형식&mdash;([.NET 백 엔드](#dotnet) 또는 [Node.js 백 엔드](#nodejs))과 일치하는 아래의 절차를 사용합니다.

### <a name="dotnet"></a>.NET 백 엔드 프로젝트

1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다. Microsoft.Azure.NotificationHubs를 검색한 다음 **설치**를 클릭합니다. Notification Hubs 클라이언트 라이브러리를 설치합니다.
2. **컨트롤러**를 확장하고 TodoItemController.cs를 열고 다음 using 문을 추가합니다.

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. **PostTodoItem** 메서드에서 **InsertAsync**에 대한 호출 뒤에 다음 코드를 추가합니다.

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    이 코드는 새 항목을 삽입한 후에 알림 허브에 푸시 알림을 전송하도록 지시합니다.

4. 서버 프로젝트를 다시 게시합니다.

### <a name="nodejs"></a>Node.js 백 엔드 프로젝트
1. 아직 수행하지 않은 경우 [빠른 시작 프로젝트를 다운로드](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)하거나 [Azure 포털에서 온라인 편집기](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)를 사용합니다.
2. todoitem.js 파일의 기존 코드를 다음으로 바꿉니다.

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    이는 새 todo 항목이 삽입된 경우 item.text가 포함된 WNS 알림 메시지를 보냅니다.

3. 로컬 컴퓨터에서 파일을 편집할 때 서버 프로젝트를 다시 게시합니다.

## <a id="update-app"></a>앱에 푸시 알림 추가
다음으로 앱은 시작에서 푸시 알림에 등록해야 합니다. 사용할 수 있는 인증이 있는 경우 푸시 알림을 등록하기 전에 사용자가 로그인해야 합니다.

1. **App.xaml.cs** 프로젝트 파일을 열고 다음 `using` 문을 추가합니다.

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. 동일한 파일에서 다음 **InitNotificationsAsync** 메서드 정의를 **App** 클래스에 추가합니다.

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    이 코드는 WNS에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 App Service 모바일 앱에 등록합니다.

3. **App.xaml.cs**의 **OnLaunched** 이벤트 처리기 맨 위에서 다음 예제와 같이 메서드 정의에 **비동기** 한정자를 추가하고 새 **InitNotificationsAsync**메서드에 다음 호출을 추가합니다.

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    이제 애플리케이션을 시작할 때마다 단기 ChannelURI가 등록됩니다.

4. UWP 앱 프로젝트를 다시 빌드합니다. 이제 앱에서 알림을 받을 수 있습니다.

## <a id="test"></a>앱에서 푸시 알림 테스트

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>다음 단계

푸시 알림에 대해 자세히 알아봅니다.

* [Azure Mobile Apps에 관리되는 클라이언트를 사용하는 방법](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications) 템플릿은 유연성을 제공하여 플랫폼간 푸시 및 지역화된 푸시를 보냅니다. 템플릿을 등록하는 방법에 대해 자세히 알아봅니다.
* [푸시 알림 문제 진단](../notification-hubs/notification-hubs-push-notification-fixer.md) 디바이스에서 알림이 삭제되거나 끝나지 않는 다양한 이유가 있습니다. 이 항목에서는 푸시 알림 실패의 근본 원인을 분석 및 파악하는 방법을 보여 줍니다.

다음 자습서 중 하나를 진행하는 것이 좋습니다.

* [앱에 인증 추가](app-service-mobile-windows-store-dotnet-get-started-users.md) ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Mobile App 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
