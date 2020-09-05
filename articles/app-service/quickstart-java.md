---
title: '빠른 시작: Azure App Service에서 Java 앱 만들기'
description: 몇 분 안에 첫 번째 Java Hello World를 Azure App Service에 배포합니다. Azure Web App Plugin for Maven을 사용하면 Java 앱을 편리하게 배포할 수 있습니다.
keywords: azure, 앱 서비스, 웹앱, windows, linux, java, maven, 빠른 시작
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 274228ea5aa9ac9de9725176c8b6221ee9e9542e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182700"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>빠른 시작: Azure App Service에서 Java 앱 만들기

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다.  이 빠른 시작에서는 [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)과 함께 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 Java 웹 보관(WAR) 파일을 배포하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서에서는 WAR 파일에 패키지된 Java 앱만 사용합니다. 플러그 인에서 JAR 웹 애플리케이션도 지원됩니다. [Linux 기반 App Service에 Java SE JAR 파일 배포](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)를 방문하여 직접 확인해 보세요.

> [!NOTE]
> IntelliJ 및 Eclipse와 같은 인기 있는 IDE를 사용하여 동일한 작업을 수행할 수도 있습니다. [Azure Toolkit for IntelliJ 빠른 시작](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) 또는 [Azure Toolkit for Eclipse 빠른 시작](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)에서 유사한 문서를 확인하세요.
>
![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java 앱 만들기

Cloud Shell 프롬프트에서 다음 Maven 명령을 실행하여 `helloworld`라는 새 앱을 만듭니다.

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

그런 다음, 작업 디렉터리를 프로젝트 폴더로 변경합니다.

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Maven 플러그 인 구성

Azure App Service에 대한 배포 프로세스는 Azure CLI에서 Azure 자격 증명을 자동으로 적용할 수 있습니다. Azure CLI가 로컬로 설치되어 있지 않으면 Maven 플러그 인은 Oauth 또는 디바이스 로그인으로 사용자를 로그인합니다. 필요한 경우 [Maven 플러그 인으로 인증](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)에 대한 세부 정보를 확인합니다.

아래의 maven 명령을 실행하여 배포를 구성할 수 있습니다.
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows" 
다음 중 선택하라는 메시지가 표시됩니다. 
* **OS(기본값: `linux`)**
* **Java 버전(기본값: `1.8`)**
* **웹 컨테이너(기본값: `tomcat 8.5`)** 
 
첫 번째 단계에서 **`2`** 를 입력하여 **Windows** OS를 선택해야 합니다. 나머지 다른 구성은 **ENTER** 키를 눌러 기본값으로 두면 됩니다. 마지막으로 **확인(Y/N)** 프롬프트에서 **`Y`** 를 눌러 구성을 완료합니다.

샘플 프로세스는 다음과 같습니다.

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  

다음 중 선택하라는 메시지가 표시됩니다. 
* **OS(기본값: `linux`)**
* **Java 버전(기본값: `Java 8`)**
* **웹 컨테이너(기본값: `Tomcat 8.5`)** 

**ENTER**를 눌러 모든 구성을 기본값으로 둘 수 있습니다. 마지막으로 **확인(Y/N)** 프롬프트에서 **`Y`** 를 눌러 구성을 완료합니다.
샘플 프로세스는 다음과 같습니다.

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```
::: zone-end

필요한 경우 `pom.xml`에서 직접 App Service에 대한 구성을 수정할 수 있습니다. 몇 가지 일반적인 사항은 다음과 같습니다.

 속성 | 필수 | Description | 버전
---|---|---|---
`<schemaVersion>` | false | 구성 스키마의 버전을 지정합니다. 지원되는 값은 `v1`, `v2`입니다. | 1.5.2
`<resourceGroup>` | true | 웹앱에 대한 Azure 리소스 그룹입니다. | 0.1.0+
`<appName>` | true | 웹앱의 이름입니다. | 0.1.0+
`<region>` | true | 웹앱이 호스트되는 지역을 지정합니다(기본값: **westeurope**). [지원되는 지역](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 섹션에 있는 모든 유효한 지역입니다. | 0.1.0+
`<pricingTier>` | false | 웹앱에 대한 가격 책정 계층입니다. 기본값은 **P1V2**입니다.| 0.1.0+
`<runtime>` | true | 런타임 환경 구성이며, [여기](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)에서 세부 정보를 볼 수 있습니다. | 0.1.0+
`<deployment>` | true | 배포 구성이며, [여기](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)에서 세부 정보를 볼 수 있습니다. | 0.1.0+

`<appName>` 및 `<resourceGroup>`값을 잘 보어야 합니다(이 데모에서는 `helloworld-1590394316693` 및 `helloworld-1590394316693-rg`). 나중에 사용됩니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>앱 배포

Azure App Service에 대한 배포 프로세스는 Azure CLI의 계정 자격 증명을 사용합니다. 계속하려면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```
그런 후, 다음 명령을 사용하여 Java 앱을 Azure에 배포할 수 있습니다.

```bash
mvn package azure-webapp:deploy
```

배포가 완료되면 애플리케이션이 `http://<appName>.azurewebsites.net/`(이 데모에서는 `http://helloworld-1590394316693.azurewebsites.net`)에 준비됩니다. 로컬 웹 브라우저에서 url을 열면 다음이 표시됩니다.

![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**축하합니다.** App Service에 첫 번째 Java 앱을 배포했습니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 예상되는 경우 포털에서 리소스 그룹을 삭제하거나 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Java를 사용하여 Azure SQL Database에 연결](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Java를 사용하여 Azure DB for MySQL에 연결](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Java를 사용하여 Azure DB for PostgreSQL에 연결](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Java 개발자 리소스용 Azure](/java/azure/)

> [!div class="nextstepaction"]
> [Java 앱 구성](configure-language-java.md)

> [!div class="nextstepaction"]
> [Jenkins를 사용하는 CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [사용자 지정 도메인 매핑](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Azure용 Maven 플러그 인에 대한 자세한 정보](https://github.com/microsoft/azure-maven-plugins)
