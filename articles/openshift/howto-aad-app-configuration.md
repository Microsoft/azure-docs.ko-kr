---
title: Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합
description: Microsoft Azure Red Hat OpenShift 클러스터에서 앱을 테스트 하기 위해 Azure AD 보안 그룹 및 사용자를 만드는 방법에 대해 알아봅니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 6e2437fadb743706d4f4215bbcbab8616817de5f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545622"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift에 대 한 Azure Active Directory 통합

Azure Active Directory (Azure AD) 테넌트를 아직 만들지 않은 경우 다음 지침을 계속 진행 하기 전에 [Azure Red Hat OpenShift에 대 한 AZURE ad 테넌트 만들기](howto-create-tenant.md) 의 지침을 따르세요.

Red Hat OpenShift Microsoft Azure 클러스터 대신 작업을 수행할 수 있는 권한이 필요 합니다. 조직에 서비스 주체로 사용할 Azure AD 사용자, Azure AD 보안 그룹 또는 Azure AD 앱 등록이 아직 없는 경우 다음 지침에 따라 해당 사용자를 만듭니다.

## <a name="create-a-new-azure-active-directory-user"></a>새 Azure Active Directory 사용자 만들기

[Azure Portal](https://portal.azure.com)에서 테넌트가 포털의 오른쪽 위에 있는 사용자 이름 아래에 표시 되는지 확인 합니다.

테넌트가](./media/howto-create-tenant/tenant-callout.png) 오른쪽 위에 나열 된 포털의 ![잘못 된 테넌트가 표시 되 면 오른쪽 위에서 사용자 이름을 클릭 하 고 **디렉터리 전환**을 클릭 한 다음 **모든 디렉터리** 목록에서 올바른 테넌트를 선택 합니다.

새 Azure Active Directory 전역 관리자 사용자를 만들어 Azure Red Hat OpenShift 클러스터에 로그인 합니다.

1. [사용자-모든 사용자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) 블레이드로 이동 합니다.
2. **+ 새 사용자** 를 클릭 하 여 **사용자** 창을 엽니다.
3. 이 사용자의 **이름을** 입력 합니다.
4. 만든 테넌트의 이름을 기준으로 **사용자 이름을** 만들고, `.onmicrosoft.com` 끝에 추가 합니다. `yourUserName@yourTenantName.onmicrosoft.com`)을 입력합니다. 이 사용자 이름을 적어 씁니다. 클러스터에 로그인 하는 데 필요 합니다.
5. 디렉터리 **역할** 을 클릭 하 여 디렉터리 역할 창을 열고 **전역 관리자** 를 선택한 다음 창 맨 아래에서 **확인** 을 클릭 합니다.
6. **사용자** 창에서 **암호 표시** 를 클릭 하 고 임시 암호를 기록 합니다. 처음 로그인 하면 다시 설정 하 라는 메시지가 표시 됩니다.
7. 창 맨 아래에서 **만들기** 를 클릭 하 여 사용자를 만듭니다.

## <a name="create-an-azure-ad-security-group"></a>Azure AD 보안 그룹 만들기

클러스터 관리자 액세스 권한을 부여 하기 위해 Azure AD 보안 그룹의 멤버 자격은 OpenShift 그룹 "osa-고객-관리자"로 동기화 됩니다. 지정 하지 않으면 클러스터 관리자 액세스 권한이 부여 되지 않습니다.

1. [Azure Active Directory 그룹](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) 블레이드를 엽니다.
2. **+ 새 그룹**을 클릭 합니다.
3. 그룹 이름 및 설명을 제공 합니다.
4. **그룹 종류** 를 **보안**으로 설정 합니다.
5. **멤버 자격 유형** 을 **할당 됨**으로 설정 합니다.

    이전 단계에서 만든 Azure AD 사용자를이 보안 그룹에 추가 합니다.

6. 멤버 **를 클릭 하** 여 **멤버 선택** 창을 엽니다.
7. 구성원 목록에서 위에서 만든 Azure AD 사용자를 선택 합니다.
8. 포털의 맨 아래에서 **선택** 을 클릭 한 다음 **만들기** 를 클릭 하 여 보안 그룹을 만듭니다.

    그룹 ID 값을 기록 합니다.

9. 그룹이 만들어지면 모든 그룹 목록에 표시 됩니다. 새 그룹을 클릭 합니다.
10. 표시 되는 페이지에서 **개체 ID**를 복사 합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서 `GROUPID`으로이 값을 참조 합니다.

> [!IMPORTANT]
> 이 그룹을 osa-고객-관리자 OpenShift 그룹과 동기화 하려면 Azure CLI를 사용 하 여 클러스터를 만듭니다. 현재 Azure Portal에이 그룹을 설정할 필드가 없습니다.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD 앱 등록 만들기

