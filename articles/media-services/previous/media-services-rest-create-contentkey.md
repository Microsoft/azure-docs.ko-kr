---
title: REST를 사용하여 콘텐츠 키 만들기 | Microsoft Docs
description: 자산에 대한 보안 액세스를 제공하는 콘텐츠 키를 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95e9322b-168e-4a9d-8d5d-d7c946103745
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 84fd4f0971c56d8cf2cdf138ba8ac8ea1a6e07eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711529"
---
# <a name="create-content-keys-with-rest"></a>REST를 사용하여 콘텐츠 키 만들기
> [!div class="op_single_selector"]
> * [REST (영문)](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services를 사용하면 암호화된 자산을 제공할 수 있습니다. **ContentKey**는 **자산**에 대한 보안 액세스를 제공합니다. 

새 자산을 만들 때(예: [파일 업로드](media-services-rest-upload-files.md) 전) **StorageEncrypted**, **CommonEncryptionProtected** 또는 **EnvelopeEncryptionProtected** 암호화 옵션을 지정할 수 있습니다. 

클라이언트에 자산을 제공할 때 다음 두 암호화 중 하나를 사용하여 [자산이 동적으로 암호화되도록 구성](media-services-rest-configure-asset-delivery-policy.md)할 수 있습니다. **DynamicEnvelopeEncryption** 또는 **DynamicCommonEncryption**.

암호화된 자산은 **ContentKey**와 연관되어야 합니다. 이 문서에서는 콘텐츠 키를 만드는 방법을 설명합니다.

다음은 암호화하려는 자산과 연결할 콘텐츠 키를 생성하기 위한 일반적인 단계입니다. 

1. 16바이트 AES 키(일반 및 봉투 암호화의 경우) 또는 32 바이트 AES 키(저장소 암호화의 경우)를 임의로 생성합니다. 
   
    이는 자산에 대한 콘텐츠 키입니다. 즉, 암호화하는 동안 해당 자산과 연결된 모든 파일이 동일한 콘텐츠 키를 사용해야 합니다. 
2. [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) 및 [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) 메서드를 호출하여 콘텐츠 키를 암호화하는 데 사용해야 하는 올바른 X.509 인증서를 가져옵니다.
3. X.509 인증서의 공개 키로 콘텐츠 키를 암호화합니다. 
   
   Media Services.NET SDK는 암호화 시 OAEP가 포함된 RSA를 사용합니다.  [EncryptSymmetricKeyData 함수](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)에서 예를 확인할 수 있습니다.
4. 키 식별자 및 콘텐츠 키를 사용하여 계산된 체크섬 값(PlayReady AES 키 체크섬 알고리즘에 기반)을 만듭니다. 자세한 내용은 [여기](https://www.microsoft.com/playready/documents/)에 있는 PlayReady 헤더 개체 문서의 "PlayReady AES 키 체크섬 알고리즘" 섹션을 참조하세요.
   
   다음.NET 예제에서는 키 식별자와 암호화되지 않은 콘텐츠 키의 GUID 부분을 사용하여 체크섬을 계산합니다.
   
        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
         {
 
             byte[] array = null;
             using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
             {
                 aesCryptoServiceProvider.Mode = CipherMode.ECB;
                 aesCryptoServiceProvider.Key = contentKey;
                 aesCryptoServiceProvider.Padding = PaddingMode.None;
                 ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
                 array = new byte[16];
                 cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
             }
             byte[] array2 = new byte[8];
             Array.Copy(array, array2, 8);
             return Convert.ToBase64String(array2);
         }
5. 이전 단계에서 받은**EncryptedContentKey**(base64 인코딩된 문자열로 변환), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** 및 **Checksum** 값을 사용하여 콘텐츠 키를 만듭니다.
6. $links 작업을 통해 **ContentKey** 엔터티와 **Asset** 엔터티를 연결합니다.

이 문서는 AES 키를 생성하고, 키를 암호화하고 체크섬을 계산하는 방법을 표시하지 않습니다. 

> [!NOTE]
> 
> Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a name="retrieve-the-protectionkeyid"></a>ProtectionKeyId 검색
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

## <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>ProtectionKeyId에 대한 Protectionkey 검색
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

## <a name="create-the-contentkey"></a>ContentKey 만들기
X.509 인증서를 검색한 다음 이 인증서의 공개 키를 사용하여 콘텐츠 키를 암호화한 후 **ContentKey** 엔터티를 만들고 해당 속성 값을 적절하게 설정합니다.

콘텐츠 키를 만들 때 설정해야 하는 값 중 하나가 이 유형입니다. 다음 값 중 하나를 선택합니다.

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }


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


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

