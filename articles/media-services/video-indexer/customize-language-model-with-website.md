---
title: Video Indexer 웹 사이트로 언어 모델 사용자 지정
titleSuffix: Azure Media Services
description: Video Indexer 웹 사이트를 사용 하 여 언어 모델을 사용자 지정 하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: f9eedba6c8aee5c8aab73e2ed598a4511b4c5982
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020459"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정

Video Indexer를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다.

사용자 지정 언어 모델에 대 한 자세한 개요 및 모범 사례는 [Video Indexer 사용 하 여 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조 하세요.

이 항목에 설명된 대로 Video Indexer 웹 사이트를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동 하 여 로그인 합니다.
2. 계정에서 모델을 사용자 지정 하려면 페이지의 오른쪽 위 모서리에서 **콘텐츠 모델 사용자 지정** 단추를 선택 합니다.

   ![Video Indexer에서 콘텐츠 모델 사용자 지정](./media/content-model-customization/content-model-customization.png)

3. **언어** 탭을 선택합니다.

    지원되는 언어 목록이 표시됩니다.

    ![Video Indexer 언어 모델 목록](./media/customize-language-model/customize-language-model.png)

4. 원하는 언어에서 **모델 추가**를 선택 합니다.
5. 언어 모델의 이름을 입력하고 Enter 키를 누릅니다.

    이 단계에서는 모델을 만들고 텍스트 파일을 모델에 업로드 하는 옵션을 제공 합니다.

6. 텍스트 파일을 추가 하려면 **파일 추가**를 선택 합니다. 파일 탐색기가 열립니다.

7. 텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

    언어 모델의 오른쪽에 있는 **... 단추를** 선택 하 고 **파일 추가**를 선택 하 여 텍스트 파일을 추가할 수도 있습니다.

8. 텍스트 파일 업로드가 완료 되 면 녹색 **학습** 옵션을 선택 합니다.

    ![Video Indexer에서 언어 모델 학습](./media/customize-language-model/train-model.png)

학습 프로세스는 몇 분 정도 걸릴 수 있습니다. 학습이 완료되면 모델 옆에 **학습됨**이 표시됩니다. 모델에서 파일을 미리 보고 다운로드하고 삭제할 수 있습니다.

![Video Indexer의 학습 된 언어 모델](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>새로운 비디오에서 언어 모델 사용

새 비디오에서 언어 모델을 사용 하려면 다음 작업 중 하나를 수행 합니다.

* 페이지 맨 위에서 **업로드** 단추를 선택 합니다.

    ![업로드 단추 Video Indexer](./media/customize-language-model/upload.png)

* 오디오 또는 비디오 파일을 원에 놓거나 파일을 찾습니다.

    ![미디어 파일 Video Indexer 업로드](./media/customize-language-model/upload2.png)

**비디오 원본 언어**를 선택할 수 있는 옵션이 제공 됩니다. 드롭다운을 선택 하 고 목록에서 만든 언어 모델을 선택 합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.

페이지 맨 아래에서 **업로드** 옵션을 선택 하면 언어 모델을 사용 하 여 새 비디오가 인덱싱됩니다.

### <a name="using-a-language-model-to-reindex"></a>언어 모델을 사용하여 다시 인덱싱

언어 모델을 사용하여 컬렉션의 비디오를 다시 인덱싱하려면 [Video Indexer](https://www.videoindexer.ai/) 홈페이지의 **계정 비디오**로 이동하여 다시 인덱싱할 비디오의 이름을 커서로 가리킵니다.

비디오를 편집 하 고, 비디오를 삭제 하 고, 비디오를 재 인덱싱 하는 옵션이 표시 됩니다. 비디오를 재 인덱싱 하는 옵션을 선택 합니다.

![Video Indexer를 사용 하 여 인덱스](./media/customize-language-model/reindex1.png)

비디오를 인덱싱 위한 **비디오 원본 언어** 를 선택할 수 있는 옵션이 제공 됩니다. 드롭다운을 선택 하 고 목록에서 만든 언어 모델을 선택 합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.

![비디오 소스 언어 선택-Video Indexer으로 비디오 인덱스를 바꿉니다.](./media/customize-language-model/reindex.png)

**인덱스 다시** 사용 단추를 선택 하면 사용자의 언어 모델을 사용 하 여 비디오가 인덱싱해야 됩니다.

## <a name="edit-a-language-model"></a>언어 모델 편집

이름을 변경하고 파일을 추가 및 삭제하여 언어 모델을 편집할 수 있습니다.

언어 모델에서 파일을 추가 하거나 삭제 하는 경우 녹색 **학습** 옵션을 선택 하 여 모델을 다시 학습 해야 합니다.

### <a name="rename-the-language-model"></a>언어 모델 이름 바꾸기

언어 모델의 오른쪽에 있는 줄임표 (**...**) 단추를 선택 하 고 **이름 바꾸기**를 선택 하 여 언어 모델의 이름을 변경할 수 있습니다.

새 이름을 입력하고 Enter 키를 누릅니다.

### <a name="add-files"></a>파일 추가

텍스트 파일을 추가 하려면 **파일 추가**를 선택 합니다. 파일 탐색기가 열립니다.

텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

언어 모델의 오른쪽에 있는 줄임표 (**...**) 단추를 선택 하 고 **파일 추가**를 선택 하 여 텍스트 파일을 추가할 수도 있습니다.

### <a name="delete-files"></a>파일 삭제

언어 모델에서 파일을 삭제 하려면 텍스트 파일의 오른쪽에 있는 줄임표 (**...**) 단추를 선택 하 고 **삭제**를 선택 합니다. 삭제를 실행 취소할 수 없음을 알리는 새 창이 표시 됩니다. 새 창에서 **삭제** 옵션을 선택 합니다.

이 작업은 언어 모델에서 파일을 완전히 제거합니다.

## <a name="delete-a-language-model"></a>언어 모델 삭제

계정에서 언어 모델을 삭제 하려면 언어 모델의 오른쪽에 있는 줄임표 (**...**) 단추를 선택 하 고 **삭제**를 선택 합니다.

삭제를 실행 취소할 수 없음을 알리는 새 창이 표시 됩니다. 새 창에서 **삭제** 옵션을 선택 합니다.

이 작업은 사용자 계정에서 언어 모델을 완전히 제거합니다. 삭제 된 언어 모델을 사용 하 던 비디오는 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지 합니다. 비디오를 재 인덱싱 하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 Video Indexer는 기본 모델을 사용 하 여 비디오를 인덱스를 인덱싱합니다.

## <a name="customize-language-models-by-correcting-transcripts"></a>기록을 수정 하 여 언어 모델 사용자 지정

Video Indexer는 비디오의 비디오에 대 한 사용자의 실제 수정 작업을 기반으로 언어 모델의 자동 사용자 지정을 지원 합니다.

1. 성적 증명서를 수정 하려면 계정 비디오에서 편집 하려는 비디오를 엽니다. **타임 라인** 탭을 선택 합니다.

    ![언어 모델 타임 라인 탭 사용자 지정-Video Indexer](./media/customize-language-model/timeline.png)

1. 연필 아이콘을 선택 하 여 기록에 대 한 기록을 편집 합니다.

    ![언어 모델 편집 기록을 사용자 지정-Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer는 비디오의 기록에서 자동으로 수정 되는 모든 줄을 캡처하고 "기록 편집 내용에서" 라는 텍스트 파일에 자동으로 추가 합니다. 이러한 편집은이 비디오를 인덱싱하는 데 사용 된 특정 언어 모델을 다시 학습 하는 데 사용 됩니다. 
    
    [위젯의](video-indexer-embed-widgets.md) 타임 라인에서 수행 된 편집도 포함 됩니다.
    
    이 비디오를 인덱싱할 때 언어 모델을 지정 하지 않은 경우이 비디오에 대 한 모든 편집 내용이 검색 된 비디오 언어 내에서 "Account adaptation" 라는 기본 언어 모델에 저장 됩니다.
    
    같은 줄에 여러 개의 편집 내용이 있는 경우 수정 된 줄의 마지막 버전만 언어 모델을 업데이트 하는 데 사용 됩니다.  
    
    > [!NOTE]
    > 사용자 지정에는 텍스트 수정만 사용 됩니다. 실제 단어 (예: 문장 부호 또는 공백)가 포함 되지 않은 수정은 포함 되지 않습니다.
    
1. 콘텐츠 모델 사용자 지정 페이지의 언어 탭에는 기록 수정이 표시 됩니다.

    ![언어 모델 사용자 지정-Video Indexer](./media/customize-language-model/customize.png)

   각 언어 모델에 대 한 "인 증명서 편집" 파일을 보려면 선택 하 여 엽니다.

    ![기록 편집에서-Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>다음 단계

[Api를 사용 하 여 언어 모델 사용자 지정](customize-language-model-with-api.md)
