---
title: IoT 비용에 대한 Azure Security Center 이해 | Microsoft Docs
description: IoT에 대한 Azure Security Center와 관련 된 비용 및 제어 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348514"
---
# <a name="pricing-and-associated-costs"></a>가격 책정 및 관련 비용

이 문서에서는 IoT 가격 책정 모델에 대한 Azure Security Center 설명 하 고 관련 된 모든 비용을 요약 하 고 관리 하는 방법을 설명 합니다.

## <a name="pricing"></a>가격 책정

IoT 가격 책정 모델에 대한 Azure Security Center는 두 부분으로 구성 되며, IoT 용 Azure Security Center에서 IoT Hub [사용 하도록 설정](quickstart-onboard-iot-hub.md) 되 면 요금이 청구 됩니다.

- 장치-IoT Hub 로그 분석을 기반으로 하는 장치 기본 제공 보안 기능입니다.

- IoT Edge 또는 리프 장치의 보안 메시지를 기반으로 하는 메시지 강화 보안 기능을 사용 하 여 비용을 절감 합니다.


자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조 하세요.

## <a name="associated-costs"></a>관련 비용

IoT에 대한 Azure Security Center에는 직접 가격 책정의 일부가 아닌 연결 된 비용이 있습니다.


- 저장소 비용 Log Analytics

특정 솔루션 기능을 옵트아웃 하 여 관련 비용을 줄일 수 있습니다. 설정을 변경 하 여 옵트아웃 (Opt out) 합니다.

설정을 변경 하려면:

1. IoT Hub를 엽니다.

2. **보안**에서 **개요**를 클릭 합니다.

3. **설정**을 클릭합니다.

다음 표에서는 각 옵션의 관련 비용 및 의미에 대한 요약을 제공 합니다.

|     | 사용법 | 설명 |
| --- | --- | --- |
| **Log Analytics 저장소** |  |
| 장치 권장 사항 및 경고| 서비스에서 생성 하는 보안 권장 사항 및 경고 | 선택 사항 아님 |
| 원시 보안 데이터| IoT 장치에서 보안 에이전트로 수집 된 원시 보안 데이터 | _원시 장치 보안 이벤트 저장_ 사용 안 함 |
|

>[!Important]
> 옵트아웃은 IoT 보안 기능 가용성에 대한 Azure Security Center에 심각한 영향을 미칩니다. 
  
| 옵트아웃 | 관련성 |
| --- | --- |
| _쌍 메타 데이터 컬렉션_ | [사용자 지정 경고](quickstart-create-custom-alerts.md) 사용 안 함 |
| | IoT Edge 매니페스트 권장 구성 사용 안 함 |
| | 장치 id 기반 권장 사항 및 경고 사용 안 함 |
| _원시 장치 보안 이벤트 저장_ | 장치 OS 기준 권장 사항에 대한 세부 정보를 사용할 수 없음 |
| | [경고](concept-security-alerts.md) 및 [권장 사항](concept-recommendations.md) 조사에 대한 세부 정보를 사용할 수 없음 |
|


## <a name="see-also"></a>참조

- [원시 보안 데이터](how-to-security-data-access.md) 액세스
- [장치 조사](how-to-investigate-device.md)
- [보안 권장 사항](concept-recommendations.md) 이해 및 탐색
- [보안 경고](concept-security-alerts.md) 이해 및 탐색
