---
title: Application Insights로 SCOM 통합 | Microsoft Docs
description: SCOM 사용자인 경우 Application Insights로 성능을 모니터링하고 문제를 진단합니다. 포괄적 대시보드, 스마트 경고, 강력한 진단 도구 및 분석 쿼리.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: a08a67a78362325e29b1002b44f390a94e78888a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784880"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>SCOM에 대해 Application Insights를 사용하여 애플리케이션 성능 모니터링
서버를 관리하는 데 SCOM(System Center Operations Manager)을 사용하는 경우 [Azure Application Insights](../../azure-monitor/app/asp-net.md)를 활용해 성능을 모니터링하고 성능 문제를 진단할 수 있습니다. Application Insights는 사용자 웹 애플리케이션에 들어오는 요청과 나가는 REST 및 SQL 호출, 예외 및 로그 추적을 모니터링합니다. 메트릭 차트 및 스마트 경고뿐만 아니라 이 원격 분석을 통한 강력한 진단 검색 및 분석 쿼리가 포함된 대시보드를 제공합니다. 

SCOM 관리 팩을 사용하여 Application Insights 모니터링으로 전환할 수 있습니다.

> [!IMPORTANT]
> 이 System Center Operations Manager Management Pack은 이제 **사용되지 않습니다**. 최신 Application Insights SDK를 지원하지 않으며, 더 이상 권장되지 않습니다.

## <a name="before-you-start"></a>시작하기 전에
다음을 가정합니다.

* SCOM에 대해 잘 알고 있으며 SCOM 2012 R2 또는 2016을 사용하여 IIS 웹 서버를 관리합니다.
* 서버에 Application Insights로 모니터링하려는 웹 애플리케이션을 이미 설치했습니다.
* 앱 프레임워크 버전은 .NET 4.5 이상입니다.
* [Microsoft Azure](https://azure.com)에서 구독에 액세스하여 [Azure Portal](https://portal.azure.com)에 로그인할 수 있습니다. 조직에 구독이 있으면 해당 구독에 Microsoft 계정을 추가할 수 있습니다.

(개발 팀은 [Application Insights SDK](../../azure-monitor/app/asp-net.md)를 웹앱으로 빌드할 수 있습니다. 이 빌드 시간 계측은 사용자 지정 원격 분석을 작성하는 데 더 많은 유연성을 제공합니다. 하지만 이것은 크게 중요하지 않습니다. SDK가 기본 제공되는지 여부에 관계없이 여기에 설명된 단계를 따르면 됩니다.)

## <a name="one-time-install-application-insights-management-pack"></a>(한 번) Application Insights 관리 팩 설치
Operations Manager를 실행하는 컴퓨터에서 다음을 수행합니다.

1. 이전 버전의 관리 팩을 제거합니다.
   1. Operations Manager에서 관리, 관리 팩을 엽니다. 
   2. 이전 버전을 삭제합니다.
2. 카탈로그에서 관리 팩을 다운로드하여 설치합니다.
3. Operations Manager를 다시 시작합니다.

## <a name="create-a-management-pack"></a>관리 팩 만들기
1. Operations Manager에서 **작성**, **.NET...with Application Insights**, **모니터링 추가 마법사**를 열고 **.NET...with Application Insights**를 한 번 더 선택합니다.
   
    ![](./media/scom/020.png)
2. 앱 다음에 구성의 이름을 지정합니다. (한 번에 하나의 앱을 계측해야 합니다.)
   
    ![](./media/scom/030.png)
3. 동일한 마법사 페이지에서 새 관리 팩을 만들거나 Application Insights에 대해 이전에 만든 팩을 선택합니다.
   
     (Application Insights [관리 팩](https://technet.microsoft.com/library/cc974491.aspx)은 인스턴스를 만들 수 있는 템플릿입니다. 동일한 인스턴스를 나중에 재사용할 수 있습니다.)

    ![일반 속성 탭에서 앱의 이름을 입력합니다. 새로 만들기를 클릭하고 관리 팩의 이름을 입력합니다. 확인을 클릭한 후 다음을 클릭합니다.](./media/scom/040.png)

1. 모니터링할 앱을 하나 선택합니다. 검색 기능으로 서버에 설치된 앱을 검색합니다.
   
    ![모니터링할 항목 탭에서 추가를 클릭하고 앱 이름 부분을 입력하며, 검색을 클릭하고 앱을 선택한 후 추가, 확인을 선택합니다.](./media/scom/050.png)
   
    일부 서버의 앱만 모니터링하려는 경우 모니터링 범위 필드(선택 사항)를 사용하여 서버의 서브넷을 지정할 수 있습니다.
2. 다음 마법사 페이지에서는 먼저 Microsoft Azure에 로그인할 자격 증명을 제공해야 합니다.
   
    이 페이지에서 원격 분석 데이터를 분석하고 표시할 Application Insights 리소스를 선택합니다. 
   
   * 개발 중에 Application Insights에 대해 애플리케이션이 구성된 경우 기존 리소스를 선택합니다.
   * 그렇지 않은 경우 해당 앱에 대해 명명된 새 리소스를 만듭니다. 동일한 시스템의 구성 요소인 다른 앱이 있는 경우 이를 동일한 리소스 그룹에 배치하면 원격 분석에 보다 쉽게 액세스하여 관리할 수 있습니다.
     
     나중에 이러한 설정을 변경할 수 있습니다.
     
     ![Application Insights 설정 탭에서 '로그인'을 클릭하고 Azure에 대한 Microsoft 계정 자격 증명을 제공합니다. 그런 다음 구독, 리소스 그룹 및 리소스를 선택합니다.](./media/scom/060.png)
3. 마법사를 완료합니다.
   
    ![만들기 클릭 ](./media/scom/070.png)

모니터링할 각 앱에 대해 다음 절차를 반복합니다.

나중에 설정을 변경해야 하는 경우 작업 창에서 모니터의 속성을 다시 엽니다.

![작성에서 .NET Application Performance Monitoring with Application Insights를 선택하고 모니터를 선택한 후 속성을 클릭합니다.](./media/scom/080.png)

## <a name="verify-monitoring"></a>모니터링 확인
설치한 모니터가 모든 서버에서 앱을 검색합니다. 여기서 앱을 찾으면 Application Insights 상태 모니터를 구성하여 앱을 모니터링합니다. 필요한 경우 먼저 서버에 상태 모니터를 설치합니다.

찾은 앱의 인스턴스를 확인할 수 있습니다.

![모니터링에서 Application Insights를 엽니다.](./media/scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Application Insights에서 원격 분석 보기
[Azure 포털](https://portal.azure.com)에서 앱에 대한 리소스로 이동합니다. 앱에서 [원격 분석을 보여 주는 차트를 확인](../../azure-monitor/app/app-insights-dashboards.md) 합니다. (기본 페이지에 표시되지 않으면 라이브 메트릭 스트림을 클릭합니다.)

## <a name="next-steps"></a>다음 단계
* [대시보드를 설정](../../azure-monitor/app/app-insights-dashboards.md) 하여 현재 항목 및 기타 앱을 모니터링하는 가장 중요한 차트를 모읍니다.
* [매트릭에 대해 알아보기](../../azure-monitor/app/metrics-explorer.md)
* [경고 설정](../../azure-monitor/app/alerts.md)
* [성능 문제 진단](../../azure-monitor/app/detect-triage-diagnose.md)
* [강력한 분석 쿼리](../../azure-monitor/app/analytics.md)
* [가용성 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md)

