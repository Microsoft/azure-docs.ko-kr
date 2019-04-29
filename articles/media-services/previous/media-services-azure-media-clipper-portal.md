---
title: 포털에서 Azure Media Clipper 사용 | Microsoft Docs
description: Azure Portal에서 Azure Media Clipper를 사용하여 클립 만들기
services: media-services
keywords: 클립;서브클립;인코딩;미디어
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8c88caefb0909da55de87116a23fa520c1679cc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465850"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>포털에서 Azure Media Clipper를 사용하여 클립 만들기  

포털에서 Azure Media Clipper를 사용하여 미디어 서비스 계정의 자산에서 클립을 만들 수 있습니다. 시작하려면 포털의 미디어 서비스 계정으로 이동합니다. 그런 다음 **서브클립** 탭을 선택합니다.

**서브클립** 탭에서 클립 작성을 시작할 수 있습니다. 포털에서 Clipper는 단일 비트 전송률 MP4, 다중 비트 전송률 MP4 및 유효한 스트리밍 로케이터로 게시된 라이브 보관을 로드합니다. 게시되지 않은 자산은 로드되지 않습니다.

Clipper는 현재 공개 미리 보기로 제공됩니다. Azure Portal에서 Clipper에 액세스하려면 이 [공개 미리 보기 페이지](https://portal.azure.com/?feature.subclipper=true)로 이동합니다.

다음 이미지는 미디어 서비스 계정의 Clipper 방문 페이지를 보여 줍니다. ![Azure Portal의 Azure Media Clipper](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>클립 생성
클립을 만들려면 클립 인터페이스에 자산을 끌어 놓습니다. 마크 시간을 알고 있다면 인터페이스에 수동으로 입력할 수 있습니다. 또는 자산을 재생하거나 플레이헤드를 끌어 원하는 마크인 및 마크아웃 시간을 찾습니다. 마크인 또는 마크아웃 시간이 제공되지 않으면 클립은 입력 자산의 처음부터 시작하거나 끝까지 계속됩니다.

프레임 정확도/GOP 정확도로 탐색하려면 프레임 앞으로/GOP 앞으로 또는 프레임 뒤로/GOP 뒤로 단추를 사용합니다. 여러 자산에 대해 클리핑하려면 자산 선택 패널에서 여러 자산을 클립 인터페이스로 끌어 놓습니다. 인터페이스에서 원하는 순서로 자산을 선택하고 재지정할 수 있습니다. 자산 선택 패널은 각 자산에 대한 자산 기간, 유형 및 해상도 메타데이터를 제공합니다. 여러 자산을 함께 연결할 때는 각 입력 파일의 원본 해상도를 고려합니다. 원본 해상도가 다를 경우 가장 높은 해상도 자산의 해상도에 맞게 낮은 해상도 입력이 상향 조정됩니다. 클리핑 작업의 결과를 미리 보려면 미리 보기 단추를 선택합니다. 그러면 선택한 마크 시간부터 클립이 재생됩니다.

## <a name="producing-dynamic-manifest-filters"></a>동적 매니페스트 필터 생성
[동적 매니페스트 필터](https://azure.microsoft.com/blog/dynamic-manifest/)는 매니페스트 특성 및 자산 타임라인을 기반으로 일련의 규칙을 설명합니다. 이러한 규칙은 스트리밍 엔드포인트에서 출력 재생 목록(매니페스트)을 조작하는 방법을 결정합니다. 필터를 사용하여 재생을 위해 스트리밍되는 세그먼트를 변경할 수 있습니다. Clipper에 의해 생성된 필터는 로컬 필터이며 원본 자산에만 적용됩니다. 렌더링된 클립과 달리 필터는 새로운 자산이 아니며 생성 시 인코딩 작업이 필요하지 않습니다. [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) 또는 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest)를 통해 신속하게 생성할 수 있지만 단지 GOP 정확도입니다. 일반적으로 스트리밍을 위해 인코딩된 자산의 경우 GOP 크기는 2초입니다.

동적 매니페스트 필터를 만들려면 **자산** 탭으로 이동하고 원하는 자산을 선택합니다. 상단 메뉴에서 **하위 클립** 단추를 선택합니다. 고급 설정 메뉴에서 클리핑 모드로 동적 매니페스트 필터를 선택합니다. 그런 다음 동일한 프로세스를 따라 렌더링된 클립을 생성하여 필터를 만들 수 있습니다. 필터는 단일 자산에서만 생성할 수 있습니다.

다음 이미지는 Azure Portal에서 동적 매니페스트 필터 모드의 Clipper를 보여 줍니다. ![Azure Portal에서 동적 매니페스트 필터 모드의 Azure Media Clipper](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>클리핑 작업 제출
클립 작성을 마치면 해당 클리핑 작업 또는 동적 매니페스트 호출을 시작하도록 작업 제출 단추를 선택합니다.

## <a name="next-steps"></a>다음 단계
Azure Media Clipper를 사용하려면 [시작](media-services-azure-media-clipper-getting-started.md) 문서에서 위젯을 배포하는 방법에 대해 자세히 읽어보세요.
