---
title: PlayReady로 보호 된 콘텐츠의 오프 라인 스트리밍을 위한 계정 구성-Azure
description: 이 문서에서는 Windows 10 오프 라인에서 PlayReady를 스트리밍하는 Azure Media Services 계정을 구성 하는 방법을 보여 줍니다.
services: media-services
keywords: 대시, DRM, Widevine 오프 라인 모드, Android
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
ms.openlocfilehash: 350b8d111652511627ddf67236f63248a5489015
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970451"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Windows 10 용 오프 라인 PlayReady 스트리밍  

> [!div class="op_single_selector" title1="사용 중인 Media Services의 버전을 선택 합니다."]
> * [3-as verzió](../latest/offline-plaready-streaming-for-windows-10.md)
> * [2-es verzió](offline-playready-streaming-windows-10.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). 또한 [v2에서 v3로 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md) 을 참조 하세요.

Azure Media Services DRM 보호를 사용 하 여 오프 라인 다운로드/재생을 지원 합니다. 이 문서에서는 Windows 10/PlayReady 클라이언트에 대 한 Azure Media Services의 오프 라인 지원에 대해 설명 합니다. 다음 문서에서 iOS/FairPlay 및 Android/Widevine 장치에 대 한 오프 라인 모드 지원에 대해 알아볼 수 있습니다.

- [Offline FairPlay-streamelés iOS-hez](media-services-protect-hls-with-offline-fairplay.md)
- [Android 용 오프 라인 Widevine 스트리밍](offline-widevine-for-android.md)

## <a name="overview"></a>Áttekintés

이 섹션에서는 오프 라인 모드 재생에 대 한 몇 가지 배경, 특히 다음을 제공 합니다.

* 일부 국가/지역에서는 인터넷 가용성 및/또는 대역폭이 여전히 제한 됩니다. 사용자는 만족 스러운 보기 환경을 위해 충분히 높은 해상도로 콘텐츠를 볼 수 있도록 먼저를 다운로드 하도록 선택할 수 있습니다. 이 경우에는 네트워크 가용성이 아니라 네트워크 대역폭이 제한 되기 때문에이 문제가 자주 발생 합니다. 이상에서 오프 라인 모드 지원을 요청 하 고 있습니다.
* Netflix 2016 Q3 shareholder 회의에 공개 된 대로 콘텐츠 다운로드는 "oft 요청 기능" 이며 "microsoft는 공개 되었습니다" 라고 생각 합니다.
* 일부 콘텐츠 공급자는 국가/지역 테두리를 넘어 DRM 라이선스 배달을 허용 하지 않을 수 있습니다. 사용자가 해외 이동 해야 하 고 콘텐츠를 계속 보려는 경우 오프 라인 다운로드가 필요 합니다.
 
오프 라인 모드를 구현 하는 문제는 다음과 같습니다.

* MP4는 여러 플레이어, 인코더 도구에서 지원 되지만 MP4 컨테이너와 DRM 사이에는 바인딩이 없습니다.
* 장기적으로 CENC로 CFF을 이동 하는 방법이 있습니다. 그러나 현재 도구/플레이어 지원 에코 시스템이 아직 없습니다. 지금은 솔루션이 필요 합니다.
 
개념: H264/AAC를 사용 하는 부드러운 스트리밍 ([Piff](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)) 파일 형식에는 PLAYREADY (AES-128 CTR)가 포함 된 바인딩이 있습니다. 개별 부드러운 스트리밍. ismv 파일 (오디오가 비디오에서 muxed 가정) 자체가 fMP4 재생에 사용할 수 있습니다. 부드러운 스트리밍 콘텐츠가 PlayReady 암호화를 통해 이동 하는 경우 각. ismv 파일은 PlayReady로 보호 되 고 조각난 MP4로 바뀝니다. 기본 비트 전송률이 포함 된. ismv 파일을 선택 하 고 다운로드를 위해 mp4로 이름을 바꿀 수 있습니다.

프로그레시브 다운로드를 위해 PlayReady로 보호 된 MP4를 호스트 하는 두 가지 옵션이 있습니다.

* 이 MP4을 동일한 컨테이너/미디어 서비스 자산에 배치 하 고 점진적 다운로드를 위해 Azure Media Services 스트리밍 엔드포인트을 활용할 수 있습니다.
* Azure Media Services을 무시 하 고 Azure Storage에서 직접 점진적 다운로드를 위해 SAS 로케이터를 사용할 수 있습니다.
 
다음 두 가지 유형의 PlayReady 라이선스 배달을 사용할 수 있습니다.

* Azure Media Services의 PlayReady 라이선스 배달 서비스
* 어디에서 나 PlayReady 라이선스 서버를 호스트 합니다.

다음은 Azure VM에 호스트 된 PlayReady 라이선스 서버를 사용 하는 두 번째는 AMS에서 PlayReady 라이선스 배달을 사용 하는 첫 번째 테스트 자산 집합입니다.

자산 #1:

* 프로그레시브 다운로드 URL: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

자산 #2:

* 프로그레시브 다운로드 URL: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (온-프레미스): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

재생 테스트의 경우 Windows 10에서 유니버설 Windows 응용 프로그램을 사용 했습니다. [Windows 10 유니버설 샘플](https://github.com/Microsoft/Windows-universal-samples)에는 [적응 스트리밍 샘플](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)이라는 기본적인 플레이어 샘플이 있습니다. 다운로드 한 비디오를 선택 하 고 원본으로 사용 하는 코드를 추가 하 여 적응 스트리밍 원본 대신 사용 하기만 하면 됩니다. 단추 클릭 이벤트 처리기에 변경 내용이 있습니다.

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

 ![PlayReady로 보호 된 fMP4의 오프 라인 모드 재생](./media/offline-playready/offline-playready1.jpg)

비디오는 PlayReady 보호 상태 이므로 스크린샷은 비디오를 포함할 수 없습니다.

요약 하자면, Azure Media Services에 대 한 오프 라인 모드를 달성 했습니다.

* Azure Media Services 또는 다른 도구에서 콘텐츠 트랜스 코딩 및 PlayReady 암호화를 수행할 수 있습니다.
* 콘텐츠는 점진적 다운로드를 위해 Azure Media Services 또는 Azure Storage에서 호스팅될 수 있습니다.
* PlayReady 라이선스 배달은 Azure Media Services 또는 다른 위치에서 받을 수 있습니다.
* 준비 된 부드러운 스트리밍 콘텐츠는 계속 해 서 대시를 통한 온라인 스트리밍 또는 PlayReady로 PlayReady를 통해 사용할 수 있습니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="next-steps"></a>Következő lépések

[Hibrid DRM-rendszer terve](hybrid-design-drm-sybsystem.md)
