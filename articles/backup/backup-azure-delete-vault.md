---
title: Azure에서 Recovery Services 자격 증명 모음 삭제
description: Recovery Services 자격 증명 모음을 삭제 하는 방법을 설명 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: 51de1c4ac17360282877f05d52c3ea8fa2c6d712
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310755"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

이 문서에서는 삭제 하는 방법에 설명 합니다는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음입니다. 종속성을 제거 자격 증명 모음을 삭제 하 고 강제로 자격 증명 모음을 삭제 하기 위한 지침을 포함 합니다.


## <a name="before-you-start"></a>시작하기 전에

시작 하기 전에 서버가 있는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다는 이해, 등록 또는 백업 데이터를 보유 하는 중요 한 것입니다.

- 자격 증명 모음을 정상적으로 삭제 하려면 포함 된 서버의 등록을 취소 자격 증명 모음 데이터를 제거한 다음 자격 증명 모음을 삭제 합니다.
- 자격 증명 모음을 삭제 하려고 하면는 아직 종속성, 오류 메시지가 발생 합니다 및 포함 하 여 자격 증명 모음 종속성을 수동으로 제거 해야 합니다.
    - 백업 된 항목
    - 보호 된 서버
    - Backup 관리 서버 (Azure Backup Server, DPM) ![해당 대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Recovery Services 자격 증명 모음에 있는 모든 데이터를 유지 하 고 자격 증명 모음을 삭제 하려면에 않으려면 강제로 자격 증명 모음을 삭제할 수 있습니다.
- 자격 증명 모음을 삭제하려고 해도 할 수 없는 경우 자격 증명 모음이 여전히 백업 데이터를 받도록 구성되어 있습니다.


## <a name="delete-a-vault-from-the-azure-portal"></a>Azure portal에서 자격 증명 모음 삭제

1. 자격 증명 모음 대시보드를 엽니다.  
2. 대시보드를 클릭 **삭제**합니다. 삭제할 것임을 확인 합니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

오류가 발생 하는 경우 제거 [backup 항목](#remove-backup-items)를 [인프라 서버](#remove-azure-backup-management-servers), 및 [복구 지점을](#remove-azure-backup-agent-recovery-points), 한 다음 자격 증명 모음을 삭제 합니다.

![자격 증명 모음 오류를 삭제 합니다.](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 삭제

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 설치에서 chocolatey [여기](https://chocolatey.org/) 실행 ARMClient를 설치 하 여 아래 명령을:

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. Azure 계정에 로그인 하 고이 명령을 실행 합니다.

    ` ARMClient.exe login [environment name] `

3. Azure portal에서 삭제 하려는 자격 증명 모음에 대 한 구독 ID 및 리소스 그룹 이름을 수집 합니다.

이 참조 ARMClient 명령에 대 한 자세한 내용은 [문서](https://github.com/projectkudu/ARMClient/blob/master/README.md)합니다.

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음을 삭제 하려면

1. 사용자 구독 ID, 리소스 그룹 이름 및 자격 증명 모음 이름을 사용 하 여 다음 명령을 실행 합니다. 명령을 실행 하면 모든 종속성이 없는 경우 자격 증명 모음을 삭제 합니다.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 자격 증명 모음의 비어 있지, "자격 증명 모음 수 없습니다.이 자격 증명이 모음 내에서 기존 리소스는 삭제 됨" 오류가 나타납니다. 보호 된 항목을 제거 하려면 다음을 수행 하는 자격 증명 모음에 내에서 컨테이너 /:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal에서 데이터를 자격 증명 모음 삭제를 확인 합니다.


## <a name="remove-vault-items-and-delete-the-vault"></a>자격 증명 모음 항목을 제거 하 고 자격 증명 모음 삭제

이러한 절차는 백업 데이터 및 인프라 서버 제거에 대 한 몇 가지 예제를 제공 합니다. 모든 자격 증명 모음에서 제거 된 후에이 삭제할 수 있습니다.

### <a name="remove-backup-items"></a>백업 항목 제거

이 절차는 Azure files에서 백업 데이터를 제거 하는 방법을 보여 주는 예제를 제공 합니다.

1. 클릭 **Backup 항목** > **Azure Storage (Azure Files)**

    ![백업 유형 선택](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 각 Azure Files 항목을 제거 하 고 클릭 마우스 오른쪽 단추로 클릭 **백업 중지**합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/stop-backup-item.png)


3. **Backup 중지** > **옵션을 선택**선택 **Backup 데이터 삭제**합니다.
4. 항목의 이름을 입력 하 고 클릭 **백업 중지**합니다.
   - 이 항목을 삭제할 것인지 확인 합니다.
   - 합니다 **백업 중지** 확인 한 후 단추를 활성화 합니다.
   - 유지 하 고 데이터를 삭제 하지 마십시오 자격 증명 모음을 삭제할 수 없습니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 필요에 따라 데이터를 삭제 하는 이유는 이유를 제공 하 고 주석을 추가 합니다.
6. 삭제 작업이 완료 되었는지 확인 하려면 Azure 메시지 확인 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png).
7. 서비스 작업이 완료 되 면 메시지를 보냅니다: **백업 프로세스가 중지 되 고 백업 데이터가 삭제**합니다.
8. 목록에서 항목을 삭제한 후 **Backup 항목** 메뉴에서 **새로 고침**을 클릭하면 자격 증명 모음의 항목이 표시됩니다.

      ![백업 데이터 삭제](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-azure-backup-management-servers"></a>Azure Backup 관리 서버를 제거 합니다.

1. 자격 증명 모음 대시보드 메뉴에서 클릭 **Backup 인프라**합니다.
2. 클릭 **Backup 관리 서버** 서버를 표시 합니다.

    ![해당 대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 항목을 마우스 오른쪽 단추로 클릭 > **삭제**합니다.
4. 에 **삭제할** 메뉴에서 서버 이름을 입력 하 고 클릭 **삭제**합니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  필요에 따라 데이터를 삭제 하는 이유는 이유를 제공 하 고 주석을 추가 합니다.

> [!NOTE]
> 파일을 보호 된 서버의 MARS 콘솔에서 또는 관리 서버 콘솔에서 항목을 제거 하려면 보호를 중지 하 고 백업을 삭제 합니다. 백업 항목을 유지 하는 경우 삭제 하 고 서버 등록을 취소 하려고 할 때 다음 오류가 표시 됩니다.
>
>![삭제 하지 못했습니다.](./media/backup-azure-delete-vault/deletion-failed.png)

6. 삭제 작업이 완료 되었는지 확인 하려면 Azure 메시지 확인 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png).
7. 서비스 작업이 완료 되 면 메시지를 보냅니다: **백업 프로세스가 중지 되 고 백업 데이터가 삭제**합니다.
8. 목록의 항목을 삭제 한 후를 **Backup 인프라** 메뉴에서 클릭 **새로 고침** 자격 증명 모음에서 항목을 표시 합니다.


### <a name="remove-azure-backup-agent-recovery-points"></a>Azure Backup 에이전트 복구 지점을 제거합니다

1. 자격 증명 모음 대시보드 메뉴에서 클릭 **Backup 인프라**합니다.
2. 클릭 **보호 된 서버** 인프라 서버를 표시 합니다.

    ![대시보드를 열려면 자격 증명 모음 선택](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. **보호되는 서버** 목록에서 Azure Backup 에이전트를 클릭합니다.

    ![백업 유형 선택](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Azure Backup 에이전트를 사용 하 여 보호 된 서버의 목록에서 서버를 클릭 합니다.

    ![특정 보호된 서버 선택](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 선택한 서버 대시보드에서 **삭제**합니다.

    ![선택한 대시보드 삭제](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 에 **삭제할** 메뉴에서 서버 이름을 입력 하 고 클릭 **삭제**합니다.

     ![백업 데이터 삭제](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. 필요에 따라 데이터를 삭제 하는 이유는 이유를 제공 하 고 주석을 추가 합니다.

> [!NOTE]
> 이러한 서버의 등록을 삭제 하기 전에 백업 관리 서버 또는 Azure Backup Agent 서버에 연결 된 백업 항목을 삭제 되어야 합니다. 백업 항목을 제거 하려면 해당 하는 경우 서버에서 MARS 관리 콘솔, SC DPM 및 MABS에 이동 하 고 보호를 중지 하 고 백업을 삭제 하는 관련 옵션을 선택 합니다. 백업 항목에 계속 연결 되어 있으면 다음 오류가 표시 됩니다.
>
>
>![삭제 하지 못했습니다.](./media/backup-azure-delete-vault/deletion-failed.png)

8. 삭제 작업이 완료 되었는지 확인 하려면 Azure 메시지 확인 ![백업 데이터 삭제](./media/backup-azure-delete-vault/messages.png).
9. 목록의 항목을 삭제 한 후를 **Backup 인프라** 메뉴에서 클릭 **새로 고침** 자격 증명 모음에서 항목을 표시 합니다.

### <a name="delete-the-vault-after-removing-dependencies"></a>종속성을 제거한 후 자격 증명 모음 삭제

1. 모든 종속성을 제거한 경우 스크롤하여 합니다 **Essentials** 자격 증명 모음 메뉴의 창.
2. 확인 된 **Backup 항목**를 **Backup 관리 서버**, 또는 **복제 된 항목** 나열 합니다. 자격 증명 모음에 항목이 아직 나타날를 제거 합니다.

3. 자격 증명 모음에 더 이상 항목이 없을 경우 자격 증명 모음 대시보드에서 **삭제**를 클릭합니다.

    ![백업 데이터 삭제](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 자격 증명 모음을 삭제할 것인지 확인하려면 **예**를 클릭합니다. 자격 증명 모음이 삭제되고 포털이 **새로 만들기** 서비스 메뉴로 돌아갑니다.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>백업 프로세스를 중지하지만 데이터가 남아 있는 경우?

백업 프로세스를 중지 해도 실수로 데이터를 유지 하는 경우에 이전 섹션에 설명 된 대로 백업 데이터를 삭제 해야 합니다.

## <a name="next-steps"></a>다음 단계

[에 대 한 자세한](backup-azure-recovery-services-vault-overview.md) Recovery Services 자격 증명 모음입니다.
