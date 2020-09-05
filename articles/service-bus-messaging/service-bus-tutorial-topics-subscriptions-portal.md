---
title: Azure Portal 및 토픽/구독을 사용하여 재고 업데이트
description: 이 자습서에서는 토픽과 구독에서 메시지를 보내고 받는 방법과 .NET을 사용하여 필터 규칙을 추가하고 사용하는 방법을 알아봅니다.
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 811777fff28cf56d7732461924b14e9e4b619c0c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000175"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>자습서: Azure Portal 및 토픽/구독을 사용하여 재고 업데이트

Microsoft Azure Service Bus는 애플리케이션과 서비스 간에 정보를 보내는 다중 테넌트 클라우드 메시징 서비스입니다. 비동기 작업은 구조적 FIFO(선입 선출) 메시징 및 게시/구독 기능과 함께 유연하게 조정할 수 있는 메시징 기능을 제공합니다. 이 자습서에서는 소매점 재고 시나리오에서 Azure Portal 및 .NET을 사용하여 게시/구독 채널을 통해 Service Bus 토픽과 구독을 사용하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Portal을 사용하여 Service Bus 토픽 및 해당 토픽에 대한 하나 이상의 구독 만들기
> * .NET 코드를 사용하여 토픽 필터 추가
> * 내용이 서로 다른 두 개의 메시지 만들기
> * 메시지 보내기 및 필요한 구독에 도착했는지 확인
> * 구독에서 메시지 받기

이 시나리오의 예는 여러 소매점에 대한 재고 분류 업데이트입니다. 이 시나리오에서 각 상점 또는 상점 집합은 해당 분류를 업데이트하기 위한 메시지를 받습니다. 이 자습서에서는 구독과 필터를 사용하여 이 시나리오를 구현하는 방법을 보여 줍니다. 먼저 3개의 구독이 포함된 토픽을 만들고. 몇 가지 규칙과 필터를 추가한 다음, 토픽과 구독에서 메시지를 보내고 받습니다.

