---
title: 오프라인 Apple FairPlay로 HLS 콘텐츠 보호 - Azure | Microsoft Docs
description: 이 항목에서는 오프라인 모드에서 Azure Media Services를 사용하여 Apple FairPlay에서 HLS(HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화하는 방법과 개요를 설명합니다.
services: media-services
keywords: HLS, DRM, FairPlay 스트리밍(FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 31da745727d567e1b3b85b3508df368d78ad84a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557275"
---
# <a name="offline-fairplay-streaming-for-ios"></a>iOS용 오프라인 FairPlay 스트리밍 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [버전 3](../latest/offline-fairplay-for-ios.md)
> * [버전 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

Azure Media Services는 다음을 대상으로 하는 적절히 디자인된 [콘텐츠 보호 서비스](https://azure.microsoft.com/services/media-services/content-protection/) 집합을 제공합니다.

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 암호화

콘텐츠의 DRM(디지털 권한 관리)/AES(Advanced Encryption Standard) 암호화는 다양한 스트리밍 프로토콜에 대한 요청에 따라 동적으로 수행됩니다. DRM 라이선스/AES 암호 해독 키 배달 서비스 또한 Media Services에서 제공합니다.

다양한 스트리밍 프로토콜을 통한 온라인 스트리밍에 대한 콘텐츠를 보호하는 것 외에도, 보호된 콘텐츠에 대한 오프 라인 모드 또한 자주 요청되는 기능입니다. 다음 시나리오에 대해 오프라인 모드 지원이 필요합니다.

* 인터넷 연결을 사용할 수 없을 경우(예: 여행 중) 재생합니다.
* 일부 콘텐츠 공급자는 해당 국가 외에는 DRM 라이선스 배달을 허용하지 않을 수도 있습니다. 해외 여행 중 콘텐츠를 보고 싶은 경우 오프라인 다운로드가 필요합니다.
* 일부 국가에서는 인터넷 사용 및/또는 대역폭이 여전히 제한됩니다. 사용자가 만족스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 보고자 먼저 다운로드를 선택할 수도 있습니다. 이 경우, 일반적으로 문제는 네트워크 가용성이 아니라 제한된 네트워크 대역폭입니다. OTT(Over-the-top)/OVP(온라인 비디오 플랫폼) 공급자는 오프라인 모드 지원을 요청합니다.

이 문서에서는 iOS 10 이상을 실행하는 디바이스를 대상으로 하는 FairPlay 스트리밍(FPS) 오프라인 모드 지원에 대해 설명합니다. 이 기능은 watchOS, tvOS, 또는 macOS의 Safari와 같은 다른 Apple 플랫폼을 지원하지 않습니다.

## <a name="preliminary-steps"></a>준비 단계
iOS 10+ 디바이스에서 FairPlay에 대한 오프라인 DRM을 구현하기 전에 다음을 수행합니다.

* FairPlay에 대한 온라인 콘텐츠 보호를 숙지하도록 합니다. 자세한 내용은 다음 문서 및 샘플을 참조하세요.

    - [일반적으로 사용 가능한 Azure Media Services용 Apple FairPlay 스트리밍](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Microsoft PlayReady 또는 Apple FairPlay로 HLS 콘텐츠 보호](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [온라인 FPS 스트리밍에 대한 샘플](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Apple Developer Network에서 FPS SDK를 가져옵니다. FPS SDK에는 두 가지 구성 요소가 들어 있습니다.

    - FPS Server SDK에는 KSM(키 보안 모듈), 클라이언트 샘플, 사양 및 테스트 벡터 집합이 들어 있습니다.
    - FPS Deployment Pack은 D 함수 사양 및 FPS 인증서를 생성하는 방법에 관한 지침, 고객별 개인 키 및 애플리케이션 비밀 키를 포함합니다. Apple은 사용이 허가된 콘텐츠 공급자에게만 FPS Deployment Pack을 발급합니다.

## <a name="configuration-in-media-services"></a>Media Services의 구성
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)를 통한 FPS 오프라인 모드 구성의 경우, FPS 오프라인 모드를 구성하는 데 필요한 API를 제공하는 Media Services .NET SDK 버전 4.0.0.4 이상을 사용합니다.
온라인 모드 FPS 콘텐츠 보호를 구성하려면 작업 코드도 필요합니다. FPS에 대한 온라인 모드 콘텐츠 보호를 구성하는 코드를 가져온 후에 다음 두 가지 사항만 변경하면 됩니다.

## <a name="code-change-in-the-fairplay-configuration"></a>FairPlay 구성에서 코드 변경
첫 번째 변경은 오프라인 DRM 시나리오를 사용하도록 설정하는 경우 true에 해당하는 objDRMSettings.EnableOfflineMode라는 "오프라인 모드 사용" 부울을 정의하는 것입니다. 이 지표에 따라 FairPlay 구성을 다음과 같이 변경합니다.

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>자산 배달 정책 구성의 코드 변경
두 번째 변경은 세 번째 키를 Dictionary<AssetDeliveryPolicyConfigurationKey, string>에 추가하는 것입니다.
다음과 같이 AssetDeliveryPolicyConfigurationKey를 추가합니다.
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

이 단계 후에 FPS 자산 배달 정책의 <Dictionary_AssetDeliveryPolicyConfigurationKey> 문자열에 다음 세 가지 항목이 포함됩니다.

* 사용된 FPS KSM/키 서버 및 여러 자산에서 동일한 자산 배달 정책을 다시 사용하는지 여부 등의 요인에 따라 AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl 또는 AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

이제 Media Services 계정이 오프라인 FairPlay 라이선스를 배달하도록 구성되었습니다.

## <a name="sample-ios-player"></a>샘플 iOS 플레이어
FPS 오프라인 모드 지원은 iOS 10 이상에서만 제공됩니다. FPS Server SDK(버전 3.0 이상)에는 FPS 오프라인 모드에 대한 문서와 샘플이 포함되어 있습니다. 특히 FPS Server SDK(버전 3.0 이상)에는 오프라인 모드와 관련된 다음 두 항목이 들어 있습니다.

* 문서: "FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하는 오프라인 재생" Apple, 2016년 9월 14일 발행. FPS Server SDK 버전 4.0에서 이 문서는 주 FPS 문서에 병합되어 있습니다.
* 샘플 코드: \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\에 있는 FPS 오프라인 모드에 대한 HLSCatalog 샘플. HLSCatalog 샘플 앱에서 다음 코드 파일은 오프라인 모드 기능을 구현하는 데 사용됩니다.

    - AssetPersistenceManager.swift 코드 파일: AssetPersistenceManager는 다음 방법을 보여 주는 이 샘플의 기본 클래스입니다.

        - 다운로드를 시작 및 취소하고, 디바이스에서 기존 자산을 삭제하는 데 사용되는 API와 같은 HLS 스트림 다운로드를 관리합니다.
        - 다운로드 진행률을 모니터링합니다.
    - AssetListTableViewController.swift 및 AssetListTableViewCell.swift 코드 파일: AssetListTableViewController는 이 샘플의 주 인터페이스입니다. 이 인터페이스는 샘플이 재생, 다운로드, 삭제 또는 다운로드 취소에 사용할 수 있는 자산 목록을 제공합니다. 

이러한 단계는 실행 중인 iOS 플레이어를 설정하는 방법을 보여 줍니다. FPS Server SDK 버전 4.0.1의 HLSCatalog 샘플에서 시작한다고 가정하고 다음과 같이 코드를 변경합니다.

다음 코드를 사용하여 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift에서 `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` 메서드를 구현합니다. HLS URL에 "drmUr" 변수가 할당되도록 합니다.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift에서 `requestApplicationCertificate()` 메서드를 구현합니다. 이 구현은 디바이스에 인증서(공개 키만)를 포함 또는 웹에 인증서를 호스트하는지 여부에 달려 있습니다. 다음은 테스트 샘플에서 사용된 호스트된 애플리케이션 인증서를 사용합니다. "certUrl"을 애플리케이션 인증서 URL을 포함하는 변수로 사용합니다.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

최종 통합된 테스트인 경우 동영상 URL 및 애플리케이션 인증서 URL은 모두 "통합 테스트" 섹션에 제공됩니다.

HLSCatalog\Shared\Resources\Streams.plist에서 테스트 비디오 URL을 추가합니다. 콘텐츠 키 ID로, skd 프로토콜을 고유 값으로 포함하는 FairPlay 라이선스 취득 URL을 사용합니다.

![오프라인 FairPlay iOS 앱 스트림](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

자체 테스트 비디오 URL, FairPlay 라이선스 취득 URL 및 애플리케이션 인증서 URL을 설정한 경우 이러한 항목을 사용합니다. 또는 테스트 샘플이 포함된 다음 섹션을 계속 진행할 수 있습니다.

## <a name="integrated-test"></a>통합 테스트
Media Services의 세 가지 테스트 샘플은 다음 세 시나리오를 다룹니다.

* 보호된 FPS – 동영상, 오디오 및 대체 오디오 트랙 사용
* 보호된 FPS – 동영상 및 오디오 사용, 대체 오디오 트랙 없음
* 보호된 FPS – 동영상만 사용, 오디오 없음

이러한 샘플은 Azure 웹앱에 호스트되는 해당 애플리케이션 인증서를 사용하여 [이 데모 사이트](https://aka.ms/poc#22)에서 찾을 수 있습니다.
FPS Server SDK의 버전 3 또는 버전 4 샘플을 사용하여 마스터 재생 목록에 대체 오디오가 들어 있다면 오프라인 모드 동안에 오디오만을 재생합니다. 따라서 대체 오디오를 제거해야 합니다. 즉, 이전에 나열된 두 번째 및 세 번째 샘플은 온라인 및 오프라인 모드에서 작동합니다. 온라인 스트리밍이 제대로 작동하지만, 첫 번째로 나열된 샘플은 오프라인 모드 동안에는 오디오만 재생합니다.

## <a name="faq"></a>FAQ
다음 질문과 대답은 문제 해결에 도움이 됩니다.

- **왜 오프라인 모드에서는 오디오만 재생되고 비디오는 재생되지 않나요?** 이 동작은 의도적으로 샘플 앱의 것입니다. 대체 오디오 트랙이 존재할 때(HLS에 대한 경우임), 오프라인 모드 동안 iOS 10과 iOS 11은 모두 기본 설정에 따라 대체 오디오 트랙을 사용합니다. FPS 오프라인 모드에 대한 이 동작을 보완하려면 스트림에서 대체 오디오 트랙을 제거합니다. Media Services에서 이를 실행하기 위해 동적 매니페스트 필터 "audio-only=false"를 추가합니다. 즉, HLS URL은 .ism/manifest(format=m3u8-aapl,audio-only=false)로 끝납니다. 
- **audio-only=false를 추가한 후에도 왜 여전히 오프라인 모드에서 동영상 없이 오디오만 재생되나요?** CDN(콘텐츠 배달 네트워크) 캐시 키 디자인에 따라, 콘텐츠가 캐시될 수 있습니다. 캐시를 제거합니다.
- **FPS 오프라인 모드 또한 iOS 10 외에도 iOS 11에서 지원됩니까?**  예. FPS 오프라인 모드는 iOS 10과 iOS 11 모두에서 지원됩니다.
- **FPS Server SDK에서 “FairPlay 스트리밍 및 HTTP 라이브 스트리밍을 사용하여 오프라인 재생” 문서를 찾을 수 없는 이유는 무엇인가요?** FPS Server SDK 버전 4부터 이 문서는 “FairPlay Streaming Programming Guide”에 병합되었습니다.
- **마지막 매개변수는 FPS 오프라인 모드에 대한 다음 API에서 무엇을 의미합니까?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    이 API에 대한 설명서를 보려면 [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration 메서드](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)를 참조하세요. 이 매개 변수는 오프라인 임대 기간(시간 단위)을 나타냅니다.
- **iOS 디바이스에서 다운로드된/오프라인 파일 구조체는 무엇입니까?** iOS 디바이스에 다운로드된 파일 구조체는 다음 스크린샷과 같습니다. `_keys` 폴더에는 다운로드된 FPS 라이선스가 저징됩니다(각 라이선스 서비스 호스트당 하나의 저장소 파일). `.movpkg` 폴더에는 오디오 및 동영상 콘텐츠가 저장됩니다. 대시에 이어 숫자로 끝나는 이름의 첫 번째 폴더는 동영상 콘텐츠를 포함합니다. 숫자 값은 동영상 변환의 PeakBandwidth입니다. 대시에 이어 0으로 끝나는 이름의 두 번째 폴더는 오디오 콘텐츠를 포함합니다. "Data"라는 이름의 세 번째 폴더는 FPS 콘텐츠의 마스터 재생 목록을 포함합니다. 마지막으로, boot.xml은 `.movpkg` 폴더 내용에 대한 전체 설명을 제공합니다. 

![오프라인 FairPlay iOS 샘플 앱 파일 구조체](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

샘플 boot.xml 파일:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>요약
이 문서에는 FPS 오프라인 모드를 구현하는 데 사용할 수 있는 다음 단계 및 정보가 포함되어 있습니다.

* Media Services .NET API를 통한 Media Services 콘텐츠 보호 구성은 Media Services에서 동적 FairPlay 암호화 및 FairPlay 라이선스 배달을 구성합니다.
* FPS Server SDK의 샘플을 기준으로 하는 iOS 플레이어는 온라인 스트리밍 모드 또는 오프라인 모드로 FPS 콘텐츠를 재생할 수 있는 iOS 플레이어를 설정합니다.
* 샘플 FPS 비디오는 오프라인 모드 및 온라인 스트리밍을 테스트하는 데 사용됩니다.
* FPS 오프라인 모드에 대한 FAQ 질문과 대답
