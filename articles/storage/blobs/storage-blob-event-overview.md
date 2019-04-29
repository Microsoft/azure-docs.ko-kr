---
title: Azure Blob Storage 이벤트에 대응 | Microsoft Docs
description: Azure Event Grid를 사용하여 Blob Storage 이벤트를 구독합니다.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: 4bc683908646a5c05fee14f721e2c26482518947
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61427630"
---
# <a name="reacting-to-blob-storage-events"></a>Blob Storage 이벤트에 대응

Azure Storage 이벤트를 사용하면 애플리케이션은 서버 없는 최신 아키텍처를 사용하여 BLOB의 생성 및 삭제에 대응할 수 있습니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다.  대신, 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자로 푸시되거나 사용자 지정 HTTP 수신기로도 푸시되며, 요금은 사용한 만큼만 청구됩니다.

Blob Storage 이벤트는 다양한 다시 시도 정책 및 배달 못한 편지를 통해 응용 프로그램에 신뢰할 수 있는 배달 서비스를 제공하는 Event Grid 서비스에 안정적으로 전송됩니다.

일반적인 Blob Storage 이벤트 시나리오에는 이미지 또는 비디오 처리, 검색 인덱싱, 또는 파일 중심의 워크플로가 포함됩니다.  비동기 파일 업로드는 이벤트에 매우 적합합니다.  변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

빠른 예제를 보려면 [Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅 - CLI](storage-blob-event-quickstart.md) 또는 [Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅 - PowerShell](storage-blob-event-quickstart-powershell.md)을 참조하세요. 

