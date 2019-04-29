---
title: REST를 사용하여 Azure Media Services 계정에 파일 업로드 | Microsoft Docs
description: 자산을 만들고 업로드하여 Media Services에 미디어 콘텐츠를 가져오는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f63087d107b9db30e2af6273afde7f51f1c72404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817598"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>REST를 사용하여 Media Services 계정에 파일 업로드  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST (영문)](media-services-rest-upload-files.md)
> * [포털](media-services-portal-upload-files.md)
> 

Media Services에서 자산에 디지털 파일을 업로드합니다. [자산](https://docs.microsoft.com/rest/api/media/operations/asset) 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다.  자산에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다. 

이 자습서에서는 파일을 업로드하는 방법 및 파일과 관련된 기타 작업에 대해 알아보겠습니다.

> [!div class="checklist"]
> * 모든 업로드 작업에 대해 Postman 설정
> * Media Services에 연결 
> * 쓰기 권한으로 액세스 정책 만들기
> * 자산 만들기
> * SAS 로케이터 만들기 및 업로드 URL 만들기
> * 업로드 URL을 사용하여 Blob Storage에 파일 업로드
> * 자산에 업로드한 미디어 파일에 대한 메타데이터 만들기

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [Azure Portal을 사용하여 Azure Media Services 계정을 만듭니다](media-services-portal-create-account.md).
- [AAD 인증을 사용하여 Azure Media Services API 액세스 개요](media-services-use-aad-auth-to-access-ams-api.md)를 검토합니다.
- **Postman**을 구성합니다([Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md) 참조).

## <a name="considerations"></a>고려 사항

Media Services REST API를 사용할 때는 다음 사항을 고려해야 합니다.
 
* Media Services REST API를 사용하여 엔터티에 액세스하는 경우 HTTP 요청에 특정 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요. <br/>이 자습서에서 사용된 Postman 컬렉션은 필요한 모든 헤더 설정을 처리합니다.
* Media Services는 스트리밍 콘텐츠(예: http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.)를 위해 URL을 작성할 때 IAssetFile.Name 속성 값을 사용합니다. 이러한 이유로 퍼센트 인코딩은 허용되지 않습니다. **Name** 속성 값에는 !*'();:@&=+$,/?%#[]"와 같은 [퍼센트 인코딩 예약 문자](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 파일 이름 확장명에는 ‘.’ 하나만 사용할 수 있습니다.
* 이름 길이는 260자보다 클 수 없습니다.
* Media Services에서 처리를 위해 지원되는 최대 파일 크기에 제한이 있습니다. 파일 크기 제한에 대한 세부 정보는 [이](media-services-quotas-and-limitations.md) 문서를 참조하세요.

## <a name="set-up-postman"></a>Postman 설정

이 자습서를 위해 Postman을 설정하는 방법에 대한 자세한 내용은 [Postman 구성](media-rest-apis-with-postman.md)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

1. 사용자 환경에 연결 값을 추가합니다. 

    **MediaServices** [환경](postman-environment.md)에 속한 일부 변수를 수동으로 먼저 설정해야 [컬렉션](postman-collection.md)에 정의된 작업을 실행할 수 있습니다.

    처음 5개 변수의 값을 가져오려면 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

    ![파일 업로드](./media/media-services-rest-upload-files/postman-import-env.png)
2. **MediaFileName** 환경 변수의 값을 지정합니다.

    업로드할 미디어의 파일 이름을 지정합니다. 이 예제에서는 BigBuckBunny.mp4를 업로드하겠습니다. 
3. **AzureMediaServices.postman_environment.json** 파일을 검사합니다. 컬렉션의 거의 모든 작업이 “test” 스크립트를 실행하는 것을 확인할 수 있습니다. 스크립트는 응답으로 반환된 일부 값을 사용하고 적절한 환경 변수를 설정합니다.

    예를 들어 첫 번째 작업은 액세스 토큰을 가져와 다른 모든 작업에서 사용되는 **AccessToken** 환경 변수에 설정합니다.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. **Postman** 창의 왼쪽에서 **1. AAD 인증 토큰 가져오기** -> **서비스 주체에 대한 Azure AD 토큰 가져오기**를 클릭합니다.

    URL 부분은 **AzureADSTSEndpoint** 환경 변수(자습서의 앞부분에서 컬렉션을 지원하는 환경 변수 값을 설정)로 채웁니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postment-get-token.png)

