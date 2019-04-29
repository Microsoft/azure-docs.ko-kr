---
title: IoT 솔루션 아키텍처 미리 보기에 대 한 Azure Security Center 이해 | Microsoft Docs
description: IoT 서비스에 대 한 Azure Security Center에서 정보의 흐름에 알아봅니다.
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
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: a0eb459391da65f8d0e2ae251809805924d07ad1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360386"
---
# <a name="azure-security-center-for-iot-architecture"></a>IoT 아키텍처에 대 한 azure Security Center

이 문서에서는 IoT 솔루션에 대 한 Azure 보안 센터 (ASC)의 기능 시스템 아키텍처를 설명 합니다. 

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="asc-for-iot-components"></a>ASC IoT 구성 요소

ASC IoT에 대 한 다음 구성 요소로 구성 됩니다.
- 장치 에이전트
- SDK의 보안 메시지 보내기
- IoT Hub 통합
- 분석 파이프라인
 
### <a name="asc-for-iot-workflow"></a>ASC IoT 워크플로에 대 한

IoT 장치 에이전트에 대 한 ASC를 사용 하면 장치에서 원시 보안 이벤트를 쉽게 수집할 수 있습니다. 원시 보안 이벤트 IP 연결, 프로세스 생성, 사용자 로그인 및 기타 보안 관련 정보를 포함할 수 있습니다. 또한 ASC IoT 장치 에이전트에 대 한 높은 네트워크 처리량을 방지 하려면 이벤트 집계를 처리 합니다. 에이전트는 사용할 수 있도록 하는 가장 빠른 SLA만 중요 한 정보를 보내는 등의 특정 작업 또는 더 큰 세그먼트를 광범위 한 보안 정보 및 컨텍스트를 집계 하는 것에 대 한 더 높은 서비스 비용을 방지 하는 고도로 사용자 지정 합니다.
 
장치 에이전트 및 기타 응용 프로그램 사용 합니다 **ASC Azure SDK의 보안 메시지를 보낼** Azure IoT Hub에 보안 정보를 보내도록 합니다. IoT Hub이이 정보를 선택 하 고 IoT 서비스에 대 한 ASC로 전달 합니다.

전달 된 데이터 외에도 IoT 서비스에 대 한 ASC 활성화 되 면 IoT Hub에 게 전송 ASC에서 분석에 대 한 내부 데이터의 모든 IoT에 대 한 합니다. 이 데이터는 장치-클라우드 작업 로그, 장치 id 및 허브 구성을 포함 합니다. 이 정보는 모두 IoT 분석 파이프라인에 대 한 ASC를 만들 수 있습니다.
 
ASC IoT 분석 파이프라인에 대 한 Microsoft 및 Microsoft 내의 다양 한 소스에서 추가 위협 인텔리전스 스트림을 수신 파트너입니다. IoT 전체 분석 파이프라인에 대 한 ASC (예: 사용자 지정 경고 및 메시지를 보내는 보안 SDK 사용) 서비스에 대 한 모든 고객 구성을 사용 하 여 작동 합니다.
 
ASC IoT에 대 한 모든 실행 가능한 권장 사항 및 경고를 생성 하는 정보 스트림 결합 분석 파이프라인을 사용 하 합니다. 파이프라인 보안 연구원 및 전문가 뿐만 아니라 기계 학습 표준 장치 동작 및 위험 분석에서 편차에 대 한 검색 모델을 만들어 두 사용자 지정 규칙을 포함 합니다.
 
ASC IoT 권장 사항 및 경고 (분석 출력을 파이프라인)에 대 한 각 고객의 Log Analytics 작업 영역에 기록 됩니다. 원시 이벤트를 포함 하 여 작업 영역 뿐만 아니라 경고 및 권장 사항 심층 분석 조사 및 검색 된 의심 스러운 활동의 정확한 세부 정보를 사용 하 여 쿼리를 사용 합니다.  

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 아키텍처와 ASC의 IoT 솔루션에 대 한 워크플로 대해 알아보았습니다. 필수 구성 요소에 자세히 알아보려면 시작 하 고 IoT Hub에서 보안 솔루션을 사용 하도록 설정, 다음 문서를 참조 하는 방법.

- [서비스 필수 구성 요소](service-prerequisites.md)
- [시작](getting-started.md)
- [솔루션 구성](quickstart-configure-your-solution.md)
- [IoT Hub의 보안을 사용 하도록 설정](quickstart-onboard-iot-hub.md)
- [ASC for IoT FAQ](resources-frequently-asked-questions.md)
- [IoT 보안 경고에 대 한 ASC](concept-security-alerts.md)

