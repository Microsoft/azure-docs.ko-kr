---
title: IoT 보안 에이전트 아키텍처에 대한 Azure Security Center 이해 | Microsoft Docs
description: IoT 서비스의 Azure Security Center에 사용 되는 에이전트에 대한 보안 에이전트 아키텍처를 이해 합니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596916"
---
# <a name="security-agent-reference-architecture"></a>보안 에이전트 참조 아키텍처

IoT 용 Azure Security Center는 IoT Hub를 통해 보안 데이터를 기록, 처리, 집계 및 전송 하는 보안 에이전트에 대한 참조 아키텍처를 제공 합니다.

보안 에이전트는 제한 된 IoT 환경에서 작동 하도록 설계 되었으며, 사용 하는 리소스와 비교할 때 제공 하는 값을 기준으로 사용자 지정이 매우 높습니다.

보안 에이전트는 다음과 같은 기능을 지원 합니다.

- 기본 운영 체제 (Linux, Windows)에서 원시 보안 이벤트를 수집 합니다. 사용 가능한 보안 데이터 수집기에 대한 자세한 내용은 [IoT 에이전트 구성 Azure Security Center](how-to-agent-configuration.md)를 참조 하세요.

- IoT Hub를 통해 전송 되는 메시지에 원시 보안 이벤트를 집계 합니다.

- 기존 장치 id 또는 전용 모듈 id를 사용 하 여 인증 합니다. 자세히 알아보려면 [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 을 참조 하세요.

- **Azureiotsecurity** 모듈 쌍을 사용 하 여 원격으로 구성 합니다. 자세히 알아보려면 [IoT 에이전트에 대한 Azure Security Center 구성](how-to-agent-configuration.md)을 참조 하세요.

IoT 보안 에이전트에 대한 Azure Security Center는 오픈 소스 프로젝트로 개발 되며 GitHub에서 사용할 수 있습니다. 

- [IoT C 기반 에이전트에 대한 Azure Security Center](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [IoT C#기반 에이전트에 대한 Azure Security Center](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>에이전트 지원 플랫폼

IoT에 대한 Azure Security Center는 32 비트 및 64 비트 Windows 용 설치 관리자 에이전트를 제공 하 고, 32 비트 및 64 비트 Linux의 경우 동일 합니다. 다음 표에 따라 각 장치에 대한 올바른 에이전트 설치 관리자가 있는지 확인 합니다.

| 아키텍처 | Linux | Windows |    세부 정보|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 비트  | C  | C#  ||
| 64 비트  | C#또는 C           | C#      | 더 제한 된 장치나 최소 장치 리소스를 사용 하는 장치에는 C 에이전트를 사용 하는 것이 좋습니다.|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 보안 에이전트 아키텍처와 사용 가능한 설치 관리자에 대한 Azure Security Center에 대해 알아보았습니다.

IoT 배포에 대한 Azure Security Center 시작 하려면 다음 문서를 사용 하세요.

- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md) 이해
- [보안 에이전트](how-to-deploy-agent.md) 선택 및 배포
- IoT [서비스 필수 구성 요소](service-prerequisites.md) 에 대한 Azure Security Center 검토
- [IoT Hub에서 IoT 서비스에 Azure Security Center를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법을 알아봅니다.
- [IoT에 대한 AZURE SECURITY CENTER FAQ](resources-frequently-asked-questions.md) 에서 서비스에 대해 자세히 알아보세요.
