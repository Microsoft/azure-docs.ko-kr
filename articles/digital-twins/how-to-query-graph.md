---
title: 쌍 그래프 쿼리
titleSuffix: Azure Digital Twins
description: 자세한 내용은 Azure Digital Twins 쌍 그래프를 쿼리 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e6236d9ed5ed75b6b5e10914e668de545c48fc2c
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055637"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Azure Digital Twins 쌍 그래프 쿼리

이 문서에서는 [Azure Digital Twins 쿼리 언어](concepts-query-language.md) 를 사용 하 여 정보에 대 한 쌍 [그래프](concepts-twins-graph.md) 를 쿼리 하는 방법에 대 한 예제 및 자세한 정보를 제공 합니다. Azure Digital Twins [**쿼리 api**](how-to-use-apis-sdks.md)를 사용 하 여 그래프에서 쿼리를 실행 합니다.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

이 문서의 나머지 부분에서는 이러한 작업을 사용 하는 방법에 대 한 예제를 제공 합니다.

## <a name="query-syntax"></a>쿼리 구문

이 섹션에는 쿼리 언어 구조를 설명 하 고 [디지털](concepts-twins-graph.md)쌍에서 가능한 쿼리 작업을 수행 하는 예제 쿼리가 포함 되어 있습니다.

### <a name="select-top-items"></a>상위 항목 선택

절을 사용 하 여 쿼리에서 여러 "최상위" 항목을 선택할 수 있습니다 `Select TOP` .

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="query-by-property"></a>속성으로 쿼리

**속성** (ID 및 메타 데이터 포함)으로 디지털 쌍을 가져옵니다.
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> 디지털 쌍의 ID는 메타 데이터 필드를 사용 하 여 쿼리 됩니다 `$dtId` .

**특정 속성이 정의 되어 있는지 여부**에 따라 쌍을 가져올 수도 있습니다. 다음은 정의 된 *Location* 속성을 가진 쌍를 가져오는 쿼리입니다.

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

그러면 태그를 [디지털 쌍에 추가](how-to-use-tags.md)에 설명 된 대로 *태그* 속성에 따라 쌍을 가져오는 데 도움이 될 수 있습니다. *Red*로 태그가 지정 된 모든 쌍를 가져오는 쿼리는 다음과 같습니다.

```sql
select * from digitaltwins where is_defined(tags.red) 
```

**속성의 형식**에 따라 쌍을 가져올 수도 있습니다. *온도* 속성이 숫자 인 쌍를 가져오는 쿼리는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>모델 별로 쿼리

`IS_OF_MODEL`연산자를 사용 하 여 쌍의 [**모델**](concepts-models.md)을 기준으로 필터링 할 수 있습니다. 상속을 지원 하 고 여러 오버 로드 옵션을 포함 합니다.

를 사용 하는 가장 간단한 방법은 매개 변수를 사용 하는 것 `IS_OF_MODEL` `twinTypeName` `IS_OF_MODEL(twinTypeName)` 입니다.
이 매개 변수에 값을 전달 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

가 사용 되는 경우와 같이 두 개 이상 있을 때 검색할 쌍 컬렉션을 지정 하려면 `JOIN` `twinCollection` 매개 변수를 추가 `IS_OF_MODEL(twinCollection, twinTypeName)` 합니다.
이 매개 변수에 대 한 값을 추가 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

정확히 일치 하는 항목을 수행 하려면 `exact` 매개 변수를 추가 `IS_OF_MODEL(twinTypeName, exact)` 합니다.
이 매개 변수에 대 한 값을 추가 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

