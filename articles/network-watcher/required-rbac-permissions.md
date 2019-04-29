---
title: Azure Network Watcher 기능을 사용하는 데 필요한 사용 권한 | Microsoft Docs
description: Network Watcher 기능을 사용하는 데 필요한 Azure 역할 기반 액세스 제어 권한에 대해 알아봅니다.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 429f7862901814fbd2017c395706fbfa2c345f72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652897"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Network Watcher 기능을 사용하는 데 필요한 역할 기반 액세스 제어 권한

Azure RBAC(역할 기반 액세스 제어)를 사용하면 사용자가 할당된 업무를 완료하는 데 필요한 조직의 멤버에게 특정 작업만을 할당할 수 있습니다. Network Watcher 기능을 사용하려면 Azure에 로그인하는 계정은 [소유자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor) 또는 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) 기본 제공 역할에 할당되거나 다음에 나오는 섹션에서 각 Network Watcher 기능에 대해 나열된 작업을 할당받은 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)에 할당되어야 합니다. Network Watcher의 기능에 대해 자세히 알아보려면 [Network Watcher란?](network-watcher-monitoring-overview.md)을 참조하세요.

## <a name="network-watcher"></a>Network Watcher

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Network Watcher 가져오기                                          |
| Microsoft.Network/networkWatchers/write                             | Network Watcher 만들기 또는 업데이트                             |
| Microsoft.Network/networkWatchers/delete                            | Network Watcher 삭제                                       |

## <a name="nsg-flow-logs"></a>NSG 흐름 로그

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | 흐름 로그 구성                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | 흐름 로그에 대한 쿼리 상태                                    |

## <a name="connection-troubleshoot"></a>연결 문제 해결

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | 연결 문제 해결 테스트 시작
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | 연결 문제 해결 테스트의 쿼리 결과                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | 연결 문제 해결 테스트 실행                             |

## <a name="connection-monitor"></a>연결 모니터

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | 연결 모니터 시작                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | 연결 모니터 중지                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | 연결 모니터 쿼리                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | 연결 모니터 가져오기                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | 연결 모니터 만들기                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | 연결 모니터 삭제                                    |

## <a name="packet-capture"></a>패킷 캡처

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 패킷 캡처의 상태 쿼리                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | 패킷 캡처 중지                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | 패킷 캡처 가져오기                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | 패킷 캡처 만들기                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | 패킷 캡처 삭제                                        |

## <a name="ip-flow-verify"></a>IP 흐름 확인

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | IP 흐름 확인                                              |

## <a name="next-hop"></a>다음 홉

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | VM에서 다음 홉 가져오기                                     |

## <a name="network-security-group-view"></a>네트워크 보안 그룹 보기

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | 보안 그룹 보기                                           |

## <a name="topology"></a>토폴로지

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | 토폴로지 가져오기                                                   |

## <a name="reachability-report"></a>연결 가능성 보고서

| 액션(Action)                                                              | 이름                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Azure 연결 가능성 보고서 가져오기                               |

## <a name="additional-actions"></a>추가 작업

Network Watcher 기능에는 다음 작업도 필요합니다.

- Microsoft.Authorization/\*/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
