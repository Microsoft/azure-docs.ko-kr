---
title: Azure Monitor 뷰 디자이너에서 통합 문서로 전환 가이드
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5c07d14f4a6d29b65a1ffe31fc6eaad18e8fbe26
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170904"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor 뷰 디자이너에서 통합 문서로 전환 가이드
[뷰 디자이너](view-designer.md) 는 Azure Monitor 기능으로, 차트, 목록 및 타임 라인을 사용 하 여 Log Analytics 작업 영역에서 데이터를 시각화 하는 데 도움이 되는 사용자 지정 보기를 만들 수 있습니다. 이러한 기능은 추가 기능을 제공 하는 통합 문서로 단계적으로 교체 됩니다. 이 문서에서는 기존 보기를 통합 문서로 변환 하는 프로세스에 대 한 개요를 제공 합니다.

## <a name="workbooks-overview"></a>통합 문서 개요
[통합 문서](../insights/vminsights-workbooks.md) 는 텍스트, [로그 쿼리](../log-query/query-language.md), 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합 합니다. Azure 리소스에 대 한 동일한 액세스 권한이 있는 팀 구성원도 통합 문서를 편집할 수 있습니다.

통합 문서는 다음과 같은 시나리오에 유용 합니다.

-   관심 있는 메트릭을 미리 알지 못하는 경우 (CPU 사용률, 디스크 공간, 메모리, 네트워크 종속성 등) 가상 머신의 사용 현황을 살펴보세요. 다른 사용 현황 분석 도구와 달리 통합 문서를 사용 하면 여러 종류의 시각화 및 분석을 결합 하 여 이러한 종류의 자유 형식 탐색에 유용 하 게 사용할 수 있습니다.
-   주요 카운터 및 기타 로그 이벤트에 대 한 메트릭을 표시 하 여 최근에 프로 비전 된 VM이 수행 되는 방식을 팀에 설명 합니다.
-   팀의 다른 멤버와 VM의 크기 조정 실험 결과를 공유 합니다. 텍스트를 사용 하 여 실험의 목표를 설명 하 고, 실험을 평가 하는 데 사용 되는 각 사용 메트릭 및 분석 쿼리를 표시 하 고 각 메트릭이 대상 보다 높거나 낮은 지 여부에 대 한 명확한 호출을 수행할 수 있습니다.
-   VM 사용에 대 한 가동 중단의 영향 보고, 데이터 결합, 텍스트 설명 및 향후 중단을 방지 하는 다음 단계에 대 한 논의를 보고 합니다.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>뷰 디자이너 대시보드를 통합 문서로 변환 하는 이유

뷰 디자이너는 다른 쿼리 기반 뷰 및 시각화를 생성 하는 기능을 제공 합니다. 그러나 그리드 및 타일 레이아웃 서식 지정 또는 데이터를 나타내는 대체 그래픽 선택과 같은 많은 고급 사용자 지정은 제한되어 있습니다. 뷰 디자이너는 데이터를 나타내기 위해 총 9 개의 고유 타일로 제한 됩니다.

통합 문서는 데이터의 잠재력을 최대한 활용하는 플랫폼입니다. 통합 문서는 모든 기능을 유지할 뿐만 아니라 텍스트, 메트릭, 매개 변수 등을 통해 추가 기능을 지원 합니다. 예를 들어 통합 문서를 사용 하면 사용자가 조밀한 그리드를 통합 하 고 검색 막대를 추가 하 여 데이터를 쉽게 필터링 하 고 분석할 수 있습니다. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>뷰 디자이너에서 통합 문서를 사용할 경우의 이점

* 는 로그와 메트릭을 모두 지원 합니다.
* 개별 access control 및 공유 통합 문서 보기에 대 한 개인 보기를 모두 허용 합니다.
* 탭, 크기 조정 및 크기 조정 컨트롤을 사용 하는 사용자 지정 레이아웃 옵션입니다.
* 여러 Log Analytics 작업 영역, Application Insights 응용 프로그램 및 구독 간의 쿼리 지원.
* 연결 된 차트와 시각화를 동적으로 업데이트 하는 사용자 지정 매개 변수를 사용 합니다.
* 공개 GitHub에서 템플릿 갤러리를 지원 합니다.

이 가이드에서는 일반적으로 사용 되는 여러 뷰 디자이너 뷰를 직접 다시 만드는 간단한 단계를 제공 하지만 통합 문서를 사용 하면 사용자 지정 시각화 및 메트릭을 자유롭게 만들고 디자인할 수 있습니다. 다음 스크린샷은 [작업 영역 사용 템플릿에서](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) 와 만들 수 있는 통합 문서에 대 한 예제를 보여 줍니다.


![통합 문서 응용 프로그램의 예](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>통합 문서 사용을 시작 하는 방법
통합 문서에서 열린 통합 문서는 Log Analytics 작업 영역에서 뷰 디자이너 위치 바로 아래에 있는 측면 탐색 모음의 항목으로 사용할 수 있습니다.

![통합 문서 탐색](media/view-designer-conversion-overview/workbooks-nav.png)

이를 선택 하면 작업 영역에 대해 저장 된 모든 통합 문서 및 템플릿을 나열 하는 갤러리가 표시 됩니다.

![통합 문서 갤러리](media/view-designer-conversion-overview/workbooks-gallery.png)

새 통합 문서를 시작 하려면 **빠른 시작**에서 **빈** 템플릿을 선택 하거나 위쪽 탐색 모음에서 **새로 만들기** 아이콘을 선택 합니다. 템플릿을 보거나 저장 된 통합 문서로 돌아가려면 갤러리에서 항목을 선택 하거나 검색 창에서 이름을 검색 합니다.

통합 문서를 저장 하려면 특정 제목, 구독, 리소스 그룹 및 위치를 사용 하 여 보고서를 저장 해야 합니다.
통합 문서는 같은 구독, 리소스 그룹을 사용 하 여 LA 작업 영역과 동일한 설정으로 자동 채우기를 하지만 사용자는 이러한 보고서 설정을 변경할 수 있습니다. 통합 문서는 기본적으로 *내 보고서*에 저장 되며 개별 사용자만 액세스할 수 있습니다. 공유 보고서에 직접 저장 하거나 나중에 공유할 수도 있습니다.

![통합 문서 저장](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>다음 단계

- [변환 옵션](view-designer-conversion-options.md)
