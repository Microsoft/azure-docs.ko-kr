---
title: Azure Portal을 사용한 주문형 비디오 제공 시작 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용한 Azure Media Services 애플리케이션으로 기본 주문형 비디오 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 5ad846a3d8a304e01425407cb2bf5297c36615e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463196"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>Azure Portal을 사용한 주문형 콘텐츠 제공 시작

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

이 자습서에서는 Azure Portal을 사용한 Azure Media Services 애플리케이션으로 기본 주문형 비디오 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다.

## <a name="prerequisites"></a>필수 조건
자습서를 완료하는 데 필요한 항목은 다음과 같습니다.

* Azure 계정. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
* Media Services 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.

이 자습서에는 다음 작업이 포함되어 있습니다.

1. 스트리밍 엔드포인트를 시작합니다.
2. 비디오 파일을 업로드합니다.
3. 원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.
4. 자산을 게시하고, 스트리밍 기능을 사용하고, URL을 점진적으로 다운로드합니다.  
5. 콘텐츠를 재생합니다.

## <a name="start-the-streaming-endpoint"></a>스트리밍 엔드포인트 시작

Azure Media Services 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 통해 비디오를 제공하는 것입니다. Media Services는 동적 패키징을 제공합니다. 동적 패키징을 통해 적응 비트 전송률 MP4 인코딩 콘텐츠를 Media Services에서 지원하는 Just-In-Time 스트리밍 형식으로 제공할 수 있습니다. 예로 Apple HLS(HTTP 라이브 스트리밍), Microsoft 부드러운 스트리밍, HTTP를 통한 동적 적응 스트리밍(DASH, MPEG-DASH라고도 함)이 있습니다. Media Services 적응 비트 전송률 스트리밍을 사용하여 이러한 각 스트리밍 형식의 미리 패키징된 버전을 저장하지 않고 비디오를 제공할 수 있습니다.

> [!NOTE]
> Media Services 계정이 만들어질 때 기본 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 

스트리밍 엔드포인트를 시작하려면

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **설정** > **스트리밍 엔드포인트**를 선택합니다. 
3. 기본 스트리밍 엔드포인트를 선택합니다. **기본 스트리밍 엔드포인트 세부 정보** 창이 나타납니다.
4. **시작** 아이콘을 선택합니다.
5. **저장** 단추를 선택합니다.

