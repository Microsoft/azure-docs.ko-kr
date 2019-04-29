---
title: 앱에 대한 진단 로깅 사용 - Azure App Service
description: 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835722"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service에서 앱에 대한 진단 로깅 사용
## <a name="overview"></a>개요
Azure는 [App Service 앱](https://go.microsoft.com/fwlink/?LinkId=529714)을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. 이 문서에서는 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법을 설명합니다.

이 문서에서는 진단 로그 작업에 [Azure Portal](https://portal.azure.com)과 Azure CLI를 사용합니다. Visual Studio를 사용하여 진단 로그로 작업하는 방법에 대한 자세한 내용은 [Visual Studio에서 Azure 문제 해결](troubleshoot-dotnet-visual-studio.md)을 참조하세요.

## <a name="whatisdiag"></a>웹 서버 진단 및 애플리케이션 진단
App Service는 웹 서버와 웹 애플리케이션 모두의 정보에 로깅할 수 있도록 진단 기능을 제공합니다. 이는 논리적으로 **웹 서버 진단** 및 **애플리케이션 진단**으로 구분됩니다.

### <a name="web-server-diagnostics"></a>웹 서버 진단
다음과 같은 종류의 로그를 사용하거나 사용하지 않도록 설정할 수 있습니다.

* **자세한 오류 로깅** -HTTP 상태 코드 400 이상에 모든 요청에 대 한 정보를 자세히 설명 합니다. 여기에는 서버에서 오류 코드를 반환한 이유를 확인하는 데 도움이 되는 정보가 포함될 수 있습니다. HTML 파일은 앱의 파일 시스템에 50 오류 (파일)까지 각 오류에 대 한 생성 하나 유지 됩니다. HTML 파일 수가 50을 초과 하는 경우 가장 오래 된 26 파일 자동으로 삭제 됩니다.
* **실패한 요청 추적** - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하여 실패한 요청에 대해 자세한 정보입니다. 사이트 성능을 향상시키거나 특정 HTTP 오류를 분리하려는 경우에 유용합니다. 폴더는 앱의 파일 시스템에서 각 오류에 대해 생성 됩니다. 파일 보존 정책 위에 로깅의 자세한 오류와 동일 합니다.
* **웹 서버 로깅** - [W3C 확장 로그 파일 형식](/windows/desktop/Http/w3c-logging)을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 처리된 요청 수 또는 특정 IP 주소의 요청 수와 같은 전체 사이트 메트릭을 확인하는 경우에 유용합니다.

### <a name="application-diagnostics"></a>애플리케이션 진단
애플리케이션 진단을 통해 웹 애플리케이션에서 생성된 정보를 캡처할 수 있습니다. ASP.NET 애플리케이션은 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 클래스를 사용하여 애플리케이션 진단 로그에 정보를 로깅할 수 있습니다. 예를 들면 다음과 같습니다.

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

런타임 시 문제 해결을 위해 이러한 로그를 검색할 수 있습니다. 자세한 내용은 [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)을 참조하세요.

또한 콘텐츠를 앱에 게시하는 경우 App Service에서 배포 정보도 기록합니다. 이는 자동으로 수행되며 배포 로깅에 대한 구성 설정은 없습니다. 배포 로깅을 사용하면 배포가 실패한 이유를 확인할 수 있습니다. 예를 들어 사용자 지정 배포 스크립트를 사용하는 경우 배포 로깅을 사용하여 스크립트가 실패한 이유를 확인할 수 있습니다.

## <a name="enablediag"></a>진단을 사용하도록 설정하는 방법
[Azure Portal](https://portal.azure.com)에서 진단을 사용하려면, 앱의 페이지로 이동하여 **설정>진단 로그**를 클릭합니다.

<!-- todo:cleanup dogfood addresses in screenshot -->
![로그 부분](./media/web-sites-enable-diagnostic-log/logspart.png)

**애플리케이션 진단**을 사용하도록 설정하는 경우 **수준**도 선택합니다. 다음 표에는 각 수준에 포함되는 로그의 범주가 나와 있습니다.

| Level| 포함되는 로그 범주 |
|-|-|
|**사용 안 함** | 없음 |
|**오류** | 오류, 위험 |
|**Warning** | 경고, 오류, 위험|
|**정보** | 정보, 경고, 오류, 위험|
|**자세한 정보** | 추적, 디버그, 정보, 경고, 오류, 위험(모든 범주) |
|-|-|

**애플리케이션 로깅**의 경우 디버그를 위해 파일 시스템 옵션을 일시적으로 켤 수 있습니다. 이 옵션은 12시간 후에 자동으로 꺼집니다. 또한 Blob Storage 옵션을 켜서 로그를 쓸 Blob 컨테이너를 선택할 수도 있습니다.

> [!NOTE]
> 현재 .NET 애플리케이션 로그만 Blob Storage에 쓸 수 있습니다. Java, PHP, Node.js, Python 애플리케이션 로그는 파일 시스템에만 저장할 수 있습니다(외부 스토리지에 로그를 쓰기 위해 코드를 수정하지 않음).
>
>

**웹 서버 로깅**의 경우 **저장소** 또는 **파일 시스템**을 선택할 수 있습니다. **저장소**를 선택하면 저장소 계정을 선택하고 로그를 기록할 Blob 컨테이너를 선택할 수 있습니다. 

파일 시스템에 로그를 저장하는 경우 파일은 FTP로 액세스하거나, Azure CLI를 사용하여 Zip 아카이브로 다운로드할 수 있습니다.

기본적으로 로그는 자동으로 삭제되지 않습니다(**애플리케이션 로깅(파일 시스템)** 제외). 로그를 자동으로 삭제하려면 **보존 기간(일)** 필드를 설정합니다.

> [!NOTE]
> [저장소 계정의 선택키를 다시 생성](../storage/common/storage-create-storage-account.md)하는 경우 해당 로깅 구성을 다시 설정하여 업데이트한 키를 사용해야 합니다. 다음을 수행합니다.
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기**로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

파일 시스템 또는 Blob Storage를 원하는 방식으로 결합하여 동시에 사용하도록 설정할 수 있으며 로그 수준은 개별적으로 구성할 수 있습니다. 예를 들어 장기적인 로깅 솔루션으로 Blob Storage에 오류 및 경고를 기록하면서 파일 시스템 로깅은 세부 정보 표시 수준으로 사용할 수 있습니다.

이 두 가지 스토리지 위치는 모두 로깅된 이벤트에 대해 동일한 기본 정보를 제공하되, **Blob Storage**는 인스턴스 ID, 스레드 ID, 그리고 **파일 시스템**에 비해 상세한 타임스탬프(눈금 형식)와 같은 정보를 추가로 로깅합니다.

> [!NOTE]
> **Blob Storage**에 저장된 정보는 이러한 스토리지 시스템에서 바로 작업할 수 있는 스토리지 클라이언트 또는 애플리케이션을 사용해서만 액세스할 수 있습니다. 예를 들어, Visual Studio 2013에는 Blob Storage를 탐색하는 데 사용할 수 있는 스토리지 탐색기를 포함하고 있으며, HDInsight는 Blob Storage에 저장된 데이터에 액세스하는 데 사용될 수 있습니다. 또한 [Azure SDK](https://azure.microsoft.com/downloads/)중 하나를 사용하여 Azure Storage에 액세스하는 애플리케이션을 작성할 수도 있습니다.
>

## <a name="download"></a> 방법: 로그 다운로드
앱 파일 시스템에 저장된 진단 정보는 FTP를 사용하여 직접 액세스할 수 있습니다. Azure CLI를 사용하여 Zip 아카이브로 다운로드할 수도 있습니다.

로그가 저장되는 디렉터리 구조는 다음과 같습니다.

* **애플리케이션 로그** - /LogFiles/Application/입니다. 이 폴더에는 애플리케이션 로깅으로 생성된 정보가 포함된 하나 이상의 텍스트 파일이 포함됩니다.
* **실패한 요청 추적** - /LogFiles/W3SVC#########/입니다. 이 폴더에는 하나의 XSL 파일 및 하나 이상의 XML 파일이 포함되어 있습니다. XSL 파일은 XML 파일을 Internet Explorer에서 볼 때 XML 파일의 내용에 서식을 지정하고 필터링하는 기능을 제공하므로 XSL 파일은 XML 파일과 동일한 디렉터리에 다운로드해야 합니다.
* **Detailed Error Logs** - /LogFiles/DetailedErrors/입니다. 이 폴더에는 발생한 HTTP 오류에 대해 방대한 정보를 제공하는 하나 이상의 .htm 파일이 포함되어 있습니다.
* **웹 서버 로그** - /LogFiles/http/RawLogs입니다. 이 폴더에는 [W3C 확장 로그 파일 형식](/windows/desktop/Http/w3c-logging)을 사용하여 서식이 지정된 하나 이상의 텍스트 파일이 포함되어 있습니다.
* **Deployment logs** - /LogFiles/Git입니다. 이 폴더에는 Azure App Service에서 사용된 내부 배포 프로세스에서 생성된 로그와 Git 배포용 로그가 포함되어 있습니다. D:\home\site\deployments에서 배포 로그를 찾을 수도 있습니다.

### <a name="ftp"></a>FTP

앱의 FTP 서버에 대한 FTP 연결을 열려면 [FTPFTP/S를 사용하여 앱에 Azure App Service에 배포](deploy-ftp.md)를 참조하세요.

앱의 FTP/S 서버에 연결되면 로그 파일이 저장되어 있는 **LogFiles** 폴더를 엽니다.

### <a name="download-with-azure-cli"></a>Azure CLI를 사용하여 다운로드
Azure 명령줄 인터페이스를 사용하여 로그 파일을 다운로드하려면 새 명령 프롬프트, PowerShell, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

    az webapp log download --resource-group resourcegroupname --name appname

이 명령은 'appname' 라는 파일에는 앱에 대 한 로그 저장 **webapp_logs.zip** 현재 디렉터리에 있습니다.

> [!NOTE]
> Azure CLI를 설치하지 않았거나 Azure 구독을 사용하도록 구성하지 않은 경우 [Azure CLI를 사용하는 방법](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)을 참조하세요.
>
>

## <a name="how-to-view-logs-in-application-insights"></a>방법: Application Insights에서 로그 보기
Visual Studio Application Insights는 로그 필터링과 검색을 위한 도구 및 요청과 다른 이벤트와 로그를 연결하기 위한 도구를 제공합니다.

1. Visual Studio의 프로젝트에 Application Insights SDK 추가
   * 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가를 선택합니다. 인터페이스는 Application Insights 리소스를 만드는 방법을 포함한 단계를 설명합니다. [자세히 알아보기](../azure-monitor/app/asp-net.md)
2. 추적 수신기 패키지를 프로젝트에 추가 합니다.
   * 프로젝트를 마우스 오른쪽 단추로 클릭하고 NuGet 패키지 관리를 선택합니다. 자동으로 로그를 삭제하려면 `Microsoft.ApplicationInsights.TraceListener` [자세히 알아보기](../azure-monitor/app/asp-net-trace-logs.md)
3. 프로젝트를 업로드하고 실행하여 로그 데이터를 생성합니다.
4. [Azure Portal](https://portal.azure.com/)에서 새 Application Insights 리소스를 찾아서 **검색**을 엽니다. 요청, 사용량 및 다른 원격 분석과 함께 로그 데이터가 표시됩니다. 일부 원격 분석에 몇 분 정도 걸릴 수 있습니다. 새로 고침을 클릭합니다. [자세히 알아보기](../azure-monitor/app/diagnostic-search.md)

[Application Insights로 추적되는 성능에 대해 알아보기](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> 방법: 로그 스트리밍
애플리케이션을 개발하는 동안 거의 실시간의 로깅 정보를 보는 것이 종종 유용합니다. Azure CLI를 개발 환경에 로깅 정보를 스트리밍할 수 있습니다.

> [!NOTE]
> 일부 유형의 로깅 버퍼는 로그 파일에 기록하고 이로 인해 스크림에서 이벤트가 작동하지 않을 수 있습니다. 예를 들어 사용자가 페이지를 방문할 때 발생한 애플리케이션 로그 항목이 페이지 요청에 대한 해당 HTTP 로그 항목보다 먼저 스트림에 표시될 수 있습니다.
>
> [!NOTE]
> 로그 스트리밍은 **D:\\home\\LogFiles\\** 폴더에 저장된 모든 텍스트 파일에 기록된 정보도 스트리밍합니다.
>
>

### <a name="streaming-with-azure-cli"></a>Azure CLI를 사용하여 스트리밍
로깅 정보를 스트리밍하려면 새 명령 프롬프트, PowerShell, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

    az webapp log tail --name appname --resource-group myResourceGroup

이 명령은 'appname'이라는 웹앱에 연결하고 로그 이벤트가 앱에서 발생하면 창에 정보를 스트리밍하기 시작합니다. /LogFiles 디렉터리(d:/home/logfiles)에 저장된 .txt, .log 또는 .htm으로 끝나는 파일에 기록된 정보는 로컬 콘솔로 스트리밍됩니다.

오류와 같은 특정 이벤트를 필터링하려면 **--Filter** 매개 변수를 사용합니다. 예를 들면 다음과 같습니다.

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

HTTP와 같은 특정 로그 유형을 필터링하려면 **-Path** 매개 변수를 사용합니다. 예를 들면 다음과 같습니다.

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Azure CLI를 설치하지 않았거나 Azure 구독을 사용하도록 구성하지 않은 경우 [Azure CLI를 사용하는 방법](../cli-install-nodejs.md)을 참조하세요.
>
>

## <a name="understandlogs"></a> 방법: 진단 로그 이해
### <a name="application-diagnostics-logs"></a>애플리케이션 진단 로그
애플리케이션 진단은 로그가 파일 시스템과 Blob Storage 중 어디에 저장되는지에 따라 .NET 애플리케이션용 형식으로 정보를 저장합니다. 

이벤트가 발생한 날짜 및 시간, 이벤트가 생성된 프로세스 ID, 이벤트 형식(정보, 경고, 오류), 이벤트 메시지 등 저장된 데이터의 기본 집합은 두 스토리지 형식에서 동일합니다. 파일 시스템에서는 로그 파일이 거의 즉각적으로 업데이트되므로 문제 해결을 위해 즉각적인 액세스가 필요한 경우에는 로그 저장을 위해 파일 시스템을 사용하는 것이 유용합니다. 파일이 캐시된 다음, 일정에 따라 스토리지 컨테이너로 플러시되므로 Blob 스토리지는 보관 용도로 사용됩니다.

**파일 시스템**

파일 시스템에 로깅되거나 스트리밍을 통해 수신된 각 줄은 다음 형식과 같습니다.

    {Date}  PID[{process ID}] {event type/level} {message}

예를 들어 오류 이벤트는 다음 샘플과 비슷하게 나타납니다.

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

파일 시스템에 로깅하면 사용 가능한 세 가지 메서드의 가장 기본적인 정보를 제공하며 시간, 프로세스 ID, 이벤트 수준 및 메시지만 제공합니다.

**Blob Storage**

Blob Storage에 로깅하는 경우 데이터는 쉼표로 구분된 값(CSV) 형식으로 저장됩니다. 이벤트에 대해 좀 더 세부적인 정보를 제공하기 위해 추가 필드가 로깅됩니다. CSV에서 다음 속성이 각 행에 사용됩니다.

| 속성 이름 | 값/형식 |
| --- | --- |
| Date |이벤트가 발생한 날짜 및 시간 |
| Level |이벤트 수준(예: 오류, 경고, 정보) |
| ApplicationName |앱 이름 |
| InstanceId |이벤트가 발생한 앱의 인스턴스 |
| EventTickCount |이벤트가 발생한 날짜 및 시간(눈금 형식, 더 높은 정밀도) |
| EventId |이 이벤트의 이벤트 ID<p><p>지정하지 않을 경우 0으로 기본 설정됨 |
| Pid |프로세스 ID |
| Tid |이벤트가 생성된 스레드의 스레드 ID |
| Message |이벤트 세부 정보 메시지 |

Blob에 저장된 데이터는 다음 예제와 비슷합니다.

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> ASP.NET Core의 경우, [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 공급자를 사용하여 로깅이 이루어집니다. 이 공급자는 추가 로그 파일을 Blob 컨테이너에 저장합니다. 자세한 내용은 [Azure의 ASP.NET Core 로깅](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure)을 참조하세요.
>
>

### <a name="failed-request-traces"></a>실패한 요청 추적
실패한 요청 추적은 **fr######.xml**이라는 XML 파일에 저장됩니다. 로깅된 정보를 더 쉽게 볼 수 있도록 **freb.xsl**이라는 XSL 스타일시트가 XML 파일과 동일한 디렉터리에 제공됩니다. Internet Explorer에서 XML 파일 중 하나를 열면 Internet Explorer는 다음 예제와 비슷하게 XSL 스타일시트를 사용하여 서식이 지정된 추적 정보를 표시합니다.

![브라우저에 표시된 실패한 요청](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> 서식이 지정 된 실패 한 요청 추적을 보고 하는 간편한 방법은 포털에서 앱의 페이지로 이동 하는 경우 왼쪽된 메뉴에서 선택 **진단 및 문제 해결**를 검색 한 다음 **실패 한 요청 추적 로그**, 찾아보고 원하는 추적을 보려면 아이콘을 클릭 합니다.
>

### <a name="detailed-error-logs"></a>자세한 오류 로그
자세한 오류 로그는 발생한 HTTP 오류에 대해 좀 더 자세한 정보를 제공하는 HTML 문서입니다. 이들은 단순한 HTML 문서이므로 웹 브라우저를 사용하여 볼 수 있습니다.

### <a name="web-server-logs"></a>웹 서버 로그
웹 서버 로그는 [W3C 확장 로그 파일 형식](/windows/desktop/Http/w3c-logging)을 사용하여 서식이 지정됩니다. 이 정보는 텍스트 편집기를 사용하여 읽거나 [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619)와 같은 유틸리티를 사용하여 구문 분석할 수 있습니다.

> [!NOTE]
> Azure App Service에서 생성된 로그는 **s-computername**, **s-ip** 또는 **cs-version** 필드를 지원하지 않습니다.
>
>

## <a name="nextsteps"></a> 다음 단계
* [Azure App Service에서 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
