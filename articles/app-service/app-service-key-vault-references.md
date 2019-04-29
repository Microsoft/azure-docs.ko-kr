---
title: Key Vault 참조 - Azure App Service | Microsoft Docs
description: Azure App Service 및 Azure Functions의 Azure Key Vault 참조에 대한 개념적 참조 및 설정 가이드
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 662260c3cf37f8f8a675c522f3d3dea41153e485
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853147"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>App Service 및 Azure Functions의 Key Vault 참조 사용(미리 보기)

> [!NOTE] 
> Key Vault 참조는 현재 미리 보기로 제공됩니다.

이 항목에서는 코드 변경 없이도 App Service 또는 Azure Functions 애플리케이션에서 Azure Key Vault의 비밀을 사용하는 방법을 보여 줍니다. [Azure Key Vault](../key-vault/key-vault-overview.md)는 액세스 정책 및 감사 기록에 대한 전체 제어와 함께 중앙 집중식 비밀 관리를 제공하는 서비스입니다.

## <a name="granting-your-app-access-to-key-vault"></a>Key Vault에 앱 액세스 권한 부여

Key Vault에서 비밀을 읽으려면 자격 증명 모음을 만들고 해당 자격 증명에 앱 권한을 부여해야 합니다.

1. [Key Vault 빠른 시작](../key-vault/quick-create-cli.md)에 따라 키 자격 증명 모음을 만듭니다.

1. 애플리케이션에 대한 [시스템 할당 관리형 ID](overview-managed-identity.md)를 만듭니다.

   > [!NOTE] 
   > Key Vault 참조는 현재 시스템 할당 관리형 ID만 지원합니다. 사용자 할당 ID를 사용할 수 없습니다.

1. 이전에 만든 애플리케이션 ID에 대한 [Key Vault에서 액세스 정책](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies)을 만듭니다. 이 정책에 대한 “가져오기” 비밀 권한을 사용하도록 설정합니다. "권한 있는 애플리케이션" 또는 `appliationId` 설정은 관리 ID와 호환되지 않으므로 구성하지 마세요.

## <a name="reference-syntax"></a>참조 구문

Key Vault 참조는 `@Microsoft.KeyVault({referenceString})` 형식이며, 여기서 `{referenceString}`은 다음 옵션 중 하나로 대체됩니다.

> [!div class="mx-tdBreakAll"]
> | 참조 문자열                                                            | 설명                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri**는 버전을 포함하여 Key Vault에 있는 비밀의 전체 데이터 평면 URI여야 합니다(예: https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931).  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName**은 Key Vault 리소스의 이름이어야 합니다. **SecretName**은 대상 비밀의 이름이어야 합니다. **SecretVersion**은 사용할 비밀의 버전이어야 합니다. |

> [!NOTE] 
> 현재 미리 보기에서 버전은 필수입니다. 비밀을 회전할 때 애플리케이션 구성에서 버전을 업데이트해야 합니다.

예를 들어 전체 참조는 다음과 같습니다.

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

또는 다음과 같습니다.

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Key Vault의 원본 애플리케이션 설정

Key Vault 참조를 [애플리케이션 설정](web-sites-configure.md#app-settings) 값으로 사용할 수 있어 사이트 구성 대신 Key Vault에서 비밀을 유지할 수 있습니다. 애플리케이션 설정은 안전하게 암호화되어 있지만 비밀 관리 기능이 필요한 경우 Key Vault로 이동해야 합니다.

애플리케이션 설정에 Key Vault 참조를 사용하려면 참조를 설정 값으로 설정합니다. 앱은 키를 사용하여 비밀을 정상적으로 참조할 수 있습니다. 코드 변경은 필요하지 않습니다.

> [!TIP]
> Key Vault 참조를 사용하는 대부분의 애플리케이션 설정은 각 환경에 대해 별도의 자격 증명 모음이 있어야 슬롯 설정으로 표시되어야 합니다.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager 템플릿을 통해 리소스 배포를 자동화할 때 이 기능이 작동하려면 종속성을 특정 순서로 시퀀스해야 할 수 있습니다. 중요한 것은, 사이트 정의에서 `siteConfig` 속성을 사용하는 대신 애플리케이션 설정을 자체 리소스로 정의해야 합니다. 이는 사이트를 먼저 정의해야 사이트를 사용하여 시스템 할당 ID를 만들고 액세스 정책에서 사용할 수 있기 때문입니다.

함수 앱에 대한 예제 의사 템플릿은 다음과 같습니다.

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 이 예제에서 원본 제어 배포는 애플리케이션 설정에 따라 다릅니다. 일반적으로 앱 설정 업데이트는 비동기적으로 동작하므로 안전하지 않은 동작입니다. 그러나 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 애플리케이션 설정을 포함했으므로 업데이트가 동기적입니다. 이는 애플리케이션 설정이 완전히 업데이트된 후에만 원본 제어 배포가 시작됨을 의미합니다.
