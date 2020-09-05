---
title: Azure Notification Hubs를 사용하여 특정 Windows 휴대폰에 푸시 알림 보내기 | Microsoft Docs
description: 이 자습서에서는 Azure Notification Hubs를 사용하여 애플리케이션 백 엔드로 등록된 특정(모두는 아님) Windows Phone 8 또는 Windows Phone 8.1 디바이스에 알림을 푸시하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
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
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998240"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 특정 Windows 휴대폰에 푸시 알림 보내기

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

이 자습서에서는 Azure Notification Hubs를 사용하여 특정 Windows Phone 8 또는 Windows Phone 8.1 디바이스에 푸시 알림을 보내는 방법을 보여줍니다. Windows Phone 8.1(비 Silverlight)을 대상으로 하는 경우 이 자습서의 [Windows 범용](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 버전을 참조하세요.

알림 허브에서 등록을 만들 때 하나 이상의 *태그*를 포함하여 이 시나리오를 활성화합니다. 태그에 알림이 전송되면 태그에 대해 등록된 모든 디바이스에서 알림을 받게 됩니다. 태그에 대한 자세한 내용은 [등록의 태그](notification-hubs-tags-segment-push-message.md)를 참조하세요.

> [!NOTE]
> Notification Hubs Windows Phone SDK에서는 Windows Phone 8.1 Silverlight 앱에서의 WNS(Windows 푸시 알림 서비스) 사용을 지원하지 않습니다. Windows Phone 8.1 Silverlight 앱에서 MPNS 대신 WNS를 사용하려면 REST API를 사용하는 [Notification Hubs - Windows Phone Silverlight 자습서]를 따릅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 모바일 앱에 범주 선택 추가
> * 태그를 사용하여 알림 등록
> * 태그가 지정된 알림 보내기
> * 앱 테스트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 [자습서: Azure Notification Hubs를 사용하여 Windows Phone 앱에 알림 푸시](notification-hubs-windows-mobile-push-notifications-mpns.md)를 완료하세요. 이 자습서에서는 관심이 있는 속보 범주를 등록하고 해당 범주의 푸시 알림만 받을 수 있도록 모바일 애플리케이션을 업데이트합니다.

## <a name="add-category-selection-to-the-mobile-app"></a>모바일 앱에 범주 선택 추가

첫 번째 단계는 기존의 기본 페이지에 사용자가 등록할 범주를 선택할 수 있도록 하는 UI 요소를 추가하는 것입니다. 사용자가 선택한 범주는 디바이스에 저장됩니다. 앱을 시작하면 디바이스 등록이 선택한 범주와 함께 태그로서 알림 허브에 생성됩니다.

1. `MainPage.xaml` 파일을 연 다음, `TitlePanel` 및 `ContentPanel`이라는 `Grid` 요소를 다음 코드로 바꿉니다.

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. `Notifications`라는 클래스를 프로젝트에 추가합니다. `public` 한정자를 클래스 정의에 추가합니다. 그런 다음, 다음 `using` 문을 새 파일에 추가합니다.

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. 다음 코드를 새 `Notifications` 클래스에 복사합니다.

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    이 클래스는 격리된 스토리지를 사용하여, 이 디바이스에서 받아야 할 뉴스의 범주를 저장합니다. 또한 [템플릿](notification-hubs-templates-cross-platform-push-messages.md) 알림 등록을 사용하여 이러한 범주에 등록하는 메서드가 포함됩니다.
4. `App.xaml.cs` 프로젝트 파일에서 `App` 클래스에 다음 속성을 추가합니다. `<hub name>`과 `<connection string with listen access>` 자리 표시자를 알림 허브 이름과 앞서 얻었던 *DefaultListenSharedAccessSignature*의 연결 문자열로 바꿉니다.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > 클라이언트 앱과 함께 배포되는 자격 증명은 일반적으로 안전하지 않기 때문에 클라이언트 앱과 함께 listen access용 키만 배포해야 합니다. Listen access를 통해 앱에서 알림을 등록할 수 있지만, 기존 등록을 수정할 수 없으며 알림을 전송할 수도 없습니다. 안전한 백 엔드 서비스에서 알림을 보내고 기존 등록을 변경하는 데에는 모든 액세스 키가 사용됩니다.

5. `MainPage.xaml.cs`에서 다음 줄을 추가합니다.

    ```csharp
    using Windows.UI.Popups;
    ```
6. MainPage.xaml.cs 프로젝트 파일에 다음 메서드를 추가합니다.

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    이 메서드는 범주 목록을 만들고 `Notifications` 클래스를 사용하여, 로컬 스토리지에 목록을 저장하고 알림 허브에 해당 태그를 등록합니다. 범주가 변경되면 새 범주로 등록이 다시 생성됩니다.

이제 사용자가 범주 선택을 변경할 때마다 앱은 범주 집합을 디바이스의 로컬 스토리지에 저장하고 알림 허브에 등록할 수 있습니다.

## <a name="register-for-notifications"></a>알림 등록

다음 단계에서는 로컬 스토리지에 저장된 범주를 사용하여 시작 시 알림 허브에 등록합니다.

> [!NOTE]
> MPNS(Microsoft 푸시 알림 서비스)에서 할당하는 채널 URI는 언제든지 변경될 수 있으므로 알림 실패를 피하려면 알림을 자주 등록해야 합니다. 이 예제에서는 앱이 시작될 때마다 알림을 등록합니다. 자주(하루 두 번 이상) 실행되는 앱에서는 이전 등록 이후 만 하루가 지나지 않은 경우 대역폭 유지를 위한 등록을 건너뛸 수 있습니다.

1. App.xaml.cs 파일을 열고 `async` 한정자를 `Application_Launching` 메서드에 추가하고 [Notification Hubs 시작]에서 추가한 Notification Hubs 등록 코드를 다음 코드로 바꿉니다.

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    이 코드를 통해 앱이 시작될 때마다 로컬 스토리지에서 범주를 검색하고, 이러한 범주에 대한 등록을 요청하게 됩니다.
2. MainPage.xaml.cs 프로젝트 파일에서 `OnNavigatedTo` 메서드를 구현하는 다음 코드를 추가합니다.

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    이 코드는 전에 저장한 범주의 상태를 기반으로 기본 페이지를 업데이트합니다.

이제 앱이 완료되며, 사용자가 범주 선택을 변경할 때마다 알림 허브 등록에 사용된 디바이스의 로컬 스토리지에 범주 집합을 저장할 수 있습니다. 다음에는 범주 알림을 이 앱에 보낼 수 있는 백 엔드를 정의합니다.

## <a name="send-tagged-notifications"></a>태그가 지정된 알림 보내기

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>앱 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 컴파일 및 시작합니다.

    ![범주가 있는 모바일 앱][1]

    앱 UI는 구독할 범주를 선택하도록 하는 토글 집합을 제공합니다.
2. 하나 이상의 범주 토글을 사용하도록 설정한 후 **구독**을 클릭합니다.

    앱은 선택한 범주를 태그로 변환하고 알림 허브에서 선택한 태그에 대한 새로운 디바이스 등록을 요청합니다. 등록된 범주가 반환되어 대화 상자에 표시됩니다.

    ![구독된 메시지][2]
3. 범주가 구독 완료되었다는 확인을 받은 후 각 범주에 대한 알림을 보내는 콘솔 앱을 실행합니다. 구독한 범주에 대한 알림만 받는지 확인합니다.

    ![알림 메시지][3]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 등록에 태그가 연결된 특정 디바이스에 알림을 푸시하는 방법을 배웠습니다. 여러 디바이스를 사용할 수 있는 특정 사용자에게 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
>[특정 사용자에 알림 푸시](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Notification Hubs 시작]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
