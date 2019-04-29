---
title: Azure Backup 에이전트 FAQ
description: Azure Backup 에이전트 작동 방식, 백업 및 보존 제한과 관련된 일반적인 질문에 대한 대답입니다.
services: backup
author: trinadhk
manager: shreeshd
keywords: 백업 및 재해 복구; 백업 서비스
ms.service: backup
ms.topic: conceptual
ms.date: 8/6/2018
ms.author: trinadhk
ms.openlocfilehash: c1690fe6d0ce24bd319b042a3850bbfe487ffcfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641231"
---
# <a name="questions-about-the-azure-backup-agent"></a>Azure Backup 에이전트에 대한 질문
이 문서에서는 Azure Backup 에이전트 구성 요소를 빨리 이해하는 데 도움이 되는 일반적인 질문에 대한 대답을 제공합니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure Backup 서비스에 대한 질문도 게시할 수 있습니다.

## <a name="configure-backup"></a>백업 구성
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>최신 Azure Backup 에이전트를 어디서 다운로드할 수 있나요? <br/>
Windows Server, System Center DPM 또는 Windows 클라이언트를 백업하기 위한 최신 에이전트를 [여기](https://aka.ms/azurebackup_agent)에서 다운로드할 수 있습니다. 가상 머신을 백업하려는 경우 VM 에이전트(적절한 확장을 자동으로 설치)를 사용합니다. Azure 갤러리에서 만든 가상 머신에는 VM 에이전트가 이미 있습니다.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Azure Backup 에이전트를 구성할 때 저장소 자격 증명을 입력하라는 메시지가 표시됩니다. 저장소 자격 증명은 만료되나요?
예, 저장소 자격 증명은 48시간이 지나면 만료됩니다. 파일이 만료되면 Azure Portal에 로그인하고 해당 자격 증명 모음에서 저장소 자격 증명 파일을 다운로드하세요.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>어떤 드라이브 유형의 파일 및 폴더를 백업할 수 있나요? <br/>
다음과 같은 드라이브/볼륨은 백업할 수 없습니다.

* 이동식 미디어: 모든 백업 항목 원본은 고정된 것으로 보고되어야 합니다.
* 읽기 전용 볼륨: VSS(볼륨 섀도 복사본 서비스)가 작동하려면 볼륨에 데이터 쓰기가 가능해야 합니다.
* 오프라인 볼륨: VSS가 작동하려면 볼륨이 온라인 상태여야 합니다.
* 네트워크 공유: 온라인 백업을 사용하여 백업할 서버의 로컬 볼륨이어야 합니다.
* BitLocker로 보호된 볼륨: 볼륨의 잠금을 해제해야 백업이 실행될 수 있습니다.
* 파일 시스템 식별: NTFS는 지원되는 유일한 파일 시스템입니다.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>내 서버에서 어떤 파일 및 폴더 형식을 백업할 수 있나요?<br/>
다음과 같은 형식이 지원됩니다.

* 암호화
* 압축
* 스파스
* 압축 + 스파스
* 하드 링크: 지원되지 않음, 건너뜀
* 재분석 지점: 지원되지 않음, 건너뜀
* 암호화됨 + 스파스: 지원되지 않음, 건너뜀
* 압축된 스트림: 지원되지 않음, 건너뜀
* 스파스 스트림: 지원되지 않음, 건너뜀

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>VM 확장을 사용하여 Azure Backup 서비스에서 이미 지원하는 Azure VM에 Azure Backup 에이전트를 설치할 수 있나요? <br/>
그렇습니다. Azure Backup은 VM 확장을 사용하여 Azure VM에 대한 VM 수준 백업을 제공합니다. 게스트 Windows OS의 파일 및 폴더를 보호하려면 게스트 Windows OS에 Azure Backup 에이전트를 설치합니다.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Azure VM에 Azure Backup 에이전트를 설치하여 Azure VM에서 제공하는 임시 저장소에 존재하는 파일 및 폴더를 백업할 수 있나요? <br/>
예. 게스트 Windows OS에 Azure Backup 에이전트를 설치하고 임시 저장소에 파일 및 폴더를 백업합니다. 임시 저장소 데이터가 초기화된 후에는 Backup 작업이 실패합니다. 또한 임시 저장소 데이터가 삭제된 경우 비휘발성 저장소에만 복원할 수 있습니다.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>캐시 폴더의 최소 크기 요구 사항은 무엇인가요? <br/>
캐시 폴더의 크기는 백업하는 데이터의 양에 따라 결정됩니다. 백업 데이터의 전체 크기에 비교해서 캐시 폴더의 볼륨은 최소 5-10%의 여유 공간이 있어야 합니다. 볼륨에 여유 공간이 5% 미만일 경우 볼륨 크기를 늘리거나 [충분한 여유 공간이 있는 볼륨으로 캐시 폴더를 이동](backup-azure-file-folder-backup-faq.md#backup)합니다.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>다른 데이터 센터에 내 서버를 등록하려면 어떻게 해야 하나요?<br/>
Backup 데이터는 등록된 자격 증명 모음의 데이터 센터로 전송됩니다. 데이터 센터를 변경하는 가장 쉬운 방법은 에이전트를 제거하고 다시 설치한 후 원하는 데이터 센터에 속한 새 자격 증명 모음에 등록하는 것입니다.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Azure Backup 에이전트는 Windows 2012 중복제거를 사용하는 서버에서 작동합니까? <br/>
예. 에이전트 서비스는 백업 작업을 준비할 때 중복 제거 된 데이터를 일반 데이터로 변환 합니다. 그 다음 백업에 대한 데이터를 최적화하고 데이터를 암호화한 다음 온라인 백업 서비스에 암호화된 데이터를 보냅니다.

## <a name="prerequisites-and-dependencies"></a>필수 구성 요소 및 종속성
### <a name="what-features-of-microsoft-azure-recovery-services-mars-agent-require-net-framework-452-and-higher"></a>MARS(Microsoft Azure Recovery Services) 에이전트의 어떤 기능에 .NET Framework 4.5.2 이상이 필요한가요?
*데이터 복구* 마법사에서 개별 파일 및 폴더의 복원을 활성화하는 [즉시 복원](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) 기능에 .NET Framework 4.5.2 이상이 필요합니다.

## <a name="backup"></a>Backup
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Azure Backup 에이전트에 대해 지정된 캐시 위치를 변경하려면 어떻게 해야 하나요?<br/>
다음 목록을 사용하여 캐시 위치를 변경합니다.

1. 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 Backup 엔진을 중지합니다.

    ```PS C:\> Net stop obengine```

2. 파일을 이동하지 마십시오. 대신 캐시 공간 폴더를 충분한 공간이 있는 다른 드라이브로 복사합니다. 백업이 새 캐시 공간으로 작업 중임을 확인한 후 원래 캐시 공간을 제거할 수 있습니다.
3. 다음 레지스트리 항목을 새 캐시 공간 폴더의 경로로 업데이트합니다.<br/>

    | 레지스트리 경로 | 레지스트리 키 | 값 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*새 캐시 폴더 위치* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*새 캐시 폴더 위치* |

4. 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 Backup 엔진을 다시 시작합니다.

    ```PS C:\> Net start obengine```

새 캐시 위치로 성공적으로 백업 작성이 완료되면 원래 캐시 폴더를 제거할 수 있습니다.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Azure Backup 에이전트가 예상대로 작동하도록 캐시 폴더를 어디에 배치해야 하나요?<br/>
캐시 폴더에 대해 다음 위치는 권장되지 않습니다.

* 네트워크 공유 또는 이동식 미디어: 캐시 폴더는 온라인 백업을 사용하여 백업해야 하는 서버에 대해 로컬이어야 합니다. 네트워크 위치 또는 USB 드라이브 같은 이동식 미디어는 지원되지 않습니다.
* 오프라인 볼륨: 캐시 폴더는 Azure Backup 에이전트를 사용하는 예상된 백업에 대해 온라인 상태여야 합니다.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>지원되지 않는 캐시 폴더의 특성이 있나요?<br/>
다음과 같은 특성 또는 해당 조합은 캐시 폴더에 지원되지 않습니다.

* 암호화
* 중복 제거
* 압축
* 스파스
* 재분석 지점

캐시 폴더와 메타데이터 VHD에는 모두 Azure Backup 에이전트에 필요한 특성이 없습니다.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Backup 서비스에서 사용되는 대역폭의 양을 조정하는 방법이 있나요?<br/>
  예, Backup 에이전트에서 **속성 변경** 옵션을 사용하여 대역폭을 조정합니다. 대역폭을 사용하는 경우 시간과 대역폭의 양을 조정할 수 있습니다. 단계별 지침은 **[네트워크 제한 사용](backup-configure-vault.md#enable-network-throttling)** 을 참조하세요.

## <a name="restore"></a>복원

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?
진행 중인 복원 작업이 취소되면 복원 프로세스가 중지되고 취소 전에 모든 파일이 복원되면 롤백 없이 구성된 대상(원래 또는 대체 위치)에 남아 있습니다.


## <a name="manage-backups"></a>백업 관리

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Azure에 데이터를 백업하는 Windows 서버 이름을 바꾸면 어떻게 되나요?<br/>
서버 이름을 바꾸면 현재 구성된 모든 백업이 중지됩니다. Backup 자격 증명 모음에 서버의 새 이름을 등록합니다. 자격 증명 모음에 새 이름을 등록하면 첫 번째 백업 작업은 *전체* 백업입니다. 이전 서버 이름으로 자격 증명 모음에 백업된 데이터를 복원해야 하는 경우에는 **데이터 복구** 마법사의 [**다른 서버**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) 옵션을 사용합니다.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Azure Backup 에이전트를 사용하여 Backup 정책에서 지정할 수 있는 최대 파일 경로 길이는 무엇인가요? <br/>
Azure Backup 에이전트는 NTFS에 의존합니다. [파일 경로 길이 사양은 Windows API에 의해 제한됩니다](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths). 보호하려는 파일의 경로가 Windows API에 허용되는 길이보다 긴 경우 상위 폴더 또는 디스크 드라이브를 백업합니다.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure Backup 에이전트를 사용하는 Azure Backup 정책의 파일 경로에 어떤 문자가 허용되나요? <br>
 Azure Backup 에이전트는 NTFS에 의존합니다. 파일 사양의 일부분으로 [NTFS 지원 문자](/windows/desktop/FileIO/naming-a-file#naming_conventions) 를 사용할 수 있습니다.

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>백업 정책을 구성했는데도 "Azure Backup이 이 서버에 대해 구성되지 않았습니다"라는 경고가 표시됩니다. <br/>
이 경고는 로컬 서버에 저장된 백업 일정 설정과 백업 자격 증명 모음에 저장된 설정이 동일하지 않은 경우에 발생합니다. 서버 혹은 설정이 좋은 상태로 복구된 경우, 백업 일정은 동기화를 잃을 수 있습니다. 이 경고가 발생하면 [백업 정책을 다시 구성](backup-azure-manage-windows-server.md) 한 다음 **지금 백업 실행** 을 수행하여 로컬 서버를 Azure와 다시 동기화합니다.
