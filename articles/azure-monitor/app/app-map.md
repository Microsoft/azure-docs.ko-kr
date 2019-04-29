---
title: Azure Application Insights의 애플리케이션 맵 | Microsoft Docs
description: 애플리케이션 맵을 사용하여 복잡한 애플리케이션 토폴로지 모니터링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 89aa5006882680205816e7e5d1e7e55b9c4b2ab0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693188"
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

이러한 환경은 구성 요소를 점진적으로 검색으로 시작됩니다. Application map을 처음 로드 하면이 구성 요소와 관련 된 구성 요소를 검색 쿼리 집합이 트리거됩니다. 왼쪽 위 구석에 있는 단추는 검색된 애플리케이션의 구성 요소 수로 업데이트됩니다. 

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

성능 문제를 해결하려면 **성능 조사**를 선택합니다.

![성능 조사 단추 스크린샷](media/app-map/investigate-performance.png)

![성능 환경 스크린샷](media/app-map/performance.png)

### <a name="go-to-details"></a>세부 정보로 이동

**세부 정보로 이동**을 선택하여 호출 스택 수준으로 수행된 보기를 제공할 수 있는 엔드 투 엔드 트랜잭션 환경을 탐색합니다.

![세부 정보로 이동 단추 스크린샷](media/app-map/go-to-details.png)

