---
title: Azure HDInsight의 Apache Kafka에 대한 Bring Your Own Key(미리 보기)
description: 이 문서에서는 Azure Key Vault에서 사용자 고유의 키를 사용하여 Azure HDInsight의 Apache Kafka에 저장된 데이터를 암호화하는 방법을 설명합니다.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ce9df58e9640cab2e6ba50fce772f1e30739dc5a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115451"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Kafka에 대 한 고유한 키 가져오기

Azure HDInsight에는 Apache Kafka에 대한 BYOK(Bring Your Own Key) 지원이 포함됩니다. 이 기능을 사용하면 미사용 데이터를 암호화하는 데 사용되는 키를 소유하고 관리할 수 있습니다. 

HDInsight의 모든 관리 디스크는 Azure SSE(저장소 서비스 암호화)로 보호됩니다. 기본적으로 해당 디스크의 데이터는 Microsoft 관리 키를 사용하여 암호화됩니다. BYOK를 사용하도록 설정한 경우 HDInsight에 대한 암호화 키를 제공하여 Azure Key Vault를 통해 사용하고 관리합니다. 

BYOK 암호화는 추가 비용 없이 클러스터를 만드는 동안 처리되는 1단계 프로세스입니다. Azure Key Vault를 통해 HDInsight를 관리 ID로 등록하고 클러스터를 만들 때 암호화 키를 추가하면 됩니다.

Kafka에 대한 모든 메시지(Kafka에서 유지 관리되는 복제본 포함)는 대칭 DEK(데이터 암호화 키)로 암호화됩니다. DEK는 Key Vault의 KEK(Key Encryption Key)를 사용하여 보호됩니다. 암호화 및 암호 해독 프로세스는 전적으로 Azure HDInsight에 의해 처리됩니다. 

Azure Portal 또는 Azure CLI를 사용하여 Key Vault의 키를 안전하게 회전할 수 있습니다. 키가 회전될 때 HDInsight Kafka 클러스터는 몇 분 안에 새 키를 사용하기 시작합니다. 랜 섬 웨어 시나리오 및 실수로 인 한 삭제를 방지 하려면 "일시 삭제" 키 보호 기능을 사용 하도록 설정 합니다. 이 보호 기능은 지원 되지 않습니다 하지 않고 키를 자격 증명 모음입니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>BYOK 시작
만들려는 BYOK를 사용 하도록 Kafka 클러스터 설정, 다음 단계를 거치게 됩니다.
1. Azure 리소스에 대 한 관리 되는 id 만들기
2. Azure Key Vault 및 키를 설정 합니다.
3. 사용 하도록 설정 하는 BYOK를 사용 하 여 HDInsight Kafka 클러스터 만들기
4. 암호화 키 순환

## <a name="create-managed-identities-for-azure-resources"></a>Azure 리소스에 대 한 관리 되는 id 만들기

   Key Vault에 인증을 사용 하 여 관리 되는 사용자 할당 id를 만듭니다는 [Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)합니다 [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), 또는 [ Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)합니다. Azure HDInsight에서 관리 되는 identities 작업에 대 한 자세한 내용은 참조 하세요. [Azure HDInsight에서 id 관리](../hdinsight-managed-identities.md)합니다. Kafka에 대한 관리 ID 및 BYOK에는 Azure Active Directory가 필요하지만 ESP(Encapsulating Security Payload)는 필요하지 않습니다. Key Vault 액세스 정책에 추가할 경우를 위해 관리 ID 리소스 ID를 저장해야 합니다.

   ![Azure Portal에서 사용자가 할당한 관리 ID 만들기](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Key Vault 및 키를 설정 합니다.

   HDInsight는 Azure Key Vault만 지원합니다. 고유한 Key Vault가 있는 경우 Azure Key Vault로 키를 가져올 수 있습니다. 키 "일시 삭제" 있어야 함을 기억 합니다. "일시 삭제" 기능은 REST,.NET을 통해 사용할 수 있는 /C#, PowerShell 및 Azure CLI 인터페이스.

   1. 새 Key Vault를 만들려면 [Azure Key Vault](../../key-vault/key-vault-overview.md) 빠른 시작을 수행합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](../../key-vault/about-keys-secrets-and-certificates.md)를 참조하세요.

   2. 사용 하 여 key vault에 "일시 삭제"를 사용 하도록 설정 합니다 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli 명령입니다.
        ' ' Azure CLI az keyvault update-이름 <Key Vault Name> -소프트 삭제 사용
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
