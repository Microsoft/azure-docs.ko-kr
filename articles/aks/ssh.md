---
title: AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH 연결 만들기
description: 문제 해결 및 유지 관리 작업은 AKS(Azure Kubernetes Service) 클러스터 노드를 사용하여 SSH 연결을 만드는 방법을 알아봅니다.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/05/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: 680e087e80d3e9891e201e7cb474ccfcf7fcc70b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031661"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>유지 관리 또는 문제 해결을 위해 AKS(Azure Kubernetes Service) 클러스터 노드에 대한 SSH와 연결

AKS(Azure Kubernetes Service) 클러스터의 수명 주기 내내 AKS 노드에 액세스해야 합니다. 유지 관리, 로그 수집 또는 기타 문제 해결 작업을 위해 이 액세스를 사용할 수 있습니다. SSH를 사용하여 액세스할 수 있도록 AKS 노드는 Linux VM입니다. 보안을 위해 AKS 노드는 인터넷에 노출되지 않습니다.

이 아티클에서는 사설 IP 주소를 사용하여 AKS 노드와 SSH를 연결하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure cli 버전 2.0.59 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="add-your-public-ssh-key"></a>공용 SSH 키 추가

기본적으로 AKS 클러스터를 만들 때 SSH 키가 생성됩니다. AKS 클러스터를 만들 때 고유한 SSH 키를 지정하지 않은 경우 AKS 노드에 공용 SSH 키를 추가합니다.

SSH 키를 AKS 노드에 추가하려면 다음 단계를 완료합니다.

1. [az aks show][az-aks-show]를 사용하여 AKS 클러스터 리소스에 대한 리소스 그룹 이름을 가져옵니다. 고유한 핵심 리소스 그룹 및 AKS 클러스터 이름을 입력합니다.

    ```azurecli
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. [az vm list][az-vm-list] 명령을 사용하여 AKS 클러스터 리소스 그룹에서 VM을 나열합니다. 이러한 VM은 AKS 노드입니다.

    ```azurecli
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_chinaeast -o table
    ```

    다음 예제 출력에서는 AKS 노드를 보여줍니다.

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_chinaeast  chinaeast
    ```

1. SSH 키를 노드에 추가하려면 [az vm user update][az-vm-user-update] 명령을 사용합니다. 이전 단계에서 가져온 리소스 그룹 이름 및 AKS 노드 중 하나를 차례로 입력합니다. AKS 노드에 대한 사용자 이름은 기본적으로 *azureuser*입니다. *~/.ssh/id_rsa.pub*와 같은 고유한 SSH 공용 키 위치를 입력하거나 SSH 공개 키의 내용을 붙여넣습니다.

    ```azurecli
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>AKS 노드 주소 가져오기

AKS 노드는 공개적으로 인터넷에 노출되지 않습니다. AKS 노드에 SSH하려면 사설 IP 주소를 사용합니다. 다음 단계에서 만든 도우미 pod 수 있는 AKS 클러스터의 SSH 노드의이 개인 IP 주소.

[az vm list-ip-addresses][az-vm-list-ip-addresses] 명령을 사용하여 AKS 클러스터 노드의 사설 IP 주소를 확인합니다. 이전 [az-aks-show][az-aks-show] 단계에서 가져온 고유한 AKS 클러스터 리소스 그룹 이름을 입력합니다.

```azurecli
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_chinaeast -o table
```

다음 예제 출력에서는 AKS 노드의 사설 IP 주소를 보여줍니다.

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>SSH 연결 만들기

AKS 노드에 SSH를 연결하려면 AKS 클러스터에서 도우미 Pod를 실행합니다. 이 도우미 Pod는 클러스터에 대한 SSH 액세스 및 추가 SSH 노드 액세스를 제공합니다. 이 도우미 Pod를 만들고 사용하려면 다음 단계를 수행합니다.

1. `debian` 컨테이너 이미지를 실행하고 여기에 터미널 세션을 연결합니다. 이 컨테이너를 사용하여 AKS 클러스터에서 노드가 있는 SSH 세션을 만들 수 있습니다.

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. 기본 Debian 이미지에는 SSH 구성 요소가 포함되지 않습니다. 터미널 세션이 컨테이너에 연결되면 다음과 같이 `apt-get`을 사용하여 SSH 클라이언트를 설치합니다.

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. 컨테이너에 연결되지 않은 새 터미널 창에서 [kubectl get pods][kubectl-get] 명령을 사용하여 AKS 클러스터의 Pod를 나열합니다. 이전 단계에서 만든 Pod는 다음 예제와 같이 *aks-ssh* 이름으로 시작합니다.

    ```
    $ kubectl get pods

    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. 이 아티클의 첫 번째 단계에서는 AKS 노드에 공용 SSH 키를 추가했습니다. 이제 Pod에 개인 SSH 키를 복사합니다. 이 개인 키를 사용하여 AKS 노드에 SSH를 만듭니다.

    이전 단계에서 가져온 고유한 *aks-ssh* Pod 이름을 입력합니다. 필요에 따라 *~/.ssh/id_rsa*를 개인 SSH 키의 위치로 변경합니다.

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. 컨테이너에 대한 터미널 세션으로 다시 돌아가서 사용자 읽기 전용이 되도록 복사된 `id_rsa` 개인 SSH 키에 대한 사용 권한을 업데이트합니다.

    ```console
    chmod 0600 id_rsa
    ```

1. 이제 AKS 노드에 대한 SSH 연결을 만듭니다. AKS 노드에 대한 기본 사용자 이름은 *azureuser*입니다. SSH 키를 먼저 신뢰할 수 있다면 계속 연결하기 위한 프롬프트를 수락합니다. 그런 다음, AKS 노드의 bash 프롬프트와 함께 표시됩니다.

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH 액세스 제거

작업이 완료되면 SSH 세션을 `exit`한 다음, 대화형 컨테이너 세션을 `exit`합니다. 이 컨테이너 세션을 닫을 때 AKS 클러스터에서 SSH 액세스에 사용되는 Pod가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

추가 문제 해결 데이터가 필요하면 [kubelet 로그를 확인][view-kubelet-logs]하거나 [Kubernetes 마스터 노드 로그를 확인][view-master-logs]하면 됩니다.

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[az-vm-list]: https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-list
[az-vm-user-update]: https://docs.azure.cn/zh-cn/cli/vm/user?view=azure-cli-latest#az-vm-user-update
[az-vm-list-ip-addresses]: https://docs.azure.cn/zh-cn/cli/vm?view=azure-cli-latest#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest