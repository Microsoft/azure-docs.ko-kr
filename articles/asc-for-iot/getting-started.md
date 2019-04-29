---
title: Azure 보안 센터 (ASC)를 사용 하 여 IoT 미리 보기에 대 한 시작 | Microsoft Docs
description: 시작 IoT 기능 및 서비스에 대 한 Azure Security Center의 기본 워크플로 이해 합니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: aac15d766439a725f593ca421cbdc6da496f29f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358560"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>IoT 용 Azure Security Center 시작 

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서는 IoT 서비스에 대 한 설명은 Azure 보안 센터 (ASC)의 다른 구성 요소를 제공 하 고 시작 하는 방법에 설명 [서비스를 사용 하도록 설정 하면](quickstart-onboard-iot-hub.md)합니다. 

IoT에 대 한 ASC 보안 분석 IoT hub 구성, 장치 id 및 허브-장치 통신 패턴을 제공 하 여 IoT Hub에 원활 하 게 통합할 수 있습니다.
ASC IoT에 대 한 향상 된 보안 기능에 대 한 에이전트 기반 컬렉션 IoT 장치에서 보안 데이터를 제공합니다.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>IoT Hub에서 IoT 매끄러운 통합 ASC

개별 IoT 장치를 보호 하는 동안, 네트워크 또는 장치에서 직접 데이터를 수집할 수 있습니다 때 해야 합니다. 이러한 노력을 지원 하려면 IoT 용 ASC 사용량이 보안 장치 모니터링 및 보안 강화를 제공 하는 에이전트 라는 제공 합니다.

둘 다에서 Linux 및 Windows 보안 에이전트에 대 한 참조 아키텍처 IoT 미리 보기에 대 한 asc에서 C# C 제공 됩니다.
에이전트는 이벤트 집계 비용을 줄이고 장치 모듈 쌍을 통해 구성 하는 장치 운영 체제에서 원시 이벤트 컬렉션을 처리 합니다.
보안 메시지는 IoT 분석 서비스에 대 한로 IoT Hub를 통해 전송 됩니다.

## <a name="asc-for-iot-basics"></a>ASC IoT 기본 사항에 대 한

IoT 장치 및 환경 요구 사항을 만족 하는 워크플로 시나리오를 선택 합니다.

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>ASC를 사용 하 여 IoT Hub에서 IoT 매끄러운 통합 시작 

>[!Note]
>이 워크플로 사용 하면 IoT 보안 에이전트에 대 한 ASC를 사용 하지 않고 서비스를 사용할 수 있습니다. 

장치 모니터링을 사용 하도록 id 관리, 장치, 클라우드 및 클라우드-장치 통신 패턴을 사용 하 여 서비스를 시작 하 고 테스트에 대 한 기본 워크플로 따르는: 

1. [IoT Hub에서 IoT 서비스에 대 한 ASC를 사용 하도록 설정](quickstart-onboard-iot-hub.md)
1. IoT Hub에 등록 된 장치 없음 [새 장치를 등록](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)합니다.
1. [장치의 azureiotsecurity 보안 모듈 만들기](quickstart-create-security-twin.md) 장치에 대 한 합니다. 
1. 일반적인 장치 및 시스템 동작을 통해 정의할 [사용자 지정 경고](quickstart-create-custom-alerts.md)합니다. 
1. 시스템 서비스 및 장치 상태를 확인 하려면 테스트를 수행 합니다. 
1. 탐색 [경고](concept-security-alerts.md)를 [권장 사항](concept-recommendations.md), 및 [Log Analytics를 사용 하 여 심층 분석](how-to-security-data-access.md) IoT Hub를 사용 하 여 합니다. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>IoT 보안 에이전트에 대 한 ASC를 사용 하 여 시작

ASC의 테스트 하 고 서비스를 사용 하도록 설정 하려면 다음과 같은 기본 워크플로 사용 하 여 원격 연결, 활성 응용 프로그램, 로그인 이벤트 및 OS 구성에 대 한 유용한 정보를 모니터링 하는 등 IoT 향상 된 보안 기능을 사용 하 여 확인 합니다. 

1. [IoT Hub에 IoT 서비스에 대 한 ASC를 사용 하도록 설정](quickstart-onboard-iot-hub.md)
1. IoT Hub에 등록 된 장치 없음 [새 장치를 등록](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)합니다.
1. [Azureiotsecurity 보안 모듈 만들기](quickstart-create-security-twin.md) 장치에 대 한 합니다.
1. 실제 장치에서 설치 하는 대신 Azure 시뮬레이션 된 장치에 에이전트를 설치 하려면 [스핀을 새 Azure 가상 머신 (VM)를](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 사용 가능한 영역에 있습니다. 
1. [IoT 보안 에이전트는 ASC를 배포](how-to-deploy-linux-cs.md) IoT 장치 또는 새 VM에서.
1. 지침을 따르세요 [trigger_events](https://aka.ms/iot-security-github-trigger-events) 무해 한 공격 시뮬레이션을 실행 합니다.
1. 이전 단계에서 시뮬레이션 된 공격에 대 한 응답으로 IoT 경고에 대 한 ASC를 확인 합니다. 스크립트를 실행 한 후 5 분 확인을 시작 합니다.
1. 탐색 [경고](concept-security-alerts.md)를 [권장 사항](concept-recommendations.md), 및 [Log Analytics를 사용 하 여 심층 분석](how-to-security-data-access.md) IoT Hub를 사용 하 여 합니다. 

## <a name="next-steps"></a>다음 단계

- 사용 하도록 설정 [ASC IoT에 대 한](quickstart-onboard-iot-hub.md)
- 구성 프로그램 [솔루션](quickstart-configure-your-solution.md)
- [보안 모듈 만들기](quickstart-create-security-twin.md)
- 구성 [사용자 지정 경고](quickstart-create-custom-alerts.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
