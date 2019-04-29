---
title: RBAC(역할 기반 액세스 제어)를 사용하여 Azure Site Recovery 액세스 관리 | Microsoft Docs
description: 이 문서에서는 RBAC(역할 기반 액세스 제어)를 적용하여 Azure Site Recovery 액세스를 관리하는 방법을 설명합니다.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 33fc2cd19152fb6cbbffb106aa058948d39555f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471437"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)를 사용하여 Site Recovery 액세스 관리

Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하여 팀 내 책임을 구분하고, 특정 작업을 수행하는 데 필요한 사용자에게 특정 액세스 권한만 부여할 수 있습니다.

Azure Site Recovery는 Site Recovery 관리 작업을 제어하는 3가지 기본 제공 역할을 제공합니다. [Azure RBAC 기본 제공 역할](../role-based-access-control/built-in-roles.md)에 대해 알아보기

* [Site Recovery 참가자](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - 이 역할에는 Recovery Services 자격 증명 모음에서 Azure Site Recovery 작업을 관리하는 데 필요한 모든 사용 권한이 있습니다. 그러나 이 역할의 사용자는 Recovery Services 자격 증명 모음을 만들거나 삭제할 수 없고 액세스 권한을 다른 사용자에게 할당할 수 없습니다. 이 역할은 애플리케이션이나 전체 조직에 재해 복구를 사용하도록 설정하고 관리할 수 있는 재해 복구 관리자에 대해 가장 적합합니다.
* [Site Recovery 연산자](../role-based-access-control/built-in-roles.md#site-recovery-operator) - 이 역할에는 장애 조치 및 장애 복구 작업을 실행하고 관리하는 사용 권한이 있습니다. 이 역할의 사용자는 복제를 활성화하거나 비활성화할 수 없고, 자격 증명 모음을 만들거나 삭제할 수 없으며, 새로운 인프라를 등록하거나 다른 사용자에게 액세스 권한을 할당할 수 없습니다. 이 역할은 실제 또는 시뮬레이션된 재해 상황에서 DR 드릴과 같은 애플리케이션 소유자 및 IT 관리자가 지시하는 경우 가상 머신 또는 애플리케이션을 장애 조치할 수 있는 재해 복구 연산자에 가장 적합합니다. 재해를 해결한 후에 게시 DR 연산자는 가상 머신을 다시 보호하고 장애 복구할 수 있습니다.
* [Site Recovery 읽기 권한자](../role-based-access-control/built-in-roles.md#site-recovery-reader) - 이 역할은 모든 Site Recovery 관리 작업을 볼 수 있는 권한을 갖습니다. 이 역할은 현재 보호 상태를 모니터링하고 필요한 경우 지원 티켓을 발행할 수 있는 IT 모니터링 경영자에게 가장 적합합니다.

더 많은 제어를 위해 사용자 고유의 역할을 정의하려는 경우 Azure의 [사용자 지정 역할 작성](../role-based-access-control/custom-roles.md) 방법을 참조하세요.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>새 가상 머신에 복제를 사용하는 데 필요한 권한
Azure Site Recovery를 사용하여 새 Virtual Machine을 Azure에 복제할 때 연결된 사용자의 액세스 수준이 유효한지 확인하여 사용자에게 Site Recovery에 제공되는 Azure 리소스를 사용하는 데 필요한 권한이 있는지 검토됩니다.

새 가상 머신에 대한 복제를 사용하도록 설정하려면 사용자에게 다음 권한이 있어야 합니다.
* 선택한 리소스 그룹에서 가상 머신을 만들 수 있는 권한
* 선택한 가상 네트워크에서 가상 컴퓨터를 만들 수 있는 권한
* 선택한 Storage 계정에 대한 쓰기 권한

사용자는 새 가상 머신의 복제를 완료하려면 다음 권한이 필요합니다.

> [!IMPORTANT]
>리소스 배포에 사용되는 배포 모델(Resource Manager/클래식)마다 적절한 권한이 추가되는지 확인합니다.

| **리소스 종류** | **배포 모델** | **사용 권한** |
| --- | --- | --- |
| 컴퓨팅 | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | 클래식 | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| 네트워크 | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | 클래식 | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | 클래식 | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| 리소스 그룹 | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Resource Manager 및 클래식 배포 모델에 대해 각각 'Virtual Machine 참여자' 및 '클래식 Virtual Machine 참여자' [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md): Azure Portal에서 RBAC를 시작합니다.
* 다음을 사용하여 액세스를 관리하는 방법에 대해 알아봅니다.
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [역할 기반 액세스 제어 문제 해결](../role-based-access-control/troubleshooting.md): 일반적인 문제 해결에 대한 제안을 구합니다.
