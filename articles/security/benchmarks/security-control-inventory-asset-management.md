---
title: Azure 보안 제어-인벤토리 및 자산 관리
description: 보안 제어 인벤토리 및 자산 관리
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930070"
---
# <a name="security-control-inventory-and-asset-management"></a>보안 제어: 인벤토리 및 자산 관리

인벤토리 및 자산 관리 권장 사항은 권한 있는 리소스에만 액세스할 수 있도록 모든 Azure 리소스를 적극적으로 관리 (인벤토리, 추적 및 수정) 하는 것과 관련 된 문제를 해결 하 고, 권한 없는 리소스와 관리 되지 않는 리소스는 식별 되 고 제거 됩니다.

## <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery 사용

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Azure 리소스 그래프를 사용 하 여 구독 내에서 계산, 저장소, 네트워크, 포트, 프로토콜 등의 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다.  테넌트에서 적절 한 (읽기) 권한을 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 확인 하는 방법:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC 이해:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타 데이터 유지 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.2 | 1.5 | Customer |

Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 하는 메타 데이터를 제공 합니다.

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한이 없는 Azure 리소스를 삭제 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.3 | 1.6 | Customer |

태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정 하 고 권한이 없는 리소스가 적시에 구독에서 삭제 되도록 합니다.

추가 Azure 구독을 만드는 방법:

https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹를 만드는 방법:

https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인 된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.4 | 2.1 | Customer |

계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어를 정의 합니다.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인 되지 않은 Azure 리소스에 대 한 모니터링

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.5 | 2.3, 2.4 | Customer |

Azure Policy를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인 &nbsp;확인 합니다.

Azure Policy를 구성 하 고 관리 하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph를 사용 하 여 쿼리를 만드는 방법:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 계산 리소스 내에서 승인 되지 않은 소프트웨어 응용 프로그램 모니터링

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.6 | 2.3/2.4 | Customer |

Azure 가상 컴퓨터 인벤토리를 사용 하 여 Virtual Machines의 모든 소프트웨어에 대 한 정보 수집을 자동화할 수 있습니다. Azure Portal에서 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간을 사용할 수 있습니다. 설치 날짜 및 기타 정보에 액세스 하려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져옵니다.

Azure 가상 컴퓨터 인벤토리를 사용 하도록 설정 하는 방법:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인 되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.7 | 2.5 | Customer |

Azure Security Center의 변경 내용 추적 (파일 무결성 모니터링) 및 가상 컴퓨터 인벤토리를 사용 하 여 Virtual Machines에 설치 된 모든 소프트웨어를 식별할 수 있습니다. 권한이 없는 소프트웨어를 제거 하는 사용자 고유의 프로세스를 구현할 수 있습니다. 타사 솔루션을 사용 하 여 승인 되지 않은 소프트웨어를 식별할 수도 있습니다.

파일 무결성 모니터링을 사용 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Azure 변경 내용 추적 이해:

https://docs.microsoft.com/azure/automation/change-tracking

Azure 가상 컴퓨터 인벤토리를 사용 하도록 설정 하는 방법:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: 승인 된 응용 프로그램만 사용 하십시오.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.8 | 2.6 | Customer |

Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한 있는 소프트웨어만 실행 되 고 권한이 없는 모든 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단 합니다.

Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: 승인 된 Azure 서비스만 사용 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.9 | 2.6 | Customer |

Azure Policy를 사용 하 여 사용자 환경에서 프로 비전 할 수 있는 서비스를 제한할 수 있습니다.

Azure Policy를 구성 하 고 관리 하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy를 사용 하 여 특정 리소스 종류를 거부 하는 방법:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: 승인 된 응용 프로그램 목록 구현

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.1 | 2.7 | Customer |

Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하 여 규칙을 적용할 수 있거나 적용할 수 없는 파일 형식을 지정 합니다.

요구 사항에 맞지 않는 경우 타사 솔루션을 구현 합니다.

Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 Azure Resource Manager 상호 작용할 수 있도록 제한

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.11 | 2.8 | Customer |

Azure 조건부 액세스를 사용 하 여 &quot;Microsoft Azure 관리&quot; 앱에 대 한 액세스&quot; &quot;를 구성 하 여 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

Azure Resource Manager에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 계산 리소스 내에서 스크립트를 실행 하는 기능을 제한 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.12 | 2.8 | Customer |

운영 체제별 구성 또는 타사 리소스를 사용 하 여 Azure compute 리소스 내에서 스크립트를 실행 하는 사용자의 기능을 제한 합니다.

예를 들어 Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법은 다음과 같습니다.

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 6.13 | 2.9 | Customer |

비즈니스 운영에 필요 하지만 조직에서 더 높은 위험을 초래할 수 있는 소프트웨어는 자체 가상 머신 및/또는 가상 네트워크 내에서 격리 되 고 Azure 방화벽 또는 네트워크 보안 그룹을 사용 하 여 충분히 안전 하 게 보호 되어야 합니다.

가상 네트워크를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용 하 여 NSG를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>다음 단계

다음 보안 제어: [보안 구성](security-control-secure-configuration.md) 을 참조 하세요.
