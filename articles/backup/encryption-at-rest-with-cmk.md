---
title: 고객 관리 키를 사용 하 여 백업 데이터 암호화
description: Azure Backup를 사용 하 여 고객 관리 키 (CMK)를 사용 하 여 백업 데이터를 암호화 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 989b06b62f2db8df5504107eee8ed26710d13a57
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022414"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 백업 데이터 암호화

Azure Backup를 사용 하면 기본적으로 사용 하도록 설정 된 플랫폼 관리 키를 사용 하는 대신 CMK (고객 관리 키)를 사용 하 여 백업 데이터를 암호화할 수 있습니다. 백업 데이터를 암호화 하는 데 사용 되는 키는 [Azure Key Vault](../key-vault/index.yml)에 저장 되어야 합니다.

백업 암호화에 사용 되는 암호화 키는 원본에 사용 된 것과 다를 수 있습니다. 데이터는 AES 256 기반 DEK (데이터 암호화 키)를 사용 하 여 보호 되며, 키를 사용 하 여 보호 됩니다 (KEK). 이를 통해 데이터와 키에 대 한 모든 권한을 제공 합니다. 암호화를 허용 하려면 Recovery Services 자격 증명 모음에 Azure Key Vault 암호화 키에 대 한 액세스 권한을 부여 해야 합니다. 필요한 경우 키를 변경할 수 있습니다.

이 문서에서는 다음에 대해 설명 합니다.

- Recovery Services 자격 증명 모음 만들기
- 고객 관리 키를 사용 하 여 백업 데이터를 암호화 하도록 Recovery Services 자격 증명 모음 구성
- 고객 관리 키를 사용 하 여 암호화 된 자격 증명 모음에 백업 수행
- 백업에서 데이터 복원

## <a name="before-you-start"></a>시작하기 전에

- 이 기능을 사용 하면 **새 Recovery Services 자격 증명 모음만**암호화할 수 있습니다. 등록 하거나 등록 하려고 시도 하는 기존 항목을 포함 하는 모든 자격 증명 모음은 지원 되지 않습니다.

- Recovery Services 자격 증명 모음에 대해 사용 하도록 설정 된 경우, 고객 관리 키를 사용 하는 암호화는 플랫폼 관리 키 (기본값)를 사용 하 여 되돌릴 수 없습니다. 요구 사항에 따라 암호화 키를 변경할 수 있습니다.

- 이 기능은 현재 **MARS 에이전트를 사용 하 여 백업을 지원 하지**않으며, 동일한 경우 cmk 암호화 자격 증명 모음을 사용 하지 못할 수 있습니다. MARS 에이전트는 사용자 암호 기반 암호화를 사용 합니다. 또한이 기능은 클래식 Vm의 백업을 지원 하지 않습니다.

- 이 기능은 BitLocker (Windows 용) 및 DM (Linux)을 사용 하 여 VM 디스크의 게스트 기반 암호화를 사용 하는 [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)와 관련이 없습니다.

- Recovery Services 자격 증명 모음은 **동일한 지역**에 있는 Azure Key Vault에 저장 된 키로만 암호화할 수 있습니다. 또한 키는 **RSA 2048 키** 여야 하며 **사용** 상태 여야 합니다.

- 리소스 그룹 및 구독에서 CMK 암호화 된 Recovery Services 자격 증명 모음을 이동 하는 것은 현재 지원 되지 않습니다.

- 이 기능은 현재 Azure Portal 에서만 구성할 수 있습니다.

Recovery Services 자격 증명 모음을 만들고 구성 하지 않은 경우 여기에서 [작업을 수행 하는 방법을 읽을](backup-create-rs-vault.md)수 있습니다.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 하도록 자격 증명 모음 구성

이 섹션에는 다음 단계가 포함 됩니다.

1. Recovery Services 자격 증명 모음에 관리 id 사용

1. 자격 증명 모음에 사용 권한을 할당 하 여 Azure Key Vault의 암호화 키에 액세스

1. Azure Key Vault에서 일시 삭제 및 보호 제거 사용

1. Recovery Services 자격 증명 모음에 암호화 키 할당

이러한 모든 단계를 앞에서 설명한 순서 대로 수행 하 여 원하는 결과를 달성할 수 있도록 해야 합니다. 각 단계에 대해서는 아래에서 자세히 설명 합니다.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 관리 id 사용

Azure Backup는 시스템 할당 관리 id를 사용 하 여 Azure Key Vault에 저장 된 암호화 키에 액세스 하기 위해 Recovery Services 자격 증명 모음을 인증 합니다. Recovery Services 자격 증명 모음에 관리 되는 id를 사용 하도록 설정 하려면 아래에 설명 된 단계를 따르세요.

