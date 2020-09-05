---
title: Azure Notification Hubs를 사용하여 특정 사용자에게 알림 보내기 | Microsoft Docs
description: UWP(유니버설 Windows 플랫폼) 애플리케이션을 사용하여 특정 사용자에게 알림을 보내는 방법을 알아봅니다.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 865aaf748fd8fad5f10350cb5b57d31b3eadf7a0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018045"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 특정 사용자에게 알림 보내기

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>개요

이 자습서에서는 Azure Notification Hubs를 사용하여 특정 디바이스에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. ASP.NET WebAPI 백 엔드는 클라이언트를 인증하는 데 사용 됩니다. 백 엔드가 클라이언트 애플리케이션 사용자를 인증하는 경우 알림 등록에 자동으로 태그를 추가합니다. 백 엔드는 이 태그를 사용하여 특정 사용자에게 알림을 보냅니다.

> [!NOTE]
> 이 자습서에 대해 완료된 코드는 [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers)에서 찾을 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * WebAPI 프로젝트 만들기
> * WebAPI 백 엔드에 클라이언트 인증
> * WebAPI 백 엔드를 사용하여 알림 등록
> * WebAPI 백 엔드에서 알림 보내기
> * 새 WebAPI 백 엔드 게시
> * 클라이언트 프로젝트에 대한 코드 업데이트
> * 애플리케이션 테스트

## <a name="prerequisites"></a>필수 구성 요소

