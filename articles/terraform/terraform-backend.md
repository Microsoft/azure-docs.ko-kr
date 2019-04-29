---
title: Azure Storage를 Terraform 백 엔드로 사용
description: Azure Storage에서의 Terraform 상태 저장을 소개합니다.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 7145a50bc53fd28afafd3de9c724b5e5f71624fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905893"
---
# <a name="store-terraform-state-in-azure-storage"></a>Terraform 상태를 Azure Storage에 저장

Terraform 상태는 Terraform 구성을 사용하여 배포된 리소스를 조정하는 데 사용됩니다. Terraform은 상태를 사용하여 추가, 업데이트 또는 삭제할 Azure 리소스를 파악합니다. 기본적으로 Terraform 상태는 *Terraform apply*를 실행할 때 로컬로 저장됩니다. 이 구성은 몇 가지 이유로 이상적이지 않습니다.

- 로컬 상태는 팀 또는 공동 작업 환경에서 잘 작동하지 않습니다.
- Terraform 상태는 중요한 정보를 포함할 수 있습니다.
- 상태를 로컬로 저장하면 실수로 삭제할 가능성이 높아집니다.

Terraform에는 Terraform 상태에 대한 원격 저장소인 상태 백 엔드의 개념이 포함됩니다. 상태 백 엔드를 사용하는 경우 상태 파일은 Azure Storage와 같은 데이터 저장소에 저장됩니다. 이 문서에서는 Azure Storage를 Terraform 상태 백 엔드로 구성 및 사용하는 방법을 자세히 설명합니다.

## <a name="configure-storage-account"></a>저장소 계정 구성

Azure Storage를 백 엔드로 사용하기 전에 스토리지 계정을 만들어야 합니다. 저장소 계정은 Azure Portal, PowerShell, Azure CLI 또는 자체 Terraform을 사용하여 만들 수 있습니다. Azure CLI를 사용하여 저장소 계정을 구성하려면 다음 샘플을 사용합니다.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

저장소 계정 이름, 컨테이너 이름 및 저장소 액세스 키를 적어둡니다. 이러한 값은 원격 상태를 구성할 때 필요합니다.

## <a name="configure-state-backend"></a>상태 백 엔드 구성

Terraform 상태 백 엔드는 *Terraform init*를 실행할 때 구성됩니다. 상태 백 엔드를 구성하려면 다음 데이터가 필요합니다.

- storage_account_name - Azure Storage 계정의 이름입니다.
- container_name - blob 컨테이너의 이름입니다.
- key - 만들 상태 저장소 파일의 이름입니다.
- access_key - 저장소 액세스 키입니다.

이러한 값은 Terraform 구성 파일 또는 명령 줄에서 각각 지정할 수 있지만, `access_key`에 대한 환경 변수를 사용하는 것이 좋습니다. 환경 변수를 사용하면 키가 디스크에 쓰이는 것을 방지할 수 있습니다.

Azure Storage 액세스 키의 값을 사용하여 `ARM_ACCESS_KEY`라는 환경 변수를 만듭니다.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Azure Storage 계정 액세스 키를 추가로 보호하려면 Azure Key Vault에 저장합니다. 그런 다음, 다음과 비슷한 명령을 사용하여 환경 변수를 설정할 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault 설명서][azure-key-vault]를 참조하세요.

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

백 엔드를 사용하도록 Terraform을 구성하려면 Terraform 구성 내에서 *azurerm* 형식의 *백 엔드* 구성을 포함합니다. *storage_account_name*, *container_name* 및 *key* 값을 구성 블록에 추가합니다.

다음 예제에서는 Terraform 백 엔드를 구성하고 Azure 리소스 그룹을 만듭니다. 해당 값을 사용자 환경의 값으로 바꿉니다.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

이제 *Terraform init*를 사용하여 구성을 초기화한 다음, *Terraform apply*를 사용하여 구성을 실행합니다. 완료되면 Azure Storage Blob에서 상태 파일을 찾을 수 있습니다.

## <a name="state-locking"></a>상태 잠금

상태 스토리지에 대한 Azure Storage Blob을 사용하는 경우 blob은 상태를 기록하는 모든 작업 전에 자동으로 잠겨 있습니다. 이 구성은 손상을 일으킬 수 있는 여러 동시 상태 작업을 방지합니다. 자세한 내용은 Terraform 설명서의 [상태 잠금][terraform-state-lock]을 참조하세요.

Azure Portal 또는 기타 Azure 관리 도구를 통해 blob을 검토할 때는 잠겨져 있습니다.

![잠금을 사용한 Azure blob](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

기본적으로 Azure Blob에 저장된 데이터는 저장소 인프라에 영구 저장되기 전에 암호화됩니다. Terraform에 상태가 필요한 경우 백 엔드에서 검색하고 개발 시스템에서 메모리에 저장합니다. 이 구성에서 상태는 Azure Storage에서 보호되며, 로컬 디스크에 작성되지 않습니다.

Azure Storage 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화][azure-storage-encryption]를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Terraform 백 엔드 설명서][terraform-backend]에서 Terraform 백 엔드 구성에 대해 자세히 알아보세요.

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
