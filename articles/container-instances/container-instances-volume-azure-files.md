---
title: Azure Container Instances에서 Azure Files 볼륨 탑재
description: Azure Container Instances를 사용하여 상태가 유지되도록 Azure Files 볼륨을 탑재하는 방법에 대해 알아봅니다.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564024"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances에서 Azure 파일 공유 탑재

Azure Container Instances는 기본적으로 상태 비저장 방식으로 작동합니다. 컨테이너의 작동이 중단되거나 중지되면 모든 상태가 손실됩니다. 컨테이너 수명이 지난 후에도 상태를 유지하려면 외부 저장소에서 볼륨을 탑재해야 합니다. 이 문서에서는 Azure Container Instances에서 사용하기 위해 [Azure Files](../storage/files/storage-files-introduction.md)를 사용하여 만든 Azure 파일 공유를 탑재하는 방법을 보여줍니다. Azure Files는 산업 표준 SMB(서버 메시지 블록) 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure Container Instances에서 Azure 파일 공유를 사용하면 Azure Virtual Machines에서 Azure 파일 공유를 사용하는 것과 유사한 파일 공유 기능을 제공합니다.

> [!NOTE]
> 현재 Azure 파일 공유를 탑재하는 작업은 Linux 컨테이너로만 제한되어 있습니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure Container Instances에서 Azure 파일 공유를 사용하려면 먼저 파일 공유를 만들어야 합니다. 다음 스크립트를 실행하여 파일 공유를 호스팅할 저장소 계정과 파일 공유 자체를 만듭니다. Storage 계정 이름은 전역적으로 고유해야 하므로 이 스크립트는 기준 문자열에 임의 값을 추가합니다.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>저장소 자격 증명 가져오기

Azure Container Instances의 볼륨으로 Azure 파일 공유를 탑재하려면 Storage 계정 이름, 공유 이름, Storage 액세스 키의 세 가지 값이 필요합니다.

위의 스크립트를 사용한 경우 스토리지 계정 이름은 $ACI_PERS_STORAGE_ACCOUNT_NAME 변수에 저장되었습니다. 계정 이름을 확인하려면 다음을 입력하세요.

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

공유 이름은 이미 알려져 있으므로(위 스크립트에서 *acishare*로 정의됨) 저장소 계정 키만 확인하면 됩니다. 이 키는 다음 명령을 사용하여 확인할 수 있습니다.

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>컨테이너 및 탑재 볼륨 배포

Azure 파일 공유를 컨테이너의 볼륨으로 탑재하려면 [az container create][az-container-create]를 사용하여 컨테이너를 만들 때 공유 및 볼륨 탑재 지점을 지정합니다. 이전 단계를 수행한 경우 다음 명령으로 이전에 만든 공유를 탑재하여 컨테이너를 만들 수 있습니다.

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label` 값은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 명령을 실행한 결과 **DNS 이름 레이블** 오류 메시지가 표시되는 경우에는 이전 명령의 값을 업데이트합니다.

## <a name="manage-files-in-mounted-volume"></a>탑재된 볼륨의 파일 관리

컨테이너가 시작 되 면 Microsoft를 통해 배포 된 간단한 웹 앱은 사용할 수 있습니다 [aci hellofiles] [ aci-hellofiles] 이미지 지정한 탑재 경로에서 Azure 파일 공유에 작은 텍스트 파일을 만듭니다. 아래와 같이 [az container show][az-container-show] 명령을 사용하여 웹앱의 FQDN(정규화된 도메인 이름)을 확인합니다.

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

[Azure Portal][portal] 또는 [Microsoft Azure Storage 탐색기][storage-explorer]와 같은 도구를 사용하여 파일 공유에 작성된 파일을 검색하고 검사할 수 있습니다.

## <a name="mount-multiple-volumes"></a>여러 볼륨 탑재

컨테이너 인스턴스에서 여러 볼륨을 탑재하려면 [Azure Resource Manager 템플릿](/azure/templates/microsoft.containerinstance/containergroups) 또는 YAML 파일을 사용하여 배포해야 합니다.

템플릿을 사용하려면 먼저 공유 세부 정보를 제공하고 템플릿의 `properties` 섹션에서 `volumes` 배열을 채워서 볼륨을 정의합니다. 예를 들어, 스토리지 계정 *myStorageAccount*에 *share1* 및 *share2*라는 두 개의 Azure 파일 공유를 만든 경우 `volumes` 배열이 다음과 비슷하게 표시됩니다.

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

다음으로 볼륨을 탑재하려는 컨테이너 그룹에 있는 각 컨테이너의 경우 컨테이너 정의의 `properties` 섹션에서 `volumeMounts` 배열을 채웁니다. 예를 들어 다음은 이전에 정의한 *myvolume1* 및 *myvolume2*라는 두 개의 볼륨을 탑재합니다.

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Azure Resource Manager 템플릿을 사용하는 컨테이너 인스턴스 배포의 예제를 보려면 [컨테이너 그룹 배포](container-instances-multi-container-group.md)를 참조하세요. YAML 파일을 사용하는 예제는 [YAML을 사용하여 다중 컨테이너 그룹 배포](container-instances-multi-container-yaml.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Container Instances에서 다른 볼륨 유형을 탑재하는 방법을 알아봅니다.

* [Azure Container Instances에서 emptyDir 볼륨 탑재](container-instances-volume-emptydir.md)
* [Azure Container Instances에서 gitRepo 볼륨 탑재](container-instances-volume-gitrepo.md)
* [Azure Container Instances에서 비밀 볼륨 탑재](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show