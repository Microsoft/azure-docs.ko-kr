---
title: Azure Container Registry 다단계 작업을 통해 이미지 작성, 테스트 및 패치 자동화
description: 클라우드에서 컨테이너 이미지를 빌드하고, 테스트하고, 패치하기 위한 작업 기반 워크플로를 제공하는 Azure Container Registry의 ACR 작업 기능인 다단계 작업에 대해 소개합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: ac0e4e9019a35d3fdb35c0b7af9cb1289f4bceeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829585"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>ACR 작업에서 다단계 작성, 테스트 및 패치 작업 실행

다단계 작업을 수행하면 여러 단계/컨테이너를 기반으로 하는 워크플로를 통해 ACR 작업의 단일 이미지 작성 및 푸시 기능을 확장할 수 있습니다. 다중 단계 작업을 사용하여 시리즈 또는 병렬로 여러 이미지를 빌드 및 푸시합니다. 그런 다음, 단일 작업 실행 내에서 명령으로 해당 이미지를 실행합니다. 각 단계는 컨테이너 이미지 작성 또는 푸시 작업을 정의하며 컨테이너 실행도 정의할 수 있습니다. 다단계 작업의 각 단계는 실행 환경으로 컨테이너를 사용합니다.

> [!IMPORTANT]
> 이전에 미리 보기 중에 `az acr build-task` 명령을 사용하여 작업을 만든 경우 [az acr task][az-acr-task] 명령을 사용하여 해당 작업을 다시 만들어야 합니다.

예를 들어 다음 논리를 자동화하는 단계가 포함된 작업을 실행할 수 있습니다.

1. 웹 애플리케이션 이미지 빌드
1. 웹 애플리케이션 컨테이너 실행
1. 웹 애플리케이션 테스트 이미지 빌드
1. 실행 중인 애플리케이션 컨테이너에 대해 테스트를 수행하는 웹 애플리케이션 테스트 컨테이너 실행
1. 테스트에 통과하면 Helm 차트 보관 패키지 빌드
1. 새 Helm 차트 보관 패키지를 사용하여 `helm upgrade` 수행

모든 단계는 Azure 내에서 수행됩니다. 즉, Azure 계산 리소스가 작업을 수행하므로 인프라 관리를 별도로 수행할 필요가 없습니다. Azure Container Registry를 제외하면 사용한 리소스에 대한 요금만 지불하면 됩니다. 가격 정보는 [Azure Container Registry pricing][pricing]의 **Container Build** 섹션을 참조하세요.


## <a name="common-task-scenarios"></a>일반 작업 시나리오

다단계 작업을 통해 수행할 수 있는 시나리오는 다음 논리와 같습니다.

* 하나 이상의 컨테이너 이미지를 차례로 또는 병렬로 작성/태그 지정/푸시
* 단위 테스트 및 코드 검사를 실행하고 해당 결과 캡처
* 기능 테스트를 실행하고 해당 결과 캡처. ACR 작업에서는 둘 이상의 컨테이너를 실행하고 컨테이너 간에 일련의 요청을 실행할 수 있습니다.
* 컨테이너 이미지 작성 전/후 단계를 포함한 작업 기반 실행 과정 수행
* 즐겨 사용하는 배포 엔진을 통해 대상 환경에 컨테이너 하나 이상 배포

## <a name="multi-step-task-definition"></a>다단계 작업 정의

ACR 작업의 다단계 작업은 YAML 파일 내에서 일련의 단계로 정의됩니다. 각 단계는 이전 단계 하나 이상의 정상 완료에 따른 종속성을 지정할 수 있습니다. 사용 가능한 작업 단계 유형은 다음과 같습니다.

* [`build`](container-registry-tasks-reference-yaml.md#build): 친숙 한를 사용 하 여 하나 이상의 컨테이너 이미지 빌드 `docker build` 시리즈 또는 병렬로 구문입니다.
* [`push`](container-registry-tasks-reference-yaml.md#push): 빌드된 이미지를 컨테이너 레지스트리로 푸시하십시오. Azure Container Registry 등의 개인 레지스트리와 공용 Docker 허브가 모두 지원됩니다.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): 실행 중인 작업의 컨텍스트 내에서 함수로 작동할 수 있도록 컨테이너를 실행 합니다. 컨테이너의 `[ENTRYPOINT]`에 매개 변수를 전달하고 env, detach 및 흔히 사용되는 기타 `docker run` 매개 변수와 같은 속성을 지정할 수 있습니다. `cmd` 단계 유형에서는 컨테이너를 동시에 실행하면서 단위 및 기능 테스트를 수행할 수 있습니다.

다음 코드 조각은 이러한 작업 단계 형식을 결합하는 방법을 보여줍니다. 다중 단계 작업은 다음과 유사한 YAML 파일을 사용하여 Dockerfile에서 단일 이미지를 빌드하고 레지스트리에 푸시하는 방법처럼 간단할 수 있습니다.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

더 복잡한 경우 이 가상의 다단계 정의에는 Helm을 빌드하고, 테스트하고, 패키징하고, 배포하기 위한 단계가 포함됩니다(컨테이너 레지스트리 및 Helm 리포지토리 구성이 표시되지 않음).

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

몇 가지 시나리오의 경우 전체 다단계 작업 YAML 파일 및 Dockerfile은 [작업 예제][task-examples]를 참조하세요.

## <a name="run-a-sample-task"></a>샘플 작업 실행

작업에서는 Git 커밋 또는 기본 이미지 업데이트 시의 자동화된 실행과 수동 실행(“빠른 실행”)이 모두 지원됩니다.

작업을 실행하려면 먼저 YAML 파일에서 작업의 단계를 정의한 다음 Azure CLI 명령 [az acr run][az-acr-run]을 실행합니다.

아래에는 샘플 작업 YAML 파일을 사용하여 작업을 실행하는 예제 Azure CLI 명령이 나와 있습니다. 이 작업의 단계는 이미지를 작성한 다음 푸시합니다. 명령을 실행하기 전에 `\<acrName\>`을 실제 Azure Container Registry 이름으로 바꾸세요.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

작업을 실행하면 출력에는 YAML 파일에 정의된 각 단계의 진행률이 표시됩니다. 다음 출력에서 단계는 `acb_step_0` 및 `acb_step_1`로 표시됩니다.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Git 커밋 또는 기본 이미지 업데이트 시의 자동화된 작성 작업에 대한 자세한 내용은 [이미지 작성 자동화](container-registry-tutorial-build-task.md) 및 [기본 이미지 업데이트 작성](container-registry-tutorial-base-image-update.md) 자습서 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

다단계 작업 참조 및 예제는 아래 문서에서 확인할 수 있습니다.

* [작업 참조](container-registry-tasks-reference-yaml.md) - 작업 단계 유형, 해당 속성 및 사용법을 확인할 수 있습니다.
* [작업 예제][task-examples] - 다양한 복잡도의 여러 시나리오에 사용 가능한 예제 `task.yaml` 파일이 제공됩니다.
* [Cmd 리포지토리](https://github.com/AzureCR/cmd) - ACR 작업에 대한 명령인 컨테이너의 컬렉션입니다.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task