---
title: Azure App Services 성능 모니터링 | Microsoft Docs
description: Azure App Services에 대한 애플리케이션 성능 모니터링입니다. 차트 로드 및 응답 시간, 종속성 정보 및 성능에 대 한 경고를 설정 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/11/2019
ms.openlocfilehash: 3ca9cbf2e282e3f67af3c5da470a3d81e6055f98
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189581"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service 성능 모니터링

이제 [Azure 앱 서비스](https://docs.microsoft.com/azure/app-service/) 에서 실행 되는 ASP.NET 및 ASP.NET Core 기반 웹 응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하는 것이 훨씬 쉬워졌습니다. 이전에 사이트 확장을 수동으로 설치 해야 하는 경우에는 이제 기본적으로 최신 확장/에이전트가 app service 이미지에 기본 제공 됩니다. 이 문서에서는 Application Insights 모니터링을 사용 하도록 설정 하는 과정을 안내 하 고 대규모 배포 프로세스를 자동화 하기 위한 예비 지침을 제공 합니다.

> [!NOTE]
> **개발 도구** > **확장** 을 통해 Application Insights 사이트 확장을 수동으로 추가 하는 것은 더 이상 사용 되지 않습니다. 이 확장 설치 방법은 각 새 버전의 수동 업데이트에 따라 달라 집니다. 확장의 안정적인 최신 릴리스는 이제 App Service 이미지의 일부로 [미리 설치](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) 됩니다. 파일은 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`에 있으며 각 안정적인 릴리스로 자동으로 업데이트 됩니다. 에이전트 기반 지침에 따라 아래 모니터링을 사용 하는 경우 사용 되지 않는 확장이 자동으로 제거 됩니다.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure 앱 Services 호스팅된 응용 프로그램에 대해 응용 프로그램 모니터링을 사용 하도록 설정 하는 방법에는 두 가지가 있습니다.

* **에이전트 기반 응용 프로그램 모니터링** (ApplicationInsightsAgent).  
    * 이 방법은 사용 하기 가장 쉽고 고급 구성이 필요 하지 않습니다. 이를 종종 "런타임" 모니터링 이라고 합니다. Azure 앱 서비스의 경우이 모니터링 수준을 최소한으로 설정 하는 것이 좋지만, 특정 시나리오에 따라 수동 계측을 통한 고급 모니터링이 필요한 지 여부를 평가할 수 있습니다.

* Application Insights SDK를 설치 하 여 **코드를 통해 수동으로 응용 프로그램을 계측** 합니다.

    * 이 방법은 훨씬 더 사용자 지정이 가능 하지만 [APPLICATION INSIGHTS SDK NuGet 패키지에 대 한 종속성을 추가](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)해야 합니다. 또한이 메서드는 최신 버전의 패키지에 대 한 업데이트를 직접 관리 해야 함을 의미 합니다.

    * 에이전트 기반 모니터링을 사용 하 여 기본적으로 캡처되지 않는 이벤트/종속성을 추적 하기 위해 사용자 지정 API 호출을 수행 해야 하는 경우이 방법을 사용 해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭 용 API 문서](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 를 확인 하세요.

> [!NOTE]
> 에이전트 기반 모니터링과 수동 SDK 기반 계측이 모두 검색 된 경우에는 수동 계측 설정만 허용 됩니다. 이는 중복 데이터가 전송 되지 않도록 방지 하기 위한 것입니다. 이에 대 한 자세한 내용을 보려면 아래의 [문제 해결 섹션](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) 을 확인 하세요.

## <a name="enable-agent-based-monitoring"></a>에이전트 기반 모니터링 사용

# <a name="nettabnet"></a>[.NET](#tab/net)

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED와 urlCompression의 조합은 지원 되지 않습니다. 자세한 내용은 [문제 해결 섹션](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)의 설명을 참조 하세요.


1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource-01.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. ASP.NET 앱 모니터링은 기본적으로 두 가지 수준의 수집을 사용 하 여 설정 됩니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new.png)

   * .NET **기본 수집** 수준은 필수적인 단일 인스턴스 APM 기능을 제공합니다.

   * .NET **추천 수집** 수준은 다음과 같습니다.
       * CPU, 메모리 및 I/O 사용량 추세를 추가합니다.
       * 요청/종속성 경계 간에 마이크로 서비스를 상호 연결합니다.
       * 사용량 추세를 수집하고, 가용성 결과와 트랜잭션의 상관 관계를 사용하도록 설정합니다.
       * 호스트 프로세스에서 처리되지 않은 예외를 수집합니다.
       * 샘플링을 사용하는 경우 부하 상태에서 APM 메트릭 정확도가 향상됩니다.

3. 이전에 applicationinsights .config 파일을 통해 제어할 수 있는 샘플과 같은 설정을 구성 하려면 이제 해당 접두사를 사용 하 여 응용 프로그램 설정을 통해 동일한 설정과 상호 작용할 수 있습니다. 

    * 예를 들어 초기 샘플링 비율을 변경 하려면 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`의 응용 프로그램 설정 및 `100`값을 만들 수 있습니다.

    * 지원 되는 적응 샘플링 원격 분석 프로세서 설정 목록에 대해서는 [코드](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) 및 [관련 설명서](https://docs.microsoft.com/azure/azure-monitor/app/sampling)를 참조할 수 있습니다.

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

다음 버전의 .NET Core가 지원 됩니다. ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

.NET Core, 자체 포함 배포 및 Linux 기반 응용 프로그램에서 전체 프레임 워크를 대상으로 지정 하는 것은 현재 에이전트/확장 기반 모니터링에서 **지원 되지 않습니다** . 코드를 통한[수동 계측](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 은 모든 이전 시나리오에서 작동 합니다.

1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights-01.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource-01.png)

2. 사용할 리소스를 지정한 후에는 응용 프로그램에 대 한 플랫폼별 데이터 수집 Application Insights 방법을 선택할 수 있습니다. .Net Core는 .NET Core 2.0, 2.1, 2.2 및 3.0에 대해 **권장 되는 컬렉션** 을 제공 하거나 **사용 하지 않도록 설정** 합니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

App Service 웹 앱 내에서 **설정** > Application Insights > **사용**을 **선택** 합니다. Node.js 에이전트 기반 모니터링은 현재 미리 보기 상태입니다.

# <a name="javatabjava"></a>[Java](#tab/java)

Java App Service 기반 웹 응용 프로그램은 현재 자동 에이전트/확장 기반 모니터링을 지원 하지 않습니다. Java 응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하려면 [수동으로 응용 프로그램을 계측](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)해야 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python App Service 기반 웹 응용 프로그램은 현재 자동 에이전트/확장 기반 모니터링을 지원 하지 않습니다. Python 응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하려면 [수동으로 응용 프로그램을 계측](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)해야 합니다.

---

## <a name="enable-client-side-monitoring"></a>클라이언트 쪽 모니터링 사용

# <a name="nettabnet"></a>[.NET](#tab/net)

클라이언트 쪽 모니터링이 ASP.NET에 대해 옵트인 (opt in) 됩니다. 클라이언트 쪽 모니터링을 사용 하도록 설정 하려면:

* **설정** > * * * * 응용 프로그램 설정 * * * *을 선택 합니다.
   * 응용 프로그램 설정에서 새 **앱 설정 이름** 및 **값**을 추가 합니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `true`

   * 설정을 **저장**하고 앱을 **다시 시작**합니다.

![응용 프로그램 설정 UI의 스크린샷](./media/azure-web-apps/appinsights-javascript-enabled.png)

클라이언트 쪽 모니터링을 사용 하지 않도록 설정 하려면 응용 프로그램 설정에서 연결 된 키 값 쌍을 제거 하거나 값을 false로 설정 합니다.

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

클라이언트 쪽 모니터링은 앱 설정 ' APPINSIGHTS_JAVASCRIPT_ENABLED '이 있는지 여부에 관계 없이 **권장 컬렉션이**있는 .net Core 앱에 대해 **기본적으로 사용 하도록 설정** 됩니다.

어떤 이유로 든 클라이언트 쪽 모니터링을 사용 하지 않도록 설정 하려는 경우:

* **설정** > **응용 프로그램 설정** 선택
   * 응용 프로그램 설정에서 새 **앱 설정 이름** 및 **값**을 추가 합니다.

     이름: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     값: `false`

   * 설정을 **저장**하고 앱을 **다시 시작**합니다.

![응용 프로그램 설정 UI의 스크린샷](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejstabnodejs"></a>[Node.JS](#tab/nodejs)

Node.js 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 사용 하도록 설정 하려면 [클라이언트 쪽 JAVASCRIPT SDK를 응용 프로그램에 수동으로 추가](https://docs.microsoft.com/azure/azure-monitor/app/javascript)해야 합니다.

# <a name="javatabjava"></a>[Java](#tab/java)

Java 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 사용 하도록 설정 하려면 [클라이언트 쪽 JAVASCRIPT SDK를 응용 프로그램에 수동으로 추가](https://docs.microsoft.com/azure/azure-monitor/app/javascript)해야 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 응용 프로그램에 대 한 클라이언트 쪽 모니터링을 사용 하도록 설정 하려면 [클라이언트 쪽 JAVASCRIPT SDK를 응용 프로그램에 수동으로 추가](https://docs.microsoft.com/azure/azure-monitor/app/javascript)해야 합니다.

---

## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights에서 원격 분석 컬렉션을 사용 하도록 설정 하려면 응용 프로그램 설정만 설정 해야 합니다.

   ![사용 가능한 Application Insights 설정으로 응용 프로그램 설정 App Service](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>응용 프로그램 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어 하는 기본 확장입니다. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드 에서만 최적의 성능을 보장 하기 위해 필수 기능을 사용할 수 있습니다. | `default` 또는 `recommended`입니다. |
|InstrumentationEngine_EXTENSION_VERSION | 이진 재작성 엔진 `InstrumentationEngine`를 켤 지 여부를 제어 합니다. 이 설정은 성능에 영향을 주며 콜드 시작/시작 시간에 영향을 줍니다. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL & Azure 테이블 텍스트가 종속성 호출과 함께 캡처될 수 있는지 여부를 제어 합니다. 성능 경고: 응용 프로그램 콜드 시작 시간이 영향을 받습니다. 이 설정에는 `InstrumentationEngine`필요 합니다. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 응용 프로그램 설정 App Service

[Azure Resource Manager 템플릿을](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)사용 하 여 App Services에 대 한 응용 프로그램 설정을 관리 하 고 구성할 수 있습니다. 이 메서드는 Azure Resource Manager 자동화를 사용 하 여 새 App Service 리소스를 배포 하거나 기존 리소스의 설정을 수정 하는 경우에 사용할 수 있습니다.

App service에 대 한 응용 프로그램 설정 JSON의 기본 구조는 다음과 같습니다.

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Application Insights에 대해 구성 된 응용 프로그램 설정이 있는 Azure Resource Manager 템플릿의 예를 보려면이 [템플릿이](https://github.com/Andrew-MSFT/BasicImageGallery) 도움이 될 수 있습니다. 특히 [238 줄](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)에서 시작 하는 섹션입니다.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights 리소스의 생성을 자동화 하 고 새로 만든 App Service에 연결 합니다.

구성 된 모든 기본 Application Insights 설정을 사용 하 여 Azure Resource Manager 템플릿을 만들려면 Application Insights 사용 하도록 설정 된 새 웹 앱을 만드는 것 처럼 프로세스를 시작 합니다.

**자동화 옵션** 선택

   ![App Service 웹 앱 만들기 메뉴](./media/azure-web-apps/create-web-app.png)

이 옵션은 모든 필수 설정이 구성 된 최신 Azure Resource Manager 템플릿을 생성 합니다.

  ![웹 앱 템플릿 App Service](./media/azure-web-apps/arm-template.png)

다음은 `AppMonitoredSite`의 모든 인스턴스를 사이트 이름으로 대체 하는 샘플입니다.

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>PowerShell을 통한 사용 설정

PowerShell을 통해 응용 프로그램 모니터링을 사용 하도록 설정 하려면 기본 응용 프로그램 설정만 변경 해야 합니다. 다음은 리소스 그룹 "Appmonitoredsite"에서 "AppMonitoredSite" 라는 웹 사이트에 대해 응용 프로그램 모니터링을 사용 하도록 설정 하 고 "012345678-ef01-2345-6789abcd" 계측 키로 전송할 데이터를 구성 하는 샘플입니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>업그레이드 모니터링 확장/에이전트

### <a name="upgrading-from-versions-289-and-up"></a>2\.8.9 이상 버전에서 업그레이드

2\.8.9 버전에서의 업그레이드는 추가 작업 없이 자동으로 발생 합니다. 새 모니터링 비트는 백그라운드에서 대상 app service로 전달 되 고, 응용 프로그램을 다시 시작 하면 선택 됩니다.

실행 중인 확장의 버전을 확인 하려면 다음을 방문 `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Url 경로 http://yoursitename.scm.azurewebsites.net/ApplicationInsights의 스크린샷](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>버전 1.0.0에서 업그레이드-2.6.5

버전 2.8.9부터 사전 설치 된 사이트 확장이 사용 됩니다. 이전 버전인 경우 다음 두 가지 방법 중 하나를 통해 업데이트할 수 있습니다.

* [포털을 통해를 사용 하 여 업그레이드](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)합니다. (Azure App Service에 대 한 Application Insights 확장이 설치 된 경우에도 UI는 **사용** 단추만 표시 합니다. 백그라운드에서 이전 개인 사이트 확장이 제거 됩니다.

* [PowerShell을 통해 업그레이드](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. 미리 설치 된 사이트 확장 ApplicationInsightsAgent를 사용 하도록 응용 프로그램 설정을 설정 합니다. [Powershell을 통한 사용](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)을 참조 하세요.
    2. Azure App Service에 대 한 Application Insights 확장 이라는 개인 사이트 확장을 수동으로 제거 합니다.

2\.5.1 이전 버전에서 업그레이드를 수행 하는 경우 응용 프로그램 bin 폴더에서 ApplicationInsigths dll이 제거 되었는지 확인 하 고 [문제 해결 단계를 참조](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)하세요.

## <a name="troubleshooting"></a>문제 해결

다음은 Azure 앱 서비스에서 실행 되는 .NET 및 .NET Core 기반 응용 프로그램에 대 한 확장/에이전트 기반 모니터링에 대 한 단계별 문제 해결 가이드입니다.

> [!NOTE]
> Java 응용 프로그램은 수동 SDK 기반 계측을 통해 Azure 앱 서비스 에서만 지원 되므로 아래 단계는 이러한 시나리오에 적용 되지 않습니다.

1. `ApplicationInsightsAgent`를 통해 응용 프로그램을 모니터링 하는지 확인 합니다.
    * `ApplicationInsightsAgent_EXTENSION_VERSION` 앱 설정이 "~ 2" 값으로 설정 되어 있는지 확인 합니다.
2. 응용 프로그램이 모니터링 해야 하는 요구 사항을 충족 하는지 확인 합니다.
    * `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`로 이동

    ![https://yoursitename.scm.azurewebsites/applicationinsights 결과 페이지의 스크린샷](./media/azure-web-apps/app-insights-sdk-status.png)

    * `Application Insights Extension Status` 있는지 확인 `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 실행 되 고 있지 않으면 [Application Insights 모니터링 사용 지침](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) 을 따릅니다.

    * 상태 원본이 존재 하 고 다음과 같이 표시 되는지 확인 합니다. `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 유사한 값이 없는 경우에는 응용 프로그램이 현재 실행 되 고 있지 않거나 지원 되지 않음을 의미 합니다. 응용 프로그램이 실행 되 고 있는지 확인 하려면 런타임 정보를 사용할 수 있게 하는 응용 프로그램 url/응용 프로그램 끝점을 수동으로 방문해 보세요.

    * `IKeyExists` 있는지 확인 `true`
        * `false`경우 ikey guid를 사용 하 여 `APPINSIGHTS_INSTRUMENTATIONKEY` 및 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 응용 프로그램 설정에 추가 합니다.

    * `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`및 `AppContainsAspNetTelemetryCorrelationAssembly`에 대 한 항목이 없는지 확인 합니다.
        * 이러한 항목이 있는 경우 응용 프로그램에서 `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`및 `Microsoft.AspNet.TelemetryCorrelation`패키지를 제거 합니다.

아래 표에서는 이러한 값의 의미, 기본적인 원인 및 권장 픽스를 보다 자세히 설명 합니다.

|문제 값|설명|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | 이 값은 확장에서 SDK의 일부 측면이 응용 프로그램에 이미 있고 백오프 됨을 감지 했음을 나타냅니다. `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`또는 `Microsoft.ApplicationInsights`에 대 한 참조가 원인일 수 있습니다.  | 참조를 제거 합니다. 이러한 참조 중 일부는 특정 Visual Studio 템플릿에서 기본적으로 추가 되며, 이전 버전의 Visual Studio에서는 `Microsoft.ApplicationInsights`에 대 한 참조를 추가할 수 있습니다.
|`AppAlreadyInstrumented:true` | 응용 프로그램이 .NET Core 2.1 또는 2.2를 대상으로 하 고 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 를 참조 하는 경우에는 Application Insights를 가져오고 확장이 백오프 됩니다. | .NET Core 2.1, 2.2의 고객은 AspNetCore를 대신 사용 하는 [것이 좋습니다](https://github.com/aspnet/Announcements/issues/287) .|
|`AppAlreadyInstrumented:true` | 이 값은 이전 배포에서 앱 폴더에 위의 dll이 있는 경우에도 발생할 수 있습니다. | 이러한 dll이 제거 되도록 앱 폴더를 정리 합니다. 로컬 앱의 bin 디렉터리와 App Service의 wwwroot 디렉터리를 모두 확인 합니다. App Service 웹 앱의 wwwroot 디렉터리를 확인 하려면: 고급 도구 (Kudu) > 디버그 콘솔 > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 이 값은 확장에서 응용 프로그램의 `Microsoft.AspNet.TelemetryCorrelation`에 대 한 참조를 검색 하 여 백오프 함을 나타냅니다. | 참조를 제거합니다.
|`AppContainsDiagnosticSourceAssembly**:true`|이 값은 확장에서 응용 프로그램의 `System.Diagnostics.DiagnosticSource`에 대 한 참조를 검색 하 여 백오프 함을 나타냅니다.| 참조를 제거합니다.
|`IKeyExists:false`|이 값은 AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`에 계측 키가 없음을 나타냅니다. 가능한 원인: 값이 실수로 제거 되었거나 자동화 스크립트에서 값을 설정 하는 것을 잊은 경우 | 설정이 App Service 응용 프로그램 설정에 표시 되는지 확인 합니다.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED 및 urlCompression은 지원 되지 않습니다.

콘텐츠가 인코딩된 경우 APPINSIGHTS_JAVASCRIPT_ENABLED = true를 사용 하는 경우 다음과 같은 오류가 발생할 수 있습니다. 

- 500 URL 재작성 오류
- 500.53 HTTP 응답의 콘텐츠를 인코딩할 때 메시지 아웃 바운드 재작성 규칙을 사용 하 여 URL 재작성 모듈 오류를 적용할 수 없습니다 (' gzip '). 

이는 APPINSIGHTS_JAVASCRIPT_ENABLED 응용 프로그램 설정이 true로 설정 되 고 콘텐츠 인코딩이 동시에 표시 되기 때문입니다. 이 시나리오는 아직 지원 되지 않습니다. 해결 방법은 응용 프로그램 설정에서 APPINSIGHTS_JAVASCRIPT_ENABLED를 제거 하는 것입니다. 그러나이는 클라이언트/브라우저 쪽 JavaScript 계측이 여전히 필요한 경우 웹 페이지에 대 한 수동 SDK 참조가 필요 함을 의미 합니다. JavaScript SDK를 사용한 수동 계측에 대 한 [지침](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) 을 따르세요.

Application Insights 에이전트/확장에 대 한 최신 정보는 [릴리스](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)정보를 확인 하세요.

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](../platform/diagnostics-extension-to-application-insights.md) 하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../platform/data-platform.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../platform/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.
