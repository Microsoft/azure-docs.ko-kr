---
title: Azure Network Watcher를 사용하여 네트워크 보안 그룹에 대한 흐름 로깅 소개 | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher의 NSG 흐름 로그 기능을 사용하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 6e15149dec9fdbb7413745d36b3f6a158113b586
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684564"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>네트워크 보안 그룹에 대한 흐름 로깅 소개

NSG(네트워크 보안 그룹) 흐름 로그는 NSG를 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 흐름 로그는 JSON 형식으로 작성되고 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC(네트워크 인터페이스), 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜), 버전 2에서는 처리량 정보(바이트 및 패킷)를 보여줍니다.


![흐름 로그 개요](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

흐름 로그는 NSG를 대상으로 하지만 다른 로그와 동일하게 표시되지 않습니다. 흐름 로그는 스토리지 계정 내에만 저장되며 다음 예제와 같이 로깅 경로를 따릅니다.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
[트래픽 분석](traffic-analytics.md)을 사용하여 흐름 로그를 분석하고 네트워크 트래픽에 대한 인사이트를 얻을 수 있습니다.

다른 로그에서 보듯이 흐름 로그에 동일한 보존 정책을 적용합니다. 1일에서 2147483647일까지 로그 보존 정책을 설정할 수 있습니다. 보존 정책을 설정하지 않으면 로그는 계속 유지됩니다.

> [!NOTE] 
> NSG 흐름 로깅과 함께 보존 정책 기능을 사용하면 대량의 스토리지 작업이 파생되고 관련 비용이 발생할 수 있습니다. 보존 정책 기능이 필요하지 않은 경우 이 값을 0으로 설정하는 것이 좋습니다.


## <a name="log-file"></a>로그 파일

흐름 로그에는 다음 속성이 포함됩니다.

* **시간** - 이벤트가 로그된 시간
* **systemId** - 네트워크 보안 그룹 리소스 ID
* **범주** - 이벤트의 범주입니다. 범주는 항상 **NetworkSecurityGroupFlowEvent**입니다.
* **resourceid** - NSG의 리소스 ID
* **operationName** - 항상 NetworkSecurityGroupFlowEvents
* **속성** - 흐름의 속성 컬렉션
    * **버전** - 흐름 로그 이벤트 스키마의 버전 번호
    * **흐름** - 흐름의 컬렉션입니다. 이 속성에는 서로 다른 규칙에 대한 여러 항목이 있습니다.
        * **규칙** - 흐름이 나열된 규칙
            * **흐름** - 흐름의 컬렉션
                * **mac** - 흐름이 수집된 VM에 대한 NIC의 MAC 주소
                * **flowTuples** - 쉼표로 구분된 형식에서 흐름 튜플에 대한 여러 속성을 포함하는 문자열
                    * **타임스탬프** - 이 값은 흐름이 UNIX EPOCH 형식에서 발생하는 경우의 타임스탬프입니다.
                    * **원본 IP** - 원본 IP
                    * **대상 IP** - 대상 IP
                    * **원본 포트** - 원본 포트
                    * **대상 포트** - 대상 포트
                    * **프로토콜** - 흐름의 프로토콜입니다. 유효한 값은 TCP에 대해서 **T**이며 UDP에 대해서 **U**입니다.
                    * **트래픽 흐름** - 트래픽 흐름의 방향입니다. 유효한 값은 인바운드에 대해서 **I**이며 아웃바운드에 대해서 **O**입니다.
                    * **트래픽 의사 결정** - 트래픽이 허용되었는지 또는 거부되었는지 여부입니다. 유효한 값은 허용에 대해 **A**이며 거부에 대해 **D**입니다.
                    * **흐름 상태 - 버전 2만** - 흐름의 상태를 캡처합니다. 가능한 상태는 다음과 같습니다. **B**: 흐름이 만들어질 때 시작합니다. 통계가 제공되지 않습니다. **C**: 지속적인 흐름에 대해 계속됩니다. 통계가 5분 간격으로 제공됩니다. **E**: 흐름이 종료되면 끝납니다. 통계가 제공됩니다.
                    * **패킷 - 원본에서 대상으로 - 버전 2만** 마지막 업데이트 이후 원본에서 대상으로 전송된 TCP 또는 UDP 패킷의 총 수입니다.
                    * **전송된 바이트 - 원본에서 대상으로 - 버전 2만** 마지막 업데이트 이후 원본에서 대상으로 전송된 TCP 또는 UDP 패킷 바이트의 총 수입니다. 패킷 바이트에는 패킷 헤더 및 페이로드가 포함됩니다.
                    * **패킷 - 대상에서 원본으로 - 버전 2만** 마지막 업데이트 이후 대상에서 원본으로 전송된 TCP 또는 UDP 패킷의 총 수입니다.
                    * **전송된 바이트 - 대상에서 원본으로 - 버전 2만** 마지막 업데이트 이후 대상에서 원본으로 전송된 TCP 및 UDP 패킷 바이트의 총 수입니다. 패킷 바이트에는 패킷 헤더 및 페이로드가 포함됩니다.

## <a name="nsg-flow-logs-version-2"></a>NSG 흐름 로그 버전 2

로그의 버전 2에는 흐름 상태가 도입되었습니다. 수신하는 흐름 로그 버전을 구성할 수 있습니다. 흐름 로그를 사용하도록 설정하는 방법을 알아보려면 [NSG 흐름 로깅을 사용하도록 설정](network-watcher-nsg-flow-logging-portal.md)을 참조하세요.

흐름이 시작되면 흐름 상태 *B*가 기록됩니다. 흐름 상태 *C* 및 흐름 상태 *E*는 각각 흐름의 연속 및 흐름 종료를 나타내는 상태입니다. *C* 및 *E* 상태 둘 다 트래픽 대역폭 정보를 포함합니다.

연속 *C* 및 종료 *E* 흐름 상태의 경우, 바이트 및 패킷 수는 이전 흐름 튜플 레코드 시간으로부터의 집계 개수입니다. 이전 예제 대화를 참조할 때 전송된 패킷의 총 수는 1021+52+8005+47 = 9125가 됩니다. 전송된 바이트의 총 수는 588096+29952+4610880+27072 = 5256000입니다.

**예제**: 185.170.185.105:35370과 10.2.0.4:23 간 TCP 대화의 흐름 튜플:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

연속 *C* 및 종료 *E* 흐름 상태의 경우, 바이트 및 패킷 수는 이전 흐름 튜플 레코드 시간으로부터의 집계 개수입니다. 이전 예제 대화를 참조할 때 전송된 패킷의 총 수는 1021+52+8005+47 = 9125가 됩니다. 전송된 바이트의 총 수는 588096+29952+4610880+27072 = 5256000입니다.

다음 텍스트는 흐름 로그의 예제입니다. 이전 섹션에 설명된 속성 목록을 따르는 여러 레코드를 볼 수 있습니다.

## <a name="nsg-flow-logging-considerations"></a>NSG 흐름 로깅 고려 사항

**리소스에 연결된 모든 NSG에서 NSG 흐름 로깅 사용**: Azure의 흐름 로깅은 NSG 리소스에서 구성됩니다. 하나의 흐름은 하나의 NSG 규칙에만 연결됩니다. 모든 NSG가 활용되는 시나리오에서는 리소스의 서브넷 또는 네트워크 인터페이스가 적용된 모든 NSG에서 NSG 흐름 로깅을 사용하도록 설정하여 모든 트래픽이 기록되도록 하는 것이 좋습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [트래픽 평가 방식](../virtual-network/security-overview.md#how-traffic-is-evaluated)참조하세요. 

**흐름 로깅 비용**: NSG 흐름 로그는 생성된 로그 양에 따라 요금이 청구됩니다. 트래픽 볼륨이 많으면 흐름 로그 볼륨과 관련 비용도 증가할 수 있습니다. NSG 흐름 로그의 가격에는 스토리지의 기본 비용이 포함되지 않습니다. NSG 흐름 로깅과 함께 보존 정책 기능을 사용하면 대량의 스토리지 작업이 파생되고 관련 비용이 발생할 수 있습니다. 보존 정책 기능이 필요하지 않은 경우 이 값을 0으로 설정하는 것이 좋습니다. 자세한 내용은 [Network Watcher 가격 책정](https://azure.microsoft.com/en-us/pricing/details/network-watcher/) 및 [Azure Storage 가격 책정](https://azure.microsoft.com/en-us/pricing/details/storage/)을 참조하세요.

**공용 Ip 없이 Vm을 인터넷 Ip에서에서 로그온 흐름 인바운드**: Vm 인스턴스 수준 공용 IP로 NIC와 연결 된 공용 IP 주소를 통해 할당 된 공용 IP가 없는 또는 기본 부하 분산 장치 백 엔드 풀을 사용 하 여의 일부인 [기본 SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) 에서 할당 된 IP 주소가 및 아웃 바운드 연결을 위해 azure를 제공 합니다. 흐름의 흐름 로그 항목이 표시 될 수는 결과적으로, 인터넷에서 IP 주소, 포트 할당 snat 포트의 범위에 흐름을 대상으로 하는 경우. Azure VM에 이러한 흐름을 허용 하지 않습니다 하는 동안 시도가 기록 되 고 디자인 하 여 Network Watcher NSG 흐름 로그에 표시 됩니다. NSG를 사용 하 여 원치 않는 인바운드 인터넷 트래픽을 명시적으로 차단 하는 것이 좋습니다.

## <a name="sample-log-records"></a>샘플 로그 레코드

다음 텍스트는 흐름 로그의 예제입니다. 이전 섹션에 설명된 속성 목록을 따르는 여러 레코드를 볼 수 있습니다.


> [!NOTE]
> **flowTuples* 속성의 값은 쉼표로 구분된 목록입니다.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>버전 1 NSG 흐름 로그 형식 샘플
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>버전 2 NSG 흐름 로그 형식 샘플
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>다음 단계

- 흐름 로그를 사용하도록 설정하는 방법을 알아보려면 [NSG 흐름 로깅을 사용하도록 설정](network-watcher-nsg-flow-logging-portal.md)을 참조하세요.
- 흐름 로그를 읽는 방법을 알아보려면 [NSG 흐름 로그 읽기](network-watcher-read-nsg-flow-logs.md)를 참조하세요.
- NSG 로깅에 대 한 자세한 내용은 참조 하세요 [네트워크 보안 그룹 (Nsg)에 대 한 Azure Monitor 로그](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)합니다.
- 트래픽이 허용되거나 VM에서 거부할지 여부를 결정하려면 [VM 네트워크 트래픽 필터 문제 진단](diagnose-vm-network-traffic-filtering-problem.md)을 참조하세요.
