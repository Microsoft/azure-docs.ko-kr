---
title: MARS 에이전트 백업 관리 및 모니터링
description: Azure Backup 서비스를 사용 하 여 MARS (Microsoft Azure Recovery Services) 에이전트 백업을 관리 하 고 모니터링 하는 방법에 대해 알아봅니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b3b648ca27a407640b42932fe2ed7c32f5109114
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145572"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup 서비스를 사용 하 여 MARS (Microsoft Azure Recovery Services) 에이전트 백업 관리

이 문서에서는 Microsoft Azure Recovery Services 에이전트를 사용 하 여 백업 되는 파일 및 폴더를 관리 하는 방법을 설명 합니다.

## <a name="modify-a-backup-policy"></a>백업 정책 수정

백업 정책을 수정할 때 새 항목을 추가 하거나, 백업에서 기존 항목을 제거 하거나, 제외 설정을 사용 하 여 백업에서 파일을 제외할 수 있습니다.

- **항목 추가** 백업할 새 항목을 추가 하는 경우에만이 옵션을 사용 합니다. 기존 항목을 제거 하려면 **항목 제거** 또는 **제외 설정** 옵션을 사용 합니다.  
- **항목 제거** 이 옵션을 사용 하 여 백업에서 항목을 제거 합니다.
  - **항목을 제거**하는 대신 볼륨 내의 모든 항목을 제거 하는 데 **제외 설정을** 사용 합니다.
  - 볼륨의 모든 선택을 취소 하면 항목의 이전 백업이 수정 범위를 제외 하 고 마지막 백업 시 보존 설정에 따라 보존 됩니다.
  - 이러한 항목을 변경 하면 첫 번째 전체 백업이 수행 되 고 새 정책 변경 내용은 이전 백업에 적용 되지 않습니다.
  - 전체 볼륨을 선택을 취소 하면 보존 정책을 수정 하기 위한 범위 없이 이전 백업이 유지 됩니다.
- **제외 설정** 이 옵션을 사용 하 여 특정 항목을 백업에서 제외할 수 있습니다.

### <a name="add-new-items-to-existing-policy"></a>기존 정책에 새 항목 추가

1. **작업**에서 **백업 예약**을 선택 합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault/schedule-first-backup.png)

2. **정책 항목 선택** 탭에서 **파일 및 폴더에 대 한 백업 일정 수정** 을 선택 하 고 **다음**을 선택 합니다.

    ![정책 항목 선택](./media/backup-azure-manage-mars/select-policy-items.png)

