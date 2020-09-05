---
title: 게시 된 앱에 대 한 사용자 지정 홈 페이지-Azure AD 응용 프로그램 프록시
description: Azure AD 애플리케이션 프록시 커넥터에 대한 기본 사항 제공
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155a91ded6c814e2d868e8edd4572459460d006f
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642064"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정

이 문서에서는 사용자를 사용자 지정 홈 페이지로 보내도록 앱을 구성 하는 방법을 설명 합니다. 응용 프로그램 프록시를 사용 하 여 앱을 게시 하는 경우 내부 URL을 설정 하지만 사용자에 게 먼저 표시 되어야 하는 페이지가 아닌 경우도 있습니다. 사용자가 앱에 액세스할 때 오른쪽 페이지를 사용할 수 있도록 사용자 지정 홈 페이지를 설정 합니다. 사용자가 설정 하는 사용자 지정 홈 페이지는 Azure Active Directory 내 앱 또는 Microsoft 365 앱 시작 관리자에서 앱에 액세스 하는지 여부에 관계 없이 표시 됩니다.

사용자가 앱을 시작 하면 기본적으로 게시 된 앱의 루트 도메인 URL로 전달 됩니다. 방문 페이지는 일반적으로 홈페이지 URL로 설정됩니다. 앱 사용자가 앱 내의 특정 페이지에 배치 하도록 하려면 Azure AD PowerShell 모듈을 사용 하 여 사용자 지정 홈 페이지 URL을 정의 합니다.

회사에서 사용자 지정 홈 페이지를 설정 하는 이유를 설명 하는 한 가지 시나리오는 다음과 같습니다.

- 회사 네트워크 내에서 사용자가 `https://ExpenseApp/login/login.aspx` 로그인 하 여 앱에 액세스 하기 위해 이동 합니다.
- 응용 프로그램 프록시가 폴더 구조의 최상위 수준에서 액세스 해야 하는 다른 자산 (예: 이미지)이 있으므로를 내부 URL로 사용 하 여 앱을 게시 합니다 `https://ExpenseApp` .
- 기본 외부 URL은 `https://ExpenseApp-contoso.msappproxy.net` 외부 사용자를 로그인 페이지로 사용 하지 않는입니다.
- `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`대신 홈 페이지 URL로 설정 하 여 외부 사용자가 로그인 페이지를 먼저 볼 수 있도록 하려고 합니다.

> [!NOTE]
> 사용자에 게 게시 된 앱에 대 한 액세스 권한을 부여 하면 앱이 [내 앱](../user-help/my-apps-portal-end-user-access.md) 및 [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)에 표시 됩니다.

## <a name="before-you-start"></a>시작하기 전에

홈페이지 URL을 설정하기 전에 다음 요구 사항에 유의하세요.

- 지정 하는 경로는 루트 도메인 URL의 하위 도메인 경로 여야 합니다.

  예를 들어 루트 도메인 URL이 인 경우 `https://apps.contoso.com/app1/` 구성 하는 홈 페이지 url은로 시작 해야 합니다 `https://apps.contoso.com/app1/` .

- 게시된 앱을 변경하는 경우 이로 인해 홈페이지 URL 값을 다시 설정할 수 있습니다. 나중에 앱을 업데이트할 경우 홈페이지 URL을 다시 확인하고 필요한 경우 업데이트해야 합니다.

Azure Portal 또는 PowerShell을 사용 하 여 홈 페이지 URL을 설정할 수 있습니다.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure Portal에서 홈페이지 변경

Azure AD 포털을 통해 앱의 홈 페이지 URL을 변경 하려면 다음 단계를 수행 합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory**를 선택한 다음 **앱 등록**합니다. 등록 된 앱 목록이 표시 됩니다.
1. 목록에서 앱을 선택 합니다. 등록 된 앱의 세부 정보를 표시 하는 페이지가 표시 됩니다.
1. **관리**에서 **브랜딩**을 선택 합니다.
1. 새 경로를 사용 하 여 **홈 페이지 URL**  을 업데이트 합니다.

   ![홈 페이지 URL 필드를 표시 하는 등록 된 앱에 대 한 브랜딩 페이지](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. **저장**을 선택합니다.

## <a name="change-the-home-page-with-powershell"></a>PowerShell을 사용하여 홈페이지 변경

PowerShell을 사용 하 여 앱의 홈 페이지를 구성 하려면 다음을 수행 해야 합니다.

1. Azure AD PowerShell 모듈을 설치합니다.
1. 앱의 ObjectId 값을 찾습니다.
1. PowerShell 명령을 사용 하 여 앱의 홈 페이지 URL을 업데이트 합니다.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

PowerShell을 사용하여 사용자 지정 홈페이지 URL을 정의하기 전에 Azure AD PowerShell 모듈을 설치합니다.Graph API 엔드포인트를 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)에서 패키지를 다운로드할 수 있습니다.

