---
title: Notification Hubs enterprise push 아키텍처
description: 엔터프라이즈 환경에서 Azure Notification Hubs를 사용 하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 018315b7ed468e24fb922337848d14703ffdcd4d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003629"
---
# <a name="enterprise-push-architectural-guidance"></a>엔터프라이즈 푸시 아키텍처 지침

오늘날 기업에서는 최종 사용자(외부)를 위해 또는 직원(내부)을 위해 모바일 애플리케이션을 만드는 일이 점점 많아지고 있습니다. 기업은 가동 중인 기존 백 엔드 시스템이 모바일 애플리케이션 아키텍처에 통합되어야 하는 메인프레임 또는 일부 LoB 애플리케이션이 되도록 합니다. 이 가이드에서는 일반적인 시나리오에 사용 가능한 솔루션을 권장하는 이 통합을 가장 잘 수행할 수 있는 방법에 대해 설명합니다.

백엔드 시스템에서 관심 이벤트가 발생하는 경우 해당 모바일 애플리케이션을 통해 사용자에게 푸시 알림을 보내기 위한 일반적인 요구 사항입니다. 예를 들어 iPhone에서 은행 뱅킹 앱을 보유 하 고 있는 은행 고객은 계정에서 특정 금액을 초과 하는 경우 또는 Windows Phone에 대 한 예산 승인 앱을 보유 하는 재무 부서의 직원이 승인 요청이 수신 될 때이를 통보 하려고 합니다.

은행 계좌 또는 승인 처리는 사용자에게 푸시를 초기화해야 하는 일부 백 엔드 시스템에서 수행될 수 있습니다. 이벤트가 알림을 트리거할 때 같은 종류의 논리를 모두 동일하게 빌드하여 푸시해야 하는 여러 백 엔드 시스템이 있을 수 있습니다. 여기에서는 여러 백 엔드 시스템을 단일 푸시 시스템에 함께 통합할 때의 복잡성을 설명합니다. 최종 사용자는 다양한 알림을 구독했을 수 있고 여기에는 여러 모바일 애플리케이션이 있을 수도 있습니다. 예를 들어 인트라넷 모바일 앱의 경우 특정 모바일 애플리케이션이 그러한 여러 백 엔드 시스템에서 알림을 받기를 원할 수 있습니다. 백 엔드 시스템은 푸시 의미론/기술을 모르거나 알고 있어야 합니다. 그래서 일반적인 솔루션은 전통적으로 모든 관심 이벤트의 백 엔드 시스템을 폴링한 구성 요소를 소개했으며 클라이언트에 푸시 메시지를 전송하는 역할을 담당합니다.

보다 나은 솔루션은 확장 가능한 솔루션을 만드는 동안 복잡성을 줄일 수 있는 Azure Service Bus - 항목/구독 모델을 사용하는 것입니다.

이는 일반 솔루션 아키텍처입니다(여러 모바일 앱으로 일반화되었지만 모바일 앱이 하나뿐일 경우 동일하게 적용할 수 있음).

## <a name="architecture"></a>Architecture

![이벤트, 구독 및 푸시 메시지를 통한 흐름을 보여 주는 엔터프라이즈 아키텍처 다이어그램][1]

이 아키텍처 다이어그램의 핵심 부분은 항목/구독 프로그래밍 모델(자세한 내용은 [Service Bus Pub/Sub 프로그래밍]참조)을 제공하는 Azure Service Bus입니다. 이 경우 수신기는 모바일 백 엔드 (일반적으로 모바일 앱에 푸시를 시작 하는 [Azure 모바일 서비스])는 백 엔드 시스템에서 직접 메시지를 수신 하지 않고, 모바일 백 엔드가 하나 이상의 백 엔드 시스템에서 메시지를 받을 수 있도록 하는 [Azure Service Bus]에서 제공 하는 중간 추상화 계층입니다. 각각의 백 엔드 시스템(예: 계정, HR, 재정)에 대해 Service Bus 항목을 만들어야 하며 기본적으로 메시지를 푸시 알림으로 보내기 시작할 관심 "항목"입니다. 백 엔드 시스템은 이러한 항목에 메시지를 보냅니다. 모바일 백엔드는 Service Bus 구독을 만들어 이러한 항목을 하나 이상 구독할 수 있습니다. 이를 통해 모바일 백 엔드가 해당 백 엔드 시스템에서 알림을 받도록 할 수 있습니다. 모바일 백엔드는 계속 해당 구독에서 메시지를 수신하고 메시지가 도착하는 즉시 다시 해당 알림 허브에 알림을 보냅니다. 그런 다음 알림 허브는 마침내 메시지를 모바일 앱으로 전달합니다. 주요 구성 요소 목록은 다음과 같습니다.

