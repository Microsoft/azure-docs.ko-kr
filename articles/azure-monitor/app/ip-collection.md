---
title: Azure 애플리케이션 Insights IP 주소 컬렉션 | Microsoft Docs
description: Azure 애플리케이션 정보를 사용 하 여 IP 주소 및 지리적 위치를 처리 하는 방법 이해
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/11/2019
ms.openlocfilehash: 5a647dda21855f754754f76682e5c00443eaac55
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432598"
---
# <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리

이 문서에서는 기본 동작을 수정 하는 방법과 함께 Application Insights에서 지리적 위치 조회 및 IP 주소 처리를 수행 하는 방법을 설명 합니다.

## <a name="default-behavior"></a>기본 동작

기본적으로 IP 주소는 임시로 수집 되지만 Application Insights에 저장 되지 않습니다. 기본 프로세스는 다음과 같습니다.

IP 주소는 원격 분석 데이터의 일부로 Application Insights 전송 됩니다. Azure에서 수집 엔드포인트에 도달 하면 IP 주소는 [MaxMind에서 GeoLite2를](https://dev.maxmind.com/geoip/geoip2/geolite2/)사용 하 여 지리적 위치 조회를 수행 하는 데 사용 됩니다. 이 조회 결과는 다음 필드 `client_City`, `client_StateOrProvince``client_CountryOrRegion`를 채우는 데 사용 됩니다. 이 시점에서 IP 주소가 삭제 되 고 `0.0.0.0` `client_IP` 필드에 기록 됩니다.

* 브라우저 원격 분석: 보낸 사람의 IP 주소를 일시적으로 수집 합니다. IP 주소는 수집 엔드포인트에서 계산 됩니다.
* 서버 원격 분석: Application Insights 모듈은 클라이언트 IP 주소를 일시적으로 수집 합니다. `X-Forwarded-For`가 설정된 경우에는 수집되지 않습니다.

이 동작은 의도 하지 않은 개인 데이터 컬렉션을 방지 하기 위한 것입니다. 가능 하면 개인 데이터의 수집을 방지 하는 것이 좋습니다. 

## <a name="overriding-default-behavior"></a>기본 동작 재정의

기본 동작은 개인 데이터의 수집을 최소화 하는 것 이지만 여전히 IP 주소 데이터를 수집 하 고 저장할 수 있는 유연성을 제공 합니다. IP 주소와 같은 개인 데이터를 저장 하도록 선택 하기 전에 적용 되는 규정 준수 요구 사항 또는 규정을 위반 하지 않는지 확인 하는 것이 좋습니다. Application Insights에서 개인 데이터를 처리 하는 방법에 대 한 자세한 내용은 [개인 데이터에 대 한 지침](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)을 참조 하세요.

## <a name="storing-ip-address-data"></a>IP 주소 데이터 저장

IP 컬렉션과 저장소를 사용 하도록 설정 하려면 Application Insights 구성 요소의 `DisableIpMasking` 속성을 `true`으로 설정 해야 합니다. 이 속성은 Azure Resource Manager 템플릿을 통해 설정 하거나 REST API를 호출 하 여 설정할 수 있습니다. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>포털 

단일 Application Insights 리소스에 대 한 동작을 수정 해야 하는 경우 Azure Portal를 통해이를 수행 하는 가장 쉬운 방법입니다.  

1. Application Insights 리소스 > **설정** 으로 이동 하 > **템플릿 내보내기** 

    ![템플릿 내보내기](media/ip-collection/export-template.png)

2. **배포** 선택

    ![빨간색으로 강조 표시 된 배포 단추](media/ip-collection/deploy.png)

3. **템플릿 편집**을 선택 합니다. 템플릿에이 예제 템플릿에 표시 되지 않는 추가 속성이 나 리소스가 있는 경우에는 주의 해 서 모든 리소스에서 템플릿 배포를 증분 변경/업데이트로 수락 하는지 확인 합니다.

    ![템플릿 편집](media/ip-collection/edit-template.png)

4. 리소스에 대해 json을 다음과 같이 변경 하 고 **저장**을 클릭 합니다.

    !["IbizaAIExtension" 뒤에 쉼표를 추가 하 고 "DisableIpMasking": true를 사용 하 여 아래에 새 줄을 추가 합니다.](media/ip-collection/save.png)

    > [!WARNING]
    > 다음 오류가 발생 하는 경우:  **_리소스 그룹이 템플릿에 있는 하나 이상의 리소스에서 지원 되지 않는 위치에 있습니다. 다른 리소스 그룹을 선택 하세요._** 드롭다운에서 다른 리소스 그룹을 일시적으로 선택한 다음 원래 리소스 그룹을 다시 선택 하 여 오류를 해결 합니다.

5.  > **구매**에 **동의** 를 선택 합니다. 

    ![템플릿 편집](media/ip-collection/purchase.png)

    이 경우 새를 구매 하는 것이 아니라 기존 Application Insights 리소스의 구성을 업데이트 합니다.

6. 배포가 완료 되 면 새 원격 분석 데이터가 기록 됩니다.

    템플릿을 다시 선택 하 고 편집 하는 경우 기본 템플릿만 표시 되며 새로 추가 된 속성 및 관련 값이 표시 되지 않습니다. IP 주소 데이터가 표시 되지 않고 `"DisableIpMasking": true` 설정 되어 있는지 확인 하려는 경우 다음 PowerShell을 실행 합니다. (`Fabrikam-dev`를 적절 한 리소스 및 리소스 그룹 이름으로 바꿉니다.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    속성 목록이 결과로 반환 됩니다. 속성 중 하나가 `DisableIpMasking: true`를 읽어야 합니다. Azure Resource Manager를 사용 하 여 새 속성을 배포 하기 전에 PowerShell을 실행 하는 경우 속성이 존재 하지 않습니다.

### <a name="rest-api"></a>Rest API

동일한 수정 작업을 수행 하는 [REST API](https://docs.microsoft.com/rest/api/azure/) 페이로드는 다음과 같습니다.

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>원격 분석 이니셜라이저

전체 또는 일부 IP 주소를 기록 하는 `DisableIpMasking` 보다 유연 하 게 대체 해야 하는 경우 [원격 분석 이니셜라이저](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) 를 사용 하 여 ip 전체 또는 일부를 사용자 지정 필드에 복사할 수 있습니다. 

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> `ISupportProperties`에 액세스할 수 없는 경우 Application Insights SDK의 안정적인 최신 릴리스를 확인 하 여 실행 중인지 확인 합니다. `ISupportProperties`는 높은 카디널리티 값을 위한 것 이지만 `GlobalProperties`는 지역 이름, 환경 이름 등과 같은 낮은 카디널리티 값에 더 적합 합니다. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>ASP.NET에 대 한 원격 분석 이니셜라이저 사용

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core에 대 한 원격 분석 이니셜라이저 사용

ASP.NET와 동일한 ASP.NET Core 방식으로 원격 분석 이니셜라이저를 만들 수 있지만 이니셜라이저를 사용 하도록 설정 하려면 다음 예제를 참조에 사용 합니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>클라이언트 쪽 JavaScript

서버 쪽 Sdk와 달리 클라이언트 쪽 Javascript SDK는 IP 주소를 계산 하지 않습니다. 기본적으로 클라이언트 쪽 원격 분석에 대 한 IP 주소 계산은 원격 분석 도착 시 Azure의 수집 엔드포인트에서 수행 됩니다. 즉, 클라이언트 쪽 데이터를 프록시로 보낸 다음 수집 엔드포인트으로 전달 하는 경우 IP 주소 계산에는 클라이언트가 아닌 프록시의 IP 주소가 표시 될 수 있습니다. 프록시가 사용 되지 않는 경우에는 문제가 되지 않습니다.

클라이언트 쪽에서 직접 IP 주소를 계산 하려는 경우 사용자 지정 논리를 추가 하 여이 계산을 수행 하 고 결과를 사용 하 여 `ai.location.ip` 태그를 설정 해야 합니다. `ai.location.ip` 설정 되 면 수집 엔드포인트에서 IP 주소 계산을 수행 하지 않으며 제공 된 IP 주소를 사용 하 여 지역 조회를 수행 합니다. 이 시나리오에서 IP 주소는 기본적으로 0으로 제한 되어 있습니다. 

사용자 지정 논리에서 계산 된 전체 IP 주소를 유지 하려면 `ai.location.ip`에서 제공한 IP 주소 데이터를 별도의 사용자 지정 필드에 복사 하는 원격 분석 이니셜라이저를 사용할 수 있습니다. 그러나 타사 라이브러리 또는 고유한 사용자 지정 클라이언트 쪽 IP 컬렉션 논리를 사용 하지 않고 서버 쪽 Sdk와 달리 클라이언트 쪽 SDK는 IP를 계산 하지 않습니다.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>원격 분석 이니셜라이저의 결과 보기

그런 다음 사이트에 대 한 새 트래픽을 트리거하고 약 2-5 분 동안 대기 하 여 수집 시간을 확보 한 경우 Kusto 쿼리를 실행 하 여 IP 주소 수집이 작동 하는지 확인할 수 있습니다.

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

새로 수집 된 IP 주소는 `customDimensions_client-ip` 열에 표시 되어야 합니다. 기본 `client-ip` 열에는 구성 요소 수준에서 IP 주소 수집을 구성한 방법에 따라 4 개의 8 진수가 모두 0으로 표시 되거나 처음 3 개의 8 진수만 표시 됩니다. 원격 분석 이니셜라이저를 구현한 후 로컬로 테스트 하 고 `customDimensions_client-ip`에 대해 표시 되는 값이 `::1` 되는 경우이는 예상 된 동작입니다. `::1`는 i p v 6의 루프백 주소를 나타냅니다. 이는 i p v 4에서 `127.0.01` 하는 것과 동일 하며 localhost에서 테스트 하는 경우에 표시 되는 결과입니다.

## <a name="next-steps"></a>다음 단계

* Application Insights의 [개인 데이터 수집](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) 에 대해 자세히 알아보세요.

* Application Insights에서 [IP 주소 수집이](https://apmtips.com/blog/2016/07/05/client-ip-address/) 작동 하는 방식에 대해 자세히 알아보세요. (이는 엔지니어 중 한 명에 의해 작성 된 이전 외부 블로그 게시물입니다. IP 주소가 `0.0.0.0`으로 기록 되는 현재 기본 동작을 이전의 기본 제공 `ClientIpHeaderTelemetryInitializer`의 메커니즘에 더 높은 수준으로 이동 합니다.
