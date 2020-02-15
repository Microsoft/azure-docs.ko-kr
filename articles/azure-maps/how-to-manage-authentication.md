---
title: 인증 관리 | Microsoft Azure 맵
description: Microsoft Azure 맵에서 인증을 관리 하려면 Azure Portal을 사용 합니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ef88f9794fcba8c702ee793044d5482912ec3d89
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210007"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만든 후에는 Azure Active Directory (Azure AD) 인증 및 공유 키 인증을 지원 하기 위해 클라이언트 ID와 키가 생성 됩니다.

## <a name="view-authentication-details"></a>인증 정보 보기

Azure Maps 계정을 만든 후에는 기본 키와 보조 키가 생성 됩니다. [공유 키 인증을 사용 하 여 Azure Maps를 호출](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)하는 경우 기본 키를 구독 키로 사용 하는 것이 좋습니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다. 자세한 내용은 [Azure Maps의 인증](https://aka.ms/amauth)을 참조 하세요.

Azure Portal에서 인증 세부 정보를 볼 수 있습니다. 계정에서 **설정** 메뉴의 **인증**을 선택 합니다.

![인증 세부 정보](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Azure AD 앱 등록 및 구성

1. Azure Portal의 Azure 서비스 목록에서 **Azure Active Directory** > 를 선택 하 **앱 등록** **새 등록**을 > 합니다.  

    ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

1. 앱을 이미 등록 한 경우에는 다음 단계를 계속 진행 합니다. 앱을 등록 하지 않은 경우 **이름을**입력 하 고 **지원 계정 유형을**선택한 다음 **등록**을 선택 합니다.  

    ![앱 등록 세부 정보](./media/how-to-manage-authentication/app-create.png)

1. Azure Maps에 위임 된 API 권한을 할당 하려면 응용 프로그램으로 이동 합니다. 그런 다음 **앱 등록**에서 **API 권한** > **사용 권한 추가**를 선택 합니다. **내 조직에서 사용 하는 api**에서를 검색 하 고 **Azure Maps**를 선택 합니다.

    ![앱 API 권한 추가](./media/how-to-manage-authentication/app-permissions.png)

1. **액세스 Azure Maps**옆의 확인란을 선택 하 고 **사용 권한 추가**를 선택 합니다.

    ![앱 API 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

1. 인증 방법에 따라 다음 단계 중 하나를 완료 합니다. 

    * 응용 프로그램에서 Azure Maps 웹 SDK를 사용 하 여 사용자 토큰 인증을 사용 하는 경우 `oauth2AllowImplicitFlow`를 사용 하도록 설정 합니다. 이 기능을 사용 하도록 설정 하려면 앱 등록의 **매니페스트** 섹션에서 `oauth2AllowImplicitFlow`을 true로 설정 합니다. 
    
       ![앱 매니페스트](./media/how-to-manage-authentication/app-manifest.png)

    * 응용 프로그램에서 서버 또는 응용 프로그램 인증을 사용 하는 경우 앱 등록 페이지에서 **인증서 & 암호**로 이동 합니다. 그런 다음, 공개 키 인증서를 업로드 하거나 **새 클라이언트**암호를 선택 하 여 암호를 만듭니다. 
    
       ![클라이언트 비밀 만들기](./media/how-to-manage-authentication/app-keys.png)

        암호를 만드는 경우 **추가**를 선택한 후 암호를 복사 하 고 안전 하 게 저장 합니다. 이 암호를 사용 하 여 Azure AD에서 토큰을 가져옵니다.

       ![클라이언트 암호 추가](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Azure Maps에 역할 기반 액세스 제어 권한 부여

Azure AD 테 넌 트에 Azure Maps 계정을 연결한 후 액세스 제어를 허용할 수 있습니다. 사용자, 그룹 또는 응용 프로그램을 하나 이상의 Azure Maps 액세스 제어 역할에 할당 하 여 RBAC ( *역할 기반 액세스 제어* )를 부여 합니다. 

1. **Azure Maps 계정**으로 이동 합니다. **액세스 제어 (IAM)**  > **역할 할당**을 선택 합니다.

    ![RBAC 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. **역할 할당** 탭의 **역할**아래에서 **Azure Maps Date Reader (미리 보기)** 를 선택 합니다. **액세스 할당**에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**를 선택합니다. 사용자 또는 응용 프로그램을 선택 합니다. 그런 다음 **저장**을 선택합니다.

    ![역할 할당 추가](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>사용 가능한 Azure Maps RBAC 역할 보기

Azure Maps 사용할 수 있는 RBAC 역할을 보려면 **Access control (IAM)** 로 이동 합니다. **역할**을 선택한 다음 *Azure Maps*로 시작 하는 역할을 검색 합니다. 이러한 Azure Maps 역할은 액세스 권한을 부여할 수 있는 역할입니다.

![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>RBAC Azure Maps 보기

RBAC는 세분화 된 액세스 제어를 제공 합니다.

Azure Maps에 RBAC가 부여 된 사용자 및 앱을 보려면 **Access Control (IAM)** 로 이동 합니다. 여기에서 **역할 할당**을 선택 하 고 **Azure Maps**를 기준으로 필터링 합니다.

![RBAC가 부여 된 사용자 및 앱 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

앱을 등록 하 고 Azure Maps에 연결한 후 액세스 토큰을 요청할 수 있습니다.

응용 프로그램에서 Azure Maps 웹 SDK를 사용 하 여 사용자 토큰 인증을 사용 하는 경우 Azure Maps 클라이언트 ID 및 Azure AD 앱 ID를 사용 하 여 HTML 페이지를 구성 합니다.

응용 프로그램에서 서버 또는 응용 프로그램 인증을 사용 하는 경우 `https://login.microsoftonline.com`Azure AD 토큰 끝점에서 토큰을 요청 합니다. 요청에서 다음 정보를 사용 합니다. 

* Azure AD 리소스 ID `https://atlas.microsoft.com/`
* Azure Maps 클라이언트 ID
* Azure AD 앱 ID
* Azure AD 앱 등록 암호 또는 인증서

| Azure 환경   | Azure AD 토큰 끝점 | Azure 리소스 ID |
| --------------------|-------------------------|-------------------|
| Azure 공용 클라우드        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government 클라우드   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Azure AD에서 사용자 및 서비스 사용자에 대 한 액세스 토큰을 요청 하는 방법에 대 한 자세한 내용은 [AZURE ad에 대 한 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE AD 및 Azure Maps 웹 SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)를 참조 하세요.

Azure Maps 계정에 대 한 API 사용 메트릭을 찾습니다.
> [!div class="nextstepaction"] 
> [사용 메트릭 보기](how-to-view-api-usage.md)

Azure Maps와 Azure AD를 통합 하는 방법을 보여 주는 샘플을 탐색 합니다.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
