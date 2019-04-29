---
title: Linux에서 Apache Tomcat 서버용 Azure Service Fabric 컨테이너 만들기 | Microsoft Docs
description: Azure Service Fabric의 Apache Tomcat 서버에서 실행되는 애플리케이션을 노출하는 Linux 컨테이너를 만듭니다. 애플리케이션과 Apache Tomcat 서버를 사용하여 Docker 이미지를 빌드하고, 이 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 컨테이너 애플리케이션을 빌드하고 배포합니다.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 5ae2ca352c6d3cbe02b659a97fe3147c1a31128f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947436"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Linux에서 Apache Tomcat 서버를 실행하는 Service Fabric 컨테이너 만들기
Apache Tomcat은 Java 서블릿 및 Java 서버 기술의 인기 있는 오픈 소스 구현입니다. 이 문서에서는 Apache Tomcat 및 간단한 웹 애플리케이션이 포함된 컨테이너를 빌드하고, Linux를 실행하는 Service Fabric 클러스터에 이 컨테이너를 배포한 다음, 웹 애플리케이션에 연결하는 방법에 대해 설명합니다.  

Apache Tomcat에 대한 자세한 내용은 [Apache Tomcat 홈페이지](https://tomcat.apache.org/)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건
* 다음을 실행하는 개발 컴퓨터
  * [Service Fabric SDK 및 도구](service-fabric-get-started-linux.md)
  * [Linux용 Docker CE](https://docs.docker.com/engine/installation/#prior-releases) 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry의 컨테이너 레지스트리. [Azure Portal](../container-registry/container-registry-get-started-portal.md) 또는 [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry)를 사용하여 Azure 구독에 컨테이너 레지스트리를 만들 수 있습니다. 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tomcat 이미지 빌드 및 로컬로 실행
이 섹션의 단계에 따라 Apache Tomcat 이미지와 간단한 웹 응용 프로그램을 기반으로 하는 Docker 이미지를 빌드한 다음, 로컬 시스템의 컨테이너에서 실행합니다. 
 
1. 개발 컴퓨터의 [Java로 시작하는 Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started) 샘플 리포지토리를 복제합니다.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. 디렉터리를 Apache Tomcat 서버 샘플 디렉터리(*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*)로 변경합니다.

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Docker 허브 및 Tomcat 서버 샘플에 있는 공식 [Tomcat 이미지](https://hub.docker.com/_/tomcat/)를 기반으로 하는 Docker 파일을 만듭니다. *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* 디렉터리에서 *Dockerfile*이라는 파일(파일 확장명 없음)을 만듭니다. *Dockerfile*에 다음을 추가하고 변경 내용을 저장합니다.

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/)를 참조하세요.


4. `docker build` 명령을 실행하여 웹 애플리케이션을 실행하는 이미지를 만듭니다.

   ```bash
   docker build . -t tomcattest
   ```

   이 명령은 Dockerfile의 지침을 사용하여 새 이미지를 빌드하고, `tomcattest` 이미지의 이름을 지정합니다(-t 태그 지정). 컨테이너 이미지를 빌드하기 위해 먼저 기본 이미지가 Docker 허브에서 다운로드되고 애플리케이션이 추가됩니다. 

   빌드 명령이 완료되면 `docker images` 명령을 실행하여 새 이미지에 대한 정보를 확인합니다.

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. 컨테이너 레지스트리를 푸시하기 전에 컨테이너화된 애플리케이션이 로컬로 실행되는지 확인합니다.
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name`은 컨테이너의 이름을 지정하므로 ID가 아닌 친숙한 이름을 사용하여 참조할 수 있습니다.
   * `-p`는 컨테이너와 호스트 OS 간의 포트 매핑을 지정합니다. 

   > [!Note]
   > `-p` 매개 변수를 사용하여 여는 포트는 Tomcat 애플리케이션에서 요청을 수신 대기하는 포트여야 합니다. 현재의 예제에는 *ApacheTomcat/conf/server.xml* 파일에서 HTTP 요청을 8080 포트에서 수신 대기하도록 구성된 커넥터가 있습니다. 이 포트는 호스트의 8080 포트에 매핑됩니다. 

   다른 매개 변수에 대한 자세한 내용은 [docker run 설명서](https://docs.docker.com/engine/reference/commandline/run/)를 참조하세요.

1. 컨테이너를 테스트하려면 브라우저를 열고 다음 URL 중 하나를 입력합니다. "Hello World!" 변형이 표시됩니다. 각 URL에 대한 시작 화면

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. 컨테이너를 중지하고, 개발 컴퓨터에서 이 컨테이너를 삭제합니다.

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>컨테이너 레지스트리에 Tomcat 이미지 푸시
Tomcat 이미지가 개발 컴퓨터의 컨테이너에서 실행되는지 확인했으므로 이제 이 이미지를 컨테이너 레지스트리의 리포지토리로 푸시합니다. 이 문서에서는 Azure Container Registry를 사용하여 이미지를 저장하지만, 단계를 약간 수정하면 선택한 모든 컨테이너 레지스트리를 사용할 수 있습니다. 이 문서에서 레지스트리 이름은 *myregistry*로 간주되며, 전체 레지스트리 이름은 myregistry.azurecr.io입니다. 시나리오에 맞게 적절하게 변경합니다. 

1. [레지스트리 자격 증명](../container-registry/container-registry-authentication.md)을 사용하여 컨테이너 레지스트리에 로그인하려면 `docker login`을 실행합니다.

   다음 예제는 Azure Active Directory [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)의 ID와 암호를 전달합니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 서비스 주체를 할당할 수 있습니다. 또는 레지스트리 사용자 이름과 암호를 사용하여 로그인할 수 있습니다.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. 다음 명령은 레지스트리에 대한 정규화된 경로를 사용하여 이미지의 태그 또는 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 `samples` 네임스페이스에 이미지를 배치합니다.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. 이미지를 컨테이너 레지스트리에 푸시합니다.

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Service Fabric 컨테이너 애플리케이션 빌드 및 배포
Tomcat 이미지를 컨테이너 레지스트리로 푸시했으므로 이제 레지스트리에서 Tomcat 이미지를 가져와서 클러스터의 컨테이너 서비스로 실행하는 Service Fabric 컨테이너 애플리케이션을 빌드하고 배포할 수 있습니다. 

1. 로컬 복제본의 외부(*service-fabric-java-getting-started* 디렉터리 트리의 외부)에 새 디렉터리를 만듭니다. 이 디렉터리로 전환하고 Yeoman을 사용하여 컨테이너 애플리케이션에 대한 스캐폴드를 만듭니다. 

   ```bash
   yo azuresfcontainer 
   ```
   메시지가 표시되면 다음 값을 입력합니다.

   * 응용 프로그램 이름을 지정 합니다. ServiceFabricTomcat
   * 응용 프로그램 서비스의 이름: TomcatService
   * 이미지 이름 입력: 컨테이너 레지스트리에서 컨테이너 이미지에 대 한 URL을 제공 합니다. 예를 들어 myregistry.azurecr.io/samples/tomcattest 합니다.
   * 명령: 이 항목을 비워둡니다. 이 이미지가 워크로드 진입점을 정의하지 않으므로 입력 명령을 명시적으로 지정하지 않아도 됩니다(컨테이너 내에서 명령을 실행하면 시작된 후에 컨테이너가 실행되도록 유지함).
   * 게스트 컨테이너 응용 프로그램의 인스턴스 수: 1

   ![컨테이너용 Service Fabric Yeoman 생성기](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. 서비스 매니페스트(*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*)에서 루트 **ServiceManfest** 태그 아래에 다음 XML을 추가하여 애플리케이션에서 요청을 수신 대기하는 포트를 엽니다. **엔드포인트** 태그는 엔드포인트에 대한 프로토콜과 포트를 선언합니다. 이 문서에서는 컨테이너화된 서비스가 8080 포트에서 수신 대기합니다. 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. 애플리케이션 매니페스트(*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*)의 **ServiceManifestImport** 태그 아래에 다음 XML을 추가합니다. **RepositoryCredentials** 태그의 **AccountName** 및 **Password**를 로그인하는 데 필요한 컨테이너 레지스트리의 이름과 암호로 바꿉니다.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   **ContainerHostPolicies** 태그는 컨테이너 호스트를 활성화하기 위한 정책을 지정합니다.
    
   * **PortBinding** 태그는 컨테이너 포트-호스트 포트 매핑 정책을 구성합니다. Dockerfile에서 지정한 대로 컨테이너에서 8080 포트를 노출하므로 **ContainerPort** 특성은 8080으로 설정됩니다. **EndpointRef** 특성은 이전 단계의 서비스 매니페스트에 정의된 엔드포인트인 "endpointTest"로 설정됩니다. 따라서 8080 포트에서 서비스로 들어오는 요청은 컨테이너의 8080 포트에 매핑됩니다. 
   * **RepositoryCredentials** 태그는 컨테이너에서 이미지를 가져오는 리포지토리(개인)를 사용하여 인증해야 하는 자격 증명을 지정합니다. 공용 리포지토리에서 이미지를 가져오는 경우에는 이 정책이 필요하지 않습니다.
    

12. *ServiceFabricTomcat* 폴더에서 Service Fabric 클러스터에 연결합니다. 

   * 로컬 Service Fabric 클러스터에 연결하려면 다음을 실행합니다.

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * 보안 Azure 클러스터에 연결하려면 클라이언트 인증서가 *ServiceFabricTomcat* 디렉터리에 .pem 파일로 있는지 확인하고 다음을 실행합니다. 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     앞의 명령에서 `your-certificate.pem`을 클라이언트 인증서 파일의 이름으로 바꿉니다. 개발 및 테스트 환경에서 클러스터 인증서는 종종 클라이언트 인증서로 사용됩니다. 인증서가 자체 서명되어 있지 않으면 `-no-verify` 매개 변수를 생략합니다. 
       
     클러스터 인증서는 일반적으로 로컬에서 .pfx 파일로 다운로드됩니다. PEM 형식의 인증서가 아직 없으면 다음 명령을 실행하여 .pfx 파일에서 .pem 파일을 만들 수 있습니다.

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     .pfx 파일이 암호로 보호되어 있지 않으면 마지막 매개 변수에 `-passin pass:`를 사용합니다.


13. 템플릿에 제공된 설치 스크립트를 실행하여 클러스터에 애플리케이션을 배포합니다. 스크립트는 애플리케이션 패키지를 클러스터의 이미지 저장소에 복사하고, 애플리케이션 유형을 등록하며, 애플리케이션의 인스턴스를 만듭니다.

     ```bash
     ./install.sh
     ```

   설치 스크립트가 실행되면 브라우저를 열고 Service Fabric Explorer로 이동합니다.
    
   * 로컬 클러스터에서 `http://localhost:19080/Explorer`를 사용합니다(Mac OS X에서 Vagrant를 사용하는 경우 *localhost*를 VM의 사설 IP로 바꿉니다).
   * 보안 Azure 클러스터에서 `https://PublicIPorFQDN:19080/Explorer`를 사용합니다. 
    
   **애플리케이션** 노드를 펼칩니다. 그러면 **ServiceFabricTomcatType** 애플리케이션 유형에 대한 항목 및 해당 유형의 첫 번째 인스턴스에 대한 다른 항목이 있습니다. 애플리케이션이 완전히 배포될 때까지 몇 분이 걸릴 수 있으므로 기다려주세요.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Tomcat 서버에서 애플리케이션에 액세스하려면 브라우저 창을 열고 다음 URL 중 하나를 입력합니다. 로컬 클러스터에 배포한 경우 *PublicIPorFQDN*에 *localhost*를 사용합니다. "Hello World!" 변형이 표시됩니다. 각 URL에 대한 시작 화면

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>정리
템플릿에 제공된 제거 스크립트를 사용하여 클러스터에서 애플리케이션 인스턴스를 삭제하고 애플리케이션 유형을 등록 취소합니다.

```bash
./uninstall.sh
```

이미지를 컨테이너 레지스트리에 푸시한 후에 개발 컴퓨터에서 로컬 이미지를 삭제할 수 있습니다.

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>다음 단계
* 추가 Linux 컨테이너 기능에 대한 빠른 단계는 [Linux에서 첫 번째 Service Fabric 컨테이너 애플리케이션 만들기](service-fabric-get-started-containers-linux.md)를 참조하세요.
* Linux 컨테이너에 대한 자세한 단계는 [Linux 컨테이너 앱 자습서 만들기](service-fabric-tutorial-create-container-images.md) 자습서를 참조하세요.
* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.


