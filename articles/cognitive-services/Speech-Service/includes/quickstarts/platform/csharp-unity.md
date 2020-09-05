---
title: '빠른 시작: C# Unity용 Speech SDK 플랫폼 설정 - Speech Service'
titleSuffix: Azure Cognitive Services
description: 이 가이드를 사용하여 Speech Service SDK에서 C# Unity용 플랫폼을 설정합니다.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 7678ffa1684fb1fe1ea198e9e1836a152cd51b4b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926676"
---
이 가이드에서는 [Unity](https://unity3d.com/)용 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)를 설치하는 방법을 보여 줍니다.

> [!NOTE]
> Unity용 Speech SDK는 Windows 데스크톱(x86 및 x64) 또는 유니버설 Windows 플랫폼(x86, x64, ARM/ARM64), Android(x86, ARM32/64) 및 iOS(x64 시뮬레이터, ARM32 및 ARM64)를 지원합니다.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에는 다음이 필요합니다.

- [Unity 2018.3 이상](https://store.unity.com/)([UWP ARM64 지원이 추가된 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal) 포함)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Visual Studio 2017 버전 15.9 이상도 허용됩니다.
- Windows ARM64를 지원하려면 [ARM64용 선택적 빌드 도구 및 ARM64용 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)를 설치합니다.

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

Unity용 Speech SDK를 설치하려면 다음 단계를 수행합니다.

1. Unity 자산 패키지(.unitypackage)로 패키지된 [Unity용 Speech SDK](https://aka.ms/csspeech/unitypackage)를 다운로드하여 엽니다. 이 SDK는 Unity와 이미 연결되어 있습니다. 자산 패키지가 열리면 **Unity 패키지 가져오기** 대화 상자가 표시됩니다. 이 단계를 수행하려면 빈 프로젝트를 만들고 열어야 할 수 있습니다.

   [![Unity 편집기의 Unity 패키지 가져오기 대화 상자](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. 모든 파일이 선택되었는지 확인하고, **가져오기**를 선택합니다. 잠시 후에 Unity 자산 패키지를 프로젝트로 가져옵니다.

자산 패키지를 Unity로 가져오는 방법에 대한 자세한 내용은 [Unity 설명서](https://docs.unity3d.com/Manual/AssetPackages.html)를 참조하세요.

이제 아래의 [다음 단계](#next-steps)로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [windows](../quickstart-list.md)]
