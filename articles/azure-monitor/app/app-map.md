---
title: Azure Application Insights의 애플리케이션 맵 | Microsoft Docs
description: 애플리케이션 맵을 사용하여 복잡한 애플리케이션 토폴로지 모니터링
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: d4067b23c4ff365857c37d7dfdce6dc42e129989
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936608"
---
# <a name="application-map-triage-distributed-applications"></a>애플리케이션 맵: 분산 애플리케이션 심사

애플리케이션 맵을 사용하면 분산된 애플리케이션의 모든 구성 요소에서 성능 병목 상태 또는 실패 핫스폿을 찾는 데 도움이 됩니다. 맵의 각 노드는 애플리케이션 구성 요소 또는 해당 종속성을 나타내며 상태 KPI 및 경고 상태를 나타냅니다. 구성 요소부터 Application Insights 이벤트와 같은 보다 자세한 진단까지 클릭하면서 살펴볼 수 있습니다. 앱에서 Azure 서비스를 사용하는 경우 SQL Database Advisor 권장 사항과 같은 Azure 진단도 살펴볼 수 있습니다.

## <a name="what-is-a-component"></a>구성 요소란?

구성 요소는 독립적으로 배포할 수 있는 분산/마이크로 서비스 애플리케이션의 부분입니다. 개발자 및 운영 팀은 이러한 애플리케이션 구성 요소에서 생성된 원격 분석에 대한 코드 수준의 가시성 또는 액세스 권한을 갖습니다. 

* 구성 요소는 팀/조직에서 액세스할 수 없는 SQL, EventHub 등과 같은 "관찰된" 외부 종속성(코드 또는 원격 분석)과 다릅니다.
* 구성 요소는 서버/역할/컨테이너 인스턴스의 수에 관계없이 실행됩니다.
* 구성 요소는 별도의 Application Insights 계측 키(구독이 다른 경우에도) 또는 단일 Application Insights 계측 키에 보고하는 다른 역할일 수 있습니다. 맵 미리 보기 환경은 구성 요소를 설정 방식에 관계없이 표시합니다.

## <a name="composite-application-map"></a>복합 애플리케이션 맵

여러 수준의 관련된 애플리케이션 구성 요소의 전체 애플리케이션 토폴로지를 볼 수 있습니다. 구성 요소는 다른 Application Insights 리소스이거나 단일 리소스 내의 다른 역할일 수 있습니다. 앱은 Application Insights SDK가 설치된 서버 간에 수행된 HTTP 종속성 호출에 따라 구성 요소를 찾습니다. 

이러한 환경은 구성 요소를 점진적으로 검색으로 시작됩니다. 응용 프로그램 맵을 처음 로드할 때이 구성 요소와 관련 된 구성 요소를 검색 하기 위해 쿼리 집합이 트리거됩니다. 왼쪽 위 구석에 있는 단추는 검색된 애플리케이션의 구성 요소 수로 업데이트됩니다. 

"맵 구성 요소 업데이트"를 클릭하면 맵이 해당 시점까지 검색된 모든 구성 요소로 새로 고쳐집니다. 애플리케이션의 복잡성에 따라, 로드하는 데 다소 시간이 걸릴 수 있습니다.

모든 구성 요소가 단일 Application Insights 리소스 내의 역할인 경우 이 검색 단계가 필요하지 않습니다. 이러한 애플리케이션에 대한 초기 로드 시에는 모든 구성 요소가 포함됩니다.

![애플리케이션 맵 스크린샷](media/app-map/app-map-001.png)

수백 가지 구성 요소가 있는 복잡한 토폴로지를 시각화하는 것이 이 환경의 주요 목표 중 하나입니다.

모든 구성 요소를 클릭하여 관련 정보를 확인하고, 해당 구성 요소에 대한 성능 및 실패 심사 환경으로 이동합니다.

