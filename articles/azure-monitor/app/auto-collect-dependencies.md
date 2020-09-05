---
title: Azure Application Insights - 종속성 자동 수집 | Microsoft Docs
description: Application Insights에서 자동으로 종속성 수집 및 시각화
ms.topic: reference
ms.custom: devx-track-dotnet
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/06/2020
ms.openlocfilehash: ca1c63f042bd06c19f232c2ff8170d23741e73f2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936438"
---
# <a name="dependency-auto-collection"></a>종속성 자동 수집

애플리케이션의 코드에 대한 추가 수정 없이 종속성으로 자동으로 검색되는 현재 지원되는 종속성 호출 목록은 아래와 같습니다. Application Insights [애플리케이션 맵](./app-map.md) 및 [트랜잭션 진단](./transaction-diagnostics.md) 보기에서 이러한 종속성을 시각화합니다. 종속성이 아래 목록에 없으면 [추적 종속성 호출](./api-custom-events-metrics.md#trackdependency)을 사용하여 수동으로 추적할 수 있습니다.

## <a name="net"></a>.NET

| 앱 프레임워크| 버전 |
| ------------------------|----------|
| ASP.NET Webforms | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> 통신 라이브러리</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0-안정적인 최신 릴리스. 아래 참고를 참조 하세요.
| [EventHubs 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Storage 클라이언트</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> 이전 버전의 Microsoft. SqlClient에는 [알려진 문제가](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) 있습니다. 이 문제를 완화 하려면 1.1.0 이상을 사용 하는 것이 좋습니다. Entity Framework Core는 Microsoft의 안정적인 최신 릴리스와 함께 제공 되는 것은 아닙니다. 따라서이 문제를 방지 하기 위해 최소한 1.1.0에 있는지 확인 하는 것이 좋습니다.   


## <a name="java"></a>Java
| 앱 서버 | 버전 |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>앱 프레임워크 </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>통신 라이브러리</b> |  |
| [Apache HTTP 클라이언트](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Storage 클라이언트</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (베타 지원)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>로깅 라이브러리</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>메트릭 라이브러리</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *반응형 프로그래밍 지원을 제외합니다.
> <br>†[JVM 에이전트](./java-agent.md#install-the-application-insights-agent-for-java)를 설치해야 합니다.

## <a name="nodejs"></a>Node.js

| 통신 라이브러리 | 버전 |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Storage 클라이언트</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x-에서처럼 |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16 |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6gb-7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x-2.x |
| <b>로깅 라이브러리</b> | |
| [콘솔](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x-에서처럼 |

## <a name="javascript"></a>JavaScript

| 통신 라이브러리 | 버전 |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | 모두 |

## <a name="next-steps"></a>다음 단계

- [.NET](./asp-net-dependencies.md)에 대한 사용자 지정 종속성 추적을 설정합니다.
- [Java](./java-agent.md)에 대한 사용자 지정 종속성 추적을 설정합니다.
- [OpenCensus Python](./opencensus-python-dependency.md)에 대 한 사용자 지정 종속성 추적을 설정 합니다.
- [사용자 지정 종속성 원격 분석을 작성합니다](./api-custom-events-metrics.md#trackdependency).
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](./data-model.md)을 참조하세요.
- Application Insights에서 지원되는 [플랫폼](./platforms.md)을 확인합니다.

