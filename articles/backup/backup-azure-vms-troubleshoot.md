---
title: Azure Vm을 사용 하 여 백업 오류 문제 해결
description: 이 문서에서는 Azure 가상 컴퓨터의 백업 및 복원에서 발생 하는 오류를 해결 하는 방법에 대해 알아봅니다.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 9dbb76b3c0bb6c0ff1f4fb51fbf4846b74a3a1f3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019098"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Azure 가상 머신에서 백업 오류 문제 해결

아래에 나열 된 정보를 사용 하 여 Azure Backup를 사용 하는 동안 발생 한 오류를 해결할 수 있습니다.

## <a name="backup"></a>Backup

이 섹션에서는 Azure 가상 컴퓨터의 백업 작업 실패에 대해 설명 합니다.

### <a name="basic-troubleshooting"></a>기본 문제 해결

* VM 에이전트 (WA 에이전트)가 [최신 버전](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)인지 확인 합니다.
* Windows 또는 Linux VM OS 버전이 지원 되는지 확인 하세요. [IAAS Vm Backup 지원 매트릭스](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)를 참조 하세요.
* 다른 백업 서비스가 실행 되 고 있지 않은지 확인 합니다.
  * 스냅숏 확장 문제가 없는지 확인 하려면 확장을 제거 하 여 [강제로 다시 로드 한 후 백업을 다시 시도](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-backup-extension-fails-to-update-or-load)하세요.
* VM이 인터넷에 연결 되어 있는지 확인 합니다.
  * 다른 백업 서비스가 실행 되 고 있지 않은지 확인 하십시오.
