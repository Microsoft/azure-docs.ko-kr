---
title: Azure Cosmos DB의 MongoDB용 API에서 MongoDB 읽기 기본 설정 사용
description: Azure Cosmos DB의 MongoDB용 API에서 MongoDB 읽기 기본 설정을 사용하는 방법에 대한 자세한 정보
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.openlocfilehash: dfb1e0093893fadf22c7a92ef5f351ae8920a977
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929290"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 MongoDB용 API를 사용하여 읽기를 전역 배포하는 방법

이 문서에서는 Azure Cosmos DB의 MongoDB용 API를 사용하여 [MongoDB 읽기 기본 설정](https://docs.mongodb.com/manual/core/read-preference/)으로 읽기 작업을 전역 배포하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건 
Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Azure Portal을 사용하여 Cosmos 계정을 전역 배포로 설정한 다음, 연결하는 방법에 대한 지침은 이 [빠른 시작](tutorial-global-distribution-mongodb.md) 문서를 참조하세요.

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

git bash와 같은 git 터미널 창을 열고 `cd`를 수행하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 관심 있는 플랫폼에 따라 다음 샘플 리포지토리 중 하나를 사용합니다.

1. [.NET 샘플 애플리케이션](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS 샘플 애플리케이션]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose 샘플 애플리케이션](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java 샘플 애플리케이션](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot 샘플 애플리케이션](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>애플리케이션 실행

사용된 플랫폼에 따라 필요한 패키지를 설치하고 애플리케이션을 시작합니다. 종속성을 설치하려면 샘플 애플리케이션 리포지토리에 포함된 추가 정보 파일을 따릅니다. 예를 들어 NodeJS 샘플 애플리케이션에서 다음 명령을 사용하여 필요한 패키지를 설치하고 애플리케이션을 시작합니다.

```bash
cd mean
npm install
node index.js
```
애플리케이션에서 MongoDB 원본에 대한 연결을 시도하지만 연결 문자열이 잘못되었기 때문에 실패합니다. 추가 정보 파일의 단계에 따라 `url` 연결 문자열을 업데이트합니다. 또한 Cosmos 계정에서 `readFromRegion`을 읽기 지역으로 업데이트합니다. NodeJS 샘플에서 제공되는 지침은 다음과 같습니다.

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

이러한 단계를 수행하면 샘플 애플리케이션이 실행되고 다음 출력이 생성됩니다.

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>읽기 기본 설정 모드를 사용하여 읽기

MongoDB 프로토콜은 클라이언트에서 사용할 수 있는 다음과 같은 읽기 기본 설정 모드를 제공합니다.

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

이러한 읽기 기본 설정 모드 각각의 동작에 대한 자세한 내용은 [MongoDB 읽기 기본 설정 동작](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) 설명서를 참조하세요. Cosmos DB에서 PRIMARY 설정은 WRITE 지역으로, SECONDARY 설정은 READ 지역으로 매핑됩니다.

일반적인 시나리오에 따라 다음 설정을 사용하는 것이 좋습니다.

1. **대기 시간이 짧은 읽기**가 필요한 경우 **NEAREST** 읽기 기본 설정 모드를 사용합니다. 이 설정은 읽기 작업을 사용 가능한 가장 가까운 지역으로 보냅니다. 가장 가까운 지역이 WRITE 지역이면, 이러한 작업은 해당 지역으로 보내집니다.
2. **고가용성 및 읽기의 지리적 분포**가 필요한 경우(대기 시간은 제약 조건이 아님), **SECONDARY PREFERRED** 읽기 기본 설정 모드를 사용합니다. 이 설정은 읽기 작업을 사용 가능한 READ 지역으로 보냅니다. READ 지역을 사용할 수 없는 경우 요청은 WRITE 지역으로 보내집니다.

샘플 애플리케이션의 다음 코드 조각에서는 NodeJS에서 NEAREST 읽기 기본 설정을 구성하는 방법을 보여 줍니다.

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

마찬가지로, 아래 코드 조각에서는 NodeJS에서 SECONDARY_PREFERRED 읽기 기본 설정을 구성하는 방법을 보여 줍니다.

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

읽기 기본 설정은 `readPreference`를 연결 문자열 URI 옵션의 매개 변수로 전달하여 설정할 수도 있습니다.

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

[.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) 및 [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)와 같은 다른 플랫폼의 경우 해당 샘플 애플리케이션 리포지토리를 참조하세요.

## <a name="read-using-tags"></a>태그를 사용하여 읽기

읽기 기본 설정 모드 외에도, MongoDB 프로토콜은 태그를 사용하여 읽기 작업을 보낼 수 있도록 허용합니다. Cosmos DB의 MongoDB용 API에서 `region` 태그는 기본적으로 `isMaster` 응답의 일부로 포함되어 있습니다.

```json
"tags": {
         "region": "West US"
      }
```

따라서 MongoClient에서 지역 이름과 함께 `region` 태그를 사용하여 특정 지역에 읽기 작업을 보낼 수 있습니다. Cosmos 계정의 경우 지역 이름은 Azure Portal의 왼쪽에 있는 **설정 -> 전역 복제본 데이터**에서 확인할 수 있습니다. 이 설정은 **읽기 격리**, 즉 클라이언트 애플리케이션에서 읽기 작업을 특정 지역에만 보내려는 경우에 유용합니다. 이 설정은 백그라운드에서 실행되지만 프로덕션에 중요한 서비스가 아닌 비프로덕션/분석 유형 시나리오에 적합합니다.

샘플 애플리케이션의 다음 코드 조각에서는 NodeJS에서 태그를 사용하여 읽기 기본 설정을 구성하는 방법을 보여 줍니다.

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

[.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) 및 [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)와 같은 다른 플랫폼의 경우 해당 샘플 애플리케이션 리포지토리를 참조하세요.

이 문서에서는 Azure Cosmos DB의 MongoDB용 API에서 읽기 기본 설정을 사용하여 읽기 작업을 전역으로 배포하는 방법을 알아보았습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 응용 프로그램을 계속 사용하지 않으려면 Azure Portal에서 다음 단계에 따라 이 문서에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md)
* [Azure Cosmos DB의 MongoDB용 API를 사용하여 전역 분산 데이터베이스 설정](tutorial-global-distribution-mongodb.md)합니다.
* [Azure Cosmos DB 에뮬레이터를 사용하여 로컬로 개발](local-emulator.md)
