---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 2ed74a4ba19af3a441bcf26a48890f033e6c365f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509865"
---
[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

[Azure Container Service 클러스터를 배포](../articles/container-service/dcos-swarm/container-service-deployment.md)한 후 에이전트 노드의 수를 변경해야 할 수 있습니다. 예를 들어 더 많은 컨테이너 애플리케이션 또는 인스턴스를 실행하기 위해 더 많은 에이전트가 필요할 수 있습니다. 

Azure Portal 또는 Azure CLI를 사용하여 DC/OS, Docker Swarm 또는 Kubernetes 클러스터의 에이전트 노드 수를 변경할 수 있습니다. 

## <a name="scale-with-the-azure-portal"></a>Azure Portal을 사용하여 규모 조정

1. [Azure Portal](https://portal.azure.com)에서 **컨테이너 서비스**를 찾아본 후 수정하고자 하는 컨테이너 서비스를 클릭합니다.
2. **컨테이너 서비스** 블레이드에서 **에이전트**를 클릭합니다.
3. **VM 개수**에서 원하는 에이전트 노드 수를 입력합니다.

    ![포털에서 풀 규모 조정](./media/container-service-scale/container-service-scale-portal.png)

4. 구성을 저장하려면 **저장**을 클릭합니다.

## <a name="scale-with-the-azure-cli"></a>Azure CLI를 사용하여 크기 조정

최신 Azure CLI를 [설치](/cli/azure/install-az-cli2)하고 Azure 계정(`az login`)에 로그인했는지 확인합니다.

### <a name="see-the-current-agent-count"></a>현재 에이전트 수 확인
현재 클러스터에 있는 에이전트의 개수를 확인하려면 `az acs show` 명령을 실행합니다. 이 명령은 클러스터 구성을 보여 줍니다. 예를 들어, 다음 명령은 리소스 그룹 `myResourceGroup`의 `containerservice-myACSName`이라는 컨테이너 서비스의 구성을 보여 줍니다.

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

이 명령은 `AgentPoolProfiles` 아래 `Count` 값에 에이전트 수를 반환합니다.

### <a name="use-the-az-acs-scale-command"></a>az acs scale 명령 사용
에이전트 노드 수를 변경하려면 `az acs scale` 명령을 실행하고 **리소스 그룹**, **컨테이너 서비스 이름** 및 원하는 **새 에이전트 수**를 입력합니다. 더 작은 숫자를 사용하여 규모를 축소하거나 더 큰 숫자를 사용하여 규모를 확장할 수 있습니다.

예를 들어, 이전 클러스터에서 에이전트 수를 10으로 변경하려면 다음 명령을 입력합니다.

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLI에서는 새 에이전트 수를 포함한 컨테이너 서비스의 새 구성을 나타내는 JSON 문자열을 반환합니다.

자세한 명령 옵션을 보려면 `az acs scale --help`를 실행합니다.

## <a name="scaling-considerations"></a>규모 조정 고려 사항

* 에이전트 노드 수는 1에서 100(포함) 사이여야 합니다. 

* 코어 할당량에 따라 클러스터의 에이전트 노드 수가 제한될 수 있습니다.

* 에이전트 노드 규모 조정 작업은 에이전트 풀을 포함하는 Azure 가상 머신 확장 집합에 적용됩니다. DC/OS 클러스터의 경우 이 문서에 나와 있는 작업으로 개인 풀의 에이전트 노드의 크기만 조정됩니다.

* 클러스터에서 배포하는 조정자에 따라 클러스터에서 실행되는 컨테이너의 인스턴스 수를 개별적으로 조정할 수 있습니다. 예를 들어, DC/OS 클러스터의 경우 [Marathon UI](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md)를 사용하여 컨테이너 애플리케이션의 인스턴스 수를 변경합니다.


## <a name="next-steps"></a>다음 단계
* Azure Container Service에서 Azure CLI 명령을 사용하는 방법에 대한 [추가 예제](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)를 참조하세요.
* Azure Container Service의 [DC/OS 에이전트 풀](../articles/container-service/dcos-swarm/container-service-dcos-agents.md)에 대해 자세히 알아보세요.

