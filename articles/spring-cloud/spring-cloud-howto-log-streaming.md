---
title: 실시간으로 Azure Spring Cloud 앱 로그 스트림
description: 로그 스트리밍을 사용 하 여 응용 프로그램 로그를 즉시 보는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java
ms.openlocfilehash: fb76f7897b9647a688e21993002f9c96fe9487f8
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046751"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>실시간으로 Azure Spring Cloud 앱 로그 스트림
Azure 스프링 클라우드를 사용 하면 Azure CLI의 로그 스트리밍을 통해 문제 해결을 위한 실시간 응용 프로그램 콘솔 로그를 가져올 수 있습니다. [진단 설정을 사용 하 여 로그 및 메트릭을 분석할](./diagnostic-services.md)수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 스프링 클라우드, 최소 버전 0.2.0에 대 한 [Azure CLI 확장](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) 을 설치 합니다.
* 응용 프로그램을 실행 하는 **Azure 스프링 클라우드의** 인스턴스 (예: [스프링 클라우드 앱](./spring-cloud-quickstart-launch-app-cli.md)).

> [!NOTE]
>  ASC CLI 확장은 버전 0.2.0에서 0.2.1로 업데이트 됩니다. 이 변경 내용은 로그 스트리밍에 대 한 명령 구문에 영향을 줍니다. `az spring-cloud app log tail` 은로 대체 되었습니다 `az spring-cloud app logs` . 명령은 `az spring-cloud app log tail` 이후 릴리스에서 더 이상 사용 되지 않습니다. 버전 0.2.0를 사용 하는 경우 0.2.1로 업그레이드할 수 있습니다. 먼저 명령을 사용 하 여 이전 버전을 제거 `az extension remove -n spring-cloud` 합니다.  그런 다음 명령을 통해 0.2.1를 설치 `az extension add -n spring-cloud` 합니다.

## <a name="use-cli-to-tail-logs"></a>CLI를 사용 하 여 비상 로그

리소스 그룹 및 서비스 인스턴스 이름을 반복적으로 지정 하지 않으려면 기본 리소스 그룹 이름과 클러스터 이름을 설정 합니다.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
다음 예제에서는 리소스 그룹 및 서비스 이름이 명령에서 생략 됩니다.

### <a name="tail-log-for-app-with-single-instance"></a>단일 인스턴스를 사용 하는 앱에 대 한 비상 로그
Auth service 라는 앱에 인스턴스가 하나만 있는 경우 다음 명령을 사용 하 여 앱 인스턴스의 로그를 볼 수 있습니다.
```
az spring-cloud app logs -n auth-service
```
로그를 반환 합니다.
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>여러 인스턴스를 사용 하는 앱에 대 한 비상 로그
이라는 앱에 대 한 인스턴스가 여러 개 있는 경우 `auth-service` 옵션을 사용 하 여 인스턴스 로그를 볼 수 있습니다 `-i/--instance` . 

먼저, 다음 명령을 사용 하 여 앱 인스턴스 이름을 가져올 수 있습니다.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
결과 포함:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
그런 다음 옵션 옵션을 사용 하 여 앱 인스턴스의 로그를 스트리밍할 수 있습니다 `-i/--instance` .

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Azure Portal에서 앱 인스턴스의 세부 정보를 가져올 수도 있습니다.  Azure 스프링 클라우드 서비스의 왼쪽 탐색 창에서 **앱** 을 선택한 후 **앱 인스턴스**를 선택 합니다.

### <a name="continuously-stream-new-logs"></a>계속 해 서 새 로그 스트림
기본적으로는 `az spring-cloud ap log tail` 앱 콘솔로 스트리밍되는 기존 로그만 인쇄 한 후 종료 됩니다. 새 로그를 스트리밍하려면-f (--follow)를 추가 합니다.  

```
az spring-cloud app logs -n auth-service -f
``` 
지원 되는 모든 로깅 옵션을 확인 하려면:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>다음 단계
* [빠른 시작: 로그, 메트릭 및 추적을 사용 하 여 Azure 스프링 클라우드 앱 모니터링](spring-cloud-quickstart-logs-metrics-tracing.md)
* [진단 설정을 사용 하 여 로그 및 메트릭 분석](./diagnostic-services.md)

 





