---
title: API 필수 구성 요소 | Microsoft Docs
description: 클라우드 파트너 포털 API 사용을 위한 필수 구성 요소에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0d743d89c344ce9cb6eec116ed9c3e0cbe56f86e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720889"
---
<a name="api-prerequisites"></a>API 필수 구성 요소
================

클라우드 파트너 포털 API를 사용하려면 필요한 두 가지 필수 프로그래밍 자산은 서비스 주체와 Azure AD(Active Directory) 액세스 토큰입니다.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에서 서비스 주체 만들기
----------------------------------------------------------------

먼저 Azure AD 테넌트에서 서비스 주체를 만들어야 합니다. 이 테넌트에는 클라우드 파트너 포털의 자체 권한 집합이 할당됩니다. 코드는 개인 자격 증명을 사용하는 대신 이 테넌트를 사용하여 API를 호출합니다.  서비스 주체를 만드는 방법의 전체 설명은 [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)를 참조하세요.


<a name="add-the-service-principal-to-your-account"></a>계정에 서비스 주체 추가
-----------------------------------------

테넌트에 서비스 주체를 만들었으므로 이제 클라우드 파트너 포털 계정에 해당 서비스 주체를 사용자로 추가할 수 있습니다. 서비스 주체는 일반 사용자처럼 포털의 참가자나 소유자일 수 있습니다.

서비스 주체를 추가하려면 다음 단계를 수행합니다.

1. 클라우드 파트너 포털에 로그온합니다. 
2. 왼쪽 메뉴 모음에서 **사용자**를 클릭하고 **사용자 추가**를 선택합니다.

   ![포털에 사용자 추가](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. **유형** 드롭다운에서 **서비스 주체**를 선택하고 다음 세부 정보를 추가합니다.

-   서비스 주체의 **표시 이름**(예: `spAccount`)
-   **애플리케이션 ID**. 이 식별자를 확인하려면 [Azure Portal](https://portal.azure.com)로 이동하여 **Azure Active Directory**를 클릭하고 **앱 등록**을 선택한 후에 앱을 클릭합니다.
-   Azure AD 테넌트의 **테넌트 ID**(**디렉터리 ID**)라고도 함. 이 식별자는 [Azure Portal](https://portal.azure.com)의 Azure Active Directory 페이지 내 **속성** 아래에서 확인할 수 있습니다.
-   서비스 주체 개체의 **개체 ID**. Azure Portal에서 이 식별자를 가져올 수 있습니다. **Azure Active Directory**로 이동하여 **앱 등록**을 선택하고 앱을 클릭한 후에 **로컬 디렉터리의 관리되는 애플리케이션** 아래에서 앱 이름을 클릭합니다. 그런 다음 **속성** 페이지로 이동하여 개체 ID를 확인합니다. 앱에 표시되는 초기 개체 ID가 아니라 관리되는 애플리케이션의 개체 ID를 가져와야 합니다.
-   계정과 연결된 **역할**(RBAC에 사용됨)

     ![포털에 관리되는 앱 추가](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. **추가**를 클릭하여 계정에 서비스 주체를 추가합니다.

   ![서비스 주체 추가](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Azure AD 액세스 토큰 가져오기
----------------------------

클라우드 파트너 포털 API는 인증 중에 다음 자산과 프로토콜을 사용합니다.

- 리소스 액세스 권한 요청을 위한 JWT(JSON 웹 토큰) 전달자 토큰
- ID 확인을 위한 OIDC([OpenID Connect](https://openid.net/connect/)) 프로토콜
- [Azure AD(Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)(ID 기관)

JWT 토큰을 프로그래밍 방식으로 가져오는 방식은 원칙적으로 다음의 두 가지입니다.

- .NET용 Microsoft 인증 라이브러리([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet))를 사용합니다.  .NET 개발자용의 수준이 높은 방식입니다. 
- Azure AD oauth **token** 엔드포인트에 대해 다음 형식으로 **HTTP POST** 요청을 수행합니다.

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

이제 API 요청에 대한 권한 부여 헤더의 일부로 이 토큰을 전달할 수 있습니다.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> 이 참조에 나와 있는 모든 API에서는 권한 부여 헤더가 항상 전달된다고 가정하므로 헤더에 대해 별도로 언급하지는 않습니다.

요청에서 인증 오류가 발생하는 경우 [인증 오류 문제 해결](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)을 참조하세요.
