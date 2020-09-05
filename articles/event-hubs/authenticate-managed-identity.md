---
title: Azure Active Directory를 사용 하 여 관리 id 인증
description: 이 문서에서는 Azure Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id를 인증 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4d606e6cd035d4dae388d8559d100988a46e8203
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010021"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증
Azure Event Hubs [는 azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 Azure Active Directory (azure AD) 인증을 지원 합니다. Azure 리소스에 대 한 관리 id는 azure Virtual Machines (Vm), 함수 앱, Virtual Machine Scale Sets 및 기타 서비스에서 실행 되는 응용 프로그램의 Azure AD 자격 증명을 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한을 부여할 수 있습니다 Azure 리소스에 대 한 관리 되는 id를 Azure AD 인증과 함께 사용 하 여 클라우드에서 실행 되는 응용 프로그램에 자격 증명을 저장 하지 않을 수 있습니다.

이 문서에서는 Azure VM에서 관리 되는 id를 사용 하 여 이벤트 허브에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다.

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용
Azure 리소스에 관리 되는 id를 사용 하 여 VM에서 Event Hubs 리소스에 권한을 부여 하려면 먼저 VM에서 Azure 리소스에 대 한 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리 되는 id에 대 한 사용 권한 부여
응용 프로그램의 관리 되는 id에서 Event Hubs 서비스에 대 한 요청에 권한을 부여 하려면 먼저 관리 되는 id에 대 한 RBAC (역할 기반 액세스 제어) 설정을 구성 합니다. Azure Event Hubs는 Event Hubs에서 전송 및 읽기 권한을 포함 하는 Azure 역할을 정의 합니다. Azure 역할이 관리 id에 할당 되 면 관리 되는 id에 적절 한 범위에서 Event Hubs 데이터에 대 한 액세스 권한이 부여 됩니다.

Azure 역할을 할당 하는 방법에 대 한 자세한 내용은 [Event Hubs 리소스에 대 한 액세스를 위해 Azure Active Directory 인증](authorize-access-azure-active-directory.md)을 참조 하세요.

## <a name="use-event-hubs-with-managed-identities"></a>Event Hubs에서 관리 ID 사용
관리 id에 Event Hubs를 사용 하려면 id를 역할과 적절 한 범위에 할당 해야 합니다. 이 섹션의 절차에서는 관리 되는 id로 실행 되 고 Event Hubs 리소스에 액세스 하는 간단한 응용 프로그램을 사용 합니다.

여기서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에서 호스트 되는 샘플 웹 응용 프로그램을 사용 하 고 있습니다. 웹 응용 프로그램을 만드는 방법에 대 한 단계별 지침은 [Azure에서 ASP.NET Core 웹 앱 만들기](../app-service/quickstart-dotnetcore.md) 를 참조 하세요.

응용 프로그램을 만든 후에는 다음 단계를 수행 합니다. 

1. **설정** 으로 이동 하 여 **id**를 선택 합니다. 
1. **상태** **를 선택 합니다.** 
1. **저장**을 선택하여 설정을 저장합니다. 

    ![웹 앱에 대 한 관리 id](./media/authenticate-managed-identity/identity-web-app.png)

이 설정을 사용 하도록 설정 하면 Azure Active Directory (Azure AD)에서 새 서비스 id가 만들어지고 App Service 호스트로 구성 됩니다.

이제 Event Hubs 리소스에서 필요한 범위의 역할에이 서비스 id를 할당 합니다.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 역할을 할당 하려면
Event Hubs 리소스에 역할을 할당 하려면 Azure Portal에서 해당 리소스로 이동 합니다. 리소스의 IAM (Access Control) 설정을 표시 하 고 다음 지침에 따라 역할 할당을 관리 합니다.

> [!NOTE]
> 다음 단계에서는 Event Hubs 네임 스페이스에 서비스 id 역할을 할당 합니다. 동일한 단계를 수행 하 여 Event Hubs 리소스에 범위가 지정 된 역할을 할당할 수 있습니다. 

1. Azure Portal에서 Event Hubs 네임 스페이스로 이동 하 여 네임 스페이스에 대 한 **개요** 를 표시 합니다. 
1. 왼쪽 메뉴에서 **Access Control (IAM)** 을 선택 하 여 이벤트 허브에 대 한 액세스 제어 설정을 표시 합니다.
1.  **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
3.  **추가** 를 선택 하 여 새 역할을 추가 합니다.
4.  **역할 할당 추가** 페이지에서 할당 하려는 Event Hubs 역할을 선택 합니다. 그런 다음를 검색 하 여 역할을 할당 하기 위해 등록 한 서비스 id를 찾습니다.
    
    ![역할 할당 추가 페이지](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  **저장**을 선택합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 서비스 id가 데이터 소유자 Event Hubs 있음을 보여 줍니다.
    
    ![역할에 할당 된 id](./media/authenticate-managed-identity/role-assigned.png)

역할을 할당 하면 웹 응용 프로그램은 정의 된 범위에서 Event Hubs 리소스에 액세스할 수 있습니다. 

### <a name="test-the-web-application"></a>웹 애플리케이션 테스트
1. Event Hubs 네임 스페이스 및 이벤트 허브를 만듭니다. 
2. Azure에 웹 앱을 배포 합니다. GitHub에서 웹 응용 프로그램에 대 한 링크는 다음 탭 섹션을 참조 하세요. 
3. SendReceive .aspx가 웹 앱에 대 한 기본 문서로 설정 되었는지 확인 합니다. 
3. 웹 앱에 대 한 **id** 를 사용 하도록 설정 합니다. 
4. 이 id를 네임 스페이스 수준이 나 이벤트 허브 수준에서 **Event Hubs 데이터 소유자** 역할에 할당 합니다. 
5. 웹 응용 프로그램을 실행 하 고, 네임 스페이스 이름 및 이벤트 허브 이름 및 메시지를 입력 하 고, **보내기**를 선택 합니다. 이벤트를 받으려면 **수신**을 선택 합니다. 

#### <a name="azuremessagingeventhubs-latest"></a>[EventHubs (최신)](#tab/latest)
이제 웹 응용 프로그램을 시작 하 고 브라우저가 샘플 aspx 페이지를 가리키도록 할 수 있습니다. [GitHub 리포지토리의](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)Event Hubs 리소스에서 데이터를 보내고 받는 샘플 웹 응용 프로그램을 찾을 수 있습니다.

[NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)에서 최신 패키지를 설치 하 고 **EventHubConsumerClient**를 사용 하 여 **EventHubProducerClient** 및 수신 이벤트를 사용 하 여 Event Hubs에 이벤트를 보내기 시작 합니다. 

> [!NOTE]
> 관리 id를 사용 하 여 이벤트 허브에 이벤트를 게시 하는 Java 샘플은 [GitHub에서 Azure identity 샘플을 사용 하 여 이벤트 게시](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)를 참조 하세요.

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs(레거시)](#tab/old)
이제 웹 응용 프로그램을 시작 하 고 브라우저가 샘플 aspx 페이지를 가리키도록 할 수 있습니다. [GitHub 리포지토리의](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)Event Hubs 리소스에서 데이터를 보내고 받는 샘플 웹 응용 프로그램을 찾을 수 있습니다.

[NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)에서 최신 패키지를 설치 하 고 다음 코드와 같이 EventHubClient를 사용 하 여 Event hubs에서 데이터를 보내고 받는 작업을 시작 합니다. 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Kafka용 Event Hubs
Apache Kafka 응용 프로그램을 사용 하 여 관리 되는 id OAuth를 통해 Azure Event Hubs에서 메시지를 보내고 받을 수 있습니다. GitHub의 다음 샘플을 참조 하세요. [Kafka에 대 한 Event Hubs-관리 Id OAuth를 사용 하 여 메시지를 보내고 받습니다](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>샘플
- **EventHubs** 샘플
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    이러한 샘플은 이전 **EventHubs** 라이브러리를 사용 하지만, 최신 **EventHubs** 라이브러리를 사용 하 여 쉽게 업데이트할 수 있습니다. 이전 라이브러리를 사용 하 여 샘플을 새 라이브러리로 이동 하려면 [EventHubs에서 EventHubs로 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)를 참조 하세요.
    이 샘플은 최신 **EventHubs** 라이브러리를 사용 하도록 업데이트 되었습니다.
- [Kafka에 대 한 Event Hubs-관리 id OAuth를 사용 하 여 메시지 보내기 및 받기](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>다음 단계
- Azure 리소스에 대 한 관리 id [는 무엇 인가요? azure 리소스에 대 한 관리 id는 무엇입니까?](../active-directory/managed-identities-azure-resources/overview.md) 를 참조 하세요.
- 다음 관련 문서를 참조 하세요.
    - [Azure Active Directory를 사용 하 여 응용 프로그램에서 Azure Event Hubs에 대 한 요청 인증](authenticate-application.md)
    - [공유 액세스 서명을 사용 하 여 Azure Event Hubs에 대 한 요청 인증](authenticate-shared-access-signature.md)
    - [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)
    - [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-shared-access-signature.md)
