---
title: '자습서: 여러 계정 연결을 위해 AWS(Amazon Web Services)와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure AD와 Amazon Web Services 간에 Single Sign-On를 구성 하는 방법 (AWS) (레거시 자습서)을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 20674f5a793267c3a9e2fa078f95cbf96624df13
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550173"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>자습서: Amazon Web Services와 Azure Active Directory 통합 (AWS) (레거시 자습서)

이 자습서에서는 Azure Active Directory (Azure AD)와 Amazon Web Services (AWS) (레거시 자습서)를 통합 하는 방법에 대해 알아봅니다.

AWS(Amazon Web Services)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 AWS(Amazon Web Services)에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 AWS(Amazon Web Services)에 자동으로 로그인(Single Sign-On)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱을 통합 하는 방법에 대 한 자세한 내용은 [응용 프로그램 액세스 및 Azure Active Directory Single Sign-On](../manage-apps/what-is-single-sign-on.md)를 참조 하세요.

![결과 목록의 AWS(Amazon Web Services)](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> 모든 AWS 계정에 하나의 AWS 앱을 연결하는 것은 권장되는 접근 방식이 아닙니다. 대신 [이](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) 방법을 사용하여 Azure AD에 있는 여러 AWS 앱 인스턴스에 대해 여러 AWS 계정 인스턴스를 구성하는 것이 좋습니다. AWS 계정 및 역할이 거의 없는 경우에만이 방법을 사용 해야 합니다 .이 모델은 AWS 계정 및 이러한 계정 내의 역할이 확장 되므로 확장할 수 없습니다. 이 방법은 Azure AD 사용자 프로 비전을 사용 하는 AWS 역할 가져오기 기능을 사용 하지 않으므로 수동으로 역할을 추가/업데이트/삭제 해야 합니다. 이 접근 방식에 대 한 기타 제한 사항은 아래 세부 정보를 참조 하세요.

**다음과 같은 이유로 이 방법을 사용하지 않는 것이 좋습니다.**

* Microsoft Graph 탐색기를 사용 하 여 모든 역할을 앱에 패치 해야 합니다. 매니페스트 파일 방식은 권장되지 않습니다.

* 단일 AWS 앱에 대해 1,200가지 이내의 앱 역할을 추가한 후에 앱에서 작업을 수행하면 크기와 관련된 오류가 발생한다는 것을 보고하는 고객이 확인되었습니다. 애플리케이션 개체의 경우 하드 크기 제한이 있습니다.

* 역할은 추가가 아닌 바꾸기 방식으로 계정에서 추가되므로 역할을 수동으로 업데이트해야 합니다. 또한 계정이 커지는 경우 계정 및 역할과 n x n 관계가 됩니다.

* 모든 AWS 계정은 동일한 페더레이션 메타데이터 XML 파일을 사용하며, 인증서 롤오버 시 이러한 방대한 작업을 진행하여 모든 AWS 계정에서 인증서를 동시에 업데이트해야 합니다.

## <a name="prerequisites"></a>전제 조건

AWS(Amazon Web Services)와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* AWS(Amazon Web Services) Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* AWS(Amazon Web Services)에서 **SP 및 IDP** 시작 SSO 지원
* Amazon Web Services (AWS)를 구성한 후에는 세션 제어를 적용 하 여 조직에서 중요 한 데이터의 반출 및 침입를 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>갤러리에서 AWS(Amazon Web Services) 추가

Azure AD에 AWS(Amazon Web Services)를 통합하도록 구성하려면 갤러리의 AWS(Amazon Web Services)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **AWS(Amazon Web Services)** 를 입력합니다.
1. 결과 창에서 **AWS(Amazon Web Services)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

1. 응용 프로그램이 추가 되 면 **속성** 페이지로 이동 하 여 **개체 ID**를 복사 합니다.

    ![결과 목록의 AWS(Amazon Web Services)](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 AWS(Amazon Web Services) 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 AWS(Amazon Web Services)의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

AWS(Amazon Web Services)에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[AWS(Amazon Web Services) Single Sign-On 구성](#configure-amazon-web-services-aws-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 AWS(Amazon Web Services) 애플리케이션에서 Single Sign-On을 구성합니다.

**AWS(Amazon Web Services)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **AWS(Amazon Web Services)** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 앱이 Azure와 이미 사전 통합 되었으므로 사용자는 아무 단계도 수행할 필요가 없습니다. **저장**을 클릭 합니다.

5. AWS(Amazon Web Services) 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML을 사용 하 여 Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭 하 여 **클레임 & 사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | Name  | 원본 특성  | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | 역할            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | “900초(15분)에서 43200초(12시간) 사이의 값을 제공합니다.” |  `https://aws.amazon.com/SAML/Attributes` |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임 스페이스** 텍스트 상자에 해당 행에 대해 표시 되는 네임 스페이스 값을 입력 합니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **페더레이션 메타데이터 XML**을 컴퓨터에 다운로드하고 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>AWS(Amazon Web Services) Single Sign-On 구성

1. 다른 브라우저 창에서 AWS(Amazon Web Services) 회사 사이트에 관리자로 로그인합니다.

1. **AWS 홈**을 클릭합니다.

    ![Single Sign-On 홈 구성][11]

1. **ID 및 액세스 관리**를 클릭합니다.

    ![Single Sign-On ID 구성][12]

1. **ID 공급자**를 클릭한 다음 **공급자 만들기**를 클릭합니다.

    ![Single Sign-On 공급자 구성][13]

1. **공급자 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 대화 상자 구성][14]

    a. **공급자 유형**으로 **SAML**을 선택합니다.

    b. **공급자 이름** 텍스트 상자에 공급자 이름(예: *WAAD*)을 입력합니다.

    다. Azure Portal에서 다운로드한 **메타데이터 파일**을 업로드하려면 **파일 선택**을 클릭합니다.

    d. **다음 단계**를 클릭합니다.

1. **공급자 정보 확인** 대화 상자 페이지에서 **만들기**를 클릭합니다.

    ![Single Sign-On 확인 구성][15]

1. **역할**을 클릭하고 **역할 만들기**를 클릭합니다.

    ![Single Sign-On 역할 구성][16]

1. **역할 만들기** 페이지에서 다음 단계를 수행합니다.  

    ![Single Sign-On 트러스트 구성][19]

    a. **신뢰할 수 있는 엔터티 유형 선택**에서 **SAML 2.0 페더레이션**을 선택합니다.

    b. **SAML 2.0 공급자 선택**에서 이전에 만든 **SAML 공급자**를 선택합니다(예: *WAAD*).

    다. **프로그래밍 및 AWS 관리 콘솔 액세스 허용**을 선택합니다.
  
    d. **다음: 권한**을 클릭합니다.

1. 검색 창에서 **관리자 액세스** 를 검색 하 고 관리자 **액세스** 확인란을 선택한 후 **다음: 태그**를 클릭 합니다.

    ![관리자 액세스 선택](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. **태그 추가 (선택 사항)** 섹션에서 다음 단계를 수행 합니다.

    ![관리자 액세스 선택](./media/aws-multi-accounts-tutorial/config2.png)

    a. **키** 텍스트 상자에 Ex: Azureadtest의 키 이름을 입력 합니다.

    b. **값 (선택 사항)** 텍스트 상자에 다음 형식을 사용 하 여 키 값을 입력 합니다 `accountname-aws-admin` . 계정 이름은 모두 소문자 여야 합니다.

    다. **다음: 검토**를 클릭 합니다.

1. **검토** 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 검토 구성][34]

    a. **역할 이름** 텍스트 상자에 다음 패턴으로 값을 입력 합니다 `accountname-aws-admin` .

    b. **역할 설명** 텍스트 상자에 역할 이름에 사용한 것과 동일한 값을 입력 합니다.

    다. **역할 만들기**를 클릭합니다.

    d. 필요한 만큼 역할을 만들어서 ID 공급자에 매핑합니다.

    > [!NOTE]
    > 마찬가지로 accountname-금융-admin, accountname-읽기 전용-사용자, accountname-devops-user, accountname-user와 같은 나머지 다른 역할을 만듭니다. 또한 이러한 역할 정책은 AWS 계정 당 요구 사항에 따라 변경 될 수 있지만 항상 AWS 계정에서 각 역할에 대해 동일한 정책을 유지 하는 것이 좋습니다.

1. 아래 강조 표시 된 것 처럼 EC2 속성 또는 IAM 대시보드에서 해당 AWS 계정에 대 한 계정 ID를 적어 두세요.

    ![관리자 액세스 선택](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. 이제 [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 **그룹**으로 이동 합니다.

1. 이전에 만든 IAM 역할과 동일한 이름으로 새 그룹을 만들고 이러한 새 그룹의 **개체 id** 를 적어둡니다.

    ![관리자 액세스 선택](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. 현재 AWS 계정에서 로그아웃하고 Azure AD에 Single Sign-On을 구성할 다른 계정으로 로그인합니다.

1. 계정에서 역할이 모두 만들어지면 해당 계정의 **역할** 목록에 나타납니다.

    ![역할 설정](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. 모든 계정에서 모든 역할에 대해 모든 역할 ARN 및 신뢰할 수 있는 엔터티를 캡처해야 하므로 Azure AD 애플리케이션에 수동으로 매핑해야 합니다.

1. 역할을 클릭하여 **역할 ARN** 및 **신뢰할 수 있는 엔터티** 값을 복사합니다. Azure AD에서 만들어야 하는 모든 역할에 대해 이 값이 필요합니다.

    ![역할 설정](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. 모든 계정의 모든 역할에 대해 위의 단계를 수행하고 메모장에 모든 항목을 **역할 ARN, 신뢰할 수 있는 엔터티** 형식으로 저장합니다.

1. 다른 창에서 [Microsoft Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer) 를 엽니다.

    a. 테 넌 트의 전역 관리자/공동 관리자 자격 증명을 사용 하 여 Microsoft Graph Explorer 사이트에 로그인 합니다.

    b. 역할을 만들 수 있는 권한이 필요합니다. **권한 수정**을 클릭하여 필요한 권한을 얻을 수 있습니다.

    ![Microsoft Graph 탐색기 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    다. 목록에서 다음 권한을 선택하고(아직 선택하지 않은 경우) "권한 수정"을 클릭합니다. 

    ![Microsoft Graph 탐색기 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. 여기서는 다시 로그인하고 동의할 것을 요청합니다. 동의를 수락 하면 Microsoft Graph 탐색기에 다시 로그인 됩니다.

    e. 버전 드롭다운을 **베타**로 변경합니다. 테넌트로부터 모든 서비스 사용자를 가져오려면 다음 쿼리를 사용합니다.

    `https://graph.microsoft.com/beta/servicePrincipals`

    여러 디렉터리를 사용하는 경우 다음 패턴을 사용할 수 있습니다. 여기서는 주 도메인이 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`에 있습니다.

    ![Microsoft Graph 탐색기 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. 가져온 서비스 사용자 목록에서 수정할 서비스 사용자를 가져옵니다. Ctrl+F를 사용하여 나열된 모든 ServicePrincipals에서 애플리케이션을 검색할 수도 있습니다. Azure AD 속성 페이지에서 복사한 **서비스 사용자 개체 ID** 를 사용 하 여 다음 쿼리를 사용 하 여 해당 서비스 주체에 액세스할 수 있습니다.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Microsoft Graph 탐색기 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. 서비스 사용자 개체에서 appRoles 속성을 추출합니다.

    ![Microsoft Graph 탐색기 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. 이제 애플리케이션에 대한 새 역할을 생성해야 합니다. 

    i. 다음 JSON은 appRoles 개체의 예입니다. 애플리케이션에 역할을 추가할 유사한 개체를 만듭니다.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > 패치 작업에 대한 **msiam_access** 다음에만 새 역할을 추가할 수 있습니다. 또한 조직 요구에 따라 원하는 만큼의 역할을 추가할 수 있습니다. Azure AD는 SAML 응답의 클레임 값으로 이러한 역할의 **값** 을 보냅니다.

    j. Microsoft Graph 탐색기로 돌아가서 메서드를 **GET** 에서 **PATCH**로 변경 합니다. 위 예제의 것과 유사하게 appRoles 속성을 업데이트하여 원하는 역할을 갖도록 서비스 사용자 개체를 패치합니다. **쿼리 실행**을 클릭하여 패치 작업을 실행합니다. Amazon Web Services 애플리케이션에 대한 역할이 만들어졌음을 확인하는 성공 메시지가 표시됩니다.

    ![Microsoft Graph 탐색기 대화 상자](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. 서비스 사용자에 더 많은 역할이 패치되면 각 역할에 사용자/그룹을 할당할 수 있습니다. 이 작업을 수행하려면 포털로 이동한 후 Amazon Web Services 애플리케이션으로 이동합니다. 맨 위에서 **사용자 및 그룹** 탭을 클릭합니다.

1. 해당 그룹에 특정 역할을 할당할 수 있도록 모든 AWS 역할에 대해 새 그룹을 만드는 것이 좋습니다. 하나의 그룹과 하나의 역할에 대한 일대일 매핑입니다. 그런 다음, 해당 그룹에 속하는 멤버를 추가할 수 있습니다.

1. 그룹을 만든 후에는 그룹을 선택하여 애플리케이션에 할당합니다.

    ![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > 그룹을 할당할 때 중첩 그룹은 지원되지 않습니다.

1. 그룹에 역할을 할당하려면 역할을 선택하고 페이지 아래의 **할당** 단추를 클릭합니다.

    ![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > 새 역할을 확인하려면 Azure Portal에서 세션을 새로 고쳐야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 AWS(Amazon Web Services) 타일을 클릭하면 역할 선택 옵션이 없는 AWS(Amazon Web Services) 애플리케이션 페이지가 나타납니다.

![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

SAML 응답에서도 클레임으로 전달된 역할을 확인할 수 있습니다.

![Single Sign-On 구성 추가](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [MS Graph Api를 사용 하 여 프로 비전을 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [고급 표시 유형 및 컨트롤을 사용하여 AWS(Amazon Web Services)를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/protect-aws)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/