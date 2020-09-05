---
title: 진단 로깅 사용
description: 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법에 대해 알아봅니다.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 89162a0b8ca20e59319802f9e2359c2f27ff163f
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962182"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service에서 앱에 대한 진단 로깅 사용
## <a name="overview"></a>개요
Azure는 [App Service 앱](overview.md)을 디버그하는 데 도움이 되는 기본 제공 진단을 제공합니다. 이 문서에서는 진단 로그를 사용하도록 설정하는 방법, 애플리케이션에 계측을 추가하는 방법 및 Azure에서 기록된 정보에 액세스하는 방법을 설명합니다.

이 문서에서는 진단 로그 작업에 [Azure Portal](https://portal.azure.com)과 Azure CLI를 사용합니다. Visual Studio를 사용하여 진단 로그로 작업하는 방법에 대한 자세한 내용은 [Visual Studio에서 Azure 문제 해결](troubleshoot-dotnet-visual-studio.md)을 참조하세요.

> [!NOTE]
> 이 문서의 로깅 지침 외에도 Azure 모니터링을 사용 하는 새로운 통합 로깅 기능이 있습니다. 이 기능에 대 한 자세한 내용은 [Azure Monitor에 대 한 로그 보내기 (미리 보기)](#send-logs-to-azure-monitor-preview) 섹션에서 확인할 수 있습니다. 
>
>

|유형|플랫폼|위치|Description|
|-|-|-|-|
| 애플리케이션 로깅 | Windows, Linux | App Service 파일 시스템 및/또는 Azure Storage blob | 응용 프로그램 코드에 의해 생성 된 메시지를 기록 합니다. 사용자가 선택한 웹 프레임 워크 또는 사용자 언어의 표준 로깅 패턴을 사용 하 여 직접 응용 프로그램 코드에서 메시지를 생성할 수 있습니다. 각 메시지에는 **중요**, **오류**, **경고**, **정보**, **디버그**및 **추적**범주 중 하나가 할당 됩니다. 응용 프로그램 로깅을 사용 하도록 설정할 때 심각도 수준을 설정 하 여 로깅을 원하는 세부 정보를 선택할 수 있습니다.|
| 웹 서버 로깅| Windows | App Service 파일 시스템 또는 Azure Storage blob| [W3C 확장 로그 파일 형식의](/windows/desktop/Http/w3c-logging)원시 HTTP 요청 데이터입니다. 각 로그 메시지에는 HTTP 메서드, 리소스 URI, 클라이언트 IP, 클라이언트 포트, 사용자 에이전트, 응답 코드 등의 데이터가 포함 됩니다. |
| 자세한 오류 메시지| Windows | App Service 파일 시스템 | 클라이언트 브라우저로 전송 된 *.htm* 오류 페이지의 복사본입니다. 보안상의 이유로, 자세한 오류 페이지는 프로덕션 환경에서 클라이언트로 전송 되어서는 안 되지만 응용 프로그램 오류가 발생할 때마다 HTTP 코드 400 이상이 포함 된 오류 페이지를 저장할 수 App Service. 이 페이지에는 서버에서 오류 코드를 반환 하는 이유를 확인 하는 데 도움이 되는 정보가 포함 될 수 있습니다. |
| 실패한 요청 추적 | Windows | App Service 파일 시스템 | 요청을 처리 하는 데 사용 되는 IIS 구성 요소 추적 및 각 구성 요소에서 소요 된 시간을 비롯 하 여 실패 한 요청에 대 한 자세한 추적 정보입니다. 사이트 성능을 향상시키거나 특정 HTTP 오류를 분리하려는 경우에 유용합니다. 실패 한 각 요청에 대해 XML 로그 파일이 포함 된 폴더와 로그 파일을 볼 XSL 스타일 시트가 하나씩 생성 됩니다. |
| 배포 로깅 | Windows, Linux | App Service 파일 시스템 | 앱에 콘텐츠를 게시 하는 경우에 대 한 로그입니다. 배포 로깅은 자동으로 수행 되며 배포 로깅에 대 한 구성 가능한 설정이 없습니다. 배포가 실패 한 이유를 확인 하는 데 도움이 됩니다. 예를 들어 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 사용 하는 경우 배포 로깅을 사용 하 여 스크립트가 실패 하는 이유를 확인할 수 있습니다. |

> [!NOTE]
> App Service는 응용 프로그램 문제를 해결 하는 데 도움이 되는 전용 대화형 진단 도구를 제공 합니다. 자세한 내용은 [Azure App Service 진단 개요](overview-diagnostics.md)를 참조하세요.
>
> 또한 다른 Azure 서비스를 사용 하 여 [Azure Monitor](../azure-monitor/app/azure-web-apps.md)와 같은 앱의 로깅 및 모니터링 기능을 향상 시킬 수 있습니다.
>

## <a name="enable-application-logging-windows"></a>응용 프로그램 로깅 사용 (Windows)

> [!NOTE]
> Blob 저장소에 대 한 응용 프로그램 로깅은 App Service와 동일한 지역에 있는 저장소 계정만 사용할 수 있습니다.

[Azure Portal](https://portal.azure.com)에서 Windows 앱에 대 한 응용 프로그램 로깅을 사용 하도록 설정 하려면 앱으로 이동 하 여 **App Service 로그**를 선택 합니다.

**응용 프로그램 로깅 (파일 시스템)** 또는 **응용 프로그램 로깅 (Blob)** 중 하나 또는 둘 모두에 대해 **켜기** 를 선택 합니다. 

**Filesystem** 옵션은 임시 디버깅 용도로 사용 되며 12 시간 이내에 해제 됩니다. **Blob** 옵션은 장기 로깅을 위한 것 이며, 로그를 쓸 blob 저장소 컨테이너가 필요 합니다.  또한 **Blob** 옵션에는 로그 메시지의 원본 VM 인스턴스 ID ( `InstanceId` ), 스레드 id () `Tid` 및 보다 세분화 된 타임 스탬프 ()와 같은 추가 정보가 로그 메시지에 포함 됩니다 [`EventTickCount`](/dotnet/api/system.datetime.ticks) .

> [!NOTE]
> 현재 .NET 애플리케이션 로그만 Blob Storage에 쓸 수 있습니다. Java, PHP, Node.js, Python 응용 프로그램 로그는 App Service 파일 시스템에만 저장할 수 있습니다. 단, 외부 저장소에 로그를 기록 하기 위해 코드를 수정 하지 않아도 됩니다.
>
> 또한 [저장소 계정의 액세스 키를 다시 생성](../storage/common/storage-account-create.md)하는 경우 업데이트 된 액세스 키를 사용 하도록 각 로깅 구성을 다시 설정 해야 합니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기**로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

로깅할 **수준**또는 세부 정보 수준을 선택 합니다. 다음 표에는 각 수준에 포함 된 로그 범주가 나와 있습니다.

| Level | 포함 된 범주 |
|-|-|
|**사용 안 함** | 없음 |
|**오류** | 오류, 위험 |
|**경고** | 경고, 오류, 위험|
|**정보** | 정보, 경고, 오류, 위험|
|**Verbose** | 추적, 디버그, 정보, 경고, 오류, 위험(모든 범주) |

작업을 마쳤으면 **저장**을 선택합니다.

## <a name="enable-application-logging-linuxcontainer"></a>응용 프로그램 로깅 사용 (Linux/컨테이너)

[Azure Portal](https://portal.azure.com)에서 Linux 앱 또는 사용자 지정 컨테이너 앱에 대 한 응용 프로그램 로깅을 사용 하도록 설정 하려면 앱으로 이동 하 여 **App Service 로그**를 선택 합니다.

**응용 프로그램 로깅**에서 **파일 시스템**을 선택 합니다.

**할당량 (MB)** 에서 응용 프로그램 로그에 대 한 디스크 할당량을 지정 합니다. **보존 기간 (일)** 에서 로그를 보존할 일 수를 설정 합니다.

작업을 마쳤으면 **저장**을 선택합니다.

## <a name="enable-web-server-logging"></a>웹 서버 로깅 사용

[Azure Portal](https://portal.azure.com)에서 Windows 앱에 대 한 웹 서버 로깅을 사용 하도록 설정 하려면 앱으로 이동 하 여 **App Service logs**를 선택 합니다.

**웹 서버 로깅**의 경우 blob storage에 로그를 저장 하려면 **저장소** 를 선택 하 고, App Service 파일 시스템에 로그를 저장 하려면 **파일 시스템** 을 선택 합니다. 

**보존 기간 (일)** 에서 로그를 보존할 일 수를 설정 합니다.

> [!NOTE]
> [스토리지 계정의 선택키를 다시 생성](../storage/common/storage-account-create.md)하는 경우 해당 로깅 구성을 다시 설정하여 업데이트한 키를 사용해야 합니다. 가상 하드 디스크 파일에 대한 중요 정보를 제공하려면
>
> 1. **구성** 탭에서 해당 로깅 기능을 **끄기**로 설정합니다. 설정을 저장합니다.
> 2. 스토리지 계정 Blob에 로깅을 다시 사용합니다. 설정을 저장합니다.
>
>

작업을 마쳤으면 **저장**을 선택합니다.

## <a name="log-detailed-errors"></a>자세한 오류 기록

[Azure Portal](https://portal.azure.com)에서 Windows 앱에 대 한 오류 페이지 또는 실패 한 요청 추적을 저장 하려면 앱으로 이동 하 여 **App Service 로그**를 선택 합니다.

**자세한 오류 로깅** 또는 **실패 한 요청 추적**에서 **켜기**를 선택한 다음, **저장**을 선택 합니다.

두 로그 유형 모두 App Service 파일 시스템에 저장 됩니다. 최대 50 오류 (파일/폴더)가 보존 됩니다. HTML 파일 수가 50을 초과 하면 가장 오래 된 26 개 오류가 자동으로 삭제 됩니다.

## <a name="add-log-messages-in-code"></a>코드에 로그 메시지 추가

응용 프로그램 코드에서 일반적인 로깅 기능을 사용 하 여 응용 프로그램 로그에 로그 메시지를 보냅니다. 예를 들어:

- ASP.NET 애플리케이션은 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 클래스를 사용하여 애플리케이션 진단 로그에 정보를 로깅할 수 있습니다. 예를 들어:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 기본적으로 ASP.NET Core는 [Microsoft. 확장명](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 을 사용 하 여 로깅 공급자를 사용 합니다. 자세한 내용은 [Azure의 ASP.NET Core 로깅](/aspnet/core/fundamentals/logging/)을 참조하세요.

## <a name="stream-logs"></a>로그 스트리밍

로그를 실시간으로 스트리밍하기 전에 원하는 로그 유형을 사용 하도록 설정 합니다. / *Logfiles* 디렉터리 (d:/home/logfiles)에 저장 된 .txt, .log 또는 .htm으로 끝나는 파일에 기록 된 모든 정보는 App Service에 의해 스트리밍됩니다.

> [!NOTE]
> 일부 유형의 로깅 버퍼는 로그 파일에 기록하고 이로 인해 스크림에서 이벤트가 작동하지 않을 수 있습니다. 예를 들어 사용자가 페이지를 방문할 때 발생한 애플리케이션 로그 항목이 페이지 요청에 대한 해당 HTTP 로그 항목보다 먼저 스트림에 표시될 수 있습니다.
>

### <a name="in-azure-portal"></a>Azure Portal에서

[Azure Portal](https://portal.azure.com)에서 로그를 스트리밍하려면 앱으로 이동 하 여 **로그 스트림**을 선택 합니다. 

### <a name="in-cloud-shell"></a>Cloud Shell에서

로그를 [Cloud Shell](../cloud-shell/overview.md)에서 라이브로 스트리밍하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

오류와 같은 특정 이벤트를 필터링하려면 **--Filter** 매개 변수를 사용합니다. 예를 들어:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
HTTP와 같은 특정 로그 유형을 필터링하려면 **-Path** 매개 변수를 사용합니다. 예를 들어:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>로컬 터미널

로컬 콘솔에서 로그를 스트리밍하려면 Azure CLI를 [설치](/cli/azure/install-azure-cli) 하 고 [계정에 로그인](/cli/azure/authenticate-azure-cli)합니다. 로그인 한 후 [에는 Cloud Shell에 대 한 지침](#in-cloud-shell) 을 따릅니다.

## <a name="access-log-files"></a>로그 파일 액세스

로그 유형에 대 한 Azure Storage blob 옵션을 구성 하는 경우 Azure Storage에서 작동 하는 클라이언트 도구가 필요 합니다. 자세한 내용은 [Azure Storage 클라이언트 도구](../storage/common/storage-explorers.md)를 참조 하세요.

App Service 파일 시스템에 저장 된 로그의 경우 브라우저에서 ZIP 파일을 다운로드 하는 가장 쉬운 방법은 다음과 같습니다.

- Linux/컨테이너 앱: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows 앱: `https://<app-name>.scm.azurewebsites.net/api/dump`

Linux/컨테이너 앱의 경우 ZIP 파일에는 docker 호스트와 docker 컨테이너 모두에 대 한 콘솔 출력 로그가 포함 되어 있습니다. 스케일 아웃 된 앱의 경우 ZIP 파일에는 각 인스턴스에 대 한 로그 집합이 하나 포함 됩니다. App Service 파일 시스템에서 이러한 로그 파일은 */home/LogFiles* 디렉터리의 내용입니다.

Windows 앱의 경우 ZIP 파일에는 App Service 파일 시스템의 *D:\Home\LogFiles* 디렉터리 내용이 포함 되어 있습니다. 구조는 다음과 같습니다.

| 로그 형식 | 디렉터리 | 설명 |
|-|-|-|
| **애플리케이션 로그 전송 사용** |*/LogFiles/Application/* | 하나 이상의 텍스트 파일을 포함 합니다. 로그 메시지의 형식은 사용 하는 로깅 공급자에 따라 달라 집니다. |
| **실패 한 요청 추적** | */LogFiles/W3SVC # # # # # # # # #/* | XML 파일 및 XSL 파일을 포함 합니다. 브라우저에서 형식이 지정 된 XML 파일을 볼 수 있습니다. |
| **자세한 오류 로그** | */LogFiles/DetailedErrors/* | 에는 HTM 오류 파일이 포함 되어 있습니다. 브라우저에서 HTM 파일을 볼 수 있습니다.<br/>실패 한 요청 추적을 보는 또 다른 방법은 포털에서 앱 페이지로 이동 하는 것입니다. 왼쪽 메뉴에서 **문제 진단 및 해결**을 선택 하 고 **실패 한 요청 추적 로그**를 검색 한 다음 아이콘을 클릭 하 여 원하는 추적을 찾아 봅니다. |
| **웹 서버 로그** | */LogFiles/http/RawLogs/* | [W3C 확장 로그 파일 형식을](/windows/desktop/Http/w3c-logging)사용 하 여 서식이 지정 된 텍스트 파일을 포함 합니다. 이 정보는 텍스트 편집기나 [로그 파서와](https://go.microsoft.com/fwlink/?LinkId=246619)같은 유틸리티를 사용 하 여 읽을 수 있습니다.<br/>App Service는 `s-computername` , 또는 필드를 지원 하지 않습니다 `s-ip` `cs-version` . |
| **배포 로그** | */LogFiles/Git/* 및 */deployments/* | Git 배포에 대 한 로그 뿐만 아니라 내부 배포 프로세스에서 생성 된 로그를 포함 합니다. |

## <a name="send-logs-to-azure-monitor-preview"></a>Azure Monitor로 로그 보내기 (미리 보기)

새 [Azure Monitor 통합](https://aka.ms/appsvcblog-azmon)을 사용 하 여 [진단 설정 (미리 보기)을 만들어](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) 저장소 계정, Event Hubs 및 Log Analytics에 로그를 보낼 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![진단 설정 (미리 보기)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>지원 되는 로그 유형

다음 표에서는 지원 되는 로그 유형 및 설명을 보여 줍니다. 

| 로그 형식 | Windows 지원 | Linux (Docker) 지원 | 설명 |
|-|-|-|
| AppServiceConsoleLogs | TBA | 예 | 표준 출력 및 표준 오류 |
| AppServiceHTTPLogs | 예 | 예 | 웹 서버 로그 |
| Appservice환경 Platformlogs | 예 | 예 | App Service Environment: 크기 조정, 구성 변경 및 상태 로그|
| AppServiceAuditLogs | 예 | 예 | FTP 및 Kudu를 통한 로그인 활동 |
| AppServiceFileAuditLogs | 예 | TBD | FTP 및 Kudu를 통해 파일 변경 |
| AppServiceAppLogs | TBA | Java SE & Tomcat | 애플리케이션 로그 전송 사용 |
| AppServiceIPSecAuditLogs  | 예 | 예 | IP 규칙의 요청 |
| AppServicePlatformLogs  | TBA | 예 | 컨테이너 로그 |

## <a name="next-steps"></a><a name="nextsteps"></a> 다음 단계
* [Azure Monitor를 사용하여 로그 쿼리](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service에서 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)