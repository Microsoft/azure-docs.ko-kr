---
title: AKS(Azure Kubernetes Service)에서 Pod용 정적 볼륨 만들기
description: AKS(Azure Kubernetes Service)에서 Pod에 사용할 Azure 디스크가 포함된 볼륨을 수동으로 만드는 방법에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 9017c8cf721fbb9c493dc18da769b9d6e83ddf05
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616130"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure 디스크가 포함된 볼륨을 수동으로 만들어 사용

컨테이너 기반 애플리케이션은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Pod 하나가 스토리지에 액세스해야 하는 경우 Azure 디스크를 사용하여 애플리케이션에서 사용하도록 할 기본 볼륨을 제공할 수 있습니다. 이 문서에서는 Azure 디스크를 수동으로 만들어 AKS의 Pod에 연결하는 방법을 설명합니다.

> [!NOTE]
> Azure 디스크는 한 번에 한 Pod에만 탑재할 수 있습니다. 여러 pod에서 영구적 볼륨을 공유 해야 하는 경우 [Azure Files][azure-files-volume]를 사용 합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 응용 프로그램에 대한 저장소 옵션][concepts-storage]을 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

## <a name="create-an-azure-disk"></a>Azure 디스크 만들기

AKS에서 사용할 Azure 디스크를 만들 때는 **노드** 리소스 그룹에 디스크 리소스를 만들 수 있습니다. 이러한 방식을 사용하면 AKS 클러스터가 디스크 리소스에 액세스하고 해당 리소스를 관리할 수 있습니다. 이 방식 대신 별도의 리소스 그룹에 디스크를 만들어야 하는 경우에는 클러스터의 AKS(Azure Kubernetes Service) 서비스 주체에 디스크 리소스 그룹에 대한 `Contributor` 역할을 부여해야 합니다.

이 문서에서는 노드 리소스 그룹에 디스크를 만듭니다. 먼저 [az aks show][az-aks-show] 명령을 사용 하 여 리소스 그룹 이름을 가져오고 쿼리 매개 변수를 `--query nodeResourceGroup` 추가 합니다. 다음 예제는 *myResourceGroup* 리소스 그룹에서 AKS 클러스터 *myAKSCluster*의 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

이제 [az disk create][az-disk-create] 명령을 사용 하 여 디스크를 만듭니다. 이전 명령에서 가져온 노드 리소스 그룹 이름을 지정한 다음 디스크 리소스의 이름을 *myAKSDisk*와 같이 지정합니다. 다음 예에서는 *20 개의*GiB 디스크를 만들고 생성 된 디스크의 ID를 출력 합니다. Windows Server 컨테이너에 사용할 디스크를 만들어야 하는 경우 (현재 AKS에서 미리 보기 상태), 디스크를 올바르게 `--os-type windows` 포맷 하는 매개 변수를 추가 합니다.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure 디스크는 특정 크기가 되면 SKU를 기준으로 요금이 청구됩니다. 이러한 Sku는 32GiB에서 S4 또는 P4 디스크의 범위를 S80 또는 P80 디스크 (미리 보기)에 32TiB 합니다. 프리미엄 관리 디스크의 처리량 및 IOPS 성능은 SKU 및 AKS 클러스터에서 노드의 인스턴스 크기에 따라 달라집니다. [Managed Disks의 가격 및 성능][managed-disk-pricing-performance]을 참조 하세요.

다음 예제 출력에 나와 있는 것처럼 명령이 정상적으로 완료되면 디스크 리소스 ID가 표시됩니다. 이 디스크 ID를 사용하여 다음 단계에서 디스크를 탑재합니다.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>볼륨으로 디스크 탑재

Pod에 Azure 디스크를 탑재하려면 컨테이너 사양에서 볼륨을 구성합니다. 다음 내용이 포함된 새 파일 `azure-disk-pod.yaml`을 만듭니다. `diskName`은 이전 단계에서 만든 디스크의 이름으로, `diskURI`는 disk create 명령의 출력에 표시된 디스크 ID로 업데이트합니다. 원하는 경우 Pod에서 Azure 디스크가 탑재되는 경로인 `mountPath`를 업데이트합니다. Windows Server 컨테이너 (현재 AKS의 미리 보기 상태)의 경우 *' d: '* 와 같은 windows 경로 규칙을 사용 하 여 *mountPath* 를 지정 합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

`kubectl` 명령을 사용하여 Pod을 작성합니다.

```console
kubectl apply -f azure-disk-pod.yaml
```

이제 Azure 디스크가 `/mnt/azure`에 탑재된 Pod가 실행됩니다. `kubectl describe pod mypod`를 사용하여 디스크가 제대로 탑재되었는지 확인할 수 있습니다. 압축된 다음 예제 출력에서는 컨테이너에 탑재된 볼륨을 보여 줍니다.

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS의 저장소 및 백업에 대한 모범 사례][operator-best-practices-storage]를 참조 하세요.

Azure 디스크와 상호 작용 하는 AKS 클러스터에 대한 자세한 내용은 [Azure 디스크용 Kubernetes 플러그 인][kubernetes-disks]을 참조 하세요.

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
