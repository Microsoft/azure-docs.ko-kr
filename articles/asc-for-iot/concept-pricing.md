---
title: IoT 용 Azure Security Center 미리 보기 비용 이해 | Microsoft Docs
description: IoT 및 제어 하는 방법에 대 한 Azure Security Center와 사용 하 여 관련 비용에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 41b2d012ef2f6dd1ca5f57e04da43eb1a06dafde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360152"
---
# <a name="pricing-and-associated-costs"></a>가격 책정 및 관련 비용

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT 가격 책정 모델에 대 한 Azure 보안 센터 (ASC)에 대해 설명, 모든 관련 된 비용 요약 및 관리 하는 방법에 설명 합니다.

## <a name="pricing"></a>가격

두 가지 가격 책정 모델은 구성 된 IoT 용 ASC 부분으로 및 IoT Hub는 한 요금이 청구 됩니다 [활성화](quickstart-onboard-iot-hub.md) IoT 용 ASC에서:

- 장치가 IoT Hub 로그 분석을 기반으로 하는 기본 제공 보안 기능으로 비용.

- 메시지-향상 된 보안 기능을 IoT Edge 리프 장치에서 보안 메시지를 기반으로 하 여 비용입니다.

  >[!Note]
  > 보안 메시지에 IoT Hub 할당량 사용도 발생 합니다.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/en-us/pricing/details/security-center/)합니다.

## <a name="associated-costs"></a>관련 된 비용

IoT 용 ASC에 두 가지 유형의 관련된 비용을 직접 가격의 일부가 아닙니다.

- IoT Hub 할당량 사용

- 로그 분석 저장소 비용

설정을 변경 하 여 특정 기능을 옵트아웃 하 여 관련 된 비용을 줄일 수 있습니다.

사용자 설정을 변경 하려면:

1. IoT Hub를 엽니다.

2. 아래 **Security**, 클릭 **개요**합니다.

3. **설정**을 클릭합니다.

다음 표에서 각 옵션의 의미 및 관련 된 비용의 요약을 제공합니다.

|     | 사용 현황 | 주석 |
| --- | --- | --- |
| **IoT Hub 할당량 사용** |  |
| [장치를 내보내기](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) 작업 (쌍 내보내기) | 하루 한 번 | 사용 하지 않도록 설정 _쌍 메타 데이터 컬렉션_ |
| **Log Analytics 저장소** |  |
| 장치 권장 사항 및 경고| 보안 권장 사항 및 서비스에 의해 생성 된 경고 | 필수 |
| 원시 보안 데이터| 보안 에이전트에 의해 수집 된 IoT 장치에서 원시 보안 데이터 | 사용 하지 않도록 설정 _원시 장치 보안 이벤트를 저장 합니다._ |

>[!Important]
> 옵트아웃에 사용 가능한 보안 기능에 심각한 영향을 줍니다.
  
| 옵트아웃 | 영향 |
| --- | --- |
| _쌍 메타 데이터 컬렉션_ | 사용 하지 않도록 설정 [사용자 지정 경고](quickstart-create-custom-alerts.md) |
| | IoT Edge 매니페스트 권장 사항을 사용 하지 않도록 설정 |
| | 장치 id를 기반으로 권장 사항 및 경고를 사용 하지 않도록 설정 |
| _원시 장치 보안 이벤트를 저장 합니다._ | 장치 OS 기준 권장 사항에 대 한 세부 정보를 사용할 수 없는 경우 |
| | 세부 정보 [경고](concept-security-alerts.md) 하 고 [권장 사항을](concept-recommendations.md) 조사를 사용할 수 없는 경우 |


## <a name="see-also"></a>참고 항목

- 액세스에 [원시 보안 데이터](how-to-security-data-access.md)
- [장치를 조사 합니다.](how-to-investigate-device.md)
- 이해 및 탐색 [보안 권장 사항](concept-recommendations.md)
- 이해 및 탐색 [보안 경고](concept-security-alerts.md)
