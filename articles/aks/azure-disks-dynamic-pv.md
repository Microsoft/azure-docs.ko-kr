---
title: AKS(Azure Kubernetes Service)에서 Azure 디스크를 사용하여 영구 볼륨을 동적으로 만들어 사용
description: Azure Kubernetes 서비스 (AKS)에서 Azure 디스크로 영구적 볼륨을 동적으로 만드는 방법에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1c7a406f0b06b94aaa6d8b4af63b1416b11c7c56
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847325"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure 디스크를 사용하여 영구 볼륨을 동적으로 만들어 사용

영구적 볼륨은 Kubernetes Pod와 함께 사용하기 위해 프로비전된 스토리지 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. 이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 한 Pod에 사용할 Azure 디스크가 포함된 영구 볼륨을 동적으로 만드는 방법을 설명합니다.

> [!NOTE]
> Azure 디스크는 *액세스 모드* 형식 *ReadWriteOnce*만 사용하여 탑재할 수 있으며, 이렇게 탑재한 디스크는 AKS의 한 Pod에서만 사용 가능합니다. 여러 pod에서 영구적 볼륨을 공유 해야 하는 경우 [Azure Files][azure-files-pvc]를 사용 합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 응용 프로그램에 대한 저장소 옵션][concepts-storage]을 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

## <a name="built-in-storage-classes"></a>기본 제공 스토리지 클래스

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 저장소 클래스에 대한 자세한 내용은 [Kubernetes 저장소 클래스][kubernetes-storage-classes]를 참조 하세요.

모든 AKS 클러스터에는 Azure 디스크에서 작동하도록 구성된 2개의 미리 만들어진 스토리지 클래스가 포함되어 있습니다.

* *default* 스토리지 클래스는 표준 Azure 디스크를 프로비전합니다.
    * 표준 스토리지는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 스토리지를 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.
* *managed-premium* 스토리지 클래스는 프리미엄 Azure 디스크를 프로비전합니다.
    * 프리미엄 디스크는 SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 프로덕션 워크로드를 실행하는 VM에 완벽한 디스크입니다. 클러스터의 AKS 노드가 Premium Storage를 사용하는 경우 *managed-premium* 클래스를 선택합니다.
    
이러한 기본 저장소 클래스를 사용 하면 만든 볼륨 크기를 업데이트할 수 없습니다. 이 기능을 사용 하도록 설정 하려면 기본 저장소 클래스 중 하나에 *allowVolumeExpansion: true* 줄을 추가 하거나 사용자 지정 저장소 클래스를 직접 만듭니다. `kubectl edit sc` 명령을 사용 하 여 기존 저장소 클래스를 편집할 수 있습니다. 저장소 클래스에 대한 자세한 내용 및 직접 만들기에 대한 자세한 내용은 [AKS의 응용 프로그램에 대한 저장소 옵션][storage-class-concepts]을 참조 하세요.

[Kubectl get sc][kubectl-get] 명령을 사용 하 여 미리 만든 저장소 클래스를 확인 합니다. 다음 예제에서는 AKS 클러스터 내에서 사용할 수 있는 미리 생성된 스토리지 클래스를 보여 줍니다.

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> 영구 볼륨 클레임은 GiB로 지정되지만 Azure 관리 디스크는 특정 크기에 대한 SKU로 청구됩니다. 이러한 Sku는 32GiB에서 S4 또는 P4 디스크의 범위를 S80 또는 P80 디스크 (미리 보기)에 32TiB 합니다. 프리미엄 관리 디스크의 처리량 및 IOPS 성능은 SKU 및 AKS 클러스터에서 노드의 인스턴스 크기에 따라 달라집니다. 자세한 내용은 [Managed Disks의 가격 및 성능][managed-disk-pricing-performance]을 참조 하세요.

## <a name="create-a-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

PVC(영구적 볼륨 클레임)을 사용하여 스토리지 클래스를 기반으로 하는 스토리지를 자동으로 프로비전합니다. 이 경우에 PVC는 미리 생성된 스토리지 클래스 중 하나를 사용하여 표준 또는 프리미엄 Azure 관리 디스크를 만들 수 있습니다.

