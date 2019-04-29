---
title: Windows에서 Azure Service Fabric Linux 클러스터 설정 | Microsoft Docs
description: 이 문서에서는 Windows 개발 컴퓨터에서 실행되는 Service Fabric Linux 클러스터를 설정하는 방법을 알아봅니다. 이 기능은 크로스 플랫폼 개발에 특히 유용합니다.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719943"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows 개발자 컴퓨터에서 Linux Service Fabric 클러스터 설정

이 문서에서는 Windows 개발 컴퓨터에 로컬 Linux Service Fabric을 설정하는 방법을 설명합니다. 로컬 Linux 클러스터를 설정하는 경우 Linux 클러스터를 대상으로 하지만 Windows 컴퓨터에서 개발되는 애플리케이션을 빠르게 테스트하는 데 유용합니다.

## <a name="prerequisites"></a>필수 조건
Linux 기반 Service Fabric 클러스터는 기본적으로 Windows에서 실행되지 않습니다. 로컬 Service Fabric 클러스터를 실행하기 위해 미리 구성된 Docker 컨테이너 이미지를 제공합니다. 시작하기 전에 다음 항목이 필요합니다.

* RAM 4GB 이상
* 최신 버전의 [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker는 Linux 모드에서 실행 되어야 합니다.

>[!TIP]
> * 공식 Docker [설명서](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)에서 언급한 단계를 따라 Windows에 Docker를 설치할 수 있습니다. 
> * 설치가 완료되면 [여기](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)에서 언급된 단계를 따라 적절하게 설치되었는지 유효성을 검사합니다.


## <a name="create-a-local-container-and-setup-service-fabric"></a>로컬 컨테이너 만들기 및 Service Fabric 설치
로컬 Docker 컨테이너를 설정하고 서비스 패브릭 클러스터가 실행되도록 하려면 PowerShell에서 다음 단계를 수행합니다.


1. 다음을 사용하여 호스트에서 Docker 디먼 구성을 업데이트하고 Docker 디먼을 다시 시작합니다. 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    권장되는 업데이트 방법은 Docker 아이콘 > 설정 > 디먼 > 고급으로 이동하고 업데이트하는 것입니다. 다음으로 변경 내용을 적용하려면 Docker 디먼을 다시 시작합니다. 

2. 새 디렉터리에서 Service Fabric 이미지를 빌드할 `Dockerfile` 파일을 만듭니다.

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >컨테이너에 추가 프로그램 또는 종속성을 추가하도록 이 파일을 조정할 수 있습니다.
    >예를 들어 `RUN apt-get install nodejs -y`를 추가하면 게스트 실행 파일인 `nodejs` 애플리케이션에 대한 지원이 허용됩니다.
    
    >[!TIP]
    > 기본적으로 이렇게 하면 최신 버전의 Service Fabric으로 이미지를 가져옵니다. 특정 수정 버전은 [Docker 허브](https://hub.docker.com/r/microsoft/service-fabric-onebox/) 페이지를 참조하세요.

3. `Dockerfile`에서 다시 사용할 수 있는 이미지를 빌드하려면 터미널을 열고 `Dockerfile`을 보관하는 디렉터리에 `cd`한 후 다음을 실행합니다.

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >이 작업에 다소 시간이 걸릴 수 있지만 한 번만 수행하면 됩니다.

4. 이제 필요할 때마다 다음을 실행하여 신속하게 Service Fabric의 로컬 복사를 시작할 수 있습니다.

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >읽기 쉬운 방식으로 처리할 수 있도록 컨테이너 인스턴스의 이름을 제공합니다. 
    >
    >애플리케이션을 특정 포트에서 수신 대기하는 경우 추가 `-p` 태그를 사용하여 포트를 지정해야 합니다. 예를 들어 애플리케이션이 포트 8080에서 수신 대기하는 경우 다음 `-p` 태그를 추가합니다.
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. 잠시 후 클러스터가 시작되면, 다음 명령을 사용하여 로그를 보거나 대시보드로 이동하여 클러스터 상태([http://localhost:19080](http://localhost:19080))를 볼 수 있습니다.

    ```powershell 
    docker logs sftestcluster
    ```

6. 5단계를 성공적으로 완료한 후 Windows에서 ``http://localhost:19080``으로 이동하고 Service Fabric 탐색기를 볼 수 있습니다. 이때 Windows 개발자 컴퓨터의 도구를 사용하여 이 클러스터에 연결하고 Linux Service Fabric 클러스터용 애플리케이션을 배포할 수 있습니다. 

    > [!NOTE]
    > Eclipse 플러그 인은 현재 Windows에서 지원되지 않습니다. 

7. 작업을 모두 마쳤으면 이 명령을 사용하여 컨테이너를 중지하고 정리합니다.

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>알려진 제한 사항 
 
 다음은 Mac용 컨테이너에서 실행하는 로컬 클러스터의 알려진 제한 사항입니다. 
 
 * DNS 서비스가 실행되지 않으며 지원되지 않음 [문제 #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>다음 단계
* [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse) 시작
* 다른 [Java 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started) 확인


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
