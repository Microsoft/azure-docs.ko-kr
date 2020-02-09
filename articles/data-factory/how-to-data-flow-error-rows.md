---
title: Azure Data Factory에서 데이터 흐름이 매핑되는 오류 행 처리
description: 데이터 흐름 매핑을 사용 하 여 Azure Data Factory에서 SQL 잘림 오류를 처리 하는 방법에 대해 알아봅니다.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166539"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Data Factory 매핑 데이터 흐름에서 SQL 잘림 오류 행 처리

매핑 데이터 흐름을 사용할 때 Data Factory 일반적인 시나리오는 변환 된 데이터를 Azure SQL database에 쓰는 것입니다. 이 시나리오에서는을 방지 해야 하는 일반적인 오류 조건을 열 잘림을 수행할 수 있습니다. 이러한 단계를 수행 하 여 대상 문자열 열에 맞지 않는 열에 대한 로깅을 제공 하면 데이터 흐름이 해당 시나리오에서 계속 될 수 있습니다.

## <a name="scenario"></a>시나리오

1. "Name" 이라는 ```nvarchar(5)``` 열을 포함 하는 대상 Azure SQL database 테이블이 있습니다.

2. 데이터 흐름 내에서 싱크의 동영상 제목을 해당 대상 "이름" 열에 매핑해야 합니다.

    ![영화 데이터 흐름 1](media/data-flow/error4.png)
    
3. 문제는 동영상 타이틀이 모두 5 자만 포함할 수 있는 싱크 열에 들어가지 않는다는 것입니다. 이 데이터 흐름을 실행 하면 다음과 같은 오류가 표시 됩니다. ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>이 조건을 중심으로 디자인 하는 방법

1. 이 시나리오에서 "이름" 열의 최대 길이는 5 자입니다. 따라서 5 자 보다 긴 "제목"을 사용 하 여 행을 로깅할 수 있도록 하는 조건부 분할 변환을 추가 하 고 해당 공간에 맞출 수 있는 나머지 행도 데이터베이스에 쓸 수 있도록 합니다.

    ![조건부 분할](media/data-flow/error1.png)

2. 이 조건부 분할 변환은 "제목"의 최대 길이를 5로 정의 합니다. 5 보다 작거나 같은 행은 ```GoodRows``` 스트림으로 이동 합니다. 5 보다 큰 행은 ```BadRows``` 스트림으로 이동 합니다.

3. 이제 실패 한 행을 기록해 야 합니다. 로깅에 대한 ```BadRows``` 스트림에 싱크 변환을 추가 합니다. 여기에서는 전체 트랜잭션 레코드의 로깅을 사용할 수 있도록 모든 필드를 "자동 매핑" 합니다. Blob Storage의 단일 파일에 대한 텍스트로 구분 된 CSV 파일 출력입니다. 로그 파일 "badrows. csv"를 호출 합니다.

    ![잘못 된 행](media/data-flow/error3.png)
    
4. 완료 된 데이터 흐름은 다음과 같습니다. 이제 SQL 잘림 오류를 방지 하 고 이러한 항목을 로그 파일에 저장 하기 위해 오류 행을 분할할 수 있습니다. 한편 성공 행은 대상 데이터베이스에 계속 쓸 수 있습니다.

    ![전체 데이터 흐름](media/data-flow/error2.png)

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.
