---
title: Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 웹 사이트를 사용하여 개인 모델을 사용자 지정하는 방법을 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555827"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정

Video Indexer는 동영상 콘텐츠 유명인 인식을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. 자세한 개요를 참조 하세요 [Video Indexer에서 사용자 모델을 사용자 지정](customize-person-model-overview.md)합니다.

Video Indexer 웹 사이트를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. 에 설명 된 대로 API를 사용할 수도 있습니다 [Api를 사용 하 여 사용자 모델을 사용자 지정](customize-person-model-with-api.md)합니다.

## <a name="central-management-of-person-models-in-your-account"></a>사용자 계정에는 모델의 중앙 관리

1. 보기, 편집 하 고 계정의 사용자 모델을 삭제, Video Indexer 웹 사이트로 이동 하 고 로그인 합니다.
2. 페이지의 오른쪽 위 모서리에 있는 콘텐츠 모델 사용자 지정 단추를 클릭 합니다.

    ![콘텐츠 모델 사용자 지정](./media/customize-face-model/content-model-customization.png)
3. 사용자 탭을 선택 합니다.

    계정에 기본 사용자 모델이 표시 됩니다. 기본 사용자 모델 편집 또는 변경 정보를 지정 하지 않은 사용자 지정 사용자 모델을 인덱싱하는 동안 비디오의 경우 모든 얼굴을 보유 합니다. 

    다른 사람이 모델을 만든 경우이 페이지에 나열 됩니다. 

    ![콘텐츠 모델 사용자 지정](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>새 개인 모델 만들기

1. 클릭 합니다 **+ 추가 모델** 단추입니다.

    ![새 사람 추가](./media/customize-face-model/add-new-person.png)
2. 모델의 이름을 입력 하 고 이름 옆에 있는 확인 단추를 클릭 합니다.

    ![새 사람 추가](./media/customize-face-model/add-new-person2.png)

    새 사용자 모델을 만든 것입니다. 이제 새 사용자 모델에 얼굴을 추가할 수 있습니다.
3. 목록 메뉴 단추를 클릭 하 고 선택 **+ 사용자 추가**합니다.

    ![새 사람 추가](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>사용자 모델에 새 사용자 추가

> [!NOTE]
> Video Indexer를 사용 하면 사용자 모델에서 동일한 이름 가진 여러 사용자를 추가할 수 있습니다. 그러나 이름을 지정 해야 unque 각 사람에 게 모델의 유용성 및 쉽게 구별할 수 있도록 하는 것이 좋습니다.

1. 사용자 모델에 새 얼굴을 추가할 얼굴을 추가 하려는 사용자 모델 옆의 목록 메뉴 단추를 클릭 합니다.
1. 클릭 **+ 사용자 추가** 합니다.

    ![새 면을 직원에 게 추가](./media/customize-face-model/add-new-face.png)
 
    팝업은 사람의 세부 정보를 입력 하 라는 메시지가 나타납니다. 사용자 이름을 입력 하 고 확인 단추를 클릭 합니다.

    ![새 면을 직원에 게 추가](./media/customize-face-model/add-new-face2.png)
 
수 다음 파일 탐색기에서 선택 하거나 끌어서 놓으면 글꼴의 얼굴 이미지. Video Indexer 걸립니다 모든 표준 이미지 파일 형식 (예: JPG, PNG, 등)입니다.

Video Indexer 인덱스는 및 있습니다가 이미 인덱싱된 경우이 새 얼굴을 추가 하는 사용자 모델을 사용 하는 현재 비디오 향후 비디오에서이 사용자의 항목을 검색 하 수 있어야 합니다. 현재 비디오에서 사람의 인식이 일괄 처리를 그대로 적용 하는 데 시간이 걸릴 수 있습니다.


## <a name="rename-a-person-model"></a>사용자 모델 이름 바꾸기

기본 사용자 모델을 포함 하 여 계정의 모든 사용자 모델을 바꿀 수 있습니다. 기본 사용자 모델의 이름을 바꾸면 하는 경우에 여전히 제공할 서비스 계정에 기본 사용자 모델로 합니다.

1. 이름을 바꿀 사용자 모델 옆의 목록 메뉴 단추를 클릭 합니다.
2. 클릭 **이름 바꾸기** 합니다.

    ![사용자 이름 바꾸기](./media/customize-face-model/rename-person.png)
3. 모델의 현재 이름을 클릭 하 고 새 이름을 입력 합니다.

    ![사용자 이름 바꾸기](./media/customize-face-model/rename-person2.png)
4. 바꿀 모델에 대 한 확인 단추를 클릭 합니다.

## <a name="delete-a-person-model"></a>개인 모델 삭제

사용자가 만든 계정의 모든 사용자 모델을 삭제할 수 있습니다. 그러나 기본 사용자 모델을 삭제할 수 없습니다.

1. 클릭 **삭제** 합니다.

    ![사용자 삭제](./media/customize-face-model/delete-person.png)
    
    팝업 표시 되며이 작업은 사용자 모델 및 모든 사용자 및 포함 된 파일 삭제 알림. 이 작업은 취소할 수 없습니다. 

    ![사용자 삭제](./media/customize-face-model/delete-person2.png)
1. 을 확신 하는 경우 삭제를 다시 클릭 합니다.

> [!NOTE]
> (지금 삭제)이 사용자 모델을 사용 하 여 인덱싱되지 기존 비디오에서 얼굴 비디오에 표시 되는 열의 이름을 업데이트 하는 기능을 지원 하지 않습니다. 다른 사람이 모델을 사용 하 여를 reindex 후에이 비디오에서 얼굴의 이름을 편집할 수 없게 됩니다. 사용자 모델을 지정 하지 않고 reindex 있습니다 기본 모델이 사용 됩니다. 

## <a name="manage-existing-people-in-a-person-model"></a>사용자 모델의 기존 사용자를 관리 합니다.

사용자 모델의 콘텐츠를 살펴보려면 사용자 모델의 이름 옆의 화살표를 클릭 합니다.
드롭다운 목록 보여 줍니다 모든 사용자는 특정 사용자 모델에서. 각 사용자 옆의 목록 메뉴 단추를 클릭 하면 표시를 관리, 이름 바꾸기 및 삭제 옵션입니다.  

![새 면을 직원에 게 추가](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>사용자 이름 바꾸기

1. 사용자 모델에 사람 바꾸려면 목록 메뉴 단추를 클릭 하 고 선택 **이름 바꾸기** 목록 메뉴에서.
1. 새 이름에 입력 하 고 사용자의 현재 이름을 클릭 합니다.
1. 확인 단추를 클릭 하 고 사용자 이름이 변경 됩니다.

### <a name="delete-a-person"></a>사용자 삭제

1. 사용자 모델에서 사용자를 삭제 하려면 목록 메뉴 단추를 클릭 하 고 선택 **삭제** 목록 메뉴에서.
1. 팝업이이 작업은 사용자를 삭제 하 고이 작업은 실행 취소할 수 없습니다는를 알려 줍니다.
1. 삭제를 다시 클릭이 사용자 모델에서 사용자를 제거 합니다.

### <a name="manage-a-person"></a>사용자 관리

클릭 하면 **관리**,이 사용자 모델에서 학습 되는 모든 얼굴을 표시 합니다. 이러한 면이 사람이 모델을 사용 하는 비디오에서 해당 사용자의 항목에서 또는 수동으로 업로드 한 이미지에서 제공 됩니다. 

추가 이미지를 클릭 하 여 직원에 게 더 많은 얼굴을 추가할 수 있습니다.

사용자 모델에서 사용자를 삭제 하는 사람 바꾸려면 관리 창에 사용할 수 있습니다.

## <a name="use-a-person-model-to-index-a-video"></a>사용자 모델을 사용 하 여 비디오 인덱스

비디오를 업로드 하는 동안 사용자 모델을 할당 하 여 새 비디오를 인덱싱할 사용자 모델을 사용할 수 있습니다.

사용자 모델에서 새 비디오를 사용 하려면 다음을 수행 합니다.

1. 클릭 합니다 **업로드** 페이지 맨 위에 있는 단추입니다.

    ![업로드](./media/customize-face-model/upload.png)
1. 원에 비디오 파일을 삭제 하거나 파일 찾습니다.
1. 고급 옵션 화살표를 클릭 합니다.

    ![업로드](./media/customize-face-model/upload2.png)
1. 드롭다운을 클릭 하 고 사용자가 만든 사용자 모델을 선택 합니다.

    ![업로드](./media/customize-face-model/upload3.png)
1. 페이지의 맨 아래에서 업로드 옵션을 클릭 하 고 새 비디오 사용자 모델을 사용 하 여 인덱싱됩니다.

업로드 하는 동안 사용자 모델을 지정 하지 않으면 Video Indexer 계정에 기본 사용자 모델을 사용 하는 비디오를 인덱스 됩니다.

## <a name="use-a-person-model-to-reindex-a-video"></a>비디오를 다시 인덱싱하기 사용자 모델을 사용 합니다. 

비디오 컬렉션에서 인덱스를 다시 만드는 사용자 모델을 사용 하려면으로 계정 비디오 가리키기 고 Video Indexer 홈 페이지에서 인덱스 다시 작성 하려는 비디오의 이름

편집, 삭제 및 비디오를 인덱스 다시 작성 하는 옵션이 표시 됩니다. 

1. 비디오를 인덱스 다시 작성 하는 옵션을 클릭 합니다.

    ![다시 인덱싱](./media/customize-face-model/reindex.png)

    지금 비디오를 다시 인덱싱하기 사용자 모델을 선택할 수 있습니다.
1. 드롭다운을 클릭 하 고 사용 하려는 사용자 모델을 선택 합니다. 

    ![다시 인덱싱](./media/customize-face-model/reindex2.png)

1. 클릭 합니다 **Reindex** 단추 및 비디오를 인덱싱해야 사용자 모델을 사용 합니다.

얼굴 감지 하 고 있습니다만 인덱스가 다시 작성 하는 비디오에서 인식 하는 모든 새 편집 내용이 비디오 인덱스 다시 작성 하는 데는 사용자 모델에 저장 됩니다.

## <a name="managing-people-in-your-videos"></a>비디오에서 사용자 관리

검색 된 얼굴 및 편집 및 얼굴을 삭제 하 여 인덱스는 비디오에서 인식 된 사용자를 관리할 수 있습니다.

얼굴 삭제 insights 비디오의 특정 얼굴을 제거 합니다.

얼굴 편집, 얼굴을 감지 되 고 비디오에서 인식 가능한을 이름을 바꿉니다. 비디오에서 얼굴을 편집할 때 해당 이름은 비디오를 업로드 및 인덱싱 중 할당 된 사용자 모델에 있는 사용자 항목으로 저장 됩니다.

업로드 하는 동안 사용자 모델 비디오를 할당 하지 않은 경우 편집 계정의 기본 사용자 모델에 저장 됩니다.

### <a name="edit-a-face"></a>얼굴 편집


> [!NOTE]
> 사용자 모델을 같은 이름의 두 개 이상의 다른 사람에 해당 사용자 모델을 사용 하는 동영상 내에서 해당 이름을 태그 수 없습니다. Video Indexer에서 콘텐츠 모델 사용자 지정 페이지의 사용자 탭에서 해당 이름을 공유 하는 사용자를 변경 하려면 수만 있습니다. 사용자 모델에 고유한 이름을 각 사람에 게 부여 하는 것이 좋습니다.

1. Video Indexer 웹 사이트를 찾아 로그인 합니다.
1. 계정에서 보고 편집하려는 비디오를 검색합니다.
1. 비디오에서 얼굴을 편집 하려면 Insights 탭으로 이동 하 고 창의 오른쪽 위 모서리에서 연필 아이콘을 클릭 합니다.

    ![비디오에서 얼굴 편집](./media/customize-face-model/edit-face.png)
1. 감지된 얼굴을 클릭하고 “알 수 없는 #X”(또는 이전에 얼굴에 할당된 이름)에서 이름을 변경합니다. 
1. 새 이름을 입력한 후 새 이름 옆에 있는 확인 아이콘을 클릭합니다. 이 새 이름을 저장 및 인식 하 고 모든 다른 최신 비디오 및 나중에 업로드 하는 비디오에서이 얼굴이 이름을 지정 합니다. 현재 다른 비디오에서 얼굴 인식이 일괄 처리를 그대로 적용 하는 데 시간이 걸릴 수 있습니다.

비디오를 사용 하는 사용자 모델의 기존 사용자의 이름을 사용 하 여 얼굴을 이름을 지정 하면 이미 모델에 있는 내용을 사용 하 여 해당 사용자의이 비디오에서 검색 된 얼굴 이미지 이렇게 병합 됩니다. 얼굴을 완전히 새로운 이름으로에 이름을 지정할 경우 비디오를 사용 하는 사용자 모델에 새 사용자 항목이 만들어집니다이 합니다. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>얼굴 삭제

비디오에서 검색 된 얼굴을 삭제 하려면 Insights 창으로 이동 하 고 창의 오른쪽 위 모서리에서 연필 아이콘을 클릭 합니다. 글꼴의 이름 아래에 있는 삭제 옵션을 클릭 합니다. 이렇게 하면 삭제한 얼굴이 비디오에서 제거됩니다. 표시 되는 다른 비디오에서 사람의 얼굴 검색 됩니다 계속 하지만 인덱싱 되었음을 후도 해당 비디오에서 얼굴을 삭제할 수 있습니다. Person, 명명 되지 했습니다도 계속 인덱스는 삭제 발생 하는 특히 사용자 모델에서 사용자를 삭제 하지 않는 한 비디오를 사용한 사용자 모델에 존재 합니다.

![비디오에서 얼굴을 삭제 합니다.](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>다음 단계

[API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)
