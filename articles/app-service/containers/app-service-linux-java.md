---
title: Linux 기반의 App Service에 대한 Java 개발자 가이드 - Azure | Microsoft Docs
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
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 5c9f70650f518c72a75d9a7826e7cbc30a95a00c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852724"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Linux 기반의 App Service에 대한 Java 개발자 가이드

Linux 기반의 Azure App Service는 Java 개발자가 Tomcat 또는 Java SE(Standard Edition) 패키지 웹 애플리케이션을 신속하게 구축하고, 완벽하게 관리되는 Linux 기반 서비스에 배포하고, 규모를 조정할 수 있게 도와줍니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드에서는 Linux용 App Service를 사용하는 Java 개발자를 위해 핵심 개념을 설명하고 지침을 제공합니다. Linux용 Azure App Service를 사용한 경험이 없는 분들은 먼저 [Java 빠른 시작](quickstart-java.md)을 정독해야 합니다. Java 개발에 국한되지 않는 Linux용 App Service 사용에 대한 일반적인 질문의 답은 [App Service Linux FAQ](app-service-linux-faq.md)에서 찾을 수 있습니다.

## <a name="deploying-your-app"></a>앱 배포

사용할 수 있습니다 [Azure App Service의 Maven 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) .jar와.war 파일을 배포 합니다. 사용 하 여 인기 있는 Ide 사용 하 여 배포도 지원 됩니다 [IntelliJ 용 Azure 도구 키트](/java/azure/intellij/azure-toolkit-for-intellij) 하거나 [Eclipse 용 Azure 도구 키트](/java/azure/eclipse/azure-toolkit-for-eclipse)합니다.

그렇지 않으면 배포 방법 보관 형식에 따라 달라 집니다.

- .war 파일을 Tomcat에 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)를 참조하세요.
- Java SE 이미지에서 .jar 파일을 배포하려면 Kudu 사이트의 `/api/zipdeploy/` 엔드포인트를 사용합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)를 참조하세요.

FTP를 사용하여 .war 또는.jar을 배포하지 마십시오. FTP 도구는 시작 스크립트, 종속성 또는 기타 런타임 파일을 업로드하기 위해 설계되었습니다. 웹앱을 배포하기 위한 최적의 선택은 아닙니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 이러한 진단 도구를 사용하는 방법에 대한 자세한 내용은 [Azure App Service 진단 개요](/azure/app-service/overview-diagnostics)를 참조하세요.

## <a name="application-performance-monitoring"></a>애플리케이션 성능 모니터링

Linux의 App Service에서 실행되는 Java 앱으로 New Relic 및 AppDynamics을 구성하는 방법에 대한 지침은 [Linux에서 Azure App Service의 Java 앱을 사용한 애플리케이션 성능 모니터링 도구](how-to-java-apm-monitoring.md)를 참조하세요.

### <a name="ssh-console-access"></a>SSH 콘솔 액세스

앱을 실행 하는 Linux 환경에 SSH 연결을 사용할 수 있습니다. 웹 브라우저 또는 로컬 터미널을 통해 Linux 시스템에 연결하는 방법에 대한 전체 지침은 [Linux 기반의 Azure App Service에 대한 SSH 지원](/azure/app-service/containers/app-service-linux-ssh-support)을 참조하세요.

### <a name="streaming-logs"></a>스트리밍 로그

빠른 디버깅 및 문제 해결을 위해 Azure CLI를 사용하여 콘솔에 로그를 스트리밍할 수 있습니다. `az webapp log config` 명령을 사용하여 로깅을 사용하도록 CLI를 구성합니다.

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

그런 다음, `az webapp log tail` 명령을 사용하여 로그를 콘솔로 스트리밍합니다.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

자세한 내용은 [Azure CLI를 사용하여 로그 스트리밍](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)을 참조하세요.

### <a name="app-logging"></a>앱 로깅

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](/azure/app-service/troubleshoot-diagnostic-logs#enablediag)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 응용 프로그램을 구성합니다. Java 및 Tomcat 응용 프로그램 로그에서 찾을 수 있습니다는 `/home/LogFiles/Application/` 디렉터리입니다.

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](/azure/application-insights/app-insights-java-trace-logs)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.

### <a name="troubleshooting-tools"></a>문제 해결 도구

