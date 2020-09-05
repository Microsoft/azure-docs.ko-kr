---
title: Azure VM을 만들 때 백업 사용
description: Azure Backup를 사용 하 여 Azure VM을 만들 때 백업을 사용 하도록 설정 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: bbc00239a34fc0eb88991fcabd91c5a0eb7dbea7
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892306"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Azure VM을 만들 때 백업 사용

Azure Backup 서비스를 사용 하 여 Azure Vm (가상 머신)을 백업 합니다. Vm은 백업 정책에 지정 된 일정에 따라 백업 되며 백업에서 복구 지점이 생성 됩니다. 복구 지점은 Recovery Services 자격 증명 모음에 저장 됩니다.

이 문서에서는 Azure Portal에서 VM (가상 머신)을 만들 때 백업을 사용 하도록 설정 하는 방법에 대해 자세히 설명 합니다.  

## <a name="before-you-start"></a>시작하기 전에

- VM을 만들 때 백업을 사용 하도록 설정 하는 경우 지원 되는 운영 체제를 [확인](backup-support-matrix-iaas.md#supported-backup-actions) 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

계정에 아직 로그인 하지 않은 경우 [Azure Portal](https://portal.azure.com)에 로그인 합니다.

## <a name="create-a-vm-with-backup-configured"></a>백업이 구성 된 VM 만들기

1. Azure Portal에서 **리소스 만들기**를 클릭 합니다.

2. Azure Marketplace에서 **Compute**를 클릭 한 다음 VM 이미지를 선택 합니다.

3. [Windows](../virtual-machines/windows/quick-create-portal.md) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md) 지침에 따라 VM을 설정 합니다.

4. **관리** 탭의 **백업 사용**에서 **켜기**를 클릭 합니다.
5. Recovery Services 자격 증명 모음에 백업을 Azure Backup 합니다. 기존 자격 증명 모음이 없는 경우 **새로 만들기** 를 클릭 합니다.
6. 제안 된 자격 증명 모음 이름을 그대로 적용 하거나 고유한 이름을 지정 합니다.
7. 자격 증명 모음을 배치할 리소스 그룹을 지정 하거나 만듭니다. 리소스 그룹 자격 증명 모음은 VM 리소스 그룹과 다를 수 있습니다.

    ![VM에 대 한 백업 사용](./media/backup-during-vm-creation/enable-backup.png)

8. 기본 백업 정책을 적용 하거나 설정을 수정 합니다.
    - 백업 정책은 VM의 백업 스냅숏을 가져오는 빈도와 백업 복사본을 보관 하는 기간을 지정 합니다.
    - 기본 정책은 하루에 한 번 VM을 백업합니다.
    - 매일 또는 매주 백업을 수행 하도록 Azure VM에 대 한 고유한 백업 정책을 사용자 지정할 수 있습니다.
    - Azure Vm에 대 한 백업 고려 사항에 대해 [자세히 알아보세요](backup-azure-vms-introduction.md#backup-and-restore-considerations) .
    - 즉시 복원 기능에 [대해 자세히 알아보세요](backup-instant-restore-capability.md) .

      ![기본 백업 정책](./media/backup-during-vm-creation/daily-policy.png)

>[!NOTE]
>[SSE 및 PMK는](backup-encryption.md) Azure vm에 대 한 기본 암호화 방법입니다. Azure Backup은 이러한 Azure Vm의 백업 및 복원을 지원 합니다.

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Virtual Machines에 대 한 Azure Backup 리소스 그룹

Backup 서비스는 VM의 리소스 그룹과 다른 별도 리소스 그룹 (RG)을 만들어 복원 지점 컬렉션 (RPC)을 저장 합니다. RPC는 관리 되는 Vm의 인스턴트 복구 위치를 보관 합니다. 백업 서비스에서 만든 리소스 그룹의 기본 명명 형식은 `AzureBackupRG_<Geo>_<number>` 입니다. 예: *AzureBackupRG_northeurope_1*. 이제 Azure Backup에서 만든 리소스 그룹 이름을 사용자 지정할 수 있습니다.

주의할 사항:

1. RG의 기본 이름을 사용 하거나 회사 요구 사항에 따라 편집할 수 있습니다.
2. VM 백업 정책을 만드는 동안 RG 이름 패턴을 입력으로 제공 합니다. RG 이름은 형식 이어야 `<alpha-numeric string>* n <alpha-numeric string>` 합니다. ' n '은 1부터 시작 하는 정수로 바뀌고 첫 번째 RG full 인 경우 규모를 확장 하는 데 사용 됩니다. 1 RG는 현재 최대 600의 Rpc를 사용할 수 있습니다.
              ![정책을 만들 때 이름 선택](./media/backup-during-vm-creation/create-policy.png)
3. 패턴은 아래의 RG 명명 규칙을 따라야 하며 총 길이는 최대 허용 RG 이름 길이를 초과 해서는 안 됩니다.
    1. 리소스 그룹 이름에는 영숫자, 마침표, 밑줄, 하이픈 및 괄호만 사용할 수 있습니다. 일정 기간 내에 종료할 수 없습니다.
    2. 리소스 그룹 이름은 RG 이름과 접미사를 포함 하 여 최대 74 자까지 포함할 수 있습니다.
4. 첫 번째는 필수 항목이 며 `<alpha-numeric-string>` ' n ' 뒤의 두 번째는 선택 사항입니다. 이는 사용자 지정 된 이름을 지정 하는 경우에만 적용 됩니다. 텍스트 상자 중 하나에 아무것도 입력 하지 않으면 기본 이름이 사용 됩니다.
5. 필요한 경우 정책을 수정 하 여 RG 이름을 편집할 수 있습니다. 이름 패턴이 변경 되 면 새 RG에 새 Rp가 만들어집니다. 그러나 RP 컬렉션은 리소스 이동을 지원 하지 않으므로 이전 RPs는 여전히 이전 RG에 상주 하 고 이동 되지 않습니다. 결과적으로, 해당 지점이 만료 되 면 RPs가 가비지 수집 됩니다.
![정책을 수정할 때 이름 변경](./media/backup-during-vm-creation/modify-policy.png)
6. 백업 서비스에서 사용 하기 위해 만든 리소스 그룹을 잠그지 않는 것이 좋습니다.

PowerShell을 사용 하 여 Virtual Machines에 대 한 Azure Backup 리소스 그룹을 구성 하려면 [스냅숏 보존 중 Azure Backup 리소스 그룹 만들기](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention)를 참조 하세요.

## <a name="start-a-backup-after-creating-the-vm"></a>VM을 만든 후 백업 시작

VM 백업은 백업 정책에 따라 실행 됩니다. 그러나 초기 백업을 실행 하는 것이 좋습니다.

VM을 만든 후 다음을 수행 합니다.

1. VM 속성에서 **백업**을 클릭 합니다. 초기 백업이 실행 될 때까지 VM 상태는 초기 백업 보류 중입니다.
2. **지금** 백업을 클릭 하 여 요청 시 백업을 실행 합니다.

    ![주문형 백업 실행](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>리소스 관리자 템플릿을 사용 하 여 보호 된 VM 배포

이전 단계에서는 Azure Portal를 사용 하 여 가상 머신을 만들고 Recovery Services 자격 증명 모음에서 보호 하는 방법을 설명 합니다. 하나 이상의 Vm을 신속 하 게 배포 하 고 Recovery Services 자격 증명 모음에서 보호 하려면 [WINDOWS VM 배포 및 백업 사용](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)템플릿을 참조 하세요.

## <a name="next-steps"></a>다음 단계

VM을 보호 했으므로 이제 VM을 관리 하 고 복원 하는 방법을 알아봅니다.

- [VM 관리 및 모니터링](backup-azure-manage-vms.md)
- [VM 복원](backup-azure-arm-restore-vms.md)

문제가 발생 하는 경우 문제 해결 가이드를 [검토](backup-azure-vms-troubleshoot.md) 합니다.
