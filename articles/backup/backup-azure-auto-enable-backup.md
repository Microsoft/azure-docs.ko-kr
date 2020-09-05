---
title: Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화
description: Azure Policy를 사용 하 여 지정 된 범위에서 만든 모든 Vm에 대 한 백업을 자동으로 활성화 하는 방법을 설명 하는 문서입니다.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826075"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화

조직에서 백업 또는 규정 준수 관리자의 주요 업무 중 하나는 업무상 중요 한 모든 컴퓨터가 적절 한 보존으로 백업 되도록 하는 것입니다.

현재 Azure Backup는 **구독 또는 리소스 그룹 내의 지정 된 위치에 있는 모든 Azure vm**에 할당 될 수 있는 기본 제공 정책 (Azure Policy 사용)을 제공 합니다. 이 정책이 지정 된 범위에 할당 되 면 해당 범위에서 만들어진 모든 새 Vm은 **동일한 위치 및 구독의 기존 자격 증명 모음**에 백업 하도록 자동으로 구성 됩니다. 사용자는 백업 된 Vm을 연결할 자격 증명 모음 및 보존 정책을 지정할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 기본 제공 정책은 현재 Azure Vm에 대해서만 지원 됩니다. 사용자는 할당 중에 지정 된 보존 정책이 VM 보존 정책 인지 확인 해야 합니다. 이 정책에서 지 원하는 모든 VM Sku를 보려면 [이](./backup-azure-policy-supported-skus.md) 문서를 참조 하세요.

* 정책은 한 번에 하나의 위치 및 구독에 할당 될 수 있습니다. 위치 및 구독의 Vm에 대 한 백업을 사용 하도록 설정 하려면 각 위치 및 구독의 조합에 대해 하나씩, 정책 할당의 여러 인스턴스를 만들어야 합니다.

* 지정 된 자격 증명 모음 및 백업용으로 구성 된 Vm은 다른 리소스 그룹에 있을 수 있습니다.

* 관리 그룹 범위는 현재 지원 되지 않습니다.

* 기본 제공 정책은 현재 국가별 클라우드에서 사용할 수 없습니다.

## <a name="using-the-built-in-policy"></a>기본 제공 정책 사용

필요한 범위에 정책을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인 하 여 **정책** 대시보드로 이동 합니다.
1. 왼쪽 메뉴에서 **정의** 를 선택 하 여 Azure 리소스의 모든 기본 제공 정책 목록을 가져옵니다.
1. **Category = Backup**의 목록을 필터링 합니다. ' 동일한 위치의 기존 중앙 자격 증명 모음에 대 한 위치의 Vm에 백업 구성 ' 이라는 단일 정책으로 필터링 된 목록이 표시 됩니다.
![정책 대시보드](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. 정책 이름을 선택 합니다. 이 정책에 대 한 세부 정의로 리디렉션됩니다.
![정책 정의 창](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. 창 위쪽에서 **할당** 단추를 선택 합니다. 그러면 **정책 할당** 창으로 리디렉션됩니다.
1. **기본**에서 **범위** 필드 옆에 있는 세 개의 점을 선택 합니다. 그러면 적용할 정책의 구독을 선택할 수 있는 올바른 컨텍스트 창이 열립니다. 또한 필요에 따라 리소스 그룹을 선택 하 여 특정 리소스 그룹의 Vm에 대해서만 정책이 적용 되도록 할 수 있습니다.
![정책 할당 기본 사항](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. **매개 변수** 탭의 드롭다운에서 위치를 선택 하 고 범위의 vm이 연결 되어야 하는 자격 증명 모음 및 백업 정책을 선택 합니다.
![정책 할당 매개 변수](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. **효과가** deployIfNotExists로 설정 되었는지 확인 합니다.
1. **검토 + 만들기** 로 이동 하 고 **만들기**를 선택 합니다.

> [!NOTE]
>
> Azure Policy는 [관리](../governance/policy/how-to/remediate-resources.md)를 사용 하 여 기존 vm 에서도 사용할 수 있습니다.

> [!NOTE]
>
> 이 정책은 한 번에 200 개 이상의 Vm에 할당 되는 것이 좋습니다. 정책이 200 개 이상의 Vm에 할당 되 면 백업이 일정에 지정 된 시간 보다 몇 시간 후에 트리거될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Policy에 대 한 자세한 정보](../governance/policy/overview.md)