## <a name="upload-files"></a>파일 업로드
Media Services를 사용하여 비디오를 스트림하려면 원본 비디오를 업로드하고 다중 비트 전송률로 인코딩하고 결과를 게시합니다. 첫 번째 단계는 이 섹션에서 다룹니다. 

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. 계정 배포 진행 상태를 보려면 **설정** > **자산**을 참조하세요. 그런 다음 **업로드** 단추를 선택합니다.
   
    ![파일 업로드](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    **비디오 자산 업로드** 창이 나타납니다.
   
   > [!NOTE]
   > Media Services는 비디오를 업로드하기 위한 파일 크기를 제한하지 않습니다.
   > 
   > 
3. 컴퓨터에서 업로드하려는 비디오로 이동합니다. 비디오를 선택한 다음 **확인**을 선택합니다.  
   
    업로드가 시작됩니다. 파일 이름 아래에 진행 상태가 표시됩니다.  

업로드가 완료되면 새 자산이 **자산** 창에 나열됩니다. 

## <a name="encode-assets"></a>자산 인코딩
동적 패키징을 활용하려면 원본 파일을 다중 비트 전송률 MP4 파일 집합으로 인코딩해야 합니다. 인코딩 단계는 이 섹션에 나와 있습니다.

### <a name="encode-assets-in-the-portal"></a>포털에서 자산 인코딩
Azure Portal에서 Media Encoder Standard를 사용하여 콘텐츠를 인코딩하려면

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. 계정 배포 진행 상태를 보려면 **설정** > **자산**을 참조하세요. 인코딩하려는 자산을 선택합니다.
3. **인코딩** 단추를 선택합니다.
4. **자산 인코딩** 창에서 **Media Encoder Standard** 프로세서 및 사전 설정을 선택합니다. 사전 설정에 대한 자세한 내용은 [비트 전송률 사다리 자동 생성](media-services-autogen-bitrate-ladder-with-mes.md) 및 [Media Encoder Standard에 대한 작업 미리 설정](media-services-mes-presets-overview.md)을 참조하세요. 입력 비디오에 가장 적합한 사전 설정을 선택하는 것이 중요합니다. 예를 들어 입력 비디오가 1920x1080픽셀 해상도를 포함하는 것을 알고 있는 경우 **H264 다중 비트 전송률 1080p** 사전 설정을 선택할 수 있습니다. 저해상도(640x360) 비디오가 있는 경우 **H264 다중 비트 전송률 1080p** 사전 설정을 사용할 수 없습니다.
   
   리소스를 관리할 수 있도록 출력 자산의 이름과 작업 이름을 편집할 수 있습니다.
   
   ![자산 인코딩](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **만들기**를 선택합니다.

### <a name="monitor-encoding-job-progress"></a>인코딩 작업의 진행 상태 모니터링
인코딩 작업의 진행 상태를 모니터링하려면 페이지 맨 위에 있는 **설정**을 선택한 후 **작업**을 선택합니다.

![교육](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>콘텐츠 게시
콘텐츠를 스트리밍 또는 다운로드하는 데 사용할 수 있는 URL을 사용자에게 제공하려면 먼저 로케이터를 만들어 자산을 게시해야 합니다. 로케이터는 자산에 있는 파일에 대한 액세스를 제공합니다. Azure Media Services는 두 가지 유형의 로케이터를 지원합니다. 

* **스트리밍(OnDemandOrigin) 로케이터** 스트리밍 로케이터는 적응 스트리밍에 사용됩니다. 적응 스트리밍의 예로 HLS, 부드러운 스트리밍 및 MPEG-DASH를 들 수 있습니다. 스트리밍 로케이터를 만들려면 자산에 .ism 파일이 포함되어야 합니다. 
* **점진적(공유 액세스 서명) 로케이터** 점진적 로케이터는 점진적 다운로드를 통해 비디오를 제공하는 데 사용합니다.

HLS 스트리밍 URL을 작성하려면 URL에 *(format=m3u8-aapl)* 을 추가합니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

부드러운 스트리밍 자산을 재생하기 위해 스트리밍 URL을 작성하려면 다음 URL 형식을 사용합니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

MPEG-DASH 스트리밍 URL을 작성하려면 URL에 *(format=mpd-time-csf)* 를 추가합니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

공유 액세스 서명 URL의 형식은 다음과 같습니다.

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> 2015년 3월 이전에 Azure Portal에서 생성된 로케이터는 만료 날짜가 2년입니다.  
> 
> 

로케이터의 만료 날짜를 업데이트하려면 [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) 또는 [.NET API](https://go.microsoft.com/fwlink/?LinkID=533259)를 사용할 수 있습니다. 

> [!NOTE]
> 공유 액세스 서명 로케이터의 만료 날짜를 업데이트할 경우 URL이 변경됩니다.

### <a name="to-use-the-portal-to-publish-an-asset"></a>자산을 게시하기 위해 포털을 사용하려면
1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. 계정 배포 진행 상태를 보려면 **설정** > **자산**을 참조하세요. 게시하려는 자산을 선택합니다.
3. **게시** 단추를 선택합니다.
4. 로케이터 유형을 선택합니다.
5. **추가**를 선택합니다.
   
    ![비디오 게시](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL이 **게시된 URL**목록에 추가됩니다.

## <a name="play-content-from-the-portal"></a>포털에서 콘텐츠 재생
Azure Portal의 콘텐츠 플레이어에서 비디오를 테스트할 수 있습니다.

비디오를 선택한 다음 **재생** 단추를 선택합니다.

![Azure Portal에서 비디오 재생](./media/media-services-portal-vod-get-started/media-services-play.png)

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* 스트리밍을 시작하려면 기본 스트리밍 엔드포인트 실행을 시작하세요.
* 비디오가 게시된 것을 확인합니다.
* Azure Portal 미디어 플레이어가 기본 스트리밍 엔드포인트에서 재생됩니다. 기본이 아닌 스트리밍 엔드포인트에서 재생하려면 URL 복사를 선택하여 복사한 후 다른 플레이어에 붙여넣습니다. 예를 들어 [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html)에서 비디오를 테스트할 수 있습니다.

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
