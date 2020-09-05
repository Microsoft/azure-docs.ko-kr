---
title: Azure Notification Hubs를 사용하여 Windows Phone 앱에 푸시 알림 보내기 | Microsoft Docs
description: 이 자습서에서는 Azure Notification Hubs를 사용하여 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 애플리케이션에 푸시 알림을 보내는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: windows
keywords: 푸시 알림, 푸시 알림, windows phone 푸시
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998342"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 Windows Phone 앱에 푸시 알림 보내기

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

이 자습서에서는 Azure Notification Hubs를 사용하여 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 애플리케이션에 푸시 알림을 보내는 방법을 보여줍니다. Windows Phone 8.1(비 Silverlight)을 대상으로 하는 경우 이 자습서의 [Windows 범용](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 버전을 참조하세요.

이 자습서에서는 MPNS(Microsoft 푸시 알림 서비스)를 사용하여 푸시 알림을 받는 빈 Windows Phone 8 앱을 만듭니다. 앱을 만든 후 알림 허브를 사용하여 앱을 실행하는 모든 디바이스로 푸시 알림을 브로드캐스트합니다.

> [!NOTE]
> Notification Hubs Windows Phone SDK에서는 Windows Phone 8.1 Silverlight 앱에서의 WNS(Windows 푸시 알림 서비스) 사용을 지원하지 않습니다. Windows Phone 8.1 Silverlight 앱에서 MPNS 대신 WNS를 사용하려면 REST API를 사용하는 [Notification Hubs - Windows Phone Silverlight 자습서]를 따릅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 알림 허브 만들기
> * Windows Phone 애플리케이션 만들기
> * 알림 테스트 보내기

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/).
* [모바일 개발 구성 요소가 있는 Visual Studio 2015 Express](https://www.visualstudio.com/vs/older-downloads/)

이 자습서를 완료해야 다른 모든 Windows Phone 8 앱용 Notification Hubs 자습서를 진행할 수 있습니다.

## <a name="create-your-notification-hub"></a>알림 허브 만들기

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Windows Phone(MPNS) 설정 구성

1. **알림 설정** 아래에서 **Windows Phone(MPNS)** 을 선택합니다.
2. **인증 푸시 사용**을 선택합니다.
3. 도구 모음에서 **저장**을 선택합니다.

    ![Azure Portal - 인증되지 않은 푸시 알림 사용](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    이제 Windows Phone의 인증되지 않은 알림을 보낼 수 있는 허브가 생성 및 구성되었습니다.

    > [!NOTE]
    > 이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. MPNS 인증되지 않은 모드에는 각 채널로 보낼 수 있는 알림에 대한 제한이 있습니다. Notification Hubs는 인증서를 업로드할 수 있도록 하여 [MPNS 인증 모드](/previous-versions/windows/apps/ff941099(v=vs.105))를 지원합니다.

## <a name="create-a-windows-phone-application"></a>Windows Phone 애플리케이션 만들기

이 섹션에서는 알림 허브에 자신을 등록하는 Windows Phone 애플리케이션을 만듭니다.

1. Visual Studio에서 새 Windows Phone 8 애플리케이션을 만듭니다.

    ![Visual Studio - 새 프로젝트 - Windows Phone 앱][13]

    Visual Studio 2013 업데이트 2 이상에서는 대신 Windows Phone Silverlight 애플리케이션을 만듭니다.

    ![Visual Studio - 새 프로젝트 - 새 응용 프로그램 - Windows Phone Silverlight][11]
2. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
3. `WindowsAzure.Messaging.Managed`를 검색하고 **설치**를 클릭한 후 사용 약관에 동의합니다.

    ![Visual Studio - NuGet 패키지 관리자][20]
4. App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. `App.xaml.cs`에서 `Application_Launching` 메서드 맨 위에 다음 코드를 추가합니다.

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > 값 `MyPushChannel`은 [HttpNotificationChannel](/previous-versions/ff402781(v=vs.110)) 컬렉션에서 기존 채널을 조회하는 데 사용되는 인덱스입니다. 항목이 없으면 해당 이름을 가진 새 항목을 만듭니다.

    이전 섹션에서 적어둔 허브 이름과 `DefaultListenSharedAccessSignature`라는 연결 문자열을 삽입합니다.
    이 코드는 MPNS에서 앱의 채널 URI를 검색한 후 해당 채널 URI를 알림 허브에 등록합니다. 또한 이 코드는 애플리케이션이 시작될 때마다 채널 URI가 알림 허브에 등록되도록 보장합니다.

   > [!NOTE]
   > 이 자습서에서는 알림 메시지를 디바이스로 보냅니다. 타일 알림을 보내는 경우 채널에서 `BindToShellTile` 메서드를 대신 호출해야 합니다. 알림 및 타일 알림을 둘 다 지원하려면 `BindToShellTile` 및 `BindToShellToast`를 모두 호출합니다.

6. 솔루션 탐색기에서 **속성**을 확장하고 `WMAppManifest.xml` 파일을 연 후 **기능** 탭을 클릭하고 **ID_CAP_PUSH_NOTIFICATION** 기능이 선택되었는지 확인합니다. 앱은 이제 푸시 알림을 받을 수 있습니다.

    ![Visual Studio - Windows Phone 앱 기능][14]
7. `F5` 키를 눌러 앱을 실행합니다. 앱에 등록 메시지가 표시됩니다.
8. 앱을 닫거나 홈페이지로 전환합니다.

   > [!NOTE]
   > 푸시 알림 메시지를 받으려면 애플리케이션이 포그라운드에서 실행되고 있지 않아야 합니다.

## <a name="test-send-a-notification"></a>알림 테스트 보내기

1. Azure Portal에서 개요 탭으로 전환합니다.
2. **테스트 보내기**를 선택합니다.

    ![테스트 보내기 단추](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. **테스트 보내기** 창에서 다음 단계를 수행합니다.

    1. **플랫폼**에 대해 **Windows Phone**을 선택합니다.
    2. **알림 유형**에 대해 **알림**을 선택합니다.
    3. **보내기**를 선택합니다.
    4. 창의 맨 아래에 있는 목록에 **결과**가 표시됩니다.

        ![테스트 보내기 창](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. Windows Phone 에뮬레이터 또는 Windows 휴대폰에서 알림 메시지가 표시되는지 확인합니다.

    ![Windows 휴대폰의 알림](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>다음 단계

이 간단한 예제에서는 모든 Windows Phone 8 디바이스로 푸시 알림을 브로드캐스트했습니다. 특정 디바이스로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
>[특정 디바이스에 알림 푸시](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[Notification Hubs - Windows Phone Silverlight 자습서]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
