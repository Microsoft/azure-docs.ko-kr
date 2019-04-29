---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 796b6280c2814e6358ce1942a230488cd484415d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462446"
---
장치 문제를 해결 하려면 Microsoft 지원 팀에 문의 해야 합니다. Microsoft 지원을 지원 세션을 사용 하 여 장치에 로그인 해야 합니다.

1. 관리자 권한으로 Windows PowerShell을 실행합니다.
2. 변수는 장치 IP 주소를 할당 합니다. 명령 프롬프트에 다음을 입력합니다.

    $ip = "<device_ip>"

    대체 `<device_ip>` 장치의 IP 주소를 사용 하 여 합니다.
 
3. 장치의 Windows PowerShell 세션을 시작 하 고 minishell runspace에 연결 합니다.

    ```
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Force
    $minishellSession= New-PSSession -ComputerName $ip -ConfigurationName "Minishell" -Credential ~\EdgeUser
    ```
    메시지가 표시 되 면 장치에 로그인 하는 데 사용한 암호를 제공 합니다.

4. 지원 세션 runspace에 연결 합니다.  

    ```
    Invoke-Command -Session $minishellSession -ScriptBlock { Enable-HcsSupportAccess }
    ```  
    이 명령은 암호화 된 키를 출력 합니다. 메모장과 같은 텍스트 편집기에 이 문자열을 복사합니다.

5. 이 키를 지원 엔지니어에 게 전자 메일로 보냅니다. 지원 엔지니어가 지원 세션에 대 한 해당 액세스 키를 만듭니다.

6. 지원 엔지니어에 게 액세스 키를 사용 하 여 다음 명령에서 암호로 됩니다.

    ``` 
    $supportSession = New-PSSession -ComputerName $ip -Credential ~\EdgeSupport -ConfigurationName SupportSession
    Enter-PSSession -Session $supportSession
    ```
7. 지원 세션에서 이제 합니다. 다음 샘플 출력에는 지원 세션에 연결 하는 방법을 보여 줍니다.

    ```
    PS C:\WINDOWS\system32> $ip = "10.100.10.10";
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Force
    PS C:\WINDOWS\system32> $minishellSession= New-PSSession -ComputerName $ip -ConfigurationName "Minishell" -Credential ~\EdgeUser
    WARNING: The Windows PowerShell interface of your device is intended to
    be used only for the initial network configuration. Please
    engage Microsoft Support if you need to access this interface
    to troubleshoot any potential issues you may be experiencing.
    Changes made through this interface without involving Microsoft
    Support could result in an unsupported configuration.
    PS C:\WINDOWS\system32> Invoke-Command -Session $minishellSession -ScriptBlock { Enable-HcsSupportAccess }
    UAAAADcAMQAyAEYAQwBDAEEANwA0ADIARABCADUANgA1ADkANQA0AEIAQgA0ADgAMQBEADEAMQAxADMAMgAyADYAOAA3AEIANwA0ADgAMwBFAEMAiix6gA6zBIELa6vbb73CSO9/Yo/g85QRp2g5ngw773sKgBVcirk5sTHFuSQXWRirggGEip9NI5m54iPcVxdIEcoH+2vlvxCAJVWXOLGOB6WqWDtzR3XWSwJKig95LZfBjtPO1sM5TZLu65iCRCB4AV9nOezhAoy2lGdTuZOpXP2w5FIZPFvmgR4+4m+pfsD0NQSsw+PD3hNBHvUUyIhc4WTVaIJbzoJBzg06uJnc6C0Zo1YnYD6u8SoevejXbt3dgU7m36Vg3K0qPfCPA9WNLd71uiUsV1lMLwCabj60rAuRp/qJlPRXPU5PbGeayKNQJQfUkQYh6afUxtOXohke+A==
    PS C:\WINDOWS\system32> $supportSession = New-PSSession -ComputerName $ip -Credential ~\EdgeSupport -ConfigurationName SupportSession
    PS C:\WINDOWS\system32> Enter-PSSession -Session $supportSession
    [10.100.10.10]: PS C:\Users\EdgeSupport\Documents>
    ```
    지원 세션 활성화 될 때 했던 동일한 암호화 된 액세스 키를 사용 하는 경우는 `Get-HcsSupportAccessKey` cmdlet.

    ```
    [10.128.24.33]: PS C:\Users\EdgeSupport\Documents> Get-HcsSupportAccessKey
    UAAAADcAMQAyAEYAQwBDAEEANwA0ADIARABCADUANgA1ADkANQA0AEIAQgA0ADgAMQBEADEAMQAxADMAMgAyADYAOAA3AEIANwA0ADgAMwBFAEMAiix6gA6zBIELa6vbb73CSO9/Yo/g85QRp2g5ngw773sKgBVcirk5sTHFuSQXWRirggGEip9NI5m54iPcVxdIEcoH+2vlvxCAJVWXOLGOB6WqWDtzR3XWSwJKig95LZfBjtPO1sM5TZLu65iCRCB4AV9nOezhAoy2lGdTuZOpXP2w5FIZPFvmgR4+4m+pfsD0NQSsw+PD3hNBHvUUyIhc4WTVaIJbzoJBzg06uJnc6C0Zo1YnYD6u8SoevejXbt3dgU7m36Vg3K0qPfCPA9WNLd71uiUsV1lMLwCabj60rAuRp/qJlPRXPU5PbGeayKNQJQfUkQYh6afUxtOXohke+A==
    [10.128.24.33]: PS C:\Users\EdgeSupport\Documents>
    ```

    지원 세션 계속 8 시간 동안 사용 됩니다. 언제 든 지 지원 세션을 사용 하지는 `Disable-HcsSupportAccess` cmdlet.

