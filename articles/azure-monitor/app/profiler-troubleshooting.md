---
title: Azure Application Insights Profiler 관련 문제 해결 | Microsoft Docs
description: 이 문서에는 Application Insights Profiler를 사용하도록 설정하거나 사용하는 데 문제가 있는 개발자에게 도움이 되는 문제 해결 단계 및 정보가 나와 있습니다.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 6b57ffbd3cb2b31da3fc2882e941f9788d83fea8
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341667"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Application Insights Profiler를 사용하도록 설정하거나 볼 때 발생하는 문제 해결

## <a id="troubleshooting"></a>일반적인 문제 해결

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profiler가 실행되는 동안 애플리케이션에 대한 요청이 있을 때만 프로필에 업로드됨

Azure Application Insights Profiler는 1시간마다 2분 동안 프로파일링 데이터를 수집합니다. 또한 **Application Insights Profiler 구성** 창에서 **지금 프로필** 단추를 선택해도 데이터를 수집합니다. 하지만 프로파일링 데이터는 Profiler가 실행되는 동안 발생한 요청에 첨부할 수 있을 때만 업로드됩니다. 

Profiler는 Application Insights 리소스에 추적 메시지 및 사용자 지정 이벤트를 씁니다. 해당 이벤트를 사용하여 Profiler의 실행 방식을 확인할 수 있습니다. Profiler가 실행 중이며 추적을 캡처해야 하는데 **성능** 창에 추적이 표시되지 않으면 Profiler의 실행 방식을 확인할 수 있습니다.

1. Profiler가 Application Insights 리소스에 보낸 추적 메시지 및 사용자 지정 이벤트를 검색합니다. 이러한 검색 문자열을 사용하여 관련 데이터를 찾을 수 있습니다.

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    다음 이미지에는 두 AI 리소스에서 수행한 검색의 두 가지 예가 나와 있습니다. 
    
   * 왼쪽 예의 경우 Profiler가 실행되는 동안 애플리케이션이 요청을 수신하지 않습니다. 그리고 활동이 없어서 업로드가 취소되었음을 설명하는 메시지가 표시됩니다. 

   * 오른쪽 예의 경우에는 Profiler가 시작되었으며, Profiler가 실행되는 동안 발생한 요청이 검색되면 사용자 지정 이벤트가 전송되었습니다. ServiceProfilerSample 사용자 지정 이벤트가 표시되는 경우 Profiler가 추적을 요청에 첨부한 것이며, 그러면 **Application Insights 성능** 창에서 추적을 확인할 수 있습니다.

     원격 분석이 표시되지 않으면 Profiler 실행되고 있지 않은 것입니다. 이러한 문제를 해결하려면 이 문서 뒷부분에서 사용 중인 특정 앱 유형에 해당하는 문제 해결 섹션을 참조하세요.  

     ![Profiler 원격 분석 검색][profiler-search-telemetry]

1. Profiler가 실행되는 동안 요청이 발생한 경우 Profiler가 사용하도록 설정된 애플리케이션 부분에서 해당 요청을 처리했는지 확인합니다. 애플리케이션은 여러 구성 요소를 포함할 수도 있지만 Profiler는 일부 구성 요소에서만 사용하도록 설정됩니다. 추적이 업로드된 구성 요소는 **Application Insights Profiler 구성** 창에 표시됩니다.

