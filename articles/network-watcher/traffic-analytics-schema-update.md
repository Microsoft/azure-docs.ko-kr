---
title: Azure 트래픽 분석 스키마 업데이트 - 2020년 3월 | Microsoft Docs
description: 트래픽 분석 스키마의 새 필드를 사용한 샘플 쿼리입니다.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d7c4f1853ff8dcb9249ab6ec4f536e1f8cfa10e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018227"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>트래픽 분석 스키마의 새 필드를 사용한 샘플 쿼리(2019년 8월 스키마 업데이트)

[트래픽 분석 로그 스키마](./traffic-analytics-schema.md)는 **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s** 의 새 필드를 포함합니다. 새 필드는 원본 및 대상 IP에 대한 정보를 제공하고 쿼리를 단순화합니다.

향후 몇 개월 후에는 **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d** 와 같은 오래된 필드가 더 이상 사용되지 않게 됩니다.

다음 세 가지 예제에서는 이전 필드를 새 필드로 바꾸는 방법을 보여 줍니다.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>예제 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s 및 PublicIPs_s 필드

AzurePublic 및 ExternalPublic 흐름의 **FlowDirection_s** 필드에서 원본 및 대상 사례를 유추할 필요가 없습니다. 네트워크 가상 어플라이언스에 대해 **FlowDirection_s** 필드를 사용하는 것은 적합하지 않을 수도 있습니다.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>예제 2: NSGRules_s 필드

이전 필드는 다음 형식을 사용합니다.

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

더 이상 NSG(네트워크 보안 그룹)를 통해 데이터를 집계하지 않습니다. 업데이트된 스키마 **NSGList_s** 에는 하나의 NSG만 포함됩니다. 또한 **NSGRules** 에는 하나의 규칙만 포함됩니다. 여기에 제시된 필드 및 예제에 표시된 다른 필드에서 복잡한 서식을 제거했습니다.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>예제 3: FlowCount_d 필드

NSG에서 데이터를 결합하지 않으므로 **FlowCount_d** 는 다음과 같이 간단합니다.

**AllowedInFlows_d** + **DeniedInFlows_d** + **AllowedOutFlows_d** + **DeniedOutFlows_d**

4개 필드 중 하나만 0이 아닙니다. 다른 세 필드는 0이 됩니다. 필드는 흐름이 캡처된 NIC의 상태와 개수를 표시하도록 채워집니다.

다음 조건을 나타냅니다.

- 흐름이 허용된 경우 "Allowed" 접두사가 붙은 필드 중 하나가 채워집니다.
- 흐름이 거부된 경우 "Denied" 접두사가 붙은 필드 중 하나가 채워집니다.
- 흐름이 인바운드인 경우 "InFlows_d" 접미사가 붙은 필드 중 하나가 채워집니다.
- 흐름이 아웃바운드인 경우 "OutFlows_d" 접미사로 붙은 필드 중 하나가 채워집니다.

이러한 조건에 따라 4개의 필드 중 어떤 필드를 채울지 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 자주 묻는 질문에 대한 답변은 [트래픽 분석 FAQ](traffic-analytics-faq.md)를 참조하세요.
- 기능에 대한 자세한 내용은 [트래픽 분석 설명서](traffic-analytics.md)를 참조하세요.