---
title: 수동으로 컨테이너 그룹 중지 또는 시작
description: Azure Container Instances에서 컨테이너 그룹을 수동으로 중지 하거나 시작 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: f697f18459959cf40e71d3ca90cd8b42aaa74239
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799080"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Azure Container Instances에서 수동으로 컨테이너 중지 또는 시작

컨테이너 그룹의 [다시 시작 정책](container-instances-restart-policy.md) 설정은 컨테이너 인스턴스를 기본적으로 시작 하거나 중지 하는 방법을 결정 합니다. 컨테이너 그룹을 수동으로 중지 하거나 시작 하 여 기본 설정을 재정의할 수 있습니다.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>중지

실행 중인 컨테이너 그룹을 수동으로 중지 합니다. 예를 들어 [az container stop][az-container-stop] 명령을 사용 하거나 Azure Portal 합니다. 특정 컨테이너 워크 로드의 경우 정의 된 기간 후 장기 실행 컨테이너 그룹을 중지 하 여 비용을 절감할 수 있습니다. 

*컨테이너 그룹이 중지 됨 상태로 들어가면 그룹의 모든 컨테이너를 종료 하 고 재활용 합니다. 컨테이너 상태를 유지 하지 않습니다.*

컨테이너를 재활용 하면 [리소스](container-instances-container-groups.md#resource-allocation) 할당이 취소 되 고 컨테이너 그룹에 대 한 청구는 중지 됩니다.

컨테이너 그룹이 이미 종료 된 경우 (성공 또는 실패 상태)에는 중지 작업이 적용 되지 않습니다. 예를 들어 실행 한 후 실행 된 컨테이너 태스크가 있는 컨테이너 그룹은 성공 상태에서 성공적으로 종료 됩니다. 해당 상태에서 그룹을 중지 하려고 하면 상태가 변경 되지 않습니다. 

## <a name="start"></a>시작

컨테이너가 자체에서 종료 되었거나 수동으로 그룹을 중지 했기 때문에 컨테이너 그룹을 중지 한 경우 컨테이너를 시작할 수 있습니다. 예를 들어 [az container start][az-container-start] 명령을 사용 하거나 Azure Portal를 사용 하 여 그룹의 컨테이너를 수동으로 시작할 수 있습니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 시작하면 동일한 컨테이너 구성을 가진 새 배포가 시작됩니다. 이 작업은 예상대로 작동하는 알려진 컨테이너 그룹 구성을 빠르게 다시 사용하는 데 도움이 될 수 있습니다. 동일한 워크로드를 실행하기 위해 새 컨테이너 그룹을 만들 필요가 없습니다.

컨테이너 그룹의 모든 컨테이너는이 작업에 의해 시작 됩니다. 그룹에서 특정 컨테이너를 시작할 수 없습니다.

컨테이너 그룹을 수동으로 시작 또는 다시 시작한 후에 컨테이너 그룹은 구성된 다시 시작 정책에 따라 실행됩니다.
  
## <a name="restart"></a>재시작

예를 들어 [az container restart][az-container-restart] 명령을 사용 하 여 실행 되는 동안 컨테이너 그룹을 다시 시작할 수 있습니다. 이 작업은 컨테이너 그룹의 모든 컨테이너를 다시 시작합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 다시 시작하면 배포 문제를 해결하려는 경우 도움이 됩니다. 예를 들어 임시 리소스 제한 사항으로 인해 컨테이너가 성공적으로 실행되지 않는 경우 그룹을 다시 시작하면 문제가 해결될 수 있습니다.

컨테이너 그룹의 모든 컨테이너는이 작업으로 다시 시작 됩니다. 그룹에서 특정 컨테이너를 다시 시작할 수 없습니다.

컨테이너 그룹을 수동으로 다시 시작 하면 컨테이너 그룹은 구성 된 다시 시작 정책에 따라 실행 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Container Instances에서 [다시 시작 정책 설정](container-instances-restart-policy.md) 에 대해 자세히 알아보세요.

기존 구성을 사용 하 여 컨테이너 그룹을 수동으로 중지 하 고 시작 하는 것 외에도 실행 중인 컨테이너 그룹의 [설정을 업데이트할](container-instances-update.md) 수 있습니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
