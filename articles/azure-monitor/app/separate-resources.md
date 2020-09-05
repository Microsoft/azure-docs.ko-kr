---
title: Application Insights 배포를 설계하는 방법 - 하나의 리소스 및 여러 리소스
description: 개발, 테스트 및 프로덕션 스탬프에 대한 다양한 리소스에 직접 원격 분석
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 264cbe35e7af50577b345d686b639e47760f428d
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258721"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>배포해야 하는 Application Insights 리소스의 수

웹 애플리케이션의 다음 버전을 개발할 때 새 버전과 이미 릴리스된 버전의 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 원격 분석이 혼동되지 않도록 하려고 합니다. 혼동을 방지하기 위해 서로 다른 개발 단계의 원격 분석을 별도의 계측 키(ikeys)와 함께 별도의 Application Insights 리소스에 보냅니다. 버전이 단계별로 이동됨에 따라 계측 키 변경을 보다 쉽게 하기 위해서는 구성 파일 대신 코드에 ikey를 설정하는 것이 더 나을 수 있습니다.

시스템이 Azure 클라우드 서비스인 경우 [별도의 ikey를 설정하는 다른 방법](../../azure-monitor/app/cloudservices.md)이 있습니다.

## <a name="about-resources-and-instrumentation-keys"></a>리소스 및 계측 키 정보

사용자 웹앱에 대해 Application Insights 모니터링을 설정할 경우 Microsoft Azure에서 Application Insights *리소스*를 만듭니다. 앱에서 수집된 원격 분석을 보고 분석하기 위해서는 Azure Portal에서 이 리소스를 엽니다. 리소스는 해당 *계측 키*(ikey)로 식별됩니다. 앱을 모니터링하기 위해 Application Insights 패키지를 설치하는 경우 원격 분석을 보낼 위치를 파악할 수 있도록 계측 키로 구성합니다.

각 Application Insights 리소스에는 기본 제공되는 메트릭이 제공됩니다. 완전히 별개의 구성 요소가 동일한 Application Insights 리소스에 보고되는 경우 이러한 메트릭은 대시보드/경고에 적합하지 않을 수 있습니다.

### <a name="when-to-use-a-single-application-insights-resource"></a>단일 Application Insights 리소스를 사용하는 경우

-   함께 배포되는 애플리케이션 구성 요소의 경우 일반적으로 단일 팀에서 개발되며 동일한 DevOps/ITOps 사용자 세트를 통해 관리됩니다.
-   기본적으로 응답 기간, 대시보드의 실패율 등과 같은 KPI(핵심 성과 지표)를 전체적으로 집계하는 것이 적절한 경우에는 메트릭 탐색기 환경에서 역할 이름별로 분류하도록 선택할 수 있습니다.
-   애플리케이션 구성 요소 간에 RBAC(역할 기반 액세스 제어)를 다르게 관리할 필요가 없는 경우
-   구성 요소 간에 다른 메트릭 경고 조건이 필요하지 않은 경우
-   구성 요소 간에 연속 내보내기를 다르게 관리할 필요가 없는 경우
-   구성 요소 간에 청구/할당량을 다르게 관리할 필요가 없는 경우
-   API 키가 모든 구성 요소의 데이터에 동일한 액세스 권한을 갖도록 할 수 있는 경우 그리고 10개의 API 키를 통해 모든 요구 사항을 충족할 수 있는 경우
-   모든 역할에 동일한 스마트 검색 및 작업 항목 통합 설정을 사용해야 하는 경우

### <a name="other-things-to-keep-in-mind"></a>주의해야 할 기타 사항

-   의미 있는 값이 [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) 특성으로 설정되도록 사용자 지정 코드를 추가해야 할 수 있습니다. 이 특성에 대해 의미 있는 값이 설정되지 않으면 포털 환경의 *NONE*이 작동합니다.
- Service Fabric 애플리케이션 및 클래식 클라우드 서비스의 경우 SDK는 Azure 역할 환경에서 자동으로 읽고 이를 설정합니다. 다른 모든 유형의 앱의 경우 이를 명시적으로 설정해야 할 수도 있습니다.
-   라이브 메트릭 환경은 역할 이름별로 분할하는 것을 지원하지 않습니다.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> 동적 계측 키

코드가 프로덕션 단계 사이에서 이동할 때 ikey를 더 쉽게 변경할 수 있도록 하드 코드 된/정적 값을 사용 하는 대신 코드에서 키를 동적으로 참조 합니다.

ASP.NET 서비스의 global.aspx.cs 같은 초기화 메서드에서 키를 설정합니다.

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

이 예제에서는 서로 다른 리소스에 대한 ikeys는 다른 버전의 웹 구성 파일에 배치됩니다. 웹 구성 파일 교체는 릴리스 스크립트의 일부로 수행될 수 있고 대상 리소스를 교체합니다.

### <a name="web-pages"></a>웹 페이지
iKey는 [빠른 시작 창에서 가져온 스크립트](../../azure-monitor/app/javascript.md)에 있는 앱의 웹페이지에서도 사용됩니다. 스크립트에 문자 그대로 코딩하는 대신, 서버 상태로부터 생성합니다. 예를 들어, ASP.NET 응용 프로그램에서:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>추가 Application Insights 리소스 만들기

Application Insights 리소스를 만들려면 [리소스 생성 가이드](./create-new-resource.md)를 따르세요.

### <a name="getting-the-instrumentation-key"></a>계측 키 가져오기
계측 키는 사용자가 만든 리소스를 식별합니다.

사용자의 앱이 데이터를 보낼 모든 리소스의 계측 키가 필요합니다.

## <a name="filter-on-build-number"></a>빌드 번호 필터링
앱의 새 버전을 게시하면서 다른 빌드의 원격 분석을 구분하고자 할 수 있습니다.

애플리케이션 버전 속성을 설정하여 [검색](../../azure-monitor/app/diagnostic-search.md) 및 [메트릭 탐색기](../../azure-monitor/platform/metrics-charts.md) 결과를 필터링할 수 있습니다.

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
애플리케이션 버전을 추적하려면 `buildinfo.config`가 Microsoft Build Engine 프로세스에 의해 생성되도록 해야 합니다. `.csproj` 파일에서 다음을 추가합니다.  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

빌드 정보가 있는 경우 Application Insights 웹 모듈에서 원격 분석의 모든 항목에 **애플리케이션 버전**을 속성으로 자동으로 추가합니다. 이렇게 하면 [진단 검색](../../azure-monitor/app/diagnostic-search.md)을 수행하거나 [메트릭을 탐색](../../azure-monitor/platform/metrics-charts.md)할 때 버전을 기준으로 필터링할 수 있습니다.

그러나 빌드 버전 번호는 Visual Studio의 개발자 빌드가 아니라 Microsoft Build Engine에서만 생성됩니다.

### <a name="release-annotations"></a>릴리스 주석
Azure DevOps를 사용하는 경우 새 버전을 릴리스할 때마다 [주석 표식](../../azure-monitor/app/annotations.md)이 차트에 추가됩니다. 

## <a name="next-steps"></a>다음 단계

* [여러 역할에 대한 공유 리소스](../../azure-monitor/app/app-map.md)
* [A|B 변형을 구분하는 원격 분석 이니셜라이저 만들기](../../azure-monitor/app/api-filtering-sampling.md#add-properties)