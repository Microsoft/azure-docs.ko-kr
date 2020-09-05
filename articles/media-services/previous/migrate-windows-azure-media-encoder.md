---
title: Windows Azure Media Encoder에서 Media Encoder Standard로 마이그레이션 | Microsoft Docs
description: 이 항목에서는 Azure Media Encoder에서 Media Encoder Standard Media 프로세서로 마이그레이션하는 방법에 대해 설명 합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a4a5f2f448cc5e954cf21f9785f788925d5a461e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263946"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Windows Azure Media Encoder에서 Media Encoder Standard로 마이그레이션

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

이 문서에서는 WAME (기존 Windows Azure Media Encoder) 미디어 프로세서 (사용 중지 됨)에서 Media Encoder Standard 미디어 프로세서로 마이그레이션하는 단계를 설명 합니다. 사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 항목을 참조하세요.

WAME을 사용 하 여 파일을 인코딩할 때 고객은 일반적으로와 같은 명명 된 사전 설정 문자열을 사용 `H264 Adaptive Bitrate MP4 Set 1080p` 했습니다. 마이그레이션하기 위해 코드를 업데이트 하 여 WAME 대신 **Media Encoder Standard** 미디어 프로세서를 사용 하 고와 같은 해당 [시스템 사전 설정](media-services-mes-presets-overview.md) 중 하나를 사용 하도록 업데이트 해야 `H264 Multiple Bitrate 1080p` 합니다. 

## <a name="migrating-to-media-encoder-standard"></a>Media Encoder Standard로 마이그레이션

다음은 레거시 구성 요소를 사용 하는 일반적인 c # 코드 샘플입니다. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Media Encoder Standard를 사용 하는 업데이트 된 버전은 다음과 같습니다.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>고급 시나리오 

해당 스키마를 사용 하 여 WAME에 대해 고유한 인코딩 사전 설정을 만든 경우 [Media Encoder Standard에 대해 동일한 스키마](media-services-mes-schema.md)가 있습니다.

## <a name="known-differences"></a>알려진 차이점 

Media Encoder Standard 더 강력 하 고 안정적 이며, 더 나은 성능을 가지 며, 레거시 WAME 인코더 보다 품질이 우수한 출력을 생성 합니다. 또한: 

* Media Encoder Standard은 WAME와 다른 명명 규칙을 사용 하 여 출력 파일을 생성 합니다.
* Media Encoder Standard는 [입력 파일 메타 데이터](media-services-input-metadata-schema.md) 및 [출력 파일 메타 데이터](media-services-output-metadata-schema.md)를 포함 하는 파일과 같은 아티팩트를 생성 합니다.
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding) (특히 FAQ 섹션)에 설명 된 대로 Media Encoder Standard를 사용 하 여 비디오를 인코딩하면 출력으로 생성 된 파일의 기간을 기준으로 요금이 청구 됩니다. WAME을 사용 하면 입력 비디오 파일의 크기와 출력 비디오 파일을 기준으로 요금이 청구 됩니다.

## <a name="need-help"></a>도움 필요 시

[새 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)
