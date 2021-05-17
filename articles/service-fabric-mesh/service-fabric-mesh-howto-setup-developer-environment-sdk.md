---
title: Service Fabric Mesh용 Windows 개발 환경 설정
description: Service Fabric Mesh 애플리케이션을 만들고 Azure Service Fabric Mesh에 배포할 수 있도록 Windows 개발 환경을 설정합니다.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625738"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Service Fabric Mesh 앱을 빌드하기 위한 Windows 개발 환경 설정

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

Windows 개발 머신에서 Azure Service Fabric Mesh 애플리케이션을 빌드하고 실행하려면 다음이 필요합니다.

* Docker
* Visual Studio 2017 이상
* Service Fabric Mesh 런타임
* Service Fabric Mesh SDK 및 도구

그리고, 다음 Windows 버전 중 하나

* Windows 10(Enterprise, Professional 또는 Education) 버전 1709 (Fall Creators 업데이트) 또는 1803(Windows 10 2018년 4월 업데이트)
* Windows Server 버전 1709
* Windows Server 버전 1803

실행되는 Windows 버전에 따라 설치된 모든 구성 요소를 가져오는 데 도움이 되는 지침은 다음과 같습니다.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 이상은 Service Fabric Mesh 애플리케이션을 배포하는 데 필요합니다. [설치 버전 15.6.0][download-visual-studio] 이상은 다음 워크로드를 사용하도록 설정합니다.

* ASP.NET 및 웹 개발
* Azure 개발

## <a name="install-docker"></a>Docker 설치

Docker가 이미 설치되어 있으면 최신 버전인지 확인합니다. 새 버전이 없으면 Docker에서 메시지를 표시할 수 있지만, 수동으로 최신 버전이 있는지 확인하세요.

#### <a name="install-docker-on-windows-10"></a>Windows 10에 Docker 설치

Service Fabric Mesh에서 사용하는 컨테이너화된 Service Fabric 앱을 지원하기 위해 [Docker Community Edition for Windows][download-docker] 최신 버전을 다운로드하여 설치합니다.

설치하는 동안 요청 시 **Linux 컨테이너 대신 Windows 컨테이너 사용** 을 선택합니다.

컴퓨터에서 Hyper-V를 사용하도록 설정되지 않은 경우 Docker 설치 시 설정하라는 메시지가 표시됩니다. 메시지가 표시되면 **확인** 을 클릭하여 설정합니다.

#### <a name="install-docker-on-windows-server-2016"></a>Windows Server 2016에 Docker 설치

Hyper-V 역할을 사용하도록 설정하지 않은 경우 관리자 권한으로 PowerShell을 열고 다음 명령을 실행하여 Hyper-V를 사용하도록 설정한 다음, 컴퓨터를 다시 시작합니다. 자세한 내용은 [Windows Server용 Docker Enterprise Edition][download-docker-server]을 참조하세요.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

컴퓨터를 다시 시작합니다.

관리자 권한으로 PowerShell을 열고 다음 명령을 실행하여 Docker를 설치합니다.

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK 및 도구

다음 순서에 따라 Service Fabric Mesh 런타임, SDK 및 도구를 설치합니다.

1. 웹 플랫폼 설치 관리자를 사용하여 [Service Fabric Mesh SDK][download-sdkmesh]를 설치합니다. 그러면 Microsoft Azure Service Fabric SDK 및 런타임도 설치합니다.
2. Visual Studio Marketplace에서 [Visual Studio Service Fabric Mesh 도구(미리 보기) 확장][download-tools]을 설치합니다.

## <a name="build-a-cluster"></a>클러스터 빌드

> [!IMPORTANT]
> Docker는 클러스터를 빌드하기 전에 실행 **해야** 합니다.
> 터미널 창을 열고 오류가 발생하는지를 확인하는 `docker ps`를 실행하여 Docker가 실행되는지 테스트합니다. 응답이 오류를 나타내지 않는 경우 Docker가 실행되고 클러스터를 빌드할 준비가 되었습니다.

> [!Note]
> Windows Fall Creators 업데이트(버전 1709) 머신에서 개발하는 경우 Windows 버전 1709 Docker 이미지만 사용할 수 있습니다.
> Windows 10 2018년 4월 업데이트(버전 1803) 머신에서 개발하는 경우 Windows 버전 1709 또는 1803의 Docker 이미지를 사용합니다.

Visual Studio를 사용하는 경우 계정이 없으면 Visual Studio가 자동으로 로컬 클러스터를 만들기 때문에 이 섹션을 건너뛰어도 됩니다.

한 번에 단일 Service Fabric 앱을 만들고 실행할 때 최상의 디버깅 성능을 얻으려면 단일 노드 로컬 개발 클러스터를 만듭니다. 한 번에 여러 애플리케이션을 실행하는 경우 5개 노드 로컬 개발 클러스터를 만듭니다. Service Fabric Mesh 프로젝트를 배포하거나 디버그할 때마다 클러스터가 실행되고 있어야 합니다.

런타임, SDK, Visual Studio 도구 및 Docker를 설치한 후에 Docker를 실행하고 개발 클러스터를 만듭니다.

1. PowerShell 창을 닫습니다.
2. 관리자 권한으로 새롭게 상승된 PowerShell 창을 엽니다. 이 단계는 최근에 설치한 Service Fabric 모듈을 로드하기 위해 필요합니다.
3. 다음 PowerShell 명령을 실행하여 개발 클러스터를 만듭니다.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. 로컬 클러스터 관리자 도구를 시작하려면 다음 PowerShell 명령을 실행합니다.

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. 서비스 클러스터 관리자 도구를 실행하면(시스템 트레이에 표시됨) 해당 항목을 마우스 오른쪽 단추로 클릭하고 **로컬 클러스터 시작** 을 클릭합니다.

![그림 1 - 로컬 클러스터 시작](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

이제 Service Fabric Mesh 애플리케이션을 만들 준비가 되었습니다!

## <a name="next-steps"></a>다음 단계

[Azure Service Fabric 앱 만들기](service-fabric-mesh-tutorial-create-dotnetcore.md) 자습서를 읽습니다.

[일반적인 질문 및 알려진 문제](service-fabric-mesh-faq.md)에 대한 답변을 찾습니다.

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