패키지를 설치하려면 다음 단계를 따르세요.

1. 표준 PowerShell 창을 열고 다음 명령을 실행합니다.

   ```powershell
   Install-Module -Name AzureAD
   ```

    관리자가 아닌 사용자로 명령을 실행하는 경우 `-scope currentuser` 옵션을 사용합니다.

1. 설치 하는 동안 Nuget.org에서 두 개의 패키지를 설치 하려면 **Y** 를 선택 합니다. 두 패키지가 모두 필요 합니다.

### <a name="find-the-objectid-of-the-app"></a>앱의 ObjectId 찾기

표시 이름 또는 홈 페이지에서 앱을 검색 하 여 앱의 ObjectId를 가져옵니다.

1. 동일한 PowerShell 창에서 Azure AD 모듈을 가져옵니다.

   ```powershell
   Import-Module AzureAD
   ```

1. 테넌트 관리자로 Azure AD 모듈에 로그인합니다.

   ```powershell
   Connect-AzureAD
   ```

1. 앱을 찾습니다. 이 예제에서는 PowerShell을 사용 하 여 표시 이름이 인 앱을 검색 하 여 ObjectId를 찾습니다 `SharePoint` .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   여기에 표시된 것과 비슷한 결과가 표시되어야 합니다. 다음 섹션에서 사용할 ObjectId GUID를 복사 합니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   또는 모든 앱의 목록을 가져오고, 특정 표시 이름 또는 홈 페이지를 사용 하 여 앱 목록을 검색 하 고, 앱이 검색 되 면 앱의 ObjectId를 복사할 수 있습니다.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>홈페이지 URL 업데이트

홈 페이지 URL을 만들고 해당 값으로 앱을 업데이트 합니다. 동일한 PowerShell 창을 계속 사용 하거나, 새 PowerShell 창을 사용 하는 경우를 사용 하 여 Azure AD 모듈에 다시 로그인 `Connect-AzureAD` 합니다. 그런 다음, 다음 단계를 수행합니다.

1. 이전 섹션에서 복사한 ObjectId 값을 저장할 변수를 만듭니다. 이 SharePoint 예제에서 사용 되는 ObjectId 값을 앱의 ObjectId 값으로 바꿉니다.

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 다음 명령을 실행 하 여 올바른 앱이 있는지 확인 합니다. 출력은 이전 섹션에서 본 출력과 동일 해야 합니다 ([응용 프로그램의 ObjectId 찾기](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. 변경 내용을 저장하기 위해 비어 있는 애플리케이션 개체를 만듭니다.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. 홈페이지 URL을 원하는 값으로 설정합니다. 값은 게시된 앱의 하위 도메인 경로여야 합니다. 예를 들어 홈페이지 URL을 `https://sharepoint-iddemo.msappproxy.net/`에서 `https://sharepoint-iddemo.msappproxy.net/hybrid/`로 변경하면 앱 사용자가 사용자 지정 홈페이지로 바로 이동합니다.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. 홈 페이지의 업데이트를 수행 합니다.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 변경이 성공적으로 완료 되었는지 확인 하려면 2 단계에서 다음 명령을 다시 실행 합니다.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   이 예에서는 이제 출력이 다음과 같이 표시 됩니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. 앱을 다시 시작 하 여 홈 페이지가 예상 대로 첫 번째 화면으로 표시 되는지 확인 합니다.

> [!NOTE]
> 앱에 대한 변경 내용으로 인해 홈페이지 URL을 다시 설정할 수도 있습니다. 홈 페이지 URL을 다시 설정하는 경우 이 섹션의 단계를 반복하여 재설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정](application-proxy-integrate-with-sharepoint-server.md)
- [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)
