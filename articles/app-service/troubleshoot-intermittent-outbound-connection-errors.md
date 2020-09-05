---
title: Azure App Service의 간헐적인 아웃 바운드 연결 오류 문제 해결
description: Azure App Service에서 간헐적 연결 오류 및 관련 된 성능 문제 해결
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 467f7b3525883e16e57a06ff97cf4fd386279d22
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958238"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Azure App Service의 간헐적인 아웃 바운드 연결 오류 문제 해결

이 문서는 [Azure App Service](./overview.md)에서 간헐적 연결 오류 및 관련 된 성능 문제를 해결 하는 데 도움이 됩니다. 이 항목에서는에 대 한 자세한 내용 및 문제 해결 방법론을 제공 하 고, SNAT (원본 주소 네트워크 변환) 포트를 소모 합니다. 이 문서의 어느 시점에서 든 도움이 필요한 경우 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에 게 문의 하세요. 또는 Azure 지원 인시던트를 파일에 제공 합니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 **지원 받기**를 선택 합니다.

## <a name="symptoms"></a>증상

Azure 앱 서비스에서 호스트 되는 응용 프로그램 및 함수는 다음과 같은 증상 중 하나 이상을 나타낼 수 있습니다.

* 서비스 계획의 일부 또는 일부 인스턴스에 대 한 응답 시간이 느립니다.
* 간헐적인 5xx 또는 **잘못 된 게이트웨이** 오류
* 시간 제한 오류 메시지
* 외부 끝점 (예: SQLDB, Service Fabric, 기타 앱 서비스 등)에 연결할 수 없습니다.

## <a name="cause"></a>원인

이러한 증상의 주요 원인은 응용 프로그램 인스턴스가 다음 제한 중 하나에 도달 하 여 외부 끝점에 대 한 새 연결을 열 수 없는 것입니다.

* TCP 연결: 설정할 수 있는 아웃 바운드 연결 수에 제한이 있습니다. 이는 사용 되는 작업자의 크기와 연결 됩니다.
* SNAT 포트: azure [의 아웃 바운드 연결](../load-balancer/load-balancer-outbound-connections.md)에 설명 된 대로, AZURE는 snat (원본 네트워크 주소 변환) 및 Load Balancer (고객에 게 노출 되지 않음)를 사용 하 여 공용 IP 주소 공간에 있는 Azure 외부의 끝점과 통신 합니다. Azure 앱 서비스의 각 인스턴스에는 처음에 할당 된 **128** SNAT 포트 수가 지정 됩니다. 이 제한은 동일한 호스트 및 포트 조합에 대 한 열린 연결에 영향을 줍니다. 앱이 혼합 된 주소 및 포트 조합에 대 한 연결을 만드는 경우 SNAT 포트를 사용 하지 않습니다. SNAT 포트는 동일한 주소 및 포트 조합에 대 한 호출이 반복 될 때 사용 됩니다. 포트를 해제한 후에는 필요에 따라 포트를 재사용할 수 있습니다. Azure 네트워크 부하 분산 장치는 4 분 동안 기다린 후에만 닫힌 연결에서 SNAT 포트를 회수 합니다.

응용 프로그램 또는 함수가 새 연결을 신속 하 게 열면 128 포트의 미리 할당 된 할당량을 빠르게 고갈 시킬 수 있습니다. 그러면 추가 SNAT 포트를 동적으로 할당 하거나 회수 된 SNAT 포트를 다시 사용 하 여 새 SNAT 포트를 사용할 수 있게 될 때까지 차단 됩니다. 새 연결을 만들 수 없기 때문에 차단 된 응용 프로그램이 나 함수는이 문서의 **현상** 단원에 설명 된 문제 중 하나 이상이 발생 하기 시작 합니다.

## <a name="avoiding-the-problem"></a>문제 방지

