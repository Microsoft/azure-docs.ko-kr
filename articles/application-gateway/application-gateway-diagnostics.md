---
title: 백 엔드 상태 및 진단 로그
titleSuffix: Azure Application Gateway
description: Azure Application Gateway에 대한 액세스 및 성능 로그를 사용하고 관리하는 방법을 알아봅니다
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: 1ddbc8e909c5ba0b720e893e87c0f495d256a886
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966920"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Application Gateway에 대한 백 엔드 상태 및 진단 로그

다음과 같은 방법으로 Azure 애플리케이션 게이트웨이 리소스를 모니터링할 수 있습니다.

* [백 엔드 상태](#back-end-health) - Application Gateway는 Azure Portal과 PowerShell을 통해 백 엔드 풀에 있는 서버의 상태를 모니터링하는 기능을 제공합니다. 또한 성능 진단 로그를 통해 백 엔드 풀의 상태도 확인할 수 있습니다.

* [로그](#diagnostic-logging) - 로그를 사용하면 모니터링하기 위해 리소스에서 성능, 액세스 및 기타 데이터를 저장하거나 사용할 수 있습니다.

* [메트릭](application-gateway-metrics.md): Application Gateway에는 시스템이 예상 대로 수행 되는지 확인 하는 데 도움이 되는 몇 가지 메트릭이 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>백 엔드 상태

Application Gateway는 포털, PowerShell 및 CLI(명령줄 인터페이스)를 통해 백 엔드 풀의 개별 멤버에 대한 상태를 모니터링하는 기능을 제공합니다. 또한 성능 진단 로그를 통해 백 엔드 풀에 대해 집계된 상태 요약도 확인할 수 있습니다.

백 엔드 상태 보고서는 백 엔드 인스턴스에 대한 Application Gateway 상태 프로브의 결과를 반영합니다. 프로브가 성공하고 백 엔드에서 트래픽을 받을 수 있으면 정상 상태로 간주됩니다. 그렇지 않으면 비정상 상태로 간주됩니다.

> [!IMPORTANT]
> Application Gateway 서브넷에 NSG (네트워크 보안 그룹)가 있는 경우 v1 Sku에 대한 포트 범위 65503-65534 및 인바운드 트래픽에 대한 Application Gateway 서브넷의 v2 Sku (65200-65535)를 엽니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. Azure 인증서에 의해 보호(잠김)됩니다. 적절한 인증서가 없는 경우 해당 게이트웨이 고객을 포함하여 외부 엔터티는 해당 엔드포인트에서 변경을 시작할 수 없습니다.


### <a name="view-back-end-health-through-the-portal"></a>포털을 통해 백 엔드 상태 보기

포털에서는 백 엔드 상태를 자동으로 제공합니다. 기존 애플리케이션 게이트웨이에서 **모니터링** > **백 엔드 상태**를 차례로 선택합니다.

NIC, IP 또는 FQDN과 관계 없이 백 엔드 풀의 각 멤버가 이 페이지에 나열됩니다. 백 엔드 풀 이름, 포트, 백 엔드 HTTP 설정 이름 및 상태도 표시됩니다. 상태의 유효한 값은 **정상**, **비정상** 및 **알 수 없음**입니다.

> [!NOTE]
> 백 엔드 상태가 **알 수 없음**이면 백 엔드에 대한 액세스가 가상 네트워크의 NSG 규칙, UDR(사용자 정의 경로) 또는 사용자 지정 DNS에 의해 차단되지 않도록 합니다 .

![백 엔드 상태][10]

### <a name="view-back-end-health-through-powershell"></a>PowerShell을 통해 백 엔드 상태 보기

다음 PowerShell 코드에서는 `Get-AzApplicationGatewayBackendHealth` cmdlet을 사용하여 백 엔드 상태를 확인하는 방법을 보여 줍니다.

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Azure CLI를 통해 백 엔드 상태 보기

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>결과

다음은 응답의 예제를 보여 주는 코드 조각입니다.

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>진단 로그

Azure에서 다양한 유형의 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. Azure Blob Storage에서 모든 로그를 추출하여 다양한 도구(예: [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md),Excel 및 Power BI)에서 볼 수 있습니다. 다음 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **활동 로그** - [Azure 활동 로그](../monitoring-and-diagnostics/insights-debugging-with-events.md)(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 확인할 수 있습니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.
* **액세스 로그**:이 로그를 사용 하 여 Application Gateway 액세스 패턴을 보고 중요 한 정보를 분석할 수 있습니다. 여기에는 호출자의 IP, 요청 된 URL, 응답 대기 시간, 반환 코드 및 바이트 (in/out)가 포함 됩니다. 액세스 로그는 300 초 마다 수집 됩니다. 이 로그에는 Application Gateway 인스턴스당 하나의 레코드가 포함됩니다. Application Gateway 인스턴스는 instanceId 속성으로 식별됩니다.
* **성능 로그** - 이 로그를 사용하여 Application Gateway 인스턴스를 수행하는 방법을 확인할 수 있습니다. 이 로그는 인스턴스 단위로 처리된 총 요청 수, 처리량(바이트), 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다. 성능 로그는 60초마다 수집됩니다. 성능 로그는 v1 SKU에 대해서만 사용할 수 있습니다. V2 SKU의 경우 성능 데이터에 대한 [메트릭을](application-gateway-metrics.md) 사용 합니다.
* **방화벽 로그** - 이 로그를 사용하면 웹 애플리케이션 방화벽으로 구성된 애플리케이션 게이트웨이의 검색 모드 또는 방지 모드를 통해 로깅된 요청을 확인할 수 있습니다.

> [!NOTE]
> 로그는 Azure Resource Manager 배포 모델에 배포된 리소스에만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델에 대해 더 잘 이해하려면 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/management/deployment-models.md) 문서를 참조하세요.

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **Event hubs**: event hubs는 다른 siem (보안 정보 및 이벤트 관리) 도구와 통합 하 여 리소스에 대한 경고를 얻는 데 유용한 옵션입니다.
* **Azure Monitor 로그**: Azure Monitor 로그는 응용 프로그램의 일반적인 실시간 모니터링 또는 추세를 확인 하는 데 가장 적합 합니다.

### <a name="enable-logging-through-powershell"></a>PowerShell을 통한 로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 액세스 및 성능 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

1. 로그 데이터를 저장할 스토리지 계정의 리소스 ID를 적어 둡니다. 이 값의 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Storage/storageAccounts/\<스토리지 계정 이름\>입니다. 구독의 모든 스토리지 계정을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다.

    ![포털: 스토리지 계정의 리소스 ID](./media/application-gateway-diagnostics/diagnostics1.png)

2. 로깅을 사용할 Application Gateway의 리소스 ID를 적어 둡니다. 이 값은 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Network/applicationGateways/\<응용 프로그램 게이트웨이 이름\>입니다. 포털을 사용하여 이 정보를 찾을 수 있습니다.

    ![포털: 애플리케이션 게이트웨이의 리소스 ID](./media/application-gateway-diagnostics/diagnostics2.png)

3. 다음 PowerShell cmdlet을 사용하여 진단 로깅을 사용하도록 설정합니다.

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>활동 로그에는 별도의 스토리지 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 스토리지를 사용할 경우 서비스 요금이 부과됩니다.

### <a name="enable-logging-through-the-azure-portal"></a>Azure Portal을 통한 로깅 사용

1. Azure Portal에서 리소스를 찾고 **진단 설정**을 선택 합니다.

   Application Gateway의 경우 다음 세 가지 로그를 사용할 수 있습니다.

   * 액세스 로그
   * 성능 로그
   * 방화벽 로그

2. 데이터 수집을 시작 하려면 **진단 켜기**를 선택 합니다.

   ![진단 켜기][1]

3. **진단 설정** 페이지에서는 진단 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. 또한 이벤트 허브 및 스토리지 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

   ![구성 프로세스 시작][2]

5. 설정의 이름을 입력 하 고, 설정을 확인 하 고, **저장**을 선택 합니다.

### <a name="activity-log"></a>활동 로그

Azure에서는 기본적으로 활동 로그를 생성합니다. 이러한 로그는 Azure 이벤트 로그 저장소에 90일 동안 유지됩니다. [이벤트 및 활동 로그 보기](../monitoring-and-diagnostics/insights-debugging-with-events.md) 문서를 참조하여 이러한 로그에 대해 자세히 알아보세요.

### <a name="access-log"></a>액세스 로그

이전 단계에서 설명한 대로 액세스 로그는 각 Application Gateway 인스턴스에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. Application Gateway에 대한 각 액세스는 v1에 대한 다음 예제와 같이 JSON 형식으로 기록 됩니다.

|값  |Description  |
|---------|---------|
|instanceId     | 요청을 처리한 Application Gateway 인스턴스        |
|clientIP     | 요청에 대한 원래 IP        |
|clientPort     | 요청에 대한 원래 포트       |
|httpMethod     | 요청에서 사용된 HTTP 메서드       |
|requestUri     | 받은 요청의 URI        |
|RequestQuery     | **Server-Routed**: 요청을 보낸 백 엔드 풀 인스턴스입니다.</br>**X-AzureApplicationGateway-LOG-ID**: 요청에 사용된 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. </br>**SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드       |
|UserAgent     | HTTP 요청 헤더의 사용자 에이전트        |
|httpStatus     | Application Gateway에서 클라이언트로 반환한 HTTP 상태 코드       |
|httpVersion     | 요청의 HTTP 버전        |
|receivedBytes     | 받은 패킷의 크기(바이트)        |
|sentBytes| 보낸 패킷의 크기(바이트)|
|timeTaken| 요청을 처리하고 응답을 보내는 데 걸리는 시간(밀리초)입니다. 이 값은 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 받은 시점부터 응답 보내기 작업을 완료하는 시점까지의 간격으로 계산됩니다. 걸린 시간(Time-Taken) 필드에는 대개 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간이 포함됩니다. |
|sslEnabled| 백 엔드 풀에 대한 통신에서 SSL이 사용되었는지 여부입니다. 유효한 값은 on과 off입니다.|
|host| 요청이 백 엔드 서버로 전송 된 호스트 이름입니다. 백 엔드 호스트 이름이 재정의 되는 경우이 이름에이 반영 됩니다.|
|originalHost| 클라이언트에서 Application Gateway 요청을 수신 하는 데 사용 된 호스트 이름입니다.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Application Gateway 및 WAF v 2의 경우 로그에 약간의 추가 정보가 표시 됩니다.

|값  |Description  |
|---------|---------|
|instanceId     | 요청을 처리한 Application Gateway 인스턴스        |
|clientIP     | 요청에 대한 원래 IP        |
|clientPort     | 요청에 대한 원래 포트       |
|httpMethod     | 요청에서 사용된 HTTP 메서드       |
|requestUri     | 받은 요청의 URI        |
|UserAgent     | HTTP 요청 헤더의 사용자 에이전트        |
|httpStatus     | Application Gateway에서 클라이언트로 반환한 HTTP 상태 코드       |
|httpVersion     | 요청의 HTTP 버전        |
|receivedBytes     | 받은 패킷의 크기(바이트)        |
|sentBytes| 보낸 패킷의 크기(바이트)|
|timeTaken| 요청을 처리 하 고 응답을 보내는 데 걸리는 시간 ( **초**)입니다. 이 값은 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 받은 시점부터 응답 보내기 작업을 완료하는 시점까지의 간격으로 계산됩니다. 걸린 시간(Time-Taken) 필드에는 대개 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간이 포함됩니다. |
|sslEnabled| 백 엔드 풀에 대한 통신에서 SSL이 사용되었는지 여부입니다. 유효한 값은 on과 off입니다.|
|sslCipher| Ssl 통신에 사용 되는 암호 그룹입니다 (SSL을 사용 하는 경우).|
|sslProtocol| SSL/TLS 프로토콜을 사용 하 고 있습니다 (SSL을 사용 하는 경우).|
|serverRouted| Application gateway에서 요청을 라우팅하는 백 엔드 서버입니다.|
|serverStatus| 백 엔드 서버의 HTTP 상태 코드입니다.|
|serverResponseLatency| 백 엔드 서버의 응답 대기 시간입니다.|
|host| 요청의 호스트 헤더에 나열 된 주소입니다.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>성능 로그

이전 단계에서 설명한 대로 성능 로그는 각 Application Gateway 인스턴스에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. 성능 로그 데이터는 1분 간격으로 생성됩니다. V1 SKU에 대해서만 사용할 수 있습니다. V2 SKU의 경우 성능 데이터에 대한 [메트릭을](application-gateway-metrics.md) 사용 합니다. 다음 데이터가 로깅됩니다.


|값  |Description  |
|---------|---------|
|instanceId     |  성능 데이터가 생성되는 Application Gateway 인스턴스입니다. 다중 인스턴스 애플리케이션 게이트웨이의 경우 인스턴스마다 하나의 행이 있습니다.        |
|healthyHostCount     | 백 엔드 풀의 정상 호스트 수        |
|unHealthyHostCount     | 백 엔드 풀의 비정상 호스트 수        |
|requestCount     | 처리된 요청 수        |
|latency | 인스턴스와 요청을 처리하는 백 엔드 사이의 평균 요청 대기 시간(밀리초)입니다. |
|failedRequestCount| 실패한 요청 수|
|throughput| 마지막 로그 이후의 평균 처리량(초당 바이트 수로 측정됨)|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 대기 시간은 HTTP 요청의 첫 번째 바이트를 받은 시간부터 HTTP 응답의 마지막 바이트를 보낸 시간까지 계산됩니다. 즉 Application Gateway 처리 시간, 백 엔드로의 네트워크 사용 시간 및 백 엔드에서 요청을 처리하는 데 걸린 시간의 합계입니다.

### <a name="firewall-log"></a>방화벽 로그

이전 단계에서 설명한 대로 방화벽 로그는 각 애플리케이션 게이트웨이에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 또한 이 로그를 사용하려면 애플리케이션 게이트웨이에서 웹 애플리케이션 방화벽을 구성해야 합니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. 다음 데이터가 로깅됩니다.


|값  |Description  |
|---------|---------|
|instanceId     | 방화벽 데이터가 생성되는 Application Gateway 인스턴스입니다. 다중 인스턴스 애플리케이션 게이트웨이의 경우 인스턴스마다 하나의 행이 있습니다.         |
|clientIp     |   요청에 대한 원래 IP      |
|clientPort     |  요청에 대한 원래 포트       |
|requestUri     | 받은 요청의 URL       |
|ruleSetType     | 규칙 집합 유형이며, 사용 가능한 값은 OWASP입니다.        |
|ruleSetVersion     | 사용된 규칙 집합 버전이며, 사용 가능한 값은 2.2.9 및 3.0입니다.     |
|ruleId     | 트리거 이벤트의 규칙 ID        |
|message     | 사용자에게 친숙한 트리거 이벤트에 대한 메시지이며, 자세한 내용은 세부 정보 섹션에서 제공됩니다.        |
|action     |  요청에서 수행되는 동작이며, 사용 가능한 값은 일치 하 고 차단 됩니다.      |
|site     | 로그를 생성한 사이트이며, 현재 규칙이 전역이므로 Global만 나열됩니다.|
|자세히     | 트리거 이벤트의 세부 정보        |
|details.message     | 규칙에 대한 설명        |
|details.data     | 규칙과 일치하는 요청 내 특정 데이터         |
|details.file     | 규칙이 포함된 구성 파일        |
|details.line     | 이벤트를 트리거한 구성 파일의 줄 번호       |
|hostname   | Application Gateway의 호스트 이름 또는 IP 주소입니다.    |
|transactionId  | 동일한 요청 내에서 발생 한 여러 규칙 위반을 그룹화 하는 데 도움이 되는 지정 된 트랜잭션에 대한 고유 ID입니다.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100",
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [Resource Manager의 활동 작업](../azure-resource-manager/management/view-activity-logs.md) 문서에 자세히 나와 있습니다.
* **Power BI** - [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우 무료로 사용해볼 수 있습니다. [Power BI 템플릿 앱](https://docs.microsoft.com/power-bi/service-template-apps-overview)을 사용 하 여 데이터를 분석할 수 있습니다.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>액세스, 성능 및 방화벽 로그 보기 및 분석

[Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)는 Blob Storage 계정에서 카운터 및 이벤트 로그 파일을 수집할 수 있습니다. 여기에는 로그를 분석하는 시각화 및 강력한 검색 기능이 포함되어 있습니다.

스토리지 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>GoAccess를 통해 액세스 로그 분석

Application Gateway 액세스 로그에 대해 널리 사용되는 [GoAccess](https://goaccess.io/) 로그 분석기를 설치하고 실행하는 Resource Manager 템플릿을 게시했습니다. GoAccess는 고유 방문자, 요청한 파일, 호스트, 운영 체제, 브라우저, HTTP 상태 코드 및 기타 유용한 HTTP 트래픽 통계를 제공 합니다. 자세한 내용은 [GitHub의 Resource Manager 템플릿 폴더에 대한 추가 정보 파일](https://aka.ms/appgwgoaccessreadme)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그를 시각화합니다.
* [Power BI를 사용하여 Azure 활동 로그 시각화](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물
* [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
