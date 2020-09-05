---
title: Azure Log Analytics 데이터의 대시보드 만들기 및 공유 | Microsoft Docs
description: 이 자습서는 Log Analytics 대시보드가 저장된 모든 로그 쿼리를 시각화하여 환경을 보는 단일 렌즈를 제공하는 방법을 이해하는 데 도움이 됩니다.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: d38a4ba04529e70ecfd772eef7f60179041f0273
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927371"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Log Analytics 데이터의 대시보드 만들기 및 공유

Log Analytics 대시보드를 통해 저장된 모든 로그 쿼리를 시각화하고 조직의 IT 작동 데이터를 찾고, 상호 연결하며 공유하는 기능을 제공할 수 있습니다.  이 자습서는 IT 운영 지원 팀에서 액세스하는 공유 대시보드를 지원하는 데 사용할 로그 쿼리를 만드는 방법을 설명합니다.  다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 공유 대시보드 만들기
> * 성능 로그 쿼리 시각화 
> * 공유 대시보드에 로그 쿼리 추가 
> * 공유 대시보드에서 타일 사용자 지정

이 자습서의 예제를 완료하려면 [Log Analytics 작업 영역에 연결된](quick-collect-azurevm.md) 기존 가상 머신이 있어야 합니다.  
 
## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다. 

## <a name="create-a-shared-dashboard"></a>공유 대시보드 만들기
**대시보드**를 선택하여 기본 [대시보드](../../azure-portal/azure-portal-dashboards.md)를 엽니다. 대시보드가 아래 예제와는 다르게 보입니다.

![Azure 포털 대시보드](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Azure Log Analytics의 원격 분석을 포함하여, 모든 Azure 리소스에서 IT에 가장 중요한 운영 데이터를 이곳에 한데 모을 수 있습니다.  로그 쿼리 시각화를 단계별로 실행하기 전에 먼저 대시보드를 만들어 공유해 보겠습니다.  다음으로 꺾은선형 차트로 렌더링될 예제 성능 로그 쿼리에 집중하여 대시보드에 추가할 수 있습니다.  

> [!NOTE]
> 다음 차트 종류는 로그 쿼리를 사용하여 Azure 대시보드에서 지원됩니다.
> - areachart
> - columnchart
> - piechart(대시보드에서 도넛형으로 렌더링됨)
> - scatterchart
> - 시간 차트

대시보드를 만들려면 현재 대시보드 이름 옆에 있는 **새 대시보드** 단추를 선택합니다.

![Azure Portal에서 새 대시보드 만들기](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

이 작업은 새 비어 있는 프라이빗 대시보드를 만들고 대시보드의 이름을 지정하고 타일을 추가하거나 다시 정렬할 수 있는 사용자 지정 모드를 설정합니다. 대시보드 이름을 편집하고 이 자습서에 대해 *샘플 대시보드*를 지정한 후 **사용자 지정 완료**를 선택합니다.<br><br> ![사용자 지정된 Azure 대시보드 저장](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

대시보드를 만들 때 기본적으로 프라이빗입니다. 즉, 볼 수 있는 유일한 사람은 사용자입니다. 다른 사람이 볼 수 있도록 하려면 다른 대시보드 명령 옆에 나타나는 **공유** 단추를 사용합니다.

![Azure Portal에서 새 대시보드 공유](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

게시하려는 대시보드에 대한 구독 및 리소스 그룹을 선택하라는 메시지가 표시됩니다. 편의를 위해 포털의 게시 환경에서는 **대시보드**라는 리소스 그룹에 대시보드를 배치한 위치의 패턴을 안내합니다.  선택한 구독을 확인한 후 **게시**를 클릭합니다.  대시보드에 표시되는 정보에 대한 액세스는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)로 제어합니다.   

## <a name="visualize-a-log-query"></a>로그 쿼리 시각화
[Log Analytics](../log-query/get-started-portal.md)는 로그 쿼리 및 해당 결과로 작업하는 데 사용되는 전용 포털입니다. 여기에는 여러 줄, 선택적인 실행 코드, 상황에 맞는 중요한 Intellisense 및 스마트 분석에서 쿼리를 편집하는 기능이 포함됩니다. 이 자습서에서는 Log Analytics를 사용하여 성능 보기를 그래프 양식으로 만들고 이후 쿼리를 위해 저장하며 이전에 만든 공유 대시보드에 고정합니다.

Azure Monitor 메뉴에서 **로그**를 선택하여 Log Analytics를 엽니다. 비어 있는 새 쿼리로 시작합니다.

![홈 페이지](media/tutorial-logs-dashboards/homepage.png)

다음 쿼리를 입력하여 Windows 및 Linux 컴퓨터에 대한 프로세서 사용률 레코드를 Computer 및 TimeGenerated로 그룹화하고, 시각적 차트로 표시하여 반환합니다. **실행**을 클릭하여 쿼리를 실행하고 결과 차트를 봅니다.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

페이지 상단의 **저장** 단추를 선택하여 쿼리를 저장합니다.

![쿼리 저장](media/tutorial-logs-dashboards/save-query.png)

**쿼리 저장** 제어판에서 이름(예: *Azure VM - 프로세서 사용률*) 및 범주(예: **대시보드**)를 제공한 다음 *저장*을 클릭합니다.  이러한 방식으로 일반 쿼리 라이브러리를 만들어 사용하고 수정할 수 있습니다.  마지막으로, 페이지 오른쪽 상단의 **대시보드에 고정** 단추를 선택한 다음, 대시보드 이름을 선택하여 이전에 만든 공유 대시보드에 이를 고정합니다.

이제 대시보드에 쿼리가 고정되었으며 그 아래 일반적인 제목과 설명이 있음을 확인할 수 있습니다.

![Azure 대시보드 샘플](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 보는 사람이 쉽게 이해할 수 있는 의미있는 이름으로 바꾸어야 합니다.  편집 단추를 클릭하여 타일의 제목 및 부제목을 사용자 지정한 다음 **업데이트**를 클릭합니다.  변경 내용을 게시하거나 취소할지 묻는 배너가 표시됩니다.  **복사본 저장**을 클릭합니다.  

![샘플 대시보드의 구성 완료됨](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 대시보드를 만들고 로그 쿼리를 추가하는 방법을 배웠습니다.  다음 자습서에서는 로그 쿼리 결과를 기반으로 구현할 수 있는 다양한 응답에 대해 알아봅니다.  

> [!div class="nextstepaction"]
> [Log Analytics 경고로 이벤트에 응답](tutorial-response.md)
