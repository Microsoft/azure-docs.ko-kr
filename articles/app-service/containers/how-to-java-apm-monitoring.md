---
title: Linux에서 Java APM 및 모니터링 도구 구성 - Azure App Service
description: App Service Linux에서 실행되는 Java 애플리케이션의 로그 및 메트릭을 NewRelic 및 App Dynamics APM 공급자에게 보내는 방법을 알아봅니다.
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849968"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>방법: Linux에서 Azure App Service의 Java 앱을 사용한 애플리케이션 성능 모니터링 도구

이 문서에서는 NewRelic 및 AppDynamics APM(애플리케이션 성능 모니터링) 플랫폼을 사용하여 Linux의 Azure App Service에 배포된 Java 애플리케이션을 연결하는 방법을 보여 줍니다.

## <a name="configure-new-relic"></a>New Relic 구성

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 `newrelic-java-x.x.x.zip`과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 대해 SSH를 실행](/azure/app-service/containers/app-service-linux-ssh-support)하고 새 디렉터리 `/home/site/wwwroot/apm`을 만듭니다.
5. 압축이 풀린 NewRelic Java 에이전트 파일을 `/home/site/wwwroot/apm` 아래 디렉터리에 업로드합니다. 에이전트 파일은 `/home/site/wwwroot/apm/newrelic`에 있어야 합니다.
6. `/home/site/wwwroot/apm/newrelic/newrelic.yml`에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - 앱이 **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.
    - 사용 중인 경우 **WildFly**, New Relic 설명서를 참조 하십시오 [여기](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) JBoss 구성과 Java 에이전트를 설치 하는 방법에 대 한 지침에 대 한 합니다.
    - `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `javaagent` 옵션을 추가합니다.

## <a name="configure-appdynamics"></a>AppDynamics 구성

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
1. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드합니다. 파일 이름은 `AppServerAgent-x.x.x.xxxxx.zip`과 유사합니다.
1. [App Service 인스턴스에 대해 SSH를 실행](/azure/app-service/containers/app-service-linux-ssh-support)하고 새 디렉터리 `/home/site/wwwroot/apm`을 만듭니다.
1. Java 에이전트 파일을 `/home/site/wwwroot/apm` 아래 디렉터리에 업로드합니다. 에이전트 파일은 `/home/site/wwwroot/apm/appdynamics`에 있어야 합니다.
1. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<YOUR_SITE_NAME>`은 App Service 이름입니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<YOUR_SITE_NAME>`은 App Service 이름입니다.
    - 사용 중인 경우 **WildFly**, AppDynamics 설명서를 참조 하십시오 [여기](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) JBoss 구성과 Java 에이전트를 설치 하는 방법에 대 한 지침에 대 한 합니다.
