---
title: Azure Media Services v 2에서 v3로 마이그레이션
description: 이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: be0c12eacae9bb13a475de4634746e9d38d35e43
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267567"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2와 v3 비교

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다.

## <a name="general-changes-from-v2"></a>V2의 일반 변경 내용

* V3로 생성 된 자산의 경우 Media Services는 [Azure Storage 서버 쪽 저장소 암호화](../../storage/common/storage-service-encryption.md)만 지원 합니다.
    * Media Services에서 제공하는 [스토리지 암호화](../previous/media-services-rest-storage-encryption.md)(AES 256)를 사용하는 v2 API로 만든 자산에 v3 API를 사용할 수 있습니다.
    * v3 API를 사용하는 레거시 AES 256 [스토리지 암호화](../previous/media-services-rest-storage-encryption.md)로는 새 자산을 만들 수 없습니다.
* V3의 [자산](assets-concept.md)속성은 v 2와 다릅니다. [속성 매핑 방법](#map-v3-asset-properties-to-v2)을 참조 하세요.
* v3 SDK가 Storage SDK에서 분리되었으며, 따라서 사용하고 싶은 Storage SDK를 보다 철저하게 제어하고 버전 문제를 피할 수 있게 되었습니다. 
* v3 API에서 모든 인코딩 비트 전송률은 비트/초입니다. 이것은 v2 Media Encoder Standard 미리 설정과 다른 점입니다. 예를 들어 v2의 비트 전송률은 128(kbps)로 지정되지만 v3에서는 128000(비트/초)이 됩니다. 
* Entities AssetFiles, AccessPolicies 및 IngestManifests는 v3에 없습니다.
* v3에서 IAsset.ParentAssets 속성이 없습니다.
* ContentKeys는 더 이상 엔터티가 아닌 스트리밍 로케이터의 속성입니다.
* Event Grid 지원은 NotificationEndpoints를 대체합니다.
* 다음 엔터티의 이름이 바뀌었습니다.
    * Job Output은 Task를 대체하며, 이제 Job의 일부입니다.
    * Streaming Locator는 Locator를 대체합니다.
    * Live Event는 Channel을 대체합니다.<br/>라이브 이벤트 요금은 라이브 채널 미터를 기반으로 청구됩니다. 자세한 내용은 [대금 청구](live-event-states-billing.md) 및 [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요.
    * Live Output은 Program을 대체합니다.
* 라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. v2 API에서는 프로그램이 다르게 작동했습니다. 생성 후 시작해야 했습니다.
* 작업에 대 한 정보를 가져오려면 작업이 만들어진 변환 이름을 알아야 합니다. 
* V2에서 XML [입력](../previous/media-services-input-metadata-schema.md) 및 [출력](../previous/media-services-output-metadata-schema.md) 메타 데이터 파일은 인코딩 작업의 결과로 생성 됩니다. V3에서 메타 데이터 형식이 XML에서 JSON으로 변경 되었습니다. 
* Media Services v 2에서는 IV (initialization vector)를 지정할 수 있습니다. Media Services v3에서는 FairPlay IV를 지정할 수 없습니다. 패키징 및 라이선스 배달 모두에 대해 Media Services를 사용 하는 고객에 게는 영향을 주지 않지만 타사 DRM 시스템을 사용 하 여 FairPlay 라이선스 (하이브리드 모드)를 제공할 때 문제가 될 수 있습니다. 이 경우 FairPlay IV는 cbcs-aapl 키 ID에서 파생 되 고 다음 수식을 사용 하 여 검색할 수 있다는 것을 알고 있어야 합니다.

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    다음과 같이 바꿉니다.

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    자세한 내용은 [라이브 및 VOD 작업 모두에 대 한 Azure Functions c # 코드 (하이브리드 모드의 Media Services v3](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3))를 참조 하세요.
 
> [!NOTE]
> [Media Services v3 리소스](media-services-apis-overview.md#naming-conventions)에 적용 되는 명명 규칙을 검토 합니다. [Blob 명명](assets-concept.md#naming)도 검토 합니다.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>v2 API와 관련된 기능 격차

v3 API는 v2 API와 관련하여 다음과 같은 기능 격차가 있습니다. 격차를 해소하기 위한 작업이 진행 중입니다.

* v3를 통해 [프리미엄 인코더](../previous/media-services-premium-workflow-encoder-formats.md) 및 레거시 [미디어 분석 프로세서](../previous/media-services-analytics-overview.md)(Azure Media Services Indexer 2 미리 보기, Face Redactor 등)에 액세스할 수 없습니다.<br/>Media Indexer 1 또는 2 미리 보기에서 마이그레이션하려는 고객은 v3 API의 AudioAnalyzer 프리셋을 즉시 사용할 수 있습니다.  이 새 프리셋은 기존 Media Indexer 1 또는 2보다 많은 기능을 포함하고 있습니다. 
* [V2 api에 있는 Media Encoder Standard의 다양 한 고급 기능은](../previous/media-services-advanced-encoding-with-mes.md) 현재 v3에서 사용할 수 없습니다. 예를 들면 다음과 같습니다.
  
    * 자산 연결
    * 오버레이
    * 자르기
    * 썸네일 스프라이트
    * 입력에 오디오가 없을 때 무음 오디오 트랙 삽입
    * 입력에 비디오가 없을 때 비디오 트랙 삽입
* 코드 변환을 사용하는 라이브 이벤트는 현재 슬레이트 삽입 중간 스트림 및 API 호출을 통한 광고 표시기 삽입을 지원하지 않습니다. 
* `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git`에서 V2 REST API를 사용 하는 방법에 대 한 모범 사례 및 패턴은 샘플 코드를 참조 하세요. NETCore SDK.

## <a name="asset-specific-changes"></a>Asset 관련 변경 내용

### <a name="map-v3-asset-properties-to-v2"></a>V2에 v3 자산 속성 매핑

다음 표에서는 v3의 [자산](/rest/api/media/assets/createorupdate#asset)속성이 v 2의 자산 속성에 매핑되는 방법을 보여 줍니다.

|v3 속성|v2 속성|
|---|---|
|`id`-(고유) 전체 Azure Resource Manager 경로 ( [자산의](/rest/api/media/assets/createorupdate) 예제 참조)||
|`name`-(고유) [명명 규칙](media-services-apis-overview.md#naming-conventions) 참조 ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(고유) 값은 접두사로 시작 `nb:cid:UUID:` 합니다.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (생성 옵션)|
|`type`||

### <a name="storage-side-encryption"></a>스토리지 쪽 암호화

미사용 자산을 보호하려면 스토리지 쪽 암호화를 사용하여 자산을 암호화해야 합니다. 다음 표는 Media Services에서 스토리지 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services 스토리지 암호화|AES-256 암호화, Media Services에서 관리 하는 키입니다.|지원<sup>(1)</sup>|지원되지 않음<sup>(2)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](../../storage/common/storage-service-encryption.md)|Azure Storage에서 제공 하는 서버 쪽 암호화, Azure 또는 고객이 관리 하는 키입니다.|지원됨|지원됨|
|[스토리지 클라이언트 쪽 암호화](../../storage/common/storage-client-side-encryption.md)|Azure storage에서 제공 하는 클라이언트 쪽 암호화는 Key Vault에서 고객이 관리 하는 키입니다.|지원되지 않음|지원되지 않음|

<sup>1</sup> Media Services는 암호화 형식 없이 clear/의 콘텐츠 처리를 지원 하지만 그렇게 하지 않는 것이 좋습니다.

<sup>2</sup> Media Services v3에서 스토리지 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. 즉, v3은 기존 storage 암호화 된 자산과 함께 작동 하지만 새로 만들 수는 없습니다.

## <a name="code-differences"></a>코드 차이점

다음 표는 일반적인 시나리오에서 v2와 v3의 코드가 어떻게 다른지 보여줍니다.

|시나리오|V2 API|V3 API|
|---|---|---|
|자산 만들기 및 파일 업로드 |[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|작업 제출|[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>먼저 변환을 만든 후 작업을 제출하는 방법을 보여줍니다.|
|AES 암호화를 사용하여 자산 게시 |1. ContentKeyAuthorizationPolicyOption 만들기<br/>2. ContentKeyAuthorizationPolicy 만들기<br/>3. Asset배달 Ypolicy 만들기<br/>4. 자산을 만들고 콘텐츠를 업로드 하거나 작업을 제출 하 고 출력 자산을 사용 합니다.<br/>5. Asset배달 Ypolicy와 자산 연결<br/>6. ContentKey 만들기<br/>7. 콘텐츠 키를 자산에 연결<br/>8. AccessPolicy 만들기<br/>9. 로케이터 만들기<br/><br/>[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 콘텐츠 키 정책 만들기<br/>2. 자산 만들기<br/>3. 콘텐츠 업로드 또는 자산을 JobOutput으로 사용<br/>4. 스트리밍 로케이터 만들기<br/><br/>[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|작업 세부 정보 가져오기 및 작업 관리 |[V2를 사용 하 여 작업 관리](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[V3로 작업 관리](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> 이 문서에 책갈피를 지정하고 업데이트를 계속 확인하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md)