1. 백엔드 시스템(LoB/레거시 시스템)
   * Service Bus 항목 만들기
   * 메시지 보내기
1. 모바일 백엔드
   * 서비스 구독 만들기
   * 메시지 받기(백엔드 시스템에서)
   * 클라이언트에 알림 보내기(Azure 알림 허브를 통해)
1. 모바일 애플리케이션
   * 알림 수신 및 표시

### <a name="benefits"></a>이점

1. 수신자(알림 허브를 통한 모바일 앱/서비스)와 발신자(백엔드 시스템) 사이를 분리하면 최소한의 변경 내용을 가진 추가 백엔드 시스템을 통합할 수 있습니다.
1. 이렇게 하면 하나 이상의 백 엔드 시스템에서 이벤트를 받을 수 있는 여러 모바일 앱 시나리오를 만들 수도 있습니다.  

## <a name="sample"></a>예제

### <a name="prerequisites"></a>필수 구성 요소

개념뿐만 아니라 일반적인 만들기 및 구성 단계에 익숙해지려면 다음 자습서를 완료합니다.

1. [Service Bus Pub/Sub 프로그래밍] - 이 자습서는 Service Bus 항목/구독으로 작업하는 세부 사항, 항목/구독을 포함하는 네임스페이스를 만드는 방법 및 메시지를 송신 및 수신하는 방법에 대해 설명합니다.
2. [Notification Hubs - Windows 범용 자습서] - 이 자습서는 Windows 스토어 앱을 설정하고 Notification Hubs를 사용하여 등록한 다음 알림을 수신하는 방법에 대해 설명합니다.

### <a name="sample-code"></a>예제 코드

전체 샘플 코드는 [알림 허브 샘플]에서 사용 가능합니다. 세 가지 구성 요소로 구성되어 있습니다.

