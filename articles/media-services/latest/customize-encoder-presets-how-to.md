---
title: Media Services v3 .NET을 사용 하 여 사용자 지정 변환 인코딩-Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services v 3을 사용 하 여 .NET을 사용 하 여 사용자 지정 변환을 인코딩하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 8557a11a4768b547096fb56371f0f358c890f883
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266989"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>사용자 지정 변환-.NET을 사용 하 여 인코딩하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용 하 여 인코딩하면 [스트리밍 파일](stream-files-tutorial-with-api.md) 자습서에서 설명한 대로 업계 모범 사례를 기반으로 하 여 권장 되는 기본 제공 사전 설정 중 하나를 빠르게 시작할 수 있습니다. 특정 시나리오 또는 장치 요구 사항을 대상으로 하는 사용자 지정 사전 설정을 빌드할 수도 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용 됩니다.

* AVC 콘텐츠의 높이 및 너비에 대 한 모든 값은 4의 배수 여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 전송률은 초당 비트 단위입니다. 이는 킬로 비트/초를 단위로 사용한 v2 Api와는 다릅니다. 예를 들어 v 2의 비트 전송률이 128 (k b/초)로 지정 된 경우 v3에서 128000 (비트/초)로 설정 됩니다.

## <a name="prerequisites"></a>필수 구성 요소 

[Media Services 계정 만들기](./create-account-howto.md)

## <a name="download-the-sample"></a>샘플 다운로드

다음 명령을 사용하여 전체 .NET Core 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
사용자 지정 미리 설정 샘플은 [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) 폴더에 있습니다.

## <a name="create-a-transform-with-a-custom-preset"></a>사용자 지정 미리 설정을 사용하여 변환 만들기 

새 [Transform](/rest/api/media/transforms)을 만드는 경우 출력으로 생성하려는 것을 지정해야 합니다. 필수 매개 변수는 아래 코드와 같이 [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput) 개체입니다. 각 **TransformOutput**에는 **Preset**이 포함됩니다. **사전 설정은** 원하는 **TransformOutput**을 생성 하는 데 사용 되는 비디오 및/또는 오디오 처리 작업에 대 한 단계별 지침을 설명 합니다. 다음 **TransformOutput**은 사용자 지정 코덱 및 레이어 출력 설정을 만듭니다.

[Transform](/rest/api/media/transforms)을 만드는 경우 먼저 **Get** 메서드를 사용하여 해당 Transform이 이미 있는지 확인해야 합니다. 아래 코드를 참조하세요. Media Services v3에서 엔터티에 대 한 **Get** 메서드는 엔터티가 존재 하지 않는 경우 **null** 을 반환 합니다 (이름에 대 한 대/소문자를 구분 하지 않음).

### <a name="example"></a>예

다음 예제에서는이 변환을 사용할 때 생성 하려는 출력 집합을 정의 합니다. 먼저 오디오 인코딩에 대해 AacAudio 계층을 추가 하 고 비디오 인코딩에 대해 경우 h264video 계층 두 개를 추가 합니다. 비디오 레이어에서 출력 파일 이름에 사용할 수 있도록 레이블을 할당 합니다. 다음에는 출력에 미리 보기도 포함 하려고 합니다. 아래 예제에서는 입력 비디오의 해상도는 50%로 생성 되 고 입력 비디오의 길이는 {25%, 50%, 75%} 인 3 개의 타임 스탬프에 생성 된 이미지를 PNG 형식으로 지정 합니다. 마지막으로, 출력 파일에 대 한 형식을 지정 합니다. 즉, 비디오 + 오디오의 경우, 다른 하나는 미리 보기로 지정 합니다. 여러 H264Layers 있으므로 계층 당 고유한 이름을 생성 하는 매크로를 사용 해야 합니다. `{Label}`또는 매크로를 사용할 수 있습니다 `{Bitrate}` .이 예제에서는 이전을 보여 줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md) 
