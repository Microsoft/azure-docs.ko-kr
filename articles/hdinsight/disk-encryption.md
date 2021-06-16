---
title: 미사용 데이터에 대한 이중 암호화
titleSuffix: Azure HDInsight
description: 이 문서에서는 Azure HDInsight 클러스터의 미사용 데이터에 사용할 수 있는 두 가지 암호화 계층에 대해 설명합니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6ada7a8c09491416224f40852a761c55a652f4ac
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110702324"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>미사용 데이터에 대한 Azure HDInsight 이중 암호화

이 문서에서는 Azure HDInsight 클러스터에서 미사용 데이터를 암호화하는 방법을 설명합니다. 미사용 데이터 암호화는 HDInsight 클러스터 가상 머신에 연결된 관리 디스크(데이터 디스크, OS 디스크 및 임시 디스크)의 암호화를 나타냅니다. 

이 문서는 Azure Storage 계정에 저장된 데이터를 처리하지 않습니다. 클러스터에는 암호화 키가 Microsoft에서 관리하거나 고객이 관리할 수 있는 하나 이상의 연결된 Azure Storage 계정이 있을 수 있지만 암호화 서비스는 다릅니다. Azure Storage 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

## <a name="introduction"></a>소개

Azure에는 데이터 디스크, OS 디스크 및 임시 디스크라는 3가지 주요 관리 디스크 역할이 있습니다. 여러 유형의 관리 디스크에 대한 자세한 내용은 [Azure 관리 디스크 소개](../virtual-machines/managed-disks-overview.md)를 참조하세요. 

HDInsight는 두 가지 계층에서 여러 유형의 암호화를 지원합니다.

- SSE(서버 쪽 암호화) - SSE는 스토리지 서비스에 의해 수행됩니다. HDInsight에서 SSE는 OS 디스크 및 데이터 디스크를 암호화하는 데 사용됩니다. 기본적으로 설정됩니다. SSE는 계층 1 암호화 서비스입니다.
- 플랫폼 관리형 키를 사용하는 호스트에서 암호화 - SSE와 유사하게 이 유형의 암호화는 스토리지 서비스에 의해 수행됩니다. 그러나 임시 디스크용으로만 사용되며 기본적으로 사용하도록 설정되어 있지 않습니다. 호스트의 암호화도 계층 1 암호화 서비스입니다.
- 고객 관리형 키를 사용하여 미사용 암호화 - 이 유형의 암호화는 데이터 및 임시 디스크에서 사용할 수 있습니다. 기본적으로 사용하도록 설정되어 있지 않으며 고객은 Azure Key Vault를 통해 자신의 키를 제공해야 합니다. 미사용 암호화는 계층 2 암호화 서비스입니다.

이러한 유형은 다음 표에 요약되어 있습니다.

