---
title: Azure IoT Hub 사용자 지정 엔드포인트 이해 | Microsoft Docs
description: 개발자 가이드 - 라우팅 쿼리를 사용하여 디바이스-클라우드 메시지를 사용자 지정 엔드포인트로 라우팅합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61244347"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>디바이스-클라우드 메시지에 대해 메시지 라우팅 및 사용자 지정 엔드포인트 사용

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [메시지 라우팅](iot-hub-devguide-routing-query-syntax.md)을 사용하면 사용자가 디바이스-클라우드 메시지를 서비스 연결 엔드포인트로 라우팅할 수 있습니다. 라우팅은 엔드포인트에 라우팅하기 전에 데이터를 필터링하는 쿼리 기능도 제공합니다. 구성하는 각 라우팅 쿼리에는 다음과 같은 속성이 있습니다.

| 자산      | 설명 |
| ------------- | ----------- |
| **Name**      | 쿼리를 식별하는 고유한 이름입니다. |
| **원본**    | 처리할 데이터 스트림의 원본입니다. 예를 들어 디바이스 원격 분석이 있습니다. |
| **Condition** | 엔드포인트의 일치 항목인지 확인하기 위해 메시지 애플리케이션 속성, 시스템 속성, 메시지 본문, 디바이스 쌍 태그 및 디바이스 쌍 속성에 대해 실행되는 라우팅 쿼리의 쿼리 식입니다. 쿼리 생성에 대한 자세한 내용은 [메시지 라우팅 쿼리 구문](iot-hub-devguide-routing-query-syntax.md)을 참조하세요. |
| **엔드포인트**  | IoT Hub에서 쿼리와 일치하는 메시지를 보내는 엔드포인트의 이름입니다. IoT Hub와 동일한 지역에서 엔드포인트를 선택하는 것이 좋습니다. |

하나의 메시지가 여러 라우팅 쿼리의 조건과 일치할 수 있습니다. 이 경우 IoT Hub는 일치된 각 쿼리와 연결된 엔드포인트로 메시지를 전송합니다. 또한 IoT Hub는 메시지 전송을 자동으로 중복 제거하므로 메시지가 동일한 대상을 가진 여러 쿼리와 일치하면 해당 대상에 한 번만 기록됩니다.

## <a name="endpoints-and-routing"></a>엔드포인트 및 라우팅

IoT 허브에는 [기본 제공 엔드포인트](iot-hub-devguide-messages-read-builtin.md)가 있습니다. 구독의 다른 서비스를 허브에 연결하여 메시지를 라우팅할 사용자 지정 엔드포인트를 만들 수 있습니다. IoT Hub는 현재 사용자 지정 엔드포인트로 Azure Storage 컨테이너, Event Hubs, Service Bus 큐 및 Service Bus 토픽을 지원합니다.

라우팅 및 사용자 지정 엔드포인트를 사용할 때 메시지에 일치하는 쿼리가 없으면 기본 제공 엔드포인트에만 메시지가 전달됩니다. 메시지를 기본 제공 엔드포인트와 사용자 지정 엔드포인트에 모두 전달하려면 메시지를 기본 제공 **이벤트** 엔드포인트에 보내는 경로를 추가합니다.

> [!NOTE]
> * IoT Hub는 데이터를 Azure Storage 컨테이너에 BLOB으로 쓰는 것만 지원합니다.
> * **세션** 또는 **중복 검색**이 사용하도록 설정된 Service Bus 큐 및 토픽은 사용자 지정 엔드포인트로 지원되지 않습니다.

IoT Hub에 사용자 지정 엔드포인트를 만드는 방법에 대한 자세한 내용은 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

사용자 지정 엔드포인트에서 읽는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Storage 컨테이너](../storage/blobs/storage-blobs-introduction.md)에서 읽기

* [Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)에서 읽기

* [Service Bus 큐](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)에서 읽기

* [Service Bus 토픽](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)에서 읽기

## <a name="next-steps"></a>다음 단계

* IoT Hub 엔드포인트에 대한 자세한 내용은 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

* 라우팅 쿼리를 정의하는 데 사용하는 쿼리 언어에 대한 자세한 내용은 [메시지 라우팅 쿼리 구문](iot-hub-devguide-routing-query-syntax.md)을 참조하세요.

* [경로를 사용하여 IoT Hub 장치-클라우드 메시지 처리](tutorial-routing.md) 자습서는 라우팅 쿼리와 사용자 지정 엔드포인트를 사용하는 방법을 보여 줍니다.