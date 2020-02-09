---
title: ACR 작업 샘플
description: 컨테이너 이미지를 빌드, 실행 및 패치 하는 샘플 Azure Container Registry 작업 (ACR 작업)
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456092"
---
# <a name="acr-tasks-samples"></a>ACR 작업 샘플

이 문서에서는 몇 가지 [Azure Container Registry 작업](container-registry-tasks-overview.md) (ACR 작업) 시나리오에 대한 예제 `task.yaml` 파일 및 관련 Dockerfiles에 연결 합니다. 

추가 예제는 [Azure 샘플][task-examples] 리포지토리를 참조 하세요.

## <a name="scenarios"></a>시나리오

* **이미지** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 빌드

*  - [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml) **컨테이너를 실행** 합니다.

* [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) - **이미지를 빌드하고 푸시합니다** .

* 이미지 - [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) 을 **빌드 및 실행** 합니다.

* [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) -  **여러 이미지를 빌드하고 푸시합니다** .

* 동시 -  [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) **에서 이미지 빌드 및 테스트**

* 여러 레지스트리 - [Yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile) **에 이미지를 빌드하고 푸시합니다** .


## <a name="next-steps"></a>다음 단계

ACR 작업에 대해 자세히 알아보세요.

* [다중 단계 작업](container-registry-tasks-multi-step.md) -클라우드의 컨테이너 이미지를 빌드, 테스트 및 패치 하기 위한 ACR 작업 기반 워크플로.
* [작업 참조](container-registry-tasks-reference-yaml.md) - 작업 단계 유형, 해당 속성 및 사용법을 확인할 수 있습니다.
* [Cmd 리포지토리](https://github.com/AzureCR/cmd) -ACR 태스크에 대한 명령으로 컨테이너의 컬렉션입니다.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
