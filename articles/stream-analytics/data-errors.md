---
title: Azure Stream Analytics 진단 로그 데이터 오류
description: 이 문서는 다양 한 입력 및 Azure Stream Analytics를 사용 하는 경우 발생할 수 있는 출력 데이터 오류를 설명 합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ecc7077bf208adf1ac89adcce2f2e480ce34888e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329592"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics 데이터 오류

데이터 오류는 데이터를 처리 하는 동안 발생 하는 오류입니다.  이러한 오류는 대부분의 데이터 역직렬화를 직렬화 하는 동안 발생 하 고 쓰기 작업.  데이터 오류가 발생 하면 Stream Analytics 자세한 정보 및 이벤트의 예로 진단 로그에 기록 합니다.  일부 경우에도이 정보의 요약 포털 알림을 통해 제공 됩니다.

이 문서에서는 다양 한 오류 유형, 원인 및 입력 및 출력 데이터 오류에 대 한 진단 로그 세부 정보를 설명 합니다.

## <a name="diagnostic-log-schema"></a>진단 로그 스키마

참조 [진단 로그를 사용 하 여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) 진단 로그의 스키마를 확인 합니다. 다음 JSON에 대 한 예제 값은는 **속성** 데이터 오류에 대 한 진단 로그의 필드입니다.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>입력된 데이터 오류

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 원인: 선택한 압축 입력된 형식에는 데이터가 일치 하지 않습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 잘못 된 압축 유형을 포함 하 여 deserialization 오류를 사용 하 여 메시지 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 메시지 식별자를 입력 합니다. 이벤트 허브에 대 한 식별자가 PartitionId, 오프셋 및 시퀀스 번호입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 원인: 입력된 데이터의 헤더가 잘못 되었습니다. 예를 들어, CSV 이름이 중복 된 열이 있습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 잘못 된 헤더를 포함 하 여 deserialization 오류를 사용 하 여 메시지 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 메시지 식별자를 입력 합니다. 
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 원인: TIMESTAMP BY를 통해 또는 CREATE TABLE을 사용 하 여 정의 된 입력된 열이 존재 하지 않습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 누락 된 열을 사용 하 여 이벤트 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 메시지 식별자를 입력 합니다. 
   * 누락 된 열의 이름입니다. 
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 원인: CREATE TABLE 문에서 지정 된 형식에 대 한 입력을 변환할 수 없습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 이벤트 유형 변환 오류를 사용 하 여 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 메시지 식별자를 입력 합니다. 
   * 필요한 형식 및 열 이름입니다.

**오류 메시지**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 원인: 입력된 데이터가 올바른 형식이 아닙니다. 예를 들어, 입력에 유효한 JSON 하지 않습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 잘못 된 데이터 오류가 발생 했습니다. 이후의 메시지의 모든 이벤트는 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 메시지 식별자를 입력 합니다. 
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 원인: TIMESTAMP BY 식의 값을 datetime로 변환할 수 없습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 잘못 된 입력된 타임 스탬프를 사용 하 여 이벤트 입력에서 삭제 됩니다.
* 로그 세부 정보
   * 메시지 식별자를 입력 합니다. 
   * 오류 메시지입니다. 
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 원인: TIMESTAMP BY OVER timestampColumn의 값은 NULL입니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향: 입력에서 잘못 된 입력된 타임 스탬프 키를 가진 이벤트가 삭제 됩니다.
* 로그 세부 정보
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 원인: 응용 프로그램 시간과 도착 시간 사이의 차이 지연 도착 허용 시간 보다 큽니다.
* 포털 알림을 제공 합니다. 아닙니다.
* 진단 로그 수준: 정보
* 영향:  늦은 입력된 이벤트를 "처리" 다른 이벤트에 따라 처리는 이벤트 작업 구성의 섹션을 순서를 설정 합니다. 자세한 내용은 참조 [시간 처리 정책](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)합니다.
* 로그 세부 정보
   * 응용 프로그램 시간과 도착 시간입니다. 
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 원인: 응용 프로그램 시간과 도착 시간 사이의 차이가 5 분 보다 큽니다.
* 포털 알림을 제공 합니다. 아닙니다.
* 진단 로그 수준: 정보
* 영향:  초기 입력된 이벤트를 처리 하는 "다른 이벤트를 처리할"에 따라 작업 구성의 섹션 이벤트 순서를 설정 합니다. 자세한 내용은 참조 [시간 처리 정책](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)합니다.
* 로그 세부 정보
   * 응용 프로그램 시간과 도착 시간입니다. 
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 원인: 이벤트는 순서가 정의 잘못 된 순서 허용 시간에 따라 간주 됩니다.
* 포털 알림을 제공 합니다. 아닙니다.
* 진단 로그 수준: 정보
* 영향:  이벤트를 "처리" 다른 이벤트에 따라 처리 되는 순서가 설정 이벤트 순서 지정 작업 구성 섹션입니다. 자세한 내용은 참조 [시간 처리 정책](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)합니다.
* 로그 세부 정보
   * 몇 킬로바이트 최대 실제 페이로드입니다.

**오류 메시지**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>출력 데이터 오류

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 원인: 출력에 필요한 열이 존재 하지 않습니다. 예를 들어, Azure 테이블 PartitionKey 않는으로 정의 된 열이 존재 합니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향:  누락 필요한 열을 포함 하 여 모든 출력 데이터 변환 오류에 따라 처리 합니다 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정 합니다.
* 로그 세부 정보
   * 열 및 레코드 식별자 또는 레코드의 일부분의 이름입니다.

**오류 메시지**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 원인: 출력을 사용 하 여 열 값을 준수 하지 않습니다. 예를 들어 열 이름에는 유효한 Azure 표 열 하지 않습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향:  잘못 된 열 이름을 포함 하 여 모든 출력 데이터 변환 오류에 따라 처리 합니다 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정 합니다.
* 로그 세부 정보
   * 열 및 레코드 식별자 또는 레코드의 일부분의 이름입니다.

**오류 메시지**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 원인: 열 출력에 유효한 형식으로 변환할 수 없습니다. 예를 들어, 열의 값 제약 조건 또는 SQL 테이블에 정의 된 형식을 사용 하 여 호환 되지 않습니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향:  유형 변환 오류를 포함 하 여 모든 출력 데이터 변환 오류에 따라 처리 합니다 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정 합니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 원인: 메시지의 값이 지원 되는 출력 크기 보다 큽니다. 예를 들어, 레코드를 이벤트 허브 출력에 대 한 1MB 보다 큽니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향:  레코드 초과 크기 제한을 비롯 한 모든 출력 데이터 변환 오류에 따라 처리 합니다 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정 합니다.
* 로그 세부 정보
   * 레코드 식별자 또는 레코드의 일부입니다.

**오류 메시지**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 원인: 레코드는 시스템 열 이름이 같은 열에 이미 있습니다. 예를 들어 열을 사용 하 여 CosmosDB 출력 라는 다른 열에 ID 열이 ID입니다.
* 포털 알림을 제공 합니다. 예
* 진단 로그 수준: Warning
* 영향:  중복 키를 포함 하 여 모든 출력 데이터 변환 오류에 따라 처리 합니다 [출력 데이터 정책](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) 설정 합니다.
* 로그 세부 정보
   * 열의 이름입니다.
   * 레코드 식별자 또는 레코드의 일부입니다.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>다음 단계

* [진단 로그를 사용 하 여 Azure Stream Analytics 문제 해결](stream-analytics-job-diagnostic-logs.md)

* [Stream Analytics 작업 모니터링 및 쿼리를 모니터링 하는 방법 이해](stream-analytics-monitoring.md)