![플라이아웃](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>오류 조사

**오류 조사**를 선택하여 오류 창을 실행합니다.

![오류 조사 단추 스크린샷](media/app-map/investigate-failures.png)

![오류 환경 스크린샷](media/app-map/failures.png)

### <a name="investigate-performance"></a>성능 조사

성능 문제를 해결 하려면 **성능 조사**를 선택 합니다.

![성능 조사 단추 스크린샷](media/app-map/investigate-performance.png)

![성능 환경 스크린샷](media/app-map/performance.png)

### <a name="go-to-details"></a>세부 정보로 이동

**세부 정보로 이동** 을 선택 하 여 호출 스택 수준까지 뷰를 제공할 수 있는 종단 간 트랜잭션 환경을 탐색 합니다.

![세부 정보로 이동 단추 스크린샷](media/app-map/go-to-details.png)

![엔드투엔드 트랜잭션 세부 정보 스크린샷](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>로그 보기 (분석)

응용 프로그램 데이터를 더 쿼리하고 조사 하려면 **로그에서 보기 (분석)** 를 클릭 합니다.

![Analytics에서 보기 단추 스크린샷](media/app-map/view-logs.png)

![분석 환경의 스크린샷 지난 12 시간 동안의 요청 평균 응답 기간을 요약 하는 선 그래프입니다.](media/app-map/log-analytics.png)

### <a name="alerts"></a>경고

경고가 트리거될 수 있는 활성 경고 및 기본 규칙을 보려면 **경고**를 선택합니다.

![경고 단추 스크린샷](media/app-map/alerts.png)

![Analytics 환경 스크린샷](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>클라우드 역할 이름 설정 또는 재정의

응용 프로그램 맵은 **클라우드 역할 이름** 속성을 사용 하 여 맵의 구성 요소를 식별 합니다. 클라우드 역할 이름을 수동으로 설정 또는 재정의 하 고 응용 프로그램 맵에 표시 되는 항목을 변경 하려면:

> [!NOTE]
> Application Insights SDK 또는 에이전트는 Azure App Service 환경의 구성 요소에서 내보낸 원격 분석에 클라우드 역할 이름 속성을 자동으로 추가 합니다.

# <a name="netnetcore"></a>[.NET/NetCore](#tab/net)

**아래와 같이 사용자 지정 TelemetryInitializer을 작성 합니다.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET apps: 활성 TelemetryConfiguration에 이니셜라이저를 로드 합니다.**

ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

웹 앱을 ASP.NET 하는 다른 방법은 코드에서 이니셜라이저를 인스턴스화하는 것입니다 (예: Global.aspx.cs).

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> `ApplicationInsights.config` 또는 `TelemetryConfiguration.Active`를 사용하여 이니셜라이저를 추가하는 것은 ASP.NET Core 애플리케이션에 적합하지 않습니다. 

**ASP.NET Core apps: TelemetryConfiguration에 이니셜라이저를 로드 합니다.**

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 애플리케이션의 경우 새 `TelemetryInitializer` 추가 작업은 아래와 같이 종속성 주입 컨테이너에 추가하여 수행됩니다. 이 작업은 `Startup.cs` 클래스의 `ConfigureServices` 메서드에서 수행됩니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java 에이전트**

[Java 에이전트 3.0](./java-in-process-agent.md) 의 경우 클라우드 역할 이름은 다음과 같이 설정 됩니다.

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

환경 변수를 사용 하 여 클라우드 역할 이름을 설정할 수도 있습니다 ```APPLICATIONINSIGHTS_ROLE_NAME``` .

**Java SDK**

Application Insights Java SDK 2.5.0부터 SDK를 사용 하는 경우 파일에를 추가 하 여 클라우드 역할 이름을 지정할 수 있습니다 `<RoleName>` `ApplicationInsights.xml` (예:).

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Application Insights Spring Boot 스타터에서 Spring Boot를 사용하는 경우 application.properties 파일에서 애플리케이션에 대한 사용자 지정 이름을 설정하도록 변경하기만 하면 됩니다.

`spring.application.name=<name-of-app>`

스프링 부팅 스타터는 spring.application.name 속성에 대해 사용자가 입력 한 값에 클라우드 역할 이름을 자동으로 할당 합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Node.js에 대한 대체 방법

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>응용 프로그램 맵의 컨텍스트 내에서 클라우드 역할 이름 이해

**클라우드 역할 이름**에 대해 생각 하는 것과 같이 여러 개의 클라우드 역할 이름이 있는 응용 프로그램 맵을 살펴보면 도움이 될 수 있습니다.

![애플리케이션 맵 스크린샷](media/app-map/cloud-rolename.png)

위의 응용 프로그램 맵에서 녹색 상자에는이 특정 배포 응용 프로그램의 다양 한 측면에 대 한 클라우드 역할 이름 값이 있습니다. 따라서이 앱의 역할은 `Authentication` ,, `acmefrontend` `Inventory Management` , a로 `Payment Processing Worker Role` 구성 됩니다. 

이 앱의 경우 각 클라우드 역할 이름은 고유한 계측 키가 있는 다른 고유한 Application Insights 리소스도 나타냅니다. 이 응용 프로그램의 소유자는 이러한 4 개의 서로 다른 Application Insights 리소스에 대해 액세스할 수 있으므로 응용 프로그램 맵은 기본 관계의 맵을 함께 연결할 수 있습니다.

[공식 정의](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)의 경우:

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

또는 클라우드 역할 **인스턴스** 는 **클라우드 역할 이름이** 웹 프런트 엔드의 어딘가에 문제가 있음을 알리는 시나리오에 유용할 수 있습니다. 그러나 부하 분산 된 여러 서버에서 웹 프런트 엔드를 실행 하 여 kusto 쿼리를 통해 더 심층적으로 계층을 드릴 하 고 문제가 모든 웹 프런트 엔드 서버/인스턴스에 영향을 주는지 아니면 하나만 매우 중요할 수 있는지 확인할 수 있습니다.

앱이 특정 문제를 찾기에 충분 한 정보가 없을 수 있는 컨테이너 화 된 환경에서 앱이 실행 되는 경우 클라우드 역할 인스턴스의 값을 재정의 하는 것이 좋습니다.

원격 분석 이니셜라이저를 사용 하 여 클라우드 역할 이름 속성을 재정의 하는 방법에 대 한 자세한 내용은 [Add 속성: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)를 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

Application Map이 예상대로 작동하지 않는 경우 다음 단계를 수행하세요.

### <a name="general"></a>일반

1. 공식적으로 지원되는 SDK를 사용하고 있는지 확인합니다. 비지원/커뮤니티 SDK는 상관 관계를 지원하지 않을 수 있습니다.

    지원되는 SDK 목록에 대해서는 이 [문서](./platforms.md)를 참조하세요.

2. 모든 구성 요소를 최신 SDK 버전으로 업그레이드합니다.

3. C #을 사용 하 여 Azure Functions를 사용 하는 경우 [V2 함수로](../../azure-functions/functions-versions.md)업그레이드 합니다.

4. [클라우드 역할 이름이](#set-or-override-cloud-role-name) 올바르게 구성 되었는지 확인 합니다.

5. 종속성이 누락된 경우 [자동 수집 종속성](./auto-collect-dependencies.md) 목록에 들어 있는지 확인합니다. 이 목록에 없어도 [종속성 호출 추적](./api-custom-events-metrics.md#trackdependency)을 사용하여 수동으로 계속 추적할 수 있습니다.

### <a name="too-many-nodes-on-the-map"></a>맵에 노드가 너무 많음

응용 프로그램 맵은 요청 원격 분석에 있는 고유한 각 클라우드 역할 이름에 대 한 응용 프로그램 노드를 생성 하 고 종속성 원격 분석에서 형식, 대상 및 클라우드 역할 이름의 각 고유 조합에 대 한 종속성 노드를 생성 합니다. 원격 분석에 노드가 1만 개 이상 있는 경우 응용 프로그램 맵은 모든 노드와 링크를 인출할 수 없으므로 map이 완전 하지 않습니다. 이 경우 맵을 볼 때 경고 메시지가 표시 됩니다.

또한 응용 프로그램 맵은 한 번에 렌더링 된 분리 되지 않은 개별 노드를 최대 1000 개만 지원 합니다. 응용 프로그램 맵은 동일한 유형 및 호출자를 포함 하는 종속성을 그룹화 하 여 시각적 복잡성을 줄여 주지만 원격 분석에 고유한 클라우드 역할 이름이 너무 많거나 너무 많은 종속성 유형이 있는 경우 해당 그룹화는 충분 하지 않으며 지도를 렌더링할 수 없게 됩니다.

이 문제를 해결 하려면 클라우드 역할 이름, 종속성 유형 및 종속성 대상 필드를 올바르게 설정 하도록 계측을 변경 해야 합니다.

* 종속성 대상은 종속성의 논리적 이름을 나타내야 합니다. 대부분의 경우 종속성의 서버 또는 리소스 이름과 동일 합니다. 예를 들어 HTTP 종속성의 경우 호스트 이름으로 설정 됩니다. 한 요청에서 다른 요청으로 변경 되는 고유 Id 또는 매개 변수를 포함 해서는 안 됩니다.

* 종속성 유형은 종속성의 논리적 유형을 나타내야 합니다. 예를 들어 HTTP, SQL 또는 Azure Blob은 일반적으로 종속성 유형입니다. 고유 Id를 포함 해서는 안 됩니다.

* 클라우드 역할 이름의 용도는 [위의 섹션](#set-or-override-cloud-role-name)에 설명 되어 있습니다.

## <a name="portal-feedback"></a>포털 사용자 의견

사용자 의견을 제공하려면 사용자 의견 옵션을 사용하세요.

![MapLink-1 이미지](./media/app-map/14-updated.png)

## <a name="next-steps"></a>다음 단계

* 에서 상관 관계 Application Insights의 작동 방식에 대 한 자세한 내용은 [원격 분석 상관 관계 문서](correlation.md)를 참조 하세요.
* [종단 간 트랜잭션 진단 환경은](transaction-diagnostics.md) 모든 Application Insights 모니터링 되는 구성 요소에서 서버 쪽 원격 분석을 단일 보기로 상호 연결 합니다.
* ASP.NET Core 및 ASP.NET의 고급 상관 관계 시나리오는 [사용자 지정 작업 추적](custom-operations-tracking.md) 문서를 참조 하세요.