파일 `azure-premium.yaml`을 만들고 다음 매니페스트에 복사합니다. 클레임은 `azure-managed-disk`ReadWriteOnce*액세스 권한이*5GB*크기의*이라는 디스크를 요구합니다. *managed-premium* 스토리지 클래스를 스토리지 클래스로 지정합니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> 표준 스토리지를 사용하는 디스크를 만들려면 `storageClassName: default`managed-premium*대신*를 사용합니다.

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 영구 볼륨 클레임을 만들고 *azure-premium. yaml* 파일을 지정 합니다.

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 디스크가 성공적으로 프로비전되면 디스크에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 *azure-managed-disk*라는 영구적 볼륨 클레임을 사용하여 `/mnt/azure` 경로에 Azure 디스크를 탑재하는 기본 NGINX Pod를 만듭니다. Windows Server 컨테이너 (현재 AKS의 미리 보기 상태)의 경우 *' d: '* 와 같은 windows 경로 규칙을 사용 하 여 *mountPath* 를 지정 합니다.

파일 `azure-pvc-disk.yaml`을 만들고 다음 매니페스트에 복사합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만듭니다.

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

이제 Azure 디스크가 `/mnt/azure` 디렉터리에 탑재된 Pod가 실행되고 있습니다. 다음 압축된 예제에 표시된 것처럼 `kubectl describe pod mypod`를 통해 Pod를 검사할 때 이 구성을 볼 수 있습니다.

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>영구적 볼륨 백업

영구적 볼륨에 데이터를 백업하려면 볼륨에 대한 관리 디스크의 스냅샷을 만듭니다. 그런 다음, 이 스냅샷을 사용하여 복원된 디스크를 만들고 데이터를 복원하는 수단으로 Pod에 연결할 수 있습니다.

먼저 `kubectl get pvc`azure-managed-disk*라는 PVC의 경우처럼*  명령을 사용하여 볼륨 이름을 가져옵니다.

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

이 볼륨 이름은 기본 Azure 디스크 이름을 형성합니다. 다음 예제와 같이 [az disk list][az-disk-list] 를 사용 하 여 디스크 ID를 쿼리하고 PVC 볼륨 이름을 제공 합니다.

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

[Az snapshot create][az-snapshot-create]를 사용 하 여 스냅숏 디스크를 만들려면 디스크 ID를 사용 합니다. 다음 예에서는 AKS 클러스터와 동일한 리소스 그룹에서 *pvcSnapshot*이라는 스냅샷을 만듭니다(*MC_myResourceGroup_myAKSCluster_eastus*). AKS 클러스터가 액세스 권한이 없는 리소스 그룹에서 스냅샷을 만들고 디스크를 복원하는 경우 사용 권한 문제가 발생할 수 있습니다.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

디스크의 데이터 양에 따라 스냅샷을 만드는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="restore-and-use-a-snapshot"></a>스냅샷 복원 및 사용

디스크를 복원 하 고 Kubernetes pod와 함께 사용 하려면 [az disk create][az-disk-create]를 사용 하 여 디스크를 만들 때 스냅숏을 원본으로 사용 합니다. 원래 데이터 스냅샷에 액세스해야 할 경우 이 작업은 원래 리소스를 보존합니다. 다음 예제에서는 *pvcSnapshot*이라는 스냅샷에서 *pvcRestored*라는 디스크를 만듭니다.

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Pod를 사용하여 복원된 디스크를 사용하려면 매니페스트에 디스크의 ID를 지정합니다. [Az disk show][az-disk-show] 명령을 사용 하 여 디스크 ID를 가져옵니다. 다음 예제에서는 이전 단계에서 만든 *pvcRestored*에 대한 디스크 ID를 가져옵니다.

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

`azure-restored.yaml`이라는 Pod 매니페스트를 만들고 이전 단계에서 가져온 디스크 URI를 지정합니다. 다음 예제에서는 */mnt/azure*에 복원된 디스크를 볼륨으로 탑재하여 기본 NGINX 웹 서버를 만듭니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만듭니다.

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

볼륨 정보를 보여주는 압축된 다음 예제와 같이 `kubectl describe pod mypodrestored`를 사용하여 Pod의 세부 정보를 볼 수 있습니다.

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS의 저장소 및 백업에 대한 모범 사례][operator-best-practices-storage]를 참조 하세요.

Azure 디스크를 사용하는 Kubernetes 영구적 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 디스크에 대한 Kubernetes 플러그 인][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
