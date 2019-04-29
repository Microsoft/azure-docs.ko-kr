---
title: Studio 3T를 사용하여 Azure Cosmos DB의 MongoDB API에 연결
titleSuffix: Azure Cosmos DB
description: Studio 3T 및 Azure Cosmos DB의 MongoDB API를 사용하여 Cosmos DB에 연결하는 방법을 알아봅니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
origin.date: 12/26/2018
ms.date: 03/18/2019
author: rockboyfor
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 9b0a6393d2372c831fdc964dee18acbccfd39a77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61332048"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Studio 3T를 사용하여 Cosmos 계정에 연결

Azure Cosmos DB의 MongoDB API를 사용하여 Cosmos 계정에 연결하려면 다음을 수행해야 합니다.

* [Studio 3T](https://studio3t.com/)를 다운로드 및 설치
* Cosmos DB [연결 문자열](connect-mongodb-account.md) 정보 확보

## <a name="create-the-connection-in-studio-3t"></a>Studio 3T에서 연결 만들기
Cosmos 계정을 Studio 3T 연결 관리자에 추가하려면 다음 단계를 수행합니다.

1. [Azure Cosmos DB에 MongoDB 애플리케이션 연결](connect-mongodb-account.md) 문서의 지침을 사용하여 Azure Cosmos DB의 MongoDB용 API로 구성된 Cosmos 계정의 연결 정보를 검색합니다.

    ![연결 문자열 페이지의 스크린샷](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. **연결**을 클릭하여 연결 관리자를 열려고 **새 연결**을 클릭합니다.

    ![Studio 3T 연결 관리자의 스크린샷](./media/mongodb-mongochef/ConnectionManager.png)
3. **새 연결** 창의 **서버** 탭에서 Azure Cosmos DB 계정의 호스트(FQDN) 및 포트를 입력합니다.

    ![Studio 3T 연결 관리자 서버 탭의 스크린샷](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. **새 연결** 창의 **인증** 탭에서 **기본(MONGODB CR 또는 SCARM-SHA-1)** 인증 모드를 선택하고, 사용자 이름 및 암호를 입력합니다.  기본 인증 DB(관리자)를 그대로 사용하거나 고유한 값을 지정합니다.

    ![Studio 3T 연결 관리자 인증 탭의 스크린샷](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. **새 연결** 창의 **SSL** 탭에서 **SSL 프로토콜을 사용하여 연결** 확인란 및 **서버 자체 서명된 SSL 인증서 허용** 라디오 단추를 확인합니다.

    ![Studio 3T 연결 관리자 SSL 탭의 스크린샷](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. **연결 테스트** 단추를 클릭하여 연결 정보의 유효성을 검사하고 **확인**을 클릭하여 새 연결 창으로 돌아간 다음 **저장**을 클릭합니다.

    ![Studio 3T 스크린샷 테스트 연결 창](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Studio 3T를 사용하여 데이터베이스, 컬렉션 및 문서 만들기
Studio 3T를 사용하여 데이터베이스, 컬렉션 및 문서를 만들려면 다음 단계를 수행합니다.

1. **연결 관리자**에서 연결을 강조 표시하고 **연결**을 클릭합니다.

    ![Studio 3T 연결 관리자의 스크린샷](./media/mongodb-mongochef/ConnectToAccount.png)
2. 호스트를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 추가**를 선택합니다.  데이터베이스 이름을 지정하고 **확인**을 클릭합니다.

    ![Studio 3T 데이터베이스 추가 옵션의 스크린샷](./media/mongodb-mongochef/AddDatabase1.png)
3. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 추가**를 선택합니다.  컬렉션 이름을 지정하고 **만들기**를 클릭합니다.

    ![Studio 3T 컬렉션 추가 옵션의 스크린샷](./media/mongodb-mongochef/AddCollection.png)
4. **컬렉션** 메뉴 항목을 클릭한 다음 **문서 추가**를 클릭합니다.

    ![Studio 3T 문서 추가 메뉴 항목의 스크린샷](./media/mongodb-mongochef/AddDocument1.png)
5. 문서 추가 대화 상자에서 다음을 붙여 넣고 **문서 추가**를 클릭합니다.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. 이번에는 다음 내용이 포함된 다른 문서를 추가합니다.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. 샘플 쿼리를 실행합니다. 예를 들어 'Andersen' 성을 가진 가족을 검색하고 부모 및 상태 필드를 반환합니다.

    ![스크린 샷: Mongochef 쿼리 결과](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB의 MongoDB API와 함께 [Robo 3T를 사용](mongodb-robomongo.md)하는 방법을 알아봅니다.
- Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB [샘플](mongodb-samples.md)을 살펴봅니다.

<!-- Update_Description: update meta properties, wording update -->