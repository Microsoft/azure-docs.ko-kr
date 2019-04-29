---
title: 수동으로 중지 하거나 Azure Container Instances에서 컨테이너를 시작 합니다.
description: 수동으로 중지 하거나 Azure Container Instances에서 컨테이너 그룹을 시작 하는 방법에 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 50f3ecf69561313a5bda67827cfb02d2f61d461f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653664"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>수동으로 중지 하거나 Azure Container Instances에서 컨테이너를 시작 합니다.

합니다 [다시 시작 정책](container-instances-restart-policy.md) 설정 컨테이너 그룹의 컨테이너 인스턴스 시작 또는 기본적으로 중지 하는 방법을 결정 합니다. 기본 설정을 수동으로 중지 하거나 컨테이너 그룹을 시작 하 여 재정의할 수 있습니다.

## <a name="stop"></a>중지

수동으로 사용 하 여 실행 중인 컨테이너 그룹-예를 들어, 중지 된 [az 컨테이너 중지] [ az-container-stop] 명령 또는 Azure portal. 특정 컨테이너 워크 로드를 하려는 비용을 절약에 정의 된 기간이 지나면 장기 실행 컨테이너 그룹을 중지 합니다. 

*중지 됨 상태로 전환 하는 컨테이너 그룹을 종료 하 고 그룹의 모든 컨테이너를 재활용 합니다. 컨테이너 상태를 유지 하지 않습니다.*

중지 됨 컨테이너 그룹의 컨테이너는 재활용 합니다 [리소스](container-instances-container-groups.md#resource-allocation) 사용에 대 한 할당 된 상태로 유지 합니다. 따라서 청구를 중지 컨테이너 그룹에 대 한 계속합니다.

컨테이너 그룹에서 이미 종료 하는 경우 중지 작업은 효과가 없습니다 (성공 또는 실패 상태의 됩니다). 예를 들어, 성공 상태가 성공적으로 실행 하는 한 번 실행 컨테이너 작업을 사용 하 여 컨테이너 그룹을 종료 합니다. 상태는 상태를 변경 하지는 그룹을 중지 하려고 합니다. 

## <a name="start"></a>시작

컨테이너 그룹은 중지 하거나-그룹을 수동으로 중지 또는 컨테이너를 자체적으로 종료 하기 때문에 시작할 수 있습니다 컨테이너. 예를 들어 사용 합니다 [az 컨테이너 시작] [ az-container-start] 명령 또는 Azure portal의 그룹에서 컨테이너를 수동으로 시작 합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 시작하면 동일한 컨테이너 구성을 가진 새 배포가 시작됩니다. 이 작업은 예상대로 작동하는 알려진 컨테이너 그룹 구성을 빠르게 다시 사용하는 데 도움이 될 수 있습니다. 동일한 워크로드를 실행하기 위해 새 컨테이너 그룹을 만들 필요가 없습니다.

컨테이너 그룹의 모든 컨테이너는이 동작에 의해 시작 됩니다. 그룹에서 특정 컨테이너를 시작할 수 있습니다.

컨테이너 그룹을 수동으로 시작 또는 다시 시작한 후에 컨테이너 그룹은 구성된 다시 시작 정책에 따라 실행됩니다.
  
## <a name="restart"></a>다시 시작

실행 되는 동안-예를 들어 사용 하 여 컨테이너 그룹을 다시 시작할 수 있습니다 합니다 [az 컨테이너 다시 시작] [ az-container-restart] 명령입니다. 이 작업은 컨테이너 그룹의 모든 컨테이너를 다시 시작합니다. 컨테이너의 컨테이너 이미지가 업데이트되면 새 이미지가 풀됩니다. 

컨테이너 그룹을 다시 시작하면 배포 문제를 해결하려는 경우 도움이 됩니다. 예를 들어 임시 리소스 제한 사항으로 인해 컨테이너가 성공적으로 실행되지 않는 경우 그룹을 다시 시작하면 문제가 해결될 수 있습니다.

컨테이너 그룹의 모든 컨테이너는이 동작에 의해 다시 시작 됩니다. 그룹의 특정 컨테이너를 다시 시작할 수 없습니다. 있습니다.

컨테이너 그룹을 수동으로 다시 시작 하면 정책에 따라 구성 된 컨테이너 그룹 실행에 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 [다시 시작 정책 설정](container-instances-restart-policy.md) Azure Container Instances에서.

수동으로 중지 하 고 기존 구성을 사용 하 여 컨테이너 그룹을 시작 하는 것 외에도 수 있습니다 [설정을 업데이트](container-instances-update.md) 실행 중인 컨테이너 그룹의 합니다.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
