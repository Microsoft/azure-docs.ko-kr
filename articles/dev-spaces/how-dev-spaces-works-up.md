---
title: Azure Dev Spaces 작업을 사용 하 여 코드를 실행 하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces 사용 하 여 Azure Kubernetes Service에서 코드를 실행 하는 프로세스를 설명 합니다.
keywords: azds, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 9dbc1f0f21c2883e5caadbdae268a515eb94d145
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208684"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces 작업을 사용 하 여 코드를 실행 하는 방법

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. 프로젝트를 [개발 공간에서 실행할 준비가][how-it-works-prep]되 면 개발 공간을 사용 하 여 AKS 클러스터에서 프로젝트를 빌드하고 실행할 수 있습니다.

이 문서에서는 Dev Spaces를 사용 하 여 AKS에서 코드를 실행 하는 상황을 설명 합니다.

## <a name="run-your-code"></a>코드 실행

개발 공간에서 코드를 실행 하려면 `up` 파일과 동일한 디렉터리에서 명령을 실행 합니다 `azds.yaml` .

```cmd
azds up
```

이 `up` 명령은 프로젝트를 빌드하고 개발 공간에 실행 하는 데 필요한 응용 프로그램 소스 파일 및 기타 아티팩트를 업로드 합니다. 여기에서 개발자 공간의 컨트롤러는 다음과 같습니다.

1. 응용 프로그램을 배포 하는 Kubernetes 개체를 만듭니다.
1. 응용 프로그램에 대 한 컨테이너를 빌드합니다.
1. 개발 공간에 응용 프로그램을 배포 합니다.
1. 구성 된 경우 응용 프로그램 끝점에 대해 공개적으로 액세스할 수 있는 DNS 이름을 만듭니다.
1. 는 *포트 전달을* 사용 하 여를 통해 응용 프로그램 끝점에 대 한 액세스를 제공 http://localhost 합니다.
1. Stdout 및 Stderr을 클라이언트 쪽 도구에 전달합니다.


## <a name="starting-a-service"></a>서비스 시작

개발 공간에서 서비스를 시작 하는 경우 클라이언트 쪽 도구와 컨트롤러는 조정 하 여 소스 파일을 동기화 하 고, 컨테이너 및 Kubernetes 개체를 만들고, 응용 프로그램을 실행 합니다.

보다 세분화 된 수준에서를 실행할 때 발생 하는 작업은 `azds up` 다음과 같습니다.

1. 파일은 사용자의 컴퓨터에서 사용자의 AKS 클러스터에 고유한 Azure 파일 저장소로 [동기화 됩니다][sync-section] . 소스 코드, 투구 차트 및 구성 파일이 업로드 됩니다.
1. 컨트롤러가 새 세션을 시작 하는 요청을 만듭니다. 이 요청에는 고유 ID, 공간 이름, 소스 코드 경로 및 디버깅 플래그를 비롯 한 여러 속성이 포함 됩니다.
1. 컨트롤러는 투구 차트에서 *$ (tag)* 자리 표시자를 고유한 세션 ID로 바꾸고 서비스에 대 한 투구 차트를 설치 합니다. 고유한 세션 ID에 대 한 참조를 투구 차트에 추가 하면이 특정 세션에 대 한 AKS 클러스터에 배포 된 컨테이너가 세션 요청 및 관련 정보에 다시 연결 될 수 있습니다.
1. 투구 차트를 설치 하는 동안 Kubernetes webhook 허용 서버는 응용 프로그램의 pod에 추가 컨테이너를 추가 하 여 프로젝트의 소스 코드에 대 한 계측 및 액세스를 수행 합니다. HTTP 추적 및 공간 라우팅을 제공 하기 위해 devspaces 프록시 및 devspaces-프록시-init 컨테이너가 추가 됩니다. 응용 프로그램의 컨테이너를 빌드하기 위한 Docker 인스턴스 및 프로젝트 소스 코드에 대 한 액세스 권한이 pod에 제공 되도록 devspaces-빌드 컨테이너가 추가 됩니다.
1. 응용 프로그램의 pod가 시작 되 면 응용 프로그램 컨테이너를 빌드하기 위해 devspaces-build 컨테이너와 devspaces-proxy-init 컨테이너가 사용 됩니다. 그러면 응용 프로그램 컨테이너와 devspaces 프록시 컨테이너가 시작 됩니다.
1. 응용 프로그램 컨테이너가 시작 되 면 클라이언트 쪽 *기능은 Kubernetes 기능* 을 사용 하 여 응용 프로그램에 대 한 HTTP 액세스를 제공 http://localhost 합니다. 이 포트 전달은 개발 컴퓨터를 dev 공간의 서비스에 연결 합니다.
1. Pod의 모든 컨테이너가 시작 되 면 서비스가 실행 중입니다. 이 시점에서 클라이언트 쪽 기능은 HTTP 추적, stdout 및 stderr의 스트리밍을 시작 합니다. 이 정보는 개발자를 위한 클라이언트 쪽 기능에 의해 표시 됩니다.

