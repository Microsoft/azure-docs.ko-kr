---
title: IoT Edge 용 IoT 보안 모듈에 대한 Azure Security Center 이해 Microsoft Docs
description: IoT Edge 용 IoT 보안 모듈 Azure Security Center의 아키텍처 및 기능을 이해 합니다.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315901"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge 보안 모듈

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) 는에 지에서 비즈니스 워크플로를 관리 하 고 수행 하기 위한 강력한 기능을 제공 합니다.
IoT 환경에서 IoT Edge를 재생 하는 주요 부분은 악의적인 행위자에 게 특히 유용 합니다.

IoT 보안 모듈에 대한 Azure Security Center IoT Edge 장치에 대한 포괄적인 보안 솔루션을 제공 합니다.
IoT 모듈 Azure Security Center는 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 수집 하 고 집계 하 고 분석 하 여 실행 가능한 보안 권장 사항 및 경고로 변환 합니다.

Iot 장치에 대한 IoT 보안 에이전트의 Azure Security Center와 마찬가지로 모듈 쌍을 통해 IoT Edge 모듈의 Azure Security Center를 사용자 지정할 수 있습니다.
자세히 알아보려면 [에이전트 구성](how-to-agent-configuration.md) 을 참조 하세요.

IoT Edge 용 IoT 보안 모듈에 대한 Azure Security Center는 다음과 같은 기능을 제공 합니다.

- 기본 운영 체제 (Linux) 및 IoT Edge 컨테이너 시스템에서 원시 보안 이벤트를 수집 합니다.
  
  사용 가능한 보안 데이터 수집기에 대한 자세한 내용은 [IoT 에이전트 구성 Azure Security Center](how-to-agent-configuration.md) 를 참조 하세요.

- IoT Edge 배포 매니페스트를 분석 합니다.

- [IoT Edge 허브](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)를 통해 전송 되는 메시지에 원시 보안 이벤트를 집계 합니다.

- 보안 모듈 쌍을 사용 하 여 구성을 제거 합니다.

  자세히 알아보려면 [IoT 에이전트에 대한 Azure Security Center 구성](how-to-agent-configuration.md) 을 참조 하세요.

IoT Edge 용 IoT 보안 모듈에 대한 Azure Security Center IoT Edge의 특권 모드에서 실행 됩니다.
모듈에서 운영 체제 및 기타 IoT Edge 모듈을 모니터링할 수 있도록 하려면 특권 모드가 필요 합니다.

## <a name="module-supported-platforms"></a>모듈 지원 플랫폼

IoT Edge 용 IoT 보안 모듈에 대한 Azure Security Center는 현재 Linux 에서만 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Edge 용 IoT 보안 모듈 Azure Security Center의 아키텍처 및 기능에 대해 알아보았습니다.

IoT 배포에 대한 Azure Security Center 시작 하려면 다음 문서를 사용 하세요.

- [IoT Edge에 대한 보안 모듈](how-to-deploy-edge.md) 배포
- [보안 모듈을 구성](how-to-agent-configuration.md) 하는 방법 알아보기
- IoT [서비스 필수 구성 요소](service-prerequisites.md) 에 대한 Azure Security Center 검토
- [IoT Hub에서 IoT 서비스에 Azure Security Center를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법을 알아봅니다.
- [IoT에 대한 AZURE SECURITY CENTER FAQ](resources-frequently-asked-questions.md) 에서 서비스에 대해 자세히 알아보세요.