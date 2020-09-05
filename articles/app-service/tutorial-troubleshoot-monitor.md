---
title: '자습서: Azure Monitor 문제 해결'
description: Azure Monitor 및 Log Analytics를 통해 App Service 웹앱을 모니터링하는 방법을 알아봅니다. Azure Monitor는 환경을 모니터링하기 위한 종합적인 솔루션을 제공하여 가용성을 극대화합니다.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: c34cf47a5b8c20c10b160ac6e55309b3c18448f3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959020"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>자습서: Azure Monitor를 사용하여 App Service 앱 문제 해결

> [!NOTE]
> Azure Monitor와 App Service의 통합은 [미리 보기](https://aka.ms/appsvcblog-azmon)에 있습니다.
>

이 자습서에서는 [Azure Monitor](../azure-monitor/overview.md)를 사용하여 [App Service](overview.md) 앱의 문제를 해결하는 방법을 보여줍니다. 샘플 앱에는 메모리를 소모하고 HTTP 500 오류가 발생하는 코드가 포함되어 있으므로 Azure Monitor를 사용하여 문제를 진단하고 해결할 수 있습니다. 완료되면 [Azure Monitor](../azure-monitor/overview.md)와 통합된 Linux의 App Service에서 샘플 앱이 실행됩니다.

[Azure Monitor](../azure-monitor/overview.md)는 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동하기 위한 종합적인 솔루션을 제공하여 애플리케이션 및 서비스의 가용성과 성능을 극대화합니다.

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Monitor를 사용하여 웹앱 구성
> * Log Analytics에 콘솔 로그 보내기
> * 로그 쿼리를 사용하여 웹앱 오류 식별 및 해결

이 자습서의 단계는 macOS, Linux, Windows에서 수행할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure 리소스 만들기

먼저 여러 명령을 로컬로 실행하여 이 자습서에서 사용할 샘플 앱을 설정합니다. 이러한 명령은 샘플 앱을 복제하고, Azure 리소스를 만들고, 배포 사용자를 만들고, 앱을 Azure에 배포합니다. 배포 사용자를 만드는 과정의 일환으로 제공된 암호를 묻는 메시지가 표시됩니다. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Azure Monitor 구성(미리 보기)

### <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

이제 샘플 앱을 Azure App Service에 배포했으므로 문제가 발생할 때 앱 문제를 해결할 수 있도록 모니터링 기능을 구성합니다. Azure Monitor는 로그 데이터를 Log Analytics 작업 영역에 저장합니다. 작업 영역은 데이터 및 구성 정보를 포함하는 컨테이너입니다.

이 단계에서는 앱을 사용하여 Azure Monitor를 구성하기 위해 Log Analytics 작업 영역을 만듭니다.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Azure Monitor Log Analytics의 경우 데이터 수집 및 데이터 보존 비용을 지불해야 합니다.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>진단 설정 만들기

진단 설정을 사용하면 로그 쿼리를 사용하여 다른 모니터링 데이터를 분석하기 위해 특정 Azure 서비스에 대한 메트릭을 Azure Monitor 로그에 수집할 수 있습니다. 이 자습서에서는 웹 서버 및 표준 출력/오류 로그를 사용하도록 설정합니다. 로그 유형 및 설명의 전체 목록은 [지원되는 로그 유형](./troubleshoot-diagnostic-logs.md#supported-log-types)을 참조하세요.

다음 명령을 실행하여 AppServiceConsoleLogs(표준 출력/오류) 및 AppServiceHTTPLogs(웹 서버 로그)에 대한 진단 설정을 만듭니다. _\<app-name>_ 및 _\<workspace-name>_ 를 값으로 바꿉니다. 

> [!NOTE]
> 처음 두 명령인 `resourceID` 및 `workspaceID`는 `az monitor diagnostic-settings create` 명령에 사용되는 변수입니다. 이 명령에 대한 자세한 내용은 [Azure CLI를 사용하여 진단 설정 만들기](../azure-monitor/platform/diagnostic-settings.md#create-using-azure-cli)를 참조하세요.
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>앱 문제 해결

`http://<app-name>.azurewebsites.net`으로 이동합니다.

ImageConverter 샘플 앱은 포함된 이미지를 `JPG`에서 `PNG`로 변환합니다. 이 자습서의 코드에는 버그가 의도적으로 배치되었습니다. 이미지가 충분하게 선택되면 이미지 변환 중에 앱에서 HTTP 500 오류를 생성합니다. 이 시나리오는 개발 단계에서 고려되지 않았다고 가정합니다. Azure Monitor를 사용하여 이 오류를 해결합니다.

### <a name="verify-the-app-is-works"></a>앱이 작동하는지 확인

이미지를 변환하려면 `Tools`를 클릭하고 `Convert to PNG`를 선택합니다.

!['도구'를 클릭하고 'PNG로 변환'을 선택합니다.](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

처음 두 이미지를 선택하고 `convert`를 클릭합니다. 그러면 성공적으로 변환됩니다.

![처음 두 이미지 선택](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>앱 중단

이제 두 이미지를 성공적으로 변환하여 앱을 확인했으므로 처음 5개의 이미지를 변환하려고 합니다.

![처음 5개 이미지 변환](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

이 작업이 실패하고 개발 중에 테스트되지 않은 `HTTP 500` 오류가 생성됩니다.

![변환하면 HTTP 500 오류가 발생합니다.](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>로그 쿼리를 사용하여 Azure Monitor 로그 보기

Log Analytics 작업 영역에서 사용할 수 있는 로그를 확인해 보겠습니다. 

이 [Log Analytics 작업 영역 링크](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces)를 클릭하여 Azure Portal의 작업 영역에 액세스합니다.

Azure Portal에서 Log Analytics 작업 영역을 선택합니다.

### <a name="log-queries"></a>로그 쿼리

로그 쿼리를 사용하면 Azure Monitor 로그에서 수집된 데이터의 값을 완벽하게 활용할 수 있습니다. 로그 쿼리를 사용하여 AppServiceHTTPLogs 및 AppServiceConsoleLogs 모두에서 로그를 식별합니다. 로그 쿼리에 대한 자세한 내용은 [로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)를 참조하세요.

### <a name="view-appservicehttplogs-with-log-query"></a>로그 쿼리를 사용하여 AppServiceHTTPLogs 보기

이제 앱에 액세스했으므로 `AppServiceHTTPLogs`에 있는 HTTP 요청과 연결된 데이터를 살펴보겠습니다.

1. 왼쪽 탐색 영역에서 `Logs`를 클릭합니다.

![Logs Anlytics 작업 영역 로그](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. `appservice`를 검색하고 `AppServiceHTTPLogs`를 두 번 클릭합니다.

![로그 분석 작업 영역 테이블](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. `Run`을 클릭합니다.

![Log Analytics 작업 영역의 App Service HTTP 로그](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs` 쿼리는 지난 24시간 동안의 모든 요청을 반환합니다. `ScStatus` 열에는 HTTP 상태가 포함되어 있습니다. `HTTP 500` 오류를 진단하려면 아래와 같이 `ScStatus`를 500으로 제한하고 쿼리를 실행합니다.

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>로그 쿼리를 사용하여 AppServiceConsoleLogs 보기

이제 HTTP 500을 확인했으므로 앱의 표준 출력/오류를 살펴보겠습니다. 이러한 로그는 'AppServiceConsoleLogs'에 있습니다.

(1) `+`를 클릭하여 새 쿼리를 만듭니다. 

(2) `AppServiceConsoleLogs` 테이블을 두 번 클릭하고, `Run`을 클릭합니다. 

5개의 이미지를 변환하면 서버 오류가 발생하므로 아래와 같이 오류에 대해 `ResultDescription`을 필터링하여 앱에서 오류도 기록하는지 확인할 수 있습니다.

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

`ResultDescription` 열에 다음 오류가 표시됩니다.

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>AppServiceHTTPLogs 및 AppServiceConsoleLogs 조인

HTTP 500 및 표준 오류를 모두 식별했으므로 이러한 메시지 간에 상관 관계가 있는지 확인해야 합니다. 다음으로, `TimeGenerated` 타임스탬프를 기준으로 테이블을 조인합니다.

> [!NOTE]
> 다음 작업을 수행하는 쿼리가 준비되었습니다.
>
> - 500 오류에 대해 HTTPLogs 필터링
> - 콘솔 로그 쿼리
> - `TimeGenerated`를 기준으로 테이블 조인
>

다음 쿼리를 실행합니다.

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

`ResultDescription` 열에 웹 서버 오류와 동시에 다음 오류가 표시됩니다.

<pre>
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
</pre>

메시지 상태 메모리가 `process.php`의 20번 줄에서 모두 소모되었습니다. 이제 HTTP 500 오류 중에 애플리케이션에서 오류를 생성했음을 확인했습니다. 문제를 식별하는 코드를 살펴보겠습니다.

## <a name="identify-the-error"></a>오류 식별

로컬 디렉터리에서 `process.php`를 열고 20번 줄을 살펴봅니다. 

```php
imagepng($imgArray[$x], $filename);
```

첫 번째 `$imgArray[$x]` 인수는 변환이 필요한 모든 JPG를 포함하는 변수(메모리 내)입니다. 그러나 `imagepng`에서는 모든 이미지가 아니라 일부 이미지만 변환하면 됩니다. 이미지를 미리 로드할 필요가 없으며, 메모리 소모로 인해 HTTP 500이 발생할 수 있습니다. 필요에 따라 이미지를 로드하여 문제가 해결되는지 확인하도록 코드를 업데이트해 보겠습니다. 다음으로, 메모리 문제를 해결하는 코드를 향상시킵니다.

## <a name="fix-the-app"></a>앱 수정

### <a name="update-locally-and-redeploy-the-code"></a>로컬로 코드 업데이트 및 다시 배포

메모리 소모를 처리하기 위해 `process.php`를 다음과 같이 변경합니다.

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Git에서 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

`http://<app-name>.azurewebsites.net`으로 이동합니다. 

이미지를 변환하면 HTTP 500 오류가 더 이상 생성되지 않습니다.

![Azure App Service에서 실행 중인 PHP 앱](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

다음 명령을 사용하여 진단 설정을 삭제합니다.

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Monitor를 사용하여 웹앱 구성
> * Log Analytics에 로그 보내기
> * 로그 쿼리를 사용하여 웹앱 오류 식별 및 해결

## <a name="next-steps"></a><a name="nextsteps"></a> 다음 단계
* [Azure Monitor를 사용하여 로그 쿼리](../azure-monitor/log-query/log-query-overview.md)
* [Visual Studio에서 Azure App Service 문제 해결](troubleshoot-dotnet-visual-studio.md)
* [HDInsight에서 앱 로그 분석](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)