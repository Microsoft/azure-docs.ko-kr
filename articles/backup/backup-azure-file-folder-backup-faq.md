---
title: Microsoft Azure Recovery Services (MARS) 에이전트-FAQ
description: Azure Backup를 사용 하 여 파일과 폴더를 백업 하는 방법에 대 한 일반적인 질문을 해결 합니다.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: e3a5b6d07982c3261b457d4999025c44489c0a8d
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182516"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>MARS (질문과 대답 Microsoft Azure Recovery Services) 에이전트

이 문서에서는 [Azure Backup](backup-overview.md) 서비스에서 MARS (Microsoft Azure Recovery Services) 에이전트로 데이터를 백업 하는 방법에 대 한 일반적인 질문에 답변 합니다.

## <a name="configure-backups"></a>백업 구성

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>최신 버전의 MARS 에이전트는 어디에서 다운로드할 수 있나요?

Windows Server 컴퓨터, System Center DPM 및 Microsoft Azure Backup 서버를 백업할 때 사용 되는 최신 MARS 에이전트를 [다운로드할](https://aka.ms/azurebackup_agent)수 있습니다.

### <a name="where-can-i-download-the-vault-credentials-file"></a>자격 증명 모음 자격 증명 파일은 어디에서 다운로드할 수 있나요?

Azure Portal에서 자격 증명 모음에 대 한 **속성** 으로 이동 합니다. **백업 자격 증명**에서 **최신 Recovery Services 에이전트를 이미 사용 하 고 있는**확인란을 선택 합니다. **다운로드**를 선택합니다.

![자격 증명 다운로드](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>자격 증명 모음 자격 증명의 유효 기간은 얼마 인가요?

자격 증명 모음 자격 증명은 10 일 후에 만료 됩니다. 자격 증명 파일이 만료 되 면 Azure Portal에서 파일을 다시 다운로드 합니다.

### <a name="what-characters-are-allowed-for-the-passphrase"></a>암호에 허용 되는 문자는 무엇 인가요?

암호는 ascii 문자 집합의 문자를 사용 해야 하며 [ascii 값이 127 보다 작거나 같아야](https://docs.microsoft.com/office/vba/language/reference/user-interface-help/character-set-0127)합니다.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>어떤 드라이브에서 파일 및 폴더를 백업할 수 있나요?

다음 유형의 드라이브 및 볼륨은 백업할 수 없습니다.

* 이동식 미디어: 모든 백업 항목 소스는 고정 된 것으로 보고 해야 합니다.
* 읽기 전용 볼륨: 볼륨은 VSS (볼륨 섀도 복사본 서비스)가 작동 하려면 쓰기 가능 해야 합니다.
* 오프 라인 볼륨: VSS가 작동 하려면 볼륨이 온라인 상태 여야 합니다.
* 네트워크 공유: 온라인 백업을 사용 하 여 백업할 서버의 로컬 볼륨 이어야 합니다.
* BitLocker로 보호 된 볼륨: 볼륨의 잠금을 해제 해야 백업이 실행 될 수 있습니다.
* 파일 시스템 식별: NTFS가 유일하게 지원되는 파일 시스템입니다.

### <a name="what-file-and-folder-types-are-supported"></a>지원 되는 파일 및 폴더 유형은 무엇입니까?

백업에 지원 되는 파일 및 폴더 유형에 대해 [자세히 알아보세요](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) .

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>MARS 에이전트를 사용 하 여 Azure VM에서 파일 및 폴더를 백업할 수 있나요?  

예. Azure Backup은 Azure VM 에이전트에 대 한 VM 확장을 사용 하 여 Azure vm에 대 한 VM 수준 백업을 제공 합니다. VM의 게스트 Windows 운영 체제에서 파일과 폴더를 백업 하려는 경우 MARS 에이전트를 설치 하 여이 작업을 수행할 수 있습니다.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>MARS 에이전트를 사용 하 여 Azure VM의 임시 저장소에 파일 및 폴더를 백업할 수 있나요?

예. MARS 에이전트를 설치 하 고 게스트 Windows 운영 체제의 파일 및 폴더를 임시 저장소에 백업 합니다.

* 임시 저장소 데이터가 초기화 되 면 백업 작업이 실패 합니다.
* 임시 저장소 데이터를 삭제 한 경우 비휘발성 저장소로만 복원할 수 있습니다.

### <a name="how-do-i-register-a-server-to-another-region"></a>다른 지역에 서버를 등록 어떻게 할까요??

백업 데이터는 서버가 등록 된 자격 증명 모음의 데이터 센터로 전송 됩니다. 데이터 센터를 변경 하는 가장 쉬운 방법은 에이전트를 제거 하 고 다시 설치한 다음 필요한 지역의 새 자격 증명 모음에 컴퓨터를 등록 하는 것입니다.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 에이전트가 Windows Server 2012 중복 제거를 지원 하나요?

예. MARS 에이전트는 백업 작업을 준비할 때 중복 제거 된 데이터를 일반 데이터로 변환 합니다. 그런 다음 백업에 대 한 데이터를 최적화 하 고 데이터를 암호화 한 다음 암호화 된 데이터를 자격 증명 모음에 보냅니다.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>MARS 에이전트를 설치 하 고 구성 하려면 관리자 권한이 필요 한가요?

예, mars 에이전트를 설치 하 고 MARS 콘솔을 사용 하 여 백업을 구성 하는 경우 사용자가 보호 된 서버의 로컬 관리자 여야 합니다.

## <a name="manage-backups"></a>백업 관리

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>백업용으로 구성 된 Windows 컴퓨터의 이름을 바꾸면 어떻게 되나요?

Windows 컴퓨터의 이름을 바꾸면 현재 구성 된 모든 백업이 중지 됩니다.

* 백업 자격 증명 모음에 새 컴퓨터 이름을 등록 해야 합니다.
* 자격 증명 모음에 새 이름을 등록 하는 경우 첫 번째 작업은 *전체* 백업입니다.
* 이전 서버 이름을 사용 하 여 자격 증명 모음에 백업 된 데이터를 복구 해야 하는 경우 데이터 복구 마법사에서 대체 위치로 복원 하는 옵션을 사용 합니다. [자세한 정보를 알아보세요](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>백업의 최대 파일 경로 길이는 얼마 인가요?

MARS 에이전트는 NTFS를 사용 하며 [WINDOWS API](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths)에 의해 제한 된 filepath 길이 지정을 사용 합니다. 보호 하려는 파일이 허용 된 값 보다 길면 부모 폴더 또는 디스크 드라이브를 백업 합니다.  

### <a name="what-characters-are-allowed-in-file-paths"></a>파일 경로에 허용 되는 문자는 무엇입니까?

MARS 에이전트는 NTFS를 사용 하며 파일 이름/경로에서 [지원 되는 문자](/windows/win32/FileIO/naming-a-file#naming-conventions) 를 허용 합니다.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>"이 서버에 대해 Azure backup이 구성 되지 않았습니다." 라는 경고가 나타납니다.

이 경고는 로컬 서버에 저장 된 백업 일정 설정이 백업 자격 증명 모음에 저장 된 설정과 동일 하지 않을 때 백업 정책을 구성한 경우에도 나타날 수 있습니다.

* 서버 또는 설정이 알려진 양호한 상태로 복구 된 경우 백업 일정은 동기화 되지 않을 수 있습니다.
* 이 경고가 표시 되 면 백업 정책을 다시 [구성한](backup-azure-manage-windows-server.md) 다음 요청 시 백업을 실행 하 여 로컬 서버를 Azure와 다시 동기화 합니다.

## <a name="manage-the-backup-cache-folder"></a>백업 캐시 폴더 관리

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>캐시 폴더의 최소 크기 요구 사항은 무엇인가요?

캐시 폴더의 크기는 백업 중인 데이터의 양을 결정 합니다.

* 캐시 폴더 볼륨에는 백업 데이터의 총 크기 중 5-10% 이상에 해당 하는 여유 공간이 있어야 합니다.
* 볼륨의 사용 가능한 공간이 5% 미만이 면 볼륨 크기를 늘리거나 캐시 폴더를 공간이 충분 한 볼륨으로 이동 합니다. [이 단계](#how-do-i-change-the-cache-location-for-the-mars-agent)를 수행 합니다.
* Windows 시스템 상태를 백업 하는 경우 캐시 폴더를 포함 하는 볼륨에 추가 30-35 GB의 사용 가능한 공간이 필요 합니다.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>스크래치 폴더가 유효 하 고 액세스할 수 있는지 확인 하는 방법

1. 기본적으로 스크래치 폴더는에 있습니다. `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 스크래치 폴더 위치의 경로가 아래에 표시 된 레지스트리 키 항목의 값과 일치 하는지 확인 합니다.

    | 레지스트리 경로 | 레지스트리 키 | 값 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 캐시 폴더 위치* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 캐시 폴더 위치* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>MARS 에이전트의 캐시 위치를 변경 어떻게 할까요??

1. 관리자 권한 명령 프롬프트에서이 명령을 실행 하 여 백업 엔진을 중지 합니다.

    ```Net stop obengine```
2. 시스템 상태 백업을 구성한 경우 디스크 관리를 열고 형식의 이름을 사용 하 여 디스크를 분리 `"CBSSBVol_<ID>"` 합니다.
3. 기본적으로 스크래치 폴더는에 있습니다. `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. `\Scratch`공간이 충분 한 다른 드라이브에 전체 폴더를 복사 합니다. 콘텐츠가 복사 되었는지 확인 하 고 이동 하지 않습니다.
5. 새로 이동한 스크래치 폴더의 경로를 사용 하 여 다음 레지스트리 항목을 업데이트 합니다.

    | 레지스트리 경로 | 레지스트리 키 | 값 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 스크래치 폴더 위치* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 스크래치 폴더 위치* |

6. 관리자 권한 명령 프롬프트에서 백업 엔진을 다시 시작 합니다.

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 주문형 백업을 실행합니다. 새 위치를 사용 하 여 백업이 성공적으로 완료 되 면 원래 캐시 폴더를 제거할 수 있습니다.

### <a name="where-should-the-cache-folder-be-located"></a>캐시 폴더를 어디에 배치 해야 하나요?

캐시 폴더에 대 한 다음 위치는 권장 되지 않습니다.

* 네트워크 공유/이동식 미디어: 캐시 폴더는 온라인 백업을 사용 하 여 백업 해야 하는 서버에 대해 로컬 이어야 합니다. 네트워크 위치 또는 이동식 미디어 (예: USB 드라이브)는 지원 되지 않습니다.
* 오프 라인 볼륨: 캐시 폴더는 Azure Backup 에이전트를 사용 하는 예상 백업을 위해 온라인 상태 여야 합니다.

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>지원 되지 않는 캐시 폴더의 특성이 있나요?

다음 특성 또는 해당 조합은 캐시 폴더에 대해 지원 되지 않습니다.

* 암호화됨
* 중복 제거
* Compressed
* 스파스
* 재분석 지점

캐시 폴더와 메타 데이터 VHD에 Azure Backup 에이전트에 필요한 특성이 없습니다.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>백업에 사용 되는 대역폭의 양을 조정 하는 방법이 있나요?

예, MARS 에이전트의 **속성 변경** 옵션을 사용 하 여 대역폭과 타이밍을 조정할 수 있습니다. [자세한 정보를 알아보세요](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>복원

### <a name="manage"></a>관리

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>암호를 잊은 경우 복구할 수 있나요?

Azure Backup 에이전트에는 복원 중에 백업 된 데이터의 암호를 해독 하는 데 필요한 암호 (등록 중에 사용자가 입력)가 필요 합니다. 다음 시나리오를 검토 하 여 손실 된 암호를 처리 하는 옵션을 이해 합니다.

| 원본 컴퓨터 <br> *(백업이 수행 된 원본 컴퓨터)* | 암호 | 사용 가능한 옵션 |
| --- | --- | --- |
| 사용 가능 |실패 |원래 컴퓨터 (백업이 수행 된 위치)를 사용할 수 있고 동일한 Recovery Services 자격 증명 모음에 등록 되어 있는 경우 다음 [단계](./backup-azure-manage-mars.md#re-generate-passphrase)를 수행 하 여 암호를 다시 생성할 수 있습니다.  |
| 실패 |실패 |데이터 또는 데이터를 복구할 수 없습니다. |

다음 조건을 확인합니다.

* 동일한 원래 컴퓨터에서 에이전트를 제거 하 고 다시 등록 하는 경우
  * 백업 *데이터를 복원할*수 있습니다.
  * *다른 암호*를 사용할 경우 백업한 데이터를 복원할 수 없습니다.
* 를 사용 하 여 *다른 컴퓨터* 에 에이전트를 설치 하는 경우
  * 원본 컴퓨터에서 사용 되는 *동일한 암호* 를 사용 하 여 백업 된 데이터를 복원할 수 있습니다.
  * *다른 암호*를 사용할 경우 백업한 데이터를 복원할 수 없습니다.
* 원래 컴퓨터가 손상 된 경우 (MARS 콘솔을 통해 암호를 다시 생성 하지 않도록 방지) MARS 에이전트에서 사용 하는 원래 스크래치 폴더를 복원 하거나 액세스할 수 있는 경우에는 (암호를 잊어버린 경우) 복원할 수 있습니다. 추가 지원이 필요한 경우 고객 지원에 문의 하세요.

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>원본 컴퓨터가 손실 된 경우 (백업이 수행 된 위치) 복구 어떻게 할까요?

원본 컴퓨터의 동일한 암호 (등록 중에 제공한 암호)가 있는 경우 백업 된 데이터를 대체 컴퓨터로 복원할 수 있습니다. 복원 옵션을 이해 하려면 아래 시나리오를 검토 하세요.

| 원본 컴퓨터 | 암호 | 사용 가능한 옵션 |
| --- | --- | --- |
| 실패 |사용 가능 |원래 컴퓨터를 등록할 때 제공한 것과 동일한 암호를 사용 하 여 다른 컴퓨터에 MARS 에이전트를 설치 하 고 등록할 수 있습니다. **복구 옵션**  >  **Another location** 을 선택 하 여 복원을 수행 합니다. 자세한 내용은 [이 문서](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)를 참조하세요.
| 실패 |실패 |데이터 또는 데이터를 복구할 수 없습니다. |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>백업 작업이 실패 하거나 오랜 시간 동안 실행 되 고 있지 않습니다. 보존 기간을 초과 했습니다. 그래도 복원할 수 있나요?

보안 조치로 Azure Backup은 보존 기간이 지난 경우에도 가장 최근 복구 지점을 유지 합니다. 백업이 다시 시작 되 고 새 복구 지점을 사용할 수 있게 되 면 이전 복구 지점은 지정 된 보존에 따라 제거 됩니다.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?

진행 중인 복원 작업이 취소 되 면 복원 프로세스가 중지 됩니다. 취소 하기 전에 복원 된 모든 파일은 롤백 없이 구성 된 대상 (원래 또는 대체 위치)에 유지 됩니다.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 에이전트는 파일, 폴더 및 볼륨에 대해 설정 된 Acl을 백업 하 고 복원 합니까?

* MARS 에이전트는 파일, 폴더 및 볼륨에 설정 된 Acl을 백업 합니다.
* 볼륨 복원 복구 옵션의 경우 MARS 에이전트는 복구 중인 파일이 나 폴더에 대 한 ACL 사용 권한을 복원 하는 옵션을 제공 합니다.
* 개별 파일 및 폴더 복구 옵션의 경우 MARS 에이전트는 ACL 사용 권한으로 복원 됩니다 (ACL 복원을 건너뛸 수 있는 옵션은 없음).

## <a name="next-steps"></a>다음 단계

Windows 컴퓨터를 백업 하는 방법에 [대해 알아봅니다](tutorial-backup-windows-server-to-azure.md) .
