---
title: Azure CLI를 사용 하 여 Azure Blockchain 서비스 관리
description: Azure CLI를 사용 하 여 Azure Blockchain 서비스를 관리 하는 방법
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455586"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure Blockchain 서비스 관리

Azure Portal 외에도 Azure CLI를 사용 하 여 Azure Blockchain 서비스의 블록 체인 구성원 및 트랜잭션 노드를 관리할 수 있습니다.

최신 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 를 설치 하 고 `az login`를 사용 하 여의 Azure 계정에 로그인 했는지 확인 합니다.

다음 예제에서는 예제 `<parameter names>`를 사용자 고유의 값으로 바꿉니다.

## <a name="create-blockchain-member"></a>Blockchain 구성원 만들기

예제에서는 새 컨소시엄에서 쿼럼 원장 프로토콜을 실행 하는 Azure Blockchain 서비스에서 blockchain 멤버를 만듭니다.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain Service 블록체인 멤버를 식별하는 고유한 이름입니다. 이 이름은 공용 엔드포인트 주소에 사용됩니다. 예: `myblockchainmember.blockchain.azure.com`. |
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. 예: `eastus`. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **password** | 멤버 계정 암호 멤버 계정 암호는 기본 인증을 사용하여 블록체인 멤버의 공용 엔드포인트를 인증할 때 사용됩니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 1 개의 소문자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 따옴표 (\`), 큰따옴표 ("), 작은따옴표 ('), 대시 (-) 및 semicolumn (;)) 사이 여야 합니다.|
| **protocol** | 공개 미리 보기는 쿼럼을 지원 합니다. |
| **컨소시엄** | 참가할 또는 만들 컨소시엄의 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 암호입니다. 암호는 컨소시엄을 조인 하는 데 사용 됩니다. |
| **ruleName** | IP 주소 범위 허용 목록에 대 한 규칙 이름입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|
| **startIpAddress** | 허용 목록에 대 한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **endIpAddress** | 허용 목록에 대 한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **skuName** | 계층 유형입니다. 표준에는 S0를 사용하고, 기본에는 B0를 사용합니다. |

## <a name="change-blockchain-member-password"></a>Blockchain 구성원 암호 변경

예에서는 blockchain 멤버의 암호를 변경 합니다.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 구성원을 식별 하는 이름입니다. |
| **password** | 멤버 계정 암호 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 1 자 소문자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 큰따옴표 (\`), 큰따옴표 ("), 작은따옴표 ('), 대시 (-) 및 세미콜론 (;)) 사이 여야 합니다. |

## <a name="create-transaction-node"></a>트랜잭션 노드 만들기

기존 blockchain 멤버 안에 트랜잭션 노드를 만듭니다. 트랜잭션 노드를 추가 하 여 보안 격리를 강화 하 고 부하를 분산할 수 있습니다. 예를 들어 다른 클라이언트 응용 프로그램에 대 한 트랜잭션 노드 엔드포인트이 있을 수 있습니다.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름도 포함 하는 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |
| **위치** | 블록체인 멤버가 만들어지는 Azure 지역입니다. 예: `eastus`. 사용자 또는 다른 Azure 애플리케이션에 가장 가까운 위치를 선택합니다. |
| **password** | 트랜잭션 노드 암호입니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 1 자 소문자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 큰따옴표 (\`), 큰따옴표 ("), 작은따옴표 ('), 대시 (-) 및 세미콜론 (;)) 사이 여야 합니다. |
| **ruleName** | IP 주소 범위 허용 목록에 대 한 규칙 이름입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **startIpAddress** | 허용 목록에 대 한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다. |
| **endIpAddress** | 허용 목록에 대 한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|

## <a name="change-transaction-node-password"></a>트랜잭션 노드 암호 변경

예는 트랜잭션 노드 암호를 변경 합니다.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름도 포함 하는 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |
| **password** | 트랜잭션 노드 암호입니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 1 자 소문자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 큰따옴표 (\`), 큰따옴표 ("), 작은따옴표 ('), 대시 (-) 및 세미콜론 (;)) 사이 여야 합니다. |

## <a name="change-consortium-management-account-password"></a>컨소시엄 관리 계정 암호 변경

컨소시엄 관리 계정은 consortium 멤버 관리에 사용 됩니다. 각 멤버는 컨소시엄 관리 계정에 의해 고유 하 게 식별 되며, 다음 명령을 사용 하 여이 계정의 암호를 변경할 수 있습니다.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service 리소스가 만들어지는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 구성원을 식별 하는 이름입니다. |
| **consortiumManagementAccountPassword** | 컨소시엄 관리 계정 암호입니다. 암호는 다음 4 가지 요구 사항 중 세 가지를 충족 해야 합니다. 길이는 12 & 72 자, 1 자 소문자, 1 개의 대문자, 1 개의 숫자 및 1 개의 숫자 기호 (#), 백분율 (%), 쉼표 (,), 별표 (*), 큰따옴표 (\`), 큰따옴표 ("), 작은따옴표 ('), 대시 (-) 및 세미콜론 (;)) 사이 여야 합니다. |
  
## <a name="update-firewall-rules"></a>방화벽 규칙 업데이트

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |
| **ruleName** | IP 주소 범위 허용 목록에 대 한 규칙 이름입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|
| **startIpAddress** | 허용 목록에 대 한 IP 주소 범위의 시작입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|
| **endIpAddress** | 허용 목록에 대 한 IP 주소 범위의 끝입니다. 방화벽 규칙에 대 한 선택적 매개 변수입니다.|

## <a name="list-api-keys"></a>API 키 나열

API 키는 사용자 이름 및 암호와 유사한 노드 액세스에 사용할 수 있습니다. 키 회전을 지 원하는 두 개의 API 키가 있습니다. 다음 명령을 사용 하 여 API 키를 나열 합니다.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름도 포함 하는 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |

## <a name="regenerate-api-keys"></a>API 키 다시 생성

다음 명령을 사용 하 여 API 키를 다시 생성 합니다.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 새 트랜잭션 노드 이름도 포함 하는 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |
| **keyName** | \<keyValue\>를 key1 또는 key2로 바꿉니다. |

## <a name="delete-a-transaction-node"></a>트랜잭션 노드 삭제

예에서는 blockchain 구성원 트랜잭션 노드를 삭제 합니다.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 삭제할 트랜잭션 노드 이름도 포함 하는 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |

## <a name="delete-a-blockchain-member"></a>Blockchain 구성원 삭제

이 예에서는 blockchain 멤버를 삭제 합니다.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| 매개 변수 | 설명 |
|---------|-------------|
| **resource-group** | Azure Blockchain 서비스 리소스가 있는 리소스 그룹 이름입니다. |
| **name** | 삭제할 Azure Blockchain 서비스 Blockchain 멤버의 이름입니다. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Azure AD 사용자에 대 한 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **담당자** | Azure AD 사용자 ID입니다. 예: `user@contoso.com` |
| **scope** | 역할 할당의 범위입니다. Blockchain 멤버 또는 트랜잭션 노드일 수 있습니다. |

**예:**

Azure AD 사용자에 대 한 노드 액세스를 blockchain **구성원**에 게 부여:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**예:**

Blockchain **트랜잭션 노드에**Azure AD 사용자에 대 한 노드 액세스를 부여 합니다.

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Azure AD 그룹 또는 응용 프로그램 역할에 대 한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **담당자-개체-id** | Azure AD 그룹 ID 또는 응용 프로그램 ID입니다. |
| **scope** | 역할 할당의 범위입니다. Blockchain 멤버 또는 트랜잭션 노드일 수 있습니다. |

**예:**

**응용 프로그램 역할** 에 대 한 노드 액세스 권한 부여

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD 노드 액세스 제거

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| 매개 변수 | 설명 |
|---------|-------------|
| **role** | Azure AD 역할의 이름입니다. |
| **담당자** | Azure AD 사용자 ID입니다. 예: `user@contoso.com` |
| **scope** | 역할 할당의 범위입니다. Blockchain 멤버 또는 트랜잭션 노드일 수 있습니다. |

## <a name="next-steps"></a>다음 단계

Azure Portal를 사용 하 여 [Azure Blockchain 서비스 트랜잭션 노드를 구성](configure-transaction-nodes.md)하는 방법을 알아봅니다.