![Event Grid 모델](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob Storage 계정
Blob Storage 이벤트는 범용 v2 저장소 계정과 Blob Storage 계정에서 사용할 수 있습니다. **범용 v2** 저장소 계정은 Blob, 파일, 큐 및 테이블을 포함하여 모든 저장소 서비스의 모든 기능을 지원합니다. **Blob Storage 계정**은 Azure Storage에서 Blob와 같은 구조화되지 않은 데이터(개체) 저장을 위한 특수 Storage 계정입니다. Blob Storage 계정은 범용 스토리지 계정과 유사합니다. 블록 Blob과 연결 Blob에 대한 100% API 일관성을 포함하여 현재 제공되는 뛰어난 내구성, 가용성, 확장성은 모두 같습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

## <a name="available-blob-storage-events"></a>사용 가능한 Blob Storage 이벤트
Event Grid는 [이벤트 구독](../../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다.  Blob Storage 이벤트 구독에는 다음 두 가지 유형의 이벤트가 포함될 수 있습니다.  

> |이벤트 이름|설명|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|`PutBlob`, `PutBlockList` 또는 `CopyBlob` 작업에 의해 Blob이 만들어지거나 대체될 때 실행됩니다.|
> |`Microsoft.Storage.BlobDeleted`|`DeleteBlob` 작업에 의해 Blob이 삭제될 때 실행됩니다.|

## <a name="event-schema"></a>이벤트 스키마
Blob Storage 이벤트에는 데이터 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다.  eventType 속성이 "Microsoft.Storage"로 시작하므로 Blob 스토리지 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../../event-grid/event-schema.md)에 설명되어 있습니다.  

> |자산|Type|설명|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |토픽|문자열|이벤트를 내보내는 저장소 계정의 전체 Azure Resource Manager ID입니다.|
> |제목|문자열|이벤트 주체인 개체에 대한 상대 리소스 경로로, Azure RBAC용 저장소 계정, 서비스 및 컨테이너를 설명하는 데 사용하는 것과 동일한 확장 Azure Resource Manager 형식을 사용합니다.  이 형식은 대소문자가 구분되는 Blob 이름을 포함합니다.|
> |eventTime|문자열|이벤트가 생성된 날짜/시간(ISO 8601 형식)|
> |eventType|문자열|"Microsoft.Storage.BlobCreated" 또는 "Microsoft.Storage.BlobDeleted"|
> |Id|문자열|이 이벤트의 고유 식별자|
> |dataVersion|문자열|데이터 개체의 스키마 버전입니다.|
> |metadataVersion|문자열|최상위 속성의 스키마 버전입니다.|
> |데이터|object|Blob Storage 관련 이벤트 데이터의 컬렉션|
> |data.contentType|문자열|Blob의 콘텐츠 형식으로, Blob에서 Content-Type 헤더에 반환됩니다.|
> |data.contentLength|number|Blob의 크기(바이트 수를 나타내는 정소)로, Blob에서 Content-Length 헤더에 반환됩니다.  BlobCreated 이벤트와 함께 전송되지만 BlobDeleted와 함께 전송되지 않습니다.|
> |data.url|문자열|이벤트의 주체인 개체의 URL|
> |data.eTag|문자열|이 이벤트가 발생할 때 개체의 etag입니다.  BlobDeleted 이벤트에 대해서는 사용할 수 없습니다.|
> |data.api|문자열|이 이벤트를 트리거한 api 작업의 이름입니다. BlobCreated 이벤트의 경우 이 값은 "PutBlob", "PutBlockList" 또는 "CopyBlob"입니다. BlobDeleted 이벤트의 경우 이 값은 "DeleteBlob"입니다. 이러한 값은 Azure Storage 진단 로그에 있는 것과 동일한 api 이름입니다. [기록한 작업 및 상태 메시지](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)를 참조하세요.|
> |data.sequencer|문자열|특정 Blob 이름에 대한 이벤트의 논리적 순서를 나타내는 불투명 문자열 값입니다.  사용자는 표준 문자열 비교를 사용하여 동일한 Blob 이름에 대한 두 이벤트의 상대적 순서를 이해할 수 있습니다.|
> |data.requestId|문자열|저장소 API 작업에 대한 서비스에서 생성된 요청 ID입니다. 로그의 "request-id-header" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, 'x-ms-request-id' 헤더에서 API 호출을 시작하여 반환됩니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요.|
> |data.clientRequestId|문자열|저장소 API 작업에 대한 클라이언트에서 제공된 요청 ID입니다. 로그의 "client-request-id" 필드를 사용하여 Azure Storage 진단 로그와의 상관 관계를 지정하는 데 사용할 수 있으며, "x-ms-client-request-id" 헤더를 사용하여 클라이언트 요청에 제공할 수 있습니다. [로그 형식](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)을 참조하세요. |
> |data.storageDiagnostics|object|경우에 따라 Azure Storage 서비스에 의해 포함되는 진단 데이터입니다. 포함될 경우, 이벤트 소비자는 무시해야 합니다.|
|data.blobType|문자열|Blob의 형식입니다. 유효한 값은 "BlockBlob" 또는 "PageBlob"입니다.| 

다음은 BlobCreated 이벤트의 예입니다.
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

자세한 내용은 [Blob Storage 이벤트 스키마](../../event-grid/event-schema-blob-storage.md)를 참조하세요.

## <a name="filtering-events"></a>이벤트 필터링
Blob 이벤트 구독은 이벤트 형식에 따라, 그리고 생성 또는 삭제된 개체의 컨테이너 이름 및 Blob 이름을 기준으로 필터링할 수 있습니다.  이벤트 구독의 [생성](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 동안 또는 [나중에](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 이벤트 구독에 필터를 적용할 수 있습니다. Event Grid의 제목 필터는 "시작 문자" 및 "끝 문자" 일치 항목을 기준으로 작동하므로 일치하는 제목이 있는 이벤트는 구독자에게 전달됩니다. 

Blob Storage 이벤트의 제목은 다음 형식을 사용합니다.

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

저장소 계정의 모든 이벤트와 일치하는 항목을 찾으려면 제목 필터를 비워 둡니다.

접두사를 공유하는 컨테이너 집합에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containerprefix
```

특정 컨테이너에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containername/
```

Blob 이름 접두사를 공유하는 특정 컨테이너에서 생성된 Blob의 이벤트와 일치하는 항목을 찾으려면 다음과 같은 `subjectBeginsWith` 필터를 사용합니다.

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Blob 접미사를 공유하는 특정 컨테이너에 만들어진 Blob의 이벤트와 일치시키려면 `subjectEndsWith` 필터(예: ".log" 또는 ".jpg")를 사용합니다. 자세한 내용은 [Event Grid 개념](../../event-grid/concepts.md#event-subscriptions)을 참조하세요.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침
Blob Storage 이벤트를 처리하는 애플리케이션은 아래 권장되는 몇 가지 지침을 따라야 합니다.
> [!div class="checklist"]
> * 동일한 이벤트 처리기로 이벤트를 라우팅하도록 여러 구독이 구성될 수 있으므로, 이벤트가 특정 원본에서 온 것이라고 가정하지 않고 메시지의 토픽을 확인하여 예상하는 저장소 계정에서 왔음을 확실히 아는 것이 중요합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * 메시지가 잘못된 순서로 오거나 조금 늦게 도착할 수도 있으므로 etag 필드를 사용하여 개체에 대한 정보가 아직 최신 상태인지 여부를 확인합니다.  또한 sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * blobType 필드를 사용하여 Blob에 허용되는 작업 형식을 파악하고 Blob에 액세스하는 데 사용해야 하는 클라이언트 라이브러리 형식을 확인합니다. 유효한 값은 `BlockBlob` 또는 `PageBlob`입니다. 
> * Blob에 액세스하려면 `CloudBlockBlob` 및 `CloudAppendBlob` 생성자에 URL 필드를 사용합니다.
> * 이해할 수 없는 필드는 무시합니다. 이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Blob Storage 이벤트를 사용해보세요.

- [Event Grid 정보](../../event-grid/overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](storage-blob-event-quickstart.md)
