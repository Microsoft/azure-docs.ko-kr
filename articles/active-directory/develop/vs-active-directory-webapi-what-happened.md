---
title: Azure AD에 연결할 때 WebAPI 프로젝트에 대 한 변경 내용
description: Visual Studio를 사용 하 여 Azure AD에 연결할 때 WebAPI 프로젝트의 상황을 설명 합니다.
author: ghogen
manager: jillfra
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 595a89ca58e970a9c886d0b6c2dd05aecd1411ef
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159389"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>내 WebAPI 프로젝트(Visual Studio Azure Active Directory 연결 서비스)의 변경 내용

> [!div class="op_single_selector"]
> - [시작](vs-active-directory-webapi-getting-started.md)
> - [변경된 내용](vs-active-directory-webapi-what-happened.md)

이 아티클에서는 [Visual Studio를 사용하여 Azure Active Directory의 연결된 서비스](vs-active-directory-add-connected-service.md)를 추가할 때 ASP.NET WebAPI, ASP.NET 단일 페이지 애플리케이션 및 ASP.NET Azure API 프로젝트의 정확한 변경 내용을 설명합니다. 이는 Visual Studio 2015의 ASP.NET Azure 모바일 서비스 프로젝트에도 적용됩니다.

연결된 서비스를 사용한 작업에 대한 자세한 내용은 [시작](vs-active-directory-webapi-getting-started.md)을 참조하세요.

## <a name="added-references"></a>추가된 참조

프로젝트 파일(*.NET 참조) 및 `packages.config`(NuGet 참조)에 영향을 줍니다.

| Type | 참조 |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

**디렉터리 데이터 읽기** 옵션을 선택한 경우 추가 참조는 다음과 같습니다.

| Type | 참조 |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer(Visual Studio 2015만 해당) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015만 해당) |
| .NET; NuGet | System.Spatial |

다음 참조가 제거됩니다(Visual Studio 2015와 마찬가지로 ASP.NET 4 프로젝트만 해당).

| Type | 참조 |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>프로젝트 파일 변경 내용

- `IISExpressSSLPort` 속성을 고유 번호로 설정합니다.
- `WebProject_DirectoryAccessLevelKey`디렉터리 데이터 읽기**옵션을 선택한 경우** 속성을 0 또는 1로 설정합니다.
- `IISUrl` 속성을 `https://localhost:<port>/`로 설정합니다. 여기서 `<port>`는 `IISExpressSSLPort` 값과 일치합니다.

## <a name="webconfig-or-appconfig-changes"></a>web.config 또는 app.config 변경 내용

- 다음 구성 항목을 추가했습니다.

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017만 해당: `<appSettings>`" 아래에 다음 항목도 추가했습니다.

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- `<dependentAssembly>`에 대한 `<runtime><assemblyBinding>` 노드 아래에 `System.IdentityModel.Tokens.Jwt` 요소를 추가했습니다.

- **디렉터리 데이터 읽기** 옵션을 선택한 경우 `<appSettings>` 아래에 다음 구성 항목이 추가됩니다.

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>코드 변경 및 추가

- `[Authorize]` 특성을 `Controllers/ValueController.cs` 및 다른 기존 컨트롤러에 추가했습니다.

- Azure AD 인증을 위한 시작 논리를 포함하는 인증 시작 클래스 `App_Start/Startup.Auth.cs`를 추가하거나 적절히 수정했습니다. **디렉터리 데이터 읽기** 옵션을 선택한 경우 이 파일에 OAuth 코드를 수신하고 액세스 토큰으로 교환하는 코드도 포함됩니다.

- (ASP.NET 4 앱이 포함된 Visual Studio 2015만 해당) `App_Start/IdentityConfig.cs`를 제거하고 `Controllers/AccountController.cs`, `Models/IdentityModel.cs` 및 `Providers/ApplicationAuthProvider.cs`를 추가했습니다.

- Visual Studio가 연결된 서비스의 추가를 추적하는 데 사용하는 정보가 포함된 `Connected Services/AzureAD/ConnectedService.json`(Visual Studio 2017) 또는 `Service References/Azure AD/ConnectedService.json`(Visual Studio 2015)을 추가했습니다.

### <a name="file-backup-visual-studio-2015"></a>파일 백업(Visual Studio 2015)

Visual Studio 2015는 연결된 서비스를 추가할 때 변경 및 제거된 파일을 백업합니다. 영향을 받는 모든 파일은 `Backup/AzureAD` 폴더에 저장됩니다. Visual Studio 2017은 백업을 만들지 않습니다.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Azure 변경 내용

- 연결된 서비스를 추가할 때 선택한 도메인에 Azure AD 애플리케이션이 생성됩니다.
- 해당 옵션이 선택된 경우 **디렉터리 데이터 읽기** 권한을 포함하도록 앱을 업데이트했습니다.

[Azure Active Directory에 대해 자세히 알아보세요](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 인증 시나리오](authentication-scenarios.md)
- [ASP.NET 웹앱에 Microsoft에 로그인 추가](quickstart-v2-aspnet-webapp.md)
