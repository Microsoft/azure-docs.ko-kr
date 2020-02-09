---
title: Azure 개발 공간 구성과 작동
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Power Azure Dev Spaces 프로세스 및 azds 구성 파일에서 구성 하는 방법에 대해 설명 합니다.
keywords: azds, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771141"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Azure 개발 공간 구성과 작동

Kubernetes 응용 프로그램을 개발 하는 것은 어려울 수 있습니다. Docker 및 Kubernetes 구성 파일이 필요 합니다. 응용 프로그램을 로컬로 테스트 하 고 다른 종속 서비스와 상호 작용 하는 방법을 파악 해야 합니다. 한 번에 여러 서비스에 대한 개발 및 테스트를 처리 하 고 개발자 팀과 함께 처리 해야 할 수도 있습니다.

Azure 개발 공간을 사용하면  직접 Azure Kubernetes Service(AKS)에서 Kubernetes 응용 프로그램을 개발, 배포하고 디버그할 수 있습니다. Azure 개발 공간에서는 또한 팀이 개발 공간을 공유할 수 있습니다. 팀 전체에 걸쳐 개발 공간을 공유하면 개별 팀 구성원이 클러스터의 종속성이나 다른 응용 프로그램을 복제하거나 모의하지 않고도 격리되어 개발할 수 있습니다.

Azure Dev Spaces은 AKS에서 Kubernetes 응용 프로그램을 배포, 실행 및 디버그 하기 위한 구성 파일을 만들고 사용 합니다. 이 구성 파일은 응용 프로그램의 코드에 있으며 버전 제어 시스템에 추가할 수 있습니다.

이 문서에서는 power Azure Dev Spaces 프로세스와 Azure Dev Spaces 구성 파일에서 이러한 프로세스를 구성 하는 방법을 설명 합니다. Azure Dev Spaces 신속 하 게 실행 하 고 있는지 확인 하려면 빠른 시작 중 하나를 완료 합니다.

