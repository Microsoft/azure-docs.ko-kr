---
title: 미사용 Azure CLI 관리 디스크에서 이중 암호화 사용
description: Azure CLI를 사용 하 여 관리 디스크 데이터에 대해 미사용 이중 암호화를 사용 하도록 설정 합니다.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 76e5ab49e354faac085be533fed1ff16c0315a5e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816833"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Azure CLI를 사용 하 여 관리 디스크에 대 한 미사용 암호화를 사용 하도록 설정 합니다.

Azure 디스크 저장소는 관리 디스크에 대 한 미사용 암호화를 지원 합니다. 휴지 상태의 이중 암호화 및 기타 관리 되는 디스크 암호화 유형에 대 한 개념 정보는 디스크 암호화 문서의 [휴지 상태의 이중 암호화](disk-encryption.md#double-encryption-at-rest) 섹션을 참조 하세요.

## <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>사전 요구 사항

최신 [Azure CLI](/cli/azure/install-az-cli2) 를 설치 하 고 [az login](/cli/azure/reference-index)을 사용 하 여 Azure 계정에 로그인 합니다.

## <a name="getting-started"></a>시작하기

1. Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만드는 경우 일시 삭제 및 제거 보호를 사용하도록 설정해야 합니다. 일시 삭제를 사용하면 지정된 보존 기간(기본적으로 90일) 동안 Key Vault에 삭제된 키를 보관하게 됩니다. 제거 보호를 사용하면 보존 기간이 지날 때까지 삭제된 키를 영구 삭제할 수 없습니다. 이러한 설정은 실수로 삭제하여 데이터가 손실되지 않도록 보호합니다. 이러한 설정은 관리 디스크를 암호화하기 위해 Key Vault를 사용하는 경우 필수입니다.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    EncryptionType를 EncryptionAtRestWithPlatformAndCustomerKeys로 설정 하 여 DiskEncryptionSet를 만듭니다. Azure Resource Manager (ARM) 템플릿에서 API 버전 **2020-05-01** 을 사용 합니다. 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Key Vault에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여합니다. 

        > [!NOTE]
        > Azure가 Azure Active Directory에서 DiskEncryptionSet의 ID를 만드는 데는 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다"와 같은 오류가 발생하면 몇 분 정도 기다린 후 다시 시도하세요.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>다음 단계

이러한 리소스를 만들고 구성 했으므로 이제 이러한 리소스를 사용 하 여 관리 디스크를 보호할 수 있습니다. 다음 링크에는 관리 디스크를 보호 하는 데 사용할 수 있는 각각의 시나리오를 포함 하는 예제 스크립트가 포함 되어 있습니다.

- [Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [서버 쪽 암호화를 사용 하 여 고객이 관리 하는 키 사용-예](disks-enable-customer-managed-keys-cli.md#examples)