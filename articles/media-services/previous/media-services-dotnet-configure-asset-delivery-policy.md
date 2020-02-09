---
title: .NET SDK를 사용 하 여 자산 배달 정책 구성 | Microsoft Docs
description: 이 항목에서는 Azure Media Services .NET SDK를 사용 하 여 다양 한 자산 배달 정책을 구성 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974515"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>.NET SDK를 사용 하 여 자산 배달 정책 구성
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Áttekintés
암호화 된 자산을 배달 하려는 경우 Media Services 콘텐츠 배달 워크플로의 단계 중 하나는 자산에 대 한 배달 정책을 구성 하는 것입니다. 자산 배달 정책은 사용자의 자산 배달 방법 (예: MPEG 대시, HLS, 부드러운 스트리밍 또는 모두)을 동적으로 암호화할지 여부를 지정 하 여 자산 배달 방법을 Media Services에 알려 줍니다. 자산 및 방법 (봉투 (envelope) 또는 일반 암호화).

이 문서에서는 자산 배달 정책을 만들고 구성 하는 이유와 방법을 설명 합니다.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett**, **Leállítva** állapotú streamvégpontot a fiókhoz. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 
>
>또한 동적 패키징 및 동적 암호화를 사용 하려면 자산이 적응 비트 전송률 Mp4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함 해야 합니다.

동일한 자산에 다른 정책을 적용할 수 있습니다. 예를 들어 부드러운 스트리밍 및 AES 봉투 (Envelope) 암호화에 PlayReady 암호화를 적용 하 여 MPEG 대시와 HLS를 사용할 수 있습니다. A továbbítási szabályzatban meg nem határozott protokollok streameléshez való használatát a rendszer nem engedélyezi (ilyen lehet például, ha csupán egyetlen szabályzatot állít be, amely kizárólag a HLS-protokoll használatát tartalmazza). Kivételt jelent, ha egyáltalán nem állít be objektumtovábbítási szabályzatot. Ebben az esetben a rendszer az összes protokollt engedélyezi.

저장소 암호화 된 자산을 배달 하려면 자산의 배달 정책을 구성 해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버에서 저장소 암호화를 제거 하 고 지정 된 배달 정책을 사용 하 여 콘텐츠를 스트리밍합니다. 예를 들어 AES(Advanced Encryption Standard) (AES) 봉투 (envelope) 암호화 키로 암호화 된 자산을 배달 하려면 정책 유형을 **DynamicEnvelopeEncryption**로 설정 합니다. Clear에서 저장소 암호화를 제거 하 고 자산을 스트리밍하려면 정책 유형을 **Nodynamicencryption**으로 설정 합니다. 이러한 정책 유형을 구성 하는 방법을 보여 주는 예제는 다음과 같습니다.

자산 배달 정책을 구성 하는 방법에 따라 부드러운 스트리밍, HLS 및 MPEG 대시 등의 스트리밍 프로토콜을 동적으로 패키지 하 고, 암호화 하 고 스트리밍할 수 있습니다.

다음 목록에서는 부드러운, HLS 및 대시를 스트리밍하는 데 사용 하는 형식을 보여 줍니다.

부드러운 스트리밍:

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

HLS

{스트리밍 엔드포인트 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = m3u8-aapl-v3-aapl)

MPEG DASH

{스트리밍 엔드포인트 이름-media services 계정 이름}. windowsazure.mediaservices/{locator ID}/{filename}.ism/Manifest (format = mpd)

## <a name="considerations"></a>Megfontolandó szempontok
* Asset배달 Ypolicy를 삭제 하기 전에 해당 자산과 연결 된 모든 스트리밍 로케이터를 삭제 해야 합니다. 새 Asset배달 Ypolicy를 사용 하 여 나중에 원하는 경우 새 스트리밍 로케이터를 만들 수 있습니다.
* 자산 배달 정책이 설정 되지 않은 경우 저장소 암호화 된 자산에 스트리밍 로케이터를 만들 수 없습니다.  자산이 저장소로 암호화 되지 않은 경우 시스템에서 로케이터를 만들고 자산 배달 정책 없이 clear에서 자산을 스트리밍할 수 있습니다.
* 단일 자산과 연결 된 자산 배달 정책이 여러 개 있을 수 있지만, 지정 된 AssetDeliveryProtocol를 처리 하는 한 가지 방법만 지정할 수 있습니다.  AssetDeliveryProtocol. SmoothStreaming 프로토콜을 지정 하는 두 배달 정책을 연결 하려고 하면 클라이언트에서 부드러운 스트리밍 요청을 만들 때 시스템에서 적용 하려는 항목을 알지 못하기 때문에 오류가 발생할 수 있습니다.
* 기존 스트리밍 로케이터를 사용 하는 자산이 있는 경우 자산에 새 정책을 연결할 수 없습니다. 자산에서 기존 정책의 연결을 해제 하거나 자산과 연결 된 배달 정책을 업데이트할 수 있습니다.  먼저 스트리밍 로케이터를 제거 하 고 정책을 조정한 다음 스트리밍 로케이터를 다시 만들어야 합니다.  스트리밍 로케이터를 다시 만들 때 동일한 locatorId를 사용할 수 있지만 원본 또는 다운스트림 CDN에서 콘텐츠를 캐시할 수 있으므로에서 클라이언트에 문제가 발생 하지 않도록 해야 합니다.

