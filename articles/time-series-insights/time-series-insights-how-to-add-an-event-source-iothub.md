---
title: IoT hub 이벤트 소스를 추가 하는 방법-Azure Time Series Insights | Microsoft Docs
description: Time Series Insights 환경에 IoT hub 이벤트 원본을 추가 하는 방법에 대해 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: 3ea73e2ca20faea30294bc5d5e1788415095c39f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905376"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Time Series Insights 환경에 IoT Hub 이벤트 원본 추가

이 문서에서는 Azure Portal을 사용하여 Azure IoT Hub에서 데이터를 읽는 이벤트 원본을 Azure Time Series Insights 환경에 추가하는 방법을 다룹니다.

> [!NOTE]
> 이 문서의 지침은 Azure Time Series Insights GA 및 Time Series Insights 미리 보기 환경에 적용됩니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Time Series Insights 환경](time-series-insights-update-create-environment.md)을 만듭니다.
* [Azure Portal을 사용하여 IoT Hub](../iot-hub/iot-hub-create-through-portal.md)를 만듭니다.
* IoT Hub에는 전송 중인 활성 메시지 이벤트가 있어야 합니다.
* Time Series Insights에서 사용할 IoT Hub의 전용 소비자 그룹을 만듭니다. 각 Time Series Insights 이벤트 원본에는 다른 소비자와 공유되지 않은 전용 소비자 그룹 자체가 있어야 합니다. 여러 판독기가 동일한 소비자 그룹의 이벤트를 사용 하는 경우 모든 판독기에 오류가 발생할 수 있습니다. 자세한 내용은 [Azure IoT Hub 개발자 가이드](../iot-hub/iot-hub-devguide.md)를 참조 하세요.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

애플리케이션은 소비자 그룹을 사용하여 Azure IoT Hub의 데이터를 끌어옵니다. IoT hub에서 데이터를 안정적으로 읽으려면이 Time Series Insights 환경 에서만 사용 되는 전용 소비자 그룹을 제공 합니다.

IoT Hub에 새 소비자 그룹을 추가하려면

1. [Azure Portal](https://portal.azure.com)에서 IoT hub를 찾아서 엽니다.

1. **설정**에서 **기본 제공 엔드포인트**을 선택 하 고 **이벤트** 엔드포인트을 선택 합니다.

   [![빌드 엔드포인트 페이지에서 이벤트 단추를 선택 합니다.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. **소비자 그룹**에 소비자 그룹의 고유한 이름을 입력합니다. 새 이벤트 원본을 만들 때 Time Series Insights 환경에서 이 동일한 이름을 사용합니다.

1. **저장**을 선택합니다.

## <a name="add-a-new-event-source"></a>새 이벤트 원본 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. Time Series Insights 환경을 선택합니다.

1. **설정**아래에서 **이벤트 원본**을 선택 하 고 **추가**를 선택 합니다.

   [이벤트 원본 ![선택 하 고 추가 단추를 선택 합니다.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. **새 이벤트 원본** 창에서 **이벤트 원본 이름**으로 이 Time Series Insights 환경에 고유한 이름을 입력합니다. 예를 들어, **event-stream**을 입력합니다.

1. **원본**으로 **IoT Hub**를 선택합니다.

1. **가져오기 옵션** 값을 선택합니다.

   * 구독 중 하나에 IoT Hub가 이미 있는 경우 **사용 가능한 구독에서 IoT Hub 사용**을 선택합니다. 이 옵션이 가장 쉬운 방법입니다.
   
     [![새 이벤트 원본 창에서 옵션을 선택 합니다.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

    * 다음 표에서는 **사용 가능한 구독에서 IoT Hub 사용** 옵션에 필요한 속성에 대해 설명합니다.

       [![새 이벤트 원본 창-사용 가능한 구독에서 IoT Hub 사용 옵션에서 설정할 속성](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | 속성 | Description |
       | --- | --- |
       | Subscription | 원하는 iot hub가 속한 구독입니다. |
       | IoT Hub 이름 | 선택한 iot hub의 이름입니다. |
       | IoT Hub 정책 이름 | 공유 액세스 정책을 선택합니다. IoT hub 설정 탭에서 공유 액세스 정책을 찾을 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **서비스 연결** 사용 권한이 *반드시* 있어야 합니다. |
       | IoT Hub 정책 키 | 키는 미리 채워져 있습니다. |

    * IoT Hub가 구독 외부에 있거나 고급 옵션을 선택하려는 경우 **수동으로 IoT Hub 설정 제공**을 선택합니다.

      다음 표에서는 **수동으로 IoT Hub 설정 제공**에 대한 필수 속성을 설명합니다.

       | 속성 | Description |
       | --- | --- |
       | 구독 ID | 원하는 iot hub가 속한 구독입니다. |
       | 리소스 그룹 | 이 IoT Hub가 만들어진 리소스 그룹 이름입니다. |
       | IoT Hub 이름 | IoT Hub의 이름입니다. IoT Hub를 만들 때 IoT Hub에 대해 입력한 이름입니다. |
       | IoT Hub 정책 이름 | 공유 액세스 정책입니다. IoT hub 설정 탭에서 공유 액세스 정책을 만들 수 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 원본에 대한 공유 액세스 정책에는 **서비스 연결** 사용 권한이 *반드시* 있어야 합니다. |
       | IoT Hub 정책 키 | Azure Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. 기본 키 또는 보조 키를 여기에 입력합니다. |

    * 두 옵션 모두 다음 구성 옵션을 공유 합니다.

       | 속성 | Description |
       | --- | --- |
       | IoT Hub 소비자 그룹 | IoT Hub에서 이벤트를 읽는 소비자 그룹입니다. 이벤트 원본에 대한 전용 소비자 그룹을 사용하는 것이 좋습니다. |
       | 이벤트 직렬화 형식 | 현재, JSON이 사용 가능한 유일한 직렬화 형식입니다. 이벤트 메시지는 이 형식이어야 합니다. 그렇지 않으면 데이터를 읽을 수 없습니다. |
       | 타임스탬프 속성 이름 | 이 값을 확인하려면 IoT Hub로 전송되는 메시지 데이터의 메시지 형식을 이해해야 합니다. 이 값은 이벤트 타임스탬프로 사용하려는 메시지 데이터에 있는 특정 이벤트 속성의 **이름**입니다. 이 값은 대/소문자를 구분합니다. 이 값을 비워 두면 이벤트 원본의 **이벤트를 큐에 넣는 시간**이 이벤트 타임스탬프로 사용됩니다. |


1. 추가한 전용 Time Series Insights 소비자 그룹 이름을 IoT Hub에 추가합니다.

1. **만들기**를 선택합니다.

1. 이벤트 원본을 만들면 Time Series Insights가 자동으로 데이터를 환경으로 스트리밍하기 시작합니다.

## <a name="next-steps"></a>다음 단계

* 데이터를 보호하기 위한 [데이터 액세스 정책 정의](time-series-insights-data-access.md)

* 이벤트 원본으로 [이벤트 전송](time-series-insights-send-events.md)

* [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경에 액세스
