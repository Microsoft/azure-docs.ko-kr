---
title: Azure 미디어 분석으로 얼굴 편집 안내 | Microsoft Docs
description: 이 항목에서는 AMSE(Azure Media Services 탐색기) 및 Azure Media Redactor Visualizer(오픈 소스 도구)를 사용하여 전체 교정 워크플로를 실행하는 방법에 대한 단계별 지침을 보여줍니다.
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: rli; juliako;
ms.openlocfilehash: 3e4844c3174e41ca7f6f5667a2777aba11f70f11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875074"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Azure 미디어 분석으로 얼굴 편집 안내

## <a name="overview"></a>개요

**Azure Media Redactor**는 클라우드에서 확장성 있는 얼굴 편집 기능을 제공하는 [Azure Media Analytics](media-services-analytics-overview.md) MP(미디어 프로세서)입니다. 얼굴 편집을 사용하면 선택한 개인의 얼굴을 흐리게 표시하기 위해 동영상을 수정할 수 있습니다. 공공 안전과 새 미디어 시나리오를 위해 얼굴 편집 서비스를 사용할 수 있습니다. 짧은 장면이라도 여러 명의 얼굴이 포함된 경우 수동으로 편집하려면 많은 시간이 걸릴 수 있지만 이 서비스를 사용하면 몇 번의 간단한 단계를 통해 얼굴을 편집할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-redactor/) 를 참조하세요.

**Azure Media Redactor**에 대한 자세한 내용은 [얼굴 교정 개요](media-services-face-redaction.md) 항목을 참조하세요.

이 항목에서는 AMSE(Azure Media Services 탐색기) 및 Azure Media Redactor Visualizer(오픈 소스 도구)를 사용하여 전체 교정 워크플로를 실행하는 방법에 대한 단계별 지침을 보여줍니다.

자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/redaction-preview-available-globally) 를 참조하세요.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services 탐색기 워크플로

Redactor를 시작하는 가장 쉬운 방법은 GitHub에서 오픈 소스 AMSE 도구를 사용하는 것입니다. 주석 json 또는 얼굴 jpg 이미지에 액세스할 필요가 없는 경우 **결합** 모드를 통해 간소화된 워크플로를 실행할 수 있습니다.

### <a name="download-and-setup"></a>다운로드 및 설치

1. AMSE 도구를 [여기서](https://github.com/Azure/Azure-Media-Services-Explorer) 다운로드합니다.
1. 서비스 키를 사용하여 Media Services 계정에 로그인합니다.

    계정 이름 및 키 정보를 가져오려면 [Azure Portal](https://portal.azure.com/)로 이동하여 AMS 계정을 선택합니다. 그런 후 설정 > 키를 선택합니다. 키 관리 창에 계정 이름과 기본 및 보조 키가 표시됩니다. 계정 이름 및 기본 키 값을 복사해 둡니다.

![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>1 패스 – 분석 모드

1. 자산 –> 업로드를 통해 또는 끌어서 놓기를 통해 미디어 파일을 업로드합니다. 
1. 마우스 오른쪽 단추를 클릭하고 Media Analytics –> Azure Media Redactor –> 분석 모드를 사용하여 미디어 파일을 처리합니다. 


![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

출력에는 얼굴 위치 데이터가 있는 주석 json 파일과 감지된 각 얼굴의 jpg가 포함됩니다. 

![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>2 패스 – 교정 모드

1. 1 패스의 출력으로 원본 비디오 자산을 업로드하고 기본 자산으로 설정합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (선택 사항) 교정하려는 ID의 줄 바꿈으로 구분된 목록이 포함된 'Dance_idlist.txt' 파일을 업로드합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (선택 사항) 경계 상자 경계를 늘리는 등, annotations.json 파일을 편집합니다. 
4. 1 패스의 출력 자산을 마우스 오른쪽 단추로 클릭하고 Redactor를 선택한 후 **교정** 모드에서 실행합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. 교정된 최종 출력 자산을 다운로드하거나 공유합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Visualizer 오픈 소스 도구

오픈 소스 [Visualizer 도구](https://github.com/Microsoft/azure-media-redactor-visualizer)는 개발자가 주석 형식부터 시작해서 출력을 구문 분석하고 사용할 수 있도록 지원합니다.

리포지토리를 복제한 후에 프로젝트를 실행하기 위해 해당 [공식 사이트](https://ffmpeg.org/download.html)에서 FFMPEG를 다운로드해야 합니다.

JSON 주석 데이터를 구문 분석하려는 개발자는 Models.MetaData 내부에서 샘플 코드 예제를 확인합니다.

### <a name="set-up-the-tool"></a>도구 설정

1.  전체 솔루션을 다운로드하고 빌드합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  [여기](https://ffmpeg.org/download.html)에서 FFMPEG를 다운로드합니다. 이 프로젝트는 정적 링크가 있는 be1d324(2016-10-04)를 사용하여 개발되었습니다. 
3.  ffmpeg.exe 및 ffprobe.exe를 AzureMediaRedactor.exe와 동일한 출력 폴더에 복사합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. AzureMediaRedactor.exe를 실행합니다. 

### <a name="use-the-tool"></a>도구 사용

1. 분석 모드에서 Redactor MP를 사용하여 Azure Media Services 계정에서 비디오를 처리합니다. 
2. 원본 비디오 파일 및 교정 - 분석 작업의 출력을 모두 다운로드합니다. 
3. Visualizer 애플리케이션을 실행하고 위의 파일을 선택합니다. 

    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. 파일을 미리 봅니다. 오른쪽의 사이드바에서 흐리게 표시하려는 얼굴을 선택합니다. 
    
    ![얼굴 편집](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  아래쪽 텍스트 필드는 얼굴 ID로 업데이트됩니다. 줄 바꿈으로 구분된 이러한 ID 목록으로 "idlist.txt"라는 파일을 만듭니다. 

    >[!NOTE]
    > idlist.txt는 ANSI로 저장해야 합니다. ANSI로 저장하려면 메모장을 사용할 수 있습니다.
    
6.  1단계의 출력 자산에 이 파일을 업로드합니다. 이 자산에도 원본 비디오를 업로드하고 기본 자산으로 설정합니다. 
7.  "Redact" 모드를 사용하여 이 자산에 대해 교정 작업을 실행하여 교정된 최종 비디오를 얻습니다. 

## <a name="next-steps"></a>다음 단계 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Azure Media Analytics의 얼굴 편집 발표](https://azure.microsoft.com/blog/azure-media-redactor/)
