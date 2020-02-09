---
title: Azure 방화벽 로그 분석 샘플
description: Azure Monitor 로그를 사용 하 여 Azure 방화벽을 분석할 수 있습니다. 샘플 파일은 Azure Monitor의 뷰 디자이너에서 빌드됩니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/23/2020
ms.author: victorh
ms.openlocfilehash: bc34afe82c1b73afb5f3d5d1a07f2a5059590146
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705991"
---
# <a name="azure-firewall-log-analytics-samples"></a>Azure 방화벽 로그 분석 샘플

다음 Azure Monitor 로그 예제를 사용 하 여 Azure 방화벽 로그를 분석할 수 있습니다. 이 샘플 파일은 Azure Monitor의 뷰 디자이너에서 작성 되었으며 [Azure Monitor의 뷰 디자이너](https://docs.microsoft.com/azure/log-analytics/log-analytics-view-designer) 에는 뷰 디자인 개념에 대한 자세한 정보가 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Azure Monitor 로그 보기

로그 시각화 Azure Monitor 예제를 구성 하는 방법은 다음과 같습니다. [azure-docs-json-samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) 리포지토리에서 시각화 예제를 다운로드할 수 있습니다. 가장 쉬운 방법은 이 페이지의 하이퍼링크를 마우스 오른쪽 단추로 클릭하고 *다른 이름으로 저장*을 클릭한 후, **AzureFirewall.omsview**와 같은 이름을 입력합니다. 

다음 단계를 실행 하 여 Log Analytics 작업 영역에 보기를 추가 합니다.

1. Azure Portal에서 Log Analytics 작업 영역을 엽니다.
2. **일반** 아래에서 **뷰 디자이너**를 엽니다.
3. **가져오기**를 클릭합니다.
4. 이전에 다운로드한 **AzureFirewall.omsview** 파일을 찾아 선택합니다.
5. **저장**을 클릭합니다.

애플리케이션 규칙 로그 데이터에 대한 뷰는 다음과 같이 표시됩니다.

![애플리케이션 규칙 로그 데이터](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

또한 네트워크 규칙 로그 데이터의 경우:

![네트워크 규칙 로그 데이터]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Azure 방화벽은 범주가 포함 된 AzureDiagnostics 아래에 **AzureFirewallApplicationRule** 또는 **AzureFirewallNetworkRule**로 데이터를 기록 합니다. 세부 정보가 포함된 데이터는 msg_s 필드에 저장됩니다. [parse](https://docs.microsoft.com/azure/kusto/query/parseoperator) 연산자를 사용하면 msg_s 필드에서 다양한 흥미로운 속성을 추출할 수 있습니다. 아래 쿼리는 두 범주에 대한 정보를 추출합니다.

## <a name="application-rules-log-data-query"></a>애플리케이션 규칙 로그 데이터 쿼리

아래 쿼리는 애플리케이션 규칙 로그 데이터를 구문 분석합니다. 다양한 주석 줄에 쿼리가 빌드된 방식에 대한 몇 가지 지침이 나와 있습니다.

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

더 요약된 형식의 동일한 쿼리는 다음과 같습니다.

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>네트워크 규칙 로그 데이터 쿼리

다음 쿼리는 네트워크 규칙 로그 데이터를 구문 분석 합니다. 다양한 주석 줄에 쿼리가 빌드된 방식에 대한 몇 가지 지침이 나와 있습니다.

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

더 요약된 형식의 동일한 쿼리는 다음과 같습니다.

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>위협 인텔리전스 로그 데이터 쿼리

다음 쿼리는 위협 인텔리전스 규칙 로그 데이터를 구문 분석 합니다.

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>샘플 로그

다음 로그 예제는 로그 항목에 포함 된 데이터를 보여 줍니다.

![로그 항목](media/log-analytics-samples/log1.png)

![로그 항목](media/log-analytics-samples/log2.png)

![로그 항목](media/log-analytics-samples/log3.png)
## <a name="next-steps"></a>다음 단계

Azure Firewall 모니터링 및 진단에 대한 자세한 내용은 [자습서: Azure Firewall 로그 및 메트릭 모니터링](tutorial-diagnostics.md)을 참조하세요.
