---
title: 서비스 주체를 사용 하 여 Windows 가상 데스크톱 (클래식)에 대 한 관리 도구 배포-Azure
description: PowerShell을 사용 하 여 Windows 가상 데스크톱 (클래식)에 대 한 관리 도구를 배포 하는 방법
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: d7219751d584eb458cded9f4e30cccb1439dfa1b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069020"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>PowerShell을 사용 하 여 Windows 가상 데스크톱 (클래식) 관리 도구 배포

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 지원하지 않는 Windows Virtual Desktop(클래식)에 적용됩니다.

이 문서에서는 PowerShell을 사용하여 관리 도구를 배포하는 방법을 보여줍니다.

## <a name="important-considerations"></a>중요 고려 사항

Azure AD(Azure Active Directory) 테넌트의 구독마다 별도의 관리 도구를 배포해야 합니다. 이 도구는 Azure AD B2B(기업 간) 시나리오를 지원하지 않습니다.

이 관리 도구는 샘플입니다. Microsoft에서 중요한 보안 및 품질 업데이트를 제공할 것입니다. [소스 코드는 GitHub에서 받을 수 있습니다](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). 고객 또는 파트너는 모두 이 도구를 사용자 지정하여 비즈니스 요구 사항을 충족하는 것이 좋습니다.

다음 브라우저는 관리 도구와 호환됩니다.

- Google Chrome 68 이상
- Microsoft Edge 40.15063 이상
- Mozilla Firefox 52.0 이상
- Safari 10 이상(macOS에만 해당)

## <a name="what-you-need-to-deploy-the-management-tool"></a>관리 도구를 배포하는 데 필요한 사항

관리 도구를 배포하기 전에 앱 등록을 만들고 관리 UI를 배포하는 Azure AD(Azure Active Directory) 사용자가 필요합니다. 이 사용자가 다음 조건을 충족해야 합니다.

- Azure 구독에서 리소스를 만드는 데 필요한 권한 보유
- Azure AD 애플리케이션을 만드는 데 필요한 권한 보유 [필요한 권한](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)의 지침에 따라 사용자에게 필요한 권한이 있는지 확인하려면 다음 단계를 수행합니다.

관리 도구를 배포하고 구성한 후에는 사용자에게 관리 UI를 시작하여 모든 기능이 작동하는지 확인하도록 요청하는 것이 좋습니다. 관리 UI를 시작하는 사용자에게는 Windows Virtual Desktop 테넌트를 보거나 편집할 수 있는 역할 할당이 있어야 합니다.

## <a name="set-up-powershell"></a>PowerShell 설정

Az 및 Azure AD PowerShell 모듈 모두에 로그인하여 시작하세요. 로그인 방법은 다음과 같습니다.

1. 관리자 권한으로 PowerShell을 열고 PowerShell 스크립트를 저장한 디렉터리로 이동합니다.
2. 다음 cmdlet을 실행하여 관리 도구를 만드는 데 사용할 Azure 구독에 대한 소유자 또는 기여자 권한이 있는 계정을 사용하여 Azure에 로그인합니다.

    ```powershell
    Login-AzAccount
    ```

3. 다음 cmdlet을 실행하여 Az PowerShell 모듈에 사용한 것과 동일한 계정으로 Azure AD에 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

4. 그런 다음, RDS 템플릿 GitHub 리포지토리에서 두 PowerShell 스크립트를 저장한 폴더로 이동합니다.

로그인하는 동안 추가 PowerShell cmdlet을 실행하기 위해 로그인하는 데 사용한 PowerShell 창을 열어 둡니다.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory 앱 등록 만들기

관리 도구를 성공적으로 배포하고 구성하려면 먼저 [RDS 템플릿 GitHub 리포지토리](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)에서 다음 PowerShell 스크립트를 다운로드해야 합니다.

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

다음 명령을 실행하여 필수 API 권한으로 앱 등록을 만듭니다.

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

이제 Azure AD 앱 등록을 완료했으므로 관리 도구를 배포할 수 있습니다.

## <a name="deploy-the-management-tool"></a>관리 도구 배포

다음 PowerShell 명령을 실행하여 관리 도구를 배포하고 방금 만든 서비스 사용자와 연결합니다.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

웹앱을 만든 후에는 사용자를 로그인시킬 리디렉션 URI를 Azure AD 애플리케이션에 추가해야 합니다.