* [CLI 및 Visual Studio Code를 사용 하는 Java](quickstart-java.md)
* [CLI 및 Visual Studio Code를 사용 하는 .NET Core](quickstart-netcore.md)
* [Visual Studio를 사용 하는 .NET Core](quickstart-netcore-visualstudio.md)
* [CLI 및 Visual Studio Code를 사용 하는 node.js](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces의 작동 원리

Azure Dev Spaces에는 컨트롤러 및 클라이언트 쪽 도구와 상호 작용 하는 두 개의 고유 구성 요소가 있습니다.

![Azure Dev Spaces 구성 요소](media/how-dev-spaces-works/components.svg)

컨트롤러는 다음 작업을 수행 합니다.

* 개발 공간 만들기 및 선택 항목을 관리 합니다.
* 응용 프로그램의 Helm 차트를 설치하고 Kubernetes 개체를 만듭니다.
* 응용 프로그램의 컨테이너 이미지를 빌드합니다.
* 응용 프로그램을 AKS에 배포 합니다.
* 소스 코드가 변경 될 때 증분 빌드와 다시 시작 합니다.
* 로그 및 HTTP 추적을 관리 합니다.
* Stdout 및 stderr을 클라이언트 쪽 도구에 전달 합니다.
* 팀 멤버가 부모 개발 공간에서 파생 된 자식 개발 공간을 만들 수 있도록 허용 합니다.
* 공간 내의 응용 프로그램에 대한 라우팅과 부모 및 하위 공간을 모두 구성 합니다.

컨트롤러가 AKS 외부에 있습니다. 클라이언트 쪽 도구와 AKS 클러스터 간의 동작 및 통신을 구동 합니다. Azure Dev Spaces를 사용 하도록 클러스터를 준비할 때 Azure CLI를 사용 하 여 컨트롤러를 사용할 수 있습니다. 사용 하도록 설정 되 면 클라이언트 쪽 도구를 사용 하 여 상호 작용할 수 있습니다.

클라이언트 쪽 도구를 사용 하 여 다음을 수행할 수 있습니다.
* 응용 프로그램에 대한 Dockerfile, 투구 차트 및 Azure Dev Spaces 구성 파일을 생성 합니다.
* 부모 및 자식 개발 공간을 만듭니다.
* 응용 프로그램을 빌드하고 시작 하도록 컨트롤러에 지시 합니다.

응용 프로그램이 실행 되는 동안 클라이언트 쪽 도구는 다음을 수행 합니다.
* AKS에서 실행 되는 응용 프로그램에서 stdout 및 stderr을 받아서 표시 합니다.
* 는 [포트 전달을](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) 사용 하 여 http:\//localhost.를 사용 하는 응용 프로그램에 대한 웹 액세스를 허용 합니다.
* AKS에서 실행 중인 응용 프로그램에 디버거를 연결 합니다.
* 증분 빌드에 대한 변경 내용이 검색 되 면 개발 공간에 소스 코드를 동기화 하 여 빠른 반복을 허용 합니다.

`azds` 명령의 일부로 명령줄에서 클라이언트 쪽 도구를 사용할 수 있습니다. 클라이언트 쪽 도구를 다음과 함께 사용할 수도 있습니다.

* [Azure Dev Spaces 확장](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)을 사용 하 여 Visual Studio Code 합니다.
* [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)포함 된 Visual Studio

Azure Dev Spaces를 설정 하 고 사용 하는 기본 흐름은 다음과 같습니다.
1. Azure Dev Spaces에 대한 AKS 클러스터 준비
1. Azure Dev Spaces에서 실행할 코드 준비
1. 개발 공간에서 코드 실행
1. 개발 공간에서 코드 디버그
1. 개발 공간 공유

아래 각 섹션에서 Azure Dev Spaces 작동 하는 방법에 대한 자세한 내용을 다룹니다.

## <a name="prepare-your-aks-cluster"></a>AKS 클러스터 준비

AKS 클러스터를 준비 하는 과정은 다음과 같습니다.
* AKS 클러스터가 [Azure Dev Spaces에서 지 원하는][supported-regions]지역에 있는지 확인 하는 중입니다.
* Kubernetes 1.10.3 이상을 실행 하 고 있는지 확인 하는 중입니다.
* `az aks use-dev-spaces`를 사용 하 여 클러스터에서 Azure Dev Spaces 사용

Azure Dev Spaces에 대한 AKS 클러스터를 만들고 구성 하는 방법에 대한 자세한 내용은 시작 가이드 중 하나를 참조 하세요.
* [Java를 사용 하 여 Azure Dev Spaces 시작](get-started-java.md)
* [.NET Core 및 Visual Studio를 사용 하 여 Azure Dev Spaces 시작](get-started-netcore-visualstudio.md)
* [.NET Core를 사용 하 여 Azure Dev Spaces 시작](get-started-netcore.md)
* [Node.js를 사용 하 여 Azure Dev Spaces 시작](get-started-nodejs.md)

AKS 클러스터에서 Azure Dev Spaces 사용 하도록 설정 되 면 클러스터에 대한 컨트롤러를 설치 합니다. 컨트롤러는 클러스터 외부의 별도의 Azure 리소스 이며 클러스터의 리소스에 대해 다음을 수행 합니다.

* 개발 공간으로 사용할 Kubernetes 네임 스페이스를 만들거나 지정 합니다.
* *Azds*라는 Kubernetes 네임 스페이스가 있는 경우이를 제거 하 고 새 네임 스페이스를 만듭니다.
* Kubernetes webhook 구성을 배포 합니다.
* Webhook 허용 서버를 배포 합니다.
    

또한 AKS 클러스터에서 다른 Azure Dev Spaces 구성 요소에 대한 서비스 호출을 수행 하는 데 사용 하는 것과 동일한 서비스 주체를 사용 합니다.

![클러스터 준비 Azure Dev Spaces](media/how-dev-spaces-works/prepare-cluster.svg)

Azure Dev Spaces를 사용 하려면 Dev 공간이 하나 이상 있어야 합니다. Azure Dev Spaces는 AKS 클러스터 내에서 Dev 공간에 Kubernetes 네임 스페이스를 사용 합니다. 컨트롤러를 설치 하는 경우 새 Kubernetes 네임 스페이스를 만들거나 첫 번째 dev 공간으로 사용할 기존 네임 스페이스를 선택 하 라는 메시지가 표시 됩니다. 네임 스페이스를 dev 공간으로 지정 하면 컨트롤러는 해당 네임 스페이스에 *azds.io/space=true* 레이블을 추가 하 여이를 dev 공간으로 식별 합니다. 사용자가 만들거나 지정 하는 초기 개발 공간은 클러스터를 준비한 후 기본적으로 선택 됩니다. 공백을 선택 하면 새 작업을 만들기 위해 Azure Dev Spaces에서 사용 됩니다.

기본적으로 컨트롤러는 기존 *기본* Kubernetes 네임 스페이스를 업그레이드 하 여 *기본* 이라는 개발 공간을 만듭니다. 클라이언트 쪽 도구를 사용 하 여 새 개발 공간을 만들고 기존 개발 공간을 제거할 수 있습니다. Kubernetes의 제한으로 인해 *기본* 개발 공간은 제거할 수 없습니다. 또한 컨트롤러는 클라이언트 쪽 도구에서 사용 하는 `azds` 명령과 충돌 하지 않도록 *azds* 이라는 기존 Kubernetes 네임 스페이스를 제거 합니다.

Kubernetes webhook 허용 서버는 계측을 위해 배포 하는 동안 세 개의 컨테이너를 사용 하 여 pod를 삽입 하는 데 사용 됩니다. **이러한 세 컨테이너는 모두 AKS 클러스터에서 루트 액세스로 실행 됩니다.** 또한 AKS 클러스터에서 다른 Azure Dev Spaces 구성 요소에 대한 서비스 호출을 수행 하는 데 사용 하는 것과 동일한 서비스 주체를 사용 합니다.

![Azure Dev Spaces Kubernetes webhook 허용 서버](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Devspaces-proxy 컨테이너는 응용 프로그램 컨테이너에서 들어오고 나가는 모든 TCP 트래픽을 처리 하는 사이드카 컨테이너 이며 라우팅을 수행 하는 데 도움이 됩니다. 특정 공백이 사용 되는 경우 devspaces 프록시 컨테이너는 HTTP 메시지를 라우팅합니다. 예를 들어 부모 및 자식 공간의 응용 프로그램 간에 HTTP 메시지를 라우팅하는 데 도움이 될 수 있습니다. 모든 비 HTTP 트래픽은 devspaces-프록시를 수정 되지 않은 상태로 전달 합니다. 또한 devspaces 프록시 컨테이너는 모든 인바운드 및 아웃 바운드 HTTP 메시지를 기록 하 고이를 클라이언트 쪽 도구에 추적으로 보냅니다. 개발자는 이러한 추적을 보고 응용 프로그램의 동작을 검사할 수 있습니다.

Devspaces-proxy-init 컨테이너는 응용 프로그램 컨테이너에 공간 계층 구조를 기반으로 하는 추가 라우팅 규칙을 추가 하는 [init 컨테이너](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) 입니다. 시작 하기 전에 응용 프로그램 컨테이너의 */etc/resolv.conf* 파일 및 iptables 구성을 업데이트 하 여 라우팅 규칙을 추가 합니다. */Etc/resolv.conf* 에 대한 업데이트는 부모 공간에서 서비스에 대한 DNS 확인을 허용 합니다. Iptables 구성 업데이트는 응용 프로그램의 컨테이너에 대한 모든 TCP 트래픽이 devspaces 프록시를 통해 라우팅되는 지 확인 합니다. Kubernetes에서 추가 하는 규칙 외에도 devspaces의 모든 업데이트-프록시-init가 발생 합니다.

Devspaces-build 컨테이너는 init 컨테이너 이며 프로젝트 소스 코드와 Docker 소켓이 탑재 되어 있습니다. 프로젝트 소스 코드 및 Docker에 대한 액세스를 통해 pod에서 직접 응용 프로그램 컨테이너를 빌드할 수 있습니다.

> [!NOTE]
> Azure Dev Spaces는 동일한 노드를 사용 하 여 응용 프로그램의 컨테이너를 빌드하고 실행 합니다. 따라서 응용 프로그램을 빌드 및 실행 하기 위해 Azure Dev Spaces에는 외부 컨테이너 레지스트리가 필요 하지 않습니다.

Kubernetes webhook 허용 서버는 AKS 클러스터에서 만들어진 모든 새 pod를 수신 대기 합니다. 이 pod가 *azds.io/space=true* 레이블을 사용 하 여 네임 스페이스에 배포 되는 경우 추가 컨테이너를 사용 하 여 해당 pod를 삽입 합니다. Devspaces-build 컨테이너는 응용 프로그램의 컨테이너가 클라이언트 쪽 도구를 사용 하 여 실행 되는 경우에만 삽입 됩니다.

AKS 클러스터를 준비한 후에는 클라이언트 쪽 도구를 사용 하 여 개발 공간에서 코드를 준비 하 고 실행할 수 있습니다.

## <a name="prepare-your-code"></a>코드 준비

개발 공간에서 응용 프로그램을 실행 하려면 컨테이너 화 된 해야 하며, Kubernetes에 배포 하는 방법을 정의 해야 합니다. 응용 프로그램을 컨테이너 화 하려면 Dockerfile이 필요 합니다. 응용 프로그램을 Kubernetes에 배포 하는 방법을 정의 하려면 [투구 차트가](https://docs.helm.sh/)필요 합니다. 응용 프로그램에 대한 Dockerfile 및 투구 차트를 만드는 데 도움을 주는 클라이언트 쪽 도구는 `prep` 명령을 제공 합니다.

```cmd
azds prep --public
```

`prep` 명령은 프로젝트의 파일을 확인 하 고 Kubernetes에서 응용 프로그램을 실행 하기 위한 Dockerfile 및 투구 차트를 만듭니다. 현재 `prep` 명령은 다음 언어로 Dockerfile 및 투구 차트를 생성 합니다.

* Java
* Node.js
* .NET Core

소스 코드를 포함 하는 디렉터리에서 `prep` 명령을 실행 *해야* 합니다. 올바른 디렉터리에서 `prep` 명령을 실행 하면 클라이언트 쪽 도구에서 언어를 식별 하 고 적절 한 Dockerfile을 만들어 응용 프로그램을 컨테이너 화 수 있습니다. Java 프로젝트용 *pom .xml* 파일이 포함 된 디렉터리에서 `prep` 명령을 실행할 수도 있습니다.

소스 코드를 포함 하지 않는 디렉터리에서 `prep` 명령을 실행 하는 경우 클라이언트 쪽 도구는 Dockerfile을 생성 하지 않습니다. *지원 되지 않는 언어로 인해 Dockerfile을 생성할*수 없다는 오류도 표시 됩니다. 이 오류는 클라이언트 쪽 도구에서 프로젝트 형식을 인식 하지 못하는 경우에도 발생 합니다.

`prep` 명령을 실행 하는 경우 `--public` 플래그를 지정 하는 옵션이 있습니다. 이 플래그는 컨트롤러에이 서비스에 대한 인터넷 액세스 가능 엔드포인트을 만들도록 지시 합니다. 이 플래그를 지정 하지 않으면 클러스터 내 에서만 또는 클라이언트 쪽 도구에서 만든 localhost 터널을 사용 하 여 서비스에 액세스할 수 있습니다. 생성 된 투구 차트를 업데이트 하 여 `prep` 명령을 실행 한 후이 동작을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

`prep` 명령은 프로젝트에서 기존 Dockerfiles 또는 투구 차트를 대체 하지 않습니다. 기존 Dockerfile 또는 투구 차트에서 `prep` 명령으로 생성 된 파일과 동일한 명명 규칙을 사용 하는 경우 `prep` 명령은 해당 파일 생성을 건너뜁니다. 그렇지 않으면 `prep` 명령은 기존 파일과 함께 자체 Dockerfile 또는 투구 차트를 생성 합니다.

또한 `prep` 명령은 프로젝트의 루트에 `azds.yaml` 파일을 생성 합니다. Azure Dev Spaces는이 파일을 사용 하 여 응용 프로그램을 빌드, 설치, 구성 및 실행 합니다. 이 구성 파일은 Dockerfile 및 투구 차트의 위치를 나열 하 고 이러한 아티팩트 위에 추가 구성을 제공 합니다.

다음은 [.Net Core 샘플 응용 프로그램](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)을 사용 하 여 만든 azds 파일의 예입니다.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`prep` 명령으로 생성 된 `azds.yaml` 파일은 간단한 단일 프로젝트 개발 시나리오에서 제대로 작동 해야 합니다. 특정 프로젝트의 복잡성이 증가 하는 경우 `prep` 명령을 실행 한 후이 파일을 업데이트 해야 할 수 있습니다. 예를 들어 프로젝트에 개발 또는 디버깅 요구에 따라 빌드 또는 시작 프로세스에 대한 약간의 트윅이 필요할 수 있습니다. 프로젝트에 여러 응용 프로그램이 있을 수도 있습니다 .이 경우 여러 빌드 프로세스나 다른 빌드 콘텐츠가 필요 합니다.

## <a name="run-your-code"></a>코드 실행

개발 공간에서 코드를 실행 하려면 `azds.yaml` 파일과 동일한 디렉터리에서 `up` 명령을 실행 합니다.

```cmd
azds up
```

`up` 명령은 프로젝트를 빌드하고 개발 공간에 실행 하는 데 필요한 응용 프로그램 소스 파일 및 기타 아티팩트를 업로드 합니다. 여기에서 개발자 공간의 컨트롤러는 다음과 같습니다.

1. 응용 프로그램을 배포 하는 Kubernetes 개체를 만듭니다.
1. 응용 프로그램에 대한 컨테이너를 빌드합니다.
1. 개발 공간에 응용 프로그램을 배포 합니다.
1. 구성 된 경우 응용 프로그램 엔드포인트에 대해 공개적으로 액세스할 수 있는 DNS 이름을 만듭니다.
1. 는 *포트 전달을* 사용 하 여 http://localhost 사용 하 여 응용 프로그램 엔드포인트에 대한 액세스를 제공 합니다.
1. Stdout 및 stderr을 클라이언트 쪽 도구에 전달 합니다.


### <a name="starting-a-service"></a>서비스 시작

개발 공간에서 서비스를 시작 하는 경우 클라이언트 쪽 도구와 컨트롤러는 조정 하 여 소스 파일을 동기화 하 고, 컨테이너 및 Kubernetes 개체를 만들고, 응용 프로그램을 실행 합니다.

보다 세부적인 수준에서 `azds up`를 실행할 때 발생 하는 작업은 다음과 같습니다.

1. 파일은 사용자의 컴퓨터에서 사용자의 AKS 클러스터에 고유한 Azure 파일 저장소로 동기화 됩니다. 소스 코드, 투구 차트 및 구성 파일이 업로드 됩니다. 동기화 프로세스에 대한 자세한 내용은 다음 섹션에서 확인할 수 있습니다.
1. 컨트롤러가 새 세션을 시작 하는 요청을 만듭니다. 이 요청에는 고유 ID, 공간 이름, 소스 코드 경로 및 디버깅 플래그를 비롯 한 여러 속성이 포함 됩니다.
1. 컨트롤러는 투구 차트에서 *$ (tag)* 자리 표시자를 고유한 세션 ID로 바꾸고 서비스에 대한 투구 차트를 설치 합니다. 고유한 세션 ID에 대한 참조를 투구 차트에 추가 하면이 특정 세션에 대한 AKS 클러스터에 배포 된 컨테이너가 세션 요청 및 관련 정보에 다시 연결 될 수 있습니다.
1. 투구 차트를 설치 하는 동안 Kubernetes webhook 허용 서버는 응용 프로그램의 pod에 추가 컨테이너를 추가 하 여 프로젝트의 소스 코드에 대한 계측 및 액세스를 수행 합니다. HTTP 추적 및 공간 라우팅을 제공 하기 위해 devspaces 프록시 및 devspaces-프록시-init 컨테이너가 추가 됩니다. 응용 프로그램의 컨테이너를 빌드하기 위한 Docker 인스턴스 및 프로젝트 소스 코드에 대한 액세스 권한이 pod에 제공 되도록 devspaces-빌드 컨테이너가 추가 됩니다.
1. 응용 프로그램의 pod가 시작 되 면 응용 프로그램 컨테이너를 빌드하기 위해 devspaces-build 컨테이너와 devspaces-proxy-init 컨테이너가 사용 됩니다. 그러면 응용 프로그램 컨테이너와 devspaces 프록시 컨테이너가 시작 됩니다.
1. 응용 프로그램 컨테이너가 시작 된 후 클라이언트 쪽 *기능은 Kubernetes 기능* 을 사용 하 여 http://localhost 통해 응용 프로그램에 대한 HTTP 액세스를 제공 합니다. 이 포트 전달은 개발 컴퓨터를 dev 공간의 서비스에 연결 합니다.
1. Pod의 모든 컨테이너가 시작 되 면 서비스가 실행 중입니다. 이 시점에서 클라이언트 쪽 기능은 HTTP 추적, stdout 및 stderr의 스트리밍을 시작 합니다. 이 정보는 개발자를 위한 클라이언트 쪽 기능에 의해 표시 됩니다.

### <a name="updating-a-running-service"></a>실행 중인 서비스 업데이트

서비스를 실행 하는 동안 프로젝트 원본 파일이 변경 되 면 해당 서비스를 업데이트할 수 있는 Azure Dev Spaces 있습니다. 또한 Dev 공백은 변경 되는 파일의 형식에 따라 서비스 업데이트를 다르게 처리 합니다. Dev Spaces에서 실행 중인 서비스를 업데이트할 수 있는 방법에는 세 가지가 있습니다.

* 파일 직접 업데이트
* 실행 중인 응용 프로그램의 컨테이너 내에서 응용 프로그램의 프로세스 다시 작성 및 다시 시작
* 응용 프로그램의 컨테이너 다시 빌드 및 재배포

![Azure Dev Spaces 파일 동기화](media/how-dev-spaces-works/file-sync.svg)

Html, css, cshtml 파일 등의 정적 자산에 해당 하는 특정 프로젝트 파일은 아무것도 다시 시작 하지 않고 응용 프로그램의 컨테이너에서 직접 업데이트할 수 있습니다. 정적 자산이 변경 되 면 새 파일이 dev 공간에 동기화 된 다음 실행 중인 컨테이너에서 사용 됩니다.

소스 코드 또는 응용 프로그램 구성 파일과 같은 파일에 대한 변경 내용은 실행 중인 컨테이너 내에서 응용 프로그램의 프로세스를 다시 시작 하 여 적용할 수 있습니다. 이러한 파일이 동기화 되 면 *devhostagent* 프로세스를 사용 하 여 실행 중인 컨테이너 내에서 응용 프로그램의 프로세스가 다시 시작 됩니다. 응용 프로그램의 컨테이너를 처음 만들 때 컨트롤러는 응용 프로그램의 startup 명령을 *devhostagent*이라는 다른 프로세스로 바꿉니다. 그러면 응용 프로그램의 실제 프로세스가 *devhostagent*에서 자식 프로세스로 실행 되 고 해당 출력은 *devhostagent*의 출력을 사용 하 여 전달 됩니다. *Devhostagent* 프로세스는 또한 dev 공간의 일부 이며 dev 공간을 대신 하 여 실행 중인 컨테이너에서 명령을 실행할 수 있습니다. 다시 시작을 수행 하는 경우 *devhostagent*는 다음과 같습니다.

* 응용 프로그램과 관련 된 현재 프로세스 또는 프로세스를 중지 합니다.
* 응용 프로그램을 다시 빌드합니다.
* 응용 프로그램과 관련 된 프로세스를 다시 시작 합니다.

*Devhostagent* 가 이전 단계를 실행 하는 방법은 `azds.yaml` 구성 파일에서 구성 됩니다. 이 구성은 이후 섹션에서 자세히 설명 합니다.

Dockerfiles, .csproj 파일 또는 투구 차트의 일부와 같은 프로젝트 파일을 업데이트 하려면 응용 프로그램의 컨테이너를 다시 빌드하고 다시 배포 해야 합니다. 이러한 파일 중 하나가 dev 공간에 동기화 되 면 컨트롤러는 [투구 업그레이드](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure) 명령을 실행 하 고 응용 프로그램의 컨테이너가 다시 작성 되 고 다시 배포 됩니다.

### <a name="file-synchronization"></a>파일 동기화

개발 공간에서 응용 프로그램이 처음 시작 될 때 모든 응용 프로그램의 원본 파일이 업로드 됩니다. 응용 프로그램이 실행 되 고 나중에 다시 시작 될 때 변경 된 파일만 업로드 됩니다. 이 프로세스를 조정 하는 데 사용 되는 두 개의 파일은 클라이언트 쪽 파일과 컨트롤러 측 파일입니다.

클라이언트 쪽 파일은 임시 디렉터리에 저장 되며, 개발 공간에서 실행 중인 프로젝트 디렉터리의 해시에 따라 이름이 지정 됩니다. 예를 들어 Windows에서 프로젝트에 대한 *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 와 같은 파일을 사용할 수 있습니다. Linux에서 클라이언트 쪽 파일은 */tmp* 디렉터리에 저장 됩니다. `echo $TMPDIR` 명령을 실행 하 여 macOS에서 디렉터리를 찾을 수 있습니다.

이 파일은 다음을 포함 하는 JSON 형식입니다.

* 개발 공간과 동기화 된 각 프로젝트 파일에 대한 항목입니다.
* 동기화 ID
* 마지막 동기화 작업의 타임 스탬프입니다.

각 프로젝트 파일 항목에는 파일에 대한 경로 및 해당 타임 스탬프가 포함 됩니다.

컨트롤러 측 파일은 AKS 클러스터에 저장 됩니다. 여기에는 마지막 동기화의 동기화 ID와 타임 스탬프가 포함 됩니다.

동기화 타임 스탬프가 클라이언트 쪽 파일과 컨트롤러 측 파일 사이에서 일치 하지 않을 경우 동기화가 발생 합니다. 동기화 하는 동안 클라이언트 쪽 도구는 클라이언트 쪽 파일의 파일 항목을 반복 합니다. 파일의 타임 스탬프가 동기화 타임 스탬프 이후 이면 해당 파일이 dev 공간에 동기화 됩니다. 동기화가 완료 되 면 클라이언트 쪽 파일 및 컨트롤러 쪽 파일에서 동기화 타임 스탬프가 업데이트 됩니다.

클라이언트 쪽 파일이 없는 경우 모든 프로젝트 파일이 동기화 됩니다. 이 동작을 통해 클라이언트 쪽 파일을 삭제 하 여 전체 동기화를 강제로 수행할 수 있습니다.

### <a name="how-routing-works"></a>라우팅 작동 방법

Dev 공간은 AKS을 기반으로 구축 되며 동일한 [네트워킹 개념](../aks/concepts-network.md)을 사용 합니다. 또한 Azure Dev Spaces에는 중앙 집중식 *ingressmanager* 서비스가 있으며 자체 수신 컨트롤러를 AKS 클러스터에 배포 합니다. *Ingressmanager* 서비스는 dev 공간을 사용 하 여 AKS 클러스터를 모니터링 하 고, application pod로 라우팅하는 수신 개체를 사용 하 여 클러스터의 Azure Dev Spaces 수신 컨트롤러를 보강 합니다. 각 pod의 devspaces 프록시 컨테이너는 URL을 기반으로 하는 개발 공간에 HTTP 트래픽에 대한 `azds-route-as` HTTP 헤더를 추가 합니다. 예를 들어 *http://azureuser.s.default.serviceA.fedcba09...azds.io* URL에 대한 요청은 `azds-route-as: azureuser` 를 사용 하 여 HTTP 헤더를 가져옵니다. Devspaces-proxy 컨테이너는 이미 있는 경우 `azds-route-as` 헤더를 추가 하지 않습니다.

클러스터 외부에서 서비스에 대한 HTTP 요청이 이루어지면 요청이 수신 컨트롤러로 이동 합니다. 수신 컨트롤러는 수신 개체 및 규칙에 따라 적절 한 pod로 직접 요청을 라우팅합니다. Pod의 devspaces-proxy 컨테이너는 요청을 수신 하 고 URL에 따라 `azds-route-as` 헤더를 추가한 다음 요청을 응용 프로그램 컨테이너로 라우팅합니다.

클러스터 내의 다른 서비스에서 서비스에 대한 HTTP 요청이 이루어지면 요청은 먼저 호출 하는 서비스의 devspaces 프록시 컨테이너를 통해 이동 합니다. Devspaces-proxy 컨테이너는 HTTP 요청을 보고 `azds-route-as` 헤더를 확인 합니다. 헤더에 따라 devspaces-proxy 컨테이너는 헤더 값과 연결 된 서비스의 IP 주소를 조회 합니다. IP 주소가 있는 경우 devspaces 프록시 컨테이너는 해당 IP 주소에 대한 요청을 라우팅합니다. IP 주소를 찾을 수 없는 경우 devspaces 프록시 컨테이너는 해당 요청을 부모 응용 프로그램 컨테이너로 라우팅합니다.

예를 들어 *Servicea* 및 *servicea* 응용 프로그램은 *기본값*이라는 부모 개발 공간에 배포 됩니다. *Servicea는* *servicea* 에 의존 하 고이에 대한 HTTP 호출을 수행 합니다. Azure 사용자는 *azureuser*라는 *기본* 공간을 기반으로 하는 자식 개발 공간을 만듭니다. 또한 Azure 사용자는 자신의 고유 버전의 *Servicea* 를 해당 하위 공간에 배포 합니다. *http://azureuser.s.default.serviceA.fedcba09...azds.io* 에 대한 요청이 수행 되는 경우:

![Azure Dev Spaces 라우팅](media/how-dev-spaces-works/routing.svg)

1. 수신 컨트롤러는 URL ( *Servicea azureuser*)과 연결 된 POD의 IP를 조회 합니다.
1. 수신 컨트롤러는 Azure 사용자의 개발 공간에서 pod의 IP를 찾아 요청을 *Servicea azureuser* pod로 라우팅합니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 요청을 수신 하 고 `azds-route-as: azureuser`를 HTTP 헤더로 추가 합니다.
1. *Servicea. azureuser* pod의 devspaces-proxy 컨테이너는 요청을 *servicea. Azureuser* pod의 *servicea* 응용 프로그램 컨테이너로 라우팅합니다.
1. *Servicea. azureuser* Pod의 *servicea* 응용 프로그램은 *servicea*를 호출 합니다. *Servicea* 응용 프로그램에는 기존 `azds-route-as` 헤더를 유지 하는 코드 (이 경우에는 `azds-route-as: azureuser`)도 포함 되어 있습니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 요청을 수신 하 고 `azds-route-as` 헤더의 값을 기반으로 *SERVICEA* 의 IP를 조회 합니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 *servicea azureuser*에 대한 IP를 찾지 않습니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 부모 *공간에서 SERVICEA의 IP* 를 찾습니다 *. 기본값은 servicea입니다.*
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 *SERVICEA* 의 IP를 찾고 요청을 *servicea. 기본* pod로 라우팅합니다.
1. Serviceb의 devspaces-proxy 컨테이너 *. 기본* pod는 요청을 수신 하 고 *serviceb. 기본* pod의 *serviceb* 응용 프로그램 컨테이너에 요청을 라우팅합니다.
1. Serviceb의 *serviceb* 응용 프로그램은 *기본* pod에 대한 응답을 반환 *합니다.*
1. *Servicea. azureuser* pod의 devspaces-proxy 컨테이너는 응답을 수신 하 고 *servicea. Azureuser* pod의 *servicea* 응용 프로그램 컨테이너로 응답을 라우팅합니다.
1. *Servicea* 응용 프로그램은 응답을 받은 다음 자체 응답을 반환 합니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 *servicea* 응용 프로그램 컨테이너에서 응답을 받고 응답을 클러스터 외부의 원래 호출자에 게 라우팅합니다.

HTTP가 아닌 다른 모든 TCP 트래픽은 수신 컨트롤러 및 devspaces 프록시 컨테이너를 통해 수정 되지 않은 상태로 전달 됩니다.

### <a name="how-running-your-code-is-configured"></a>코드 실행 방법 구성

Azure Dev Spaces `azds.yaml` 파일을 사용 하 여 서비스를 설치 하 고 구성 합니다. 컨트롤러는 `azds.yaml` 파일의 `install` 속성을 사용 하 여 투구 차트를 설치 하 고 Kubernetes 개체를 만듭니다.

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

기본적으로 `prep` 명령은 투구 차트를 생성 합니다. 또한 *설치. chart* 속성을 투구 차트의 디렉터리로 설정 합니다. 다른 위치에서 투구 차트를 사용 하려는 경우이 속성을 업데이트 하 여 해당 위치를 사용할 수 있습니다.

투구 차트를 설치할 때 Azure Dev Spaces는 투구 차트에서 값을 재정의 하는 방법을 제공 합니다. 투구 차트의 기본값은 `charts/APP_NAME/values.yaml`입니다.

*Install. values* 속성을 사용 하 여 투구 차트에서 대체 하려는 값을 정의 하는 하나 이상의 파일을 나열할 수 있습니다. 예를 들어 개발 공간에서 응용 프로그램을 실행할 때 호스트 이름 또는 데이터베이스 구성이 특별히 필요한 경우이 재정의 기능을 사용할 수 있습니다. *를 추가할* 수도 있습니다. 모든 파일 이름 끝에 선택적으로 설정 합니다.

*Install. set* 속성을 사용 하면 투구 차트에서 바꾸려는 값을 하나 이상 구성할 수 있습니다. *Install. set* 에 구성 된 값은 *install. values*에 나열 된 파일에 구성 된 값을 재정의 합니다. *Install. set* 의 속성은 투구 차트의 값에 따라 다르며 생성 된 투구 차트에 따라 달라질 수 있습니다.

위의 예에서 *replicaCount* 속성은 개발자 공간에서 실행할 응용 프로그램의 인스턴스 수를 컨트롤러에 알려 줍니다. 시나리오에 따라이 값을 늘릴 수 있지만 응용 프로그램의 pod에 디버거를 연결 하는 데 영향을 줄 수 있습니다. 자세한 내용은 [문제 해결 문서](troubleshooting.md)를 참조 하세요.

생성 된 투구 차트에서 컨테이너 이미지는 {{로 설정 됩니다 *. Values. repository}}: {{. Values. tag}}* . `azds.yaml` 파일은 기본적으로 {{의 값으로 사용 되는 *$ (tag)* 로 *설치 합니다* .-tag 속성을 정의 합니다.  *Values. tag}}* . 이렇게 하면 *install.* . tag 속성을 설정 하 여 Azure Dev Spaces 실행 될 때 응용 프로그램에 대한 컨테이너 이미지를 고유한 방식으로 태그를 지정할 수 있습니다. 이 경우 이미지는 *이미지에서\<값으로 태그가 지정 됩니다. 리포지토리 >: $ (tag)* . 개발 공간을 인식 하 고 AKS 클러스터에서 컨테이너를 찾기 위해 *$ (tag)* 변수를 *install.* 값으로 사용 해야 합니다.

위의 예제에서 `azds.yaml`는 *install*. *Install. ingress* 속성은 공용 엔드포인트에 대한 호스트 이름 형식을 정의 합니다. 이 속성은 컨트롤러에서 제공 하는 값인 *$ (spacePrefix)* , *$ (rootSpacePrefix)* 및 *$ (hostsuffix)* 도 사용 합니다. 

*$ (SpacePrefix)* 는 *SPACENAME*형식으로 사용 되는 자식 개발 공간의 이름입니다. *$ (RootSpacePrefix)* 는 부모 공간의 이름입니다. 예를 들어 *azureuser* 가 *기본값*의 하위 공간이 면 *$ (rootSpacePrefix)* 에 대한 값은 *기본값이* 고 *$ (spacePrefix)* 의 값은 *azureuser. s*입니다. 공간이 하위 공간이 아니면 *$ (spacePrefix)* 가 비어 있습니다. 예를 들어 *기본* 공간에 부모 공간이 없으면 *$ (rootSpacePrefix)* 에 대한 값이 *기본값이* 고 *$ (spacePrefix)* 의 값이 비어 있습니다. *$ (Hostsuffix)* 는 AKS 클러스터에서 실행 되는 Azure Dev Spaces 수신 컨트롤러를 가리키는 DNS 접미사입니다. 이 DNS 접미사는\*와 같은 와일드 카드 DNS 항목에 해당 *합니다. RANDOM_VALUE*AKS 클러스터에 Azure Dev Spaces 컨트롤러를 추가할 때 생성 된 azds입니다.

위의 `azds.yaml` 파일에서 *설치* 를 업데이트 하 여 응용 프로그램의 호스트 이름을 변경할 수도 있습니다. 예를 들어 *$ (spacePrefix) $ (rootSpacePrefix) webfrontend 엔드 $ (hostSuffix)* 에서 *$ (spacePrefix) $ (rootSpacePrefix) 웹 $ (hostsuffix)* 로 응용 프로그램의 호스트 이름을 간소화 하려는 경우를 예로 들 수 있습니다.

응용 프로그램에 대한 컨테이너를 빌드하기 위해 컨트롤러는 `azds.yaml` 구성 파일의 아래 섹션을 사용 합니다.

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

컨트롤러는 Dockerfile을 사용 하 여 응용 프로그램을 빌드하고 실행 합니다.

*빌드 컨텍스트* 속성은 Dockerfiles가 있는 디렉터리를 나열 합니다. *빌드. dockerfile* 속성은 응용 프로그램의 프로덕션 버전을 빌드하기 위한 dockerfile의 이름을 정의 합니다. *구성. dockerfile* 속성은 응용 프로그램 개발 버전의 dockerfile 이름을 구성 합니다.

개발 및 프로덕션에 대해 다른 Dockerfiles을 사용 하면 개발 중에 특정 작업을 사용 하도록 설정 하 고 프로덕션 배포에 대해 해당 항목을 사용 하지 않도록 설정할 수 있습니다 예를 들어 개발 중에 디버깅 또는 자세한 정보 로깅을 사용 하도록 설정 하 고 프로덕션 환경에서 사용 하지 않도록 설정할 수 있습니다. Dockerfiles의 이름이 다르게 지정 되거나 다른 위치에 있는 경우 이러한 속성을 업데이트할 수도 있습니다.

개발 중에 빠르게 반복 하는 데 도움이 되도록 Azure Dev Spaces는 변경 내용을 로컬 프로젝트에서 동기화 하 고 응용 프로그램을 증분 업데이트 합니다. `azds.yaml` 구성 파일의 아래 섹션은 동기화 및 업데이트를 구성 하는 데 사용 됩니다.

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

변경 내용을 동기화 하는 파일 및 디렉터리는 *구성. 개발. container. .sync* 속성에 나열 됩니다. 이러한 디렉터리는 변경 내용이 검색 되는 경우를 비롯 하 여 `up` 명령을 실행할 때 처음에 동기화 됩니다. 개발 공간에 동기화 하려는 추가 또는 다른 디렉터리가 있는 경우이 속성을 변경할 수 있습니다.

*구성. container. buildCommands* 속성은 개발 시나리오에서 응용 프로그램을 빌드하는 방법을 지정 합니다. *구성. container. command* 속성은 개발 시나리오에서 응용 프로그램을 실행 하기 위한 명령을 제공 합니다. 개발 하는 동안 사용할 추가 빌드 또는 런타임 플래그 또는 매개 변수가 있는 경우 이러한 속성 중 하나를 업데이트 하는 것이 좋습니다.

*구성. 개발* . 응용 프로그램을 중지 하는 프로세스를 중지 하는 프로세스를 나열 합니다. 개발 하는 동안 응용 프로그램의 다시 시작 동작을 변경 하려는 경우이 속성을 업데이트 해야 할 수 있습니다. 예를 들어, 구성을 업데이트 한 경우. *컨테이너. buildCommands* 또는 구성별을 반복 합니다. *container. command* 속성을 만들어 응용 프로그램을 빌드 또는 시작 하는 방법을 변경 하려면 중지 된 프로세스를 변경 해야 할 수 있습니다.

`azds prep` 명령을 사용 하 여 코드를 준비할 때 `--public` 플래그를 추가 하는 옵션이 있습니다. `--public` 플래그를 추가 하면 응용 프로그램에 대해 공개적으로 액세스할 수 있는 URL이 만들어집니다. 이 플래그를 생략 하면 응용 프로그램은 클러스터 내에서 또는 localhost 터널을 사용 하는 경우에만 액세스할 수 있습니다. `azds prep` 명령을 실행 한 후 `charts/APPNAME/values.yaml`에서 *수신* 설정 속성을 수정 하 여이 설정을 변경할 수 있습니다.

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>코드 디버그

Java, .NET 및 node.js 응용 프로그램의 경우 Visual Studio Code 또는 Visual Studio를 사용 하 여 개발 공간에서 직접 실행 되는 응용 프로그램을 디버그할 수 있습니다. Visual Studio Code 및 Visual Studio는 개발자 공간에 연결 하 고, 응용 프로그램을 시작 하 고, 디버거를 연결 하는 도구를 제공 합니다. `azds prep`를 실행 한 후 Visual Studio Code 또는 Visual Studio에서 프로젝트를 열 수 있습니다. Visual Studio Code 또는 Visual Studio는 `azds prep`실행과 분리 된 연결에 대한 자체 구성 파일을 생성 합니다. Visual Studio Code 또는 Visual Studio 내에서 중단점을 설정 하 고 개발 공간에 응용 프로그램을 시작할 수 있습니다.

![코드 디버그](media/get-started-node/debug-configuration-nodejs2.png)

디버깅을 위해 Visual Studio Code 또는 Visual Studio를 사용 하 여 응용 프로그램을 시작 하면 `azds up`를 실행 하는 것과 같은 방식으로 개발 공간에 대한 시작 및 연결을 처리 합니다. Visual Studio Code 및 Visual Studio의 클라이언트 쪽 도구는 또한 디버깅을 위한 특정 정보가 포함 된 추가 매개 변수를 제공 합니다. 매개 변수에는 디버거 이미지의 이름, 디버거의 이미지 내에 있는 디버거의 위치 및 디버거 폴더를 탑재 하기 위한 응용 프로그램 컨테이너 내의 대상 위치가 포함 됩니다.

디버거 이미지는 클라이언트 쪽 도구에 의해 자동으로 결정 됩니다. Dockerfile에서 사용 된 것과 유사한 메서드를 사용 하 고 `azds prep`를 실행 하면 투구 차트가 생성 됩니다. 디버거는 응용 프로그램의 이미지에 탑재 된 후 `azds exec`를 사용 하 여 실행 됩니다.

## <a name="sharing-a-dev-space"></a>개발 공간 공유

팀으로 작업할 때 [전체 팀에서 개발 공간을 공유](how-to/share-dev-spaces.md) 하 고 파생 된 개발 공간을 만들 수 있습니다. 개발 공간의 리소스 그룹에 대한 참가자 액세스 권한이 있는 사용자는 dev 공간을 사용할 수 있습니다.

다른 개발 공간에서 파생 된 새 개발 공간을 만들 수도 있습니다. 파생 된 dev 공간을 만들 때 *azds.io/parent-space=PARENT-SPACE-NAME* 레이블이 파생 된 dev 공간의 네임 스페이스에 추가 됩니다. 또한 부모 개발 공간의 모든 응용 프로그램은 파생 된 개발 공간과 공유 됩니다. 파생 된 개발 공간에 업데이트 된 버전의 응용 프로그램을 배포 하는 경우 파생 된 dev 공간에만 존재 하며 부모 개발 공간은 영향을 받지 않습니다. 최대 세 수준의 파생 된 개발 공간 또는 *최상위* 공간을 사용할 수 있습니다.

또한 파생 된 개발 공간은 자체 응용 프로그램과 부모에서 공유 되는 응용 프로그램 간에 요청을 지능적으로 라우팅합니다. 라우팅은 파생 된 개발 공간의 응용 프로그램에 요청을 라우팅하고 부모 개발 공간에서 공유 응용 프로그램으로 대체 하는 방식으로 작동 합니다. 응용 프로그램이 부모 공간에 있지 않은 경우 라우팅은 최상위 공간의 공유 응용 프로그램으로 대체 됩니다.

예:
* 개발 공간 *기본값* 에는 응용 프로그램 *Servicea* 및 *servicea* 가 있습니다.
* Dev space *azureuser* 는 *기본값*에서 파생 됩니다.
* *Servicea* 의 업데이트 된 버전이 *azureuser*에 배포 됩니다.

*Azureuser*를 사용 하는 경우 *servicea* 에 대한 모든 요청은 *azureuser*의 업데이트 된 버전으로 라우팅됩니다. *Serviceb* 에 대한 요청은 먼저 *Azureuser* 버전의 *serviceb*로 라우팅됩니다. 이 파일은 존재 하지 않으므로 *기본* 버전의 *serviceb*로 라우팅됩니다. *Servicea* 의 *azureuser* 버전이 제거 되 면 *servicea* 에 대한 모든 요청이 *기본* 버전의 *servicea*를 사용 하는 것으로 대체 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 사용을 시작 하려면 다음 퀵 스타트를 참조 하세요.

* [CLI 및 Visual Studio Code를 사용 하는 Java](quickstart-java.md)
* [CLI 및 Visual Studio Code를 사용 하는 .NET Core](quickstart-netcore.md)
* [Visual Studio를 사용 하는 .NET Core](quickstart-netcore-visualstudio.md)
* [CLI 및 Visual Studio Code를 사용 하는 node.js](quickstart-nodejs.md)

팀 개발을 시작 하려면 다음 방법 문서를 참조 하세요.

* [팀 개발-CLI 및 Visual Studio Code를 사용 하는 Java](team-development-java.md)
* [팀 개발-CLI 및 Visual Studio Code를 사용 하는 .NET Core](team-development-netcore.md)
* [팀 개발-Visual Studio를 사용 하는 .NET Core](team-development-netcore-visualstudio.md)
* [팀 개발-CLI 및 Visual Studio Code를 사용 하는 node.js](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
