---
title: Azure Key Vault에 자격 증명 저장
description: Azure Data Factory에서 런타임 시 자동으로 검색할 수 있는 Azure Key Vault에 사용되는 데이터 저장소의 자격 증명을 저장하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 1418205843fefc76db4e73832736b308d0cc79a3
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122613"
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault에 자격 증명 저장

[Azure Key Vault](../key-vault/key-vault-overview.md)에 데이터 저장소 및 컴퓨터의 자격 증명을 저장할 수 있습니다. Azure Data Factory에서는 데이터 저장소/계산을 사용하는 작업을 실행할 때 자격 증명을 검색합니다.

현재, 사용자 지정 활동을 제외한 모든 활동 유형에서 이 기능이 지원됩니다. 구체적인 커넥터 구성에 대해서는 [각 커넥터 항목](copy-activity-overview.md#supported-data-stores-and-formats)의 "연결된 서비스 속성" 섹션을 확인하세요.

## <a name="prerequisites"></a>필수 조건

이 기능은 데이터 팩터리 관리 id에 의존 합니다. [데이터 팩터리에 대한 관리 id](data-factory-service-identity.md) 에서 작동 하는 방식에 대해 알아보고 데이터 팩터리에 연결 된 id가 있는지 확인 합니다.

## <a name="steps"></a>단계

Azure Key Vault에 저장된 자격 증명을 참조하려면 다음을 수행해야 합니다.

1. 팩터리와 함께 생성 된 "관리 Id 개체 ID"의 값을 복사 하 여 **데이터 팩터리 관리 id를 검색** 합니다. ADF 제작 UI를 사용 하는 경우 관리 id 개체 ID는 Azure Key Vault 연결 된 서비스 만들기 창에 표시 됩니다. Azure Portal에서 검색할 수도 있습니다. [data factory 관리 Id 검색](data-factory-service-identity.md#retrieve-managed-identity)을 참조 하세요.
2. **관리 id에 Azure Key Vault에 대한 액세스 권한을 부여 합니다.** 키 자격 증명 모음에서 액세스 정책 >-> 추가->이 관리 되는 id를 검색 하 여 비밀 권한 드롭다운에서 **Get** 권한을 부여 합니다. 그러면 이 지정된 팩터리가 키 자격 증명 모음에 있는 비밀에 액세스할 수 있습니다.
3. **Azure Key Vault를 가리키는 연결된 서비스를 만듭니다.** [Azure Key Vault 연결된 서비스](#azure-key-vault-linked-service)를 참조합니다.
4. **해당 비밀을 키 자격 증명 모음에 저장한 참조 내에서 데이터 저장소 연결된 서비스를 만듭니다.** [키 자격 증명 모음에 저장된 참조 비밀](#reference-secret-stored-in-key-vault)을 참조하세요.

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 연결된 서비스

Azure Key Vault 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureKeyVault**로 설정되어야 합니다. | 예 |
| baseUrl | Azure Key Vault URL을 지정합니다. | 예 |

**제작 UI 사용:**

**연결** -> **연결된 서비스** ->  **+새로 만들기**를 클릭하고 "Azure Key Vault"에 대해 검색합니다.

![AKV 검색](media/store-credentials-in-key-vault/search-akv.png)

자격 증명이 저장된 프로비전된 Azure Key Vault를 선택합니다. **연결 테스트**를 수행하여 AKV 연결이 유효한지 확인할 수 있습니다. 

![AKV 구성](media/store-credentials-in-key-vault/configure-akv.png)

**JSON 예제:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>키 자격 증명 모음에 저장된 참조 비밀

키 자격 증명 모음 암호를 참조하는 연결된 서비스에서 필드를 구성할 때 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 필드의 형식 속성을 **AzureKeyVaultSecret**으로 설정해야 합니다. | 예 |
| secretName | Azure Key Vault의 비밀 이름입니다. | 예 |
| secretVersion | Azure Key Vault에 있는 암호의 버전입니다.<br/>지정하지 않으면 항상 최신 버전의 비밀을 사용합니다.<br/>지정하는 경우 지정된 버전을 사용합니다.| 아닙니다. |
| store | 자격 증명을 저장하는 데 사용하는 Azure Key Vault 연결된 서비스를 나타냅니다. | 예 |

**제작 UI 사용:**

데이터 저장소/계산에 대한 연결을 만드는 동안 비밀 필드에 대한 **Azure Key Vault**를 선택합니다. 프로비전된 Azure Key Vault 연결된 서비스를 선택하고 **비밀 이름**을 제공합니다. 필요에 따라 비밀 버전도 제공할 수 있습니다. 

>[!TIP]
>SQL Server, Blob storage 등의 연결 된 서비스에서 연결 문자열을 사용 하는 커넥터의 경우 비밀 필드만 저장 하거나 (예: AKV에 암호) AKV에 전체 연결 문자열을 저장 하도록 선택할 수 있습니다. UI에서 두 옵션을 모두 찾을 수 있습니다.

![AKV 비밀 구성](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON 예제: ("암호" 섹션 참조)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
