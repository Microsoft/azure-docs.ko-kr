---
title: Azure Application Insights에서 개발, 테스트 및 릴리스의 원격 분석 구분 | Microsoft Docs
description: 개발, 테스트 및 프로덕션 스탬프에 대한 다양한 리소스에 직접 원격 분석
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2e9c599c12ed10327d352baee02500d2284d98d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713469"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>개발, 테스트 및 프로덕션의 원격 분석 구분

웹 애플리케이션의 다음 버전을 개발할 때 새 버전과 이미 릴리스된 버전의 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 원격 분석이 혼동되지 않도록 하려고 합니다. 혼동을 방지하기 위해 서로 다른 개발 단계의 원격 분석을 별도의 계측 키(ikeys)와 함께 별도의 Application Insights 리소스에 보냅니다. 버전이 단계별로 이동됨에 따라 계측 키 변경을 보다 쉽게 하기 위해서는 구성 파일 대신 코드에 ikey를 설정하는 것이 더 나을 수 있습니다. 

시스템이 Azure 클라우드 서비스인 경우 [별도의 ikey를 설정하는 다른 방법](../../azure-monitor/app/cloudservices.md)이 있습니다.

## <a name="about-resources-and-instrumentation-keys"></a>리소스 및 계측 키 정보

사용자 웹앱에 대해 Application Insights 모니터링을 설정할 경우 Microsoft Azure에서 Application Insights *리소스*를 만듭니다. 앱에서 수집된 원격 분석을 보고 분석하기 위해서는 Azure Portal에서 이 리소스를 엽니다. 리소스는 해당 *계측 키*(ikey)로 식별됩니다. 앱을 모니터링하기 위해 Application Insights 패키지를 설치하는 경우 원격 분석을 보낼 위치를 파악할 수 있도록 계측 키로 구성합니다.

일반적으로 다양한 시나리오에 별도의 리소스 또는 단일 공유 리소스를 사용하도록 선택합니다.

* 서로 다른 독립적인 애플리케이션 - 각 앱에 대해 별도의 리소스와 ikey를 사용합니다.
* 한 비즈니스 애플리케이션의 여러 구성 요소 또는 역할 - 모든 구성 요소 앱에 대해 [단일 공유 리소스](../../azure-monitor/app/app-map.md)를 사용합니다. cloud_RoleName 속성에 따라 원격 분석을 필터링 또는 분할할 수 있습니다.
* 개발, 테스트 및 릴리스 - '스탬프' 프로덕션 단계에서 시스템 버전에 따라 별도의 리소스 및 ikey를 사용합니다.
* A | B 테스트 - 단일 리소스를 사용합니다. 변형을 식별하는 원격 분석에 속성을 추가하는 TelemetryInitializer를 만듭니다.


## <a name="dynamic-ikey"></a> 동적 계측 키

코드가 프로덕션 단계 간에 이동함에 따라 보다 쉽게 ikey를 변경할 수 있도록 구성 파일 대신 코드에 설정합니다.

ASP.NET 서비스의 global.aspx.cs 같은 초기화 메서드에서 키를 설정합니다.

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

이 예제에서는 서로 다른 리소스에 대한 ikeys는 다른 버전의 웹 구성 파일에 배치됩니다. 웹 구성 파일 교체는 릴리스 스크립트의 일부로 수행될 수 있고 대상 리소스를 교체합니다.

### <a name="web-pages"></a>웹 페이지
iKey는 [빠른 시작 블레이드에서 가져온 스크립트](../../azure-monitor/app/javascript.md)내의 응용 프로그램 웹페이지에서도 사용 가능합니다. 스크립트에 문자 그대로 코딩하는 대신, 서버 상태로부터 생성합니다. 예를 들어, ASP.NET 응용 프로그램에서:

*Razor에서 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>추가 Application Insights 리소스 만들기
동일한 구성 요소의 다른 애플리케이션 구성 요소 또는 다른 스탬프(개발/테스트/프로덕션)에 대한 원격 분석을 분리하려면 새 Application Insights 리소스를 만들어야 합니다.

