---
title: AMS REST API를 사용하여 저장소 암호화로 콘텐츠 암호화
description: AMS REST API를 사용하여 저장소 암호화로 콘텐츠를 암호화하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a26388de85ff6293985fe23adac8ca4d04d0de61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952271"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>저장소 암호화로 콘텐츠 암호화 

AES-256비트 암호화를 사용하여 암호화되지 않은 콘텐츠를 로컬에서 암호화한 다음 암호화된 상태로 저장할 Azure Storage에 이를 업로드하는 것이 좋습니다.

이 문서에서는 AMS 저장소 암호화에 대한 개요를 제공하며, 저장소 암호화된 콘텐츠를 업로드하는 방법을 보여 줍니다.

* 콘텐츠 키를 만듭니다.
* 자산을 만듭니다. 자산을 만들 때 AssetCreationOption을 StorageEncryption으로 설정합니다.
  
     암호화된 자산을 콘텐츠 키와 연결합니다.
* 콘텐츠 키를 자산에 연결합니다.  
* AssetFile 엔터티에서 암호화 관련 매개 변수를 설정합니다.

## <a name="considerations"></a>고려 사항 

저장소에서 암호화된 자산을 배달하려는 경우 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버가 저장소 암호화를 제거하고 지정된 배달 정책을 사용하여 콘텐츠를 스트리밍합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요. 

### <a name="storage-side-encryption"></a>저장소 쪽 암호화

