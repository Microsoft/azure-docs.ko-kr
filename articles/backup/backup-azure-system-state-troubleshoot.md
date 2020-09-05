---
title: 시스템 상태 백업 문제 해결
description: 이 문서에서는 온-프레미스 Windows server에 대 한 시스템 상태 백업 문제를 해결 하는 방법에 대해 알아봅니다.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7eb596853bfe17ec5bf14c8830c1b95bde5b7c98
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022363"
---
# <a name="troubleshoot-system-state-backup"></a>시스템 상태 백업 문제 해결

이 문서에서는 시스템 상태 백업을 사용 하는 동안 발생할 수 있는 문제에 대 한 해결 방법을 설명 합니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

시스템 상태 백업 문제 해결을 시작 하기 전에 다음 유효성 검사 단계를 수행 하는 것이 좋습니다.

- [MARS(Microsoft Azure Recovery Services) 에이전트가 최신 상태인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지 확인](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Microsoft Azure Recovery Services가 실행 중인지 확인(서비스 콘솔에서) 필요한 경우 다시 시작하고 작업을 다시 시도
- [스크래치 폴더 위치에서 5~10% 볼륨 여유 공간을 사용할 수 있는지 확인](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해하는지 확인](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [예약 백업은 실패하지만 수동 백업은 작동](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- OS에 최신 업데이트가 설치되었는지 확인
- [지원 되지 않는 특성이 있는 지원 되지 않는 드라이브 및 파일이 백업에서 제외 되는지 확인](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 보호되는 시스템의 **시스템 클록**이 올바른 표준 시간대로 구성되었는지 확인 <br>
- [서버에 적어도 .Net Framework 버전 4.5.2 이상이 설치되었는지 확인](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 자격 증명 모음에 **서버를 등록** 하려는 경우 다음을 수행 합니다. <br>
  - 에이전트가 서버에서 제거 되 고 포털에서 삭제 되었는지 확인 합니다. <br>
  - 처음 서버 등록에 사용한 것과 동일한 암호 사용 <br>
- 오프 라인 백업 인 경우 오프 라인 백업 작업을 시작 하기 전에 Azure PowerShell 버전 3.7.0가 원본 컴퓨터와 복사 컴퓨터 모두에 설치 되어 있는지 확인 합니다.
- [Azure 가상 머신에서 백업 에이전트가 실행 되는 경우 고려 사항](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>제한 사항

- Microsoft에서 시스템 상태 복구를 사용 하 여 다른 하드웨어로 복구 하지 않는 것이 좋습니다.
- 시스템 상태 백업은 현재 "온-프레미스" Windows 서버를 지원 합니다. Azure Vm에는이 기능을 사용할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Backup를 사용 하 여 시스템 상태 백업 문제를 해결 하기 전에 다음 필수 구성 요소 검사를 수행 합니다.  

### <a name="verify-windows-server-backup-is-installed"></a>Windows Server 백업 설치 되어 있는지 확인

서버에 Windows Server 백업 설치 되어 있고 사용 하도록 설정 되어 있는지 확인 합니다. 설치 상태를 확인 하려면 다음 PowerShell 명령을 실행 합니다.

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

출력에 **설치 상태가** **사용 가능**으로 표시 되는 경우 Windows server backup 기능을 설치에 사용할 수 있지만 서버에 설치 하지는 않습니다. 그러나 Windows Server 백업 설치 되지 않은 경우 아래 방법 중 하나를 사용 하 여 설치 합니다.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>방법 1: PowerShell을 사용 하 여 Windows Server 백업 설치

PowerShell을 사용 하 여 Windows Server 백업을 설치 하려면 다음 명령을 실행 합니다.

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>방법 2: 서버 관리자를 사용 하 여 Windows Server 백업 설치

서버 관리자를 사용 하 여 Windows Server 백업를 설치 하려면 다음 단계를 수행 합니다.

1. **서버 관리자**에서 **역할 및 기능 추가**를 클릭 합니다. **역할 및 기능 추가 마법사** 가 나타납니다.

    ![대시보드](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. **설치 유형** 을 선택 하 고 **다음**을 클릭 합니다.

    ![설치 유형](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 서버 풀에서 서버를 선택 하 고 **다음**을 클릭 합니다. 서버 역할에서 기본 선택 항목을 그대로 두고 **다음**을 클릭 합니다.
4. **기능** 탭에서 **Windows Server 백업** 를 선택 하 고 **다음**을 클릭 합니다.

    ![기능 창 선택](./media/backup-azure-system-state-troubleshoot/features.png)

5. **확인** 탭에서 **설치** 를 클릭 하 여 설치 프로세스를 시작 합니다.
6. **결과** 탭에는 Windows Server에 Windows Server 백업 기능이 성공적으로 설치 되어 표시 됩니다.

    ![설치 결과](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>시스템 볼륨 정보 사용 권한

로컬 시스템에 Windows가 설치 된 볼륨에 있는 **시스템 볼륨 정보** 폴더에 대 한 모든 권한이 있는지 확인 합니다. 일반적으로이는 **C:\system 볼륨 정보**입니다. 위의 권한이 올바르게 설정 되지 않은 경우 Windows Server 백업은 실패할 수 있습니다.

### <a name="dependent-services"></a>종속 서비스

아래 서비스가 실행 중 상태 인지 확인 합니다.

**서비스 이름** | **시작 유형**
--- | ---
RPC (원격 프로시저 호출) | 자동
COM + 이벤트 시스템 (EventSystem) | 자동
시스템 이벤트 알림 서비스 (SENS) | 자동
VSS (볼륨 섀도 복사본) | 설명서
Microsoft 소프트웨어 섀도 복사본 공급자 (SWPRV) | 설명서

### <a name="validate-windows-server-backup-status"></a>Windows Server 백업 상태 유효성 검사

Windows Server 백업 상태를 확인 하려면 다음 단계를 수행 합니다.

- WSB PowerShell이 실행 중인지 확인

  - `Get-WBJob`관리자 권한 PowerShell에서를 실행 하 고 다음 오류를 반환 하지 않는지 확인 합니다.

    > [!WARNING]
    > WBJob: ' WBJob ' 용어는 cmdlet, 함수, 스크립트 파일 또는 작동 가능한 프로그램의 이름으로 인식 되지 않습니다. 이름의 철자를 확인하거나 경로가 포함되어 있으면 경로가 올바른지 확인하고 다시 시도합니다.

    - 이 오류가 발생 하 여 실패 하는 경우 필수 구성 요소의 1 단계에 설명 된 대로 서버 컴퓨터에 Windows Server 백업 기능을 다시 설치 합니다.

  - 관리자 권한 명령 프롬프트에서 다음 명령을 실행 하 여 WSB 백업이 제대로 작동 하는지 확인 합니다.

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >X를 시스템 상태 백업 이미지를 저장 하려는 볼륨의 드라이브 문자로 바꿉니다.

    - `Get-WBJob`관리자 권한 PowerShell에서 명령을 실행 하 여 작업의 상태를 주기적으로 확인 합니다.
    - 백업 작업이 완료 된 후 명령을 실행 하 여 작업의 최종 상태를 확인 합니다. `Get-WBJob -Previous 1`

작업이 실패 하는 경우에는 MARS 에이전트 시스템 상태 백업 실패가 발생 하는 WSB 문제를 나타냅니다.

## <a name="common-errors"></a>일반 오류

### <a name="vss-writer-timeout-error"></a>VSS 기록기 시간 초과 오류

| 증상 | 원인 | 해결 방법
| -- | -- | --
| -"WSB 작업이 VSS 오류로 인해 실패 했습니다." 라는 오류 메시지와 함께 MARS 에이전트가 실패 합니다. VSS 이벤트 로그를 확인 하 여 오류 해결<br/><br/> -다음 오류 로그는 VSS 응용 프로그램 이벤트 로그에 표시 됩니다. "VSS 기록기가 오류 0x800423f2 인 이벤트를 거부 했습니다. 중지 및 재개 이벤트 사이에 기록기의 제한 시간이 만료 되었습니다."| 컴퓨터의 CPU 및 메모리 리소스가 부족 하 여 VSS 기록기를 시간 내에 완료할 수 없습니다. <br/><br/> 다른 백업 소프트웨어에서 이미 VSS 기록기를 사용 하 고 있으므로이 백업에 대 한 결과 스냅숏 작업을 완료할 수 없습니다. | 시스템에서 CPU/메모리가 확보 될 때까지 기다리거나 메모리/CPU를 너무 많이 차지 하는 프로세스를 중단 하 고 작업을 다시 시도 하십시오. <br/><br/>  진행 중인 백업이 완료 될 때까지 기다린 후 컴퓨터에서 실행 중인 백업이 없을 때 나중에 작업을 시도 합니다.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>디스크 공간이 부족 하 여 섀도 복사본을 확장할 수 없습니다.

| 증상 | 해결 방법
| -- | --
| -MARS 에이전트가 실패 했습니다. 오류 메시지: 시스템 파일을 포함 하는 볼륨의 디스크 공간이 부족 하 여 섀도 복사본 볼륨을 늘릴 수 없어서 백업에 실패 했습니다. <br/><br/> -다음 오류/경고 로그는 volsnap 시스템 이벤트 로그에 있습니다. "볼륨 C에 디스크 공간이 부족 합니다. C: 섀도 복사본에 대 한 섀도 복사본 저장소 크기를 증가 시키려면 볼륨 C: 볼륨 C:의 모든 섀도 복사본을 삭제 하는 위험 | -백업이 진행 되는 동안 섀도 복사본을 늘릴 수 있는 충분 한 공간이 있도록 이벤트 로그에서 강조 표시 된 볼륨의 공간을 확보 합니다. <br/><br/> -섀도 복사본 공간을 구성 하는 동안 섀도 복사본에 사용 되는 공간 크기를 제한할 수 있습니다. 자세한 내용은 관련 [문서](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)를 참조하세요.

### <a name="efi-partition-locked"></a>EFI 파티션 잠김

| 증상 | 해결 방법
| -- | --
| "EFI 시스템 파티션이 잠겨 있어서 시스템 상태 백업에 실패 했습니다." 라는 오류 메시지와 함께 MARS 에이전트가 실패 합니다. 이는 타사 보안 또는 백업 소프트웨어에의 한 시스템 파티션 액세스 때문일 수 있습니다. " | -타사 보안 소프트웨어로 인해 문제가 발생 한 경우 MARS 에이전트를 허용 하도록 바이러스 백신 공급 업체에 문의 해야 합니다. <br/><br/> -타사 백업 소프트웨어가 실행 중인 경우 완료 될 때까지 기다렸다가 다시 시도 하십시오.

## <a name="next-steps"></a>다음 단계

- 리소스 관리자 배포의 Windows 시스템 상태에 대 한 자세한 내용은 [Windows Server 시스템 상태 백업](backup-azure-system-state.md) 을 참조 하세요.
