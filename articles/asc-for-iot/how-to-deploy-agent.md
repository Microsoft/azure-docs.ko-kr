---
title: IoT 에이전트에 대한 Azure Security Center 선택 및 배포 | Microsoft Docs
description: Iot 장치에서 IoT 보안 에이전트의 Azure Security Center을 선택 하 고 배포 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: d70f2f3ec87c8673013bcf7b6f70ebcbb8d06f08
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770019"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>IoT 장치에서 보안 에이전트 선택 및 배포

IoT 용 Azure Security Center은 IoT 장치에서 데이터를 모니터링 하 고 수집 하는 보안 에이전트에 대한 참조 아키텍처를 제공 합니다.
자세히 알아보려면 [보안 에이전트 참조 아키텍처](security-agent-architecture.md)를 참조 하세요.

에이전트는 오픈 소스 프로젝트로 개발 되며 다음과 같은 두 가지 방법으로 사용할 수 있습니다. <br> [C](https://aka.ms/iot-security-github-c), 및 [C#](https://aka.ms/iot-security-github-cs)

이 문서에서는 다음 방법을 설명합니다. 
> [!div class="checklist"]
> * 보안 에이전트 특색 비교
> * 지원 되는 에이전트 플랫폼 검색
> * 솔루션의 올바른 에이전트 버전 선택

## <a name="understand-security-agent-options"></a>보안 에이전트 옵션 이해

IoT 보안 에이전트 버전에 대한 모든 Azure Security Center는 동일한 기능 집합을 제공 하 고 유사한 구성 옵션을 지원 합니다. 

C 기반 보안 에이전트는 메모리 사용 공간이 적고 사용 가능한 리소스가 적은 장치에 적합 합니다. 

|     | C 기반 보안 에이전트 | C#기반 보안 에이전트 |
| --- | ----------- | --------- |
| 오픈 소스 | [GitHub](https://aka.ms/iot-security-github-cs) 의 [MIT 라이선스](https://en.wikipedia.org/wiki/MIT_License) 에서 사용 가능 | [GitHub](https://aka.ms/iot-security-github-c) 의 [MIT 라이선스](https://en.wikipedia.org/wiki/MIT_License) 에서 사용 가능 |
| 개발 언어    | C | C# |
| 지원 되는 Windows 플랫폼 | 아닙니다. | 예 |
| Windows 필수 조건 | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| 지원 되는 Linux 플랫폼 인가요? | 예, x64 및 x86 | 예, x64만 |
| Linux 필수 구성 요소 | libunwind8, libcurl3, uuid-runtime, auditd, audispd-플러그 인 | libunwind8, libcurl3, uuid-runtime, auditd, audispd-플러그 인, sudo, netstat, iptables |
| 디스크 공간 | 10.5 M B | 90 M B |
| 메모리 사용 공간 (평균) | 5.5 M B | 33 M B |
| IoT Hub에 대한 [인증](concept-security-agent-authentication-methods.md) | 예 | 예 |
| 보안 데이터 [수집](how-to-agent-configuration.md#supported-security-events) | 예 | 예 |
| 이벤트 집계 | 예 | 예 |
| [보안 모듈](concept-security-module.md) 쌍을 통한 원격 구성 | 예 | 예 |
|

## <a name="security-agent-installation-guidelines"></a>보안 에이전트 설치 지침

**Windows**의 경우: 설치 securityagent. ps1 스크립트는 관리자 PowerShell 창에서 실행 해야 합니다. 

**Linux**: InstallSecurityAgent.sh은 수퍼유저로 실행 해야 합니다. 설치 명령에 "sudo"를 접두사로 사용 하는 것이 좋습니다.


## <a name="choose-an-agent-flavor"></a>에이전트 버전 선택 

올바른 에이전트를 선택 하려면 IoT 장치에 대해 다음과 같은 질문에 답변 하세요.

- _Windows Server_ 또는 _windows IoT Core_를 사용 하 고 있나요? 

    [Windows 용 C#기반 보안 에이전트를 배포](how-to-deploy-windows-cs.md)합니다.

- X86 아키텍처에서 Linux 배포를 사용 하 고 있나요? 

    [Linux 용 C 기반 보안 에이전트를 배포](how-to-deploy-linux-c.md)합니다.

- X64 아키텍처를 사용 하는 Linux 배포를 사용 하 고 있나요?

    두 에이전트의 버전을 모두 사용할 수 있습니다. <br>
    [Linux 용 C 기반 보안 에이전트를 배포](how-to-deploy-linux-c.md) 하거나 [linux 용 기반 보안 에이전트 C#를 배포](how-to-deploy-linux-cs.md)합니다.

두 에이전트 모두 동일한 기능 집합을 제공 하 고 유사한 구성 옵션을 지원 합니다.
자세히 알아보려면 [보안 에이전트 비교](how-to-deploy-agent.md#understand-security-agent-options) 를 참조 하세요.

## <a name="supported-platforms"></a>지원 플랫폼

다음 목록에는 현재 지원 되는 모든 플랫폼이 포함 되어 있습니다.

|IoT 에이전트에 대한 Azure Security Center |운영 체제 |아키텍처 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64, ARMv7|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, 빌드 17763    |x64|
|

## <a name="next-steps"></a>다음 단계

구성 옵션에 대해 자세히 알아보려면 에이전트 구성 방법 가이드를 참조 하세요. 
> [!div class="nextstepaction"]
> [에이전트 구성 방법 가이드](./how-to-agent-configuration.md)
