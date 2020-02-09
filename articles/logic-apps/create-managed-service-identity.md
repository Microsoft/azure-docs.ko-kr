---
title: 관리되는 ID를 사용하여 인증
description: 관리 id를 사용 하 여 자격 증명 또는 암호를 사용 하 여 로그인 하지 않고 다른 Azure Active Directory 테넌트의 리소스에 액세스
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 714faa43f34de965055ceba80de08972dd4192ac
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861203"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps에서 관리 되는 id를 사용 하 여 Azure 리소스에 대 한 액세스 인증

다른 Azure Active Directory (Azure AD) 테넌트의 리소스에 액세스 하 고 로그인 하지 않고 id를 인증 하기 위해 논리 앱은 자격 증명이 나 비밀이 아닌 시스템 할당 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) (이전의 관리 서비스 ID 또는 MSI)를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 도움이 됩니다. 이 문서에서는 논리 앱에서 시스템 할당 관리 id를 설정 하 고 사용 하는 방법을 보여 줍니다. 현재 관리 되는 id는 관리 되는 커넥터나 연결이 아닌 [특정 기본 제공 트리거 및 작업](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls)에서만 작동 합니다.

자세한 내용은 다음 항목을 참조하세요.

* [관리 id를 지 원하는 트리거 및 작업](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [관리 id를 사용 하 여 Azure AD 인증을 지 원하는 azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [아웃 바운드 호출에서 지원 되는 인증 유형](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [논리 앱에 대 한 관리 id 제한](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>필수 조건

* Azure 구독, 구독이 없는 경우 [체험판 Azure 계정에 등록하세요](https://azure.microsoft.com/free/). 액세스 하려는 관리 되는 id와 대상 Azure 리소스 모두 동일한 Azure 구독을 사용 해야 합니다.

* 대상 리소스와 동일한 Azure AD 테넌트에서 관리 되는 id에 역할을 할당할 수 있는 [AZURE ad 관리자 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 관리 id에 Azure 리소스에 대 한 액세스 권한을 부여 하려면 대상 리소스에서 해당 id에 대 한 역할을 추가 해야 합니다.

* 액세스 하려는 대상 Azure 리소스

* 관리 되는 id를 [지 원하는 트리거 및 동작](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) 을 사용 하는 논리 앱

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>시스템 할당 id 사용

사용자 할당 id와 달리 시스템 할당 id는 수동으로 만들 필요가 없습니다. 논리 앱의 시스템 할당 id를 설정 하려면 다음 옵션을 사용할 수 있습니다.

* [Azure Portal](#azure-portal-system-logic-app)
* [Azure 리소스 관리자 템플릿](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure Portal에서 시스템 할당 id 사용

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **id** > **시스템이 할당 됨**을 선택 합니다. **상태**에서 **설정** > **저장** > **예**를 선택 합니다.

   ![시스템이 할당 한 id 사용](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   이제 논리 앱은 Azure Active Directory에 등록 되 고 개체 ID로 표시 되는 시스템 할당 id를 사용할 수 있습니다.

   ![시스템 할당 id에 대 한 개체 ID](./media/create-managed-service-identity/object-id.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **개체 ID** | <*identity-resource-ID*> | Azure AD 테넌트에서 논리 앱의 시스템 할당 id를 나타내는 GUID (Globally Unique Identifier)입니다. |
   ||||

1. 이제 [리소스에 대 한 id 액세스 권한을 부여 하는 단계](#access-other-resources)를 따릅니다.

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 시스템 할당 id 사용

논리 앱과 같은 Azure 리소스 만들기 및 배포를 자동화 하기 위해 [Azure Resource Manager 템플릿을](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)사용할 수 있습니다. 템플릿에서 논리 앱에 대 한 시스템 할당 관리 id를 사용 하도록 설정 하려면 `identity` 개체 및 `type` 자식 속성을 템플릿의 논리 앱 리소스 정의에 추가 합니다. 예를 들면 다음과 같습니다.

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Azure에서 논리 앱 리소스 정의를 만들 때 `identity` 개체는 다음과 같은 추가 속성을 가져옵니다.

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Property(JSON) | 값 | Description |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Azure AD 테넌트에서 논리 앱을 나타내는 관리 되는 id에 대 한 서비스 주체 개체의 GUID (Globally Unique Identifier)입니다. 이 GUID는 "개체 ID" 또는 `objectID`으로 나타날 수도 있습니다. |
| `tenantId` | <*Azure-AD-tenant-ID*> | 논리 앱이 현재 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID (Globally Unique Identifier)입니다. Azure AD 테넌트 내부에서 서비스 주체는 논리 앱 인스턴스와 이름이 같습니다. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>리소스에 id 액세스 권한 부여

인증을 위해 논리 앱의 시스템 할당 관리 id를 사용 하려면 먼저 id를 사용 하려는 Azure 리소스에 대 한 id 액세스를 제공 합니다. 이 작업을 완료 하려면 대상 Azure 리소스에서 해당 id에 적절 한 역할을 할당 합니다. 사용할 수 있는 옵션은 다음과 같습니다.

* [Azure Portal](#azure-portal-assign-access)
* [Azure Resource Manager 템플릿](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment))-자세한 내용은 [Azure RBAC를 사용 하 여 역할 할당 추가 및 Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)를 참조 하세요.
* Azure CLI ([az role 할당 create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create))-자세한 내용은 [Azure RBAC 및 Azure CLI를 사용 하 여 역할 할당 추가](../role-based-access-control/role-assignments-cli.md)를 참조 하세요.
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Azure Portal에서 액세스 할당

1. [Azure Portal](https://portal.azure.com)에서 관리 id를 액세스할 수 있도록 하려는 Azure 리소스로 이동 합니다.

1. 리소스 메뉴에서 **액세스 제어 (IAM)**  > **역할 할당** 을 선택 하 여 해당 리소스에 대 한 현재 역할 할당을 검토할 수 있습니다. 도구 모음에서 **추가** > **역할 할당 추가**를 선택 합니다.

   !["추가" > "역할 할당 추가"를 선택 합니다.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > **역할 할당 추가** 옵션이 사용 하지 않도록 설정 된 경우에는 대부분 권한이 없을 가능성이 높습니다. 리소스의 역할을 관리할 수 있는 권한에 대 한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조 하세요.

1. **역할 할당 추가**에서 대상 리소스에 대 한 필요한 액세스 권한을 id에 부여 하는 **역할** 을 선택 합니다.

   이 항목의 예에서는 id에 [Azure Storage 컨테이너의 blob에 액세스할 수 있는 역할이](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)필요 합니다.

   !["저장소 Blob 데이터 참가자" 역할을 선택 합니다.](./media/create-managed-service-identity/assign-role.png)

1. **다음에 대한 액세스 할당** 상자에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**를 선택합니다.

   ![시스템 할당 id에 대 한 액세스 선택](./media/create-managed-service-identity/assign-access-system.png)

1. **선택** 상자에서 논리 앱을 찾아 선택 합니다.

   ![시스템 할당 id에 대 한 논리 앱 선택](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 완료되면 **저장**을 선택합니다.

   이제 대상 리소스의 역할 할당 목록에 선택한 관리 되는 id와 역할이 표시 됩니다.

   ![대상 리소스에 관리 되는 id 및 역할을 추가 했습니다.](./media/create-managed-service-identity/added-roles-for-identities.png)

1. 이제 관리 되는 id를 지 원하는 트리거 또는 작업에서 id를 사용 하 여 [액세스를 인증 하는 단계](#authenticate-access-with-identity) 를 수행 합니다.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>관리 id를 사용 하 여 액세스 인증

[논리 앱에 관리 되는 id를 사용 하도록 설정](#azure-portal-system-logic-app) 하 고 해당 id에 [대상 리소스나 엔터티에](#access-other-resources)대 한 액세스 권한을 부여 하 고 나면 관리 되는 [id를 지 원하는 트리거와 작업](logic-apps-securing-a-logic-app.md#managed-identity-authentication)에서 해당 id를 사용할 수 있습니다.

> [!IMPORTANT]
> 시스템이 할당 한 id를 사용 하려는 Azure 함수가 있는 경우 먼저 [azure 기능에 대해 인증을 사용 하도록 설정](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)합니다.

다음 단계에서는 Azure Portal을 통해 트리거 또는 작업에 관리 되는 id를 사용 하는 방법을 보여 줍니다. 트리거 또는 작업의 기본 JSON 정의에서 관리 되는 id를 지정 하려면 [관리 id 인증](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 아직 수행 하지 않은 경우 [관리 되는 id를 지 원하는 트리거 또는 작업](logic-apps-securing-a-logic-app.md#managed-identity-authentication)을 추가 합니다.

   예를 들어, HTTP 트리거 또는 작업은 논리 앱에 대해 사용 하도록 설정 된 시스템 할당 id를 사용할 수 있습니다. 일반적으로 HTTP 트리거 또는 작업은 이러한 속성을 사용 하 여 액세스 하려는 리소스나 엔터티를 지정 합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **메서드** | 예 | 실행 하려는 작업에서 사용 하는 HTTP 메서드입니다. |
   | **URI** | 예 | 대상 Azure 리소스 또는 엔터티에 액세스 하기 위한 엔드포인트 URL입니다. URI 구문은 일반적으로 Azure 리소스 또는 서비스에 대 한 [리소스 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) 를 포함 합니다. |
   | **헤더** | 아닙니다. | 필요한 모든 헤더 값 (예: 콘텐츠 형식)을 보내는 요청에 포함 하려고 합니다. |
   | **쿼리** | 아닙니다. | 특정 작업에 대 한 매개 변수 또는 실행 하려는 작업의 API 버전 등 요청에 포함 하려는 모든 쿼리 매개 변수 |
   | **인증** | 예 | 대상 리소스 또는 엔터티에 대 한 액세스를 인증 하는 데 사용할 인증 유형입니다. |
   ||||

   특정 한 예로, 이전에 id에 대 한 액세스를 설정한 Azure Storage 계정의 blob에 대해 [스냅숏 blob 작업](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) 을 실행 하려고 한다고 가정 합니다. 그러나 [Azure Blob Storage 커넥터](https://docs.microsoft.com/connectors/azureblob/) 는 현재이 작업을 제공 하지 않습니다. 대신 [HTTP 동작](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 또는 다른 [Blob Service REST API 작업](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)을 사용 하 여이 작업을 실행할 수 있습니다.

   > [!IMPORTANT]
   > HTTP 요청 및 관리 되는 id를 사용 하 여 방화벽 뒤에 있는 Azure 저장소 계정에 액세스 하려면 신뢰할 수 있는 [Microsoft 서비스의 액세스를 허용 하는 예외](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)를 사용 하 여 저장소 계정도 설정 해야 합니다.

   [Blob 스냅숏 작업](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)을 실행 하려면 HTTP 동작에서 다음 속성을 지정 합니다.

   | 속성 | 필수 | 예제 값 | Description |
   |----------|----------|---------------|-------------|
   | **메서드** | 예 | `PUT`| 스냅숏 Blob 작업에서 사용 하는 HTTP 메서드입니다. |
   | **URI** | 예 | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | 이 구문을 사용 하는 Azure 전역 (공용) 환경의 Azure Blob Storage 파일에 대 한 리소스 ID입니다. |
   | **헤더** | 예, Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure Storage 작업에 필요한 `x-ms-blob-type` 및 `x-ms-version` 헤더 값입니다. <p><p>**중요**: Azure Storage에 대 한 나가는 HTTP 트리거와 작업 요청에서 헤더에는 실행할 작업에 대 한 `x-ms-version` 속성 및 API 버전이 필요 합니다. <p>자세한 내용은 다음 항목을 참조하세요. <p><p>- [요청 헤더-스냅숏 Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>[Azure Storage 서비스에 대 한 - 버전 관리](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **쿼리** | 예 (이 작업의 경우) | `comp` = `snapshot` | Snapshot Blob 작업에 대 한 쿼리 매개 변수 이름 및 값입니다. |
   | **인증** | 예 | `Managed Identity` | Azure blob에 대 한 액세스를 인증 하는 데 사용할 인증 유형입니다. |
   |||||

   다음은 이러한 모든 속성 값을 보여 주는 예제 HTTP 동작입니다.

   ![HTTP 작업을 추가 하 여 Azure 리소스에 액세스](./media/create-managed-service-identity/http-action-example.png)

   사용 가능한 모든 Azure REST API 작업에 대 한 자세한 내용은 [azure REST API 참조](https://docs.microsoft.com/rest/api/azure/)를 참조 하세요.

1. **인증** 목록에서 **관리 ID**를 선택합니다. [ **인증** 속성이 지원](logic-apps-securing-a-logic-app.md#add-authentication-outbound) 되지만 숨겨진 경우에는 **새 매개 변수 추가** 목록을 열고 **인증**을 선택 합니다.

   > [!NOTE]
   > 모든 트리거 및 동작을 통해 인증 유형을 선택할 수 있는 것은 아닙니다. 자세한 내용은 [아웃 바운드 호출에 인증 추가](logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조 하세요.

   !["인증" 속성에서 "관리 되는 Id"를 선택 합니다.](./media/create-managed-service-identity/select-managed-identity.png)

1. **관리 id**를 선택 하면 일부 트리거 및 작업에 대 한 **대상** 속성이 표시 됩니다. **대상** 속성이 지원 되지만 숨겨진 경우 **새 매개 변수 추가** 목록을 열고 **대상 그룹**을 선택 합니다.

1. 대상 리소스 또는 서비스에 대 한 리소스 ID로 **대상 값을** 설정 했는지 확인 합니다. 그렇지 않은 경우 기본적으로 **대상** 속성은 Azure Resource Manager에 대 한 리소스 id 인 `https://management.azure.com/` 리소스 id를 사용 합니다.

   > [!IMPORTANT]
   > 대상 리소스 ID가 필요한 후행 슬래시를 포함 하 여 Azure Active Directory (AD)가 예상 하는 값 *과 정확* 하 게 일치 하는지 확인 합니다. 예를 들어 모든 Azure Blob Storage 계정에 대 한 리소스 ID는 후행 슬래시가 필요 합니다. 그러나 특정 저장소 계정에 대 한 리소스 ID는 후행 슬래시가 필요 하지 않습니다. [AZURE AD를 지 원하는 azure 서비스의 리소스 id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 확인 합니다.

   이 예제에서는 인증에 사용 되는 액세스 토큰이 모든 저장소 계정에 대해 유효 하도록 **대상** 속성을 `https://storage.azure.com/`로 설정 합니다. 그러나 특정 저장소 계정에 대 한 루트 서비스 URL `https://fabrikamstorageaccount.blob.core.windows.net`를 지정할 수도 있습니다.

   !["대상" 속성에서 대상 리소스 ID 지정](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure Storage Azure AD를 사용 하 여 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 다음 항목을 참조 하세요.

   * [Azure Active Directory를 사용 하 여 Azure blob 및 큐에 대 한 액세스 권한 부여](../storage/common/storage-auth-aad.md)
   * [Azure Active Directory를 사용 하 여 Azure Storage에 대 한 액세스 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>시스템 할당 id 제거

논리 앱에 대 한 시스템 할당 id 사용을 중지 하려면 다음 옵션을 사용할 수 있습니다.

* [Azure Portal](#azure-portal-disable)
* [Azure 리소스 관리자 템플릿](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

논리 앱을 삭제 하는 경우 Azure는 Azure AD에서 관리 되는 id를 자동으로 제거 합니다.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Azure Portal에서 시스템 할당 id 제거

Azure Portal에서 시스템 할당 id를 [논리 앱에서](#disable-identity-logic-app) 제거 하 고 [대상 리소스에서](#disable-identity-target-resource)id의 액세스를 제거 합니다.

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>논리 앱에서 시스템 할당 id 제거

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **id** > **시스템이 할당 됨**을 선택 합니다. **상태**에서 **끄기** 를 선택 하 > **저장** > **예**를 선택 합니다.

   ![시스템 할당 id 사용 중지](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>리소스에서 id 액세스 제거

1. [Azure Portal](https://portal.azure.com)에서 관리 되는 id에 대 한 액세스를 제거 하려는 대상 Azure 리소스로 이동 합니다.

1. 대상 리소스의 메뉴에서 **Access control (IAM)** 을 선택 합니다. 도구 모음에서 **역할 할당**을 선택 합니다.

1. 역할 목록에서 제거 하려는 관리 되는 id를 선택 합니다. 도구 모음에서 **제거**를 선택 합니다.

   > [!TIP]
   > **제거** 옵션을 사용 하지 않도록 설정 하면 사용 권한이 없는 것입니다. 리소스의 역할을 관리할 수 있는 권한에 대 한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조 하세요.

이제 관리 되는 id가 제거 되 고 더 이상 대상 리소스에 대 한 액세스 권한이 없습니다.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 관리 되는 id 사용 안 함

Azure Resource Manager 템플릿을 사용 하 여 논리 앱의 시스템 관리 id를 사용 하도록 설정한 경우 `identity` 개체의 `type` 자식 속성을 `None`로 설정 합니다. 또한이 작업은 Azure AD에서 시스템 관리 id의 보안 주체 ID를 삭제 합니다.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 액세스 및 데이터 보호](../logic-apps/logic-apps-securing-a-logic-app.md)
