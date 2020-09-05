---
title: Azure RBAC를 사용 하 여 Azure key vault에 액세스 하는 응용 프로그램에 권한 부여 | Microsoft Docs
description: Azure 역할 기반 액세스 제어를 사용 하 여 키, 암호 및 인증서에 대 한 액세스를 제공 하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f1e95c1244d327478862c3919481394d974ea42
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270347"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>Azure 역할 기반 액세스 제어 (미리 보기)를 사용 하 여 Key Vault 키, 인증서 및 암호에 대 한 액세스 제공

Azure RBAC (역할 기반 access control)는 Azure 리소스에 대 한 세분화 된 액세스 관리를 제공 하는 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 을 기반으로 하는 권한 부여 시스템입니다.

Azure RBAC를 통해 사용자는 키, 암호 및 인증서 권한을 관리할 수 있습니다. 모든 주요 자격 증명 모음에 대 한 모든 사용 권한을 관리 하는 한 곳을 제공 합니다. 

Azure RBAC 모델은 관리 그룹, 구독, 리소스 그룹 또는 개별 리소스의 다양 한 범위 수준에서 사용 권한을 설정 하는 기능을 제공 합니다.  키 자격 증명 모음에 대 한 Azure RBAC는 개별 키, 암호 및 인증서에 대 한 별도의 권한을 제공 하는 기능도 제공 합니다.

