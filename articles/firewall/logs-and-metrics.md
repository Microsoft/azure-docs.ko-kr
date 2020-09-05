---
title: Azure 방화벽 로그 및 메트릭 개요
description: 방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: 51804a9f98bfa17dcfbeb90a268b91b2d28dbbde
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827225"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall 로그 및 메트릭

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그는 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md), 저장소 및 Event Hubs 전송 되며 Azure Monitor 로그 나 Excel 및 Power BI 같은 다른 도구에 의해 분석 될 수 있습니다.

메트릭은 간단한 실시간 시나리오를 지원 하 고 경고 및 빠른 문제 검색에 유용 하 게 사용할 수 있습니다.

## <a name="diagnostic-logs"></a>진단 로그

 다음 진단 로그는 Azure Firewall에 사용할 수 있습니다.

* **애플리케이션 규칙 로그**

   응용 프로그램 규칙 로그는 저장소 계정에 저장 되 고, 이벤트 허브로 스트리밍 되며, 각 Azure 방화벽에 대해 사용 하도록 설정한 경우에만 Azure Monitor 로그에 전송 됩니다. 허용/거부된 연결에 대한 로그에서 구성된 애플리케이션 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **네트워크 규칙 로그**

   네트워크 규칙 로그는 저장소 계정에 저장 되 고, 이벤트 허브로 스트리밍 되며, 각 Azure 방화벽에 대해 사용 하도록 설정한 경우에만 Azure Monitor 로그에 전송 됩니다. 허용/거부된 연결에 대한 로그에서 구성된 네트워크 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **이벤트 허브** - 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.
* **Azure Monitor 로그**: Azure Monitor 로그는 응용 프로그램의 일반적인 실시간 모니터링 또는 추세를 확인 하는 데 가장 적합 합니다.

## <a name="activity-logs"></a>활동 로그

   활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.

   [Azure 활동 로그](../azure-resource-manager/management/view-activity-logs.md) (이전의 작업 로그 및 감사 로그)를 사용 하 여 azure 구독에 제출 된 모든 작업을 볼 수 있습니다.

## <a name="metrics"></a>메트릭

Azure Monitor 메트릭은 특정 시간에 시스템의 일부 측면을 설명 하는 숫자 값입니다. 메트릭은 1 분 마다 수집 되며 자주 샘플링 될 수 있으므로 경고에 유용 합니다. 비교적 간단한 논리를 사용 하 여 경고를 신속 하 게 발생 시킬 수 있습니다.

Azure 방화벽에 사용할 수 있는 메트릭은 다음과 같습니다.

- **응용 프로그램 규칙 적중 횟수** -응용 프로그램 규칙이 적중 된 횟수입니다.

    단위: 개수

- **네트워크 규칙 적중 횟수** -네트워크 규칙이 적중 된 횟수입니다.

    단위: 개수

- **처리 된 데이터** -지정 된 기간에 방화벽을 통과 하는 데이터의 합계입니다.

    단위: 바이트

- **처리량** -초당 방화벽을 통과 하는 데이터의 속도입니다.

    단위: 비트/초

- **방화벽 상태** -SNAT 포트 가용성을 기반으로 하는 방화벽의 상태를 나타냅니다.

    단위:%

   이 메트릭에는 두 개의 차원이 있습니다.
  - 상태: 가능한 값은 *정상*, *저하*됨, *비정상*입니다.
  - 이유: 방화벽의 해당 상태에 대 한 이유를 나타냅니다. 

     SNAT 포트를 95% > 사용 하는 경우 사용 중인 것으로 간주 되 고 상태는 50% 상태 =**저하** 됨 및 이유 =**SNAT 포트**입니다. 방화벽은 처리 트래픽을 유지하고 기존 연결은 영향을 받지 않습니다. 그러나 새 연결이 간헐적으로 설정되지 않을 수 있습니다.

     SNAT 포트를 95% < 사용 하는 경우 방화벽은 정상으로 간주 되 고 상태는 100%로 표시 됩니다.

     SNAT 포트 사용량이 보고되지 않으면 상태가 0%로 표시됩니다. 

- **Snat 포트 사용률** -방화벽에서 사용한 SNAT 포트의 백분율입니다.

    단위:%

   방화벽에 공용 IP 주소를 더 추가하는 경우 SNAT 포트 사용률을 줄여 주는 더 많은 SNAT 포트를 사용할 수 있습니다. 또한 방화벽이 CPU 또는 처리량과 같은 다양한 이유로 스케일 아웃될 경우 추가 SNAT 포트도 사용할 수 있게 됩니다. 즉, 서비스가 확장 되기 때문에 공용 IP 주소를 추가 하지 않고도 SNAT 포트 사용률의 지정 된 비율이 중단 될 수 있습니다. 사용 가능한 공용 IP 주소 수를 직접 제어 하 여 방화벽에서 사용할 수 있는 포트를 늘릴 수 있습니다. 그러나 방화벽 크기 조정을 직접 제어할 수는 없습니다.


## <a name="next-steps"></a>다음 단계

- Azure Firewall 로그 및 메트릭을 모니터링하는 방법은 [자습서: Azure Firewall 로그 모니터링](tutorial-diagnostics.md)을 참조하세요.

- Azure Monitor의 메트릭에 대 한 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/platform/data-platform-metrics.md)을 참조 하세요.
