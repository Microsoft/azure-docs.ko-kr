---
title: Java에서 Queue Storage를 사용하는 방법 | Microsoft Docs
description: Azure 큐 서비스를 사용하여 큐를 작성 및 삭제하고 메시지를 삽입하고 가져오고 삭제하는 방법을 알아봅니다. 샘플은 Java로 작성되었습니다.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 7c2fbc168c663cc4dc5981071d40540cfc4797ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108951"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java에서 Queue Storage를 사용하는 방법
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>개요
이 가이드에서는 Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure Storage SDK][Azure Storage SDK for Java](영문)를 사용합니다. 여기서 다루는 시나리오에는 큐 **만들기** 및 **삭제**뿐만 아니라 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

참고: SDK는 Android 디바이스에서 Azure Storage를 사용하는 개발자에게 제공됩니다. 자세한 내용은 [Android용 Azure Storage SDK][Azure Storage SDK for Android]를 참조하세요.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기
이 가이드에서는 Java 애플리케이션 내에서 로컬로 실행할 수 있거나 Azure의 웹 역할 또는 작업자 역할 내에서 실행되는 코드에서 실행할 수 있는 스토리지 기능을 사용합니다.

그러려면 JDK(Java Development Kit)를 설치하고 Azure 구독에서 Azure 저장소 계정을 만들어야 합니다. 그러고 나면 개발 시스템에서 GitHub의 [Java용 Azure Storage SDK][Azure Storage SDK for Java] 리포지토리에 있는 최소 요구 사항과 종속성을 충족하는지 확인해야 합니다. 시스템에서 해당 요구 사항을 충족하는 경우에는 리포지토리에서 시스템의 Java용 Azure Storage Library를 다운로드 및 설치하기 위한 지침을 따를 수 있습니다. 작업을 완료하고 나면 이 문서의 예를 사용하는 Java 애플리케이션을 만들 수 있습니다.

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 응용 프로그램 구성
Azure 저장소 API를 사용하여 큐에 액세스하려는 Java 파일의 맨 위에 다음 import 문을 추가합니다.

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure 저장소 연결 문자열 설정
Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 엔드포인트 및 자격 증명을 저장합니다. 클라이언트 애플리케이션에서 실행할 경우 *AccountName*과 *AccountKey* 값에 대해 [Azure Portal](https://portal.azure.com)에 나열된 저장소 계정의 이름과 기본 액세스 키를 사용하여 다음 형식의 저장소 연결 문자열을 제공해야 합니다. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Microsoft Azure의 역할 내에서 실행되는 애플리케이션에서는 이 문자열이 서비스 구성 파일 *ServiceConfiguration.cscfg*에 저장될 수 있고, **RoleEnvironment.getConfigurationSettings** 메서드 호출을 통해 이 문자열에 액세스할 수 있습니다. 다음은 서비스 구성 파일에서 이름이 **StorageConnectionString** 인 *설정* 요소에서 연결 문자열을 가져오는 예제입니다.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

다음 샘플에서는 저장소 연결 문자열을 가져오기 위해 위의 두 메서드 중 하나를 사용한 것으로 가정합니다.

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기
**CloudQueueClient** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudQueueClient** 개체를 만듭니다. (참고: **CloudStorageAccount** 개체를 만들 수 있는 방법이 더 있습니다. 자세한 내용은 [Azure Storage 클라이언트 SDK 참조]에서 **CloudStorageAccout**를 참조하세요.

**CloudQueueClient** 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 큐가 없는 경우 새로 만들 수 있습니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>방법: 큐에 메시지 추가
기존 큐에 메시지를 삽입하려면 먼저 새 **CloudQueueMessage**를 만듭니다. 그런 다음, **addMessage** 메서드를 호출합니다. **CloudQueueMessage** 는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 "Hello, World" 메시지를 삽입하는 코드입니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 보기
큐에서 메시지를 제거하지 않고도 **peekMessage**를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 대기 중인 메시지의 콘텐츠 변경
큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로, 다시 시도 수도 유지하므로, 메시지가 *n* 번 넘게 다시 시도된 경우 메시지를 지울 수도 있습니다. 이 기능은 처리될 때마다 애플리케이션 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

다음 코드 샘플에서는 메시지 큐를 검색하여 내용에서 "Hello, World"와 일치하는 최초의 메시지를 찾고 메시지 내용을 수정한 후 종료합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

한편 다음 코드 샘플에서는 큐에서 처음으로 보이는 메시지를 업데이트합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기
큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **downloadAttributes** 메서드는 큐에 있는 메시지 수를 포함한 일부 현재 값을 큐 서비스에 요청합니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 메시지 수는 근사치일 뿐입니다. **getApproximateMessageCount** 메서드는 큐 서비스를 호출하지 않고도 **downloadAttributes**를 호출하여 검색된 마지막 값을 반환합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>방법: 큐에서 다음 메시지 제거
다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **retrieveMessage**를 호출하면 큐에서 다음 메시지를 가져올 수 있습니다. **retrieveMessage** 에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **deleteMessage**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **deleteMessage** 를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>큐에서 메시지를 제거하는 추가 옵션
큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

다음 코드 예제는 **retrieveMessages** 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음 **for** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분(300초)으로 설정합니다. 5분은 모든 메시지에 대해 동시에 시작되므로, **retrieveMessages**에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>방법: 큐 나열
현재 큐의 목록을 가져오려면 **CloudQueue** 개체의 컬렉션을 반환하는 **CloudQueueClient.listQueues()** 메서드를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제
큐 및 해당 큐의 모든 메시지를 삭제하려면 **CloudQueue** 개체의 **deleteIfExists** 메서드를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="next-steps"></a>다음 단계
이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 스토리지 작업에 대해 알아보세요.

* [Java용 Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage 클라이언트 SDK 참조][Azure Storage 클라이언트 SDK 참조]
* [Azure Storage 서비스 REST API][Azure Storage Services REST API]
* [Azure Storage 팀 블로그][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage 클라이언트 SDK 참조]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
