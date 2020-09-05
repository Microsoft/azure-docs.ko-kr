---
title: Azure Stream Analytics에서 Service Bus 큐 출력
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력으로 Service Bus 큐에 대해 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: aa9ec5454935816e733c1607e006ba1c30476cac
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875891"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Azure Stream Analytics에서 Service Bus 큐 출력

[Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)는 FIFO 메시지 전송을 하나 이상의 경쟁 소비자에 제공합니다. 일반적으로 메시지는 큐에 추가된 시간 순서대로 수신기에서 수신되고 처리됩니다. 각 메시지는 하나의 메시지 소비자에서만 수신되고 처리됩니다.

[호환성 수준 1.2](stream-analytics-compatibility-level.md)에서 Azure Stream Analytics는 [AMQP(고급 메시지 큐 프로토콜)](../service-bus-messaging/service-bus-amqp-overview.md) 메시징 프로토콜을 사용하여 Service Bus 큐 및 토픽에 씁니다. AMQP를 사용하여 여러 플랫폼 간에 개방형 표준 프로토콜을 사용하는 하이브리드 애플리케이션을 빌드할 수 있습니다.

## <a name="output-configuration"></a>출력 구성

다음 표에는 큐 출력을 만들기 위한 속성 이름 및 해당 설명이 나와 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리에서 쿼리 출력을 이 Service Bus 큐로 보내는 데 사용되는 식별 이름입니다. |
| Service Bus 네임스페이스 |메시징 엔터티 세트에 대한 컨테이너입니다. |
| 큐 이름 |Service Bus 큐의 이름입니다. |
| 큐 정책 이름 |큐를 만들 때 큐의 **구성** 탭에서 공유 액세스 정책을 만들 수도 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 큐 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 이벤트 직렬화 형식 |출력 데이터에 대한 직렬화 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 |CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 |JSON 형식에만 적용됩니다. **줄로 구분됨**은 각 JSON 개체를 새 줄로 구분된 형식의 출력이 되도록 지정합니다. **줄로 구분됨**을 선택하는 경우 JSON에서 한 번에 하나의 개체를 읽습니다. 전체 콘텐츠 자체는 유효한 JSON이 아닙니다. **배열**은 JSON 개체 배열 형식의 출력이 되도록 지정합니다. |
| 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지의 사용자 속성으로 첨부해야 하는 쉼표로 구분된 열입니다. 이 기능에 대한 자세한 내용은 [출력에 대한 사용자 지정 메타데이터 속성](#custom-metadata-properties-for-output) 섹션에 있습니다. |
| 시스템 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지에 첨부해야 하는 시스템 속성 및 해당 열 이름에 대한 키 값 쌍입니다.  |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.

## <a name="partitioning"></a>분할

분할이 자동으로 선택 됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다. 출력 기록기의 수는 출력 큐의 파티션 수와 같습니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 표준 계층의 경우 메시지당 256 KB이 고 프리미엄 계층의 경우 1MB입니다. 자세한 내용은 [Service Bus 제한](../service-bus-messaging/service-bus-quotas.md)을 참조 하세요. 최적화 하려면 메시지당 단일 이벤트를 사용 합니다.

## <a name="custom-metadata-properties-for-output"></a>출력에 대한 사용자 지정 메타데이터 속성

쿼리 열을 사용자 속성으로 나가는 메시지에 첨부할 수 있습니다. 이러한 열은 페이로드로 이동하지 않습니다. 속성은 출력 메시지에서 사전 형식으로 제공됩니다. *키*는 열 이름이고 *값*은 속성 사전의 열 값입니다. 레코드 및 배열을 제외한 모든 Stream Analytics 데이터 형식이 지원됩니다.

## <a name="system-properties"></a>시스템 속성

쿼리 열을 [시스템 속성](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)으로 나가는 Service Bus 큐 또는 토픽 메시지에 첨부할 수 있습니다.

이러한 열은 페이로드로 이동하지 않습니다. 대신 해당 BrokeredMessage [시스템 속성](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)이 쿼리 열 값으로 채워집니다.
지원되는 시스템 속성은 `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`입니다.

이러한 열의 문자열 값은 해당 시스템 속성 값 형식으로 구문 분석되고, 구문 분석 실패는 데이터 오류로 처리됩니다.
이 필드는 JSON 개체 형식으로 제공됩니다. 이 형식에 대 한 자세한 내용은 다음과 같습니다.

* 중괄호({})로 묶입니다.
* 키/값 쌍으로 작성됩니다.
* 키와 값은 문자열이어야 합니다.
* 키는 시스템 속성 이름이고, 값은 쿼리 열 이름입니다.
* 키와 값은 콜론으로 구분됩니다.
* 각 키/값 쌍은 쉼표로 구분됩니다.

이 속성을 사용하는 방법은 다음과 같습니다.

* 쿼리: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 시스템 속성 열: `{ "MessageId": "column1", "PartitionKey": "column2"}`

이는 Service Bus 큐 메시지에서 `MessageId`가 `column1`의 값으로 설정되고 PartitionKey가 `column2`의 값으로 설정됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