5. **보내기**를 누릅니다.

    “access_token”이 포함된 응답을 볼 수 있습니다. “test” 스크립트는 이 값을 사용하고 **AccessToken** 환경 변수를 설정합니다(위 설명 참조). 환경 변수를 검사하면 이 변수에 이제 나머지 작업에서 사용되는 액세스 토큰(전달자 토큰) 값이 포함되어 있음을 확인할 수 있습니다. 

    토큰이 만료되면 “서비스 주체에 대한 Azure AD 토큰 가져오기” 단계를 다시 수행합니다. 

## <a name="create-an-access-policy-with-write-permission"></a>쓰기 권한으로 액세스 정책 만들기

### <a name="overview"></a>개요 

>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 배치되는 로케이터에 대한 정책) 동일한 정책 ID를 사용해야 합니다. 자세한 내용은 [이](media-services-dotnet-manage-entities.md#limit-access-policies) 문서를 참조하세요.

년간 File Storage  유지 관리 비용 합계 이렇게 하려면 AccessPolicies 엔터티 집합에 HTTP 요청을 게시합니다. 작성 시 DurationInMinutes 값을 정의하지 않으면 응답에서 500 내부 서버 오류 메시지가 다시 나타납니다. AccessPolicies에 대한 자세한 내용은 [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy)를 참조하세요.

### <a name="create-an-access-policy"></a>액세스 정책 만들기

1. **AccessPolicy** -> **업로드에 대한 AccessPolicy 만들기**를 선택합니다.
2. **보내기**를 누릅니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-access-policy.png)

    "test" 스크립트는 AccessPolicy Id를 가져와 해당 환경 변수를 설정합니다.

## <a name="create-an-asset"></a>자산 만들기

### <a name="overview"></a>개요

[자산](https://docs.microsoft.com/rest/api/media/operations/asset)은 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일을 포함하여 여러 개체 유형이나 집합에 사용되는 컨테이너입니다. REST API에서 자산을 만들려면 Media Services에 POST 요청을 보내고 자산에 대한 속성 정보를 요청 본문에 배치해야 합니다.

자산을 만들 때 추가할 수 있는 속성 중 하나는 **옵션**입니다. 다음 암호화 옵션 중 하나를 지정할 수 있습니다. **없음**(기본적으로 암호화가 사용되지 않음), **StorageEncrypted**(클라이언트 쪽 스토리지 암호화를 사용하여 미리 암호화된 콘텐츠의 경우), **CommonEncryptionProtected** 또는 **EnvelopeEncryptionProtected**. 암호화된 자산이 있는 경우 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

자산이 암호화된 경우 **ContentKey**를 만들어 자산에 연결해야 합니다 ([ContentKey를 만드는 방법](media-services-rest-create-contentkey.md) 문서의 설명 참조). 자산에 파일을 업로드한 후에는 **자산**을 암호화하는 동안 얻은 값으로 **AssetFile** 엔터티의 암호화 속성을 업데이트해야 합니다. **MERGE** HTTP 요청을 사용하여 이를 수행합니다. 

이 예제에서는 암호화되지 않은 자산을 만듭니다. 

### <a name="create-an-asset"></a>자산 만들기

1. **자산** -> **자산 만들기**를 선택합니다.
2. **보내기**를 누릅니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-create-asset.png)

    "test" 스크립트는 Asset Id를 가져와 해당 환경 변수를 설정합니다.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>SAS 로케이터 만들기 및 업로드 URL 만들기

### <a name="overview"></a>개요

AccessPolicy와 로케이터를 설정했으면 실제 파일은 Azure Storage REST API를 사용하여 Azure Blob Storage 컨테이너에 업로드됩니다. 블록 blob으로 파일을 업로드해야 합니다. 페이지 blob은 Azure Media Services에서 지원되지 않습니다.  

Azure 저장소 Blob 작업에 대한 자세한 내용은 [Blob 서비스 REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)를 참조하세요.