![종단 간 트랜잭션 세부 정보 스크린샷](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Analytics에서 보기

애플리케이션 데이터를 더 쿼리하고 조사하려면 **Analytics에서 보기**를 클릭합니다.

![Analytics에서 보기 단추 스크린샷](media/app-map/view-in-analytics.png)

![Analytics 환경 스크린샷](media/app-map/analytics.png)

### <a name="alerts"></a>경고

경고가 트리거될 수 있는 활성 경고 및 기본 규칙을 보려면 **경고**를 선택합니다.

![경고 단추 스크린샷](media/app-map/alerts.png)

![Analytics 환경 스크린샷](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>클라우드 역할 이름 설정

응용 프로그램 맵을 사용 하는 **클라우드 역할 이름** 맵에서 구성 요소를 식별 하는 속성입니다. Application Insights SDK 구성 요소에서 내보낸 원격 분석에 클라우드 역할 이름 속성을 자동으로 추가 합니다. 예를 들어, SDK 클라우드 역할 이름 속성에는 웹 사이트 이름 또는 서비스 역할 이름을 추가 합니다. 그러나 기본값을 대체할 수 있는 경우가 있습니다. 클라우드 역할 이름 재정의 및 Application Map에 표시 되는 내용 변경:

### <a name="net"></a>.NET

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
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**이니셜라이저 로드**

ApplicationInsights.config에서:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

또는 코드에서 이니셜라이저를 인스턴스화할 수 있습니다(예: Global.aspx.cs).

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

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

### <a name="java"></a>자바

Application Insights Spring Boot 스타터에서 Spring Boot를 사용하는 경우 application.properties 파일에서 애플리케이션에 대한 사용자 지정 이름을 설정하도록 변경하기만 하면 됩니다.

`spring.application.name=<name-of-app>`

Spring Boot starter는 자동으로 클라우드 역할 이름 spring.application.name 속성에 대 한 입력 값에 할당 합니다.

Java에 대 한 자세한 내용은 클라우드 역할을 구성 하는 방법과 상관 관계 이름을 아닌 SpringBoot 응용 프로그램 체크 아웃할 [섹션](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) 상관 관계에 있습니다.

### <a name="clientbrowser-side-javascript"></a>클라이언트/브라우저 쪽 JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>응용 프로그램 맵의 컨텍스트 내에서 클라우드 역할 이름 이해

생각 하는 방법에 관해서는 **클라우드 역할 이름**, 여러 클라우드 역할 이름에 있는 응용 프로그램 맵을 살펴보면 도움이 될 수 있습니다.

![애플리케이션 맵 스크린샷](media/app-map/cloud-rolename.png)

위의 각 녹색 상자에 이름을 Application Map에 클라우드 역할 이름 값이 특정 배포 응용 프로그램의 다양 한 측면에 대 한 합니다. 이 앱에 대해 해당 역할의 구성 되므로: `Authentication`, `acmefrontend`를 `Inventory Management`, `Payment Processing Worker Role`합니다. 

이 앱의 경우 각 해당 클라우드 역할 이름 또한 나타냅니다 다른 고유한 Application Insights 리소스를 자체 계측 키를 사용 하 여 합니다. 이 응용 프로그램의 소유자가 각 네 개의 서로 다른 Application Insights 리소스에 대 한 액세스를 응용 프로그램 맵 이므로 기본 관계의 맵을 함께 연결할 수 있습니다.

에 대 한 합니다 [공식 정의](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

또는 **클라우드 역할 인스턴스** 시나리오에 유용할 수 있는 **클라우드 역할 이름** 알려 웹 프런트 엔드, 안에 있으면 문제가 있지만에서 프런트 엔드 웹 실행 중일 수 있습니다 여러 부하 분산 된 서버 Kusto 쿼리를 통해 심층적인 계층에서 드릴 없게 및 모든 웹 프런트 엔드 서버/인스턴스에 영향을 주는 문제는 아니면 하나만 매우 중요할 수 있습니다.

클라우드 역할 인스턴스에 대 한 값을 재정의 하려는 경우 앱 실행 되 고 있는지를 컨테이너 화 된 환경에서 개별 서버 아는 하지 수 있는 충분 한 정보를 특정된 문제를 찾을 수 있습니다.

원격 분석 이니셜라이저를 사용 하 여 클라우드 역할 이름 속성을 재정의 하는 방법에 대 한 자세한 내용은 참조 하세요. [속성을 추가 합니다. ITelemetryInitializer 추가](api-filtering-sampling.md#add-properties-itelemetryinitializer)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

Application Map이 예상대로 작동하지 않는 경우 다음 단계를 수행하세요.

### <a name="general"></a>일반

1. 공식적으로 지원되는 SDK를 사용하고 있는지 확인합니다. 비지원/커뮤니티 SDK는 상관 관계를 지원하지 않을 수 있습니다.

    지원되는 SDK 목록에 대해서는 이 [문서](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)를 참조하세요.

2. 모든 구성 요소를 최신 SDK 버전으로 업그레이드합니다.

3. C#으로 Azure Functions를 사용하는 경우 [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions)로 업그레이드합니다.

4. 확인 [클라우드 역할 이름](#set-cloud-role-name) 올바르게 구성 되어 있습니다.

5. 종속성이 누락된 경우 [자동 수집 종속성](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) 목록에 들어 있는지 확인합니다. 이 목록에 없어도 [종속성 호출 추적](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)을 사용하여 수동으로 계속 추적할 수 있습니다.

### <a name="too-many-nodes-on-the-map"></a>지도에 너무 많은 노드

응용 프로그램 맵 형식, 대상 및 클라우드 역할 이름 종속성 원격 분석에서 각 고유 조합에 대 한 종속성 노드와 요청 원격 분석에 있는 각 고유한 클라우드 역할 이름에 대 한 응용 프로그램 노드를 생성합니다. 원격 분석에서 10,000 개 보다 많은 노드가 없으면 응용 프로그램 맵 맵에 표시 되지 않습니다 있도록 모든 노드 및 링크를 가져올 수 없습니다. 이 경우 맵을 볼 때 경고 메시지가 표시 됩니다.

또한 응용 프로그램 맵만 렌더링을 한 번에 최대 1,000 개의 별도 그룹화 되지 않은 노드를 지원 합니다. 응용 프로그램 맵 같은 형식 및 호출자가 종속성을 함께 그룹화 하 여 시각적 복잡성 감소 하지만 원격 분석에 너무 많은 고유한 클라우드 역할 이름 또는 너무 많은 종속성 종류, 그룹화 되지 충분 하 고 지도 할 수 없습니다. 렌더링 합니다.

이 해결 하려면 클라우드 역할 이름, 종속성 유형 및 종속성 대상 필드를 올바르게 설정 하 여 계측을 변경 해야 합니다.

* 종속성 대상 종속성의 논리적 이름을 나타내야 합니다. 대부분의 경우 서버 또는 종속성의 리소스 이름에 동일 합니다. 예를 들어, HTTP 종속성의 경우 설정은 호스트 이름입니다. 고유 Id 또는 다른 요청에서을 변경 하는 매개 변수는 포함 하지 않아야 합니다.

* 종속성 유형 종속성 논리 형식을 나타내야 합니다. 예를 들어, HTTP, SQL 또는 Azure Blob 형식은 일반적인 종속성입니다. 고유 Id는 포함 되지 않습니다.

* 클라우드 역할 이름의 용도에 설명 되어는 [구역 위에](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name)합니다.

## <a name="portal-feedback"></a>포털 사용자 의견

사용자 의견을 제공하려면 사용자 의견 옵션을 사용하세요.

![MapLink-1 이미지](./media/app-map/14-updated.png)

## <a name="next-steps"></a>다음 단계

* [상관 관계 이해](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)