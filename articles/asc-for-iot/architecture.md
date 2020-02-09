---
title: IoT 솔루션 아키텍처에 대한 Azure Security Center 이해 | Microsoft Docs
description: IoT 서비스 Azure Security Center의 정보 흐름에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922172"
---
# <a name="azure-security-center-for-iot-architecture"></a>IoT용 Azure Security Center 아키텍처

이 문서에서는 IoT 솔루션에 대한 Azure Security Center의 기능 시스템 아키텍처를 설명 합니다. 

## <a name="azure-security-center-for-iot-components"></a>IoT 구성 요소에 대한 Azure Security Center

IoT에 대한 Azure Security Center은 다음 구성 요소로 구성 됩니다.
- IoT Hub 통합
- 장치 에이전트 (선택 사항)
- 보안 메시지 SDK 보내기
- 분석 파이프라인
 
### <a name="azure-security-center-for-iot-workflows"></a>IoT 워크플로에 대한 Azure Security Center

IoT에 대한 Azure Security Center는 두 가지 기능 워크플로 (기본 제공 및 고급) 중 하나로 작동 합니다.  

### <a name="built-in"></a>기본 제공
**기본 제공** 모드에서는 IoT Hub에서 **보안** 옵션을 설정 하도록 선택할 때 IoT에 대한 Azure Security Center를 사용할 수 있습니다. 실시간 모니터링, 권장 사항 및 경고를 제공 하는 기본 제공 모드는 단일 단계 장치 가시성 및 보안 불일치를 제공 합니다. 빌드 모드에서는 모든 장치에 에이전트를 설치할 필요가 없으며 기록 된 활동에 대한 고급 분석을 사용 하 여 현장 장치를 분석 하 고 보호할 수 있습니다. 

### <a name="enhanced"></a>향상된 
**고급** 모드에서는 IoT Hub의 **보안** 옵션을 설정 하 고 장치에 IoT 장치 에이전트의 Azure Security Center를 설치 하 고 나면 에이전트는 장치에서 원시 보안 이벤트를 수집, 집계 및 분석 합니다. 원시 보안 이벤트에는 IP 연결, 프로세스 만들기, 사용자 로그인 및 기타 보안 관련 정보가 포함 될 수 있습니다. IoT 장치 에이전트에 대한 Azure Security Center도 높은 네트워크 처리량을 방지 하기 위해 이벤트 집계를 처리 합니다. 에이전트는 항상 사용자 지정이 가능 하므로, 가장 빠른 SLA에서 중요 한 정보만 보내거나 광범위 한 보안 정보 및 컨텍스트를 더 큰 세그먼트로 집계 하 여 더 높은 서비스 비용을 방지 하는 등의 특정 작업에 사용할 수 있습니다.

![IoT용 Azure Security Center 아키텍처](./media/architecture/azure-iot-security-architecture.png)
 
장치 에이전트 및 기타 응용 프로그램은 **Azure send security MESSAGE SDK** 를 사용 하 여 Azure IoT Hub에 보안 정보를 보냅니다. IoT Hub이 정보를 선택 하 여 IoT 서비스용 Azure Security Center에 전달 합니다.

IoT 서비스에 대한 Azure Security Center를 사용 하도록 설정 하면 전달 된 데이터 외에도 IoT에 대해 Azure Security Center 하 여 분석을 위해 모든 내부 데이터를 전송 IoT Hub. 이 데이터에는 장치-클라우드 작업 로그, 장치 id 및 허브 구성이 포함 됩니다. 이 모든 정보를 통해 IoT 분석 파이프라인에 대한 Azure Security Center를 만들 수 있습니다.
 
IoT 분석 파이프라인에 대한 Azure Security Center는 Microsoft 및 Microsoft 파트너 내 다양 한 소스에서 추가 위협 인텔리전스 스트림도 수신 합니다. IoT 전체 분석 파이프라인에 대한 Azure Security Center는 서비스에서 만들어진 모든 고객 구성 (예: 사용자 지정 경고 및 send Security message SDK 사용)과 함께 작동 합니다.
 
IoT에 대한 Azure Security Center 분석 파이프라인을 사용 하 여 모든 정보 스트림을 결합 하 여 조치 가능한 권장 사항 및 경고를 생성 합니다. 파이프라인에는 보안 연구원 및 전문가가 만든 사용자 지정 규칙과 표준 장치 동작 및 위험 분석의 편차를 검색 하는 기계 학습 모델이 모두 포함 됩니다.
 
IoT 권장 사항 및 경고 (분석 파이프라인 출력)에 대한 Azure Security Center은 각 고객의 Log Analytics 작업 영역에 기록 됩니다. 경고 및 권장 사항 뿐만 아니라 작업 영역에 원시 이벤트를 포함 하면 검색 된 의심 스러운 활동의 정확한 세부 정보를 사용 하 여 심층 조사 및 쿼리를 수행할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 아키텍처와 IoT 솔루션에 대한 Azure Security Center 워크플로를 배웠습니다. 필수 구성 요소, 시작 및 IoT Hub에서 보안 솔루션을 사용 하도록 설정 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [서비스 필수 구성 요소](service-prerequisites.md)
- [시작](getting-started.md)
- [솔루션 구성](quickstart-configure-your-solution.md)
- [IoT Hub에서 보안 사용](quickstart-onboard-iot-hub.md)
- [IoT FAQ에 대한 Azure Security Center](resources-frequently-asked-questions.md)
- [IoT용 Azure Security Center 보안 경고](concept-security-alerts.md)
