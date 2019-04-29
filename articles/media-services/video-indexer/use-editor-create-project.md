---
title: Video Indexer 편집기를 사용 하 여 프로젝트를 만들려면
titlesuffix: Azure Media Services
description: 이 항목에서는 Video Indexer 편집기를 사용 하 여 프로젝트를 만드는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560935"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Video Indexer 편집기를 사용 하 여 프로젝트를 만들려면

비디오 인덱서 웹 사이트를 사용 하면 비디오의 깊이 있는 통찰력을 사용 하 합니다: 올바른 미디어 콘텐츠를 찾을, 관심을 완전히 새로운 프로젝트를 만들려면 결과 사용 하는 부분을 찾습니다. 프로젝트를 렌더링 및 Video Indexer에서 다운로드할 수 있습니다를 만든 후 응용 프로그램 또는 다운스트림 워크플로 고유한 편집에 사용할 수 있습니다.

일부 시나리오에 유용할 수 있습니다이 기능은 다음과 같습니다. 

* 영화를 만드는 트레일러에 대 한 강조 표시 합니다.
* 뉴스 캐스트에서 비디오의 이전 클립을 사용합니다.
* 소셜 미디어에 대 한 짧은 콘텐츠를 만드는 중입니다.

이 문서에서는 처음부터 새로 프로젝트를 만드는 방법 및 계정에는 비디오에서 프로젝트를 만드는 방법을 보여 줍니다.

## <a name="create-new-project-and-manage-videos"></a>새 프로젝트를 만들고 관리 비디오

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
1. 선택 된 **프로젝트** 탭 합니다. 하기 전에 프로젝트를 만들면 모든 다른 프로젝트 여기 나타납니다.
1. 클릭 **새 프로젝트 만들기**합니다.  

    ![새 프로젝트](./media/video-indexer-view-edit/new-project.png)