이 자습서는 [자습서: Azure Notification Hubs를 사용하여 유니버설 Windows 플랫폼 앱에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 자습서에서 완료한 프로젝트의 코드를 업데이트합니다. 따라서 이 자습서를 시작하기 전에 완료합니다.

> [!NOTE]
> Azure App Service의 Mobile Apps를 백 엔드 서비스로 사용 중인 경우 이 자습서의 [Mobile Apps 버전](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push)을 참조하세요.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>UWP 클라이언트의 코드 업데이트

이 섹션에서는 [자습서: Azure Notification Hubs를 사용하여 유니버설 Windows 플랫폼 앱에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 자습서에서 완료한 프로젝트의 코드를 업데이트합니다. 프로젝트는 Windows 스토어와 이미 연결되어 있어야 합니다. 또한 알림 허브를 사용하도록 구성되어야 합니다. 이 섹션에서는 새 WebAPI 백 엔드를 호출할 코드를 추가하고, 알림을 등록하고 보내는 데 이 코드를 사용합니다.

1. Visual Studio에서 [자습서: Azure Notification Hubs를 사용하여 유니버설 Windows 플랫폼 앱에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)에서 만든 솔루션을 엽니다.
2. 솔루션 탐색기에서 UWP(유니버설 Windows 플랫폼) 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
3. 왼쪽에서 **찾아보기**를 선택합니다.
4. **검색** 상자에 **Http 클라이언트**를 입력합니다.
5. 결과 목록에서 **System.Net.Http**를 클릭하고, **설치**를 클릭합니다. 설치를 완료합니다.
6. 다시 NuGet **검색** 상자에 **Json.net**을 입력합니다. **Newtonsoft.json** 패키지를 설치한 다음, NuGet 패키지 관리자 창을 닫습니다.
7. 솔루션 탐색기의 **WindowsApp** 프로젝트에서 **MainPage.xaml**을 두 번 클릭하여 Visual Studio 편집기에서 엽니다.
8. `MainPage.xaml` XML 코드에서 `<Grid>` 섹션을 다음 코드로 바꿉니다. 이 코드는 사용자가 인증하는 데 사용하는 사용자 이름 및 암호 텍스트 상자를 추가합니다. 또한 알림 메시지 및 알림을 받을 사용자 이름 태그용 텍스트 상자도 추가합니다.

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```
9. 솔루션 탐색기에서 **(Windows 8.1)** 및 **(Windows Phone 8.1)** 프로젝트의 `MainPage.xaml.cs` 파일을 엽니다. 두 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
10. **WindowsApp** 프로젝트의 `MainPage.xaml.cs`에서 다음 멤버를 `MainPage` 클래스에 추가합니다. `<Enter Your Backend Endpoint>`를 이전에 얻은 실제 백 엔드 엔드포인트으로 바꿔야 합니다. 예들 들어 `http://mybackend.azurewebsites.net`입니다.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
11. **(Windows 8.1)** 및 **(Windows Phone 8.1)** 프로젝트의 `MainPage.xaml.cs`에서 아래 코드를 MainPage 클래스에 추가합니다.

    `PushClick` 메서드는 **Send Push(푸시 전송)** 단추의 클릭 처리기입니다. `to_tag` 매개 변수와 일치하는 사용자 이름 태그가 있는 모든 디바이스로 알림을 트리거하도록 백 엔드를 호출합니다. 알림 메시지는 요청 본문의 JSON 콘텐츠로 전송됩니다.

    `LoginAndRegisterClick` 메서드는 **로그인 및 등록** 단추의 클릭 처리기입니다. 기본 인증 토큰(이는 인증 체계에서 사용하는 모든 토큰을 나타냄)을 로컬 스토리지에 저장한 다음, `RegisterClient`를 사용하여 백 엔드가 사용되는 알림에 등록합니다.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```
12. `App.xaml.cs`를 열고 `OnLaunched()` 이벤트 처리기의 `InitNotificationsAsync()`에 대한 호출을 찾습니다. `InitNotificationsAsync()`에 대한 호출을 주석으로 처리하거나 삭제합니다. 단추 처리기는 알림 등록을 초기화합니다.

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
13. **WindowsApp** 프로젝트를 마우스 오른쪽 단추로 클릭하고, **추가**를 클릭한 다음, **클래스**를 클릭합니다. 클래스 이름을 `RegisterClient.cs`로 지정한 다음, **확인**을 클릭하여 클래스를 생성합니다.

    이 클래스는 푸시 알림에 등록하기 위해 앱 백 엔드에 접속하는 데 필요한 REST 호출을 래핑합니다. 또한 *앱 백 엔드에서 등록* 에 설명된 대로 알림 허브에서 생성된 [registrationId](/previous-versions/azure/azure-services/dn743807(v=azure.100))를 로컬로 저장합니다. 이 구성 요소는 **로그인 및 등록** 단추를 클릭할 때 로컬 스토리지에 저장된 인증 토큰을 사용합니다.
14. RegisterClient.cs 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
15. 다음 코드를 `RegisterClient` 클래스 정의 내에 추가합니다.

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```
16. 변경 내용을 모두 저장합니다.

## <a name="test-the-application"></a>애플리케이션 테스트

1. 두 Windows에서 애플리케이션을 시작합니다.
2. 아래 화면에 표시된 것처럼 **사용자 이름** 및 **암호**를 입력합니다. Windows Phone에서 입력하는 사용자 이름 및 암호와 달라야 합니다.
3. **Log in and register(로그인 및 등록)** 를 클릭하고 대화 상자에 로그인되었다고 표시되는지 확인합니다. 이 코드는 **푸시 전송** 단추도 활성화합니다.

    ![입력한 사용자 이름과 암호를 표시하는 Notification Hubs 애플리케이션의 스크린샷.][14]
5. 그런 다음, **받는 사람 사용자 이름 태그** 필드에 등록한 사용자 이름을 입력합니다. 알림 메시지를 입력하고 **Send Push(푸시 전송)** 를 클릭합니다.
6. 일치하는 사용자 이름 태그로 등록된 디바이스만 이 알림 메시지를 받습니다.

    ![푸시된 메시지를 표시하는 Notification Hubs 애플리케이션의 스크린샷.][15]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 등록에 태그가 연결된 특정 사용자에게 알림을 푸시하는 방법을 배웠습니다. 위치 기반 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
>[위치 기반 알림 푸시](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
