---
title: Cassandra에 대한 Azure Cosmos DB API의 변경 피드
description: Cassandra에 대한 Azure Cosmos DB API에서 변경 피드를 사용 하 여 데이터에 대한 변경 내용을 가져오는 방법에 대해 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694624"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Cassandra에 대한 Azure Cosmos DB API의 변경 피드

Cassandra에 대한 Azure Cosmos DB API의 [변경 피드](change-feed.md) 지원은 CQL (Cassandra query Language)의 쿼리 조건자를 통해 사용할 수 있습니다. 이러한 조건자 조건을 사용 하 여 변경 피드 API를 쿼리할 수 있습니다. 응용 프로그램은 CQL에 필요한 기본 키 (파티션 키 라고도 함)를 사용 하 여 테이블에 대한 변경 내용을 가져올 수 있습니다. 그런 다음 결과에 따라 추가 작업을 수행할 수 있습니다. 테이블의 행에 대한 변경 내용은 수정 시간 순서 대로 캡처되고 파티션 키 당 정렬 순서가 보장 됩니다.

다음 예에서는 .NET을 사용 하 여 Cassandra API Keyspace 테이블의 모든 행에 대한 변경 피드를 가져오는 방법을 보여 줍니다. 조건자 COSMOS_CHANGEFEED_START_TIME ()는 지정 된 시작 시간 (이 경우 현재 날짜/시간)에서 변경 피드의 항목을 쿼리 하기 위해 CQL 내에서 직접 사용 됩니다. [여기](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/)에서 전체 샘플을 다운로드할 수 있습니다.

각 반복에서 페이징 상태를 사용 하 여 마지막 요소 변경 내용을 읽은 후 쿼리를 다시 시작 합니다. Keyspace에서 테이블에 대한 새 변경 내용의 연속 스트림을 볼 수 있습니다. 삽입 되거나 업데이트 되는 행에 대한 변경 내용이 표시 됩니다. Cassandra API에서 변경 피드를 사용 하는 삭제 작업에 대한 감시는 현재 지원 되지 않습니다. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

기본 키를 기준으로 단일 행에 대한 변경 내용을 가져오려면 쿼리에 기본 키를 추가 하면 됩니다. 다음 예에서는 "user_id = 1" 인 행의 변경 내용을 추적 하는 방법을 보여 줍니다.

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>현재 제한 사항

Cassandra API에서 변경 피드를 사용 하는 경우 다음과 같은 제한 사항이 적용 됩니다.

* 현재 삽입 및 업데이트가 지원 됩니다. 삭제 작업은 아직 지원 되지 않습니다. 해결 방법으로 삭제 중인 행에 소프트 표식을 추가할 수 있습니다. 예를 들어 "deleted" 라는 행에 필드를 추가 하 고이를 "true"로 설정 합니다.
* 마지막 업데이트는 핵심 SQL API와 동일 하 게 유지 되며 엔터티에 대한 중간 업데이트를 사용할 수 없습니다.


## <a name="error-handling"></a>오류 처리

Cassandra API에서 변경 피드를 사용 하는 경우 다음과 같은 오류 코드 및 메시지가 지원 됩니다.

* **HTTP 오류 코드 429** -변경 피드가 속도로 제한 된 경우 빈 페이지를 반환 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager 템플릿을 사용 하 여 Azure Cosmos DB Cassandra API 리소스 관리](manage-cassandra-with-resource-manager.md)
