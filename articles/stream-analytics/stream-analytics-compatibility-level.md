---
title: Azure Stream Analytics 호환성 수준
description: Azure Stream Analytics 작업의 호환성 수준을 설정하는 방법과 최신 호환성 수준의 주요 변경 내용을 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 14b04f7d6068863c79c7060d29b58232be1f40cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201801"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업의 호환성 수준

이 문서에서는 Azure Stream Analytics의 호환성 수준 옵션을 설명 합니다. Stream Analytics은 정기적인 기능 업데이트 및 성능 향상을 포함 하는 관리 되는 서비스입니다. 대부분의 서비스 런타임 업데이트는 최종 사용자가 자동으로 사용할 수 있게 됩니다. 

그러나 서비스의 일부 새로운 기능은 기존 작업의 동작에 대 한 변경 이나 실행 중인 작업에서 데이터가 사용 되는 방식 변경 등의 주요 변경 사항을 도입할 수 있습니다. 호환성 수준 설정을 낮추면 기존 Stream Analytics 작업을 중요 변경 없이 계속 실행할 수 있습니다. 최신 런타임 동작을 수행할 준비가 되 면 호환성 수준을 높여 옵트인 (opt in) 할 수 있습니다. 

## <a name="choose-a-compatibility-level"></a>호환성 수준 선택

호환성 수준은 스트림 분석 작업의 런타임 동작을 제어합니다. 

현재 Azure Stream Analytics는 다음과 같은 세 가지 호환성 수준을 지원 합니다.

* 1.0-이전 동작
* 1.1-기본 동작
* 1.2-최신 개선 사항에 대 한 최신 동작

원래 1.0 호환성 수준은 몇 년 전 Azure Stream Analytics의 일반 공급 중에 도입 되었습니다.

새 Stream Analytics 작업을 만들 때 최신 호환성 수준을 사용 하 여 만드는 것이 가장 좋습니다. 나중에 변경 및 복잡성이 추가 되지 않도록 하기 위해 최신 동작에 의존 하는 작업 설계를 시작 합니다.

## <a name="set-the-compatibility-level"></a>호환성 수준 설정

Azure Portal에서 Stream Analytics 작업에 대 한 호환성 수준을 설정 하거나 [작업 만들기 REST API 호출](/rest/api/streamanalytics/stream-analytics-job)을 사용 하 여 설정할 수 있습니다.

Azure Portal에서 작업의 호환성 수준을 업데이트 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 를 사용 하 여 Stream Analytics 작업을 찾습니다.
2. 호환성 수준을 업데이트 하기 전에 작업을 **중지** 합니다. 작업이 실행 중인 상태에서는 호환성 수준을 업데이트할 수 없습니다.
3. **구성** 제목 아래에서 **호환성 수준**을 선택 합니다.
4. 원하는 호환성 수준 값을 선택 합니다.
5. 페이지 맨 아래에서 **저장** 을 선택 합니다.

