---
title: 감사 정책 구성 정책을 적용 하는 동안 가상 컴퓨터가 응답 하지 않습니다.
description: 이 문서에서는 Azure VM의 부팅을 방지 하는 감사 정책 구성 정책을 적용 하는 동안 VM (가상 머신)이 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3f6383b5-81fa-49ea-9434-2fe475e4cbef
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: e55fa377f28572901202b4d722bea70786edae22
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942128"
---
# <a name="virtual-machine-is-unresponsive-while-applying-audit-policy-configuration-policy"></a>감사 정책 구성 정책을 적용 하는 동안 가상 컴퓨터가 응답 하지 않습니다.

이 문서에서는 Azure VM의 부팅을 방지 하는 감사 정책 구성 정책을 적용 하는 동안 VM (가상 머신)이 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 **감사 정책 구성 정책을 적용**하는 메시지를 사용 하 여 부팅 하는 동안 운영 체제 (OS)가 멈춘 것이 표시 되는 것을 볼 수 있습니다.

  !["감사 정책 구성 정책 적용" 메시지를 사용 하 여 OS를 부팅 합니다.](./media/vm-unresponsive-applying-audit-configuration-policy/1.png)

  !["감사 정책 구성 정책 적용" 메시지와 함께 Windows Server 2012에서 부팅 하는 OS](./media/vm-unresponsive-applying-audit-configuration-policy/2.png)

## <a name="cause"></a>원인

정책이 이전 사용자 프로필을 정리 하려고 할 때 충돌 하는 잠금이 있습니다.

> [!NOTE]
> 이는 Windows Server 2012 및 Windows Server 2012 R2에만 적용됩니다.

다음은 문제에 대 한 정책 *입니다. Computer Configuration\Policies\Administrative template \ System/User 프로 파일링은 시스템을 다시 시작할 때 지정 된 기간 (일) 보다 오래 된 사용자 프로필을 삭제 합니다.*

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM을 만들고 액세스합니다.
1. 정책을 사용하지 않도록 설정합니다.
1. 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
1. VM을 다시 빌드합니다.
1. 메모리 덤프 파일을 수집 하 고 지원 티켓을 제출 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="disable-the-policy"></a>정책 사용 안 함

1. 복구 VM에서 **레지스트리 편집기**를 엽니다.
1. 키 **HKEY_LOCAL_MACHINE** 를 찾고 메뉴에서 **파일 > Hive 로드** 를 선택 합니다.

   ![하이브를 로드 하는 레지스트리 편집기 내의 탐색](./media/vm-unresponsive-applying-audit-configuration-policy/3.png)

   - Load Hive를 사용 하 여 오프 라인 시스템에서 레지스트리 키를 로드할 수 있습니다. 이 경우 시스템은 복구 VM에 연결 된 손상 된 디스크입니다.
   - 시스템 수준 설정은 **HKEY_LOCAL_MACHINE** 에 저장 되며 **HKLM**으로 축약 될 수 있습니다.

1. 연결 된 디스크에서 파일을 엽니다 `\windows\system32\config\SOFTWARE` .

   - 이름을 입력 하 라는 메시지가 표시 되 면 **BROKENSOFTWARE**를 입력 합니다.
   - **BROKENSOFTWARE** 이 로드 되었는지 확인 하려면 **HKEY_LOCAL_MACHINE** 를 확장 하 고 추가 된 **BROKENSOFTWARE** 키를 찾습니다.

