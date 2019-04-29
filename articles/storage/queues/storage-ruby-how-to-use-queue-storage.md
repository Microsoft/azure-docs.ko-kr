---
title: Ruby에서 Queue Storage를 사용하는 방법 | Microsoft Docs
description: Azure 큐 서비스를 사용하여 큐를 작성 및 삭제하고 메시지를 삽입하고 가져오고 삭제하는 방법을 알아봅니다. 샘플은 Ruby로 작성되었습니다.
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: 7ebb4326a8ec8a3382a5488ce3b966526bef446a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112007"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Ruby에서 Queue Storage를 사용하는 방법
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요
이 가이드에서는 Microsoft Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 Ruby Azure API를 사용하여 작성되었습니다.
여기서 다루는 시나리오에는 **큐 만들기 및 삭제**뿐만 아니라 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**가 포함됩니다.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby 애플리케이션 만들기
Ruby 애플리케이션을 만듭니다. 지침은 [Linux의 App Service에서 Ruby 앱 만들기](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)를 참조하세요.

## <a name="configure-your-application-to-access-storage"></a>스토리지에 액세스하도록 애플리케이션 구성
Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems를 사용하여 패키지 가져오기
1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.
2. 명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### <a name="import-the-package"></a>패키지 가져오기
원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure Storage 연결 설정
Azure 모듈은 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS_KEY** 환경 변수를 읽고 Azure Storage 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않으면 **Azure::QueueService** 를 사용하기 전에 다음 코드로 계정 정보를 지정해야 합니다.

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Azure 포털의 클래식 또는 Resource Manager 저장소 계정에서 이러한 값을 가져오려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 사용하려는 저장소 계정으로 이동합니다.
3. 오른쪽의 설정 블레이드에서 **액세스 키**를 클릭합니다.
4. 나타나는 액세스 키 블레이드에 액세스 키 1 및 액세스 키 2가 표시되어 있습니다. 이 둘 중 하나를 사용할 수 있습니다. 
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다. 

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기
다음 코드는 **Azure::QueueService** 개체를 만들어 큐 작업을 수행할 수 있게 해 줍니다.

```ruby
azure_queue_service = Azure::QueueService.new
```

**create_queue()** 메서드를 사용하여 지정된 이름이 있는 큐를 만듭니다.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>방법: 큐에 메시지 삽입
큐에 메시지를 삽입하려면 **create_message()** 메서드를 사용하여 새 메시지를 만들고 이 메시지를 큐에 추가합니다.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 피킹
큐에서 메시지를 제거하지 않고도 **peek\_messages()** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다. 기본적으로 **peek\_messages()** 는 단일 메시지를 읽습니다. 보려는 메시지의 수를 지정할 수도 있습니다.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>방법: 큐에서 다음 메시지 제거
2단계를 거쳐 큐에서 메시지를 제거할 수 있습니다.

1. **list\_messages()** 를 호출하면 기본적으로 큐에서 다음 메시지를 가져옵니다. 가져오려는 메시지의 수를 지정할 수도 있습니다. **list\_messages()** 에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 표시 제한 시간(초 단위)을 매개 변수로 전달합니다.
2. 큐에서 메시지 제거를 완료하려면 **delete_message()** 도 호출해야 합니다.

메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **delete\_message()** 를 호출합니다.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 큐 대기 메시지의 콘텐츠 변경
큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 아래 코드는 **update_message()** 메서드를 사용하여 메시지를 업데이트합니다. 이 메서드는 큐 메시지의 pop 확인 및 메시지가 큐에 표시되는 시간을 나타내는 UTC 날짜 시간 값이 포함된 튜플을 반환합니다.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>방법: 큐에서 메시지를 제거하는 추가 옵션
큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

1. 메시지의 배치를 가져올 수 있습니다.
2. 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

다음 코드 예제는 **list\_messages()** 메서드를 사용하여 한 번 호출에서 15개의 메시지를 가져옵니다. 그런 다음 각 메시지를 인쇄하고 삭제합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기
큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **get\_queue\_metadata()** 메서드는 큐 서비스에 대략적인 메시지 개수 및 큐에 대한 메타데이터를 반환하도록 요청합니다.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제
큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 **delete\_queue()** 메서드를 호출합니다.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>다음 단계
이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 스토리지 작업에 대해 알아보세요.

* [Azure Storage 팀 블로그](https://blogs.msdn.com/b/windowsazurestorage/)
* GitHub에서 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) (영문) 리포지토리를 방문하세요.

이 항목에서 다룬 Azure 큐 서비스와 [Service Bus 큐를 사용하는 방법](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) 항목에서 다루는 Azure Service Bus 큐를 비교하려면 [Azure Queues 및 Service Bus 큐 - 비교 및 대조](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)를 참조하세요.
