---
title: 애플리케이션 성능 FAQ - Azure App Service | Microsoft Docs
description: Azure App Service의 Web Apps 기능에서 가용성, 성능 및 애플리케이션 문제에 대한 질문과 대답을 확인합니다.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: ae59f54de9c02bfc4e538dd10c809ba398d599ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61222243"
---
> [!NOTE]
> 일부는 아래 지침 에서만 작동할 수 있습니다 Windows 또는 Linux App Services. 예를 들어, Linux App Services 기본적으로 64 비트 모드에서 실행 합니다.
>

# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Azure의 Web Apps에 대한 애플리케이션 성능 FAQ

이 문서에는 [Azure App Service의 Web Apps 기능](https://azure.microsoft.com/services/app-service/web/) 관련 애플리케이션 성능 문제에 대한 FAQ(질문과 대답)가 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>내 앱이 느린 이유는 무엇인가요?

느린 앱 성능의 원인에는 여러 요인이 관련될 수 있습니다. 자세한 문제 해결 단계는 [느린 웹앱 성능 문제 해결](troubleshoot-performance-degradation.md)을 참조하세요.

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>높은 CPU 사용 시나리오의 문제는 어떻게 해결하나요?

일부 높은 CPU 사용 시나리오의 경우 앱에는 실제로 더 많은 컴퓨팅 리소스가 필요할 수 있습니다. 이 경우 애플리케이션이 필요한 모든 리소스를 얻도록 상위 서비스 계층으로 크기를 조정하는 것이 좋습니다. 다른 경우에 높은 CPU 사용은 잘못된 반복이나 코딩 사례로 인해 발생할 수 있습니다. CPU 사용 증가를 트리거하는 항목을 파악하는 프로세스는 두 부분으로 구성됩니다. 먼저 프로세스 덤프를 만들고 프로세스 덤프를 분석합니다. 자세한 내용은 [Capture and analyze a dump file for high CPU consumption for Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/)(Web Apps의 높은 CPU 사용에 대한 덤프 파일 캡처 및 분석)를 참조하세요.

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>높은 메모리 사용 시나리오의 문제는 어떻게 해결하나요?

일부 높은 메모리 사용 시나리오의 경우 앱에는 실제로 더 많은 컴퓨팅 리소스가 필요할 수 있습니다. 이 경우 애플리케이션이 필요한 모든 리소스를 얻도록 상위 서비스 계층으로 크기를 조정하는 것이 좋습니다. 다른 경우에는 코드의 버그로 인해 메모리 누수가 발생할 수 있습니다. 코딩 사례가 메모리 사용을 증가시킬 수도 있습니다. 높은 메모리 사용을 트리거하는 항목을 파악하는 프로세스는 두 부분으로 구성됩니다. 먼저 프로세스 덤프를 만들고 프로세스 덤프를 분석합니다. Azure Site Extension Gallery의 Crash Diagnoser가 이러한 단계를 둘 다 효율적으로 수행할 수 있습니다. 자세한 내용은 [Capture and analyze a dump file for intermittent high memory for Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/)(Web Apps의 간헐적 높은 메모리에 대한 덤프 파일 캡처 및 분석)를 참조하세요.

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>PowerShell를 사용하여 App Service Web Apps를 어떻게 자동화할 수 있나요?

PowerShell cmdlet을 사용하여 App Service Web Apps를 관리 및 유지 관리할 수 있습니다. 블로그 게시물 [Automate web apps hosted in Azure App Service by using PowerShell](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)(PowerShell을 사용하여 Azure App Service에 호스트된 Web Apps 자동화)에서 Azure Resource Manager 기반 PowerShell cmdlet을 사용하여 일반적인 작업을 자동화하는 방법을 설명합니다. 이 블로그 게시물에는 다양한 Web Apps 관리 작업에 대한 샘플 코드도 있습니다. 모든 App Service 웹앱 cmdlet에 대한 설명과 구문은 [Az.Websites](/powershell/module/az.websites)를 참조하세요.

## <a name="how-do-i-view-my-web-apps-event-logs"></a>내 웹앱의 이벤트 로그는 어떻게 볼 수 있나요?

웹앱의 이벤트 로그를 보려면:

1. [Kudu 웹 사이트](https://*yourwebsitename*.scm.azurewebsites.net)에 로그인합니다.
2. 메뉴에서 **디버그 콘솔** > **CMD**를 선택합니다.
3. **LogFiles** 폴더를 선택합니다.
4. 이벤트 로그를 보려면 **eventlog.xml** 옆에 있는 연필 아이콘을 선택합니다.
5. 로그를 다운로드하려면 PowerShell cmdlet `Save-AzureWebSiteLog -Name webappname`을 실행합니다.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>내 웹앱의 사용자 모드 메모리 덤프는 어떻게 캡처할 수 있나요?

웹앱의 사용자 모드 메모리 덤프를 캡처하려면:

1. [Kudu 웹 사이트](https://*yourwebsitename*.scm.azurewebsites.net)에 로그인합니다.
2. **프로세스 탐색기** 메뉴를 선택합니다.
3. **w3wp.exe** 프로세스 또는 WebJob 프로세스를 마우스 오른쪽 단추로 클릭합니다.
4. **Download Memory Dump**(메모리 덤프 다운로드) > **Full Dump**(전체 덤프)를 선택합니다.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>내 웹앱의 프로세스 수준 정보는 어떻게 볼 수 있나요?

웹앱에 대한 프로세스 수준 정보를 보는 데는 두 가지 옵션이 있습니다.

*   Azure Portal에서 다음을 수행합니다.
    1. 웹앱에 대한 **프로세스 탐색기**를 선택합니다.
    2. 세부 정보를 확인하려면 **w3wp.exe** 프로세스를 선택합니다.
*   Kudu 콘솔에서:
    1. [Kudu 웹 사이트](https://*yourwebsitename*.scm.azurewebsites.net)에 로그인합니다.
    2. **프로세스 탐색기** 메뉴를 선택합니다.
    3. **w3wp.exe** 프로세스에 대한 **속성**을 선택합니다.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>내 앱으로 이동할 때 “오류 403 - 이 웹앱이 중지되었습니다.”가 표시되었습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 오류는 세 가지 조건으로 인해 발생할 수 있습니다.

* 웹앱이 청구 한도에 도달했으며 사용하지 않도록 설정되었습니다.
* 웹앱이 포털에서 중지되었습니다.
* 웹앱이 무료 또는 공유 크기 조정 서비스 계획에 적용될 수 있는 리소스 할당량 한도에 도달했습니다.

오류의 원인을 확인하고 문제를 해결하려면 [Web Apps: "Error 403 – This web app is stopped"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/)(Web Apps: "오류 403 – 이 웹앱이 중지되었습니다.")의 단계를 따릅니다.

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>다양한 App Service 계획의 할당량 및 한도에 대한 자세한 내용은 어디서 알아볼 수 있나요?

할당량 및 한도에 대한 자세한 내용은 [App Service 한도](../azure-subscription-service-limits.md#app-service-limits)를 참조하세요. 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>유휴 시간 후 첫 번째 요청에 대한 응답 시간을 어떻게 줄일 수 있나요?

기본적으로 웹앱은 일정 기간 유휴 상태인 경우 언로드됩니다. 이를 통해 시스템이 리소스를 절약할 수 있습니다. 단점은 웹앱이 로드되고 응답 제공을 시작할 수 있도록 웹앱이 언로드된 후 첫 번째 요청에 대한 응답 시간이 더 길어진다는 것입니다. 기본 및 표준 서비스 계획에서 **무중단** 설정을 켜서 항상 앱을 계속 로드할 수 있습니다. 이렇게 하면 앱이 유휴 상태인 후에 로드 시간이 길어지지 않습니다. **무중단** 설정을 변경하려면:

1. Azure Portal에서 웹앱으로 이동합니다.
2. **애플리케이션 설정**을 선택합니다.
3. **무중단**에 대해 **켜기**를 선택합니다.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>실패한 요청 추적을 어떻게 켜나요?

실패한 요청 추적을 켜려면:

1. Azure Portal에서 웹앱으로 이동합니다.
3. **모든 설정** > **진단 로그**를 선택합니다.
4. **실패한 요청 추적**에 대해 **켜기**를 선택합니다.
5. **저장**을 선택합니다.
6. 웹앱 블레이드에서 **도구**를 선택합니다.
7. **Visual Studio Online**을 선택합니다.
8. 설정이 **켜기**가 아니면 **켜기**를 선택합니다.
9. **이동**을 선택합니다.
10. **Web.config**를 선택합니다.
11. system.webServer에서 이 구성을 추가합니다(특정 URL을 캡처하기 위해).

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. 느린 성능 문제를 해결하려면 이 구성을 추가합니다(캡처 요청에 30초 이상 걸리는 경우).
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. 실패한 요청 추적을 다운로드하려면 [Portal](https://portal.azure.com)에서 웹 사이트로 이동합니다.
15. **도구** > **Kudu** > **이동**을 선택합니다.
18. 메뉴에서 **디버그 콘솔** > **CMD**를 선택합니다.
19. **LogFiles** 폴더를 선택하고 이름이 **W3SVC**로 시작하는 폴더를 선택합니다.
20. XML 파일을 확인하려면 연필 아이콘을 선택합니다.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>“’메모리 비율’ 한도로 인해 작업 프로세스가 재생을 요청했습니다.”라는 메시지가 표시됩니다. 이 문제를 어떻게 해결하나요?

32비트 프로세스(64비트 운영 체제에서도)에 대한 최대 사용 가능한 메모리 양은 2GB입니다. 기본적으로 작업자 프로세스는 App Service에서 32비트로 설정됩니다(레거시 웹 애플리케이션과의 호환성을 위해).

웹 작업자 역할에서 사용 가능한 추가 메모리를 사용할 수 있도록 64비트 프로세스로 전환하는 것을 고려하세요. 이렇게 하면 웹앱 다시 시작이 트리거되므로 이에 따라 일정이 트리거됩니다.

또한 64비트 환경에는 기본 또는 표준 서비스 계획이 필요합니다. 무료 및 공유 계획은 항상 32비트 환경에서 실행됩니다.

자세한 내용은 [Azure App Service에서 웹앱 구성](web-sites-configure.md)을 참조하세요.

## <a name="why-does-my-request-time-out-after-230-seconds"></a>230초 후 요청 시간이 초과되는 이유는 무엇인가요?

Azure Load Balancer에는 4분의 기본 유휴 시간 제한 설정이 있습니다. 이 설정은 일반적으로 웹 요청에 대한 합리적인 응답 시간 제한입니다. 웹앱에 백그라운드 처리가 필요한 경우 Azure WebJobs를 사용하는 것이 좋습니다. Azure 웹앱은 WebJobs를 호출하고 백그라운드 처리가 완료되면 알림을 받을 수 있습니다. 큐 및 트리거를 포함하여 WebJobs를 사용하기 위한 여러 방법 중에서 선택할 수 있습니다.

WebJobs는 백그라운드에서 처리되도록 디자인됩니다. WebJob에서 원하는 만큼 백그라운드 처리를 수행할 수 있습니다. WebJobs에 대한 자세한 내용은 [WebJob으로 백그라운드 작업 실행](webjobs-create.md)을 참조하세요.

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>App Service에서 호스트되는 ASP.NET Core 애플리케이션이 때때로 응답을 중지합니다. 이 문제를 어떻게 해결하나요?

초기 [Kestrel 버전](https://github.com/aspnet/KestrelHttpServer/issues/1182)에서 알려진 문제로 인해 App Service에서 호스트되는 ASP.NET Core 1.0 앱이 간헐적으로 응답을 중지할 수 있습니다. “지정한 CGI 애플리케이션에서 오류가 발생하여 서버에서 프로세스가 종료되었습니다.”라는 메시지가 표시될 수도 있습니다.

이 문제는 Kestrel 버전 1.0.2에서 해결되었습니다. 이 버전은 ASP.NET Core 1.0.3 업데이트에 포함되어 있습니다. 이 문제를 해결하려면 Kestrel 1.0.2를 사용하도록 앱 종속성을 업데이트해야 합니다. 또는 블로그 게시물 [ASP.NET Core 1.0 slow perf issues in App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)(App Service Web Apps의 ASP.NET Core 1.0 느린 성능 문제)에 설명된 두 가지 해결 방법 중 하나를 사용할 수 있습니다.


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>내 웹앱의 파일 구조에서 로그 파일을 찾을 수 없습니다. 어떻게 찾을 수 있나요?

App Service의 로컬 캐시 기능을 사용할 경우 App Service 인스턴스에 대한 LogFiles 및 Data 폴더의 폴더 구조에 영향을 미칩니다. 로컬 캐시가 사용될 경우 하위 폴더는 저장소 LogFiles 및 Data 폴더에 생성됩니다. 하위 폴더에는 명명 패턴 "고유 식별자" + 타임스탬프가 사용됩니다. 각 하위 폴더는 웹앱이 실행 중이거나 실행된 VM 인스턴스에 해당합니다.

로컬 캐시를 사용 중인지 확인하려면 App Service **애플리케이션 설정** 탭을 확인합니다. 로컬 캐시가 사용되고 있으면 앱 설정 `WEBSITE_LOCAL_CACHE_OPTION`이 `Always`로 설정되어 있습니다.

로컬 캐시를 사용하고 있지 않고 이 문제가 발생하지 않으면 지원 요청을 제출합니다.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>“액세스 권한에 의해 숨겨진 소켓에 액세스를 시도했습니다.”라는 메시지가 표시됩니다. 이 문제를 해결하려면 어떻게 해야 하나요?

일반적으로 이 오류는 아웃바운드 TCP 연결을 모두 사용한 경우 발생합니다. App Service에서는 각 VM 인스턴스에 대해 생성될 수 있는 최대 아웃바운드 연결 수에 대한 제한이 적용됩니다. 자세한 내용은 [Cross-VM numerical limits](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)(VM 간 숫자 제한)를 참조하세요.

이 오류는 애플리케이션에서 로컬 주소에 액세스하려는 경우에도 발생할 수 있습니다. 자세한 내용은 [Local address requests](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)(로컬 주소 요청)를 참조하세요.

웹앱의 아웃바운드 연결에 대한 자세한 내용은 [Azure Websites에 대한 아웃바운드 연결](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)에 관한 블로그 게시물을 참조하세요.

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>내 App Service 웹앱을 원격 디버그하는 데 Visual Studio를 사용하려면 어떻게 해야 하나요?

Visual Studio를 사용하여 웹앱을 디버그하는 방법을 보여 주는 자세한 연습은 [Remote debug your App Service web app](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/)(App Service 웹앱 원격 디버그)을 참조하세요.