![Azure Portal의 Stream Analytics 호환성 수준](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

호환성 수준을 업데이트할 때 T-컴파일러는 선택한 호환성 수준에 해당 하는 구문을 사용 하 여 작업의 유효성을 검사 합니다.

## <a name="compatibility-level-12"></a>호환성 수준 1.2

호환성 수준 1.2에서 도입 된 주요 변경 내용은 다음과 같습니다.

###  <a name="amqp-messaging-protocol"></a>AMQP 메시징 프로토콜

**1.2 level**: Azure Stream Analytics는 [Amqp (Advanced Message queuing protocol)](../service-bus-messaging/service-bus-amqp-overview.md) 메시징 프로토콜을 사용 하 여 Service Bus 큐 및 토픽에 기록 합니다. AMQP를 사용하여 여러 플랫폼 간에 개방형 표준 프로토콜을 사용하는 하이브리드 애플리케이션을 빌드할 수 있습니다.

### <a name="geospatial-functions"></a>지리 공간적 함수

**이전 수준:** 지리 계산을 사용 Azure Stream Analytics.

**1.2 수준:** Azure Stream Analytics를 사용 하면 기하학적 프로젝션 지역 좌표를 계산할 수 있습니다. 지리 공간적 함수의 서명은 변경 되지 않습니다. 그러나 해당 의미 체계가 약간 다르므로 이전 보다 더 정확한 계산을 수행할 수 있습니다.

Azure Stream Analytics 지리 공간적 참조 데이터 인덱싱을 지원 합니다. 더 빠른 조인 계산을 위해 지리 공간적 요소를 포함 하는 참조 데이터를 인덱싱할 수 있습니다.

업데이트 된 지리 공간적 함수는 잘 알려진 텍스트 (WKT) 지리 공간적 형식의 전체 표현을를 가져옵니다. GeoJson에서 이전에 지원 되지 않는 다른 지리 공간적 구성 요소를 지정할 수 있습니다.

자세한 내용은 [Azure Stream Analytics – Cloud 및 IoT Edge의 지리 공간적 기능 업데이트](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)를 참조 하세요.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>여러 파티션이 있는 입력 원본에 대 한 병렬 쿼리 실행

**이전 수준:** Azure Stream Analytics 쿼리는 PARTITION BY 절을 사용 하 여 입력 원본 파티션 간에 쿼리 처리를 병렬화 해야 했습니다.

**1.2 수준:** 쿼리 논리를 입력 원본 파티션 간에 병렬화 할 수 있는 경우 Azure Stream Analytics는 별도의 쿼리 인스턴스를 만들고 계산을 병렬로 실행 합니다.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 출력과 네이티브 대량 API 통합

**이전 수준:** Upsert 동작이 *insert 또는 merge*입니다.

**1.2 수준:** CosmosDB 출력과 네이티브 대량 API를 통합 하면 처리량이 최대화 되 고 제한 요청이 효율적으로 처리 됩니다. 자세한 내용은 [Azure Cosmos DB에 출력 Azure Stream Analytics 페이지](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)를 참조 하세요.

Upsert 동작은 *insert 또는 replace*입니다.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>SQL 출력에 쓸 때 DateTimeOffset

**이전 수준:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 형식은 UTC로 조정 되었습니다.

**1.2 수준:** DateTimeOffset은 더 이상 조정 되지 않습니다.

### <a name="long-when-writing-to-sql-output"></a>SQL 출력에 쓰는 경우 Long

**이전 수준:** 대상 유형에 따라 값이 잘렸습니다.

**1.2 수준:** 대상 형식에 맞지 않는 값은 출력 오류 정책에 따라 처리 됩니다.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>SQL 출력에 쓸 때의 레코드 및 배열 serialization

**이전 수준:** 레코드는 "Record"로 작성 되었으며 배열은 "Array"로 작성 되었습니다.

**1.2 수준:** 레코드와 배열은 JSON 형식으로 직렬화 됩니다.

### <a name="strict-validation-of-prefix-of-functions"></a>함수 접두사의 엄격한 유효성 검사

**이전 수준:** 함수 접두사의 엄격한 유효성 검사는 없었습니다.

**1.2 수준:** Azure Stream Analytics는 함수 접두사의 엄격한 유효성 검사를 수행 합니다. 기본 제공 함수에 접두사를 추가 하면 오류가 발생 합니다. 예를 들어`myprefix.ABS(…)` 지원 되지 않습니다.

기본 제공 집계에 접두사를 추가 하면 오류가 발생 하기도 합니다. 예를 들어 `myprefix.SUM(…)` 지원 되지 않습니다.

사용자 정의 함수에 "system" 접두사를 사용 하면 오류가 발생 합니다.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB 출력 어댑터에서 배열 및 개체를 키 속성으로 허용 안 함

**이전 수준:** 배열 및 개체 형식은 키 속성으로 지원 됩니다.

**1.2 수준:** 배열 및 개체 형식은 더 이상 키 속성으로 지원 되지 않습니다.

## <a name="compatibility-level-11"></a>호환성 수준 1.1

호환성 수준 1.1의 주요 변경 내용은 다음과 같습니다.

### <a name="service-bus-xml-format"></a>Service Bus XML 형식

**1.0 수준:** DataContractSerializer를 사용 Azure Stream Analytics 메시지 내용에 XML 태그가 포함 되었습니다. 다음은 그 예입니다.

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 수준:** 메시지 콘텐츠는 추가 태그 없이 스트림을 직접 포함 합니다. 예: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>필드 이름에 대 한 대/소문자 구분 유지

**1.0 수준:** Azure Stream Analytics 엔진에서 처리 될 때 필드 이름이 소문자로 변경 되었습니다.

**1.1 수준:** Azure Stream Analytics 엔진에서 처리 하는 경우 필드 이름에 대 한 대/소문자 구분이 유지 됩니다.

> [!NOTE]
> 지속적인 대/소문자 구분은 에지 환경을 사용하여 호스팅되는 Stream Analytic 작업에 아직 사용할 수 없습니다. 결과적으로, 작업이 에지에서 호스팅되는 경우 모든 필드 이름은 소문자로 변환됩니다.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 수준:** CREATE TABLE 명령에서 NaN (숫자가 아님)을 사용 하 여 이벤트를 필터링 하지 않았습니다. 왜냐하면 이러한 이벤트는 이러한 숫자에 대해 문서화된 범위를 벗어나기 때문입니다.

**1.1 수준:** CREATE TABLE를 사용 하면 강력한 스키마를 지정할 수 있습니다. Stream Analytics 엔진은 데이터가 이 스키마를 준수하는지 확인합니다. 이 모델을 사용하면 이 명령으로 NaN 값을 사용하는 이벤트를 필터링할 수 있습니다.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>JSON에서 datetime 문자열에 대해 자동 업 캐스트 사용 안 함

**1.0 수준:** JSON 파서는 자동으로 날짜/시간/영역 정보를 포함 하는 문자열 값을 DateTime 형식으로 업 캐스트 다음 UTC로 변환 합니다. 이 동작으로 인해 표준 시간대 정보가 손실 되었습니다.

**1.1 수준:** 날짜/시간/영역 정보를 포함 하는 문자열 값을 DateTime 형식으로 자동으로 업 캐스트 수 없습니다. 따라서 표준 시간대 정보가 유지됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 입력 문제 해결](stream-analytics-troubleshoot-input.md)
* [Stream Analytics 리소스 상태](stream-analytics-resource-health.md)
