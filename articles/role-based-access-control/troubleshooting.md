---
title: Azure 리소스에 대한 RBAC 문제 해결 | Microsoft Docs
description: Azure 리소스에 대한 RBAC(역할 기반 액세스 제어) 문제를 해결합니다.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 67d624bb81105b8219030c57460b6d7bf7458671
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980982"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Azure 리소스에 대한 RBAC 문제 해결

이 문서에서는 Azure 리소스에 대한 RBAC(역할 기반 액세스 제어)와 관련된 일반적인 질문에 대답합니다. Azure Portal의 역할을 사용할 때 예상되는 동작을 이해하고 액세스 문제를 해결하는 데 도움이 될 것입니다.

## <a name="problems-with-rbac-role-assignments"></a>RBAC 역할 할당 관련 문제

- **추가** > **역할 할당** 추가 옵션을 사용할 수 없거나 "개체 id가 있는 클라이언트에 게 작업을 수행할 수 있는 권한이 없습니다." 라는 권한 오류가 발생 하 여 AZURE PORTAL **액세스 제어 (IAM)** 에서 역할 할당을 추가할 수 없는 경우 역할을 할당 하려는 범위에서 [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 와 같은 `Microsoft.Authorization/roleAssignments/write` 권한이 있는 역할이 할당 된 사용자로 현재 로그인 했는지 확인 합니다.
- 역할을 할당 하려고 할 때 "더 이상 역할 할당을 만들 수 없습니다 (코드: RoleAssignmentLimitExceeded)" 라는 오류 메시지가 표시 되는 경우 대신 그룹에 역할을 할당 하 여 역할 할당의 수를 줄여 보세요. Azure는 구독당 최대 **2000**개의 역할 할당을 지원합니다. 이 역할 할당 제한은 고정 되어 있으므로 늘릴 수 없습니다.

## <a name="problems-with-custom-roles"></a>사용자 지정 역할의 문제

- 사용자 지정 역할을 만드는 방법에 대 한 단계가 필요한 경우 [Azure PowerShell](tutorial-custom-role-powershell.md) 또는 [Azure CLI](tutorial-custom-role-cli.md)를 사용 하 여 사용자 지정 역할 자습서를 참조 하세요.
- 기존 사용자 지정 역할을 업데이트할 수 없는 경우 [소유자](built-in-roles.md#owner) 또는 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator)와 같은 `Microsoft.Authorization/roleDefinition/write` 권한이 있는 역할이 할당 된 사용자로 현재 로그인 했는지 확인 합니다.
- 사용자 지정 역할을 삭제할 수 없고 "역할을 참조 하는 기존 역할 할당이 있습니다 (코드: RoleDefinitionHasAssignments)" 라는 오류 메시지가 표시 되는 경우 사용자 지정 역할을 사용 하는 역할 할당도 있습니다. 이 경우 해당 역할 할당을 제거하고 다시 삭제해 봅니다.
- 새 사용자 지정 역할을 만들려고 할 때 "역할 정의 제한을 초과했습니다. 더 이상 역할 정의를 만들 수 없습니다. (코드: Roledefinition한계가 초과 됨) "새 사용자 지정 역할을 만들려고 하면 사용 되지 않는 모든 사용자 지정 역할을 삭제 합니다. Azure는 테넌트에서 최대 **5000** 개의 사용자 지정 역할을 지원 합니다. (Azure Government, Azure 독일, Azure 중국 21Vianet 같은 특수 클라우드는 사용자 지정 역할 2000개로 제한됩니다.)
- "클라이언트에 '/subscriptions/{subscriptionid} ' 범위에서 ' Microsoft. Authorization/roleDefinitions/write ' 작업을 수행할 수 있는 권한이 있지만 연결 된 구독을 찾을 수 없습니다." 라는 오류 메시지가 표시 되 면, 사용자 지정 역할을 업데이트 하려고 할 때 테넌트에서 [할당 가능한 범위가](role-definitions.md#assignablescopes) 하나 이상 삭제 되었는지 확인 합니다. 범위가 삭제되었으면 지원 티켓을 만듭니다. 현재는 사용 가능한 셀프 서비스 솔루션이 없기 때문입니다.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>테넌트에서 구독이 이동될 때 RBAC 복구

- 다른 Azure AD 테넌트에 구독을 전송 하는 방법에 대 한 단계가 필요한 경우 [Azure 구독의 소유권을 다른 계정으로 이전](../cost-management-billing/manage/billing-subscription-transfer.md)을 참조 하세요.
- 구독을 다른 Azure AD 테넌트로 전송하는 경우 모든 역할 할당이 원본 Azure AD 테넌트에서 영구적으로 삭제되고 대상 Azure AD 테넌트로 마이그레이션되지 않습니다. 대상 테넌트에서 역할 할당을 다시 만들어야 합니다. 또한 Azure 리소스에 대 한 관리 되는 id를 수동으로 다시 만들어야 합니다. 자세한 내용은 [관리 id의 faq 및 알려진 문제](../active-directory/managed-identities-azure-resources/known-issues.md)를 참조 하세요.
- Azure AD 전역 관리자이 고 테넌트 간에 이동 된 후 구독에 대 한 액세스 권한이 없는 경우 **azure 리소스에 대 한 액세스 관리** 를 사용 하 여 구독에 대 한 액세스 권한을 일시적으로 [상승](elevate-access-global-admin.md) 시킬 수 있습니다.

## <a name="issues-with-service-admins-or-co-admins"></a>서비스 관리자 또는 공동 관리자 관련 문제

- 서비스 관리자 또는 공동 관리자에 게 문제가 있는 경우 [azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md) 및 [클래식 구독 관리자 역할, azure RBAC 역할 및 azure AD 관리자 역할](rbac-and-directory-admin-roles.md)추가 또는 변경을 참조 하세요.

## <a name="access-denied-or-permission-errors"></a>액세스 거부 또는 사용 권한 오류

- 리소스를 만들려고 할 때 "개체 id가 있는 클라이언트에 대해 작업을 수행 하기 위한 권한 부여가 없습니다 (코드: AuthorizationFailed)" 라는 권한 오류가 표시 되 면 쓰기 권한이 있는 사용자로 현재 로그인 했는지 확인 합니다. 선택한 범위에서 리소스에 대 한 사용 권한입니다. 예를 들어 리소스 그룹의 가상 머신을 관리하려면 리소스 그룹(또는 부모 범위)에 대한 [가상 머신 기여자](built-in-roles.md#virtual-machine-contributor) 역할이 필요합니다. 각 기본 제공 역할의 권한 목록은 [Azure 리소스의 기본 제공 역할](built-in-roles.md)을 참조하세요.
- 지원 티켓을 만들거나 업데이트 하려고 할 때 "지원 요청을 만들 수 있는 권한이 없습니다." 라는 권한 오류가 표시 되 면 [지원 요청 참가자](built-in-roles.md#support-request-contributor)와 같이 `Microsoft.Support/supportTickets/write` 권한이 있는 역할이 할당 된 사용자로 현재 로그인 했는지 확인 합니다.

## <a name="role-assignments-with-unknown-security-principal"></a>보안 주체를 알 수 없는 역할 할당

보안 주체 (사용자, 그룹, 서비스 주체 또는 관리 id)에 역할을 할당 하 고 나중에 역할 할당을 제거 하지 않고 해당 보안 주체를 삭제 하는 경우 역할 할당의 보안 주체 형식이 **알 수 없음**으로 나열 됩니다. 다음 스크린샷은 Azure Portal에서의 예를 보여줍니다. 보안 주체 이름이 **identity deleted** 로 나열 되 고 **id가 더 이상 존재 하지**않습니다. 

![웹앱 리소스 그룹](./media/troubleshooting/unknown-security-principal.png)

Azure PowerShell를 사용 하 여이 역할 할당을 나열 하면 빈 `DisplayName` 및 `ObjectType` 알 수 없음으로 설정 된 것을 볼 수 있습니다. 예를 들어 [AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 는 다음과 유사한 역할 할당을 반환 합니다.

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

마찬가지로 Azure CLI를 사용 하 여이 역할 할당을 나열 하는 경우 빈 `principalName`표시 됩니다. 예를 들어 [az role 할당 목록은](/cli/azure/role/assignment#az-role-assignment-list) 다음과 같은 역할 할당을 반환 합니다.

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

이러한 역할 할당을 유지 하는 것은 문제가 되지 않지만 다른 역할 할당과 비슷한 단계를 사용 하 여 제거할 수 있습니다. 역할 할당을 제거 하는 방법에 대 한 자세한 내용은 [Azure Portal](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)또는 [Azure CLI](role-assignments-cli.md#remove-a-role-assignment) 를 참조 하세요.

PowerShell에서 개체 ID 및 역할 정의 이름을 사용 하 여 역할 할당을 제거 하려고 하지만 둘 이상의 역할 할당이 매개 변수와 일치 하는 경우 "제공 된 정보가 역할 할당에 매핑되지 않습니다." 라는 오류 메시지가 표시 됩니다. 다음은 오류 메시지의 예를 보여 줍니다.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

이 오류 메시지가 표시 되는 경우 `-Scope` 또는 `-ResourceGroupName` 매개 변수도 지정 해야 합니다.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>RBAC 변경 내용이 인식되지 않음

Azure Resource Manager는 경우에 따라 성능 향상을 위해 구성 및 데이터를 캐시합니다. 역할 할당을 만들거나 삭제하는 경우 변경 내용이 적용되는 데 최대 30분이 걸릴 수 있습니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하는 경우 로그아웃 및 로그인하여 역할 할당 변경 내용을 강제로 새로 고칠 수 있습니다. REST API 호출을 사용하여 역할 할당을 변경하는 경우 액세스 토큰을 새로 고쳐 강제로 새로 고칠 수 있습니다.

## <a name="web-app-features-that-require-write-access"></a>쓰기 액세스 권한이 필요한 웹앱 기능

사용자에게 단일 웹앱에 대한 읽기 전용 액세스를 부여하면 예상치 않게 일부 기능을 사용하지 못할 수 있습니다. 다음 관리 기능을 사용하려면 참여자나 소유자에게 제공되는 웹앱에 대한 **쓰기** 권한이 필요하며, 읽기 전용 시나리오에서는 이러한 기능을 사용할 수 없습니다.

* 시작, 중지 등의 명령
* 일반 구성, 규모 설정, 백업 설정, 모니터링 설정 등의 설정 변경
* 게시 자격 증명과 앱 설정, 연결 문자열 등의 기타 암호 액세스
* 스트리밍 로그
* 진단 로그 구성
* 콘솔(명령 프롬프트)
* 활성 및 최근 배포(로컬 Git 연속 배포의 경우)
* 예상 소요 시간
* 웹 테스트
* 가상 네트워크(쓰기 권한이 있는 사용자가 이전에 가상 네트워크를 구성한 경우에만 읽기 권한자에게 표시됨)

이러한 타일에 액세스할 수 없는 경우 관리자에게 웹앱에 대한 참가자 권한을 요청해야 합니다.

## <a name="web-app-resources-that-require-write-access"></a>쓰기 액세스 권한이 필요한 웹앱 리소스

웹앱에서 몇 가지 리소스가 상호 연동되는 경우 웹앱의 구조가 복잡해집니다. 아래에는 웹 사이트 몇 개가 포함된 일반적인 리소스 그룹이 나와 있습니다.

![웹앱 리소스 그룹](./media/troubleshooting/website-resource-model.png)

따라서 사용자에게 웹앱에 대한 권한만 부여하면 Azure Portal에서 웹 사이트 블레이드의 기능을 대부분 사용할 수 없게 됩니다.

다음 항목을 사용하려면 웹 사이트에 해당하는 **App Service 계획**에 대한 **쓰기** 권한이 필요합니다.  

* 웹앱의 가격 책정 계층 보기(무료 또는 표준)  
* 크기 조정 구성(인스턴스 수, 가상 머신 크기, 자동 크기 조정 설정)  
* 할당량(스토리지, 대역폭, CPU)  

다음 항목을 사용하려면 웹 사이트를 포함하는 전체 **리소스 그룹**에 대한 **쓰기** 권한이 필요합니다.  

* SSL 인증서 및 바인딩(SSL 인증서는 같은 리소스 그룹과 지리적 위치의 사이트 간에 공유될 수 있음)  
* 경고 규칙  
* 자동 크기 조정 설정  
* Application Insights 구성 요소  
* 웹 테스트  

## <a name="virtual-machine-features-that-require-write-access"></a>쓰기 액세스 권한이 필요한 가상 머신 기능

웹앱과 마찬가지로 가상 머신 블레이드의 일부 기능 역시 가상 머신 또는 리소스 그룹의 기타 리소스에 대한 쓰기 권한이 있어야 사용할 수 있습니다.

가상 머신은 도메인 이름, 가상 네트워크, 스토리지 계정 및 경고 규칙과 관련이 있습니다.

다음 항목을 사용하려면 **가상 머신**에 대한 **쓰기** 권한이 필요합니다.

* 엔드포인트  
* IP 주소  
* 디스크  
* 확장  

다음 항목을 사용하려면 **가상 머신**와 가상 머신이 속한 **리소스 그룹**(도메인 이름 포함) 둘 다에 대한 **쓰기** 권한이 필요합니다.  

* 가용성 집합  
* 부하 분산된 집합  
* 경고 규칙  

이러한 타일에 액세스할 수 없는 경우 관리자에게 리소스 그룹에 대한 참가자 권한을 요청합니다.

## <a name="azure-functions-and-write-access"></a>Azure Functions 및 쓰기 액세스

[Azure Functions](../azure-functions/functions-overview.md)의 일부 기능에는 쓰기 액세스 권한이 있어야 합니다. 예를 들어 사용자에 게 [독자](built-in-roles.md#reader) 역할이 할당 된 경우 함수 앱 내에서 함수를 볼 수 없습니다. 포털에서는 **(액세스 권한 없음)** 을 표시합니다.

![함수 앱 액세스 권한 없음](./media/troubleshooting/functionapps-noaccess.png)

판독기는 **플랫폼 기능** 탭을 클릭한 다음, **모든 설정**을 클릭하여 함수 앱(웹앱과 유사)에 관련된 일부 설정을 볼 수 있지만 이러한 설정을 수정할 수 없습니다. 이러한 기능에 액세스 하려면 [참가자](built-in-roles.md#contributor) 역할이 필요 합니다.

## <a name="next-steps"></a>다음 단계

- [게스트 사용자에 대 한 문제 해결](role-assignments-external-users.md#troubleshoot)
- [RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스 관리](role-assignments-portal.md)
- [Azure 리소스에 대한 RBAC 변경 내용의 활동 로그 보기](change-history-report.md)