![토픽](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음을 설치했어야 합니다.

- [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](https://www.visualstudio.com/vs) 이상
- [NET Core SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독

[토픽에 대한 각 구독](service-bus-messaging-overview.md#topics)은 각 메시지의 복사본을 받을 수 있습니다. 토픽은 완전히 프로토콜이며, 의미상 Service Bus 큐와 호환됩니다. Service Bus 토픽은 메시지 속성을 설정하거나 수정하는 선택적 동작과 함께 필터 조건이 포함된 다양한 선택 규칙을 지원합니다. 규칙이 일치할 때마다 메시지를 생성합니다. 규칙, 필터 및 작업에 대해 자세히 알아보려면 이 [링크](topic-filters.md)를 따르세요.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>구독에 대한 필터 규칙 만들기

네임스페이스와 토픽/구독이 프로비전되고 필요한 자격 증명이 있으면 구독에 대한 필터 규칙을 만듭니다. 그러면 메시지를 보내고 받을 준비가 됩니다. [이 GitHub 샘플 폴더](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters)에서 코드를 검사할 수 있습니다.

## <a name="send-and-receive-messages"></a>메시지 보내기 및 받기

코드를 실행하려면 다음을 수행합니다.

1. 명령 프롬프트 또는 PowerShell 프롬프트에서 다음 명령을 실행하여 [Service Bus GitHub 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. 다음 샘플 폴더로 이동합니다. `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`

3. 이 자습서의 관리 자격 증명 얻기 섹션에서 메모장에 복사한 연결 문자열을 가져옵니다. 또한 이전 섹션에서 만든 토픽의 이름도 필요합니다.

4. 명령 프롬프트에서 다음 명령을 입력합니다.

   ```shell
   dotnet build
   ```

5. `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0` 폴더로 이동합니다.

6. 다음 명령을 입력하여 프로그램을 실행합니다. `myConnectionString`을 앞에서 가져온 값으로 바꾸고, `myTopicName`을 만든 토픽의 이름으로 바꿔야 합니다.

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. 먼저, 콘솔의 지침에 따라 필터 만들기를 선택합니다. 필터 만들기의 일부로 기본 필터를 제거하는 것이 포함됩니다. PowerShell 또는 CLI를 사용할 때는 기본 필터를 제거할 필요가 없지만, 코드에서 이 작업을 수행해야 하는 경우에는 제거해야 합니다. 콘솔 명령 1과 3은 이전에 만든 구독의 필터를 관리하는 데 도움이 됩니다.

   - 실행 1: 기본 필터를 제거합니다.
   - 실행 2: 사용자 고유의 필터를 추가합니다.
   - 실행 3: 필요에 따라 사용자 고유의 필터를 제거합니다. 이렇게 하면 기본 필터가 다시 만들어지지 않습니다.

     ![2의 출력 표시](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. 필터가 만들어지면 메시지를 보낼 수 있습니다. 4를 누르고 토픽에 보내는 10개 메시지를 관찰합니다.

    ![출력 보내기](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. 5를 누르고 받는 메시지를 관찰합니다. 10개 메시지를 다시 받지 못하는 경우 "m"을 눌러 메뉴를 표시한 다음, 5를 다시 누릅니다.

    ![출력 받기](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 네임스페이스와 큐를 삭제합니다. 이렇게 하려면 포털에서 이러한 리소스를 선택하고 **삭제**를 클릭합니다.

## <a name="understand-the-sample-code"></a>샘플 코드 이해

이 섹션에는 샘플 코드의 기능에 대한 자세한 내용이 포함되어 있습니다.

### <a name="get-connection-string-and-topic"></a>연결 문자열 및 토픽 가져오기

먼저, 다음 코드에서는 프로그램의 나머지 실행을 구동하는 변수 집합을 선언합니다.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

연결 문자열 및 토픽 이름은 명령줄 매개 변수를 통해 표시된 대로 전달된 다음, `Main()` 메서드에서 읽습니다.

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>기본 필터 제거

구독이 만들어지면 Service Bus에서 구독별 기본 필터를 만듭니다. 이 필터를 사용하면 토픽으로 보낸 모든 메시지를 받을 수 있습니다. 사용자 지정 필터를 사용하려는 경우 다음 코드와 같이 기본 필터를 제거할 수 있습니다.

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>필터 생성

다음 코드에서는 이 자습서에서 정의한 사용자 지정 필터를 추가합니다.

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>만든 사용자 지정 필터 제거

구독에 대한 모든 필터를 제거하려는 경우 다음 코드에서 이 작업을 수행하는 방법을 보여 줍니다.

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>메시지 보내기

토픽에 메시지를 보내는 것은 큐에 메시지를 보내는 것과 비슷합니다. 다음 예제에서는 작업 목록과 비동기 처리를 사용하여 메시지를 보내는 방법을 보여 줍니다.

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>메시지 받기

메시지는 작업 목록을 통해 다시 받고, 코드에서 일괄 처리를 사용합니다. 일괄 처리를 사용하여 보내고 받을 수 있지만, 다음 예제에서는 일괄 처리 방식으로 받는 방법만 보여 줍니다. 실제로 루프를 중단하지 않고 반복을 유지하며, 1분과 같이 더 긴 시간 간격을 설정합니다. broker에 대한 수신 호출은 이 시간 동안 열려 있고, 메시지가 도착하면 즉시 반환되며, 새 수신 호출이 발급됩니다. 이 개념을 *긴 폴링*이라고 합니다. [빠른 시작](service-bus-quickstart-portal.md) 및 리포지토리의 여러 다른 샘플에서 볼 수 있는 수신 펌프를 사용하는 것이 더 일반적인 옵션입니다.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal을 사용하여 리소스를 프로비전한 다음, Service Bus 토픽 및 해당 구독에서 메시지를 보내고 받았습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 Service Bus 토픽 및 해당 토픽에 대한 하나 이상의 구독 만들기
> * .NET 코드를 사용하여 토픽 필터 추가
> * 내용이 서로 다른 두 개의 메시지 만들기
> * 메시지 보내기 및 필요한 구독에 도착했는지 확인
> * 구독에서 메시지 받기

메시지 송수신에 대한 더 많은 예제는 [GitHub의 Service Bus 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted)에서 시작하세요.

Service Bus의 게시/구독 기능을 사용하는 방법에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Event Grid를 통해 이벤트에 응답](service-bus-to-event-grid-integration-example.md)

[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
