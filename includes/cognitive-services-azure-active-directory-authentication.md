---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: b08ffa79e012344cad6cf72df98a0f1ba5240ce0
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76508610"
---
## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory를 사용하여 인증

> [!IMPORTANT]
> 1. 현재 Computer Vision API, Face API, 텍스트 분석 API, 몰입 형 판독기, 폼 인식기, 변칙 탐지기 및 모든 Bing 서비스 (Bing Custom Search를 제외 하 고 Azure Active Directory (AAD)를 사용 하 여 **인증을 지원** 합니다.
> 2. AAD 인증은 항상 Azure 리소스의 사용자 지정 하위 도메인 이름과 함께 사용 해야 합니다. [지역 엔드포인트](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) 은 AAD 인증을 지원 하지 않습니다.

이전 섹션에서는 단일 서비스 또는 다중 서비스 구독 키 중 하나를 사용 하 여 Azure Cognitive Services에 대해 인증 하는 방법을 살펴보았습니다. 이러한 키는 개발을 시작할 수 있는 빠르고 쉬운 경로를 제공 하지만, 역할 기반 액세스 제어를 요구 하는 보다 복잡 한 시나리오에서는 간단 합니다. AAD (Azure Active Directory를 사용 하 여 인증 하는 데 필요한 사항을 살펴보겠습니다.

다음 섹션에서는 Azure Cloud Shell 환경 또는 Azure CLI를 사용 하 여 하위 도메인을 만들고, 역할을 할당 하 고, Azure Cognitive Services를 호출 하는 전달자 토큰을 가져옵니다. 중단 되는 경우 Azure Cloud Shell/Azure CLI의 각 명령에 대해 사용 가능한 모든 옵션을 사용 하 여 각 섹션에 링크가 제공 됩니다.

### <a name="create-a-resource-with-a-custom-subdomain"></a>사용자 지정 하위 도메인을 사용 하 여 리소스 만들기

첫 번째 단계는 사용자 지정 하위 도메인을 만드는 것입니다. 사용자 지정 하위 도메인 이름이 없는 기존 Cognitive Services 리소스를 사용 하려는 경우 [사용자 지정 하위 도메인 Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) 의 지침에 따라 리소스에 대한 사용자 지정 하위 도메인을 사용 하도록 설정 합니다.

1. Azure Cloud Shell를 열어 시작 합니다. 그런 다음 [구독을 선택 합니다](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0).

   ```azurecli-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 다음으로 사용자 지정 하위 도메인을 사용 하 여 [Cognitive Services 리소스를 만듭니다](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) . 하위 도메인 이름은 전역적으로 고유 해야 하며 특수 문자 (예: ".", "!", ",")를 포함할 수 없습니다.

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 성공 하는 경우 **엔드포인트** 은 리소스에 고유한 하위 도메인 이름을 표시 해야 합니다.


### <a name="assign-a-role-to-a-service-principal"></a>서비스 주체에 역할 할당

이제 리소스와 연결 된 사용자 지정 하위 도메인이 있으므로 서비스 주체에 역할을 할당 해야 합니다.

> [!NOTE]
> AAD 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있다는 점에 유의 하세요.

1. 먼저 [AAD 응용 프로그램](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)을 등록 하겠습니다.

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   다음 단계에서 **ApplicationId** 가 필요 합니다.

2. 다음으로, AAD 응용 프로그램에 대한 [서비스 주체를 만들어야](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) 합니다.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Azure Portal에 응용 프로그램을 등록 하는 경우이 단계가 완료 됩니다.

3. 마지막 단계는 ["Cognitive Services 사용자" 역할](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) 을 서비스 사용자 (리소스로 범위 지정)에 할당 하는 것입니다. 역할을 할당 하 여 서비스 사용자에 게이 리소스에 대한 액세스 권한을 부여 합니다. 구독에서 여러 리소스에 대한 동일한 서비스 사용자 액세스 권한을 부여할 수 있습니다.
   >[!NOTE]
   > 서비스 주체의 ObjectId가 사용 되며 응용 프로그램의 ObjectId는 사용 되지 않습니다.
   > ACCOUNT_ID은 사용자가 만든 Cognitive Services 계정의 Azure 리소스 Id입니다. Azure Portal에서 리소스의 "속성"에서 Azure 리소스 Id를 찾을 수 있습니다.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>샘플 요청

이 샘플에서는 암호를 사용 하 여 서비스 사용자를 인증 합니다. 그런 다음 제공 된 토큰을 사용 하 여 Computer Vision API를 호출 합니다.

1. **TenantId**가져오기:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 토큰 가져오기:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Computer Vision API를 호출 합니다.
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

또는 인증서를 사용 하 여 서비스 사용자를 인증할 수 있습니다. 서비스 사용자 외에도 다른 AAD 응용 프로그램을 통해 위임 된 사용 권한을 사용 하 여 사용자 보안 주체를 지원 합니다. 이 경우 암호 또는 인증서 대신 토큰을 획득 하는 경우 사용자에 게 2 단계 인증을 요구 하는 메시지가 표시 됩니다.
