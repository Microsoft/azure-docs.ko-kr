---
title: 워크플로 디자이너와 고급 Encoding 워크플로 만들기 | Microsoft Docs
description: 워크플로 디자이너와 고급 인코딩 워크플로를 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: a53027ec1f775c240164f67e7ee0473a44690964
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262688"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>워크플로 디자이너와 고급 Encoding 워크플로 만들기

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>개요
**Workflow Designer**는 **미디어 인코더 Premium 워크플로**를 사용하여 인코딩할 워크플로를 설계하고 만드는 데 사용되는 Windows 데스크톱 도구입니다.
워크플로 디자이너 도구의 강력한 기능을 사용하여 **미디어 인코더 프리미엄**에서 실행될 복잡한 워크플로를 설계하고 만들 수 있습니다.  

워크플로는 고객 의사 결정 논리와 입력된 소스 파일의 속성에 따른 분기를 포함할 수 있습니다. 클라우드에서 가장 복잡한 인코딩 작업을 쉽게 반복하고 사용자 지정할 수 있도록 하기 위해 재정의 가능한 속성 및 동적 값으로 워크플로를 만들 수 있습니다.

만들 수 있는 예제 워크플로는 다음과 같습니다.

* 해결 방법에 대한 원본 콘텐츠를 검사하고 원하는 출력 트랙만 인코딩하는 워크플로를 기반으로 한 의사 결정.  이는 원본 콘텐츠를 실수로 업스케일링하여 생성되는 낭비 트랙을 제거하는 데 도움이 됩니다.
* 캡션, 오버레이, 그리고 콘텐츠 연결을 지원하는 데 여러 개의 입력 파일을 사용할 수 있습니다. 

이 도구를 사용하여 [게시 워크플로](media-services-workflow-designer.md#existing_workflows)를 수정할 수도 있습니다. 

> [!NOTE]
> Workflow Designer 도구의 복사본을 얻으려면 mepd@microsoft.com에 문의하세요.

워크플로 파일을 만든 후 이를 자산으로 업로드한 다음 미디어 파일을 인코딩하는 데 사용할 수 있습니다. **.NET**을 사용하여 **미디어 인코더 Premium 워크플로**에서 인코딩하는 방법에 대한 자세한 내용은 [미디어 인코더 Premium 워크플로 사용하여 고급 인코딩](media-services-encode-with-premium-workflow.md)을 참조하세요.

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>기존 워크플로 수정
디자이너 도구를 사용하여 기본 [게시 워크플로](media-services-workflow-designer.md#existing_workflows) 를 수정할 수 있습니다. [여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)에서 기본 워크플로 파일을 가져올 수 있습니다. 폴더에는 이러한 파일에 대한 설명도 포함되어 있습니다.

다음 비디오에서는 디자이너를 사용하는 방법을 보여줍니다.

### <a name="day-1--getting-started"></a>1일차 – 시작하기
1일차 비디오에서 다루는 내용은 다음과 같습니다.

* 디자이너 개요
* 기본 워크플로 – "Hello World"
* Azure Media Services 스트리밍에서 사용할 여러 출력 MP4 파일 만들기

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2일차
2일차 비디오에서 다루는 내용은 다음과 같습니다.

* 다양한 소스 파일 시나리오 – 오디오 처리
* 고급 논리를 사용한 워크플로
* 그래프 단계

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3일차
3일차 비디오에서 다루는 내용은 다음과 같습니다.

* 워크플로/청사진 내부 스크립팅
* 현재 인코더에 대한 제한 사항
* Q&A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>도움 필요 시

[새 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-step"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>참고 항목
[Azure Premium 인코더 워크플로 디자이너 교육 비디오](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

