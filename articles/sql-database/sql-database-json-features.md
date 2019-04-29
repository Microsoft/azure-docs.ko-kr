---
title: Azure SQL Database에서 JSON 데이터 사용 | Microsoft Docs
description: Azure SQL Database에서는 JSON(JavaScript Object Notation) 표기법으로 데이터 구문 분석, 쿼리 및 서식 지정을 수행할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 77f6125980c43817230b8a8d4beb32757f23e6c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702970"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Azure SQL Database의 JSON 기능 시작
Azure SQL Database를 사용하면 [JSON](https://www.json.org/)(JavaScript Object Notation) 형식으로 표현된 데이터를 구문 분석 및 쿼리하고 관계형 데이터를 JSON 텍스트로 내보낼 수 있습니다. 다음 JSON 시나리오는 Azure SQL Database에서 사용할 수 있습니다.
- `FOR JSON` 절을 사용하여 [관계형 데이터 형식을 JSON으로 지정](#formatting-relational-data-in-json-format).
- [JSON 데이터 작업](#working-with-json-data)
- JSON 스칼라 함수를 사용하여 [JSON 데이터 쿼리](#querying-json-data).
- `OPENJSON` 함수를 사용하여 [JSON을 테이블 형식으로 변환](#transforming-json-into-tabular-format).

## <a name="formatting-relational-data-in-json-format"></a>관계형 데이터 형식을 JSON으로 지정
데이터베이스 계층에서 데이터를 가져오고 JSON 형식으로 응답을 제공하는 웹 서비스 또는 JSON으로 형식이 지정된 데이터를 수락하는 클라이언트 쪽 JavaScript 프레임워크나 라이브러리가 있는 경우 SQL 쿼리에서 직접 데이터베이스 콘텐츠 형식을 JSON으로 지정할 수 있습니다. Azure SQL Database의 결과 형식을 JSON으로 지정하는 애플리케이션 코드를 작성하거나 테이블 형식 쿼리 결과를 변환한 다음, 개체를 JSON 형식으로 직렬화하는 JSON 직렬화 라이브러리를 더 이상 포함할 필요가 없습니다. 대신, FOR JSON 절을 사용하여 Azure SQL Database에서 SQL 쿼리 결과의 형식을 JSON으로 지정하고 애플리케이션에서 직접 사용할 수 있습니다.

다음 예제에서 Sales.Customer 테이블의 행은 FOR JSON 절을 사용하여 JSON으로 형식이 지정됩니다.

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

FOR JSON PATH 절은 쿼리의 결과 형식을 JSON 텍스트로 지정합니다. 열 이름은 키로 사용되지만 셀 값은 JSON 값으로 생성됩니다.

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

각 행이 별도의 JSON 개체로 형식이 지정될 경우 결과 집합은 JSON 배열로 형식이 지정됩니다.

PATH는 열 별칭에 점 표기법을 사용하여 JSON 결과의 출력 형식을 사용자 지정할 수 있음을 나타냅니다. 다음 쿼리는 출력 JSON 형식에서 "CustomerName" 키의 이름을 변경하고 전화번호 및 팩스 번호를 "Contact" 하위 개체에 추가합니다.

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

이 쿼리의 출력은 다음과 같습니다.

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

이 예제에서는 [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) 옵션을 지정하여 배열 대신 단일 JSON 개체를 반환했습니다. 쿼리 결과로 단일 개체를 반환하는지 알고 있다면 이 옵션을 사용할 수 있습니다.

FOR JSON 절의 주 값은 중첩된 JSON 개체 또는 배열로 형식이 지정된 데이터베이스에서 복잡한 계층적 데이터를 반환할 수 있도록 합니다. 다음 예제에서는 `Customer`에 속하는 `Orders` 테이블의 행을 `Orders`의 중첩 배열로 포함하는 방법을 보여 줍니다.

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Customer 데이터를 가져온 다음 관련 Orders 목록을 인출하는 별도의 쿼리를 전송하는 대신, 다음 샘플 출력에 표시된 대로 단일 쿼리로 필요한 모든 데이터를 가져올 수 있습니다.

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>JSON 데이터 작업
엄격하게 구조화된 데이터가 없거나, 복잡한 하위 개체, 배열 또는 계층적 데이터가 있거나, 시간이 지나면서 데이터 구조가 변화할 경우 JSON 형식을 사용하면 복잡한 데이터 구조를 나타내는 데 도움이 될 수 있습니다.

JSON은 Azure SQL Database에서 다른 문자열 형식처럼 사용할 수 있는 텍스트 형식입니다. JSON 데이터를 표준 NVARCHAR로 전송하거나 저장할 수 있습니다.

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

이 예제에서 사용되는 JSON 데이터는 NVARCHAR(MAX) 형식을 사용하여 표시됩니다. JSON을 이 테이블에 삽입하거나, 다음 예제와 같이 표준 Transact-SQL 구문을 사용하여 저장 프로시저의 인수로 제공할 수 있습니다.

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Azure SQL Database의 문자열 데이터를 사용하는 모든 클라이언트 쪽 언어 또는 라이브러리에서도 JSON 데이터를 사용할 수 있습니다. JSON은 메모리 최적화 테이블 또는 시스템 버전 테이블같이 NVARCHAR 형식을 지원하는 모든 테이블에 저장할 수 있습니다. JSON을 사용할 경우 클라이언트 쪽 코드 또는 데이터베이스 계층에 어떤 제약도 적용되지 않습니다.

## <a name="querying-json-data"></a>JSON 데이터 쿼리
Azure SQL 테이블에 저장된 JSON으로 형식이 지정된 데이터가 있는 경우 JSON 함수를 통해 SQL 쿼리에서 이 데이터를 사용할 수 있습니다.

Azure SQL 데이터베이스에서 사용할 수 있는 JSON 함수는 JSON으로 형식이 지정된 데이터를 다른 SQL 데이터 형식처럼 처리할 수 있습니다. JSON 텍스트에서 쉽게 값을 추출하고 쿼리에서 JSON 데이터를 사용할 수 있습니다.

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

JSON_VALUE 함수는 데이터 열에 저장된 JSON 텍스트의 값을 추출합니다. 이 함수는 JavaScript와 비슷한 경로를 사용하여 추출할 JSON 텍스트의 값을 참조합니다. 추출된 값은 SQL 쿼리의 모든 부분에서 사용할 수 있습니다.

JSON_QUERY 함수는 JSON_VALUE와 비슷합니다. JSON_VALUE와 달리 이 함수는 JSON 텍스트에 배치된 배열 또는 개체와 같은 복잡한 하위 개체를 추출합니다.

JSON_MODIFY 함수를 사용하여 이전 값을 덮어쓰는 새 값뿐만 아니라 업데이트해야 하는 JSON 텍스트의 값 경로를 지정할 수도 있습니다. 이러한 방식으로 전체 구조를 다시 구문 분석하지 않고 JSON 텍스트를 쉽게 업데이트할 수 있습니다.

JSON은 표준 텍스트로 저장되므로 텍스트 열에 저장된 값의 형식이 올바를 것으로 보장할 수 없습니다. JSON 열에 저장된 텍스트는 표준 Azure SQL Database check 제약 조건 및 ISJSON 함수를 사용하여 올바르게 형식이 지정되었는지 확인할 수 있습니다.

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

입력 텍스트가 JSON으로 올바르게 형식이 지정되면 ISJSON 함수는 값 1을 반환합니다. JSON 열이 삽입되거나 업데이트될 때마다 이 제약 조건은 새 텍스트 값이 잘못된 형식의 JSON이 아닌지 확인합니다.

## <a name="transforming-json-into-tabular-format"></a>JSON을 테이블 형식으로 변환
Azure SQL Database에서는 JSON 컬렉션을 테이블 형식으로 변환하고 JSON 데이터를 로드 또는 쿼리할 수 있습니다.

OPENJSON은 JSON 텍스트를 구문 분석하고, JSON 개체의 배열을 찾고, 배열의 요소를 반복하고, 배열의 각 요소에 대한 출력 결과에 하나의 행을 반환하는 테이블 값 함수입니다.

![JSON 테이블 형식](./media/sql-database-json-features/image_2.png)

위의 예에서 열려야 하는 JSON 배열을 찾을 위치($.Orders 경로), 결과로 반환해야 하는 열 및 셀로 반환될 JSON 값을 찾을 위치를 지정할 수 있습니다.

@orders 변수의 JSON 배열을 행 집합으로 변환하거나 이 결과 집합을 분석하거나, 행을 표준 테이블에 삽입할 수 있습니다.

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
JSON 배열로 형식이 지정되고 저장 프로시저에 대한 매개 변수로 제공되는 주문 컬렉션은 구문 분석된 후 Orders 테이블에 삽입될 수 있습니다.

## <a name="next-steps"></a>다음 단계
JSON을 애플리케이션에 통합하는 방법을 알아보려면 다음 리소스를 참조하세요.

* [TechNet 블로그](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [MSDN 설명서](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9 비디오](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

JSON을 애플리케이션에 통합하는 다양한 시나리오에 대해 알아보려면 이 [Channel 9 비디오](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds)에서 데모를 참조하거나 [JSON 블로그 게시물](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/)에서 사용 사례와 일치하는 시나리오 찾아보세요.

