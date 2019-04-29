---
title: Python에서 Queue Storage를 사용하는 방법 | Microsoft Docs
description: Azure 큐 서비스를 사용하여 Python에서 큐를 작성 및 삭제하고 메시지를 삽입하고 가져오고 삭제하는 방법을 알아봅니다."
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: afa0b6d4f87c4325f116463242c15df9d9b6e7c4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108807"
---
# <a name="how-to-use-queue-storage-from-python"></a>Python에서 Queue Storage를 사용하는 방법
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요
이 가이드에서는 Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 Python으로 작성되었으며 [Microsoft Azure Storage SDK for Python]을 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기 및 삭제**뿐만 아니라 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계] 섹션을 참조하세요.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python 다운로드 및 설치

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python)에는 Python 2.7, 3.3, 3.4, 3.5 또는 3.6이 필요합니다.
 
### <a name="install-via-pypi"></a>PyPi를 통해 설치

PyPi(Python Package Index)를 통해 설치하려면 다음을 입력합니다.

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Azure Storage SDK for Python 버전 0.36 또는 이전 버전에서 업그레이드하는 경우 최신 패키지를 설치하기 전에 `pip uninstall azure-storage`를 사용하여 이전 SDK를 제거합니다.

대체 설치 방법은 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/)을 참조하세요.

## <a name="view-the-sample-application"></a>샘플 애플리케이션 보기

Azure Queues에서 Python을 사용하는 방법을 보여주는 샘플 애플리케이션을 보고 실행하려면 [Azure Storage: Python에서 Azure Queues 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started)을 참조하세요. 

샘플 애플리케이션을 실행하려면 `azure-storage-queue` 및 `azure-storage-common` 패키지를 둘 다 설치해야 합니다.

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기

**QueueService** 개체를 사용하면 큐로 작업할 수 있습니다. 다음 코드는 **QueueService** 개체를 만듭니다. 프로그래밍 방식으로 Azure Storage에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

```python
from azure.storage.queue import QueueService
```

다음 코드는 저장소 계정 이름 및 계정 키를 사용하는 **QueueService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 사용자의 계정 이름 및 키로 바꾸세요.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>방법: 큐에 메시지 삽입
큐에 메시지를 삽입하려면 **put\_message** 메서드를 사용하여 새 메시지를 만들고 큐에 추가합니다.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 피킹
큐에서 메시지를 제거하지 않고도 **peek\_messages** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다. 기본적으로 **peek\_messages**는 단일 메시지를 읽습니다.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>방법: 큐에서 메시지 제거
다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **get\_messages**를 호출하면 기본적으로 큐에서 다음 메시지를 가져옵니다. **get\_messages**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **delete\_message**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **delete\_message**를 호출합니다.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.
먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **get\_messages** 메서드를 사용하여 한 번 호출에 16개의 메시지를 가져옵니다. 그런 다음에 for 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 큐 대기 메시지의 콘텐츠 변경
큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 아래 코드에서는 **update\_message** 메서드를 사용하여 메시지를 업데이트합니다. 표시 제한 시간은 0으로 설정되어 있습니다.이는 메시지가 즉시 표시되고 콘텐츠가 업데이트됨을 의미합니다.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기
큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **get\_queue\_metadata** 메서드는 큐 서비스에 큐에 대한 메타데이터 및 **approximate_message_count**를 반환하도록 요청합니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 결과는 근사치일 뿐입니다.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제
큐 및 해당 큐의 모든 메시지를 삭제하려면 **delete\_queue** 메서드를 호출합니다.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>다음 단계
이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 자세히 알아보세요.

* [Python 개발자 센터](https://azure.microsoft.com/develop/python/)
* [Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
