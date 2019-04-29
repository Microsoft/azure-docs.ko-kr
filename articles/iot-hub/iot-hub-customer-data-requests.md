---
title: 고객 데이터 요청 기능
description: 고객 데이터 요청 기능 요약
author: robinsh
manager: philmea
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 1519637eddf909040131a1efac5738fc7cc8e565
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845803"
---
# <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약

Azure IoT Hub는 수백만 개의 디바이스와 분할된 Azure 서비스 간의 안전한 양방향 통신을 지원하는 엔터프라이즈 고객을 대상으로 하는 REST API 기반 클라우드 서비스입니다.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

개별 디바이스에는 테넌트 관리자에 의해 디바이스 식별자(디바이스 ID)가 할당됩니다. 디바이스 데이터는 할당된 디바이스 ID를 기반으로 합니다. Microsoft는 어떤 정보도 유지 관리하지 않으며, 디바이스 ID와 사용자의 상관 관계를 허용하는 데이터에 액세스할 수 없습니다.

Azure IoT Hub에서 관리되는 디바이스는 대부분 개인 디바이스가 아닙니다(예: 사무실 온도 조절 디바이스 또는 공장 로봇). 그러나 고객은 일부 디바이스를 개인적으로 식별 가능한 것으로 간주할 수 있으며, 재량에 따라 디바이스를 개인과 연결하는 자체 자사 또는 재고 추적 방법을 유지할 수 있습니다. Azure IoT Hub는 디바이스와 연결된 모든 데이터를 개인 데이터인 것처럼 관리 및 저장합니다.

테넌트 관리자는 Azure Portal 또는 서비스의 REST API를 사용하여 디바이스 ID와 연결된 데이터를 내보내거나 삭제하는 방식으로 정보 요청을 이행할 수 있습니다.

Azure IoT Hub 서비스의 라우팅 기능을 사용하여 디바이스 메시지를 다른 서비스에 전달하는 경우 해당 디바이스에 대한 전체 요청을 완료하려면 각 라우팅 엔드포인트에 대해 테넌트 관리자가 데이터 요청을 수행해야 합니다. 자세한 내용은 각 엔드포인트의 참조 설명서를 참조하세요. 지원되는 엔드포인트에 대한 자세한 내용은 [참조 - IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

Azure IoT Hub 서비스의 Azure Event Grid 통합 기능을 사용하는 경우 이러한 이벤트의 각 구독자에 대해 테넌트 관리자가 데이터 요청을 수행해야 합니다. 자세한 내용은 [Event Grid를 사용하여 IoT Hub 이벤트에 대응](iot-hub-event-grid.md)을 참조하세요.

Azure IoT Hub 서비스의 Azure Monitor 통합 기능을 사용하여 진단 로그를 만드는 경우 저장된 로그에 대해 테넌트 관리자가 데이터 요청을 수행해야 합니다. 자세한 내용은 [Azure IoT Hub의 상태 모니터링](iot-hub-monitor-resource-health.md)을 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

테넌트 관리자는 Azure Portal에서 Azure IoT Hub 확장의 IoT 디바이스 블레이드를 사용하여 디바이스를 삭제할 수 있습니다. 그러면 해당 디바이스와 연결된 데이터가 삭제됩니다.

또한 REST API를 사용하여 디바이스에 대한 삭제 작업을 수행할 수 있습니다. 자세한 내용은 [서비스 - 디바이스 삭제](/rest/api/iothub/service/deletedevice)를 참조하세요.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

테넌트 관리자는 Azure Portal에서 Azure IoT Hub 확장의 IoT 디바이스 블레이드 내 복사하여 붙여넣기를 사용하여 해당 디바이스와 연결된 데이터를 내보낼 수 있습니다.

또한 REST API를 사용하여 디바이스에 대한 내보내기 작업을 수행할 수 있습니다. 자세한 내용은 [서비스 - 디바이스 가져오기](/rest/api/iothub/service/getdevice)를 참조하세요.

> [!NOTE]
> Microsoft 엔터프라이즈 서비스를 사용하는 경우 Microsoft는 시스템 생성 로그라는 일부 정보를 생성합니다. 일부 Azure IoT Hub 시스템 생성 로그는 테넌트 관리자가 액세스하거나 내보낼 수 없습니다. 이러한 로그는 서비스 내에서 수행된 실제 작업 및 개별 디바이스와 관련된 진단 데이터로 구성됩니다.

## <a name="links-to-additional-documentation"></a>추가 설명서에 대한 링크

Azure IoT Hub 서비스 API에 대한 전체 설명서는 [IoT Hub 서비스 API](https://docs.microsoft.com/rest/api/iothub/service)에 있습니다.
