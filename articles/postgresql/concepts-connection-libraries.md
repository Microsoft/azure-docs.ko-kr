---
title: PostgreSQL용 Azure 데이터베이스에 대한 연결 라이브러리
description: 이 문서에서는 개발자가 PostgreSQL용 Azure 데이터베이스에 연결하고 쿼리하도록 애플리케이션을 코딩할 때 사용할 수 있는 여러 개의 라이브러리 및 드라이버에 대해 설명합니다.
services: postgresql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
origin.date: 02/28/2018
ms.date: 12/03/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559775"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에 대한 연결 라이브러리
이 문서에서는 개발자가 Azure Database for PostgreSQL에 연결하고 쿼리하도록 애플리케이션을 개발하는 데 사용할 수 있는 라이브러리 및 드라이버를 나열합니다.

## <a name="client-interfaces"></a>클라이언트 인터페이스
PostgreSQL 서버에 연결하기 위한 대부분의 언어 클라이언트 라이브러리는 외부 프로젝트이며 독립적으로 배포됩니다. 나열된 라이브러리는 Azure Database for PostgreSQL에 연결하기 위해 Windows, Linux 및 Mac 플랫폼에서 지원됩니다. 몇 가지 빠른 시작 예제는 다음 단계 섹션에 나열됩니다.

| **언어** | **클라이언트 인터페이스** | **추가 정보** | **다운로드** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 호환 | [다운로드](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | 데이터베이스 확장 | [설치](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | 순수 JavaScript 비차단 클라이언트 | [설치](https://www.npmjs.com/package/pg) |
| 자바 | [JDBC](https://jdbc.postgresql.org/) | 형식 4 JDBC 드라이버 | [다운로드](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby 인터페이스 | [다운로드](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 순수 Go postgres 드라이버 | [설치](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET 데이터 공급자 | [다운로드](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC 드라이버 | [다운로드](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | 기본 C 언어 인터페이스 | 포함 |
| C++ | [libpqxx](http://pqxx.org/) | 새 스타일 C++ 인터페이스 | [다운로드](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>다음 단계
선택한 언어를 사용하여 PostgreSQL용 Azure Database에 연결하고 쿼리하는 방법에 대한 빠른 시작을 읽어보세요.

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