## <a name="updating-a-running-service"></a>실행 중인 서비스 업데이트

서비스를 실행 하는 동안 프로젝트 원본 파일이 변경 되 면 해당 서비스를 업데이트할 수 있는 Azure Dev Spaces 있습니다. 또한 Dev 공백은 변경 되는 파일의 형식에 따라 서비스 업데이트를 다르게 처리 합니다. Dev Spaces에서 실행 중인 서비스를 업데이트할 수 있는 방법에는 세 가지가 있습니다.

* 파일 직접 업데이트
* 실행 중인 응용 프로그램의 컨테이너 내에서 응용 프로그램의 프로세스 다시 작성 및 다시 시작
* 응용 프로그램의 컨테이너 다시 빌드 및 재배포

![Azure Dev Spaces 파일 동기화](media/how-dev-spaces-works/file-sync.svg)

Html, css, cshtml 파일 등의 정적 자산에 해당 하는 특정 프로젝트 파일은 아무것도 다시 시작 하지 않고 응용 프로그램의 컨테이너에서 직접 업데이트할 수 있습니다. 정적 자산이 변경 되 면 새 파일이 dev 공간에 동기화 된 다음 실행 중인 컨테이너에서 사용 됩니다.

소스 코드 또는 응용 프로그램 구성 파일과 같은 파일에 대 한 변경 내용은 실행 중인 컨테이너 내에서 응용 프로그램의 프로세스를 다시 시작 하 여 적용할 수 있습니다. 이러한 파일이 동기화 되 면 *devhostagent* 프로세스를 사용 하 여 실행 중인 컨테이너 내에서 응용 프로그램의 프로세스가 다시 시작 됩니다. 응용 프로그램의 컨테이너를 처음 만들 때 컨트롤러는 응용 프로그램의 startup 명령을 *devhostagent*이라는 다른 프로세스로 바꿉니다. 그러면 응용 프로그램의 실제 프로세스가 *devhostagent*에서 자식 프로세스로 실행 되 고 해당 출력은 *devhostagent*의 출력을 사용 하 여 전달 됩니다. *Devhostagent* 프로세스는 또한 dev 공간의 일부 이며 dev 공간을 대신 하 여 실행 중인 컨테이너에서 명령을 실행할 수 있습니다. 다시 시작을 수행 하는 경우 *devhostagent*는 다음과 같습니다.

* 응용 프로그램과 관련 된 현재 프로세스 또는 프로세스를 중지 합니다.
* 응용 프로그램을 다시 빌드합니다.
* 응용 프로그램과 관련 된 프로세스를 다시 시작 합니다.

*Devhostagent* 가 이전 단계를 실행 하는 방식은 [에서 `azds.yaml` 구성 ][azds-yaml-section]됩니다.

Dockerfiles, .csproj 파일 또는 투구 차트의 일부와 같은 프로젝트 파일을 업데이트 하려면 응용 프로그램의 컨테이너를 다시 빌드하고 다시 배포 해야 합니다. 이러한 파일 중 하나가 dev 공간에 동기화 되 면 컨트롤러는 [투구 업그레이드][helm-upgrade] 명령을 실행 하 고 응용 프로그램의 컨테이너가 다시 작성 되 고 다시 배포 됩니다.

