---
title: NSGs에 대 한 흐름 로깅 소개
titleSuffix: Azure Network Watcher
description: 이 문서에서는 Azure Network Watcher의 NSG 흐름 로그 기능을 사용하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c0001add9ddbafb67dc7ac305c5fc171a8e24a51
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070584"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>네트워크 보안 그룹에 대한 흐름 로깅 소개

## <a name="introduction"></a>소개

Nsg ( [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) ) 흐름 로그는 nsg를 통과 하는 IP 트래픽에 대 한 정보를 로깅할 수 있는 Azure Network Watcher의 기능입니다. 흐름 데이터는 액세스할 수 있는 위치에서 Azure Storage 계정으로 전송 되며 원하는 시각화 도구, SIEM 또는 ID로 내보낼 수 있습니다.

![흐름 로그 개요](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>흐름 로그를 사용 하는 이유

보안, 규정 준수 및 성능이 저하되지 않도록 네트워크를 모니터링하고 관리하고 파악하기 위해 필요합니다. 네트워크를 보호하고 최적화하는 데 있어서 가장 중요한 것은 사용자가 자신의 정확히 아는 것입니다. 네트워크의 현재 상태, 연결 중인 사용자 및 연결되는 위치, 인터넷에 대해 열려 있는 포트, 예상되는 네트워크 동작, 비정상적인 네트워크 동작, 갑작스런 트래픽 증가에 대해 알아야 하는 경우가 종종 있습니다.

흐름 로그는 클라우드 환경의 모든 네트워크 활동에 대 한 진위의 원본입니다. 예정 된 시작 리소스 또는 대기업에서 침입을 감지 하려고 시도 하는 경우, 흐름 로그는 최상의 방법입니다. 네트워크 흐름을 최적화 하 고, 처리량을 모니터링 하 고, 규정 준수를 확인 하 고, 침입을 감지 하는 데 사용할 수 있습니다.

## <a name="common-use-cases"></a>일반 사용 예

**네트워크 모니터링**: 알 수 없거나 원치 않는 트래픽을 식별 합니다. 트래픽 수준 및 대역폭 사용량을 모니터링 합니다. IP 및 포트로 흐름 로그를 필터링 하 여 응용 프로그램 동작을 이해 합니다. 흐름 로그를 선택한 분석 및 시각화 도구로 내보내 모니터링 대시보드를 설정 합니다.

**사용 모니터링 및 최적화:** 네트워크에서 상위 토커를 식별 합니다. GeoIP 데이터와 결합 하 여 지역 간 트래픽을 식별 합니다. 용량 예측을 위한 트래픽 증가를 이해 합니다. 데이터를 사용 하 여 overtly 제한적인 트래픽 규칙을 제거 합니다.

**규정 준수**: 흐름 데이터를 사용 하 여 엔터프라이즈 액세스 규칙에 대 한 네트워크 격리 및 준수를 확인 합니다.

**네트워크 법률 & 보안 분석**: 손상 된 ip 및 네트워크 인터페이스에서 네트워크 흐름을 분석 합니다. 흐름 로그를 선택한 SIEM 또는 IDS 도구로 내보냅니다.

## <a name="how-logging-works"></a>로깅 작동 방법

**키 속성**

- 흐름 로그는 [계층 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) 에서 작동 하 고 nsg에서 들어오고 나가는 모든 IP 흐름을 기록 합니다.
- 로그는 Azure 플랫폼을 통해 수집 되며 고객 리소스나 네트워크 성능에 영향을 주지 않습니다.
- 로그는 JSON 형식으로 작성 되며, NSG 규칙에 따라 아웃 바운드 및 인바운드 흐름을 표시 합니다.
- 각 로그 레코드에는 흐름이 적용 되는 NIC (네트워크 인터페이스), 5 튜플 정보, 트래픽 결정 & (버전 2에만 해당) 처리량 정보가 포함 됩니다. 전체 세부 정보는 아래 _로그 형식_ 을 참조 하세요.
- 흐름 로그에는 생성 후 최대 1 년까지 로그를 자동으로 삭제할 수 있는 보존 기능이 있습니다. 

> [!NOTE]
> 보존은 범용 [V2 저장소 계정 (GPv2)](https://docs.microsoft.com/azure/storage/common/storage-account-overview#types-of-storage-accounts)을 사용 하는 경우에만 사용할 수 있습니다. 

**핵심 개념**

- 소프트웨어 정의 네트워크는 Vnet (가상 네트워크) 및 서브넷을 중심으로 구성 됩니다. 이러한 Vnet 및 서브넷의 보안은 NSG를 사용 하 여 관리할 수 있습니다.
- NSG (네트워크 보안 그룹)에는 연결 된 리소스의 네트워크 트래픽을 허용 하거나 거부 하는 _보안 규칙_ 목록이 포함 되어 있습니다. NSGs는 서브넷, 개별 Vm 또는 Vm에 연결 된 개별 NIC (네트워크 인터페이스)에 연결 될 수 있습니다 (리소스 관리자). 자세한 내용은 [네트워크 보안 그룹 개요](https://docs.microsoft.com/azure/virtual-network/security-overview?toc=%2Fazure%2Fnetwork-watcher%2Ftoc.json)를 참조하세요.
- 네트워크의 모든 트래픽 흐름은 해당 NSG의 규칙을 사용 하 여 평가 됩니다.
- 이러한 평가 결과는 NSG 흐름 로그입니다. 흐름 로그는 Azure 플랫폼을 통해 수집 되며 고객 리소스를 변경할 필요가 없습니다.
- 참고: 규칙은 종료 & 두 가지 유형으로, 각각 서로 다른 로깅 동작을 포함 합니다.
- - NSG 거부 규칙이 종료 되 고 있습니다. 트래픽을 거부 하는 NSG는 흐름 로그에 로그를 기록 하 고이 경우 처리는 모든 NSG가 트래픽을 거부 한 후 중지 됩니다. 
- - NSG 허용 규칙은 종료 하지 않습니다. 즉, 하나의 NSG에서 허용 하는 경우에도 다음 NSG로 처리를 계속 합니다. 트래픽을 허용 하는 마지막 NSG는 흐름 로그에 대 한 트래픽을 기록 합니다.
- NSG 흐름 로그는 액세스할 수 있는 위치에서 저장소 계정에 기록 됩니다.
- TA, Splunk, Grafana, Stealthwatch 등과 같은 도구를 사용 하 여 흐름 로그를 내보내고, 처리 하 고, 분석 하 고, 시각화할 수 있습니다.

## <a name="log-format"></a>로그 형식

흐름 로그에는 다음 속성이 포함됩니다.

* **시간** - 이벤트가 로그된 시간
* **systemid** -네트워크 보안 그룹 시스템 id입니다.
* **범주** - 이벤트의 범주입니다. 범주는 항상 **NetworkSecurityGroupFlowEvent**입니다.
* **resourceid** -nsg의 리소스 ID
* **operationName** - 항상 NetworkSecurityGroupFlowEvents
* **속성** - 흐름의 속성 컬렉션
    * **버전** - 흐름 로그 이벤트 스키마의 버전 번호
    * **흐름** - 흐름의 컬렉션입니다. 이 속성에는 서로 다른 규칙에 대한 여러 항목이 있습니다.
        * **규칙** - 흐름이 나열된 규칙
            * **흐름** -흐름의 컬렉션
                * **mac** - 흐름이 수집된 VM에 대한 NIC의 MAC 주소
                * **flowTuples** - 쉼표로 구분된 형식에서 흐름 튜플에 대한 여러 속성을 포함하는 문자열
                    * **타임 스탬프** -이 값은 흐름이 UNIX epoch 형식으로 발생 했을 때의 타임 스탬프입니다.
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


**NSG 흐름 로그 버전 2 (vs 버전 1)** 

로그 버전 2는 흐름 상태의 개념을 소개 합니다. 수신하는 흐름 로그 버전을 구성할 수 있습니다.

흐름이 시작되면 흐름 상태 _B_가 기록됩니다. 흐름 상태 _C_ 및 흐름 상태 _E_는 각각 흐름의 연속 및 흐름 종료를 나타내는 상태입니다. _C_ 및 _E_ 상태 둘 다 트래픽 대역폭 정보를 포함합니다.

### <a name="sample-log-records"></a>샘플 로그 레코드

다음 텍스트는 흐름 로그의 예제입니다. 이전 섹션에 설명된 속성 목록을 따르는 여러 레코드를 볼 수 있습니다.

> [!NOTE]
> *Flowtuples* 속성의 값은 쉼표로 구분 된 목록입니다.
 
**버전 1 NSG 흐름 로그 형식 샘플**
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
        
        
```
**버전 2 NSG 흐름 로그 형식 샘플**
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
        }
        
```
**설명 된 로그 튜플**

![흐름 로그 튜플](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**샘플 대역폭 계산**

185.170.185.105:35370과 10.2.0.4:23 간 TCP 대화의 흐름 튜플:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

연속 _C_ 및 종료 _E_ 흐름 상태의 경우, 바이트 및 패킷 수는 이전 흐름 튜플 레코드 시간으로부터의 집계 개수입니다. 이전 예제 대화를 참조할 때 전송된 패킷의 총 수는 1021+52+8005+47 = 9125가 됩니다. 전송된 바이트의 총 수는 588096+29952+4610880+27072 = 5256000입니다.


## <a name="enabling-nsg-flow-logs"></a>NSG 흐름 로그 사용

흐름 로그 사용에 대 한 지침을 보려면 아래에서 관련 링크를 사용 하십시오.

- [Azure Portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)
- [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-powershell)
- [CLI](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-cli)
- [REST (영문)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-rest)
- [Azure Resource Manager](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)

## <a name="updating-parameters"></a>매개 변수 업데이트

**Azure Portal**

Azure Portal에서 Network Watcher의 NSG 흐름 로그 섹션으로 이동 합니다. 그런 다음 NSG의 이름을 클릭 합니다. 그러면 흐름 로그에 대 한 설정 창이 표시 됩니다. 원하는 매개 변수를 변경 하 고 **저장** 을 눌러 변경 내용을 배포 합니다.

**PS/CLI/REST/ARM**

명령줄 도구를 통해 매개 변수를 업데이트 하려면 앞에서 설명한 것과 같은 명령을 사용 하 여 변경 하려는 업데이트 된 매개 변수를 사용 합니다.

## <a name="working-with-flow-logs"></a>흐름 로그 작업

*흐름 로그 읽기 및 내보내기*

- [&amp;포털에서 보기 흐름 로그 다운로드](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log)
- [PowerShell 함수를 사용 하 여 흐름 로그 읽기](https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs)
- [NSG 흐름 로그를 Splunk로 내보내기](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

흐름 로그는 NSG를 대상으로 하지만 다른 로그와 동일하게 표시되지 않습니다. 흐름 로그는 스토리지 계정 내에만 저장되며 다음 예제와 같이 로깅 경로를 따릅니다.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*흐름 로그 시각화*

- [Azure 트래픽 분석](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) 은 흐름 로그를 처리 하 고, 통찰력을 추출 하 고, 흐름 로그를 시각화 하는 azure native service입니다. 
- [자습서 Power BI를 사용 하 여 NSG 흐름 로그 시각화](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-power-bi)
- [자습서 탄력적 스택을 사용 하 여 NSG 흐름 로그 시각화](https://docs.microsoft.com/azure/network-watcher/network-watcher-visualize-nsg-flow-logs-open-source-tools)
- [자습서 Grafana를 사용 하 여 NSG 흐름 로그 관리 및 분석](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-grafana)
- [자습서 Graylog를 사용 하 여 NSG 흐름 로그 관리 및 분석](https://docs.microsoft.com/azure/network-watcher/network-watcher-analyze-nsg-flow-logs-graylog)


## <a name="nsg-flow-logging-considerations"></a>NSG 흐름 로깅 고려 사항

**저장소 계정 고려 사항**: 

- 위치: 사용 되는 저장소 계정은 NSG와 동일한 지역에 있어야 합니다.
- 자체 관리 키 회전: 액세스 키를 저장소 계정으로 변경/회전 하는 경우 NSG 흐름 로그가 작동을 중지 합니다. 이 문제를 해결 하려면 NSG 흐름 로그를 사용 하지 않도록 설정한 다음 다시 사용 하도록 설정 해야 합니다.

**흐름 로깅 비용**: nsg 흐름 로깅은 생성 된 로그의 양에 따라 청구 됩니다. 트래픽 볼륨이 많으면 흐름 로그 볼륨과 관련 비용도 증가할 수 있습니다. NSG 흐름 로그의 가격에는 스토리지의 기본 비용이 포함되지 않습니다. NSG 흐름 로깅에서 보존 정책 기능을 사용 하는 것은 오랜 시간 동안 별도의 저장소 비용을 발생 시킵니다. 보존 정책 기능이 필요하지 않은 경우 이 값을 0으로 설정하는 것이 좋습니다. 자세한 내용은 [Network Watcher 가격](https://azure.microsoft.com/pricing/details/network-watcher/) 책정 및 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 을 참조 하세요.

**사용자 정의 인바운드 TCP 규칙에**대 한 문제: [nsgs (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview) 는 [상태 저장 방화벽](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true)으로 구현 됩니다. 그러나 현재 플랫폼 제한으로 인해 인바운드 TCP 흐름에 영향을 주는 사용자 정의 규칙은 상태 비저장 방식으로 구현 됩니다. 이로 인해 사용자 정의 인바운드 규칙의 영향을 받는 흐름이 종료 되지 않습니다. 이러한 흐름에 대해서는 바이트 및 패킷 수가 추가로 기록 되지 않습니다. 결과적으로 NSG 흐름 로그 (및 트래픽 분석)에서 보고 되는 바이트 수와 패킷은 실제 숫자와 다를 수 있습니다. 이러한 문제를 해결 하는 옵트인 플래그는 12 월 2020 최신 버전에서 사용할 수 있도록 예약 되어 있습니다. 이 문제로 인해 발생 하는 심각한 문제를 야기 하는 고객은 지원을 통해 옵트인을 요청할 수 있습니다. Network Watcher > NSG 흐름 로그에서 지원 요청을 발생 시킵니다.  

**인터넷 ip에서 공용 ip가 없는 vm으로 로그온 하는 인바운드 흐름**: 인스턴스 수준 공용 IP로 NIC와 연결 된 공용 ip 주소를 통해 할당 되거나 기본 부하 분산 장치 백 엔드 풀의 일부인 vm은 [기본 SNAT](../load-balancer/load-balancer-outbound-connections.md) 를 사용 하 고 AZURE에서 할당 된 IP 주소를 사용 하 여 아웃 바운드 연결을 용이 하 게 합니다. 따라서 흐름이 SNAT에 할당 된 포트 범위의 포트를 대상으로 하는 경우 인터넷 IP 주소에서 흐름에 대 한 흐름 로그 항목이 표시 될 수 있습니다. Azure는 VM에 대 한 이러한 흐름을 허용 하지 않지만, 시도는 기록 되 고 Network Watcher의 NSG 흐름 로그에 설계상 표시 됩니다. 원치 않는 인바운드 인터넷 트래픽이 NSG를 사용 하 여 명시적으로 차단 되도록 하는 것이 좋습니다.

## <a name="best-practices"></a>모범 사례

**중요 한 vnet/서브넷에 대해 설정**: 흐름 로그는 감사 기능 및 보안 모범 사례로 서 구독의 모든 중요 한 vnet/서브넷에서 사용 하도록 설정 되어야 합니다. 

**리소스에 연결 된 모든 nsg에 대해 Nsg 흐름 로깅을 사용 하도록 설정**: Azure의 흐름 로깅은 nsg 리소스에 구성 됩니다. 하나의 흐름은 하나의 NSG 규칙에만 연결됩니다. 여러 NSGs를 사용 하는 시나리오에서 리소스의 서브넷 또는 네트워크 인터페이스를 적용 한 모든 NSGs에 대해 NSGS 흐름 로그를 사용 하도록 설정 하 여 모든 트래픽이 기록 되도록 하는 것이 좋습니다. 자세한 내용은 네트워크 보안 그룹에서 [트래픽 평가 방법](../virtual-network/network-security-group-how-it-works.md) 을 참조 하세요.

**저장소 프로 비전**: 저장소는 예상 흐름 로그 볼륨이 포함 된 튜닝에서 프로 비전 되어야 합니다.

## <a name="troubleshooting-common-issues"></a>일반적인 문제 해결

**NSG 흐름 로그를 사용하도록 설정할 수 없습니다.**

- **Microsoft Insights** 리소스 공급자가 등록 되지 않았습니다.

_Authorizationfailed_ 또는 _GatewayAuthenticationFailed_ 오류를 받은 경우 구독에서 Microsoft Insights 리소스 공급자를 사용하도록 설정하지 않았을 수 있습니다. [지침에 따라](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#register-insights-provider) Microsoft Insights 공급자를 사용 하도록 설정 합니다.

**NSG 흐름 로그를 사용하도록 설정했지만 스토리지 계정에 데이터가 표시되지 않습니다.**

- **설치 시간**

스토리지 계정에 NSG 흐름 로그가 표시되는 데 최대 5분이 걸릴 수 있습니다(올바르게 구성된 경우). [여기에 설명된 대로](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal#download-flow-log) 액세스할 수 있는 PT1H.json이 나타납니다.

- **NSG에 트래픽이 없음**

경우에 따라 VM이 활성화되어 있지 않거나 App Gateway의 업스트림 필터 또는 NSG에 대한 트래픽을 차단하는 기타 디바이스 때문에 로그가 표시되지 않습니다.

**NSG 흐름 로그를 자동화하려고 함**

ARM 템플릿을 통한 자동화 지원은 현재 NSG 흐름 로그에 대해 사용할 수 없습니다. 자세한 내용은 [기능 공지](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) 를 참조 하세요.

## <a name="faq"></a>FAQ

**NSG 흐름 로그는 어떻게 되나요?**

Azure 네트워크 리소스는 [NSGs (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview)를 통해 결합 및 관리할 수 있습니다. NSG 흐름 로그를 사용 하 여 Nsg를 통한 모든 트래픽에 대 한 5 튜플 흐름 정보를 기록할 수 있습니다. 원시 흐름 로그는 필요에 따라 추가로 처리, 분석, 쿼리 또는 내보낼 수 있는 Azure Storage 계정에 기록 됩니다.

**흐름 로그를 사용 하 여 네트워크 대기 시간 또는 성능에 영향을 미칩니까?**

흐름 로그 데이터는 네트워크 트래픽 경로 외부에서 수집 되므로 네트워크 처리량 또는 대기 시간에 영향을 주지 않습니다. 네트워크 성능에 영향을 미치는 위험 없이 흐름 로그를 만들거나 삭제할 수 있습니다.

**방화벽 뒤의 저장소 계정을 사용 하 여 NSG 흐름 로그를 사용 어떻게 할까요??**

방화벽 뒤의 저장소 계정을 사용 하려면 저장소 계정에 액세스 하기 위해 신뢰할 수 있는 Microsoft 서비스에 대 한 예외를 제공 해야 합니다.

- 포털의 전역 검색 또는 [저장소 계정 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) 에서 저장소 계정 이름을 입력 하 여 저장소 계정으로 이동 합니다.
- **설정** 섹션 아래에서 **방화벽 및 가상 네트워크** 를 선택 합니다.
- 다음 **에서 액세스 허용**에서  **선택한 네트워크**를 선택 합니다. 그런 다음  **예외**아래에서 * * * *이 저장소 계정에 액세스할 수 있는 신뢰할 수 있는 Microsoft 서비스 허용 * * * * 옆의 상자를 선택 합니다.
- 이미 선택되어 있는 경우에는 변경할 필요가 없습니다.
- [Nsg 흐름 로그 개요 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) 에서 대상 nsg를 찾고, 위의 저장소 계정을 선택 하 여 Nsg 흐름 로그를 사용 하도록 설정 합니다.

몇 분 후에 스토리지 로그를 확인할 수 있습니다. 그러면 업데이트된 타임스탬프 또는 새로 만든 JSON 파일이 표시됩니다.

**서비스 끝점 뒤의 저장소 계정으로 NSG 흐름 로그를 사용 어떻게 할까요??**

NSG 흐름 로그는 추가 구성이 필요 없이 서비스 끝점과 호환 됩니다. 가상 네트워크에서 [서비스 끝점을 사용 하도록 설정 하는 방법에 대 한 자습서](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) 를 참조 하세요.

**흐름 로그 버전 1 & 2의 차이점은 무엇 인가요?**

흐름 로그 버전 2에는 전송 되는 바이트와 패킷에 대 한 정보를 저장 하 & _흐름 상태의_ 개념이 도입 되었습니다. [자세히 알아보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)

## <a name="pricing"></a>가격 책정

NSG 흐름 로그는 수집 된 로그의 GB 당 요금이 청구 되며 구독 당 5gb/월의 무료 계층으로 제공 됩니다. 해당 지역의 현재 가격은 가격 [책정 페이지를 Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/)참조 하세요.

로그의 저장소는 별도로 청구 됩니다. 관련 가격은 [Azure Storage 블록 blob 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/) 를 참조 하세요.
 
