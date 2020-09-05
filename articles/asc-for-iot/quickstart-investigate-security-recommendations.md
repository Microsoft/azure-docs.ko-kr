---
title: '빠른 시작: 보안 권장 사항 조사'
description: IoT 보안 서비스용 Azure Security Center를 통해 보안 권장 사항을 조사합니다.
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
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88144047"
---
# <a name="quickstart-investigate-security-recommendations"></a>빠른 시작: 보안 권장 사항 조사


IoT용 Azure Security Center에서 권장 사항을 적시에 분석하고 완화하는 것은 IoT 솔루션 전체에서 보안 상태를 개선하고 공격 노출 영역을 줄이는 가장 좋은 방법입니다.

이 빠른 시작에서는 각 IoT 보안 권장 사항에서 사용할 수 있는 정보를 살펴보고, 위험을 줄이기 위해 각 권장 사항 및 관련 디바이스의 세부 정보를 드릴다운하고 사용하는 방법을 설명합니다.

이제 시작하겠습니다.

## <a name="investigate-new-recommendations"></a>새 권장 사항 조사

IoT Hub 권장 사항 목록에는 IoT Hub에 대해 집계된 모든 보안 권장 사항이 표시됩니다.

1.  Azure Portal에서 새 권장 사항을 조사하려는  **IoT Hub** 를 엽니다.

1.   **보안**  메뉴에서  **권장 사항**을 선택합니다. IoT Hub에 대한 모든 보안 권장 사항이 표시되고  **새** 플래그가 포함된 권장 사항이 지난 24시간 동안의 권장 사항을 표시합니다. 

    [ ![IoT용 ASC로 보안 권장 사항 조사](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  목록에서 권장 사항을 선택하고 열어 권장 사항 세부 정보를 열고 경고 세부 정보로 드릴다운합니다.

## <a name="security-recommendation-details"></a>보안 권장 사항 세부 정보

각 집계된 권장 사항을 열어 권장 사항을 트리거한 각 디바이스에 대한 자세한 권장 사항 설명, 수정 단계, 디바이스 ID를 표시합니다. 또한 Log Analytics를 사용하여 권장 사항 심각도 및 직접 조사 액세스를 표시합니다.

1.   **IoT Hub** \> **보안** \> **권장 사항** 목록에서 보안 권장 사항을 선택하여 엽니다.

1.  집계 기간에 이 권장 사항을 발행한 모든 디바이스의 권장 사항 **설명**,  **심각도**,  **디바이스 세부 정보** 를 검토합니다. 

1.  권장 사항 세부 정보를 검토한 후  **수동 수정 단계**  지침을 사용하여 권장 사항을 발생시킨 문제를 수정 및 해결합니다. 

    [ ![IoT용 ASC로 보안 권장 사항 수정](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  드릴다운 페이지에서 원하는 디바이스를 선택하여 특정 디바이스에 대한 권장 사항 세부 정보를 살펴봅니다.

    [ ![IoT용 ASC를 사용하여 디바이스에 대한 특정 보안 권장 사항 조사](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  추가 조사가 필요한 경우 링크를 사용하여  **Log Analytics의 권장 사항을 조사** 합니다. 


## <a name="next-steps"></a>다음 단계

사용자 지정 경고를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md)
