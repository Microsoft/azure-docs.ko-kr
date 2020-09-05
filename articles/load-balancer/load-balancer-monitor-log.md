---
title: 공용 기본 Load Balancer에 대한 작업, 이벤트 및 카운터 모니터링
titleSuffix: Azure Load Balancer
description: 공용 기본 Load Balancer에 대한 경고 이벤트 및 상태 프로브 상태 로깅을 사용하도록 설정하는 방법에 대해 알아보기
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 42ec5a661bd7b42ba5de5bfa99b3898291cc60fa
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935605"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>공용 기본 Load Balancer용 Azure Monitor 로그

Azure에서 기본 Load Balancer를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며, 로그는 이벤트 허브 또는 Log Analytics 작업 영역으로 스트리밍할 수 있습니다. Azure blob storage에서 모든 로그를 추출 하 고 Excel 및 Power BI와 같은 다른 도구에서 볼 수 있습니다.  아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **활동 로그:** [활동 로그 보기를 사용 하 여 리소스에 대 한 작업을 모니터링](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) 하 여 Azure 구독에 제출 되는 모든 활동 및 해당 상태를 볼 수 있습니다. 활동 로그는 기본적으로 사용 하도록 설정 되며 Azure Portal에서 볼 수 있습니다.
* **경고 이벤트 로그:** 이 로그를 사용하여 부하 분산 장치에서 발생한 경고를 볼 수 있습니다. 부하 분산 장치에 대한 상태는 5분 마다 수집됩니다. 이 로그는 부하 분산 장치 경고 이벤트가 발생하는 경우에만 기록됩니다.
* **상태 프로브 로그:** 상태 프로브 오류 때문에 부하 분산 장치에서 요청을 받지 않는 백 엔드 풀에 있는 인스턴스의 수와 같은 상태 프로브에서 발견한 문제를 보기 위해 이 로그를 사용할 수 있습니다. 상태 프로브 상태가 변경되는 경우에 이 로그가 기록됩니다.

