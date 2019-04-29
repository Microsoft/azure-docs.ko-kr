---
title: Azure App Service Linux Java 앱 구성 | Microsoft Docs
description: Linux의 Azure App Service에서 실행되는 Java 앱을 구성하는 방법을 알아봅니다.
keywords: azure app service, 웹앱, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850463"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux Java 앱 구성

Linux 기반의 Azure App Service는 Java 개발자가 Tomcat 또는 Java SE(Standard Edition) 패키지 웹 애플리케이션을 신속하게 구축하고, 완벽하게 관리되는 Linux 기반 서비스에 배포하고, 규모를 조정할 수 있게 도와줍니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드는 주요 개념 및 기본 제공 Linux 컨테이너를 사용 하 여 App Service에서 Java 개발자를 위한 지침을 제공 합니다. Azure App Service를 사용한 경험이 없는 경우에 따라 합니다 [Java 빠른 시작](quickstart-java.md) 하 고 [PostgreSQL 자습서를 사용 하 여 Java](tutorial-java-enterprise-postgresql-app.md) 첫 번째입니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 자세한 내용은 [Azure App Service 진단 개요](../overview-diagnostics.md)합니다.

### <a name="ssh-console-access"></a>SSH 콘솔 액세스

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

자세한 내용은 [Azure CLI를 사용하여 로그 스트리밍](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)을 참조하세요.

### <a name="app-logging"></a>앱 로깅

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 응용 프로그램을 구성합니다.

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](/azure/application-insights/app-insights-java-trace-logs)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux 용 azure App Service에서 Azure portal 및 CLI를 통해 사용자 지정 하 고 상자 튜닝을 지원합니다. 비 특정 Java 웹 앱 구성에 대 한 다음 문서를 검토 합니다.

