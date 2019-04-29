---
title: Python 앱에서 캡처한 데이터 읽기 - Azure Event Hubs | Microsoft Docs
description: Azure Python SDK를 통해 Event Hubs 캡처 기능을 사용하는 방법을 보여 주는 샘플입니다.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822405"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs 캡처 연습: Python

캡처는 Azure Event Hubs의 기능입니다. 이 기능을 사용하여 Event Hub의 스트리밍 데이터를 선택한 Azure Blob Storage 계정에 자동으로 전달할 수 있습니다. 이 기능을 통해 손쉽게 실시간 스트리밍 데이터에 대한 일괄 처리를 수행할 수 있습니다. 이 문서에서는 Python과 함께 Event Hubs 캡처를 사용하는 방법을 설명합니다. Event Hubs 캡처에 대한 자세한 내용은 [개요 문서](event-hubs-capture-overview.md)를 참조하세요.

이 샘플에서는 [Azure Python SDK](https://azure.microsoft.com/develop/python/)를 사용하여 캡처 기능을 보여 줍니다. sender.py 프로그램이 JSON 형식으로 Event Hubs에 시뮬레이트된 환경 원격 분석을 보냅니다. Event Hub는 캡처 기능을 사용하여 이 데이터를 Blob Storage에 일괄적으로 쓰도록 구성되어 있습니다. capturereader.py 앱이 이러한 Blob을 읽고 디바이스당 추가 파일을 만듭니다. 그런 다음 .csv 파일에 데이터를 씁니다.

## <a name="what-youll-accomplish"></a>수행할 작업

1. Azure Portal을 사용하여 내부에 Azure Blob Storage 계정 및 Blob 컨테이너 만들기
2. Azure Portal을 사용하여 Event Hubs 네임스페이스 만들기
3. Azure Portal을 사용하여 캡처 기능이 활성화된 Event Hub 만들기
4. Python 스크립트를 사용하여 Event Hub에 데이터 보내기
5. 다른 Python 스크립트를 사용하여 캡처에서 파일 읽기 및 처리

## <a name="prerequisites"></a>필수 조건

- Python 2.7.x
- Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- 활성 [Event Hubs 네임스페이스 및 이벤트 허브](event-hubs-create.md) 
- 다음의 지침을 따라 이벤트 허브에 대해 **캡처** 기능을 활성화합니다. [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 만들기
1. [Azure Portal][Azure portal]에 로그인합니다.
2. Azure Portal에서 **새로 만들기** > **저장소** > **저장소 계정**을 선택합니다.
3. **저장소 계정 만들기** 창에서 선택 항목을 완료한 다음, **만들기**를 선택합니다.
   
   !["저장소 계정 만들기" 창][1]
4. **배포 성공** 메시지가 나타나면 새 저장소 계정의 이름을 선택한 다음, **Essentials** 블레이드에서 **Blob**을 선택합니다. **Blob service** 창이 열리면 맨 위의 **+ 컨테이너**를 선택합니다. 컨테이너 이름을 **capture**로 지정한 다음, **Blob service** 창을 닫습니다.
5. 왼쪽 창에서 **액세스 키**를 선택하고 저장소 계정 이름과 **key1** 값을 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 저장합니다.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>이벤트 허브로 이벤트를 보내는 Python 스크립트 만들기
1. 선호하는 Python 편집기(예: [Visual Studio Code][Visual Studio Code])를 엽니다.
2. **sender.py**라는 스크립트를 만듭니다. 이 스크립트는 이벤트 허브에 200개의 이벤트를 전송합니다. 이 이벤트는 JSON 형식으로 전송한 간단한 환경 판독값입니다.
3. 다음 코드를 sender.py에 붙여넣습니다.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Event Hubs 네임스페이스를 만들 때 얻은 네임스페이스 이름, 키 값 및 이벤트 허브 이름을 사용하도록 앞의 코드를 업데이트합니다.

## <a name="create-a-python-script-to-read-your-capture-files"></a>캡처 파일을 읽는 Python 스크립트 만들기

1. 창을 채우고 **만들기**를 선택합니다.
2. **capturereader.py**라는 스크립트를 만듭니다. 이 스크립트는 캡처된 파일을 읽고 디바이스마다 파일을 만들어 해당 디바이스에 대한 데이터만 씁니다.
3. capturereader.py에 다음 코드를 붙여넣습니다.
   
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
           if not dict.has_key(parsed_json['id']):
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
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. 호출의 저장소 계정 이름 및 키의 적절한 값을 `startProcessing`에 붙여넣습니다.

## <a name="run-the-scripts"></a>스크립트 실행
1. 해당 경로에 Python을 포함하는 명령 프롬프트를 열고 다음 명령을 실행하여 Python 필수 구성 요소 패키지를 설치합니다.
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   이전 버전의 **azure-storage**나 **azure**가 있는 경우 **--upgrade** 옵션을 사용해야 할 수 있습니다.
   
   또한 다음 명령을 실행해야 할 수도 있습니다(대부분의 시스템에는 필요 없음).
   
   ```
   pip install cryptography
   ```
2. sender.py 및 capturereader.py를 저장한 디렉터리로 변경하고 다음 명령을 실행합니다.
   
   ```
   start python sender.py
   ```
   
   이 명령은 발신자를 실행하는 새 Python 프로세스를 시작합니다.
3. 몇 분 정도 기다리면 캡처가 실행됩니다. 그런 다음 원래 명령 창에 다음 명령을 입력합니다.
   
   ```
   python capturereader.py
   ```

   이 캡처 프로세서는 로컬 디렉터리를 사용하여 저장소 계정/컨테이너에서 모든 Blob을 다운로드합니다. 비어 있지 않은 모든 Blob을 처리하고 로컬 디렉터리에 .csv 파일로 결과를 작성합니다.

## <a name="next-steps"></a>다음 단계

다음 링크를 사용하여 Event Hubs에 대해 자세히 알아볼 수 있습니다.

* [Event Hubs 캡처 개요][Overview of Event Hubs Capture]
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs 개요][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
