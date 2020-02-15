---
title: Azure Monitor에서 로그 데이터를 분석 하는 보기 만들기 | Microsoft Docs
description: Azure Monitor에서 뷰 디자이너를 사용 하 여 Azure Portal에 표시 되는 사용자 지정 보기를 만들고 Log Analytics 작업 영역의 데이터에 대 한 다양 한 시각화를 포함할 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 포함되어 있습니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/10/2019
ms.openlocfilehash: e5d707200d1e1eab9becbb30181649525f3a5a7b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166471"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Azure Monitor에서 뷰 디자이너를 사용 하 여 사용자 지정 보기 만들기
Azure Monitor에서 뷰 디자이너를 사용 하 여 Log Analytics 작업 영역에서 데이터를 시각화 하는 데 도움이 될 수 있는 다양 한 사용자 지정 보기를 Azure Portal 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 제공됩니다.

> [!IMPORTANT]
> Azure Monitor 뷰는 단계적으로 진행 되며 추가 기능을 제공 하는 [통합 문서로](workbooks-overview.md) 대체 됩니다. 기존 보기를 통합 문서로 변환 하는 방법에 대 한 자세한 내용은 [Azure Monitor 뷰 디자이너에서 통합 문서 전환 가이드를](view-designer-conversion-overview.md) 참조 하세요.

뷰 디자이너에 대한 자세한 내용은 다음을 참조하세요.

* [타일 참조](view-designer-tiles.md): 사용자 지정 보기에서 사용 가능한 각 타일의 설정에 대한 참조 가이드를 제공합니다.
* [시각화 요소 참조](view-designer-parts.md): 사용자 지정 보기에서 사용 가능한 시각화 요소의 설정에 대한 참조 가이드를 제공합니다.


## <a name="concepts"></a>개념
보기는 Azure Portal의 Azure Monitor **개요** 페이지에 표시 됩니다. **인사이트** 섹션에서 **기타**를 클릭하여 **Azure Monitor** 메뉴에서 이 페이지를 엽니다. 각 사용자 지정 보기의 타일은 사전순으로 표시 되 고, 모니터링 솔루션에 대 한 타일이 동일한 작업 영역에 설치 됩니다.

![개요 페이지](media/view-designer/overview-page.png)

뷰 디자이너로 만든 보기에는 다음 표에 설명된 요소가 포함됩니다.

| 부분 | Description |
|:--- |:--- |
| 타일 | Azure Monitor **개요** 페이지에 표시 됩니다. 각 타일에는 해당 타일이 나타내는 사용자 지정 보기의 시각적 요약이 표시됩니다. 각 타일 유형은 레코드의 다른 시각화를 제공합니다. 사용자 지정 보기를 표시하려면 타일을 선택합니다. |
| 사용자 지정 보기 | 타일을 선택하면 표시됩니다. 각 보기는 시각화 요소를 하나 이상 포함합니다. |
| 시각화 요소 | 하나 이상의 [로그 쿼리](../log-query/log-query-overview.md)를 기반으로 Log Analytics 작업 영역에 데이터 시각화를 제공 합니다. 대부분의 요소는 대략적인 시각화를 제공하는 헤더와 상위 결과를 표시하는 목록을 포함합니다. 각 요소 유형은 Log Analytics 작업 영역에 레코드의 다른 시각화를 제공합니다. 파트에서 요소를 선택 하 여 자세한 레코드를 제공 하는 로그 쿼리를 수행 합니다. |

