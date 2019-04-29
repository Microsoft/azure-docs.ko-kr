---
title: Managed Applications를 통한 Azure Key Vault 사용 | Microsoft Docs
description: Managed Applications를 배포하는 경우 Azure Key Vault에서 액세스 비밀 사용하는 방법 보여주기
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 55410250ccd4dfceac8ac9ae5b81d4736de0d91a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588297"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Azure Managed Applications를 배포할 때 Key Vault 비밀 액세스

배포 중에 보안 값(예: 암호)을 매개 변수로 전달해야 할 경우 [Azure Key Vault](../key-vault/key-vault-whatis.md)에서 값을 검색할 수 있습니다. Managed Applications를 배포할 때 Key Vault에 액세스하려면 **어플라이언스 리소스 공급자** 서비스 주체에 액세스를 허용해야 합니다. Managed Applications 서비스는 이 ID를 사용하여 작업을 실행합니다. 배포하는 동안 Key Vault에서 값을 검색하려면 서비스 주체가 Key Vault에 액세스할 수 있어야 합니다.

이 문서에서는 Managed Applications로 작업하려면 Key Vault를 구성하는 방법을 설명합니다.

## <a name="enable-template-deployment"></a>템플릿 배포 사용하도록 설정

1. 포털에서 Key Vault를 선택합니다.

1. **액세스 정책**을 선택합니다.   

   ![액세스 정책 선택](./media/key-vault-access/select-access-policies.png)

1. **클릭하여 고급 액세스 정책 표시**를 선택합니다.

   ![고급 액세스 정책 표시](./media/key-vault-access/advanced.png)

1. **템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 사용**을 선택합니다. 그런 다음 **저장**을 선택합니다.

   ![템플릿 배포 사용하도록 설정](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>서비스를 기여자로 추가

1. **액세스 제어(IAM)** 를 선택합니다.

   ![액세스 제어 선택](./media/key-vault-access/access-control.png)

1. **역할 할당 추가**를 선택합니다.

   ![추가 선택](./media/key-vault-access/add-access-control.png)

1. 역할에 대해 **기여자**를 선택합니다. **어플라이언스 리소스 공급자**를 검색하고 사용 가능한 옵션 중에서 선택합니다.

   ![공급자 검색](./media/key-vault-access/search-provider.png)

1. **저장**을 선택합니다.

## <a name="reference-key-vault-secret"></a>Key Vault 비밀 참조

비밀을 Key Vault에서 Managed Application의 템플릿에 전달하려면 [연결된 템플릿](../azure-resource-manager/resource-group-linked-templates.md)을 사용하고 연결된 템플릿에 대한 매개 변수에서 Key Vault를 참조해야 합니다. Key Vault의 리소스 ID 및 비밀의 이름을 제공합니다.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>다음 단계

Managed Application을 배포하는 동안 액세스할 수 있도록 Key Vault를 구성했습니다.

* Key Vault에서 값을 템플릿 매개 변수로 전달하는 방법에 대한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/resource-manager-keyvault-parameter.md)을 참조합니다.
* 관리되는 애플리케이션 예제는 [Azure 관리되는 애플리케이션의 샘플 프로젝트](sample-projects.md)를 참조하세요.
* 관리형 애플리케이션에 대한 UI 정의 파일을 만드는 방법은 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.