|암호화 옵션|설명|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services 저장소 암호화|AES-256 암호화, Media Services에서 키 관리|지원<sup>(1)</sup>|지원되지 않음<sup>(2)</sup>|
|[미사용 데이터에 대한 Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage가 제공하는 서버 쪽 암호화, Azure 또는 고객이 키 관리|지원됨|지원됨|
|[저장소 클라이언트 쪽 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage가 제공하는 클라이언트 쪽 암호화, Key Vault의 고객이 키 관리|지원되지 않음|지원되지 않음|

<sup>1</sup> Media Services가 깨끗한/어떠한 형태의 암호화도 없는 콘텐츠 처리를 지원하기는 하지만, 그렇게 하지 않는 것이 좋습니다.

<sup>2</sup> Media Services v3에서 저장소 암호화(AES-256 암호화)는 자산을 Media Services v2를 사용하여 만들었을 경우 이전 버전과의 호환성에 대해서만 지원됩니다. v3는 기존 저장소 암호화된 자산과 함께 작동하지만 새로 만들기를 허용하지는 않습니다.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a name="storage-encryption-overview"></a>저장소 암호화 개요
AMS 저장소 암호화는 **AES-CTR** 모드 암호화를 전체 파일에 적용합니다.  AES CTR 모드는 임의 길이 데이터를 여백 없이 암호화할 수 있는 블록 암호화입니다. AES 알고리즘으로 카운터 블록을 암호화한 다음 암호화 또는 해독할 데이터에 대해 AES의 출력을 XOR 연산하는 방식으로 작동합니다.  사용되는 카운터 블록은 카운터 값의 0~7바이트에 InitializationVector 값을 복사하여 구조화되며 카운터 값의 8~15바이트는 0으로 설정됩니다. 16바이트 카운터 블록에서 8~15바이트(즉, 최하위 바이트)는 부호 없는 64비트 단순 정수로 사용되며, 처리되는 후속 데이터 블록마다 1씩 증가하고 네트워크 바이트 순서로 유지됩니다. 이 정수가 최대값(0xFFFFFFFFFFFFFFFF)에 도달했을 때 이 값이 증가하면 카운터의 다른 64비트(즉, 0~7바이트)에 미치는 영향 없이 블록 카운트가 0(8~15바이트)으로 재설정됩니다.   AES-CTR 모드 암호화의 보안 유지를 위해 각 콘텐츠 키의 지정된 키 식별자에 대한 InitializationVector 값은 파일마다 고유해야 하며 파일 길이는 2^64블록 미만이어야 합니다.  이 고유값은 카운터 값이 특정 키에서 재사용되지 않도록 합니다. CTR 모드에 대한 자세한 내용은 [이 wiki 페이지](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) 를 참조하세요(wiki 문서에서는 "InitializationVector" 대신 "Nonce"라는 용어를 사용함).

AES 256비트 암호화를 사용하여 암호화되지 않은 콘텐츠를 로컬에서 암호화한 다음에 암호화되어 저장된 Azure Storage에 업로드하려면 **스토리지 암호화**를 사용합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.

저장소에서 암호화된 자산을 배달하려면 Media Services에서 콘텐츠 배달 방법을 알 수 있도록 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거하고 지정된 배달 정책(예: AES, 일반 암호화 또는 암호화 없음)을 사용하여 콘텐츠를 스트리밍합니다.

## <a name="create-contentkeys-used-for-encryption"></a>암호화에 사용되는 ContentKey 만들기
암호화된 자산을 저장소 암호화 키에 연결합니다. 자산 파일을 만들기 전에 암호화에 사용할 콘텐츠 키를 만듭니다. 이 섹션에서는 콘텐츠 키를 만드는 방법을 설명합니다.

다음은 암호화하려는 자산과 연결할 콘텐츠 키를 생성하기 위한 일반적인 단계입니다. 

1. 저장소 암호화를 위해 32바이트 AES 키를 임의로 생성합니다. 
   
    32바이트 AES 키는 자산에 대한 콘텐츠 키입니다. 즉, 암호화하는 동안 해당 자산과 연결된 모든 파일이 동일한 콘텐츠 키를 사용해야 합니다. 
2. [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) 및 [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) 메서드를 호출하여 콘텐츠 키를 암호화하는 데 사용해야 하는 올바른 X.509 인증서를 가져옵니다.
3. X.509 인증서의 공개 키로 콘텐츠 키를 암호화합니다. 
   
   Media Services.NET SDK는 암호화 시 OAEP가 포함된 RSA를 사용합니다.  [EncryptSymmetricKeyData 함수](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)에서 .NET 예제를 확인할 수 있습니다.
4. 키 식별자 및 콘텐츠 키를 사용하여 계산된 체크섬 값을 만듭니다. 다음.NET 예제에서는 키 식별자와 암호화되지 않은 콘텐츠 키의 GUID 부분을 사용하여 체크섬을 계산합니다.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. 이전 단계에서 받은**EncryptedContentKey**(base64 인코딩된 문자열로 변환), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** 및 **Checksum** 값을 사용하여 콘텐츠 키를 만듭니다.

    저장소 암호화를 위해 다음 속성을 요청 본문에 포함해야 합니다.

    요청 본문 속성    | 설명
    ---|---
    Id | ContentKey ID는 다음 형식을 사용 하 여 생성 됩니다 "nb:kid:UUID:\<새 GUID >"입니다.
    ContentKeyType | 콘텐츠 키 형식은 키를 정의하는 정수입니다. 저장소 암호화 형식에서 값은 1입니다.
    EncryptedContentKey | 256비트(32바이트) 값인 새 콘텐츠 키 값을 만듭니다. GetProtectionKeyId 및 GetProtectionKey 메서드에 대한 HTTP GET 요청을 실행하여 Microsoft Azure Media Services에서 검색하는 저장소 암호화 X.509 인증서를 사용하여 키를 암호화합니다. .NET 코드에 대한 예제로, **여기** 에 정의된 [EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)메서드를 참조하세요.
    ProtectionKeyId | 콘텐츠 키를 암호화하는 데 사용한 저장소 암호화 X.509 인증서에 대한 보호 키 ID입니다.
    ProtectionKeyType | 콘텐츠 키를 암호화하는 데 사용한 보호 키에 대한 암호화 형식입니다. 이 값은 예제에서 StorageEncryption(1)입니다.
    Checksum |콘텐츠 키에 대한 MD5 계산 된 체크섬입니다. 콘텐츠 키로 콘텐츠 ID를 암호화하여 계산합니다. 예제 코드는 체크섬을 계산하는 방법을 보여줍니다.


### <a name="retrieve-the-protectionkeyid"></a>ProtectionKeyId 검색
다음 예제에서는 콘텐츠 키를 암호화할 때 사용해야 하는 인증서의 ProtectionKeyId(인증서 지문)를 검색하는 방법을 보여 줍니다. 컴퓨터에 적절한 인증서가 이미 있는지 확인하려면 이 단계를 수행합니다.

요청:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

응답:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>ProtectionKeyId에 대한 Protectionkey 검색
다음 예제에서는 이전 단계에서 받은 ProtectionKeyId를 사용하여 X.509 인증서를 검색하는 방법을 보여 줍니다.

요청:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

응답:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>콘텐츠 키 만들기
X.509 인증서를 검색한 다음 이 인증서의 공개 키를 사용하여 콘텐츠 키를 암호화한 후 **ContentKey** 엔터티를 만들고 해당 속성 값을 적절하게 설정합니다.

콘텐츠 키를 만들 때 설정해야 하는 값 중 하나가 이 유형입니다. 저장소 암호화를 사용할 때 값은 '1'로 설정해야 합니다. 

다음 예제에서는 저장소 암호화("1")에 대해 설정된 **ContentKeyType**과 "0"으로 설정된 **ProtectionKeyType**으로 **ContentKey**를 만들어서 보호 키 ID가 X.509 인증서 지문임을 나타내는 방법을 보여줍니다.  

요청

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>자산 만들기
다음 예제에서는 자산을 만드는 방법을 보여 줍니다.

**HTTP 요청**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

    HTP/1.1 201 Created
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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>자산으로 ContentKey 연결
ContentKey를 만든 후 다음 예제와 같이 $links 작업을 사용하여 이를 자산에 연결합니다.

요청:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

응답:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>AssetFile 만들기
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) 엔터티는 blob 컨테이너에 저장된 비디오 또는 오디오 파일을 나타냅니다. 자산 파일은 항상 자산에 연결되며 자산에는 하나 이상의 자산 파일이 포함될 수 있습니다. 자산 파일 개체가 blob 컨테이너의 디지털 파일과 연결되지 않은 경우 Media Services 인코더 작업을 하지 못합니다.

**AssetFile** 인스턴스 및 실제 미디어 파일은 별개의 두 개체입니다. AssetFile 인스턴스는 미디어 파일에 대한 메타데이터를 포함하는 반면 미디어 파일은 실제 미디어 콘텐츠를 포함합니다.

Blob 컨테이너에 디지털 미디어 파일을 업로드하면 **MERGE** HTTP 요청을 사용하여 미디어 파일에 대한 정보로 AssetFile을 업데이트하게 됩니다(이 문서에 나와 있지 않음). 

**HTTP 요청**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
