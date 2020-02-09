---
title: Python 앱에서 Azure Event Hubs 캡처된 데이터 읽기 | Microsoft Docs
description: 이 문서에서는 이벤트 허브로 전송 되는 데이터를 캡처하고 Azure Storage에서 캡처한 이벤트 데이터를 읽는 Python 코드를 작성 하는 방법을 보여 줍니다.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293541"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Azure Storage에서 Event Hubs 데이터 캡처 및 Python을 사용 하 여 읽기 
이벤트 허브에 전송 되는 데이터를 Azure Storage 또는 Azure Data Lake Storage로 캡처하도록 이벤트 허브 구성을 사용할 수 있습니다. 이 문서에서는 Python 코드 작성을 사용 하 여 이벤트 허브로 이벤트를 보내고 Azure blob storage에서 캡처된 데이터를 읽는 방법을 보여 줍니다. 이 기능에 대한 자세한 내용은 [Event Hubs 캡처 기능 개요](event-hubs-capture-overview.md)를 참조 하세요.

이 샘플에서는 [Azure Python SDK](https://azure.microsoft.com/develop/python/)를 사용하여 캡처 기능을 보여 줍니다. sender.py 프로그램이 JSON 형식으로 Event Hubs에 시뮬레이트된 환경 원격 분석을 보냅니다. Event Hub는 캡처 기능을 사용하여 이 데이터를 Blob Storage에 일괄적으로 쓰도록 구성되어 있습니다. capturereader.py 앱이 이러한 Blob을 읽고 디바이스당 추가 파일을 만듭니다. 그런 다음 .csv 파일에 데이터를 씁니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 Azure Event Hubs Python SDK 버전 5를 사용 합니다. Python SDK의 이전 버전 1을 사용 하는 빠른 시작은 [이 문서](event-hubs-capture-python.md)를 참조 하세요. SDK 버전 1을 사용 하는 경우 코드를 최신 버전으로 마이그레이션하는 것이 좋습니다. 자세한 내용은 [마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)를 참조 하세요.

이 빠른 시작에서 관련 정보는 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal에서 Azure Blob 스토리지 계정 및 컨테이너 만들기
> * Azure Portal를 사용 하 여 Event Hubs 네임 스페이스를 만듭니다.
> * 캡처 기능이 활성화 된 이벤트 허브를 만들고 저장소 계정에 연결 합니다.
> * Python 스크립트를 사용하여 이벤트 허브에 데이터 보내기
> * 다른 Python 스크립트를 사용하여 Event Hubs 캡처에서 파일 읽기 및 처리

## <a name="prerequisites"></a>필수 조건

- `pip` 설치 및 업데이트 된 Python 2.7 및 3.5 이상
- Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- [네임 스페이스에 Event Hubs 네임 스페이스와 이벤트 허브를 만듭니다](event-hubs-create.md). Event Hubs 네임 스페이스의 이름, 이벤트 허브 이름 및 네임 스페이스에 대한 기본 액세스 키를 적어둡니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)문서의 지침에 따라 액세스 키를 가져옵니다. 기본 키 이름은 **RootManageSharedAccessKey**입니다. 자습서에 대한 연결 문자열은 필요 하지 않습니다. 기본 키만 필요 합니다. 
- **Azure Storage 계정** 및 **blob 컨테이너**를 만들려면 다음 단계를 따르세요.
    1. [Azure Storage 계정을 만듭니다](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [저장소에 blob 컨테이너를 만듭니다](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [저장소 계정에 대한 연결 문자열을 가져옵니다](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string).

        **연결 문자열과** **컨테이너 이름을**적어 둡니다. 코드에서 나중에 사용 합니다. 
- 다음 지침에 따라 이벤트 허브에 대한 **캡처** 기능을 사용 하도록 설정: [Azure Portal를 사용 하 여 Event Hubs 캡처를 사용 하도록 설정](event-hubs-capture-enable-through-portal.md)합니다. 이전 단계에서 만든 저장소 계정 및 blob 컨테이너를 선택 합니다. 또한 이벤트 허브를 만들 때이 기능을 사용 하도록 설정할 수 있습니다. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>이벤트 허브로 이벤트를 보내는 Python 스크립트 만들기
이 섹션에서는 이벤트 허브로 200 이벤트 (10 개 장치 * 20 개 이벤트)를 전송 하는 Python 스크립트를 만듭니다. 이러한 이벤트는 JSON 형식으로 전송 되는 샘플 환경 읽기입니다. 

1. 선호하는 Python 편집기(예: [Visual Studio Code][Visual Studio Code])를 엽니다.
2. **sender.py**라는 스크립트를 만듭니다. 
3. Sender.py에 다음 코드를 붙여 넣습니다. 자세한 내용은 코드 주석을 참조 하십시오. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. 스크립트에서 다음 값을 바꿉니다.
    1. `EVENT HUBS NAMESPACE CONNECTION STRING` Event Hubs 네임 스페이스에 대한 연결 문자열로 바꿉니다.
    2. `EVENT HUB NAME`을 이벤트 허브의 이름으로 바꿉니다. 
5. 이벤트 허브로 이벤트를 전송 하는 스크립트를 실행 합니다. 
6. Azure Portal에서 이벤트 허브가 메시지를 받았는지 확인할 수 있습니다. **메트릭** 섹션에서 **메시지** 뷰로 전환 합니다. 페이지를 새로 고쳐 차트를 업데이트 합니다. 메시지가 수신 되었음을 표시 하는 데 몇 초 정도 걸릴 수 있습니다. 

    [이벤트 허브에서 메시지를 받았는지 확인 ![](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>캡처 파일을 읽는 Python 스크립트 만들기
이 예제에서 캡처된 데이터는 Azure Blob Storage에 저장 됩니다. 이 섹션의 스크립트는 Azure Storage에서 캡처 데이터 파일을 읽고 콘텐츠를 쉽게 열고 볼 수 있는 CSV 파일을 생성 합니다. 응용 프로그램의 현재 작업 디렉터리에 10 개의 파일이 표시 됩니다. 이러한 파일에는 10 개의 장치에 대한 환경 판독값이 포함 됩니다. 

1. Python 편집기에서 **capturereader.py**라는 스크립트를 만듭니다. 이 스크립트는 캡처된 파일을 읽고 디바이스마다 파일을 만들어 해당 디바이스에 대한 데이터만 씁니다.
2. Capturereader.py에 다음 코드를 붙여 넣습니다. 자세한 내용은 코드 주석을 참조 하십시오. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
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
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. `<AZURE STORAGE CONNECTION STRING>`을 Azure Storage 계정에 대한 연결 문자열로 바꿉니다. 이 자습서에서 만든 컨테이너의 이름은 **capture**입니다. 컨테이너에 다른 이름을 사용한 경우 `capture`를 저장소 계정의 컨테이너 이름으로 바꿉니다. 

## <a name="run-the-scripts"></a>스크립트 실행
1. 해당 경로에 Python을 포함하는 명령 프롬프트를 열고 다음 명령을 실행하여 Python 필수 구성 요소 패키지를 설치합니다.
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. sender.py 및 capturereader.py를 저장한 디렉터리로 변경하고 다음 명령을 실행합니다.
   
   ```
   python sender.py
   ```
   
   이 명령은 발신자를 실행하는 새 Python 프로세스를 시작합니다.
3. 몇 분 정도 기다리면 캡처가 실행됩니다. 그런 다음 원래 명령 창에 다음 명령을 입력합니다.
   
   ```
   python capturereader.py
   ```

   이 캡처 프로세서는 로컬 디렉터리를 사용하여 스토리지 계정/컨테이너에서 모든 Blob을 다운로드합니다. 비어 있지 않은 모든 Blob을 처리하고 로컬 디렉터리에 .csv 파일로 결과를 작성합니다.

## <a name="next-steps"></a>다음 단계
[여기](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)에서 GitHub의 Python 샘플을 확인 하세요. 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
