---
title: Azure Application Insights에서 경고 설정 | Microsoft Docs
description: 느린 응답 시간, 예외 및 웹앱의 기타 성능 또는 사용 변경에 대한 알림을 받습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: eb8e98f66d000290ce7eb07d3d73e82fbc43514a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693377"
---
# <a name="set-alerts-in-application-insights"></a>Application Insights에서 경고 설정
[Azure Application Insights][start]는 웹앱의 성능 및 사용 메트릭이 변경되면 사용자에게 경고할 수 있습니다. 

Application Insights는 [다양한 플랫폼][platforms]에서 라이브 앱을 모니터링하여 성능 문제를 진단하고 사용 패턴을 이해할 수 있습니다.

여러 유형의 경고는

* [**메트릭 경고** ](../../azure-monitor/platform/alerts-metric-overview.md) 메트릭이 응답 시간, 예외 수, CPU 사용량 또는 페이지 보기와 같은 일정 기간 동안에 대 한 임계값을 초과 하는 경우 알려줍니다.
* [**로그 경고** ](../../azure-monitor/platform/alerts-unified-log.md) 는 경고를 설명 하는 경고 신호를 사용자 지정 Kusto 쿼리 기반 합니다.
* [**웹 테스트**][availability]는 인터넷에서 사이트를 사용할 수 없거나 응답 속도가 느려지면 그 사실을 알려 줍니다. [자세한 정보][availability].
* [**사전 진단**](../../azure-monitor/app/proactive-diagnostics.md)은 비정상적인 성능 패턴에 대해 알려 주기 위해 자동으로 구성됩니다.

## <a name="set-a-metric-alert"></a>메트릭 경고 설정
경고 규칙 탭을 열고 추가 단추를 사용 합니다.

![경고 규칙 탭에서 경고 추가 선택 합니다. 측정할 리소스로 앱을 설정하고, 경고의 이름을 입력하고, 메트릭을 선택합니다.](./media/alerts/01-set-metric.png)

* 다른 속성에 앞서 리소스를 설정합니다. **"(구성 요소)" 리소스 선택** 성능 또는 사용 메트릭에 대한 경고를 설정하려는 경우.
* 경고에 입력하는 이름은 리소스 그룹(애플리케이션 아님) 내에서 고유한 이름이어야 합니다.
* 임계값을 입력하라는 단위에 주의하세요.
* "메일 소유자..." 확인란을 선택하면 이 리소스 그룹에 액세스하는 모든 사용자에게 메일로 경고가 보내집니다. 사용자 집합을 확장하려면 [리소스 그룹 또는 구독](../../azure-monitor/app/resources-roles-access-control.md) (리소스 아님)에 추가합니다.
* "추가 메일"을 지정하면 "메일 소유자..." 확인란의 선택 여부와 상관없이 이러한 개인 또는 그룹에게 경고가 보내집니다. 
* 경고에 응답하는 웹앱을 설정한 경우 [웹후크 주소](../../azure-monitor/platform/alerts-webhooks.md)를 설정합니다. 경고가 활성화될 때 및 해결될 때 모두 호출됩니다. 그러나 현재 쿼리 매개 변수는 웹후크 속성으로 전달되지 않습니다.
* 사용 하지 않도록 설정할 수 있습니다 하거나 경고를 사용 하도록 설정: 맨 위에 있는 단추를 참조 하세요.

*경고 추가 단추가 보이지 않습니다.*

* 조직 계정을 사용 중이신가요? 이 애플리케이션 리소스에 소유자 또는 참가자 액세스가 가능하면 경고를 설정할 수 있습니다. 액세스 제어 탭을 살펴보세요. [액세스 제어에 대해 자세히 알아보세요][roles].

> [!NOTE]
> 경고 블레이드에 [자동 관리 진단](../../azure-monitor/app/proactive-failure-diagnostics.md) 경고 설정이 이미 있습니다. 자동 경고는 특정 메트릭, 요청 실패율을 모니터링합니다. 사전 경고를 사용하지 않으려는 경우가 아니면 요청 실패율에 대한 자체 경고를 설정할 필요가 없습니다.
> 
> 

