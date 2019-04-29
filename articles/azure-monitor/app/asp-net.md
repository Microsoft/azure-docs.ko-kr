---
title: Azure Application Insights로 ASP.NET에 대한 웹앱 분석 설정 | Microsoft Docs
description: Azure 또는 온-프레미스에 호스트되는 ASP.NET 웹 사이트에 대한 성능, 가용성 및 사용자 동작 분석을 구성합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 719cbe1ec8962b320aa2850053d44cdef7f56a8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691603"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>ASP.NET 웹 사이트용 Application Insights 설정

이 절차는 ASP.NET 웹앱에서 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 서비스로 원격 분석을 보내도록 구성합니다. 사용자 고유의 IIS 서버 온-프레미스 또는 클라우드에서 호스팅되는 ASP.NET 앱에서 작동합니다. 차트 및 강력한 쿼리 언어를 사용하여 앱의 성능, 사용 방식 및 오류 또는 성능 문제에 대한 자동 알림을 이해할 수 있습니다. 많은 개발자가 이 기능이 있는 그대로 훌륭한 것으로 알게 되지만, 필요에 따라 원격 분석을 확장하고 사용자 지정할 수도 있습니다.

Visual Studio에서 설치 프로그램을 몇 번만 클릭하면 됩니다. 원격 분석의 양을 제한하여 비용 부담을 피할 수 있는 옵션이 있습니다. 이 기능을 사용 하 여 실험 하 고, 디버그 또는 사용자가 많지 않은 사용 하 여 사이트를 모니터링할 수 있습니다. 프로덕션 사이트를 먼저 모니터링하려고 결정하는 경우 나중에 제한을 쉽게 늘릴 수 있습니다.

## <a name="prerequisites"></a>필수 조건
Application Insights를 ASP.NET 웹 사이트에 추가하려면 다음을 수행해야 합니다.

