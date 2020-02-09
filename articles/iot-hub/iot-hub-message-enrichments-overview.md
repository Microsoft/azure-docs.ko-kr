---
title: Azure IoT Hub 메시지 강화 개요
description: 이 문서에서는 메시지를 지정 된 엔드포인트으로 보내기 전에 추가 정보를 사용 하 여 메시지를 스탬프 할 수 IoT Hub 제공 하는 메시지 강화를 보여 줍니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429117"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>장치-클라우드 IoT Hub 메시지의 메시지 강화

*메시지 강화* 는 지정 된 엔드포인트으로 메시지를 보내기 전에 추가 정보를 사용 하 여 메시지를 *스탬프* 하는 IoT Hub의 기능입니다. 메시지 강화를 사용 하는 한 가지 이유는 다운스트림 처리를 간소화 하는 데 사용할 수 있는 데이터를 포함 하는 것입니다. 예를 들어 장치 쌍 태그를 사용 하는 장치 원격 분석 메시지를 보강 고객에 대 한 부하를 줄여이 정보에 대 한 장치 쌍 API 호출을 수행할 수 있습니다.

![메시지 강화 흐름](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

메시지 보강에는 다음과 같은 세 가지 주요 요소가 있습니다.

* 보강 이름 또는 키

* 값

* 보강을 적용 해야 하는 하나 이상의 [엔드포인트](iot-hub-devguide-endpoints.md) 입니다.

**키** 가 문자열입니다. 키에는 영숫자 문자 또는 하이픈 (`-`), 밑줄 (`_`), 마침표 (`.`) 등의 특수 문자만 사용할 수 있습니다.

**값** 은 다음 중 하나일 수 있습니다.

* 모든 정적 문자열입니다. 조건, 논리, 연산 및 함수와 같은 동적 값은 허용 되지 않습니다. 예를 들어 여러 고객이 사용 하는 SaaS 응용 프로그램을 개발 하는 경우 각 고객에 게 식별자를 할당 하 고 해당 식별자를 응용 프로그램에서 사용할 수 있도록 설정할 수 있습니다. 응용 프로그램이 실행 되 면 IoT Hub는 고객의 식별자를 사용 하 여 장치 원격 분석 메시지를 스탬프 처리 하므로 각 고객에 대해 메시지를 다르게 처리할 수 있습니다.

* 메시지를 보내는 IoT hub의 이름입니다. 이 값은 *$iothubname*입니다.

* 장치 쌍의 정보 (예: 경로) 예를 들면 *$twin* 와 *$twin*입니다.

   > [!NOTE]
   > 현재는 $iothubname, $twin, $twin, 속성 및 $twin만이 메시지 보강에 대해 지원 되는 변수입니다.

메시지 강화 선택 된 엔드포인트에 전송 된 메시지에 응용 프로그램 속성으로 추가 됩니다.  

## <a name="applying-enrichments"></a>강화 적용

메시지는 다음 예제를 포함 하 여 [IoT Hub 메시지 라우팅에서](iot-hub-devguide-messages-d2c.md)지 원하는 모든 데이터 소스에서 가져올 수 있습니다.

* 장치 원격 분석 (예: 온도 또는 압력)
* 장치 쌍 변경 알림--장치 쌍의 변경 내용
* 장치를 만들거나 삭제 하는 경우와 같은 장치 수명 주기 이벤트

IoT Hub의 기본 제공 엔드포인트으로 이동 하는 메시지 또는 Azure Blob storage, Service Bus 큐 또는 Service Bus 항목과 같은 사용자 지정 엔드포인트으로 라우팅되는 메시지에 강화를 추가할 수 있습니다.

Event Grid으로 엔드포인트을 선택 하 여 Event Grid에 게시 되는 메시지에 강화를 추가할 수 있습니다. Event Grid 구독에 따라 장치 원격 분석에 대 한 IoT Hub 기본 경로를 만듭니다. 이 단일 경로는 모든 Event Grid 구독을 처리할 수 있습니다. 장치 원격 분석에 대 한 event grid 구독을 만든 후 event grid 엔드포인트에 대 한 강화을 구성할 수 있습니다. 자세한 내용은 [Iot Hub 및 Event Grid](iot-hub-event-grid.md)를 참조 하세요.

강화는 엔드포인트 별로 적용 됩니다. 특정 엔드포인트에 대해 스탬프할 5 개의 강화 지정 하는 경우 해당 엔드포인트으로 이동 하는 모든 메시지는 동일한 5 개의 강화으로 스탬프 됩니다.

강화는 다음 메서드를 사용 하 여 구성할 수 있습니다.

| **메서드** | **명령** |
| ----- | -----| 
| 포털 | [Azure Portal](https://portal.azure.com) | [메시지 강화 자습서](tutorial-message-enrichments.md) 를 참조 하세요. | 
| Azure CLI   | [az iot hub message-보강](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

메시지 강화 추가는 메시지 라우팅에 대기 시간을 추가 하지 않습니다.

메시지 강화를 확인 하려면 [강화 자습서 메시지](tutorial-message-enrichments.md) 를 참조 하세요.

## <a name="limitations"></a>제한 사항

* 표준 또는 기본 계층에서 해당 허브에 대 한 IoT Hub 당 최대 10 개의 강화를 추가할 수 있습니다. 무료 계층에서 IoT Hub의 경우 최대 2 개의 강화를 추가할 수 있습니다.

* 경우에 따라 값이 장치 쌍의 태그나 속성으로 설정 된 보강을 적용 하는 경우 값은 문자열 값으로 기록 됩니다. 예를 들어 보강 값이 $twin로 설정 된 경우 메시지는 쌍의 해당 필드 값이 아니라 문자열 "$twin"로 스탬프 됩니다. 이는 다음과 같은 경우에 발생 합니다.

   * IoT Hub 기본 계층에 있습니다. 기본 계층 IoT hub는 장치 쌍을 지원 하지 않습니다.

   * IoT Hub 표준 계층에 있지만 메시지를 보내는 장치에 장치 쌍이 없습니다.

   * IoT Hub 표준 계층에 있지만 보강의 값에 사용 되는 장치 쌍 경로가 존재 하지 않습니다. 예를 들어 보강 값이 $twin로 설정 되 고 장치 쌍이 태그 아래에 location 속성을가지고 있지 않으면 메시지에 "$twin. tags. location" 문자열이 포함 됩니다. 

* 장치 쌍에 대 한 업데이트는 해당 보강 값에 반영 되는 데 최대 5 분이 걸릴 수 있습니다.

* 강화를 포함 하 여 총 메시지 크기는 256 KB를 초과할 수 없습니다. 메시지 크기가 256 KB를 초과 하면 IoT Hub 메시지를 삭제 합니다. [IoT Hub 메트릭을](iot-hub-metrics.md) 사용 하 여 메시지를 삭제할 때 오류를 식별 하 고 디버그할 수 있습니다. 예를 들어 d2c를 모니터링할 수 있습니다.

* 강화 메시지는 디지털 쌍 변경 이벤트 ( [IoT 플러그 앤 플레이 공개 미리 보기](../iot-pnp/overview-iot-plug-and-play.md)의 일부)에 적용 되지 않습니다.

## <a name="pricing"></a>가격

메시지 강화는 추가 요금 없이 사용할 수 있습니다. 현재 IoT Hub에 메시지를 보낼 때 요금이 청구 됩니다. 메시지가 여러 엔드포인트으로 이동 하더라도 해당 메시지에 대해 한 번만 요금이 청구 됩니다.

## <a name="next-steps"></a>다음 단계

IoT Hub로 메시지를 라우팅하는 방법에 대 한 자세한 내용은 다음 문서를 확인 하세요.

* [메시지 강화 자습서](tutorial-message-enrichments.md)

* [IoT Hub 메시지 라우팅을 사용 하 여 다른 엔드포인트으로 장치-클라우드 메시지 보내기](iot-hub-devguide-messages-d2c.md)

* [자습서: IoT Hub 라우팅](tutorial-routing.md)