## <a name="file-synchronization"></a>파일 동기화

개발 공간에서 응용 프로그램이 처음 시작 될 때 모든 응용 프로그램의 원본 파일이 업로드 됩니다. 응용 프로그램이 실행 되 고 나중에 다시 시작 될 때 변경 된 파일만 업로드 됩니다. 이 프로세스를 조정 하는 데 사용 되는 두 개의 파일은 클라이언트 쪽 파일과 컨트롤러 측 파일입니다.

클라이언트 쪽 파일은 임시 디렉터리에 저장 되며, 개발 공간에서 실행 중인 프로젝트 디렉터리의 해시에 따라 이름이 지정 됩니다. 예를 들어 Windows에서 프로젝트에 대 한 *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* 와 같은 파일을 사용할 수 있습니다. Linux에서 클라이언트 쪽 파일은 */tmp* 디렉터리에 저장 됩니다. 명령을 실행 하 여 macOS에서 디렉터리를 찾을 수 있습니다 `echo $TMPDIR` .

이 파일은 다음을 포함 하는 JSON 형식입니다.

* 개발 공간과 동기화 된 각 프로젝트 파일에 대 한 항목입니다.
* 동기화 ID
* 마지막 동기화 작업의 타임 스탬프입니다.

각 프로젝트 파일 항목에는 파일에 대 한 경로 및 해당 타임 스탬프가 포함 됩니다.

컨트롤러 측 파일은 AKS 클러스터에 저장 됩니다. 여기에는 마지막 동기화의 동기화 ID와 타임 스탬프가 포함 됩니다.

동기화 타임 스탬프가 클라이언트 쪽 파일과 컨트롤러 측 파일 사이에서 일치 하지 않을 경우 동기화가 발생 합니다. 동기화 하는 동안 클라이언트 쪽 도구는 클라이언트 쪽 파일의 파일 항목을 반복 합니다. 파일의 타임 스탬프가 동기화 타임 스탬프 이후 이면 해당 파일이 dev 공간에 동기화 됩니다. 동기화가 완료 되 면 클라이언트 쪽 파일 및 컨트롤러 쪽 파일에서 동기화 타임 스탬프가 업데이트 됩니다.

클라이언트 쪽 파일이 없는 경우 모든 프로젝트 파일이 동기화 됩니다. 이 동작을 통해 클라이언트 쪽 파일을 삭제 하 여 전체 동기화를 강제로 수행할 수 있습니다.

## <a name="how-running-your-code-is-configured"></a>코드 실행 방법 구성

Azure Dev Spaces는 파일을 사용 하 여 `azds.yaml` 서비스를 설치 하 고 구성 합니다. 컨트롤러는 파일의 속성을 사용 하 여 `install` `azds.yaml` 투구 차트를 설치 하 고 Kubernetes 개체를 만듭니다.

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

기본적으로 `prep` 이 명령은 투구 차트를 생성 합니다. 또한 *설치. chart* 속성을 투구 차트의 디렉터리로 설정 합니다. 다른 위치에서 투구 차트를 사용 하려는 경우이 속성을 업데이트 하 여 해당 위치를 사용할 수 있습니다.

투구 차트를 설치할 때 Azure Dev Spaces는 투구 차트에서 값을 재정의 하는 방법을 제공 합니다. 투구 차트의 기본값은에 `charts/APP_NAME/values.yaml` 있습니다.

*Install. values* 속성을 사용 하 여 투구 차트에서 대체 하려는 값을 정의 하는 하나 이상의 파일을 나열할 수 있습니다. 예를 들어 개발 공간에서 응용 프로그램을 실행할 때 호스트 이름 또는 데이터베이스 구성이 특별히 필요한 경우이 재정의 기능을 사용할 수 있습니다. *를 추가할* 수도 있습니다. 모든 파일 이름 끝에 선택적으로 설정 합니다.

