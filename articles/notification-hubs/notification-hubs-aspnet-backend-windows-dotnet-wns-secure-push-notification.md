---
title: Azure Notification Hubs 보안 푸시
description: Azure에서 보안 푸시 알림을 보내는 방법에 대해 알아봅니다. 코드 샘플은 .NET API를 사용하여 C#으로 작성되었습니다.
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: cf23ef5df3bdcaad23841da111fa06cc36b4cd57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459245"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Azure Notification Hubs에서 안전하게 알림 푸시

> [!div class="op_single_selector"]
> * [Windows 범용](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>개요

Microsoft Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 애플리케이션 및 엔터프라이즈 애플리케이션 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다.

규제나 보안 제약 조건 때문에 애플리케이션의 알림에 표준 푸시 알림 인프라를 통해 전송할 수 없는 어떤 항목을 포함해야 할 수 있습니다. 이 자습서에서는 클라이언트 디바이스와 앱 백 엔드 간에 인증된 보안 연결을 통해 중요한 정보를 전송하여 동일한 경험을 얻는 방법에 대해 설명합니다.

개략적으로 흐름은 다음과 같습니다.

1. 앱 백 엔드:
   * 백 엔드 데이터베이스에 보안 페이로드를 저장합니다.
   * 이 알림의 ID를 디바이스에 보냅니다(보안 정보는 전송되지 않음).
2. 알림 수신 시 디바이스의 앱:
   * 디바이스가 보안 페이로드를 요청하는 백 엔드에 접속합니다.
   * 앱이 디바이스에서 페이로드를 알림으로 표시할 수 있습니다.

앞의 흐름과 이 자습서에서는 사용자가 로그인한 후 디바이스가 인증 토큰을 로컬 스토리지에 저장한다고 가정합니다. 이렇게 하면 디바이스가 이 토큰을 사용하여 알림의 보안 페이로드를 검색할 수 있으므로 매우 원활한 환경이 보장됩니다. 애플리케이션이 인증 토큰을 장치에 저장하지 않거나 이 토큰이 만료될 수 없으면 알림 수신 시 장치 앱은 사용자에게 앱을 시작할지 묻는 메시지가 포함된 일반 알림을 표시해야 합니다. 그리고 나서 앱은 사용자를 인증하고 알림 페이로드를 표시합니다.

이 보안 푸시 자습서에서는 푸시 알림을 안전하게 보내는 방법을 보여 줍니다. 이 자습서는 [사용자에게 알림](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 자습서를 기반으로 빌드되므로 해당 자습서의 단계를 먼저 완료해야 합니다.

> [!NOTE]
> 이 자습서에서는 [Notification Hubs 시작(Windows 스토어)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)에 설명된 대로 Notification Hubs를 만들고 구성했다고 가정합니다.
> 또한 Windows Phone 8.1에 Windows(Windows Phone이 아님) 자격 증명이 필요하며, 백그라운드 작업은 Windows Phone 8.0 또는 Silverlight 8.1에서 작동하지 않습니다. Windows 스토어 애플리케이션은 앱에서 잠금 화면이 사용되는 경우(Appmanifest에서 확인란 클릭)에만 백그라운드 작업을 통해 알림을 받을 수 있습니다.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Windows Phone 프로젝트 수정

1. **NotifyUserWindowsPhone** 프로젝트에서 App.xaml.cs에 다음 코드를 추가하여 푸시 백그라운드 작업을 등록합니다. 다음 코드 줄을 `OnLaunched()` 메서드의 끝에 추가합니다.

    ```c#
    RegisterBackgroundTask();
    ```
2. App.xaml.cs에서 `OnLaunched()` 메서드의 바로 뒤에 다음 코드를 추가합니다.

    ```c#
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. App.xaml.cs 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```c#
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. Visual Studio의 **파일** 메뉴에서 **모두 저장**을 클릭합니다.

## <a name="create-the-push-background-component"></a>푸시 백그라운드 구성 요소 만들기

다음은 푸시 백그라운드 구성 요소를 만드는 단계입니다.

1. 솔루션 탐색기에서 솔루션의 최상위 노드(이 경우 **Solution SecurePush**)를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 프로젝트**를 차례로 클릭합니다.
2. **스토어 앱**을 확장하고 **Windows Phone 앱**, **Windows 런타임 구성 요소(Windows Phone)** 를 차례로 클릭합니다. 프로젝트 이름을 **PushBackgroundComponent**로 지정하고 **확인**을 클릭하여 프로젝트를 만듭니다.

    ![][12]
3. 솔루션 탐색기에서 **PushBackgroundComponent(Windows Phone 8.1)** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 클릭합니다. 새 클래스 이름을 `PushBackgroundTask.cs`로 지정합니다. **추가** 를 클릭하여 클래스를 생성합니다.
4. `PushBackgroundComponent` 네임스페이스 정의의 전체 콘텐츠를 다음 코드로 바꾸고 자리 표시자 `{back-end endpoint}`를 백 엔드 배포 시 얻은 백 엔드 엔드포인트로 바꿉니다.

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. 솔루션 탐색기에서 **PushBackgroundComponent(Windows Phone 8.1)** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.
6. 왼쪽에서 **온라인**을 클릭합니다.
7. **검색** 상자에 **Http 클라이언트**를 입력합니다.
8. 결과 목록에서 **Microsoft HTTP 클라이언트 라이브러리**를 클릭하고 **설치**를 클릭합니다. 설치를 완료합니다.
9. 다시 NuGet **검색** 상자에 **Json.net**을 입력합니다. **Json.NET** 패키지를 설치하고 NuGet 패키지 관리자 창을 닫습니다.
10. `PushBackgroundTask.cs` 파일 맨 위에 다음 `using` 문을 추가합니다.

    ```c#
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. 솔루션 탐색기의 **NotifyUserWindowsPhone(Windows Phone 8.1)** 프로젝트에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가...** 를 클릭합니다. 참조 관리자 대화 상자에서 **PushBackgroundComponent** 옆의 상자를 선택하고 **확인**을 클릭합니다.
12. 솔루션 탐색기의 **NotifyUserWindowsPhone(Windows Phone 8.1)** 프로젝트에서 **Package.appxmanifest**를 두 번 클릭합니다. **알림**에서 **알림 가능**을 **예**로 설정합니다.

    ![][3]
13. 계속 **Package.appxmanifest**에서 맨 위 근처에서 **선언**을 클릭합니다. **사용 가능한 선언** 드롭다운에서 **백그라운드 태스크**, **추가**를 차례로 클릭합니다.
14. **Package.appxmanifest**의 **속성**에서 **푸시 알림**을 선택합니다.
15. **Package.appxmanifest**의 **앱 설정**에서 **진입점** 필드에 **PushBackgroundComponent.PushBackgroundTask**를 입력합니다.

    ![][13]
16. **파일** 메뉴에서 **모두 저장**을 클릭합니다.

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행하려면 다음을 수행합니다.

1. Visual Studio에서 **AppBackend** Web API 애플리케이션을 실행합니다. ASP.NET 웹 페이지가 표시됩니다.
2. Visual Studio에서 **NotifyUserWindowsPhone (Windows Phone 8.1)** Windows Phone 앱을 실행합니다. Windows Phone 에뮬레이터가 실행되고 자동으로 앱을 로드합니다.
3. **NotifyUserWindowsPhone** 앱 UI에서 사용자 이름과 암호를 입력합니다. 이는 임의 문자열일 수 있지만 같은 값이어야 합니다.
4. **NotifyUserWindowsPhone** 앱 UI에서 **로그인 및 등록**을 클릭합니다. 그리고 나서 **푸시 보내기**를 클릭합니다.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
