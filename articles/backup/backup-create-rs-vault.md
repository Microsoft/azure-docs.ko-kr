---
title: Recovery Services 자격 증명 모음 만들기 및 구성
description: 이 문서에서는 백업과 복구 지점이 저장 되는 Recovery Services 자격 증명 모음을 만들고 구성 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 81c6fd47ccea2ea17a20535df04931727c23be6f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177196"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기 및 구성

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>스토리지 중복 설정

Azure Backup는 자격 증명 모음에 대 한 저장소를 자동으로 처리 합니다. 저장소를 복제 하는 방법을 지정 해야 합니다.

> [!NOTE]
> 자격 증명 모음에서 백업을 구성 하기 전에 Recovery Services 자격 증명 모음에 대 한 **저장소 복제 유형** (로컬 중복/지역 중복)을 변경 해야 합니다. 백업을 구성한 후에는 수정 하는 옵션을 사용할 수 없습니다.
>
>- 백업을 아직 구성 하지 않은 경우 다음 단계를 [수행](#set-storage-redundancy) 하 여 설정을 검토 하 고 수정 합니다.
>- 백업을 이미 구성 했 고 GRS에서 LRS로 이동 해야 하는 경우 [이러한 해결 방법을 검토](#how-to-change-from-grs-to-lrs-after-configuring-backup)합니다.

1. **Recovery Services 자격** 증명 모음 창에서 새 자격 증명 모음을 선택 합니다. **설정** 섹션에서 **속성**을 선택 합니다.
1. **속성**의 **백업 구성**에서 **업데이트**를 선택 합니다.

1. 저장소 복제 유형을 선택 하 고 **저장**을 선택 합니다.

     ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Azure를 기본 백업 저장소 끝점으로 사용 하는 경우 기본 **지역 중복** 설정을 계속 사용 하는 것이 좋습니다.
   - Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다.
   - [지역](../storage/common/storage-redundancy.md) 및 [로컬](../storage/common/storage-redundancy.md) 중복성에 대해 자세히 알아보세요.

>[!NOTE]
>현재 솔루션이 스냅숏 기반 이며 자격 증명 모음으로 전송 된 데이터가 없으므로 자격 증명 모음에 대 한 저장소 복제 설정은 Azure 파일 공유 백업과 관련이 없습니다. 스냅숏은 백업 된 파일 공유와 동일한 저장소 계정에 저장 됩니다.

## <a name="set-cross-region-restore"></a>지역 간 복원 설정

복원 옵션 중 하나로, CRR (교차 지역 복원)을 사용 하면 azure [쌍을 이루는 지역](../best-practices-availability-paired-regions.md)에 있는 보조 지역에서 azure vm을 복원할 수 있습니다. 이 옵션을 사용 하면 다음을 수행할 수 있습니다.

- 감사 또는 규정 준수 요구 사항이 있는 경우 드릴 수행
- 주 지역에 재해가 발생 한 경우 VM 또는 해당 디스크를 복원 합니다.

이 기능을 선택 하려면 **백업 구성** 창에서 **교차 영역 복원 사용** 을 선택 합니다.

이 프로세스의 경우 저장소 수준에 따라 가격 책정이 영향을 미칩니다.

>[!NOTE]
>시작하기 전에
>
>- 지원 되는 관리 되는 형식 및 지역 목록은 [지원 매트릭스](backup-support-matrix.md#cross-region-restore) 를 검토 하세요.
>- 이제 모든 Azure 공용 지역에서 CRR (교차 지역 복원) 기능을 미리 볼 수 있습니다.
>- CRR은 GRS 자격 증명 모음에 대 한 자격 증명 모음 수준 옵트인 기능입니다 (기본적으로 해제 됨).
>- 옵트인 후에는 보조 지역에서 백업 항목을 사용 하는 데 최대 48 시간이 걸릴 수 있습니다.
>- 현재 CRR은 백업 관리 유형 ARM Azure VM에 대해서만 지원 됩니다 (클래식 Azure VM은 지원 되지 않음).  추가 관리 유형이 CRR을 지 원하는 경우 **자동으로** 등록 됩니다.
>- 보호를 처음으로 시작한 후에는 지역 간 복원을 현재 GRS 또는 LRS로 되돌릴 수 없습니다.

### <a name="configure-cross-region-restore"></a>지역 간 복원 구성

GRS 중복성으로 만든 자격 증명 모음에는 지역 간 복원 기능을 구성 하는 옵션이 포함 됩니다. 모든 GRS 자격 증명 모음에는 설명서에 연결 되는 배너가 있습니다. 자격 증명 모음에 대해 CRR을 구성 하려면이 기능을 사용 하도록 설정 하는 옵션이 포함 된 백업 구성 창으로 이동 합니다.

 ![백업 구성 배너](./media/backup-azure-arm-restore-vms/banner.png)

1. 포털에서 Recovery Services 자격 증명 모음 > 설정 > 속성으로 이동 합니다.
2. **이 자격 증명 모음에서 지역 간 복원 사용** 을 선택 하 여 기능을 사용 하도록 설정 합니다.

   ![이 자격 증명 모음에서 지역 간 복원 사용을 선택 하기 전에](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![이 자격 증명 모음에서 지역 간 복원 사용을 선택한 후](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

[보조 지역에서 백업 항목을 보는](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)방법에 대해 알아봅니다.

[보조 지역에서 복원](backup-azure-arm-restore-vms.md#restore-in-secondary-region)하는 방법을 알아봅니다.

[보조 지역 복원 작업을 모니터링](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)하는 방법에 대해 알아봅니다.

## <a name="set-encryption-settings"></a>암호화 설정 설정

기본적으로 Recovery Services 자격 증명 모음의 데이터는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 이 암호화를 사용 하도록 설정 하기 위해 끝에서 명시적인 조치가 필요 하지 않으며 Recovery Services 자격 증명 모음에 백업 되는 모든 워크 로드에 적용 됩니다.  이 자격 증명 모음에서 백업 데이터를 암호화 하기 위해 고유한 키를 가져오도록 선택할 수 있습니다. 이를 고객이 관리 하는 키 라고 합니다. 사용자 고유의 키를 사용 하 여 백업 데이터를 암호화 하려는 경우이 자격 증명 모음에 항목을 보호 하기 전에 암호화 키를 지정 해야 합니다. 키를 사용 하 여 암호화를 사용 하도록 설정한 후에는 되돌릴 수 없습니다.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 하도록 자격 증명 모음 구성

자격 증명 모음에서 고객 관리 키를 사용 하 여 암호화 하도록 구성 하려면 이러한 단계를 다음 순서 대로 수행 해야 합니다.

1. Recovery Services 자격 증명 모음에 관리 id 사용

1. 자격 증명 모음에 사용 권한을 할당 하 여 Azure Key Vault의 암호화 키에 액세스

1. Azure Key Vault에서 일시 삭제 및 보호 제거 사용

1. Recovery Services 자격 증명 모음에 암호화 키 할당

이러한 각 단계에 대 한 지침은 [이 문서에서](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)찾을 수 있습니다.

## <a name="modifying-default-settings"></a>기본 설정 수정

자격 증명 모음에서 백업을 구성하기 전에 **스토리지 복제 유형** 및 **보안 설정**에 대한 기본 설정을 검토하는 것이 좋습니다.

- 기본적으로 **저장소 복제 유형은** **지역 중복** (GRS)으로 설정 됩니다. 백업을 구성한 후에는 수정 하는 옵션을 사용할 수 없습니다.
  - 백업을 아직 구성 하지 않은 경우 다음 단계를 [수행](#set-storage-redundancy) 하 여 설정을 검토 하 고 수정 합니다.
  - 백업을 이미 구성 했 고 GRS에서 LRS로 이동 해야 하는 경우 [이러한 해결 방법을 검토](#how-to-change-from-grs-to-lrs-after-configuring-backup)합니다.

- 새로 만든 자격 증명 모음에 대해 기본적으로 **일시 삭제** 를 **사용** 하 여 실수로 인 한 삭제 또는 악의적인 삭제 로부터 백업 데이터를 보호 합니다. 설정을 검토 하 고 수정 하려면 [다음 단계를 수행](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 합니다.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>백업 구성 후 GRS에서 LRS로 변경 하는 방법

GRS에서 LRS (로컬 중복 저장소)로 이동 하도록 결정 하기 전에 시나리오에 맞는 저렴 한 비용과 높은 데이터 내구성 간의 장단점을 검토 합니다. GRS에서 LRS로 이동 해야 하는 경우 두 가지 옵션을 선택할 수 있습니다. 백업 데이터를 유지 하기 위한 비즈니스 요구 사항에 따라 달라 집니다.

- [이전 백업 데이터를 보존할 필요가 없습니다.](#dont-need-to-preserve-previous-backed-up-data)
- [이전 백업 데이터를 유지 해야 합니다.](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>이전 백업 데이터를 보존할 필요가 없습니다.

새 LRS 자격 증명 모음에서 워크 로드를 보호 하려면 GRS 자격 증명 모음에서 현재 보호 및 데이터를 삭제 하 고 백업을 다시 구성 해야 합니다.

>[!WARNING]
>다음 작업은 소거식 이며 실행 취소할 수 없습니다. 보호 된 서버와 연결 된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제 됩니다. 주의하여 진행하세요.

GRS 자격 증명 모음에서 현재 보호를 중지 하 고 삭제 합니다.

1. GRS 자격 증명 모음 속성에서 일시 삭제를 사용 하지 않도록 설정 합니다. 일시 삭제를 사용 하지 않도록 설정 하려면 [다음 단계](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) 를 수행 합니다.

1. 보호를 중지 하 고 기존 GRS vault에서 백업을 삭제 합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 항목**을 선택 합니다. LRS 자격 증명 모음으로 이동 해야 하는 여기에 나열 된 항목은 해당 백업 데이터와 함께 제거 되어야 합니다. [클라우드에서 보호 된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) 하 고 [온-프레미스에서 보호 된 항목을 삭제](backup-azure-delete-vault.md#delete-protected-items-on-premises)하는 방법을 참조 하세요.

1. AFS (Azure 파일 공유), SQL server 또는 SAP HANA 서버를 이동할 계획인 경우 등록을 취소 해야 합니다. 자격 증명 모음 대시보드 메뉴에서 **백업 인프라**를 선택 합니다. [SQL server 등록을 취소](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)하 고, [Azure 파일 공유와 연결 된 저장소 계정을 등록 취소](manage-afs-backup.md#unregister-a-storage-account)하 고, [SAP HANA 인스턴스의 등록](sap-hana-db-manage.md#unregister-an-sap-hana-instance)을 취소 하는 방법을 참조 하세요.

1. GRS 자격 증명 모음에서 제거 되 면 새 LRS 자격 증명 모음에서 워크 로드에 대 한 백업을 계속 구성 합니다.

#### <a name="must-preserve-previous-backed-up-data"></a>이전 백업 데이터를 유지 해야 합니다.

GRS 자격 증명 모음에서 현재 보호 된 데이터를 유지 하 고 새 LRS 자격 증명 모음에서 보호를 계속 해야 하는 경우 일부 워크 로드에 대해 제한 된 옵션이 있습니다.

- MARS의 경우 [데이터 보관을 사용 하 여 보호를 중지](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) 하 고 새 LRS vault에 에이전트를 등록할 수 있습니다.

  - Azure Backup 서비스는 GRS 자격 증명 모음의 기존 복구 지점은 계속 유지 합니다.
  - GRS 자격 증명 모음에서 복구 지점이 유지 되도록 요금을 지불 해야 합니다.
  - GRS 자격 증명 모음의 만료 되지 않은 복구 지점만 백업 된 데이터를 복원할 수 있습니다.
  - LRS 자격 증명 모음에 새 초기 데이터 복제본을 만들어야 합니다.

- Azure VM의 경우 GRS 자격 증명 모음에서 VM에 대 한 [데이터 보관을 사용 하 여 보호를 중지](backup-azure-manage-vms.md#stop-protecting-a-vm) 하 고 vm을 다른 리소스 그룹으로 이동한 다음 LRS 자격 증명 모음에서 vm을 보호할 수 있습니다. 다른 리소스 그룹으로 VM을 이동 하는 방법에 대 한 [지침 및 제한 사항](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) 을 참조 하세요.

  VM은 한 번에 하나의 자격 증명 모음 에서만 보호할 수 있습니다. 그러나 새 리소스 그룹의 VM은 다른 VM으로 간주 되므로 LRS 자격 증명 모음에서 보호할 수 있습니다.

  - Azure Backup 서비스는 GRS 자격 증명 모음에 백업 된 복구 지점이 유지 됩니다.
  - GRS 자격 증명 모음에서 복구 지점이 유지 되도록 요금을 지불 해야 합니다 (자세한 내용은 [Azure Backup 가격 책정](azure-backup-pricing.md) 참조).
  - 필요한 경우 GRS 자격 증명 모음에서 VM을 복원할 수 있습니다.
  - 새 리소스에서 VM의 LRS 자격 증명 모음에 대 한 첫 번째 백업은 초기 복제본이 됩니다.

## <a name="next-steps"></a>다음 단계

[자세한 정보](backup-azure-recovery-services-vault-overview.md) 자격 증명 모음을 Recovery Services 합니다.
[자세한 정보](backup-azure-delete-vault.md) Recovery Services 자격 증명 모음을 삭제 합니다.
