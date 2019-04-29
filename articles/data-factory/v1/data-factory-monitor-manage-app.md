---
title: 데이터 파이프라인 모니터링 및 관리 - Azure | Microsoft Docs
description: 모니터링 및 관리 앱을 사용하여 Azure Data Factory 및 파이프라인을 모니터링하고 관리하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826750"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>모니터링 및 관리 앱을 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리
> [!div class="op_single_selector"]
> * [Azure 포털/Azure PowerShell 사용](data-factory-monitor-manage-pipelines.md)
> * [모니터링 및 관리 앱 사용](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory 파이프라인 모니터링 및 관리](../monitor-visually.md)를 참조하세요.

이 문서는 모니터링 및 관리 앱을 사용하여 Data Factory 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 다음 비디오를 시청하여 애플리케이션 사용을 시작할 수 있습니다.

> [!NOTE]
> 비디오에 표시된 사용자 인터페이스는 포털에 표시된 것과 정확하게 일치하지 않을 수 있습니다. 약간 더 오래되었지만 개념은 동일합니다. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>모니터링 및 관리 앱 시작
모니터링 및 관리 앱을 시작하려면 사용자 데이터 팩터리의 **Data Factory** 블레이드에서 **모니터링 및 관리** 타일을 클릭합니다.

![Data Factory 홈페이지의 모니터링 타일](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

모니터링 및 관리 앱이 별도 창에 열리는 것을 볼 수 있습니다.  

![모니터링 및 관리 앱](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> 웹 브라우저가 "권한 부여..." 상태로 중지된 것을 확인하면 **Block third-party cookies and site data**(타사 쿠키 및 사이트 데이터 차단) 확인란 선택을 해제하거나 선택된 상태로 두고 **login.microsoftonline.com**에 대한 예외를 만든 다음 앱을 다시 열어봅니다.


가운데 창의 작업 창 목록에는 작업의 각 실행에 대한 작업 창이 표시됩니다. 예를 들어 5시간 동안 매시간 실행되도록 예약된 작업이 있는 경우 5개의 데이터 조각과 연결된 5개 작업 창이 표시됩니다. 아래쪽의 목록에 작업 창이 표시되지 않는 경우 다음을 수행합니다.
 
- 맨 위에 있는 **시작 시간** 및 **종료 시간** 필터를 업데이트하여 파이프라인의 시작 및 종료 시간을 일치시킨 다음 **적용** 단추를 클릭합니다.  
- 작업 창 목록은 자동으로 고쳐지지 않습니다. **작업 창** 목록의 도구 모음에서 **새로 고침** 단추를 클릭합니다.  

이러한 단계를 테스트할 데이터 팩터리 애플리케이션이 없는 경우 자습서: [데이터 팩터리를 사용하여 Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 수행합니다.

## <a name="understand-the-monitoring-and-management-app"></a>모니터링 및 관리 앱 이해
왼쪽에는 다음과 같은 세 가지 탭이 있습니다. **리소스 탐색기**, **모니터링 보기** 및 **경고**. 첫 번째 탭(**리소스 탐색기**)은 기본적으로 선택됩니다.

### <a name="resource-explorer"></a>리소스 탐색기
다음이 표시됩니다.

* 왼쪽 창에 리소스 탐색기 **트리 뷰**.
* 가운데 창의 맨 위쪽에 **다이어그램 보기**.
* 가운데 창의 아래쪽에 **활동 기간** 목록.
* 오른쪽 창에 **속성**, **작업 창 탐색기** 및 **스크립트** 탭.

리소스 탐색기에서 데이터 팩터리의 모든 리소스(파이프라인, 데이터 세트, 연결된 서비스)를 트리 뷰로 볼 수 있습니다. 리소스 탐색기에서 개체를 선택하는 경우:

* 연결된 Data Factory 엔터티가 다이어그램 뷰에 강조 표시됩니다.
* [연관된 활동 기간](data-factory-scheduling-and-execution.md)이 아래쪽 활동 기간 목록에 강조 표시됩니다.  
* 선택한 개체의 속성이 오른쪽 창의 속성 창에 표시됩니다.
* 해당하는 경우 선택된 개체의 JSON 정의가 표시됩니다. 예: 연결된 서비스, 데이터 세트 또는 파이프라인.

![리소스 탐색기](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

활동 기간에 대한 자세한 개념 정보는 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

### <a name="diagram-view"></a>다이어그램 뷰
데이터 팩터리의 다이어그램 뷰에는 데이터 팩터리와 그 자산을 모니터링하고 관리하기 위한 단일 창이 제공됩니다. 다이어그램 뷰에서 Data Factory 엔터티(데이터 세트/파이프라인)를 선택하는 경우:

* 데이터 팩터리 엔터티가 트리 뷰에서 선택됩니다.
* 연관된 활동 기간이 활동 기간 목록에 강조 표시됩니다.
* 선택한 개체의 속성이 속성 창에 표시됩니다.

파이프라인을 사용하도록 설정한 경우(일시 중지된 상태가 아님) 녹색 선으로 표시됩니다.

![파이프라인 실행](./media/data-factory-monitor-manage-app/PipelineRunning.png)

다이어그램 보기에서 선택하고 명령 모음에서 단추를 사용하여 파이프라인을 일시 중지, 다시 시작 또는 종료할 수 있습니다.

![명령 모음에서 일시 중지/다시 시작](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
다이어그램 뷰에 파이프라인에 대한 세 개의 명령 모음 단추가 있습니다. 두 번째 단추를 사용하여 파이프라인을 일시 중지할 수 있습니다. 일시 중지할 경우 현재 실행 중인 활동을 종료하지 않고 완료될 때까지 계속할 수 있습니다. 세 번째 단추는 파이프라인을 일시 중지하고 실행 중인 기존의 활동을 종료합니다. 첫 번째 단추는 파이프라인을 다시 시작합니다. 파이프라인이 일시 중지되면 파이프라인 색이 변경됩니다. 예를 들어, 일시 중지된 파이프라인은 다음 그림과 같이 표시됩니다. 

![파이프라인 일시 중지](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Ctrl 키를 사용하여 두 개 이상의 파이프라인을 다중 선택할 수 있습니다. 명령 모음 단추를 사용하여 여러 파이프라인을 한 번에 일시 중지/다시 시작할 수 있습니다.

파이프라인을 마우스 오른쪽 단추로 클릭하고 파이프라인을 일시 중단, 다시 시작 또는 종료하는 옵션을 선택할 수도 있습니다. 

![파이프라인에 대한 상황에 맞는 메뉴](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

**파이프라인 열기** 옵션을 선택하여 파이프라인의 모든 작업을 확인합니다. 

![파이프라인 열기 메뉴](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

열린 파이프라인 뷰에서 파이프라인의 모든 작업이 표시됩니다. 이 예제에는 다음과 같은 하나의 작업만 있습니다. 복사 작업. 

![열린 파이프라인](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

이전 보기로 돌아가려면 맨 위에서 breadcrumb 메뉴의 데이터 팩터리 이름을 클릭합니다.

파이프라인 뷰에서 출력 데이터 세트를 선택할 경우 또는 출력 데이터 세트 위로 마우스를 이동하는 경우, 해당 데이터 세트에 대한 활동 기간 팝업 창이 표시됩니다.

![활동 기간 팝업 창](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

활동 기간을 클릭하면 오른쪽 창의 **속성** 창에서 그에 대한 자세한 내용을 볼 수 있습니다.

![활동 기간 속성](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

오른쪽 창에서 **활동 기간 탐색기** 탭으로 전환하여 자세한 내용을 봅니다.

![작업 창 탐색기](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

**Attempts**(시도) 섹션에서 활동의 각 실행 시도에 대한 **확인된 변수**도 볼 수 있습니다.

![확인된 변수](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

**스크립트** 탭으로 전환하여 선택한 개체에 대한 JSON 스크립트 정의를 참조하세요.   

![스크립트 탭](./media/data-factory-monitor-manage-app/ScriptTab.png)

세 위치에서 작업 창을 확인할 수 있습니다.

* 다이어그램 뷰에서 활동 기간 팝업(가운데 창).
* 오른쪽 창에서 활동 기간 탐색기.
* 아래쪽 창에서 활동 기간 목록.

활동 기간 팝업 및 활동 기간 탐색기에서 왼쪽 및 오른쪽 화살표를 사용하여 이전 주 및 다음 주로 스크롤할 수 있습니다.

![활동 기간 탐색기 왼쪽/오른쪽 화살표](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

다이어그램 보기 하단에는 다음과 같은 단추가 표시됩니다. 확대, 축소, 크기에 맞게, 100% 확대/축소, 레이아웃 잠금. **Lock layout**(레이아웃 잠금) 단추는 다이어그램 뷰에서 테이블 및 파이프라인을 실수로 이동하지 않도록 방지합니다. 기본적으로 해제된 상태입니다. 기능을 해제하고 다이어그램에서 엔터티를 이동할 수 있습니다. 해제한 경우 마지막 단추를 사용하여 테이블 및 파이프라인을 자동으로 배치할 수 있습니다. 마우스 휠을 사용하여 확대하거나 축소할 수도 있습니다.

![다이어그램 뷰 확대/축소 명령](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>활동 기간 목록
가운데 창의 맨 아래에 있는 활동 기간 목록에는 리소스 탐색기 또는 다이어그램 뷰에서 선택한 데이터 세트에 대한 모든 활동 기간이 표시됩니다. 기본적으로 목록은 내림차순이며, 창 위쪽에 최신 활동 기간이 표시됩니다.

![활동 기간 목록](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

이 목록은 자동으로 새로 고쳐지지 않으며 수동으로 새로 고치려면 도구 모음에서 새로 고침 단추를 사용합니다.  

활동 기간은 다음 상태 중 하나일 수 있습니다.

<table>
<tr>
    <th align="left">상태</th><th align="left">하위 상태</th><th align="left">설명</th>
</tr>
<tr>
    <td rowspan="8">대기</td><td>ScheduleTime</td><td>활동 기간을 실행할 시간이 아직 되지 않습니다.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>업스트림 종속성이 준비되지 않습니다.</td>
</tr>
<tr>
<td>ComputeResources</td><td>계산 리소스를 사용할 수 없습니다.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>모든 활동 인스턴스는 다른 작업 창을 실행하고 있습니다.</td>
</tr>
<tr>
<td>ActivityResume</td><td>활동이 일시 중지되어 재개될 때까지 활동 기간을 실행할 수 없습니다.</td>
</tr>
<tr>
<td>Retry</td><td>활동 실행을 다시 시도 중입니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사가 아직 시작되지 않았습니다.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>유효성 감사 다시 시도를 대기 중입니다.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>유효성 검사 중</td><td>유효성 검사가 진행 중입니다.</td>
</tr>
<td>-</td>
<td>작업 창을 처리 중입니다.</td>
</tr>
<tr>
<td rowspan="4">실패</td><td>TimedOut</td><td>활동 실행이 활동에서 허용하는 것보다 오래 걸렸습니다.</td>
</tr>
<tr>
<td>Canceled</td><td>활동 기간이 사용자 작업으로 인해 취소되었습니다.</td>
</tr>
<tr>
<td>유효성 검사</td><td>유효성 검사가 실패했습니다.</td>
</tr>
<tr>
<td>-</td><td>활동 창이 생성되거나 유효성이 검사되지 못했습니다.</td>
</tr>
<td>Ready</td><td>-</td><td>작업 창을 사용할 준비가 되었습니다.</td>
</tr>
<tr>
<td>생략</td><td>-</td><td>활동 기간이 처리되지 않습니다.</td>
</tr>
<tr>
<td>없음</td><td>-</td><td>활동 기간이 다른 상태와 함께 존재하는 데 사용되었지만 다시 설정되었습니다.</td>
</tr>
</table>


목록에서 활동 기간을 클릭하면 오른쪽에 있는 **속성** 창 또는 **활동 기간 탐색기**에 자세한 내용이 표시됩니다.

![작업 창 탐색기](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>작업 창 새로 고침
세부 정보는 자동으로 새로 고쳐지지 않으므로 명령 모음에서 새로 고침 단추(두 번째 단추)를 사용하여 수동으로 활동 기간 목록을 새로 고칩니다.  

### <a name="properties-window"></a>속성 창
속성 창은 모니터링 및 관리 앱의 가장 오른쪽 창에 있습니다.

![속성 창](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

리소스 탐색기(트리 뷰), 다이어그램 뷰 또는 활동 기간 목록에서 선택한 항목에 대한 속성이 표시됩니다.

### <a name="activity-window-explorer"></a>작업 창 탐색기
**활동 기간 탐색기** 창은 모니터링 및 관리 앱의 가장 오른쪽 창에 있습니다. 활동 기간 팝업 또는 활동 기간 목록에 선택된 활동 기간에 대한 세부 정보가 표시됩니다.

![작업 창 탐색기](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

위쪽의 일정 보기에서 클릭하여 다른 작업 창으로 전환할 수 있습니다. 또한 위쪽에서 왼쪽 화살표/오른쪽 화살표 단추를 사용하여 이전 주/다음 주의 활동 기간을 볼 수 있습니다.

아래쪽 창에서 도구 모음 단추를 사용하여 활동 기간을 다시 실행하거나 창의 세부 정보를 새로 고칠 수 있습니다.

### <a name="script"></a>스크립트
**스크립트** 탭을 사용하여 선택한 Data Factory 엔터티(연결된 서비스, 데이터 세트 또는 파이프라인)의 JSON 정의를 볼 수 있습니다.

![스크립트 탭](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>시스템 뷰 사용
모니터링 및 관리 앱은 데이터 팩터리에 대해 최근/실패한/진행 중인 활동 기간을 보여주는 미리 작성된 시스템 뷰를 포함합니다(**Recent activity windows**(최근 활동 기간), **Failed activity windows**(실패한 활동 기간), **In-Progress activity windows**(진행 중인 활동 기간)).

왼쪽에서 **모니터링 뷰** 탭을 클릭하여 전환합니다.

![모니터링 뷰 탭](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

현재는 지원되는 세 가지 시스템 뷰가 있습니다. (가운데 창의 맨 아래에 있는)활동 기간 목록에서 최근 활동 기간, 실패한 활동 기간 또는 진행 중인 활동 기간을 보려면 옵션을 선택합니다.

**Recent activity windows**(최근 활동 기간) 옵션을 선택하면 **마지막 시도 시간**의 내림차순으로 최근 활동 기간이 모두 표시됩니다.

**실패한 작업 창** 뷰를 사용하여 목록에서 실패한 작업 창을 모두 볼 수 있습니다. **속성** 창 또는 **활동 기간 탐색기**에서 이에 대한 자세한 내용을 보려면 목록에서 실패한 활동 기간을 선택하세요. 실패한 작업 창에 대한 로그를 다운로드할 수도 있습니다.

## <a name="sort-and-filter-activity-windows"></a>활동 기간 정렬 및 필터링
명령 모음에서 **시작 시간** 및 **종료 시간** 설정을 변경하여 작업 창을 필터링합니다. 시작 시간 및 종료 시간을 변경한 후에 활동 기간 목록을 새로 고치려면 종료 시간 옆에 있는 단추를 클릭합니다.

![시작 및 종료 시간](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> 현재 모니터링 및 관리 앱의 모든 시간은 UTC 형식입니다.
>
>

**작업 창 목록**에서 열의 이름을 클릭합니다(예: 상태).

![활동 기간 목록 열 메뉴](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

다음을 수행할 수 있습니다.

* 오름차순으로 정렬.
* 내림차순으로 정렬.
* 하나 이상의 값으로 필터링(준비, 대기 중 등).

열에 필터를 지정할 때 해당 열에 대해 사용하도록 설정된 필터 단추가 표시되어 열의 값이 필터링된 값임을 나타냅니다.

![활동 기간 목록의 열에서 필터링](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

동일한 팝업 창을 사용하여 필터를 지울 수 있습니다. 활동 기간 목록에 대한 필터를 모두 지우려면 명령 모음에서 필터 지우기 단추를 클릭합니다.

![활동 기간 목록의 모든 필터 지우기](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>배치 작업 수행
### <a name="rerun-selected-activity-windows"></a>선택한 작업 창 다시 실행
활동 기간을 선택하고 첫 번째 명령 모음 단추의 아래쪽 화살표를 클릭한 다음 **다시 실행** / **Rerun with upstream in pipeline**(파이프라인에서 업스트림으로 다시 실행)을 선택합니다. **Rerun with upstream in pipeline**(파이프라인에서 업스트림으로 다시 실행) 옵션을 선택하면 모든 업스트림 활동 기간이 다시 실행됩니다.
    ![작업 창 다시 실행](./media/data-factory-monitor-manage-app/ReRunSlice.png)

또한 목록에서 여러 개의 작업 창을 선택하고 동시에 다시 실행할 수 있습니다. 상태를 기준으로 작업 창을 필터링(예: **실패**)한 다음, 작업 창에 실패를 일으키는 문제를 해결한 후에 실패한 작업 창을 다시 실행할 수 있습니다. 목록에서 작업 창을 필터링하는 자세한 내용은 다음 섹션을 참조합니다.  

### <a name="pauseresume-multiple-pipelines"></a>여러 파이프라인 일시 중지/다시 시작
Ctrl 키를 사용하여 두 개 이상의 파이프라인을 다중 선택할 수 있습니다. 명령 모음 단추(아래 이미지에 빨간색 사각형으로 강조 표시됨)를 사용하여 일시 중지/다시 시작할 수 있습니다.

![명령 모음에서 일시 중지/다시 시작](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
