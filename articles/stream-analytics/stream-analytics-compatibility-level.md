---
title: Azure Stream Analytics 작업의 호환성 수준 이해
description: Azure Stream Analytics 작업의 호환성 수준을 설정하는 방법과 최신 호환성 수준의 주요 변경 내용을 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: e4bbfdbcf7a295089570d4c8b77b07fd7270b3fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771710"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업의 호환성 수준

이 문서에서는 Azure Stream Analytics 호환성 수준 옵션을 설명 합니다. Stream Analytics는 정기적으로 기능이 업데이트 및 성능 향상을 사용 하 여 관리 되는 서비스입니다. 최종 사용자에 게 대부분의 서비스의 런타임 업데이트를 사용할 수 자동으로 있습니다. 

그러나 서비스에서 몇 가지 새로운 기능에는 기존 작업의 동작 변경과 같은 주요 변경 내용을 발생할 수 있습니다 또는 작업 실행 되는 방식으로 데이터를 변경 합니다. 설정 하면 호환성 수준에 맡기고 크게 변경 되지 않고 실행 하 여 기존 Stream Analytics 작업을 유지할 수 있습니다. 최신 런타임 동작에 대 한 준비가 되었을 때 수 옵트인 할 호환성 수준을 높여 합니다. 

## <a name="choose-a-compatibility-level"></a>호환성 수준을 선택합니다

호환성 수준은 스트림 분석 작업의 런타임 동작을 제어합니다. 

Azure Stream Analytics는 현재 세 가지 호환성 수준을 지원합니다.

* 1.0-기본 수준
* 1.1-현재 릴리스 동작
* 1.2 (미리 보기)-평가의 향상 된 최신 기능을 사용 하 여 최신 동작

1.0 호환성 수준 몇 년 전 Azure Stream Analytics의 일반 공급 때 도입 되었습니다.

새 Stream Analytics 작업을 만들면 최신 호환성 수준을 사용 하 여 만드는 것이 좋습니다. 나중에 추가 되는 변경 및 복잡성을 방지 하려면 최신 동작으로 신뢰 작업 디자인을 시작 합니다.

## <a name="set-the-compatibility-level"></a>호환성 수준 설정

Azure portal에서 또는 사용 하 여 Stream Analytics 작업에 대 한 호환성 수준을 설정할 수 있습니다 합니다 [REST API 호출 작업을 만들](/rest/api/streamanalytics/stream-analytics-job)합니다.

Azure portal에서 작업의 호환성 수준을 업데이트 합니다.

