---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901651"
---
다음 표에서는 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)에 한정 된 할당량 및 제한을 제공 합니다. Event Hubs 가격에 대한 자세한 내용은 [Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

다음 제한은 기본, 표준 및 전용 계층에서 공통적으로 적용 됩니다. 

| 제한 | 범위 | 메모 | 값 |
| --- | --- | --- | --- |
| 구독당 Event Hubs 네임스페이스 수 |Subscription |- |100 |
| 네임스페이스당 Event Hubs 개수 |네임스페이스 |새 이벤트 허브 만들기에 대한 후속 요청이 거부 됩니다. |10 |
| Event Hub당 파티션 수 |엔터티 |- |32 |
| Event Hub 이름의 최대 크기 |엔터티 |- |50자 |
| 소비자 그룹당 비 Epoch 수신자 수 |엔터티 |- |5 |
| 최대 처리량 단위 |네임스페이스 |처리량 단위 제한을 초과 하면 데이터가 정체 되며 [서버 사용 중 예외가](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)생성 됩니다. 표준 계층에 대해 많은 수의 처리량 단위를 요청 하려면 [지원 요청](/azure/azure-portal/supportability/how-to-create-azure-support-request)을 파일에 만듭니다. [추가 처리량 단위](../articles/event-hubs/event-hubs-auto-inflate.md)는 약정된 구매를 기준으로 20개 단위로 사용할 수 있습니다. |20 |
| 네임스페이스당 권한 부여 규칙의 수 |네임스페이스|권한 부여 규칙 만들기에 대한 후속 요청이 거부 됩니다.|12 |
| GetRuntimeInformation 메서드에 대한 호출 수입니다. | 엔터티 | - | 초당 50 | 
| VNet (가상 네트워크) 및 IP 구성 규칙 수 | 엔터티 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic 및 Standard-할당량 및 한도
| 제한 | 범위 | 메모 | Basic | Standard |
| --- | --- | --- | -- | --- |
| Event Hubs 이벤트의 최대 크기|엔터티 | &nbsp; | 256KB | 1MB |
| Event Hub당 소비자 그룹 수 |엔터티 | &nbsp; |1 |20 |
| 네임스페이스당 AMQP 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부 되 고 호출 코드에서 예외를 수신 합니다. |100 |5,000|
| 이벤트 데이터의 최대 보존 기간 |엔터티 | &nbsp; |1일 |1-7일 |
|Apache Kafka 사용 네임 스페이스|네임스페이스 |Kafka 프로토콜을 사용 하 여 네임 스페이스 스트림 응용 프로그램 Event Hubs |아닙니다. | 예 |
|캡처 |엔터티 | 사용 하도록 설정 하면 동일한 스트림에서 마이크로 일괄 처리가 사용 됩니다. |아닙니다. |예 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated-할당량 및 한도
Event Hubs Dedicated 제품은 최소 4 시간 사용으로 고정 된 월별 가격으로 청구 됩니다. 전용 계층은 표준 요금제의 모든 기능을 제공 하지만, 엔터프라이즈 규모 용량과 워크 로드가 까다로운 고객에 대한 제한을 제공 합니다. 

| 기능 | 제한 |
| --- | ---|
| Bandwidth |  20 CUs |
| 네임스페이스 | CU 당 50 |
| Event Hubs |  1000 네임 스페이스 |
| 수신 이벤트 | 포함 항목 |
| 메시지 크기 | 1MB |
| 파티션 | CU 당 2000 |
| 소비자 그룹 | CU 당 제한 없음, 이벤트 허브 당 1000 |
| 조정된 연결 | 100,000개 포함 |
| 메시지 보존 | CU 당 90 일, 10TB 포함 |
| 캡처 | 포함 항목 |
