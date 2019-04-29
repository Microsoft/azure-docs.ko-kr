---
title: Azure Stream Analytics 사용자 지정 Blob 출력 분할
description: 이 문서에서는 Azure Stream Analytics 작업의 Blob 스토리지 출력에 대한 사용자 지정 날짜/시간 경로 패턴과 사용자 지정 필드 또는 특성 기능을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771814"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics 사용자 지정 Blob 출력 분할

Azure Stream Analytics는 사용자 지정 필드 또는 특성과 사용자 지정 날짜/시간 경로 패턴을 이용한 사용자 지정 Blob 출력 분할을 지원합니다. 

## <a name="custom-field-or-attributes"></a>사용자 지정 필드 또는 특성

사용자 지정 필드 또는 입력 특성은 출력을 더 많이 제어하도록 허용하여 다운스트림 데이터 처리 및 보고 워크플로를 개선합니다.

### <a name="partition-key-options"></a>파티션 키 옵션

입력 데이터 분할에 사용되는 파티션 키 또는 열 이름에는 하이픈, 밑줄 및 공백이 있는 영숫자가 포함될 수 있습니다. 별칭과 함께 사용하지 않는 한 중첩 필드를 파티션 키로 사용하는 것은 불가능합니다.

### <a name="example"></a>예

한 작업이 외부 비디오 게임 서비스에 연결된 라이브 사용자 세션에서 입력 데이터를 가져오고, 수집된 데이터에 세션을 식별하기 위한 **client_id** 열이 포함되어 있다고 가정해 봅시다. 데이터를 **client_id**로 분할하기 위해, 작업을 만들 때 Blob 출력 속성에 **{client_id}** 파티션 토큰이 포함되도록 Blob 경로 패턴 필드를 설정합니다. 다양한 **client_id** 값을 갖는 데이터가 Stream Analytics 작업을 통해 흐르면 폴더별 단일 **client_id** 값에 따라 별도의 폴더에 출력 데이터가 저장됩니다.

