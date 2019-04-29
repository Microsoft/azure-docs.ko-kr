---
title: 설치 하 여 Linux 배포 가이드 C# IoT 미리 보기에 대 한 Azure Security Center의 에이전트 | Microsoft Docs
description: 32 비트 및 64 비트 Linux에서 IoT 에이전트에 대 한 Azure Security Center를 설치 하는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 4a53bcf77696d3aa53a4a404bfacd6f6d468885b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357896"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>IoT 용 Azure Security Center를 배포할 C#-Linux에 대 한 보안 에이전트 기반

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 가이드에서는 설치 및 IoT에 대 한 Azure 보안 센터 (ASC)를 배포 하는 방법을 설명 C#-Linux에서 보안 에이전트를 기반으로 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>필수 조건

다른 플랫폼 및 에이전트 버전에 대 한 참조 [적합 한 보안 에이전트 선택](how-to-deploy-agent.md)합니다.

1. 보안 에이전트를 배포하려면 설치하려는 머신에 대한 로컬 관리자 권한이 필요합니다. 

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치 

보안 에이전트를 배포하려면 다음을 수행합니다.

1. [Gittub](https://aka.ms/iot-security-github-cs)에서 최신 버전을 머신에 다운로드합니다.

1. 패키지의 콘텐츠를 추출하고 _/Install_ 폴더로 이동합니다.

1. `chmod +x InstallSecurityAgent.sh`를 실행하여 **InstallSecurityAgent 스크립트**에 실행 권한을 추가합니다. 

1. 다음으로, 다음을 실행합니다. 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트는 다음을 수행합니다.

- 필수 구성 요소를 설치합니다.

- 대화형 로그인을 사용하지 않도록 설정된 서비스 사용자를 추가합니다.

- 에이전트를 **디먼**으로 설치합니다. 여기서는 서비스 관리를 위해 디바이스에서 **systemd**를 사용한다고 가정합니다.

- 에이전트에서 특정 작업을 루트로 수행할 수 있도록 **sudoers**를 구성합니다.

- 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다.


추가 도움말을 보려면 -help 매개 변수를 사용하여 스크립트를 실행합니다(`./InstallSecurityAgent.sh --help`).

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 -u 매개 변수를 사용하여 스크립트를 실행합니다(`./InstallSecurityAgent.sh -u`). 

> [!NOTE]
> 설치 제거는 설치 중에 설치된 모든 누락된 필수 구성 요소를 제거하지 않습니다.

## <a name="troubleshooting"></a>문제 해결  

1. 다음을 실행하여 배포 상태를 확인합니다.

    `systemctl status ASCIoTAgent.service`

2. 로깅을 사용하도록 설정합니다.  
   에이전트가 시작되지 않는 경우 자세한 정보를 얻으려면 로깅을 설정합니다.

   다음을 수행하여 로깅을 설정합니다.

   1. Linux 편집기에서 편집할 구성 파일을 엽니다.

        `vi /var/ASCIoTAgent/General.config`

   1. 다음 값을 편집합니다. 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       **logFilePath** 값은 구성할 수 있습니다. 

       > [!NOTE]
       > 문제가 해결되면 로깅을 **해제**하는 것이 좋습니다. 로깅을 **설정**으로 그대로 두면 로그 파일 크기와 데이터 사용량이 증가합니다.

   1. 다음을 실행하여 에이전트를 다시 시작합니다.

       `systemctl restart ASCIoTAgent.service`

   1. 오류에 대한 자세한 내용을 확인하려면 로그 파일을 살펴봅니다.  

       로그 파일 위치는 `/var/ASCIoTAgent/IotAgentLog.log`입니다.

       2단계에서 **logFilePath**에 대해 선택한 이름에 따라 파일 위치 경로를 변경합니다. 

## <a name="next-steps"></a>다음 단계

- IoT용 ASC 서비스 [개요](overview.md)를 참조합니다.
- IoT용 ASC 서비스 [아키텍처](architecture.md)를 자세히 알아봅니다.
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [경고](concept-security-alerts.md)를 살펴봅니다.