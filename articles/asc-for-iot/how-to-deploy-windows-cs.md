---
title: Azure Security Center의 미리 보기 IoT 에이전트용 Windows 설치 | Microsoft Docs
description: 32 비트 또는 64 비트 Windows 장치에서 IoT 에이전트에 대 한 Azure Security Center를 설치 하는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 601ee706adedf522890acc3f3996a7d36b349348
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357781"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>IoT 용 Azure Security Center는 배포 C#-Windows에 대 한 보안 에이전트 기반

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 가이드에서는 IoT 용 Azure 보안 센터 (ASC)를 설치 하는 방법을 설명 C#-Windows에서 보안 에이전트를 기반으로 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>필수 조건

다른 플랫폼과 에이전트 종류는 [적합한 보안 에이전트 선택](how-to-deploy-agent.md)을 참조하세요.

1. 설치 하려는 컴퓨터에 대 한 로컬 관리자 권한 

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치 

보안 에이전트를 설치하려면 다음을 수행합니다.

1. Windows IoT에 대 한 ASC를 설치 하려면 C# 장치에 에이전트 버전을 다운로드 최신 컴퓨터에 ASC에서 IoT [GitHub 리포지토리](https://github.com/Azure/Azure-IoT-Security-Agent-CS)합니다.

2. 패키지의 콘텐츠를 추출하고 /Install 폴더로 이동합니다.

3. 관리자 권한으로 Windows PowerShell을 엽니다. 
    1. ```Unblock-File .\InstallSecurityAgent.ps1```을 실행하여 InstallSecurityAgent 스크립트에 실행 권한을 추가합니다.
    
        그리고 다음을 실행합니다.

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    예를 들면 다음과 같습니다.
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트는 다음을 수행합니다.

- 필수 구성 요소를 설치합니다.

- 대화형 로그인을 사용하지 않도록 설정된 서비스 사용자를 추가합니다.

- 에이전트를 **시스템 서비스**로 설치합니다.

- 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다.


추가 도움말을 보려면 PowerShell에서 Get-Help 명령을 사용합니다. <br>Get-Help 예제:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>배포 상태 확인

- 다음을 실행하여 에이전트 배포 상태를 확인합니다.<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 다음을 수행합니다.

1. **-mode** 매개 변수를 **Uninstall**로 설정한 상태에서 다음 PowerShell 스크립트를 실행합니다.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>문제 해결

에이전트가 시작되지 않는 경우 자세한 정보를 얻으려면 로깅을 설정합니다(기본적으로 로깅은 *해제*임).

로깅을 설정하려면 다음을 수행합니다.

1. 표준 파일 편집기를 사용 하 여 편집에 대 한 구성 파일 (General.config)을 엽니다.

1. 다음 값을 편집합니다.

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > 문제가 해결되면 로깅을 **해제**하는 것이 좋습니다. 로깅을 **설정**으로 그대로 두면 로그 파일 크기와 데이터 사용량이 증가합니다. 

1. 다음 PowerShell 또는 명령줄을 실행하여 에이전트를 다시 시작합니다.

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   또는

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. 오류에 대한 자세한 내용을 확인하려면 로그 파일을 검토합니다.

   로그 파일 위치: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>다음 단계
- IoT용 ASC 서비스 [개요](overview.md)를 참조합니다.
- IoT용 ASC 서비스 [아키텍처](architecture.md)를 자세히 알아봅니다.
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [경고](concept-security-alerts.md)를 살펴봅니다.