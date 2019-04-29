---
title: Azure 데이터 탐색기 데이터 수집
description: Azure 데이터 탐색기에서 데이터를 수집(로드)할 수 있는 다양한 방법 알아보기
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 891d2acc42f8d6f03976f0553e2e3127bc6d16f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759341"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure 데이터 탐색기 데이터 수집

데이터 수집은 하나 이상의 원본에서 데이터 레코드를 로드하여 Azure 데이터 탐색기에서 테이블을 만들거나 업데이트하는 데 사용되는 프로세스입니다. 수집한 후에는 데이터를 쿼리에 사용할 수 있게 됩니다. 아래 다이어그램은 데이터 수집을 포함하여 Azure 데이터 탐색기 작업에 대한 종단 간 흐름을 보여줍니다.

![데이터 흐름](media/ingest-data-overview/data-flow.png)

데이터 수집을 담당하는 Azure 데이터 탐색기 데이터 관리 서비스는 다음과 같은 기능을 제공합니다.

1. **데이터 끌어오기**: 외부 원본(Event Hubs)에서 데이터를 끌어오거나 Azure 큐의 수집 요청을 읽습니다.

1. **일괄 처리**: 같은 데이터베이스와 테이블로 흐르는 데이터를 일괄 처리하여 수집 처리량을 최적화합니다.

1. **유효성 검사**: 필요한 경우 사전 유효성 검사 및 형식 변환을 수행합니다.

1. **데이터 조작**: 스키마 매칭, 데이터 구성, 인덱싱, 인코딩 및 압축을 수행합니다.

1. **수집 흐름의 지속성 지점**: 엔진의 수집 부하를 관리하고 일시적인 오류 시 다시 시도를 처리합니다.

1. **데이터 수집 커밋**: 데이터를 쿼리에 사용할 수 있도록 합니다.

## <a name="ingestion-methods"></a>수집 메서드

Azure 데이터 탐색기는 각각의 목표 시나리오, 장점 및 단점이 있는 여러 가지 수집 메서드를 지원합니다. Azure 데이터 탐색기는 공통 서비스에 대한 파이프라인 및 커넥터, SDK를 사용한 프로그래밍 방식 수집, 탐색용 엔진에 대한 직접 액세스를 제공합니다.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>파이프라인, 커넥터 및 플러그 인을 사용한 수집

Azure Data Explorer는 현재 다음을 지원합니다.

* Event Grid 파이프라인 - Azure Portal의 관리 마법사를 사용하여 관리할 수 있습니다. 자세한 내용은 [Azure Data Explorer에 Azure Blob 수집](ingest-data-event-grid.md)을 참조하세요.

* 이벤트 허브 파이프라인 - Azure Portal의 관리 마법사를 사용하여 관리할 수 있습니다. 자세한 내용은 [이벤트 허브에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)을 참조하세요.

* Logstash 플러그 인 - [Logstash에서 Azure Data Explorer로 데이터 수집](ingest-data-logstash.md)을 참조하세요.

* Kafka 커넥터 - [Kafka에서 Azure Data Explorer로 데이터 수집](ingest-data-kafka.md)을 참조하세요.

### <a name="ingestion-using-integration-services"></a>통합 서비스를 사용한 수집

* Azure 데이터 팩터리 (ADF)를 azure에서 사용 하 여 Azure 데이터 탐색기에서 데이터를 복사 하는 분석 워크 로드를 위한 완전히 관리 되는 데이터 통합 서비스 [지원 되는 데이터 저장소 및 형식](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)합니다. 자세한 내용은 [Azure 데이터 탐색기로 Azure Data Factory에서 데이터를 복사](/azure/data-explorer/data-factory-load-data)합니다.

### <a name="programmatic-ingestion"></a>프로그래밍 방식 수집

Azure 데이터 탐색기는 쿼리 및 데이터 수집에 사용할 수 있는 SDK를 제공합니다. 프로그래밍 방식 수집은 수집 프로세스 중 및 후에 저장소 트랜잭션을 최소화하여 수집 비용(COG)을 줄이도록 최적화됩니다.

**사용 가능한 SDK 및 오픈 소스 프로젝트**:

Kusto는 데이터를 수집하고 쿼리하는 데 사용할 수 있는 다음과 같은 클라이언트 SDK를 제공합니다.

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**프로그래밍 방식 수집 기술**:

* Azure 데이터 탐색기 데이터 관리 서비스를 통해 데이터 수집(높은 처리량 및 안정적인 수집):

    [**수집 일괄 처리**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample)(SDK에서 제공): 클라이언트는 Azure Blob Storage(Azure 데이터 탐색기 데이터 관리 서비스에서 지정)에 데이터를 업로드하고 Azure 큐에 알림을 게시합니다. 수집 일괄 처리는 안정적이고 저렴한 대용량 데이터 수집을 위해 권장되는 기술입니다.