1. **EnterprisePushBackendSystem**

    a. 이 프로젝트는 **Windowsazure.servicebus ServiceBus** NuGet 패키지를 사용 하 고 [Service Bus Pub/Sub 프로그래밍]을 기반으로 합니다.

    b. 이 애플리케이션은 모바일 앱으로 메시지를 전달하기 시작하는 LoB 시스템을 시뮬레이션하기 위한 간단한 C# 콘솔 앱입니다.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    다. `CreateTopic`은 Service Bus 항목을 만드는 데 사용됩니다.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage` 는 이 Service Bus 항목으로 메시지를 보내는 데 사용됩니다. 이 코드는 단순히 샘플 목적으로 항목에 임의 메시지 집합을 정기적으로 보냅니다. 일반적으로 이벤트가 발생하면 메시지를 보내는 백 엔드 시스템이 됩니다.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. 이 프로젝트는 *WindowsAzure.ServiceBus* 및 **Microsoft.Web.WebJobs.Publish** NuGet 패키지를 사용하며 [Service Bus Pub/Sub 프로그래밍]을 기반으로 합니다.

    b. LoB/백 엔드 시스템에서 메시지를 지속적으로 수신하려면 이를 실행해야 하므로 다음 콘솔 앱은 [Azure WebJob]으로 실행합니다. 이 애플리케이션은 모바일 백 엔드의 일부입니다.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    다. `CreateSubscription`은 백 엔드 시스템이 메시지를 보내는 항목에 대한 Service Bus 구독을 만드는 데 사용됩니다. 비즈니스 시나리오에 따라 이 구성 요소는 해당 항목에 대한 하나 이상의 구독을 만듭니다(예: 일부는 HR 시스템에서, 일부는 재무 시스템 등에서 메시지를 수신할 수 있음).

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`은 해당 구독을 사용하여 항목에서 메시지를 보내는 데 사용되며 읽기에 성공한 경우 Azure Notification Hubs를 사용하여 모바일 애플리케이션으로 보낼 알림(샘플 시나리오의 경우 Windows 네이티브 토스트 알림)을 만듭니다.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. 이 앱을 **WebJob**으로 게시하려면 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **WebJob으로 게시**를 선택합니다.

    ![빨간색으로 표시 된 Azure WebJob으로 게시와 함께 표시 되는 오른쪽 클릭 옵션의 스크린샷][2]

    f. 게시 프로필을 선택한 후 Azure 웹 사이트가 없는 경우 이 WebJob을 호스트할 새 Azure 웹 사이트를 만들고, 웹 사이트가 있는 경우 **게시**합니다.

    :::image type="complex" source="./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png" alt-text="Azure에서 사이트를 만드는 워크플로를 보여 주는 스크린샷":::
    Microsoft Azure Websites 옵션이 선택 된 상태에서 웹 게시 대화 상자의 스크린샷, 빨간색으로 표시 된 새 옵션을 사용 하 여 기존 웹 사이트 선택 대화 상자를 가리키는 녹색 화살표, 사이트 이름 및 만들기 옵션을 빨간색으로 표시 하 여 Microsoft Azure에서 사이트 만들기 대화 상자를 가리키는 녹색 화살표가 표시 됩니다.
    :::image-end:::

    g. [Azure Portal]에 로그인할 때 다음과 같이 표시되어야 하므로 작업이 “계속 실행”되도록 구성합니다.

    ![Enterprise push 백엔드 webjobs 표시 되 고 이름, 일정 및 로그 값이 빨간색으로 표시 된 Azure Portal의 스크린샷][4]

3. **EnterprisePushMobileApp**

    a. 이 애플리케이션은 모바일 백 엔드의 일부로 실행 중인 WebJob에서 토스트 알림을 수신하여 이를 표시하는 Windows 스토어 애플리케이션입니다. 이 코드는 [Notification Hubs - Windows 유니버설 자습서]를 기반으로 합니다.  

    b. 애플리케이션이 토스트 알림을 받을 수 있는지 확인합니다.

    다. 앱 시작 시 다음 Notification Hubs 등록 코드가 호출되었는지 확인합니다(`HubName` 및 `DefaultListenSharedAccessSignature` 값 교체 후).

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>샘플 실행

1. WebJob이 성공적으로 실행 중이고 계속 실행되도록 예약되었는지 확인합니다.
2. Windows 스토어 앱을 시작 하는 **EnterprisePushMobileApp**를 실행 합니다.
3. **EnterprisePushBackendSystem** 콘솔 애플리케이션을 실행하면 LoB 백 엔드를 시뮬레이션 하고 메시지를 보내기 시작하기 때문에 다음 이미지와 같이 나타나는 토스트 알림이 보여야 합니다.

    ![엔터프라이즈 푸시 백 엔드 시스템 앱 및 앱에서 보낸 메시지를 실행 하는 콘솔의 스크린샷][5]

4. 원래 메시지는 웹 작업의 Service Bus 구독에서 모니터링하는 Service Bus 항목으로 전송되었습니다. 메시지가 수신되면 알림이 생성되어 모바일 앱으로 전송됩니다. 사용자의 웹 작업에 대한 [Azure Portal]의 로그 링크로 이동하면 WebJob 로그를 통해 처리 상태를 확인할 수 있습니다.

    ![빨간색으로 표시 된 메시지를 포함 하는 연속 WebJob 세부 정보 대화 상자의 스크린샷][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[알림 허브 샘플]: https://github.com/Azure/azure-notificationhubs-samples
[Azure 모바일 서비스]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: ../service-bus-messaging/service-bus-messaging-overview.md
[Service Bus Pub/Sub 프로그래밍]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs - Windows 범용 자습서]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Azure Portal]: https://portal.azure.com/
