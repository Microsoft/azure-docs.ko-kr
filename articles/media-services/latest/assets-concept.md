---
title: Media Services의 자산 - Azure | Microsoft Docs
description: 이 문서에서는 자산이 무엇이고 Azure Media Services가 어떤 자산을 사용하는지 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ec2ddbac5d0368aaf1b46208c9ebb44bf12a622
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734357"
---
# <a name="assets"></a>자산

Azure Media Services의 [자산](https://docs.microsoft.com/rest/api/media/assets)에는 디지털 파일(비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일 포함)과 이러한 파일에 대한 메타데이터가 포함됩니다. 디지털 파일이 자산에 업로드되면 Media Services 콘텐츠 워크플로 인코딩, 스트리밍, 분석에 사용할 수 있습니다. 자세한 내용은 아래의 [자산에 디지털 파일 업로드](#upload-digital-files-into-assets) 섹션을 참조하세요.

자산은 [Azure Storage 계정](storage-account-concept.md)의 Blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 Blob으로 저장됩니다. Media Services는 계정이 범용 v2(GPv2) 스토리지를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 [쿨 또는 보관 스토리지](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)로 이동할 수 있습니다. **보관** 스토리지는 더 이상 필요 없는 원본 파일을 보관하는 데 적합합니다(예: 인코딩된 후).

**보관** 스토리지 계층은 이미 인코딩되었고 인코딩 작업 출력이 출력 Blob 컨테이너에 배치된 대용량 원본 파일에만 사용할 것을 권장합니다. 자산과 연결하여 콘텐츠를 스트리밍 또는 분석하는 데 사용하려는 출력 컨테이너의 Blob은 **핫** 또는 **쿨** 스토리지 계층에 있어야 합니다.

> [!NOTE]
> 날짜/시간 형식의 자산 속성은 언제나 UTC 형식입니다.

## <a name="upload-digital-files-into-assets"></a>자산에 디지털 파일 업로드

일반적인 Media Services 워크플로 중 하나는 파일 업로드, 인코딩 및 스트리밍입니다. 이 섹션에서는 일반 단계를 간략하게 설명합니다.

1. Media Services v3 API를 사용하여 새 "입력" 자산을 만듭니다. 이 작업은 Media Services 계정과 연결된 스토리지 계정에 컨테이너를 만듭니다. API는 컨테이너 이름을 반환합니다(예: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    자산과 연결하려는 Blob 컨테이너가 이미 있는 경우 자산을 만들 때 컨테이너 이름을 지정할 수 있습니다. Media Services는 현재 컨테이너 루트의 Blob만 지원하고 파일 이름의 경로는 지원하지 않습니다. 따라서 파일 이름이 "input.mp4"인 컨테이너는 작동합니다. 하지만 파일 이름이 "videos/inputs/input.mp4"인 컨테이너는 작동하지 않습니다.

    Azure CLI를 사용하여 구독에서 권한이 있는 스토리지 계정 및 컨테이너에 직접 업로드할 수 있습니다. <br/>컨테이너 이름은 고유해야 하며 스토리지 명명 지침을 준수해야 합니다. 이름이 Media Services 자산 컨테이너 이름(자산-GUID) 서식을 따를 필요는 없습니다. 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. 읽기-쓰기 권한을 사용하여 디지털 파일을 자산 컨테이너에 업로드하는 데 사용할 SAS URL을 가져옵니다. Media Services API를 사용하여 [자산 컨테이너 URL을 나열](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)할 수 있습니다.
3. Azure Storage API 또는 SDK(예: [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) 또는 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용하여 파일을 자산 컨테이너에 업로드합니다. 
4. Media Services v3 API를 사용하여 "입력" 자산을 처리하는 Transform 및 Job을 만듭니다. 자세한 내용은 [Transform 및 Jobs](transform-concept.md)를 참조하세요.
5. "출력" 자산의 콘텐츠를 스트리밍합니다.

자산을 만들고, 쓰기 가능한 SAS URL을 스토리지의 자산 컨테이너로 가져오고, SAS URL을 사용하여 파일을 스토리지의 컨테이너에 업로드하는 방법을 보여주는 전체 .NET 예제는 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요.

### <a name="create-a-new-asset"></a>새 자산 만들기

#### <a name="rest"></a>REST (영문)

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST 예제는 [REST를 사용하여 자산 만들기](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) 예제를 참조하세요.

이 예제에서는 설명, 컨테이너 이름, 스토리지 계정, 기타 정보를 포함하여 유용한 정보를 지정할 수 있는 **요청 본문**을 만드는 방법을 보여줍니다.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

전체 예제는 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요. Media Services v3의 작업 입력은 HTTPS URL에서도 만들 수 있습니다([HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md) 참조).

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="storage-side-encryption"></a>저장소 쪽 암호화

미사용 자산을 보호하려면 저장소 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services에서 저장소 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services 저장소 암호화|AES-256 암호화, Media Services에서 키 관리|지원<sup>(1)</sup>|지원되지 않음<sup>(2)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage가 제공하는 서버 쪽 암호화, Azure 또는 고객이 키 관리|지원됨|지원됨|
|[저장소 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage가 제공하는 클라이언트 쪽 암호화, Key Vault의 고객이 키 관리|지원되지 않음|지원되지 않음|

<sup>1</sup> Media Services가 깨끗한/어떠한 형태의 암호화도 없는 콘텐츠 처리를 지원하기는 하지만, 그렇게 하지 않는 것이 좋습니다.

<sup>2</sup> Media Services v3에서 저장소 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. v3는 기존 저장소 암호화된 자산과 함께 작동하지만 새로 만들기를 허용하지는 않습니다.

## <a name="next-steps"></a>다음 단계

* [파일 스트리밍](stream-files-dotnet-quickstart.md)
* [클라우드 DVR 사용](live-event-cloud-dvr.md)
* [Media Services v2와 v3의 차이점](migrate-from-v2-to-v3.md)
