---
title: 데이터 없음 문제 해결 - .NET용 Application Insights
description: Azure Application Insights에서 데이터를 볼 수 없나요? 여기를 참조하세요.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 11d63889555d6899df07e83168813994907476af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691214"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>데이터 없음 문제 해결 - .NET용 Application Insights
## <a name="some-of-my-telemetry-is-missing"></a>일부 원격 분석이 누락됨
*Application Insights에서 내 앱이 생성하는 이벤트의 일부만 표시됩니다.*

* 동일한 부분이 일관되게 표시되는 경우 적응 [샘플링](../../azure-monitor/app/sampling.md)때문일 것입니다. 이러한 사항을 확인하려면 개요 블레이드에서 검색을 열고 요청 또는 다른 이벤트의 인스턴스를 확인합니다. 속성 섹션의 아래쪽에서 "..."를 클릭하여 전체 속성 세부 정보를 표시합니다. 요청 수가 1보다 크면 샘플링이 작동 중인 것입니다.
* 그렇지 않은 경우 요금제의 [데이터 속도 제한](../../azure-monitor/app/pricing.md#limits-summary) 에 도달한 것일 수 있습니다. 이러한 제한은 분당으로 적용됩니다.

## <a name="no-data-from-my-server"></a>내 서버에서 데이터 없음
*웹 서버에 이 앱을 설치했지만 지금 원격 분석이 표시되지 않습니다. 내 개발 컴퓨터에서 문제 없이 작동했습니다.*

* 아마도 방화벽 문제일 것입니다. [Application Insights에 대한 방화벽 예외를 설정하여 데이터를 전송합니다](../../azure-monitor/app/ip-addresses.md).
* IIS 서버에 필수 구성 요소 .NET Extensibility 4.5 및 ASP.NET 4.5가 없는 것이 원인일 수 있습니다.

기존 앱을 모니터링하려고 웹 서버에 *[상태 모니터를 설치](../../azure-monitor/app/monitor-performance-live-website-now.md)했지만 결과가 보이지 않습니다.*

* [상태 모니터 문제 해결](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot)을 참조하세요.

## <a name="q01"></a>Visual Studio에 'Application Insights 추가' 옵션이 없음
*Solution Explorer에서 기존 프로젝트를 마우스 오른쪽 단추로 클릭할 때 Application Insights 옵션이 표시되지 않습니다.*

* 이 도구가 모든 유형의 .NET 프로젝트를 지원하지는 않습니다. 웹 및 WCF 프로젝트는 지원됩니다. 데스크톱 또는 서비스 애플리케이션 같은 기타 프로젝트 유형은 여전히 [수동으로 프로젝트에 Application Insights SDK를 추가](../../azure-monitor/app/windows-desktop.md)할 수 있습니다.
* [Visual Studio 2013 업데이트 3 이후](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs)가 설치되어 있는지 확인하세요. Application Insights SDK를 제공하는 개발자 분석 도구가 사전 설치되어 제공됩니다.
* **도구**, **확장 및 업데이트**를 차례로 선택하고 **개발자 분석 도구**가 설치 및 활성화되었는지 확인하세요. 그렇다면 **업데이트** 를 클릭하여 제공되는 업데이트가 있는지 확인합니다.
* 새 프로젝트 대화 상자를 열고 ASP.NET 웹 애플리케이션을 선택합니다. Application Insights 옵션이 보이면 도구가 설치된 것입니다. 옵션이 보이지 않으면 Application Insights Tools를 다시 설치해 보세요.

## <a name="q02"></a>Application Insights 추가 실패
*기존 프로젝트에 Application Insights를 추가하려고 할 때 오류 메시지가 표시됩니다.*

가능한 원인:

* Application Insights 포털과의 통신이 실패했거나 또는
* Azure 계정에 문제가 있습니다.
* [새 리소스를 만들려고 하던 구독 또는 그룹에 대한 읽기 액세스](../../azure-monitor/app/resources-roles-access-control.md)권한만이 있습니다.

해결 방법:

* 올바른 Azure 계정의 로그인 자격 증명을 입력했는지 확인하세요.
* 브라우저에서 [Azure 포털](https://portal.azure.com)에 대한 액세스 권한이 있는지 확인합니다. 설정을 열고 제한이 있는지 확인합니다.
* [Application Insights를 기존 프로젝트에 추가](../../azure-monitor/app/asp-net.md): 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 "Application Insights 추가"를 선택합니다.
* 여전히 작동하지 않는 경우 [수동 절차](../../azure-monitor/app/windows-services.md) 에 따라 포털에서 리소스에 추가한 다음 SDK를 프로젝트에 추가합니다.

## <a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류가 발생합니다.
Application Insights를 설치하는 동안 문제가 발생했거나 로깅 어댑터에 문제가 있을 수 있습니다.

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Application Insights > Application Insights 구성**을 차례로 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하도록 초대하는 대화 상자가 표시됩니다.

## <a name="NuGetBuild"></a> 빌드 서버에 "NuGet 패키지가 없음"
*개발 컴퓨터에서 디버깅을 수행하면 모든 것이 정상적으로 빌드되지만 빌드 서버에서는 NuGet 오류가 발생합니다.*

[NuGet 패키지 복원](https://docs.nuget.org/Consume/Package-Restore) 및 [자동 패키지 복원](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)을 참조하세요.

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Visual Studio에서 Application Insights를 여는 메뉴 명령 누락
*프로젝트 솔루션 탐색기를 마우스 오른쪽 단추로 클릭해도 Application Insights 명령 또는 Application Insights 열기 명령이 보이지 않습니다.*

가능한 원인:

* Application Insights 리소스를 수동으로 만들었거나 프로젝트가 Application Insights 도구에서 지원되지 않는 유형입니다.
* Visual Studio에서 개발자 분석 도구가 비활성화되었습니다.
* Visual Studio 버전이 2013 업데이트 3보다 오래되었습니다.

해결 방법:

* Visual Studio가 2013 업데이트 3 이상 버전인지 확인하세요.
* **도구**, **확장 및 업데이트**를 차례로 선택하고 **개발자 분석 도구**가 설치 및 활성화되었는지 확인하세요. 그렇다면 **업데이트** 를 클릭하여 제공되는 업데이트가 있는지 확인합니다.
* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **Application Insights > Application Insights 구성** 명령이 보이면 그 명령을 사용하여 Application Insights 서비스의 리소스에 프로젝트를 연결합니다.

명령이 보이지 않으면 프로젝트가 Application Insights 도구에서 바로 지원되지 않는 유형입니다. 원격 분석을 보려면 [Azure Portal](https://portal.azure.com)에 로그인하고, 왼쪽의 탐색 모음에서 Application Insights를 선택하고, 애플리케이션을 선택합니다.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Visual Studio에서 Application Insights를 열면 '액세스 거부' 오류
*'Application Insights 열기' 메뉴 명령을 실행하면 Azure Portal로 이동되지만 '액세스 거부' 오류가 발생합니다.*

기본 브라우저에서 마지막으로 사용한 Microsoft 로그인은 [이 앱에 Application Insights를 추가할 때 만들어진 리소스](../../azure-monitor/app/asp-net.md)에 대한 액세스 권한을 갖고 있지 않습니다. 가능한 원인은 두 가지입니다.

* Microsoft 계정이 여러 개 있습니다. 예를 들어 회사용 Microsoft 계정과 개인용 Microsoft 계정이 있습니다. 기본 브라우저에서 마지막으로 사용한 로그인이 [프로젝트에 Application Insights를 추가](../../azure-monitor/app/asp-net.md)할 수 있는 액세스 권한을 가진 계정이 아닌 다른 계정의 로그인입니다.
  * 해결 방법: 브라우저 창 오른쪽 상단에서 본인의 이름을 클릭하고 로그아웃합니다. 액세스 권한이 있는 계정으로 로그인합니다. 그런 다음, 왼쪽의 탐색 모음에서 Application Insights를 클릭하고 앱을 선택합니다.
* 다른 사용자가 프로젝트에 Application Insights를 추가한 후 나에게 프로젝트가 생성된 [리소스 그룹에 대한 액세스 권한](../../azure-monitor/app/resources-roles-access-control.md)을 부여하는 것을 잊어버렸습니다.
  * 해결 방법: 조직 계정을 사용하는 다른 사용자가 나를 팀에 추가해 주거나 나에게 리소스 그룹에 대한 개별 액세스 권한을 부여하는 방법도 있습니다.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>Visual Studio에서 Application Insights를 열면 '자산을 찾을 수 없음' 오류
*'Application Insights 열기' 메뉴 명령을 실행하면 Azure Portal로 이동되지만 '자산을 찾을 수 없음' 오류가 발생합니다.*

가능한 원인:

* 애플리케이션의 Application Insights 리소스가 삭제되었습니다. 또는
* 프로젝트 파일을 업데이트하지 않고 계측 키를 바로 편집하여 ApplicationInsights.config에서 계측 키가 설정되었거나 변경되었습니다.

ApplicationInsights.config의 계측 키는 원격 분석이 전송되는 위치를 제어합니다. 프로젝트 파일의 줄은 Visual Studio에서 명령을 사용할 때 열리는 리소스를 제어합니다.

해결 방법:

* 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights, Application Insights 구성을 차례로 선택합니다. 대화 상자에서 기존 리소스로 원격 분석을 전송하거나 새로 만들 수 있습니다. 또는
* 리소스를 직접 엽니다. [Azure Portal](https://portal.azure.com)에 로그인하고, 왼쪽의 탐색 모음에서 Application Insights를 클릭한 다음 앱을 선택합니다.

## <a name="where-do-i-find-my-telemetry"></a>내 원격 분석은 어디서 찾을 수 있습니까?
*[Microsoft Azure Portal](https://portal.azure.com)에 로그인한 다음 Azure 홈 대시보드를 보고 있습니다. 내 Application Insights 데이터는 어디서 찾나요?*

* 왼쪽의 탐색 모음에서 Application Insights를 클릭한 다음 앱 이름을 클릭합니다. 선택할 프로젝트가 없는 경우 [웹 프로젝트에서 Application Insights를 추가 또는 구성](../../azure-monitor/app/asp-net.md)해야 합니다.  
  요약 차트 몇 개가 보일 것입니다. 요약 차트를 클릭하면 세부 정보를 수 있습니다.
* Visual Studio에서 앱을 디버깅하는 동안 Application Insights 단추를 클릭합니다.

## <a name="q03"></a> 서버 데이터 없음(데이터가 하나도 없음)
*앱을 실행한 다음, Microsoft Azure에서 Application Insights 서비스를 열었지만, 모든 차트에 '수집하는 방법을 알아보세요...' 또는 '구성되지 않았습니다' 메시지가 표시됩니다.* 또는 *페이지 보기와 사용자 데이터만 표시되고 서버 데이터는 표시되지 않습니다.*

* Visual Studio의 디버그 모드에서 애플리케이션을 실행합니다(F5). 애플리케이션을 사용하여 원격 분석을 생성합니다. Visual Studio 출력 창에서 기록된 이벤트를 볼 수 있는지 확인합니다.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Application Insights 포털에서 [진단 검색](../../azure-monitor/app/diagnostic-search.md)을 엽니다. 일반적으로 데이터는 여기에 처음으로 나타납니다.
* 새로고침 단추를 클릭합니다. 블레이드 자체는 주기적으로 새로 고쳐지지만 수동으로 새로 고칠 수도 있습니다. 시간 범위가 커지면 새로 고침 간격이 길어집니다.
* 계측 키가 일치하는지 확인합니다. Application Insights 포털에서 내 앱의 기본 블레이드로 이동한 다음 **기본 정보** 드롭다운 목록에서 **계측 키**를 확인합니다. 그런 다음, Visual Studio의 프로젝트에서 ApplicationInsights.config를 열고 `<instrumentationkey>`를 찾습니다. 두 키가 같은지 확인합니다. 같이 않으면  
  * 포털에서 Application Insights를 클릭하고 오른쪽 키를 사용하여 앱 리소스를 찾아봅니다. 또는
  * Visual Studio 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights, 구성을 차례로 선택합니다. 올바른 리소스에 원격 분석을 보내도록 앱을 다시 설정합니다.
  * 일치하는 키를 찾을 수 없는 경우 포털에서 사용한 것과 동일한 로그인 자격 증명을 Visual Studio에서 사용하고 있는지 확인합니다.
* [Microsoft Azure 홈 대시보드](https://portal.azure.com)에서 서비스 상태 맵을 살펴봅니다. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 애플리케이션 블레이드를 닫고 다시 엽니다.
* 또한 [상태 블로그](https://blogs.msdn.microsoft.com/servicemap-status/)를 확인합니다.
* `TelemetryClient`인스턴스 또는 `TelemetryContext`의 계측 키가 변경될 수 있는 코드를 [서버 쪽 SDK](../../azure-monitor/app/api-custom-events-metrics.md)에 작성했습니까? 또는 너무 촘촘하게 걸러내는 [필터 또는 샘플링 구성](../../azure-monitor/app/api-filtering-sampling.md)을 작성했습니까?
* ApplicationInsights.config를 편집한 경우 [TelemetryInitializers 및 TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md)의 구성을 신중하게 확인합니다. 형식 또는 매개 변수를 잘못 명명하면 SDK에서 빈 데이터를 보내게 될 수 있습니다.

## <a name="q04"></a>페이지 보기, 브라우저, 사용량에 데이터 없음
*서버 응답 시간 및 서버 요청 차트에는 데이터가 보이는데 페이지 보기 로드 시간, 브라우저 또는 사용량 블레이드에는 데이터가 없습니다.*

데이터는 웹 페이지의 스크립트에서 비롯됩니다. 

* 기존 웹 프로젝트에 Application Insights를 추가한 경우 [스크립트를 직접 추가해야 합니다](../../azure-monitor/app/javascript.md).
* Internet Explorer가 호환성 모드에서 사이트를 표시하고 있는지 확인합니다.
* 데이터가 `dc.services.visualstudio.com`으로 전송되고 있는지 확인하려면 브라우저의 디버그 기능을 사용합니다(일부 브라우저의 경우 F12 키를 누른 다음 네트워크 선택).

## <a name="no-dependency-or-exception-data"></a>종속성 또는 예외 데이터 없음
[종속성 원격 분석](../../azure-monitor/app/asp-net-dependencies.md) 및 [예외 원격 분석](asp-net-exceptions.md)을 참조하세요.

## <a name="no-performance-data"></a>성능 데이터 없음
성능 데이터(CPU, IO 속도 등)는 [Java 웹 서비스](../../azure-monitor/app/java-collectd.md), [Windows 데스크톱 앱](../../azure-monitor/app/windows-desktop.md), [IIS Web Apps 및 서비스(상태 모니터를 설치한 경우)](../../azure-monitor/app/monitor-performance-live-website-now.md) 및 [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md)에 사용할 수 있습니다. 이 내용은 설정, 서버 아래에 있습니다.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>내 서버에 앱을 게시한 이후로 (서버) 데이터가 없음
* 실제로 Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll을 포함하여 모든 Microsoft ApplicationInsights DLL을 Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll과 함께 서버에 추가합니다.
* 방화벽에서 [일부 TCP 포트를 열어야](../../azure-monitor/app/ip-addresses.md)할 수 있습니다.
* 회사 네트워크를 벗어나 보내기 위해 프록시를 사용해야 하는 경우, Web.config에서 [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) 를 설정하십시오.
* Windows Server 2008: 다음 업데이트를 설치했는지 확인합니다. [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>데이터를 보는 데 중지되었습니다.
* [상태 블로그](https://blogs.msdn.com/b/applicationinsights-status/)를 참조하세요.
* 데이터 요소의 월간 할당량에 도달했습니까? 설정/할당량 및 가격을 열어 찾아봅니다. 그렇다면 계획을 업그레이드하거나 추가 용량에 대한 비용을 지불할 수 있습니다. [가격 체계](https://azure.microsoft.com/pricing/details/application-insights/)를 참조하세요.

## <a name="i-dont-see-all-the-data-im-expecting"></a>기대한 모든 데이터가 표시되지 않는 경우
애플리케이션이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에 대해 Application Insights SDK를 사용하는 경우 [적응 샘플링](../../azure-monitor/app/sampling.md) 기능이 작동하여 원격 분석의 백분율만 보낼 수 있습니다.

이 기능을 비활성화할 수는 있지만 그러지 않는 것이 좋습니다. 샘플링은 진단을 목적으로 관련 원격 분석이 올바르게 전송되도록 설계되었습니다.

## <a name="client-ip-address-is-0000"></a>클라이언트 IP 주소는 0.0.0.0

2018년 2월 5일에 클라이언트 IP 주소의 로깅이 제거되었다고 발표했습니다. 이 조치는 지리적 위치에 영향을 주지 않습니다.

> [!NOTE]
> IP 주소의 처음 3개의 8진수가 필요하면 [원격 분석 이니셜라이저](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer)를 사용하여 사용자 지정 특성을 추가할 수 있습니다.
> 2018년 2월 5일 이전에 수집된 데이터에는 영향을 주지 않습니다.

## <a name="wrong-geographical-data-in-user-telemetry"></a>사용자 원격 분석에 잘못된 지리적 데이터
도시, 지역 및 국가 차원이 IP 주소에서 파생되며 항상 정확하지는 않습니다. 이러한 IP 주소를 먼저 위치에 대해 처리한 다음, 저장할 위치를 0.0.0.0으로 변경합니다.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services에서 실행할 때의 "메서드를 찾을 수 없음" 예외
.NET 4.6용으로 빌드하셨나요? 4.6은 Azure Cloud Services 역할에서 자동으로 지원되지 않습니다. [각 역할에 4.6을 설치](../../cloud-services/cloud-services-dotnet-install-dotnet.md) 합니다.

## <a name="troubleshooting-logs"></a>문제 해결 로그

프레임워크에 대한 문제 해결 로그를 캡처하려면 다음 지침을 따릅니다.

### <a name="net-framework"></a>.NET Framework

1. NuGet에서 [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) 패키지를 설치합니다. 설치한 버전이 현재 설치된 `Microsoft.ApplicationInsighs` 버전과 같아야 합니다.

2. 다음을 포함하도록 applicationinsights.config 파일을 수정합니다.

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    애플리케이션에는 구성된 위치에 대한 쓰기 권한이 있어야 합니다.

3. SDK를 통해 이러한 새 설정이 선택되도록 프로세스를 다시 시작

4. 완료되면 이러한 변경 내용을 되돌립니다.

### <a name="net-core"></a>.NET Core

1. 설치 합니다 [Microsoft.AspNetCore.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.ApplicationInsights.HostingStartup) NuGet에서 패키지 있습니다. 설치한 버전이 현재 설치된 `Microsoft.ApplicationInsights` 버전과 같아야 합니다.

2. `Startup.cs` 클래스의 `ConfigureServices` 메서드를 수정합니다.

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    애플리케이션에는 구성된 위치에 대한 쓰기 권한이 있어야 합니다.

3. SDK를 통해 이러한 새 설정이 선택되도록 프로세스를 다시 시작

4. 완료되면 이러한 변경 내용을 되돌립니다.

## <a name="still-not-working"></a>여전히 작동하지 않습니다.
* [Application Insights 포럼](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
