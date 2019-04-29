---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335392"
---
## <a name="repeatability-during-copy"></a>복사 중 반복성
다른 데이터 저장소에서 Azure SQL/SQL Server로 데이터를 복사할 때 의도치 않은 결과를 방지하려면 반복성을 유지해야 합니다. 

Azure SQL/SQL Server 데이터베이스에 데이터를 복사할 때 복사 작업이 기본적으로 싱크 테이블에 데이터 집합을 추가합니다. 예를 들어 두 레코드가 포함된 CSV(쉼표로 구분된 값 데이터) 파일 원본에서 Azure SQL/SQL Server 데이터베이스로 데이터를 복사하는 경우 테이블의 모양은 다음과 같습니다.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

원본 파일에서 오류를 발견하고 Down Tube의 수량을 2개에서 4개로 업데이트했다고 가정해 보겠습니다. 해당 기간에 대한 데이터 조각을 다시 실행하면 Azure/SQL Server 데이터베이스에 새 레코드가 2개 추가된 것을 알 수 있습니다. 아래에서는 테이블에 기본 키 제약 조건이 있는 열이 없다고 가정합니다.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

이를 방지하려면 아래에 나온 두 메커니즘 중 하나를 활용하여 UPSERT 의미 체계를 지정해야 합니다.

> [!NOTE]
> 조각은 지정된 재시도 정책에 따라 Azure Data Factory에서 자동으로 다시 실행할 수 있습니다.
> 
> 

### <a name="mechanism-1"></a>메커니즘 1
조각을 실행할 때 먼저 정리 작업을 수행하려면 **sqlWriterCleanupScript** 속성을 활용해야 합니다. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

지정된 조각에 대한 복사 중에 정리 스크립트를 먼저 실행하면 SQL 테이블에서 해당 조각에 대한 데이터가 삭제됩니다. 이 작업은 이어서 SQL 테이블에 해당 데이터를 삽입합니다. 

이제 조각을 다시 실행하면 원하는 수량으로 업데이트된 것을 알 수 있습니다.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

원본 csv에서 Flat Washer 레코드가 제거되었다고 가정해 보겠습니다. 이제 조각을 다시 실행하면 다음과 같은 결과가 나옵니다. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
새로운 어떤 작업도 수행할 필요가 없습니다. 복사 작업에서는 정리 스크립트를 실행하여 해당 조각에 대한 데이터를 삭제했습니다. 그런 다음 csv(1개의 레코드만 포함)에서 입력을 읽어서 테이블에 삽입했습니다. 

### <a name="mechanism-2"></a>메커니즘 2
> [!IMPORTANT]
> sliceIdentifierColumnName은 이번에 Azure SQL Data Warehouse에 지원되지 않습니다. 

반복성을 유지하는 또 다른 메커니즘은 대상 테이블에 전용 열(**sliceIdentifierColumnName**)을 만드는 것입니다. 이 열은 Azure 데이터 팩터리에서 원본 및 대상을 동기화 상태로 유지하도록 할 때 사용됩니다. 이 방법은 대상 SQL 테이블 스키마를 유연하게 변경하거나 정의할 수 있을 때 작동됩니다. 

이 열은 반복성 용도로 Azure 데이터 팩터리에서 사용되며 Azure 데이터 팩터리가 테이블에 대해 어떠한 스키마 변경도 하지 않는 프로세스에서도 사용됩니다. 이 방식을 사용하는 방법:

1. 대상 SQL 테이블에서 이진 형식(32)으로 열을 정의합니다. 이 열에는 제약 조건이 없어야 합니다. 이 예제에서는 이 열의 이름을 'ColumnForADFuseOnly'로 지정하겠습니다.
2. 복사 작업에서 다음과 같이 사용합니다.
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure 데이터 팩터리에서는 원본 및 대상을 동기화 상태로 유지하기 위해 필요에 따라 이 열을 채웁니다. 이 열의 값은 이 컨텍스트 외부에서 사용할 수 없습니다. 

메커니즘 1과 마찬가지로, 복사 작업은 먼저 대상 SQL 테이블에서 지정된 조각에 대한 데이터를 자동으로 정리한 다음, 정상적으로 복사 작업을 실행하여 원본에서 대상으로 해당 조각의 데이터를 삽입합니다. 

