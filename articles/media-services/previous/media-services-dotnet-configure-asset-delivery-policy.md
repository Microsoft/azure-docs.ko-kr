---
title: .NET SDK를 사용하여 자산 배달 정책 구성 | Microsoft 문서
description: 이 항목에서는 Azure Media Services.NET SDK를 사용하여 여러 자산 배달 정책을 구성하는 방법을 설명합니다.
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
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465612"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>.NET SDK를 사용하여 자산 배포 정책 구성
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>개요
암호화된 자산을 배달하려는 경우 Media Services 콘텐츠 배달 워크플로의 단계 중 하나는 자산에 대한 배달 정책을 구성하는 것입니다. 자산 배달 정책은 어떤 스트리밍 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두)로 사용자의 자산을 동적으로 패키지할 지와 같은 사용자가 원하는 자산 배달 방법과 사용자의 자산을 동적으로 암호화할 지 여부 및 방법(봉투 또는 일반 암호화)를 Media Services에 알려줍니다.

이 문서에서는 자산 배달 정책을 만들고 구성하는 이유와 방법을 설명합니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 
>
>또한 동적 패키징 및 동적 암호화를 사용하려면 자산이 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다.

동일한 자산에 다른 정책을 적용할 수 있습니다. 예를 들어, 부드러운 스트리밍에 PlayReady 암호화, MPEG DASH 및 HLS에 AES 봉투(envelope) 암호화를 적용할 수 있습니다. 배달 정책에 정의되지 않은 모든 프로토콜(예: HLS만 프로토콜로 지정하는 단일 정책)은 스트리밍에서 차단됩니다. 자산 배달 정책이 전혀 정의되어 있지 않은 경우는 예외입니다. 이렇게 하면 모든 프로토콜이 허용됩니다.

저장소에서 암호화된 자산을 배달하려는 경우 자산의 배달 정책을 구성해야 합니다. 자산을 스트리밍하기 전에 스트리밍 서버가 저장소 암호화를 제거하고 지정된 배달 정책을 사용하여 콘텐츠를 스트리밍합니다. 예를 들어 표준 AES 봉투 암호화 키로 암호화된 자산을 배달하려면 정책 유형을 **DynamicEnvelopeEncryption**으로 설정해야 합니다. 저장소 암호화를 제거하고 암호화되지 않은 자산을 스트리밍하려면 정책 유형을 **NoDynamicEncryption**으로 설정하세요. 이러한 정책 유형을 구성 하는 방법을 보여주는 예제입니다.

사용자가 자산 전송 정책을 구성하는 방법에 따라 다음 스트리밍 프로토콜을 동적으로 패키지하고, 암호화하고 스트림할 수 있습니다. 부드러운 스트리밍, HLS 및 MPEG DASH.

다음 목록에서는 부드러운 스트리밍, HLS 및 DASH 등의 프로토콜을 스트리밍할 때 사용하는 형식을 보여 줍니다.

부드러운 스트리밍:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>고려 사항
* AssetDeliveryPolicy를 삭제하기 전에 자산과 연결된 모든 스트리밍 로케이터를 삭제해야 합니다. 나중에 원하는 경우 새 AssetDeliveryPolicy를 사용하여 스트리밍 로케이터를 새로 만들 수 있습니다.
* 자산 배달 정책이 설정되지 않은 경우 암호화된 저장소 자산에 스트리밍 로케이터를 만들 수 없습니다.  자산이 암호화된 저장소가 아닌 경우 시스템에서 로케이터를 만들고 자산 배달 정책 없이 일반 텍스트인 자산을 스트리밍할 수 있습니다.
* 단일 자산과 여러 자산 배달 정책을 연결하여 사용할 수 있지만 지정된 AssetDeliveryProtocol을 처리하는 방법은 하나만 지정할 수 있습니다.  즉, AssetDeliveryProtocol.SmoothStreaming 프로토콜을 지정하는 두 가지 배달 정책을 연결하려는 경우 클라이언트가 부드러운 스트리밍을 요청할 때 시스템이 어떤 정책을 적용할지 모르기 때문에 오류가 발생합니다.
* 기존 스트리밍 로케이터를 사용하는 자산이 있는 경우 해당 자산에 새 정책을 연결할 수 없습니다. 자산에서 기존 정책의 연결을 해제하거나 자산과 연결된 배달 정책을 업데이트할 수 있습니다.  먼저 스트리밍 로케이터를 제거하고, 정책을 조정한 다음, 스트리밍 로케이터를 다시 만들어야 합니다.  스트리밍 로케이터를 다시 만들 때 동일한 locatorId를 사용할 수 있지만 원본 또는 다운스트림 CDN이 콘텐츠를 캐시할 수 있으므로 클라이언트에 문제가 발생하지 않는지 확인해야 합니다.

## <a name="clear-asset-delivery-policy"></a>자산 배달 정책 지우기

다음 **ConfigureClearAssetDeliveryPolicy** 메서드는 동적 암호화를 적용하지 않고 다음 프로토콜 중 하나에서 스트림을 전송하도록 지정합니다.  MPEG DASH, HLS 및 부드러운 스트리밍 프로토콜 중 하나에서 스트림을 배달하도록 지정하는 자산 배달 정책을 만듭니다. 이 정책을 저장소에서 암호화된 자산에 적용할 수 있습니다.

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy를 정의할 때 사용되는 형식](#types) 섹션을 참조하세요.

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

다음 **CreateAssetDeliveryPolicy** 메서드는 부드러운 스트리밍 프로토콜에 동적 일반 암호화(**DynamicCommonEncryption**)를 적용하도록 구성된 **AssetDeliveryPolicy**를 만듭니다(스트리밍에서 다른 프로토콜은 차단됨). 이 메서드는 다음과 같은 두 개의 매개 변수를 사용합니다. **Asset**(전송 정책을 적용하려는 자산) 및 **IContentKey**(**CommonEncryption** 유형의 콘텐츠 키, 자세한 내용은 다음 참조: [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md#common_contentkey)).

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy를 정의할 때 사용되는 형식](#types) 섹션을 참조하세요.

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

Azure Media Services를 사용하면 Widevine 암호화를 추가할 수 있습니다. 다음 예제에서는 PlayReady 및 Widevine이 자산 배달 정책에 추가되는 과정을 보여 줍니다.

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
> Widevine을 사용하여 암호화하는 경우 DASH를 통해서만 배달할 수 있습니다. 자산 배달 프로토콜에서 DASH를 지정해야 합니다.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption 자산 배달 정책
다음 **CreateAssetDeliveryPolicy** 메서드는 부드러운 스트리밍, HLS 및 DASH 프로토콜에 동적 봉투 암호화(**DynamicEnvelopeEncryption**)를 적용하도록 구성된 **AssetDeliveryPolicy**를 만듭니다(일부 프로토콜은 지정되지 않으면 스트리밍에서 차단됨). 이 메서드는 다음과 같은 두 개의 매개 변수를 사용합니다. **Asset**(전송 정책을 적용하려는 자산) 및 **IContentKey**(**EnvelopeEncryption** 유형의 콘텐츠 키, 자세한 내용은 다음 참조: [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

AssetDeliveryPolicy을 만들 때 사용자가 지정하는 값에 대한 자세한 정보는 [AssetDeliveryPolicy를 정의할 때 사용되는 형식](#types) 섹션을 참조하세요.   

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

## <a id="types"></a>AssetDeliveryPolicy를 정의할 때 사용되는 형식

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

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
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

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
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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
## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

