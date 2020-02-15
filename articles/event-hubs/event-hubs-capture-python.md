---
title: '빠른 시작: Python 앱에서 캡처한 데이터 읽기 - Azure Event Hubs'
description: '빠른 시작: Azure Python SDK를 사용하여 Event Hubs 캡처 기능을 보여주는 스크립트입니다.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187284"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>빠른 시작: Event Hubs 캡처 연습: Python(azure-eventhub 버전 1)

캡처는 Azure Event Hubs의 기능입니다. 캡처를 사용하여 이벤트 허브의 스트리밍 데이터를 원하는 Azure Blob 스토리지 계정에 자동으로 전달할 수 있습니다. 이 기능을 통해 손쉽게 실시간 스트리밍 데이터에 대한 일괄 처리를 수행할 수 있습니다. 이 문서에서는 Python과 함께 Event Hubs 캡처를 사용하는 방법을 설명합니다. Event Hubs 캡처에 대한 자세한 내용은 [Azure Event Hubs를 통해 이벤트 캡처][Overview of Event Hubs Capture]를 참조하세요.

이 연습에서는 [Azure Python SDK](https://azure.microsoft.com/develop/python/)를 사용하여 캡처 기능을 보여줍니다. *sender.py* 프로그램은 시뮬레이션된 환경 원격 분석 데이터를 JSON 형식으로 Event Hubs에 보냅니다. 이벤트 허브는 캡처 기능을 사용하여 이 데이터를 Blob 스토리지에 일괄적으로 씁니다. *capturereader.py* 앱은 이러한 Blob을 읽고, 각 디바이스에 대한 추가 파일을 만들고, 각 디바이스의 *.csv* 파일에 데이터를 씁니다.

> [!WARNING]
> 이 빠른 시작은 Azure Event Hubs Python SDK 버전 1을 위한 것입니다. 코드를 [Python SDK 버전 5](get-started-capture-python-v2.md)로 [마이그레이션](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)하는 것이 좋습니다.

이 연습에서는 다음을 수행합니다. 

> [!div class="checklist"]
> * Azure Portal에서 Azure Blob 스토리지 계정 및 컨테이너 만들기
> * Event Hubs 캡처를 사용하도록 설정하고 스토리지 계정에 직접 전달
> * Python 스크립트를 사용하여 이벤트 허브에 데이터 보내기
> * 다른 Python 스크립트를 사용하여 Event Hubs 캡처에서 파일 읽기 및 처리

## <a name="prerequisites"></a>사전 요구 사항

- `pip`가 설치 및 업데이트된 Python 3.4 이상
  
- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
  
- [빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기](event-hubs-create.md)의 지침에 따라 만든 활성 Event Hubs 네임스페이스 및 이벤트 허브. 네임스페이스와 이벤트 허브 이름을 적어 두세요. 이 연습의 뒷부분에서 사용됩니다. 
  
  > [!NOTE]
  > 사용할 스토리지 컨테이너가 이미 있는 경우 이벤트 허브를 만들 때 캡처를 사용하도록 설정하고 스토리지 컨테이너를 선택할 수 있습니다. 
  > 
  
- Event Hubs 공유 액세스 키 이름 및 기본 키 값. Event Hubs 페이지의 **공유 액세스 정책**에서 이러한 값을 찾거나 만들 수 있습니다. 기본 액세스 키 이름은 **RootManageSharedAccessKey**입니다. 액세스 키 이름과 기본 키 값을 복사해 두세요. 이 연습의 뒷부분에서 사용됩니다. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Azure Blob 스토리지 계정 및 컨테이너 만들기

캡처에 사용할 스토리지 계정 및 컨테이너를 만듭니다. 

1. [Azure Portal][Azure portal]에 로그인합니다.
2. 왼쪽 탐색 영역에서 **스토리지 계정**을 선택하고, **스토리지 계정** 화면에서 **추가**를 선택합니다.
3. 스토리지 계정 만들기 화면에서 구독 및 리소스 그룹을 선택하고, 스토리지 계정의 이름을 지정합니다. 다른 선택 항목은 기본값을 적용해도 됩니다. **검토 + 만들기**를 선택하고 설정을 검토한 다음, **만들기**를 선택합니다. 
   
   ![스토리지 계정 만들기][1]
   
4. 배포가 완료되면 **리소스로 이동**을 선택하고, 스토리지 계정 **개요** 화면에서 **컨테이너**를 선택합니다.
5. **컨테이너** 화면에서 **+ 컨테이너**를 선택합니다. 
6. **새 컨테이너** 화면에서 컨테이너 이름을 지정하고 **확인**을 선택합니다. 컨테이너 이름을 적어 둡니다. 이 연습의 뒷부분에서 사용됩니다. 
7. **컨테이너** 화면의 왼쪽 탐색 영역에서 **액세스 키**를 선택합니다. **스토리지 계정 이름**과 **key1**아래의 **키** 값을 복사해 둡니다. 이 연습의 뒷부분에서 사용됩니다.
 
## <a name="enable-event-hubs-capture"></a>Event Hubs 캡처를 사용하도록 설정

1. Azure Portal의 **모든 리소스**에서 Event Hubs 네임스페이스를 선택하고, 왼쪽 탐색 영역에서 **Event Hubs**를 선택한 다음, 해당 이벤트 허브를 선택하여 이벤트 허브로 이동합니다. 
2. 이벤트 허브 **개요** 화면에서 **이벤트 캡처**를 선택합니다.
3. **캡처** 화면에서 **켜기**를 선택합니다. 그런 다음, **Azure Storage 컨테이너**에서 **컨테이너 선택**을 선택합니다. 
4. **컨테이너** 화면에서 사용할 스토리지 컨테이너를 선택한 다음, **선택**을 누릅니다. 
5. **캡처** 화면에서 **변경 내용 저장**을 선택합니다. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>이벤트 허브로 이벤트를 보내는 Python 스크립트 만들기
이 스크립트는 이벤트 허브에 200개의 이벤트를 전송합니다. 이 이벤트는 JSON 형식으로 전송한 간단한 환경 판독값입니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code][Visual Studio Code])를 엽니다.
2. *sender.py*라는 새 파일을 만듭니다. 
3. 다음 코드를 *sender.py*에 붙여넣습니다. Event Hubs \<네임스페이스>, \<AccessKeyName>, \<기본 키 값> 및 \<eventhub>를 사용자 고유의 값으로 바꿉니다.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. 파일을 저장합니다.