*Install. set* 속성을 사용 하면 투구 차트에서 바꾸려는 값을 하나 이상 구성할 수 있습니다. *Install. set* 에 구성 된 값은 *install. values*에 나열 된 파일에 구성 된 값을 재정의 합니다. *Install. set* 의 속성은 투구 차트의 값에 따라 다르며 생성 된 투구 차트에 따라 달라질 수 있습니다.

위의 예에서 *replicaCount* 속성은 개발자 공간에서 실행할 응용 프로그램의 인스턴스 수를 컨트롤러에 알려 줍니다. 시나리오에 따라이 값을 늘릴 수 있지만 응용 프로그램의 pod에 디버거를 연결 하는 데 영향을 줄 수 있습니다. 자세한 내용은 [문제 해결 문서][troubleshooting]를 참조 하세요.

생성 된 투구 차트에서 컨테이너 이미지는 {{로 설정 됩니다 *. Values. repository}}: {{. Values. tag}}*. `azds.yaml`이 파일은 기본적으로 *$ (tag)로 설정 하 여 $ (tag)* 로 *설정* 합니다 .이 속성은 {{에 대 한 값으로 사용 됩니다. * Values. tag}}*. 이렇게 하면 *install.* .. tag 속성을 설정 하 여 Azure Dev Spaces 실행 될 때 응용 프로그램에 대 한 컨테이너 이미지를 고유한 방식으로 태그를 지정할 수 있습니다. 이 특정 사례에서 이미지는 * \<value from image.repository> $ (tag)* 로 태그가 지정 됩니다. 개발 공간을 인식 하 고 AKS 클러스터에서 컨테이너를 찾기 위해 *$ (tag)* 변수를   *install.* 값으로 사용 해야 합니다.

위의 예제에서는 install... `azds.yaml` *install.set.ingress.hosts* *Install. ingress* 속성은 공용 끝점에 대 한 호스트 이름 형식을 정의 합니다. 이 속성은 컨트롤러에서 제공 하는 값인 *$ (spacePrefix)*, *$ (rootSpacePrefix)* 및 *$ (hostsuffix)* 도 사용 합니다.

*$ (SpacePrefix)* 는 *SPACENAME*형식으로 사용 되는 자식 개발 공간의 이름입니다. *$ (RootSpacePrefix)* 는 부모 공간의 이름입니다. 예를 들어 *azureuser* 가 *기본값*의 하위 공간이 면 *$ (rootSpacePrefix)* 에 대 한 값은 *기본값이* 고 *$ (spacePrefix)* 의 값은 *azureuser. s*입니다. 공간이 하위 공간이 아니면 *$ (spacePrefix)* 가 비어 있습니다. 예를 들어 *기본* 공간에 부모 공간이 없으면 *$ (rootSpacePrefix)* 에 대 한 값이 *기본값이* 고 *$ (spacePrefix)* 의 값이 비어 있습니다. *$ (Hostsuffix)* 는 AKS 클러스터에서 실행 되는 Azure Dev Spaces 수신 컨트롤러를 가리키는 DNS 접미사입니다. 이 DNS 접미사는 와일드 카드 DNS 항목에 해당 합니다 (예:) * \* . RANDOM_VALUE*AKS 클러스터에 Azure Dev Spaces 컨트롤러를 추가할 때 생성 된 azds입니다.

위의 파일에서 `azds.yaml` update-help를 업데이트 하 여 응용 *install.set.ingress.hosts* 프로그램의 호스트 이름을 변경할 수도 있습니다. 예를 들어 *$ (spacePrefix) $ (rootSpacePrefix) webfrontend 엔드 $ (hostSuffix)* 에서 *$ (spacePrefix) $ (rootSpacePrefix) 웹 $ (hostsuffix)* 로 응용 프로그램의 호스트 이름을 간소화 하려는 경우를 예로 들 수 있습니다.

응용 프로그램에 대 한 컨테이너를 빌드하기 위해 컨트롤러는 구성 파일의 아래 섹션을 사용 합니다 `azds.yaml` .

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