`az openshift create` 명령에 `--aad-client-app-id` 플래그를 생략 하 여 클러스터를 만드는 과정의 일부로 Azure AD (Azure Active Directory) 앱 등록 클라이언트를 자동으로 만들 수 있습니다. 이 자습서에서는 완전성을 위해 Azure AD 앱 등록을 만드는 방법을 보여 줍니다.

조직에 서비스 주체로 사용할 Azure AD (Azure Active Directory) 앱 등록이 아직 없는 경우 다음 지침에 따라 계정을 만드세요.

1. [앱 등록 블레이드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 를 열고 **+ 새로 만들기 등록**을 클릭 합니다.
2. 응용 프로그램 **등록** 창에서 응용 프로그램 등록의 이름을 입력 합니다.
3. **지원 되는 계정 유형** 에서 **이 조직 디렉터리의 계정도** 선택 되어 있는지 확인 합니다. 가장 안전 하 게 선택 합니다.
4. 클러스터의 URI를 알고 있으면 나중에 리디렉션 URI를 추가 합니다. **등록** 단추를 클릭 하 여 Azure AD 응용 프로그램 등록을 만듭니다.
5. 표시 되는 페이지에서 **응용 프로그램 (클라이언트) ID**를 복사 합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서 `APPID`으로이 값을 참조 합니다.

![앱 개체 페이지의 스크린샷](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

Azure Active Directory에 대 한 앱 인증을 위한 클라이언트 암호를 생성 합니다.

1. 앱 등록 페이지의 **관리** 섹션에서 **인증서 & 암호**를 클릭 합니다.
2. **인증서 & 비밀** 창에서 **+ 새 클라이언트 암호**를 클릭 합니다.  **클라이언트 암호 추가** 창이 나타납니다.
3. **설명을**입력 합니다.
4. 설정은 사용자가 원하는 기간 (예: **2 년**)으로 **만료** 됩니다.
5. **추가** 를 클릭 하면 키 값이 페이지의 **클라이언트 암호** 섹션에 나타납니다.
6. 키 값을 복사 합니다. [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서에서 `SECRET`으로이 값을 참조 합니다.

![인증서 및 암호 창의 스크린샷](./media/howto-create-tenant/create-key.png)

Azure 애플리케이션 개체에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)를 참조 하세요.

새 Azure AD 응용 프로그램을 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory v 1.0 엔드포인트으로 앱 등록](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)을 참조 하세요.

## <a name="add-api-permissions"></a>API 사용 권한 추가

1. **관리** 섹션에서 **API 권한**을 클릭 합니다.
2. **권한 추가** 를 클릭 하 고 **Azure Active Directory 그래프** , **위임 된 권한**을 차례로 선택 합니다. 
3. 아래 목록에서 **사용자** 를 확장 하 고 **사용자의 읽기** 권한을 사용 하도록 설정 합니다. **사용자** 가 기본적으로 사용 하도록 설정 되어 있는 경우에는 **Azure Active Directory Graph** 권한 **사용자**인지 확인 합니다. **Microsoft Graph** 권한 사용자는 읽을 수 *없습니다* . **읽기**.
4. 위로 스크롤하여 **응용 프로그램 사용 권한**을 선택 합니다.
5. 아래 목록에서 **디렉터리** 를 확장 하 고 디렉터리를 사용 하도록 설정 **합니다. readall**
6. **권한 추가** 를 클릭 하 여 변경 내용을 적용 합니다.
7. 이제 API 사용 권한 패널에 *User. Read* 및 Directory를 모두 표시 해야 합니다 *. readall*. 디렉터리 옆에 있는 **관리자 동의 필요** 열에서 경고를 확인 하십시오 *. readall*.
8. *Azure 구독 관리자*인 경우 아래  ***구독 이름* 에 대해 관리자 동의 부여** 를 클릭 합니다. *Azure 구독 관리자*가 아닌 경우 관리자에 게 동의를 요청 합니다.
API 사용 권한 패널의 ![스크린샷 사용자. 읽기 및 디렉터리. 모든 권한이 추가 되었고 디렉터리에 관리자 동의가 필요 합니다. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> 동의가 부여 된 후에만 클러스터 관리자 그룹의 동기화가 작동 합니다. *관리 동의 필요* 열에서 확인 표시와 메시지 " *구독 이름*에 대해 부여 됨" 메시지가 표시 된 녹색 원이 표시 됩니다.

관리자 및 기타 역할 관리에 대 한 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)을 참조 하세요.

## <a name="resources"></a>리소스

* [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [빠른 시작: Azure Active Directory v 1.0 엔드포인트으로 앱 등록](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>다음 단계

모든 [Azure Red Hat OpenShift 필수 구성 요소](howto-setup-environment.md)를 충족 하는 경우 첫 번째 클러스터를 만들 준비가 되었습니다.

자습서를 사용해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)
