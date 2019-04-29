---
title: Linux에서 Java Enterprise 지원 - Azure App Service | Microsoft Docs
description: Linux 기반의 Azure App Service와 Wildfly를 사용하여 Java Enterprise 앱을 배포하는 방법을 안내하는 개발자 가이드입니다.
keywords: azure 앱 서비스, 웹 앱, linux, oss, java, wildfly, enterprise, java ee을 jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 8db65fd9a1f271aea4ceb345f4d9dfbb6b9ff8a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852229"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Linux 기반의 App Service에 대한 Java Enterprise 가이드

> [!NOTE] 
> App Service Linux에서 Java Enterprise Edition은 현재 미리 보기 중입니다. 이 스택이 **되지** 프로덕션 지향 작업에 대 한 것이 좋습니다. 참조 하십시오 합니다 [Java 개발자 가이드](app-service-linux-java.md) 우리의 Java SE 및 Tomcat 스택에 대 한 자세한 내용은 합니다.

Linux의 azure App Service에 Java 개발자가 빌드 및 배포를 완전히 관리 되는 Linux 기반 서비스에 Java Enterprise (Java EE) 응용 프로그램을 확장할 수 있습니다.  기본 Java Enterprise 런타임 환경은 오픈 소스 [Wildfly](https://wildfly.org/) 애플리케이션 서버입니다.

이 가이드에서는 Linux용 App Service를 사용하는 Java Enterprise 개발자를 위해 핵심 개념 및 지침을 제공합니다. Linux용 Azure App Service를 사용하여 Java 애플리케이션을 배포해본 경험이 없다면 [Java 빠른 시작](quickstart-java.md)을 먼저 완료해야 합니다. Java Enterprise에 국한되지 않는 Linux용 App Service 사용에 대한 질문의 답은 [Java 개발자 가이드](app-service-linux-java.md) 및 [App Service Linux FAQ](app-service-linux-faq.md)에서 찾을 수 있습니다.

## <a name="scale-with-app-service"></a>App Service의 크기 조정 

Linux 기반의 App Service에서 실행 중인 WildFly 애플리케이션 서버는 도메인 구성이 아닌 독립 실행형 모드에서 실행됩니다. App Service 계획을 확장할 때 각 WildFly 인스턴스는 독립 실행형 서버로 구성됩니다.

 [크기 조정 규칙](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json)을 사용하고 [인스턴스 수를 늘려서](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 애플리케이션을 수직 또는 수평적으로 확장합니다. 

## <a name="customize-application-server-configuration"></a>사용자 지정 애플리케이션 서버 구성

웹앱 인스턴스는 상태 비저장이므로 시작된 새로운 각 인스턴스는 애플리케이션에서 필요한 Wildfly 구성을 지원하도록 시작 시 구성되어야 합니다.
다음을 수행하기 위해 WildFly CLI를 호출하도록 시작 Bash 스크립트를 작성할 수 있습니다.

- 데이터 원본 설정
- 메시징 공급자 구성
- Wildfly 서버 구성에 다른 모듈 및 종속성을 추가합니다.

  Wildfly는 작동되어 실행되지만 애플리케이션은 시작되기 전에 스크립트가 실행됩니다. 스크립트는 `/opt/jboss/wildfly/bin/jboss-cli.sh`에서 호출된 [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)를 사용하여 서버를 시작한 후 필요한 구성 또는 변경 사항을 사용하여 애플리케이션 서버를 구성해야 합니다. 

Wildfly를 구성하는 데 CLI의 대화형 모드를 사용하지 마십시오. 대신 다음과 같은 `--file` 명령을 사용하여 JBoss CLI에 명령 스크립트를 제공할 수 있습니다.

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

시작 스크립트를 App Service 인스턴스의 `/home/site/deployments/tools`에 업로드합니다. FTP 자격 증명을 가져오는 방법에 대한 지침은 [이 문서](/azure/app-service/deploy-configure-credentials#userscope)를 참조하세요. 

다음 예와 같이 Azure Portal의 **시작 스크립트** 필드를 시작 셸 스크립트의 위치에 설정합니다. `/home/site/deployments/tools/your-startup-script.sh`

애플리케이션 구성에서 [애플리케이션 설정](/azure/app-service/web-sites-configure#application-settings)을 제공하여 스크립트에서 사용하기 위한 환경 변수를 전달합니다. 애플리케이션 설정은 연결 문자열 및 버전 제어에서 벗어나도록 애플리케이션을 구성하는 데 필요한 기타 비밀을 유지합니다.

## <a name="modules-and-dependencies"></a>모듈 및 종속성

모듈 및 해당 종속성을 JBoss CLI를 통해 Wildfly 클래스 경로에 설치하려면 해당 디렉터리에서 다음 파일을 작성해야 합니다.  일부 모듈 및 종속성에는 JNDI 명명 또는 기타 API 관련 구성과 같은 추가 구성이 필요할 수 있습니다. 따라서 이 목록은 대부분의 경우에서 종속성을 구성해야 하는 최소 세트입니다.

- [XML 모듈 설명자](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). 이 XML 파일은 모듈의 이름, 특성 및 종속성을 정의합니다. 이 [예제 module.xml 파일](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)은 Postgres 모듈, 해당 JAR 파일 JDBC 종속성 및 필요한 기타 모듈 종속성을 정의합니다.
- 사용자 모듈에 대한 필수 JAR 파일 종속성.
- 새 모듈을 구성하기 위한 JBoss CLI 명령을 사용하는 스크립트. 이 파일은 종속성을 사용하도록 서버를 구성하기 위해 JBoss CLI에서 실행할 명령을 포함합니다. 모듈, 데이터 원본 및 메시징 공급자를 추가하는 명령에 대한 문서는 [이 문서](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)를 참조하세요.
-  JBoss CLI를 호출하고 이전 단계에서 스크립트를 실행하는 Bash 시작 스크립트. 이 파일은 스케일 아웃을 수행하는 동안 App Service 인스턴스를 다시 시작하거나 새 인스턴스를 프로비전할 때 실행됩니다.  이 시작 스크립트는 JBoss 명령이 JBoss CLI로 전달되므로 애플리케이션에 대해 다른 구성을 수행할 수 있습니다. 최소한 이 파일은 JBoss CLI 명령 스크립트를 JBoss CLI에 전달하는 단일 명령일 수 있습니다. 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

모듈에 대한 파일 및 콘텐츠가 준비되면 아래 단계에 따라 모듈을 Wildfly 애플리케이션 서버에 추가합니다. 

1. App Service 인스턴스의 `/home/site/deployments/tools`에 파일을 FTP로 업로드합니다. FTP 자격 증명을 가져오기 위한 지침은 이 문서를 참조하세요. 
2. 다음 예제와 같이 Azure Portal의 애플리케이션 설정 블레이드에서 “시작 스크립트” 필드를 시작 셸 스크립트의 위치에 설정합니다. `/home/site/deployments/tools/your-startup-script.sh`
3. 포털의 **개요** 섹션에서 **다시 시작** 단추를 누르거나 Azure CLI를 사용하여 App Service 인스턴스를 다시 시작합니다.

## <a name="data-sources"></a>데이터 원본

데이터 원본 연결을 위해 Wildfly를 구성하려면 모듈 설치 및 종속성 섹션에서 위에서 설명한 동일한 프로세스를 수행합니다. Azure Database 서비스에 동일한 단계를 수행할 수 있습니다.

1. 데이터베이스 버전에 대한 JDBC 드라이버를 다운로드합니다. 편의를 위해 여기서 [Postgres](https://jdbc.postgresql.org/download.html) 및 [MySQL](https://dev.mysql.com/downloads/connector/j/)용 드라이버를 제공합니다. 다운로드 압축을 풀어 .jar 파일을 가져옵니다.
2. “모듈 및 종속성”에 설명된 단계에 따라 XML 모듈 설명자, JBoss CLI 스크립트, 시작 스크립트 및 JDBC .jar 종속성을 만들고 업로드합니다.


[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) 및 [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)를 사용한 Wildfly 구성에 대한 자세한 정보가 제공됩니다. 이러한 사용자 정의 지침과 함께 위의 일반화된 접근 방식을 사용하여 서버에 데이터 원본 정의를 추가할 수 있습니다.

## <a name="messaging-providers"></a>메시징 공급자

Service Bus를 메시징 메커니즘으로 사용하여 메시지 기반 Bean을 사용하도록 설정하려면

1. [Apache QPId JMS 메시징 라이브러리](https://qpid.apache.org/proton/index.html)를 사용합니다. 애플리케이션에 대한 pom.xml(또는 다른 빌드 파일)에 이 종속성을 포함시킵니다.

2.  [Service Bus 리소스](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)를 만듭니다. 보내기 및 받기 기능을 사용하여 해당 네임스페이스 및 공유 액세스 정책 내에 Azure Service Bus 네임스페이스 및 큐를 만듭니다.

3. 정책의 기본 키를 URL로 인코딩하거나 [Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)를 사용하여 공유 액세스 정책 키를 코드에 전달합니다.

4. JMS 공급자에 대한 모듈 XML 설명자, .jar 종속성, JBoss CLI 명령 및 시작 스크립트를 사용하여 모듈 설치 및 종속성 섹션에 설명된 단계를 따릅니다. 4개의 파일 외에도 JMS 큐 및 토픽에 대한 JNDI 이름을 정의하는 XML 파일을 만들어야 합니다. 참조 구성 파일은 [이 리포지토리](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)를 참조하세요.


## <a name="configure-session-management-caching"></a>세션 관리 캐싱 구성

기본적으로 Linux의 App Service는 기존 세션을 사용하는 클라이언트 요청이 동일한 애플리케이션 인스턴스로 라우팅되도록 세션 선호도 쿠키를 사용합니다. 이 기본 동작에는 구성이 필요하지 않지만, 다음과 같은 몇 가지 제한 사항이 있습니다.

- 애플리케이션 인스턴스가 다시 시작하거나 규모가 축소되면 애플리케이션 서버의 사용자 세션 상태가 손실됩니다.
- 애플리케이션의 세션 시간 초과 설정이 길거나 사용자 수가 고정된 경우 새 세션만 새로 시작된 인스턴스로 라우팅되므로 자동으로 크기가 조정된 새 인스턴스가 로드를 받는 데 시간이 다소 걸릴 수 있습니다.

[Azure Cache for Redis](/azure/azure-cache-for-redis/)와 같은 외부 세션 저장소를 사용하도록 Wildfly를 구성할 수 있습니다. 세션 쿠키 기반 라우팅을 해제하고 구성된 Wildfly 세션 저장소가 간섭 없이 작동하도록 하려면 [기존 ARR 인스턴스 선호도](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 구성을 해제해야 합니다.

## <a name="enable-web-sockets"></a>웹 소켓 사용

기본적으로 App Service에서 웹 소켓은 사용하도록 설정되어 있습니다. 애플리케이션에서 WebSockets를 사용하여 시작하려면 [이 빠른 시작](https://github.com/wildfly/quickstart/tree/master/websocket-hello)을 참조하세요.

## <a name="logs-and-troubleshooting"></a>로그 및 문제 해결

App Service는 애플리케이션 문제를 해결하는 데 도움이 되는 도구를 제공합니다.

-   왼쪽 탐색 창에서 **진단 로그**를 클릭하여 로그를 켭니다. **파일 시스템**을 클릭하여 스토리지 할당량 및 보존 기간을 설정하고 변경 내용을 저장합니다. 이러한 로그는 `/home/LogFiles/`에서 찾을 수 있습니다.
-   실행 중인 애플리케이션의 로그를 보려면 [SSH를 사용하여 애플리케이션 인스턴스에 연결합니다](app-service-linux-ssh-support.md).
-   포털의 **진단 로그** 패널 또는 다음과 같은 Azure CLI 명령을 사용하여 검사 진단 로그를 확인합니다. `az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group>`
