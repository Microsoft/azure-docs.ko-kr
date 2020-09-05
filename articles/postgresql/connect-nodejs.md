---
title: '빠른 시작: Node.js를 사용하여 Azure Database for PostgreSQL - 단일 서버에 연결'
description: 이 빠른 시작에서는 Azure Database for PostgreSQL - 단일 서버의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Node.js 코드 샘플을 제공합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom:
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- devx-track-javascript
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 10c7abf7349a3b2419e81459866920f20c3af6f7
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182734"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>빠른 시작: Node.js를 사용하여 Azure Database for PostgreSQL - 단일 서버의 데이터 연결 및 쿼리

이 빠른 시작에서는 Node.js 애플리케이션을 사용하여 Azure Database for PostgreSQL에 연결합니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서의 단계에서는 개발자가 Node.js를 사용하여 개발하는 것에 익숙하고 Azure Database for PostgreSQL 작업에 익숙하지 않다고 가정합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [빠른 시작: Azure Portal에서 Azure Database for PostgreSQL 서버 만들기](quickstart-create-server-database-portal.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Database for PostgreSQL 만들기](quickstart-create-server-database-azure-cli.md)의 단계를 사용하여 만든 Azure Database for PostgreSQL - 단일 서버

- [Node.JS](https://nodejs.org)

## <a name="install-pg-client"></a>Pg 클라이언트 설치
Node.js용 PostgreSQL 클라이언트인 [pg](https://www.npmjs.com/package/pg)를 설치합니다.

이렇게 하려면 pg 클라이언트를 설치하기 위한 명령줄에서 JavaScript에 대한 노드 패키지 관리자(npm)를 실행합니다.
```bash
npm install pg
```

설치된 패키지를 나열하여 설치를 확인합니다.
```bash
npm list
```

## <a name="get-connection-information"></a>연결 정보 가져오기
PostgreSQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에서 만든 서버(예: **mydemoserver**)를 검색하여 선택합니다.

1. 서버의 **개요** 패널에서 **서버 이름**과 **관리자 사용자 이름**을 기록해 둡니다. 암호를 잊어버리면 이 패널에서 암호를 재설정할 수 있습니다.

   ![Azure Database for PostgreSQL 연결 문자열](./media/connect-nodejs/server-details-azure-database-postgresql.png)

## <a name="running-the-javascript-code-in-nodejs"></a>Node.js에서 JavaScript 코드 실행
`node`를 입력하여 bash 셸, 터미널 또는 Windows 명령 프롬프트에서 Node.js를 시작한 다음, 예시 JavaScript 코드를 복사하여 프롬프트에 붙여넣어 대화형으로 실행할 수 있습니다. 또는 JavaScript 코드를 텍스트 파일로 저장하고 파일 이름을 매개 변수로 지정한 `node filename.js`를 시작하여 실행할 수 있습니다.

## <a name="connect-create-table-and-insert-data"></a>테이블 연결, 생성 및 데이터 삽입
**CREATE TABLE** 및 **INSERT INTO** SQL 문을 사용하여 데이터를 연결하고 로드하려면 다음 코드를 사용하세요.
[pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>데이터 읽기
**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>데이터 업데이트
**UPDATE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>데이터 삭제
**DELETE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) 개체는 PostgreSQL 서버와 상호 작용하는 데 사용됩니다. [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) 함수는 서버에 연결하는 데 사용됩니다. [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) 함수는 PostgreSQL 데이터베이스에 대해 SQL 쿼리를 실행하는 데 사용됩니다. 

host, dbname, user 및 password 매개 변수는 서버 및 데이터베이스를 만들 때 지정한 값으로 바꾸세요. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
