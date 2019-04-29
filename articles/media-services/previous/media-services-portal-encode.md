---
title: Azure Portal에서 Media Encoder Standard를 사용하여 자산 인코딩 | Microsoft Docs
description: 이 자습서에서는 Azure Portal에서 Media Encoder Standard를 사용하여 자산을 인코딩하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 90190f426419e65bd580b9004ae76a2c6b0c12e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463162"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Azure Portal에서 Media Encoder Standard를 사용하여 자산 인코딩

> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

Azure Media Services에서 작업하는 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 제공하는 것입니다. Media Services는 다음과 같은 적응 비트 전송률 스트리밍 기술을 지원합니다. 이러한 기술에는 Apple HLS(HTTP 라이브 스트리밍), Microsoft 부드러운 스트리밍, HTTP를 통한 동적 적응 스트리밍(DASH, MPEG-DASH라고도 함)이 있습니다. 적응 비트 전송률 스트리밍을 위한 비디오를 준비하려면 먼저 원본 비디오를 다중 비트 전송률 파일로 인코딩합니다. Azure Media Encoder Standard를 사용하여 비디오를 인코딩할 수 있습니다.  

Media Services는 동적 패키징을 제공합니다. 동적 패키징을 사용하여 이러한 스트리밍 형식으로 다시 패키징하지 않고 HLS, 부드러운 스트리밍 및 MPEG-DASH 형식으로 다중 비트 전송률 MP4를 제공할 수 있습니다. 동적 패키징을 사용하는 경우 단일 저장소 형식으로 파일을 저장하고 요금을 지불할 수 있습니다. Media Services는 클라이언트의 요청에 따라 적절한 응답을 빌드하고 제공합니다.

동적 패키징을 활용하려면 원본 파일을 다중 비트 전송률 MP4 파일 집합으로 인코딩해야 합니다. 인코딩 단계는 이 문서에 나와 있습니다.

미디어 처리의 크기를 조정하는 방법을 알아보려면 [Azure Portal을 사용하여 미디어 처리 크기 조정](media-services-portal-scale-media-processing.md)을 참조하세요.

## <a name="encode-in-the-azure-portal"></a>Azure Portal에서 인코딩

Media Encoder Standard를 사용하여 콘텐츠를 인코딩하려면:

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. 계정 배포 진행 상태를 보려면 **설정** > **자산**을 참조하세요. 인코딩하려는 자산을 선택합니다.
3. **인코딩** 단추를 선택합니다.
4. **자산 인코딩** 창에서 **Media Encoder Standard** 프로세서 및 사전 설정을 선택합니다. 사전 설정에 대한 자세한 내용은 [비트 전송률 사다리 자동 생성](media-services-autogen-bitrate-ladder-with-mes.md) 및 [Media Encoder Standard에 대한 작업 미리 설정](media-services-mes-presets-overview.md)을 참조하세요. 입력 비디오에 가장 적합한 사전 설정을 선택하는 것이 중요합니다. 예를 들어 입력 비디오가 1920x1080픽셀 해상도를 포함하는 것을 알고 있는 경우 **H264 다중 비트 전송률 1080p** 사전 설정을 선택할 수 있습니다. 저해상도(640x360) 비디오가 있는 경우 **H264 다중 비트 전송률 1080p** 사전 설정을 사용할 수 없습니다.
   
   리소스를 관리할 수 있도록 출력 자산의 이름과 작업 이름을 편집할 수 있습니다.
   
   ![자산 인코딩](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **만들기**를 선택합니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
* Azure Portal에서 [인코딩 작업의 진행률 모니터링](media-services-portal-check-job-progress.md)  