[portal.azure.com](https://portal.azure.com)에서 Application Insights 리소스를 추가합니다.

![새로 만들기, Application Insights 클릭](./media/separate-resources/01-new.png)

* **애플리케이션 유형**은 개요 블레이드에 표시되는 내용 및 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)에서 사용할 수 있는 속성에 영향을 줍니다. 앱 유형이 표시되지 않으면 웹 페이지에 대해 웹 유형 중 하나를 선택합니다.
* **리소스 그룹** 은 [액세스 제어](../../azure-monitor/app/resources-roles-access-control.md)와 같은 속성을 관리하기 위한 편의 기능입니다. 개발, 테스트 및 프로덕션 환경에 대 한 별도 리소스 그룹을 사용할 수 있습니다.
* **구독** 은 Azure의 지불 계정입니다.
* **위치** 는 데이터를 보관하는 곳입니다. 현재는 변경할 수 없습니다. 
* **대시보드에 추가** 는 Azure 홈 페이지에 리소스에 대한 빠른 액세스 타일을 넣습니다. 

리소스 생성 시 몇 초 정도 걸립니다. 완료되면 알림이 표시 됩니다.

(리소스를 자동으로 만드는 [PowerShell 스크립트](../../azure-monitor/app/powershell-script-create-resource.md) 를 작성할 수 있습니다).

### <a name="getting-the-instrumentation-key"></a>계측 키 가져오기
계측 키는 사용자가 만든 리소스를 식별합니다. 

![Essentials과 계측 키를 차례로 클릭하고, CTRL + C 누릅니다.](./media/separate-resources/02-props.png)

사용자의 앱이 데이터를 보낼 모든 리소스의 계측 키가 필요합니다.

## <a name="filter-on-build-number"></a>빌드 번호 필터링
앱의 새 버전을 게시하면서 다른 빌드의 원격 분석을 구분하고자 할 수 있습니다.

애플리케이션 버전 속성을 설정하여 [검색](../../azure-monitor/app/diagnostic-search.md) 및 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md) 결과를 필터링할 수 있습니다.

![속성 필터링](./media/separate-resources/050-filter.png)

애플리케이션 버전 속성은 몇 가지 다른 방법으로 설정할 수 있습니다.

* 직접 설정:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* [원격 분석 이니셜라이저](../../azure-monitor/app/api-custom-events-metrics.md#defaults) 에서 해당 줄을 래핑하여 모든 TelemetryClient 인스턴스가 일관되게 설정되었는지 확인합니다.
* [ASP.NET] `BuildInfo.config`에서 버전을 설정합니다. 웹 모듈은 BuildLabel 노드에서 버전을 선택합니다. 프로젝트에 이 파일을 포함하고 솔루션 탐색기에서 항상 복사 속성을 설정합니다.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] MSBuild에서 BuildInfo.config를 자동으로 생성합니다. 이 작업을 수행하려면 `.csproj` 파일에 몇 줄을 추가합니다.

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    그러면 *yourProjectName*.BuildInfo.config 파일이 생성됩니다. 게시 프로세스의 이름이 BuildInfo.config로 바뀝니다.

    Visual Studio를 사용하여 빌드할때 빌드 레이블에는 자리 표시자(AutoGen_...)가 포함됩니다. MSBuild로 빌드할 때는 정확한 버전 번호가 입력됩니다.

    MSBuild가 버전 번호를 생성하게 하려면 AssemblyReference.cs에서 `1.0.*` 같이 버전을 설정합니다.

## <a name="version-and-release-tracking"></a>버전 및 릴리스 추적
애플리케이션 버전을 추적하려면 `buildinfo.config`가 Microsoft Build Engine 프로세스에 의해 생성되도록 해야 합니다. .csproj 파일에서 다음을 추가합니다.  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

빌드 정보가 있는 경우 Application Insights 웹 모듈에서 원격 분석의 모든 항목에 **애플리케이션 버전**을 속성으로 자동으로 추가합니다. 이렇게 하면 [진단 검색](../../azure-monitor/app/diagnostic-search.md)을 수행하거나 [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md)할 때 버전을 기준으로 필터링할 수 있습니다.

그러나 빌드 버전 번호는 Visual Studio의 개발자 빌드가 아니라 Microsoft Build Engine에서만 생성된다는 점에 유의해야 합니다.

### <a name="release-annotations"></a>릴리스 주석
Azure DevOps를 사용하는 경우 새 버전을 릴리스할 때마다 [주석 표식](../../azure-monitor/app/annotations.md)이 차트에 추가됩니다. 다음 이미지는 이러한 표식이 어떻게 나타나는지를 보여줍니다.

![차트의 샘플 릴리스 주석 스크린샷](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>다음 단계

* [여러 역할에 대한 공유 리소스](../../azure-monitor/app/app-map.md)
* [A|B 변형을 구분하는 원격 분석 이니셜라이저 만들기](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
