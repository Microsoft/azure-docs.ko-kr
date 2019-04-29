---
title: Azure Queue Storage 및 Visual Studio 연결 서비스 시작(ASP.NET) | Microsoft Docs
description: Visual Studio 연결 서비스를 사용하여 스토리지 계정에 연결한 후 Visual Studio ASP.NET 프로젝트에서 Azure Queue Storage 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 3f3a649308449a8ba4e1dbaebd4db4afbf731c5b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110488"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Azure Queue Storage 및 Visual Studio 연결 서비스 시작(ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요

Azure Queue Storage는 애플리케이션 구성 요소 간에 클라우드 메시징을 제공합니다. 규모를 고려하여 애플리케이션을 디자인할 때는 애플리케이션 구성 요소를 개별적으로 확장할 수 있도록 각 구성 요소를 분리하는 경우가 많습니다. 큐 저장소는 클라우드, 데스크톱, 온-프레미스 서버 또는 모바일 디바이스에서 실행 중인지와 관계 없이 응용 프로그램 구성 요소 간에 통신을 위한 비동기 메시징을 제공합니다. 큐 저장소는 또한 비동기 작업 관리와 프로세스 워크플로 작성을 지원합니다.

이 자습서에서는 Azure Queue Storage 항목을 사용하여 몇 가지 일반적인 시나리오에 대한 ASP.NET 코드를 작성하는 방법을 보여 줍니다. 이러한 시나리오는 Azure 큐 작성, 큐 메시지 추가, 수정, 읽기 및 제거와 같은 일반적인 작업을 포함합니다.

