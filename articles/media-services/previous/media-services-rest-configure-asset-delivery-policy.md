---
title: Media Services REST API를 사용하여 자산 배달 정책 구성 | Microsoft 문서
description: 이 항목에서는 Media Services REST API를 사용하여 여러 자산 배달 정책을 구성하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 49fc67bcd45d0c364f47276a9b47676a65f6d837
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268675"
---
# <a name="configuring-asset-delivery-policies"></a>자산 배달 정책 구성

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

동적으로 암호화된 자산을 배달하려는 경우 Media Services 콘텐츠 배달 워크플로의 단계 중 하나는 자산에 대한 배달 정책을 구성하는 것입니다. 자산 배달 정책은 어떤 스트리밍 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두)로 사용자의 자산을 동적으로 패키지할 지와 같은 사용자가 원하는 자산 배달 방법과 사용자의 자산을 동적으로 암호화할 지 여부 및 방법(봉투 또는 일반 암호화)를 Media Services에 알려줍니다.

이 항목에서는 자산 배달 정책을 만들고 구성하는 이유와 방법을 설명합니다.

> [!NOTE]
> AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 
>
> 또한 동적 패키징 및 동적 암호화를 사용하려면 자산이 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다.

동일한 자산에 다른 정책을 적용할 수 있습니다. 예를 들어, 부드러운 스트리밍에 PlayReady 암호화, MPEG DASH 및 HLS에 AES 봉투(envelope) 암호화를 적용할 수 있습니다. 배달 정책에 정의되지 않은 모든 프로토콜(예: HLS만 프로토콜로 지정하는 단일 정책)은 스트리밍에서 차단됩니다. 정의한 자산 배달 정책이 없는 경우는 예외입니다. 이렇게 하면 모든 프로토콜이 허용됩니다.

스토리지에서 암호화된 자산을 배달하려는 경우 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버가 스토리지 암호화를 제거하고 지정된 배달 정책을 사용하여 콘텐츠를 스트리밍합니다. 예를 들어 표준 AES 봉투 암호화 키로 암호화된 자산을 배달하려면 정책 유형을 **DynamicEnvelopeEncryption**으로 설정해야 합니다. 스토리지 암호화를 제거하고 암호화되지 않은 자산을 스트리밍하려면 정책 유형을 **NoDynamicEncryption**으로 설정하세요. 이러한 정책 유형을 구성 하는 방법을 보여주는 예제입니다.

사용자가 자산 배달 정책을 구성하는 방법에 따라 다음 스트리밍 프로토콜을 동적으로 패키지하고, 동적으로 암호화하고 스트림할 수 있습니다(부드러운 스트리밍, HLS 및 MPEG DASH 스트림).

다음 목록에서는 부드러운 스트리밍, HLS, DASH 등의 프로토콜을 스트리밍할 때 사용하는 형식을 보여 줍니다.

부드러운 스트리밍:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


자산을 게시하고 스트리밍 URL을 작성하는 방법은 [스트리밍 URL 작성](media-services-deliver-streaming-content.md)을 참조하세요.

## <a name="considerations"></a>고려 사항
* 자산에 대한 주문형(스트리밍) 로케이터가 있는 동안 해당 자산과 연결된 AssetDeliveryPolicy를 삭제할 수 없습니다. 정책을 삭제하기 전에 자산에서 정책을 제거하는 것이 좋습니다.
* 자산 배달 정책이 설정되지 않은 경우 암호화된 스토리지 자산에 스트리밍 로케이터를 만들 수 없습니다.  자산이 암호화된 스토리지가 아닌 경우 시스템에서 로케이터를 만들고 자산 배달 정책 없이 일반 텍스트인 자산을 스트리밍할 수 있습니다.
* 단일 자산과 여러 자산 배달 정책을 연결하여 사용할 수 있지만 지정된 AssetDeliveryProtocol을 처리하는 방법은 하나만 지정할 수 있습니다.  즉, AssetDeliveryProtocol.SmoothStreaming 프로토콜을 지정하는 두 가지 배달 정책을 연결하려는 경우 클라이언트가 부드러운 스트리밍을 요청할 때 시스템이 어떤 정책을 적용할지 모르기 때문에 오류가 발생합니다.
* 기존 스트리밍 로케이터를 사용하는 자산이 있는 경우 해당 자산에 새 정책을 연결, 자산에서 기존 정책의 연결 해제 또는 자산과 연결된 배달 정책을 업데이트할 수 없습니다.  먼저 스트리밍 로케이터를 제거하고, 정책을 조정한 다음, 스트리밍 로케이터를 다시 만들어야 합니다.  스트리밍 로케이터를 다시 만들 때 동일한 locatorId를 사용할 수 있지만 원본 또는 다운스트림 CDN이 콘텐츠를 캐시할 수 있으므로 클라이언트에 문제가 발생하지 않는지 확인해야 합니다.

> [!NOTE]
> 
> Media Services에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

## <a name="connect-to-media-services"></a>Media Services에 연결

AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

