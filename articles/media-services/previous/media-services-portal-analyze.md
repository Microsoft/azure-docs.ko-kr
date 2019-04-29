---
title: Azure Portal을 사용하여 미디어 분석 | Microsoft 문서
description: 이 항목에서는 Azure Portal을 사용하여 미디어 분석 미디어 프로세서(MP)로 미디어를 처리하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d3917f65d8be08d6355013393f6c6675ea6c7fc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61131820"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Azure Portal을 사용하여 미디어 분석 
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

## <a name="overview"></a>개요
Azure Media Services Analytics는 조직과 기업이 비디오 파일에서 실질적인 통찰력을 끌어내기 쉽도록 만드는 언어 및 시각 구성 요소 모음으로, 미디어 분석을 엔터프라이즈 규모, 규정 준수, 보안 및 전 세계 범위로 제공합니다. Azure Media Services Analytics에 대한 자세한 개요는 [이](media-services-analytics-overview.md) 항목을 참조하세요. 

이 항목에서는 Azure Portal을 사용하여 미디어 분석 미디어 프로세서(MP)로 미디어를 처리하는 방법을 설명합니다. 미디어 분석 MP는 MP4 파일 또는 JSON 파일을 생성합니다. 미디어 프로세서가 MP4 파일을 생성한 경우 파일을 점진적으로 다운로드합니다. 미디어 프로세서가 JSON 파일을 생성한 경우 Azure Blob Storage에서 해당 파일을 다운로드합니다. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>분석하려는 자산을 선택
1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **자산**을 선택합니다.  
   
    ![비디오 분석](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. 분석할 자산을 선택하고 **분석** 단추를 누릅니다.
   
    ![비디오 분석](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. **미디어 분석기를 사용하여 미디어 자산 처리** 창에서 프로세서를 선택합니다. 
   
    문서의 나머지 부분에서는 각 프로세서를 사용하는 이유와 방법을 설명합니다. 
5. **만들기**를 눌러 작업을 시작합니다.

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Azure Media Indexer** 미디어 프로세서를 사용하여 미디어 파일과 콘텐츠를 검색 가능하도록 설정할 수 있으며 선택 캡션 트랙을 생성할 수 있습니다. 이 섹션에서는 이 MP에 대해 지정하는 옵션에 대한 자세한 내용을 제공합니다.

![비디오 분석](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>언어
멀티미디어 파일에서 인식되는 자연 언어입니다. 예를 들어 영어 또는 스페인어입니다. 

### <a name="captions"></a>자막
콘텐츠에서 생성할 자막 형식을 선택할 수 있습니다. 인덱싱 작업은 다음 형식의 선택 캡션 파일을 생성할 수 있습니다.  

* **SAMI**
* **TTML**
* **WebVTT**

이러한 형식의 CC(선택 캡션)는 청각 장애가 있는 사용자가 액세스할 수 있는 오디오 및 비디오 파일을 만드는 데 사용될 수 있습니다.

### <a name="aib-file"></a>AIB 파일
사용자 지정 SQL Server IFilter에 사용하기 위해 Audio Index Blob 파일을 생성하려는 경우 이 옵션을 선택합니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) 를 참조하세요.

### <a name="keywords"></a>키워드
키워드 XML 파일을 생성하려는 경우 이 옵션을 선택합니다. 이 파일은 빈도 및 오프셋 정보를 포함하며 음성 콘텐츠에서 추출된 키워드를 포함합니다.

### <a name="job-name"></a>작업 이름
작업을 식별할 수 있는 친숙한 이름입니다. [이](media-services-portal-check-job-progress.md) 문서에서는 작업 진행률을 모니터링하는 방법을 설명합니다. 

### <a name="output-file"></a>출력 파일
출력 콘텐츠를 식별할 수 있는 친숙한 이름입니다. 

### <a name="speed"></a>속도
입력 비디오를 가속화할 속도를 지정합니다. 출력은 입력 비디오의 안정화되고 시간 경과된 표현입니다.

### <a name="job-name"></a>작업 이름
작업을 식별할 수 있는 친숙한 이름입니다. [이](media-services-portal-check-job-progress.md) 문서에서는 작업 진행률을 모니터링하는 방법을 설명합니다. 

### <a name="output-file"></a>출력 파일
출력 콘텐츠를 식별할 수 있는 친숙한 이름입니다. 

## <a name="azure-media-face-detector"></a>Azure 미디어 얼굴 탐지기
**Azure 미디어 얼굴 탐지기** MP(미디어 프로세서)를 사용하여 이동 추적, 계산이 가능해지며 표정을 통해 대상 그룹 참여 및 반응 판단도 가능합니다. 이 서비스는 두 가지 기능을 포함합니다. 

* **얼굴 검색**
  
    얼굴 검색은 동영상 내의 얼굴을 찾아 추적합니다. 여러 얼굴이 검색될 수 있으며 이후 JSON 파일로 반환되는 시간 및 위치 메타데이터를 사용하여 얼굴이 움직일 때마다 추적할 수 있습니다. 추적하는 동안 화면에서 사용자가 움직일 때, 가려지거나 프레임에서 잠시 벗어나는 경우에도 동일한 얼굴에 일관된 ID를 지정하려고 합니다.
  
  > [!NOTE]
  > 이 서비스는 안면 인식을 수행하지 않습니다. 너무 오래 프레임에서 벗어나있거나 가려지는 경우에는 다시 돌아왔을 때 새 ID가 지정됩니다.
  > 
  > 
* **감정 검색**
  
    감정 검색은 검색된 얼굴로부터 행복, 슬픔, 두려움, 분노 등의 여러 감정적 특성에 대한 분석을 반환하는 얼굴 탐지 미디어 프로세서의 선택적 구성 요소입니다. 

![비디오 분석](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>감지 모드
프로세서에 다음 모드 중 하나를 사용할 수 있습니다.

* 얼굴 감지
* 얼굴별 감정 감지
* 감정 집계 감지

### <a name="job-name"></a>작업 이름
작업을 식별할 수 있는 친숙한 이름입니다. [이](media-services-portal-check-job-progress.md) 문서에서는 작업 진행률을 모니터링하는 방법을 설명합니다. 

### <a name="output-file"></a>출력 파일
출력 콘텐츠를 식별할 수 있는 친숙한 이름입니다. 

## <a name="azure-media-motion-detector"></a>Azure 미디어 동작 탐지기
**Azure 미디어 동작 탐지기** 의 MP(미디어 프로세서)를 사용하여 길고 특별하지 않은 동영상 중에서 원하는 섹션을 효율적으로 식별합니다. 동작 검색은 고정된 카메라 장면에서 동작이 발생한 동영상 섹션을 식별하는 데 사용할 수 있습니다. 이벤트가 발생한 경계 영역 및 타임스탬프가 있는 메타데이터를 포함하는 JSON 파일을 생성합니다.

보안 동영상 피드를 대상으로 하는 이 기술은 동작을 관련 이벤트와 그림자 및 조명 변화와 같은 가양성으로 분류할 수 있습니다. 이를 통해 수많은 관련 없는 이벤트로 인해 스팸 처리되지 않고 카메라 피드로부터 보안 경고를 생성할 수 있으며, 아주 긴 감시 동영상으로부터 필요한 순간을 추출할 수 있습니다.

![비디오 분석](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure 미디어 비디오 미리 보기
이 프로세서를 사용하면 원본 비디오에서 흥미로운 조각을 자동으로 선택하여 긴 비디오의 요약을 만들 수 있습니다. 이는 긴 비디오에서 예상되는 사항에 대한 빠른 개요를 제공하려는 경우에 유용합니다. 자세한 내용 및 예제는 [Azure 미디어 비디오 미리 보기를 사용하여 비디오 요약 만들기](media-services-video-summarization.md)

![비디오 분석](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>작업 이름
작업을 식별할 수 있는 친숙한 이름입니다. [이](media-services-portal-check-job-progress.md) 문서에서는 작업 진행률을 모니터링하는 방법을 설명합니다. 

### <a name="output-file"></a>출력 파일
출력 콘텐츠를 식별할 수 있는 친숙한 이름입니다. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
이 프로세서를 사용하면 비디오에서 혹시라도 있을 수 있는 성인/외설 콘텐츠를 검색할 수 있습니다. 프로세서가 비디오의 샷과 키프레임을 자동으로 검색합니다. 성인/외설 콘텐츠에 대해 키프레임 점수를 매기고 기본 임계값에 따라 검토를 제안합니다. 자세한 내용 및 예제는 [Azure Media Content Moderator를 사용하여 비디오 조정](media-services-content-moderation.md)을 참조하세요.

![비디오 조정](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
"2.0"을 사용합니다.

### <a name="mode"></a>Mode
2.0 버전은 `Mode` 설정을 무시합니다.

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로 보기.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
