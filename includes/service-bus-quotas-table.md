---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c8412a01f4a5056b352b1d985f36e5a51a25a649
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76158893"
---
다음 표에는 Azure Service Bus 메시징과 관련 된 할당량 정보가 나와 있습니다. Service Bus에 대한 가격 책정 및 기타 할당량에 대한 자세한 내용은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조 하세요.

| 할당량 이름 | 범위 | 메모 | 값 |
| --- | --- | --- | --- |
| Azure 구독 당 기본 또는 표준 네임 스페이스의 최대 수 |네임스페이스 |추가 기본 또는 표준 네임 스페이스에 대한 후속 요청은 Azure Portal에서 거부 됩니다. |100|
| Azure 구독 당 최대 프리미엄 네임 스페이스 수 |네임스페이스 |추가 프리미엄 네임 스페이스에 대한 후속 요청은 포털에서 거부 됩니다. |100 |
| 큐 또는 토픽 크기 |엔터티 |큐 또는 토픽을 만들 때 정의 됩니다. <br/><br/> 이후에 들어오는 메시지는 거부 되 고 호출 코드에서 예외를 수신 합니다. |1, 2, 3, 4GB 또는 5GB<br /><br />Premium SKU 및 [분할](/azure/service-bus-messaging/service-bus-partitioning) 을 사용 하는 표준 SKU에서 최대 큐 또는 토픽 크기는 80입니다. |
| 네임스페이스에 대한 동시 연결 수 |네임스페이스 |추가 연결에 대한 후속 요청이 거부 되 고 호출 코드에서 예외를 수신 합니다. REST 작업은 동시 TCP 연결에 계산 되지 않습니다. |NetMessaging: 1000.<br /><br />AMQP: 5000. |
| 큐, 토픽 또는 구독 엔터티의 동시 수신 요청 수 |엔터티 |후속 수신 요청이 거부 되 고 호출 코드에서 예외를 수신 합니다. 이 할당량은 항목의 모든 구독 전반에 걸쳐 종합된 동시 수신 명령 수에 적용됩니다. |5,000 |
| 네임 스페이스 당 항목 또는 큐 수 |네임스페이스 |네임스페이스에 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]를 통해 구성 된 경우 오류 메시지가 생성 됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |기본 또는 표준 계층의 경우 1만입니다. 네임스페이스에서 토픽 및 큐의 총수는 10,000 이하이어야 합니다. <br/><br/>프리미엄 계층의 경우, 1000 MU (메시징 단위) 당입니다. 최대한도는 4000입니다. |
| 네임 스페이스 당 [분할 된 항목 또는 큐](/azure/service-bus-messaging/service-bus-partitioning) 수 |네임스페이스 |네임스페이스에 분할된 새 토픽 또는 큐를 만들기 위한 후속 요청이 거부됩니다. 따라서 [Azure Portal][Azure portal]를 통해 구성 된 경우 오류 메시지가 생성 됩니다. 관리 API에서 호출 된 경우 호출 코드에서 **QuotaExceededException** 예외를 수신 합니다. |기본 및 표준 계층: 100.<br/><br/>분할 된 엔터티는 [프리미엄](../articles/service-bus-messaging/service-bus-premium-messaging.md) 계층에서 지원 되지 않습니다.<br/><br />각 분할 된 큐 또는 항목은 네임 스페이스 당 1000 엔터티의 할당량을 계산 합니다. |
| 모든 메시징 엔터티 경로의 최대 크기: 큐 또는 토픽 |엔터티 |- |260 자 |
| 모든 메시징 엔터티 이름의 최대 크기: 네임스페이스, 구독 또는 구독 규칙 |엔터티 |- |50 자 |
| [메시지 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid)의 최대 크기 | 엔터티 |- | 128 |
| 메시지 [세션 ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 의 최대 크기 | 엔터티 |- | 128 |
| 큐, 토픽 또는 구독 엔터티의 메시지 크기 |엔터티 |이러한 할당량을 초과 하는 들어오는 메시지는 거부 되 고 호출 코드에서 예외를 수신 합니다. |최대 메시지 크기: [표준 계층](../articles/service-bus-messaging/service-bus-premium-messaging.md)의 경우 256 KB, [프리미엄 계층](../articles/service-bus-messaging/service-bus-premium-messaging.md)의 경우 1mb <br /><br />시스템 오버헤드로 인해, 이 제한이 이러한 값보다 작습니다.<br /><br />최대 헤더 크기: 64 KB<br /><br />속성 모음의 최대 헤더 속성 수: **byte/int. Int32.maxvalue**.<br /><br />속성 모음의 최대 속성 크기: 명시적 제한은 없습니다. 최대 헤더 크기로 제한됩니다. |
| 큐, 토픽 또는 구독 엔터티의 메시지 속성 크기 |엔터티 | **SerializationException** 예외가 생성 됩니다. |각 속성의 최대 메시지 속성 크기는 32000입니다. 모든 속성의 누적 크기는 64000를 초과할 수 없습니다. 이 제한은 사용자 속성과 시스템 속성 (예: [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid))이 모두 포함 된 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)의 전체 헤더에 적용 됩니다. |
| 토픽당 구독 수 |엔터티 |토픽에 추가 구독을 만들기 위한 후속 요청이 거부됩니다. 따라서 포털을 통해 구성된 경우 오류 메시지가 표시됩니다. 관리 API에서 호출되는 경우 호출 코드에서 예외가 수신됩니다. |표준 계층에 대한 항목당 2000. |
| 토픽당 SQL 필터 수 |엔터티 |토픽에서 추가 필터를 만들기 위한 후속 요청이 거부 되 고 호출 코드에서 예외를 수신 합니다. |2,000 |
| 토픽당 상관 관계 필터 수 |엔터티 |토픽에서 추가 필터를 만들기 위한 후속 요청이 거부 되 고 호출 코드에서 예외를 수신 합니다. |100,000개의 |
| SQL 필터 또는 작업 크기 |네임스페이스 |추가 필터를 만들기 위한 후속 요청이 거부 되 고 호출 코드에서 예외를 수신 합니다. |필터 조건 문자열의 최대 길이: 1024 (1 K).<br /><br />규칙 동작 문자열의 최대 길이: 1024 (1 K).<br /><br />규칙 작업당 식의 최대 수: 32 |
| 네임스페이스, 큐 또는 토픽당 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 규칙 수 |엔터티, 네임스페이스 |추가 규칙 만들기에 대한 후속 요청이 거부 되며 호출 코드에서 예외를 수신 합니다. |최대 규칙 수: 12 <br /><br /> Service Bus 네임스페이스에 구성된 규칙이 해당 네임스페이스에 있는 모든 큐 및 토픽에 적용됩니다. |
| 트랜잭션당 메시지 수 | 트랜잭션 | 추가 수신 메시지는 거부 되 고 호출 코드에서 "단일 트랜잭션에서 100 개 이상의 메시지를 보낼 수 없습니다." 라는 예외를 수신 합니다. | 100 <br /><br /> **Send()** 및 **SendAsync()** 작업 모두에 해당합니다. |
| 가상 네트워크 및 IP 필터 규칙 수 | 네임스페이스 | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
