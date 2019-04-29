---
title: Azure IoT Hub 업그레이드 | Microsoft Docs
description: 더 많은 메시징 및 디바이스 관리 기능을 확보하기 위해 IoT Hub에 대한 가격 책정 및 크기 계층을 변경합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440236"
---
# <a name="how-to-upgrade-your-iot-hub"></a>IoT Hub를 업그레이드하는 방법

IoT 솔루션 증대에 따라 Azure IoT Hub도 고객의 확장을 지원할 수 있습니다. Azure IoT Hub는 다른 기능을 사용하고자 하는 고객에게 부응하기 위해 기본(B) 및 표준(S) 등의 두 계층을 제공합니다. 각 계층 안에는 매일 보낼 수 있는 메시지의 수를 결정하는 3개 크기(1, 2, 3)가 있습니다.

더 많은 디바이스가 있고 추가적인 기능이 필요할 경우 세 가지 방법으로 요구에 맞게 IoT Hub를 조절할 수 있습니다.

* IoT Hub 안에 단위를 추가합니다. 예를 들어 B1 IoT Hub 안의 각 추가 단위는 매일 400,000건의 추가적인 메시지를 허용합니다.

* IoT Hub의 크기를 변경합니다. 예를 들어, 각 단위는 하루 지원할 수 있는 메시지의 수를 늘리려면 B2 계층 B1 계층에서 마이그레이션하십시오.

* 상위 계층으로 업그레이드합니다. 예를 들어 동일한 메시징 기능을 사용 하 여 고급 기능에 대 한 액세스를 위해 S1 계층으로 B1 계층에서 업그레이드 합니다.

이러한 변경은 모두 기존 작업 중단 없이 가능합니다.

IoT hub를 다운 그레이드 하려면 단위를 제거 하 고 IoT hub의 크기를 줄일 수 있지만 하위 계층으로 다운 그레이드할 수 없습니다. 예를 들어 S2 계층에서 S1 계층으로는 이동할 수 있지만 S2 계층에서 B1 계층으로는 이동할 수 없습니다. 한 가지 형식만 [Iot Hub 버전](https://azure.microsoft.com/pricing/details/iot-hub/) 계층 내에서 IoT Hub 당 선택할 수 있습니다. 예를 들어 S1 및 B3 또는 S1 및 S2와 같은 서로 다른 버전의 단위 혼합이 아닌 여러 단위의 S1을 사용하여 IoT Hub를 만들 수 있습니다.

이 예제는 사용자 솔루션 변경에 따라 IoT Hub를 조정하는 방법을 이해하는 데 도움이 됩니다. 각 계층의 기능에 대 한 특정 내용은 항상 참조 해야 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)합니다.

## <a name="upgrade-your-existing-iot-hub"></a>기존 IoT Hub 업그레이드

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 IoT Hub로 이동합니다.

2. **가격 책정 및 크기 조정**을 선택합니다.

   ![가격 및 크기 조정](./media/iot-hub-upgrade/pricing-scale.png)

3. 허브에 대한 계층을 변경하려면 **가격 책정 및 계층 크기 조정**을 선택합니다. 새 계층을 선택한 다음, **선택**을 클릭합니다.

   ![가격 책정 및 규모 계층](./media/iot-hub-upgrade/select-tier.png)

4. 허브에서 단위 수를 변경하려면 **IoT Hub 단위** 아래 새 값을 입력합니다.

5. **저장**을 선택하여 변경 내용을 저장합니다.

이제 IoT Hub가 조정되었으며 구성은 변경되지 않았습니다.

기본 계층 IoT Hub 및 IoT Hub 표준 계층에 대 한 최대 파티션 제한은 32입니다. 대부분의 IoT Hub는 4개의 파티션만 필요합니다. 파티션 제한은 IoT Hub가 만들어질 때 선택되며 디바이스-클라우드 메시지를 이러한 메시지의 동시 독자 수와 연결합니다. 기본 계층에서 표준 계층으로 마이그레이션할 때 이 값은 변경되지 않습니다.

## <a name="next-steps"></a>다음 단계

[올바른 IoT Hub 계층 선택 방법](iot-hub-scaling.md)에 대한 자세한 내용을 알아봅니다.