---
title: 설치 하 여 IoT 에이전트 미리 보기에 대 한 Azure Security Center의 Linux C 에이전트 배포 가이드 | Microsoft Docs
description: 32 비트 및 64 비트 Linux에서 IoT 에이전트에 대 한 Azure Security Center를 설치 하는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 0089fd1af6576f9bcdebe4b7f270a573205dea82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358355"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Linux 용 IoT C 기반 보안 에이전트에 대 한 Azure Security Center 배포

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 가이드에는 설치 하 여 Linux에서 IoT C 기반 보안 에이전트에 대 한 Azure 보안 센터 (ASC)를 배포 하는 방법을 설명 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>필수 조건

다른 플랫폼 및 에이전트 버전에 대 한 참조 [적합 한 보안 에이전트 선택](how-to-deploy-agent.md)합니다.

1. 보안 에이전트를 배포 하려면 로컬 관리자 권한 (sudo)에 설치 하려는 컴퓨터에 필요 합니다.

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치 

보안 에이전트를 설치하고 배포하려면 다음을 수행합니다.


1. [Gittub](https://aka.ms/iot-security-github-c)에서 최신 버전을 머신에 다운로드합니다.

1. 패키지의 콘텐츠를 추출하고 _/Install_ 폴더로 이동합니다.

1. 다음을 실행하여 **InstallSecurityAgent 스크립트**에 실행 권한을 추가합니다.
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 다음으로, 다음을 실행합니다. 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트는 다음을 수행합니다.

1. 필수 구성 요소를 설치합니다.

2. 대화형 로그인을 사용하지 않도록 설정된 서비스 사용자를 추가합니다.

3. 에이전트를 **디먼**으로 설치합니다. 여기서는 서비스 관리를 위해 디바이스에서 **systemd**를 사용한다고 가정합니다.

4. 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다. 

추가 도움말을 보려면 -help 매개 변수를 사용하여 스크립트를 실행합니다. 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거 하려면 사용 하 여 스크립트를 실행 합니다.--매개 변수를 제거 합니다.

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>문제 해결
다음을 실행하여 배포 상태를 확인합니다.

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>다음 단계
- IoT용 ASC 서비스 [개요](overview.md)를 참조합니다.
- IoT용 ASC 서비스 [아키텍처](architecture.md)를 자세히 알아봅니다.
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [보안 경고](concept-security-alerts.md)를 살펴봅니다.