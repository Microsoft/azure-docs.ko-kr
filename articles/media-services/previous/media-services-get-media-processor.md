---
title: Azure Media Services SDK for .NET을 사용하여 미디어 프로세서를 만드는 방법 | Microsoft 문서
description: Azure Media Services용 미디어 콘텐츠를 인코딩하거나 형식을 변환하거나 암호화하거나 암호 해독하기 위한 미디어 프로세서 구성 요소를 만드는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463847"
---
# <a name="how-to-get-a-media-processor-instance"></a>방법: 미디어 프로세서 인스턴스 가져오기
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST (영문)](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>개요
Media Services에서 미디어 프로세서는 미디어 콘텐츠 인코딩, 형식 변환, 암호화 또는 암호 해독과 같은 특정 처리 작업을 다루는 구성 요소입니다. 일반적으로 미디어 콘텐츠 인코드, 암호화 또는 형식 변환 작업을 만들 때 미디어 프로세서를 만듭니다.

## <a name="azure-media-processors"></a>Azure 미디어 프로세서 

미디어 프로세스 목록은 다음 항목에서 제공됩니다.

* [Encoding 미디어 프로세서](scenarios-and-availability.md#encoding-media-processors)
* [분석 미디어 프로세서](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>미디어 프로세서 가져오기

다음 메서드는 미디어 프로세서 인스턴스를 가져오는 방법을 보여 줍니다. 이 코드 예제에서는 **_context**라는 모듈 수준 변수를 사용하여 [방법: 프로그래밍 방식으로 Media Services에 연결](media-services-use-aad-auth-to-access-ams-api.md) 섹션에 설명된 대로 서버 컨텍스트를 참조한다고 가정합니다.

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
미디어 프로세서 인스턴스를 가져오는 방법을 알아보았으므로 이제 Media Encoder Standard를 사용하여 자산을 인코딩하는 방법을 보여 주는 [자산을 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md) 토픽으로 이동합니다.

