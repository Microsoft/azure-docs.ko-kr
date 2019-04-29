---
title: Azure에서 Linux VM의 디스크 암호화 | Microsoft Docs
description: Azure CLI를 사용하여 보안을 강화하기 위해 Linux VM에서 가상 디스크를 암호화하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731620"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Azure에서 Linux 가상 머신을 암호화하는 방법

VM(가상 머신)의 보안과 규정 준수 상태를 향상시키기 위해 가상 디스크 및 VM 자체를 암호화할 수 있습니다. VM은 Azure Key Vault에 안전하게 보관되는 암호화 키를 사용하여 암호화됩니다. 이러한 암호화 키를 제어하고 용도를 감사할 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 Linux VM에서 가상 디스크를 암호화하는 방법을 자세히 설명합니다. 

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="overview-of-disk-encryption"></a>디스크 암호화 개요
Linux VM의 가상 디스크는 미사용 시 [dm-crypt](https://wikipedia.org/wiki/Dm-crypt)를 사용하여 암호화됩니다. Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 

VM을 암호화하는 프로세스는 다음과 같습니다.

1. Azure Key Vault에서 암호화 키를 만듭니다.
1. 암호화하는 디스크에 사용될 수 있도록 암호화 키를 구성합니다.
1. 가상 디스크에 대한 디스크 암호화를 사용합니다.
1. Azure Key Vault에서 필요한 암호화 키를 요청합니다.
1. 가상 디스크는 제공된 암호화 키를 사용하여 암호화됩니다.


## <a name="requirements-and-limitations"></a>요구 사항 및 제한 사항
디스크 암호화에 대해 지원되는 시나리오 및 요구 사항은 다음과 같습니다.

* 다음 Linux Server SKU - Ubuntu, CentOS, SUSE 및 SLES(SUSE Linux Enterprise Server)와 Red Hat Enterprise Linux.
* 모든 리소스(예: Key Vault, Storage 계정, VM)는 동일한 Azure 지역 및 구독 내에 있어야 합니다.
* 표준 A, D, DS, G, GS 등, 시리즈 VM.
* 이미 암호화된 Linux VM에서 암호화 키 업데이트.

다음 시나리오의 경우 디스크 암호화가 현재 지원되지 않습니다.

* 기본 계층 VM.
* 클래식 배포 모델을 사용하여 만든 VM.
* Linux VM에서 OS 디스크 암호화 비활성화.
* 사용자 지정 Linux 이미지 사용.

지원되는 시나리오 및 제한 사항에 대한 자세한 내용은 [IaaS VM에 대한 Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.


## <a name="create-an-azure-key-vault-and-keys"></a>Azure Key Vault 및 키 만들기
다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myKey*, *myVM*이 포함됩니다.

첫 번째 단계는 암호화 키를 저장할 Azure Key Vault를 만드는 것입니다. Azure Key Vault는 애플리케이션 및 서비스에 안전하게 구현할 수 있는 키와 암호를 저장할 수 있습니다. 가상 디스크 암호화의 경우 Key Vault를 사용하여 가상 디스크 암호화 또는 암호 해독에 사용되는 암호화 키를 저장합니다.

Azure 구독 내에서 [az provider register](/cli/azure/provider#az-provider-register)를 사용하여 Azure Key Vault 공급자를 사용하도록 설정하고 [az group create](/cli/azure/group#az-group-create)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `eastus` 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

암호화 키를 포함하는 Azure Key Vault와 스토리지 및 VM과 같은 연결된 계산 리소스는 동일한 지역에 상주해야 합니다. [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 사용하여 Azure Key Vault을 만들고 디스크 암호화에 사용할 Key Vault를 사용하도록 설정합니다. *keyvault_name*에 대한 고유한 Key Vault 이름을 다음과 같이 지정합니다.

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

소프트웨어 또는 HSM(하드웨어 보안 모델) 보호를 사용하여 암호화 키를 저장할 수 있습니다. HSM을 사용하려면 프리미엄 Key Vault가 필요합니다. 소프트웨어 보호 키를 저장하는 표준 Key Vault가 아닌 프리미엄 Key Vault를 만들려면 추가 비용이 소요됩니다. 프리미엄 Key Vault를 만들려면 앞의 단계에서 `--sku Premium`을 명령에 추가합니다. 표준 Key Vault를 만들었기 때문에 다음 예제는 소프트웨어 보호 키를 사용합니다.

두 가지 보호 모델 모두, 가상 디스크의 암호를 해독하기 위해 VM이 부팅될 때 암호화 키를 요청하려면 Azure 플랫폼에 액세스 권한이 허용되어야 합니다. [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)를 사용하여 Key Vault에 암호화 키를 만듭니다. 다음 예제는 *myKey*라는 키를 만듭니다.

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>가상 머신 만들기
[az vm create](/cli/azure/vm#az-vm-create)를 사용하여 VM을 만들고 5GB 데이터 디스크를 연결합니다. 특정 Marketplace 이미지만 디스크 암호화를 지원합니다. 다음 예제는 *Ubuntu 16.04 LTS* 이미지를 사용하여 *myVM*이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

위 명령의 출력에 표시된 *publicIpAddress*를 사용하여 VM에 대해 SSH를 수행합니다. 파티션 및 파일 시스템을 만든 후 데이터 디스크를 탑재합니다. 자세한 내용은 [Linux VM에 연결하여 새 디스크 탑재](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk)를 참조하세요. SSH 세션을 닫습니다.


## <a name="encrypt-the-virtual-machine"></a>가상 머신 암호화


[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable)을 사용하여 VM을 암호화합니다.

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

디스크 암호화 프로세스를 완료하는 데 약간의 시간이 걸립니다. [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show)를 사용하여 프로세스의 상태를 모니터링합니다.

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

완료되면 출력은 다음 예제와 유사합니다.

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>데이터 디스크 더 추가하기
데이터 디스크를 암호화하고 나면 VM에 가상 디스크를 더 추가하고 암호화할 수 있습니다. 

데이터 디스크가 VM에 추가되면 다음과 같이 명령을 다시 실행하여 가상 디스크를 암호화합니다.

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>다음 단계
* 암호화 키 및 Key Vault 삭제를 비롯한 Azure Key Vault 관리에 대한 자세한 내용은 [CLI를 사용하여 Key Vault 관리](../../key-vault/key-vault-manage-with-cli2.md)를 참조하세요.
* Azure에 업로드할 암호화된 사용자 지정 VM 준비와 같은 디스크 암호화에 대한 자세한 내용은 [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)을 참조하세요.
