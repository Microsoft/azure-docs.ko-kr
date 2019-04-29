---
title: Azure Container Instances에서 컨테이너 업데이트
description: Azure Containers Instances 컨테이너 그룹에서 실행되는 컨테이너를 업데이트하는 방법을 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 2df6a2724cbdcd6bbb6c6ca6636256b7e399da8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686894"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 업데이트

컨테이너 인스턴스가 정상적으로 작동하는 동안 컨테이너 그룹의 컨테이너를 업데이트해야 할 수도 있습니다. 예를 들어 이미지 버전을 업데이트하거나, DNS 이름을 변경하거나, 환경 변수를 업데이트하거나, 애플리케이션이 손상된 컨테이너의 상태를 새로 고치려는 경우가 있습니다.

## <a name="update-a-container-group"></a>컨테이너 그룹 업데이트

하나 이상의 수정된 속성으로 기존 그룹을 다시 배포하여 컨테이너 그룹의 컨테이너를 업데이트합니다. 컨테이너 그룹을 업데이트하면 그룹에서 실행되는 모든 컨테이너가 다시 시작됩니다.

create 명령을 실행하거나 Azure Portal을 사용하여 기존 컨테이너 그룹을 다시 배포하고 기존 그룹의 이름을 지정합니다. create 명령을 실행하여 재배포가 트리거되면 그룹의 유효한 속성을 하나 이상 수정합니다. 일부 컨테이너 그룹 속성은 재배포에 적합하지 않습니다. 지원되지 않는 속성에 대한 목록은 [삭제해야 하는 속성](#properties-that-require-container-delete)을 참조하세요.

다음 Azure CLI 예제에서는 컨테이너 그룹을 새 DNS 이름 레이블로 업데이트합니다. 그룹의 DNS 이름 레이블 속성이 수정되므로 컨테이너 그룹이 다시 배포되고 해당 컨테이너가 다시 시작됩니다.

*myapplication-staging* DNS 이름 레이블을 사용한 초기 배포는 다음과 같습니다.

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

컨테이너 그룹을 새 *myapplication* DNS 이름 레이블로 업데이트합니다.

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>업데이트 혜택

기존 컨테이너 그룹을 업데이트하는 주요 이점은 배포 속도가 더 빠르다는 것입니다. 기존 컨테이너 그룹을 다시 배포하면 이전 배포에서 캐시된 계층에서 해당 컨테이너 이미지 계층을 가져옵니다. 새 배포를 통해 수행되는 것처럼 레지스트리에서 모든 이미지 계층을 새로 가져오는 대신 수정된 계층(있는 경우)만 가져옵니다.

Windows Server Core와 같이 더 큰 컨테이너 이미지를 기반으로 하는 애플리케이션에서 삭제하여 새로 배포하는 대신 업데이트하는 경우 배포 속도가 크게 향상될 수 있습니다.

## <a name="limitations"></a>제한 사항

컨테이너 그룹의 일부 속성만 업데이트를 지원합니다. 컨테이너 그룹의 일부 속성을 변경하려면 먼저 해당 그룹을 삭제한 다음, 다시 배포해야 합니다. 자세한 내용은 [컨테이너를 삭제해야 하는 속성](#properties-that-require-container-delete)을 참조하세요.

컨테이너 그룹을 업데이트하면 컨테이너 그룹의 모든 컨테이너가 다시 시작됩니다. 다중 컨테이너 그룹에 속한 특정 컨테이너에 대한 업데이트 또는 적절한 재시작은 수행할 수 없습니다.

일반적으로 컨테이너의 IP 주소는 업데이트 간에 변경되지 않지만 동일하게 유지되지는 않습니다. 컨테이너 그룹이 동일한 기본 호스트에 배포되는 한 컨테이너 그룹에서 해당 IP 주소를 유지합니다. 매우 드문 경우이지만 Azure Container Instances에서 동일한 호스트에 다시 배포하기 위해 모든 노력을 기울이는 한편, 다른 호스트로 다시 배포할 수 있는 일부 Azure 내부 이벤트가 있습니다. 이 문제를 완화하려면 컨테이너 인스턴스에 항상 DNS 이름 레이블을 사용합니다.

종료되거나 삭제된 컨테이너 그룹은 업데이트할 수 없습니다. 컨테이너 그룹이 중지되거나(*종료됨* 상태) 삭제되면 해당 그룹이 새 그룹으로 배포됩니다.

## <a name="properties-that-require-container-delete"></a>컨테이너를 삭제해야 하는 속성

앞에서 설명한 대로 컨테이너 그룹 속성 중 일부만 업데이트할 수 있습니다. 예를 들어 컨테이너의 포트 또는 다시 시작 정책을 변경하려면 먼저 컨테이너 그룹을 삭제한 다음, 해당 그룹을 다시 만들어야 합니다.

이러한 속성은 먼저 컨테이너 그룹을 삭제한 후에 다시 배포해야 합니다.

* OS 유형
* CPU
* 메모리
* 다시 시작 정책
* 포트

컨테이너 그룹을 삭제하고 다시 만들면 해당 그룹은 "다시 배포"되지 않고 새로 만들어집니다. 모든 이미지 계층은 이전 배포에서 캐시된 계층이 아니라 레지스트리에서 새로 가져옵니다. 컨테이너의 IP 주소도 다른 기본 호스트에 배포되어 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 **컨테이너 그룹**이 여러 번 언급되었습니다. Azure Container Instances의 모든 컨테이너는 컨테이너 그룹에 배포되고, 이 컨테이너 그룹에는 둘 이상의 컨테이너가 포함될 수 있습니다.

[Azure Container Instances의 컨테이너 그룹](container-instances-container-groups.md)

[다중 컨테이너 그룹 배포](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
