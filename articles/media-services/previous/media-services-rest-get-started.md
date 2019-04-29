---
title: REST를 사용한 주문형 콘텐츠 제공 시작 | Microsoft Docs
description: 이 자습서에서는 REST API를 사용한 Azure Media Services로 주문형 콘텐츠 배달 애플리케이션을 구현하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: fea9bae9fadc20622a6ca3d2e08db9cd3a92c800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639445"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>REST를 사용한 주문형 콘텐츠 제공 시작  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

이 자습서에서는 AMS(Azure Media Services) REST API를 사용하여 주문형 비디오(VoD) 콘텐츠 제공 애플리케이션을 구현하는 단계를 안내합니다.

기본적인 Media Services 워크플로와 Media Services 개발에 필요한 가장 일반적인 프로그래밍 개체 및 작업을 소개합니다. 자습서를 마치면 업로드하고 인코딩하고 다운로드한 샘플 미디어 파일을 스트리밍하거나 점진적으로 다운로드할 수 있습니다.

다음 이미지에서는 Media Services OData 모델에 대해 VoD 애플리케이션을 개발할 때 가장 일반적으로 사용되는 개체 중 일부를 보여 줍니다.

전체 크기로 보려면 이미지를 클릭합니다.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>필수 조건
Media Services REST API를 사용하여 개발을 시작하려면 다음 필수 조건이 필요합니다.

* Azure 계정. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Media Services 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.
* Media Services REST API를 사용하여 개발하는 방법을 이해합니다. 자세한 내용은 [Media Services REST API 개요](media-services-rest-how-to-use.md)를 참조하세요
* HTTP 요청 및 응답을 보낼 수 있도록 선택한 애플리케이션입니다. 이 자습서에서는 [Fiddler](https://www.telerik.com/download/fiddler)를 사용합니다.

다음 작업은 본 퀵 스타트에 표시됩니다.

1. 스트리밍 엔드포인트 시작(Azure Portal 사용)
2. REST API를 통해 Media Services 계정에 연결합니다.
3. REST API를 통해 새 자산을 만들고 비디오를 업로드합니다.
4. REST API를 통해 원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.
5. REST API를 통해 자산을 게시하고 스트리밍 기능 및 URL 점진적 다운로드를 사용합니다.
6. 콘텐츠를 재생합니다.

>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 유지되는 로케이터에 대한 정책) 동일한 정책 ID를 사용합니다. 자세한 내용은 [이](media-services-dotnet-manage-entities.md#limit-access-policies) 문서를 참조하세요.

이 문서에 사용된 AMS REST 엔터티에 대한 자세한 내용은 [Azure Media Services REST API 참조](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)를 참조하세요. 참고 항목: [Azure Media Services 개념](media-services-concepts.md)

>[!NOTE]
>Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Azure Portal을 사용하여 스트리밍 엔드포인트 시작

Azure Media Services 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 통해 비디오를 제공하는 것입니다. Media Services는 적응 비트 전송률 MP4 인코딩 콘텐츠를 Media Services에서 적시에 지원되는 각 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)의 사전 패키징된 버전을 저장하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다.

스트리밍 엔드포인트를 시작하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 설정 창에서 스트리밍 엔드포인트를 클릭합니다.
3. 기본 스트리밍 엔드포인트를 클릭합니다.

    기본 스트리밍 엔드포인트 세부 정보 창이 나타납니다.

4. 시작 아이콘을 클릭합니다.
5. 저장 단추를 클릭하여 변경 내용을 저장합니다.

## <a id="connect"></a>REST API를 통해 Media Services 계정에 연결합니다.

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a id="upload"></a>REST API를 통해 새 자산을 만들고 비디오를 업로드합니다.

Media Services에서 자산에 디지털 파일을 업로드합니다. **자산** 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다.  자산에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

