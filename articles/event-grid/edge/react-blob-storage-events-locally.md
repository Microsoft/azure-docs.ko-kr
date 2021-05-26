---
title: Blob Storage 모듈 이벤트에 대응 - Azure Event Grid IoT Edge | Microsoft Docs
description: Blob Storage 모듈 이벤트에 대응
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 461e8c20b70abe3b6b5361de95451a72ea8a0996
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378304"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>자습서: IoT Edge에서 Blob Storage 이벤트에 대응(미리 보기)
이 문서에서는 IoT 모듈에 Azure Blob Storage를 배포하는 방법을 보여줍니다. 이 모듈은 Blob 만들기 및 Blob 삭제에 대한 이벤트를 Event Grid에 전송하는 Event Grid 게시자 역할을 합니다.  

IoT Edge에서 Azure Blob Storage에 대한 개요는 [IoT Edge에 대한 Azure Blob Storage](../../iot-edge/how-to-store-data-blob.md) 및 해당 기능을 참조하세요.

> [!WARNING]
> IoT Edge의 Azure Blob Storage와 Event Grid 통합은 미리 보기로 제공됨

이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 디바이스** - 디바이스가 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 디바이스](../../iot-edge/quickstart.md)용 빠른 시작의 단계를 따릅니다.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge 모듈 배포

IoT Edge 디바이스에 모듈을 배포하는 방법은 여러 가지이며 해당 방법은 모두 IoT Edge의 Azure Event Grid에서 작동합니다. 이 문서에서는 Azure Portal에서 IoT Edge의 Event Grid를 배포하는 단계를 설명합니다.

>[!NOTE]
> 이 자습서에서는 지속성을 사용하지 않는 Event Grid 모듈을 배포합니다. 즉, 모듈을 다시 배포하는 경우 이 자습서에서 만든 모든 토픽 및 구독은 삭제됩니다. 지속성을 설정하는 방법에 대한 자세한 내용은 [Linux에서 상태 유지](persist-state-linux.md) 또는 [Windows에서 상태 유지](persist-state-windows.md) 문서를 참조하세요. 프로덕션 워크로드의 경우 지속성을 사용하는 Event Grid 모듈을 설치하는 것이 좋습니다.


### <a name="select-your-iot-edge-device"></a>IoT Edge 디바이스 선택