## <a name="prerequisites"></a>필수 조건

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 저장소 계정](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC 컨트롤러 만들기 

1. **솔루션 탐색기**에서 **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->컨트롤러**를 차례로 선택합니다.

    ![ASP.NET MVC 앱에 컨트롤러 추가](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. **스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러 - 비어 있음**을 선택하고 **추가**를 선택합니다.

    ![MVC 컨트롤러 유형 지정](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. **컨트롤러 추가** 대화 상자에서 컨트롤러 이름을 *QueuesController*로 설정하고 **추가**를 선택합니다.

    ![MVC 컨트롤러 이름 지정](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. 다음 *using* 지시문을 `QueuesController.cs` 파일에 추가합니다.

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>큐 만들기

다음 단계에서는 큐를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment) 단계를 완료했다고 가정합니다. 

1. `QueuesController.cs` 파일을 엽니다. 

1. **ActionResult**를 반환하는 **CreateQueue**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **CreateQueue** 메서드 내에서 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*&lt;storage-account-name>* 을 액세스 중인 Azure Storage 계정의 이름으로 변경합니다.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. 원하는 큐 이름에 대한 참조를 나타내는 **CloudQueue** 개체를 가져옵니다. **CloudQueueClient.GetQueueReference** 메서드는 Queue Storage에 대한 요청을 만들지 않습니다. 큐가 있는지 여부에 관계없이 참조가 반환됩니다. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. 큐가 아직 없으면 **CloudQueue.CreateIfNotExists** 메서드를 호출하여 만듭니다. 큐가 없는 경우 큐가 성공적으로 생성되면 **CloudQueue.CreateIfNotExists** 메서드는 **true**를 반환합니다. 그렇지 않으면 **false**가 반환됩니다.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. **ViewBag**을 큐의 이름으로 업데이트합니다.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **큐**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **CreateQueue**를 입력하고 **추가**를 선택합니다.

1. `CreateQueue.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. 응용 프로그램을 실행 하 고 선택 **큐 만들기** 다음 스크린샷과 유사한 결과 확인 하려면:
  
    ![큐 만들기](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    앞에서 언급했듯이 큐가 없고 만들어진 경우에만 **CloudQueue.CreateIfNotExists** 메서드에서 **true**를 반환합니다. 따라서 큐가 있을 때 앱을 실행하면 메서드에서 **false**를 반환합니다. 앱을 여러 번 실행하려면 앱을 다시 실행하기 전에 큐를 삭제해야 합니다. **CloudQueue.Delete** 메서드를 통해 큐 삭제를 수행할 수 있습니다. 또한 [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) 또는 [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 큐를 삭제할 수도 있습니다.  

## <a name="add-a-message-to-a-queue"></a>큐에 메시지 추가

[큐를 만든](#create-a-queue) 후 해당 큐에 메시지를 추가할 수 있습니다. 이 섹션에서는 큐 *test-queue*에 메시지를 추가하는 과정을 설명합니다. 

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment) 단계를 완료했다고 가정합니다. 

1. `QueuesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **AddMessage**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **AddMessage** 메서드 내에서 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*&lt;storage-account-name>* 을 액세스 중인 Azure Storage 계정의 이름으로 변경합니다.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. 큐에 대한 참조를 나타내는 **CloudQueueContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. 큐에 추가할 메시지를 나타내는 **CloudQueueMessage** 개체를 만듭니다. **CloudQueueMessage** 개체는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. **CloudQueue.AddMessage** 메서드를 호출하여 큐에 메시지를 추가합니다.

    ```csharp
    queue.AddMessage(message);
    ```

1. 보기에 표시하기 위한 몇 가지 **ViewBag** 속성을 만들고 설정합니다.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **큐**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **AddMessage**를 입력하고 **추가**를 선택합니다.

1. `AddMessage.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. 응용 프로그램을 실행 하 고 선택 **추가 메시지** 다음 스크린샷과 유사한 결과 확인 하려면:
  
    ![메시지 추가](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

두 개의 섹션 - [메시지를 제거하지 않고 큐에서 읽기](#read-a-message-from-a-queue-without-removing-it) 및 [큐에서 메시지 읽기 및 제거](#read-and-remove-a-message-from-a-queue) - 큐에서 메시지를 읽는 방법을 보여 줍니다.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>메시지를 제거하지 않고 큐에서 읽기

이 섹션에서는 대기 중인 메시지를 미리 확인하는 방법을 설명합니다(첫 번째 메시지를 제거하지 않고 읽음).  

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment) 단계를 완료했다고 가정합니다. 

1. `QueuesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **PeekMessage**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **PeekMessage** 메서드 내에서 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*&lt;storage-account-name>* 을 액세스 중인 Azure Storage 계정의 이름으로 변경합니다.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. 큐에 대한 참조를 나타내는 **CloudQueueContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.PeekMessage** 메서드를 호출하여 큐에서 메시지를 제거하지 않고도 큐의 첫 번째 메시지를 읽습니다. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. 읽힌 큐 이름 및 메시지의 두 값으로 **ViewBag**을 업데이트합니다. 합니다 **CloudQueueMessage** 개체는 개체의 값을 가져오기 위한 두 가지 속성을 노출 합니다. **CloudQueueMessage.AsBytes** 하 고 **CloudQueueMessage.AsString**합니다. **AsString**(이 예제에서 사용)은 문자열을 반환하는 반면 **AsBytes**는 바이트 배열을 반환합니다.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **큐**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **PeekMessage**를 입력하고 **추가**를 선택합니다.

1. `PeekMessage.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. 응용 프로그램을 실행 하 고 선택 **메시지 보기** 다음 스크린샷과 유사한 결과 확인 하려면:
  
    ![메시지 보기](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>큐에서 메시지 읽기 및 제거

이 섹션에서는 큐에서 메시지를 읽고 제거하는 방법을 설명합니다.   

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment) 단계를 완료했다고 가정합니다. 

1. `QueuesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **ReadMessage**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ReadMessage** 메서드 내에서 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*&lt;storage-account-name>* 을 액세스 중인 Azure Storage 계정의 이름으로 변경합니다.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. 큐에 대한 참조를 나타내는 **CloudQueueContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.GetMessage** 메서드를 호출하여 큐의 첫 번째 메시지를 읽습니다. **CloudQueue.GetMessage** 메서드는 메시지를 읽는 다른 코드에 해당 메시지가 30초(기본값) 동안 보이지 않도록 하여 사용자가 메시지를 처리하는 동안 다른 코드가 메시지를 수정하거나 삭제할 수 없게 합니다. 메시지가 보이지 않는 기간을 변경하려면 **CloudQueue.GetMessage** 메서드에 전달되는 **visibilityTimeout** 매개 변수를 수정합니다.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. **CloudQueueMessage.Delete** 메서드를 호출하여 큐에서 메시지를 삭제합니다.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. **ViewBag**을 삭제된 메시지로 업데이트하고 큐의 이름을 업데이트합니다.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **큐**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **ReadMessage**를 입력하고 **추가**를 선택합니다.

1. `ReadMessage.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. 응용 프로그램을 실행 하 고 선택 **읽기/삭제 메시지** 다음 스크린샷과 유사한 결과 확인 하려면:
  
    ![메시지 읽기 및 삭제](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>큐 길이 가져오기

이 섹션에서는 큐 길이(메시지 수)를 가져오는 방법을 보여 줍니다. 

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment) 단계를 완료했다고 가정합니다. 

1. `QueuesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **GetQueueLength**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ReadMessage** 메서드 내에서 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*&lt;storage-account-name>* 을 액세스 중인 Azure Storage 계정의 이름으로 변경합니다.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. 큐에 대한 참조를 나타내는 **CloudQueueContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.FetchAttributes** 메서드를 호출하여 큐의 특성(길이 포함)을 검색합니다. 

    ```csharp
    queue.FetchAttributes();
    ```

1. **CloudQueue.ApproximateMessageCount** 속성에 액세스하여 큐의 길이를 가져옵니다.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. **ViewBag**을 큐의 이름으로 업데이트하고 해당 길이를 업데이트합니다.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **큐**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **GetQueueLength**를 입력하고 **추가**를 선택합니다.

1. `GetQueueLengthMessage.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. 응용 프로그램을 실행 하 고 선택 **큐 길이 가져오기** 다음 스크린샷과 유사한 결과 확인 하려면:
  
    ![큐 길이 가져오기](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>큐 삭제
이 섹션에서는 큐를 삭제하는 방법을 보여 줍니다. 

> [!NOTE]
> 
> 이 섹션에서는 [개발 환경 설정](#set-up-the-development-environment) 단계를 완료했다고 가정합니다. 

1. `QueuesController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **DeleteQueue**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **DeleteQueue** 메서드 내에서 스토리지 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. (*&lt;storage-account-name>* 을 액세스 중인 Azure Storage 계정의 이름으로 변경합니다.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. 큐 서비스 클라이언트를 나타내는 **CloudQueueClient** 개체를 나타냅니다.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. 큐에 대한 참조를 나타내는 **CloudQueueContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **CloudQueue.Delete** 메서드를 호출하여 **CloudQueue** 개체로 나타내는 큐를 삭제합니다.

    ```csharp
    queue.Delete();
    ```

1. **ViewBag**을 큐의 이름으로 업데이트하고 해당 길이를 업데이트합니다.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **큐**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **DeleteQueue**를 입력하고 **추가**를 선택합니다.

1. `DeleteQueue.cshtml`을 열고 다음 코드 조각과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. 응용 프로그램을 실행 하 고 선택 **큐 길이 가져오기** 다음 스크린샷과 유사한 결과 확인 하려면:
  
    ![큐 삭제](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>다음 단계
Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.

  * [Azure Blob Storage 및 Visual Studio 연결된 서비스 시작(ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Azure Table Storage 및 Visual Studio 연결된 서비스 시작(ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
