---
title: IoT 에이전트에 대한 Linux C 에이전트 Azure Security Center 설치 및 배포 가이드 | Microsoft Docs
description: 32 비트 및 64 비트 Linux에서 IoT 에이전트에 대한 Azure Security Center를 설치 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7578811c37cd0bbe47821dadacce5fa5974f56cf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812738"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Linux 용 IoT C 기반 보안 에이전트에 대한 Azure Security Center 배포

이 가이드에서는 Linux에서 IoT C 기반 보안 에이전트에 대한 Azure Security Center를 설치 하 고 배포 하는 방법을 설명 합니다.

이 가이드에서는 다음 작업 방법을 배웁니다. 
> [!div class="checklist"]
> * 설치
> * 배포 확인
> * 에이전트 제거
> * 문제 해결 

## <a name="prerequisites"></a>전제 조건

다른 플랫폼 및 에이전트 버전의 경우 [올바른 보안 에이전트 선택](how-to-deploy-agent.md)을 참조 하세요.

1. 보안 에이전트를 배포 하려면 sudo ()에 설치 하려는 컴퓨터에서 로컬 관리자 권한이 필요 합니다.

1. 디바이스에 대한 [보안 모듈을 만듭니다](quickstart-create-security-twin.md).

## <a name="installation"></a>설치 

보안 에이전트를 설치 하 고 배포 하려면 다음 워크플로를 사용 합니다.


1. 최신 버전을 [GitHub](https://aka.ms/iot-security-github-c)에서 컴퓨터에 다운로드 합니다.

1. 패키지의 콘텐츠를 추출 하 고 _/> 설치_ 폴더로 이동 합니다.

1. 다음 명령을 실행 하 여 **Installsecurityagent 스크립트** 에 실행 권한을 추가 합니다.
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. 다음으로, 다음을 실행합니다. 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   인증 매개 변수에 대한 자세한 내용은 [인증 구성 방법](concept-security-agent-authentication-methods.md)을 참조하세요.

이 스크립트는 다음 기능을 수행 합니다.

1. 필수 구성 요소를 설치합니다.

2. 서비스 사용자를 추가 합니다 (대화형 로그인 사용 안 함).

3. 에이전트를 **디먼**으로 설치합니다. 여기서는 서비스 관리를 위해 디바이스에서 **systemd**를 사용한다고 가정합니다.

4. 제공된 인증 매개 변수를 사용하여 에이전트를 구성합니다. 

추가 도움말을 보려면 -help 매개 변수를 사용하여 스크립트를 실행합니다. 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>에이전트 제거

에이전트를 제거 하려면 –-uninstall 매개 변수를 사용 하 여 스크립트를 실행 합니다.

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>문제 해결
다음을 실행하여 배포 상태를 확인합니다.

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>다음 단계
- IoT 서비스에 대한 Azure Security Center [개요](overview.md) 를 참조 하십시오.
- IoT [아키텍처](architecture.md) 에 대한 Azure Security Center에 대해 자세히 알아보기
- [서비스](quickstart-onboard-iot-hub.md)를 사용하도록 설정합니다.
- [FAQ](resources-frequently-asked-questions.md)를 참조합니다.
- [보안 경고](concept-security-alerts.md)를 살펴봅니다.
