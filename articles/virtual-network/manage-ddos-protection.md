---
title: Azure Portal을 사용하여 Azure DDoS Protection 표준 관리
titlesuffix: Azure Virtual Network
description: Azure Monitor에서 Azure DDoS Protection 표준 원격 분석을 사용하여 공격을 완화하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 73036ba1a72d657fd07a826bbee8651781f70e9b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931967"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure DDoS Protection 표준 관리

DDoS(배포된 서비스 거) 보호를 사용 및 사용하지 않도록 설정하는 방법과 원격 분석을 사용하여 Azure DDoS Protection 표준으로 DDoS 공격을 완화하는 방법에 대해 알아봅니다. DDoS Protection 표준은 할당된 Azure [공용 IP 주소](virtual-network-public-ip-address.md)가 있는 Application Gateway, 부하 분산 장치 및 가상 머신과 같은 Azure 리소스를 보호합니다. DDoS Protection 표준 및 해당 기능에 대한 자세한 내용은 [DDoS Protection 표준 개요](ddos-protection-overview.md)를 참조하세요.

이 자습서의 단계를 완료하기 전에 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당된 계정 또는 [사용 권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)로 Azure Portal(https://portal.azure.com)에 로그인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-ddos-protection-plan"></a>DDoS 보호 계획 만들기

DDoS 보호 계획은 구독 전반에 걸쳐 DDoS 보호 표준을 사용하도록 설정된 일단의 가상 네트워크를 정의합니다. 조직에 대해 하나의 DDoS 보호 계획을 구성하고 여러 구독의 가상 네트워크를 동일한 계획에 연결할 수 있습니다. 또한 DDoS 보호 계획 자체는 계획을 만드는 중에 선택한 구독과도 연결됩니다. DDoS Protection 계획은 지역 및 구독에서 작동 합니다. 예-지역 동부-미국에서 계획을 만들고 테 넌 트에서 구독 #1에 연결할 수 있습니다. 동일한 계획은 테 넌 트 전체에서 다른 지역의 다른 구독에서 가상 네트워크에 연결할 수 있습니다. 보호된 공용 IP 주소의 수가 100개를 초과하는 경우 계획이 연결된 구독에 따라 계획에 대한 월별 정기 요금과 초과분 요금이 부과됩니다. DDoS 가격 책정에 대한 자세한 내용은 [가격 정보](https://azure.microsoft.com/pricing/details/ddos-protection/)를 참조하세요.

대부분의 조직에서는 둘 이상의 계획을 만들 필요가 없습니다. 계획은 구독 간에 이동할 수 없습니다. 계획이 속한 구독을 변경하려면 [기존 계획을 삭제](#work-with-ddos-protection-plans)하고 새 계획을 만들어야 합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
2. *DDoS*를 검색합니다. **DDoS 보호 계획이** 검색 결과에 표시 되 면 선택 합니다.
3. **만들기**를 선택합니다.
4. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력 또는 선택한 다음, **만들기**를 선택합니다.

    |설정        |값                                              |
    |---------      |---------                                          |
    |속성           | myDdosProtectionPlan                              |
    |Subscription   | 구독을 선택합니다.                         |
    |Resource group | **새로 만들기** 를 선택 하 고 *myresourcegroup* 을 입력 합니다. |
    |위치       | 미국 동부                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>새 가상 네트워크에 대한 DDoS 사용

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택 합니다.
3. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력 또는 선택하고, 나머지 기본값을 적용한 다음, **만들기**를 선택합니다.

    | 설정         | 값                                                        |
    | ---------       | ---------                                                    |
    | 속성            | myVirtualNetwork                                             |
    | Subscription    | 구독을 선택합니다.                                    |
    | Resource group  | **기존 항목 사용**을 선택한 다음, **myResourceGroup**을 선택합니다. |
    | 위치        | 미국 동부                                                      |
    | DDoS Protection 표준 | **사용**을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.|

가상 네트워크에 대해 DDoS 표준을 사용하도록 설정하면 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. DDoS 표준을 사용하도록 설정된 가상 네트워크를 이동해야 하는 경우 먼저 DDoS 표준을 사용하지 않도록 설정하고, 가상 네트워크를 이동한 다음, DDoS 표준을 사용하도록 설정합니다. 이동 후에는 가상 네트워크의 모든 보호된 공용 IP 주소에 대한 자동 조정된 정책 임계값이 다시 설정됩니다.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>기존 가상 네트워크에 DDoS 사용

1. 기존 DDoS 보호 계획이 없는 경우 [DDoS 보호 계획 만들기](#create-a-ddos-protection-plan)의 단계를 완료하여 DDoS 보호 계획을 만듭니다.
2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
3. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자**에서 DDoS 보호 표준을 사용하도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
4. **설정** 아래에서 **DDoS 보호**를 선택합니다.
5. **표준**을 선택합니다. **DDoS 보호 계획** 아래에서 기존 DDoS 보호 계획 또는 1단계에서 만든 계획을 선택한 다음, **저장**을 선택합니다. 선택한 계획은 가상 네트워크와 동일하거나 다른 구독에 있을 수 있지만, 두 구독은 모두 동일한 Azure Active Directory 테넌트에 연결되어야 합니다.

**명령** 
- Azure CLI: [az network ddos create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- Powershell: [AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>가상 네트워크에 DDoS 사용 안 함

1. 포털 위쪽의 **리소스, 서비스 및 문서 검색 상자**에서 DDoS 보호 표준을 사용하지 않도록 설정하려는 가상 네트워크의 이름을 입력합니다. 가상 네트워크의 이름이 검색 결과에 표시되면 선택합니다.
2. **DDoS Protection Standard에서**를 선택 하 고 **사용 안 함**을 선택 합니다.

**명령** 
- Azure CLI: [az network ddos delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- Powershell: [AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>DDoS 보호 계획 작업

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *DDoS*를 입력합니다. **DDoS 보호 계획**이 결과에 표시되면 선택합니다.
3. 목록에서 보려는 보호 계획을 선택합니다.
4. 계획과 연결된 모든 가상 네트워크가 나열됩니다.
5. 계획을 삭제하려면 먼저 모든 가상 네트워크와의 연결을 끊어야 합니다. 가상 네트워크에서 계획을 분리하려면 [가상 네트워크에 DDoS 사용 안 함](#disable-ddos-for-a-virtual-network)을 참조하세요.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 경고 구성

Azure Monitor 경고 구성을 사용하면 공격 중에 활성 완화가 있는 경우 알리는 데 사용할 수 있는 DDoS 보호 메트릭 중 하나를 선택할 수 있습니다. 조건이 충족되면 지정된 주소에서 경고 메일을 받습니다.

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **공유 서비스** 아래에서 **메트릭**을 선택합니다.
4. 값을 직접 입력 또는 선택하거나 다음 예의 값을 입력하고, 나머지 기본값을 적용한 다음, **확인**을 선택합니다.

    |설정                  |값                                                                                               |
    |---------                |---------                                                                                           |
    |속성                     | myDdosAlert                                                                                        |
    |구독             | 경고를 받으려는 공용 IP 주소가 포함된 구독을 선택합니다.        |
    |리소스 그룹           | 경고를 받으려는 공용 IP 주소가 포함된 리소스 그룹을 선택합니다.      |
    |리소스                 | 경고를 받으려는 공용 IP 주소가 포함된 공용 IP 주소를 선택합니다. DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service 환경 및 Azure VPN Gateway를 제외하고 [Azure 서비스에 대한 가상 네트워크](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)에 나열된 Resource Manager(클래식이 아님)를 통해 배포된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.                   |
    |메트릭                   | DDoS 공격 진행 여부                                                                            |
    |임계값                | 1 - **1**은 공격을 받고 있음을 나타냅니다. **0**은 공격을 받고 있지 않음을 나타냅니다.                         |
    |기간                   | 선택한 값을 선택합니다.                                                                   |
    |이메일을 통해 알림         | 확인란을 선택합니다.                                                                                  |
    |추가 관리자 | 구독에 대한 이메일 소유자, 기여자 또는 읽기 권한자가 아닌 경우 이메일 주소를 입력합니다. |

    공격 탐지 후 몇 분 내에 다음 그림과 비슷한 Azure Monitor 메트릭에서 이메일을 받게 됩니다.

    ![공격 경고](./media/manage-ddos-protection/ddos-alert.png)


DDoS 공격을 시뮬레이션하여 경고의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](#validate-ddos-detection)를 참조하세요.

경고를 만들기 위해 [웹후크 구성](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 대해 자세히 알아볼 수도 있습니다.

## <a name="use-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 사용

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. 이러한 원격 분석 기능은 공용 IP 주소의 위험이 완화되는 동안에만 사용할 수 있습니다. 공격이 완화되기 전 또는 후에는 원격 분석이 표시되지 않습니다.

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **공유 서비스**아래에서 **메트릭**을 선택 합니다.
4. 원격 분석하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹**을 선택합니다.
5. **리소스 종류**에 대해 **공용 IP 주소**를 선택한 다음, 원격 분석하려는 특정 공용 IP 주소를 선택합니다.
6. 일련의 **사용 가능한 메트릭**이 화면 왼쪽에 표시됩니다. 이러한 메트릭을 선택할 경우 개요 화면의 **Azure Monitor 메트릭 차트**에 그래프로 표시됩니다.
7. **집계** 유형을 **Max** 로 선택 합니다.

메트릭 이름은 다양한 패킷 유형과 바이트 및 패킷을 나타내며, 각 메트릭에서 태그 이름의 기본 구조는 다음과 같습니다.

- **Dropped 태그 이름**(예: **Inbound Packets Dropped DDoS**): DDoS 보호 시스템에서 삭제/스크럽한 패킷의 수입니다.
- **Forwarded 태그 이름**(예: **Inbound Packets Forwarded DDoS**): DDoS 시스템에서 대상 VIP로 전달한 패킷의 수입니다(필터링되지 않은 트래픽).
- **태그 이름 없음**(예: **Inbound Packets DDoS**): 스크럽 시스템으로 들어온 패킷의 총 수입니다(삭제 및 전달된 패킷의 합계를 나타냄).

DDoS 공격을 시뮬레이션하여 원격 분석의 유효성을 검사하려면 [DDoS 탐지 유효성 검사](#validate-ddos-detection)를 참조하세요.

## <a name="view-ddos-mitigation-policies"></a>DDoS 완화 정책 보기

DDoS 보호 표준은 DDoS를 사용하도록 설정된 가상 네트워크에서 보호되는 리소스의 각 공용 IP 주소에 대해 자동 조정된 세 가지 완화 정책(TCP SYN, TCP 및 UDP)을 적용합니다. 다음 그림에 표시 된 것 처럼 DDoS 완화 및 **인바운드 UDP 패킷을** 트리거하여 **집계** 유형을 포함 하는 DDoS 완화 메트릭을 ' 최대 '로 트리거할 **인바운드 TCP 패킷을** 선택 하 여 정책 임계값을 볼 수 있습니다.

![완화 정책 보기](./media/manage-ddos-protection/view-mitigation-policies.png)

정책 임계값은 Azure 기계 학습 기반의 네트워크 트래픽 프로파일링을 통해 자동으로 구성됩니다. 정책 임계값을 위반한 경우에만 공격을 받고 있는 IP 주소에 대해 DDoS 완화가 발생합니다.

## <a name="configure-ddos-attack-analytics"></a>DDoS 공격 분석 구성
Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. 

## <a name="configure-ddos-attack-mitigation-reports"></a>DDoS 공격 완화 보고서 구성
공격 완화 보고서는 집계된 Netflow 프로토콜 데이터를 사용하여 리소스에 대한 공격에 대한 자세한 정보를 제공합니다. 공용 IP 리소스가 공격을 받을 때마다 완화가 시작되는 즉시 보고서 생성이 시작됩니다. 5분마다 생성되는 증분 보고서와 전체 완화 기간에 대한 사후 완화 보고서가 있습니다. 이렇게 하면 DDoS 공격이 더 오랜 시간 지속되는 경우 5분마다 완화 보고서의 최신 스냅샷을 볼 수 있고 공격 완화가 완료되면 전체 요약을 볼 수 있습니다. 

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **설정** 아래에서 **진단 설정**을 선택합니다.
4. 로깅하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹**을 선택합니다.
5. **리소스 종류**에 대해 **공용 IP 주소**를 선택한 다음, 메트릭을 로깅하려는 특정 공용 IP 주소를 선택합니다.
6. **DDoSMitigationReports 로그를 수집하도록 진단 켜기**를 선택한 다음, 다음 옵션 중에서 필요한 만큼 선택합니다.

    - **스토리지 계정에 보관**: 데이터가 Azure Storage 계정에 기록됩니다. 이 옵션에 대해 자세히 알아보려면 [리소스 로그 보관](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.
    - **이벤트 허브로의 스트림**: 로그 수신기에서 Azure Event Hub를 사용하여 로그를 선택할 수 있도록 합니다. 이벤트 허브는 Splunk 또는 기타 SIEM 시스템과 통합할 수 있습니다. 이 옵션에 대 한 자세한 내용은 [이벤트 허브로 리소스 로그 스트리밍](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.
    - **Log Analytics 보내기**: Azure Monitor 서비스에 로그를 기록 합니다. 이 옵션에 대 한 자세한 내용은 [Azure Monitor 로그에서 사용할 로그 수집](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.

증분 및 사후 공격 완화 보고서 모두에 포함된 필드는 다음과 같습니다
- 공격 벡터
- 트래픽 통계
- 패킷이 삭제된 이유
- 관련 프로토콜
- 상위 10개 원본 국가 또는 지역
- 상위 10개 원본 ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS 공격 완화 흐름 로그 구성
공격 완화 흐름 로그를 사용하면 활성 DDoS 공격 중에 삭제된 트래픽, 전달된 트래픽 및 기타 흥미로운 데이터 요소를 거의 실시간으로 검토할 수 있습니다. 이벤트 허브를 통해 이 데이터의 지속적인 스트림을 SIEM 시스템으로 수집하여 거의 실시간으로 모니터링하고, 잠재적인 작업을 수행하며, 방어 작업의 필요성을 해결할 수 있습니다. 

1. 포털의 왼쪽 위에서 **모든 서비스**를 선택합니다.
2. **필터** 상자에서 *모니터*를 입력합니다. 결과에 **모니터**가 표시되면 해당 모니터를 선택합니다.
3. **설정** 아래에서 **진단 설정**을 선택합니다.
4. 로깅하려는 공용 IP 주소가 포함된 **구독** 및 **리소스 그룹**을 선택합니다.
5. **리소스 종류**에 대해 **공용 IP 주소**를 선택한 다음, 메트릭을 로깅하려는 특정 공용 IP 주소를 선택합니다.
6. **DDoSMitigationFlowLogs 로그를 수집하도록 진단 켜기**를 선택한 다음, 다음 옵션 중에서 필요한 만큼 선택합니다.

    - **스토리지 계정에 보관**: 데이터가 Azure Storage 계정에 기록됩니다. 이 옵션에 대해 자세히 알아보려면 [리소스 로그 보관](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.
    - **이벤트 허브로의 스트림**: 로그 수신기에서 Azure Event Hub를 사용하여 로그를 선택할 수 있도록 합니다. 이벤트 허브는 Splunk 또는 기타 SIEM 시스템과 통합할 수 있습니다. 이 옵션에 대 한 자세한 내용은 [이벤트 허브로 리소스 로그 스트리밍](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.
    - **Log Analytics 보내기**: Azure Monitor 서비스에 로그를 기록 합니다. 이 옵션에 대 한 자세한 내용은 [Azure Monitor 로그에서 사용할 로그 수집](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.
1. Azure 분석 대시보드에서 흐름 로그 데이터를 보려면 https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip에서 샘플 대시보드를 가져올 수 있습니다.

흐름 로그에 포함된 필드는 다음과 같습니다. 
- 원본 IP
- 대상 IP
- 원본 포트 
- 대상 포트 
- 프로토콜 유형 
- 완화 중에 수행된 작업

공격 분석은 공용 IP 주소의 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정한 경우에만 작동 합니다. 

## <a name="validate-ddos-detection"></a>DDoS 탐지 유효성 검사

Microsoft는 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)와 협력하여 시뮬레이션을 위해 DDoS Protection을 사용하도록 설정된 공용 IP 주소에 대한 트래픽을 생성할 수 있는 인터페이스를 구축했습니다. BreakPoint Cloud 시뮬레이션을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- Microsoft Azure DDoS Protection에서 DDoS 공격으로부터 Azure 리소스를 보호하는 방법을 확인합니다.
- DDoS 공격을 받는 동안 인시던트 응답 프로세스를 최적화합니다.
- DDoS 규정 준수 문서화
- 네트워크 보안 팀을 교육합니다.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Azure Security Center에서 DDoS protection 경고 보기

Azure Security Center는 문제를 조사 하 고 수정 하는 데 도움이 되는 정보를 제공 하는 [보안 경고](/azure/security-center/security-center-managing-and-responding-alerts)의 목록을 제공 합니다. 이 기능을 사용 하면 DDoS 공격 관련 경고 및 가까운 공격을 완화 하기 위해 수행 하는 작업을 포함 하 여 경고에 대 한 통합 된 보기를 얻을 수 있습니다.
DDoS 공격 감지 및 완화에 대해 다음과 같은 두 가지 특정 경고가 표시 됩니다.

- **공용 ip에 대 한 DDoS 공격 검색**:이 경고는 DDoS protection 서비스에서 공용 ip 주소 중 하나가 DDoS 공격의 대상인 것을 감지할 때 생성 됩니다.
- **공용 ip에 대 한 DDoS 공격 완화**:이 경고는 공용 ip 주소에 대 한 공격이 완화 된 경우에 생성 됩니다.
경고를 보려면 Azure Portal에서 **Security Center** 를 엽니다. **위협 방지**에서 **보안 경고**를 선택 합니다. 다음 스크린샷은 DDoS 공격 경고의 예를 보여 줍니다.

![Azure Security Center의 DDoS 경고](./media/manage-ddos-protection/ddos-alert-asc.png)

경고에는 공격에 포함 된 공용 IP 주소, 지역 및 위협 인텔리전스 정보 및 재구성 단계에 대 한 일반 정보가 포함 됩니다.

## <a name="permissions"></a>사용 권한

DDoS 보호 계획을 사용하려면 다음 표에 나열된 적절한 작업이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당해야 합니다.

| 작업                                            | 속성                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS 보호 계획 읽기              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS 보호 계획 만들기 또는 업데이트  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS 보호 계획 삭제            |
| Microsoft.Network/ddosProtectionPlans/join/action | DDoS 보호 계획에 조인              |

가상 네트워크에 DDoS 보호를 사용하도록 설정하려면 계정에도 [가상 네트워크에 적절한 작업](manage-virtual-network.md#permissions)을 할당해야 합니다.

## <a name="next-steps"></a>다음 단계

- 가상 네트워크에 대 한 [Azure Policy](policy-samples.md) 정의 만들기 및 할당
