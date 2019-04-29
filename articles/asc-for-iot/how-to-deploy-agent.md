---
title: 선택 하 고 IoT 에이전트 미리 보기에 대 한 Azure Security Center 배포 | Microsoft Docs
description: 에 대 한 방법을 선택 하 고 IoT 장치에서 IoT 보안 에이전트에 대 한 Azure Security Center 배포에 대해 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358406"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>선택 하 고 IoT 장치의 보안 에이전트 배포

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

IoT 용 azure Security Center (ASC) 모니터링 하 고 IoT 장치에서 데이터를 수집 하는 보안 에이전트에 대 한 참조 아키텍처를 제공 합니다.
참조 [보안 에이전트 참조 아키텍처](security-agent-architecture.md) 에 대해 자세히 알아보세요.

에이전트 오픈 소스 프로젝트로 개발 됩니다 되며 두 버전에서 사용할 수 있습니다. <br> [C](https://aka.ms/iot-security-github-c), 및 [ C# ](https://aka.ms/iot-security-github-cs)합니다.

이 문서에서는 다음 방법을 설명합니다. 
> [!div class="checklist"]
> * 보안 에이전트 버전 비교
> * 지원 되는 에이전트 플랫폼 검색
> * 솔루션에 대 한 올바른 에이전트 버전을 선택 합니다.

## <a name="understand-security-agent-options"></a>보안 에이전트 옵션 이해

IoT 보안 에이전트 버전에 대 한 모든 ASC은 같은 다양 한 기능을 제공 하 고 유사한 구성 옵션을 지원 합니다. 

C 기반 보안 에이전트 낮은 메모리 점유율, 고 사용 가능한 리소스가 더 적게 사용 하 여 장치는 이상적인 방법입니다. 

|     | C 기반 보안 에이전트 | C#-보안 에이전트 기반 |
| --- | ----------- | --------- |
| 공개 소스 | 사용할 수 있습니다 [MIT 라이선스](https://en.wikipedia.org/wiki/MIT_License) 에서 [Github](https://aka.ms/iot-security-github-cs) | 사용할 수 있습니다 [MIT 라이선스](https://en.wikipedia.org/wiki/MIT_License) 에서 [Github](https://aka.ms/iot-security-github-c) |
| 개발 언어    | C | C# |
| 지원 되는 Windows 플랫폼? | 아닙니다. | 예 |
| Windows 필수 구성 요소 | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| 지원 되는 Linux 플랫폼? | 예, x64 및 x86 | 예, x64만 해당 |
| Linux 필수 구성 요소 | libunwind8 libcurl3, uuid 런타임, auditd, audispd 플러그 인 | libunwind8 libcurl3, uuid 런타임, auditd, audispd 플러그 인, sudo, netstat, iptables |
| 디스크 공간 | 10.5MB | 90MB |
| (평균)의 메모리 공간 | 5.5MB | 33MB |
| [인증](concept-security-agent-authentication-methods.md) IoT Hub에 | 예 | 예 |
| 보안 데이터 [컬렉션](how-to-agent-configuration.md#supported-security-events) | 예 | 예 |
| 이벤트 집계 | 예 | 예 |
| 통해 원격 구성을 [보안 모듈 쌍](concept-security-module.md) | 예 | 예 |


## <a name="choose-an-agent-flavor"></a>에이전트 버전을 선택 합니다. 

올바른 에이전트를 선택 하 여 IoT 장치에 대 한 다음 질문에 대답 합니다.

- 사용 하 고 _Windows Server_ 하거나 _Windows IoT Core_? 

    [배포는 C#-Windows에 대 한 보안 에이전트 기반](how-to-deploy-windows-cs.md)합니다.

- 사용 하 고 Linux 배포를 x86을 사용 하 여 아키텍처? 

    [Linux 용 C 기반 에이전트 배포](how-to-deploy-linux-c.md)합니다.

- 사용 하 고 Linux 배포를 x64 아키텍처?

    에이전트 버전 중 하나를 사용할 수 있습니다. <br>
    [Linux 용 C 기반 에이전트 배포](how-to-deploy-linux-c.md) 및/또는 [배포를 C#-Linux에 대 한 보안 에이전트 기반](how-to-deploy-linux-cs.md)합니다.

에이전트 버전 모두 동일한 기능 집합을 제공 하 고 유사한 구성 옵션을 지원 합니다.
참조 [보안 에이전트 비교](how-to-deploy-agent.md#understand-security-agent-options) 에 대해 자세히 알아보세요.

## <a name="supported-platforms"></a>지원되는 플랫폼

다음은 현재 지원 되는 모든 플랫폼을 포함합니다.

|ASC IoT 에이전트에 대 한 |운영 체제 |아키텍처 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core 빌드 17763 |x64|

## <a name="next-steps"></a>다음 단계

구성 옵션에 대 한 자세한 내용은 에이전트 구성에 대 한 방법 가이드를 계속 합니다. 
> [!div class="nextstepaction"]
> [에이전트 구성 안내 하는 방법](./how-to-agent-configuration.md)