### <a name="other-things-to-check"></a>기타 확인해야 할 사항
* 앱이 .NET Framework 4.6에서 실행 중인지 확인합니다.
* 웹앱이 ASP.NET Core 애플리케이션인 경우 ASP.NET Core 2.0 이상을 실행해야 합니다.
* 확인하려는 데이터가 생성된 지 2주 이상 지난 경우에는 시간 필터의 범위를 제한한 후에 다시 시도해 봅니다. 추적은 7일 후에 삭제됩니다.
* 프록시 또는 방화벽이 [https://gateway.azureserviceprofiler.net](https://gateway.azureserviceprofiler.net ) 에 대한 액세스를 차단하지 않았는지 확인합니다.

### <a id="double-counting"></a>병렬 스레드에서 이중 계산

경우에 따라 스택 뷰어의 총 시간 메트릭은 요청 기간 이상입니다.

요청과 연결된 둘 이상의 스레드가 병렬로 작동 중이면 이러한 상황이 발생할 수 있습니다. 이 경우 총 스레드 시간은 경과된 시간 이상입니다. 하나의 스레드가 다른 스레드가 완료될 때까지 대기 중일 수 있습니다. 뷰어는 이러한 상황의 검색을 시도하여 불필요한 대기를 생략합니다. 그런데 이 과정에서 중요할 수 있는 정보를 생략할 위험을 방지하기 위해 너무 많은 정보가 표시되는 문제가 발생합니다.

추적에 병렬 스레드가 표시되면 대기 중인 스레드를 확인하여 요청에 중요한 경로를 파악합니다. 대기 상태로 빠르게 전환되는 스레드는 다른 스레드를 기다리는 중일 뿐인 경우가 많습니다. 다른 스레드에 집중하고 대기 중인 스레드 대기 시간을 무시합니다.

### <a name="error-report-in-the-profile-viewer"></a>프로파일링 뷰어의 오류 보고서
포털에서 지원 티켓을 제출합니다. 오류 메시지의 상관 관계 ID를 포함해야 합니다.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Azure App Service에서 Profiler 문제 해결
Profiler가 제대로 작동하도록 하려면 다음 조건을 충족해야 합니다.
* 웹앱 서비스 계획이 기본 계층 이상이어야 합니다.
* 웹앱에서 Application Insights를 사용할 수 있어야 합니다.
* 웹 앱에는 다음 앱 설정이 있어야 합니다.

    |앱 설정    | 값    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스의 iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* **ApplicationInsightsProfiler3** 웹 작업이 실행되고 있어야 합니다. 웹 작업을 확인하려면 다음 단계를 수행합니다.
   1. [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)로 이동합니다.
   1. **도구** 메뉴에서 **웹 작업 대시보드**를 선택합니다.  
      **웹 작업** 창이 열립니다. 
   
      ![profiler-webjob]   
   
   1. 로그를 포함 하 여, 즉 webjob의 세부 정보를 보려면 선택 합니다 **ApplicationInsightsProfiler3** 링크 합니다.  
     **지속적인 웹 작업 세부 정보** 창이 열립니다.

      ![profiler-webjob-log]

Profiler를 작동 하지 않는 이유를 파악할 수 없는 경우 로그를 다운로드 하 고 지원이 필요한 경우 팀에 게 보낼 수 serviceprofilerhelp@microsoft.com입니다. 
    
### <a name="manual-installation"></a>수동 설치

Profiler를 구성하면 웹앱의 설정에 업데이트가 이루어집니다. 사용 중인 환경에 필요한 경우 수동으로 업데이트를 적용할 수 있습니다. 예제에서 애플리케이션이 PowerApps의 Web Apps 환경에서 실행될 수 있습니다. 수동으로 업데이트를 적용 합니다.

1. **Web App 제어** 창에서 **설정**을 엽니다.

1. 설정할 **.NET Framework 버전** 하 **v4.6**합니다.

1. **무중단**을 **사용**으로 설정합니다.
1. 이러한 앱 설정을 만듭니다.

    |앱 설정    | 값    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 리소스의 iKey    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>너무 많은 활성 프로파일링 세션

현재 동일한 서비스 계획으로 실행 중인 최대 4개의 Azure 웹앱 및 배포 슬롯에서 Profiler를 사용하도록 설정할 수 있습니다. App Service 계획 하나에서 웹앱을 5개 이상 실행 중인 경우 Profiler에서 *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*을 throw할 수 있습니다. Profiler는 각 웹앱에 대해 개별적으로 실행되며 각 앱에 대한 ETW(Windows용 이벤트 추적) 세션을 시작하려고 합니다. 하지만 한 번에 활성화할 수 있는 ETW 세션의 수는 제한되어 있습니다. Profiler 웹 작업이 너무 많은 활성 프로파일링 세션을 보고하는 경우 일부 웹앱을 다른 Service 계획으로 이동합니다.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>배포 오류: 디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Profiler를 사용하는 Web Apps 리소스에 웹앱을 다시 배포하는 경우 다음과 같은 메시지가 표시될 수 있습니다.

*디렉터리가 비어 있지 않음 'D:\\home\\site\\wwwroot\\App_Data\\jobs'*

스크립트 또는 Azure DevOps 배포 파이프라인에서 웹 배포를 실행하는 경우 이 오류가 발생합니다. 솔루션은 웹 배포 작업에 다음과 같은 배포 매개 변수를 더 추가합니다.

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

이러한 매개 변수는 Application Insights Profiler에서 사용하는 폴더를 삭제하고 재배포 프로세스의 차단을 해제합니다. 현재 실행 중인 Profiler 인스턴스에는 영향을 주지 않습니다.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Application Insights Profiler가 실행되고 있는지 어떻게 알 수 있나요?

Profiler는 웹앱에서 지속적인 웹 작업으로 실행됩니다. [Azure Portal](https://portal.azure.com)에서 웹앱 리소스를 열 수 있습니다. **WebJobs** 창에서 **ApplicationInsightsProfiler**의 상태를 확인합니다. 실행되지 않는 경우 **로그**를 열어 자세한 정보를 찾습니다.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Profiler 및 Azure Diagnostics 문제 해결

>**Cloud services는 WAD에서 제공 되는 프로파일러 버그 수정 되었습니다.** 최신 버전의 Cloud Services에 대 한 WAD (1.12.2.0) 모든 최신 버전의 App Insights SDK를 사용 하 여 작동합니다. 클라우드 서비스 호스트는 WAD를 자동으로 업그레이드 되지만 바로 실행 되지 않습니다. 업그레이드를 적용 하려면 서비스를 다시 배포할 수도 있고 노드를 다시 부팅 수 있습니다.

Azure Diagnostics를 통해 Profiler가 올바르게 구성되어 있는지 여부를 확인하려면 다음의 세 가지 작업을 수행합니다. 
1. 먼저 배포된 Azure Diagnostics 구성의 내용이 필요한 구성인지를 확인합니다. 

1. 다음으로 Azure Diagnostics가 Profiler 명령줄에서 적절한 iKey를 전달하는지 확인합니다. 

1. 셋째로는 Profiler 로그 파일에서 Profiler가 실행되었으나 오류가 반환되었는지 여부를 확인합니다. 

Azure Diagnostics를 구성하는 데 사용된 설정을 확인하려면 다음 단계를 수행합니다.

1. 가상 컴퓨터 (VM)에 로그인 한 후이 위치의 로그 파일을 엽니다. (드라이브 c: 수 또는 d: 및 플러그 인 버전 다를 수 있습니다.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    또는
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. 해당 파일에서 **WadCfg** 문자열을 검색하면 Azure Diagnostics를 구성하기 위해 VM에 전달된 설정을 확인할 수 있습니다. Profiler 싱크에서 사용하는 iKey가 올바른지 확인할 수 있습니다.

1. Profiler를 시작하는 데 사용되는 명령줄을 확인합니다. Profiler를 시작 하는 데 사용 되는 인수는 다음 파일에는. (드라이브 c: 또는 d:를 수 있습니다.)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Profiler 명령줄의 ikey가 올바른지 확인합니다. 

1. 위의 *config.json* 파일에 있는 경로를 사용하여 Profiler 로그 파일을 확인합니다. 이 파일에는 Profiler가 사용하는 설정을 나타내는 디버그 정보가 표시됩니다. 또한 Profiler의 상태 및 오류 메시지도 표시됩니다.  

    애플리케이션이 요청을 수신하는 동안 Profiler가 실행되는 경우 다음 메시지가 표시됩니다. *iKey에서 활동이 검색되었습니다*. 

    추적을 업로드하는 동안에는 다음 메시지가 표시됩니다. *추적 업로드를 시작합니다*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








