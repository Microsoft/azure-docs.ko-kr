---
title: PlayReady 보호 콘텐츠의 오프라인 스트리밍 계정 구성 - Azure
description: 이 문서에서는 Windows 10에서 PlayReady 오프라인 스트리밍에 대한 Azure Media Services 계정을 구성하는 방법을 보여 줍니다.
services: media-services
keywords: DASH, DRM, Widevine 오프라인 모드, ExoPlayer, Android
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
ms.author: willzhan
ms.openlocfilehash: 974062b06c58ee23a001066a70a08675e2e94e48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638091"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Windows 10에서 PlayReady 오프라인 스트리밍  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [버전 3](../latest/offline-plaready-streaming-for-windows-10.md)
> * [버전 2](offline-playready-streaming-windows-10.md)

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

Azure Media Services는 DRM 보호 기능을 사용하여 오프라인 다운로드/재생을 지원합니다. 이 문서에서는 Windows 10/PlayReady 클라이언트에 대한 Azure Media Services의 오프라인 지원을 설명합니다. 다음 문서에서는 iOS/FairPlay 및 Android/Widevine 디바이스에 대한 오프라인 모드 지원에 대해 알아볼 수 있습니다.

- [iOS용 오프라인 FairPlay 스트리밍](media-services-protect-hls-with-offline-fairplay.md)
- [Android용 오프라인 Widevine 스트리밍](offline-widevine-for-android.md)

## <a name="overview"></a>개요

이 섹션에서는 오프라인 모드 재생에 관한 약간의 배경 지식을 제공합니다.

* 일부 국가에서는 인터넷 가용성 및/또는 대역폭이 여전히 제한됩니다. 사용자가 만족스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 보고자 먼저 다운로드를 선택할 수도 있습니다. 이 경우 대개 문제는 네트워크 가용성이 아니라 제한된 네트워크 대역폭입니다. OTT/OVP 공급자가 오프라인 모드 지원을 요청하고 있습니다.
* Netflix 2016 Q3 주주 컨퍼런스에서 공개된 바와 같이 Netflix CEO인 Reed Hastings는 콘텐츠 다운로드는 “자주 요청되는 기능”이고 “이 기능에 대해 열려 있습니다”라고 밝혔습니다.
* 일부 콘텐츠 공급자는 해당 국가의 국경 너머로 DRM 라이선스 배달을 허용하지 않을 수도 있습니다. 사용자가 해외 여행을 하면서도 콘텐츠를 보려는 경우 오프라인 다운로드가 필요합니다.
 
오프라인 모드 구현 시 직면하는 문제는 다음과 같습니다.

* MP4는 다양한 플레이어와 인코더 도구를 지원하지만 MP4 컨테이너와 DRM 사이에 바인딩이 되어 있지 않습니다.
* 장기적으로는, CENC 지원 CFF가 바람직합니다. 하지만 현재 에코시스템을 지원하는 도구/플레이어가 아직 없습니다. 솔루션이 필요합니다.
 
H264/AAC를 사용하는 부드러운 스트리밍([PIFF](https://go.microsoft.com/?linkid=9682897)) 파일 형식은 PlayReady(AES-128 CTR)와 바인딩되어 있습니다. 개별 부드러운 스트리밍 .ismv 파일(비디오에 오디오를 담은)은 그 자체가 fMP4이며 재생에 사용할 수 있습니다. 부드러운 스트리밍 콘텐츠가 PlayReady 암호화를 거칠 경우 각 .ismv 파일은 PlayReady 보호 조각난 MP4가 됩니다. 기본 비트 전송률을 사용하는 .ismv 파일을 선택하여 .mp4로 이름을 바꾸어 다운로드할 수 있습니다.

점진적 다운로드 시 PlayReady 보호 MP4를 호스트하는 데 두 가지 옵션이 있습니다.

* 하나는 이 MP4를 동일한 컨테이너/미디어 서비스 자산에 배치하여 점진적 다운로드 시 Azure Media Services 스트리밍 엔드포인트를 활용할 수 있습니다.
* 하나는 Azure Media Services를 바이패스하여 Azure Storage에서 바로 점진적 다운로드 시 SAS 로케이터를 사용할 수 있습니다.
 
두 가지 유형의 PlayReady 라이선스 제공을 사용할 수 있습니다.

* Azure Media Services의 PlayReady 라이선스 제공 서비스
* 어디에서든 호스팅되는 PlayReady 라이선스 서버

테스트 자산의 두 가지 세트는 다음과 같습니다. 첫 번째는 AMS의 PlayReady 라이선스 제공을 사용하는 것이며 두 번째는 Azure VM에 호스팅된 PlayReady 라이선스 서버를 사용하는 것입니다.

자산 #1:

* 점진적 다운로드 URL: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL(AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

자산 #2:

* 점진적 다운로드 URL: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL(온-프레미스): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

재생 테스트 시 Windows 10에서 Universal Windows Application을 사용했습니다. [Windows 10 유니버설 샘플](https://github.com/Microsoft/Windows-universal-samples)에는 [적응 스트리밍 샘플](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)을 호출하는 기본 플레이어 샘플이 있습니다. 우리가 해야 할 일은 코드를 추가하여 다운로드한 비디오를 선택하여 적응 스트리밍 원본 대신에 해당 비디오를 원본으로 사용하는 것입니다. 변경 사항은 단추 클릭 이벤트 처리기에 있습니다.

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

 ![PlayReady 보호 fMP4의 오프라인 모드 재생](./media/offline-playready/offline-playready1.jpg)

비디오는 PlayReady 보호를 받고 있으므로 스크린샷에 비디오를 포함할 수 없습니다.

요약하면 Azure Media Services에서 오프라인 모드를 구현했습니다.

* Azure Media Services 또는 기타 도구에서 콘텐츠 코드 변환 및 PlayReady 암호화를 수행할 수 있습니다.
* 점진적 다운로드 시 Azure Media Services 또는 Azure Storage에서 콘텐츠를 호스팅할 수 있습니다.
* Azure Media Services 또는 다른 위치에서PlayReady 라이선스 배달을 할 수 있습니다.
* 준비된 부드러운 스트리밍 콘텐츠는 DASH 또는 DRM으로 PlayReady를 사용하는 부드러운 스트리밍을 통해 온라인 스트리밍에서 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[하이브리드 DRM 시스템 디자인](hybrid-design-drm-sybsystem.md)