대상이 서비스 끝점을 지 원하는 Azure 서비스인 경우 [지역 VNet 통합](./web-sites-integrate-with-vnet.md) 및 서비스 끝점이 나 개인 끝점을 사용 하 여 SNAT 포트 소모 문제를 방지할 수 있습니다. 지역 VNet 통합을 사용 하 고 통합 서브넷에 서비스 끝점을 추가 하는 경우 해당 서비스에 대 한 아웃 바운드 트래픽에는 아웃 바운드 SNAT 포트 제한이 적용 되지 않습니다. 마찬가지로, 지역 VNet 통합 및 개인 끝점을 사용 하는 경우에는 해당 대상에 대 한 아웃 바운드 SNAT 포트 문제가 발생 하지 않습니다. 

SNAT 포트 문제를 방지 하는 것은 동일한 호스트 및 포트에 대 한 새 연결을 반복적으로 만드는 것을 방지 하는 것입니다.

SNAT 포트 소모를 완화 하는 일반적인 전략은 **Azure 설명서의 아웃 바운드 연결** 의 [문제 해결 섹션](../load-balancer/load-balancer-outbound-connections.md) 에 설명 되어 있습니다. 이러한 전략 중에서 다음은 Azure 앱 서비스에서 호스트 되는 앱 및 함수에 적용 됩니다.

### <a name="modify-the-application-to-use-connection-pooling"></a>연결 풀링을 사용하도록 애플리케이션 수정

