---
title: IoT 필수 조건에 대한 Azure Security Center | Microsoft Docs
description: IoT 서비스 필수 구성 요소에 대한 Azure Security Center를 시작 하는 데 필요한 모든 정보를 제공 합니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299468"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>IoT 필수 조건에 대한 Azure Security Center

이 문서에서는 IoT 서비스에 대한 Azure Security Center의 다양 한 구성 요소에 대해 설명 하 고, 시작 해야 하는 작업을 설명 하 고, 서비스를 이해 하는 데 도움이 되는 기본 개념을 설명 합니다. 

## <a name="minimum-requirements"></a>최소 요구 사항

- IoT Hub 표준 계층
    - RBAC 역할 **소유자** 수준 권한 
- [Log Analytics 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (권장)
    - Azure Security Center를 사용 하는 것은 요구 사항이 아니라 권장 사항입니다. Azure Security Center 하지 않으면 IoT Hub 내에서 다른 Azure 리소스를 볼 수 없습니다. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>IoT 서비스용 Azure Security Center 작업

IoT insights 및 보고에 대한 Azure Security Center Azure IoT Hub 및 Azure Security Center를 통해 사용할 수 있습니다. Azure IoT Hub에서 IoT에 Azure Security Center을 사용 하도록 설정 하려면 **소유자** 수준의 권한이 있는 계정이 필요 합니다. IoT Hub에서 iot for IoT를 사용 하도록 설정 하 고 나면 IoT insights에 대한 Azure Security Center Azure IoT Hub의 **보안** 기능과 Azure Security Center의 **iot** 로 표시 됩니다. 

## <a name="supported-service-regions"></a>지원되는 서비스 지역 

IoT에 대한 Azure Security Center는 현재 다음 Azure 지역에서 IoT Hub에 대해 지원 됩니다.
  - 미국 중부  
  - East US 
  - 미국 동부 2
  - 미국 중서부
  - 미국 서부
  - 미국 서부2
  - 미국 중남부
  - 미국 중북부
  - 캐나다 중부
  - 캐나다 동부 
  - 유럽 북부    
  - 브라질 남부
  - 프랑스 중부  
  - 영국 서부 
  - 영국 남부
  - 유럽 서부 
  - 북유럽 
  - 일본 서부  
  - 일본 동부  
  - 오스트레일리아 남동부
  - 오스트레일리아 동부
  - 아시아 동부   
  - 동남아시아
  - 한국 중부
  - 한국 남부 
  - 인도 중부
  - 인도 남부

IoT에 대한 Azure Security Center 모든 유럽 지역의 모든 트래픽을 유럽 서부 지역 데이터 센터 및 모든 나머지 지역에서 미국 중부 지역 데이터 센터로 라우팅합니다.  
  
## <a name="wheres-my-iot-hub"></a>내 IoT Hub 어디에 있나요?

시작 하기 전에 IoT Hub 위치를 확인 하 여 서비스 가용성을 확인 하세요. 

1. IoT Hub를 엽니다. 
2. **개요**를 클릭합니다. 
3. 나열 된 위치가 [지원 되는 서비스 영역](#supported-service-regions)중 하 나와 일치 하는지 확인 합니다. 


## <a name="supported-platforms-for-agents"></a>에이전트에 대해 지원 되는 플랫폼 

IoT 에이전트에 대한 Azure Security Center는 증가 하는 장치 및 플랫폼 목록을 지원 합니다. 기존 또는 계획 된 장치 라이브러리를 확인 하려면 [지원 되는 플랫폼 목록](how-to-deploy-agent.md) 을 참조 하세요.  

## <a name="next-steps"></a>다음 단계
- Azure IoT 보안 [개요](overview.md) 를 참조 하십시오.
- [서비스를 사용 하도록 설정](quickstart-onboard-iot-hub.md) 하는 방법 알아보기
- [IoT에 대한 AZURE SECURITY CENTER FAQ](resources-frequently-asked-questions.md) 읽기
- [IoT 경고에 대한 Azure Security Center 이해](concept-security-alerts.md) 하는 방법 살펴보기