![클라이언트 id를 사용하는 경로 패턴](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

마찬가지로, 작업 입력이 수백만 개의 센서에서 얻은 센서 데이터이고, 각 센서에 **sensor_id**가 있는 경우 각 센서 데이터를 서로 다른 폴더에 분할할 수 있도록 경로 패턴은 **{sensor_id}** 입니다.  


REST API를 사용하면 해당 요청에 사용되는 JSON 파일의 출력 섹션은 다음과 비슷합니다.  

![REST API 출력](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

작업이 실행되기 시작하면 *클라이언트* 컨테이너는 다음과 비슷하게 보입니다.  

![클라이언트 컨테이너](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

각 폴더에 여러 Blob이 포함될 수 있으며, 각 Blob에는 하나 이상의 레코드가 포함됩니다. 위의 예제에서 "06000000" 레이블이 지정된 폴더에는 단일 Blob이 있으며 그 콘텐츠는 다음과 같습니다.

![Blob 콘텐츠](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Blob의 각 레코드에는 폴더 이름과 일치하는 **client_id** 열이 있습니다. 출력 경로의 출력을 분할하는 데 사용된 열이 **client_id**이기 때문입니다.

### <a name="limitations"></a>제한 사항

1. 경로 패턴 Blob 출력 속성에는 오직 하나의 사용자 지정 파티션 키만 허용됩니다. 다음 경로 패턴이 모두 유효합니다.

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}  

2. 파티션 키는 대/소문자를 구분하지 않으므로 "John"과 "john"은 동일한 파티션 키입니다. 또한 식을 파티션 키로 사용할 수 없습니다. 예를 들어 **{columnA + columnB}** 는 작동하지 않습니다.  

3. 파티션 키 카디널리티 8000 미만의 레코드로 입력 스트림이 구성되면 레코드가 기존 Blob에 추가되고 필요할 때만 새 Blob을 만듭니다. 카디널리티가 8000을 넘으면 기존 Blob이 작성되고 파티션 키가 동일한 임의의 수의 레코드에 대한 새 Blob이 생성된다는 보장이 없습니다.  

## <a name="custom-datetime-path-patterns"></a>사용자 지정 날짜/시간 경로 패턴

사용자 지정 날짜/시간 경로 패턴을 사용하면 Hive 스트리밍 규칙을 사용하여 정렬하는 출력 형식을 지정하여 Azure Stream Analytics에서 다운스트림 처리를 위해 Azure Databricks 및 Azure HDInsight에 데이터를 보낼 수 있는 기능을 부여할 수 있습니다. 사용자 지정 날짜/시간 경로 패턴은 서식 지정자와 함께 Blob 출력의 경로 접두사 필드에서 `datetime` 키워드를 사용하여 쉽게 구현됩니다. 예: `{datetime:yyyy}`.

### <a name="supported-tokens"></a>지원되는 토큰

다음 서식 지정자 토큰은 사용자 지정 날짜/시간 형식을 설정하기 위해 단독 또는 함께 사용할 수 있습니다.

|서식 지정자   |설명   |예제 시간 2018-01-02T10:06:08에 대한 결과|
|----------|-----------|------------|
|{datetime:yyyy}|연도(4자리 숫자)|2018|
|{datetime:MM}|월(01-12)|01|
|{datetime:M}|월(1-12)|1|
|{datetime:dd}|일(01-31)|02|
|{datetime:d}|일(1-12)|2|
|{datetime:HH}|시간(00-23의 24시간 형식)|10|
|{datetime:mm}|분(00-24)|06|
|{datetime:m}|분(0-24)|6|
|{datetime:ss}|초(00-60)|08|

사용자 지정 날짜/시간 패턴을 사용하려는 경우 {date} 및/또는 {time} 토큰을 경로 접두사에 추가하여 기본 제공 날짜/시간 서식을 사용하여 드롭다운을 생성할 수 있습니다.

![Stream Analytics 이전 날짜/시간 서식](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>확장성 및 제한 사항

경로 접두사 문자 제한에 도달할 때까지 경로 패턴에서 원하는 만큼 `{datetime:<specifier>}` 토큰을 사용할 수 있습니다. 서식 지정자는 날짜 및 시간 드롭다운 목록에서 이미 나열된 조합 이외의 단일 토큰 내에서 결합할 수 없습니다. 

`logs/MM/dd` 경로 파티션의 경우:

|유효한 식   |잘못된 식   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

경로 접두사에서 동일한 서식 지정자를 여러 번 사용할 수 있습니다. 토큰은 매번 반복되어야 합니다.

### <a name="hive-streaming-conventions"></a>Hive 스트리밍 규칙

Blob Storage에 대한 사용자 지정 경로 패턴은 Hive 스트리밍 규칙에서 사용될 수 있습니다. 폴더 이름에서 `column=`으로 레이블이 지정된 폴더가 생성됩니다.

예: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

사용자 지정 출력은 테이블을 변경하고 파티션을 수동으로 추가하는 번거로움을 없애서 Azure Stream Analytics와 Hive 간에 데이터를 전송합니다. 대신, 다음을 사용하여 자동으로 여러 폴더를 추가할 수 있습니다.

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>예

[Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) 빠른 시작 가이드에 따라 저장소 계정, 리소스 그룹, Stream Analytics 작업 및 입력 원본을 만듭니다. 빠른 시작 가이드에서 사용된 동일한 샘플 데이터를 사용하며 [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)에서도 사용 가능합니다.

다음 구성을 사용하여 Blob 출력 싱크를 만듭니다.

![Stream Analytics 생성 Blob 출력 싱크](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

전체 경로 패턴은 다음과 같습니다.


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


작업을 시작하는 경우 경로 패턴에 따라 폴더 구조는 Blob 컨테이너에서 생성됩니다. 일 수준으로 드릴다운할 수 있습니다.

![사용자 지정 경로 패턴을 사용한 Stream Analytics Blob 출력](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)