- 다음 워크로드와 함께 [Windows용 Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="ide"></a> 1단계: Application Insights SDK 추가

> [!IMPORTANT]
> 이 예제의 스크린샷은 Visual Studio 2017 버전 15.9.9 기반으로 합니다. Application Insights를 추가할 환경을 ASP.NET 템플릿 유형에 뿐만 아니라 Visual Studio 2017의 버전 마다 다릅니다. 이전 버전에는 "Application Insights 구성"와 같은 대체 텍스트가 있을 수 있습니다.

솔루션 탐색기에서 웹 앱 이름을 마우스 오른쪽 단추로 클릭 하 고 선택 **추가** > **Application Insights 원격 분석**

![Application Insights 구성이 강조 표시된 솔루션 탐색기 스크린샷](./media/asp-net/add-telemetry-new.png)

(Application Insights SDK 버전에 따라 최신 SDK 릴리스로 업그레이드할 것인지 묻는 메시지가 표시될 수도 있습니다. 메시지가 표시되면 **SDK 업데이트**를 선택합니다.)

![스크린샷: 새 버전의 Microsoft Application Insights SDK를 사용할 수 있습니다. 강조 표시된 SDK 업데이트](./media/asp-net/0002-update-sdk.png)

Application Insights 구성 화면:

**시작**을 선택합니다.

![Application Insights에 앱 등록 페이지의 스크린샷](./media/asp-net/00004-start-free.png)

데이터가 저장되는 위치나 리소스 그룹을 설정하려면 **설정 구성**을 클릭합니다. 리소스 그룹은 데이터에 대한 액세스를 제어하는 데 사용됩니다. 예를 들어, 동일한 시스템의 부분을 형성하는 몇 개의 앱이 있는 경우 해당 앱의 Application Insights 데이터를 동일한 리소스 그룹에 배치할 수 있습니다.

 **등록**을 선택합니다.

![Application Insights에 앱 등록 페이지의 스크린샷](./media/asp-net/00005-register-ed.png)

 원격 분석이 디버깅 중 및 앱을 게시한 후에 [Azure Portal](https://portal.azure.com)로 전송됩니다.
> [!NOTE]
> 디버그하는 동안 포털에 원격 분석을 보내지 않으려면 앱에 Application Insights SDK를 추가하고 포털에서 리소스를 구성하지는 않습니다. 디버그하는 동안 Visual Studio에서 원격 분석을 볼 수 있습니다. 나중에 이 구성 페이지로 돌아오거나 앱을 배포할 때까지 기다려서 [런타임에 원격 분석으로 전환할 수 있습니다](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="run"></a> 2단계: 앱 실행
F5를 사용하여 앱을 실행합니다. 다른 페이지를 열어서 일부 원격 분석을 생성합니다.

Visual Studio에서 로그된 이벤트 수가 표시됩니다.

![Visual Studio의 스크린샷. Application Insights 단추는 디버깅하는 동안 표시됩니다.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>3단계: 원격 분석 확인
원격 분석은 Visual Studio 또는 Application Insights 웹 포털에서 볼 수 있습니다. Visual Studio에서 원격 분석을 검색하여 앱을 디버그하는 데 도움을 줍니다. 시스템이 작동 중일 때 웹 포털의 성능 및 사용량을 모니터링합니다. 

### <a name="see-your-telemetry-in-visual-studio"></a>Visual Studio에서 원격 분석 확인

Visual Studio에서 Application Insights 데이터를 보려면  **솔루션 탐색기** > **연결된 서비스**를 선택하고 > **Application Insights**를 마우스 오른쪽 단추로 클릭한 다음, **라이브 원격 분석 검색**을 클릭합니다.

Visual Studio Application Insights Search 창에서 앱의 서버 쪽에 생성된 원격 분석에 대한 애플리케이션의 데이터를 볼 수 있습니다. 필터를 테스트하고 이벤트를 클릭하여 자세한 정보를 확인합니다.

![Application Insights 창의 디버그 세션 보기의 데이터 스크린샷.](./media/asp-net/55.png)

> [!Tip]
> 데이터가 보이지 않으면 시간 범위가 올바른지 확인하고 Search 아이콘을 클릭하세요.

[Visual Studio의 Application Insights 도구에 대해 자세히 알아봅니다](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>웹 포털에서 원격 분석 확인

SDK만 설치하도록 선택하지 않은 경우 Application Insights 웹 포털에서도 원격 분석을 볼 수 있습니다. 포털에는 Visual Studio보다 더 많은 차트, 분석 도구 및 구성 요소 간 보기가 있습니다. 또한 포털에서는 경고도 제공합니다.

Application Insights 리소스를 엽니다. [Azure Portal](https://portal.azure.com/)에 로그인하여 찾거나, **솔루션 탐색기** > **연결된 서비스**를 선택하고 > **Application Insights** > **Application Insights 포털 열기**를 마우스 오른쪽 단추로 클릭하여 이동합니다.

포털은 앱에서 원격 분석의 보기를 엽니다.

![Application Insights 개요 페이지 스크린샷](./media/asp-net/007.png)

포털에서 타일이나 차트를 클릭하여 자세한 정보를 확인합니다.

[에서 Application Insights를 사용하는 방법에 대해 자세히 알아봅니다](../../azure-monitor/app/app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>4단계: 앱 게시
IIS 서버 또는 Azure에 앱을 게시합니다. [라이브 메트릭 스트림](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream)을 보고 모두 문제 없이 실행되고 있는지 확인합니다.

원격 분석이 Application Insights 포털에 구축되고, 이 곳에서 메트릭을 모니터링하고 원격 분석을 검색하고 [대시보드](../../azure-monitor/app/app-insights-dashboards.md)를 설정할 수 있습니다. 또한 강력한 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하여 사용량 및 성능을 분석하거나 특정 이벤트를 찾을 수 있습니다.

[Visual Studio](../../azure-monitor/app/visual-studio.md)에서 진단 검색 및 [추세](../../azure-monitor/app/visual-studio-trends.md) 등의 도구를 사용하여 원격 분석을 계속 수행할 수도 있습니다.

> [!NOTE]
> 앱에서 너무 많은 원격 분석을 보내 [제한 한도](../../azure-monitor/app/pricing.md#limits-summary)에 도달할 경우 자동 [샘플링](../../azure-monitor/app/sampling.md)이 켜집니다. 샘플링은 앱에서 보내는 원격 분석의 양을 줄이고 진단을 위해 상호 관련된 데이터를 유지합니다.
>
>

## <a name="land"></a> 모든 설정을 완료했습니다.

축하합니다! 앱에 Application Insight 패키지를 설치하고, Azure의 Application Insights 서비스로 원격 분석을 보내도록 구성했습니다.

앱의 원격 분석을 받는 Azure 리소스는 *계측 키*로 식별됩니다. 이 키는 ApplicationInsights.config 파일에서 찾을 수 있습니다.


## <a name="upgrade-to-future-sdk-versions"></a>향후 SDK 버전으로 업그레이드
[SDK의 새 릴리스](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)로 업그레이드하려면 **NuGet 패키지 관리자**를 열고 설치된 패키지를 필터링합니다. **Microsoft.ApplicationInsights.Web**을 선택하고 **업그레이드**를 선택합니다.

ApplicationInsights.config에 대한 사용자 지정을 수행한 경우, 업그레이드 전에 복사본을 저장합니다. 그런 다음 변경 내용을 새 버전에 병합합니다.

## <a name="video"></a>비디오

* [처음부터 .NET 애플리케이션으로 Application Insight 구성](https://www.youtube.com/watch?v=blnGAVgMAfA)에 대한 외부의 단계별 비디오입니다.

## <a name="next-steps"></a>다음 단계

관심이 있는 경우 살펴볼만한 다른 문서:

* [런타임 시 웹앱 계측](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>추가 원격 분석

* **[브라우저 및 페이지 로드 데이터](../../azure-monitor/app/javascript.md)** - 웹 페이지에 코드 조각을 삽입합니다.
* **[더 자세한 종속성 및 예외 모니터링 가져오기](../../azure-monitor/app/monitor-performance-live-website-now.md)** - 서버에 상태 모니터를 설치합니다.
* **[사용자 지정 이벤트 코딩](../../azure-monitor/app/api-custom-events-metrics.md)** - 사용자 작업의 수, 시간 또는 측정값을 평가합니다.
* **[로그 데이터 가져오기](../../azure-monitor/app/asp-net-trace-logs.md)** - 로그 데이터와 원격 분석 간에 상관 관계를 지정합니다.

### <a name="analysis"></a>분석

* **[Visual Studio Online에서 Application Insights로 작업](../../azure-monitor/app/visual-studio.md)**<br/>원격 분석, 진단 검색 및 코드 드릴스루를 통한 디버깅에 대한 정보가 포함되어 있습니다.
* **[Application Insights 포털 사용](../../azure-monitor/app/app-insights-dashboards.md)**<br/> 대시보드, 강력한 진단 및 분석 도구, 경고, 애플리케이션의 라이브 종속성 맵 및 원격 분석 내보내기에 대한 정보가 포함되어 있습니다.
* **[분석](../../azure-monitor/log-query/get-started-portal.md)** - 강력한 쿼리 언어입니다.

### <a name="alerts"></a>경고

* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md): 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](../../azure-monitor/app/proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../../azure-monitor/app/alerts.md): 메트릭이 임계값을 초과할 경우 경고 하는 경고를 설정 합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.

### <a name="automation"></a>Automation

* [Application Insights 리소스 만들기 자동화](../../azure-monitor/app/powershell.md)