1. 연필 아이콘을 클릭 하 여 프로젝트 이름의 제공 합니다. 프로젝트 이름을 "제목이 없는 프로젝트" 라는 텍스트를 대체 하 고 확인을 클릭 합니다.

    ![새 프로젝트](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>프로젝트에 비디오 추가

> [!NOTE]
> 현재 프로젝트는 동일한 언어의 인덱싱된 비디오 포함할 수 있습니다. 비디오 언어에서를 선택 하면 다른 언어에 있는 계정에서 비디오를 추가할 수 없습니다.

1. 선택 하 여이 프로젝트에서 사용 하려는 비디오 추가 **비디오 추가**합니다.

    계정 및 "검색 텍스트, 키워드 또는 시각적 콘텐츠를" 표시 하는 검색 상자에 비디오를 모두 표시 됩니다. 지정 된 사용자, 레이블, 브랜드, 키워드 또는 대 본 및 OCR에서 발생 하는 비디오에 대 한 검색 합니다.
    
    예를 들어 아래 이미지에서 살펴봅니다 "GitHub"를 언급 하는 비디오에 대 한 합니다.
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    선택 하 여 결과 필터링 할 수 있습니다 **결과 필터링**합니다. 에 특정 사용자를 포함 하는 비디오를 표시 하거나 있는지만 확인 하려는 비디오 결과 지정 하려면 필터링 할 수 있습니다를 특정 언어로 특정 소유자 또는 합니다. <br/> 또한 쿼리의 범위를 지정할 수 있습니다. 에 OCR "GitHub"를 검색 하려는 경우 선택 하는 예를 들어 **시각적 텍스트**합니다.

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    쿼리에 여러 필터를 넣을 수 있습니다. 사용 된 **+** / **-** 필터를 추가/제거 하는 단추입니다. 사용 하 여 **필터를 지우려면** 모든 필터를 제거 합니다.
1. 비디오를 추가 하려면 모두 선택 하 고 선택한 **추가**합니다.
1. 이제 선택한 비디오의 모든 표시 됩니다. 이들은는 클립을 프로젝트에 대 한 선택 하려는 비디오입니다.

    끌어서 놓는 방법 또는 목록 메뉴 단추를 선택 하 고 선택 하 여 비디오의 순서를 다시 정렬할 수 있습니다 **아래로 이동** 하거나 **위로**합니다. 목록 메뉴에서이 프로젝트에서 비디오를 제거할 수도 있습니다. 

    ![다시 정렬](./media/video-indexer-view-edit/rearrange.png)
    
    더 많은 비디오를 선택 하 여 언제 든 지가이 프로젝트에 추가 옵션이 **비디오 추가**합니다. 또한 프로젝트에 동일한 비디오를 여러 번 추가할 수 있습니다. 한 비디오를 다음에서 다른 클립 클립 및 다른 클립을 첫 번째 비디오에서 표시 하려는 경우이 작업을 수행 하는 것이 좋습니다. 

### <a name="select-clips-to-use-in-your-project"></a>프로젝트에서 사용 하는 클립을 선택 합니다.

각 비디오의 오른쪽에 있는 아래쪽 화살표를 클릭 하면 타임 스탬프 (비디오 클립)에 따라 비디오에서 insights 열립니다. 

1. 선택 **정보 보기** 참조 및를 보지 않으려면 하려는 insights를 사용자 지정할 수 있습니다. 

    ![인사이트 보기](./media/video-indexer-view-edit/insights.png)
1. 특정 클립에 대 한 쿼리를 만들려면 "검색 기록, 시각적 텍스트, 사람 및 레이블이" 라는 검색 상자를 사용 합니다.
1. 추가 선택 하 여 원하는 어떤 장면에서 세부 정보를 지정 하는 필터를 추가할 **필터 옵션**합니다.

    ![필터 옵션](./media/video-indexer-view-edit/filter-options.png)

    예를 들어, 다음 클립 화면에 관해 Donovan Brown은 GitHub가 언급 하는 위치를 참조 하는 것이 좋습니다. 이 위해 해야 "People"가 "포함" 필터를 추가 하려면 insight의 형식으로 합니다. 필터 검색 상자에 "Donovan Brown"를 입력 해야 합니다.
    
    ![포함](./media/video-indexer-view-edit/include.png)
    
    Donovan Brown은 GitHub 나와 있는 클립 원하면 _되지_ 화면의 변경 하기만 하면 "포함" 필터 드롭다운을 사용 하는 "exclude" 필터에 합니다. 

1. 프로젝트에 추가할 세그먼트를 선택 하 여 클립을 추가 합니다. 세그먼트를 다시 클릭 하 여이 클립을 선택 취소할 수 있습니다.
    
    비디오 및 선택 옆에 있는 목록 메뉴 옵션을 클릭 하 여 비디오의 모든 세그먼트를 추가할 **모든 세그먼트가 선택**합니다. 

    ![모두 추가](./media/video-indexer-view-edit/add-all.png)

    선택 영역 지우기를 선택 하 여 모든 선택 항목을 지울 수 있습니다.

> [!TIP]
> 선택 하 고 클립을 정렬 하는 대로 페이지의 오른쪽에는 플레이어에서 비디오를 미리 볼 수 있습니다. 

![미리 보기](./media/video-indexer-view-edit/preview.png)

선택 하 여 변경 하는 경우 프로젝트를 저장 해야 **저장 프로젝트**합니다. 

### <a name="render-and-download-the-project"></a>렌더링 하 고 프로젝트를 다운로드 합니다.

> [!NOTE]
> Video Indexer 계정 지불, 프로젝트를 렌더링에 인코딩 비용이 있습니다. 비디오 인덱서 평가판 계정은 5 시간의 렌더링 제한 됩니다.

1. 완료 되 면 프로젝트가 저장 된 해야 합니다. 이제이 프로젝트를 렌더링할 수 있습니다. 선택 **렌더링 하 고 다운로드**합니다. 

    ![저장](./media/video-indexer-view-edit/save.png)

    Video indexer 파일로 렌더링 합니다 있는지 여부를 알려 주는 팝업 됩니다 하 고 다운로드 링크를 전자 메일로 전송 됩니다. 선택 계속 진행 합니다. 
    
    또한 프로젝트 페이지 맨 위에 렌더링 되는 알림이 표시 됩니다. 완료 되 면 렌더링 되 고 프로젝트에서 성공적으로 렌더링 하는 새 알림을 볼 수 있습니다. 프로젝트를 다운로드 하려면 알림을 클릭 합니다. Mp4 형식으로 프로젝트가 다운로드 됩니다.

    ![렌더링 작업](./media/video-indexer-view-edit/rendering-done.png)

1. 저장 된 프로젝트에 액세스할 수 있습니다 합니다 **프로젝트** 탭 합니다. 

    이 프로젝트를 선택 하는 경우에 모든 정보 및이 프로젝트의 타임 라인 것이 표시 됩니다. 선택 하는 경우 **비디오 편집기**,이 프로젝트에 편집 작업을 계속할 수 있습니다. 편집에는 추가 또는 비디오 및 클립을 제거 하거나 프로젝트 이름 바꾸기 포함 됩니다.

    ![동영상 편집기](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>비디오에서 프로젝트 만들기

계정에는 비디오에서 직접 새 프로젝트를 만들면 됩니다. 

1. 로 이동 합니다 **라이브러리** Video Indexer 웹 사이트의 탭 합니다.
1. 프로젝트를 만들려면 사용 하려는 비디오를 엽니다. Insights 및 일정 페이지에서 선택 합니다 **비디오 편집기** 단추입니다.

    이렇게 하면 새 프로젝트를 만드는 데 페이지와 동일 합니다. 새 프로젝트와 달리 이전에 편집을 시작 해야 하는 비디오의 타임 스탬프가 적용 된 insights 세그먼트를 표시 합니다.

## <a name="see-also"></a>참고 항목

[Video Indexer 개요](video-indexer-overview.md)