- [App Service 설정 구성](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [사용자 지정 도메인 설정](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL 사용](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN 추가](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

Tomcat 및 Java SE 환경에서 할당된 메모리 또는 기타 JVM 런타임 옵션을 설정하려면 아래와 같이 [애플리케이션 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)으로 JAVA_OPTS를 설정합니다. App Service Linux는 시작될 때 이 설정을 Java 런타임에 환경 변수로 전달합니다.

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에서 `$JAVA_OPTS -Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 `JAVA_OPTS`라고 하는 새 앱 설정을 만듭니다.

Azure App Service Linux Maven 플러그 인에서 앱 설정을 구성하려면 Azure 플러그 인 섹션에서 설정/값 태그를 추가합니다. 다음 예제에서는 특정 최소 및 최대 Java 힙 크기를 설정합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service 계획에서 배포 슬롯 하나를 사용하여 단일 애플리케이션을 실행하는 개발자는 다음 옵션을 사용할 수 있습니다.

- B1 및 S1 인스턴스: -Xms1024m -Xmx1024m
- B2 및 S2 인스턴스: -Xms3072m -Xmx3072m
- B3 및 S3 인스턴스: -Xms6144m -Xmx6144m


애플리케이션 힙 설정을 튜닝할 때 App Service 계획 세부 정보를 검토하고 여러 애플리케이션 및 배포 슬롯 요구 사항을 고려하여 최적의 메모리 할당을 찾아보세요.

### <a name="turn-on-web-sockets"></a>웹 소켓 켜기

애플리케이션의 Azure Portal **애플리케이션 설정**에서 웹 소켓을 켭니다. 설정을 적용하려면 애플리케이션을 다시 시작해야 합니다.

Azure CLI에서 다음 명령을 사용하여 웹 소켓 지원을 켭니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

그런 다음, 애플리케이션을 다시 시작합니다.

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>기본 문자 인코딩 설정

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에 `$JAVA_OPTS -Dfile.encoding=UTF-8` 값을 사용하여 `JAVA_OPTS`이라고 하는 새 앱 설정을 만듭니다.

또는 App Service Maven 플러그 인을 사용하여 앱 설정을 구성할 수 있습니다. 플러그 인 구성에서 설정 이름 및 값 태그를 추가합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>보안 애플리케이션

Linux용 App Service에서 실행되는 Java 애플리케이션의 [보안 모범 사례](/azure/security/security-paas-applications-using-app-services) 집합은 다른 애플리케이션과 동일합니다. 

### <a name="authenticate-users"></a>사용자 인증

사용 하 여 Azure portal에서 앱 인증을 설정 합니다 **인증 및 권한 부여** 옵션입니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 및 기타 ID 공급자 관련 문서를 참조하세요.

여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 문서의 지침을 따르세요.

 Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)를 사용하여 친숙한 Spring Security 주석 및 API로 애플리케이션을 보호할 수 있습니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[기존 사용자 지정 SSL 인증서 바인딩](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)의 지침에 따라 기존 SSL 인증서를 업로드하고 애플리케이션의 도메인 이름에 바인딩합니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

## <a name="configure-apm-platforms"></a>APM 플랫폼 구성

이 섹션에서는 NewRelic 및 AppDynamics 응용 프로그램 성능 모니터링 (APM) 플랫폼을 사용 하 여 Linux의 Azure App Service에 배포 된 Java 응용 프로그램을 연결 하는 방법을 보여 줍니다.

[New Relic 구성](#configure-new-relic)
[AppDynamics 구성](#configure-appdynamics)

### <a name="configure-new-relic"></a>New Relic 구성

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 `newrelic-java-x.x.x.zip`과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 대 한 SSH](app-service-linux-ssh-support.md) 새 디렉터리를 만들고 `/home/site/wwwroot/apm`합니다.
5. 압축이 풀린 NewRelic Java 에이전트 파일을 `/home/site/wwwroot/apm` 아래 디렉터리에 업로드합니다. 에이전트 파일은 `/home/site/wwwroot/apm/newrelic`에 있어야 합니다.
6. `/home/site/wwwroot/apm/newrelic/newrelic.yml`에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - 앱이 **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.
    - 사용 중인 경우 **WildFly**, New Relic 설명서를 참조 하십시오 [여기](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) JBoss 구성과 Java 에이전트를 설치 하는 방법에 대 한 지침에 대 한 합니다.
    - `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `javaagent` 옵션을 추가합니다.

### <a name="configure-appdynamics"></a>AppDynamics 구성

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
1. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드합니다. 파일 이름은 `AppServerAgent-x.x.x.xxxxx.zip`과 유사합니다.
1. [App Service 인스턴스에 대 한 SSH](app-service-linux-ssh-support.md) 새 디렉터리를 만들고 `/home/site/wwwroot/apm`합니다.
1. Java 에이전트 파일을 `/home/site/wwwroot/apm` 아래 디렉터리에 업로드합니다. 에이전트 파일은 `/home/site/wwwroot/apm/appdynamics`에 있어야 합니다.
1. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
    - 사용 중인 경우 **WildFly**, AppDynamics 설명서를 참조 하십시오 [여기](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) JBoss 구성과 Java 에이전트를 설치 하는 방법에 대 한 지침에 대 한 합니다.

## <a name="configure-tomcat"></a>Tomcat 구성

### <a name="connect-to-data-sources"></a>데이터 원본에 연결

>[!NOTE]
> 애플리케이션에서 Spring Framework 또는 Spring Boot를 사용하는 경우 Spring Data JPA에 대한 데이터베이스 연결 정보를 [애플리케이션 속성 파일]에 환경 변수로 설정할 수 있습니다. 그런 다음, Azure Portal 또는 CLI에서 [앱 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)을 사용하여 애플리케이션에 대한 이러한 값을 정의합니다.

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

데이터베이스 연결 JDBC (Java) 또는 Java 지 속성 API (JPA) 사용 하 여 Tomcat을 구성 하려면 먼저 사용자 지정을 `CATALINA_OPTS` 시작 시에 Tomcat 읽을 수 있는 환경 변수입니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

또는 Azure Portal의 “애플리케이션 설정” 블레이드에서 환경 변수를 설정합니다.

다음으로, 데이터 원본을 한 애플리케이션에만 제공할 것인지 또는 Tomcat 서블릿에서 실행 중인 모든 애플리케이션에 제공할 것인지 결정합니다.

#### <a name="application-level-data-sources"></a>응용 프로그램 수준 데이터 원본

1. 프로젝트의 `META-INF/` 디렉터리에 `context.xml` 파일을 만듭니다. `META-INF/` 디렉터리가 없으면 디렉터리를 만듭니다.

2. `context.xml`에서는 데이터 원본을 JNDI 주소에 연결하는 `Context` 요소를 추가합니다. `driverClassName` 자리 표시자를 위 테이블에 있는 드라이버의 클래스 이름으로 바꿉니다.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. 애플리케이션의 데이터 원본을 사용하도록 애플리케이션의 `web.xml`을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>서버 수준 리소스를 공유합니다.

1. 아직 구성이 있는 경우 SSH를 사용하여 `/usr/local/tomcat/conf`의 내용을 App Service Linux 인스턴스의 `/home/tomcat/conf`에 복사합니다.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. `server.xml`에서 `<Server>` 요소 내에 컨텍스트 요소를 추가합니다.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. 애플리케이션의 데이터 원본을 사용하도록 애플리케이션의 `web.xml`을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>구성 완료

마지막으로 드라이버 Jar Tomcat classpath에 배치 하 고 App Service를 다시 시작 하십시오.

1. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 `/home/tomcat/lib` 디렉터리에 배치합니다. (아직 존재하지 않는 경우 이 디렉터리를 만듭니다.) 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.
    1. 에 [Cloud Shell](https://shell.azure.com), 웹 앱 확장을 설치 합니다.

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. App Service에 로컬 시스템에서 SSH 터널을 만들려면 다음 CLI 명령을 실행 합니다.

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 파일을 `/home/tomcat/lib` 폴더에 업로드합니다.

    또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)을 따릅니다.

2. 서버 수준 데이터 원본을 만든 경우 App Service Linux 애플리케이션을 다시 시작합니다. Tomcat이 `CATALINA_HOME`을 `/home/tomcat/conf`로 다시 설정하고 업데이트된 구성을 사용합니다.

## <a name="configure-wildfly-server"></a>WildFly 서버 구성

[App Service를 사용 하 여 scale](#scale-with-app-service)
[사용자 지정 응용 프로그램 서버 구성](#customize-application-server-configuration)
[모듈과 종속성](#modules-and-dependencies)
[데이터 원본](#data-sources)
[메시징 공급자 사용](#enable-messaging-providers)
[세션 관리 캐싱을 구성 합니다.](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>App Service의 크기 조정

Linux 기반의 App Service에서 실행 중인 WildFly 애플리케이션 서버는 도메인 구성이 아닌 독립 실행형 모드에서 실행됩니다. App Service 계획을 확장할 때 각 WildFly 인스턴스는 독립 실행형 서버로 구성됩니다.

 [크기 조정 규칙](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)을 사용하고 [인스턴스 수를 늘려서](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 애플리케이션을 수직 또는 수평적으로 확장합니다.

### <a name="customize-application-server-configuration"></a>사용자 지정 애플리케이션 서버 구성

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

시작 스크립트를 App Service 인스턴스의 `/home/site/deployments/tools`에 업로드합니다. FTP 자격 증명을 가져오는 방법에 대한 지침은 [이 문서](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope)를 참조하세요.

다음 예와 같이 Azure Portal의 **시작 스크립트** 필드를 시작 셸 스크립트의 위치에 설정합니다. `/home/site/deployments/tools/your-startup-script.sh`

제공할 [앱 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) 스크립트에서 사용 하기 위해 환경 변수를 전달 하 고 응용 프로그램 구성에서 합니다. 애플리케이션 설정은 연결 문자열 및 버전 제어에서 벗어나도록 애플리케이션을 구성하는 데 필요한 기타 비밀을 유지합니다.

### <a name="modules-and-dependencies"></a>모듈 및 종속성

모듈 및 해당 종속성을 JBoss CLI를 통해 Wildfly 클래스 경로에 설치하려면 해당 디렉터리에서 다음 파일을 작성해야 합니다. 일부 모듈 및 종속성에는 JNDI 명명 또는 기타 API 관련 구성과 같은 추가 구성이 필요할 수 있습니다. 따라서 이 목록은 대부분의 경우에서 종속성을 구성해야 하는 최소 세트입니다.

- [XML 모듈 설명자](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). 이 XML 파일은 모듈의 이름, 특성 및 종속성을 정의합니다. 이 [예제 module.xml 파일](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)은 Postgres 모듈, 해당 JAR 파일 JDBC 종속성 및 필요한 기타 모듈 종속성을 정의합니다.
- 사용자 모듈에 대한 필수 JAR 파일 종속성.
- 새 모듈을 구성하기 위한 JBoss CLI 명령을 사용하는 스크립트. 이 파일은 종속성을 사용하도록 서버를 구성하기 위해 JBoss CLI에서 실행할 명령을 포함합니다. 모듈, 데이터 원본 및 메시징 공급자를 추가하는 명령에 대한 문서는 [이 문서](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)를 참조하세요.
- JBoss CLI를 호출하고 이전 단계에서 스크립트를 실행하는 Bash 시작 스크립트. 이 파일은 스케일 아웃을 수행하는 동안 App Service 인스턴스를 다시 시작하거나 새 인스턴스를 프로비전할 때 실행됩니다. 이 시작 스크립트는 JBoss 명령이 JBoss CLI로 전달되므로 애플리케이션에 대해 다른 구성을 수행할 수 있습니다. 최소한 이 파일은 JBoss CLI 명령 스크립트를 JBoss CLI에 전달하는 단일 명령일 수 있습니다.

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

모듈에 대한 파일 및 콘텐츠가 준비되면 아래 단계에 따라 모듈을 Wildfly 애플리케이션 서버에 추가합니다.

1. App Service 인스턴스의 `/home/site/deployments/tools`에 파일을 FTP로 업로드합니다. FTP 자격 증명을 가져오기 위한 지침은 이 문서를 참조하세요.
2. 다음 예제와 같이 Azure Portal의 애플리케이션 설정 블레이드에서 “시작 스크립트” 필드를 시작 셸 스크립트의 위치에 설정합니다. `/home/site/deployments/tools/your-startup-script.sh`
3. 포털의 **개요** 섹션에서 **다시 시작** 단추를 누르거나 Azure CLI를 사용하여 App Service 인스턴스를 다시 시작합니다.

### <a name="data-sources"></a>데이터 원본

데이터 원본 연결을 위해 Wildfly를 구성하려면 모듈 설치 및 종속성 섹션에서 위에서 설명한 동일한 프로세스를 수행합니다. Azure Database 서비스에 동일한 단계를 수행할 수 있습니다.

1. 데이터베이스 버전에 대한 JDBC 드라이버를 다운로드합니다. 편의를 위해 여기서 [Postgres](https://jdbc.postgresql.org/download.html) 및 [MySQL](https://dev.mysql.com/downloads/connector/j/)용 드라이버를 제공합니다. 다운로드 압축을 풀어 .jar 파일을 가져옵니다.
2. “모듈 및 종속성”에 설명된 단계에 따라 XML 모듈 설명자, JBoss CLI 스크립트, 시작 스크립트 및 JDBC .jar 종속성을 만들고 업로드합니다.

[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) 및 [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)를 사용한 Wildfly 구성에 대한 자세한 정보가 제공됩니다. 이러한 사용자 정의 지침과 함께 위의 일반화된 접근 방식을 사용하여 서버에 데이터 원본 정의를 추가할 수 있습니다.

### <a name="enable-messaging-providers"></a>메시징 공급자를 사용 하도록 설정

Service Bus를 메시징 메커니즘으로 사용하여 메시지 기반 Bean을 사용하도록 설정하려면

1. [Apache QPId JMS 메시징 라이브러리](https://qpid.apache.org/proton/index.html)를 사용합니다. 애플리케이션에 대한 pom.xml(또는 다른 빌드 파일)에 이 종속성을 포함시킵니다.

2. [Service Bus 리소스](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)를 만듭니다. 보내기 및 받기 기능을 사용하여 해당 네임스페이스 및 공유 액세스 정책 내에 Azure Service Bus 네임스페이스 및 큐를 만듭니다.

3. 정책의 기본 키를 URL로 인코딩하거나 [Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)를 사용하여 공유 액세스 정책 키를 코드에 전달합니다.

4. JMS 공급자에 대한 모듈 XML 설명자, .jar 종속성, JBoss CLI 명령 및 시작 스크립트를 사용하여 모듈 설치 및 종속성 섹션에 설명된 단계를 따릅니다. 4개의 파일 외에도 JMS 큐 및 토픽에 대한 JNDI 이름을 정의하는 XML 파일을 만들어야 합니다. 참조 구성 파일은 [이 리포지토리](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)를 참조하세요.

### <a name="configure-session-management-caching"></a>세션 관리 캐싱 구성

기본적으로 Linux의 App Service는 기존 세션을 사용하는 클라이언트 요청이 동일한 애플리케이션 인스턴스로 라우팅되도록 세션 선호도 쿠키를 사용합니다. 이 기본 동작에는 구성이 필요하지 않지만, 다음과 같은 몇 가지 제한 사항이 있습니다.

- 애플리케이션 인스턴스가 다시 시작하거나 규모가 축소되면 애플리케이션 서버의 사용자 세션 상태가 손실됩니다.
- 애플리케이션의 세션 시간 초과 설정이 길거나 사용자 수가 고정된 경우 새 세션만 새로 시작된 인스턴스로 라우팅되므로 자동으로 크기가 조정된 새 인스턴스가 로드를 받는 데 시간이 다소 걸릴 수 있습니다.

[Azure Cache for Redis](/azure/azure-cache-for-redis/)와 같은 외부 세션 저장소를 사용하도록 Wildfly를 구성할 수 있습니다. 세션 쿠키 기반 라우팅을 해제하고 구성된 Wildfly 세션 저장소가 간섭 없이 작동하도록 하려면 [기존 ARR 인스턴스 선호도](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 구성을 해제해야 합니다.

## <a name="docker-containers"></a>Docker 컨테이너

컨테이너에 Azure 지원 Zulu JDK를 사용하려면 [Azure에 지원되는 Azul Zulu Enterprise 다운로드 페이지](https://www.azul.com/downloads/azure-only/zulu/)에 나와 있는 미리 작성된 이미지를 끌어와서 사용하거나 [Microsoft Java GitHub 리포지토리](https://github.com/Microsoft/java/tree/master/docker)의 `Dockerfile` 예제를 사용하세요.

## <a name="statement-of-support"></a>지원 정보

### <a name="runtime-availability"></a>런타임 가용성

Linux용 App Service는 Java 웹 애플리케이션의 관리되는 호스팅에 다음과 같은 두 가지 런타임을 지원합니다.

- 웹 보관(WAR) 파일로 패키징된 애플리케이션을 실행하기 위한 [Tomcat 서블릿 컨테이너](https://tomcat.apache.org/). 지원되는 버전은 8.5 및 9.0입니다.
- Java 보관(JAR) 파일로 패키징된 애플리케이션을 실행하기 위한 Java SE 런타임 환경. 지원 되는 버전은 Java 8 및 11입니다.

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다.

주 버전 업데이트는 Linux용 Azure App Service에서 새 런타임 옵션을 통해 제공될 예정입니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

[정규화된 Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 사용하여 Azure 또는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)용 제품을 개발하는 경우 [Azure에서 지원하는 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)에 대한 제품 지원이 제공됩니다.

### <a name="runtime-support"></a>런타임 지원

개발자는 [정규화된 지원 계획](https://azure.microsoft.com/support/plans/)이 있는 경우 Azure 지원을 통해 Azul Zulu JDK 관련 [문제를 제기](/azure/azure-supportability/how-to-create-azure-support-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Java 개발자용 Azure](/java/azure/) 센터를 방문하여 Azure 빠른 시작, 자습서 및 Java 참조 설명서를 찾아보세요.

Java 개발에 국한되지 않는 Linux용 App Service 사용에 대한 일반적인 질문의 답은 [App Service Linux FAQ](app-service-linux-faq.md)에서 찾을 수 있습니다.