---
title: ACR 작업 개요
description: 안전 하 고 자동화 된 컨테이너 이미지 빌드, 관리 및 클라우드에서 패치를 제공 하는 Azure Container Registry의 기능 모음인 ACR 작업에 대해 소개 합니다.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: f8ab3c3bd259f83a61d0b030a49e158ccd6e2a69
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938872"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>ACR 작업을 사용 하 여 컨테이너 이미지 빌드 및 유지 관리 자동화

컨테이너는 새 수준의 가상화를 제공하여 인프라 및 운영 요구 사항에서 애플리케이션 및 개발자 종속성을 격리합니다. 그러나이 응용 프로그램 가상화를 관리 하 고 컨테이너 수명 주기 동안 패치 하는 방법을 해결 해야 하는 것은 무엇 인가요?

## <a name="what-is-acr-tasks"></a>ACR 작업이란?

**ACR 작업**은 Azure Container Registry 내의 기능 모음입니다. Linux, Windows 및 ARM을 비롯 한 [플랫폼](#image-platforms) 에 대한 클라우드 기반 컨테이너 이미지 빌드를 제공 하며 Docker 컨테이너에 대한 [OS 및 프레임 워크 패치](#automate-os-and-framework-patching) 를 자동화할 수 있습니다. ACR 작업은 주문형 컨테이너 이미지 빌드를 사용 하 여 "내부 루프" 개발 주기를 클라우드로 확장할 뿐만 아니라 소스 코드 업데이트, 컨테이너의 기본 이미지 업데이트 또는 타이머에 의해 트리거되는 자동화 된 빌드도 가능 하 게 합니다. 예를 들어 기본 이미지 업데이트 트리거를 사용 하 여 OS 및 응용 프로그램 프레임 워크 패치 워크플로를 자동화 하 고, 변경할 수 없는 컨테이너의 원칙을 준수 하면서 보안 환경을 유지 관리할 수 있습니다.

## <a name="task-scenarios"></a>작업 시나리오

ACR 작업은 컨테이너 이미지 및 기타 아티팩트를 빌드하고 유지 관리 하는 여러 가지 시나리오를 지원 합니다. 자세한 내용은이 문서의 다음 섹션을 참조 하세요.

* **[빠른 작업](#quick-task)** -로컬 Docker 엔진을 설치 하지 않고도 Azure의 주문형 컨테이너 레지스트리에 단일 컨테이너 이미지를 빌드하고 푸시합니다. 클라우드의 `docker build`, `docker push`를 생각하면 됩니다.
* **자동으로 트리거된 작업** -하나 이상의 *트리거* 를 사용 하 여 이미지를 빌드합니다.
  * **[소스 코드 업데이트에 대한 트리거](#trigger-task-on-source-code-update)** 
  * **[기본 이미지 업데이트에 대한 트리거](#automate-os-and-framework-patching)** 
  * **[일정에 따라 트리거](#schedule-a-task)** 
* 다단계 **[작업](#multi-step-tasks)** -다중 단계 다중 컨테이너 기반 워크플로를 사용 하 여 ACR 작업의 단일 이미지 빌드 및 푸시 기능을 확장 합니다. 

각 ACR 작업에는 연결 된 [소스 코드 컨텍스트](#context-locations) (컨테이너 이미지 또는 다른 아티팩트를 빌드하는 데 사용 되는 소스 파일 집합의 위치)가 있습니다. 예제 컨텍스트에는 Git 리포지토리 또는 로컬 파일 시스템이 포함 됩니다.

작업은 또한 [실행 변수](container-registry-tasks-reference-yaml.md#run-variables)를 활용할 수 있으므로 이미지 및 아티팩트에 대한 작업 정의 및 표준화 태그를 다시 사용할 수 있습니다.

## <a name="quick-task"></a>빠른 작업

내부 루프 개발 주기는 원본 제어에 커밋하기 전에 애플리케이션 코드 작성, 빌드 및 테스트를 반복하는 프로세스로, 실제로 컨테이너 수명 주기 관리의 시작입니다.

첫 번째 코드 줄을 커밋하기 전에, ACR 작업의 빠른 [작업](container-registry-tutorial-quick-task.md) 기능은 컨테이너 이미지 빌드를 Azure에 오프로드하여 통합 개발 환경을 제공할 수 있습니다. 빠른 작업을 사용하면 코드를 커밋하기 전에 자동화된 빌드 정의를 확인하고 잠재적인 문제점을 발견할 수 있습니다.

친숙 한 `docker build` 형식을 사용 하 여 Azure CLI의 [az acr build][az-acr-build] 명령은 [컨텍스트](#context-locations) (빌드할 파일 집합)를 사용 하 여 acr 작업을 보내고, 기본적으로 완료 되 면 빌드된 이미지를 레지스트리에 푸시합니다.

소개는 Azure Container Registry에서 [컨테이너 이미지를 빌드하고 실행](container-registry-quickstart-task-cli.md) 하는 빠른 시작을 참조 하세요.  

ACR 작업은 기본 컨테이너 수명 주기로 설계되었습니다. 예를 들어, ACR 작업을 CI/CD 솔루션에 통합합니다. [서비스 주체로][az-login-service-principal] [az login][az-login] 을 실행 하 여 CI/CD 솔루션은 [az acr build][az-acr-build] 명령을 실행 하 여 이미지 빌드를 시작할 수 있습니다.

첫 번째 ACR 작업 자습서인 [Azure Container Registry 작업을 사용하여 클라우드에서 컨테이너 이미지 빌드](container-registry-tutorial-quick-task.md)에서 빠른 작업을 사용하는 방법을 알아보세요.

> [!TIP]
> Dockerfile을 사용 하지 않고 소스 코드에서 직접 이미지를 빌드하고 푸시 하려면 [az acr pack build][az-acr-pack-build] 명령 (미리 보기)을 제공 Azure Container Registry 합니다. 이 도구는 [클라우드 네이티브 Buildpacks](https://buildpacks.io/)을 사용 하 여 응용 프로그램 소스 코드에서 이미지를 빌드하고 푸시합니다.

## <a name="trigger-task-on-source-code-update"></a>소스 코드 업데이트에 대한 트리거 작업

GitHub 또는 Azure DevOps의 공용 또는 개인 Git 리포지토리로 코드를 커밋하거나 끌어오기 요청을 만들거나 업데이트할 때 컨테이너 이미지 빌드 또는 다중 단계 작업을 트리거합니다. 예를 들어 Git 리포지토리를 지정 하 고 선택적으로 분기 및 Dockerfile을 지정 하 여 Azure CLI 명령 [az acr task create][az-acr-task-create] 를 사용 하 여 빌드 작업을 구성 합니다. 팀이 리포지토리의 코드를 업데이트할 때 ACR 작업 생성 webhook는 리포지토리에 정의 된 컨테이너 이미지의 빌드를 트리거합니다. 

ACR 태스크는 Git 리포지토리를 작업의 컨텍스트로 설정할 때 다음과 같은 트리거를 지원 합니다.

| 트리거 | 기본적으로 사용 |
| ------- | ------------------ |
| Commit | 예 |
| 끌어오기 요청 | 아닙니다. |

소스 코드 업데이트 트리거를 구성 하려면 공용 또는 개인 GitHub 또는 Azure DevOps 리포지토리에서 webhook를 설정 하기 위해 작업에 PAT (개인용 액세스 토큰)를 제공 해야 합니다.

> [!NOTE]
> 현재 ACR 작업은 GitHub Enterprise 리포지토리에서 커밋 또는 끌어오기 요청 트리거를 지원하지 않습니다.

두 번째 ACR 작업 자습서인 [Azure Container Registry 작업을 사용하여 컨테이너 이미지 빌드 자동화](container-registry-tutorial-build-task.md)에서 소스 코드 커밋 시 빌드를 트리거하는 방법을 알아보세요.

## <a name="automate-os-and-framework-patching"></a>OS 및 프레임워크 패치 자동화

컨테이너 빌드 워크플로를 실제로 향상시키는 ACR 작업의 기능은 기본 이미지에 대한 업데이트를 검색하는 기능에서 제공됩니다. 업데이트 된 기본 이미지가 레지스트리에 푸시되는 경우 또는 Docker Hub와 같은 공용 리포지토리에서 기본 이미지가 업데이트 되는 경우 ACR 작업은이에 따라 모든 응용 프로그램 이미지를 자동으로 빌드할 수 있습니다.

컨테이너 이미지는 *기본* 이미지와 *애플리케이션* 이미지로 크게 분류할 수 있습니다. 기본 이미지에는 일반적으로 애플리케이션이 빌드되는 운영 체제 및 애플리케이션 프레임워크가 다른 사용자 지정 항목과 함께 포함됩니다. 이러한 기본 이미지는 일반적으로 공용 업스트림 이미지 (예: [알파인 Linux][base-alpine], [Windows][base-windows], [.net][base-dotnet]또는 [node.js][base-node])를 기반으로 합니다. 일부 애플리케이션 이미지에서는 일반적인 기본 이미지를 공유할 수 있습니다.

중요한 OS 보안 패치와 같이 업스트림 유지 관리자가 OS 또는 응용 프로그램 프레임워크 이미지를 업데이트하는 경우, 중요한 수정 프로그램이 포함되도록 기본 이미지도 업데이트해야 합니다. 그러면 각 애플리케이션 이미지도 이제 기본 이미지에 포함된 이러한 업스트림 수정 프로그램을 포함하도록 다시 빌드해야 합니다.

ACR 작업은 컨테이너 이미지를 빌드할 때 기본 이미지 종속성을 동적으로 검색하므로 애플리케이션 이미지의 기본 이미지가 업데이트되는 시기를 검색할 수 있습니다. 미리 구성된 하나의 [빌드 작업](container-registry-tutorial-base-image-update.md#create-a-task)을 사용하면 ACR 작업에서 **모든 애플리케이션 이미지를 자동으로 다시 빌드합니다**. 이 자동 검색 및 다시 빌드를 통해 ACR 작업에서 업데이트된 기본 이미지를 참조하는 각 애플리케이션 이미지를 빠짐없이 수동으로 추적하고 업데이트하는 데 필요한 시간과 노력을 절약할 수 있습니다.

Dockerfile에서 이미지 빌드의 경우 기본 이미지가 다음 위치 중 하나에 있으면 ACR 태스크가 기본 이미지 업데이트를 추적 합니다.

* 태스크가 실행되는 동일한 Azure Container Registry
* 동일한 지역의 다른 Azure Container Registry 
* Docker Hub의 공용 리포지토리
* Microsoft 컨테이너 레지스트리의 공용 리포지토리

> [!NOTE]
> * 기본 이미지 업데이트 트리거는 ACR 작업에서 기본적으로 사용 하도록 설정 됩니다. 
> * 현재 ACR 작업은 응용 프로그램 (*런타임*) 이미지에 대한 기본 이미지 업데이트만 추적 합니다. ACR 작업은 다중 단계 Dockerfiles에서 사용 되는 중간 (*buildtime*) 이미지에 대한 기본 이미지 업데이트를 추적 하지 않습니다. 

세 번째 ACR 작업 자습서의 OS 및 프레임 워크 패치에 대한 자세한 내용은 [Azure Container Registry 작업을 사용 하 여 기본 이미지 업데이트에서 이미지 빌드 자동화](container-registry-tutorial-base-image-update.md)를 확인 하세요.

## <a name="schedule-a-task"></a>작업 예약

필요에 따라 작업을 만들거나 업데이트할 때 하나 이상의 *타이머 트리거* 를 설정 하 여 작업을 예약 합니다. 작업 예약은 정의 된 일정에 따라 컨테이너 작업을 실행 하거나 레지스트리에 정기적으로 푸시되는 이미지에 대한 유지 관리 작업 또는 테스트를 실행 하는 데 유용 합니다. 자세한 내용은 [정의 된 일정에 따라 ACR 작업 실행](container-registry-tasks-scheduled.md)을 참조 하세요.

## <a name="multi-step-tasks"></a>다단계 작업

다단계 태스크는 클라우드에서 컨테이너 이미지를 빌드, 테스트 및 패치 하기 위한 단계 기반 작업 정의 및 실행을 제공 합니다. [Yaml 파일](container-registry-tasks-reference-yaml.md) 에 정의 된 작업 단계는 컨테이너 이미지 또는 다른 아티팩트에 대한 개별 빌드 및 푸시 작업을 지정 합니다. 해당 실행 환경으로 컨테이너를 사용하여 각 단계로 하나 이상의 컨테이너 실행을 정의할 수도 있습니다.

예를 들어, 다음을 자동화하는 다단계 작업을 만들 수 있습니다.

1. 웹 애플리케이션 이미지 빌드
1. 웹 애플리케이션 컨테이너 실행
1. 웹 애플리케이션 테스트 이미지 빌드
1. 실행 중인 응용 프로그램 컨테이너에 대해 테스트를 수행 하는 웹 응용 프로그램 테스트 컨테이너를 실행 합니다.
1. 테스트에 통과하면 Helm 차트 보관 패키지 빌드
1. 새 Helm 차트 보관 패키지를 사용하여 `helm upgrade` 수행

다단계 작업을 사용하면 단계 간 종속성 지원을 통해 이미지 빌드, 실행 및 테스트를 보다 복잡한 단계로 분할할 수 있습니다. ACR 작업의 다단계 작업을 통해 이미지 빌드, 테스트, OS 및 프레임워크 패치 워크플로를 보다 세부적으로 제어할 수 있습니다.

[ACR 작업에서 다단계 빌드, 테스트 및 패치 작업 실행](container-registry-tasks-multi-step.md)에서 다단계 작업에 대해 알아보세요.

## <a name="context-locations"></a>컨텍스트 위치

다음 표에서는 ACR 작업에 지원되는 컨텍스트 위치의 몇 가지 예를 보여 줍니다.

| 컨텍스트 위치 | Description | 예 |
| ---------------- | ----------- | ------- |
| 로컬 파일 시스템 | 로컬 파일 시스템의 디렉터리 내에 있는 파일. | `/home/user/projects/myapp` |
| GitHub 마스터 분기 | 공용 또는 개인 GitHub 리포지토리의 마스터 (또는 다른 기본) 분기 내에 있는 파일입니다.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 분기 | 공용 또는 개인 GitHub 리포지토리의 특정 분기.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 하위 폴더 | 공용 또는 개인 GitHub 리포지토리의 하위 폴더에 있는 파일입니다. 예제에서는 분기와 하위 폴더 사양의 조합을 보여 줍니다. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub 커밋 | 공용 또는 개인 GitHub 리포지토리의 특정 커밋. 예제에서는 SHA (커밋 해시)와 하위 폴더 사양의 조합을 보여 줍니다. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps 하위 폴더 | 공용 또는 개인 Azure 리포지토리의 하위 폴더 내 파일. 예제에서는 분기와 하위 폴더 사양의 조합을 보여 줍니다. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| 원격 Tarball | 원격 웹 서버의 압축된 아카이브에 있는 파일. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> 작업의 컨텍스트로 개인 Git 리포지토리를 사용 하는 경우에는 PAT (개인용 액세스 토큰)를 제공 해야 합니다.

## <a name="image-platforms"></a>이미지 플랫폼

기본적으로 ACR 작업은 Linux OS 및 amd64 아키텍처용 이미지를 빌드합니다. 다른 아키텍처에 대한 Windows 이미지 또는 Linux 이미지를 빌드하기 위해 `--platform` 태그를 지정 합니다. Os를 지정 하 고 필요에 따라 OS/아키텍처 형식으로 지원 되는 아키텍처를 지정 합니다 (예: `--platform Linux/arm`). ARM 아키텍처의 경우 필요에 따라 OS/아키텍처/변형 형식 (예: `--platform Linux/arm64/v8`)에서 variant를 지정 합니다.

| OS | 아키텍처|
| --- | ------- | 
| Linux | amd64<br/>암<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>작업 로그 보기

각 태스크 실행은 작업 단계가 성공적으로 실행 되었는지 여부를 확인 하기 위해 검사할 수 있는 로그 출력을 생성 합니다. [Az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run)또는 [az acr task run](/cli/azure/acr/task#az-acr-task-run) 명령을 사용 하 여 작업을 트리거하는 경우에는 태스크 실행에 대한 로그 출력이 콘솔로 스트리밍되 고 나중에 검색할 수 있도록 저장 됩니다. 소스 코드 커밋 또는 기본 이미지 업데이트 등에서 태스크가 자동으로 트리거되면 작업 로그만 저장 됩니다. Azure Portal에서 실행 되는 작업에 대한 로그를 보거나 [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) 명령을 사용 합니다.

기본적으로 레지스트리의 태스크 실행에 대한 데이터 및 로그는 30 일 동안 보존 된 후 자동으로 제거 됩니다. 태스크 실행에 대한 데이터를 보관 하려면 [az acr 작업 업데이트-실행](/cli/azure/acr/task#az-acr-task-update-run) 명령을 사용 하 여 보관을 사용 하도록 설정 합니다. 다음 예에서는 registry *myregistry*에서 *cf11* 작업 실행을 위해 보관을 사용 하도록 설정 합니다.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>다음 단계

클라우드에서 컨테이너 이미지 빌드 및 유지 관리를 자동화할 준비가 되 면 [ACR Tasks 자습서 시리즈](container-registry-tutorial-quick-task.md)를 확인 하세요.

선택적으로 Azure 컨테이너 레지스트리와 작동할 [Visual Studio Code용 Docker 확장](https://code.visualstudio.com/docs/azure/docker)과 [Azure 계정](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 확장을 설정합니다. Visual Studio Code 내에서 Azure 컨테이너 레지스트리에 이미지를 밀어넣고 끌어오거나, ACR Tasks를 실행합니다.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