기본 제공 Java 이미지를 기반으로 합니다 [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 운영 체제입니다. 사용 된 `apk` 문제 해결을 설치 하려면 패키지 관리자 도구 또는 명령입니다.

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux 용 azure App Service에서 Azure portal 및 CLI를 통해 사용자 지정 하 고 상자 튜닝을 지원합니다. 비 Java 관련 웹앱 구성에 대한 다음 문서를 검토하세요.

- [App Service 설정 구성](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [사용자 지정 도메인 설정](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL 사용](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN 추가](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Kudu 사이트를 구성 합니다.](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

Tomcat 및 Java SE 환경에서 할당 된 메모리 또는 기타 JVM 런타임 옵션을 설정 하려면 만듭니다는 [응용 프로그램 설정](/azure/app-service/web-sites-configure#app-settings) 라는 `JAVA_OPTS` 옵션을 사용 합니다. App Service Linux는 시작될 때 이 설정을 Java 런타임에 환경 변수로 전달합니다.

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에서 `-Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 `JAVA_OPTS`라고 하는 새 앱 설정을 만듭니다.

Maven 플러그 인에서 앱 설정을 구성 하려면 Azure 플러그 인 섹션의 설정/값 태그를 추가 합니다. 다음 예제에서는 특정 최소 및 최대 Java 힙 크기를 설정합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service 계획에서 배포 슬롯 하나를 사용하여 단일 애플리케이션을 실행하는 개발자는 다음 옵션을 사용할 수 있습니다.

- B1 및 S1의 경우 인스턴스: `-Xms1024m -Xmx1024m`
- B2 및 s2의 경우 인스턴스: `-Xms3072m -Xmx3072m`
- B3 및 S3 인스턴스: `-Xms6144m -Xmx6144m`

애플리케이션 힙 설정을 튜닝할 때 App Service 계획 세부 정보를 검토하고 여러 애플리케이션 및 배포 슬롯 요구 사항을 고려하여 최적의 메모리 할당을 찾아보세요.

JAR 응용 프로그램을 배포 하는 경우 그 이름은 `app.jar` 기본 제공 이미지를 앱에 올바르게 식별할 수 있도록 합니다. (Maven 플러그 인은 자동으로 변경 합니다.) 경우 원하지 않는에 JAR을 바꾸려면 `app.jar`, JAR를 실행 하려면 명령 사용 하 여 셸 스크립트를 업로드할 수 있습니다. 이 스크립트에 전체 경로 붙여 합니다 [시작 파일](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file) 포털의 구성 섹션에서 텍스트 상자에 붙여넣습니다.

### <a name="turn-on-web-sockets"></a>웹 소켓 켜기

애플리케이션의 Azure Portal **애플리케이션 설정**에서 웹 소켓을 켭니다. 설정을 적용하려면 애플리케이션을 다시 시작해야 합니다.

Azure CLI에서 다음 명령을 사용하여 웹 소켓 지원을 켭니다.

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true
```

그런 다음, 애플리케이션을 다시 시작합니다.

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>기본 문자 인코딩 설정

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에 `-Dfile.encoding=UTF-8` 값을 사용하여 `JAVA_OPTS`이라고 하는 새 앱 설정을 만듭니다.

또는 App Service Maven 플러그 인을 사용하여 앱 설정을 구성할 수 있습니다. 플러그 인 구성에서 설정 이름 및 값 태그를 추가합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>시작 시간 제한을 조정합니다

Java 응용 프로그램 특히 큰 경우에 시작 시간 제한을 늘려야 합니다. 이렇게 하려면 응용 프로그램 설정 만들기 `WEBSITES_CONTAINER_START_TIME_LIMIT` 앱 서비스 시간 초과 전까지 대기 해야 하는 시간 (초) 수로 설정 합니다. 최대값은 `1800` 시간 (초)입니다.

## <a name="secure-applications"></a>보안 애플리케이션

Linux용 App Service에서 실행되는 Java 애플리케이션의 [보안 모범 사례](/azure/security/security-paas-applications-using-app-services) 집합은 다른 애플리케이션과 동일합니다.

### <a name="authenticate-users"></a>사용자 인증

사용 하 여 Azure portal에서 앱 인증을 설정 합니다 **인증 및 권한 부여** 옵션입니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다.  자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](/azure/app-service/configure-authentication-provider-aad) 및 기타 ID 공급자 관련 문서를 참조하세요.

여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) 문서의 지침을 따르세요.

Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)를 사용하여 친숙한 Spring Security 주석 및 API로 애플리케이션을 보호할 수 있습니다. `application.properties` 파일에서 최대 헤더 크기를 늘려야 합니다. 이 값은 `16384`로 설정하는 것이 좋습니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[기존 사용자 지정 SSL 인증서 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)의 지침에 따라 기존 SSL 인증서를 업로드하고 애플리케이션의 도메인 이름에 바인딩합니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

### <a name="use-keyvault-references"></a>사용 하 여 KeyVault 참조

[Azure KeyVault](../../key-vault/key-vault-overview.md) 액세스 정책 및 감사 기록 사용 하 여 중앙 집중식된 보안 관리를 제공 합니다. KeyVault에 비밀 (예: 암호 또는 연결 문자열)을 저장 하 고 환경 변수를 통해 응용 프로그램에서 이러한 비밀에 액세스할 수 있습니다.

먼저 지침을 따르세요 [Key Vault에 응용 프로그램 액세스 권한을 부여](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) 하 고 [비밀에 대 한 KeyVault 참조 응용 프로그램 설정에서 하](../app-service-key-vault-references.md#reference-syntax)합니다. App Service 터미널을 원격으로 액세스 하는 동안 환경 변수를 인쇄 하 여 암호에 대 한 참조 확인을 확인할 수 있습니다.

Spring 또는 Tomcat 구성 파일에서 이러한 암호를 삽입, 환경 변수 주입 구문을 사용 하 여 (`${MY_ENV_VAR}`). Spring 구성 파일에 대 한이 설명서를 참조 하십시오이 온 [구성을 표면화](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)합니다.

## <a name="data-sources"></a>데이터 원본

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

JDBC(Java Database Connectivity) 또는 JPA(Java Persistence API)를 사용하도록 Tomcat을 구성하려면 먼저 시작 시 Tomcat에서 읽은 `CATALINA_OPTS` 환경 변수를 사용자 지정합니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

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

#### <a name="shared-server-level-resources"></a>서버 수준 리소스 공유

1. 아직 구성이 있는 경우 SSH를 사용하여 `/usr/local/tomcat/conf`의 내용을 App Service Linux 인스턴스의 `/home/tomcat/conf`에 복사합니다.

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>마지막으로, 드라이버 Jar Tomcat classpath에 배치 하 고 App Service를 다시 시작

1. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 `/home/tomcat/lib` 디렉터리에 배치합니다. (아직 존재하지 않는 경우 이 디렉터리를 만듭니다.) 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.  
   1. Azure App Service 웹앱 확장을 설치합니다.

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. 다음 CLI 명령을 실행하여 로컬 시스템에서 App Service로 SSH 터널을 만듭니다.

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 파일을 `/home/tomcat/lib` 폴더에 업로드합니다.

      또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)을 따릅니다.

2. 서버 수준 데이터 원본을 만든 경우 App Service Linux 애플리케이션을 다시 시작합니다. Tomcat이 `CATALINA_HOME`을 `/home/tomcat/conf`로 다시 설정하고 업데이트된 구성을 사용합니다.

### <a name="spring-boot"></a>Spring Boot

연결 문자열 만들기 및 삽입으로 제안에 Spring Boot 응용 프로그램에서 데이터 원본에 연결 하려면 프로그램 `application.properties` 파일입니다.

1. App Service 블레이드의 "응용 프로그램 설정" 섹션에서는 문자열의 이름을 설정할 JDBC 연결 문자열 값 필드에 붙여 넣고 유형을 "Custom"으로 설정 합니다. 슬롯 설정으로 필요에 따라이 연결 문자열을 설정할 수 있습니다.

    ![포털에서 연결 문자열을 만드는 중입니다.][1]

    이 연결 문자열은 라는 환경 변수로 응용 프로그램에 액세스할 수 있는 `CUSTOMCONNSTR_<your-string-name>`합니다. 위에서 만든 연결 문자열 이름은 예를 들어 `CUSTOMCONNSTR_exampledb`합니다.

2. 사용자 `application.properties` 파일에서이 연결 문자열 환경 변수 이름으로 참조 합니다. 예를 들어 다음 사용 됩니다.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

참조 하세요 합니다 [데이터 액세스에 대 한 Spring Boot 설명서](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) 및 [구성을 표면화](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) 이 항목에 대 한 자세한 내용은 합니다.

## <a name="docker-containers"></a>Docker 컨테이너

컨테이너에 Azure 지원 Zulu JDK를 사용하려면 [Azure에 지원되는 Azul Zulu Enterprise 다운로드 페이지](https://www.azul.com/downloads/azure-only/zulu/)에 나와 있는 미리 작성된 이미지를 끌어와서 사용하거나 [Microsoft Java GitHub 리포지토리](https://github.com/Microsoft/java/tree/master/docker)의 `Dockerfile` 예제를 사용하세요.

## <a name="runtime-availability-and-statement-of-support"></a>런타임 가용성 및 문 지원

Linux용 App Service는 Java 웹 애플리케이션의 관리되는 호스팅에 다음과 같은 두 가지 런타임을 지원합니다.

- 웹 보관(WAR) 파일로 패키징된 애플리케이션을 실행하기 위한 [Tomcat 서블릿 컨테이너](https://tomcat.apache.org/). 지원되는 버전은 8.5 및 9.0입니다.
- Java 보관(JAR) 파일로 패키징된 애플리케이션을 실행하기 위한 Java SE 런타임 환경. 유일하게 지원되는 주 버전은 Java 8입니다.

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원

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

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