* `Services.msc`에서 **Windows Azure 게스트 에이전트** 서비스가 **실행**중인지 확인 합니다. **Microsoft Azure 게스트 에이전트** 서비스가 없는 경우 [Recovery Services 자격 증명 모음에 Azure vm 백업](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#install-the-vm-agent)에서 설치 합니다.
* **이벤트 로그** 에는 다른 백업 제품의 백업 오류 (예: Windows Server 백업)가 표시 될 수 있으며, Azure 백업으로 인 한 것이 아닙니다. 다음 단계를 사용 하 여 문제가 Azure Backup 있는지 여부를 확인 합니다.
  * 이벤트 원본 또는 메시지에 항목을 **백업** 하는 동안 오류가 발생 하는 경우 AZURE IaaS VM 백업 백업에 성공 했는지 여부 및 원하는 스냅숏 유형으로 복원 지점을 만들었는지 여부를 확인 합니다.
  * Azure Backup 작동 하는 경우 다른 백업 솔루션에 문제가 있을 수 있습니다.
  * 다음은 Azure 백업이 정상적으로 작동 하지만 "Windows Server 백업"이 실패 한 이벤트 뷰어 오류 517의 예입니다.<br>
    ![Windows Server 백업 실패](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Azure Backup 실패 하면이 문서의 일반적인 VM 백업 오류 섹션에서 해당 오류 코드를 찾습니다.

## <a name="common-issues"></a>일반 문제

다음은 Azure virtual machines에서의 백업 실패와 관련 된 일반적인 문제입니다.

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-자격 증명 모음에서 백업 데이터를 복사 하는 시간이 초과 되었습니다.

오류 코드: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
오류 메시지: 자격 증명 모음에서 백업 데이터를 복사 하는 시간이 초과 되었습니다.

이는 백업 서비스에서 시간 제한 기간 내에 데이터를 자격 증명 모음으로 전송 하기 위한 일시적인 저장소 오류 또는 저장소 계정 IOPS 부족으로 인해 발생할 수 있습니다. 이러한 [모범 사례](backup-azure-vms-introduction.md#best-practices) 를 사용 하 여 VM 백업을 구성 하 고 백업 작업을 다시 시도 하세요.

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState-VM이 백업을 허용 하는 상태가 아닙니다.

오류 코드: UserErrorVmNotInDesirableState <br/>
오류 메시지: VM이 백업을 허용 하는 상태가 아닙니다.<br/>

VM이 실패 상태 여 서 백업 작업이 실패 했습니다. 백업이 성공하려면 VM 상태가 실행 중, 중지됨 또는 중지됨(할당 취소)이어야 합니다.

* VM이 **실행 중**에서 **종료** 상태로 전환되고 있으면 상태가 변경될 때까지 기다립니다. 그런 다음, 백업 작업을 트리거합니다.
* VM이 Linux 에이전트이고 Security-Enhanced Linux 커널 모듈을 사용하는 경우 보안 정책에서 Azure Linux 에이전트 경로 **/var/lib/waagent**를 제외하여 백업 확장이 설치되도록 합니다.

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed-파일 시스템 일치 스냅숏을 만들기 위해 VM의 탑재 위치를 하나 이상 고정 하지 못했습니다.

오류 코드: UserErrorFsFreezeFailed <br/>
오류 메시지: 파일 시스템 일치 스냅숏을 만들기 위해 VM의 탑재 위치를 하나 이상 고정 하지 못했습니다.

* **분리할** 명령을 사용 하 여 파일 시스템 상태가 정리 되지 않은 장치를 분리 합니다.
* **Fsck** 명령을 사용 하 여 이러한 장치에 대한 파일 시스템 일관성 검사를 실행 합니다.
* 장치를 다시 탑재 하 고 백업 작업을 다시 시도 하세요.</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/Extension설치 Failedcom/Extension설치용 Failedmdtc-확장 설치/c + + 오류로 인해 작업이 실패 했습니다.

오류 코드: ExtensionSnapshotFailedCOM <br/>
오류 메시지: COM + 오류로 인해 스냅숏 작업이 실패 했습니다.

오류 코드: Extension설치 Failedcom  <br/>
오류 메시지: 확장 설치/COM + 오류로 인해 작업이 실패 했습니다.

오류 코드: Extension설치 Failedmdtc <br/>
오류 메시지: "COM +가 Microsoft DTC(Distributed Transaction Coordinator)와 통신할 수 없습니다." 오류로 인해 확장 설치가 실패 했습니다. <br/>

Windows 서비스 **Com + 시스템** 응용 프로그램 문제로 인해 백업 작업이 실패 했습니다.  이 문제를 해결하려면 다음 단계를 따릅니다.

* Windows 서비스 **Com + 시스템 응용 프로그램** 을 시작/다시 시작 해 보세요 (관리자 권한 명령 프롬프트에서 **Net Start comsysapp**).
* **DTC(Distributed Transaction Coordinator)** 서비스가 **네트워크 서비스** 계정으로 실행 되 고 있는지 확인 합니다. 그렇지 않으면 **네트워크 서비스** 계정으로 실행으로 변경 하 고 **Com + 시스템 응용 프로그램**을 다시 시작 합니다.
* 에서 서비스를 다시 시작할 수 없는 경우 다음 단계를 수행 하 여 **DTC(Distributed Transaction Coordinator)** 서비스를 다시 설치 합니다.
  * MSDTC 서비스를 중지합니다.
  * 명령 프롬프트(cmd)를 엽니다.
  * "Msdtc-uninstall" 명령을 실행 합니다.
  * "Msdtc-install" 명령을 실행 합니다.
  * MSDTC 서비스를 시작합니다.
* **COM+ 시스템 애플리케이션** Windows 서비스를 시작합니다. **COM+ 시스템 애플리케이션**이 시작되면 Azure Portal에서 백업 작업을 트리거합니다.</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>VSS 기록기가 잘못 된 상태 여 서 ExtensionFailedVssWriterInBadState-스냅숏 작업에 실패 했습니다.

오류 코드: ExtensionFailedVssWriterInBadState <br/>
오류 메시지: VSS 기록기가 잘못 된 상태 여 서 스냅숏 작업이 실패 했습니다.

잘못된 상태의 VSS 기록기를 다시 시작합니다. 관리자 권한의 명령 프롬프트에서 ```vssadmin list writers```를 실행합니다. 출력에는 모든 VSS 기록기와 해당 상태가 포함됩니다. **[1] 안정** 상태가 아닌 모든 VSS 기록기는 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 다시 시작합니다.

* ```net stop serviceName```
* ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-백업 확장에 대한 구성의 구문 분석에 실패 했습니다.

오류 코드: ExtensionConfigParsingFailure<br/>
오류 메시지: 백업 확장에 대한 구성의 구문 분석에 실패 했습니다.

이 오류는 **MachineKeys** 디렉터리: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**에 대한 권한 변경으로 인해 발생합니다.
다음 명령을 실행 하 고 **Machinekeys** 디렉터리에 대한 사용 권한이 기본값 인지 확인 합니다.**icacls%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.

기본 권한은 다음과 같습니다.

* 모든 사람: (R, W)
* BUILTIN\Administrators: (F)

**MachineKeys** 디렉터리에 대해 기본값과 다른 권한이 표시되면 아래 단계에 따라 권한을 수정하고, 인증서를 삭제한 후 백업을 트리거합니다.

1. **MachineKeys** 디렉터리에 대한 권한을 수정합니다. 디렉터리에서 탐색기 보안 속성 및 고급 보안 설정을 사용하여 권한을 기본값으로 다시 설정합니다. 디렉터리에서 기본값을 제외한 모든 사용자 개체를 제거하고 **Everyone** 사용 권한이 다음과 같은 특수한 액세스 권한을 갖는지 확인합니다.

   * 폴더 나열/데이터 읽기
   * 특성 읽기
   * 확장된 특성 읽기
   * 파일 만들기/데이터 쓰기
   * 폴더 만들기/데이터 추가
   * 특성 쓰기
   * 확장된 특성 쓰기
   * 읽기 권한
2. **발급 대상**이 클래식 배포 모델 또는 **Windows Azure CRP Certificate Generator**인 모든 인증서를 삭제합니다.

   * [로컬 머신 콘솔에서 인증서를 엽니다](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx).
   * **개인** > **인증서**에서 **발급 대상**이 클래식 배포 모델 또는 **Windows Azure CRP Certificate Generator**인 모든 인증서를 삭제합니다.
3. VM 백업 작업을 트리거합니다.

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-확장 상태는 백업 작업에 협력적인 되지 않습니다.

오류 코드: ExtensionStuckInDeletionState <br/>
오류 메시지: 확장 상태는 백업 작업에 협력적인 되지 않습니다.

백업 확장의 상태가 일관 되지 않아 백업 작업에 실패 했습니다. 이 문제를 해결하려면 다음 단계를 따릅니다.

* 게스트 에이전트가 설치되어 있고 응답하는지 확인합니다.
* Azure Portal에서 **가상 머신** > **모든 설정** > **확장** 으로 이동 합니다.
* 백업 확장 VmSnapshot 또는 VmSnapshotLinux를 선택하고 **제거**를 클릭합니다.
* 백업 확장을 삭제 한 후 백업 작업을 다시 시도 하세요.
* 후속 백업 작업은 새 확장을 원하는 상태로 설치할 것입니다.

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-연결 된 일부 디스크에 대한 스냅숏 제한을 초과 하 여 스냅숏 작업이 실패 했습니다.

오류 코드: ExtensionFailedSnapshotLimitReachedError  <br/>
오류 메시지: 연결 된 일부 디스크에 대한 스냅숏 제한을 초과 하 여 스냅숏 작업에 실패 했습니다.

연결 된 일부 디스크에 대한 스냅숏 제한이 초과 되어 스냅숏 작업이 실패 했습니다. 아래 문제 해결 단계를 완료 한 후 작업을 다시 시도 하세요.

* 필요 하지 않은 디스크 blob-스냅숏을 삭제 합니다. 디스크 blob을 삭제 하지 않도록 주의 해야 합니다. 스냅숏 blob만 삭제 해야 합니다.
* 일시 삭제를 VM 디스크 저장소 계정에서 사용 하도록 설정한 경우에는 기존 스냅숏이 언제 든 지 허용 되는 최대 크기 보다 작은 것으로 소프트 삭제 보존을 구성 합니다.
* 백업 된 VM에서 Azure Site Recovery 사용 하도록 설정 된 경우 다음 단계를 수행 합니다.

  * /Etc/azure/vmbackup.conf에서 **isanysnapshotfailed** 의 값이 false로 설정 되어 있는지 확인 합니다.
  * 다른 시간에 Azure Site Recovery 예약 하 여 백업 작업과 충돌 하지 않도록 합니다.

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive-VM 리소스가 부족 하 여 스냅숏 작업이 실패 했습니다.

오류 코드: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
오류 메시지: VM 리소스가 부족 하 여 스냅숏 작업에 실패 했습니다.

스냅숏 작업을 수행 하는 동안 네트워크 호출의 지연이 발생 하 여 VM에 대한 백업 작업이 실패 했습니다. 이 문제를 해결하려면 1단계를 수행합니다. 문제가 지속되면 2 및 3단계를 시도합니다.

**1 단계**: 호스트를 통해 스냅숏 만들기

상위(관리자) 명령 프롬프트에서 아래 명령을 실행합니다.

```text
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

이렇게 하면 Guest가 아닌 호스트를 통해 스냅샷이 만들어집니다. 백업 작업을 다시 시도합니다.

**2 단계**: VM이 부하가 적을 때의 시간으로 백업 일정을 변경 합니다 (CPU/IOps 등 더 낮음).

**3 단계**: [VM의 크기를 늘리고](https://azure.microsoft.com/blog/resize-virtual-machines/) 작업을 다시 시도 합니다.

## <a name="common-vm-backup-errors"></a>일반적인 VM 백업 오류

| 오류 세부 정보 | 해결 방법 |
| ------ | --- |
| **오류 코드**: 320001, ResourceNotFound <br/> **오류 메시지**: VM이 더 이상 존재 하지 않으므로 작업을 수행할 수 없습니다. <br/> <br/> **오류 코드**: 400094, BCMV2VMNotFound <br/> **오류 메시지**: 가상 머신이 존재 하지 않습니다. <br/> <br/>  Azure 가상 머신을 찾을 수 없습니다.  |이 오류는 주 VM이 삭제되었지만 백업 정책이 백업을 수행하기 위해 여전히 VM을 검색할 때 발생합니다. 이 오류를 해결하려면 다음 단계를 수행합니다. <ol><li> 동일한 이름 및 동일한 리소스 그룹 이름, **클라우드 서비스 이름**으로 가상 머신을 다시 만듭니다.<br>**or**</li><li> 백업 데이터를 삭제하거나 삭제하지 않고 가상 머신의 보호를 중지합니다. 자세한 내용은 [가상 머신 보호 중지](backup-azure-manage-vms.md#stop-protecting-a-vm)를 참조하세요.</li></ol>|
|**오류 코드**: UserErrorBCMPremiumStorageQuotaError<br/> **오류 메시지**: 저장소 계정에 사용 가능한 공간이 부족 하 여 가상 머신의 스냅숏을 복사할 수 없습니다. | VM 백업 스택 V1에 있는 프리미엄 VM의 경우 스토리지 계정에 스냅샷을 복사합니다. 이 단계는 스냅샷에서 작동하는 백업 관리 트래픽이 프리미엄 디스크를 사용하는 애플리케이션에서 사용 가능한 IOPS 수를 제한하지 않도록 하기 위한 것입니다. <br><br>총 스토리지 계정 공간의 50%, 17.5TB만을 할당하는 것이 좋습니다. 그런 다음, Azure Backup 서비스에서 스토리지 계정에 스냅샷을 복사하고 스토리지 계정의 복사된 위치에서 자격 증명 모음으로 데이터를 전송할 수 있습니다. |
| **오류 코드**: 380008, AzureVmOffline <br/> **오류 메시지**: 가상 머신이 실행 되 고 있지 않으므로 Microsoft Recovery Services 확장을 설치 하지 못했습니다. | VM 에이전트는 Azure Recovery Services 확장에 대한 필수 구성 요소입니다. Azure Virtual Machine 에이전트를 설치하고 등록 작업을 다시 시작합니다. <br> <ol> <li>VM 에이전트가 제대로 설치되었는지 확인합니다. <li>VM 구성의 플래그가 올바르게 설정되었는지 확인합니다.</ol> VM 에이전트 설치 및 VM 에이전트 설치의 유효성을 검사하는 방법에 대해 자세히 알아보세요. |
| **오류 코드**: ExtensionSnapshotBitlockerError <br/> **오류 메시지**: 볼륨 섀도 복사본 서비스 (VSS) 작업 오류로 인해 스냅숏 작업이 실패 했습니다 **.이 드라이브는 BitLocker 드라이브 암호화에 의해 잠겼습니다. 제어판에서이 드라이브의 잠금을 해제 해야 합니다.** |VM에 있는 모든 드라이브의 BitLocker를 끄고 VSS 문제가 해결되었는지 확인합니다. |
| **오류 코드**: VmNotInDesirableState <br/> **오류 메시지**: VM이 백업을 허용 하는 상태가 아닙니다. |<ul><li>VM이 **실행 중**에서 **종료** 상태로 전환되고 있으면 상태가 변경될 때까지 기다립니다. 그런 다음, 백업 작업을 트리거합니다. <li> VM이 Linux 에이전트이고 Security-Enhanced Linux 커널 모듈을 사용하는 경우 보안 정책에서 Azure Linux 에이전트 경로 **/var/lib/waagent**를 제외하여 백업 확장이 설치되도록 합니다.  |
| VM 에이전트가 가상 머신에 없습니다. <br>모든 필수 구성 요소 및 VM 에이전트를 설치합니다. 그런 다음, 작업을 다시 시작합니다. |[VM 에이전트 설치 및 VM 에이전트 설치의 유효성을 검사하는 방법](#vm-agent)에 대해 자세히 알아보세요. |
| **오류 코드**: ExtensionSnapshotFailedNoSecureNetwork <br/> **오류 메시지**: 보안 네트워크 통신 채널을 만드는 동안 오류가 발생 하 여 스냅숏 작업이 실패 했습니다. | <ol><li> 관리자 권한 모드에서 **regedit.exe**를 실행하여 레지스트리 편집기를 엽니다. <li> 시스템에 있는 모든 버전의 .NET Framework를 파악합니다. 이러한 버전은 레지스트리 키 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**의 계층 구조 아래에 있습니다. <li> 레지스트리 키에 있는 각 .NET Framework에 대해 다음 키를 추가합니다. <br> **SchUseStrongCrypto"=dword:00000001** </ol>|
| **오류 코드**: ExtensionVCRedistInstallationFailure <br/> **오류 메시지**: visual Studio 2012에 대한 시각적 C++ 재배포 가능 패키지 설치에 실패 하 여 스냅숏 작업에 실패 했습니다. | C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion으로 이동 하 여 vcredist2013_x64를 설치 합니다.<br/>서비스 설치를 허용 하는 레지스트리 키 값이 올바른 값으로 설정 되어 있는지 확인 합니다. 즉, **HKEY_LOCAL_MACHINE \system\currentcontrolset\services\msiserver** 의 **시작** 값을 **4**가 아닌 **3** 으로 설정 합니다. <br><br>설치하는 데 여전히 문제가 발생할 경우 관리자 권한 명령 프롬프트에서 **MSIEXEC /UNREGISTER**를 실행한 후 **MSIEXEC /REGISTER**를 실행하여 설치 서비스를 다시 시작합니다.  |

## <a name="jobs"></a>작업

| 오류 세부 정보 | 해결 방법 |
| --- | --- |
| 취소는 이 작업 유형에 지원되지 않습니다. <br>작업이 완료될 때까지 기다립니다. |없음 |
| 작업이 취소 가능한 상태에 있지 않습니다. <br>작업이 완료될 때까지 기다립니다. <br>**or**<br> 선택한 작업이 취소 가능한 상태에 있지 않습니다. <br>작업이 완료될 때까지 기다립니다. |작업이 거의 완료되는 것입니다. 작업이 완료될 때까지 기다립니다.|
| 진행 중이 아니므로 백업에서 작업을 취소할 수 없습니다. <br>취소는 진행 중인 작업에서만 지원됩니다. 진행 중인 작업을 취소하려고 합니다. |이 오류는 일시적인 상태로 인해 발생합니다. 잠시 기다렸다가 취소 작업을 다시 시도합니다. |
| 백업에서 작업을 취소하지 못했습니다. <br>작업이 완료될 때까지 기다립니다. |없음 |

## <a name="restore"></a>복원

| 오류 세부 정보 | 해결 방법 |
| --- | --- |
| 클라우드 내부 오류로 인해 복원이 실패했습니다. |<ol><li>복원하려는 클라우드 서비스가 DNS 설정을 사용하여 구성되었습니다. 다음을 확인할 수 있습니다. <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>**주소**가 구성된 경우 DNS 설정이 구성되었습니다.<br> <li>복원하려는 클라우드 서비스가 **ReservedIP**를 사용하여 구성되고, 클라우드 서비스의 기존 VM이 중단된 상태에 있습니다. 다음 PowerShell cmdlet: **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**을 사용하여 클라우드 서비스가 IP를 예약했는지 확인합니다. <br><li>동일한 클라우드 서비스에 다음과 같이 특수한 네트워크 구성을 사용하여 가상 머신을 복원하려고 시도하고 있습니다. <ul><li>부하 분산 장치 구성의 가상 머신, 내부 및 외부<li>여러 개의 예약된 IP를 사용하는 가상 머신 <li>여러 NIC가 있는 가상 머신 </ul><li>특수한 네트워크 구성을 가진 VM의 경우 [복원 고려 사항](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)을 참조하거나 UI에서 새 클라우드 서비스를 선택하세요</ol> |
| 선택된 DNS 이름이 이미 사용 중입니다. <br>다른 DNS 이름을 지정하고 다시 시도합니다. |이 DNS 이름은 클라우드 서비스 이름을 가리킵니다. 일반적으로 **.cloudapp.net**으로 끝납니다. 이 이름은 고유해야 합니다. 이 오류가 발생하는 경우 복원하는 동안 다른 VM 이름을 선택해야 합니다. <br><br> 이 오류는 Azure 포털의 사용자에게만 표시됩니다. PowerShell 통한 복원 작업은 디스크만 복원하고 VM을 만들지 않기 때문에 성공합니다. 디스크 복원 작업 후 사용자가 명시적으로 VM를 만들 경우 오류가 발생합니다. |
| 지정된 가상 네트워크 구성이 올바르지 않습니다. <br>다른 가상 네트워크 구성을 지정하고 다시 시도합니다. |없음 |
| 지정된 클라우드 서비스에서 복원 중인 가상 머신의 구성과 일치하지 않는 예약된 IP를 사용하고 있습니다. <br>예약된 IP를 사용하지 않는 다른 클라우드 서비스를 지정합니다. 또는 복원할 다른 복구 지점을 선택합니다. |없음 |
| 클라우드 서비스가 입력 엔드포인트 수의 한계에 도달했습니다. <br>다른 클라우드 서비스를 지정하거나 기존 엔드포인트를 사용하여 작업을 다시 시도합니다. |없음 |
| Recovery Services 자격 증명 모음 및 대상 스토리지 계정이 서로 다른 두 지역에 있습니다. <br>복원 작업에서 지정된 스토리지 계정이 Recovery Services 자격 증명 모음과 동일한 Azure 지역에 있는지 확인합니다. |없음 |
| 복원 작업에 지정된 스토리지 계정이 지원되지 않습니다. <br>로컬 중복 또는 지역 중복 복제 설정이 있는 기본 또는 표준 스토리지 계정만 지원됩니다. 지원되는 스토리지 계정을 선택합니다. |없음 |
| 복원 작업에 지정된 스토리지 계정의 유형이 온라인 상태에 있지 않습니다. <br>복원 작업에 지정된 스토리지 계정이 온라인 상태에 있는지 확인합니다. |이 오류는 Azure Storage의 일시적인 오류 또는 가동 중지로 인해 발생할 수 있습니다. 다른 스토리지 계정을 선택하세요. |
| 리소스 그룹 할당량에 도달했습니다. <br>Azure Portal의 일부 리소스 그룹을 삭제하거나 Azure 지원에 문의하여 제한을 늘립니다. |없음 |
| 선택한 서브넷이 존재하지 않습니다. <br>존재하는 서브넷을 선택합니다. |없음 |
| Backup 서비스에 구독의 리소스에 액세스할 수 있는 권한이 없습니다. |이 오류를 해결하려면 먼저 [백업된 디스크 복원](backup-azure-arm-restore-vms.md#restore-disks)의 단계를 사용하여 디스크를 복원합니다. 그런 다음, [복원된 디스크에서 VM 만들기](backup-azure-vms-automation.md#restore-an-azure-vm)의 PowerShell 단계를 사용합니다. |

## <a name="backup-or-restore-takes-time"></a>백업 또는 복원에 시간이 걸림

백업에 12 시간 이상 소요 되는 경우 또는 복원에 6 시간 이상 소요 되 면 [모범 사례](backup-azure-vms-introduction.md#best-practices)및 [성능 고려 사항을](backup-azure-vms-introduction.md#backup-performance) 검토 하세요.

## <a name="vm-agent"></a>VM 에이전트

### <a name="set-up-the-vm-agent"></a>VM 에이전트 설정

일반적으로, Azure 갤러리에서 만든 VM에는 VM 에이전트가 이미 있습니다. 그러나 온-프레미스 데이터 센터에서 마이그레이션되는 가상 머신에는 VM 에이전트가 설치되어 있지 않습니다. 이러한 VM의 경우 VM 에이전트를 명시적으로 설치해야 합니다.

#### <a name="windows-vms"></a>Windows VM

* [에이전트 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 필요합니다.
* 클래식 배포 모델을 사용하여 생성된 가상 머신의 경우 [VM 속성을 업데이트](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 에이전트가 설치되었다고 표시합니다. Azure Resource Manager 가상 머신의 경우 이 단계가 필요하지 않습니다.

#### <a name="linux-vms"></a>Linux VM

* 배포 리포지토리에서 최신 버전의 에이전트를 설치합니다. 패키지 이름에 대한 자세한 내용은 [Linux 에이전트 리포지토리](https://github.com/Azure/WALinuxAgent)를 참조하세요.
* 클래식 배포 모델을 사용하여 생성된 VM의 경우 [이 블로그를 사용](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)하여 VM 속성을 업데이트하고 에이전트가 설치되어 있는지 확인합니다. Resource Manager 가상 머신의 경우 이 단계가 필요하지 않습니다.

### <a name="update-the-vm-agent"></a>VM 에이전트 업데이트

#### <a name="windows-vms"></a>Windows VM

* VM 에이전트를 업데이트하려면 [VM 에이전트 이진 파일](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치합니다. 에이전트를 업데이트하기 전에 VM 에이전트 업데이트 동안 백업 작업이 발생하지 않는지 확인합니다.

#### <a name="linux-vms"></a>Linux VM

* Linux VM 에이전트를 업데이트하려면 [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서의 지침을 따릅니다.

    > [!NOTE]
    > 항상 배포 리포지토리를 사용하여 에이전트를 업데이트합니다.

    GitHub에서 에이전트 코드를 다운로드하지 마세요. 최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 최신 에이전트를 획득하기 위한 지침을 얻으세요. GitHub 리포지토리에서 최신 [Microsoft Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent/releases) 정보를 확인할 수 있습니다.

### <a name="validate-vm-agent-installation"></a>VM 에이전트 설치의 유효성 검사

Windows VM에서 VM 에이전트 버전을 확인합니다.

1. Azure Virtual Machine에 로그인하고 **C:\WindowsAzure\Packages** 폴더로 이동합니다. **WaAppAgent.exe** 파일을 찾아야 합니다.
2. 해당 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동합니다. 그런 다음 **세부 정보** 탭을 선택 합니다. **제품 버전** 필드는 2.6.1198.718 이상 이상 이어야 합니다.

## <a name="troubleshoot-vm-snapshot-issues"></a>VM 스냅샷 문제 해결

VM 백업은 기본 스토리지에 대한 스냅샷 명령 실행을 사용합니다. 스토리지에 액세스할 수 없거나 스냅샷 작업 실행이 지연되는 경우 백업 작업이 실패할 수 있습니다. 다음 조건으로 인해 스냅샷 작업 오류가 발생할 수 있습니다.

* **SQL Server 백업이 구성된 VM이 스냅샷 작업을 지연시킬 수 있습니다**. 기본적으로 VM 백업은 Windows VM에서 VSS 전체 백업을 만듭니다. SQL Server 백업이 구성된 SQL Server를 실행하는 VM에서는 스냅샷 지연이 발생할 수 있습니다. 스냅샷 지연으로 인해 백업이 실패하는 경우 다음 레지스트리 키를 설정합니다.

   ```text
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **VM이 RDP에서 종료되므로 VM 상태가 잘못 보고됩니다**. 원격 데스크톱을 사용하여 가상 머신을 종료한 경우 포털의 VM 상태가 올바른지 확인합니다. 상태가 올바르지 않은 경우 포털 VM 대시보드에서 **종료** 옵션을 사용하여 VM을 종료합니다.
* **5개 이상의 VM이 동일한 클라우드 서비스를 공유하는 경우 여러 백업 정책에 걸쳐 VM을 분산합니다**. 5개 이상의 VM 백업이 동시에 시작되지 않도록 백업 시간을 엇갈려 지정합니다. 정책의 시작 시간을 1시간 이상 간격으로 지정합니다.
* **VM이 높은 CPU 또는 메모리에서 실행됩니다**. 가상 머신이 높은 메모리 또는 CPU 사용량(90% 초과)에서 실행 중인 경우 스냅샷 작업이 큐 대기되고 지연됩니다. 결국 시간 초과 됩니다. 이 문제가 발생 하면 주문형 백업을 시도 하세요.

## <a name="networking"></a>네트워킹

IaaS VM 백업이 작동하려면 게스트 내에 DHCP를 사용하도록 설정되어야 합니다. 고정 프라이빗 IP가 필요한 경우 Azure Portal 또는 PowerShell을 통해 구성합니다. VM 내 DHCP 옵션이 활성화되었는지 확인합니다.
PowerShell을 통해 고정 IP를 설정하는 방법에 대한 자세한 정보를 가져옵니다.

* [기존 VM에 고정 내부 IP를 추가하는 방법](/previous-versions/azure/virtual-network/virtual-networks-reserved-private-ip#how-to-add-a-static-internal-ip-to-an-existing-vm)
* [네트워크 인터페이스에 할당된 개인 IP 주소의 할당 방법 변경](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)

