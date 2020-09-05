---
title: Azure Dev Spaces 작동을 위해 프로젝트를 준비 하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces 작업을 사용 하 여 프로젝트를 준비 하는 방법을 설명 합니다.
keywords: azds, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 61351072494b51d02a1d6c31399208b9e9b54fce
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213429"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Azure Dev Spaces 작동을 위해 프로젝트를 준비 하는 방법

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. 개발자 공간은 프로젝트에 대해 Dockerfiles 및 투구 차트를 생성할 수 있습니다. 또한 개발자 공간은 AKS에서 Kubernetes 응용 프로그램을 배포, 실행 및 디버그 하기 위한 구성 파일을 만들고 사용 합니다. 이러한 모든 파일은 응용 프로그램 코드와 함께 존재 하며 버전 제어 시스템에 추가할 수 있습니다.

이 문서에서는 Dev Spaces를 사용 하 여 AKS에서 실행 하기 위해 프로젝트를 준비 하는 상황을 설명 합니다.

## <a name="prepare-your-code"></a>코드 준비

개발 공간에서 응용 프로그램을 실행 하려면 컨테이너 화 된 해야 하며, Kubernetes에 배포 하는 방법을 정의 해야 합니다. 응용 프로그램을 컨테이너 화 하려면 Dockerfile이 필요 합니다. 응용 프로그램을 Kubernetes에 배포 하는 방법을 정의 하려면 [투구 차트가](https://docs.helm.sh/)필요 합니다. 응용 프로그램에 대 한 Dockerfile 및 투구 차트를 만드는 데 도움을 주는 클라이언트 쪽 도구는 `prep` 다음 명령을 제공 합니다.

```cmd
azds prep --enable-ingress
```

`prep`이 명령은 프로젝트의 파일을 확인 하 고 Kubernetes에서 응용 프로그램을 실행 하기 위한 Dockerfile 및 투구 차트를 만들려고 합니다. 현재 `prep` 명령은 다음 언어로 Dockerfile 및 투구 차트를 생성 합니다.

* Java
* Node.js
* .NET Core

*must* `prep` 소스 코드를 포함 하는 디렉터리에서 명령을 실행 해야 합니다. `prep`올바른 디렉터리에서 명령을 실행 하면 클라이언트 쪽 도구에서 언어를 식별 하 고 적절 한 Dockerfile을 만들어 응용 프로그램을 컨테이너 화 수 있습니다. `prep`Java 프로젝트용 *pom.xml* 파일을 포함 하는 디렉터리에서 명령을 실행할 수도 있습니다.

`prep`소스 코드를 포함 하지 않는 디렉터리에서 명령을 실행 하는 경우 클라이언트 쪽 도구는 Dockerfile을 생성 하지 않습니다. *지원 되지 않는 언어로 인해 Dockerfile을 생성할*수 없다는 오류도 표시 됩니다. 이 오류는 클라이언트 쪽 도구에서 프로젝트 형식을 인식 하지 못하는 경우에도 발생 합니다.

명령을 실행할 때 `prep` 플래그를 지정 하는 옵션이 있습니다 `--enable-ingress` . 이 플래그는 컨트롤러에이 서비스에 대 한 인터넷 액세스 가능 끝점을 만들도록 지시 합니다. 이 플래그를 지정 하지 않으면 클러스터 내 에서만 또는 클라이언트 쪽 도구에서 만든 localhost 터널을 사용 하 여 서비스에 액세스할 수 있습니다. `prep`생성 된 투구 차트를 업데이트 하 여 명령을 실행 한 후에이 동작을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

`prep`이 명령은 프로젝트에서 기존 Dockerfiles 또는 투구 차트를 대체 하지 않습니다. 기존 Dockerfile 또는 투구 차트에서 명령으로 생성 된 파일과 동일한 명명 규칙을 사용 하는 경우 `prep` 명령에서 `prep` 해당 파일 생성을 건너뜁니다. 그렇지 않으면 `prep` 명령은 기존 파일과 함께 자체 Dockerfile 또는 투구 차트를 생성 합니다.

> [!IMPORTANT]
> Azure Dev Spaces는 프로젝트에 대 한 Dockerfile 및 투구 차트를 사용 하 여 코드를 빌드하고 실행 하지만 프로젝트를 빌드하고 실행 하는 방법을 변경 하려면 이러한 파일을 수정할 수 있습니다.

`prep`또한이 명령은 `azds.yaml` 프로젝트의 루트에 파일을 생성 합니다. Azure Dev Spaces는이 파일을 사용 하 여 응용 프로그램을 빌드, 설치, 구성 및 실행 합니다. 이 구성 파일은 Dockerfile 및 투구 차트의 위치를 나열 하 고 이러한 아티팩트 위에 추가 구성을 제공 합니다.

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

`azds.yaml`명령으로 생성 된 파일은 `prep` 간단한 단일 프로젝트 개발 시나리오에 사용할 수 있습니다. 특정 프로젝트의 복잡성이 증가 하는 경우 명령을 실행 한 후이 파일을 업데이트 해야 할 수 있습니다 `prep` . 예를 들어 개발 또는 디버깅 요구 사항에 따라 프로젝트에 빌드 또는 시작 프로세스를 변경 해야 할 수 있습니다. 프로젝트에 여러 응용 프로그램이 있을 수도 있습니다 .이 경우 여러 빌드 프로세스나 다른 빌드 콘텐츠가 필요 합니다.

## <a name="next-steps"></a>다음 단계

개발 공간에서 코드를 실행 하는 방법에 대 한 자세한 내용은 [Azure Dev Spaces 작업을 사용 하 여 코드 실행 방법][how-it-works-up]을 참조 하세요.

[how-it-works-up]: how-dev-spaces-works-up.md