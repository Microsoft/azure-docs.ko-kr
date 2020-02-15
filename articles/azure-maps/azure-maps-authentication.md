---
title: 인증 방법 | Microsoft Azure 맵
description: 이 문서에서는 Azure Active Directory (Azure AD) 및 공유 키 인증에 대해 알아봅니다. 둘 다 Microsoft Azure Maps 서비스에 사용 됩니다. Azure Maps 구독 키를 가져오는 방법에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ee8b166077c64ae5e0f2cce18ee0bc77e8c996f4
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210126"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps 인증

Azure Maps는 공유 키 인증과 Azure Active Directory 인증 이라는 두 가지 요청 인증 방법을 지원 합니다. 이 문서에서는 Azure Maps 서비스의 구현을 안내 하는 데 도움이 되는 이러한 인증 방법을 설명 합니다.

## <a name="shared-key-authentication"></a>공유 키 인증

 Azure Maps 계정이 만들어진 후 기본 및 보조 키가 생성 됩니다. 공유 키 인증을 사용 하 여 Azure Maps를 호출할 때 기본 키를 구독 키로 사용 하는 것이 좋습니다. 공유 키 인증은 Azure Maps 계정에서 생성 된 키를 Azure Maps 서비스에 전달 합니다. Azure Maps 서비스에 대 한 각 요청에 대해 *구독 키* 를 URL에 매개 변수로 추가 합니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다.  

Azure Portal에서 키를 보는 방법에 대 한 자세한 내용은 [인증 관리](https://aka.ms/amauthdetails)를 참조 하세요.

> [!Tip]
> 키를 정기적으로 다시 생성하는 것이 좋습니다. 다른 키를 다시 생성 하는 동안 한 키로 연결을 유지할 수 있도록 두 개의 키가 제공 됩니다. 키를 다시 생성 하면 계정에 액세스 하는 모든 응용 프로그램을 새 키로 업데이트 해야 합니다.

## <a name="authentication-with-azure-active-directory-preview"></a>Azure Active Directory 인증(미리 보기)

이제 Azure Maps는 [Azure Active Directory (AZURE AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)를 사용 하 여 Azure Maps 서비스에 대 한 요청 인증을 제공 합니다. Azure AD는 [RBAC (역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview)를 비롯 한 id 기반 인증을 제공 합니다. RBAC는 Azure Maps 리소스에 대 한 사용자 수준, 그룹 수준 또는 응용 프로그램 수준 액세스 권한을 부여 하는 데 사용 됩니다. 다음 섹션에서는 Azure AD와 Azure Maps 통합의 개념 및 구성 요소에 대해 설명 합니다.

## <a name="authentication-with-oauth-access-tokens"></a>OAuth 액세스 토큰 인증

Azure Maps는 Azure Maps 계정을 포함하고 있는 Azure 구독과 연결된 Azure AD 테넌트에 대한 **OAuth 2.0** 액세스 토큰을 허용합니다. 또한 Azure Maps는 다음에 대 한 토큰을 허용 합니다.

* Azure AD 사용자
* 사용자가 위임한 권한을 사용 하는 파트너 응용 프로그램
* Azure 리소스에 대한 관리 ID

Azure Maps는 각 Azure Maps 계정에 대해 *고유 식별자(클라이언트 ID)* 를 생성합니다. 이 클라이언트 ID를 추가 매개 변수와 함께 사용 하는 경우 Azure AD에서 토큰을 요청할 수 있습니다. 토큰을 요청 하려면 Azure 환경에 따라 다음 표에 있는 값을 지정 합니다.

| Azure 환경   | Azure AD 토큰 끝점 |
| --------------------|-------------------------|
| Azure 공용        | https://login.microsoftonline.com |
| Azure Government    | https://login.microsoftonline.us |


Azure AD를 구성하고 Azure Maps에 대한 토큰을 요청하는 방법에 대한 자세한 내용은 [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)를 참조하세요.

Azure AD에 토큰을 요청하는 방법에 대한 일반적인 정보를 보려면 [인증이란?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)을 참조하세요.

## <a name="request-azure-map-resources-with-oauth-tokens"></a>OAuth 토큰을 사용하여 Azure Map 리소스 요청

Azure AD가 토큰을 받은 후에는 다음과 같은 필수 요청 헤더 집합을 사용 하 여 요청을 보냅니다 Azure Maps.

| 요청 헤더    |    값    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 권한 부여     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id`는 Azure Maps 인증 페이지에 표시되는 Azure Maps 계정 기반 GUID입니다.

OAuth 토큰을 사용하는 Azure Maps 경로 요청의 예는 다음과 같습니다.

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

클라이언트 ID를 보는 방법에 대한 내용은 [인증 세부 정보 보기](https://aka.ms/amauthdetails)를 참조하세요.

## <a name="control-access-with-rbac"></a>RBAC를 사용하여 액세스 제어

Azure AD에서 RBAC를 사용 하 여 보안 리소스에 대 한 액세스를 제어 합니다. Azure Maps 계정을 설정 하 고 Azure Maps Azure AD 테 넌 트를 등록 합니다. Azure Maps은 Azure 리소스에 대 한 관리 되는 id를 통해 개별 Azure AD 사용자, 그룹, 응용 프로그램, Azure 리소스 및 Azure 서비스에 대 한 읽기 액세스 제어를 지원 합니다. Azure Maps 포털 페이지에서 선택한 역할에 대해 RBAC를 설정할 수 있습니다.

![Azure Maps 데이터 판독기(미리 보기)](./media/azure-maps-authentication/concept.png)

RBAC 설정을 보는 방법에 대한 내용은 [Azure Maps에 대한 RBAC 구성 방법](https://aka.ms/amrbac)을 참조하세요.

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 리소스 및 Azure Maps에 대한 관리형 ID

[Azure 리소스에 대 한 관리 되](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 는 id는 Azure Maps 서비스에 액세스할 수 있는 권한을 부여 받을 수 있는 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다. 관리 id의 몇 가지 예에는 Azure App Service, Azure Functions 및 Azure Virtual Machines가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

* Azure AD 및 Azure Maps를 사용하여 애플리케이션을 인증하는 방법을 자세히 알아보려면 [Azure Maps의 인증 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)를 참조하세요.

* Azure Maps, 맵 컨트롤 및 Azure AD 인증에 대한 자세한 내용은 [Azure Maps 맵 컨트롤 사용](https://aka.ms/amaadmc)을 참조하세요.
