---
title: 고객 관리 키를 사용 하 여 Azure Kubernetes 서비스에서 Azure 디스크 암호화 (AKS)
description: 사용자 고유의 키 (BYOK)를 가져와서 AKS OS 및 데이터 디스크를 암호화 합니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/12/2020
ms.author: mlearned
ms.openlocfilehash: 1852ba059d13a8886b739cac439798b4d031a531
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932630"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 Azure 디스크를 사용 하 여 사용자 고유의 키 (BYOK) 가져오기

Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대한 추가 제어를 위해 AKS 클러스터에 대한 OS 및 데이터 디스크 모두에 대한 미사용 암호화에 사용할 [고객 관리 키][customer-managed-keys] 를 제공할 수 있습니다.

> [!NOTE]
> BYOK Linux 및 Windows 기반 AKS 클러스터는 azure 관리 디스크의 서버 쪽 암호화를 지 원하는 [azure 지역][supported-regions] 에서 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

* 이 문서에서는 사용자가 *새 AKS 클러스터*를 만드는 것으로 가정 합니다.

* Key Vault를 사용 하 여 관리 디스크를 암호화할 때 *Azure Key Vault* 에 대해 일시 삭제 및 보호 제거를 사용 하도록 설정 해야 합니다.

* Azure CLI 버전 2.0.79 이상 및 aks-preview 0.4.26 확장이 필요 합니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="install-latest-aks-cli-preview-extension"></a>최신 AKS CLI 미리 보기 확장 설치

고객 관리 키를 사용 하려면 *aks-preview* CLI extension version 0.4.26 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치한 다음 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-azure-key-vault-instance"></a>Azure Key Vault 인스턴스 만들기

Azure Key Vault 인스턴스를 사용 하 여 키를 저장 합니다.  필요에 따라 Azure Portal를 사용 하 여 [고객이 관리 하는 키를로 구성할][byok-azure-portal] 수 있습니다 Azure Key Vault

새 *리소스 그룹*을 만든 다음 새 *Key Vault* 인스턴스를 만들고 일시 삭제 및 보호 제거를 사용 하도록 설정 합니다.  각 명령에 대해 동일한 지역 및 리소스 그룹 이름을 사용 해야 합니다.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>DiskEncryptionSet의 인스턴스를 만듭니다.

*MyKeyVaultName* 을 키 자격 증명 모음의 이름으로 바꿉니다.  또한 다음 단계를 완료 하기 위해 Azure Key Vault에 저장 된 *키* 가 필요 합니다.  이전 단계에서 만든 Key Vault에 기존 키를 저장 하거나 [새 키를 생성][key-vault-generate] 하 고 *mykeyname* 을 키 이름으로 바꿉니다.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>키 자격 증명 모음에 DiskEncryptionSet 액세스 권한 부여

이전 단계에서 만든 DiskEncryptionSet 및 리소스 그룹을 사용 하 여 Azure Key Vault에 대한 DiskEncryptionSet 리소스 액세스 권한을 부여 합니다.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get

# Assign the reader role
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>새 AKS 클러스터를 만들고 OS 디스크를 암호화 합니다.

**새 리소스 그룹** 및 AKS 클러스터를 만든 다음, 키를 사용 하 여 OS 디스크를 암호화 합니다. 고객 관리 키는 1.17 보다 큰 Kubernetes 버전 에서만 지원 됩니다. 

> [!IMPORTANT]
> AKS 클러스터에 대한 새 리소스 그룹을 만들어야 합니다.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az resource show -n mydiskEncryptionSetName -g myResourceGroup --resource-type "Microsoft.Compute/diskEncryptionSets" --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version 1.17.0 --generate-ssh-keys
```

위에서 만든 클러스터에 새 노드 풀을 추가 하면 create 중에 제공 되는 고객 관리 키를 사용 하 여 OS 디스크를 암호화 합니다.

## <a name="encrypt-your-aks-cluster-data-disk"></a>AKS 클러스터 데이터 디스크 암호화

사용자 고유의 키로 AKS 데이터 디스크를 암호화할 수도 있습니다.

> [!IMPORTANT]
> 적절 한 AKS 자격 증명이 있는지 확인 합니다. 서비스 주체에 게 diskencryptionset가 배포 되는 리소스 그룹에 대한 참가자 액세스 권한이 있어야 합니다. 그렇지 않으면 서비스 주체에 권한이 없다는 것을 제안 하는 오류 메시지가 표시 됩니다.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

다음 정보를 포함 하는 **byok-azure-yaml** 라는 파일을 만듭니다.  MyAzureSubscriptionId, myResourceGroup 및 myDiskEncrptionSetName를 사용자의 값으로 바꾸고 yaml를 적용 합니다.  DiskEncryptionSet가 배포 되는 리소스 그룹을 사용 해야 합니다.  Azure Cloud Shell 사용 하는 경우 가상 또는 물리적 시스템에서 작업 하는 것 처럼 vi 또는 nano를 사용 하 여이 파일을 만들 수 있습니다.

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
다음으로 AKS 클러스터에서이 배포를 실행 합니다.
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="limitations"></a>제한 사항

* BYOK는 현재 GA 및 특정 [Azure 지역의][supported-regions] 미리 보기 에서만 사용할 수 있습니다.
* Kubernetes 버전 1.17 이상에서 지원 되는 OS 디스크 암호화   
* BYOK가 지원 되는 지역 에서만 사용할 수 있습니다.
* 고객 관리 키를 사용 하는 암호화는 현재 새로운 AKS 클러스터에만 사용 되며 기존 클러스터는 업그레이드할 수 없습니다.
* Virtual Machine Scale Sets를 사용 하는 AKS 클러스터가 필요 하며, 가상 머신 가용성 집합에 대한 지원이 없습니다.


## <a name="next-steps"></a>다음 단계

[AKS 클러스터 보안에 대한 모범 사례][best-practices-security] 검토

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions
