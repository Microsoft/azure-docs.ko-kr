---
title: Azure Resource Manager 템플릿을 사용 하 여 Azure 데이터 탐색기에서 고객이 관리 하는 키 구성
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure 데이터 탐색기의 데이터에서 고객이 관리 하는 키 암호화를 구성 하는 방법을 설명 합니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d0f7085342f972f227fc549c423672296697d7de
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281260"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키 구성

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 구성

이 섹션에서는 Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키를 구성 합니다. 기본적으로 Azure 데이터 탐색기 암호화는 Microsoft 관리 키를 사용 합니다. 이 단계에서는 Azure 데이터 탐색기 클러스터가 고객이 관리 하는 키를 사용 하도록 구성 하 고 클러스터와 연결할 키를 지정 합니다.

Azure Portal 또는 PowerShell을 사용 하 여 Azure Resource Manager 템플릿을 배포할 수 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>키 버전 업데이트

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 클러스터를 업데이트 해야 합니다. 먼저 `Get-AzKeyVaultKey`를 호출 하 여 최신 버전의 키를 가져옵니다. 그런 다음 [고객이 관리 하는 키로 암호화 구성](#configure-encryption-with-customer-managed-keys)에 표시 된 것 처럼 새 버전의 키를 사용 하도록 클러스터의 키 자격 증명 모음 속성을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보호](security.md)
* [Azure 데이터 탐색기 클러스터에 대한 관리 id 구성](managed-identities.md)
* 미사용 암호화를 사용 하도록 설정 하 여 [Azure 데이터 탐색기-Azure Portal에서 클러스터를 보호](manage-cluster-security.md) 합니다.
* [을 사용 하 여 고객이 관리 하는 키 구성C#](customer-managed-keys-csharp.md)

