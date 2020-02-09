---
title: Azure Kubernetes Service와 Azure NetApp Files 통합
description: Azure Kubernetes Service와 Azure NetApp Files를 통합 하는 방법 알아보기
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 84192a831e3b1f24e20eb07a6c8695516c28970f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329332"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure Kubernetes Service와 Azure NetApp Files 통합

[Azure NetApp Files][anf] 은 Azure에서 실행 되는 엔터프라이즈급의 고성능의 요금제 파일 저장소 서비스입니다. 이 문서에서는 Azure Kubernetes 서비스 (AKS)와 Azure NetApp Files를 통합 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

> [!IMPORTANT]
> 또한 AKS 클러스터 [는 Azure NetApp Files를 지 원하는 지역에][anf-regions]있어야 합니다.

또한 Azure CLI 버전 2.0.59 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

### <a name="limitations"></a>제한 사항

Azure NetApp Files 사용 하는 경우 다음 제한 사항이 적용 됩니다.

* Azure NetApp Files는 [선택한 Azure 지역][anf-regions]에서만 사용할 수 있습니다.
* Azure NetApp Files를 사용 하려면 먼저 Azure NetApp Files 서비스에 대한 액세스 권한이 있어야 합니다. 액세스를 위해를 적용 하려면 [Azure NetApp Files waitlist 제출 양식을][anf-waitlist]사용할 수 있습니다. Azure NetApp Files 팀에서 공식 확인 전자 메일을 받을 때까지 Azure NetApp Files 서비스에 액세스할 수 없습니다.
* Azure NetApp Files 서비스는 AKS 클러스터와 동일한 가상 네트워크에 만들어야 합니다.
* Azure NetApp Files에 대한 정적 프로 비전은 AKS에서 지원 됩니다.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files 구성

> [!IMPORTANT]
> *Microsoft NetApp* 리소스 공급자를 등록 하려면 먼저 구독에 대한 [Azure NetApp Files waitlist 제출 양식을][anf-waitlist] 작성 해야 합니다. Azure NetApp Files 팀에서 공식 확인 전자 메일을 받을 때까지 리소스를 등록할 수 없습니다.

*Microsoft NetApp* 리소스 공급자를 등록 합니다.

```azure-cli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 이 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다.

AKS와 함께 사용할 Azure NetApp 계정을 만들 때 **노드** 리소스 그룹에서 계정을 만들어야 합니다. 먼저 [az aks show][az-aks-show] 명령을 사용 하 여 리소스 그룹 이름을 가져오고 `--query nodeResourceGroup` 쿼리 매개 변수를 추가 합니다. 다음 예제에서는 리소스 그룹 이름 *Myresourcegroup*에서 *myAKSCluster* 이라는 AKS 클러스터에 대한 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

[Az netappfiles account create][az-netappfiles-account-create]를 사용 하 여 **노드** 리소스 그룹에 Azure NetApp Files 계정을 만들고 AKS 클러스터와 동일한 영역을 만듭니다. 다음 예제에서는 *MC_myResourceGroup_myAKSCluster_eastus* 리소스 그룹 및 *에서는 eastus* 지역에 *myaccount1* 이라는 계정을 만듭니다.

```azure-cli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[Az netappfiles pool create][az-netappfiles-pool-create]를 사용 하 여 새 용량 풀을 만듭니다. 다음 예제에서는 크기가 4 TB이 고 *프리미엄* 서비스 수준이 *mypool1* 이라는 새 용량 풀을 만듭니다.

```azure-cli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[Az network vnet subnet create][az-network-vnet-subnet-create]를 사용 하 여 [Azure NetApp Files에 위임할][anf-delegate-subnet] 서브넷을 만듭니다. *이 서브넷은 AKS 클러스터와 동일한 가상 네트워크에 있어야 합니다.*

```azure-cli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

[Az netappfiles volume create][az-netappfiles-volume-create]를 사용 하 여 볼륨을 만듭니다.

```azure-cli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>PersistentVolume 만들기

[Az netappfiles volume show][az-netappfiles-volume-show] 를 사용 하 여 볼륨의 세부 정보 나열
```azure-cli
$ az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"

{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

PersistentVolume를 정의 하는 `pv-nfs.yaml`을 만듭니다. `path`을 이전 명령의 *ipAddress* 인 *creationToken* 및 `server`으로 바꿉니다. 예를 들어:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

*서버* 및 *경로* 를 이전 단계에서 만든 NFS (네트워크 파일 시스템) 볼륨의 값으로 업데이트 합니다. [Kubectl apply][kubectl-apply] 명령을 사용 하 여 PersistentVolume를 만듭니다.

```console
kubectl apply -f pv-nfs.yaml
```

[Kubectl 설명][kubectl-describe] 명령을 사용 하 여 PersistentVolume *상태* 를 *사용할 수* 있는지 확인 합니다.

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>PersistentVolumeClaim 만들기

PersistentVolume를 정의 하는 `pvc-nfs.yaml`을 만듭니다. 예를 들어:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 PersistentVolumeClaim를 만듭니다.

```console
kubectl apply -f pvc-nfs.yaml
```

[Kubectl 설명][kubectl-describe] 명령을 사용 하 여 PersistentVolumeClaim의 *상태가* *바인딩* 되었는지 확인 합니다.

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Pod를 사용 하 여 탑재

PersistentVolumeClaim를 사용 하는 pod를 정의 하는 `nginx-nfs.yaml`을 만듭니다. 예를 들어:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만듭니다.

```console
kubectl apply -f nginx-nfs.yaml
```

[Kubectl 설명][kubectl-describe] 명령을 사용 하 여 Pod가 *실행* 중인지 확인 합니다.

```console
kubectl describe pod nginx-nfs
```

[Kubectl exec][kubectl-exec] 를 사용 하 여 pod에 연결 하 여 해당 볼륨이 탑재 되었는지 확인 하 고 `df -h` 하 여 해당 볼륨이 탑재 되었는지 확인 합니다.

```console
$ kubectl exec -it nginx-nfs -- bash

root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>다음 단계

Azure NetApp Files에 대한 자세한 내용은 [Azure NetApp Files 항목][anf]을 참조 하세요. AKS에서 NFS를 사용 하는 방법에 대한 자세한 내용은 [AKS (Azure Kubernetes Service)를 사용 하 여 nfs (네트워크 파일 시스템) Linux 서버 볼륨 수동 만들기 및 사용 (영문)][aks-nfs]을 참조 하세요.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
