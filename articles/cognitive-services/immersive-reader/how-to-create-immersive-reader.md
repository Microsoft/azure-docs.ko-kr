---
title: 몰입형 판독기 리소스 만들기
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 하위 도메인을 사용 하 여 새 몰입 형 판독기 리소스를 만든 다음 Azure 테 넌 트에서 Azure AD를 구성 하는 방법을 보여 줍니다.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 66a2fde47f71536661431959b957246e28c81d6a
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639813"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>몰입 형 판독기 리소스 만들기 및 Azure Active Directory 인증 구성

이 문서에서는 몰입 형 판독기 리소스를 만들고 Azure Active Directory (Azure AD) 인증을 구성 하는 스크립트를 제공 합니다. 이 스크립트나 포털에서 몰입 형 판독기 리소스를 만들 때마다 Azure AD 사용 권한으로 구성 해야 합니다. 이 스크립트는이에 도움이 됩니다.

이 스크립트는 모든 필요한 몰입 형 판독기 및 Azure AD 리소스를 한 번에 만들고 구성 하도록 디자인 되었습니다. 그러나 기존 몰입 형 판독기 리소스에 대해 Azure AD 인증을 구성할 수도 있습니다. 예를 들어, Azure Portal에서 이미 만든 경우에는이를 이미 만든 것입니다.

일부 고객의 경우 개발 및 프로덕션에 대해 또는 서비스를 배포 하는 여러 다른 지역에 대해 여러 몰입 형 판독기 리소스를 만들어야 할 수 있습니다. 이러한 경우 스크립트를 여러 번 사용 하 여 여러 가지 몰입 형 판독기 리소스를 만들고 Azure AD 권한으로 구성할 수 있습니다.

이 스크립트는 유연 하 게 설계 되었습니다. 먼저 구독에서 기존 몰입 형 판독기 및 Azure AD 리소스를 검색 하 고, 필요한 경우에만 해당 리소스를 만듭니다. 몰입 형 판독기 리소스를 처음 만들 때 스크립트는 필요한 모든 작업을 수행 합니다. 포털에서 만든 기존 몰입 형 판독기 리소스에 대해 Azure AD를 구성 하는 데만 사용 하려는 경우에도 마찬가지입니다. 또한 다양 한 몰입 형 판독기 리소스를 만들고 구성 하는 데 사용할 수 있습니다.

## <a name="set-up-powershell-environment"></a>PowerShell 환경 설정

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)를 열어 시작 합니다. Cloud Shell를 왼쪽 위 드롭다운에서 PowerShell로 설정 하거나를 입력 하 여 확인 `pwsh` 합니다.

1. 다음 코드 조각을 복사 하 여 셸에 붙여 넣습니다.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. `Create-ImmersiveReaderResource`매개 변수를 적절 하 게 제공 하 여 함수를 실행 합니다.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
      -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_Expiration>'
    ```

    | 매개 변수 | 주석 |
    | --- | --- |
    | SubscriptionName |몰입 형 판독기 리소스에 사용할 Azure 구독의 이름입니다. 리소스를 만들려면 구독이 있어야 합니다. |
    | ResourceName |  '-'가 첫 번째 또는 마지막 문자가 아닌 경우 영숫자 여야 하며 '-'를 포함할 수 있습니다. 길이는 63 자를 초과할 수 없습니다.|
    | ResourceSubdomain |사용자 지정 하위 도메인은 몰입 형 판독기 리소스에 필요 합니다. 하위 도메인은 몰입 형 판독기 서비스를 호출 하 여 판독기를 시작할 때 SDK에서 사용 됩니다. 하위 도메인은 전역적으로 고유 해야 합니다. '-'가 첫 번째 또는 마지막 문자가 아닌 경우 하위 도메인은 영숫자 여야 하 고 '-'를 포함할 수 있습니다. 길이는 63 자를 초과할 수 없습니다. 리소스가 이미 존재 하는 경우이 매개 변수는 선택 사항입니다. |
    | ResourceSKU |옵션: `S0` . 사용 가능한 각 SKU에 대 한 자세한 내용은 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) 를 참조 하세요. 리소스가 이미 존재 하는 경우이 매개 변수는 선택 사항입니다. |
    | ResourceLocation |옵션: `eastus` , `eastus2` , `southcentralus` , `westus` , `westus2` , `australiaeast` `southeastasia` `centralindia` `japaneast` `northeurope` `uksouth` ,,,,,, `westeurope` 리소스가 이미 존재 하는 경우이 매개 변수는 선택 사항입니다. |
    | ResourceGroupName |리소스는 구독 내의 리소스 그룹에 만들어집니다. 기존 리소스 그룹의 이름을 제공 합니다. 리소스 그룹이 아직 없는 경우이 이름을 가진 새 리소스 그룹이 생성 됩니다. |
    | ResourceGroupLocation |리소스 그룹이 없는 경우 그룹을 만들 위치를 제공 해야 합니다. 위치 목록을 찾으려면를 실행 `az account list-locations` 합니다. 반환 된 결과의 *이름* 속성 (공백 없음)을 사용 합니다. 리소스 그룹이 이미 있는 경우이 매개 변수는 선택 사항입니다. |
    | AADAppDisplayName |Azure Active Directory 응용 프로그램 표시 이름입니다. 기존 Azure AD 응용 프로그램을 찾을 수 없는 경우이 이름을 가진 새 응용 프로그램이 생성 됩니다. Azure AD 응용 프로그램이 이미 있는 경우이 매개 변수는 선택 사항입니다. |
    | AADAppIdentifierUri |Azure AD 앱에 대 한 URI입니다. 기존 Azure AD 앱을 찾을 수 없는 경우이 URI를 사용 하는 새 앱이 만들어집니다. 예들 들어 `https://immersivereaderaad-mycompany`입니다. |
    | AADAppClientSecret |사용자가 만드는 암호는 나중에 몰입 형 판독기를 시작 하기 위해 토큰을 획득 하는 경우 나중에 인증 하는 데 사용 됩니다. 암호는 16 자 이상 이어야 하 고 하나 이상의 특수 문자를 포함 하 고 하나 이상의 숫자 문자를 포함 해야 합니다. 이 리소스를 만든 후 Azure AD 응용 프로그램 클라이언트 암호를 관리 하려면 https://portal.azure.com `[AADAppDisplayName]` 아래 "azure ad 응용 프로그램 비밀 관리" 스크린샷에서 볼 수 있는 것 처럼 azure ad 응용 프로그램 클라이언트 암호를 방문 하 여 > Azure Active Directory-> 앱 등록 >-> 인증서 및 비밀 블레이드-> 클라이언트 암호 섹션으로 이동 하세요. |
    | AADAppClientSecretExpiration |가 만료 되는 날짜 또는 날짜/시간 `[AADAppClientSecret]` 입니다 (예: ' 2020-12-31T11:59:59 + 00:00 ' 또는 ' 2020-12-31 '). |

    Azure AD 응용 프로그램 비밀 관리

    ![Azure Portal 인증서 및 비밀 블레이드](./media/client-secrets-blade.png)

1. 나중에 사용 하기 위해 JSON 출력을 텍스트 파일로 복사 합니다. 출력은 다음과 비슷합니다.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>다음 단계

* Node.js를 사용하여 몰입형 판독기 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Node.js 빠른 시작](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)을 살펴보세요.
* [Android 자습서](./tutorial-android.md)를 보고 Java 또는 Android용 Kotlin을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* [iOS 자습서](./tutorial-ios.md)를 보고 iOS용 Swift를 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인합니다.
* Python을 사용하여 몰입형 리더 SDK를 통해 수행할 수 있는 다른 작업을 확인하려면 [Python 자습서](./tutorial-python.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기




