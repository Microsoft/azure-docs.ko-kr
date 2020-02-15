---
title: 뷰 디자이너와 통합 문서 타일 변환 Azure Monitor
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5bb02edce4a3aef30f8f9528a846c99d6d8d3b39
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170865"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor 뷰 디자이너 타일 변환
[뷰 디자이너](view-designer.md) 는 Azure Monitor 기능으로, 차트, 목록 및 타임 라인을 사용 하 여 Log Analytics 작업 영역에서 데이터를 시각화 하는 데 도움이 되는 사용자 지정 보기를 만들 수 있습니다. 이러한 기능은 추가 기능을 제공 하는 통합 문서로 단계적으로 교체 됩니다. 이 문서에서는 다양 한 타일을 통합 문서로 변환 하는 방법에 대해 자세히 설명 합니다.

## <a name="donut--list-tile"></a>도넛형 & 목록 타일

![도넛형 목록](media/view-designer-conversion-tiles/donut-list.png)

통합 문서에서 도넛형 & 목록 타일을 다시 만들려면 두 가지 별도의 시각화가 필요 합니다. 도넛형 부분에는 두 가지 옵션이 있습니다.
두 시작 모두 **쿼리 추가** 를 선택 하 고 뷰 디자이너의 원래 쿼리를 셀에 붙여 넣습니다.

**옵션 1:** **시각화** 드롭다운에서 **원형 차트** 를 선택 합니다. ![원형 차트 시각화 메뉴](media/view-designer-conversion-tiles/pie-chart.png)

**옵션 2:** **시각화** 드롭다운에서 **Set by query** 를 선택 하 고 쿼리에 `| render piechart`를 추가 합니다.

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

**예제**

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

업데이트 된 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

목록을 만들고 스파크 라인을 설정 하려면 [일반 작업](view-designer-conversion-tasks.md)에 대 한 문서를 참조 하세요.

다음은 도넛형 & 목록 타일이 통합 문서에서 어떻게 다시 해석 수 있는 방법의 예입니다.

![도넛형 통합 문서 목록](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>꺾은선형 차트 & 목록 타일
![꺾은선형 차트 목록](media/view-designer-conversion-tiles/line-list.png) 

꺾은선형 차트 부분을 다시 만들려면 다음과 같이 쿼리를 업데이트 합니다.

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Type

Updated query
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

꺾은선형 차트를 시각화 하는 데는 두 가지 옵션이 있습니다.

**옵션 1:** **시각화** 드롭다운에서 **꺾은선형 차트** 를 선택 합니다.
 
 ![꺾은선형 차트 메뉴](media/view-designer-conversion-tiles/line-visualization.png)

**옵션 2:** **시각화** 드롭다운에서 **Set by query** 를 선택 하 고 쿼리에 `| render linechart`를 추가 합니다.

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

**예제**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

목록을 만들고 스파크 라인을 설정 하려면 [일반 작업](view-designer-conversion-tasks.md)에 대 한 문서를 참조 하세요.

다음은 꺾은선형 차트 & 목록 타일이 통합 문서에서 다시 해석 수 있는 방법의 예입니다.

![꺾은선형 차트 목록 통합 문서](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>숫자 & 목록 타일

 ![타일 목록](media/view-designer-conversion-tiles/tile-list-example.png)

숫자 타일의 경우 다음과 같이 쿼리를 업데이트 합니다.

원래 쿼리
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

업데이트 된 쿼리
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

시각화 드롭다운을 **타일로** 변경한 다음 **타일 설정**을 선택 합니다.
 ![타일 시각화](media/view-designer-conversion-tiles/tile-visualization.png)

**제목** 섹션을 비워 두고 **왼쪽**을 선택 합니다. **사용 열:** 을 **개수**로, **열 렌더러** 를 **큰**값으로 변경 합니다.

![타일 설정](media/view-designer-conversion-tiles/tile-settings.png)

 
목록을 만들고 스파크 라인을 설정 하려면 [일반 작업](view-designer-conversion-tasks.md)에 대 한 문서를 참조 하세요.

다음은 통합 문서에서 숫자 & 목록 타일이 어떻게 다시 해석 수 있는 방법의 예입니다.

![번호 목록 통합 문서](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>타임라인 및 목록

 ![타임 라인 목록](media/view-designer-conversion-tiles/time-list.png)

타임 라인의 경우 다음과 같이 쿼리를 업데이트 합니다.

원래 쿼리
```KQL
search * 
| sort by TimeGenerated desc
```

업데이트 된 쿼리
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

쿼리를 가로 막대형 차트로 시각화 하는 두 가지 옵션이 있습니다.

**옵션 1:** **시각화** 드롭다운에서 **가로 막대형 차트** 를 선택 합니다. ![가로 막대형 차트 시각화](media/view-designer-conversion-tiles/bar-visualization.png)
 
**옵션 2:** **시각화** 드롭다운에서 **Set by query** 를 선택 하 고 쿼리에 `| render barchart`를 추가 합니다.

 ![시각화 메뉴](media/view-designer-conversion-tiles/set-by-query.png)

 
목록을 만들고 스파크 라인을 설정 하려면 [일반 작업](view-designer-conversion-tasks.md)에 대 한 문서를 참조 하세요.

다음은 통합 문서에서 timeline & 목록 타일이 다시 해석 될 수 있는 방법의 예입니다.

![타임 라인 목록 통합 문서](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>다음 단계

- [통합 문서 전환에 대 한 뷰 디자이너 개요](view-designer-conversion-overview.md)
