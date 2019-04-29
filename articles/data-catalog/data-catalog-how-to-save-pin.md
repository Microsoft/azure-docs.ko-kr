---
title: Azure Data Catalog에서 검색 저장 및 데이터 자산 고정
description: 데이터 원본을 저장하기 위한 Azure 데이터 카탈로그 및 나중에 사용하기 위한 데이터 자산의 기능을 강조 표시하는 방법 문서입니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: df3220ddb80ebc329ed6b0024ca4eefd2bdfb321
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61002065"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Azure Data Catalog에서 검색 저장 및 데이터 자산 고정
## <a name="introduction"></a>소개
Azure Data Catalog는 데이터 원본 검색에 대한 기능을 제공합니다. 사용자는 신속하게 검색하고 카탈로그를 필터링하여 쉽게 당면한 작업에 대한 적절한 데이터를 찾을 수 있도록 데이터 원본을 찾고 원래 용도를 이해합니다.

그러나 동일한 데이터를 정기적으로 사용해야 하는 경우에는 어떨까요? 사용자 및 다른 사용자가 카탈로그에서 동일한 데이터 원본에 지식을 정기적으로 제공하는 경우에는 어떨까요? 이러한 상황에서 반복적인 문제는 동일한 검색이 비효율적일 수 있습니다. 저장된 검색 및 고정된 데이터 자산이 유용할 수 있습니다.

## <a name="saved-searches"></a>저장된 검색
데이터 카탈로그에 저장된 검색은 재사용 가능한 사용자 단위 검색 정의입니다. 검색어, 태그 및 다른 필터를 포함하여 검색을 정의한 다음 저장할 수 있습니다. 저장된 검색 정의를 나중에 다시 실행하여 해당 검색 조건과 일치하는 데이터 자산을 반환할 수 있습니다.

### <a name="create-a-saved-search"></a>저장된 검색 만들기
저장된 검색을 만들려면 다음을 수행합니다.
1. Azure Data Catalog 포털의 **현재 검색** 창에서 **저장**을 클릭합니다. 

    ![현재 검색 설정 저장 링크](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. 다시 사용하려는 검색 조건을 입력한 다음 **저장**을 클릭합니다.

    ![현재 검색 설정 저장된 검색 이름](./media/data-catalog-how-to-save-pin/02-name.png)

3. 대화 상자가 나타나면 저장된 검색에 이름을 입력합니다. 검색에 의해 반환되는 데이터 자산 중에 의미가 있고 데이터 자산을 설명하는 이름을 선택합니다.

### <a name="manage-saved-searches"></a>저장된 검색 관리
하나 이상의 검색을 저장한 후 **현재 검색** 상자 아래에 **저장된 검색** 옵션이 표시됩니다. 목록이 확장되는 경우 저장된 모든 검색이 표시됩니다.

 ![저장된 검색의 목록](./media/data-catalog-how-to-save-pin/03-list.png)

다음 중 하나를 수행합니다.

* 검색을 수행하려면 목록에서 저장된 검색을 선택합니다.

* 저장된 검색에 대한 관리 옵션의 목록을 보려면 검색 이름 옆의 아래쪽 화살표를 클릭합니다.

    ![저장된 검색 관리에 대한 옵션](./media/data-catalog-how-to-save-pin/04-managing.png)

* 저장된 검색에 대한 새 이름을 입력하려면 **이름 바꾸기**를 선택합니다. 검색 정의는 변경되지 않습니다.

* 목록에서 저장된 검색을 제거하려면 **삭제**를 선택한 다음 삭제를 확인합니다.

* 기본 검색으로 저장된 검색을 표시하려면 **기본값으로 저장**을 선택합니다. Azure Data Catalog 홈 페이지에서 "빈" 검색을 수행하는 경우 사용자의 기본 검색이 실행됩니다. 또한, 기본 검색으로 표시된 검색이 **저장된 검색** 목록의 위쪽에 표시됩니다.

### <a name="organizational-saved-searches"></a>조직 저장된 검색
조직의 모든 사용자는 자신의 용도에 대한 검색을 저장할 수 있습니다. 데이터 카탈로그 관리자는 조직 내의 모든 사용자에 대한 검색을 저장할 수도 있습니다. 관리자가 검색을 저장하는 경우 **회사 내에서 공유** 옵션으로 제공됩니다. 이 옵션을 선택하면 조직의 모든 사용자에 대해 저장된 검색을 공유합니다.

 ![조직 저장된 검색](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>고정된 데이터 자산
저장된 검색을 사용하여 검색 정의를 저장하고 다시 사용할 수 있습니다. 검색에서 반환되는 데이터 자산은 카탈로그 변경 내용으로 시간이 지남에 따라 변경될 수 있습니다. 데이터 자산을 고정하는 경우 검색을 사용할 필요 없이 쉽게 액세스할 수 있게 하는 특정 데이터 자산을 명시적으로 식별할 수 있습니다.

데이터 자산을 고정하는 것은 간단합니다. 데이터 자산을 고정된 목록에 추가하려면 **고정** 아이콘을 간단히 클릭합니다. 아이콘이 Azure Data Catalog 포털의 목록 뷰 중 가장 왼쪽 열에 있는 타일 뷰에서 자산 타일의 모퉁이에 표시됩니다.

![데이터 자산 고정 아이콘](./media/data-catalog-how-to-save-pin/05-pinning.png)

데이터 자산을 고정 해제하는 것은 동일하게 간단합니다. **고정 해제** 아이콘을 간단히 클릭하여 선택한 자산에 대한 설정을 전환합니다.

![데이터 자산 고정 해제 아이콘](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>내 자산 섹션
데이터 카탈로그 포털 홈 페이지는 현재 사용자에 대한 관심 자산을 표시하는 **내 자산** 섹션을 포함합니다. 이 섹션은 고정된 자산 및 저장된 검색을 모두 포함합니다.

![홈 페이지의 내 자산 섹션](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>요약
Azure Data Catalog는 사용자가 필요한 데이터 원본을 쉽게 검색할 수 있는 기능을 제공하므로 사용자와 다른 조직 멤버가 데이터를 찾는 시간이 줄고 작업하는 시간은 늘어나게 됩니다. 저장된 검색 및 고정된 데이터 자산은 이러한 핵심 기능을 작성하므로 사용자가 반복해서 작업하는 데이터 원본을 쉽게 식별할 수 있습니다.