> [!IMPORTANT]
> **상태 프로브 이벤트 로그는 현재 작동 하지 않으며 [Azure Load Balancer에 대 한 알려진 문제](whats-new.md#known-issues)에 나열 됩니다.** 로그는 Resource Manager 배포 모델에 배포된 리소스에서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이벤트 및 상태 프로브 로깅을 사용 하 여 해당 로그를 통해 사용 가능한 데이터 수집을 시작 합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) .

1. 포털에서 **리소스 그룹**을 클릭 합니다.
2. **\<resource-group-name>** 부하 분산 장치가 있는 위치를 선택 합니다.
3. 부하 분산 장치를 선택합니다.
4. **활동 로그**  >  **진단 설정**을 선택 합니다.
5. **진단 설정** 창의 **진단 설정**에서 **+ 진단 설정 추가**를 선택 합니다.
6. **진단 설정** 만들기 창에서 **이름** 필드에 **myLBDiagnostics** 을 입력 합니다.
7. **진단 설정**에 대 한 세 가지 옵션이 있습니다.  하나, 둘 또는 세 가지를 모두 선택 하 고 요구 사항에 맞게 구성할 수 있습니다.
   * **스토리지 계정에 보관**
   * **이벤트 허브로 스트림**
   * **Log Analytics에 보내기**

    ### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관
    이 프로세스에 대해 이미 만들어진 저장소 계정이 필요 합니다.  저장소 계정을 만들려면 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 를 참조 하세요.

    1. **저장소 계정에 보관**옆의 확인란을 선택 합니다.
    2. **구성** 을 선택 하 여 **저장소 계정 선택** 창을 엽니다.
    3. 풀 다운 상자에서 저장소 계정이 만들어진 **구독** 을 선택 합니다.
    4. 풀 다운 상자 **에서 저장소 계정 아래에** 있는 저장소 계정의 이름을 선택 합니다.
    5. 확인을 선택합니다.

    ### <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
    이 프로세스에 대 한 이벤트 허브가 이미 생성 되어 있어야 합니다.  이벤트 허브를 만들려면 [빠른 시작: Azure Portal 사용 하 여 이벤트 허브 만들기](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) 를 참조 하세요.

    1. **이벤트 허브로 스트림** 옆의 확인란을 선택 합니다.
    2. **구성** 을 선택 하 여 **이벤트 허브 선택** 창을 엽니다.
    3. 풀 다운 상자에서 이벤트 허브가 만들어진 **구독** 을 선택 합니다.
    4. 풀 다운 상자에서 **이벤트 허브 네임 스페이스를 선택** 합니다.
    5. 풀 다운 상자에서 **이벤트 허브 정책 이름을 선택** 합니다.
    6. 확인을 선택합니다.

    ### <a name="send-to-log-analytics"></a>Log Analytics에 보내기
    이 프로세스에 대 한 log analytics 작업 영역을 만들고 구성 해야 합니다.  Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 를 참조 하세요.

    1. **Log Analytics 보내기**옆의 확인란을 선택 합니다.
    2. 풀 다운 상자에서 Log Analytics 작업 영역이 있는 **구독** 을 선택 합니다.
    3. 풀 다운 상자에서 **Log Analytics 작업 영역** 을 선택 합니다.


8. **진단 설정** 창의 **로그** 섹션 아래에서 둘 다 옆의 확인란을 선택 합니다.
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  **진단 설정** 창의 **메트릭** 섹션 아래에서 옆의 확인란을 선택 합니다.
   * **AllMetrics**

11. 모든 항목이 올바른지 확인 하 고 **진단 설정** 만들기 창의 맨 위에서 **저장** 을 클릭 합니다.

## <a name="activity-log"></a>활동 로그

활동 로그는 기본적으로 생성 됩니다. 이 로그는 Azure의 이벤트 로그 저장소에 90일 동안 유지됩니다. [작업 로그 보기에서 리소스에 대 한 작업 모니터링](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

## <a name="archive-to-storage-account-logs"></a>저장소 계정 로그에 보관

### <a name="alert-event-log"></a>경고 이벤트 로그

이 로그는 부하 분산 장치별로 설정한 경우에만 생성됩니다. 이벤트는 JSON 형식으로 로깅되며, 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. 다음은 이벤트의 예입니다.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON 출력은 부하 분산 장치에서 경고를 만든 이유를 설명 하는 *eventname* 속성을 보여 줍니다. 이 경우 생성 된 경고는 원본 IP NAT 제한 (SNAT)으로 인해 발생 하는 TCP 포트 고갈 때문에 발생 했습니다.

### <a name="health-probe-log"></a>상태 프로브 로그

이 로그는 위에서 설명한 대로 부하 분산 장치별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 스토리지 계정에 저장됩니다. 'insights-logs-loadbalancerprobehealthstatus'라는 컨테이너가 생성되고 다음 데이터가 기록됩니다.

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

속성 필드의 JSON 출력은 상태 프로브 상태에 대한 기본 정보를 보여 줍니다. *Dipdowncount* 속성은 실패 한 프로브 응답으로 인해 네트워크 트래픽을 수신 하지 않는 백 엔드의 총 인스턴스 수를 표시 합니다.

### <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법을 사용하여 활동 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, Azure CLI (명령줄 인터페이스), Azure REST API 또는 Azure Portal를 통해 활동 로그에서 정보를 검색 합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md) 문서에 자세히 나와 있습니다.
* **Power BI:**[Power BI](https:// .microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)을 사용하여 미리 구성된 대시보드에서 데이터를 분석하거나 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>상태 프로브 및 이벤트 로그 보기 및 분석

저장소 계정에 연결 하 고 이벤트 및 상태 프로브 로그에 대 한 JSON 로그 항목을 검색 합니다. JSON 파일을 다운로드 한 후에는 CSV로 변환 하 고 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
진단 정보는 이벤트 허브로 스트리밍되는 경우 타사 SIEM 도구에서 Azure Monitor 통합을 통해 중앙 집중식 로그 분석에 사용할 수 있습니다. 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration) 을 참조 하세요.

## <a name="send-to-log-analytics"></a>Log Analytics에 보내기
Azure의 리소스에는 문제 해결 및 분석을 위한 정보에 대해 복잡 한 쿼리를 실행할 수 있는 Log Analytics 작업 영역으로 직접 전송 되는 진단 정보가 있을 수 있습니다.  자세한 내용은 [Log Analytics 작업 영역에서 Azure 리소스 로그 수집](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace) 을 참조 하세요 Azure Monitor

## <a name="next-steps"></a>다음 단계

[부하 분산 장치 프로브 이해](load-balancer-custom-probe-overview.md)