1. **BROKENSOFTWARE** 으로 이동 하 여 로드 된 Hive에 **cleanupprofiles** 키가 있는지 확인 합니다.

   - 키가 있는 경우 **Cleanupprofiles** 정책이 설정 됩니다. 해당 값은 일 단위로 측정 된 보존 정책을 나타냅니다.
   - 키가 없는 경우 **Cleanupprofiles** 정책이 설정 되지 않습니다. 이 경우 [메모리 덤프 파일을 사용 하 여 지원 티켓을 제출](#collect-the-memory-dump-file-and-submit-a-support-ticket)하는 것으로 건너뜁니다.

1. 다음 명령을 사용 하 여 **Cleanupprofiles** 키를 삭제 합니다.

   `reg delete "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows\System" /v CleanupProfiles /f`

1. 다음 명령을 사용 하 여 **BROKENSOFTWARE** hive를 언로드합니다.

   `reg unload HKLM\BROKENSOFTWARE`

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>직렬 콘솔과 메모리 덤프 수집 사용

**권장**: VM을 다시 빌드하기 전에 다음 스크립트를 실행하여 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다.
1. BCD 저장소 데이터를 나열 하 고 다음 단계에서 사용할 부팅 로더 식별자를 확인 합니다.

   1. 1 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.

      `bcdedit /store <BOOT PARTITON>:\boot\bcd /enum`

      - 명령에서를 `<BOOT PARTITON>` 부팅 폴더를 포함 하는 연결 된 디스크의 파티션 문자로 바꿉니다.

        ![그림 4는 Windows 부팅 로더 아래에 식별자 번호를 나열 하는 1 세대 VM에 BCD 저장소를 나열 하는 출력을 보여 줍니다.](./media/vm-unresponsive-applying-audit-configuration-policy/4.png)

   1. 2 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.

      `bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

      - 명령에서를 `<LETTER OF THE EFI SYSTEM PARTITION>` EFI 시스템 파티션의 문자로 바꿉니다.
      - **EFI 시스템 파티션**으로 레이블이 지정 된 적절 한 시스템 파티션을 식별 하는 디스크 관리 콘솔을 시작 하는 것이 유용할 수 있습니다.
      - 식별자는 고유한 GUID 이거나 기본 **bootmgr**일 수 있습니다.

1. 다음 명령을 실행합니다.

   **직렬 콘솔을 사용하도록 설정합니다.**
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기(RAM)보다 큰지 확인합니다.

   OS 디스크에 공간이 부족한 경우 메모리 덤프 파일이 생성될 위치를 변경하고 사용 가능한 공간이 충분한 VM에 연결된 데이터 디스크의 위치를 참조합니다. 이 위치를 변경하려면 다음 명령에서 **%SystemRoot%** 를 데이터 디스크의 드라이브 문자(예: **F:** )로 바꿉니다.

   OS 덤프를 사용하도록 설정하기 위한 권장 구성:

   **손상된 OS 디스크에서 레지스트리 하이브를 로드합니다.**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **ControlSet001에서 사용하도록 설정합니다.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **ControlSet002에서 사용하도록 설정합니다.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **손상된 OS 디스크를 언로드합니다.**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-virtual-machine"></a>가상 컴퓨터 다시 빌드

1. [VM 복구 명령의 5단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 VM을 다시 빌드합니다.

1. VM이 정상적으로 부팅 되는지 테스트 하 여 문제가 해결 되었는지 확인 합니다.

   - 문제가 해결 되지 않은 경우에는 계속 [덤프 파일을 수집 하 여 지원 티켓을 제출](#collect-the-memory-dump-file-and-submit-a-support-ticket)합니다.
   - 문제가 해결 된 경우에는 추가 단계가 필요 하지 않습니다.

문제가 해결 된 경우에는 정책을 로컬에서 사용할 수 없습니다. 영구 솔루션의 경우 Vm에서 CleanupProfiles 정책을 사용 하지 마세요. 사용자 프로필은 자동으로 삭제 됩니다. 다른 방법을 사용 하 여 예약 된 작업 또는 스크립트와 같은 프로필 정리 간격를 수행 합니다.

**이 정책을 사용 하지 않음:** 
 *Machine\Admin 시스템 profil\은 시스템을 다시 시작할 때 지정 된 기간 (일) 보다 오래 된 사용자 프로필을 삭제 합니다.*

### <a name="the-issue-should-now-be-fixed"></a>이제 문제를 해결 해야 합니다.

VM을 테스트 하 여 평소와 같이 작동 하는지 확인 합니다. 여전히 문제가 발생 하는 경우 다음 섹션을 계속 진행 하 여 도움을 받을 수 있습니다.

### <a name="collect-the-memory-dump-file-and-submit-a-support-ticket"></a>메모리 덤프 파일을 수집 하 고 지원 티켓을 제출 합니다.

이 문제를 해결 하려면 먼저 작동 중단에 대 한 메모리 덤프 파일을 수집한 다음 메모리 덤프 파일을 사용 하 여 지원에 문의 해야 합니다. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1. [Vm 복구 명령의](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3 단계를 사용 하 여 새 복구 vm을 준비 합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

1. 복구 VM에서 연결 된 OS 디스크의 windows 폴더로 이동 합니다. 연결 된 OS 디스크에 할당 된 드라이버 문자에 *F*레이블이 지정 된 경우으로 이동 해야 `F:\Windows` 합니다.
1. `memory.dmp`파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.
1. 파일을 찾는 데 문제가 있는 경우 `memory.dmp` 대신 [직렬 콘솔에서 비 마스크 인터럽트 (NMI) 호출](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) 을 사용 합니다. [여기에서 NMI 통화를 사용 하 여 크래시 덤프 파일을 생성](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)하려면 가이드를 따르세요.