자세한 내용은 azure [역할 기반 액세스 제어 (AZURE RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)를 참조 하세요.

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>개별 키, 암호 및 인증서에 대 한 모범 사례

환경 별로 응용 프로그램당 자격 증명 모음을 사용 하는 것이 좋습니다 (개발, 프로덕션 전 및 프로덕션).

개별 키, 암호 및 인증서 권한은 특정 시나리오에만 사용 해야 합니다.

-   계층 간에 액세스 제어를 분리 해야 하는 다중 계층 응용 프로그램

-   응용 프로그램에서 해당 키 자격 증명 모음에 있는 암호의 하위 집합에 액세스 해야 하는 경우 공통 비밀이 포함 된 공유 키 자격 증명 모음

Azure Key Vault 관리 지침에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Key Vault 모범 사례](best-practices.md)
- [Azure Key Vault 서비스 제한 사항](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>Key Vault 데이터 평면 작업을 위한 Azure 기본 제공 역할 (미리 보기)

| 기본 제공 역할 | Description | ID |
| --- | --- | --- |
| Key Vault 관리자 (미리 보기) | 인증서, 키 및 비밀을 포함 하 여 주요 자격 증명 모음 및 해당 개체에 있는 모든 개체에 대 한 모든 데이터 평면 작업을 수행 합니다. 주요 자격 증명 모음 리소스를 관리 하거나 역할 할당을 관리할 수 없습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault 인증서 담당자 (미리 보기) | 권한 관리를 제외한 key vault의 인증서에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto 담당자 (미리 보기)| 권한 관리를 제외한 key vault 키에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault Crypto 서비스 암호화 (미리 보기) | 키의 메타 데이터를 읽고 래핑/래핑 해제 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault Crypto 사용자 (미리 보기) | 키를 사용 하 여 암호화 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault 판독기 (미리 보기)| 키 자격 증명 모음 및 해당 인증서, 키 및 비밀의 메타 데이터를 읽습니다. 비밀 콘텐츠 또는 키 자료와 같은 중요 한 값을 읽을 수 없습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault 비밀 책임자 (미리 보기)| 권한 관리를 제외한 key vault의 비밀에 대 한 작업을 수행 합니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | b86a8fe4-4948-aee5-eccb2c155cd7 |
| Key Vault 비밀 사용자 (미리 보기)| 비밀 콘텐츠를 읽습니다. ' Azure 역할 기반 액세스 제어 ' 권한 모델을 사용 하는 키 자격 증명 모음에만 적용 됩니다. | 4633458b-17de-408a-b874-0445c86b69e6 |

Azure 기본 제공 역할 정의에 대 한 자세한 내용은 [azure 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조 하세요.

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Key Vault에서 Azure RBAC 비밀, 키 및 인증서 사용 권한 사용

키 자격 증명 모음에 대 한 새 Azure RBAC 권한 모델은 자격 증명 모음 액세스 정책 권한 모델에 대 한 대안을 제공 합니다. 

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Key Vault에 대 한 Azure RBAC 사용 권한 설정

> [!IMPORTANT]
> Azure RBAC 권한 모델을 설정 하면 모든 액세스 정책 권한이 무효화 됩니다. 동일한 Azure 역할이 할당 되지 않은 경우 중단 될 수 있습니다.

1.  새 키 자격 증명 모음에 대 한 Azure RBAC 사용 권한 설정:

    ![RBAC 권한 사용-새 자격 증명 모음](../media/rbac/image-1.png)

2.  기존 키 자격 증명 모음에 대 한 Azure RBAC 사용 권한 설정:

    ![RBAC 권한 사용-기존 자격 증명 모음](../media/rbac/image-2.png)

### <a name="assign-role"></a>역할 할당

> [!Note]
> 스크립트에서 역할 이름 대신 고유한 역할 ID를 사용 하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트는 계속 작동 합니다. 미리 보기 중 모든 역할에는 나중에 제거 되는 "(미리 보기)" 접미사가 있습니다. 이 문서에서 역할 이름은 가독성을 위해서만 사용 됩니다.

명령을 Azure CLI 하 여 역할 할당을 만듭니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

Azure Portal에서 Azure 역할 할당 화면은 Access control (IAM) 탭의 모든 리소스에 대해 사용할 수 있습니다.

![역할 할당-(IAM) 탭](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>리소스 그룹 범위 역할 할당

1.  키 자격 증명 모음 리소스 그룹으로 이동 합니다.
    ![역할 할당-리소스 그룹](../media/rbac/image-4.png)

2.  액세스 제어 (IAM) \> 역할 추가 할당 추가를 클릭 합니다. \>

3.  현재 사용자에 대 한 Key Vault 읽기 권한자 역할 "Key Vault 판독기 (미리 보기)"를 만듭니다.

    ![역할 추가-리소스 그룹](../media/rbac/image-5.png)

Azure CLI:
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

위의 역할 할당은 key vault에 key vault 개체를 나열 하는 기능을 제공 합니다.

### <a name="key-vault-scope-role-assignment"></a>Key Vault 범위 역할 할당

1. Key Vault \> Access control (IAM) 탭으로 이동 합니다.

2. 추가 역할 할당 추가를 클릭 합니다. \>

3. 현재 사용자에 대 한 "Key Vault 비밀 책임자 (미리 보기)" 라는 주요 비밀 책임자 역할을 만듭니다.

    ![역할 할당-키 자격 증명 모음](../media/rbac/image-6.png)

 Azure CLI:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

위의 역할 할당을 만든 후에는 비밀을 생성/업데이트/삭제할 수 있습니다.

4. \>비밀 수준 역할 할당 테스트를 위한 새 비밀 (비밀 + 생성/가져오기)을 만듭니다.

    ![역할-키 자격 증명 모음 추가](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>비밀 범위 역할 할당

1. 이전에 만든 비밀, 알림 및 액세스 제어 (IAM) (미리 보기) 중 하나를 엽니다.

2. 액세스 제어 (IAM) (미리 보기) 탭을 클릭 합니다.

    ![역할 할당-암호](../media/rbac/image-8.png)

3. 현재 사용자에 대해 위에서 Key Vault 설명한 것과 동일한 방식으로 "Key Vault 비밀 책임자 (미리 보기)"에 대 한 주요 비밀 책임자 역할을 만듭니다.

Azure CLI:

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>테스트 및 확인

> [!NOTE]
> 역할 할당을 제거한 후 브라우저에서 캐싱 및 페이지 새로 고침을 사용 해야 합니다.<br>
> 역할 할당을 새로 고칠 때까지 몇 분 정도 허용

1. 키 자격 증명 모음 수준에서 "Key Vault 비밀 담당자" 역할 없이 새 암호를 추가 하는 유효성을 검사 합니다.

키 자격 증명 모음 액세스 제어 (IAM) 탭으로 이동 하 여이 리소스에 대 한 "Key Vault 비밀 책임자 (미리 보기)" 역할 할당을 제거 합니다.

![할당 제거-키 자격 증명 모음](../media/rbac/image-9.png)

이전에 만든 암호로 이동 합니다. 모든 비밀 속성을 볼 수 있습니다.

![액세스 권한이 있는 비밀 보기](../media/rbac/image-10.png)

새 비밀 만들기 (비밀 \> + 생성/가져오기)는 아래 오류를 표시 해야 합니다.

   ![새 비밀 만들기](../media/rbac/image-11.png)

2.  비밀 수준에서 "Key Vault 비밀 책임자" 역할을 사용 하지 않고 비밀 편집의 유효성을 검사 합니다.

-   이전에 만든 "IAM (보안 Access Control) (미리 보기) 탭으로 이동 하 여이 리소스에 대 한" Key Vault 비밀 책임자 (미리 보기) "역할 할당을 제거 합니다.

-   이전에 만든 암호로 이동 합니다. 비밀 속성을 볼 수 있습니다.

   ![액세스 권한이 없는 비밀 보기](../media/rbac/image-12.png)

3. 키 자격 증명 모음 수준에서 판독기 역할을 사용 하지 않고 읽은 비밀의 유효성을 검사 합니다.

-   키 자격 증명 모음 리소스 그룹 액세스 제어 (IAM) 탭으로 이동 하 여 "Key Vault 판독기 (미리 보기)" 역할 할당을 제거 합니다.

-   Key vault의 암호 탭으로 이동 하면 다음과 같은 오류가 표시 됩니다.

   ![비밀 탭-오류](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>사용자 지정 역할 만들기 

[az role definition create 명령](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create)

**(CLI bash 스크립트)</br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

[Azure 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

사용 가능한 작업에 대해 부록: **사용 가능한 작업** 을 확인 합니다.

## <a name="known-limits-and-performance"></a>알려진 제한 및 성능

-   2000 구독 당 Azure 역할 할당

-   역할 할당 대기 시간: 역할이 적용 되도록 역할 할당이 변경 된 후 현재 예상 되는 성능에서 최대 10 분 (600 초)이 소요 됩니다.

## <a name="learn-more"></a>자세한 정보

- [Azure RBAC 개요](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [사용자 지정 역할 자습서](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
