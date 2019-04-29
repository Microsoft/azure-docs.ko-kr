---
title: Media Services v3.NET-Azure를 사용 하 여 사용자 지정 변환을 인코딩 | Microsoft Docs
description: 이 항목에서는.NET을 사용 하 여 사용자 지정 변환 인코딩하는 데 Azure Media Services v3을 사용 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: ed2ae50aa9d7a26ed6e0569264ee981f7be35525
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733677"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>사용자 지정 변환-.NET을 사용 하 여 인코딩하는 방법

Azure Media Services로 인코딩 되 면 시작할 수 있습니다 신속 하 게에 설명 된 대로 업계 모범 사례에 따라 권장 되는 기본 제공 사전 설정 중 하나를 사용 하 여 합니다 [파일을 스트리밍](stream-files-tutorial-with-api.md) 자습서입니다. 또한 특정 시나리오 또는 장치 요구 사항을 대상으로 사전 설정 사용자 지정을 빌드할 수 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 사항을 고려해 야 합니다.

* 높이 너비 AVC 내용에 대 한 모든 값은 4의 배수 여야 합니다.
* Azure Media Services v3에서는 인코딩 비트 전송률 모두 비트 / 초입니다. 이 킬로 비트/초 단위로 v2 Api 사용 하 여 사전 설정에서 다릅니다. 예를 들어 v2의 비트 전송률 (k b/초) 128로 지정 된, 경우 v3에서 것은에 설정할 128000 (비트/초)입니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md) <br/>리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 전체 .NET Core 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
사용자 지정 미리 설정 샘플은 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 폴더에 있습니다.

## <a name="create-a-transform-with-a-custom-preset"></a>사용자 지정 미리 설정을 사용하여 변환 만들기 

새 [Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 출력으로 생성하려는 것을 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **Preset**은 원하는 **TransformOutput**을 생성하는 데 사용되는 비디오 및/또는 오디오 처리 작업에 대한 단계별 지침을 설명합니다. 다음 **TransformOutput**은 사용자 지정 코덱 및 레이어 출력 설정을 만듭니다.

[Transform](https://docs.microsoft.com/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요. Media Services v3에서 엔터티가 존재하지 않으면 엔터티에 대한 **Get** 메서드에서 **null**을 반환합니다(이름에서 대/소문자를 구분하지 않음).

### <a name="example"></a>예

다음 예제에서는이 변환을 사용 하는 경우 생성 하고자 하는 출력의 집합을 정의 합니다. 먼저 오디오 인코딩에 AacAudio 계층과 비디오 인코딩에 대 한 두 H264Video 계층 추가합니다. 비디오 레이어의 출력 파일 이름에 사용할 수 있도록 레이블을 할당 합니다. 다음으로, 미리 보기 포함 하도록 출력을 하려고 합니다. 아래 예제에서는 PNG 형식으로 입력된 비디오의 해상도의 50 %3의 타임 스탬프-{25%, 50%, 75} 입력된 비디오의 길이에서 생성 되는 이미지를 지정 합니다. 마지막으로, 비디오 + 오디오에 대 한 출력 파일-의 형식 지정 및 미리 보기에 대 한 다른 합니다. 여러 H264Layers 있으므로 계층당 고유한 이름을 생성 하는 매크로 사용 해야 합니다. 사용할 수 있습니다는 `{Label}` 또는 `{Bitrate}` 매크로 예제 전자를 보여 줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md) 
