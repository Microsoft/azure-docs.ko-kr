---
title: Media Services 플랫폼에서 미디어 분석 | Microsoft 문서
description: 엔터프라이즈 규모, 규정 준수, 보안 및 전 세계 범위의 음성 및 컴퓨터 비전 서비스 컬렉션인 미디어 분석의 공개 미리 보기
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: aac9719f8d74c4b7bc283745ee2b8e01365a81f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245271"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Services 플랫폼에서 미디어 분석 

## <a name="overview"></a>개요
직원을 교육하고, 고객을 참여시키고, 비즈니스 기능을 문서화하는 기본 미디어로 비디오를 사용하는 조직이 늘어나고 있습니다. 클라우드 컴퓨팅은 이러한 대용량 미디어 파일을 저장, 스트림, 액세스하는 방법을 제공합니다. 하지만 비디오 콘텐츠의 회사 라이브러리가 늘어남에 따라 콘텐츠에서 통찰력을 추출하는 효과적인 방법이 동일하게 필요합니다. 

이 증가하는 요구를 해결하기 위해 Azure Media Services는 Azure Media 분석을 제공합니다. 미디어 분석은 조직과 기업이 비디오 파일에서 실질적인 통찰력을 끌어내기 쉽도록 만드는 언어 및 시각 구성 요소 모음입니다. 핵심 Media Services 플랫폼 구성 요소를 사용하여 구축된 미디어 분석은 대규모 미디어를 하루에 처리할 수 있습니다.

개발자는 미디어 분석을 통해 애플리케이션에 고급 비디오 기능을 신속하게 가져올 수 있습니다. 대기업에 필요한 전체 규모, 규정 준수, 보안 및 전 세계 범위의 엔터프라이즈 환경을 제공합니다.

다음 다이어그램에서는 미디어 분석 및 Media Services 플랫폼의 다른 주요 부분을 보여 줍니다. 

