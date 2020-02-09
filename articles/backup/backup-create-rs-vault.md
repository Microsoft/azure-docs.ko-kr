---
title: Recovery Services 자격 증명 모음 만들기
description: 이 문서에서는 백업 및 복구 지점이 저장 되는 Recovery Services 자격 증명 모음을 만드는 방법에 대해 알아봅니다.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 682bf26c1485bd9e2ae288a6947e9ad573463069
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705465"
---
# <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![모든 서비스 선택](./media/backup-create-rs-vault/click-all-services.png)

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

    ![Recovery Services 자격 증명 모음 입력 및 선택](./media/backup-create-rs-vault/all-services.png)

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.

4. **Recovery Services 자격 증명 모음** 대시보드에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 추가](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Recovery Services 자격 증명 모음** 대화 상자가 열립니다. **이름**, **구독**, **리소스 그룹** 및 **이름**에 대한 값을 입력합니다.

    ![Recovery Services 자격 증명 모음 구성](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **이름**: 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다.
   - **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록 상자에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요.
   - **위치**: 자격 증명 모음의 지리적 지역을 선택합니다. 가상 머신을 보호할 자격 증명 모음을 만들려면, 자격 증명 모음이 **반드시** 가상 머신과 동일한 지역에 있어야 합니다.

      > [!IMPORTANT]
      > VM의 위치를 잘 모를 경우 대화 상자를 닫습니다. 포털에서 가상 머신의 목록으로 이동합니다. 가상 머신이 여러 지역에 있는 경우 각 지역에 Recovery Services 자격 증명 모음을 만듭니다. 첫 번째 위치에서 자격 증명 모음을 만든 후에 다른 위치에 대한 자격 증명 모음을 만듭니다. 백업 데이터를 저장할 스토리지 계정을 지정하지 않아도 됩니다. Recovery Services 자격 증명 모음 및 Azure Backup 서비스는 이를 자동으로 처리합니다.
      >
      >

5. Recovery Services 자격 증명 모음을 만들 준비가 되면 **만들기**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services 자격 증명 모음을 만드는 데 어느 정도 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 모서리에 있는 **알림** 영역에서 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에서 볼 수 있습니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

     ![백업 자격 증명 모음 목록 새로 고침](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>스토리지 중복 설정

Azure Backup는 자격 증명 모음에 대한 저장소를 자동으로 처리 합니다. 저장소를 복제 하는 방법을 지정 해야 합니다.

1. **Recovery Services 자격 증명 모음** 블레이드에서 새 자격 증명 모음을 클릭합니다. **설정** 섹션에서 **속성**을 클릭 합니다.
2. **속성**의 **백업 구성**에서 **업데이트**를 클릭 합니다.

3. 저장소 복제 유형을 선택 하 고 **저장**을 클릭 합니다.

     ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Azure를 기본 백업 저장소 엔드포인트으로 사용 하는 경우 기본 **지역 중복** 설정을 계속 사용 하는 것이 좋습니다.
   - Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다.
   - [지역](../storage/common/storage-redundancy-grs.md) 및 [로컬](../storage/common/storage-redundancy-lrs.md) 중복성에 대해 자세히 알아보세요.

> [!NOTE]
> Recovery services 자격 증명 모음에 대한 **저장소 복제 유형** (로컬 중복/지역 중복) 변경은 자격 증명 모음에서 백업을 구성 하기 전에 수행 해야 합니다. 백업을 구성한 후에는 수정 하는 옵션을 사용할 수 없으며 **저장소 복제 유형을**변경할 수 없습니다.

## <a name="set-cross-region-restore"></a>지역 간 복원 설정

복원 옵션 중 하나로, CRR (교차 지역 복원)을 사용 하면 azure [쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에 있는 보조 지역에서 azure vm을 복원할 수 있습니다. 이 옵션을 사용 하면 다음을 수행할 수 있습니다.

- 감사 또는 규정 준수 요구 사항이 있는 경우 드릴 수행
- 주 지역에 재해가 발생 한 경우 VM 또는 해당 디스크를 복원 합니다.

이 기능을 선택 하려면 **백업 구성** 블레이드에서 **교차 영역 복원 사용** 을 선택 합니다.

이 프로세스의 경우 저장소 수준에 따라 가격 책정이 영향을 미칩니다.

>[!NOTE]
>시작하기 전에
>
>- 지원 되는 관리 되는 형식 및 지역 목록은 [지원 매트릭스](backup-support-matrix.md#cross-region-restore) 를 검토 하세요.
>- CRR (지역 간 복원) 기능은 현재 WCUS 지역 에서만 사용할 수 있습니다.
>- CRR은 GRS 자격 증명 모음에 대한 자격 증명 모음 수준 옵트인 기능입니다 (기본적으로 해제 됨).
>- 이 기능에 대한 구독을 등록 하려면 다음 명령을 사용 하세요.<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- 제한 된 공개 미리 보기 중에이 기능을 등록 하는 경우 승인 전자 메일 검토에 가격 정책 세부 정보가 포함 됩니다.
>- 옵트인 후에는 보조 지역에서 백업 항목을 사용 하는 데 최대 48 시간이 걸릴 수 있습니다.
>- 현재 CRR은 백업 관리 유형 ARM Azure VM에 대해서만 지원 됩니다 (클래식 Azure VM은 지원 되지 않음).  추가 관리 유형이 CRR을 지 원하는 경우 **자동으로** 등록 됩니다.

### <a name="configure-cross-region-restore"></a>지역 간 복원 구성

GRS 중복성으로 만든 자격 증명 모음에는 지역 간 복원 기능을 구성 하는 옵션이 포함 됩니다. 모든 GRS 자격 증명 모음에는 설명서에 연결 되는 배너가 있습니다. 자격 증명 모음에 대해 CRR을 구성 하려면이 기능을 사용 하도록 설정 하는 옵션이 포함 된 백업 구성 블레이드로 이동 합니다.

 ![백업 구성 배너](./media/backup-azure-arm-restore-vms/banner.png)

1. 포털에서 Recovery Services 자격 증명 모음 > 설정 > 속성으로 이동 합니다.
2. **이 자격 증명 모음에서 교차 지역 복원 사용** 을 클릭 하 여 기능을 사용 하도록 설정 합니다.

   ![이 자격 증명 모음에서 지역 간 복원 사용을 클릭 하기 전에](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![이 자격 증명 모음에서 교차 영역 복원 사용을 클릭 한 후](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

[보조 지역에서 백업 항목을 보는](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)방법에 대해 알아봅니다.

[보조 지역에서 복원](backup-azure-arm-restore-vms.md#restore-in-secondary-region)하는 방법을 알아봅니다.

[보조 지역 복원 작업을 모니터링](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)하는 방법에 대해 알아봅니다.

## <a name="modifying-default-settings"></a>기본 설정 수정

**저장소 복제 유형** 및 **보안 설정** 에 대한 기본 설정은 자격 증명 모음에서 백업을 구성 하기 전에 검토 하는 것이 좋습니다.

- 기본적으로 **저장소 복제 유형은** **지역 중복**으로 설정 됩니다. 백업을 구성한 후에는 수정 하는 옵션을 사용할 수 없습니다. 설정을 검토 하 고 수정 하려면 다음 [단계](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) 를 수행 합니다.

- 새로 만든 자격 증명 모음에 대해 기본적으로 **일시 삭제** 를 **사용** 하 여 실수로 인 한 삭제 또는 악의적인 삭제 로부터 백업 데이터를 보호 합니다. 설정을 검토 하 고 수정 하려면 다음 [단계](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) 를 수행 합니다.

## <a name="next-steps"></a>다음 단계

[자세한 정보](backup-azure-recovery-services-vault-overview.md) 자격 증명 모음을 Recovery Services 합니다.
[자세한 정보](backup-azure-delete-vault.md) Recovery Services 자격 증명 모음을 삭제 합니다.
