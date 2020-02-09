---
title: Azure 트래픽 분석 스키마 | Microsoft Docs
description: Azure 네트워크 보안 그룹 흐름 로그를 분석 하는 트래픽 분석의 스키마를 이해 합니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666378"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>트래픽 분석에서 스키마 및 데이터 집계

트래픽 분석은 클라우드 네트워크의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 Network Watcher NSG(네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드 내 트래픽 흐름에 대한 정보를 제공합니다. 트래픽 분석을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Azure 구독에서 이루어지는 네트워크 작업을 시각화하고 핫스폿을 식별합니다.
- 열린 포트, 인터넷 액세스를 시도하는 애플리케이션, 불량 네트워크에 연결하는 VM(가상 머신) 같은 정보를 통해 네트워크에 대한 위협을 식별하고 네트워크를 보호합니다.
- Azure 지역 및 인터넷의 트래픽 흐름 패턴을 파악하여 네트워크 성능 및 용량에 맞게 네트워크 배포를 최적화합니다.
- 네트워크에서 연결 실패의 원인이 되는 네트워크 구성 오류를 정확히 파악합니다.
- 바이트, 패킷 또는 흐름의 네트워크 사용량을 파악 합니다.

### <a name="data-aggregation"></a>데이터 집계

1. "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t" 사이에 있는 NSG의 모든 흐름 로그는 트래픽 분석에서 처리 하기 전에 blob으로 저장소 계정에서 1 분 간격으로 캡처됩니다.
2. 트래픽 분석의 기본 처리 간격은 60 분입니다. 즉, 60 분 마다 집계를 위해 저장소에서 blob을 선택 트래픽 분석. 선택한 처리 간격이 10 분 이면 트래픽 분석 10 분 마다 저장소 계정에서 blob을 선택 합니다.
3. 원본 IP, 대상 IP, 대상 포트, NSG 이름, NSG 규칙, 흐름 방향 및 전송 계층 프로토콜 (TCP 또는 UDP)이 동일한 흐름 (참고: 원본 포트는 집계에 대해 제외 됨)은 단일 흐름으로 경로 프로비저닝을 실행할 됩니다 트래픽 분석
4. 이 단일 레코드는 장식 되며 (아래 섹션에 자세히 설명) 트래픽 분석 Log Analytics 수집 .이 프로세스는 최대 1 시간까지 걸릴 수 있습니다.
5. FlowStartTime_t 필드는 "FlowIntervalStartTime_t"와 "FlowIntervalEndTime_t" 사이의 흐름 로그 처리 간격에서 집계 된 흐름 (동일한 네 튜플)의 첫 번째 발생을 나타냅니다.
6. TA의 모든 리소스에 대해 UI에 표시 된 흐름은 NSG에서 표시 되는 총 흐름 이지만 Log Analytics 사용자에 게 축소 된 단일 레코드만 표시 됩니다. 모든 흐름을 보려면 저장소에서 참조할 수 있는 blob_id 필드를 사용 합니다. 해당 레코드의 총 흐름 수는 blob에 표시 되는 개별 흐름과 일치 합니다.

아래 쿼리는 지난 30 일 동안 온-프레미스의 모든 흐름 로그를 확인 하는 데 도움이 됩니다.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
위에서 언급 한 쿼리의 흐름에 대 한 blob 경로를 보려면 아래 쿼리를 사용 합니다.

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

위의 쿼리는 blob에 직접 액세스 하는 URL을 생성 합니다. 자리 표시자를 포함 하는 URL은 다음과 같습니다.

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>트래픽 분석 스키마에 사용 되는 필드
  > [!IMPORTANT]
  > 트래픽 분석 스키마가 2019 년 8 월 2 일에 업데이트 되었습니다. 새 스키마는 원본 및 대상 Ip를 별도로 제거 하 여 쿼리를 더 간단 하 게 만드는 FlowDirection 필드를 별도로 제거 해야 합니다. </br>
  > FASchemaVersion_s 1에서 2로 업데이트 됩니다. </br>
  > 사용 되지 않는 필드: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > 새 필드: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > 사용 되지 않는 필드는 2 월 2 일 11 월 2019 일까 집니다.

트래픽 분석은 Log Analytics을 기반으로 하므로 트래픽 분석로 데코레이팅된 데이터에 대해 사용자 지정 쿼리를 실행 하 고 동일한에 대해 경고를 설정할 수 있습니다.

다음은 스키마의 필드 및 표시 되는 내용입니다.

| 필드 | 형식 | 의견 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 트래픽 분석 데이터에 대 한 테이블
| SubType_s | FlowLog | 흐름 로그의 하위 형식입니다. "FlowLog"만 사용 하 고 SubType_s의 다른 값은 제품의 내부 작동에 사용 됩니다. |
| FASchemaVersion_s |   2   | 스키마 버전입니다. NSG 흐름 로그 버전을 반영 하지 않음 |
| TimeProcessed_t   | UTC 날짜 및 시간  | 트래픽 분석가 저장소 계정에서 원시 흐름 로그를 처리 한 시간입니다. |
| FlowIntervalStartTime_t | UTC 날짜 및 시간 |  흐름 로그 처리 간격의 시작 시간입니다. 흐름 간격이 측정 되는 시간입니다. |
| FlowIntervalEndTime_t | UTC 날짜 및 시간 | 흐름 로그 처리 간격의 종료 시간 |
| FlowStartTime_t | UTC 날짜 및 시간 |  "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t" 사이의 흐름 로그 처리 간격에서 첫 번째로 발생 하는 흐름 (집계 됨)입니다. 이 흐름은 집계 논리를 기반으로 집계 됩니다. |
| FlowEndTime_t | UTC 날짜 및 시간 | "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t" 사이의 흐름 로그 처리 간격에서 마지막으로 발생 한 흐름 (집계 됨)입니다. 흐름 로그 v 2를 기준으로이 필드에는 동일한 4 개 튜플이 시작 된 마지막 흐름이 시작 된 시간이 포함 됩니다 (원시 흐름 레코드에서 "B"로 표시 됨). |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 알 수 없는 개인 <br> * 알 수 없음 | 테이블 아래의 메모에 정의 된 정의 |
| SrcIP_s | 원본 IP 주소 | AzurePublic 및 ExternalPublic 흐름이 있는 경우에는 비어 있습니다. |
| DestIP_s | 대상 IP 주소 | AzurePublic 및 ExternalPublic 흐름이 있는 경우에는 비어 있습니다. |
| VMIP_s | VM의 IP | AzurePublic 및 ExternalPublic 흐름에 사용 됩니다. |
| PublicIP_s | 공용 IP 주소 | AzurePublic 및 ExternalPublic 흐름에 사용 됩니다. |
| DestPort_d | 대상 포트 | 트래픽이 들어오는 포트 |
| L4Protocol_s  | * T <br> * U  | 전송 프로토콜입니다. T = TCP <br> U = UDP |
| L7Protocol_s  | 프로토콜 이름 | 대상 포트에서 파생 |
| FlowDirection_s | * I = 인바운드<br> * O = 아웃 바운드 | 흐름 로그에 따라 NSG의 흐름 방향입니다. |
| FlowStatus_s  | * A = NSG 규칙에서 허용 <br> * D = NSG 규칙에 의해 거부 됨  | 흐름 로그를 기준으로 NSG에 의해 차단 된 흐름의 상태입니다. |
| NSGList_s | \<SUBSCRIPTIONID >\/< RESOURCEGROUP_NAME >\/< NSG_NAME > | 흐름과 연결 된 NSG (네트워크 보안 그룹) |
| NSGRules_s | \<인덱스 값 0) >\|\<NSG_RULENAME >\|\<Flow 방향 >\|\<Flow 상태 >\|\<FlowCount ProcessedByRule > |  이 흐름을 허용 하거나 거부 하는 NSG 규칙 |
| NSGRule_s | NSG_RULENAME |  이 흐름을 허용 하거나 거부 하는 NSG 규칙 |
| NSGRuleType_s | * 사용자 정의 * 기본값 |   흐름에서 사용 하는 NSG 규칙의 유형입니다. |
| MACAddress_s | MAC 주소 | 흐름이 캡처된 NIC의 MAC 주소입니다. |
| Subscription_s | Azure 가상 네트워크/네트워크 인터페이스/가상 머신의 구독이이 필드에 채워집니다. | FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow 및 UnknownPrivate flow 형식에만 적용할 수 있습니다 (한 쪽만 azure 인 흐름 형식). |
| Subscription1_s | 구독 ID | 흐름의 원본 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 컴퓨터의 구독 ID |
| Subscription2_s | 구독 ID | 흐름의 대상 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 컴퓨터의 구독 ID |
| Region_s | 흐름의 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 머신의 Azure 지역 | FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow 및 UnknownPrivate flow 형식에만 적용할 수 있습니다 (한 쪽만 azure 인 흐름 형식). |
| Region1_s | Azure 지역 | 흐름의 원본 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 컴퓨터의 Azure 지역 |
| Region2_s | Azure 지역 | 흐름의 대상 IP가 속한 가상 네트워크의 Azure 지역 |
| NIC_s | \<resourcegroup_Name >\/\<NetworkInterfaceName > |  트래픽을 전송 하거나 수신 하는 VM과 연결 된 NIC |
| NIC1_s | < resourcegroup_Name >/\<NetworkInterfaceName > | 흐름의 원본 IP와 연결 된 NIC |
| NIC2_s | < resourcegroup_Name >/\<NetworkInterfaceName > | 흐름의 대상 IP와 연결 된 NIC |
| VM_s | < resourcegroup_Name >\/\<NetworkInterfaceName > | 네트워크 인터페이스와 연결 된 가상 컴퓨터 NIC_s |
| VM1_s | < resourcegroup_Name >/\<VirtualMachineName > | 흐름의 원본 IP와 연결 된 가상 컴퓨터 |
| VM2_s | < resourcegroup_Name >/\<VirtualMachineName > | 흐름의 대상 IP와 연결 된 가상 컴퓨터 |
| Subnet_s | < ResourceGroup_Name >/< VNET_Name >/\<SubnetName > | NIC_s 연결 된 서브넷 |
| Subnet1_s | < ResourceGroup_Name >/< VNET_Name >/\<SubnetName > | 흐름의 원본 IP와 연결 된 서브넷 |
| Subnet2_s | < ResourceGroup_Name >/< VNET_Name >/\<SubnetName >    | 흐름의 대상 IP와 연결 된 서브넷입니다. |
| ApplicationGateway1_s | \<SubscriptionID >/\<ResourceGroupName >/\<ApplicationGatewayName > | 흐름의 원본 IP와 연결 된 Application gateway |
| ApplicationGateway2_s | \<SubscriptionID >/\<ResourceGroupName >/\<ApplicationGatewayName > | 흐름의 대상 IP와 연결 된 Application gateway |
| LoadBalancer1_s | \<SubscriptionID >/\<ResourceGroupName >/\<LoadBalancerName > | 흐름의 원본 IP와 연결 된 부하 분산 장치 |
| LoadBalancer2_s | \<SubscriptionID >/\<ResourceGroupName >/\<LoadBalancerName > | 흐름의 대상 IP와 연결 된 부하 분산 장치 |
| LocalNetworkGateway1_s | \<SubscriptionID >/\<ResourceGroupName >/\<LocalNetworkGatewayName > | 흐름의 원본 IP와 연결 된 로컬 네트워크 게이트웨이 |
| LocalNetworkGateway2_s | \<SubscriptionID >/\<ResourceGroupName >/\<LocalNetworkGatewayName > | 흐름의 대상 IP와 연결 된 로컬 네트워크 게이트웨이 |
| ConnectionType_s | 가능한 값은 VNetPeering 링, VpnGateway 및 Express 경로입니다. |    연결 형식 |
| ConnectionName_s | \<SubscriptionID >/\<ResourceGroupName >/\<ConnectionName > | 연결 이름입니다. 고 flowtype P2S의 경우 <gateway name>_로 형식이 지정 됩니다<VPN Client IP> |
| ConnectingVNets_s | 공백으로 구분 된 가상 네트워크 이름 목록 | 허브 및 스포크 토폴로지의 경우 허브 가상 네트워크는 여기에 채워집니다. |
| Country_s | 2 letter 국가 코드 (ISO 3166-1 알파-2) | 흐름 유형 ExternalPublic에 대해 채워집니다. PublicIPs_s 필드의 모든 IP 주소는 동일한 국가 코드를 공유 합니다. |
| AzureRegion_s | Azure 지역 위치 | 흐름 유형 AzurePublic에 대해 채워집니다. PublicIPs_s 필드의 모든 IP 주소가 Azure 지역을 공유 합니다. |
| AllowedInFlows_d | | 허용 되는 인바운드 흐름 수입니다. 흐름이 캡처된 네트워크 인터페이스에 대 한 동일한 4 튜플 인바운드를 공유 하는 흐름 수를 나타냅니다. |
| DeniedInFlows_d |  | 거부 된 인바운드 흐름의 수입니다. (흐름이 캡처된 네트워크 인터페이스에 대 한 인바운드) |
| AllowedOutFlows_d | | 허용 된 아웃 바운드 흐름 수 (흐름이 캡처된 네트워크 인터페이스로의 아웃 바운드) |
| DeniedOutFlows_d  | | 거부 된 아웃 바운드 흐름 수 (흐름이 캡처된 네트워크 인터페이스로 아웃 바운드) |
| FlowCount_d | 사용되지 않습니다. 동일한 네 튜플을 일치 시킨 총 흐름입니다. 흐름 유형이 ExternalPublic 및 AzurePublic 인 경우 개수에는 다양 한 PublicIP 주소의 흐름이 포함 됩니다.
| InboundPackets_d | NSG 규칙이 적용 된 네트워크 인터페이스에서 캡처된 대로 받은 패킷 | NSG 흐름 로그 스키마의 버전 2에만 채워집니다. |
| OutboundPackets_d  | NSG 규칙이 적용 된 네트워크 인터페이스에서 캡처되는 것으로 전송 된 패킷 | NSG 흐름 로그 스키마의 버전 2에만 채워집니다. |
| InboundBytes_d |  NSG 규칙이 적용 된 네트워크 인터페이스에서 캡처된 대로 받은 바이트 수 | NSG 흐름 로그 스키마의 버전 2에만 채워집니다. |
| OutboundBytes_d | NSG 규칙이 적용 된 네트워크 인터페이스에서 캡처된 것으로 보낸 바이트 수 | NSG 흐름 로그 스키마의 버전 2에만 채워집니다. |
| CompletedFlows_d  |  | 이 값은 NSG 흐름 로그 스키마의 버전 2에 대해서만 0이 아닌 값으로 채워집니다. |
| PublicIPs_s | < PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|\<FLOW_ENDED_COUNT >\|\<OUTBOUND_PACKETS >\|\<INBOUND_PACKETS >\|\<OUTBOUND_BYTES | 막대로 구분 된 항목 |
| SrcPublicIPs_s | < SOURCE_PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|\<FLOW_ENDED_COUNT >\|\<OUTBOUND_PACKETS >\|\<INBOUND_PACKETS >\|\<OUTBOUND_BYTES | 막대로 구분 된 항목 |
| DestPublicIPs_s | < DESTINATION_PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|\<FLOW_ENDED_COUNT >\|\<OUTBOUND_PACKETS >\|\<INBOUND_PACKETS >\|\<OUTBOUND_BYTES | 막대로 구분 된 항목 |