세 인수를 모두 함께 전달할 수도 있습니다 `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
세 매개 변수 모두에 대해 값을 지정 하는 쿼리 예제는 다음과 같습니다.

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>관계를 기반으로 하는 쿼리

디지털 쌍의 **관계**를 기반으로 쿼리 하는 경우 Azure Digital twins 쿼리 언어에는 특별 한 구문이 있습니다.

관계는 절의 쿼리 범위로 끌어옵니다 `FROM` . "기존" SQL 형식 언어와의 중요 한 차이점은이 절의 각 식이 테이블이 아니라는 것입니다 .이 `FROM` `FROM` 절은 엔터티 간 관계 순회를 표현 하 고 Azure Digital twins 버전의를 사용 하 여 작성 됩니다 `JOIN` . 

Azure Digital Twins [모델](concepts-models.md) 기능을 사용 하는 경우 관계는 twins와 독립적으로 존재 하지 않습니다. 즉, 여기에서 관계는 독립적으로 쿼리할 수 없으며 쌍에 연결 되어야 하므로 Azure Digital Twins 쿼리 언어는 `JOIN` 일반 SQL과 약간 다릅니다 `JOIN` .
이러한 차이를 반영 하기 위해 키워드는 `RELATED` 절에서 쌍의 `JOIN` 관계 집합을 참조 하는 데 사용 됩니다. 

다음 섹션에서는이 처럼 표시 되는 몇 가지 예를 제공 합니다.

> [!TIP]
> 개념적으로이 기능은 `JOIN` 문서 내의 자식 개체에 대해 수행할 수 있는 CosmosDB의 문서 중심 기능을 모방 합니다. CosmosDB는 키워드를 사용 하 여 `IN` `JOIN` 이 현재 컨텍스트 문서 내의 배열 요소를 반복 하도록 지정 합니다.

#### <a name="relationship-based-query-examples"></a>관계 기반 쿼리 예제

관계를 포함 하는 데이터 집합을 가져오려면 단일 `FROM` 문 뒤에 N `JOIN` 문을 사용 `JOIN` 합니다. 여기서 문은 이전 또는 문의 결과에 대 한 관계를 표시 합니다 `FROM` `JOIN` .

다음은 관계 기반 쿼리 예제입니다. 이 코드 조각은 *ID* 속성이 ' ABC ' 인 모든 디지털 쌍을 선택 하 고, *포함* 관계를 통해 이러한 디지털 쌍과 관련 된 모든 digital 쌍를 선택 합니다. 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> 개발자는이를 `JOIN` 절의 키 값과 상관 관계를 지정 하지 않아도 `WHERE` 됩니다 (또는 정의를 사용 하 여 인라인 키 값 지정 `JOIN` ). 관계 속성 자체가 대상 엔터티를 식별 하므로이 상관 관계는 시스템에 의해 자동으로 계산 됩니다.

#### <a name="query-the-properties-of-a-relationship"></a>관계 속성 쿼리

디지털 쌍이 DTDL을 통해 설명 하는 속성을 갖는 방식과 마찬가지로 관계에도 속성이 있을 수 있습니다. **해당 관계의 속성에 따라**쌍를 쿼리할 수 있습니다.
Azure Digital Twins 쿼리 언어를 사용 하면 절 내의 관계에 별칭을 할당 하 여 관계를 필터링 하 고 투영할 수 있습니다 `JOIN` . 

예를 들어 *reportedCondition* 속성이 있는 *servicedBy* relationship을 고려 합니다. 아래 쿼리에서는 속성을 참조 하기 위해이 관계에 ' R '의 별칭이 지정 됩니다.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

위의 예제에서 *reportedCondition* 는 *servicedBy* 관계 자체의 속성 ( *servicedBy* 관계가 있는 일부 디지털 쌍이 아님)을 확인 합니다.

### <a name="query-with-multiple-joins"></a>여러 조인이 있는 쿼리

현재 미리 보기 상태인 단일 쿼리에서 최대 5 개까지 `JOIN` 지원 됩니다. 이렇게 하면 여러 수준의 관계를 한 번에 트래버스할 수 있습니다.

다음은 대화방 1과 2의 라이트 패널에 포함 된 모든 라이트 전구을 가져오는 다중 조인 쿼리의 예입니다.

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1') 
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1') 
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="other-compound-query-examples"></a>기타 복합 쿼리 예제

조합 연산자를 사용 하 여 위의 쿼리 유형을 조합 하 여 단일 쿼리에 자세한 정보를 **포함할 수 있습니다** . 다음은 한 번에 둘 이상의 쌍 설명자 유형을 쿼리 하는 복합 쿼리의 몇 가지 추가 예입니다.

| Description | 쿼리 |
| --- | --- |
| *공간 123* 가 있는 장치에서 운영자 역할을 하는 MxChip 장치를 반환 합니다. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| ID가 *id1* 인 다른 쌍이 *포함* 된 relationship 이라는 관계가 있는 쌍을 가져옵니다. | `SELECT Room`<br>`FROM DIGITIALTWINS Room`<br>`JOIN Thermostat ON Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| *Floor11* 에 포함 된이 대화방 모델의 모든 대화방 가져오기 | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="run-queries-with-an-api-call"></a>API 호출을 사용 하 여 쿼리 실행