>[!NOTE]
>일단 사용 하도록 설정 하면 관리 되는 id를 일시적으로 **사용 하지 않도록 설정 해야 합니다** . 관리 id를 사용 하지 않도록 설정 하면 일관 되지 않은 동작이 발생할 수 있습니다.

1. Recovery Services 자격 증명 모음으로 이동-> **id**

    ![Id 설정](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. **상태** 를 **설정** 으로 변경 하 고 **저장**을 클릭 합니다.

1. 자격 증명 모음의 시스템 할당 관리 id 인 개체 ID가 생성 됩니다.

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Recovery Services 자격 증명 모음에 사용 권한을 할당 하 여 Azure Key Vault의 암호화 키에 액세스

이제 Recovery Services 자격 증명 모음에서 암호화 키를 포함 하는 Azure Key Vault에 액세스 하도록 허용 해야 합니다. 이 작업은 Recovery Services 자격 증명 모음의 관리 되는 id가 Key Vault에 액세스 하도록 허용 하 여 수행 됩니다.

1. Azure Key Vault > **액세스 정책**으로 이동 합니다. **+ 액세스 정책 추가**로 계속 진행 합니다.

    ![액세스 정책 추가](./media/encryption-at-rest-with-cmk/access-policies.png)

1. **키 권한**에서 **가져오기**, **나열**, **키 래핑** 및 **키 래핑** 작업을 선택 합니다. 허용 되는 키에 대 한 동작을 지정 합니다.

    ![키 사용 권한 할당](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. **보안 주체 선택** 으로 이동 하 여 해당 이름 또는 관리 id를 사용 하 여 검색 상자에서 자격 증명 모음을 검색 합니다. 표시 되 면 자격 증명 모음을 선택 하 고 창의 맨 아래에서 **선택** 을 클릭 합니다.

    ![보안 주체 선택](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 작업이 완료 되 면 **추가** 를 클릭 하 여 새 액세스 정책을 추가 합니다.

1. **저장** 을 클릭 하 여 Azure Key Vault의 액세스 정책에 대 한 변경 내용을 저장 합니다.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Azure Key Vault에서 일시 삭제 및 보호 제거 사용

암호화 키를 저장 하는 Azure Key Vault에 대해 **일시 삭제 및 제거 보호를 사용 하도록 설정** 해야 합니다. 다음과 같이 Azure Key Vault UI에서이 작업을 수행할 수 있습니다. 또는 Key Vault를 만드는 동안 이러한 속성을 설정할 수 있습니다. 이러한 Key Vault 속성에 대 한 자세한 내용은 [여기](../key-vault/general/soft-delete-overview.md)를 참조 하세요.

![일시 삭제 및 보호 제거 사용](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

다음 단계를 사용 하 여 PowerShell을 통해 일시 삭제 및 제거 보호를 사용 하도록 설정할 수도 있습니다.

1. Azure 계정에 로그인 합니다.

    ```azurepowershell
    Login-AzAccount
    ```

1. 자격 증명 모음을 포함 하는 구독을 선택 합니다.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. 일시 삭제 사용

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. 보호 제거 사용

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>RS 자격 증명 모음에 암호화 키 할당

>[!NOTE]
> 계속 하기 전에 다음을 확인 합니다.
>
> - 위에서 언급 한 모든 단계가 성공적으로 완료 되었습니다.
>   - Recovery Services 자격 증명 모음의 관리 되는 id가 사용 하도록 설정 되었으며 필요한 권한이 할당 되었습니다.
>   - Azure Key Vault에 일시 삭제 및 제거 방지 기능이 설정 되어 있습니다.
> - CMK 암호화를 사용 하도록 설정할 Recovery Services 자격 증명 모음에 보호 또는 등록 된 항목이 **없습니다** .

위의 내용이 확인 되 면 자격 증명 모음에 대 한 암호화 키를 계속 선택 합니다.

키를 할당 하려면:

1. Recovery Services 자격 증명 모음으로 이동-> **속성**

    ![암호화 설정](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. **암호화 설정**에서 **업데이트** 를 클릭 합니다.

1. 암호화 설정 창에서 다음 방법 중 하나를 사용 하 여 **사용자 고유의 키를 사용** 하 고 계속 키를 지정 합니다 .를 선택 합니다. **사용할 키가 사용 상태에 있는 RSA 2048 키 인지 확인 합니다.**

    1. 이 Recovery Services 자격 증명 모음에서 데이터를 암호화 하는 데 사용할 **키 URI** 를 입력 합니다. 또한이 키를 포함 하는 Azure Key Vault이 있는 구독을 지정 해야 합니다. 이 키 URI는 Azure Key Vault의 해당 키에서 가져올 수 있습니다. 키 URI가 올바르게 복사 되었는지 확인 합니다. 키 식별자와 함께 제공 되는 **클립보드로 복사** 단추를 사용 하는 것이 좋습니다.

        ![키 URI 입력](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. 키 선택 창의 Key Vault에서 키를 찾아 선택 합니다.

        ![키 자격 증명 모음에서 키 선택](./media/encryption-at-rest-with-cmk/key-vault.png)

1. **저장**을 클릭합니다.

1. **암호화 키 업데이트 진행률 추적:** Recovery Services 자격 증명 모음의 **활동 로그** 를 사용 하 여 키 할당의 진행률을 추적할 수 있습니다. 상태는 곧 **성공**으로 변경 됩니다. 이제 자격 증명 모음에서 지정 된 키를 사용 하 여 모든 데이터를 KEK로 암호화 합니다.

    ![활동 로그를 사용 하 여 진행률 추적](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![상태 성공](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> 이 프로세스는 암호화 키를 업데이트/변경 하려는 경우에도 동일 하 게 유지 됩니다. 현재 사용 중인 것과 다른 Key Vault에서 키를 업데이트 하 고 사용 하려는 경우 다음을 확인 합니다.
>
> - Key Vault은 Recovery Services 자격 증명 모음과 동일한 지역에 있습니다.
>
> - 키 자격 증명 모음에 일시 삭제 및 제거 보호가 설정 되어 있습니다.
>
> - Recovery Services 자격 증명 모음에는 Key Vault에 액세스 하는 데 필요한 권한이 있습니다.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>고객 관리 키로 암호화 된 자격 증명 모음에 백업

보호를 계속 구성 하기 전에 다음 검사 목록을 준수 하는지 확인 하는 것이 좋습니다. CMK가 아닌 암호화 된 자격 증명 모음에 대해 항목을 백업 (또는 구성) 하도록 구성 하 고, 고객 관리 키를 사용 하는 암호화를 사용 하도록 설정할 수 없으며 플랫폼 관리 키를 계속 사용 하기 때문에이 작업이 중요 합니다.

>[!IMPORTANT]
> 보호를 구성 하기 전에 다음 단계를 **성공적으로** 완료 해야 합니다.
>
>1. 구독에서 백업 자격 증명 모음에 대 한 고객 관리 키를 사용 하도록 설정 했습니다.
>1. 백업 자격 증명 모음을 만들었습니다.
>1. 백업 자격 증명 모음의 시스템 할당 관리 Id 사용
>1. Key Vault에서 암호화 키에 액세스할 수 있도록 백업 자격 증명 모음에 할당 된 권한
>1. Key Vault에 대해 일시 삭제를 사용 하도록 설정 하 고 보호를 제거 합니다.
>1. 백업 자격 증명 모음에 대 한 유효한 암호화 키 할당
>
>위의 모든 단계를 확인 한 후에만 백업 구성을 진행 합니다.

고객 관리 키를 사용 하 여 암호화 된 Recovery Services 자격 증명 모음에 대 한 백업을 구성 하 고 수행 하는 프로세스는 **환경을 변경 하지 않고**플랫폼 관리 키를 사용 하는 자격 증명 모음과 동일 합니다. 이는 VM 내에서 실행 되는 워크 로드 (예: [SAP HANA](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) 데이터베이스)의 백업 뿐만 아니라 [Azure vm의 백업](./quick-backup-vm-portal.md) 에도 적용 됩니다.

## <a name="restoring-data-from-backup"></a>백업에서 데이터 복원

### <a name="vm-backup"></a>VM 백업

Recovery Services 자격 증명 모음에 저장 된 데이터는 [여기](./backup-azure-arm-restore-vms.md)에 설명 된 단계에 따라 복원할 수 있습니다. 고객 관리 키를 사용 하 여 암호화 된 Recovery Services 자격 증명 모음에서 복원 하는 경우 DES (디스크 암호화 집합)를 사용 하 여 복원 된 데이터를 암호화 하도록 선택할 수 있습니다.

#### <a name="restoring-vm--disk"></a>V m/디스크 복원

1. "스냅숏" 복구 지점에서 디스크/VM을 복구 하는 경우 복원 된 데이터는 원본 VM의 디스크를 암호화 하는 데 사용 되는 DES로 암호화 됩니다.

1. 복구 유형을 "자격 증명 모음"으로 복구 지점에서 복원 하는 경우 복원 시 지정 된 DES를 사용 하 여 복원 된 데이터를 암호화 하도록 선택할 수 있습니다. 또는 DES를 지정 하지 않고 데이터 복원을 계속 하도록 선택할 수 있습니다 .이 경우 Microsoft 관리 키를 사용 하 여 암호화 됩니다.

복원을 시작 하는 동안 선택 된 항목에 관계 없이 복원이 완료 된 후 복원 된 디스크/v m s를 암호화할 수 있습니다.

![복원 지점](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>자격 증명 모음 복구 지점에서 복원 하는 동안 디스크 암호화 설정 선택

디스크 암호화 집합은 아래와 같이 복원 창의 암호화 설정에서 지정 됩니다.

1. 키를 **사용 하 여 디스크 암호화**에서 **예**를 선택 합니다.

1. 드롭다운에서 복원 된 디스크에 사용할 DES를 선택 합니다. **DES에 액세스할 수 있는지 확인 합니다.**

>[!NOTE]
>Azure Disk Encryption를 사용 하는 VM을 복원 하는 경우 복원 하는 동안 DES를 선택 하는 기능을 사용할 수 없습니다.

![키를 사용 하 여 디스크 암호화](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>파일 복원

파일 복원을 수행할 때 복원 된 데이터는 대상 위치를 암호화 하는 데 사용 되는 키를 사용 하 여 암호화 됩니다.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Azure Vm에서 SAP HANA/SQL 데이터베이스 복원

Azure VM에서 실행 되는 백업 된 SAP HANA/SQL database에서 복원 하는 경우 복원 된 데이터는 대상 저장소 위치에서 사용 되는 암호화 키를 사용 하 여 암호화 됩니다. VM 디스크를 암호화 하는 데 사용 되는 고객 관리 키 또는 플랫폼 관리 키 일 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 기존 백업 자격 증명 모음을 암호화할 수 있나요?

아니요, 새 자격 증명 모음에만 CMK 암호화를 사용 하도록 설정할 수 있습니다. 따라서 자격 증명 모음에 보호 된 항목이 없어야 합니다. 실제로, 고객 관리 키를 사용 하 여 암호화를 사용 하도록 설정 하기 전에 자격 증명 모음에 대 한 항목을 보호 하려고 시도 하지 않습니다.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>자격 증명 모음에 대 한 항목을 보호 하려고 했지만 실패 했습니다. 자격 증명 모음에 보호 된 항목이 아직 포함 되어 있지 않습니다. 이 자격 증명 모음에 CMK 암호화를 사용 하도록 설정할 수 있나요?

아니요, 자격 증명 모음에는 과거에 항목을 보호 하려는 시도가 없었습니다.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>CMK 암호화를 사용 하는 자격 증명 모음이 있습니다. 나중에 자격 증명 모음으로 보호 되는 백업 항목이 있는 경우에도 플랫폼 관리 키를 사용 하 여 암호화로 되돌릴 수 있나요?

아니요, CMK 암호화를 사용 하도록 설정 하면 플랫폼 관리 키를 사용 하도록 되돌릴 수 없습니다. 요구 사항에 따라 사용 되는 키를 변경할 수 있습니다.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure Backup에 대 한 CMK 암호화가 Azure Site Recovery에도 적용 되나요?

아니요,이 문서에서는 백업 데이터만 암호화 하는 방법을 설명 합니다. Azure Site Recovery의 경우 서비스에서 사용 가능한 속성을 별도로 설정 해야 합니다.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>이 문서의 단계 중 하나가 누락 되 고 내 데이터 원본을 보호 하는 데 문제가 발생 했습니다. CMK 암호화를 계속 사용할 수 있나요?

문서의 단계를 따르지 않고 항목 보호를 계속 하면 자격 증명 모음에서 고객 관리 키를 사용 하는 암호화를 사용할 수 없게 될 수 있습니다. 따라서 항목 보호를 계속 하기 전에 [이 검사 목록을](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) 참조 하는 것이 좋습니다.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>CMK 암호화를 사용 하 여 백업 비용을 추가 하나요?

백업에 CMK 암호화를 사용 하는 경우 추가 비용이 발생 하지 않습니다. 그러나 키가 저장 된 Azure Key Vault 사용에 대 한 비용이 계속 해 서 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup의 보안 기능 개요](security-overview.md)