![VoD 워크플로](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

미디어 분석 미디어 프로세서는 MP4 파일 또는 JSON 파일을 생성합니다. 미디어 프로세서가 MP4 파일을 생성한 경우 파일을 점진적으로 다운로드할 수 있습니다. 미디어 프로세서가 JSON 파일을 생성한 경우 Azure Blob Storage에서 해당 파일을 다운로드할 수 있습니다. 

## <a name="media-analytics-services"></a>미디어 분석 서비스

### <a name="indexer"></a>인덱서
Azure Media Indexer를 사용하면 콘텐츠를 검색할 수 있도록 설정하고 선택 캡션 트랙을 생성할 수 있습니다. Azure Media Indexer 2 미리 보기에는 이전 버전에 비해 보다 빠른 인덱싱과 더 광범위한 언어 지원이 있습니다. 지원되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어, 포르투갈어, 아랍어 등입니다. 자세한 내용 및 예제는 [Azure Media Indexer 2를 사용하여 비디오 처리](media-services-process-content-with-indexer2.md)를 참조하세요.
### <a name="motion-detector"></a>동작 감지기
동작 감지기를 사용하여 편지지 배경의 비디오에서 동작을 감지할 수 있습니다. 그러면 감시 카메라에서 감지된 동작 이벤트에서의 거짓 긍정을 확인할 수 있습니다. 자세한 내용 및 예제는 [Azure Media 분석에 대한 동작 감지](media-services-motion-detection.md)를 참조하세요.
### <a name="face-detector"></a>얼굴 감지기
얼굴 감지기를 사용하여 사람들의 얼굴과 행복, 슬픔, 놀라움 등의 감정을 감지할 수 있습니다. 여기에는 이벤트 참여자의 반응을 집계하고 분석하는 등 나중에 설명된 몇 가지 유용한 산업 애플리케이션이 있습니다. 자세한 내용 및 예제는 [Azure Media 분석에 대한 얼굴 및 감정 감지](media-services-face-and-emotion-detection.md)를 참조하세요.
### <a name="video-summarization"></a>비디오 요약
비디오 요약을 사용하면 원본 비디오에서 흥미로운 조각을 자동으로 선택하여 긴 비디오의 요약을 만들 수 있습니다. 이 기능은 긴 비디오에서 예상되는 사항에 대한 빠른 개요를 제공하려는 경우에 유용합니다. 자세한 내용 및 예제는 [Azure Media Video Thumbnails를 사용하여 비디오 요약 만들기](media-services-video-summarization.md)를 참조하세요.
### <a name="optical-character-recognition"></a>광학 문자 인식
Azure Media OCR(광학 문자 인식)을 사용하여 비디오 파일의 텍스트 콘텐츠를 편집 및 검색 가능한 디지털 텍스트로 변환할 수 있습니다. 그러면 미디어의 비디오 신호에서 의미 있는 메타데이터를 자동으로 추출할 수 있습니다.
### <a name="scalable-face-redaction"></a>확장 가능한 얼굴 편집
Azure Media Redactor는 클라우드에서 확장성 있는 얼굴 편집 기능을 제공하는 Media Analytics 미디어 프로세서입니다. 얼굴 편집을 사용하면 선택한 개인의 얼굴을 흐리게 표시하기 위해 동영상을 수정할 수 있습니다. 뉴스 미디어 또는 공공 안전과 관련된 경우 얼굴 편집 서비스를 사용할 수 있습니다. 짧은 장면이라도 여러 명의 얼굴이 포함된 경우 수동으로 편집하려면 많은 시간이 걸릴 수 있지만 이 서비스를 사용하면 몇 번의 간단한 단계를 통해 얼굴을 편집할 수 있습니다. 자세한 내용은 [Azure Media 분석으로 얼굴 편집](media-services-face-redaction.md) 문서를 참조하세요.
### <a name="content-moderation"></a>콘텐츠 조정
Azure Content Moderator를 통해 비디오에 대해 컴퓨터에서 지원하는 조정을 사용할 수 있습니다. 예를 들어 휴먼 조정 팀이 비디오에서 혹시라도 있을 수 있는 성인/외설 콘텐츠를 검색하고 플래그가 지정된 콘텐츠를 검토하게 하고 싶을 수 있습니다. 원치 않는 콘텐츠에 대해 비디오를 수동으로 조정하는 것은 시간도 많이 걸리고 비용도 많이 드는 작업입니다. 이 서비스 및 관련 검토 도구를 사용하면 컴퓨터에서 지원하는 조정과 휴먼 인 루프 기능을 결합하여 최상의 결과를 효율적이고 경제적으로 얻을 수 있습니다. 자세한 내용은 [Azure Content Moderator에서 비디오 처리](media-services-content-moderation.md) 문서를 참조하세요.

## <a name="common-scenarios"></a>일반적인 시나리오
미디어 분석은 조직 및 기업이 비디오에서 새로운 통찰력을 얻고 대용량의 비디오 콘텐츠를 효율적으로 관리하는 데 도움이 됩니다. 다음은 몇 가지 시나리오입니다.

* **콜 센터** 소셜 미디어의 출현에도 고객 콜 센터는 여전히 고객 서비스 트랜잭션의 많은 부분을 용이하게 합니다. 높은 고객 만족도 달성하기 위해 분석될 수 있는 많은 양의 고객 정보가 이 오디오 데이터에서 인코딩됩니다. Media Indexer를 사용하여 조직은 텍스트를 추출하고 검색 인덱스와 대시보드를 빌드할 수 있습니다. 그런 다음 일반적인 불만, 불만의 원인 및 기타 관련 데이터 인텔리전스를 추출할 수 있습니다.
* **사용자 생성 콘텐츠 조정** 뉴스 미디어 방송국부터 경찰서까지 많은 조직에는 비디오 및 이미지와 같은 사용자 생성 미디어를 허용하는 공공 포털이 있습니다. 예기치 않은 이벤트로 인해 콘텐츠 양이 급증할 수 있습니다. 이러한 시나리오에서는 콘텐츠의 적합성을 수동으로 효과적으로 검토하는 것이 어렵습니다. 고객은 콘텐츠 감수 서비스에 의존하여 적절한 콘텐츠에 중점을 둘 수 있습니다.
* **감시** IP 카메라 사용이 증가함에 따라 감시 비디오의 인벤토리도 증가합니다. 감시 비디오를 수동으로 검토하는 것은 많은 시간이 소요되고 실수가 발생하기 쉽습니다. 미디어 분석에서는 파생물을 보다 쉽게 검토, 관리 및 생성할 수 있도록 동작 감지, 얼굴 감지 및 Hyperlapse와 같은 서비스를 제공합니다.

## <a name="media-analytics-media-processors"></a>미디어 분석 미디어 프로세서
이 섹션에서는 미디어 분석 미디어 프로세서를 나열하고 .NET 또는 REST를 사용하여 미디어 프로세서(MP) 개체를 가져옵니다.

### <a name="mp-names"></a>MP 이름
* Azure Media Indexer 2 미리 보기
* Azure Media Indexer
* Azure 미디어 Hyperlapse
* Azure 미디어 얼굴 탐지기
* Azure 미디어 동작 탐지기
* Azure 미디어 비디오 미리 보기
* Azure 미디어 OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
다음 함수는 지정된 MP 이름 중 하나를 사용하고 MP 개체를 반환합니다.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST (영문)
요청:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

응답:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>데모
[Azure Media 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)를 참조하세요.

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>관련 문서
[Media Services 분석 알림](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)을 참조하세요.

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
