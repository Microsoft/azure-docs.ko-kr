---
title: 공용 기본 Load Balancer에 대한 작업, 이벤트 및 카운터 모니터링
titlesuffix: Azure Load Balancer
description: 공용 기본 Load Balancer에 대한 경고 이벤트 및 상태 프로브 상태 로깅을 사용하도록 설정하는 방법에 대해 알아보기
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: 0d7c792c5230a5d82e97f4598a5dcfb864cead74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861172"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>공용 기본 부하 분산 장치에 대 한 azure Monitor 로그

>[!IMPORTANT] 
>Azure Load Balancer는 다음의 두 가지 형식을 지원합니다. 기본 및 표준 이 문서는 기본 Load Balancer에 대해 설명합니다. 표준 Load Balancer에 대한 자세한 내용은 Azure Monitor에서 다차원 메트릭을 통해 원격 분석을 노출하는 [표준 Load Balancer 개요](load-balancer-standard-overview.md)를 참조하세요.

Azure에서 기본 Load Balancer를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며, Azure Blob Storage에서 모든 로그를 추출하고 다양한 도구(예: Excel 및 PowerBI)에서 볼 수 있습니다. 아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **감사 로그:** [Azure 감사 로그](../monitoring-and-diagnostics/insights-debugging-with-events.md)(이전의 작업 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 볼 수 있습니다. 감사 로그는 기본적으로 사용하도록 설정되며 Azure Portal에서 볼 수 있습니다.
* **경고 이벤트 로그:** 이 로그를 사용하여 부하 분산 장치에서 발생한 경고를 볼 수 있습니다. 부하 분산 장치에 대한 상태는 5분 마다 수집됩니다. 이 로그는 부하 분산 장치 경고 이벤트가 발생하는 경우에만 기록됩니다.
* **상태 프로브 로그:** 상태 프로브 오류 때문에 부하 분산 장치에서 요청을 받지 않는 백 엔드 풀에 있는 인스턴스의 수와 같은 상태 프로브에서 발견한 문제를 보기 위해 이 로그를 사용할 수 있습니다. 상태 프로브 상태가 변경되는 경우에 이 로그가 기록됩니다.

> [!IMPORTANT]
> Azure Monitor 로그 현재 공용 기본 부하 분산 장치에 대해서만 작동 합니다. 로그는 Resource Manager 배포 모델에 배포된 리소스에서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/resource-manager-deployment-model.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

감사 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 및 상태 프로브 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

[Azure 포털](https://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](load-balancer-get-started-internet-arm-ps.md) .

1. 포털에서 **찾아보기**를 클릭합니다.
2. **부하 분산 장치**를 선택합니다.

    ![포털 - 부하 분산 장치](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. 기존 부하 분산 장치 >> **모든 설정**을 선택합니다.
4. 대화 상자 오른쪽의 부하 분산 장치 이름 아래에서 **모니터링**으로 스크롤하여 **진단**을 클릭합니다.

    ![포털 - 부하 분산 장치-설정](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. **진단** 창의 **상태** 아래에서 **설정**을 선택합니다.
6. **Storage 계정**을 클릭합니다.
7. **로그** 아래에서 기존 저장소 계정을 선택하거나 새 저장소 계정을 만듭니다. 슬라이더를 사용하여 이벤트 로그에 저장된 이벤트 데이터를 유지할 날짜 수를 결정합니다. 
8. **저장**을 클릭합니다.

진단은 지정된 스토리지 계정의 Table Storage에 저장됩니다. 로그가 저장되지 않는 경우 이는 관련 로그가 생성되지 않기 때문입니다.

![포털 - 진단 로그](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> 감사 로그에는 별도의 스토리지 계정이 필요하지 않습니다. 이벤트 및 상태 프로브 로깅에 대한 저장소를 사용할 경우 서비스 요금이 부과됩니다.

## <a name="audit-log"></a>감사 로그

감사 로그는 기본적으로 생성됩니다. 이 로그는 Azure의 이벤트 로그 저장소에 90일 동안 유지됩니다. [이벤트 및 감사 로그 보기](../monitoring-and-diagnostics/insights-debugging-with-events.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

## <a name="alert-event-log"></a>경고 이벤트 로그

이 로그는 부하 분산 장치별로 설정한 경우에만 생성됩니다. 이벤트는 JSON 형식으로 로깅되며, 로깅을 사용하도록 설정할 때 지정한 저장소 계정에 저장됩니다. 다음은 이벤트의 예입니다.

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

JSON 출력은 경고가 생성된 부하 분산 장치에 대한 이유를 설명하는 *eventname* 속성을 표시합니다. 이 경우 경고는 원본 IP NAT 제한(SNAT)으로 인해 발생된 TCP 포트 소모로 인해 발생되었습니다.

## <a name="health-probe-log"></a>상태 프로브 로그

이 로그는 위에서 설명한 대로 부하 분산 장치별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 'insights-logs-loadbalancerprobehealthstatus'라는 컨테이너가 생성되고 다음 데이터가 기록됩니다.

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

속성 필드의 JSON 출력은 상태 프로브 상태에 대한 기본 정보를 보여 줍니다. *dipDownCount* 속성은 실패한 프로브 응답으로 인해 네트워크 트래픽을 수신하지 않은 백 엔드의 총 인스턴스 수를 보여 줍니다.

## <a name="view-and-analyze-the-audit-log"></a>감사 로그 보기 및 분석

다음 방법을 사용하여 감사 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, Azure CLI(명령줄 인터페이스), Azure REST API 또는 Azure 미리 보기 포털을 통해 감사 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../azure-resource-manager/resource-group-audit.md) 문서에 자세히 나와 있습니다.
* **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)을 사용하여 미리 구성된 대시보드에서 데이터를 분석하거나 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>상태 프로브 및 이벤트 로그 보기 및 분석

저장소 계정에 연결하고 이벤트 및 상태 프로브 로그에 대한 JSON 로그 항목을 검색해야 합니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, PowerBI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio 및 C# 상수와 변수의 값 변경에 대한 기본 개념을 잘 알고 있으면 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter) 를 사용할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [Power BI를 사용하여 Azure 감사 로그 시각화](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물.
* [Power BI 등에서 Azure 감사 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물.

## <a name="next-steps"></a>다음 단계

[부하 분산 장치 프로브 이해](load-balancer-custom-probe-overview.md)