|클러스터 유형 |OS 디스크(관리 디스크) |데이터 디스크(관리 디스크) |임시 데이터 디스크(로컬 SSD) |
|---|---|---|---|
|Kafka, 가속화된 쓰기를 사용하는 HBase|계층1: 기본적으로 [SSE 암호화](../virtual-machines/managed-disks-overview.md#encryption)|계층1: 기본적으로 [SSE 암호화](../virtual-machines/managed-disks-overview.md#encryption), 계층2: CMK를 사용한 선택적 미사용 암호화|계층1: PMK를 사용한 선택적 호스트에서 암호화, 계층2: CMK를 사용한 선택적 미사용 암호화|
|다른 모든 클러스터(Spark, Interactive, Hadoop, 가속화된 쓰기를 사용하지 않는 HBase)|계층1: 기본적으로 [SSE 암호화](../virtual-machines/managed-disks-overview.md#encryption)|해당 없음|계층1: PMK를 사용한 선택적 호스트에서 암호화, 계층2: CMK를 사용한 선택적 미사용 암호화|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>고객 관리형 키를 사용하여 미사용 데이터 암호화

고객 관리형 키 암호화는 추가 비용 없이 클러스터를 만드는 동안 처리되는 1단계 프로세스입니다. Azure Key Vault를 통해 관리 ID에 권한을 부여하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

클러스터의 각 노드에 있는 데이터 디스크와 임시 디스크는 모두 대칭 DEK(데이터 암호화 키)를 사용하여 암호화됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다.

클러스터 VM에 연결된 OS 디스크의 경우 단 하나의 암호화 계층(PMK)만 사용할 수 있습니다. 시나리오에 CMK 암호화가 필요한 경우 고객은 중요한 데이터를 OS 디스크로 복사하지 않는 것이 좋습니다.

Key Vault 방화벽이 디스크 암호화 키가 저장된 Key Vault에서 사용되는 경우 클러스터를 배포할 지역에 대한 HDInsight 지역별 리소스 공급자 IP 주소를 Key Vault 방화벽 구성에 추가해야 합니다. HDInsight는 신뢰할 수 있는 Azure Key Vault 서비스가 아니기 때문에 필요합니다.

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키가 회전될 때 HDInsight 클러스터는 몇 분 안에 새 키를 사용하기 시작합니다. 랜섬웨어 시나리오 및 실수로 인한 삭제를 방지하려면 [일시 삭제](../key-vault/general/soft-delete-overview.md) 키 보호 기능을 사용하도록 설정합니다. 이 보호 기능이 없는 키 자격 증명 모음은 지원되지 않습니다.

### <a name="get-started-with-customer-managed-keys"></a>고객 관리 키 사용 시작

고객 관리형 키가 사용하도록 설정된 HDInsight 클러스터를 만들려면 다음 단계를 진행합니다.

1. Azure 리소스에 대한 관리 ID 만들기
1. Azure Key Vault 생성
1. 키 만들기
1. 액세스 정책 만들기
1. 고객 관리형 키를 사용하는 HDInsight 클러스터 만들기
1. 암호화 키 회전

각 단계는 다음 섹션 중 하나에 자세히 설명되어 있습니다.

### <a name="create-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID 만들기

사용자 할당 관리 ID를 만들어 Key Vault에 인증합니다.

특정 단계에 대한 [사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 참조하세요. Azure HDInsight에서 관리 ID가 작동하는 방식에 대한 자세한 내용은 [Azure hdinsight에서 관리 ID](hdinsight-managed-identities.md)를 참조하세요. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

### <a name="create-azure-key-vault"></a>Azure Key Vault 생성

키 자격 증명 모음을 만듭니다. 특정 단계는 [Azure Key Vault 만들기](../key-vault/general/quick-create-portal.md)를 참조하세요.

HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. Key Vault에서 **일시 삭제** 를 사용하도록 설정해야 합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../key-vault/general/about-keys-secrets-certificates.md)를 참조하세요.

### <a name="create-key"></a>키 만들기

1. 새 Key Vault에서 **설정** > **키** >  **+ 생성/가져오기** 로 이동합니다.

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Azure Key Vault에 새 키 생성":::

1. 이름을 제공한 다음, **만들기** 를 선택합니다. **RSA** 의 기본 **키 유형** 을 유지합니다.

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="키 이름을 생성합니다.":::

1. **키** 페이지로 돌아가면 만든 키를 선택합니다.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="Key Vault 키 목록":::

1. 버전을 선택하여 **키 버전** 페이지를 엽니다. HDInsight 클러스터 암호화를 위해 고유한 키를 사용하는 경우 키 URI를 제공해야 합니다. **키 식별자** 를 복사하고 클러스터를 만들 준비가 될 때까지 어딘가에 저장합니다.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="키 식별자 가져오기":::

### <a name="create-access-policy"></a>액세스 정책 만들기

1. 새 Key Vault에서 **설정** > **액세스 정책** >  **+ 액세스 정책 추가** 로 이동합니다.

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="새 Azure Key Vault 액세스 정책 만들기":::

1. **액세스 정책 추가** 페이지에서 다음 정보를 제공합니다.

    |속성 |Description|
    |---|---|
    |키 권한|**가져오기**, **키 래핑 해제** 및 **키 래핑** 을 선택합니다.|
    |비밀 권한|**가져오기**, **설정** 및 **삭제** 를 선택합니다.|
    |보안 주체 선택|앞서 만든 사용자가 할당한 관리 ID를 선택합니다.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Azure Key Vault 액세스 정책에 대한 주체 선택 설정":::

1. **추가** 를 선택합니다.

1. **저장** 을 선택합니다.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Azure Key Vault 액세스 정책 저장":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>고객 관리형 키 디스크 암호화를 사용하여 클러스터 만들기

이제 HDInsight 클러스터를 만들 준비가 되었습니다. 고객 관리형 키는 클러스터를 만드는 동안 새 클러스터에만 적용할 수 있습니다. 암호화는 고객 관리형 키 클러스터에서 제거될 수 없으며 고객 관리형 키는 기존 클러스터에 추가될 수 없습니다.

2020년 11월 릴리스부터 HDInsight는 버전이 지정된 키 URI와 버전이 없는 키 URI를 모두 사용하여 클러스터를 만들 수 있도록 지원합니다. 버전 없는 키 URI를 사용하여 클러스터를 만드는 경우 HDInsight 클러스터는 Azure Key Vault에서 키가 업데이트될 때 키 자동 회전을 수행하려고 합니다. 버전이 지정된 키 URI를 사용하여 클러스터를 만드는 경우 [암호화 키 회전](#rotating-the-encryption-key)에 설명된 대로 수동 키 회전을 수행해야 합니다.

2020년 11월 릴리스 이전에 만든 클러스터의 경우 버전 키 URI를 사용하여 수동으로 키 회전을 수행해야 합니다.

#### <a name="using-the-azure-portal"></a>Azure Portal 사용

클러스터를 만드는 동안 다음과 같은 방법으로 버전이 지정된 키를 사용하거나 버전이 없는 키를 사용할 수 있습니다.

- **버전 지정됨** - 클러스터를 만드는 동안 키 버전을 포함한 전체 **키 식별자** 를 제공합니다. 예: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **버전 없음** - 클러스터를 만드는 동안 **키 식별자** 만 제공합니다. 예: `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

또한 클러스터에 관리 ID를 할당해야 합니다.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="새 클러스터 만들기":::

#### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예에서는 Azure CLI를 사용하여 디스크 암호화를 사용하는 새 Apache Spark 클러스터를 만드는 방법을 보여 줍니다. 자세한 내용은 [Azure CLI az hdinsight create](/cli/azure/hdinsight#az_hdinsight_create)를 참조하세요. `encryption-key-version` 매개 변수는 선택 사항입니다.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

다음 예에서는 Azure Resource Manager 템플릿을 사용하여 디스크 암호화가 사용하도록 설정된 새 Apache Spark 클러스터를 만드는 방법을 보여 줍니다. 자세한 내용은 [ARM 템플릿이란?](../azure-resource-manager/templates/overview.md)을 참조하세요. Resource Manager 템플릿 속성 `diskEncryptionKeyVersion`은 선택 사항입니다.

이 예에서는 PowerShell을 사용하여 템플릿을 호출합니다.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

리소스 관리 템플릿 `azuredeploy.json`의 콘텐츠:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>암호화 키 회전

Azure Portal 또는 Azure CLI를 사용하여 실행 중인 클러스터에서 사용되는 암호화 키를 변경할 수 있습니다. 이 작업의 경우 클러스터는 현재 키와 의도한 새 키 모두에 대한 액세스 권한이 있어야 합니다. 그렇지 않으면 키 회전 작업이 실패합니다. 2020년 11월 릴리스 이에 만든 클러스터의 경우 새 키에 버전을 포함시킬 것인지 여부를 선택할 수 있습니다. 2020년 11월 릴리스 이전에 만든 클러스터의 경우 암호화 키를 회전할 때는 버전이 지정된 키를 사용해야만 합니다.

#### <a name="using-the-azure-portal"></a>Azure Portal 사용

키를 회전하려면 기본 Key Vault URI가 필요합니다. 이 작업을 완료한 후에는 포털에서 HDInsight 클러스터 속성 섹션으로 이동하고 **디스크 암호화 키 URL** 에서 **키 변경** 을 클릭합니다. 새 키 URL을 입력하고 제출하여 키를 회전합니다.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="디스크 암호화 키 회전":::

#### <a name="using-azure-cli"></a>Azure CLI 사용

다음 예에서는 기존 HDInsight 클러스터에 대한 디스크 암호화 키를 회전하는 방법을 보여 줍니다. 자세한 내용은 [Azure CLI az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az_hdinsight_rotate_disk_encryption_key)를 참조하세요.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>고객 관리형 키 암호화에 대한 FAQ

**HDInsight 클러스터가 내 Key Vault에 어떻게 액세스하나요?**

HDInsight는 HDInsight 클러스터와 연결하는 관리 ID를 사용하여 Azure Key Vault 인스턴스에 액세스합니다. 클러스터를 만들기 전이나 만드는 동안 이 관리 ID를 만들 수 있습니다. 또한 키가 저장된 Key Vault에 대한 액세스 권한을 관리 ID에 부여해야 합니다.

**HDInsight의 모든 클러스터에 이 기능을 사용할 수 있나요?**

고객 관리형 키 암호화는 Spark 2.1 및 2.2를 제외한 모든 클러스터 유형에 사용할 수 있습니다.

**여러 키를 사용하여 여러 디스크 또는 폴더를 암호화할 수 있나요?**

아니요, 모든 관리 디스크와 리소스 디스크는 동일한 키로 암호화됩니다.

**클러스터가 Key Vault 또는 키에 대한 액세스 권한을 상실하면 어떻게 되나요?**

클러스터가 키에 대한 액세스 권한을 상실하면 Apache Ambari 포털에 경고가 표시됩니다. 이 상태에서 **키 변경** 작업은 실패합니다. 키 액세스가 복원되면 Ambari 경고는 더 이상 표시되지 않고 키 회전과 같은 작업은 성공적으로 수행될 수 있습니다.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="키 액세스 Ambari 경고":::

**키가 삭제될 경우 클러스터를 어떻게 복구할 수 있나요?**

“일시 삭제”를 사용하는 키만 지원되므로, 키가 Key Vault에서 복원될 경우 클러스터는 키에 대한 액세스 권한을 다시 얻어야 합니다. Azure Key Vault 키를 복구하려면 [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) 또는 [az-keyvault-key-recover](/cli/azure/keyvault/key#az_keyvault_key_recover)를 참조하세요.


**클러스터가 스케일 업된 경우, 새로운 노드가 고객 관리형 키를 원활하게 지원하나요?**

예. 클러스터는 강화하는 동안 Key Vault의 키에 대한 액세스 권한이 있어야 합니다. 클러스터의 관리 디스크와 리소스 디스크를 모두 암호화하는 데 동일한 키가 사용됩니다.

**내 위치에서 고객 관리형 키를 사용할 수 있나요?**

HDInsight 고객 관리형 키는 모든 퍼블릭 클라우드와 국가별 클라우드에서 사용할 수 있습니다.

## <a name="encryption-at-host-using-platform-managed-keys"></a>플랫폼 관리형 키를 사용하여 호스트에서 암호화

### <a name="enable-in-the-azure-portal"></a>Azure Portal에서 사용

Azure Portal에서 클러스터를 만드는 동안 호스트에서 암호화를 사용하도록 설정할 수 있습니다.

> [!Note]
> 호스트에서 암호화를 사용하는 경우 Azure Marketplace에서 HDInsight 클러스터에 애플리케이션을 추가할 수 없습니다.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="호스트에서 암호화를 사용하도록 설정합니다.":::

이 옵션은 PMK를 사용하는 HDInsight VM 임시 데이터 디스크에 대해 [호스트에서 암호화](../virtual-machines/disks-enable-host-based-encryption-portal.md)를 사용하도록 설정합니다. 호스트에서 암호화는 [제한된 지역의 특정 VM SKU에서만 지원](../virtual-machines/disks-enable-host-based-encryption-portal.md)되며 HDInsight는 [다음 노드 구성 및 SKU](./hdinsight-supported-node-configuration.md)를 지원합니다.

HDInsight 클러스터에 적합한 VM 크기를 이해하려면 [Azure HDInsight 클러스터에 적합한 VM 크기 선택](hdinsight-selecting-vm-size.md)을 참조하세요. 호스트에서 암호화가 사용하도록 설정된 경우에는 Zookeeper 노드의 기본 VM SKU가 DS2V2입니다.

### <a name="enable-using-powershell"></a>PowerShell을 사용하여 사용

다음 코드 조각에서는 PowerShell을 사용하여 호스트에서 암호화가 설정된 새 Azure HDInsight 클러스터를 만드는 방법을 보여 줍니다. `-EncryptionAtHost $true` 매개 변수를 사용하여 기능을 사용하도록 설정합니다.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Azure CLI 사용

다음 코드 조각에서는 Azure CLI를 사용하여 호스트에서 암호화가 설정된 새 Azure HDInsight 클러스터를 만들 수 있는 방법을 보여 줍니다. `--encryption-at-host true` 매개 변수를 사용하여 기능을 사용하도록 설정합니다.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>다음 단계

* Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/general/overview.md)을 참조하세요.
* [Azure HDInsight의 엔터프라이즈 보안 개요](./domain-joined/hdinsight-security-overview.md)
