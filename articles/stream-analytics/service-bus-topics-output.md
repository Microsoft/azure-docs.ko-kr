---
title: Service Bus 항목 출력 Azure Stream Analytics
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력으로 Service Bus 항목을 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 71a41a3a8d137fdf9d4f41c975e7be6c148078e1
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875843"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Service Bus 항목 출력 Azure Stream Analytics

Service Bus 큐는 송신기에서 수신기로의 일대일 통신 방법을 제공합니다. [Service Bus 토픽](https://msdn.microsoft.com/library/azure/hh367516.aspx)은 일대다 형식의 통신을 제공합니다.

다음 표에는 Service Bus 토픽 출력을 만들기 위한 속성 이름과 해당 설명이 나와 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리에서 쿼리 출력을 이 Service Bus 토픽으로 보내는 데 사용되는 식별 이름입니다. |
| Service Bus 네임스페이스 |메시징 엔터티 세트에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 Service Bus 네임스페이스도 만들었습니다. |
| 항목 이름 |토픽은 이벤트 허브 및 큐와 유사한 메시징 엔터티입니다. 디바이스 및 서비스에서 이벤트 스트림을 수집하도록 설계되었습니다. 토픽이 만들어지면 특정 이름도 지정됩니다. 구독이 만들어지지 않으면 토픽에 전송된 메시지를 사용할 수 없으므로 토픽에 하나 이상의 구독이 있는지 확인합니다. |
| 토픽 정책 이름 |Service Bus 토픽을 만들 때 토픽의 **구성** 탭에서 공유 액세스 정책을 만들 수도 있습니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 토픽 정책 키 |Service Bus 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 이벤트 직렬화 형식 |출력 데이터에 대한 직렬화 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding |CSV 또는 JSON 형식을 사용하는 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다. |
| 구분 기호 |CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지의 사용자 속성으로 첨부해야 하는 쉼표로 구분된 열입니다. 이 기능에 대한 자세한 내용은 [출력에 대한 사용자 지정 메타데이터 속성](#custom-metadata-properties-for-output) 섹션에 있습니다. |
| 시스템 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지에 첨부해야 하는 시스템 속성 및 해당 열 이름에 대한 키 값 쌍입니다. |

파티션 수는 [Service Bus SKU 및 크기에 따라](../service-bus-messaging/service-bus-partitioning.md) 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다.

## <a name="partitioning"></a>분할

분할이 자동으로 선택 됩니다. 파티션 수는 [Service Bus SKU 및 크기](../service-bus-messaging/service-bus-partitioning.md)에 따라 달라집니다. 파티션 키는 각 파티션에 대한 고유 정수 값입니다. 출력 기록기의 수는 출력 토픽의 파티션 수와 같습니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 표준 계층의 경우 메시지당 256 KB이 고 프리미엄 계층의 경우 1MB입니다. 자세한 내용은 [Service Bus 제한](../service-bus-messaging/service-bus-quotas.md)을 참조 하세요. 최적화 하려면 메시지당 단일 이벤트를 사용 합니다.

## <a name="custom-metadata-properties-for-output"></a>출력에 대한 사용자 지정 메타데이터 속성

쿼리 열을 사용자 속성으로 나가는 메시지에 첨부할 수 있습니다. 이러한 열은 페이로드로 이동하지 않습니다. 속성은 출력 메시지에서 사전 형식으로 제공됩니다. *키*는 열 이름이고 *값*은 속성 사전의 열 값입니다. 레코드 및 배열을 제외한 모든 Stream Analytics 데이터 형식이 지원됩니다.

## <a name="system-properties"></a>시스템 속성

쿼리 열을 [시스템 속성](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)으로 나가는 Service Bus 큐 또는 토픽 메시지에 첨부할 수 있습니다. 이러한 열은 페이로드로 이동하지 않습니다. 대신 해당 BrokeredMessage [시스템 속성](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties)이 쿼리 열 값으로 채워집니다.
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
