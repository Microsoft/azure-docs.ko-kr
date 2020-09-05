---
title: 자격 증명 모음 Azure Backup Recovery Services 이동 하는 방법
description: Azure 구독 및 리소스 그룹에서 Recovery Services 자격 증명 모음을 이동 하는 방법에 대 한 지침입니다.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 69021131f12b57aedcd531997029858b0722933f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181513"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Azure 구독 및 리소스 그룹 간에 Recovery Services 자격 증명 모음 이동

이 문서에서는 Azure 구독 간에 또는 동일한 구독의 다른 리소스 그룹에 Azure Backup에 대해 구성된 Recovery Services 자격 증명 모음을 이동하는 방법을 설명합니다. Azure Portal 또는 PowerShell을 사용하여 Recovery Services 자격 증명 모음을 이동할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

Recovery Services 자격 증명 모음에 대 한 리소스 이동은 오스트레일리아 동부에서 지원 됩니다. 오스트레일리아 남부 동부, 캐나다 중부, 캐나다 동부, 남부 동아시아, 동아시아, 미국 중부, 미국 중 북부, 미국 동부, 미국 동부 2, 미국 서 부, 미국 서 부, 미국 서 부 2, 미국 서 부, 미국 서 부 2, 브라질 남부, 인도 서 부, 인도 남부, 일본 서 부, 일본 서 부, 대한민국 중부, 대한민국 , 북부 유럽, 유럽 서부, 남아프리카 북부, 남아프리카 공화국 서 부, 영국 남부 및 영국 서부.

## <a name="unsupported-regions"></a>지원되지 않는 지역

프랑스 중부, 프랑스 남부, 독일 북동쪽, 독일 중부, US Gov 아이오와, 중국 북부, 중국 North2, 중국 동부, 중국 동부 2,

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Recovery Services 자격 증명 모음 이동에 대 한 필수 조건

- 리소스 그룹 간 자격 증명 모음 이동 중에는 쓰기 및 삭제 작업을 방지 하기 위해 원본 및 대상 리소스 그룹이 모두 잠깁니다. 자세한 내용은 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 자격 증명 모음을 이동할 수 있는 권한은 관리자 구독에만 있습니다.
- 구독에서 자격 증명 모음을 이동 하는 경우 대상 구독은 원본 구독과 동일한 테 넌 트에 상주해 야 하며 상태를 사용 하도록 설정 해야 합니다.
- 대상 리소스 그룹에 쓰기 작업을 수행할 수 있는 권한이 있어야 합니다.
- 자격 증명 모음을 이동하면 리소스 그룹이 변경됩니다. Recovery Services 자격 증명 모음은 동일한 위치에 있으며 변경할 수 없습니다.
- 한 번에 하나의 지역에서 하나의 Recovery Services 자격 증명 모음만 이동할 수 있습니다.
- VM이 구독 또는 새 리소스 그룹에 대해 Recovery Services 자격 증명 모음과 함께 이동 하지 않는 경우 만료 될 때까지 현재 VM 복구 지점은 자격 증명 모음에 그대로 유지 됩니다.
- VM을 자격 증명 모음과 함께 이동했는지 여부에 관계없이 자격 증명 모음의 유지된 백업 기록에서 항상 VM을 복원할 수 있습니다.
- Azure Disk Encryption 키 자격 증명 모음 및 Vm이 동일한 Azure 지역 및 구독에 있어야 합니다.
- 관리 디스크가 있는 가상 머신을 이동하려면 이 [문서](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)를 참조하세요.
- 클래식 모델을 통해 배포 된 리소스를 이동 하는 옵션은 구독 내에서 리소스를 이동 하는지 아니면 새 구독으로 이동 하는지에 따라 달라 집니다. 자세한 내용은 [이 문서](../azure-resource-manager/management/move-resource-group-and-subscription.md)를 참조하세요.
- 자격 증명 모음에 대해 정의된 백업 정책은 자격 증명 모음이 구독 간에 또는 새 리소스 그룹으로 이동 후에 유지됩니다.
- 다음 유형의 백업 항목을 포함 하는 자격 증명 모음만 이동할 수 있습니다. 아래에 나열 되지 않은 유형의 백업 항목은 모두 중지 되어야 하며 데이터는 자격 증명 모음을 이동 하기 전에 영구적으로 삭제 해야 합니다.
  - Azure Virtual Machines
  - MARS (Microsoft Azure Recovery Services) 에이전트
  - Microsoft Azure Backup 서버 (MABS)
  - DPM(Data Protection Manager)
- 구독에서 VM 백업 데이터를 포함 하는 자격 증명 모음을 이동 하는 경우에는 Vm을 동일한 구독으로 이동 하 고 이전 구독과 동일한 대상 VM 리소스 그룹 이름을 사용 하 여 백업을 계속 진행 해야 합니다.

> [!NOTE]
> Azure 지역에서 Azure Backup에 대 한 Recovery Services 자격 증명 모음 이동은 지원 되지 않습니다.<br><br>
> **Azure Site Recovery**를 사용 하 여 재해 복구를 위해 Vm (Azure IaaS, Hyper-v, VMware) 또는 물리적 컴퓨터를 구성한 경우 이동 작업이 차단 됩니다. Azure Site Recovery 자격 증명 모음을 이동 하려면 [이 문서](../site-recovery/move-vaults-across-regions.md) 를 검토 하 여 자격 증명 모음을 수동으로 이동 하는 방법에 대해 알아보세요.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure Portal를 사용 하 여 Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동

