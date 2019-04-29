---
title: Azure IoT Hub 메시징 이해 | Microsoft 문서
description: 개발자 가이드 - IoT Hub를 사용한 디바이스-클라우드 및 클라우드-디바이스 메시징 메시지 형식 및 지원되는 통신 프로토콜에 대한 정보가 포함됩니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626250"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>IoT Hub를 사용한 디바이스-클라우드 및 클라우드-디바이스 메시지 보내기

IoT Hub는 디바이스와 양방향 통신을 허용합니다. IoT Hub 메시징을 사용하여 디바이스에서 솔루션 백 엔드로 메시지를 보내고 IoT 솔루션 백 엔드에서 디바이스로 명령을 보내는 방식으로 디바이스와 통신합니다. [IoT Hub 메시지 형식](iot-hub-devguide-messages-construct.md)에 대해 자세히 알아봅니다.

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>IoT Hub에 디바이스-클라우드 메시지 보내기

IoT Hub에는 백 엔드 서비스가 디바이스에서 원격 분석 메시지를 읽는 데 사용할 수 있는 기본 제공 서비스 엔드포인트가 있습니다. 이 엔드포인트는 [Event Hubs](https://docs.microsoft.com/azure/event-hubs/)와 호환되며 표준 IoT Hub SDK를 사용하여 [이 기본 제공 엔드포인트](iot-hub-devguide-messages-read-builtin.md)에서 읽을 수 있습니다.

또한 IoT Hub는 [메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 사용하여 디바이스 원격 분석 데이터 및 이벤트를 Azure 서비스에 보내도록 사용자가 정의할 수 있는 [사용자 지정 엔드포인트](iot-hub-devguide-endpoints.md#custom-endpoints)를 지원합니다.

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>IoT Hub에서 클라우드-장치 메시지 보내기

솔루션 백 엔드에서 디바이스로 [클라우드-디바이스](iot-hub-devguide-messages-c2d.md) 메시지를 보낼 수 있습니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub 메시징 기능의 핵심 속성은 메시지의 안정성 및 내구성입니다. 이 속성을 사용하여 디바이스 쪽에서 일시적인 연결 및 클라우드 쪽에서 이벤트 처리에 급증한 부하를 복원합니다. IoT Hub는 디바이스-클라우드 및 클라우드-디바이스 메시징 모두에 대해 *한 번 이상* 전달 보증을 구현합니다.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>IoT Hub 메시징의 적합한 유형 선택

디바이스 앱에서 시계열 원격 분석 및 경고를 보내려면 디바이스-클라우드 메시지를 사용하고 디바이스 앱에 단방향 알림을 보내려면 클라우드-디바이스 메시지를 사용합니다.

* [디바이스-클라우드 통신 지침](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance)을 참조하여 디바이스-클라우드 메시지, 보고된 속성 또는 파일 업로드 중에서 선택합니다.

* [클라우드-장치 통신 지침](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance)을 참조하여 클라우드-장치 메시지, 원하는 속성 또는 직접 메서드 중에서 선택합니다.

## <a name="next-steps"></a>다음 단계

* IoT Hub [메시지 라우팅](iot-hub-devguide-messages-d2c.md)에 대해 알아봅니다.

* IoT Hub [클라우드-장치 메시징](iot-hub-devguide-messages-c2d.md)에 대해 알아봅니다.