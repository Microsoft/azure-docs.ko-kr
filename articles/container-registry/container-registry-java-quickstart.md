---
title: 빠른 시작 - Maven 및 Jib를 사용하여 Java 컨테이너 이미지를 빌드하고 Azure Container Registry로 푸시
description: 컨테이너화된 Java 앱을 빌드하고 Maven Jib 플러그인을 사용하여 Azure Container Registry로 푸시합니다.
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 42200fe61b0ac73a63b9f8a3317687fac60c5c02
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88078583"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>빠른 시작: Java 컨테이너 이미지를 빌드하고 Azure Container Registry로 푸시

이 빠른 시작에서는 컨테이너화된 Java 앱을 빌드하고 Maven Jib 플러그인을 사용하여 Azure Container Registry로 푸시하는 방법을 보여줍니다. Maven 및 Jib를 사용하는 것은 개발자 도구를 사용하여 Azure 컨테이너 레지스트리와 상호 작용하는 한 가지 예입니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [체험판 Azure 계정](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* [Azure CLI(명령줄 인터페이스)](/cli/azure/overview)
* 지원되는 JDK(Java Development Kit) Azure에서 개발하는 경우 사용할 수 있는 JDK에 대한 자세한 내용은 <https://aka.ms/azure-jdks>를 참조하세요.
* Apache의 [Maven](http://maven.apache.org) 빌드 도구(버전 3 이상)
* [Git](https://git-scm.com) 클라이언트
* [Docker](https://www.docker.com) 클라이언트
* [ACR Docker 자격 증명 도우미](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Spring Boot on Docker 시작 웹앱 만들기

다음 단계에서는 Spring Boot 웹 애플리케이션을 빌드하고 로컬로 테스트하는 과정을 안내합니다.

1. 명령 프롬프트에서 다음 명령을 사용하여 [Spring Boot on Docker 시작](https://github.com/spring-guides/gs-spring-boot-docker) 샘플 프로젝트를 복제합니다.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 디렉터리를 완료된 프로젝트로 변경합니다.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Maven을 사용하여 샘플 앱을 빌드하고 실행합니다.

   ```bash
   mvn package spring-boot:run
   ```

1. `http://localhost:8080`으로 이동하거나 `curl` 명령을 사용하여 웹앱을 테스트합니다.

   ```bash
   curl http://localhost:8080
   ```

다음 메시지가 표시되어야 합니다. **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Container Registry 만들기

이제, 다음 단계에 따라 Azure 리소스 그룹과 ACR을 만듭니다.

1. 다음 명령을 사용하여 Azure 계정에 로그인합니다.

   ```azurecli
   az login
   ```

1. 사용할 Azure 구독을 선택합니다.

   ```azurecli
   az account set -s <subscription ID>
   ```

1. 이 자습서에서 사용되는 Azure 리소스에 대한 리소스 그룹을 만듭니다. 다음 명령에서 자리 표시자를 고유한 리소스 이름 및 위치(예: `eastus`)로 바꿔야 합니다.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. 다음 명령을 사용하여 리소스 그룹에 프라이빗 Azure Container Registry를 만듭니다. 자리 표시자를 실제 값으로 바꿔야 합니다. 이 자습서는 이후 단계에서 샘플 앱을 이 레지스트리에 Docker 이미지로 푸시합니다.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Jib을 통해 컨테이너 레지스트리에 앱 푸시

마지막으로 프로젝트 구성을 업데이트하고 명령 프롬프트를 사용하여 이미지를 빌드하고 배포합니다.

> [!NOTE]
> 방금 만든 Azure 컨테이너 레지스트리에 로그인하려면 Docker 데몬을 실행해야 합니다. 머신에 Docker를 설치하기 위한 [공식 Docker 설명서가 여기에 있습니다](https://docs.docker.com/install/).

1. 다음 명령을 사용하여 Azure CLI에서 Azure Container Registry에 로그인합니다. 자리 표시자를 고유의 레지스트리 이름으로 바꿔야 합니다.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az configure` 명령은 `az acr` 명령에 사용할 기본 레지스트리 이름을 설정합니다.

1. Spring Boot 애플리케이션에 대해 완료된 프로젝트 디렉터리로 이동하고(예: "*C:\SpringBoot\gs-spring-boot-docker\complete*"또는" */users/robert/SpringBoot/gs-spring-boot-docker/complete*") 텍스트 편집기를 사용하여 *pom.xml* 파일을 엽니다.

1. *pom.xml* 파일의 `<properties>` 컬렉션을 다음 XML로 업데이트합니다. 자리 표시자를 레지스트리 이름으로 바꾸고 `2.2.0` 값 또는 [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)의 최신 버전으로 `<jib-maven-plugin.version>` 속성을 추가합니다.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. 다음 예제와 같이 `<plugin>` 요소에 `jib-maven-plugin`에 대한 항목이 포함되도록 *pom.xml* 파일에서 `<plugins>` 컬렉션을 업데이트합니다. MCR(Microsoft Container Registry): `mcr.microsoft.com/java/jdk:8-zulu-alpine`의 기본 이미지를 사용하고 있으며, 여기에는 공식적으로 지원되는 Azure용 JDK가 포함됩니다. 공식적으로 지원되는 JDK가 포함된 다른 MCR 기본 이미지는 [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless), [Java SE JDK 및 Maven](https://hub.docker.com/_/microsoft-java-maven)을 참조하세요.

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Spring Boot 애플리케이션에 대한 완성된 프로젝트 디렉터리로 이동하고, 다음 명령을 실행하여 이미지를 빌드하고 레지스트리로 푸시합니다.

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> 보안상의 이유로 `az acr login`으로 만든 자격 증명은 1시간 동안만 유효 합니다. *401 권한 없음* 오류가 발생하면 `az acr login -n <your registry name>` 명령을 다시 실행하여 다시 인증하면 됩니다.

## <a name="verify-your-container-image"></a>컨테이너 이미지 확인

축하합니다! 이제 Azure 지원 JDK에서 컨테이너화된 Java 앱 빌드가 ACR로 푸시되었습니다. 이제 이미지를 Azure App Service에 배포하거나 명령(자리 표시자 대체)을 사용하여 로컬로 끌어와서 이미지를 테스트할 수 있습니다.

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>다음 단계

공식적인 Microsoft 지원 Java 기본 이미지의 다른 버전은 다음을 참조하세요.

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK 및 Maven](https://hub.docker.com/_/microsoft-java-maven)

Spring과 Azure에 대한 자세한 사항은 Azure의 Spring 설명서 센터를 참조합니다.

> [!div class="nextstepaction"]
> [Azure의 Spring](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>추가 리소스

자세한 내용은 다음 리소스를 참조하세요.

* [Java 개발자를 위한 Azure](/azure/java)
* [Azure DevOps 및 Java 사용하기](/azure/devops/java)
* [Spring Boot on Docker 시작](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Azure App Service에 Spring Boot 애플리케이션 배포](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Azure Web App on Linux에 대한 사용자 지정 Docker 이미지 사용](../app-service/tutorial-custom-container.md)
