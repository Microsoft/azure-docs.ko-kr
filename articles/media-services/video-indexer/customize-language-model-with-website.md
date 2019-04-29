---
title: Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 웹 사이트를 사용하여 언어 모델을 사용자 지정하는 방법을 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 7b81b53c03104023823bef75beb4ac6077feede7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555439"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정

Video Indexer를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다. 

사용자 지정 언어 모델의 자세한 개요와 모범 사례에 대해서는 [Video Indexer를 사용하여 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

이 항목에 설명된 대로 Video Indexer 웹 사이트를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 계정의 모델을 사용자 지정하려면 페이지의 오른쪽 위에 있는 **콘텐츠 모델 사용자 지정** 단추를 클릭합니다.

   ![콘텐츠 모델 사용자 지정](./media/content-model-customization/content-model-customization.png)

3. **언어** 탭을 선택합니다.

    지원되는 언어 목록이 표시됩니다. 

    ![언어 모델 사용자 지정](./media/customize-language-model/customize-language-model.png)

4. 원하는 언어에서 **모델 추가**를 클릭합니다.
5. 언어 모델의 이름을 입력하고 Enter 키를 누릅니다.

    모델이 생성되고 모델에 텍스트 파일을 업로드하는 옵션이 제공됩니다.
6. 텍스트 파일을 추가하려면 **파일 추가**를 클릭합니다. 파일 탐색기가 열립니다.

7. 텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

    언어 모델의 오른쪽에 있는 **...** 단추를 클릭하고 **파일 추가**를 선택하여 텍스트 파일을 추가할 수도 있습니다.
8. 텍스트 파일 업로드를 완료했으면 녹색 **학습** 옵션을 클릭합니다.

    ![언어 모델 학습](./media/customize-language-model/train-model.png)

학습 프로세스는 몇 분 정도 걸릴 수 있습니다. 학습이 완료되면 모델 옆에 **학습됨**이 표시됩니다. 모델에서 파일을 미리 보고 다운로드하고 삭제할 수 있습니다.

![학습된 언어 모델](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>새로운 비디오에서 언어 모델 사용

새로운 비디오에서 언어 모델을 사용하려면 다음 중 하나를 수행합니다.

* 페이지 맨 위에 있는 **업로드** 단추를 클릭합니다. 

    ![업로드](./media/customize-language-model/upload.png)
* 원 안에 오디오 또는 비디오 파일을 놓거나 파일을 찾습니다.

    ![업로드](./media/customize-language-model/upload2.png)

그러면 **비디오 소스 언어**를 선택할 수 있는 옵션이 제공됩니다. 드롭다운을 클릭하고 목록에서 직접 만든 언어 모델을 선택합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.

페이지 맨 아래에 있는 **업로드** 옵션을 클릭하면 새 비디오가 언어 모델을 사용하여 인덱싱됩니다.

### <a name="using-a-language-model-to-reindex"></a>언어 모델을 사용하여 다시 인덱싱

언어 모델을 사용하여 컬렉션의 비디오를 다시 인덱싱하려면 [Video Indexer](https://www.videoindexer.ai/) 홈페이지의 **계정 비디오**로 이동하여 다시 인덱싱할 비디오의 이름을 커서로 가리킵니다.

비디오 편집, 비디오 삭제 및 비디오 다시 인덱싱 옵션이 표시됩니다. 비디오를 다시 인덱싱하는 옵션을 클릭합니다.

![다시 인덱싱](./media/customize-language-model/reindex1.png)

그러면 비디오를 다시 인덱싱하는 데 사용할 **비디오 소스 언어**를 선택할 수 있는 옵션이 제공됩니다. 드롭다운을 클릭하고 목록에서 직접 만든 언어 모델을 선택합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.

![다시 인덱싱](./media/customize-language-model/reindex.png)

**다시 인덱싱** 단추를 클릭하면 비디오가 언어 모델을 사용하여 다시 인덱싱됩니다.

## <a name="edit-a-language-model"></a>언어 모델 편집

이름을 변경하고 파일을 추가 및 삭제하여 언어 모델을 편집할 수 있습니다.

언어 모델에서 파일을 추가하거나 삭제하는 경우 녹색 **학습** 옵션을 클릭하여 모델을 다시 학습해야 합니다.

### <a name="rename-the-language-model"></a>언어 모델 이름 바꾸기

언어 모델의 오른쪽에 있는 **...** 을 클릭하고 **이름 바꾸기**를 선택하여 언어 모델의 이름을 변경할 수 있습니다. 

새 이름을 입력하고 Enter 키를 누릅니다.

### <a name="add-files"></a>파일 추가

텍스트 파일을 추가하려면 **파일 추가**를 클릭합니다. 파일 탐색기가 열립니다.

텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

언어 모델의 오른쪽에 있는 **...** 단추를 클릭하고 **파일 추가**를 선택하여 텍스트 파일을 추가할 수도 있습니다.

### <a name="delete-files"></a>파일 삭제

언어 모델에서 파일을 삭제하려면 텍스트 파일의 오른쪽에 있는 **...** 단추를 클릭하고 **삭제**를 선택합니다. 그러면 새 창이 표시되고 삭제를 실행 취소할 수 없다는 메시지가 나타납니다. 새 창에서 **삭제** 옵션을 클릭합니다.

이 작업은 언어 모델에서 파일을 완전히 제거합니다.

## <a name="delete-a-language-model"></a>언어 모델 삭제

계정에서 언어 모델을 삭제하려면 언어 모델의 오른쪽에 있는 **...** 단추를 클릭하고 **삭제**를 선택합니다.

그러면 새 창이 표시되고 삭제를 실행 취소할 수 없다는 메시지가 나타납니다. 새 창에서 **삭제** 옵션을 클릭합니다.

이 작업은 사용자 계정에서 언어 모델을 완전히 제거합니다. 삭제된 언어 모델을 사용하던 모든 비디오는 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지합니다. 비디오를 다시 인덱싱하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 Video Indexer는 기본 모델을 사용하여 비디오를 다시 인덱싱합니다. 

## <a name="customize-language-models-by-correcting-transcripts"></a>대 본을 수정 하 여 언어 모델을 사용자 지정

Video Indexer 실제 수정 사용자를 기반으로 모델의 해당 비디오 기록 하는 언어의 자동 사용자 지정을 지원 합니다.

1. 대 본 수정 되도록 비디오 계정을 비디오에서 편집 하려는 경우를 엽니다. 선택 된 **타임 라인** 탭 합니다.

    ![언어 모델 사용자 지정](./media/customize-language-model/timeline.png)
1. 에 기록의 자막을 편집 하려면 연필 아이콘을 클릭 합니다. 

    ![언어 모델 사용자 지정](./media/customize-language-model/edits.png)

    Video Indexer 비디오의 기록에서 사용자가 수정 하 고, "에서 기록 편집" 라는 텍스트 파일에 자동으로 추가 하는 모든 줄을 캡처합니다. 이러한 편집이이 비디오를 인덱스에 사용 된 특정 언어 모델 다시 학습 하는 데 사용 됩니다. 
    
    이 비디오를 인덱싱할 때 언어 모델을 지정 하지 않은, 경우 모든 편집이이 비디오에 대 한 비디오의 검색된 된 언어 내에서 계정 adaptation를 호출 하는 기본 언어 모델에 저장 됩니다. 
    
    같은 줄에 여러 번의 편집 내용이 경우 언어 모델을 업데이트 하는 것에 대 한 수정 된 줄의 마지막 버전에만 사용 됩니다.  
    
    > [!NOTE]
    > 텍스트 수정만 사용자 지정에 사용 됩니다. 즉, 실제 단어 (예: 문장 또는 공백)와 관련이 없는 수정 포함 되지 않습니다. 
    
1. 콘텐츠 모델 사용자 지정 페이지의 언어 탭에 표시 하는 대 본 수정 사항이 표시 됩니다.

    ![언어 모델 사용자 지정](./media/customize-language-model/customize.png)

   파일 "에서 기록 편집" 언어 모델의 각를 살펴보려면 클릭 하 여 엽니다. 

    ![원본 대본 편집](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>다음 단계

[API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)