쿼리 문자열을 결정 했으면 **쿼리 API**를 호출 하 여 실행 합니다.
다음 코드 조각에서는 클라이언트 앱에서이 호출을 보여 줍니다.

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

이 호출은 QueryResult 개체 형식으로 쿼리 결과를 반환 합니다. 

쿼리 호출은 페이징을 지원 합니다. 오류 처리 및 페이징이 포함 된 전체 예제는 다음과 같습니다.

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>쿼리 제한 사항

인스턴스의 변경 내용이 쿼리에 반영 될 때까지 최대 10 초가 지연 될 수 있습니다. 예를 들어 DigitalTwins API를 사용 하 여 쌍 생성 또는 삭제와 같은 작업을 완료 하는 경우 쿼리 API 요청에 결과가 즉시 반영 되지 않을 수 있습니다. 짧은 기간 동안에는 문제를 해결 하기에 충분 해야 합니다.

미리 보기 중에는를 사용 하는 경우 추가 제한 사항이 있습니다 `JOIN` .
* 문 내에서 하위 쿼리를 지원 하지 않습니다 `FROM` .
* `OUTER JOIN` 의미 체계가 지원 되지 않습니다. 즉, 관계의 순위가 0 이면 전체 "row"가 출력 결과 집합에서 제거 됩니다.
* 미리 보기 중에는 그래프 트래버스 깊이가 쿼리당 5 개 수준으로 제한 됩니다 `JOIN` .
* 작업의 소스가 `JOIN` 제한 되어 있습니다. 쿼리는 쿼리가 시작 되는 위치를 선언 해야 합니다.

## <a name="query-best-practices"></a>쿼리 모범 사례

Azure Digital Twins를 사용 하 여 쿼리 하는 몇 가지 팁은 다음과 같습니다.

* 모델 디자인 단계 중에 쿼리 패턴을 고려 합니다. 단일 쿼리에서 응답 해야 하는 관계가 단일 수준 관계로 모델링 되는지 확인 합니다.
* 그래프 순회에서 많은 결과 집합을 방지 하는 방식으로 속성을 디자인 합니다.
* 쌍의 배열을 작성 하 고 연산자를 사용 하 여 쿼리 하는 데 필요한 쿼리 수를 크게 줄일 수 있습니다 `IN` . 예를 들어 *건물* 에 *층* 이 포함 되어 있고 *층* 이 *방을*포함 하는 시나리오를 가정해 보겠습니다. 핫의 건물 내에서 방을 검색 하려면 다음을 수행할 수 있습니다.

    1. 관계에 따라 건물에서 층 찾기 `contains`
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. 한 층을 하나씩 고려 하 고 쿼리를 실행 `JOIN` 하 여 각 항목에 대 한 방을 찾는 대신 회의실을 찾으려면 빌딩에서 층의 컬렉션을 사용 하 여 쿼리할 수 있습니다 (아래 쿼리에서 *바닥* 이라고 명명).

        클라이언트 앱에서:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        쿼리:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* 속성 이름과 값은 대/소문자를 구분 하므로 모델에 정의 된 정확한 이름을 사용 해야 합니다. 속성 이름의 철자가 틀렸거나 잘못 된 경우에는 오류가 반환 되지 않고 결과 집합이 비어 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 쿼리를 실행 하는 데 사용 되는 쿼리 API를 포함 하 여 [Azure Digital Twins api 및 sdk](how-to-use-apis-sdks.md)에 대해 자세히 알아보세요.
