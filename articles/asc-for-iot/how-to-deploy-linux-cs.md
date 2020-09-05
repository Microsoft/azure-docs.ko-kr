---
title: 'Linux c # 에이전트 설치 & 배포'
description: 32 비트 및 64 비트 Linux에서 IoT 에이전트에 대 한 Azure Security Center를 설치 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 460e3f3955778945600bb3a1f9762a0ac50f5805
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205915"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Linux에 IoT용 Azure Security Center C# 기반 보안 에이전트 배포

이 가이드에서는 Linux에서 IoT c # 기반 보안 에이전트에 대 한 Azure Security Center를 설치 하 고 배포 하는 방법을 설명 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결

## <a name="prerequisites"></a>필수 구성 요소

다른 플랫폼 및 에이전트 버전의 경우 [올바른 보안 에이전트 선택](how-to-deploy-agent.md)을 참조 하세요.

1. 보안 에이전트를 배포하려면 설치하려는 머신에 대한 로컬 관리자 권한이 필요합니다.

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치

보안 에이전트를 배포 하려면 다음 단계를 사용 합니다.

1. 최신 버전을 [GitHub](https://aka.ms/iot-security-github-cs)에서 컴퓨터에 다운로드 합니다.

1. 패키지의 콘텐츠를 추출 하 고 _/install_ 폴더로 이동 합니다.

1. `chmod +x InstallSecurityAgent.sh`를 실행하여 **InstallSecurityAgent 스크립트**에 실행 권한을 추가합니다.

1. 다음으로 **루트 권한**으로 다음 명령을 실행 합니다.

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   인증 매개 변수에 대 한 자세한 내용은 [인증을 구성 하는 방법](concept-security-agent-authentication-methods.md)을 참조 하세요.

이 스크립트는 다음 작업을 수행합니다.

- 필수 구성 요소를 설치합니다.

- 서비스 사용자를 추가 합니다 (대화형 로그인 사용 안 함).

- **디먼** 으로 에이전트를 설치 합니다.-장치에서 클래식 배포 모델에 **systemd** 를 사용 한다고 가정 합니다.

- 에이전트가 특정 작업을 루트로 수행할 수 있도록 **sudoers** 을 구성 합니다.

- 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다.

추가 도움말을 보려면 -help 매개 변수를 사용하여 스크립트를 실행합니다(`./InstallSecurityAgent.sh --help`).

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거하려면 -u 매개 변수를 사용하여 스크립트를 실행합니다(`./InstallSecurityAgent.sh -u`).

> [!NOTE]
> 설치 제거는 설치 중에 설치된 모든 누락된 필수 구성 요소를 제거하지 않습니다.

## <a name="troubleshooting"></a>문제 해결

1. 다음을 실행하여 배포 상태를 확인합니다.

    `systemctl status ASCIoTAgent.service`

1. 로깅을 사용하도록 설정합니다.
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

- IoT 서비스에 대 한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대 한 Azure Security Center에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md) 읽기
- [경고](concept-security-alerts.md)를 살펴봅니다.