자산을 만들 때 제공해야 하는 값 중 하나는 자산 생성 옵션입니다. **옵션** 속성은 자산으로 만들 수 있는 암호화 옵션을 설명하는 열거 값입니다. 유효한 값은 이 목록의 값의 조합이 아니라 아래 목록에 있는 값 중 하나입니다.

* **없음** = **0** - 암호화가 사용되지 않습니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다.
    MP4를 배달하려는 경우 이 옵션을 사용하세요.
* **StorageEncrypted** = **1** - 암호화 되어 있지 않은 콘텐츠를 AES-256 비트 암호화를 사용하여 로컬에서 암호화한 다음 암호화되어 저장되는 Azure Storage에 업로드합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.
* **CommonEncryptionProtected** = **2** - 이미 암호화되어 일반적인 암호화 또는 PlayReady DRM(예: PlayReady DRM으로 보호되는 부드러운 스트리밍)으로 보호된 콘텐츠를 업로드하는 경우 이 옵션을 사용합니다.
* **EnvelopeEncryptionProtected** = **4** - AES로 암호화된 HLS를 업로드하는 경우 이 옵션을 사용합니다. 파일을 Transform Manager로 인코딩 및 암호화해야 합니다.

### <a name="create-an-asset"></a>자산 만들기
자산은 여러 유형이나 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택된 캡션 파일을 포함한 Media Services의 개체 집합에 대한 컨테이너입니다. REST API에서 자산을 만들려면 Media Services에 POST 요청을 보내고 자산에 대한 속성 정보를 요청 본문에 배치해야 합니다.

다음 예제에서는 자산을 만드는 방법을 보여 줍니다.

**HTTP 요청**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP 응답**

성공하면 다음이 반환됩니다.

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>AssetFile 만들기
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) 엔터티는 blob 컨테이너에 저장된 비디오 또는 오디오 파일을 나타냅니다. 자산 파일은 항상 자산에 연결되며 자산에는 하나 이상의 AssetFile이 포함될 수 있습니다. 자산 파일 개체가 blob 컨테이너의 디지털 파일과 연결되지 않은 경우 Media Services 인코더 작업을 하지 못합니다.

Blob 컨테이너에 디지털 미디어 파일을 업로드 한 후 **MERGE** HTTP 요청을 사용하여 미디어 파일에 대한 정보로 AssetFile을 업데이트합니다(이 항목의 뒷부분 참조).

**HTTP 요청**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP 응답**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>쓰기 권한으로 AccessPolicy 만들기
년간 File Storage  유지 관리 비용 합계 이렇게 하려면 AccessPolicies 엔터티 집합에 HTTP 요청을 게시합니다. 작성 시 DurationInMinutes 값을 정의하지 않으면 응답에서 500 내부 서버 오류 메시지가 다시 나타납니다. AccessPolicies에 대한 자세한 내용은 [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy)를 참조하세요.

다음 예제에서는 AccessPolicy를 만드는 방법을 보여 줍니다.

**HTTP 요청**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>업로드 URL 가져오기