1. [Azure 포털](https://portal.azure.com)
1. IoT Hub로 이동합니다.
1. **자동 디바이스 관리** 섹션의 메뉴에서 **IoT Edge** 를 선택합니다. 
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정** 을 선택합니다. 이 페이지를 열린 상태로 유지합니다. 다음 섹션에서 단계를 계속 진행합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에서는 JSON 문서를 수동으로 빌드하지 않고 배포 매니페스트를 만드는 방법을 설명하는 마법사를 제공합니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택합니다.
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈** 을 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: eventgridmodule
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **컨테이너 만들기 옵션**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. 페이지 맨 아래에 있는 **저장**
 1. 다음 섹션을 계속 진행하여 Azure Event Grid 구독자 모듈을 추가한 후 함께 배포합니다.

    >[!IMPORTANT]
    > 이 자습서에서는 HTTP/HTTPs 요청, 클라이언트 인증을 사용하지 않도록 설정하는 Event Grid 모듈을 배포하는 방법을 알아봅니다. 프로덕션 워크로드의 경우 클라이언트 인증을 사용하는 HTTPS 요청 및 구독자만 허용하는 것이 좋습니다. Event Grid 모듈을 안전하게 구성하는 방법에 대한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조하세요.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid 구독자 IoT Edge 모듈 배포

이 섹션에서는 이벤트를 전송할 수 있는 이벤트 처리기 역할을 하는 다른 IoT 모듈을 배포하는 방법을 보여 줍니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 다시 선택합니다. 
1. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈** 을 선택합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: 구독자
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **컨테이너 만들기 옵션**: 없음
1. 페이지 맨 아래에 있는 **저장**
1. 다음 섹션을 계속 진행하여 Azure Blob Storage 모듈을 추가합니다.

## <a name="deploy-azure-blob-storage-module"></a>Azure Blob Storage 모듈 배포

이 섹션에서는 Azure Blob Storage 모듈을 배포하는 방법을 보여줍니다. 이 모듈은 Blob 생성 및 삭제 이벤트를 게시하는 Event Grid 게시자 역할을 합니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택합니다.
2. 드롭다운 목록의 모듈 유형에서 **IoT Edge 모듈** 을 선택합니다.
3. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공합니다.

   * **이름**: azureblobstorageoniotedge
   * **이미지 URI**: mcr.microsoft.com/azure-blob-storage:latest
   * **컨테이너 만들기 옵션**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob Storage 모듈은 HTTPS와 HTTP를 모두 사용하여 이벤트를 게시할 수 있습니다. 
   > - EventGrid에 클라이언트 기반 인증을 사용하도록 설정한 경우 `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`과 같이 EVENTGRID_ENDPOINT 값을 업데이트하여 https를 허용해야 합니다.
   > - 또한 위의 JSON에 다른 환경 변수 `AllowUnknownCertificateAuthority=true`를 추가합니다. HTTPS를 통해 EventGrid와 통신할 때 **AllowUnknownCertificateAuthority** 는 스토리지 모듈이 자체 서명된 EventGrid 서버 인증서를 신뢰하도록 허용합니다.

4. 다음 정보로 복사한 JSON을 업데이트합니다.

   - `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3~24자여야 하고 소문자와 숫자만 사용해야 합니다. 공백 없음

   - `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

   - `<event grid module name>`을 Event Grid 모듈의 이름으로 바꿉니다.
   - 컨테이너 운영 체제에 따라 `<storage mount>`를 대체합니다.
     - Linux 컨테이너의 경우 **my-volume:/blobroot**
     - Windows 컨테이너의 경우 **my-volume:C:/BlobRoot**

5. 페이지 맨 아래에 있는 **저장**
6. **다음** 을 클릭하여 경로 섹션에서 이어서 진행합니다.

    > [!NOTE]
    > Azure VM을 에지 디바이스로 사용하는 경우 이 자습서에 사용된 호스트 포트(4438, 5888, 8080 및 11002)에서 인바운드 트래픽을 허용하는 인바운드 포트 규칙을 추가합니다. 규칙을 추가하는 방법에 대한 지침은 [VM에 포트를 여는 방법](../../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요.

### <a name="setup-routes"></a>경로 설정

기본 경로를 유지하고 **다음** 을 선택하여 검토 섹션에서 이어서 진행합니다.

### <a name="review-deployment"></a>배포 검토

1. 검토 섹션에서는 이전 섹션의 선택 사항에 따라 만들어진 JSON 배포 매니페스트를 보여줍니다. **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **subscriber** 및 **azureblobstorageoniotedge** 의 네 가지 모듈이 모두 배포되는 것을 확인합니다.
2. 배포 정보를 검토한 다음 **제출** 을 선택합니다.

## <a name="verify-your-deployment"></a>배포 확인

1. 배포를 제출한 후 IoT Hub의 IoT Edge 페이지로 돌아갑니다.
2. 해당 세부 정보를 열려면 배포할 대상으로 지정한 **IoT Edge 디바이스** 를 선택합니다.
3. 디바이스 세부 정보에서 Event Grid 모듈, 구독자 및 azureblobstorageoniotedge 모듈이 **배포에 지정됨** 및 **디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다.

   모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>BlobCreated 및 Blobcreated 이벤트 게시

1. 이 모듈은 **MicrosoftStorage** 항목을 자동으로 만듭니다. 존재하는지 확인
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    샘플 출력:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용하는 경우 이전에 지정된 SAS 키를 헤더로 추가해야 합니다. 따라서 말아 넘기기 요청은 다음과 같습니다. `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용하도록 설정하는 경우 말아 넘기기 요청은 다음과 같습니다. `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 구독자는 항목에 게시된 이벤트를 등록할 수 있습니다. 이벤트를 수신하려면 **MicrosoftStorage** 항목에 대한 Event Grid 구독을 만들어야 합니다.
    1. 다음 콘텐츠를 사용하여 blobsubscription.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > **endpointType** 속성은 구독자가 **웹후크** 임을 지정합니다.  **endpointUrl** 은 구독자가 이벤트를 수신 대기하는 URL을 지정합니다. 이 URL은 앞에서 배포한 Azure 구독자 샘플에 해당합니다.

    2. 다음 명령을 실행하여 항목에 대해 구독을 만듭니다. HTTP 상태 코드가 `200 OK`인지 확인합니다.

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용하는 경우 이전에 지정된 SAS 키를 헤더로 추가해야 합니다. 따라서 말아 넘기기 요청은 다음과 같습니다. `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용하도록 설정하는 경우 말아 넘기기 요청은 다음과 같습니다. `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 다음 명령을 실행하여 구독이 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       샘플 출력:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - HTTPS 흐름의 경우 SAS 키를 통해 클라이언트 인증을 사용하는 경우 이전에 지정된 SAS 키를 헤더로 추가해야 합니다. 따라서 말아 넘기기 요청은 다음과 같습니다. `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - HTTPS 흐름의 경우 인증서를 통해 클라이언트 인증을 사용하도록 설정하는 경우 말아 넘기기 요청은 다음과 같습니다. `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 다운로드하여 [로컬 스토리지에 연결](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>이벤트 전송 확인

### <a name="verify-blobcreated-event-delivery"></a>BlobCreated 이벤트 전송 확인

1. Azure Storage Explorer에서 로컬 스토리지에 블록 Blob으로 파일을 업로드하면 모듈에서 자동으로 만들기 이벤트를 게시합니다. 
2. 만들기 이벤트에 대한 구독자 로그를 확인합니다. [이벤트 전송을 확인](pub-sub-events-webhook-local.md#verify-event-delivery)하는 단계를 수행합니다.

    예제 출력:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>BlobDeleted 이벤트 전송 확인

1. Azure Storage Explorer를 사용하여 로컬 스토리지에서 Blob을 삭제하면 모듈이 자동으로 삭제 이벤트를 게시합니다. 
2. 삭제 이벤트에 대한 구독자 로그를 확인합니다. [이벤트 전송을 확인](pub-sub-events-webhook-local.md#verify-event-delivery)하는 단계를 수행합니다.

    예제 출력:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

축하합니다! 자습서를 완료했습니다. 다음 섹션에서는 이벤트 속성에 대한 세부 정보를 제공합니다.

### <a name="event-properties"></a>이벤트 속성

다음은 지원되는 이벤트 속성과 해당 유형 및 설명 목록입니다. 

| 속성 | Type | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | Type | 설명 |
| -------- | ---- | ----------- |
| api | 문자열 | 이벤트를 트리거하는 작업입니다. 다음 값 중 하나일 수 있습니다. <ul><li>BlobCreated - 허용되는 값은 `PutBlob` 및 `PutBlockList`입니다.</li><li>BlobDeleted - 허용되는 값은 `DeleteBlob`, `DeleteAfterUpload` 및 `AutoDelete`입니다. <p>deleteAfterUpload 원하는 속성이 true로 설정되어 있으므로 Blob이 자동으로 삭제되면 `DeleteAfterUpload` 이벤트가 생성됩니다. </p><p>deleteAfterMinutes 원하는 속성 값이 만료되었으므로 Blob이 자동으로 삭제되면 `AutoDelete` 이벤트가 생성됩니다.</p></li></ul>|
| clientRequestId | 문자열 | 스토리지 API 작업에 대한 클라이언트 제공 요청 ID입니다. 해당 ID는 로그의 ‘client-request-id’ 필드를 사용하여 Azure Storage 진단 로그와의 상관관계를 지정하는 데 사용할 수 있으며, ‘x-ms-client-request-id’ 헤더를 사용하여 클라이언트 요청에 제공할 수 있습니다. 자세한 내용은 [로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| requestId | 문자열 | 스토리지 API 작업에 대한 서비스에서 생성된 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
| eTag | 문자열 | 조건부로 작업을 수행하는 데 사용할 수 있는 값입니다. |
| contentType | 문자열 | Blob에 대해 지정된 콘텐츠 형식입니다. |
| contentLength | 정수 | Blob의 크기(바이트)입니다. |
| blobType | 문자열 | Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다. |
| url | 문자열 | Blob에 대한 경로입니다. <br>클라이언트가 Blob REST API를 사용할 경우 URL의 구조는 다음과 같습니다. *\<storage-account-name\>.blob.core.windows.net/\<container-name\>/\<file-name\>* <br>클라이언트가 Data Lake Storage REST API를 사용할 경우 URL의 구조는 다음과 같습니다. *\<storage-account-name\>.dfs.core.windows.net/\<file-system-name\>/\<file-name\>* |


## <a name="next-steps"></a>다음 단계

Blob Storage 설명서에서 다음 문서를 참조하세요.

- [Blob Storage 이벤트 필터링](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Blob Storage 이벤트 사용에 대한 권장 방법](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

이 자습서에서는 Azure Blob Storage에서 Blob을 만들거나 삭제하여 이벤트를 게시했습니다. 클라우드로 이벤트를 전달하는 방법에 대한 자세한 내용은 다른 자습서(Azure Event Hub or Azure IoT Hub)를 참조하세요. 

- [Azure Event Grid로 이벤트 전달](forward-events-event-grid-cloud.md)
- [Azure IoT Hub로 이벤트 전달](forward-events-iothub.md)
