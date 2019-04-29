---
title: (사용되지 않음) 빠른 시작 - Linux용 Azure Docker CE 클러스터
description: Azure Container Service에서 Azure CLI를 사용하여 Linux 컨테이너용 Docker CE 클러스터를 빠르게 만드는 방법에 대해 알아봅니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: a7a7455ce9167a9c480d317d50fdce49e2ef06a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721802"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>(사용되지 않음) Docker CE 클러스터 배포

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

이 빠른 시작에서는 Azure CLI를 사용하여 Docker CE 클러스터가 배포됩니다. 웹 프런트 엔드 및 Redis 인스턴스로 구성된 다중 컨테이너 애플리케이션이 클러스터에 배포되어 실행됩니다. 완료되면 인터넷을 통해 애플리케이션에 액세스할 수 있습니다.

Azure Container Service에서 Docker CE는 미리 보기 상태이며 **프로덕션 워크로드에는 사용할 수 없습니다**.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다.

다음 예제에서는 *westus2* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

출력

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Docker Swarm 클러스터 만들기

[az acs create](/cli/azure/acs#az-acs-create) 명령을 사용하여 Azure Container Service에서 Docker CE 클러스터를 만듭니다. Docker CE의 사용 가능한 지역에 대한 자세한 내용은 [Docker CE의 ACS 지역](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)을 참조하세요.

다음 예제에서는 하나의 Linux 마스터 노드와 세 개의 Linux 에이전트 노드가 있는 *mySwarmCluster*라는 클러스터를 만듭니다.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

제한 평가판과 같이 Azure 구독의 Azure 리소스 액세스 권한이 제한되는 경우도 있습니다. 사용 가능한 코어 제한으로 인해 배포가 실패하는 경우 [az acs create](/cli/azure/acs#az-acs-create) 명령에 `--agent-count 1`을 추가하여 기본 에이전트 수를 줄이세요. 

몇 분 후 명령이 완료되고 클러스터에 대해 JSON 형식 정보가 반환됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

이 빠른 시작 전체에서 Docker Swarm 마스터와 Docker 에이전트 풀의 FQDN이 모두 필요합니다. 마스터 및 에이전트 FQDN을 모두 반환하려면 다음 명령을 실행합니다.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

출력

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Swarm 마스터에 대한 SSH 터널을 만듭니다. `MasterFQDN`을 Swarm 마스터의 FQDN 주소로 바꿉니다.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

`DOCKER_HOST` 환경 변수를 설정합니다. 이 옵션을 사용하면 호스트의 이름을 지정하지 않고 Docker Swarm에 대해 docker 명령을 실행할 수 있습니다.

```bash
export DOCKER_HOST=localhost:2374
```

이제 Docker Swarm에서 Docker 서비스를 실행할 준비가 되었습니다.


## <a name="run-the-application"></a>애플리케이션 실행

`azure-vote.yaml`이라는 파일을 만들고 다음 콘텐츠를 복사합니다.


```yaml
version: '3'
services:
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

[docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) 명령을 실행하여 Azure Vote 서비스를 만듭니다.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

출력

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

[docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) 명령을 사용하여 애플리케이션의 배포 상태를 반환합니다.

```bash
docker stack ps azure-vote
```

각 서비스의 `CURRENT STATE`가 `Running`이면 애플리케이션이 준비된 것입니다.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>애플리케이션 테스트

Azure Vote 애플리케이션을 테스트하려면 Swarm 에이전트 풀의 FQDN을 찾습니다.

![Azure Vote로 이동하는 이미지](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>클러스터 삭제
클러스터가 더 이상 필요하지 않으면 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>코드 가져오기

이 빠른 시작에서는 Docker 서비스를 만드는 데 미리 생성된 컨테이너 이미지를 사용했습니다. 관련된 애플리케이션 코드, Dockerfile 및 Compose 파일을 GitHub에서 사용할 수 있습니다.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Docker Swarm 클러스터를 배포하고, 이 클러스터에 다중 컨테이너 애플리케이션을 배포했습니다.

Docker Swarm을 Azure DevOps와 통합하는 방법에 대해 알아보려면 Docker Swarm 및 Azure DevOps를 사용하는 CI/CD를 진행합니다.

> [!div class="nextstepaction"]
> [Docker Swarm 및 Azure DevOps를 사용하는 CI/CD](./container-service-docker-swarm-setup-ci-cd.md)
