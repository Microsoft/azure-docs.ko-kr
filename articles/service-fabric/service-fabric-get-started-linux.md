---
title: Linux에서 개발 환경 설정 | Microsoft Docs
description: Linux에서 런타임 및 SDK를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 마치면 애플리케이션을 빌드할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 9f738ab5022d1378925d920818e3f89fc2a1ee6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947077"
---
# <a name="prepare-your-development-environment-on-linux"></a>Linux에서 개발 환경 준비
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux 개발 컴퓨터에서 [Azure Service Fabric 애플리케이션](service-fabric-application-model.md)을 배포하고 실행하려면 런타임 및 일반적인 SDK를 설치해야 합니다. 또한 Java 및 .NET Core 배포에 선택적 SDK를 설치할 수 있습니다. 

이 문서의 단계에서는 기본적으로 Linux에 설치하거나 Service Fabric OneBox 컨테이너 이미지 `microsoft/service-fabric-onebox`를 사용한다고 가정합니다.

Linux용 Windows 하위 시스템에 Service Fabric 런타임 및 SDK를 설치하는 것은 지원되지 않습니다. 지원되는 Azure Service Fabric CLI(명령줄 인터페이스)를 사용하여 클라우드 또는 온-프레미스의 다른 곳에서 호스팅된 Service Fabric 엔터티를 관리할 수 있습니다. CLI를 설치하는 방법에 대한 정보는 [Service Fabric CLI 설정](./service-fabric-cli.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

개발을 위해 이러한 운영 체제 버전이 지원됩니다.

* Ubuntu 16.04(`Xenial Xerus`)

    `apt-transport-https` 패키지가 설치됐는지 확인합니다.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4(Service Fabric 미리 보기 지원)


## <a name="installation-methods"></a>설치 방법

### <a name="script-installation-ubuntu"></a>스크립트 설치(Ubuntu)

편의상 스크립트는 **sfctl** CLI와 함께 Service Fabric 런타임 및 Service Fabric 일반 SDK를 설치하기 위해 제공됩니다. 다음 섹션에서 수동 설치 단계를 실행합니다. 설치 항목 및 관련 라이선스를 표시합니다. 스크립트를 실행하면 설치된 모든 소프트웨어에 대한 라이선스에 동의하는 것으로 가정합니다.

스크립트가 성공적으로 실행된 후에 [로컬 클러스터 설정](#set-up-a-local-cluster)으로 건너뛰어도 됩니다.

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>수동 설치
Service Fabric 런타임 및 일반 SDK의 수동 설치는 이 가이드의 뒷부분을 수행합니다.

## <a name="update-your-apt-sources-or-yum-repositories"></a>APT 소스 또는 Yum 리포지토리 업데이트
apt-get 명령줄 도구를 통해 SDK 및 관련 런타임 패키지를 설치하려면 먼저 APT(Advanced Packaging Tool) 원본을 업데이트해야 합니다.

### <a name="ubuntu"></a>Ubuntu

1. 터미널을 엽니다.
2. Service Fabric 리포지토리를 원본 목록에 추가합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. 원본 목록에 `dotnet` 리포지토리를 추가합니다.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

4. 새로운 Gnu Privacy Guard(GnuPG 또는 GPG) 키를 APT 인증 키(keyring)에 추가합니다.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. APT 인증 키에 공식 Docker GPG 키를 추가합니다.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Docker 리포지토리를 설정합니다.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. APT 키링에 Azul JDK 키를 추가하고 해당 리포지토리를 설정합니다.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0x219BD9C9
    sudo apt-add-repository 'deb http://repos.azulsystems.com/ubuntu stable main'
    ```

8. 새로 추가된 리포지토리에 따라 패키지 목록을 새로 고칩니다.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4(Service Fabric 미리 보기 지원)

1. 터미널을 엽니다.
2. EPEL(Enterprise Linux)에 대한 추가 패키지를 다운로드하여 설치합니다.

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. EfficiOS RHEL7 패키지 리포지토리를 시스템에 추가합니다.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. EfficiOS 패키지 서명 키를 로컬 GPG 키링으로 가져옵니다.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Microsoft RHEL 리포지토리를 시스템에 추가합니다.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. .NET SDK를 설치합니다.

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>로컬 클러스터에 대해 Service Fabric SDK 설치 및 설정

원본을 업데이트하면 SDK를 설치할 수 있습니다. Service Fabric SDK 패키지를 설치하고, 설치를 확인한 다음, 사용권 계약에 동의합니다.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   다음 명령은 Service Fabric 패키지의 라이센스 수락을 자동화합니다.
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4(Service Fabric 미리 보기 지원)

```bash
sudo yum install servicefabricsdkcommon
```

SDK 설치와 함께 제공되는 Service Fabric 런타임에는 다음 표에 나온 패키지가 포함됩니다. 

 | | DotNetCore | 자바 | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | AzulJDK 1.8 | npm에서 암시적 | 최신 |
RHEL | - | OpenJDK 1.8 | npm에서 암시적 | 최신 |

## <a name="set-up-a-local-cluster"></a>로컬 클러스터를 설정합니다.
설치가 완료된 후에 로컬 클러스터를 시작합니다.

1. 클러스터 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. 웹 브라우저를 열고 [Service Fabric Explorer](http://localhost:19080/Explorer)(`http://localhost:19080/Explorer`)로 이동합니다. 클러스터를 시작하는 경우 Service Fabric Explorer 대시보드가 표시됩니다. 클러스터가 완전히 설치될 때까지 몇 분 정도 걸릴 수 있습니다. 브라우저에서 URL이 열리지 않거나 Service Fabric Explorer에 시스템이 준비되었다고 표시되지 않으면 몇 분 후 다시 시도합니다.

    ![Linux의 Service Fabric Explorer][sfx-linux]

    이제 게스트 컨테이너 또는 게스트 실행 파일에 따라 미리 빌드된 Service Fabric 애플리케이션 패키지 또는 새 애플리케이션 패키지를 배포할 수 있습니다. Java 또는 .NET Core SDK를 사용하여 새 서비스를 빌드하려면 다음 섹션에서 제공되는 선택적 설정 단계를 수행합니다.


> [!NOTE]
> 독립 실행형 클러스터는 Linux에서 지원되지 않습니다.


> [!TIP]
> 사용 가능한 SSD 디스크가 있는 경우 탁월한 성능을 위해 devclustersetup.sh와 함께 `--clusterdataroot`를 사용하여 SSD 폴더 경로를 전달하는 것이 좋습니다.

## <a name="set-up-the-service-fabric-cli"></a>Service Fabric CLI 설정

[Service Fabric CLI](service-fabric-cli.md)는 클러스터 및 애플리케이션을 비롯하여 Service Fabric 엔터티와 상호 작용하기 위한 명령을 포함합니다. CLI를 설치하려면 [Service Fabric CLI](service-fabric-cli.md)의 지침을 따릅니다.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>컨테이너 및 게스트 실행 파일에 Yeoman 생성기 설정
Service Fabric은 Yeoman 템플릿 생성기를 사용하여 터미널에서 Service Fabric 애플리케이션을 만들 수 있는 스캐폴딩 도구를 제공합니다. Service Fabric Yeoman 템플릿 생성기를 설정하려면 이러한 단계를 수행합니다.

1. 컴퓨터에서 Node.js 및 npm을 설치합니다.

    ```bash
    sudo apt-add-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. 컴퓨터에 npm의 [Yeoman](https://yeoman.io/) 템플릿 생성기를 설치합니다.

    ```bash
    sudo npm install -g yo
    ```
3. Service Fabric Yeo 컨테이너 생성기 및 npm의 게스트 실행 파일 생성기를 설치합니다.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

생성기를 설치한 후에는 각각 `yo azuresfguest` 또는 `yo azuresfcontainer`를 실행하여 게스트 실행 파일 또는 컨테이너 서비스를 만듭니다.

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 개발 설정

[Ubuntu용 .NET Core 2.0 SDK](https://www.microsoft.com/net/core#linuxubuntu)를 설치하여 [C# Service Fabric 애플리케이션을 만들기](service-fabric-create-your-first-linux-application-with-csharp.md)를 시작합니다. NuGet.org는 현재 미리 보기 상태인 .NET Core 2.0 Service Fabric 애플리케이션의 패키지를 호스팅합니다.

## <a name="set-up-java-development"></a>Java 개발 설정

Java를 사용하여 Service Fabric 서비스를 빌드하려면 Gradle을 설치하여 빌드 작업을 실행합니다. 아래 명령을 실행하여 Gradle을 설치합니다. Service Fabric Java 라이브러리는 Maven에서 끌어옵니다.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4(Service Fabric 미리 보기 지원)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Java 실행 파일에 대한 Service Fabric Yeo 생성기를 설치해야 합니다. [Yeoman 설치](#set-up-yeoman-generators-for-containers-and-guest-executables)를 확인한 후, 다음 명령을 실행합니다.

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Eclipse 플러그 인 설치(선택 사항)

Java 개발자용 또는 Java EE 개발자용 Eclipse IDE 내에서 Service Fabric용 Eclipse 플러그 인을 설치할 수 있습니다. Eclipse를 사용하여 Service Fabric Java 애플리케이션 외에도 Service Fabric 게스트 실행 파일 애플리케이션 및 컨테이너 애플리케이션을 만들 수 있습니다.

> [!IMPORTANT]
> Service Fabric 플러그 인에는 Eclipse Neon 이상 버전이 필요합니다. Eclipse의 버전을 확인하는 방법은 이 부분 뒤에 나오는 지침을 참조하십시오. 이전 버전의 Eclipse가 설치되어있는 경우 [Eclipse 사이트](https://www.eclipse.org)에서 최신 버전을 다운로드할 수 있습니다. 기존 Eclipse 설치 위에 설치하는 것(덮어쓰기)은 좋지 않습니다. 설치 관리자를 실행하기 전에 제거하거나 다른 디렉터리에 새 버전을 설치합니다.
> 
> Ubuntu의 경우 패키지 설치 관리자(`apt` 또는 `apt-get`)를 사용하는 대신 Eclipse 사이트에서 직접 설치하는 것이 좋습니다. 이렇게 하면 최신 버전의 Eclipse를 확보할 수 있습니다. Java 개발자용 또는 Java EE 개발자용 Eclipse IDE를 설치할 수 있습니다.

1. Eclipse에서 Eclipse Neon 이상 및 Buildship 버전 2.2.1 이상이 설치되어 있는지 확인합니다. **도움말** > **Eclipse정보** > **설치 세부 정보**를 차례로 선택하여 설치된 구성 요소의 버전을 확인합니다. [Eclipse Buildship: Gradle용 Eclipse 플러그 인][buildship-update]의 지침을 사용하여 Buildship을 업데이트할 수 있습니다.

2. Service Fabric 플러그 인을 설치하려면 **도움말** > **새 소프트웨어 설치**를 차례로 선택합니다.

3. **작업 대상** 상자에서 **https://dl.microsoft.com/eclipse**을 입력합니다.

4. **추가**를 선택합니다.

    ![사용 가능한 소프트웨어 페이지][sf-eclipse-plugin]

5. **ServiceFabric** 플러그 인을 선택하고 **다음**을 선택합니다.

6. 설치 단계를 수행합니다. 최종 사용자 라이선스 계약에 동의합니다.

Service Fabric Eclipse 플러그 인이 이미 설치되어 있으면 최신 버전인지 확인합니다. **도움말** > **Eclipse 정보** > **설치 세부 정보**를 선택하여 확인합니다. 그런 다음, 설치된 플러그 인 목록에서 Service Fabric을 검색합니다. 최신 버전을 사용할 수 있는 경우 **업데이트**를 선택합니다.

자세한 내용은 [Eclipse Java 애플리케이션 배포를 위한 Azure Service Fabric 플러그 인](service-fabric-get-started-eclipse.md)을 참조하세요.

## <a name="update-the-sdk-and-runtime"></a>SDK 및 런타임 업데이트

최신 버전의 SDK 및 런타임으로 업데이트하려면 다음 명령을 실행하세요.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Maven에서 Java SDK 이진 파일을 업데이트하려면 최신 버전을 가리키도록 ``build.gradle`` 파일에서 해당 이진 파일의 버전 정보를 업데이트해야 합니다. 버전을 업데이트해야 하는 위치를 알려면 [Service Fabric 시작 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started)에서 모든 ``build.gradle`` 파일을 참조하면 됩니다.

> [!NOTE]
> 패키지를 업데이트하면 로컬 개발 클러스터의 실행이 중지될 수 있습니다. 이 문서의 지침에 따라 업그레이드한 후에 로컬 클러스터를 다시 시작합니다.

## <a name="remove-the-sdk"></a>SDK 제거
Service Fabric SDK를 제거하려면 다음 명령을 실행합니다.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4(Service Fabric 미리 보기 지원)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>다음 단계

* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Linux에서 첫 번째 CSharp 애플리케이션 만들기](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)
* [Windows에서 Linux 개발 환경 준비](service-fabric-local-linux-cluster-windows.md)
* [Service Fabric CLI를 사용하여 애플리케이션 관리](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows 및 Linux 차이점](service-fabric-linux-windows-differences.md)
* [Linux 클러스터에서 운영 체제 패치 자동화](service-fabric-patch-orchestration-application-linux.md)
* [Service Fabric CLI 시작](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