*빌드 컨텍스트* 속성은 Dockerfiles가 있는 디렉터리를 나열 합니다. *build.dockerfile* 속성은 응용 프로그램의 프로덕션 버전을 빌드하기 위한 dockerfile의 이름을 정의 합니다. *configurations.develop.build.dockerfile* 속성은 응용 프로그램 개발 버전의 dockerfile 이름을 구성 합니다.

개발 및 프로덕션에 대해 다른 Dockerfiles을 사용 하면 개발 중에 특정 작업을 사용 하도록 설정 하 고 프로덕션 배포에 대해 해당 항목을 사용 하지 않도록 설정할 수 있습니다 예를 들어 개발 중에 디버깅 또는 자세한 정보 로깅을 사용 하도록 설정 하 고 프로덕션 환경에서 사용 하지 않도록 설정할 수 있습니다. Dockerfiles의 이름이 다르게 지정 되거나 다른 위치에 있는 경우 이러한 속성을 업데이트할 수도 있습니다.

개발 중에 빠르게 반복 하는 데 도움이 되도록 Azure Dev Spaces는 변경 내용을 로컬 프로젝트에서 동기화 하 고 응용 프로그램을 증분 업데이트 합니다. 구성 파일의 아래 섹션은 `azds.yaml` 동기화 및 업데이트를 구성 하는 데 사용 됩니다.

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

변경 내용을 동기화 하는 파일 및 디렉터리는 *구성. 개발. container. .sync* 속성에 나열 됩니다. 이러한 디렉터리는 명령이 실행 될 때 `up` 와 변경 내용이 검색 된 경우에 처음에 동기화 됩니다. 개발 공간에 동기화 하려는 추가 또는 다른 디렉터리가 있는 경우이 속성을 변경할 수 있습니다.

*구성. container. buildCommands* 속성은 개발 시나리오에서 응용 프로그램을 빌드하는 방법을 지정 합니다. *구성. container. command* 속성은 개발 시나리오에서 응용 프로그램을 실행 하기 위한 명령을 제공 합니다. 개발 하는 동안 사용할 추가 빌드 또는 런타임 플래그 또는 매개 변수가 있는 경우 이러한 속성 중 하나를 업데이트 하는 것이 좋습니다.

*구성. 개발* . 응용 프로그램을 중지 하는 프로세스를 중지 하는 프로세스를 나열 합니다. 개발 하는 동안 응용 프로그램의 다시 시작 동작을 변경 하려는 경우이 속성을 업데이트 해야 할 수 있습니다. 예를 들어, 구성을 업데이트 한 경우.. *컨테이너. buildCommands* 또는 구성별을 반복 합니다. *container. command* 속성을 만들어 응용 프로그램을 빌드 또는 시작 하는 방법을 변경 하려면 중지 된 프로세스를 변경 해야 할 수 있습니다.

명령을 사용 하 여 코드를 준비할 때 `azds prep` 플래그를 추가 하는 옵션이 있습니다 `--enable-ingress` . 플래그를 추가 하면 `--enable-ingress` 응용 프로그램에 대해 공개적으로 액세스할 수 있는 URL이 만들어집니다. 이 플래그를 생략 하면 응용 프로그램은 클러스터 내에서 또는 localhost 터널을 사용 하는 경우에만 액세스할 수 있습니다. 명령을 실행 한 후 `azds prep` 에 다음에서 수신 설정 속성을 수정 하는이 설정을 변경할 수 있습니다 *.* `charts/APPNAME/values.yaml`

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>다음 단계

네트워킹 및 요청을 라우팅하는 방법에 대 한 자세한 내용은 [Azure Dev Spaces에서 라우팅이 작동 하는 방법][how-it-works-routing]을 참조 Azure Dev Spaces.

신속 하 게 반복 하 고 개발 하는 데 Azure Dev Spaces를 사용 하는 방법에 대 한 자세한 내용은 Kubernetes를 사용 하 [는 로컬 프로세스 작동 방식][how-it-works-local-process-kubernetes] 및 [Azure Dev Spaces를 사용 하 여 코드를 원격으로 디버그][how-it-works-remote-debugging]


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md