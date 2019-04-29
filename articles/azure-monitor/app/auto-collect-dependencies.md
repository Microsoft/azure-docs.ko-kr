---
title: Azure Application Insights - 종속성 자동 수집 | Microsoft Docs
description: Application Insights에서 자동으로 종속성 수집 및 시각화
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 7f152de89f5d374a5b97368eafe7287fc9377291
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691124"
---
# <a name="dependency-auto-collection"></a>종속성 자동 수집

애플리케이션의 코드에 대한 추가 수정 없이 종속성으로 자동으로 검색되는 현재 지원되는 종속성 호출 목록은 아래와 같습니다. 통신 라이브러리, 저장소 클라이언트, 로깅 및 메트릭 라이브러리에 대한 나가는 호출뿐만 아니라 애플리케이션 프레임워크 및 서버에 들어오는 호출로 구성됩니다. Application Insights [애플리케이션 맵](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) 및 [트랜잭션 진단](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) 보기에서 이러한 종속성을 시각화합니다. 종속성이 아래 목록에 없으면 [추적 종속성 호출](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)을 사용하여 수동으로 추적할 수 있습니다.

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
| [EventHubs 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Storage 클라이언트</b>|  |
| ADO.NET | 4.5+ |
| <b>로깅 라이브러리</b> |  |
| ILogger | 1.1+ |
| System.Diagnostics.Trace | 4.5+ |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | NetStandard  1.3의 2.0.8+, .NET 4.5+의 2.0.6+ |

## <a name="java"></a>자바
| 앱 서버 | 버전 |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>앱 프레임워크 </b> |  |
| [스프링](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>통신 라이브러리</b> |  |
| [Apache HTTP 클라이언트](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Storage 클라이언트</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>로깅 라이브러리</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>메트릭 라이브러리</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *반응형 프로그래밍 지원을 제외합니다.
> <br>†[JVM 에이전트](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)를 설치해야 합니다.

## <a name="nodejs"></a>Node.js

| 통신 라이브러리 | 버전 |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Storage 클라이언트</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>로깅 라이브러리</b> | |
| [콘솔](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [윈스턴](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| 통신 라이브러리 | 버전 |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | 모두 |

## <a name="next-steps"></a>다음 단계

- [.NET](../../azure-monitor/app/asp-net-dependencies.md)에 대한 사용자 지정 종속성 추적을 설정합니다.
- [Java](../../azure-monitor/app/java-agent.md)에 대한 사용자 지정 종속성 추적을 설정합니다.
- [사용자 지정 종속성 원격 분석을 작성합니다](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](../../azure-monitor/app/data-model.md)을 참조하세요.
- Application Insights에서 지원되는 [플랫폼](../../azure-monitor/app/platforms.md)을 확인합니다.
