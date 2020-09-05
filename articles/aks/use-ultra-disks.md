---
title: Azure Kubernetes 서비스에서 Ultra Disk 지원 사용 (AKS)
description: AKS (Azure Kubernetes Service) 클러스터에서 Ultra Disks를 사용 하도록 설정 하 고 구성 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 6ad739a128839eac4d664ffb6f9e3b2fcd07f2d9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650182"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Azure Kubernetes Service에서 Azure ultra disks 사용 (미리 보기)

[Azure ultra disks](../virtual-machines/disks-enable-ultra-ssd.md) 는 상태 저장 응용 프로그램에 대해 높은 처리량, 높은 IOPS 및 일관 된 짧은 대기 시간 디스크 저장소를 제공 합니다. 울트라 디스크의 주요 장점 중 하나는 에이전트 노드를 다시 시작할 필요 없이 워크 로드와 함께 SSD의 성능을 동적으로 변경 하는 기능입니다. Ultra disks는 데이터를 많이 사용 하는 워크 로드에 적합 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 기능은 클러스터를 만들거나 노드 풀을 만들 때만 설정할 수 있습니다.

> [!IMPORTANT]
> Azure ultra disks에는 특정 VM 시리즈 뿐만 아니라 해당 디스크를 지 원하는 가용성 영역 및 지역에 배포 된 nodepools 필요 합니다. [**Ultra DISKS GA 범위 및 제한 사항**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)을 참조 하세요.

### <a name="prerequisites"></a>전제 조건

- `EnableUltraSSD`기능 플래그를 사용 하도록 설정 했는지 확인 합니다.
- 최신 `aks-preview` [CLI 확장이][az-extension-add] 설치 되어 있는지 확인 합니다.

### <a name="register-the-enableultrassd-preview-feature"></a>`EnableUltraSSD`미리 보기 기능 등록

Ultra disks를 활용할 수 있는 AKS 클러스터 또는 노드 풀을 만들려면 `EnableUltraSSD` 구독에서 기능 플래그를 사용 하도록 설정 해야 합니다.

`EnableUltraSSD`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

AKS 클러스터 또는 Ultra Disks를 사용할 수 있는 노드 풀을 만들려면 최신 *AKS-preview* CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치 하거나 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>제한 사항
- [ **ULTRA disks GA 범위 및 제한 사항을** 참조 하세요.](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Ultra disks에 대해 지원 되는 크기 범위는 100에서 1500 사이입니다.

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Ultra disks를 사용할 수 있는 새 클러스터 만들기

다음 CLI 명령을 사용 하 여 Ultra Disks를 활용할 수 있는 AKS 클러스터를 만듭니다. 플래그를 사용 `--aks-custom-headers` 하 여 기능을 설정 `EnableUltraSSD` 합니다.

Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Kubernetes 권한 부여를 위해 관리 되는 Azure AD 통합 및 Azure RBAC를 사용 하 여 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ultra disk 지원 없이 클러스터를 만들려는 경우 사용자 지정 매개 변수를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` .

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>기존 클러스터에서 Ultra disks 사용

Ultra disks를 지 원하는 클러스터에 새 노드 풀을 추가 하 여 기존 클러스터에서 ultra disks를 사용 하도록 설정할 수 있습니다. 플래그를 사용 하 여 호스트 기반 암호화를 사용 하도록 새 노드 풀을 구성 `--aks-custom-headers` 합니다.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Ultra disks를 지원 하지 않고 새 노드 풀을 만들려는 경우 사용자 지정 매개 변수를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` .

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>저장소 클래스를 사용 하 여 동적으로 ultra disks 사용

배포 또는 상태 저장 집합에서 ultra disks를 사용 하려면 [동적 프로 비전을 위해 저장소 클래스](azure-disks-dynamic-pv.md)를 사용할 수 있습니다.

### <a name="create-the-storage-class"></a>저장소 클래스 만들기

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 스토리지 클래스에 대한 자세한 내용은 [Kubernetes 스토리지 클래스][kubernetes-storage-classes]를 참조하세요.

이 경우에는 울트라 디스크를 참조 하는 저장소 클래스를 만듭니다. 파일 `azure-ultra-disk-sc.yaml`을 만들고 다음 매니페스트에 복사합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 저장소 클래스를 만들고 *azure-* 파일을 지정 합니다.

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

PVC(영구적 볼륨 클레임)을 사용하여 스토리지 클래스를 기반으로 하는 스토리지를 자동으로 프로비전합니다. 이 경우에 PVC는 미리 생성된 스토리지 클래스 중 하나를 사용하여 표준 또는 프리미엄 Azure 관리 디스크를 만들 수 있습니다.

파일 `azure-ultra-disk-pvc.yaml`을 만들고 다음 매니페스트에 복사합니다. 클레임은 `ultra-disk` 액세스 *한 후 readwriteonce* 사용 하 여 크기가 *1000* 인 디스크를 요청 합니다. *Ultra disk-sc* 저장소 클래스는 저장소 클래스로 지정 됩니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 영구 볼륨 클레임을 만들고 *azure-ultra .yaml* 파일을 지정 합니다.

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 디스크가 성공적으로 프로비전되면 디스크에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 *울트라 디스크* 라는 영구 볼륨 클레임을 사용 하 여 경로에 Azure 디스크를 탑재 하는 기본 NGINX pod를 만듭니다 `/mnt/azure` .

파일 `nginx-ultra.yaml`을 만들고 다음 매니페스트에 복사합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: nginx
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
        claimName: ultra-disk
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

이제 Azure 디스크가 `/mnt/azure` 디렉터리에 탑재된 Pod가 실행되고 있습니다. 다음 압축된 예제에 표시된 것처럼 `kubectl describe pod nginx-ultra`를 통해 Pod를 검사할 때 이 구성을 볼 수 있습니다.

```console
$ kubectl describe pod nginx-ultra

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


## <a name="next-steps"></a>다음 단계

- 울트라 디스크에 대 한 자세한 내용은 [Azure ultra Disks 사용](../virtual-machines/disks-enable-ultra-ssd.md)을 참조 하세요.
- 저장소 모범 사례에 대 한 자세한 내용은 [Azure Kubernetes Service의 저장소 및 백업 모범 사례 (AKS)][operator-best-practices-storage] 를 참조 하세요.

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
[premium-storage]: ../virtual-machines/disks-types.md
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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