1. 사용 합니다 [Azure portal](https://portal.azure.com) 하 여 Stream Analytics 작업을 찾으려고 합니다.
2. **중지** 호환성 수준을 업데이트 하기 전에 작업 합니다. 작업이 실행 중인 상태에서는 호환성 수준을 업데이트할 수 없습니다.
3. 아래는 **구성** 을 선택 **호환성 수준**합니다.
4. 원하는 호환성 수준 값을 선택 합니다.
5. 선택 **저장할** 페이지의 맨 아래에 있습니다.

![Azure Portal의 Stream Analytics 호환성 수준](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

호환성 수준을 업데이트하면 T-SQL 컴파일러는 선택된 호환성 수준에 해당하는 구문을 사용하여 작업의 유효성을 검사합니다.

## <a name="compatibility-level-12"></a>호환성 수준 1.2

주요 변경 내용은 호환성 수준 1.2에에서 도입 된:

### <a name="geospatial-functions"></a>지리 공간적 함수

**이전 수준:** Azure Stream Analytics는 Geography 계산을 사용 합니다.

**1.2 수준:** Azure Stream Analytics를 사용 하면 기하학적 예상된 지역 좌표를 계산할 수 있습니다. 지리 공간적 함수 시그니처에 변하지가 않습니다. 그러나 해당 의미 체계는 약간 다를 이전 보다 더 정확한 계산을 허용 합니다.

Azure Stream Analytics는 지리 공간 참조 데이터 인덱싱을 지원합니다. 더 빠른 조인 계산에 대 한 지리 공간 요소를 포함 하는 참조 데이터를 인덱싱할 수 있습니다.

업데이트 된 지리 공간적 함수 텍스트 WKT (Well Known) 지리 공간 형식의 전체 표현을 제공합니다. GeoJson를 사용 하 여 이전에 지원 하지 않는 다른 지리 공간 구성 요소를 지정할 수 있습니다.

자세한 내용은 [Azure Stream Analytics-클라우드 및 IoT Edge에서에서 지리 공간 기능에 대 한 업데이트](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)합니다.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>여러 파티션이 있는 입력된 원본에 대 한 병렬 쿼리 실행

**이전 수준:** Azure Stream Analytics 쿼리 입력된 원본 파티션 간 쿼리 처리를 병렬 처리에 PARTITION BY 절을 사용을 하도록 설정 합니다.

**1.2 수준:** 입력된 원본 파티션 간 쿼리 논리를 병렬 처리할 수 하는 경우 Azure Stream Analytics는 별도 쿼리 인스턴스를 만들고 병렬로 계산을 실행 합니다.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 출력을 사용 하 여 네이티브 대량 API 통합

**이전 수준:** Upsert 동작이 *삽입 또는 병합*입니다.

**1.2 수준:** CosmosDB 출력을 사용 하 여 대량 API 통합을 기본 처리량을 최대화 하 고 효율적으로 제한 요청을 처리 합니다. 자세한 내용은 [Azure Cosmos DB 페이지에 Azure Stream Analytics 출력](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)합니다.

Upsert 동작은 *삽입 또는 바꾸기*합니다.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset SQL 출력을 쓸 때

**이전 수준:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 형식을 UTC로 조정 했습니다.

**1.2 수준:** DateTimeOffset은 더 이상 조정 됩니다.

### <a name="strict-validation-of-prefix-of-functions"></a>함수의 접두사의 엄격한 유효성 검사

**이전 수준:** 함수 접두사에 대 한 엄격한 유효성이 있었습니다.

**1.2 수준:** Azure Stream Analytics에 함수 접두사의 엄격한 유효성 검사 합니다. 기본 제공 함수에 접두사를 추가 하면 오류가 발생 합니다. 예를 들어`myprefix.ABS(…)` 지원 되지 않습니다.

오류가 발생도 기본 제공 집계에 접두사를 추가 합니다. 예를 들어 `myprefix.SUM(…)` 지원 되지 않습니다.

"System" 접두사를 사용 하 여 오류에서 사용자 정의 함수 결과 대 한 합니다.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB 출력 어댑터의 키 속성으로 배열 및 개체를 허용 하지 않습니다

**이전 수준:** 배열 및 개체 형식은 키 속성으로 지원 되었습니다.

**1.2 수준:** 키 속성으로 더 이상 배열 및 개체 형식은 사용할 수 없습니다.

## <a name="compatibility-level-11"></a>호환성 수준 1.1

호환성 수준 1.1의 주요 변경 내용은 다음과 같습니다.

### <a name="service-bus-xml-format"></a>Service Bus XML 형식

**1.0 수준:** Azure Stream Analytics에서 DataContractSerializer를 사용했기 때문에 메시지 내용에 XML 태그가 포함되었습니다. 예를 들면 다음과 같습니다.

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 수준:** 추가 태그 없이 메시지 내용에 스트림이 직접 포함됩니다. 예: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>필드 이름에 대 한 유지 대/소문자 구분

**1.0 수준:** Azure Stream Analytics 엔진에서 필드 이름이 처리될 때 소문자로 변경되었습니다.

**1.1 수준:** Azure Stream Analytics 엔진에서 처리 될 때 필드 이름에 대해 대/소문자 구분 유지 됩니다.

> [!NOTE]
> 지속적인 대/소문자 구분은 에지 환경을 사용하여 호스팅되는 Stream Analytic 작업에 아직 사용할 수 없습니다. 결과적으로, 작업이 에지에서 호스팅되는 경우 모든 필드 이름은 소문자로 변환됩니다.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 수준:** CREATE TABLE 명령은 FLOAT 열 형식이 NaN(숫자가 아님. 예: 무한대, -무한대)인 이벤트를 필터링하지 않습니다. 왜냐하면 이러한 이벤트는 이러한 숫자에 대해 문서화된 범위를 벗어나기 때문입니다.

**1.1 수준:** CREATE TABLE 명령을 사용하여 강력한 스키마를 지정할 수 있습니다. Stream Analytics 엔진은 데이터가 이 스키마를 준수하는지 확인합니다. 이 모델을 사용하면 이 명령으로 NaN 값을 사용하는 이벤트를 필터링할 수 있습니다.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>JSON에서 날짜/시간 문자열에 자동 업 캐스트를 사용 하지 않도록 설정

**1.0 수준:** JSON 파서가 자동으로 표준 시간대 정보를 사용하여 문자열 값을 날짜/시간 형식으로 업캐스트한 후 UTC로 변환합니다. 이 동작은 표준 시간대 정보가 손실 되었습니다.

**1.1 수준:** 더 이상 자동으로 표준 시간대 정보를 사용하여 문자열 값을 날짜/시간 형식으로 업캐스트하지 않습니다. 따라서 표준 시간대 정보가 유지됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 입력 문제 해결](stream-analytics-troubleshoot-input.md)
* [Stream Analytics 리소스 상태](stream-analytics-resource-health.md)
