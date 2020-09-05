---
title: '자습서: Alibaba Cloud Service(역할 기반 SSO)와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Alibaba Cloud Service(역할 기반 SSO) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.openlocfilehash: 0e9ccb3f4308a1a75a715a16ab4c1a2887b0a915
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522073"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>자습서: Alibaba Cloud Service(역할 기반 SSO)와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Alibaba Cloud Service(역할 기반 SSO)를 연결하는 방법에 대해 알아봅니다. Alibaba Cloud Service(역할 기반 SSO)를 Azure AD와 연결하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Alibaba Cloud Service(역할 기반 SSO)에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Alibaba Cloud Service(역할 기반 SSO)에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Alibaba Cloud Service(역할 기반 SSO) SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Alibaba Cloud Service(역할 기반 SSO)에서 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>갤러리에서 Alibaba Cloud Service(역할 기반 SSO)추가

Alibaba Cloud Service(역할 기반 SSO)가 Azure AD에 통합되도록 구성하려면 갤러리의 Alibaba Cloud Service(역할 기반 SSO)를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Alibaba Cloud Service(역할 기반 SSO)** 를 입력합니다.
1. 결과 패널에서 **Alibaba Cloud Service(역할 기반 SSO)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.
5. **Alibaba Cloud Service(역할 기반 SSO)** 페이지에서 왼쪽 탐색 창의 **속성**을 클릭하고, 나중에 사용할 수 있도록 **개체 ID**를 복사하여 컴퓨터에 저장합니다.

    ![속성 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Alibaba Cloud Service(역할 기반 SSO)용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Alibaba Cloud Service(역할 기반 SSO)에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Alibaba Cloud Service(역할 기반 SSO)의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Alibaba Cloud Service(역할 기반 SSO)에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[Alibaba Cloud Service에서 역할 기반 Single Sign-On 구성](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Alibaba Cloud Service(역할 기반 SSO) SSO 구성](#configure-alibaba-cloud-service-role-based-sso-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Alibaba Cloud Service(역할 기반 SSO) 테스트 사용자 만들기](#create-alibaba-cloud-service-role-based-sso-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Alibaba Cloud Service(역할 기반 SSO)에 만듭니다.
3. **[단일 SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Alibaba Cloud Service(역할 기반 SSO)** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **서비스 공급자 메타데이터 파일**이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    >[!NOTE]
    >이 [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)에서 서비스 공급자 메타데이터를 얻을 것입니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    다. 메타데이터 파일이 정상적으로 업로드되면 in Alibaba Cloud Service(역할 기반 SSO) 섹션 텍스트 상자에 **식별자** 및 **회신 URL** 값이 자동으로 입력됩니다.

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

1. Alibaba Cloud Service(역할 기반 SSO)는 Azure AD에서 역할을 구성해야 합니다. 역할 클레임은 미리 구성되어 있으므로 구성할 필요가 없지만, 이 [문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)를 사용하여 Azure AD에서 만들어야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Alibaba Cloud Service(역할 기반 SSO)** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Alibaba Cloud Service(역할 기반 SSO)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Alibaba Cloud Service(역할 기반 SSO)** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 탭의 사용자 목록에서 u2를 선택하고, **선택**을 클릭합니다. 그런 다음, **할당**을 클릭합니다.

    ![테스트 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. 할당된 역할을 살펴보고 Alibaba Cloud Service(역할 기반 SSO)를 테스트 합니다.

    ![테스트 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >사용자(u2)를 할당한 후 만들어진 역할은 자동으로 사용자에게 연결됩니다. 여러 역할을 만든 경우 필요에 따라 적절한 역할을 사용자에게 연결해야 합니다. Azure AD에서 여러 Alibaba Cloud 계정으로 역할 기반 SSO를 구현하려면 위의 단계를 반복합니다.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Alibaba Cloud Service에서 역할 기반 Single Sign-On 구성

1. Account1을 사용하여 Alibaba Cloud [RAM 콘솔](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9)에 로그인합니다.

2. 왼쪽 탐색 창에서 **SSO**를 선택합니다.

3. **역할 기반 SSO** 탭에서 **IdP 만들기**를 클릭합니다.

4. 표시되는 페이지에서 IdP 이름 필드에 `AAD`를 입력하고, **참고** 필드에 설명을 입력하고, **업로드**를 클릭하여 이전에 다운로드한 페더레이션 메타데이터 파일을 업로드하고, **확인**을 클릭합니다.

5. IdP가 성공적으로 만들어지면 **RAM 역할 만들기**를 클릭합니다.

6. **RAM 역할 이름** 필드에 `AADrole`을 입력하고, **IdP 선택** 드롭다운 목록에서 `AAD`를 선택하고, [확인]을 클릭합니다.

    >[!NOTE]
    >필요에 따라 역할에 권한을 부여할 수 있습니다. IdP 및 해당 역할을 만든 후에는 나중에 사용할 수 있도록 IdP와 역할의 ARN을 저장하는 것이 좋습니다. ARN은 IdP 정보 페이지 및 역할 정보 페이지에서 얻을 수 있습니다.

7. 다음과 같이 Alibaba Cloud RAM 역할(AADrole)을 Azure AD 사용자(u2)와 연결합니다. RAM 역할을 Azure AD 사용자와 연결하려면 다음 단계에 따라 Azure AD에 역할을 만들어야 합니다.

    a. [Microsoft Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)에 로그인합니다.

    b. **권한 수정**을 클릭하여 역할 만들기에 필요한 권한을 획득합니다.

    ![Graph 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    다. 다음 그림처럼 목록에서 다음 권한을 선택하고 **권한 수정**을 클릭합니다.

    ![Graph 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >권한을 부여한 후에는 Graph 탐색기에 다시 로그온합니다.

    d. Graph 탐색기 페이지의 첫 번째 드롭다운 목록에서 **GET**을 선택하고, 두 번째 드롭다운 목록에서 **베타**를 선택합니다. 드롭다운 목록 옆에 있는 필드에 `https://graph.microsoft.com/beta/servicePrincipals`를 입력하고, **쿼리 실행**을 클릭합니다.

    ![Graph 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >여러 디렉터리를 사용하는 경우 쿼리 필드에 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`를 입력할 수 있습니다.

    e. **응답 미리 보기** 섹션에서, 나중에 사용할 수 있도록 '서비스 주체'에서 appRoles 속성을 추출합니다.

    ![Graph 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >appRoles 속성은 쿼리 필드에 `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`를 입력하여 찾을 수 있습니다. `objectID`는 Azure AD **속성** 페이지에서 복사한 개체 ID입니다.

    f. Graph 탐색기로 돌아가서 메서드를 **GET**에서 **PATCH**로 변경하고, 다음 콘텐츠를 **요청 본문** 섹션에 붙여넣고, **쿼리 실행** 을 클릭합니다.
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value`는 RAM 콘솔에서 만든 IdP와 역할의 ARN입니다. 여기서 필요에 따라 여러 역할을 추가할 수 있습니다. Azure AD는 이러한 역할의 값을 SAML 응답의 클레임 값으로 보냅니다. 그러나 패치 작업의 `msiam_access` 부분 뒤에만 새 역할을 추가할 수 있습니다. 만들기 프로세스를 원활하게 수행하려면 GUID 생성기 같은 ID 생성기를 사용하여 실시간으로 ID를 생성하는 것이 좋습니다.

    g. '서비스 주체'가 필요한 역할로 패치된 후에는 자습서의 **Azure AD 테스트 사용자 할당** 섹션에 설명된 단계에 따라 역할을 Azure AD 사용자(u2)와 연결합니다.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Alibaba Cloud Service(역할 기반 SSO) SSO 구성

**Alibaba Cloud Service(역할 기반 SSO)** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML**과 적절히 복사한 URL을 [Alibaba Cloud Service(역할 기반 SSO) 지원 팀](https://www.aliyun.com/service/)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba Cloud Service(역할 기반 SSO) 테스트 사용자 만들기

이 섹션에서는 Alibaba Cloud Service(역할 기반 SSO)에서 Britta Simon이라는 사용자를 만듭니다. [Alibaba Cloud Service(역할 기반 SSO) 지원 팀](https://www.aliyun.com/service/)과 협력하여 사용자를 Alibaba Cloud Service(역할 기반 SSO) 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이전 구성이 완료되면 다음 단계에 따라 Alibaba Cloud Service(역할 기반 SSO)를 테스트합니다.

1. Azure Portal에서 **Alibaba Cloud Service(역할 기반 SSO)** 페이지로 이동하여 **Single Sign-On**을 선택하고, **테스트**를 클릭합니다.

    ![테스트 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. **현재 사용자로 로그인**을 클릭합니다.

    ![테스트 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. 계정 선택 페이지에서 u2를 선택합니다.

    ![테스트 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. 역할 기반 SSO가 성공했음을 알리는 다음 페이지가 표시됩니다.

    ![테스트 구성](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Alibaba Cloud Service(역할 기반 SSO) 사용해보기](https://aad.portal.azure.com/)

