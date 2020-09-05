---
title: '빠른 시작: 보안 경고 조사'
description: IoT 디바이스에서 IoT용 Azure Security Center 보안 경고를 이해하고 드릴다운하고 조사합니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: a42b8f64c59126f08f09cd7c167e7aa133040a0e
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068421"
---
# <a name="quickstart-investigate-security-alerts"></a>빠른 시작: 보안 경고 조사

IoT 솔루션에 대한 규정 준수 및 보호를 보장하는 가장 좋은 방법은 IoT용 Azure Security Center에서 발급한 경고의 예약된 조사 및 수정입니다.

이 빠른 시작에서는 각 IoT 보안 경고에서 사용할 수 있는 정보를 살펴보고 각 경고 및 관련 디바이스의 세부 정보를 드릴다운하고 사용하여 응답하고 수정하는 방법을 설명합니다. 

이제 시작하겠습니다. 


## <a name="investigate-new-security-alerts"></a>새 보안 경고 조사

IoT Hub 보안 경고 목록에는 IoT Hub에 대한 모든 집계된 보안 경고가 표시됩니다. 

1. Azure Portal에서 새 경고를 조사하려는 **IoT Hub**를 엽니다.
1. **보안** 메뉴에서 **경고**를 선택합니다. IoT Hub에 대한 모든 보안 경고가 표시되고 **새** 플래그가 포함된 경고는 지난 24시간 동안의 경고를 표시합니다.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="새 경고 플래그를 사용하여 새 IoT 보안 경고 조사":::
1. 목록에서 경고를 선택하고 열어 경고 세부 정보를 열고 경고 세부 정보로 드릴다운합니다. 

## <a name="security-alert-details"></a>보안 경고 세부 정보

각 집계된 경고를 열면 자세한 경고 설명, 수정 단계, 경고를 트리거한 각 디바이스에 대한 디바이스 ID 뿐만 아니라 Log Analytics를 사용하여 경고 심각도 및 직접 조사 액세스도 표시됩니다. 

1. **IoT Hub** > **보안** > **경고** 목록에서 보안 경고를 선택하고 엽니다. 
1. 집계 기간에 이 경고를 발행한 모든 디바이스의 경고 **설명**, **심각도**, **감지의 원본**, **디바이스 세부 정보**를 검토합니다.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="집계된 경고에서 각 디바이스에 대한 세부 정보를 드릴다운하고 검토합니다."::: 
1. 경고 세부 정보를 검토한 후 **수동 수정 단계** 지침을 사용하여 경고를 발생시킨 문제를 수정 및/또는 해결합니다. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="수동 수정 단계를 수행하여 디바이스 보안 경고를 해결하거나 수정합니다.":::

1. 추가 조사가 필요한 경우 링크를 사용하여 **Log Analytics의 경고를 조사**합니다. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="경고를 자세히 조사하려면 화면에 제공된 로그 분석 링크를 사용하여 조사를 사용합니다.":::

## <a name="next-steps"></a>다음 단계

Azure Security Center 경고 유형 및 가능한 사용자 지정에 대한 자세한 내용을 보려면 다음 문서로 이동하세요.

> [!div class="nextstepaction"]
> [IoT 보안 경고 이해](concept-security-alerts.md)
