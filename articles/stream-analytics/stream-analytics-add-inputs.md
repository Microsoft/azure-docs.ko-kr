---
title: Azure Stream Analytics의 입력 이해
description: 이 문서에서는 Azure Stream Analytics 작업의 입력 개념에 대해 설명하고, 스트리밍 입력을 참조 데이터 입력과 비교해 보겠습니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 87e260c97a748807929a0e7021e3efb2ae8f8e7b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329295"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Azure Stream Analytics의 입력 이해

Azure Stream Analytics 작업은 하나 이상의 데이터 입력에 연결됩니다. 각 입력은 기존 데이터 원본에 대한 연결을 정의합니다. Stream Analytics는 Event Hubs, IoT Hub, Blob Storage를 포함한 여러 종류의 이벤트 원본에서 들어오는 데이터를 수용합니다. 입력은 각 작업에 대해 작성하는 스트리밍 SQL 쿼리에서 이름으로 참조됩니다. 쿼리에서 여러 입력을 조인하여 데이터를 혼합하거나 스트리밍 데이터를 참조 데이터 조회와 비교하고 그 결과를 출력으로 전달할 수 있습니다. 

Stream Analytics는 입력으로 세 가지 리소스를 사용 하 여 최고 수준의 통합:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

이러한 입력된 리소스는 다른 구독 또는 Stream Analytics 작업과 동일한 Azure 구독에 있을 수 있습니다.

사용할 수는 [Azure portal](stream-analytics-quick-create-portal.md#configure-job-input)를 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput)를 [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)를 [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), 및 [Visual Studio](stream-analytics-tools-for-visual-studio-install.md)만들기, 편집 및 Stream Analytics 작업 입력을 테스트 합니다.

## <a name="stream-and-reference-inputs"></a>스트림 및 참조 입력
데이터가 해당 데이터 원본에 푸시되면 Stream Analytics 작업에서 사용되고 실시간으로 처리됩니다. 입력은 데이터 스트림 입력과 참조 데이터 입력의 두 가지 형식으로 나뉩니다.

### <a name="data-stream-input"></a>데이터 스트림 입력
데이터 스트림은 시간이 지남에 따라 생성되는 이벤트의 제한 없는 시퀀스입니다. Stream Analytics 작업은 하나 이상의 데이터 스트림 입력을 포함해야 합니다. Event Hubs, IoT Hub 및 Blob Storage는 데이터 스트림 입력 원본으로 지원됩니다. 이벤트 허브는 여러 디바이스 및 서비스에서 이벤트 스트림을 수집하는 데 사용됩니다. 이러한 스트림은 소셜 미디어 활동 피드, 주식 거래 정보 또는 센서 데이터를 포함할 수 있습니다. IoT Hub는 IoT(사물 인터넷) 시나리오에서 연결된 디바이스로부터 데이터를 수집하는 데 최적화됩니다.  Blob Storage를 로그 파일과 같은 스트림으로 대량 데이터 수집을 위한 입력 원본으로 사용할 수 있습니다.  

데이터 입력 스트리밍에 대한 자세한 내용은 [Stream Analytics에 입력으로 데이터 스트리밍](stream-analytics-define-inputs.md)을 참조하세요.

### <a name="reference-data-input"></a>참조 데이터 입력
Stream Analytics은 *참조 데이터*라는 입력도 지원합니다. 참조 데이터는 완전히 정적이거나 서서히 변경됩니다. 일반적으로 상관 관계 및 조회를 수행하는 데 사용됩니다. 예를 들어 정적 값을 조회하기 위해 SQL 조인을 수행하기는 하지만 데이터 스트림 입력의 데이터를 참조 데이터의 데이터로 조인할 수 있습니다. 현재 azure Blob storage 및 Azure SQL Database는 참조 데이터용 입력 소스로 지원 됩니다. 참조 데이터 원본 blob는 쿼리 복잡성에 따라 크기가 300MB 최대 한도가 및 스트리밍 단위를 할당 (참조를 [크기 제한](stream-analytics-use-reference-data.md#size-limitation) 참조 데이터에 대 한 자세한 설명서의 섹션).

참조 데이터 입력에 대한 자세한 내용은 [Stream Analytics에서 조회에 대한 참조 데이터 사용](stream-analytics-use-reference-data.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
