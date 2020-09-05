---
title: 에이전트 및 확장 문제 해결
description: 에이전트, 확장명 및 디스크와 관련된 Azure Backup 오류의 증상, 원인 및 해결 방법
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: e5fc26231cc5d3ad412371c2f8c187b2d0033ee4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182040"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup 오류 문제 해결: 에이전트 또는 확장 관련 문제

이 문서에서는 VM 에이전트 및 확장과의 통신에 관련된 Azure Backup 오류를 해결하는 데 도움이 될 수 있는 문제 해결 단계를 제공합니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>백업 실패 문제를 해결 하는 단계별 가이드

가장 일반적인 백업 오류는 아래에 나열 된 문제 해결 단계를 수행 하 여 자체 해결할 수 있습니다.

### <a name="step-1-check-azure-vm-health"></a>1 단계: Azure VM 상태 확인

- **AZURE vm 프로 비전 상태가 ' 실행 중 ' 인지 확인**: [VM 프로 비전 상태가](../virtual-machines/states-lifecycle.md#provisioning-states) **중지 됨/할당 취소 됨/업데이트 중** 상태 이면 백업 작업에 방해가 됩니다. *Azure Portal > vm > 개요 >* 를 열고 vm 상태를 확인 하 여 **실행 중인지** 확인 하 고 백업 작업을 다시 시도 합니다.
- **보류 중인 os 업데이트 또는 다시 부팅 검토**: VM에서 보류 중인 os 업데이트 또는 다시 부팅이 보류 중인지 확인 합니다.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>2 단계: Azure VM 게스트 에이전트 서비스 상태 확인

- **AZURE VM 게스트 에이전트 서비스가 시작 되 고 최신 상태 인지 확인 합니다**.
  - Windows VM에서:
    - **Services.msc** 로 이동 하 여 **Windows Azure VM 게스트 에이전트 서비스가** 실행 중인지 확인 합니다. 또한 [최신 버전이](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) 설치 되어 있는지 확인 합니다. 자세한 내용은 [WINDOWS VM 게스트 에이전트 문제](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)를 참조 하세요.
    - Azure VM 에이전트는 포털, PowerShell, 명령줄 인터페이스 또는 Azure Resource Manager 템플릿에서 Azure Marketplace 이미지에서 배포 된 모든 Windows VM에 기본적으로 설치 됩니다. Azure에 배포 되는 사용자 지정 VM 이미지를 만들 때 [에이전트를 수동으로 설치](../virtual-machines/extensions/agent-windows.md#manual-installation) 해야 할 수 있습니다.
    - 지원 매트릭스를 검토 하 여 [지원 되는 Windows 운영 체제](backup-support-matrix-iaas.md#operating-system-support-windows)에서 VM이 실행 되는지 확인 합니다.
  - Linux VM에서
    - 명령을 실행 하 여 Azure VM 게스트 에이전트 서비스가 실행 중인지 확인 `ps-e` 합니다. 또한 [최신 버전이](../virtual-machines/extensions/update-linux-agent.md) 설치 되어 있는지 확인 합니다. 자세히 알아보려면 [LINUX VM 게스트 에이전트 문제](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)를 참조 하세요.
    - [시스템 패키지에 대 한 LINUX VM 에이전트 종속성](../virtual-machines/extensions/agent-linux.md#requirements) 의 구성이 지원 되는지 확인 합니다. 예: 지원 되는 Python 버전은 2.6 이상입니다.
    - 지원 매트릭스를 검토 하 여 [지원 되는 Linux 운영 체제](backup-support-matrix-iaas.md#operating-system-support-linux) 에서 VM이 실행 되는지 확인 합니다.

### <a name="step-3-check-azure-vm-extension-health"></a>3 단계: Azure VM 확장 상태 확인

- **모든 AZURE VM 확장이 ' 프로 비전 성공 ' 상태 인지 확인**: 확장이 실패 상태 이면 백업을 방해할 수 있습니다.
- *Azure Portal > VM > 설정 > 확장 > 확장 상태를 열고* 모든 확장이 **프로 비전 성공** 상태에 있는지 확인 합니다.
- 모든 [확장 문제가](../virtual-machines/extensions/overview.md#troubleshoot-extensions) 해결 되었는지 확인 한 후 백업 작업을 다시 시도 하십시오.
- **COM + 시스템 응용 프로그램이** 실행 중인지 확인 합니다. 또한 **DTC(Distributed Transaction Coordinator) 서비스** 는 **네트워크 서비스 계정**으로 실행 되어야 합니다. 이 문서의 단계를 수행 하 여 [COM + 및 MSDTC 문제를 해결](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)합니다.

### <a name="step-4-check-azure-backup-vm-extension-health"></a>4 단계: VM 확장 상태 Azure Backup 확인

Azure Backup VM 스냅숏 확장을 사용 하 여 Azure 가상 컴퓨터의 응용 프로그램 일치 백업을 수행 합니다. Azure Backup는 백업을 사용 하도록 설정한 후 트리거된 첫 번째 예약 된 백업의 일부로 확장을 설치 합니다.

- **VMSnapshot 확장이 실패 상태에 있지 않은지 확인**:이 [섹션](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) 에 나열 된 단계에 따라 Azure Backup 확장이 정상 상태 인지 확인 합니다.

- **바이러스 백신이 확장을 차단 하**고 있는지 확인 합니다. 특정 바이러스 백신 소프트웨어로 인해 확장이 실행 되지 않을 수 있습니다.
  
  백업 오류가 발생 한 경우 이벤트 뷰어 응용 프로그램 ***로그*** 에 로그 항목이 있는지 확인 하 고 오류가 발생 한 ***응용 프로그램 이름을 IaaSBcdrExtension.exe***합니다. 항목이 표시 되는 경우 VM에 구성 된 바이러스 백신이 백업 확장의 실행을 제한 하는 것일 수 있습니다. 바이러스 백신 구성에서 다음 디렉터리를 제외 하 고 테스트 한 후 백업 작업을 다시 시도 합니다.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **네트워크 액세스가 필요한 지 확인**합니다. Azure Storage 확장 리포지토리에서 확장 패키지를 다운로드 하 고 확장 상태 업로드가 Azure Storage에 게시 됩니다. [자세한 정보를 알아보세요](../virtual-machines/extensions/features-windows.md#network-access).
  - 지원 되지 않는 버전의 에이전트를 사용 하는 경우 VM에서 해당 지역에 있는 Azure storage에 대 한 아웃 바운드 액세스를 허용 해야 합니다.
  - 게스트 방화벽이 나 프록시를 사용 하 여에 대 한 액세스를 차단 하는 경우에 `168.63.129.16` 는 위의 방법에 관계 없이 확장이 실패 합니다. 포트 80, 443 및 32526이 필요 합니다. [자세한 정보](../virtual-machines/extensions/features-windows.md#network-access).

- **Dhcp를 게스트 vm 내에서 사용 하도록 설정**: IaaS vm 백업이 작동 하려면 dhcp에서 호스트 또는 패브릭 주소를 가져오는 데 필요 합니다. 정적 개인 IP가 필요한 경우 Azure Portal 또는 PowerShell을 통해 구성 하 고 VM 내에서 DHCP 옵션을 사용 하도록 설정 하 여 [자세한 정보](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)를 확인 해야 합니다.

- **Vss 기록기 서비스가 실행 중인지 확인**: [Vss 기록기 문제를 해결](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state)하려면 다음 단계를 수행 합니다.
- **백업 모범 사례 지침 따르기**: [모범 사례를 검토 하 여 Azure VM backup을 사용 하도록 설정](backup-azure-vms-introduction.md#best-practices)합니다.
- **암호화 된 디스크에 대 한 지침 검토**: 암호화 된 디스크가 있는 vm에 대 한 백업을 사용 하도록 설정 하는 경우 필요한 모든 권한을 제공 했는지 확인 합니다. 자세히 알아보려면 [암호화 된 AZURE VM 백업 및 복원](backup-azure-vms-encryption.md)을 참조 하세요.

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 에이전트가 Azure Backup과 통신할 수 없습니다.

**오류 코드**: UserErrorGuestAgentStatusUnavailable <br>
**오류 메시지**: VM 에이전트가 Azure Backup와 통신할 수 없습니다.<br>

Azure VM 에이전트가 중지 되었거나, 오래 되었거나, 일관 되지 않은 상태에 있거나, 설치 되어 있지 않을 수 있습니다. 이러한 상태를 통해 Azure Backup 서비스에서 스냅숏을 트리거할 수 없습니다.

- Vm **상태가** **실행 중** 이 고 **에이전트 상태가** **준비**인지 확인 하는 **Azure Portal > vm > 설정 > 속성 > 창을 엽니다** . VM 에이전트가 중지 되었거나 일관 되지 않은 상태에 있는 경우 에이전트를 다시 시작 하십시오.<br>
  - Windows Vm의 경우 다음 [단계](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) 에 따라 게스트 에이전트를 다시 시작 합니다.<br>
  - Linux Vm의 경우 다음 [단계](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) 를 수행 하 여 게스트 에이전트를 다시 시작 합니다.
- **Azure Portal > VM > 설정 > 확장을 열고** 모든 확장이 **프로 비전 성공** 상태에 있는지 확인 > 합니다. 그렇지 않은 경우 다음 [단계](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) 에 따라 문제를 해결 합니다.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 스냅샷 상태에 대해 VM 에이전트와 통신할 수 없습니다.

**오류 코드**: GuestAgentSnapshotTaskStatusError<br>
**오류 메시지**: 스냅샷 상태에 대해 VM 에이전트와 통신할 수 없습니다. <br>

Azure Backup 서비스에 대 한 VM을 등록 하 고 예약 하면 Backup은 VM 백업 확장과 통신 하 여 작업을 시작 하 여 지정 시간 스냅숏을 수행 합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  

**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**원인 3: [스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**원인 4: [Vm 에이전트 구성 옵션이 설정 되지 않음 (Linux vm의 경우)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**원인 5: [응용 프로그램 제어 솔루션이 차단 IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVMProvisioningStateFailed - VM이 실패한 프로비저닝 상태입니다.

**오류 코드**: UserErrorVmProvisioningStateFailed<br>
**오류 메시지**: VM이 프로 비전 실패 상태에 있습니다.<br>

이 오류는 확장 오류 중 하나에서 VM을 프로 비전 실패 상태로 전환 하는 경우에 발생 합니다.<br>**Azure Portal > VM > 설정 > 확장 > 확장 상태를 열고** 모든 확장이 **프로 비전 성공** 상태 인지 확인 합니다. 자세히 알아보려면 [프로 비전 상태](../virtual-machines/states-lifecycle.md#provisioning-states)를 참조 하세요.

- VMSnapshot 확장이 실패 상태 이면 실패 한 확장을 마우스 오른쪽 단추로 클릭 하 고 제거 합니다. 주문형 백업을 트리거합니다. 이 작업은 확장을 다시 설치 하 고 백업 작업을 실행 합니다.  <br>
- 다른 확장이 실패 상태 이면 백업을 방해할 수 있습니다. 이러한 확장 문제가 해결 되었는지 확인 한 후 백업 작업을 다시 시도 하십시오.
- VM 프로 비전 상태가 업데이트 중 상태 이면 백업을 방해할 수 있습니다. 정상 상태 인지 확인 하 고 백업 작업을 다시 시도 하세요.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 복원 지점 컬렉션이 최대 한도에 도달했습니다.

**오류 코드**: UserErrorRpCollectionLimitReached <br>
**오류 메시지**: 복원 지점 컬렉션이 최대 한도에 도달했습니다. <br>

- 복구 지점 리소스 그룹에 복구 지점의 자동 정리를 방지 하는 잠금이 있는 경우이 문제가 발생할 수 있습니다.
- 하루에 여러 개의 백업이 트리거되는 경우에도 이 문제가 발생할 수 있습니다. 현재는 하루에 한 번만 백업 하는 것이 좋습니다. 즉시 복원 지점은 구성 된 스냅숏 보존 당 1-5 일 동안 유지 되 고 지정 된 시간에 18 개의 인스턴트 RPs만 VM에 연결 될 수 있기 때문입니다. <br>
- VM에 대 한 복원 지점 컬렉션 및 리소스 그룹의 복원 지점 수는 18 개를 초과할 수 없습니다. 새 복원 지점을 만들려면 기존 복원 지점을 삭제 합니다.

권장 작업:<br>
이 문제를 해결하려면 VM의 리소스 그룹에 대한 잠금을 제거하고 정리를 트리거하는 작업을 다시 시도합니다.
> [!NOTE]
> 백업 서비스는 VM의 리소스 그룹과 별개의 리소스 그룹을 만들어 복원 지점 컬렉션을 저장합니다. 백업 서비스에서 사용 하기 위해 만든 리소스 그룹을 잠그지 않는 것이 좋습니다. 백업 서비스에서 만든 리소스 그룹의 명명 형식은 AzureBackupRG_ `<Geo>` _ `<number>` 입니다. 예: *AzureBackupRG_northeurope_1*

**1단계: [복원 지점 리소스 그룹에서 잠금 제거](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2단계: [복원 지점 컬렉션 정리](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Backup에는 암호화된 VM을 백업할 수 있는 키 자격 증명 모음에 대한 충분한 사용 권한이 없습니다.

**오류 코드**: UserErrorKeyvaultPermissionsNotConfigured <br>
**오류 메시지**: Backup에는 암호화된 VM을 백업하기 위한 키 자격 증명 모음에 대한 충분한 사용 권한이 없습니다. <br>

암호화 된 Vm에서 백업 작업이 성공 하려면 키 자격 증명 모음에 액세스할 수 있는 권한이 있어야 합니다. 사용 권한은 [Azure Portal](./backup-azure-vms-encryption.md) 또는 [PowerShell](./backup-azure-vms-automation.md#enable-protection)을 통해 설정할 수 있습니다.

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 가상 머신에 네트워크 연결이 없으므로 스냅샷 작업이 실패했습니다.

**오류 코드**: ExtensionSnapshotFailedNoNetwork<br>
**오류 메시지**: 가상 머신에 네트워크 연결이 없으므로 스냅샷 작업이 실패했습니다.<br>

Azure Backup 서비스에 대 한 VM을 등록 하 고 예약 하면 Backup은 VM 백업 확장과 통신 하 여 작업을 시작 하 여 지정 시간 스냅숏을 수행 합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 완료 한 후 작업을 다시 시도 하세요.

**[스냅숏 상태를 검색할 수 없거나 스냅숏을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot 확장 작업이 실패했습니다.

**오류 코드**: ExtensionOperationFailedForManagedDisks <br>
**오류 메시지**: VMSnapshot 확장 작업이 실패했습니다.<br>

Azure Backup 서비스에 대 한 VM을 등록 하 고 예약 하면 Backup은 VM 백업 확장과 통신 하 여 작업을 시작 하 여 지정 시간 스냅숏을 수행 합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 2: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 3: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - 내부 오류가 발생하여 백업하지 못했습니다.

**오류 코드**: BackUpOperationFailed/BackUpOperationFailedV2 <br>
**오류 메시지**: 내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요. <br>

Azure Backup 서비스에 대한 VM을 등록하고 예약하면 백업은 VM 백업 확장과 통신함으로써 작업을 시작하여 지정 시간 스냅샷을 수행합니다. 다음 조건 중 하나라도 충족되지 못하면 스냅샷이 트리거되지 않을 수 있습니다. 스냅샷이 트리거되지 않으면 백업 실패가 발생할 수 있습니다. 다음 문제 해결 단계를 나열된 순서에 완료하고 작업을 다시 시도하세요.  
**원인 1: [에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**원인 2: [VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**원인 3: [스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**원인 4: [리소스 그룹 잠금으로 인해 Backup 서비스에 이전 복원 지점은 삭제할 수 있는 권한이](#remove_lock_from_the_recovery_point_resource_group) 없습니다.**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize-구성 된 디스크 크기는 현재 Azure Backup에서 지원 되지 않습니다.

**오류 코드**: UserErrorUnsupportedDiskSize <br>
**오류 메시지**: 구성 된 디스크 크기는 현재 Azure Backup에서 지원 되지 않습니다. <br>

32 TB 보다 큰 디스크 크기를 사용 하 여 VM을 백업 하는 경우 백업 작업이 실패할 수 있습니다. 또한 크기가 4mb 보다 큰 암호화 된 디스크의 백업은 현재 지원 되지 않습니다. 디스크를 분할 하 여 디스크 크기가 지원 되는 한도 보다 작거나 같은지 확인 하십시오.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 다른 백업 작업이 현재 진행 중이어서 백업을 시작할 수 없습니다.

**오류 코드**: UserErrorBackupOperationInProgress <br>
**오류 메시지**: 다른 백업 작업이 현재 진행 중 이므로 백업을 시작할 수 없습니다.<br>

기존 백업 작업이 진행 중 이므로 최근 백업 작업에 실패 했습니다. 현재 작업이 완료될 때까지 새 백업 작업을 시작할 수 없습니다. 현재 진행 중인 백업 작업이 완료된 후 다른 백업 작업을 트리거 또는 예약해야 합니다. 백업 작업 상태를 확인 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인 하 고 **모든 서비스**를 선택 합니다. Recovery Services를 입력 하 고 **Recovery Services 자격 증명 모음**을 선택 합니다. Recovery Services 자격 증명 모음의 목록이 표시됩니다.
2. Recovery Services 자격 증명 모음 목록에서 백업이 구성 된 자격 증명 모음을 선택 합니다.
3. 자격 증명 모음 대시보드 메뉴에서 **백업 작업** 을 선택 하 여 모든 백업 작업을 표시 합니다.
   - 백업 작업이 진행 중이면 백업 작업이 완료될 때까지 기다리거나 취소합니다.
     - 백업 작업을 취소 하려면 백업 작업을 마우스 오른쪽 단추로 클릭 하 고 **취소** 또는 [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob)사용을 선택 합니다.
   - 다른 자격 증명 모음에서 백업을 다시 구성한 경우 이전 자격 증명 모음에서 실행 중인 백업 작업이 없는지 확인 합니다. 존재 하는 경우 백업 작업을 취소 합니다.
     - 백업 작업을 취소 하려면 백업 작업을 마우스 오른쪽 단추로 클릭 하 고 **취소** 또는 [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) 사용을 선택 합니다.
4. 백업 작업을 다시 시도합니다.

예약 된 백업 작업이 더 오래 걸리고 다음 백업 구성과 충돌 하는 경우 [최선의 구현 방법](backup-azure-vms-introduction.md#best-practices), [백업 성능](backup-azure-vms-introduction.md#backup-performance)및 [복원 고려 사항](backup-azure-vms-introduction.md#backup-and-restore-considerations)을 검토 합니다.

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - 오류로 인해 백업하지 못했습니다. 자세한 내용은 작업 오류 메시지 세부 정보를 참조하세요.

**오류 코드**: usererrorcrpreportedusererror <br>
**오류 메시지**: 오류로 인해 백업 하지 못했습니다. 자세한 내용은 작업 오류 메시지 세부 정보를 참조 하세요.

이 오류는 IaaS VM에서 보고 됩니다. 문제의 근본 원인을 확인 하려면 Recovery Services 자격 증명 모음 설정으로 이동 합니다. **모니터링** 섹션에서 **백업 작업** 을 선택 하 여 필터링 하 고 상태를 확인 합니다. **실패** 를 선택 하 여 기본 오류 메시지 정보를 검토 합니다. 오류 정보 페이지의 권장 사항에 따라 추가 작업을 수행 합니다.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent-백업 실패:이 가상 머신은 Azure Backup로 보호 되 고 있지 않습니다.

**오류 코드**: UserErrorBcmDatasourceNotPresent <br>
**오류 메시지**: 백업 실패:이 가상 머신은 Azure Backup로 보호 되 고 있지 않습니다.

지정 된 가상 컴퓨터가 Azure Backup에 의해 보호 되 고 있는지 확인 합니다 (일시 중지 상태 아님). 이 문제를 해결 하려면 가상 컴퓨터가 활성 상태 인지 확인 한 후 작업을 다시 시도 하세요.

## <a name="causes-and-solutions"></a>원인 및 해결 방법

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>에이전트가 VM에 설치되어 있지만 응답하지 않습니다(Windows VM의 경우).

#### <a name="solution-for-this-error"></a>이 오류에 대 한 해결 방법

VM 에이전트가 손상되었거나 서비스가 중지되었습니다. VM 에이전트를 다시 설치하면 최신 버전을 가져올 수 있습니다. 또한 서비스와의 통신을 다시 시작하는 데도 도움이 됩니다.

1. VM 서비스(services.msc)에서 Microsoft Azure 게스트 에이전트 서비스가 실행 중인지 확인합니다. Windows Azure 게스트 에이전트 서비스를 다시 시작하고 백업을 시작해 보세요.
2. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되지 않으면 제어판에서 **프로그램 및 기능**으로 이동하여 Windows Azure 게스트 에이전트 서비스가 설치되어 있는지 확인합니다.
3. Microsoft Azure 게스트 에이전트가 **프로그램 및 기능**에 표시되면 Windows Azure 게스트 에이전트를 제거합니다.
4. [최신 버전의 에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하고 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
5. Microsoft Azure 게스트 에이전트 서비스가 서비스에 표시되는지 확인합니다.
6. 다음과 같이 주문형 백업을 실행합니다.
   - 포털에서 **지금 백업**을 선택합니다.

또는 VM에서 [Microsoft .NET 4.5가 설치되어 있는지](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 확인합니다. .NET 4.5는 VM 에이전트가 서비스와 통신하는 데 필요합니다.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM에 설치된 에이전트가 최신이 아닙니다(Linux VM의 경우).

#### <a name="solution"></a>솔루션

Linux VM에 대부분의 에이전트 관련 또는 확장 관련 오류는 이전 VM 에이전트에 영향을 주는 문제로 인해 발생합니다. 이 문제를 해결하려면 다음과 같은 일반 지침을 수행하세요.

1. [Linux VM 에이전트 업데이트](../virtual-machines/extensions/update-linux-agent.md)의 지침을 따르세요.

   > [!NOTE]
   > 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 *강력히 권장*합니다. GitHub에서 직접 에이전트 코드를 다운로드 하 여 업데이트 하지 않는 것이 좋습니다. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 최신 에이전트를 확인하려면 GitHub 리포지토리의 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 페이지로 이동하세요.

2. `ps -e` 명령을 실행하여 VM에 Azure 에이전트가 실행 중인지 확인합니다.

   이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.

   - Ubuntu의 경우: `service walinuxagent start`
   - 기타 배포의 경우: `service waagent start`

3. [에이전트 자동 다시 시작을 구성합니다](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. 새 테스트 백업을 실행합니다. 오류가 계속되면 VM에서 다음 로그를 수집합니다.

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

Waagent에 대 한 자세한 정보 로깅이 필요한 경우 다음 단계를 수행 합니다.

1. /etc/waagent.conf 파일에서 다음 줄을 찾습니다. **자세한 정보 로깅 사용(y|n)**
2. **Logs.Verbose** 값을 *n*에서 *y*로 변경합니다.
3. 변경 내용을 저장하고 이 섹션 앞부분에 설명된 단계를 완료하여 waagent를 다시 시작합니다.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM-에이전트 구성 옵션이 설정 되지 않음 (Linux Vm의 경우)

구성 파일(/etc/waagent.conf)은 waagent의 동작을 제어합니다. 구성 파일 옵션 **확장명. Enable** 을 **y** 로 설정 하 고 **프로 비전 해야 합니다.** 백업이 작동 하려면 에이전트를 **auto** 로 설정 해야 합니다.
VM 에이전트 구성 파일 옵션의 전체 목록은 다음을 참조 하세요. <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>응용 프로그램 제어 솔루션이 차단 IaaSBcdrExtension.exe

[AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) 또는 다른 응용 프로그램 제어 솔루션을 실행 하는 경우 규칙은 게시자 또는 경로를 기반으로 하며, **IaaSBcdrExtension.exe** 실행 파일이 실행 되지 못하도록 차단할 수 있습니다.

#### <a name="solution-to-this-issue"></a>이 문제에 대 한 해결 방법

`/var/lib`AppLocker (또는 다른 응용 프로그램 제어 소프트웨어)에서 경로 또는 **IaaSBcdrExtension.exe** 실행 파일을 제외 합니다.

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>스냅샷 상태를 검색할 수 없거나 스냅샷을 만들 수 없습니다.

VM 백업은 기본 스토리지 계정에 대한 스냅샷 명령 실행을 사용합니다. 스토리지 계정에 액세스할 수 없거나 스냅샷 작업의 실행이 지연되기 때문에 백업이 실패할 수 있습니다.

#### <a name="solution-for-this-issue"></a>이 문제에 대 한 해결 방법

다음 조건으로 인해 스냅샷 작업이 실패할 수 있습니다.

| 원인 | 솔루션 |
| --- | --- |
| VM이 RDP(원격 데스크톱 프로토콜)에서 종료되므로 VM 상태가 잘못 보고됩니다. | RDP에서 VM을 종료하는 경우 VM 상태가 올바른지 여부를 확인하려면 포털을 확인합니다. 올바르지 않은 경우 VM 대시보드의 **종료** 옵션을 사용 하 여 포털에서 vm을 종료 합니다. |
| VM이 DHCP에서 호스트 또는 패브릭 주소를 가져올 수 없습니다. | IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다. VM이 DHCP 응답 245에서 호스트 또는 패브릭 주소를 가져올 수 없는 경우에는 어떠한 확장도 다운로드하거나 실행할 수 없습니다. 정적 개인 IP가 필요한 경우 **Azure Portal** 또는 **PowerShell** 을 통해 구성 하 고 VM 내의 DHCP 옵션이 사용 하도록 설정 되어 있는지 확인 해야 합니다. PowerShell을 사용 하 여 고정 IP 주소를 설정 하는 방법에 [대해 자세히 알아보세요](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) .

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>복구 지점 리소스 그룹에서 잠금 제거

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 리소스 옵션**으로 이동 하 고, AzureBackupRG_ _ 형식으로 복원 지점 수집 리소스 그룹을 선택 합니다 `<Geo>` `<number>` .
3. **설정** 섹션에서 **잠금**을 선택하여 잠금을 표시합니다.
4. 잠금을 제거 하려면 줄임표를 선택 하 고 **삭제**를 선택 합니다.

    ![잠금 삭제](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> 복원 지점 컬렉션 정리

잠금을 제거한 후 복원 지점을 정리해야 합니다.

VM의 리소스 그룹 또는 VM 자체를 삭제 하는 경우 관리 디스크의 인스턴트 복원 스냅숏은 활성 상태로 유지 되 고 보존 집합에 따라 만료 됩니다. 복원 지점 컬렉션에 저장 된 즉시 복원 스냅숏 (더 이상 필요 하지 않은 경우)을 삭제 하려면 아래 지정 된 단계에 따라 복원 지점 컬렉션을 정리 합니다.

복원 지점을 정리하려면 다음 방법 중 하나를 따르세요.<br>

- [주문형 백업을 실행 하 여 복원 지점 수집 정리](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Azure Portal에서 복원 지점 컬렉션 정리](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>주문형 백업을 실행 하 여 복원 지점 수집 정리

잠금을 제거한 후 주문형 백업을 트리거합니다. 이 작업을 수행 하면 복원 지점이 자동으로 정리 됩니다. 이 요청 시 작업이 처음 실패 하는 것으로 간주 합니다. 그러나 복원 지점의 수동 삭제 대신 자동 정리가 보장 됩니다. 정리 후 예약 된 다음 백업이 성공 해야 합니다.

> [!NOTE]
> 자동 정리는 주문형 백업을 트리거한 몇 시간 후에 발생 합니다. 예약된 백업이 여전히 실패하는 경우 [여기](#clean-up-restore-point-collection-from-azure-portal)에 나열된 단계를 따라 복원 지점 컬렉션을 수동으로 삭제해 보세요.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Azure Portal에서 복원 지점 컬렉션 정리 <br>

리소스 그룹에 대 한 잠금으로 인해 지워지지 않는 복원 지점의 컬렉션을 수동으로 지우려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **허브** 메뉴에서 **모든 리소스**를 선택 하 고, `<Geo>` VM이 있는 AzureBackupRG_ _ 형식으로 리소스 그룹을 선택 `<number>` 합니다.

    ![리소스 그룹 선택](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 리소스 그룹을 선택 하면 **개요** 창이 표시 됩니다.
4. 숨겨진 리소스를 모두 표시하려면 **숨겨진 유형 표시** 옵션을 선택합니다. AzureBackupRG_`<VMName>`_`<number>` 형식의 복원 지점 컬렉션을 선택합니다.

    ![복원 지점 컬렉션 선택](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 복원 지점 컬렉션을 정리 하려면 **삭제** 를 선택 합니다.
6. 백업 작업을 다시 시도합니다.

> [!NOTE]
 >리소스 (RP 컬렉션)에 많은 수의 복원 지점이 있는 경우 포털에서이를 삭제 하면 시간 초과 되 고 실패할 수 있습니다. 규정 된 시간에 모든 복원 지점이 삭제 되지 않고 작업 시간이 초과 되는 알려진 CRP 문제입니다. 그러나 삭제 작업은 일반적으로 두 번 이상 다시 시도한 후에 성공 합니다.