Recovery Services 자격 증명 모음 및 연결 된 리소스를 다른 리소스 그룹으로 이동 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Recovery Services 자격 증명 모음**의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

   ![Recovery Services 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   자격 증명 모음에 대 한 **필수** 정보가 표시 되지 않으면 드롭다운 아이콘을 선택 합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

   ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **리소스 그룹**옆의 **변경** 을 선택 하 여 **리소스 이동** 창을 엽니다.

   ![리소스 그룹 변경](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. **리소스 이동** 창에서 선택한 자격 증명 모음에 대해 다음 그림에 표시 된 것 처럼 확인란을 선택 하 여 선택적 관련 리소스를 이동 하는 것이 좋습니다.

   ![구독 이동](./media/backup-azure-move-recovery-services/move-resource.png)

5. 대상 리소스 그룹을 추가 하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택 하거나 **새 그룹 만들기** 옵션을 선택 합니다.

   ![리소스 만들기](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 리소스 그룹을 추가한 후에 **는 이동 된 리소스와 연결 된 도구와 스크립트가 새 리소스 id를 사용 하도록 업데이트 될 때까지 작동 하지 않는지** 확인 하 고 **확인** 을 선택 하 여 자격 증명 모음 이동을 완료 해야 합니다.

   ![확인 메시지](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure Portal를 사용 하 여 Recovery Services 자격 증명 모음을 다른 구독으로 이동

Recovery Services 자격 증명 모음 및 연결된 해당 리소스를 다른 구독으로 이동할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Recovery Services 자격 증명 모음의 목록을 열고 이동하려는 자격 증명 모음을 선택합니다. 자격 증명 모음 대시보드가 열리면 다음 이미지에 표시된 것처럼 나타납니다.

    ![Recovery Services 자격 증명 모음 열기](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    자격 증명 모음에 대 한 **필수** 정보가 표시 되지 않으면 드롭다운 아이콘을 선택 합니다. 이제 자격 증명 모음에 대한 Essentials 정보가 표시됩니다.

    ![Essentials 정보 탭](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 자격 증명 모음 개요 메뉴에서 **구독**옆의 **변경** 을 선택 하 여 **리소스 이동** 창을 엽니다.

   ![구독 변경](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 이동할 리소스를 선택합니다. 여기에서 **모두 선택** 옵션을 사용하여 나열된 모든 선택적 리소스를 선택하는 것이 좋습니다.

   ![리소스 이동](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 자격 증명 모음을 이동하려는 **구독** 드롭다운 목록에서 대상 구독을 선택합니다.
6. 대상 리소스 그룹을 추가 하려면 **리소스 그룹** 드롭다운 목록에서 기존 리소스 그룹을 선택 하거나 **새 그룹 만들기** 옵션을 선택 합니다.

   ![구독 추가](./media/backup-azure-move-recovery-services/add-subscription.png)

7. **이동 된 리소스와 연결 된 도구 및 스크립트가 새 리소스 id를 사용 하도록 업데이트 될 때까지 작동 하지** 않습니다. 옵션을 선택 하 여 확인을 선택한 다음 **확인**을 선택 합니다.

> [!NOTE]
> 크로스 구독 백업 (RS 자격 증명 모음 및 보호 된 Vm은 서로 다른 구독에 있는 경우)은 지원 되는 시나리오가 아닙니다. 또한 LRS (로컬 중복 저장소)에서 GRS (global 중복 저장소)로 저장소 중복성 옵션을 사용할 수 없으며, 그 반대의 경우도 자격 증명 모음 이동 작업을 수행 하는 동안 수정할 수 없습니다.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell을 사용 하 여 Recovery Services 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 `Move-AzureRMResource` cmdlet을 사용합니다. `Move-AzureRMResource`에는 리소스 이름 및 리소스 종류가 필요합니다. `Get-AzureRmRecoveryServicesVault` cmdlet에서 가져올 수 있습니다.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

리소스를 다른 구독으로 이동하기 위해 `-DestinationSubscriptionId` 매개 변수를 포함합니다.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

위의 cmdlet을 실행 한 후에는 지정 된 리소스를 이동할 것인지 묻는 메시지가 표시 됩니다. **Y**를 입력하여 확인합니다. 유효성 검사에 성공 후 리소스를 이동합니다.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI를 사용 하 여 Recovery Services 자격 증명 모음 이동

Recovery Services 자격 증명 모음을 다른 리소스 그룹으로 이동하려면 다음 cmdlet을 사용합니다.

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

## <a name="post-migration"></a>마이그레이션 후

1. 리소스 그룹에 대 한 액세스 제어를 설정/확인 합니다.  
2. 이동이 완료 된 후에 자격 증명 모음에 대해 백업 보고 및 모니터링 기능을 다시 구성 해야 합니다. 이전 구성은 이동 작업 중 손실됩니다.

## <a name="next-steps"></a>다음 단계

리소스 그룹 및 구독 간의 다양한 유형의 리소스를 이동할 수 있습니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.
