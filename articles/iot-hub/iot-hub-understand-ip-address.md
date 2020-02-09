---
title: IoT hub의 IP 주소 이해 | Microsoft Docs
description: IoT hub IP 주소 및 해당 속성을 쿼리 하는 방법을 이해 합니다. IoT hub의 IP 주소는 재해 복구 또는 지역 장애 조치 (failover)와 같은 특정 시나리오에서 변경 될 수 있습니다.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c5040721705b90a981f1f8a45a3a2eb70eefde05
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772157"
---
# <a name="iot-hub-ip-addresses"></a>IP 주소 IoT Hub

IoT Hub 공용 엔드포인트의 IP 주소 접두사는 _AzureIoTHub_ [service 태그](../virtual-network/service-tags-overview.md)아래에 주기적으로 게시 됩니다. 이러한 IP 주소 접두사를 사용 하 여 다양 한 네트워크 격리 목표를 구현 하기 위해 IoT Hub와 장치 또는 네트워크 자산 간의 연결을 제어할 수 있습니다.

| 목표 | 적용 가능한 시나리오 | 접근 방식 |
|------|-----------|----------|
| 장치 및 서비스가 IoT Hub 엔드포인트과만 통신 하는지 확인 | [장치-클라우드](./iot-hub-devguide-messaging.md)및 [클라우드-장치](./iot-hub-devguide-messages-c2d.md) 메시징, [직접 메서드](./iot-hub-devguide-direct-methods.md), [장치 및 모듈](./iot-hub-devguide-device-twins.md) 쌍 및 [장치 스트림](./iot-hub-device-streams-overview.md) | _AzureIoTHub_ 및 _EventHub_ 서비스 태그를 사용 하 여 IOT HUB 및 이벤트 허브 ip 주소 접두사를 검색 하 고 해당 ip 주소 접두사에 대 한 장치 및 서비스의 방화벽 설정에 대 한 허용 규칙을 적절 하 게 구성 합니다. 장치 또는 서비스에서 통신 하지 않으려는 다른 대상 IP 주소에 대 한 트래픽을 삭제 합니다. |
| IoT Hub 장치 엔드포인트이 장치 및 네트워크 자산의 연결만 수신 하는지 확인 | [장치-클라우드](./iot-hub-devguide-messaging.md)및 [클라우드-장치](./iot-hub-devguide-messages-c2d.md) 메시징, [직접 메서드](./iot-hub-devguide-direct-methods.md), [장치 및 모듈](./iot-hub-devguide-device-twins.md) 쌍 및 [장치 스트림](./iot-hub-device-streams-overview.md) | IoT Hub [IP 필터 기능](iot-hub-ip-filtering.md) 을 사용 하 여 장치와 네트워크 자산 IP 주소에서의 연결을 허용 합니다 ( [제한](#limitations-and-workarounds) 섹션 참조). | 
| 네트워크 자산 에서만 경로의 사용자 지정 엔드포인트 리소스 (저장소 계정, service bus 및 event hubs)에 연결할 수 있는지 확인 합니다. | [메시지 라우팅](./iot-hub-devguide-messages-d2c.md) | 연결 제한 (예: [방화벽 규칙](../storage/common/storage-network-security.md), [개인 링크](../private-link/private-endpoint-overview.md)또는 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)을 통해)에 대 한 리소스의 지침을 따르세요. _AzureIoTHub_ service 태그를 사용 하 여 IoT Hub ip 주소 접두사를 검색 하 고 리소스의 방화벽 구성에서 해당 ip 접두사에 대 한 허용 규칙을 추가 합니다 ( [제한](#limitations-and-workarounds) 섹션 참조). |



## <a name="best-practices"></a>모범 사례

* 장치의 방화벽 구성에서 허용 규칙을 추가할 때 [해당 프로토콜에서 사용 하는 특정 포트](./iot-hub-devguide-protocols.md#port-numbers)를 제공 하는 것이 가장 좋습니다.

* IoT hub의 IP 주소 접두사는 변경 될 수 있습니다. 이러한 변경 내용은 영향을 받기 전에 서비스 태그를 통해 정기적으로 게시 됩니다. 따라서 최신 서비스 태그를 정기적으로 검색 하 고 사용 하는 프로세스를 개발 하는 것이 중요 합니다. 이 프로세스는 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)를 통해 자동화할 수 있습니다. 서비스 태그 검색 API는 아직 미리 보기 상태 이며 경우에 따라 태그 및 IP 주소의 전체 목록을 생성 하지 못할 수도 있습니다. 검색 API를 일반적으로 사용할 수 있을 때까지 [다운로드 가능한 JSON 형식으로 서비스 태그](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)를 사용 하는 것이 좋습니다. 

* AzureIoTHub를 사용 *합니다. [ region name]* 태그를 지정 하 여 특정 지역의 IoT hub 엔드포인트에서 사용 하는 IP 접두사를 식별 합니다. 데이터 센터 재해 복구를 고려 하거나 [지역 장애 조치 (failover)](iot-hub-ha-dr.md) 를 위해 IoT Hub 지역 쌍 지역의 IP 접두사에 대 한 연결도 사용 하도록 설정 해야 합니다.

* IoT Hub 방화벽 규칙을 설정 하면 IoT Hub에 대해 Azure CLI 및 PowerShell 명령을 실행 하는 데 필요한 연결을 차단할 수 있습니다. 이를 방지 하려면 클라이언트의 IP 주소 접두사에 대 한 허용 규칙을 추가 하 여 CLI 또는 PowerShell 클라이언트가 IoT Hub와 통신할 수 있도록 다시 설정할 수 있습니다.  


## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

* IoT Hub IP 필터 기능에는 10 개의 규칙 제한이 있습니다. 이 제한은 Azure 고객 지원을 통해 요청을 통해 발생할 수 있습니다. 

* 구성 된 [ip 필터링 규칙](iot-hub-ip-filtering.md) 은 IoT hub의 기본 제공 Event Hub 엔드포인트이 아니라 IoT Hub ip 엔드포인트에만 적용 됩니다. 또한 메시지가 저장 되는 이벤트 허브에 IP 필터링을 적용 해야 하는 경우 원하는 IP 필터링 규칙을 직접 구성할 수 있는 고유한 이벤트 허브 리소스를 가져올 수 있습니다. 이렇게 하려면 사용자 고유의 이벤트 허브 리소스를 프로 비전 하 고 [메시지 라우팅을](./iot-hub-devguide-messages-d2c.md) 설정 하 여 IoT Hub의 기본 제공 이벤트 허브 대신 해당 리소스로 메시지를 전송 해야 합니다. 마지막으로 위의 표에 설명 된 대로 메시지 라우팅 기능을 사용 하도록 설정 하려면 IoT Hub의 IP 주소 접두사에서 프로 비전 된 이벤트 허브 리소스로의 연결도 허용 해야 합니다.

* 저장소 계정으로 라우팅할 때 IoT Hub의 IP 주소 접두사에서 트래픽을 허용 하는 것은 저장소 계정이 IoT Hub 다른 지역에 있는 경우에만 가능 합니다.

## <a name="support-for-ipv6"></a>I p v 6에 대 한 지원 

I p v 6은 현재 IoT Hub에서 지원 되지 않습니다.