## <a name="clear-asset-delivery-policy"></a>자산 배달 정책 지우기
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>자산 배달 정책 만들기
다음 HTTP 요청은 동적 암호화를 적용하지 않고 MPEG DASH, HLS 및 부드러운 스트리밍 프로토콜 중 하나에서 스트림을 배달하도록 지정하는 자산 배달 정책을 만듭니다. 

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy를 정의할 때 사용되는 형식](#types) 섹션을 참조하세요.   

요청:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

응답:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>자산을 자산 배달 정책과 연결
다음 HTTP 요청은 지정된 자산을 자산 배달 정책에 연결합니다.

요청:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

응답:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 자산 배달 정책
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>EnvelopeEncryption 형식의 콘텐츠 키를 만들고 자산에 연결
DynamicEnvelopeEncryption 배달 정책을 지정할 때 EnvelopeEncryption 형식의 콘텐츠 키에 자산을 연결해야 합니다. 자세한 내용은 [콘텐츠 키 만들기](media-services-rest-create-contentkey.md)를 참조하세요.

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>배달 URL 가져오기
이전 단계에서 만든 콘텐츠 키의 지정된 배달 방법에 대한 배달 URL을 가져옵니다. 클라이언트는 보호된 콘텐츠를 재생하기 위해 AES 키 또는 PlayReady 라이선스를 요청하여 반환된 URL을 사용합니다.

HTTP 요청의 본문을 가져오려면 URL의 유형을 지정 합니다. PlayReady 사용하여 콘텐츠를 보호하는 경우 keyDeliveryType에 1을 사용({"keyDeliveryType":1})하여 Media Services PlayReady 라이선스 취득 URL을 요청합니다. 봉투 암호화를 사용하여 콘텐츠를 보호하는 경우 keyDeliveryType에 대해 2를 지정({"keyDeliveryType":2})하여 키 획득 URL 요청합니다.

요청:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

응답:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>자산 배달 정책 만들기
다음 HTTP 요청은 **HLS** 프로토콜에 동적 봉투 암호화(**DynamicEnvelopeEncryption**)를 적용하도록 구성된 **AssetDeliveryPolicy**를 만듭니다. 이 예제에서는 다른 프로토콜이 스트리밍에서 차단됩니다. 

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy를 정의할 때 사용되는 형식](#types) 섹션을 참조하세요.   

요청:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


응답:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>자산을 자산 배달 정책과 연결
[자산을 자산 배달 정책과 연결](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 자산 배달 정책
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>CommonEncryption 형식의 콘텐츠 키를 만들고 자산에 연결
DynamicCommonEncryption 배달 정책을 지정할 때 CommonEncryption 형식의 콘텐츠 키에 자산을 연결해야 합니다. 자세한 내용은 [콘텐츠 키 만들기](media-services-rest-create-contentkey.md)를 참조하세요.

### <a name="get-delivery-url"></a>배달 URL 가져오기
이전 단계에서 만든 콘텐츠 키의 PlayReady 배달 방법에 대한 배달 URL을 가져옵니다. 클라이언트는 보호된 콘텐츠를 재생하기 위해 PlayReady 라이선스를 요청하여 반환된 URL을 사용합니다. 자세한 내용은 [배달 URL 가져오기](#get_delivery_url)를 참조하세요.

### <a name="create-asset-delivery-policy"></a>자산 배달 정책 만들기
다음 HTTP 요청은 **부드러운 스트리밍** 프로토콜에 동적 일반 암호화(**DynamicCommonEncryption**)를 적용하도록 구성된 **AssetDeliveryPolicy**를 만듭니다. 이 예제에서는 다른 프로토콜이 스트리밍에서 차단됩니다. 

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy를 정의할 때 사용되는 형식](#types) 섹션을 참조하세요.   

요청:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Widevine DRM을 사용하여 콘텐츠를 보호하려는 경우 값 7인 WidevineLicenseAcquisitionUrl을 사용하도록 AssetDeliveryConfiguration 값을 업데이트하고 라이선스 배달 서비스의 URL을 지정합니다. AMS 파트너([Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/))를 사용하여 Widevine 라이선스를 배달할 수 있습니다.

예를 들면 다음과 같습니다. 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> Widevine을 사용하여 암호화하는 경우 DASH를 통해서만 배달할 수 있습니다. 자산 배달 프로토콜에서 DASH(2)를 지정해야 합니다.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>자산을 자산 배달 정책과 연결
[자산을 자산 배달 정책과 연결](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>AssetDeliveryPolicy를 정의할 때 사용되는 형식

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

다음 열거형은 자산 배달 프로토콜에 대해 설정할 수 있는 값을 설명합니다.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

다음 열거형은 자산 배달 정책 유형에 대해 설정할 수 있는 값을 설명합니다.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

다음 열거형은 클라이언트로의 콘텐츠 키 배달 방법을 구성하는 데 사용할 수 있는 값을 설명합니다.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

다음 열거형은 자산 배달 정책에 대한 특정 구성을 가져오는 데 사용되는 키를 구성하기 위해 설정할 수 있는 값을 설명합니다.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

