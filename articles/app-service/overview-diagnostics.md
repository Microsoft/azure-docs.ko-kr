---
title: 진단 및 해결 도구
description: Azure Portal의 진단 및 해결 도구를 사용 하 여 Azure App Service에서 앱 문제를 해결 하는 방법에 대해 알아봅니다.
keywords: App Service, Azure App Service, 진단, 지원, 웹앱, 문제 해결, 자가 진단
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962811"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 진단 개요

웹 애플리케이션을 실행할 때 500개 오류부터 사용자가 사이트의 작동이 중단되었음을 알리는 사용자에 이르기까지 발생할 수 있는 문제에 대비해야 합니다. App Service 진단은 구성이 필요 없는 앱 문제를 해결 하는 데 도움이 되는 지능적인 대화형 환경입니다. 앱에서 문제가 발생 하는 경우 App Service 진단은 문제를 보다 쉽고 빠르게 해결 하 고 해결 하는 데 적합 한 정보를 안내해 주는 문제를 해결 합니다.

최근 24 시간 이내에 앱에 문제가 있는 경우에는이 환경이 가장 유용 하지만 모든 진단 그래프가 항상 분석에 사용할 수 있습니다.

App Service 진단은 Windows의 앱 뿐만 아니라 [Linux/컨테이너](./overview.md#app-service-on-linux), [App Service 환경](./environment/intro.md) 및 [Azure Functions](../azure-functions/functions-overview.md)의 앱에도 작용합니다.

## <a name="open-app-service-diagnostics"></a>App Service 진단 열기

App Service 진단에 액세스 하려면 App Service 웹 앱으로 이동 하거나 [Azure Portal](https://portal.azure.com)에서 App Service Environment 합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결**을 클릭합니다.

Azure Functions의 경우 함수 앱으로 이동 하 고, 상단 탐색에서 **플랫폼 기능**을 클릭 하 고, **리소스 관리** 섹션에서 **문제 진단 및 해결** 을 선택 합니다.

App Service 진단 홈페이지에서 각 홈페이지 타일의 키워드를 사용 하 여 앱에 대 한 문제를 가장 잘 설명 하는 범주를 선택할 수 있습니다. 또한이 페이지에서는 Windows 앱에 대 한 **진단 도구** 를 찾을 수 있습니다. [진단 도구 (Windows 앱에만 해당)](#diagnostic-tools-only-for-windows-app)를 참조 하세요.

![홈페이지](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> 앱이 다운 되거나 느리게 수행 되는 경우 [프로 파일링 추적을 수집](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) 하 여 문제의 근본 원인을 식별할 수 있습니다. 프로 파일링은 경량 이며 프로덕션 시나리오를 위해 설계 되었습니다.
>

## <a name="interactive-interface"></a>대화형 인터페이스

앱의 문제에 가장 잘 맞는 홈 페이지 범주를 선택 하면 App Service 진단의 대화형 인터페이스인 Genie를 사용 하 여 앱의 문제를 진단 하 고 해결 하는 과정을 안내할 수 있습니다. Genie에서 제공 하는 타일 바로 가기를 사용 하 여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기를 통해 진단 메트릭에 직접 액세스할 수 있습니다.

![타일 바로 가기](./media/app-service-diagnostics/tile-shortcuts-2.png)

이러한 타일을 클릭 한 후 타일에 설명 된 문제와 관련 된 항목의 목록을 볼 수 있습니다. 이러한 토픽에서는 전체 보고서에서 주목할 만한 정보 조각을 제공합니다. 이러한 항목 중 하나를 클릭 하 여 문제를 자세히 조사할 수 있습니다. 또한 **전체 보고서 보기** 를 클릭 하 여 단일 페이지의 모든 항목을 탐색할 수 있습니다.

![토픽](./media/app-service-diagnostics/application-logs-insights-3.png)

![전체 보고서 보기](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>진단 보고서

항목을 클릭 하 여 문제를 자세히 조사 하도록 선택한 후에는 종종 그래프 및 markmark로 보완 되는 항목에 대 한 자세한 정보를 볼 수 있습니다. 진단 보고서는 앱 문제를 어설션 매크로나 하는 강력한 도구 일 수 있습니다.

![진단 보고서](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>상태 검사

앱에 무엇이 잘못 되었는지 알지 못하거나 문제 해결을 시작할 위치를 모르는 경우 상태 점검을 시작 하는 것이 좋습니다. 상태 점검은 응용 프로그램을 분석 하 여 정상 및 잘못 된 기능을 설명 하는 빠르고 대화형 개요를 제공 하 고 문제를 조사할 위치를 알려 줍니다. 지능적인 대화형 인터페이스는 문제 해결 프로세스를 통해 지침을 제공합니다. 상태 점검은 Windows 앱에 대 한 Genie 환경 및 Linux 앱에 대 한 웹 앱 다운 진단 보고서와 통합 됩니다.

### <a name="health-checkup-graphs"></a>상태 점검 그래프

상태 점검에는 네 가지 그래프가 있습니다.

- **요청 및 오류:** HTTP 서버 오류와 함께 최근 24 시간 동안 수행 된 요청 수를 표시 하는 그래프입니다.
- **앱 성능:** 여러 백분위 수 그룹의 최근 24 시간 동안 응답 시간을 표시 하는 그래프입니다.
- **CPU 사용량:** 최근 24 시간 동안의 인스턴스당 전체 CPU 사용량을 표시 하는 그래프입니다.  
- **메모리 사용량:** 지난 24 시간 동안 인스턴스당 전체 실제 메모리 사용량을 표시 하는 그래프입니다.

![상태 검사](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>응용 프로그램 코드 문제 조사 (Windows 앱에만 해당)

많은 앱 문제가 애플리케이션 코드의 문제와 관련되어 있으므로 App Service 진단은 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합되어 예외 및 종속성 문제를 강조 표시하고, 선택된 작동 중단 시간과의 상관 관계를 표시합니다. Application Insights은 별도로 사용 하도록 설정 해야 합니다.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Application Insights 예외 및 종속성을 보려면 **웹 앱 다운** 또는 **웹 앱 저속** 타일 바로 가기를 선택 합니다.

### <a name="troubleshooting-steps-only-for-windows-app"></a>문제 해결 단계 (Windows 앱에만 해당)

최근 24 시간 이내에 특정 문제 범주를 사용 하 여 문제를 발견 한 경우 전체 진단 보고서를 볼 수 있으며, App Service 진단에서 보다 단계별 경험을 위해 더 많은 문제 해결 통지 및 다음 단계를 확인 하 라는 메시지가 표시 될 수 있습니다.

![Application Insights 및 문제 해결 및 다음 단계](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>진단 도구 (Windows 앱에만 해당)

진단 도구에는 응용 프로그램 코드 문제, 속도 저하, 연결 문자열 등을 조사 하는 데 도움이 되는 고급 진단 도구가 포함 되어 있습니다. 그리고 CPU 사용량, 요청 및 메모리와 관련 된 문제를 완화 하는 데 도움이 되는 사전 자동 도구입니다.

### <a name="proactive-cpu-monitoring"></a>자동 관리 CPU 모니터링

자동 관리 CPU 모니터링을 통해 앱 또는 앱에 대 한 자식 프로세스가 높은 CPU 리소스를 사용 하는 경우 작업을 쉽게 수행할 수 있습니다. 예기치 않은 문제에 대 한 실제 원인이 발견 될 때까지 cpu 임계값 규칙을 일시적으로 완화 하도록 사용자 고유의 CPU 임계값 규칙을 설정할 수 있습니다. 자세한 내용은 [CPU 문제가 발생 하기 전에](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)이를 완화 하는 방법을 참조 하세요.

![자동 관리 CPU 모니터링](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>자동 복구 및 자동 복구 자동 복구

자동 복구는 응용 프로그램에 예기치 않은 동작이 있을 때 수행할 수 있는 완화 작업입니다. 요청 수, 저속 요청, 메모리 제한 및 HTTP 상태 코드에 따라 사용자 고유의 규칙을 설정 하 여 완화 작업을 트리거할 수 있습니다. 이 도구를 사용 하 여 근본 원인을 찾을 때까지 예기치 않은 동작을 일시적으로 완화할 수 있습니다. 자세한 내용은 [app service diagnostics에서 새로운 자동 치료 환경 발표](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)를 참조 하세요.

![자동 복구](./media/app-service-diagnostics/auto-healing-10.png)

자동 관리 CPU 모니터링과 마찬가지로 자동 복구는 응용 프로그램의 예기치 않은 동작을 완화 하기 위한 턴 키 솔루션입니다. 자동 복구는 앱이 복구할 수 없는 상태에 있는 것으로 확인 App Service 때 앱을 다시 시작 합니다. 자세한 내용은 [자동 치료 소개](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)를 참조 하세요.

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>탐색기 및 변경 분석 (Windows 앱에만 해당)

연속 통합을 사용 하 고 앱에 많은 종속성이 있는 규모가 많은 팀에서 비정상 동작을 야기 하는 특정 변경 내용을 정확 하 게 파악 하기 어려울 수 있습니다. 탐색기를 사용 하면 앱의 종속성 맵과 동일한 구독의 모든 리소스를 자동으로 렌더링 하 여 앱의 토폴로지에 대 한 가시성을 얻을 수 있습니다. 탐색기를 사용 하면 앱 및 해당 종속성의 통합 된 변경 내용 목록을 볼 수 있으며 비정상 동작을 야기 하는 변경 내용에 대해 범위를 좁힐 수 있습니다. 홈 페이지 타일 **탐색기** 를 통해 액세스할 수 있으며 처음 사용 하기 전에 활성화 해야 합니다. 자세한 내용은 [탐색기를 사용 하 여 앱의 종속성에 대 한 가시성 가져오기](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)를 참조 하세요.

![탐색기 기본 페이지](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff 뷰](./media/app-service-diagnostics/diff-view-12.png)

앱 변경 내용에 대 한 변경 분석은 타일 바로 가기를 통해 액세스할 수 있으며, **응용 프로그램 변경** 및 **응용** 프로그램의 **가용성 및 성능 저하** 를 통해 다른 메트릭과 동시에 사용할 수 있습니다. 기능을 사용 하려면 먼저 해당 기능을 사용 하도록 설정 해야 합니다. 자세한 내용은 [App Service 진단에서 새로운 변경 분석 환경 발표](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)를 참조 하세요.

제목에 "[Diag]"를 추가 하 여 [UserVoice](https://feedback.azure.com/forums/169385-web-apps) 에서 질문이 나 의견을 게시 합니다.