## <a name="required-permissions"></a>필요한 사용 권한
보기를 만들거나 수정 하려면 Log Analytics 작업 영역에서 [참가자 수준 이상의 권한이](manage-access.md#manage-access-using-azure-permissions) 필요 합니다. 이 권한이 없으면 뷰 디자이너 옵션이 메뉴에 표시 되지 않습니다.


## <a name="work-with-an-existing-view"></a>기존 보기로 작업
뷰 디자이너로 만든 보기에는 다음 옵션이 표시됩니다.

![개요 메뉴](media/view-designer/overview-menu.png)

옵션은 다음 표에 설명되어 있습니다.

| 옵션 | Description |
|:--|:--|
| 새로 고침   | 최신 데이터로 보기를 새로 고칩니다. | 
| 로그      | 로그 쿼리를 사용 하 여 데이터를 분석 하는 [Log Analytics](../log-query/portals.md) 을 엽니다. |
| 편집       | 뷰 디자이너에서 보기를 열어서 내용과 구성을 편집합니다.  |
| 복제      | 새 보기를 만든 후 뷰 디자이너에서 엽니다. 새 보기의 이름은 원래 이름과 동일하지만 복사본이 추가됩니다. |
| 날짜 범위 | 보기에 포함된 데이터의 날짜와 시간 범위 필터를 설정합니다. 이 날짜 범위는 보기의 쿼리에서 데이터 범위가 설정되기 전에 적용됩니다.  |
| +          | 보기에 대해 정의된 사용자 지정 필터를 정의합니다. |


## <a name="create-a-new-view"></a>새 보기 만들기
Log Analytics 작업 영역의 메뉴에서 **뷰 디자이너**를 선택하여 뷰 디자이너에서 새 보기를 만들 수 있습니다.

![뷰 디자이너 타일](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>뷰 디자이너 작업
뷰 디자이너를 사용하여 새 보기를 만들거나 기존 보기를 편집합니다. 

뷰 디자이너에는 세 개의 창이 있습니다. 
* **디자인**: 만들거나 편집하는 사용자 지정 보기가 포함됩니다. 
* **컨트롤**: **디자인** 창에 추가하는 타일과 요소가 포함됩니다. 
* **속성**: 타일 또는 선택한 요소의 속성이 표시됩니다.

![뷰 디자이너](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>보기 타일 구성
사용자 지정 보기에는 타일 하나만 있을 수 있습니다. 현재 타일을 보거나 다른 타일을 선택하려면 **컨트롤** 창에서 **타일** 탭을 선택합니다. **속성** 창에 현재 타일의 속성이 표시됩니다. 

[타일 참조](view-designer-tiles.md)의 정보에 따라 타일 속성을 구성한 다음, **적용**을 클릭하여 변경 내용을 저장합니다.

### <a name="configure-the-visualization-parts"></a>시각화 요소 구성
보기에는 시각화 요소가 얼마든지 포함될 수 있습니다. 보기에 요소를 추가하려면 **보기** 탭을 선택한 다음, 시각화 요소를 선택합니다. **속성** 창에 선택한 요소의 속성이 표시됩니다. 

[시각화 요소 참조](view-designer-parts.md)의 정보에 따라 보기 속성을 구성한 다음, **적용**을 클릭하여 변경 내용을 저장합니다.

보기에는 시각화 요소의 행 하나만 있습니다. 기존 요소를 새 위치로 끌어서 다시 정렬할 수 있습니다.

요소의 오른쪽 위에 있는 **X**를 클릭하면 보기에서 시각화 요소를 제거할 수 있습니다.


### <a name="menu-options"></a>메뉴 옵션
편집 모드에서 보기 작업을 수행하는 옵션이 다음 표에 설명되어 있습니다.

![편집 메뉴](media/view-designer/edit-menu.png)

| 옵션 | Description |
|:--|:--|
| 저장        | 변경 내용을 저장하고 보기를 닫습니다. |
| 취소      | 변경 내용을 무시하고 보기를 닫습니다. |
| 보기 삭제 | 보기를 삭제합니다. |
| 내보내기      | 다른 작업 영역으로 가져올 수 있는 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md)으로 보기를 내보냅니다. 파일 이름은 보기 이름이며 확장명은 *omsview*입니다. |
| 가져오기      | 다른 작업 영역에서 내보낸 *omsview* 파일을 가져옵니다. 이렇게 하면 기존 보기의 구성을 덮어씁니다. |
| 복제       | 새 보기를 만든 후 뷰 디자이너에서 엽니다. 새 보기의 이름은 원래 이름과 동일하지만 복사본이 추가됩니다. |

## <a name="next-steps"></a>다음 단계
* 사용자 지정 보기에 [타일](view-designer-tiles.md) 추가
* 사용자 지정 보기에 [시각화 요소](view-designer-parts.md) 추가
