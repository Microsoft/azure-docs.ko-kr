---
title: Azure Firewall 로그 개요
description: 이 문서는 Azure Firewall 진단 로그의 개요입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065855"
---
# <a name="azure-firewall-logs"></a>Azure Firewall 로그

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그를 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md), 스토리지 및 Event Hubs로 보내고 Azure Monitor 로그 또는 Excel 및 Power BI와 같은 다른 도구에서 분석합니다.

## <a name="diagnostic-logs"></a>진단 로그

 다음 진단 로그는 Azure Firewall에 사용할 수 있습니다.

* **애플리케이션 규칙 로그**

   응용 프로그램 규칙 로그를 Event hubs로 스트리밍 및/또는 각 Azure 방화벽을 사용 하는 경우에 Azure Monitor 로그로 전송 저장소 계정에 저장 됩니다. 허용/거부된 연결에 대한 로그에서 구성된 애플리케이션 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

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

   네트워크 규칙 로그를 Event hubs로 스트리밍 및/또는 각 Azure 방화벽을 사용 하는 경우에 Azure Monitor 로그로 전송 저장소 계정에 저장 됩니다. 허용/거부된 연결에 대한 로그에서 구성된 네트워크 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

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

* **스토리지 계정**: 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **Event Hubs**: 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.
* **Azure Monitor 로그**: Azure Monitor 로그는 일반적으로 애플리케이션을 실시간으로 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

## <a name="activity-logs"></a>활동 로그

   활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.

   [Azure 활동 로그](../azure-resource-manager/resource-group-audit.md)(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure 방화벽 로그 및 메트릭을 모니터링 하는 방법에 알아보려면 참조 [자습서: Azure Firewall 로그 모니터링](tutorial-diagnostics.md)을 참조하세요.