## <a name="set-the-redirect-uri"></a>리디렉션 URI 설정

다음 PowerShell 명령을 실행하여 웹앱 URL을 검색하고 인증 리디렉션 URI(회신 URL이라고도 함)로 설정합니다.

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

이제 리디렉션 URI를 추가했으므로 관리 도구가 API 백 엔드 서비스와 상호 작용할 수 있도록 API URL을 업데이트해야 합니다.

## <a name="update-the-api-url-for-the-web-application"></a>웹 애플리케이션의 API URL 업데이트

다음 스크립트를 실행하여 웹 애플리케이션 프런트 엔드에서 API URL 구성을 업데이트합니다.

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

이제 관리 도구 웹앱을 완전히 구성했으므로 Azure AD 애플리케이션을 확인하고 동의를 제공해야 합니다.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Azure AD 애플리케이션을 확인하고 동의 제공

Azure AD 애플리케이션을 확인하고 동의를 제공하려면 다음을 수행합니다.

1. 인터넷 브라우저를 열고 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal 상단에 있는 검색 창에서 **앱 등록**을 검색하고 **서비스** 아래에서 항목을 선택합니다.
3. **모든 애플리케이션**을 선택하고 [Azure Active Directory 앱 등록 만들기](#create-an-azure-active-directory-app-registration)에서 PowerShell 스크립트에 제공한 고유한 앱 이름을 검색합니다.
4. 브라우저 왼쪽 패널에서 **인증**을 선택하고 다음 그림에 표시된 것처럼 리디렉션 URI가 관리 도구의 웹앱 URL과 동일한지 확인합니다.

   [ ![리디렉션 URI가 입력된 인증 페이지](../media/management-ui-redirect-uri-inline.png) ](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. 왼쪽 패널에서 **API 사용 권한**을 선택하여 사용 권한이 추가되었는지 확인합니다. 전역 관리자인 경우 **`tenantname`에 대한 관리자 동의 부여**를 선택하고 대화 상자 프롬프트에 따라 조직의 관리자 동의를 제공합니다.

    [ ![API 사용 권한 페이지](../media/management-ui-permissions-inline.png) ](../media/management-ui-permissions-expanded.png#lightbox)

이제 관리 도구 사용을 시작할 수 있습니다.

## <a name="use-the-management-tool"></a>관리 도구 사용

이제 관리 도구를 설정했으므로 언제 어디서든 시작할 수 있습니다. 이 도구를 시작하는 방법은 다음과 같습니다.

1. 웹 브라우저에서 웹앱의 URL을 엽니다. URL을 기억할 수 없는 경우 Azure에 로그인하고 관리 도구에 배포한 앱 서비스를 찾은 다음, URL을 선택할 수 있습니다.
2. Windows Virtual Desktop 자격 증명을 사용하여 로그인합니다.

   > [!NOTE]
   > 관리 도구를 구성하는 동안 관리자 동의를 부여하지 않은 경우에는 로그인하는 각 사용자가 고유한 사용자 동의를 제공해야 해당 도구를 사용할 수 있습니다.

3. 테넌트 그룹을 선택하라는 메시지가 표시되면 드롭다운 목록에서 **기본 테넌트 그룹**을 선택합니다.
4. **기본 테넌트 그룹**을 선택하면 창의 왼쪽에 메뉴가 나타납니다. 이 메뉴에서 테넌트 그룹의 이름을 찾아서 선택합니다.

   > [!NOTE]
   > 사용자 지정 테넌트 그룹이 있는 경우 드롭다운 목록에서 선택하지 말고 수동으로 이름을 입력합니다.

## <a name="report-issues"></a>문제 보고

관리 도구 또는 기타 Windows Virtual Desktop 도구에 문제가 발생하는 경우 [Remote Desktop Services용 Azure Resource Manager 템플릿](https://github.com/Azure/RDS-Templates/blob/master/README.md)의 지침에 따라 GitHub에 보고합니다.

## <a name="next-steps"></a>다음 단계

관리 도구를 배포하고 연결하는 방법을 배웠으므로, 이제 Azure Service Health를 사용하여 서비스 이슈와 상태 권고를 모니터링하는 방법을 알아볼 수 있습니다. 자세한 내용은 [서비스 경고 설정 자습서](set-up-service-alerts-2019.md)를 참조하세요.
