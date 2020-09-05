---
title: Azure Stream Analytics에서 출력 Event Hubs
description: 이 문서에서는 Azure Stream Analytics에서 Azure Event Hubs로 데이터를 출력 하는 방법을 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d18d4aa4bf9306bcdd667faa53f0d888c090e2fd
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875939"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Azure Stream Analytics에서 출력 Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 서비스는 확장성이 뛰어난 게시-구독 이벤트 수집기입니다. 초당 수 백만의 이벤트를 수집할 수 있습니다. 이벤트 허브를 출력으로 사용하는 한 가지 경우는 Stream Analytics 작업의 출력이 다른 스트리밍 작업의 입력이 되는 경우입니다. 최대 메시지 크기 및 일괄 처리 크기 최적화에 대한 자세한 내용은 [출력 일괄 처리 크기](#output-batch-size) 섹션을 참조하세요.

## <a name="output-configuration"></a>출력 구성

다음 표에서는 이벤트 허브에서 출력으로 데이터 스트림을 구성 하는 데 필요한 매개 변수를 포함 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 | 쿼리에서 쿼리 출력을 이 이벤트 허브로 보내는 데 사용되는 식별 이름입니다. |
| 이벤트 허브 네임스페이스 | 메시징 엔터티 세트에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 이벤트 허브 네임스페이스도 만들었습니다. |
| 이벤트 허브 이름 | 이벤트 허브 출력의 이름입니다. |
| 이벤트 허브 정책 이름 | 이벤트 허브의 **구성** 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| 이벤트 허브 정책 키 | 이벤트 허브 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| 파티션 키 열 | (선택 사항) 이벤트 허브 출력에 대한 파티션 키가 포함된 열입니다. |
| 이벤트 직렬화 형식 | 출력 데이터에 대한 직렬화 형식입니다. JSON, CSV 및 Avro를 지원합니다. |
| Encoding | CSV 및 JSON의 경우 UTF-8이 이번에만 지원되는 인코딩 형식입니다. |
| 구분 기호 | CSV 직렬화에만 적용됩니다. Stream Analytics는 CSV 형식에서 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다. |
| 형식 | JSON 직렬화에만 적용됩니다. **줄로 구분됨**은 각 JSON 개체를 새 줄로 구분된 형식의 출력이 되도록 지정합니다. **줄로 구분됨**을 선택하는 경우 JSON에서 한 번에 하나의 개체를 읽습니다. 전체 콘텐츠 자체는 유효한 JSON이 아닙니다. **배열**은 JSON 개체 배열 형식의 출력이 되도록 지정합니다.  |
| 속성 열 | (선택 사항) 페이로드 대신 나가는 메시지의 사용자 속성으로 첨부해야 하는 쉼표로 구분된 열입니다. 이 기능에 대한 자세한 내용은 [출력에 대한 사용자 지정 메타데이터 속성](#custom-metadata-properties-for-output) 섹션에 있습니다. |

## <a name="partitioning"></a>분할

분할은 파티션 정렬에 따라 달라 집니다. 이벤트 허브 출력의 파티션 키가 업스트림(이전) 쿼리 단계와 동일하게 맞춰지면 기록기 수는 이벤트 허브 출력의 파티션 수와 같습니다. 각 기록기는 [EventHubSender 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)를 사용하여 이벤트를 특정 파티션에 보냅니다. 이벤트 허브 출력의 파티션 키가 업스트림(이전) 쿼리 단계와 동일하게 맞춰지지 않으면 기록기의 수는 이전 단계의 파티션 수와 같습니다. 각 기록기는 **EventHubClient**의 [SendBatchAsync 클래스](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)를 사용하여 이벤트를 모든 출력 파티션에 보냅니다. 

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 메시지 크기는 256 KB 또는 메시지당 1mb입니다. 자세한 내용은 [Event Hubs 제한](../event-hubs/event-hubs-quotas.md)을 참조 하세요. 입/출력 분할이 정렬되지 않은 경우 각 이벤트는 `EventData`에 개별적으로 압축되어 최대 메시지 크기까지 일괄 처리 방식으로 보내집니다. [사용자 지정 메타데이터 속성](#custom-metadata-properties-for-output)이 사용되는 경우에도 마찬가지입니다. 입/출력 분할이 정렬되는 경우 여러 이벤트가 최대 메시지 크기까지 단일 `EventData` 인스턴스에 압축되어 보내집니다.

## <a name="custom-metadata-properties-for-output"></a>출력에 대한 사용자 지정 메타데이터 속성

쿼리 열을 사용자 속성으로 나가는 메시지에 첨부할 수 있습니다. 이러한 열은 페이로드로 이동하지 않습니다. 속성은 출력 메시지에서 사전 형식으로 제공됩니다. *키*는 열 이름이고 *값*은 속성 사전의 열 값입니다. 레코드 및 배열을 제외한 모든 Stream Analytics 데이터 형식이 지원됩니다.  

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
