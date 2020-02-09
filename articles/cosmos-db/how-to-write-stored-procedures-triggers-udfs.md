---
title: Azure Cosmos DB에서 저장 프로시저, 트리거 및 Udf 작성
description: Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 정의하는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4dee017323bda5fc08598a9b24cadd11516807cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441728"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 작성하는 방법

Azure Cosmos DB에서는 사용자가 **저장 프로시저**, **트리거** 및 **UDF(사용자 정의 함수)** 를 작성할 수 있는 언어 통합 트랜잭션 방식으로 JavaScript를 실행하도록 제공합니다. Azure Cosmos DB에서 SQL API를 사용하는 경우 JavaScript 언어로 저장 프로시저, 트리거 및 UDF를 정의할 수 있습니다. JavaScript로 논리를 작하고 데이터베이스 엔진 내에서 실행할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure Cosmos DB의 JavaScript 언어 통합 쿼리 API](javascript-query-api.md) 및 [Cosmos DB SQL API 클라이언트 SDK](sql-api-dotnet-samples.md)를 사용하여 트리거, 저장 프로시저 및 UDF를 만들고 실행할 수 있습니다. 

저장 프로시저, 트리거 및 사용자 정의 함수를 호출하려면 등록해야 합니다. 자세한 내용은 [Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수로 작업하는 방법](how-to-use-stored-procedures-triggers-udfs.md)을 참조하세요.

> [!NOTE]
> 분할된 컨테이너의 경우 저장 프로시저를 실행할 때 파티션 키 값은 요청 옵션에서 제공되어야 합니다. 저장 프로시저의 범위는 항상 파티션 키로 지정됩니다. 다른 파티션 키 값을 가진 항목은 저장 프로시저에 표시되지 않습니다. 이 트리거에도 적용되었습니다.