실제 업로드 URL을 받으려면 SAS 로케이터를 만듭니다. 로케이터는 자산에 있는 파일에 액세스하려는 클라이언트에 대한 시작 시간과 연결 엔드포인트의 형식을 정의합니다. 다양한 클라이언트 요청 및 요구 사항을 처리하기 위해 지정된 AccessPolicy 및 자산 쌍에 대해 여러 로케이터 엔터티를 만들 수 있습니다. 이러한 각 로케이터는 AccessPolicy의 StartTime 값과 DurationInMinutes 값을 사용하여 URL이 사용될 수는 시간의 길이를 결정합니다. 자세한 내용은 [로케이터](https://docs.microsoft.com/rest/api/media/operations/locator)를 참조하세요.

SAS URL의 형식은 다음과 같습니다.

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* 지정된 자산과 연관된 고유 로케이터는 한 번에 5개 이상 가질 수 없습니다. 
* 파일을 즉시 업로드해야 하는 경우 StartTime 값을 현재 시간에서 5분 전으로 설정해야 합니다. 클라이언트 컴퓨터와 Media Services 사이에 시간차가 있을 수 있기 때문입니다. 또한 StartTime 값은 다음 날짜/시간 형식이어야 합니다. YYYY-MM-DDTHH:mm:ssZ(예: "2014-05-23T17:53:50Z").    
* 로케이터를 만든 후 사용할 수 있을 때까지 30-40초의 지연이 있을 수 있습니다. 이 문제는 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 및 원본 로케이터 모두에 적용됩니다.

다음 예제에서는 요청 본문의 형식 속성에서 정의한 대로(SAS 로케이터의 경우 "1" 그리고 주문형 원본 로케이터의 경우 "2") SAS URL 로케이터를 만드는 방법을 보여 줍니다. 반환된 **경로** 속성은 파일 업로드 시 반드시 사용해야 하는 URL을 포함합니다.

**HTTP 요청**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>File Storage 사용 시작
AccessPolicy와 로케이터를 설정했으면 실제 파일은 Azure Storage REST API를 사용하여 Azure Blob Storage 컨테이너에 업로드됩니다. 블록 blob으로 파일을 업로드해야 합니다. 페이지 blob은 Azure Media Services에서 지원되지 않습니다.  

> [!NOTE]
> 이전 섹션에서 받은 로케이터 **경로** 값에 업로드하려는 파일에 대한 파일 이름을 추가해야 합니다. 예: `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`
>
>

Azure 저장소 Blob 작업에 대한 자세한 내용은 [Blob 서비스 REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)를 참조하세요.

### <a name="update-the-assetfile"></a>AssetFile 업데이트
이제 파일을 업로드했으므로 FileAsset 크기(및 기타) 정보를 업데이트합니다. 예를 들면 다음과 같습니다.

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP 응답**

성공하면 다음이 반환됩니다.

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>로케이터와 AccessPolicy 삭제
**HTTP 요청**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 응답**

성공하면 다음이 반환됩니다.

    HTTP/1.1 204 No Content
    ...

**HTTP 요청**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 응답**

성공하면 다음이 반환됩니다.

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩

Media Services에 자산을 삽입하고 나면 미디어를 클라이언트에 배달하기 전에 인코딩, 트랜스믹싱, 워터마크 지정 등을 수행할 수 있습니다. 이러한 활동은 높은 성능과 가용성을 보장하기 위해 여러 백그라운드 역할 인스턴스에 대해 예약 및 실행합니다. 이러한 활동을 작업이라고 하며 각 작업은 자산 파일에서 실제 작업을 수행하는 원자성 작업으로 구성됩니다(자세한 내용은 [작업](https://docs.microsoft.com/rest/api/media/operations/job), [태스크](https://docs.microsoft.com/rest/api/media/operations/task) 설명 참조).

앞에서 언급한 대로, Azure Media Services 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 클라이언트에 제공하는 것입니다. Media Services는 적응 비트 전송률 MP4 파일을 다음 형식 중 하나로 동적 패키징합니다. HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH

다음 섹션에서는 하나의 인코딩 작업을 포함하는 작업을 만드는 방법을 보여 줍니다. 이 작업은 **미디어 인코더 표준**을 사용하여 메자닌 파일을 적응 비트 전송률 MP4 집합으로 트랜스코딩하도록 지정합니다. 이 섹션에서는 작업 진행 상황을 모니터링하는 방법도 보여 줍니다. 작업이 완료되면 자산에 대한 액세스하는 데 필요한 로케이터를 만들 수 있습니다.

### <a name="get-a-media-processor"></a>미디어 프로세서 가져오기
Media Services에서 미디어 프로세서는 인코딩, 형식 변환, 콘텐츠, 암호화 또는 암호 해독 미디어와 같은 특정 처리 작업을 처리 하는 구성 요소입니다. 이 자습서에 표시된 인코딩 작업에서는 미디어 인코더 표준을 사용할 것입니다.

다음 코드는 인코더의 id를 요청합니다.

**HTTP 요청**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 응답**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>작업 만들기
각 작업을 수행하려는 처리 유형에 따라 하나 이상의 작업을 가질 수 있습니다. REST API를 통해 다음 두 가지 방법 중 하나로 작업 및 관련된 작업을 만들 수 있습니다. 작업 엔터티에 대한 작업 탐색 속성 또는 OData 일괄 처리를 통해 작업을 인라인으로 정의할 수 있습니다. Media Services SDK는 일괄 처리를 사용합니다. 하지만 이 문서에 있는 코드 예제 가독성의 경우 작업은 인라인으로 정의됩니다. Batch 처리에 대한 정보는 [Open Data Protocol(OData) Batch 처리](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)를 참조하세요.

다음 예제에서는 특정 해상도와 품질로 비디오를 인코딩하기 위해 하나의 작업 집합으로 작업을 만들어 게시하는 방법을 보여 줍니다. 다음 설명서 섹션은 미디어 인코더 표준 프로세서에서 지원하는 모든 [작업 사전 설정](https://msdn.microsoft.com/library/mt269960) 목록을 포함합니다.  

**HTTP 요청**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


모든 작업 요청에는 몇 가지 중요한 사항이 있습니다.

* TaskBody 속성은 리터럴 XML을 사용하여 작업에서 사용되는 입력이나 출력 수를 정의해야 합니다. 작업 문서에는 해당 XML에 대한 XML 스키마 정의가 포함됩니다.
* TaskBody 정의에서 `<inputAsset>` 및 `<outputAsset>`에 대한 각각의 내부 값은 JobInputAsset(value) 또는 JobOutputAsset(value)으로 설정되어야 합니다.
* 작업 출력 자산은 여러 개일 수 있습니다. 작업에서 하나의 JobOutputAsset(x)을 작업 출력으로 한 번만 사용할 수 있습니다.
* JobInputAsset 또는 JobOutputAsset을 작업의 입력 자산으로 지정할 수 있습니다.
* 작업은 주기를 형성해서는 안됩니다.
* JobInputAsset 또는 JobOutputAsset에 전달하는 값 매개변수는 자산에 대한 인덱스 값을 나타냅니다. 실제 자산은 작업 엔터티 정의에 있는 InputMediaAssets 및 OutputMediaAssets 탐색 속성에 정의됩니다.

> [!NOTE]
> Media Services는 OData v 3를 기반으로 하기 때문에 InputMediaAssets 및 OutputMediaAssets 탐색 속성 컬렉션에 있는 개별 자산은 "__metadata : uri" 이름 값 쌍으로 참조됩니다.
>
>

* InputMediaAsset은 Media Services에서 만든 하나 이상의 자산에 매핑됩니다. OutputMediaAsset은 시스템에 의해 생성됩니다. 기존 자산을 참조하지 않습니다.
* OutputMediaAsset은 assetName 특성을 사용하여 명명할 수 있습니다. 이 특성이 없을 경우 OutputMediaAsset의 이름은 `<outputAsset>` 요소의 내부 텍스트 값이 작업 이름 값 또는 작업 Id 값(이름 속성이 정의되어 있지 않은 경우)의 접미사를 갖는 어떤 것이든 가능합니다. 예를 들어, assetName에 대한 값을 "Sample"로 설정하는 경우 OutputMediaAsset 이름 속성은 "Sample"로 설정됩니다. 하지만 assetName에 대한 값은 설정하지 않았지만 작업 이름을 "NewJob"으로 설정한 경우 OutputMediaAsset 이름은 "JobOutputAsset(값)_NewJob"이 됩니다.

    다음 예제에서는 assetName 특성을 설정하는 방법을 보여 줍니다.

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* 작업 체인을 사용하려면,

  * 작업에 작업이 2개 이상 있어야 합니다.
  * 작업에서 입력이 다른 작업의 출력인 작업이 하나 이상 있어야 합니다.

자세한 내용은 [Media Services REST API를 사용하여 Encoding 작업 만들기](media-services-rest-encode-asset.md)를 참조하세요.

### <a name="monitor-processing-progress"></a>처리 진행 상태 모니터링
다음 예제와 같이 상태 속성을 사용하여 작업 상태를 검색할 수 있습니다.

**HTTP 요청**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>작업 취소
Media Services를 사용하면 CancelJob 함수를 통해 실행 중인 작업을 취소할 수 있습니다. 이 호출은 작업 상태가 취소됨, 취소 중, 오류 또는 완료일 때 작업을 취소하려고 하면 400 오류 코드를 반환합니다.

다음 예제에서는 CancelJob을 호출하는 방법을 보여 줍니다.

**HTTP 요청**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


성공하는 경우 메시지 본문 없이 204 응답 코드가 반환됩니다.

> [!NOTE]
> 작업 id(일반적으로 nb:jid:UUID: somevalue)가 CancelJob에 매개변수로 전달되는 경우 작업 id를 URL 인코딩해야 합니다.
>
>

### <a name="get-the-output-asset"></a>출력 자산 가져오기
다음 코드에서는 출력 자산 id를 요청하는 방법을 보여 줍니다.

**HTTP 요청**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 응답**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>REST API를 통해 자산을 게시하고 스트리밍 기능 및 URL 점진적 다운로드를 사용

자산을 스트리밍하거나 다운로드하려면 먼저 로케이터를 만들어 자산을 "게시"해야 합니다. 로케이터는 자산에 포함된 파일에 대한 액세스를 제공합니다. Media Services는 두 가지 유형의 로케이터를 지원합니다. 하나는 OnDemandOrigin 로케이터로서 미디어를 스트리밍하는 데 사용되고(예: MPEG DASH, HLS 또는 부드러운 스트리밍) 다른 하나는 SAS(공유 액세스 서명) 로케이터로서 미디어 파일을 다운로드하는 데 사용됩니다. 

로케이터를 만든 후에 파일을 스트리밍하거나 다운로드하는 데 사용되는 URL을 작성할 수 있습니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다.

부드러운 스트리밍에 대한 스트리밍 URL의 형식은 다음과 같습니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS에 대한 스트리밍 URL의 형식은 다음과 같습니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH에 대한 스트리밍 URL의 형식은 다음과 같습니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

파일을 다운로드하는 데 사용되는 SAS URL의 형식은 다음과 같습니다.

    {blob container name}/{asset name}/{file name}/{SAS signature}

이 섹션에서는 자산을 "게시"하기 위해 다음 작업을 수행하는 방법을 보여 줍니다.  

* 읽기 권한이 포함된 AccessPolicy 만들기
* 콘텐츠를 다운로드할 SAS URL 만들기
* 콘텐츠 스트리밍을 위한 원본 URL 만들기

### <a name="creating-the-accesspolicy-with-read-permission"></a>읽기 권한이 포함된 AccessPolicy 만들기
미디어 콘텐츠를 다운로드하거나 스트리밍하기 전에 먼저 읽기 권한이 포함된 AccessPolicy를 정의하고 클라이언트에 대해 사용하도록 설정하려는 배달 메커니즘 유형을 지정하는 적절한 로케이터 엔터티를 만듭니다. 사용할 수 있는 속성에 대한 자세한 내용은 [AccessPolicy 엔터티 속성](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties)을 참조하세요.

다음 예제에서는 지정된 자산에 대한 읽기 권한의 AccessPolicy를 지정하는 방법을 보여 줍니다.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

성공하면 사용자가 만든 AccessPolicy 엔터티를 설명하는 201 성공 코드가 반환됩니다. 이제 AccessPolicy Id를 로케이터 엔터티를 만들기 위해 배달하려는 파일이 포함된 자산(예: 출력 자산)의 자산 ID와 함께 사용합니다.

> [!NOTE]
> 이 기본 워크플로는 자산을 통합할 때 파일을 업로드하는 것과 같습니다(이 항목의 앞부분에서 설명함). 또한 파일을 업로드하는 것과 마찬가지로 사용자(또는 클라이언트)가 파일에 즉시 액세스해야 하는 경우 StartTime 값을 현재 시간에서 5분 전으로 설정해야 합니다. 이 동작은 클라이언트와 Media Services 사이에 시간차가 있을 수 있기 때문에 필요합니다. StartTime 값은 다음 날짜/시간 형식이어야 합니다. YYYY-MM-DDTHH:mm:ssZ(예: "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>콘텐츠를 다운로드할 SAS URL 만들기
다음 코드는 이전에 만들어서 업로드한 미디어 파일을 다운로드하는 데 사용할 수 있는 URL을 가져오는 방법을 보여 줍니다. AccessPolicy는 읽기 권한을 설정하고 로케이터 경로는 SAS 다운로드 URL을 참조합니다.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

성공하면 다음 응답이 반환됩니다.

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

반환된 **경로** 속성은 SAS URL을 포함합니다.

> [!NOTE]
> 저장소가 암호화된 콘텐츠를 다운로드하는 경우 렌더링하기 전에 수동으로 암호를 해독하거나 처리 작업에서 저장소 암호 해독 미디어 프로세서를 사용하여 암호화되지 않은 상태로 처리된 파일을 OutputAsset으로 출력한 다음 해당 자산에서 다운로드해야 있습니다. 처리에 대한 자세한 내용은 Media Services REST API를 사용하여 Encoding 작업 만들기를 참조하세요. 또한 SAS URL 로케이터를 만들고 나면 업데이트할 수 없습니다. 예를 들어, 업데이트된 StartTime 값으로 동일한 로케이터를 다시 사용할 수 없습니다. SAS URL을 만드는 방식 때문입니다. 로케이터가 만료된 후 다운로드할 자산에 액세스하려면 새 StartTime으로 새 로케이터를 만들어야 합니다.
>
>

### <a name="download-files"></a>파일 다운로드
AccessPolicy와 로케이터를 설정했으면 Azure Storage REST API를 사용하여 파일을 다운로드할 수 있습니다.  

> [!NOTE]
> 다운로드하려는 파일의 파일 이름을 이전 섹션에서 받은 로케이터 **경로** 값에 추가해야 합니다. 예를 들어 https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Azure 저장소 Blob 작업에 대한 자세한 내용은 [Blob 서비스 REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)를 참조하세요.

이전에 수행한 인코딩 작업(적응 MP4 집합으로 인코딩)의 결과로 점진적으로 다운로드할 수 있는 여러 MP4 파일이 있습니다. 예를 들면 다음과 같습니다.    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>콘텐츠 스트리밍을 위한 스트리밍 URL 만들기
다음 코드에서는 스트리밍 URL 로케이터를 만드는 방법을 보여 줍니다.

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

성공하면 다음 응답이 반환됩니다.

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

스트리밍 미디어 플레이어에서 부드러운 스트리밍 원본 URL을 스트리밍하려면 "/매니페스트" 다음에 부드러운 스트리밍 매니페스트 파일의 이름이 포함된 경로 속성을 추가해야 합니다.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS를 스트리밍하려면 "/매니페스트" 뒤에 추가(format=m3u8-aapl)해야 합니다.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH를 스트리밍하려면 "/매니페스트" 뒤에 추가(format=mpd-time-csf)해야 합니다.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>콘텐츠 재생
비디오를 스트리밍하려면 [Azure Media Services 플레이어](https://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용합니다.

점진적 다운로드를 테스트하려면 IE, Chrome, Safari 등의 브라우저에 URL을 붙여 넣습니다.

## <a name="next-steps-media-services-learning-paths"></a>다음 단계: Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
