---
title: 프로젝트 사용자 지정 번역기를 만드는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services 사용자 지정 변환기에서 프로젝트를 만들고 관리 하는 방법을 설명 합니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3043ec0600f802a409387f0114aae5f56151da16
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510988"
---
# <a name="create-a-project"></a>프로젝트 만들기

프로젝트는 각종 모델, 문서, 테스트를 담는 컨테이너입니다. 각 프로젝트에는 올바른 언어 쌍이 있는 해당 작업 영역에 업로드된 모든 문서가 자동으로 포함됩니다.

프로젝트 만들기는 모델을 빌드할 때 수행해야 하는 첫 번째 단계입니다.

## <a name="create-a-project"></a>프로젝트 만들기:

1.  [Custom Translator](https://portal.customtranslator.azure.ai) 포털에서 [프로젝트 만들기]를 클릭합니다.

    ![프로젝트 만들기](media/how-to/how-to-create-project.png)

2.  대화 상자에 프로젝트에 대한 다음과 같은 정보를 입력합니다.

    a.  프로젝트 이름(필수): 프로젝트에 의미 있는 고유 이름을 지정합니다. 제목에 언어를 명시할 필요는 없습니다.

    b.  설명: 프로젝트에 대한 간단한 요약입니다. 설명은 Custom Translator나 사용자 지정 시스템에 영향을 주지 않지만, 여러 프로젝트를 구분하는 데 도움이 될 수 있습니다.

    다.  언어 쌍(필수): 번역할 원본 언어와 대상 언어를 선택합니다.

    d.  범주(필수): 프로젝트에 해당하는 카테고리를 선택합니다. 범주는 번역하려는 문서의 스타일과 용어를 설명합니다.

    e.  범주 설명: 이 필드에는 특정 분야나 업계에 대한 설명을 입력합니다. 예를 들어, 범주가 의학이라면 여기에는 수술, 소아과와 같은 설명을 입력할 수 있습니다. 설명은 Custom Translator나 사용자 지정 시스템에 영향을 주지 않습니다.

    f.  프로젝트 레이블: [프로젝트 레이블](workspace-and-project.md#project-labels)은 언어 쌍과 범주가 동일한 여러 프로젝트를 구분해 줍니다. 동일한 언어 쌍과 범주로 여러 개의 프로젝트를 빌드하고 각 프로젝트를 서로 다른 CategoryID를 사용하여 액세스할 계획이라면 레이블‘만’ 사용합니다.** 단일 범주의 시스템을 빌드할 계획이라면 이 필드를 사용하지 않습니다. 서로 다른 언어 쌍을 구분하는 데는 프로젝트 레이블이 유용하지도, 필요하지도 않습니다. 여러 프로젝트에서 동일한 레이블을 사용할 수 있습니다.

    ![프로젝트 만들기 대화 상자](media/how-to/how-to-create-project-dialog.png)

3.  만들기 클릭 

## <a name="view-project-details"></a>프로젝트 세부 사항 보기

Custom Translator 방문 페이지에는 작업 영역에 포함된 처음 10개의 프로젝트가 표시됩니다. 프로젝트 이름, 언어 쌍, 범주, 상태, BLEU 점수가 표시됩니다.

프로젝트를 선택하면 프로젝트 페이지에서 다음을 확인할 수 있습니다.

- CategoryID: CategoryID는 WorkspaceID, 프로젝트 레이블, 범주 코드를 연결하여 만들어집니다. CategoryID를 Text Translator API와 함께 사용하면 사용자 지정 번역을 얻을 수 있습니다. 복사 하려면 복사 아이콘을 클릭 합니다.

- 훈련 단추: 이 단추를 사용하여 [모델 훈련](how-to-train-model.md)을 시작합니다.

- 문서 추가 단추: 이 단추를 사용하여 [문서를 업로드](how-to-upload-document.md)합니다.

- 문서 필터링 단추: 이 단추를 사용하여 특정 문서를 필터링하고 검색합니다.

    ![프로젝트 세부 사항 보기](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>다음 단계

- [프로젝트를 검색, 편집, 삭제하는 방법](how-to-search-edit-delete-projects.md)을 알아보세요.
- [문서를 업로드](how-to-upload-document.md)하여 번역 모델을 빌드하는 방법을 알아보세요.
