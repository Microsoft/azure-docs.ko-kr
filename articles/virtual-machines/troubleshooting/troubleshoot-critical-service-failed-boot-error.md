---
title: Azure VM을 부팅할 때 CRITICAL SERVICE FAILED 오류 발생 | Microsoft Docs
description: 부팅할 때 발생하는 "0x0000005A-CRITICAL SERVICE FAILED" 오류를 해결하는 방법 알아보기 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: e828a8fc4211a0f0c4b53a9e18fa1c2fb6f6916b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593230"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Azure VM을 부팅할 때 Windows에서 블루 스크린에 "CRITICAL SERVICE FAILED"가 표시됨
이 문서에서는 Microsoft Azure에서 Windows VM(가상 머신)을 부팅할 때 발생할 수 있는 "CRITICAL SERVICE FAILED" 오류에 대해 설명합니다. 그리고 해당 문제를 해결할 수 있는 문제 해결 단계를 제공합니다. 

> [!NOTE] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용을 설명하고 있으며, 새로운 배포에 대해 클래식 배포 모델 대신 이 모델을 사용하는 것이 좋습니다.

## <a name="symptom"></a>증상 

Windows VM이 시작되지 않습니다. [부트 진단](./boot-diagnostics.md)에서 부트 스크린샷을 확인하면 블루 스크린에 다음 오류 메시지 중 하나가 표시됩니다.

- "PC에 문제가 생겨 다시 시작해야 합니다. 다시 시작할 수 있습니다. 이 문제와 가능한 수정 사항에 대한 자세한 내용은 http://windows.com/stopcode를 참조하세요. 지원 담당자를 호출하는 경우 이 정보를 제공합니다. 코드 중지: 중요 서비스 실패" 
- "PC에 문제가 생겨 다시 시작해야 합니다. 일부 오류 정보를 수집하고 있습니다. 그런 다음 자동으로 다시 시작합니다. 자세한 내용을 보려면 나중에 온라인에서 다음 오류를 검색할 수 있습니다. CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>원인

중지 오류는 다양한 원인으로 인해 발생합니다. 가장 일반적인 원인은 다음과 같습니다.
- 드라이버 문제
- 손상된 시스템 파일 또는 메모리
- 애플리케이션에서 메모리의 금지된 섹터에 액세스

## <a name="solution"></a>해결 방법 

이 문제를 해결하려면 [지원을 요청하고 덤프 파일을 제출](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)하세요. 덤프 파일이 있으면 Microsoft에서 문제를 더 빠르게 진단할 수 있습니다. 또는 다음 자가 진단 솔루션을 사용해 볼 수도 있습니다.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>복구 VM에 OS 디스크 연결

1. 영향을 받는 VM의 OS 디스크 스냅숏을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅숏](../windows/snapshot-copy-managed-disk.md)을 참조하세요.
2. [복구 VM에 OS 디스크를 연결합니다](./troubleshoot-recovery-disks-portal-windows.md). 
3. 복구 VM에 대한 원격 데스크톱 연결을 설정합니다.

### <a name="enable-dump-logs-and-serial-console"></a>덤프 로그 및 직렬 콘솔을 사용하도록 설정

덤프 로그 및 [직렬 콘솔](./serial-console-windows.md)을 사용하는 경우 Microsoft에서 추가 문제 해결 작업을 수행할 수 있습니다.

덤프 로그 및 직렬 콘솔을 사용하도록 설정하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. 다음 스크립트를 실행합니다.

    이 스크립트에서는 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 실제로 스크립트를 실행할 때는 이 문자를 VM에 적합한 값으로 바꿔야 합니다.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>서명되지 않은 드라이버 교체

1. 복구 VM의 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다. 이 명령은 해당하는 OS 디스크가 다음 부팅 시 안전 모드로 시작되도록 설정합니다.

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    예를 들어 연결한 OS 디스크가 드라이브 F인 경우 다음 명령을 실행합니다.

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [OS 디스크를 분리한 다음 해당 VM에 OS 디스크를 다시 연결합니다](troubleshoot-recovery-disks-portal-windows.md). VM이 안전 모드로 부팅됩니다. 오류가 계속 발생하면 선택적 단계로 이동합니다.
3. **실행** 상자를 열고 **검증 도구**를 실행하여 드라이버 검증 도구 관리자 도구를 시작합니다.
4. **서명되지 않은 드라이버 자동으로 선택**을 선택하고 **다음**을 클릭합니다.
5. 서명되지 않은 드라이버 파일 목록이 표시됩니다. 파일 이름을 기억해 둡니다.
6. 이러한 파일의 동일 버전을 작동하는 VM에서 복사한 다음 서명되지 않은 파일을 바꿉니다. 

7. 안전 부팅 설정을 제거합니다.

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  VM을 다시 시작합니다. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>선택 사항: 덤프 크래시 모드로 덤프 로그 분석

덤프 로그를 직접 분석 하려면 다음이 단계를 수행 합니다.

1. 복구 VM에 OS 디스크를 연결합니다.
2. 연결한 OS 디스크에서 **\windows\system32\config**로 이동합니다. 롤백이 필요한 경우에 대비해 모든 파일을 백업으로 복사합니다.
3. **레지스트리 편집기**(regedit.exe)를 시작합니다.
4. **HKEY_LOCAL_MACHINE** 키를 선택합니다. 메뉴에서 **파일** > **Hive 로드**를 선택합니다.
5. 연결한 OS 디스크에서 **\windows\system32\config\SYSTEM** 폴더로 이동합니다. Hive 이름으로 **BROKENSYSTEM**을 입력합니다. 새 레지스트리 Hive는 **HKEY_LOCAL_MACHINE** 키 아래에 표시됩니다.
6. **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl**로 이동하여 값을 다음과 같이 변경합니다.

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  **BROKENSYSTEM**을 선택합니다. 메뉴에서 **파일** > **Hive 언로드**를 선택합니다.
8.  디버그 모드로 부팅되도록 BCD 설정을 수정합니다. 이렇게 하려면 관리자 권한 명령 프롬프트에서 다음 명령을 사용합니다.

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [OS 디스크를 분리한 다음 해당 VM에 OS 디스크를 다시 연결합니다](troubleshoot-recovery-disks-portal-windows.md).
10. VM을 부팅하여 덤프 분석이 표시되는지 확인합니다. 로드할 수 없는 파일을 찾습니다. 이 파일을 작동하는 VM의 파일로 바꿔야 합니다. 

    덤프 분석 샘플은 다음과 같습니다. filecrypt.sys에서 **오류**가 발행했는지 확인할 수 있습니다. "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. VM이 작동하고 정상적으로 부팅되면 덤프 크래시 설정을 제거합니다.

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