### <a name="notes"></a>참고

1. AzurePublic 및 ExternalPublic 흐름의 경우, 고객 소유 Azure VM IP는 VMIP_s 필드에 채워지며, 공용 IP 주소는 PublicIPs_s 필드에 채워집니다. 이러한 두 흐름 유형에 대해 SrcIP_s 및 DestIP_s 필드 대신 VMIP_s 및 PublicIPs_s를 사용 해야 합니다. AzurePublic 및 ExternalPublicIP 주소의 경우 고객 로그 분석 작업 영역에 수집 레코드 수가 최소화 되도록 추가로 집계 합니다. 이 필드는 곧 사용 되지 않으며, azure VM이 흐름의 원본 또는 대상 인지 여부에 따라 SrcIP_ 및 DestIP_s를 사용 해야 합니다.
1. 흐름 유형에 대 한 세부 정보: 흐름에 포함 된 IP 주소에 따라 흐름을 다음 흐름 유형으로 분류 합니다.
1. IntraVNet – 흐름의 두 IP 주소는 모두 동일한 Azure Virtual Network에 상주 합니다.
1. 상호 Vnet-흐름의 IP 주소는 서로 다른 두 Azure 가상 네트워크에 상주 합니다.
1. S2S – (사이트 간) IP 주소 중 하나가 Azure Virtual Network에 속하고, 다른 IP 주소는 VPN gateway 또는 Express 경로를 통해 Azure Virtual Network에 연결 된 고객 네트워크 (사이트)에 속합니다.
1. P2S-(지점 및 사이트 간) IP 주소 중 하나가 Azure Virtual Network에 속하고, 다른 IP 주소는 VPN gateway를 통해 Azure Virtual Network에 연결 된 고객 네트워크 (사이트)에 속합니다.
1. AzurePublic-IP 주소 중 하나는 Azure Virtual Network에 속하며 다른 IP 주소는 Microsoft 소유의 Azure 내부 공용 IP 주소에 속합니다. 고객 소유 공용 IP 주소는이 흐름 유형에 속하지 않습니다. 예를 들어 Azure 서비스 (저장소 엔드포인트)로 트래픽을 보내는 고객 소유의 VM은이 흐름 유형 아래에서 분류 됩니다.
1. ExternalPublic-IP 주소 중 하나가 azure Virtual Network에 속하며, 다른 IP 주소는 Azure에 없는 공용 IP 이지만, 트래픽 분석에서 처리 간격에 대해 사용 하는 ASC 피드의 악성으로 보고 되지 않습니다. " FlowIntervalStartTime_t "및" FlowIntervalEndTime_t "입니다.
1. MaliciousFlow-IP 주소 중 하나는 azure virtual network에 속하며, 다른 IP 주소는 Azure에 있지 않은 공용 IP이 고, 트래픽 분석는 " FlowIntervalStartTime_t "및" FlowIntervalEndTime_t "입니다.
1. UnknownPrivate-IP 주소 중 하나가 Azure Virtual Network에 속하며 다른 IP 주소는 RFC 1918에 정의 된 개인 IP 범위에 속하고 고객 소유 사이트 또는 Azure Virtual Network에 트래픽 분석에 매핑할 수 없습니다.
1. 알 수 없음 – 흐름의 IP 주소 중 하나를 Azure의 고객 토폴로지 및 온-프레미스 (사이트)에 매핑할 수 없습니다.
1. 일부 필드 이름에 \_s 또는 \_d가 추가 됩니다. 이는 원본 및 대상을 나타내지는 않지만 각각 데이터 형식 문자열 및 10 진수를 나타냅니다.

### <a name="next-steps"></a>다음 단계
자주 묻는 질문에 대 한 답변을 얻으려면 [트래픽 분석 FAQ](traffic-analytics-faq.md) 를 참조 하세요. 기능에 대 한 자세한 내용은 [트래픽 분석 설명서](traffic-analytics.md) 를 참조 하세요.
