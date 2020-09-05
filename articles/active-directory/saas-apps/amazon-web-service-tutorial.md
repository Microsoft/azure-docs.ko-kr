---
title: '자습서: AWS(Amazon Web Services)와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Amazon Web Services(AWS) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/14/2020
ms.author: jeedes
ms.openlocfilehash: 7377c6ea92ea53ca14525938e7522448afac541c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>자습서: AWS(Amazon Web Services)와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 AWS(Amazon Web Services)를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. AWS(Amazon Web Services)를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

![Azure AD 및 AWS 관계에 대한 다이어그램](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

여러 인스턴스에 대해 여러 식별자를 구성할 수 있습니다. 다음은 그 예입니다.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

이러한 값을 사용하면 Azure AD에서 **#** 값을 제거하고, 올바른 `https://signin.aws.amazon.com/saml` 값을 SAML 토큰의 대상 URL로 보냅니다.

다음과 같은 이유로 이 방법을 사용하는 것이 좋습니다.

- 각 애플리케이션에서 고유한 X509 인증서를 제공합니다. 그러면 AWS 앱 인스턴스의 각 인스턴스마다 인증서 만료 날짜가 다를 수 있으며, 이 만료 날짜는 개별 AWS 계정으로 관리할 수 있습니다. 이 경우 전체 인증서를 더 쉽게 롤오버할 수 있습니다.

- Azure AD에서 AWS 앱을 사용하여 사용자 프로비저닝을 사용하도록 설정하면 서비스에서 해당 AWS 계정의 모든 역할을 가져옵니다. 앱에서 AWS 역할을 수동으로 추가하거나 업데이트할 필요가 없습니다.

- 앱에 대한 앱 소유자를 개별적으로 할당할 수 있습니다. 이 사용자는 Azure AD에서 앱을 직접 관리할 수 있습니다.

> [!Note]
> 갤러리 애플리케이션만 사용해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* AWS SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* AWS(Amazon Web Services)에서 **SP 및 IDP** 시작 SSO 지원
* AWS(Amazon Web Services)를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>갤러리에서 AWS(Amazon Web Services) 추가

Azure AD에 AWS(Amazon Web Services)를 통합하도록 구성하려면 갤러리의 AWS(Amazon Web Services)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 개요 메뉴에서 **Enterprise 애플리케이션** > **모든 애플리케이션**을 선택합니다.
1. **새 애플리케이션**을 선택하여 애플리케이션을 추가합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **AWS(Amazon Web Services)** 를 입력합니다.
1. 결과 창에서 **AWS(Amazon Web Services)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>AWS(Amazon Web Services)에 대한 Azure AD SSO 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 AWS(Amazon Web Services)에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

AWS(Amazon Web Services)에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[AWS(Amazon Web Services) SSO 구성](#configure-amazon-web-services-aws-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[AWS(Amazon Web Services) 테스트 사용자 만들기](#create-amazon-web-services-aws-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 AWS(Amazon Web Services)에 만듭니다.
    1. **[AWS(Amazon Web Services)에서 역할 프로비저닝을 구성하는 방법](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **AWS(Amazon Web Services)** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 **식별자(엔터티 ID)** 및 **회신 URL**을 동일한 기본값 `https://signin.aws.amazon.com/saml`으로 업데이트합니다. **저장**을 클릭하여 구성 변경을 저장합니다.

1. 둘 이상의 인스턴스를 구성하는 경우 식별자 값을 제공합니다. 두 번째 인스턴스 이후부터 **#** 기호를 포함한 다음 형식을 사용하여 고유한 SPN 값을 지정합니다.

    `https://signin.aws.amazon.com/saml#2`

1. AWS 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 AWS 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | 속성  | 원본 특성  | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | 역할            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration             | “900초(15분)에서 43200초(12시간) 사이의 값을 제공합니다.” |  `https://aws.amazon.com/SAML/Attributes` |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서**(3단계) 대화 상자에서 **인증서 추가**를 선택합니다.

    ![새 SAML 인증서 만들기](common/add-saml-certificate.png)

1. 새 SAML 서명 인증서를 생성한 다음, **새 인증서**를 선택합니다. 인증서 알림에 사용할 이메일 주소를 입력합니다.
   
    ![새 SAML 인증서](common/new-saml-certificate.png) 

1. **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드하고 저장합니다.

    ![인증서 다운로드 링크](./media/amazon-web-service-tutorial/certificate.png)

1. **AWS(Amazon Web Services) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 개요 메뉴에서 **사용자** > **모든 사용자**를 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 AWS(Amazon Web Services)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 개요 메뉴에서 **Enterprise 애플리케이션** > **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **AWS(Amazon Web Services)** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-amazon-web-services-aws-sso"></a>Amazon Web Services(AWS) SSO 구성

1. 다른 브라우저 창에서 AWS 회사 사이트에 관리자 권한으로 로그온합니다.

2. **AWS 홈**을 선택합니다.

    ![AWS 홈 아이콘이 강조 표시된 AWS 회사 사이트의 스크린샷][11]

3. **ID 및 액세스 관리**를 선택합니다.

    ![IAM이 강조 표시된 AWS 서비스 페이지의 스크린샷][12]

4. **ID 공급자(Identity Providers)**  > **Create Provider(공급자 만들기)** 를 차례로 선택합니다.

    ![Identity Providers 및 Create Provider가 강조 표시된 IAM 페이지의 스크린샷][13]

5. **Configure Provider**(공급자 구성) 페이지에서 다음 단계를 수행합니다.

    ![Configure Provider의 스크린샷][14]

    a. **공급자 형식**으로 **SAML**을 선택합니다.

    b. **Provider Name**(공급자 이름)에 대해 공급자 이름(예: *WAAD*)을 입력합니다.

    다. Azure Portal에서 다운로드한 **메타데이터 파일**을 업로드하려면 **파일 선택**을 선택합니다.

    d. **다음 단계**를 선택합니다.

6. **Verify Provider Information**(공급자 정보 확인) 페이지에서 **Create**(만들기)를 선택합니다.

    ![Create가 강조 표시된 Verify Provider Information의 스크린샷][15]

7. **Roles(역할)**  > **Create role(역할 만들기)** 을 차례로 선택합니다.

    ![Roles 페이지의 스크린샷][16]

8. **역할 만들기** 페이지에서 다음 단계를 수행합니다.  

    ![Create role 페이지의 스크린샷][19]

    a. **Select type of trusted entity**(신뢰할 수 있는 엔터티 유형 선택) 아래에서 **SAML 2.0 federation**(SAML 2.0 페더레이션)을 선택합니다.

    b. **Choose a SAML 2.0 Provider**(SAML 2.0 공급자 선택) 아래에서 이전에 만든 **SAML provider**(SAML 공급자)를 선택합니다(예: *WAAD*)을 입력합니다.

    다. **프로그래밍 및 AWS 관리 콘솔 액세스 허용**을 선택합니다.
  
    d. 완료되면 **다음: 사용 권한**을 클릭합니다.

9. **Attach permissions policies**(권한 정책 연결) 대화 상자에서 조직에 따라 적절한 정책을 연결합니다. 그런 다음, **Next: 검토**를 클릭합니다.  

    ![Attach permissions policy 대화 상자의 스크린샷][33]

10. **Review**(검토) 대화 상자에서 다음 단계를 수행합니다.

    ![Review 대화 상자의 스크린샷][34]

    a. **Role name**(역할 이름)에서 역할 이름을 입력합니다.

    b. **Role description**(역할 설명)에서 설명을 입력합니다.

    다. **Create role**(역할 만들기)을 선택합니다.

    d. 필요한 만큼 많은 역할을 만들고, 이를 ID 공급자에 매핑합니다.

11. Azure AD 사용자 프로비저닝의 AWS 계정에서 역할을 가져오려면 AWS 서비스 계정 자격 증명을 사용합니다. 이 경우에 AWS 콘솔 홈을 엽니다.

12. **Services**(서비스)를 선택합니다. **Security, Identity & Compliance**(보안, ID 및 규정 준수)아래에서 **IAM**을 클릭합니다.

    ![Services 및 IAM이 강조 표시된 AWS 콘솔 홈의 스크린샷](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. IAM 섹션에서 **Policies**(정책)를 선택합니다.

    ![Policies가 강조 표시된 IAM 섹션의 스크린샷](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. **Create policy**(정책 만들기)를 클릭하여 Azure AD 사용자 프로비저닝의 AWS 계정에서 역할을 가져오는 새 정책을 만듭니다.

    ![Create policy가 강조 표시된 Create role 페이지의 스크린샷](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 고유한 정책을 만들어 AWS 계정에서 모든 역할을 페치합니다.

    ![JSON이 강조 표시된 Create policy 페이지의 스크린샷](./media/amazon-web-service-tutorial/policy1.png)

    a. **Create policy**(정책 만들기)에서 **JSON** 탭을 선택합니다.

    b. 정책 문서에서 다음 JSON을 추가합니다.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    다. **Review policy**(정책 검토 단추)를 클릭하여 정책의 유효성을 검사합니다.

    ![Create policy 페이지의 스크린샷](./media/amazon-web-service-tutorial/policy5.png)

16. 새 정책을 정의합니다.

    ![Name 및 Description 필드가 강조 표시된 Create policy 페이지의 스크린샷](./media/amazon-web-service-tutorial/policy2.png)

    a. **Name**(이름)에 대해 **AzureAD_SSOUserRole_Policy**를 입력합니다.

    b. **Description**(설명)에 대해 **This policy will allow to fetch the roles from AWS accounts**(이 정책을 통해 AWS 계정에서 역할을 가져올 수 있습니다)를 입력합니다.

    다. **Create policy**(정책 만들기)를 선택합니다.

17. 새 사용자 계정을 AWS IAM 서비스에 만듭니다.

    a. AWS IAM 콘솔에서 **Users**(사용자)를 선택합니다.

    ![Users가 강조 표시된 AWS IAM 콘솔의 스크린샷](./media/amazon-web-service-tutorial/policy3.png)

    b. 새 사용자를 만들려면 **Add user**(사용자 추가) 단추를 선택합니다.

    ![Add user 단추의 스크린샷](./media/amazon-web-service-tutorial/policy4.png)

    다. **Add user** 섹션에서 다음을 수행합니다.

    ![User name(사용자 이름) 및 Access type(액세스 형식)이 강조 표시된 Add user 페이지의 스크린샷](./media/amazon-web-service-tutorial/adduser1.png)

    * 사용자 이름을 **AzureADRoleManager**로 입력합니다.

    * 액세스 형식에 대해 **Programmatic access**(프로그래밍 방식 액세스)를 선택합니다. 사용자는 이 방법으로 API를 호출하고 AWS 계정에서 역할을 가져올 수 있습니다.

    * **Next Permissions**(다음: 권한)를 클릭합니다.

18. 이 사용자에 대한 새 정책을 만듭니다.

    ![사용자 추가의 스크린샷](./media/amazon-web-service-tutorial/adduser2.png)

    a. **Attach existing policies directly**(기존 정책 직접 연결)를 선택합니다.

    b. **AzureAD_SSOUserRole_Policy** 필터 섹션에서 새로 만든 정책을 검색합니다.

    다. 정책을 선택한 다음, **Next: 검토**를 클릭합니다.

19. 연결된 사용자에 대한 정책을 검토합니다.

    ![Create user가 강조 표시된 Add user 페이지의 스크린샷](./media/amazon-web-service-tutorial/adduser3.png)

    a. 사용자 이름, 액세스 형식 및 사용자에 매핑된 정책을 검토합니다.

    b. **사용자 만들기**를 선택합니다.

20. 사용자의 사용자 자격 증명을 다운로드합니다.

    ![사용자 추가의 스크린샷](./media/amazon-web-service-tutorial/adduser4.png)

    a. 사용자 **액세스 키 ID** 및 **암호 액세스 키**를 복사합니다.

    b. 이러한 자격 증명을 Azure AD 사용자 프로비저닝 섹션에 입력하여 AWS 콘솔에서 역할을 가져옵니다.

    다. **닫기**를 선택합니다.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>AWS(Amazon Web Services)에서 역할 프로비저닝을 구성하는 방법

1. Azure AD 관리 포털의 AWS 앱에서 **프로비저닝**으로 이동합니다.

    ![프로비저닝이 강조 표시된 AWS 앱의 스크린샷](./media/amazon-web-service-tutorial/provisioning.png)

2. **clientsecret** 및 **비밀 토큰** 필드에서 각각 액세스 키와 비밀을 입력합니다.

    ![관리자 자격 증명 대화 상자의 스크린샷](./media/amazon-web-service-tutorial/provisioning1.png)

    a. **clientsecret** 필드에 AWS 사용자 액세스 키를 입력합니다.

    b. **암호 토큰** 필드에 AWS 사용자 암호를 입력합니다.

    다. **연결 테스트**를 클릭합니다.

    d. **저장**을 선택하여 설정을 저장합니다.

3. **설정** 섹션에서 **프로비저닝 상태**에 대해 **켜기**를 선택합니다. 그런 다음 **저장**을 선택합니다.

    ![켜기가 강조 표시된 설정 섹션의 스크린샷](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> 프로비저닝 서비스는 AWS에서 Azure AD로 역할만 가져옵니다. 이 서비스는 사용자 및 그룹을 Azure AD에서 AWS로 프로비저닝하지 않습니다.

> [!NOTE]
> 프로비저닝 자격 증명을 저장한 후 초기 동기화 주기가 실행될 때까지 기다려야 합니다. 동기화를 완료하는 데는 일반적으로 약 40분이 걸립니다. **프로비저닝** 페이지 하단의 **현재 상태**에서 상태를 확인할 수 있습니다.

### <a name="create-amazon-web-services-aws-test-user"></a>AWS(Amazon Web Services) 테스트 사용자 만들기

이 섹션에서는 AWS(Amazon Web Services)에서 B.Simon이라는 사용자를 만듭니다. AWS(Amazon Web Services)에서는 SSO용으로 시스템에서 사용자를 만들 필요가 없으므로 여기에서 작업을 수행할 필요가 없습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 AWS(Amazon Web Services) 타일을 클릭하면 SSO를 설정하기 위한 AWS(Amazon Web Services)에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="known-issues"></a>알려진 문제

 * **프로비저닝** 섹션에서 **매핑** 하위 섹션에 "로드 중..." 메시지가 표시되고 특성 매핑이 표시되지 않습니다. 현재 지원되는 유일한 프로비저닝 워크플로는 사용자 또는 그룹을 할당하는 중에 선택할 수 있도록 역할을 AWS에서 Azure AD로 가져오는 것입니다. 이에 대한 특성 매핑은 미리 결정되어 있으며 구성할 수 없습니다.

 * **프로비전** 섹션에서는 한 번에 하나의 AWS 테넌트에 대한 하나의 자격 증명 집합만 입력할 수 있습니다. 가져온 모든 역할은 AWS 테넌트에 대한 Azure AD [`servicePrincipal` 개체](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)의 `appRoles` 속성에 기록됩니다.

   프로비저닝을 위해 여러 AWS 테넌트(`servicePrincipals`로 표시됨)를 갤러리에서 Azure AD에 추가할 수 있습니다. 그러나 SSO에 사용되는 단일 `servicePrincipal`에 프로비저닝하는 데 사용되는 여러 AWS `servicePrincipals`에서 가져온 역할을 모두 자동으로 작성할 수 없는 알려진 문제가 있습니다.

   해결 방법으로 [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)를 사용하여 가져온 모든 `appRoles`를 프로비저닝이 구성된 각 AWS `servicePrincipal`에 추출할 수 있습니다. 그런 다음, 이러한 역할 문자열을 SSO가 구성된 AWS `servicePrincipal`에 추가할 수 있습니다.

* AWS에서 Azure AD로 가져올 수 있으려면 역할이 다음 요구 사항을 충족해야 합니다.

  * 역할에는 AWS에 정의된 saml 공급자가 하나만 있어야 합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 AWS(Amazon Web Services) 사용해 보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 AWS(Amazon Web Services)를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/protect-aws)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
