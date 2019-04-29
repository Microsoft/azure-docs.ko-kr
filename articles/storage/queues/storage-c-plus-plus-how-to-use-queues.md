---
title: Queue Storage 사용 방법(C++) | Microsoft Docs
description: Azure에서 Queue Storage 서비스를 사용하는 방법에 대해 알아봅니다. 샘플은 C++로 작성되었습니다.
services: storage
author: cbrooksmsft
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.subservice: queues
ms.openlocfilehash: 08c865e5383e4095a22aa6c10c8b181f916d1d30
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109001"
---
# <a name="how-to-use-queue-storage-from-c"></a>C++에서 Queue Storage를 사용하는 방법
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요
이 가이드에서는 Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C++로 작성되었으며 [Azure Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)를 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기 및 삭제**뿐만 아니라 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**가 포함됩니다.

> [!NOTE]
> 이 가이드는 Azure Storage Client Library for C++ 버전 1.0.0 이상을 대상으로 합니다. 권장되는 버전은 Storage Client Library 2.2.0이며, [NuGet](https://www.nuget.org/packages/wastorage) 또는 [GitHub](https://github.com/Azure/azure-storage-cpp/)를 통해 사용 가능합니다.
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ 애플리케이션 만들기
이 가이드에서는 C++ 애플리케이션 내에서 실행할 수 있는 스토리지 기능을 사용합니다.

이 기능을 사용하려면, Azure Storage Client Library for C++를 설치하고 Azure 구독에서 Azure 스토리지 계정을 만들어야 합니다.

Azure Storage Client Library for C++를 설치하려면 다음 메서드를 사용할 수 있습니다.

* **Linux:** [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) 페이지의 지침을 따릅니다.
* **Windows:** Visual Studio에서 **도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 클릭합니다. [NuGet 패키지 관리자 콘솔](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) 에 다음 명령을 입력하고 **ENTER**를 누릅니다.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 애플리케이션 구성
Azure 저장소 API를 사용하여 큐에 액세스하려는 C++ 파일의 맨 위에 다음 include 문을 추가합니다.  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure 저장소 연결 문자열 설정
Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 엔드포인트 및 자격 증명을 저장합니다. 클라이언트 애플리케이션에서 실행할 경우, 스토리지 계정의 이름 및 [Azure Portal](https://portal.azure.com)에 나열된 스토리지 계정의 스토리지 액세스 키를 *AccountName* 및 *AccountKey* 값에 사용하여 다음 형식의 스토리지 연결 문자열을 제공해야 합니다. Storage 계정 및 액세스 키에 대한 자세한 내용은 [Azure Storage 계정 정보](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 참조하세요. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

로컬 Windows 컴퓨터에서 애플리케이션을 테스트하려면 [Azure SDK](https://azure.microsoft.com/downloads/)와 함께 설치된 Microsoft Azure [스토리지 에뮬레이터](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)를 사용할 수 있습니다. 저장소 에뮬레이터는 로컬 개발 컴퓨터의Azure에서 사용할 수 있는 Blob, 큐 및 Table service를 시뮬레이션하는 유틸리티입니다. 다음 예제에서는 로컬 저장소 에뮬레이터에 연결 문자열을 포함할 수 있도록 정적 필드를 선언하는 방법을 보여줍니다.  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure Storage 에뮬레이터를 시작하려면 **시작** 단추를 선택하거나 **Windows** 키를 누릅니다. **Azure Storage 에뮬레이터** 입력을 시작하고 애플리케이션 목록에서 **Microsoft Azure Storage 에뮬레이터**를 선택합니다.

다음 샘플에서는 저장소 연결 문자열을 가져오기 위해 위의 두 메서드 중 하나를 사용한 것으로 가정합니다.

## <a name="retrieve-your-connection-string"></a>연결 문자열 검색
**cloud_storage_account** 클래스를 사용하여 Storage 계정 정보를 나타낼 수 있습니다. 저장소 연결 문자열에서 저장소 계정 정보를 검색하려면 **구문 분석** 메서드를 사용할 수 있습니다.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기
**cloud_queue_client** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **cloud_queue_client** 개체를 만듭니다.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

**cloud_queue_client** 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 큐가 없는 경우 새로 만들 수 있습니다.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>방법: 큐에 메시지 삽입
기존 큐에 메시지를 삽입하려면 먼저 새 **cloud_queue_message**를 만듭니다. 그런 다음, **add_message** 메서드를 호출합니다. **cloud_queue_message**는 문자열 또는 **바이트** 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 'Hello, World' 메시지를 삽입하는 코드입니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 보기
큐에서 메시지를 제거하지 않고도 **peek_message** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 대기 중인 메시지의 콘텐츠 변경
큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로 다시 시도 수도 유지하므로, 메시지가 n번 넘게 다시 시도된 경우 메시지를 지울 수도 있습니다. 이 기능은 처리될 때마다 애플리케이션 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>방법: 큐에서 다음 메시지 제거
다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **get_message**를 호출하면 큐에서 다음 메시지를 가져올 수 있습니다. **get_message**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **delete_message**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **delete_message**를 호출합니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>방법: 큐에서 메시지를 제거하는 추가 옵션 활용
큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **get_messages** 메서드를 사용하여 한 번 호출에서 20개의 메시지를 가져옵니다. 그런 다음 **for** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다. 5분은 모든 메시지에 대해 동시에 시작되므로, **get_messages**에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기
큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **download_attributes** 메서드는 메시지 수를 포함하여 큐 특성을 검색하도록 큐 서비스에 요청합니다. **approximate_message_count** 메서드는 큐에 메시지의 대략적인 수를 가져옵니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제
큐 및 해당 큐에 포함된 모든 메시지를 삭제하려면 큐 개체의 **delete_queue_if_exists** 메서드를 호출합니다.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>다음 단계
이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 Azure Storage에 대해 알아보세요.

* [C++에서 Blob Storage를 사용하는 방법](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [C++에서 Table Storage를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [C++에서 Azure Storage 리소스 나열](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [C++용 Storage Client Library 참조(영문)](https://azure.github.io/azure-storage-cpp)
* [Azure Storage 설명서](https://azure.microsoft.com/documentation/services/storage/)