## <a name="create-a-python-script-to-read-capture-files"></a>캡처 파일을 읽는 Python 스크립트 만들기

이 스크립트는 캡처된 파일을 읽고 디바이스마다 파일을 만들어 해당 디바이스에 대한 데이터만 씁니다.

1. Python 편집기에서 *capturereader.py*라는 새 파일을 만듭니다. 
2. *capturereader.py*에 다음 코드를 붙여넣습니다. \<storageaccount>, \<스토리지 계정 액세스 키> 및 \<storagecontainer>를 저장된 값으로 바꿉니다.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not parsed_json['id'] in dict:
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Python 스크립트 실행

1. 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행하여 Python 필수 구성 요소 패키지를 설치합니다.
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   이전 버전의 `azure-storage` 또는 `azure`를 사용하는 경우 `--upgrade` 옵션을 사용해야 할 수도 있습니다.
   
   다음 명령을 실행해야 할 수도 있습니다. 대부분의 시스템에서는 이 명령을 꼭 실행할 필요가 없습니다. 
   
   ```cmd
   pip install cryptography
   ```
   
2. *sender.py* 및 *capturereader.py*를 저장한 디렉터리에서 다음 명령을 실행합니다.
   
   ```cmd
   start python sender.py
   ```
   
   이 명령은 발신자를 실행하는 새 Python 프로세스를 시작합니다.
   
3. 캡처 실행이 완료되면 다음 명령을 실행합니다.
   
   ```cmd
   python capturereader.py
   ```

   캡처 프로세서는 스토리지 계정 컨테이너에서 비어 있지 않은 모든 Blob을 다운로드하고 결과를 로컬 디렉터리에 *.csv* 파일로 작성합니다. 

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음을 참조하세요. 

* [Event Hubs 캡처 개요][Overview of Event Hubs Capture]
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs 개요][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