## <a name="see-your-alerts"></a>경고 보기
알림 상태가 비활성 및 활성 간에 변경될 때 전자 메일이 제공됩니다. 

각 경고의 현재 상태는 경고 규칙 탭에 표시 됩니다.

경고 드롭다운에 최신 활동에 대한 요약이 있습니다.

![경고 드롭다운](./media/alerts/010-alert-drop.png)

상태 변경 내역은 활동 로그에 있습니다.

![개요 탭에서 설정, 감사 로그를 클릭 합니다.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>경고 작동 방식
* 경고는 "활성화되지 않음", "활성화됨" 및 "해결됨"의 세 가지 상태로 제공됩니다. 활성화됨은 마지막으로 평가되었을 때 지정한 조건이 true였음을 의미합니다.
* 경고 상태가 변경되면 알림이 생성됩니다. (경고를 만들었을 때 이미 경고 조건이 true인 경우 조건이 false가 될 때까지 알림이 발생되지 않을 수 있습니다.)
* 메일 상자를 선택했거나 메일 주소를 제공한 경우 각 알림은 메일을 생성합니다. 알림 드롭다운 목록에서 찾아볼 수도 있습니다.
* 메트릭이 도착할 때마다 경고가 평가되고 그렇지 않은 경우는 평가되지 않습니다.
* 평가는 이전 기간에 대한 메트릭을 집계한 다음 임계값과 비교하여 새 상태를 결정합니다.
* 선택하는 기간으로 메트릭이 집계되는 간격이 지정됩니다. 기간은 경고가 평가되는 빈도에는 영향을 주지 않습니다. 이는 메트릭 도착 빈도에 따라 다릅니다.
* 일정 시간 동안 특정 메트릭에 대해 데이터가 도착하지 않는 경우 해당 간격은 메트릭 탐색기의 차트와 경고 평가에 다른 영향을 미칩니다. 메트릭 탐색기에서 데이터가 차트의 샘플링 간격보다 오랫동안 표시되지 않는 경우 차트 값에는 0이 표시됩니다. 하지만 동일한 메트릭에 따른 경고는 재평가되지 않으며 경고의 상태는 변경되지 않고 그대로 유지됩니다. 
  
    마침내 데이터가 도착하면 차트는 0이 아닌 값으로 이동합니다. 경고는 지정된 기간 동안 사용 가능한 데이터에 따라 평가합니다. 새 데이터 요소를 해당 기간에 하나만 사용할 수 있는 경우 집계는 해당 데이터 요소만 기반으로 합니다.
* 경고는 긴 기간을 설정한 경우에도 경고와 정상 상태 사이에서 자주 깜박거릴 수 있습니다. 메트릭 값이 임계값 주위를 가리키는 경우 이런 현상이 발생할 수 있습니다. 임계값에는 이력이 없습니다. 경고로 전환되는 것과 정상으로 전환되는 것이 같은 값에서 발생합니다.

## <a name="what-are-good-alerts-to-set"></a>어떤 경고를 설정하면 좋은가요?
애플리케이션에 따라 다릅니다. 처음에는 경고를 너무 많이 설정하지 않는 것이 좋습니다. 일단 앱이 실행되는 동안 메트릭 차트를 살펴보면서 정상적으로 작동할 때의 상태를 숙지합니다. 이렇게 하면 성능을 개선하는 방법을 찾는 데 도움이 됩니다. 그런 다음 메트릭이 정상 영역을 벗어나면 알려주는 경고를 설정합니다. 

다음은 많은 사람들이 사용하는 경고입니다.

* [브라우저 메트릭][client], 특히 브라우저 **페이지 로드 시간**은 웹 애플리케이션에 매우 유용합니다. 페이지에 스크립트가 많은 경우 **브라우저 예외**를 확인해야 합니다. 이러한 메트릭 및 경고를 가져오려면 [웹 페이지 모니터링][client]을 설정해야 합니다.
* 서버 쪽 웹 애플리케이션에 대한 **서버 응답 시간**. 경고 설정 외에도 이 메트릭을 주시하여 메트릭이 불균형적으로 변하고 요청 속도가 빠른지 살펴보는 것이 좋습니다. 변형은 앱에 리소스가 부족하다는 의미일 수 있습니다. 
* **서버 예외** 를 보려면 일부 [추가 설치](../../azure-monitor/app/asp-net-exceptions.md)작업을 수행해야 합니다.

[자동 관리 실패율 진단](../../azure-monitor/app/proactive-failure-diagnostics.md)은 앱이 오류 코드로 요청에 응답하는 속도를 자동으로 모니터링한다는 것을 잊지 마십시오.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>사용자 지정 로그 검색을 사용 하 여 예외 경고를 설정 하는 방법

이 섹션에서는 쿼리 기반 예외 경고를 설정 하는 방법을 살펴보겠습니다. 예를 들어 지난 24 시간 동안에서 실패 한 속도 10% 보다 클 때 경고 원하는 가정해 보겠습니다.

1. Azure portal에서 Application Insight 리소스를 이동 합니다.
2. 왼쪽 아래에서 클릭에 구성 **경고**합니다.

    ![구성의 왼쪽 아래에서 경고를 클릭 합니다.](./media/alerts/1appinsightalert.png)

3. 경고 탭의 맨 위에 있는 선택 **새 경고 규칙**합니다.

     ![경고 탭의 맨 위에 있는 새 경고 규칙을 클릭](./media/alerts/2createalert.png)

4. 리소스를 자동으로 선택 해야 합니다. 조건을 설정 하려면 **조건 추가**합니다.

    ![조건 추가 클릭](./media/alerts/3addcondition.png)

5. 신호 논리 구성 탭에서 선택 **사용자 지정 로그 검색**

    ![사용자 지정 로그 검색을 클릭 합니다.](./media/alerts/4customlogsearch.png)

6. 사용자 지정 로그 검색 탭에서 "검색 쿼리" 상자에서 쿼리를 입력 합니다. 이 예에서 사용 된 Kusto 쿼리 아래.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![검색 쿼리 상자에서 쿼리를 입력 합니다.](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 다른 유형의 쿼리 기반 경고에도 이러한 단계를 적용할 수 있습니다. 여기서 Kusto 쿼리 언어에 대 한 자세히 알아볼 수 있습니다 [Kusto 시작 문서](https://docs.microsoft.com/azure/kusto/concepts/) 또는 [Kusto SQL 참고 자료](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. "Alert logic" 결과 또는 미터법 수가 기반 인지 여부를 선택 합니다. (보다 큼, 같음, 작음) 조건 및 임계값을 선택 합니다. 이러한 값을 변경 하는 동안에 조건 문장 변경 내용 미리 보기를 볼 수 있습니다. 이 예제에서는 "같음" 사용 했습니다.

    ![경고 논리에서 조건을 확인 하 고 기반을 제공 하는 옵션에서 선택한 임계값을 입력 합니다.](./media/alerts/6alertlogic.png)

8. "평가에 따라" 아래에 있는 기간 및 빈도 설정 합니다. 여기에 기간에 대 한 입력 값과 일치 해야 위의 쿼리에서 기간입니다. 누른 **수행**합니다.

    ![맨 아래에서 기간 및 빈도 설정 하 고 [완료]를 클릭](./media/alerts/7evaluate.png)

9. 월별 예상된 비용을 사용 하 여 만든 조건을 현재 참조 하세요. 아래에서 ["작업 그룹"](../platform/action-groups.md) 기존 항목을 선택 하거나 새 그룹을 만들 수 있습니다. 원한다 면 작업을 사용자 지정할 수 있습니다.

    ![작업 그룹 단추를 만들거나 선택 클릭](./media/alerts/8actiongroup.png)

10. 마지막 경고 세부 정보를 추가 (합니다 규칙 이름, 설명, 심각도 경고). 완료 되 면 **경고 규칙 만들기** 맨 아래에 있습니다.

    ![경고 세부 정보에서 경고 규칙 이름을 입력, 설명을 작성 하 고 심각도 선택 합니다.](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>클래식 경고 전자 메일 알림 구독을 취소 하는 방법

이 섹션에 적용 됩니다 **클래식 가용성 경고**를 **클래식 Application Insights 메트릭 경고**, 및 **클래식 오류 비정상 경고**합니다.

다음 중 하나에 적용 되는 경우 이러한 클래식 경고에 대 한 전자 메일 알림을 받고 있습니다.

* 전자 메일 주소는 경고 규칙 설정에서 알림 전자 메일 받는 사람 필드에 나열 됩니다.

* 구독에 대 한 특정 역할을 보유 하는 사용자에 게 전자 메일 알림을 보내는 옵션을 활성화 되 고 해당 특정 Azure 구독에 대해 해당 역할을 보유 합니다.

![경고 알림 스크린 샷](./media/alerts/alert-notification.png)

보안 및 개인 정보 보호에서 클래식 경고에 대 한 알림 받는 사람을 명시적으로 지정 하는 것이 좋습니다를 더 잘 제어 하는 **알림 전자 메일 받는 사람** 필드입니다. 특정 역할을 보유 하는 모든 사용자에 게 알리는 옵션은 이전 버전과 호환성을 위해 제공 됩니다.

특정 경고 규칙에 의해 생성 되는 전자 메일 알림 구독을 취소 하려면에서 사용자 전자 메일 주소를 제거 합니다 **알림 전자 메일 받는 사람** 필드입니다.

전자 메일 주소를 명시적으로 나열 되지 않으면, 좋습니다 특정 역할의 모든 멤버를 자동으로 알리는 하는 옵션을 사용 하지 않도록 설정 하는 모든 사용자 전자 메일 알림 전자 메일에서 경고 규칙에 대 한 알림을 수신 해야 하는 대신 목록 받는 사람 필드입니다.

## <a name="who-receives-the-classic-alert-notifications"></a>누가 (클래식) 경고 알림을 받나요?

이 섹션은 클래식 경고에만 적용되며, 원하는 수신자만 알림을 받도록 경고 알림을 최적화하는 데 도움이 됩니다. 차이점에 대 한 자세한 [클래식 경고](../platform/alerts-classic.overview.md) 새로운 경고 환경 참조 및 합니다 [경고 개요 문서](../platform/alerts-overview.md)합니다. 새로운 경고 환경에서 경고 알림 제어 하려면 [작업 그룹](../platform/action-groups.md)합니다.

* 클래식 경고 알림에는 특정 수신자를 사용하는 것이 좋습니다.

* Application Insights 메트릭(가용성 메트릭 포함)에 대한 경고의 경우 **대량/그룹** 확인란 옵션을 사용하도록 설정하면 구독에서 owner, contributor 또는 reader 역할의 사용자에게 경고가 전송됩니다. 실제로 구독의 Application Insights 리소스에 대한 액세스 권한이 있는 ‘모든’ 사용자가 범위에 포함되며 알림을 받습니다.

> [!NOTE]
> 현재 **대량/그룹** 확인란 옵션을 사용하고 있고 사용 안 함으로 설정하는 경우에는 변경 내용을 되돌릴 수 없습니다.

역할에 따라 사용자에게 알려야 하는 경우 새 경고 환경/거의 실시간 경고를 사용합니다. [작업 그룹](../platform/action-groups.md)을 사용하면 contributor/owner/reader 역할(단일 옵션으로 결합되지 않음)의 사용자에 대해 메일 알림을 구성할 수 있습니다.

## <a name="automation"></a>Automation
* [PowerShell을 사용하여 경고 설정 자동화](../../azure-monitor/app/powershell-alerts.md)
* [Webhook를 사용하여 경고에 대한 응답 자동화](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>참고 항목
* [가용성 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md)
* [경고 설정 자동화](../../azure-monitor/app/powershell-alerts.md)
* [사전 진단](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