> [!Tip]
> Cosmos는 저장 프로시저, 트리거 및 사용자 정의 함수를 사용 하 여 컨테이너 배포를 지원 합니다. 자세한 내용은 [서버 쪽 기능을 사용 하 여 Azure Cosmos DB 컨테이너 만들기](manage-sql-with-resource-manager.md#create-sproc) 를 참조 하세요.

## <a id="stored-procedures"></a>저장 프로시저를 작성하는 방법

저장 프로시저는 JavaScript를 사용하여 작성되고, Azure Cosmos 컨테이너 내에서 항목을 만들고, 업데이트하고, 읽고, 쿼리하고, 삭제할 수 있습니다. 저장 프로시저는 컬렉션별로 등록되며 해당 컬렉션에 있는 모든 문서 또는 첨부 파일에서 작동할 수 있습니다.

**예제**

"Hello World" 응답을 반환하는 단순한 저장 프로시저는 다음과 같습니다.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

컨텍스트 개체는 Azure Cosmos DB에서 수행할 수 있는 모든 작업에 대한 액세스와 요청 및 응답 개체에 대한 액세스를 제공합니다. 이 경우에 응답 개체를 사용하여 응답의 본문이 클라이언트로 다시 전송되도록 설정합니다.

저장 프로시저가 작성되면 컬렉션에서 등록되어야 합니다. 자세한 내용은 [Azure Cosmos DB에서 저장 프로시저를 사용하는 방법](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) 문서를 참조하세요.

### <a id="create-an-item"></a>저장 프로시저를 사용하여 항목 만들기

저장 프로시저를 사용 하 여 항목을 만들 때 항목이 Azure Cosmos 컨테이너에 삽입 되 고 새로 만든 항목에 대한 ID가 반환 됩니다. 항목 만들기는 비동기 작업이고 JavaScript 콜백 함수에 따라 달라집니다. 콜백 함수에는 작업이 실패할 경우의 오류 개체 및 반환 값(이 경우에는 생성된 개체)에 각각 사용되는 두 개의 매개 변수가 있습니다. 콜백 내에서 예외를 처리하거나 오류를 throw할 수 있습니다. 콜백이 제공되지 않았고 오류가 있는 경우, Azure Cosmos DB 런타임에서 오류를 throw합니다. 

또한 저장 프로시저에는 설명을 설정하는 매개 변수가 포함되며 부울 값입니다. 매개 변수가 true로 설정되고 설명이 누락된 경우 저장 프로시저는 예외를 throw합니다. 그렇지 않으면 저장 프로시저의 나머지가 계속 실행됩니다.

다음 예의 저장 프로시저는 새 Azure Cosmos 항목을 입력으로 사용 하 고, Azure Cosmos 컨테이너에 삽입 하 고, 새로 만든 항목에 대한 ID를 반환 합니다. 이 예제에서는 [.NET SQL API 빠른 시작](create-sql-api-dotnet.md)의 ToDoList 샘플을 활용하겠습니다.

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>저장 프로시저의 입력 매개 변수로 배열 

Azure Portal에서 저장 프로시저를 정의하는 경우 입력 매개 변수는 항상 문자열로 저장 프로시저에 전송됩니다. 입력으로 문자열의 배열을 전달하는 경우에도 배열은 문자열로 변환되고 저장 프로시저로 전송됩니다. 이를 해결하기 위해 저장 프로시저 내에서 함수를 정의하여 배열로 문자열을 구문 분석할 수 있습니다. 다음 코드에서는 문자열 입력 매개 변수를 배열로 구문 분석하는 방법을 보여줍니다.

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>저장 프로시저 내의 트랜잭션

저장 프로시저를 사용하여 컨테이너 내의 항목에서 트랜잭션을 구현할 수 있습니다. 다음 예제에서는 판타지 축구 게임 앱 내에서 트랜잭션을 사용하여 단일 작업에서 두 팀 간의 플레이어를 트레이드합니다. 저장 프로시저는 각각 인수로 전달된 플레이어 ID에 해당하는 두 개의 Azure Cosmos 항목을 읽으려고 합니다. 두 플레이어가 모두 있으면 저장 프로시저는 해당 팀을 교환하여 항목을 업데이트합니다. 이 과정에서 오류가 발생할 경우 저장 프로시저는 암시적으로 트랜잭션을 중단하는 JavaScript 예외를 throw합니다.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>저장 프로시저 내의 제한된 실행

항목을 Azure Cosmos 컨테이너에 대량으로 가져오는 저장 프로시저의 예제는 다음과 같습니다. 저장 프로시저가 `createDocument`의 부울 반환 값을 검사하여 제한된 실행을 처리한 다음, 각 저장 프로시저의 호출에 삽입된 항목 수를 사용하여 일괄 처리의 진행 상황을 추적하고 다시 시작합니다.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>트리거를 작성하는 방법

Azure Cosmos DB에서는 사전 트리거와 사후 트리거를 지원합니다. 사전 트리거는 데이터베이스 항목을 수정하기 전에 실행되고, 사후 트리거는 데이터베이스 항목을 수정한 후에 실행됩니다.

### <a id="pre-triggers"></a>사전 트리거

다음 예제에서는 사전 트리거를 사용하여 만든 Azure Cosmos 항목에 있는 속성의 유효성을 검사하는 방법을 보여줍니다. 이 예제에서는 [빠른 시작 .NET SQL API](create-sql-api-dotnet.md)의 할 일 목록 샘플을 활용하여 항목이 없는 경우 새로 추가된 항목에 타임스탬프 속성을 추가합니다.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

사전 트리거는 입력 매개 변수를 사용할 수 없습니다. 트리거에서 요청 개체를 사용하여 작업과 연결된 요청 메시지를 조작합니다. 이전 예제에서 사전 트리거는 Azure Cosmos 항목을 만들 때 실행되고 요청 메시지 본문에는 JSON 형식으로 만들 항목이 포함됩니다.

트리거가 등록될 때 트리거 실행에 사용되는 작업을 지정할 수 있습니다. 이 트리거는 `TriggerOperation.Create`의 `TriggerOperation` 값을 사용하여 생성되었습니다. 즉, 다음 코드에 나와 있는 것처럼 바꾸기 작업에서 트리거를 사용하는 것은 허용되지 않습니다.

사전 트리거를 등록하고 호출하는 방법의 예제는 [사전 트리거](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) 및 [사후 트리거](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) 문서를 참조하세요. 

### <a id="post-triggers"></a>사후 트리거

다음 예제에서는 사후 트리거를 보여줍니다. 이 트리거는 메타데이터 항목을 쿼리하고 새로 만든 항목에 대한 세부 정보로 업데이트합니다.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

한 가지 중요한 사항은 Azure Cosmos DB에서 트리거의 트랜잭션을 실행하는 것입니다. 사후 트리거는 기본 항목 자체와 같은 트랜잭션의 일부로 실행됩니다. 사후 트리거를 실행하는 동안 예외가 발생하면 전체 트랜잭션이 실패합니다. 커밋된 모든 사항은 롤백되며 예외가 반환됩니다.

사전 트리거를 등록하고 호출하는 방법의 예제는 [사전 트리거](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) 및 [사후 트리거](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) 문서를 참조하세요. 

## <a id="udfs"></a>사용자 정의 함수를 작성하는 방법

다음 샘플에서는 다양한 소득 계층에서 소득세를 계산하는 UDF를 만듭니다. 이 사용자 정의 함수는 쿼리 내에서 사용됩니다. 이 예제의 목적상 다음과 같은 속성의 "소득"이라는 컨테이너가 있다고 가정합니다.

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

다양한 소득 계층에서 소득세를 계산하는 함수 정의는 다음과 같습니다.

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

사용자 정의 함수를 등록하고 사용하는 방법의 예제는 [Azure Cosmos DB에서 사용자 정의 함수를 사용하는 방법](how-to-use-stored-procedures-triggers-udfs.md#udfs) 문서를 참조하세요.

## <a name="logging"></a>로깅 

저장 프로시저, 트리거 또는 사용자 정의 함수를 사용 하는 경우 `console.log()` 명령을 사용 하 여 단계를 기록할 수 있습니다. 이 명령은 다음 예제와 같이 `EnableScriptLogging`가 true로 설정 된 경우 디버깅을 위해 문자열을 집중 합니다.

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 작성하고 사용하는 개념 및 방법을 알아봅니다.

* [Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 등록하고 사용하는 방법](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB에서 JavaScript 쿼리 API를 사용하여 저장 프로시저 및 트리거를 작성하는 방법](how-to-write-javascript-query-api.md)

* [Azure Cosmos DB에서 Azure Cosmos DB 저장 프로시저, 트리거 및 사용자 정의 함수 작업](stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB에서 JavaScript 언어 통합 쿼리 API 작업](javascript-query-api.md)
