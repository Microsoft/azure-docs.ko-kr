---
title: 프로그래밍 방식으로 Azure 구독 만들기
description: 프로그래밍 방식으로 추가 Azure 구독을 만드는 방법에 대해 알아봅니다.
author: amberbhargava
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 47d4454c47967d07898492176438e547b1e561b6
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198686"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>프로그래밍 방식으로 Azure 구독 만들기 (미리 보기)

[EA (기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)를 사용 하는 Azure 고객, [MCA (microsoft 고객 계약](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ) 또는 [MPA (microsoft 파트너 계약)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) 청구 계정을 사용 하면 프로그래밍 방식으로 구독을 만들 수 있습니다. 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 구독을 만드는 방법을 알아봅니다.

Azure 구독을 프로그래밍 방식으로 만드는 경우 해당 구독은 Microsoft 또는 공인 대리점에서 Azure 서비스를 구입한 계약의 적용을 받습니다. 자세한 내용은 [Microsoft Azure 법적 정보](https://azure.microsoft.com/support/legal/)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>EA 청구 계정에 대 한 구독 만들기

### <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 등록 계정에 대 한 소유자 역할이 있어야 합니다. 다음 두 가지 방법으로 역할을 가져올 수 있습니다.

* 등록의 엔터프라이즈 관리자는 등록 계정의 소유자가 되도록 [계정 소유자](https://ea.azure.com/helpdocs/addNewAccount) (로그인 필요)를 만들 수 있습니다.

* 등록 계정의 기존 소유자가 [액세스 권한을 부여](grant-access-to-create-subscription.md)할 수 있습니다. 마찬가지로, 서비스 주체를 사용 하 여 EA 구독을 만들려면 [해당 서비스 사용자에 게 구독을 만들 수 있는 권한을 부여](grant-access-to-create-subscription.md)해야 합니다.

### <a name="find-accounts-you-have-access-to"></a>액세스할 수 있는 계정 찾기

계정 소유자에 연결 된 등록 계정에 추가 하 고 나면 Azure는 계정-등록 관계를 사용 하 여 구독 요금을 청구할 위치를 결정 합니다. 계정으로 만든 모든 구독은 계정이 속한 EA 등록으로 청구 됩니다. 구독을 만들려면 등록 계정 및 구독을 소유하는 사용자 계정에 대한 값을 전달해야 합니다.

다음 명령을 실행하려면 기본적으로 구독이 생성되는 디렉터리인 계정 소유자의 *홈 디렉터리*에 로그인해야 합니다.

### <a name="resttabrest"></a>[REST (영문)](#tab/rest)

액세스 권한이 있는 모든 등록 계정을 나열 하는 요청:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API 응답에는 액세스할 수 있는 모든 등록 계정이 나열 됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 해당 계정의 `name`를 복사 합니다. 예를 들어 SignUpEngineering@contoso.com 등록 계정에서 구독을 만들려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사 합니다. 이 식별자는 등록 계정의 개체 ID입니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록이 값을 어딘가에 붙여넣습니다.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[Azure Cloud Shell](https://shell.azure.com/) 를 열고 PowerShell을 선택 합니다.

[Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet을 사용하여 액세스 권한이 있는 모든 등록 계정을 나열합니다.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure는 사용자가 액세스할 수 있는 등록 계정 목록으로 응답 합니다.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 해당 계정의 `ObjectId`를 복사 합니다. 예를 들어 SignUpEngineering@contoso.com 등록 계정에서 구독을 만들려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사 합니다. 다음 단계에서 `enrollmentAccountObjectId`사용할 수 있도록이 개체 ID를 어딘가에 붙여넣습니다.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 명령을 사용하여 액세스할 수 있는 모든 등록 계정을 나열합니다.

```azurecli-interactive
az billing enrollment-account list
```

Azure는 사용자가 액세스할 수 있는 등록 계정 목록으로 응답 합니다.

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

`principalName` 속성을 사용하여 구독의 요금이 청구되길 원하는 계정을 식별합니다. 해당 계정의 `name`를 복사 합니다. 예를 들어 SignUpEngineering@contoso.com 등록 계정에서 구독을 만들려면 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```를 복사 합니다. 이 식별자는 등록 계정의 개체 ID입니다. 다음 단계에서 `enrollmentAccountObjectId`로 사용할 수 있도록이 값을 어딘가에 붙여넣습니다.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>특정 등록 계정 아래에서 구독 만들기

다음 예에서는 이전 단계에서 선택한 등록 계정에 *Dev Team subscription* 이라는 구독을 만듭니다. 구독 제품은 *ms-azr-0017p-0017P* (일반 Microsoft 기업계약)입니다. 또한 구독에 대한 RBAC 소유자로 두 명의 사용자를 선택적으로 추가합니다.

### <a name="resttabrest"></a>[REST (영문)](#tab/rest)

`<enrollmentAccountObjectId>`를 첫 번째 단계(`name`)에서 복사한 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```로 바꿔서 다음 요청을 수행합니다. 소유자를 지정 하려면 [사용자 개체 id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)알아보세요.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offerType`   | yes      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `owners`      | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |

응답에서 모니터링에 대한 `subscriptionOperation` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionOperation` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

먼저 `Install-Module Az.Subscription -AllowPrerelease`를 실행 하 여이 미리 보기 모듈을 설치 합니다. `-AllowPrerelease`가 작동하는지 확인하려면 [Get PowerShellGet Module](/powershell/scripting/gallery/installing-psget)에서 최신 버전의 PowerShellGet을 설치합니다.

아래 [AzSubscription](/powershell/module/az.subscription) 명령을 실행 하 여 첫 번째 단계 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 수집 된 `ObjectId` `<enrollmentAccountObjectId>`를 바꿉니다. 소유자를 지정 하려면 [사용자 개체 id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)알아보세요.

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `OfferType`   | yes      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `EnrollmentAccountObjectId`      | yes       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `Get-AzEnrollmentAccount`에서 가져온 GUID입니다. |
| `OwnerObjectId`      | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `OwnerSignInName`    | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다.|
| `OwnerApplicationId` | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `OwnerObjectId` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.|

모든 매개 변수의 전체 목록을 보려면 [New-AzSubscription](/powershell/module/az.subscription)을 참조하세요.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

먼저 `az extension add --name subscription`를 실행 하 여이 미리 보기 확장을 설치 합니다.

아래 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) 명령을 실행 하 여 `<enrollmentAccountObjectId>`을 첫 번째 단계에서 복사한 `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)로 바꿉니다. 소유자를 지정 하려면 [사용자 개체 id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)알아보세요.

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 예      | String | 구독의 표시 이름입니다. 지정되지 않은 경우 “Microsoft Azure 엔터프라이즈”와 같은 제품의 이름으로 설정됩니다.                                 |
| `offer-type`   | yes      | String | 구독의 제안입니다. EA에 대한 두 가지 옵션은 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)(프로덕션 사용) 및 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)(개발/테스트, [EA 포털을 사용하여 켜져야](https://ea.azure.com/helpdocs/DevOrTestOffer) 함)입니다.                |
| `enrollment-account-object-id`      | yes       | String | 구독이 생성되고 비용이 청구되는 등록 계정의 개체 ID입니다. 이 값은 `az billing enrollment-account list`에서 가져온 GUID입니다. |
| `owner-object-id`      | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 개체 ID입니다.  |
| `owner-upn`    | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 사용자의 이메일 주소입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다.|
| `owner-spn` | 예       | String | 만들 때 구독에서 RBAC 소유자로 추가하려는 모든 서비스 사용자의 애플리케이션 ID입니다. `owner-object-id` 대신 이 매개 변수를 사용할 수 있습니다. 이 매개 변수를 사용하는 경우 서비스 주체는 [디렉터리에 대한 읽기 액세스 권한](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)이 있어야 합니다.|

모든 매개 변수의 전체 목록을 보려면 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)를 참조하세요.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 엔터프라이즈 구독 생성 API의 제한 사항

- 이 API를 사용하여 Azure 엔터프라이즈 구독을 만들 수 있습니다.
- 등록 계정 당 구독 수는 200 개로 제한 됩니다. 그 후에는 해당 계정에 대 한 추가 구독을 Azure Portal 에서만 만들 수 있습니다. API를 통해 더 많은 구독을 만들려면 다른 등록 계정을 만드세요.
- 계정 소유자가 아니지만 RBAC를 통해 등록 계정에 추가 된 사용자는 Azure Portal에서 구독을 만들 수 없습니다.
- 만들려는 구독에 대한 테넌트를 선택할 수 없습니다. 구독은 항상 계정 소유자의 홈 테넌트에 만들어집니다. 구독을 다른 테넌트로 이동하려면 [구독 테넌트 변경](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.


## <a name="create-subscriptions-for-an-mca-account"></a>MCA 계정에 대 한 구독 만들기

### <a name="prerequisites"></a>사전 요구 사항

구독을 만들려면 청구 프로필 또는 청구 계정에 대 한 청구서 섹션 또는 소유자 또는 참가자 역할에 소유자, 참가자 또는 Azure 구독 작성자 역할이 있어야 합니다. 자세한 내용은 [구독 청구 역할 및 작업](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)을 참조하세요.

아래에 표시 된 예제에서는 REST Api를 사용 합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-billing-accounts-that-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

아래 요청을 통해 모든 청구 계정을 나열 합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 응답에는 사용자가 액세스할 수 있는 청구 계정이 나열 됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
`displayName` 속성을 사용 하 여 구독을 만들려는 청구 계정을 식별 합니다. 계정 agreeementType이 *MicrosoftCustomerAgreement*인지 확인 합니다. 계정의 `name`를 복사 합니다.  예를 들어 `Contoso` 청구 계정에 대 한 구독을 만들려는 경우 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`를 복사 합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="find-invoice-sections-to-create-subscriptions"></a>구독을 만들기 위한 청구서 섹션 찾기

구독 요금은 청구 프로필 청구서의 한 섹션에 표시 됩니다. 다음 API를 사용 하 여 Azure 구독을 만들 수 있는 권한이 있는 청구서 섹션 및 청구 프로필 목록을 가져옵니다.

`<billingAccountName>`를 첫 번째 단계(`name`)에서 복사한 ```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```로 바꿔서 다음 요청을 수행합니다.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API 응답에는 구독을 만들기 위한 액세스 권한이 있는 모든 청구서 섹션과 해당 청구 프로필이 나열 됩니다.

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

`invoiceSectionDisplayName` 속성을 사용 하 여 구독을 만들 청구서 섹션을 식별할 수 있습니다. `invoiceSectionId``billingProfileId`를 복사 하 고 청구서 섹션의 `skuId` 중 하나를 복사 합니다. 예를 들어 `Development` invoice 섹션에 대해 `Microsoft Azure plan` 유형의 구독을 만들려는 경우 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` 및 `0001`를 복사 합니다. 다음 단계에서 사용할 수 있도록이 값을 어딘가에 붙여넣습니다.

### <a name="create-a-subscription-for-an-invoice-section"></a>송장 섹션에 대 한 구독 만들기

다음 예에서는 *Development* invoice 섹션에 대해 *Microsoft Azure Plan* 형식의 *Dev Team subscription* 이라는 구독을 만듭니다. 구독은 *Contoso 재무의* 청구 프로필로 청구 되며 해당 청구서의 *개발* 섹션에 표시 됩니다.

`<invoiceSectionId>`를 두 번째 단계 (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```)에서 복사한 `invoiceSectionId`로 바꿔서 다음 요청을 수행 합니다. `billingProfileId`를 전달 하 고 API의 요청 매개 변수에서 두 번째 단계 로부터 복사 `skuId` 합니다. 소유자를 지정 하려면 [사용자 개체 id를 가져오는 방법을](grant-access-to-create-subscription.md#userObjectId)알아보세요.

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | yes      | String | 구독의 표시 이름입니다.|
| `billingProfileId`   | yes      | String | 구독의 요금을 청구 하는 청구 프로필의 ID입니다.  |
| `skuId` | yes      | String | Azure 계획의 유형을 결정 하는 sku ID입니다. |
| `owners`      | 예       | String | 생성 될 때 구독에서 RBAC 소유자로 추가 하려는 모든 사용자 또는 서비스 주체의 개체 ID입니다.  |
| `costCenter` | 예      | String | 구독과 관련 된 비용 센터입니다. 사용 중인 csv 파일에 표시 됩니다. |
| `managementGroupId` | 예      | String | 구독이 추가 될 관리 그룹의 ID입니다. 관리 그룹 목록을 가져오려면 [관리 그룹 LIST API](/rest/api/resources/managementgroups/list)를 참조 하세요. API에서 관리 그룹의 ID를 사용 합니다. |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>MPA 청구 계정에 대 한 구독 만들기

### <a name="prerequisites"></a>사전 요구 사항

청구 계정에 대 한 구독을 만들려면 조직의 클라우드 솔루션 공급자 계정에 전역 관리자 또는 관리 에이전트 역할이 있어야 합니다. 자세한 내용은 [파트너 센터-사용자 역할 및 사용 권한 할당](https://docs.microsoft.com/partner-center/permissions-overview)을 참조 하세요.

아래에 표시 된 예제에서는 REST Api를 사용 합니다. 현재 PowerShell 및 Azure CLI는 지원되지 않습니다.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>액세스 권한이 있는 청구 계정 찾기

액세스 권한이 있는 모든 청구 계정을 나열 하려면 아래 요청을 수행 합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 응답에는 청구 계정이 나열 됩니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
`displayName` 속성을 사용 하 여 구독을 만들려는 청구 계정을 식별 합니다. 계정 agreeementType이 *MicrosoftPartnerAgreement*인지 확인 합니다. 계정에 대 한 `name`를 복사 합니다. 예를 들어 `Contoso` 청구 계정에 대 한 구독을 만들려는 경우 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`를 복사 합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="find-customers-that-have-azure-plans"></a>Azure 요금제가 있는 고객 찾기

Azure 구독을 만들 수 있는 청구 계정의 모든 고객을 나열 하기 위해 첫 번째 단계 (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)에서 복사한 `name`로 `<billingAccountName>`를 대체 하 여 다음 요청을 수행 합니다.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API 응답은 Azure 요금제를 사용 하 여 청구 계정에 있는 고객을 나열 합니다. 이러한 고객에 대 한 구독을 만들 수 있습니다.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

`displayName` 속성을 사용 하 여 구독을 만들려는 고객을 식별할 수 있습니다. 고객에 대 한 `id`를 복사 합니다. 예를 들어 `Fabrikam toys`에 대 한 구독을 만들려는 경우 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`를 복사 합니다. 이후 단계에서 사용 하려면이 값을 어딘가에 붙여넣습니다.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>간접 공급자에 대 한 선택 사항: 고객에 대 한 대리점 가져오기

CSP 2 계층 모델의 간접 공급자 인 경우 고객에 대 한 구독을 만드는 동안 재판매인을 지정할 수 있습니다.

다음 요청을 수행 하 여 `<customerId>`를 두 번째 단계 (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `id`로 바꿔서 고객에 게 제공 되는 모든 대리점을 나열 합니다.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API 응답은 고객에 대 한 대리점을 나열 합니다.

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
`description` 속성을 사용 하 여 구독과 연결 될 재판매인을 식별 합니다. 대리점에 대 한 `resellerId`를 복사 합니다. 예를 들어 `Wingtip`를 연결 하려는 경우 `3xxxxx`를 복사 합니다. 다음 단계에서 사용할 수 있도록 이 값을 어딘가에 붙여 넣습니다.

### <a name="create-a-subscription-for-a-customer"></a>고객에 대 한 구독 만들기

다음 예에서는 *Fabrikam 장난감* 에 대해 *Dev Team subscription* 이라는 구독을 만들고이 구독에 *정문* 재판매인을 연결 합니다. T

`<customerId>`를 두 번째 단계 (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)에서 복사한 `id`로 바꿔서 다음 요청을 수행 합니다. API의 요청 매개 변수에서 두 번째 단계에서 복사 된 선택적 *resellerId* 를 전달 합니다.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| 요소 이름  | 필수 | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | yes      | String | 구독의 표시 이름입니다.|
| `skuId` | yes      | String | Azure 계획의 sku ID입니다. 유형 Microsoft Azure 계획의 구독에 대해 *0001* 사용 |
| `resellerId`      | 예       | String | 구독과 연결 될 재판매인의 MPN ID입니다.  |

응답에서 모니터링에 대한 `subscriptionCreationResult` 개체를 얻습니다. 구독 만들기가 완료되면 `subscriptionCreationResult` 개체는 구독 ID가 있는 `subscriptionLink` 개체를 반환합니다.

## <a name="next-steps"></a>다음 단계

* .NET을 사용 하 여 EA (기업계약) 구독을 만드는 방법에 대 한 예제는 [GitHub의 샘플 코드](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)를 참조 하세요.
* 구독을 만들었으므로 다른 사용자 및 서비스 주체에게 해당 기능을 부여할 수 있습니다. 자세한 내용은 [Azure 엔터프라이즈 구독 만들기에 대한 액세스 권한 부여(미리 보기)](grant-access-to-create-subscription.md)를 참조하세요.
* 관리 그룹을 사용하여 많은 수의 구독 관리에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](../../governance/management-groups/overview.md)을 참조하세요.