## <a name="clear-asset-delivery-policy"></a>자산 배달 정책 지우기

다음 **ConfigureClearAssetDeliveryPolicy** 메서드는 동적 암호화를 적용 하지 않고 MPEG 대시, HLS 및 부드러운 스트리밍 프로토콜 중 하나에서 스트림을 배달 하도록 지정 합니다. 저장소 암호화 된 자산에이 정책을 적용 하는 것이 좋습니다.

Asset배달 Ypolicy를 만들 때 지정할 수 있는 값에 대 한 자세한 내용은 [Asset배달 ypolicy를 정의할 때 사용 되는 형식](#types) 섹션을 참조 하세요.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption 자산 배달 정책

다음 **CreateassetDynamicCommonEncryption ypolicy** 메서드는 부드러운 스트리밍 프로토콜에 동적 일반 암호화 ()를 적용 하도록 구성 된 **asset배달 ypolicy** 를 만듭니다 (다른 프로토콜은 스트리밍에서 차단 됨). 메서드는 두 개의 매개 변수, 즉 **asset** (배달 정책을 적용 하려는 자산) 및 **IContentKey** ( **CommonEncryption** 형식의 콘텐츠 키)를 사용 합니다. 자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md#common_contentkey)를 참조 하세요.

Asset배달 Ypolicy를 만들 때 지정할 수 있는 값에 대 한 자세한 내용은 [Asset배달 ypolicy를 정의할 때 사용 되는 형식](#types) 섹션을 참조 하세요.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services를 사용 하 여 Widevine 암호화를 추가할 수도 있습니다. 다음 예제에서는 PlayReady 및 Widevine를 자산 배달 정책에 추가 하는 방법을 보여 줍니다.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Widevine 사용 하 여 암호화 하는 경우 대시로만 제공할 수 있습니다. 자산 배달 프로토콜에서 대시를 지정 해야 합니다.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 자산 배달 정책
다음 **CreateassetDynamicEnvelopeEncryption ypolicy** 메서드는 부드러운 스트리밍, HLS 및 대시 프로토콜에 대해 동적 봉투 (envelope) 암호화 ()를 적용 하도록 구성 된 **asset배달 ypolicy** 를 만듭니다 (일부 프로토콜을 지정 하지 않기로 결정 한 경우 스트리밍에서 차단 됨). 메서드는 두 개의 매개 변수, 즉 **asset** (배달 정책을 적용 하려는 자산) 및 **IContentKey** ( **EnvelopeEncryption** 형식의 콘텐츠 키)를 사용 합니다. 자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md#envelope_contentkey)를 참조 하세요.

Asset배달 Ypolicy를 만들 때 지정할 수 있는 값에 대 한 자세한 내용은 [Asset배달 ypolicy를 정의할 때 사용 되는 형식](#types) 섹션을 참조 하세요.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>Asset배달 Ypolicy를 정의할 때 사용 되는 형식

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

다음 열거형은 자산 배달 프로토콜에 대해 설정할 수 있는 값을 설명 합니다.

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
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

다음 열거형은 자산 배달 정책 유형에 대해 설정할 수 있는 값을 설명 합니다.  
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
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

다음 열거형은 클라이언트에 대 한 콘텐츠 키의 배달 방법을 구성 하는 데 사용할 수 있는 값을 설명 합니다.
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
### <a id="AssetDeliveryPolicyConfigurationKey"></a>Dictionary<assetdeliverypolicyconfigurationkey

다음 열거형은 자산 배달 정책에 대 한 특정 구성을 가져오는 데 사용 되는 키를 구성 하기 위해 설정할 수 있는 값을 설명 합니다.
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

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

