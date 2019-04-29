---
title: IoT 보안 모듈에 대 한 Azure Security Center 미리 보기를 쌍 이해 | Microsoft Docs
description: 보안 모듈 쌍 및 IoT에 대 한 Azure Security Center에서 사용 하는 방법의 개념에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c5814b188c73ea03094d7dae565e40ca09e705c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358817"
---
# <a name="security-module"></a>보안 모듈

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT 용 Azure 보안 센터 (ASC)에서 장치 쌍 및 모듈을 사용 하는 방법을 설명 합니다. 

## <a name="device-twins"></a>디바이스 쌍

Azure에 구축된 IoT 솔루션의 경우 디바이스 쌍은 디바이스 관리와 프로세스 자동화 모두에서 중요한 역할을 수행합니다.  

IoT용 ASC는 기존 IoT 디바이스 관리 플랫폼과 완벽하게 통합되어 디바이스 보안 상태를 관리하고 기존 디바이스 제어 기능을 활용할 수 있도록 지원합니다. IoT Hub를 사용 하 여 통합을 구현 하는 메커니즘을 쌍입니다.  

개념에 자세히 알아보려면 [장치 쌍](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) Azure IoT Hub에서. 

## <a name="security-module-twins"></a>보안 모듈 쌍

ASC IoT에 대 한 서비스에서 각 장치에 대 한 보안 모듈 쌍을 유지 관리합니다.
보안 모듈 쌍 솔루션의 각 특정 장치에 대 한 장치 보안에 관련 된 모든 정보를 보유 합니다.
장치 보안 속성 보다 안전한 통신 및 업데이트 하 고 더 적은 리소스가 필요한 유지 관리를 사용 하도록 설정 하는 것에 대 한 전용된 보안 모듈 쌍에 유지 됩니다.  

참조 [만들기 보안 모듈 쌍](quickstart-create-security-twin.md) 하 고 [보안 에이전트 구성](how-to-agent-configuration.md) 만드는 방법에 알아보려면 사용자 지정 및 쌍을 구성 합니다. 참조 [이해 모듈 트윈스](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) IoT Hub의 모듈 쌍의 개념에 자세히 알아보려면 합니다. 
 

## <a name="see-also"></a>참고 항목
- [ASC IoT 미리 보기](overview.md)
- [보안 에이전트 배포](how-to-deploy-agent.md)
- [보안 에이전트 인증 방법](concept-security-agent-authentication-methods.md)