* Azure 데이터 탐색기 엔진에 직접 데이터 수집(탐색 및 프로토타입에 가장 적합):

  * **인라인 수집**: 제어 명령은 (.ingest 인라인)에서 데이터가 포함 된 임시 테스트 목적으로 만들어졌습니다.

  * **쿼리에서 수집**: 쿼리 결과를 가리키는 제어 명령(.set, .set-or-append, .set-or-replace)이 보고서 또는 작은 임시 테이블을 생성하는 데 사용됩니다.

  * **스토리지에서 수집**: 데이터가 외부(예: Azure Blob Storage)에 저장되는 제어 명령(.ingest into)을 통해 효율적인 데이터 대량 수집이 가능합니다.

**다양한 메서드의 대기 시간**:

| 방법 | 대기 시간 |
| --- | --- |
| **인라인 수집** | 즉시 |
| **쿼리에서 수집** | 쿼리 시간 + 처리 시간 |
| **저장소에서 수집** | 다운로드 시간 + 처리 시간 |
| **큐에 대기된 수집** | 일괄 처리 시간 + 처리 시간 |
| |

처리 시간은 데이터 크기에 따라 달라지며 몇 초 미만입니다. 일괄 처리 시간의 기본값은 5분입니다.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>가장 적합한 수집 방법 선택

데이터 수집을 시작하기 전에 다음 사항을 고려해야 합니다.

* 데이터가 어디에 상주하나요? 
* 데이터 형식은 무엇이며 변경할 수 있나요? 
* 쿼리해야 하는 필수 필드는 무엇인가요? 
* 예상되는 데이터 볼륨과 개발 속도는 얼마인가요? 
* 예상되는 이벤트 유형의 수는 얼마인가요(테이블 개수로 반영됨)? 
* 이벤트 스키마에 예상되는 변경 빈도는 얼마인가요? 
* 데이터를 생성하는 노드 수는 얼마인가요? 
* 원본 OS는 무엇인가요? 
* 대기 시간 요구 사항은 무엇인가요? 
* 기존에 관리되는 수집 파이프라인 중 하나를 사용할 수 있나요? 

이벤트 허브와 같은 메시징 서비스 기반의 기존 인프라가 있는 조직의 경우 커넥터를 사용하는 것이 가장 적합한 솔루션일 수 있습니다. 큐에 대기된 수집은 큰 데이터 볼륨에 적합합니다.

## <a name="supported-data-formats"></a>지원되는 데이터 형식

쿼리에서 수집을 제외한 모든 수집 방법에 대해 Azure Data Explorer에서 구문 분석이 가능하도록 데이터 형식을 지정합니다. 지원되는 데이터 형식은 다음과 같습니다.

* CSV, TSV, PSV, SCSV, SOH
* JSON(구분된 줄, 여러 줄), Avro
* ZIP 및 GZIP 

> [!NOTE]
> 데이터가 수집될 때 목표 테이블 열을 기반으로 데이터 형식이 유추됩니다. 레코드가 불완전하거나 필수 데이터 형식으로 필드를 구문 분석할 수 없는 경우, 해당 테이블 열에는 Null 값이 채워집니다.

## <a name="ingestion-recommendations-and-limitations"></a>수집 권장 사항 및 제한 사항

* 수집된 데이터의 효과적인 보존 정책은 데이터베이스의 보존 정책에서 파생됩니다. 자세한 내용은 [보존 정책](/azure/kusto/concepts/retentionpolicy)을 참조하세요. 데이터를 수집하려면 **테이블 수집기** 또는 **데이터베이스 수집기** 권한이 필요합니다.
* 수집 가능한 최대 파일 크기는 5GB입니다. 100MB에서 1GB 사이의 파일을 수집하는 것이 좋습니다.

## <a name="schema-mapping"></a>스키마 매핑

스키마 매핑은 원본 데이터 필드를 대상 테이블 열에 바인딩하는 데 도움이 됩니다.

* [CSV 매핑](/azure/kusto/management/mappings?branch=master#csv-mapping)(선택 사항)은 모든 서수 기반 형식에서 작동합니다. 수집 명령 매개 변수를 사용하여 수행하거나 [테이블에 미리 만들고](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) 수집 명령 매개 변수에서 참조할 수 있습니다.
* [JSON 매핑](/azure/kusto/management/mappings?branch=master#json-mapping)(필수) 및 [Avro 매핑](/azure/kusto/management/mappings?branch=master#avro-mapping)(필수)은 ingest 명령 매개 변수를 사용하여 수행할 수 있습니다. [테이블에 미리 만들고](/azure/kusto/management/tables#create-ingestion-mapping) ingest 명령 매개 변수에서 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이벤트 허브에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Event Grid 구독을 사용하여 Azure Data Explorer로 데이터 수집](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Kafka에서 Azure Data Explorer로 데이터 수집](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer Python 라이브러리를 사용하여 데이터 수집](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer Node 라이브러리를 사용하여 데이터 수집](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Azure Data Explorer .NET Standard SDK(미리 보기)를 사용하여 데이터 수집](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Logstash에서 Azure Data Explorer로 데이터 수집](ingest-data-logstash.md)