실제 업로드 URL을 받으려면 SAS 로케이터를 만듭니다(아래 참조). 로케이터는 자산에 있는 파일에 액세스하려는 클라이언트에 대한 시작 시간과 연결 엔드포인트의 형식을 정의합니다. 다양한 클라이언트 요청 및 요구 사항을 처리하기 위해 지정된 AccessPolicy 및 자산 쌍에 대해 여러 로케이터 엔터티를 만들 수 있습니다. 이러한 각 로케이터는 AccessPolicy의 StartTime 값과 DurationInMinutes 값을 사용하여 URL이 사용될 수는 시간의 길이를 결정합니다. 자세한 내용은 [로케이터](https://docs.microsoft.com/rest/api/media/operations/locator)를 참조하세요.

SAS URL의 형식은 다음과 같습니다.

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>고려 사항

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* 지정된 자산과 연관된 고유 로케이터는 한 번에 5개 이상 가질 수 없습니다. 자세한 내용은 로케이터를 참조하세요.
* 파일을 즉시 업로드해야 하는 경우 StartTime 값을 현재 시간에서 5분 전으로 설정해야 합니다. 클라이언트 컴퓨터와 Media Services 사이에 시간차가 있을 수 있기 때문입니다. 또한 StartTime 값은 다음 날짜/시간 형식이어야 합니다. YYYY-MM-DDTHH:mm:ssZ(예: "2014-05-23T17:53:50Z").    
* 로케이터를 만든 후 사용할 수 있을 때까지 30-40초의 지연이 있을 수 있습니다.

### <a name="create-a-sas-locator"></a>SAS 로케이터 만들기

1. **로케이터** -> **SAS 로케이터 만들기**를 선택합니다.
2. **보내기**를 누릅니다.

    “test” 스크립트는 지정한 미디어 파일 이름 및 SAS 로케이터 정보에 따라 “업로드 URL”을 만들고 적절한 환경 변수를 설정합니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>업로드 URL을 사용하여 Blob Storage에 파일 업로드

### <a name="overview"></a>개요

이제 업로드 URL이 있으므로 Azure Blob API로 일부 코드를 직접 작성하여 SAS 컨테이너에 파일을 업로드해야 합니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure Storage REST API 사용](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob 배치](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Blob Storage에 Blob 업로드](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Postman을 사용하여 파일 업로드

예를 들어 Postman을 사용하여 작은 .mp4 파일을 업로드합니다. Postman을 통한 이진 업로드에 대한 파일 크기 제한이 있을 수 있습니다.

업로드 요청은 **AzureMedia** 컬렉션의 일부가 아닙니다. 

새 요청을 만들고 설정합니다.
1. **+** 를 눌러 새 요청 탭을 만듭니다.
2. **PUT** 작업을 선택하고 URL에 **{{UploadURL}}** 을 붙여넣습니다.
2. **권한 부여** 탭을 그대로 유지합니다(**전달자 토큰**으로 설정 안 함).
3. **헤더** 탭에서 다음과 같이 지정합니다. **키**: "x-ms-blob-type" 및 **값**: "BlockBlob".
2. **본문** 탭에서 **이진**을 클릭합니다.
4. **MediaFileName** 환경 변수에 지정한 이름을 가진 파일을 선택합니다.
5. **보내기**를 누릅니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>자산에 메타데이터 만들기

파일이 업로드되면 자산과 연관된 Blob Storage에 업로드한 미디어 파일에 대한 메타데이터를 자산에 만들어야 합니다.

1. **AssetFiles** -> **CreateFileInfos**를 선택합니다.
2. **보내기**를 누릅니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-create-file-info.png)

파일을 업로드하고 해당 메타데이터를 설정해야 합니다.

## <a name="validate"></a>유효성 검사

파일이 성공적으로 업로드되었는지 확인하려면 [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile)을 쿼리하고 **ContentFileSize**(또는 기타 세부 정보)를 새 자산에 표시될 예상 값과 비교하는 것이 좋습니다. 

예를 들어 다음 **GET** 작업은 자산 파일(예제에서는 BigBuckBunny.mp4 파일)에 대한 파일 데이터를 가져옵니다. 쿼리는 앞에서 설정한 [환경 변수](postman-environment.md)를 사용하고 있습니다.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

응답에는 크기, 이름 및 기타 정보가 포함됩니다.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>다음 단계

이제 업로드된 자산을 인코딩할 수 있습니다. 자세한 내용은 [자산 인코딩](media-services-portal-encode.md)을 참조하세요.

또한 Azure Functions를 사용하여 구성된 컨테이너에 도착하는 파일에 따라 인코딩 작업을 트리거할 수도 있습니다. 자세한 내용은 [이 샘플](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )을 참조하세요.

