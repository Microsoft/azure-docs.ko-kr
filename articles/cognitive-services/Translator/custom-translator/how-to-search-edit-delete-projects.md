---
title: 프로젝트를 검색, 편집, 삭제하는 방법 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator는 프로젝트를 효율적으로 관리하는 다양한 방법을 제공합니다. 여러 개의 프로젝트를 만들고, 조건에 따라 검색하고, 프로젝트를 편집할 수 있습니다. 프로젝트를 삭제할 수도 있습니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896446"
---
# <a name="search-edit-and-delete-projects"></a>프로젝트 검색, 편집, 삭제

Custom Translator는 프로젝트를 효율적으로 관리하는 다양한 방법을 제공합니다. 여러 개의 프로젝트를 만들고, 조건에 따라 검색하고, 프로젝트를 편집할 수 있습니다. 프로젝트를 삭제할 수도 있습니다.  

## <a name="search-and-filter-projects"></a>프로젝트 검색 및 필터링

필터 도구를 사용하면 다양한 필터 조건에 따라 프로젝트를 검색할 수 있습니다. 프로젝트 이름, 상태, 원본 언어와 대상 언어, 프로젝트의 범주와 같은 기준으로 필터링할 수 있습니다.

1. 필터 단추를 클릭합니다.

    ![프로젝트 검색](media/how-to/how-to-search-project.png)

2. 프로젝트 이름, 원본 언어, 대상 언어, 범주 및 프로젝트 가용성 필드를 조합하여 필터링할 수 있습니다.

3. [적용]을 클릭합니다.

    ![프로젝트 검색 필터 옵션](media/how-to/how-to-search-project-filters.png)

4. 필터를 지우고 모든 프로젝트를 보려면 “지우기”를 탭합니다.

## <a name="edit-a-project"></a>프로젝트 편집

Custom Translator에서 프로젝트의 이름과 설명을 편집할 수 있습니다. 범주, 원본 언어, 대상 언어와 같은 프로젝트 메타데이터는 편집할 수 없습니다. 아래 단계에서는 프로젝트를 편집하는 방법을 설명합니다.

1. 프로젝트에 커서를 두면 나타나는 연필 아이콘을 클릭합니다.

    ![프로젝트 편집](media/how-to/how-to-edit-project.png)

2. 배포되는 모델이 없는 경우 대화 상자에서 프로젝트 이름, 프로젝트 설명, 범주 설명 및 프로젝트 레이블을 수정할 수 있습니다. 프로젝트를 만든 후에는 범주 또는 언어 쌍을 수정할 수 없습니다.

    ![프로젝트 편집 대화 상자](media/how-to/how-to-edit-project-dialog.png)

3. “저장” 단추를 클릭합니다.

## <a name="delete-a-project"></a>프로젝트 삭제

더 이상 필요하지 않은 프로젝트를 삭제할 수 있습니다. 프로젝트에 활성 상태(배포됨, 학습 제출됨, 데이터 처리 중, 배포 중 등)의 모델이 없어야 합니다. 그렇지 않으면 삭제 작업이 실패합니다. 아래에서는 프로젝트를 삭제하는 방법을 설명합니다.

1. 프로젝트 레코드에 커서를 두고 휴지통 아이콘을 클릭합니다.

   ![프로젝트 삭제](media/how-to/how-to-delete-project.png)

2. 삭제를 확인합니다. 프로젝트를 삭제하면 해당 프로젝트 내에 생성된 모든 모델도 삭제됩니다. 프로젝트 삭제는 문서에 영향을 주지 않습니다.

   ![삭제 확인 대화 상자](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>다음 단계

- [문서를 업로드](how-to-upload-document.md)하여 사용자 지정 번역 모델을 빌드합니다.
