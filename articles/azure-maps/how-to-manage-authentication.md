---
title: Azure Maps의 인증 관리 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Maps의 인증을 관리할 수 있습니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694102"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps의 인증 관리

Azure Maps 계정을 만들면 Azure Active Directory (Azure AD) 또는 공유 키 인증을 지원 하기 위해 클라이언트 ID 및 키 생성 됩니다.

## <a name="view-authentication-details"></a>인증 정보 보기

Azure portal에서 인증 세부 정보를 볼 수 있습니다. 계정 및 선택으로 이동 **인증** 에 **설정** 메뉴.

![인증 세부 정보](./media/how-to-manage-authentication/how-to-view-auth.png)

 자세한 내용은 참조 하세요 [Azure Maps를 사용 하 여 인증](https://aka.ms/amauth)합니다.


## <a name="set-up-azure-ad-app-registration"></a>Azure AD 앱 등록 설정

Azure Maps 계정을 만들면 Azure AD 테 넌 트 및 Azure Maps 리소스 사이의 연결을 설정 해야 합니다.

1. Azure AD 블레이드로 이동한 다음 앱 등록을 만듭니다. 등록에 대 한 이름을 제공 합니다. 에 **로그온 URL** 상자에서 웹 앱의 홈 페이지를 제공 / API (예를 들어, https:\//localhost/). 등록 된 앱에 이미 있는 경우 2 단계로 이동 합니다.

    ![앱 등록](./media/how-to-manage-authentication/app-registration.png)

    ![앱 등록 세부 정보](./media/how-to-manage-authentication/app-create.png)

2. Azure Maps로 위임 된 API 사용 권한을 할당 하려면 대상 응용 프로그램으로 이동 **앱 등록**를 선택한 후 **설정**합니다.  선택 **필요한 권한**를 선택한 후 **추가**합니다. 검색 하 고 선택 **Azure Maps** 아래에서 **API 선택**를 선택한 후는 **선택** 단추입니다.

    ![API 앱 사용 권한](./media/how-to-manage-authentication/app-permissions.png)

3. 아래 **권한을 선택**를 선택 **액세스 Azure Maps**를 선택한 후는 **선택** 단추.

    ![API 앱 사용 권한 선택](./media/how-to-manage-authentication/select-app-permissions.png)

4. 전체 단계는 또는 인증 방법에 따라 b.

    1. 응용 프로그램에서 Azure Maps 웹 SDK를 사용 하 여 사용자 토큰 인증을 사용 하는 경우 사용 하도록 설정 `oauthEnableImplicitFlow` 앱 등록 세부 정보 페이지의 매니페스트 섹션에서을 true로 설정 합니다.
    
       ![앱 매니페스트](./media/how-to-manage-authentication/app-manifest.png)

    2. 응용 프로그램 서버/응용 프로그램 인증을 사용 하는 경우 이동 합니다 **키** 블레이드에서 앱 등록에서 및 암호 만들기 또는 앱 등록에 공개 키 인증서를 업로드 합니다. 선택 하면 암호를 만들면 **저장**, 나중에 암호를 복사 하 고 안전 하 게 저장 합니다. 이 암호를 사용 하 여 Azure AD에서 토큰을 획득 됩니다.

       ![앱 키](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Azure Maps에 RBAC 부여

Azure AD 테 넌 트를 사용 하 여 Azure Maps 계정을 연결한 후에 사용자 또는 응용 프로그램 하나 이상의 Azure Maps 액세스 제어 역할을 할당 하 여 액세스 제어를 부여할 수 있습니다.

1. 로 이동 **액세스 제어 (IAM)** 를 선택 **역할 할당**를 선택한 후 **역할 할당 추가**합니다.

    ![RBAC 부여](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 에 **역할 할당 추가** 창 아래에 있는 **역할**를 선택 **Azure Maps 날짜 판독기 (미리 보기)** 합니다. **액세스 할당**에서 **Azure AD 사용자, 그룹 또는 서비스 보안 주체**를 선택합니다. 아래 **선택**, 사용자 또는 응용 프로그램을 선택 합니다. **저장**을 선택합니다.

    ![역할 할당 추가](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>사용 가능한 Azure Maps RBAC 역할 보기

Azure Maps를 사용할 수 있는 역할 기반 액세스 제어 (RBAC) 역할을 보려면로 이동 **액세스 제어 (IAM)** 를 선택 **역할**를 사용 하 여 역할에 대 한 검색을 시작 하는 다음으로 **Azure Maps**. 이들은에 대 한 액세스를 부여할 수 있는 역할입니다.

![사용 가능한 역할 보기](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC를 보려면

RBAC는 세분화 된 액세스 제어를 제공 합니다.

로 사용자 및 부여 된 RBAC Azure Maps에 대 한 앱을 보려면 **액세스 제어 (IAM)** 를 선택 **역할 할당**, 다음으로 필터링 하 고 **Azure Maps**합니다.

![사용자 및 RBAC 권한이 부여 된 앱 보기](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps에 대한 토큰 요청

앱을 등록 하 고 Azure Maps와 사용 하 여 관련 액세스 토큰을 요청할 수 있습니다.

* Azure Maps 클라이언트 ID 및 Azure AD 앱 ID를 사용 하 여 HTML 페이지를 구성 해야 하는 응용 프로그램에서 Azure Maps 웹 SDK를 사용 하 여 사용자 토큰 인증을 사용 하는 경우

* Azure AD 로그인 끝점에서 토큰을 요청 해야 하는 응용 프로그램 서버/응용 프로그램 인증을 사용 하는 경우 `https://login.microsoftonline.com` Azure AD 리소스 ID를 사용 하 여 `https://atlas.microsoft.com/`, Azure Maps 클라이언트 ID, Azure AD 앱 ID 및 Azure AD 앱 등록 암호 또는 인증서입니다.

사용자 및 서비스 주체에 대 한 Azure AD에서 액세스 토큰을 요청 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure AD의 인증 시나리오](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)합니다.


## <a name="next-steps"></a>다음 단계

* Azure AD 인증 및 Azure Maps 웹 SDK에 대한 자세한 내용은 [Azure AD 및 Azure Maps 웹 SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)를 참조하세요.