* 풀링 HTTP 연결의 경우 [HttpClientFactory를 사용 하 여 풀 http 연결](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)을 검토 합니다.
* 연결 풀링을 SQL Server는 방법에 대 한 자세한 내용은 [SQL Server 연결 풀링 (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)을 검토 하십시오.
* Entity framework 응용 프로그램을 사용 하 여 풀링을 구현 하려면 [DbContext pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)을 검토 합니다.

다음은 서로 다른 솔루션 스택에 의해 연결 풀링을 구현 하기 위한 링크 컬렉션입니다.

#### <a name="node"></a>노드

기본적으로 NodeJS에 대 한 연결은 활성 상태로 유지 되지 않습니다. 다음은 연결 풀링을 구현 하는 방법에 대 한 예제를 포함 하는 연결 풀링을 위한 인기 있는 데이터베이스 및 패키지입니다.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP 연결 유지

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v 13.9.0 설명서](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

다음은 JDBC 연결 풀링을 구현 하는 방법에 대 한 예제를 포함 하는 JDBC 연결 풀링을 사용 하는 인기 있는 라이브러리입니다.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 연결 풀링

* [Apache 연결 관리](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [클래스 PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

PHP는 연결 풀링을 지원 하지 않지만 백 엔드 서버에 대 한 영구 데이터베이스 연결을 사용해 볼 수 있습니다.
 
* MySQL 서버

   * 새 버전에 대 한 [Mysqli 연결](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 이전 버전의 PHP에 대 한 [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 기타 데이터 원본

   * [PHP 연결 관리](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (참고: MicrosoftSQL Server 외에 다른 데이터베이스와 함께 SQLAlchemy를 사용할 수 있음)
* [HTTP](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)연결 유지 [(세션을](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)사용 하는 경우 Keep-alive는 자동으로 유지 됩니다.

다른 환경에서는 응용 프로그램에서 연결 풀링을 구현 하기 위한 공급자 또는 드라이버 관련 문서를 검토 합니다.

### <a name="modify-the-application-to-reuse-connections"></a>연결을 다시 사용하도록 애플리케이션 수정

*  Azure 함수에서 연결 관리에 대 한 추가 포인터 및 예제는 [Azure Functions에서 연결 관리](../azure-functions/manage-connections.md)를 검토 하세요.

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>덜 적극적인 재시도 논리를 사용하도록 애플리케이션 수정

* 추가 지침과 예제를 보려면 [다시 시도 패턴](/azure/architecture/patterns/retry)을 검토 하십시오.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>keepalive를 사용하여 아웃바운드 유휴 시간 제한 다시 설정

* Node.js 앱에 대 한 keepalive을 구현 하 [는 경우 내 노드 응용 프로그램에서 과도 한 아웃 바운드 호출](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)을 검토 합니다.

### <a name="additional-guidance-specific-to-app-service"></a>App Service 관련 추가 지침:

* [부하 테스트](/azure/devops/test/load-test/app-service-web-app-performance-test) 는 안정적인 공급 속도로 실제 데이터를 시뮬레이션 해야 합니다. 실제 스트레스 상태에서 앱 및 함수를 테스트 하면 SNAT 포트 소모 문제를 미리 파악 하 고 해결할 수 있습니다.
* 백 엔드 서비스에서 응답을 신속 하 게 반환할 수 있는지 확인 합니다. Azure SQL Database의 성능 문제 해결에 대 한 자세한 내용은 [Intelligent Insights의 성능 문제 해결 Azure SQL Database](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)을 참조 하십시오.
* App Service 계획을 더 많은 인스턴스로 확장 합니다. 크기 조정에 대한 자세한 내용은 [Azure App Service에서 앱 크기 조정](./manage-scale-up.md)을 참조하세요. App service 계획의 각 작업자 인스턴스에는 여러 SNAT 포트가 할당 됩니다. 더 많은 인스턴스를 사용 하 여 사용량을 분산 하는 경우 고유한 원격 끝점 당 100 아웃 바운드 연결의 권장 한도 보다 낮은 인스턴스당 SNAT 포트 사용을 얻을 수 있습니다.
* 단일 아웃 바운드 IP 주소를 할당 하는 [App Service Environment (ASE)](./environment/using-an-ase.md)로 이동 하는 것이 좋습니다 .이 경우 연결 및 SNAT 포트의 한도가 훨씬 더 높습니다. ASE에서 인스턴스당 SNAT 포트 수는 [Azure 부하 분산 장치 미리 할당 테이블](../load-balancer/load-balancer-outbound-connections.md#snatporttable) 을 기반으로 합니다. 예를 들어, 1-50 작업자 인스턴스를 포함 하는 ase에는 인스턴스당 1024 미리 할당 된 포트가 있고, 51-100 작업자 인스턴스가 있는 ase에는 인스턴스당 512 미리 할당 된 포트가 있습니다.

아웃 바운드 TCP 제한을 방지 하는 것은 작업자의 크기에 따라 제한이 설정 되므로 더 쉽게 해결할 수 있습니다. [샌드박스에서 VM 간 숫자 제한-TCP 연결](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) 의 제한을 확인할 수 있습니다.

|제한 이름|설명|작음 (A1)|보통 (A2)|큼 (A3)|Isolated 계층 (ASE)|
|---|---|---|---|---|---|
|Connections|전체 VM의 연결 수|1920|3968|8064|16,000|

아웃 바운드 TCP 제한을 방지 하기 위해 작업자의 크기를 늘리거나 가로로 확장할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

두 가지 유형의 아웃 바운드 연결 제한과 앱에서 수행 하는 작업을 알면 문제 해결을 보다 쉽게 수행할 수 있습니다. 앱에서 동일한 저장소 계정에 대 한 많은 호출을 수행 하는 경우 SNAT 한도가 의심 될 수 있습니다. 앱이 인터넷을 통해 끝점에 대 한 매우 많은 호출을 만드는 경우 VM 제한에 도달 하는 것으로 의심 됩니다.

원인을 신속 하 게 확인 하기에 충분 한 응용 프로그램 동작을 모르는 경우 이러한 결정에 도움이 되는 몇 가지 도구와 기술을 App Service에서 사용할 수 있습니다.

### <a name="find-snat-port-allocation-information"></a>SNAT 포트 할당 정보 찾기

[App Service 진단을](./overview-diagnostics.md) 사용 하 여 snat 포트 할당 정보를 찾고 App Service 사이트의 snat 포트 할당 메트릭을 확인할 수 있습니다. SNAT 포트 할당 정보를 찾으려면 다음 단계를 수행 합니다.

1. App Service 진단에 액세스 하려면 App Service 웹 앱으로 이동 하거나 [Azure Portal](https://portal.azure.com/)에서 App Service Environment 합니다. 왼쪽 탐색 영역에서 **문제 진단 및 해결**을 선택 합니다.
2. 가용성 및 성능 범주를 선택 합니다.
3. 범주 아래의 사용 가능한 타일 목록에서 SNAT 포트 소모 타일을 선택 합니다. 이 방법은 128 미만으로 유지 하는 것입니다.
필요한 경우 지원 티켓을 열 수 있습니다. 그러면 지원 엔지니어가 백 엔드에서 메트릭을 받게 됩니다.

SNAT 포트 사용은 메트릭으로 사용할 수 없으므로 snat 포트 사용량에 따라 자동 크기 조정을 하거나 SNAT 포트 할당 메트릭에 따라 자동 크기 조정을 구성할 수 없습니다.

### <a name="tcp-connections-and-snat-ports"></a>TCP 연결 및 SNAT 포트

TCP 연결 및 SNAT 포트는 직접 관련 되지 않습니다. TCP 연결 사용 탐지기는 App Service 사이트의 문제 진단 및 해결 블레이드에 포함 되어 있습니다. "TCP 연결" 구를 검색 하 여 찾습니다.

* SNAT 포트는 외부 네트워크 흐름에 대해서만 사용 되는 반면 총 TCP 연결은 로컬 루프백 연결을 포함 합니다.
* 두 프로토콜, IP 주소 또는 포트에서 서로 다른 흐름의 경우 SNAT 포트를 다른 흐름에서 공유할 수 있습니다. TCP 연결 메트릭은 모든 TCP 연결을 계산 합니다.
* TCP 연결 제한은 작업자 인스턴스 수준에서 발생 합니다. Azure 네트워크 아웃 바운드 부하 분산에서는 SNAT 포트 제한에 TCP 연결 메트릭을 사용 하지 않습니다.
* TCP 연결 제한은 [샌드박스 교차 VM 숫자 제한-Tcp 연결](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits) 에 설명 되어 있습니다.

|제한 이름|설명|작음 (A1)|보통 (A2)|큼 (A3)|Isolated 계층 (ASE)|
|---|---|---|---|---|---|
|Connections|전체 VM의 연결 수|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>WebJobs 및 데이터베이스 연결
 
SNAT 포트가 소진 된 경우 WebJobs가 SQL Database에 연결할 수 없는 경우 각 개별 웹 응용 프로그램 프로세스에서 열린 연결 수를 보여 주는 메트릭이 없습니다. 문제가 있는 WebJob을 찾으려면 몇 가지 WebJobs을 다른 App Service 계획으로 이동 하 여 상황을 개선 하거나 계획 중 하나에 문제가 남아 있는지 확인 합니다. 문제가 있는 WebJob을 찾을 때까지 프로세스를 반복 합니다.

### <a name="using-snat-ports-sooner"></a>더 빨리 SNAT 포트 사용

모든 SNAT 포트가 아래 조건에 따라 해제 되 고 동작이 설계 되었으므로 사용 된 SNAT 포트를 더 빨리 해제 하도록 Azure 설정을 변경할 수 없습니다.
 
* 서버 또는 클라이언트에서 FINACK를 보내는 경우 240 초 후에 [SNAT 포트가 해제 됩니다](../load-balancer/load-balancer-outbound-connections.md) .
* RST 표시 되는 경우 15 초 후에 SNAT 포트가 해제 됩니다.
* 유휴 시간 제한에 도달 하면 포트가 해제 됩니다.
 
## <a name="additional-information"></a>추가 정보

* [App Service를 사용한 SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Azure App Service에서 느린 앱 성능 문제 해결](./troubleshoot-performance-degradation.md)