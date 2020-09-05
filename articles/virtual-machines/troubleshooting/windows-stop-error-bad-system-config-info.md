---
title: Windows 중지 오류-0x00000074 잘못 된 시스템 구성 정보
description: 이 문서에서는 Windows를 부팅할 수 없고 Azure VM (가상 머신)의 잘못 된 시스템 구성 정보로 인해 다시 시작 해야 하는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 071b5786127af31a2ad3266c128dbfb7cacad656
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942121"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Windows 중지 오류-0x00000074 잘못 된 시스템 구성 정보

이 문서에서는 Windows를 부팅할 수 없고 Azure VM (가상 머신)의 잘못 된 시스템 구성 정보로 인해 다시 시작 해야 하는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 스크린 샷에서 Windows 중지 코드 **#0x00000074** 또는 **BAD_SYSTEM_CONFIG_INFO**표시 되는 것을 볼 수 있습니다.

*PC에서 문제가 발생 하 여 컴퓨터를 다시 시작 해야 합니다. 를 다시 시작할 수 있습니다.* 
 *이 문제 및 가능한 해결 방법에 대 한 자세한 내용은 http://windows.com/stopcode 다음을 참조 하세요.* 
 *지원 담당자를 호출 하는 경우 다음 정보를 제공 합니다.* 
 *Stop code: BAD_SYSTEM_CONFIG_INFO*

  ![Windows 중지 코드 0x00000074는 "BAD_SYSTEM_CONFIG_INFO"으로 표시 됩니다. Windows에서 사용자 PC에 문제가 발생 하 여 다시 시작 해야 함을 사용자에 게 알립니다.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>원인

**BAD_SYSTEM_CONFIG_INFO** 중지 코드는 **시스템** 레지스트리 하이브가 손상 된 경우에 발생 합니다. 이 오류는 다음과 같은 이유로 인해 발생할 수 있습니다.

- 레지스트리 하이브가 제대로 닫히지 않았습니다.
- 레지스트리 하이브가 손상 되었습니다.
- 레지스트리 키 또는 값이 누락 되었습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요:

1. 복구 VM을 만들고 액세스합니다.
1. Hive 손상이 있는지 확인 합니다.
1. 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
1. VM을 다시 빌드합니다.

> [!NOTE]
> 이 오류가 발생할 경우 게스트 OS (운영 체제)가 작동 하지 않습니다. 이 문제를 해결 하기 위해 오프 라인 모드에서 문제를 해결 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.
1. Hive 손상이 있는지 확인 합니다.
1. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.
1. 폴더를 복사 `\windows\system32\config` 하 여 정상 디스크 파티션이나 다른 안전한 위치에 저장 합니다. 중요 한 레지스트리 파일을 편집할 수 있기 때문에이 폴더를 예방 조치로 백업 합니다.

> [!NOTE]
> 레지스트리에 대 한 `\windows\system32\config` 변경 내용을 롤백해야 하는 경우 백업으로 폴더 복사본을 만듭니다.

### <a name="check-for-hive-corruption"></a>Hive 손상 확인

아래 지침에 따라 원인이 hive 손상으로 인 한 것인지 또는 hive가 올바르게 닫히지 않은 경우를 확인할 수 있습니다. Hive가 올바르게 닫히지 않으면 파일의 잠금을 해제 하 고 VM을 수정할 수 있습니다.

1. 복구 VM에서 **레지스트리 편집기** 응용 프로그램을 엽니다. Windows 검색 창에서 "REGEDIT"를 입력 하 여 찾습니다.
1. 레지스트리 편집기에서 **HKEY_LOCAL_MACHINE** 를 선택 하 여 강조 표시 하 고 **파일 > Hive 로드** ...를 선택 합니다. (채널 만들기...)을 선택합니다.
1. 으로 이동 하 여 `\windows\system32\config\SYSTEM` **열기**를 선택 합니다.
1. 이름을 입력 하 라는 메시지가 표시 되 면 **BROKENSYSTEM**를 입력 합니다.

   1. Hive를 열지 못했거나 비어 있으면 hive가 손상 된 것입니다. Hive가 손상 된 경우 [지원 티켓을 엽니다](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

     ![레지스트리 편집기에서 하이브를 로드할 수 없다는 오류가 발생 합니다.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Hive가 정상적으로 열리면 hive가 제대로 닫히지 않았습니다. 5 단계를 계속 합니다.

1. 제대로 닫히지 않은 hive를 수정 하려면 **BROKENSYSTEM** 를 강조 표시 하 고 **파일 > hive 언로드 ...** 를 선택 합니다. 파일의 잠금을 해제 합니다.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>직렬 콘솔과 메모리 덤프 수집 사용

**권장**: VM을 다시 빌드하기 전에 다음 스크립트를 실행하여 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다.
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
   
### <a name="rebuild-the-vm"></a>VM 다시 빌드

[VM 복구 명령의 5단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 VM을 다시 빌드합니다.
