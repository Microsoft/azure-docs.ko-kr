---
title: Azure Monitor 뷰 필터 | Microsoft Docs
description: Azure Monitor 보기에서 필터에는 사용자가 뷰 자체를 수정 하지 않고 특정 속성의 값으로 뷰에서 데이터를 필터링 할 수 있습니다.  이 문서에서는 필터를 사용하여 사용자 지정 보기에 추가하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 31a902302ba806889854330c6517d9f5745f1c0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551723"
---
# <a name="filters-in-azure-monitor-views"></a>Azure Monitor 뷰 필터
A **필터** 에 [Azure Monitor 뷰](view-designer.md) 뷰 자체를 수정 하지 않고 특정 속성의 값으로 뷰에서 데이터를 필터링 할 수 있습니다.  예를 들어, 보기의 사용자가 특정 컴퓨터 또는 컴퓨터 집합의 데이터에 대한 보기를 필터링하도록 허용할 수 있습니다.  단일 보기에 여러 필터를 만들어서 사용자가 여러 속성별로 필터링하도록 허용할 수 있습니다.  이 문서에서는 필터를 사용하여 사용자 지정 보기에 추가하는 방법을 설명합니다.

## <a name="using-a-filter"></a>필터 사용
뷰에 대 한 날짜 / 시간 범위를 변경할 수 있는 드롭다운을 열려면 보기의 맨 위에 있는 날짜 / 시간 범위를 클릭 합니다.

![필터 예제](media/view-designer-filters/filters-example-time.png)

보기에 대해 정의된 사용자 지정 필터를 사용하여 필터를 추가하려면 **+** 를 클릭합니다. 드롭다운에서 필터에 대한 값을 선택하거나 값을 입력합니다. **+** 를 클릭하여 필터를 계속 추가합니다. 


![필터 예제](media/view-designer-filters/filters-example-custom.png)

필터에 대한 모든 값을 제거하면 해당 필터가 더 이상 적용되지 않습니다.


## <a name="creating-a-filter"></a>필터 만들기

[보기를 편집](view-designer.md)할 때 **필터** 탭에서 필터를 만듭니다.  필터는 보기에 대해 전역적이며 보기에서 모든 부분에 적용됩니다.  

![필터 설정](media/view-designer-filters/filters-settings.png)

다음 표에서는 필터의 설정을 설명합니다.

| 설정 | 설명 |
|:---|:---|
| 필드 이름 | 필터링에 사용되는 필드의 이름입니다.  이 필드에서 요약 필드와 일치 해야 합니다 **값에 대 한 쿼리**합니다. |
| 값에 대한 쿼리 | 쿼리를 실행하여 사용자에 대한 필터 드롭다운을 채웁니다.  이 쿼리 중 하나를 사용 해야 합니다 [요약](/azure/kusto/query/summarizeoperator) 또는 [고유](/azure/kusto/query/distinctoperator) 일치 해야 하며 특정 필드에 대 한 고유한 값을 제공 하는 **필드 이름**합니다.  [정렬](/azure/kusto/query/sortoperator)을 사용하여 사용자에게 표시되는 값을 정렬할 수 있습니다. |
| 태그 | 필터를 지원하는 쿼리에서 사용되는 필터의 이름은 사용자에게도 표시됩니다. |

### <a name="examples"></a>예

다음 표에는 몇 가지 공통 필터가 포함되어 있습니다.  

| 필드 이름 | 값에 대한 쿼리 | 태그 |
|:--|:--|:--|
| Computer   | 하트비트 &#124; 고유한 컴퓨터 &#124; 컴퓨터 오름차순 정렬 | 컴퓨터 |
| EventLevelName | 이벤트 &#124; 고유한 EventLevelName | 심각도 |
| SeverityLevel | Syslog &#124; 고유한 SeverityLevel | 심각도 |
| SvcChangeType | ConfigurationChange &#124; 고유한 svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>보기 쿼리 수정

적용되는 필터의 경우 보기에서 모든 쿼리를 수정하여 선택한 값을 필터링해야 합니다.  보기에서 모든 쿼리를 수정 하지 않으면, 다음 사용자가 선택 하는 모든 값은 아무런 효과가 없습니다.

쿼리에서 필터 값을 사용하기 위한 구문은 다음과 같습니다. 

    where ${filter name}  

예를 들어 보기에 이벤트를 반환 하 고 호출 하는 필터를 사용 하는 쿼리가 _컴퓨터_, 다음 쿼리를 사용할 수 있습니다.

    Event | where ${Computers} | summarize count() by EventLevelName

심각도라는 다른 필터를 추가한 경우 두 필터를 모두 사용하는 다음 쿼리를 사용할 수 있습니다.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>다음 단계
* 사용자 지정 보기에 추가할 수 있는 [시각화 파트](view-designer-parts.md)에 대해 자세히 알아봅니다.
