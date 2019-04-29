---
title: Jenkins를 사용한 Azure Service Fabric Linux 애플리케이션 연속 빌드 및 통합 | Microsoft Docs
description: Jenkins를 사용한 Service Fabric Linux 애플리케이션 연속 빌드 및 통합
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 3b1e6f769d5c65065d95ac96c4ab4ed10702e5cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038850"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Jenkins를 사용하여 Linux 애플리케이션 빌드 및 배포
Jenkins는 앱의 연속 통합 및 배포를 위한 인기 있는 도구입니다. Jenkins를 사용하여 Azure Service Fabric 애플리케이션을 빌드하고 배포하는 방법은 다음과 같습니다.

## <a name="topic-overview"></a>항목 개요
이 문서에서는 Jenkins 환경을 설정할 수 있는 몇 가지 방법과 Service Fabric 클러스터가 구축된 후에 애플리케이션을 배포하는 다양한 방법을 설명합니다. Jenkins를 성공적으로 설치하고, GitHub에서 변경 내용을 끌어오고, 애플리케이션을 빌드하고, 클러스터에 배포하려면 다음과 같은 일반적인 단계를 따르세요.

1. [필수 구성 요소](#prerequisites)를 설치해야 합니다.
1. 그런 후 다음 섹션 중 하나의 단계를 수행하여 Jenkins를 설정합니다.
   * [Service Fabric 클러스터 내에서 Jenkins 설정](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Service Fabric 클러스터 외부에서 Jenkins 설정](#set-up-jenkins-outside-a-service-fabric-cluster) 또는
   * [기존 Jenkins 환경에 Service Fabric 플러그 인 설치](#install-service-fabric-plugin-in-an-existing-jenkins-environment)
1. Jenkins를 설정한 후에, [Jenkins 작업 만들기 및 구성](#create-and-configure-a-jenkins-job)의 단계에 따라 애플리케이션이 변경될 때 Jenkins를 트리거하도록 GitHub를 설정하고, GitHub에서 변경 내용을 끌어오고 애플리케이션을 빌드하도록 빌드 단계의 Jenkins 작업 파이프라인을 구성합니다. 
1. 마지막으로 애플리케이션을 Service Fabric 클러스터에 배포하도록 Jenkins 작업 빌드 후 단계를 구성합니다. 클러스터에 애플리케이션을 배포하도록 Jenkins를 구성하는 방법에는 다음 두 가지가 있습니다.    
   * 개발 및 테스트 환경의 경우, [클러스터 관리 엔드포인트를 사용하여 배포 구성](#configure-deployment-using-cluster-management-endpoint)을 사용합니다. 이것이 가장 간단하게 설정할 수 있는 배포 방법입니다.
   * 프로덕션 환경의 경우 [Azure 자격 증명을 사용하여 배포를 구성](#configure-deployment-using-azure-credentials)합니다. Azure 자격 증명을 사용하면 Jenkins 작업이 Azure 리소스에 대해 가지는 액세스 권한을 제한할 수 있기 때문에 이 방법은 프로덕션 환경에서 권장됩니다. 

## <a name="prerequisites"></a>필수 조건

- Git가 로컬로 설치되어 있는지 확인합니다. 운영 체제에 따라 [Git 다운로드 페이지](https://git-scm.com/downloads)에서 적절한 Git 버전을 설치할 수 있습니다. Git을 처음 접하는 경우 [Git 설명서](https://git-scm.com/docs)에서 자세히 알아봅니다.
- 이 문서에서는 애플리케이션을 빌드 및 배포하기 위해 GitHub [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started)의 *Service Fabric 시작 샘플*을 사용합니다. 이 리포지토리를 포크하여 진행하거나 지침을 약간 수정하여 사용자 고유의 GitHub 프로젝트를 사용할 수 있습니다.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>기존 Jenkins 환경에 Service Fabric 플러그 인 설치
Service Fabric 플러그 인을 기존 Jenkins 환경에 추가하는 경우 다음이 필요합니다.

- [Service Fabric CLI](service-fabric-cli.md)(sfctl).

   > [!NOTE]
   > Jenkins가 CLI 명령을 실행할 수 있도록 사용자 수준이 아닌, 시스템 수준에서 CLI를 설치해야 합니다. 
   >

- Java 애플리케이션을 배포하려면 [Gradle 및 Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development)을 둘 다 설치합니다. 
- .NET Core 2.0 애플리케이션을 배포하려면 [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development)를 설치합니다. 

환경에 필요한 필수 구성 요소를 설치한 후 Jenkins 마켓플레이스에서 Azure Service Fabric 플러그 인을 검색한 후 설치할 수 있습니다.

이 플러그 인을 설치한 후에는 [Jenkins 작업 만들기 및 구성](#create-and-configure-a-jenkins-job)으로 건너뜁니다.


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Service Fabric 클러스터 내에서 Jenkins 설정

Service Fabric 클러스터 내부 또는 외부에서 Jenkins를 설정할 수 있습니다. 다음 섹션에서는 Azure Storage 계정을 사용하여 컨테이너 인스턴스 상태를 저장하면서 클러스터 내에서 이를 설정하는 방법을 보여 줍니다.

### <a name="prerequisites"></a>필수 조건
- Docker가 설치된 Service Fabric Linux 클러스터를 준비합니다. Azure에서 실행 중인 Service Fabric 클러스터에는 이미 Docker가 설치되어 있습니다. 클러스터를 로컬로 실행하는 경우(OneBox 개발 환경), `docker info` 명령을 사용하여 Docker가 컴퓨터에 설치되어 있는지 확인합니다. 설치되어 있지 않으면 다음 명령을 사용하여 Docker를 설치합니다.

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > 8081 포트가 클러스터의 사용자 지정 엔드포인트로 지정되었는지 확인합니다. 로컬 클러스터를 사용하는 경우 호스트 컴퓨터에서 포트 8081이 열려 있고 공용 IP 주소를 갖는지 확인합니다.
   >

### <a name="steps"></a>단계
1. 다음 명령을 사용하여 애플리케이션을 복제합니다.
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. 다음과 같이 파일 공유에서 Jenkins 컨테이너의 상태를 유지합니다.
   1. 클러스터와 **동일한 지역**에서 Azure 저장소 계정을 `sfjenkinsstorage1`과 같은 이름으로 만듭니다.
   1. `sfjenkins` 같은 이름의 저장소 계정에서 **파일 공유**를 만듭니다.
   1. 파일 공유에 대한 **연결**을 클릭하고 **Linux에서 연결** 아래에 표시된 값을 적어둡니다. 이 값은 다음과 비슷합니다.

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > cifs 공유를 마운트하려면 클러스터 노드에 cifs-utils 패키지를 설치해야 합니다.      
   >

1. `setupentrypoint.sh` 스크립트의 자리 표시자 값을 2단계의 Azure Storage 세부 정보로 업데이트합니다.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * `[REMOTE_FILE_SHARE_LOCATION]`을 위 2단계의 연결 출력 값인 `//sfjenkinsstorage1.file.core.windows.net/sfjenkins`로 바꿉니다.
   * `[FILE_SHARE_CONNECT_OPTIONS_STRING]`을 위 2단계의 `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` 값으로 바꿉니다.

1. **보안 클러스터에만 해당:** 
   
   Jenkins에서 보안 클러스터에 애플리케이션 배포를 구성하려면 Jenkins 컨테이너 내에서 클러스터 인증서에 액세스할 수 있어야 합니다. **ContainerHostPolicies** 태그 아래의 *ApplicationManifest.xml* 파일에서 이 인증서 참조를 추가하고, 클러스터 인증서의 지문으로 지문 값을 업데이트합니다.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   또한 *ApplicationManifest.xml* 파일의 **ApplicationManifest**(루트) 태그 아래에 다음 줄을 추가하고 클러스터 인증서의 지문으로 지문 값을 업데이트합니다.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. 클러스터에 연결하고 컨테이너 애플리케이션을 설치합니다.

   **보안 클러스터**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   위의 명령은 인증서를 PEM 형식으로 가져옵니다. 인증서가 PFX 형식이면 다음 명령을 사용하여 변환할 수 있습니다. PFX 파일이 암호로 보호되지 않은 경우 **passin** 매개 변수를 `-passin pass:`로 지정합니다.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **안전하지 않은 클러스터**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   그러면 클러스터에 Jenkins 컨테이너를 설치하고 Service Fabric Explorer를 사용하여 모니터링할 수 있습니다.

   > [!NOTE]
   > 클러스터에 Jenkins 이미지를 다운로드하는 데 몇 분이 걸릴 수 있습니다.
   >

1. 브라우저에서 `http://PublicIPorFQDN:8081`으로 이동합니다. 로그인하는 데 필요한 초기 관리자 암호의 경로가 제공됩니다. 
1. Jenkins 컨테이너가 실행 중인 노드를 확인하려면 Service Fabric Explorer를 살펴봅니다. 이 노드에 SSH(Secure Shell)로 로그인합니다.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. `docker ps -a`를 사용하여 컨테이너 인스턴스 ID를 가져옵니다.
1. 컨테이너에 SSH(Secure Shell) 로그인을 수행하고 Jenkins 포털에서 표시된 경로를 붙여넣습니다. 예를 들어 포털에서 경로 `PATH_TO_INITIAL_ADMIN_PASSWORD`가 표시된 경우 다음 명령을 실행합니다.

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Jenkins 시작 페이지에서 설치할 플러그 인 선택 옵션을 선택하고, **없음** 확인란을 선택하고, 설치를 클릭합니다.
1. 사용자를 만들거나 관리자로 계속하려면 선택합니다.

Jenkins를 설치한 후에는 [Jenkins 작업 만들기 및 구성](#create-and-configure-a-jenkins-job)으로 건너뜁니다.  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Service Fabric 클러스터 외부에서 Jenkins 설정

Service Fabric 클러스터 내부 또는 외부에서 Jenkins를 설정할 수 있습니다. 다음 섹션에서는 클러스터 외부에서 설정하는 방법을 보여 줍니다.

### <a name="prerequisites"></a>필수 조건
- 컴퓨터에 Docker가 설치되어 있는지 확인합니다. 다음 명령을 사용하여 터미널에서 Docker를 설치할 수 있습니다.

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  이제 터미널에서 `docker info`를 실행하면 출력에 Docker 서비스가 실행된다고 표시됩니다.

### <a name="steps"></a>단계
1. Service Fabric Jenkins 컨테이너 이미지를 끌어옵니다. `docker pull rapatchi/jenkins:latest` 이 이미지는 미리 설치된 Service Fabric Jenkins 플러그 인과 함께 제공됩니다.
1. 다음 컨테이너 이미지를 실행합니다. `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. 컨테이너 이미지 인스턴스 ID를 가져옵니다. 명령 `docker ps –a`를 사용하여 모든 Docker 컨테이너를 나열할 수 있습니다.
1. 다음 단계에 따라 Jenkins 포털에 로그인합니다.

   1. 호스트에서 Jenkins 셸에 로그인합니다. 컨테이너 ID의 처음 4자리 숫자를 사용합니다. 예를 들어, 컨테이너 ID가 `2d24a73b5964`이면 `2d24`을 사용합니다.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Jenkins 셸에서 컨테이너 인스턴스에 대한 관리자 암호를 가져옵니다.

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Jenkins 대시보드에 로그인하려면 웹 브라우저에서 URL `http://<HOST-IP>:8080`을 엽니다. 이전 단계의 암호를 사용하여 Jenkins의 잠금을 해제합니다.
   1. 선택 사항입니다. 처음으로 로그인한 후에 사용자만의 고유 계정을 만들어 다음 단계에서 사용하거나 관리자 계정을 계속 사용할 수 있습니다. 사용자를 만들면 해당 사용자를 계속 사용해야 합니다.
1. [새 SSH 키 생성 및 SSH 에이전트에 추가](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)의 단계를 사용하여 Jenkins를 사용하도록 GitHub을 설정합니다.
   * GitHub에서 제공되는 지침을 사용하여 SSH 키를 생성하고 SSH 키를 리포지토리를 호스팅하는 GitHub 계정에 추가합니다.
   * (호스트가 아닌) Jenkins Docker 셸에서 이전 링크에 언급된 명령을 실행합니다.
   * 호스트에서 Jenkins 셸에 로그인하려면 다음 명령을 사용합니다.

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Jenkins 컨테이너 이미지가 호스팅되는 클러스터 또는 컴퓨터에 공용 연결 IP 주소가 있는지 확인합니다. 그러면 Jenkins 인스턴스가 GitHub에서 알림을 받을 수 있습니다.

Jenkins를 설정한 후 다음 섹션, [Jenkins 작업 만들기 및 구성](#create-and-configure-a-jenkins-job)을 계속 진행합니다.

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins 작업 만들기 및 구성

이 섹션의 단계에서는 GitHub 리포지토리의 변경 내용에 응답하고, 변경 내용을 가져오고, 빌드하도록 Jenkins 작업을 구성하는 방법을 보여줍니다. 이 섹션이 끝나면 개발/테스트 환경으로 배포할지 또는 프로덕션 환경으로 배포할지에 따라 애플리케이션을 배포하도록 작업을 구성하는 마지막 단계가 제공됩니다. 

1. Jenkins 대시보드에서 **New Item**(새 항목)을 클릭합니다.
1. 항목 이름을 입력합니다(예: **MyJob**). **자유로운 프로젝트**를 선택하고 **확인**을 클릭합니다.
1. 작업 구성 페이지가 열립니다. Jenkins 대시보드에서 구성으로 이동하려면 작업을 클릭하고 **구성**을 클릭합니다.

1. **일반** 탭에서 **GitHub 프로젝트**에 대한 확인란을 선택하고 GitHub 프로젝트 URL을 지정합니다. 이 URL은 Jenkins CI/CD(연속 통합, 연속 배포) 흐름과 통합하려는 Service Fabric Java 애플리케이션을 호스트합니다(예: `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. **소스 코드 관리** 탭에서 **Git**을 선택합니다. Jenkins CI/CD 흐름과 통합하려는 Service Fabric Java 애플리케이션을 호스트하는 리포지토리 URL을 지정합니다(예: `https://github.com/{your-github-account}/service-fabric-java-getting-started`). 또한 빌드할 분기를 지정할 수도 있습니다(예: `/master`).
1. Jenkins와 통신하도록 *GitHub* 리포지토리를 구성합니다.

   a. GitHub 리포지토리 페이지에서 **설정** > **통합 및 서비스**로 이동합니다.

   나. **서비스 추가**를 선택하고 **Jenkins**를 입력하고 **Jenkins-GitHub 플러그 인**을 선택합니다.

   다. Jenkins Webhook URL을 입력합니다(기본적으로 `http://<PublicIPorFQDN>:8081/github-webhook/`이여야 함). **서비스 추가/업데이트**를 클릭합니다.

   d. 테스트 이벤트가 사용자의 Jenkins 인스턴스로 전송됩니다. GitHub의 웹후크에서 녹색 확인 표시가 나타나고 프로젝트가 빌드됩니다.

1. Jenkins의 **Build Triggers**(빌드 트리거) 탭에서 원하는 빌드 옵션을 선택합니다. 이 예제를 위해, 리포지토리로 푸시할 때마다 빌드를 트리거해야 하므로 **GITScm 폴링에 대한 GitHub 후크 트리거**를 선택합니다. (이전에는 이 옵션을 **변경 내용이 GitHub에 푸시될 경우에 빌드**라고 했습니다.)
1. **빌드** 탭에서 Java 애플리케이션을 빌드할지 또는 .NET Core 애플리케이션을 빌드할지에 따라 다음 중 하나를 수행합니다.

   * **Java 애플리케이션:** **빌드 단계 추가** 드롭다운 목록에서 **Gradle 스크립트 호출**을 선택합니다. **고급**을 클릭합니다. 고급 메뉴에서 애플리케이션에 대한 **루트 빌드 스크립트**의 경로를 지정합니다. 지정된 경로에서 build.gradle을 선택하면 이에 따라 적절하게 작동합니다. [ActorCounter 애플리케이션](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)의 경우 `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`입니다.

     ![Service Fabric Jenkins 빌드 작업][build-step]

   * **.NET Core 애플리케이션:** **빌드 단계 추가** 드롭다운 목록에서 **셸 실행**을 선택합니다. 표시되는 명령 상자에서 먼저 디렉터리를 build.sh 파일이 있는 경로로 변경해야 합니다. 디렉터리가 변경되면 build.sh 스크립트를 실행할 수 있으며 애플리케이션이 빌드됩니다.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     다음 스크린샷은 Jenkins 작업 이름 CounterServiceApplication을 사용하여 [카운터 서비스](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService)를 빌드하는 데 사용되는 명령 예제를 보여줍니다.

      ![Service Fabric Jenkins 빌드 작업][build-step-dotnet]

1. 빌드 후 작업에서 Service Fabric 클러스터에 앱을 배포하도록 Jenkins를 구성하려면 Jenkins 컨테이너에 해당 클러스터의 인증서 위치가 필요합니다. Jenkins 컨테이너를 클러스터 내부에서 실행할지 또는 외부에서 실행할지에 따라 다음 중 하나를 선택하고, 클러스터 인증서의 위치를 확인합니다.

   * **클러스터 내부에서 실행되는 jenkins:** 인증서 경로는 컨테이너 내에서 *Certificates_JenkinsOnSF_Code_MyCert_PEM* 환경 변수 값을 에코하여 찾을 수 있습니다.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **클러스터 외부에서 실행되는 jenkins:** 다음 단계를 수행하여 클러스터 인증서를 컨테이너에 복사합니다.
     1. 인증서는 PEM 형식이어야 합니다. PEM 파일이 없는 경우 인증서 PFX 파일에서 하나 만들 수 있습니다. PFX 파일이 암호로 보호되어 있지 않으면 호스트에서 다음 명령을 실행합니다.

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        PFX 파일이 암호로 보호되어 있는 경우 `-passin` 매개 변수에 해당 암호를 포함합니다. 예를 들면 다음과 같습니다.

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Jenkins 컨테이너에 대한 컨테이너 ID를 가져오려면 호스트에서 `docker ps`를 실행합니다.
     1. 다음 Docker 명령을 사용하여 컨테이너에 PEM 파일을 복사합니다.
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

이제 거의 끝났습니다. Jenkins 작업을 계속 열어 둡니다. 유일하게 남은 작업은 애플리케이션을 Service Fabric 클러스터로 배포하도록 빌드 후 단계를 구성하는 것입니다.

* 개발 또는 테스트 환경에 배포하려면 [클러스터 관리 엔드포인트를 사용하여 배포 구성](#configure-deployment-using-cluster-management-endpoint)의 단계를 따릅니다.
* 프로덕션 환경에 배포하려면 [Azure 자격 증명을 사용하여 배포 구성](#configure-deployment-using-azure-credentials)의 단계를 따릅니다.

## <a name="configure-deployment-using-cluster-management-endpoint"></a>클러스터 관리 엔드포인트를 사용하여 배포 구성
개발 및 테스트 환경의 경우, 클러스터 관리 엔드포인트를 사용하여 애플리케이션을 배포할 수 있습니다. 클러스터 관리 엔드포인트로 빌드 후 작업을 구성하여 애플리케이션을 배포하려면 최소한의 설정이 필요합니다. 프로덕션 환경에 배포하는 경우에는 [Azure 자격 증명을 사용하여 배포 구성](#configure-deployment-using-azure-credentials)으로 건너뛰어 배포 중에 사용할 Azure Active Directory 서비스 주체를 구성합니다.    

1. Jenkins 작업에서 **빌드 후 작업** 탭을 클릭합니다. 
1. **빌드 후 작업** 드롭다운에서 **Service Fabric 프로젝트 배포**를 선택합니다. 
1. **Service Fabric 클러스터 구성**에서 **Service Fabric 관리 엔드포인트** 라디오 단추를 선택합니다.
1. **관리 호스트**의 경우 클러스터에 대한 연결 엔드포인트를 입력합니다(예: `{your-cluster}.eastus.cloudapp.azure.com`).
1. **클라이언트 키** 및 **클라이언트 인증서**에 대해 Jenkins 컨테이너의 PEM 파일 위치를 입력합니다(예: `/var/jenkins_home/clustercert.pem`). ([Jenkins 작업 만들기 및 구성](#create-and-configure-a-jenkins-job)의 마지막 단계에서 인증서의 위치를 복사했습니다.)
1. **애플리케이션 구성** 아래에서 **애플리케이션 이름**, **애플리케이션 유형** 및 (상대)**애플리케이션 매니페스트 경로** 필드를 구성합니다.

   ![Service Fabric Jenkins 빌드 후 작업 관리 엔드포인트 구성](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. **Verify Configuration**을 클릭합니다. 성공적으로 확인되면 **저장**을 클릭합니다. 이제 Jenkins 작업 파이프라인이 완전히 구성되었습니다. [다음 단계](#next-steps)로 건너뛰어 배포를 테스트합니다.

## <a name="configure-deployment-using-azure-credentials"></a>Azure 자격 증명을 사용하여 배포 구성
프로덕션 환경의 경우, 애플리케이션을 배포하도록 Azure 자격 증명을 구성하는 것이 가장 좋습니다. 이 섹션에서는 빌드 후 작업에서 애플리케이션을 배포하는 데 사용할 Azure Active Directory 서비스 주체를 구성하는 방법을 보여줍니다. 디렉터리의 역할에 서비스 주체를 할당하여 Jenkins 작업의 사용 권한을 제한할 수 있습니다. 

개발 및 테스트 환경의 경우, 애플리케이션을 배포하도록 Azure 자격 증명이나 클러스터 관리 엔드포인트를 구성할 수 있습니다. 클러스터 관리 엔드포인트를 구성하는 방법에 대한 자세한 내용은 [클러스터 관리 엔드포인트를 사용하여 배포 구성](#configure-deployment-using-cluster-management-endpoint)을 참조하세요.   

1. Azure Active Directory 서비스 주체를 만들고 Azure 구독의 사용 권한을 할당하려면 [포털을 사용하여 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)의 단계를 따릅니다. 다음에 주의하세요.

   * 이 토픽의 단계를 수행할 때 다음 값을 복사 및 저장합니다. *애플리케이션 ID*, *애플리케이션 키*, *디렉터리 ID (테넌트 ID)* 및 *구독 ID*. Jenkins에서 Azure 자격 증명을 구성하는 데 필요하기 때문입니다.
   * 디렉터리에 대해 [필요한 권한](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)이 없는 경우 관리자에게 사용 권한을 부여하거나 사용자를 위한 서비스 주체를 만들 것을 요청해야 합니다. 또는 Jenkins의 작업을 위해 **빌드 후 작업**에서 관리 엔드포인트를 구성해야 합니다.
   * [Azure Active Directory 애플리케이션 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) 섹션에서 **로그온 URL**에 대해 올바른 형식의 URL을 입력할 수 있습니다.
   * [역할에 애플리케이션 할당](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) 섹션에서 클러스터의 리소스 그룹에 대한 *읽기 권한자* 역할에 애플리케이션을 할당할 수 있습니다.

1. Jenkins 작업으로 돌아가 **빌드 후 작업** 탭을 클릭합니다.
1. **빌드 후 작업** 드롭다운에서 **Service Fabric 프로젝트 배포**를 선택합니다. 
1. **Service Fabric 클러스터 구성**에서 **Service Fabric 클러스터 선택** 라디오 단추를 선택합니다. **Azure 자격 증명** 옆의 **추가**를 클릭합니다. **Jenkins**를 클릭하여 Jenkins 자격 증명 공급자를 선택합니다.
1. Jenkins 자격 증명 공급자의 **종류** 드롭다운에서 **Microsoft Azure 서비스 주체**를 선택합니다.
1. 1단계에서 서비스 주체를 설정할 때 저장한 값을 사용하여 다음 필드를 설정합니다.

   * **클라이언트 ID**: *애플리케이션 ID*
   * **클라이언트 비밀**: *애플리케이션 키*
   * **테넌트 ID**: *디렉터리 ID*
   * **구독 ID**: *구독 ID*
1. Jenkins에서 자격 증명을 선택할 때 사용하는 설명이 포함된 **ID**와 간단한 **설명**을 입력합니다. 그런 후 **서비스 주체 확인**을 클릭합니다. 확인이 성공하면 **추가**를 클릭합니다.

   ![Service Fabric Jenkins의 Azure 자격 증명 입력](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. **Service Fabric 클러스터 구성**으로 다시 돌아가 **Azure 자격 증명**으로 새 자격 증명이 선택되어 있는지 확인합니다. 
1. **리소스 그룹** 드롭다운에서 애플리케이션을 배포할 클러스터의 리소스 그룹을 선택합니다.
1. **Service Fabric** 드롭다운에서 애플리케이션을 배포할 클러스터를 선택합니다.
1. **클라이언트 키** 및 **클라이언트 인증서**에 대해 Jenkins 컨테이너의 PEM 파일 위치를 입력합니다. 예: `/var/jenkins_home/clustercert.pem`. 
1. **애플리케이션 구성** 아래에서 **애플리케이션 이름**, **애플리케이션 유형** 및 (상대)**애플리케이션 매니페스트 경로** 필드를 구성합니다.
    ![Service Fabric Jenkins 빌드 후 작업 Azure 자격 증명 구성](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. **Verify Configuration**을 클릭합니다. 성공적으로 확인되면 **저장**을 클릭합니다. 이제 Jenkins 작업 파이프라인이 완전히 구성되었습니다. [다음 단계](#next-steps)를 계속 진행하여 배포를 테스트합니다.

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins 플러그 인 문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계
이제 GitHub 및 Jenkins가 구성되었습니다. 리포지토리 https://github.com/Azure-Samples/service-fabric-java-getting-started의 포크에 있는 `reliable-services-actor-sample/Actors/ActorCounter` 프로젝트에서 몇 가지 샘플을 변경하는 것을 고려하세요. 원격 `master` 분기(또는 사용하도록 구성된 분기)에 변경 내용을 푸시합니다. 사용자가 구성한 Jenkins 작업 `MyJob`이 트리거됩니다. 그러면 GitHub에서 변경 내용을 가져오고, 해당 내용을 빌드하고, 빌드 후 작업에서 지정한 클러스터로 애플리케이션을 배포합니다.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

