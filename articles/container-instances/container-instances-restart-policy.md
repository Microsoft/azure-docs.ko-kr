---
title: Azure Container Instances의 컨테이너화된 작업에서 다시 시작 정책 사용
description: Azure Container Instances를 사용하여 빌드, 테스트 또는 이미지 렌더링 작업에서처럼 완료될 때까지 실행되는 작업을 실행하는 방법에 대해 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608121"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>다시 시작 정책으로 컨테이너 작업 실행

Azure Container Instances에서는 컨테이너를 배포하는 것이 쉽고 빠르기 때문에 컨테이너 인스턴스에서 빌드, 테스트 및 이미지 렌더링과 같은 일회성 작업을 실행하기 위한 강력한 플랫폼을 제공합니다.

구성 가능한 다시 시작 정책을 사용하면 프로세스가 완료될 때 컨테이너가 중지되도록 지정할 수 있습니다. 컨테이너 인스턴스는 초 단위로 비용이 청구되기 때문에 작업을 실행하는 컨테이너가 실행되는 동안 사용된 계산 리소스에 대해서만 요금이 부과됩니다.

이 문서에서 제시된 예제는 Azure CLI를 사용합니다. Azure CLI 버전 2.0.21 이상이 [로컬로 설치되어 있거나][azure-cli-install] [Azure Cloud Shell](../cloud-shell/overview.md)에서 CLI를 사용해야 합니다.

## <a name="container-restart-policy"></a>컨테이너 다시 시작 정책

Azure Container Instances에서 [컨테이너 그룹](container-instances-container-groups.md)을 만들 때 세 가지 다시 시작 정책 설정 중 하나를 지정할 수 있습니다.

| 다시 시작 정책   | 설명 |
| ---------------- | :---------- |
| `Always` | 컨테이너 그룹의 컨테이너가 항상 다시 시작됩니다. 컨테이너를 만들 때 다시 시작 정책이 지정되지 않은 경우 적용되는 **기본** 설정입니다. |
| `Never` | 컨테이너 그룹의 컨테이너가 절대로 다시 시작되지 않습니다. 컨테이너가 한 번만 실행됩니다. |
| `OnFailure` | 컨테이너 그룹의 컨테이너가 컨테이너에서 실행된 프로세스가 실패할 때만(0이 아닌 종료 코드로 종료될 때) 다시 시작됩니다. 컨테이너가 한 번 이상 실행됩니다. |

## <a name="specify-a-restart-policy"></a>다시 시작 정책 지정

다시 시작 정책을 지정하는 방법은 Azure CLI, Azure PowerShell cmdlet 또는 Azure Portal에서와 같이 컨테이너 인스턴스를 만드는 방법에 따라 다릅니다. Azure CLI에서 [create a container][az-container-create]를 호출할 때 `--restart-policy` 매개 변수를 지정합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>완료될 때까지 실행 예제

작업 다시 시작 정책의 작동 방식을 보려면을 Microsoft에서 컨테이너 인스턴스를 만듭니다 [aci wordcount] [ aci-wordcount-image] 이미지 및 지정 된 `OnFailure` 다시 시작 정책입니다. 이 예제 컨테이너는 기본적으로 셰익스피어의 [Hamlet](http://shakespeare.mit.edu/hamlet/full.html) 텍스트를 분석하고, 가장 많이 쓰이는 10개의 단어를 STDOUT에 쓰고 종료하는 Python 스크립트를 실행합니다.

다음 [az container create][az-container-create] 명령으로 예제 컨테이너를 실행합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances는 컨테이너를 시작한 다음, 애플리케이션(또는 이 경우 스크립트)이 종료될 때 컨테이너를 중지합니다. Azure Container Instances가 다시 시작 정책이 `Never` 또는 `OnFailure`인 컨테이너를 중지하면 컨테이너의 상태가 **Terminated**으로 설정됩니다. [az container show][az-container-show] 명령을 사용하여 컨테이너의 상태를 확인할 수 있습니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

예제 출력:

```bash
"Terminated"
```

예제 컨테이너의 상태가 *Terminated*로 표시되면 컨테이너 로그를 확인하여 작업 출력을 볼 수 있습니다. 스크립트의 출력을 보려면 [az container logs][az-container-logs] 명령을 실행합니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

출력

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

이 예제는 스크립트가 STDOUT으로 보낸 출력을 보여 줍니다. 그러나 컨테이너 작업은 나중에 검색하기 위해 출력을 영구적 저장소에 쓸 수도 있습니다. 예를 들어 [Azure 파일 공유](container-instances-mounting-azure-files-volume.md)에 쓸 수 있습니다.

## <a name="next-steps"></a>다음 단계

여러 컨테이너를 사용 하 여 큰 데이터 집합을 처리 하는 일괄 처리와 같은 작업 기반 시나리오에서 사용자 지정을 이용할 수 [환경 변수](container-instances-environment-variables.md) 하거나 [명령줄](container-instances-start-command.md) 런타임 시.

완료될 때까지 실행되는 컨테이너 출력을 유지하는 방법에 대한 자세한 내용은 [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](container-instances-mounting-azure-files-volume.md)를 참조하세요.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
