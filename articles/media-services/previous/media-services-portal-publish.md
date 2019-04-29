---
title: Azure Portal에서 콘텐츠 게시 | Microsoft Docs
description: 이 자습서에서는 Azure Portal에서 콘텐츠를 게시하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2639d47a6c9d8da53c9d6bccde0d317698d5d9fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127874"
---
# <a name="publish-content-in-the-azure-portal"></a>Azure Portal에서 콘텐츠 게시  
> [!div class="op_single_selector"]
> * [포털](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST (영문)](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>개요
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

콘텐츠를 스트리밍 또는 다운로드하는 데 사용할 수 있는 URL을 사용자에게 제공하려면 먼저 로케이터를 만들어 자산을 게시해야 합니다. 로케이터는 자산 파일에 대한 액세스를 제공합니다. Azure Media Services는 두 가지 유형의 로케이터를 지원합니다. 

* **스트리밍(OnDemandOrigin) 로케이터** 스트리밍 로케이터는 적응 스트리밍에 사용됩니다. 적응 스트리밍의 예에는 Apple HLS(HTTP 라이브 스트리밍), Microsoft 부드러운 스트리밍, HTTP를 통한 동적 적응 스트리밍(DASH, MPEG-DASH라고도 함)이 있습니다. 스트리밍 로케이터를 만들려면 자산에 .ism 파일이 포함되어야 합니다. 예: http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **점진적(공유 액세스 서명) 로케이터** 점진적 로케이터는 점진적 다운로드를 통해 비디오를 제공하는 데 사용합니다.

HLS 스트리밍 URL을 작성하려면 URL에 *(format=m3u8-aapl)* 을 추가합니다.

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

부드러운 스트리밍 자산을 재생하기 위해 스트리밍 URL을 작성하려면 다음 URL 형식을 사용합니다.

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

MPEG-DASH 스트리밍 URL을 작성하려면 URL에 *(format=mpd-time-csf)* 를 추가합니다.

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

공유 액세스 서명 URL의 형식은 다음과 같습니다.

    {blob container name}/{asset name}/{file name}/{shared access signature}

자세한 내용은 [콘텐츠 제공 개요](media-services-deliver-content-overview.md)를 참조하세요.

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

## <a name="play-content-in-the-portal"></a>포털에서 콘텐츠 재생
Azure Portal의 콘텐츠 플레이어에서 비디오를 테스트할 수 있습니다.

비디오를 선택한 다음 **재생** 단추를 선택합니다.

![Azure Portal에서 비디오 재생](./media/media-services-portal-vod-get-started/media-services-play.png)

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* 비디오가 게시된 것을 확인합니다.
* Azure Portal 미디어 플레이어가 기본 스트리밍 엔드포인트에서 재생됩니다. 기본이 아닌 스트리밍 엔드포인트에서 재생하려면 URL 복사를 선택하여 복사한 후 다른 플레이어에 붙여넣습니다. 예를 들어 [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html)에서 비디오를 테스트할 수 있습니다.
* 스트리밍을 하고 있는 스트리밍 엔드포인트가 실행 중이어야 합니다.  

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