3. **백업 일정 수정 또는 중지** 탭에서 **백업 항목 또는 시간 변경** 을 선택 하 고 **다음**을 선택 합니다.

    ![백업 수정 또는 예약](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 백업할 **항목 선택** 탭에서 **항목 추가** 를 선택 하 여 백업할 항목을 추가 합니다.

    ![백업 수정 또는 예약 항목 추가](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **항목 선택** 창에서 추가 하려는 파일 또는 폴더를 선택 하 고 **확인**을 선택 합니다.

    ![항목 선택](./media/backup-azure-manage-mars/select-item.png)

6. 다음 단계를 완료 하 고 **마침** 을 선택 하 여 작업을 완료 합니다.

### <a name="add-exclusion-rules-to-existing-policy"></a>기존 정책에 제외 규칙 추가

제외 규칙을 추가 하 여 백업 하지 않을 파일 및 폴더를 건너뛸 수 있습니다. 새 정책을 정의 하거나 기존 정책을 수정 하는 동안이 작업을 수행할 수 있습니다.

1. 작업 창에서 **백업 예약**을 선택 합니다. **백업할 항목 선택** 으로 이동 하 고 **제외 설정**을 선택 합니다.

    ![제외 설정](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **제외 설정**에서 **제외 추가**를 선택 합니다.

    ![제외 추가](./media/backup-azure-manage-mars/add-exclusion.png)

3. **제외할 항목 선택**에서 파일 및 폴더를 찾아 제외 하려는 항목을 선택 하 고 **확인**을 선택 합니다.

    ![제외할 항목 선택](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 기본적으로 선택 된 폴더 내의 모든 **하위 폴더** 는 제외 됩니다. **예** 또는 **아니요**를 선택 하 여이를 변경할 수 있습니다. 다음과 같이를 편집 하 고 제외할 파일 형식을 지정할 수 있습니다.

    ![하위 폴더 유형 선택](./media/backup-azure-manage-mars/subfolders-type.png)

5. 다음 단계를 완료 하 고 **마침** 을 선택 하 여 작업을 완료 합니다.

### <a name="remove-items-from-existing-policy"></a>기존 정책에서 항목 제거

1. 작업 창에서 **백업 예약**을 선택 합니다. **백업할 항목 선택**으로 이동 합니다. 목록에서 백업 일정에서 제거 하려는 파일 및 폴더를 선택 하 고 **항목 제거**를 선택 합니다.

    ![제거할 항목을 선택 하십시오.](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > 정책에서 볼륨을 완전히 제거 하는 경우에는 주의 하십시오.  다시 추가 해야 하는 경우 새 볼륨으로 처리 됩니다. 다음 예약 된 백업에서는 증분 백업 대신 초기 백업 (전체 백업)을 수행 합니다. 나중에 항목을 일시적으로 제거 하 고 추가 해야 하는 경우에는 전체 백업 대신 증분 백업을 위해 **항목을 제거** 하는 대신 **제외 설정을** 사용 하는 것이 좋습니다.

2. 다음 단계를 완료 하 고 **마침** 을 선택 하 여 작업을 완료 합니다.

## <a name="stop-protecting-files-and-folder-backup"></a>파일 및 폴더 백업 보호 중지

파일 및 폴더 백업 보호를 중지 하는 방법에는 다음 두 가지가 있습니다.

- **보호를 중지 하 고 백업 데이터를 보존**합니다.
  - 이 옵션은 보호에서 이후의 모든 백업 작업을 중지 합니다.
  - Azure Backup 서비스는 기존 복구 지점은 계속 유지 합니다.  
  - 만료 되지 않은 복구 지점의 백업 데이터를 복원할 수 있습니다.
  - 보호를 다시 시작 하기로 결정 한 경우 *백업 일정 다시 사용* 옵션을 사용할 수 있습니다. 그 후에는 데이터가 새 보존 정책에 따라 보존 됩니다.
- **보호를 중지 하 고 백업 데이터를 삭제**합니다.
  - 이 옵션은 미래의 모든 백업 작업에서 데이터를 보호 하 고 모든 복구 지점이 삭제 되지 않도록 합니다.
  - *이 백업 항목에 대 한 데이터를 삭제 했습니다. 메시지가 포함 된 백업 데이터 삭제 경고 전자 메일을 받게 됩니다. 이 데이터는 14 일 동안 일시적으로 사용할 수 있으며, 그 이후에는 영구적으로 삭제* 되 고 권장 조치는 *14 일 이내에 백업 항목을 다시 보호 하 여 데이터를 복구 합니다.*
  - 보호를 다시 시작 하려면 삭제 작업 으로부터 14 일 이내에 다시 보호 하십시오.

### <a name="stop-protection-and-retain-backup-data"></a>보호 중지 및 백업 데이터 보존

1. MARS 관리 콘솔을 열고 **작업 창**으로 이동 하 여 **Backup 예약을 선택**합니다.

    ![백업 일정 선택](./media/backup-azure-manage-mars/mars-actions.png)
1. **정책 항목 선택** 페이지에서 **파일 및 폴더에 대 한 백업 일정 수정** 을 선택 하 고 **다음**을 선택 합니다.

    ![정책 항목 선택](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 예약 된 **백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용 중지를 선택 하지만 일정이 다시 활성화 될 때까지 저장 된 백업 유지**를 선택 합니다. 그다음에 **다음**을 선택합니다.

    ![예약 된 백업을 중지 합니다.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **예약 된 백업 일시 중지**에서 정보를 검토 하 고 **마침**을 선택 합니다.

    ![예약 된 백업을 일시 중지 합니다.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **백업 수정 진행률**에서 일정 백업 일시 중지가 성공 상태 인지 확인 하 고 **닫기** 를 선택 하 여 완료 합니다.

### <a name="stop-protection-and-delete-backup-data"></a>보호 중지 및 백업 데이터 삭제

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동 하 여 **Backup 예약**을 선택 합니다.
2. 예약 된 **백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용 중지와 저장 된 백업 모두 삭제**를 선택 합니다. 그다음에 **다음**을 선택합니다.

    ![예약 된 백업 수정 또는 중지](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 예약 된 **백업 중지** 페이지에서 **마침**을 선택 합니다.

    ![예약 된 백업을 중지 하 고 마침을 선택 합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성 해야 하는 보안 PIN (개인 식별 번호)을 입력 하 라는 메시지가 표시 됩니다. 이렇게 하려면 먼저 Azure Portal에 로그인 합니다.
5. **Recovery Services 자격 증명 모음**  >  **설정**  >  **속성**으로 이동 합니다.
6. **보안 PIN**아래에서 **생성**을 선택 합니다. 이 PIN을 복사 합니다. PIN은 5 분 동안만 유효 합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인**을 선택 합니다.

    ![보안 PIN을 생성 합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. **백업 변경 진행률** 페이지에 다음 메시지가 표시 됩니다. 삭제 된 *백업 데이터는 14 일 동안 보존 됩니다. 이 시간 후에는 백업 데이터가 영구적으로 삭제 됩니다.*  

    ![백업 진행률 수정](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제 한 후 포털에서 다음 단계를 수행 합니다.

## <a name="re-enable-protection"></a>보호 다시 사용

데이터를 유지 하 고 보호를 다시 시작 하기로 결정 한 경우 보호를 중지 한 경우 백업 정책 수정을 사용 하 여 백업 일정을 다시 사용 하도록 설정할 수 있습니다.

1. **작업** 에서 **백업 예약**을 선택 합니다.
1. **백업 일정 다시 사용을 선택 합니다. 백업 항목 또는 시간을 수정** 하 고 **다음**을 선택할 수도 있습니다.<br>

    ![백업 일정 다시 사용](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **백업할 항목 선택**에서 **다음**을 선택 합니다.

    ![백업할 항목 선택](./media/backup-azure-manage-mars/re-enable-next.png)
1. **백업 일정 지정**에서 백업 일정을 지정 하 고 **다음**을 선택 합니다.
1. **보존 정책 선택**에서 보존 기간을 지정 하 고 **다음**을 선택 합니다.
1. 마지막으로 **확인** 화면에서 정책 세부 정보를 검토 하 고 **마침**을 선택 합니다.

## <a name="re-generate-passphrase"></a>암호 다시 생성

암호는 MARS 에이전트를 사용 하 여 온-프레미스 또는 로컬 컴퓨터를 백업 또는 복원 하는 동안 데이터를 암호화 하 고 암호 해독 하는 데 사용 됩니다. 암호를 분실 하거나 잊은 경우 다음 단계를 수행 하 여 암호를 다시 생성할 수 있습니다 (컴퓨터가 Recovery Services 자격 증명 모음에 등록 되 고 백업이 구성 된 경우).

1. MARS 에이전트 콘솔에서 **작업 창**  >  **속성 변경** >로 이동 합니다. 그런 다음 **암호화 탭**으로 이동 합니다.<br>
1. **암호 변경** 확인란을 선택 합니다.<br>
1. 새 암호를 입력 하거나 **암호 생성**을 선택 합니다.
1. **찾아보기** 를 선택 하 여 새 암호를 저장 합니다.

    ![암호를 생성 합니다.](./media/backup-azure-manage-mars/passphrase.png)

1. **확인** 을 선택 하 여 변경 내용을 적용 합니다.  Recovery Services 자격 증명 모음에 대 한 Azure Portal에서 [보안 기능](./backup-azure-security-feature.md#enable-security-features) 을 사용 하는 경우 보안 PIN을 입력 하 라는 메시지가 표시 됩니다. PIN을 받으려면이 [문서](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)에 나열 된 단계를 따르세요.<br>
1. 포털에서 보안 PIN을 붙여 넣고 **확인** 을 선택 하 여 변경 내용을 적용 합니다.<br>

    ![보안 PIN 붙여넣기](./media/backup-azure-manage-mars/passphrase2.png)
1. 암호를 원본 컴퓨터가 아닌 대체 위치에 안전 하 게 저장 하 고 Azure Key Vault 하는 것이 좋습니다. MARS 에이전트를 사용 하 여 여러 컴퓨터를 백업 하는 경우 모든 암호를 추적 하세요.

## <a name="managing-backup-data-for-unavailable-machines"></a>사용할 수 없는 컴퓨터의 백업 데이터 관리

이 섹션에서는 MARS로 보호 된 원본 컴퓨터를 삭제 하거나, 손상 되었거나, 맬웨어/랜 섬 웨어에 감염 되었거나, 서비스 해제 되어 있으므로 더 이상 사용할 수 없는 시나리오에 대해 설명 합니다.

이러한 컴퓨터의 경우 Azure Backup 서비스는 최근 복구 지점이 백업 정책에 지정 된 보존 규칙에 따라 만료 되지 않도록 합니다 (즉, 정리 되지 않음). 따라서 컴퓨터를 안전 하 게 복원할 수 있습니다.  백업 된 데이터에 대해 수행할 수 있는 다음 시나리오를 고려 하십시오.

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>시나리오 1: 원본 컴퓨터를 사용할 수 없으므로 더 이상 백업 데이터를 보존할 필요가 없습니다.

- [이 문서](backup-azure-delete-vault.md#delete-protected-items-on-premises)에 나열 된 단계를 사용 하 여 Azure Portal에서 백업 된 데이터를 삭제할 수 있습니다.

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>시나리오 2: 원본 컴퓨터를 사용할 수 없으므로 백업 데이터를 보존 해야 하는 경우

MARS의 백업 정책을 관리 하는 작업은 포털을 통해서가 아니라 MARS 콘솔을 통해 수행 됩니다. 만료 되기 전에 기존 복구 지점의 보존 설정을 확장 해야 하는 경우 컴퓨터를 복원 하 고 MARS 콘솔을 설치 하 고 정책을 확장 해야 합니다.

- 컴퓨터를 복원 하려면 다음 단계를 수행 합니다.
  1. [대체 대상 컴퓨터로 VM 복원](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. 원본 컴퓨터와 동일한 호스트 이름을 사용 하 여 대상 컴퓨터를 다시 만듭니다.
  1. 에이전트를 설치 하 고 동일한 암호를 사용 하 여 동일한 자격 증명 모음에 다시 등록
  1. MARS 클라이언트를 시작 하 여 요구 사항에 따라 보존 기간을 연장 합니다.
- MARS를 사용 하 여 보호 되는 새로 복원 된 컴퓨터는 백업을 계속 수행 합니다.  

## <a name="next-steps"></a>다음 단계

- 지원 되는 시나리오 및 제한 사항에 대 한 자세한 내용은 [MARS 에이전트에 대 한 지원 매트릭스](./backup-support-matrix-mars-agent.md)를 참조 하세요.
- [주문형 백업 정책 보존 동작](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)에 대해 자세히 알아보세요.
- 질문과 대답은 [MARS 에이전트 FAQ](backup-azure-file-folder-backup-faq.md)를 참조 하십시오.
