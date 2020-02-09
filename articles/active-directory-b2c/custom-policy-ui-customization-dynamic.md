---
title: 사용자 지정 정책을 사용 하 여 동적으로 UI (사용자 인터페이스) 사용자 지정
titleSuffix: Azure AD B2C
description: 런타임 시 동적으로 변경되는 HTML5/CSS 콘텐츠로 여러 브랜딩 환경을 지원합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bea229f27f9a69cc4ed9e4c6e1fecf3eb79f5f90
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849215"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 동적 콘텐츠로 UI 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C) 사용자 지정 정책을 사용하면 쿼리 문자열에 매개 변수를 보낼 수 있습니다. 매개 변수를 HTML 엔드포인트로 전달하면 페이지 콘텐츠를 동적으로 변경할 수 있습니다. 예를 들어 웹 또는 모바일 애플리케이션에서 전달한 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 문서는 사용자 지정 정책을 사용하여 *동적 콘텐츠*로 Azure AD B2C 사용자 인터페이스를 사용자 지정하는 방법에 중점을 둡니다. 시작하려면 [사용자 지정 정책에서 UI 사용자 지정](custom-policy-ui-customization.md)을 참조하세요.

>[!NOTE]
>Azure AD B2C 문서인 [사용자 지정 정책에서 UI 사용자 지정 구성](custom-policy-ui-customization.md)에서는 다음과 같은 기본 사항을 설명합니다.
> * 페이지 UI(사용자 인터페이스) 사용자 지정 기능
> * 샘플 콘텐츠를 사용하여 페이지 UI 사용자 지정 기능을 테스트하기 위한 필수 도구.
> * 각 페이지 유형의 핵심 UI 요소.
> * 이 기능을 적용하는 모범 사례.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>HTML5/CSS 템플릿에 대한 링크를 사용자 경험에 추가

사용자 지정 정책에서 콘텐츠 정의는 지정된 UI 단계(예: 로그인 또는 등록 페이지)에 사용되는 HTML5 페이지 URI를 정의합니다. 기본 정책에서는 HTML5 파일의 URI를 가리켜서(CSS에서) 기본 모양과 느낌을 정의합니다. 확장 정책에서 HTML5 파일에 대한 LoadUri를 재정의하여 모양과 느낌을 수정할 수 있습니다. 콘텐츠 정의에는 적절하게 HTML5/CSS 파일을 제작하여 정의된 외부 콘텐츠에 대한 URL이 포함됩니다.

`ContentDefinitions` 섹션에는 일련의 `ContentDefinition` XML 요소가 포함됩니다. `ContentDefinition` 요소의 ID 특성은 콘텐츠 정의에 관련된 페이지의 형식을 지정합니다. 즉, 요소는 사용자 지정 HTML5/CSS 템플릿이 적용될 컨텍스트를 정의합니다. 다음 표에서는 IEF 엔진이 인식하는 콘텐츠 정의 ID 집합과 이에 관련된 페이지 형식을 설명합니다.

| 콘텐츠 정의 ID | 기본 HTML5 템플릿| Description |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **오류 페이지**입니다. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **ID 공급자 선택 페이지**입니다. 이 페이지에는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자가 나열됩니다. 이러한 옵션은 일반적으로 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **등록을 위한 ID 공급자 선택 페이지**입니다. 이 페이지에는 등록하는 동안 사용자가 선택할 수 있는 ID 공급자가 나열됩니다. 이러한 옵션은 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **암호 찾기 페이지**입니다. 이 페이지에는 사용자가 암호 재설정을 시작하기 위해 완성해야 하는 양식이 포함됩니다.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **로컬 계정 로그인 페이지**입니다. 이 페이지에는 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정으로 로그인하기 위한 양식이 있습니다. 양식에는 텍스트 입력 상자 및 암호 입력란이 포함될 수 있습니다. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **로컬 계정 등록 페이지** 이 페이지에는 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정으로 등록하기 위한 양식이 있습니다. 양식에는 텍스트 입력란, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같이 다양한 입력 컨트롤이 포함될 수 있습니다. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 페이지**입니다. 등록 또는 로그인 중에 사용자가 이 페이지에서 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **소셜 계정 등록 페이지**입니다. 이 페이지에는 소셜 ID 공급자의 기존 계정을 사용하여 등록할 때 사용자가 완성해야 하는 양식이 있습니다. 암호 입력 필드를 제외하고는 위의 소셜 계정 등록 페이지와 비슷합니다. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **프로필 업데이트 페이지**입니다. 이 페이지에는 사용자가 자신의 프로필을 업데이트하기 위해 액세스할 수 있는 양식이 있습니다. 암호 입력 필드를 제외하고는 소셜 계정 등록 페이지와 비슷합니다. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **통합 등록 또는 로그인 페이지**입니다. 이 페이지는 사용자 등록 및 로그인 프로세스를 처리합니다. 사용자는 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정을 사용할 수 있습니다.  |

## <a name="serving-dynamic-content"></a>동적 콘텐츠 제공
[사용자 지정 정책에서 UI 사용자 지정 구성](custom-policy-ui-customization.md) 문서에서 Azure Blob Storage에 HTML5 파일을 업로드했습니다. 해당 HTML5 파일은 고정적이고 각 요청에 동일한 HTML 콘텐츠를 렌더링합니다.

이 문서에서는 쿼리 문자열 매개 변수를 수락하고 적절하게 응답할 수 있는 ASP.Net 웹앱을 사용합니다.

이 연습에서는 다음을 수행합니다.
* HTML5 템플릿을 호스팅하는 ASP.NET Core 웹 애플리케이션 만들기
* 사용자 지정 HTML5 템플릿 _unified.cshtml_ 추가
* Azure App Service에 웹앱 게시
* 웹앱에 대한 CORS(원본 간 리소스 공유) 설정
* HTML5 파일을 가리키도록 `LoadUri` 요소 재정의

## <a name="step-1-create-an-aspnet-web-app"></a>1단계: ASP.NET 웹앱 만들기

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택하여 프로젝트를 만듭니다.

2. **새 프로젝트** 창에서 **Visual C#**  > **웹** > **ASP.NET Core 웹 애플리케이션(.NET Core)** 을 선택합니다.

3. 애플리케이션 이름(예:*Contoso.AADB2C.UI*)을 지정한 후 **확인**을 선택합니다.

    ![새 Visual Studio 프로젝트 만들기](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. **웹 애플리케이션** 템플릿을 선택합니다.

5. 인증을 **인증 없음**으로 설정합니다.

    ![웹 애플리케이션 템플릿 선택](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. **확인**을 선택하여 프로젝트를 만듭니다.

## <a name="step-2-create-mvc-view"></a>2단계: MVC 뷰 만들기
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>2\.1단계: B2C 기본 제공 HTML5 템플릿 다운로드
사용자 지정 HTML5 템플릿은 Azure AD B2C 기본 제공 HTML5 템플릿을 기반으로 합니다. [unified.html 파일](https://login.microsoftonline.com/static/tenant/default/unified.cshtml)을 다운로드하거나 [시작 팩](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)에서 템플릿을 다운로드할 수 있습니다. 이 HTML5 파일을 사용하여 통합된 등록 또는 로그인 페이지를 만듭니다.

### <a name="step-22-add-the-mvc-view"></a>2\.2단계 MVC 뷰 추가
1. Views/Home 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가** > **새 항목**을 클릭합니다.

    ![Visual Studio에서 새 항목 추가 메뉴 항목](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. **새 항목 추가 - Contoso.AADB2C.UI** 창에서 **웹 > ASP.NET**을 선택합니다.

3. **MVC 보기 페이지**를 선택합니다.

4. **이름** 상자에서 이름을 **unified.cshtml**로 변경합니다.

5. **추가**를 선택합니다.

    ![MVC 뷰 페이지가 강조 표시 된 Visual Studio의 새 항목 추가 대화 상자](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. *unified.cshtml* 파일이 열려 있지 않으면 파일을 두 번 클릭하여 연 다음 파일 콘텐츠를 지웁니다.

7. 이 연습에서는 레이아웃 페이지에 대한 참조를 제거합니다. 다음 코드 조각을 _unified.cshtml_에 추가합니다.

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Azure AD B2C 기본 제공 HTML5 템플릿에서 다운로드한 _unified.cshtml_ 파일을 엽니다.

9. 단일 기호(@)를 이중 기호(@@)로 바꿉니다.

10. 파일 콘텐츠를 복사하여 레이아웃 정의 아래에 붙여넣습니다. 코드는 다음과 비슷해야 합니다.

    ![HTML5를 추가한 후 unified.cshtml 파일](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>2\.3단계: 배경 이미지 변경

`ID` 값 *background_background_image*가 포함된 `<img>` 요소를 찾은 다음, `src` 값을 **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** 또는 사용하려는 다른 배경 이미지로 바꿉니다.

![사용자 지정 background_background_image src 값을 사용 하는 img 요소](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>2\.4단계: MVC 컨트롤러에 보기 추가

1. **Controllers\HomeController.cs**를 열고 다음 메서드를 추가합니다.

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    이 코드는 메서드가 *View* 템플릿 파일을 사용하여 브라우저에 응답을 렌더링하도록 지정합니다. *View* 템플릿 파일의 이름을 명시적으로 지정하지 않기 때문에 MVC는 */Views/Home* 폴더의 _unified.cshtml_ 보기 파일을 기본적으로 사용합니다.

    _unified_ 메서드를 추가한 후 코드는 다음과 비슷해야 합니다.

    ![컨트롤러를 변경하여 보기 렌더링](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. 웹앱을 디버그하여 _unified_ 페이지(예: `http://localhost:<Port number>/Home/unified`)에 액세스할 수 있도록 합니다.

### <a name="step-25-publish-to-azure"></a>2\.5단계 Azure에 게시
1. **솔루션 탐색기**에서 **Contoso.AADB2C.UI** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시**를 선택합니다.

    ![Microsoft Azure App Service에 게시](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. **Microsoft Azure App Service** 타일을 게시 대상으로 선택한 다음 **게시**를 선택합니다.

    ![Microsoft Azure App Service 만들기](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **App Service 만들기** 창이 열립니다. 여기에서 Azure에서 ASP.NET 웹앱을 실행하는 데 필요한 모든 Azure 리소스를 만들 수 있습니다.

    > [!NOTE]
    > 게시에 대한 자세한 내용은 [Azure에서 ASP.NET 웹앱 만들기](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)를 참조하세요.

3. **웹앱 이름** 상자에서 고유한 앱 이름을 입력합니다(유효한 문자: a-z, A-Z, 0-9 및 -(하이픈)). 웹앱의 URL은 `http://<app_name>.azurewebsites.NET`이며, 여기서 `<app_name>`은 웹앱 이름입니다. 자동으로 생성된 이름을 적용할 수 있습니다. 이 이름은 고유합니다.

4. **만들기**를 선택하여 Azure 리소스를 만듭니다.

    ![App Service 속성 제공](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-publish3.png)

    만들기 프로세스가 완료되면 마법사가 Azure에 ASP.NET 웹앱을 게시한 다음 기본 브라우저에서 앱을 시작합니다.

5. _unified_ 페이지의 URL(예: _https://<app_name>.azurewebsites.net/home/unified_)을 복사합니다.

## <a name="step-3-configure-cors-in-azure-app-service"></a>3단계: Azure App Service에서 CORS 구성
1. [Azure Portal](https://portal.azure.com/)에서 **App Services**를 클릭한 다음 API 앱의 이름을 선택합니다.

    ![Azure Portal에서 API 앱 선택](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. **설정** 섹션에서 **API** 섹션 아래 **CORS**를 선택합니다.

    ![Azure Portal의 App Service 메뉴에 CORS 메뉴 항목이 강조 표시 됨](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. **CORS** 창의 **허용된 원본** 상자에 다음 중 하나를 수행합니다.

    * JavaScript 호출을 허용할 URL을 입력합니다. URL을 입력할 때는 모두 소문자를 사용해야 합니다.
    * 별표( * )를 입력하여 모든 원본 도메인이 허용되도록 지정합니다.

4. **저장**을 선택합니다.

    ![허용 된 원본에서 별표가 강조 표시 된 CORS 설정 페이지](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    **저장**을 클릭하면 API 앱이 지정된 URL의 JavaScript 호출을 수락합니다.

## <a name="step-4-html5-template-validation"></a>4단계: HTML5 템플릿 유효성 검사
HTML5 템플릿을 사용할 준비가 되었습니다. 하지만 `ContentDefinition` 코드로 사용할 수 없습니다. `ContentDefinition`을 사용자 지정 정책에 추가하기 전에 다음을 확인하십시오.
* 콘텐츠가 HTML5와 호환되며 액세스가 가능합니다.
* 콘텐츠 서버가 CORS에 대해 사용하도록 설정되었습니다.

    >[!NOTE]
    >콘텐츠를 호스팅하는 사이트에서 CORS를 사용할 수 있고 CORS 요청을 테스트할 수 있는지 확인하려면 [test-cors.org](https://test-cors.org/) 웹 사이트로 이동합니다.

* 제공된 콘텐츠는 **HTTPS**를 통해 보호됩니다.
* 모든 링크, CSS 콘텐츠 및 이미지에 대해 `https://yourdomain/content`와 같은 *절대 url*을 사용 합니다.

## <a name="step-5-configure-your-content-definition"></a>5단계: 콘텐츠 정의 구성
`ContentDefinition`을 구성하려면 다음을 수행합니다.
1. 정책의 기본 파일(예: *TrustFrameworkBase.xml*)을 엽니다.

2. `<ContentDefinitions>` 요소를 검색한 다음 `<ContentDefinitions>` 노드의 전체 콘텐츠를 복사합니다.

3. 확장 파일(예: *TrustFrameworkExtensions.xml*)을 연 다음 `<BuildingBlocks>` 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.

4. 복사한 `<ContentDefinitions>` 노드의 전체 콘텐츠를 `<BuildingBlocks>` 요소의 자식으로 붙여넣습니다.

5. 복사한 XML에서 `Id="api.signuporsignin"`을 포함하는 `<ContentDefinition>` 노드를 검색합니다.

6. `LoadUri` 값을 _~/tenant/default/unified_에서 _https://<app_name>.azurewebsites.net/home/unified_로 변경합니다.
    사용자 지정 정책이 다음과 비슷해야 합니다.

    ![LoadUri 요소가 강조 표시 된 예제 XML 코드 조각](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6단계: 테넌트에 정책 업로드
1. [Azure Portal](https://portal.azure.com)포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.

1. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.

1. **ID 경험 프레임워크**를 선택합니다.

1. **모든 정책**을 선택합니다.

1. **정책 업로드**를 선택합니다.

1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.

1. *TrustFrameworkExtensions.xml* 파일을 업로드하고 유효성 검사를 통과하는지 확인합니다.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7단계: 지금 실행을 사용하여 사용자 지정 정책 테스트
1. **Azure AD B2C 설정**을 선택한 다음 **ID 경험 프레임워크**를 선택합니다.

    >[!NOTE]
    >지금 실행을 사용하려면 하나 이상의 애플리케이션이 테넌트에 미리 등록되어 있어야 합니다. 애플리케이션을 등록하는 방법은 Azure AD B2C [시작](tutorial-create-tenant.md) 문서 또는 [애플리케이션 등록](tutorial-register-applications.md) 문서를 참조하세요.

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.
    앞에서 만든 배경으로 사용자 지정 HTML5를 볼 수 있어야 합니다.

    ![등록 또는 로그인 정책](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>8단계: 동적 콘텐츠 추가
_campaignId_라는 쿼리 문자열 매개 변수에 기반하여 백그라운드를 변경합니다. RP 애플리케이션(웹 및 모바일 앱)이 Azure AD B2C에 매개 변수를 보냅니다. 정책에서는 매개 변수를 읽고 HTML5 템플릿에 해당 값을 보냅니다.

### <a name="step-81-add-a-content-definition-parameter"></a>8\.1단계 콘텐츠 정의 매개 변수 추가

다음을 수행하여 `ContentDefinitionParameters` 요소를 추가합니다.
1. 정책의 *SignUpOrSignin* 파일(예: *SignUpOrSignin.xml*)을 엽니다.

2. `<DefaultUserJourney>` 노드에 `UserJourneyBehaviors` 노드를 추가합니다.

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>8\.2단계 쿼리 문자열 매개 변수를 수락하고 배경 이미지를 대체하도록 코드 변경
HomeController `unified` 메서드를 수정하여 campaignId 매개 변수를 수락합니다. 그런 다음 메서드는 매개 변수 값을 확인하고 `ViewData["background"]` 변수를 적절하게 설정합니다.

1. *Controllers\HomeController.cs* 파일을 열어서 다음 코드 조각을 추가하여 `unified` 메서드를 변경합니다.

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. `background_background_image` ID를 사용하여 `<img>` 요소를 찾고 `src` 값을 `@ViewData["background"]`로 바꿉니다.

    ![src 값이 강조 표시 된 img 요소 ](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: 변경 내용 업로드 및 정책 게시
1. Azure App Service에 Visual Studio 프로젝트를 게시합니다.

2. *SignUpOrSignin.xml* 정책을 Azure AD B2C에 업로드합니다.

3. 업로드한 RP 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.
    이전에 표시된 것과 동일한 배경 이미지가 나타납니다.

4. 브라우저의 주소 표시줄에서 URL을 복사합니다.

5. URI에 _campaignId_ 쿼리 문자열 매개 변수를 추가합니다. 예를 들어 다음 이미지와 같이 `&campaignId=hawaii`를 추가합니다.

    ![CampaignId 쿼리 문자열 매개 변수가 강조 표시 된 URI](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. **Enter**를 선택하여 하와이 배경 이미지를 표시합니다.

    ![하와이 이미지 사용자 지정 배경으로 등록 로그인 페이지](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. 값을 *도쿄*로 변경하고 **Enter**를 선택합니다.
    브라우저에 도쿄 배경이 표시됩니다.

    ![도쿄 이미지 사용자 지정 배경으로 등록 로그인 페이지](./media/custom-policy-ui-customization-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>9단계: 사용자 경험의 나머지 변경
로그인 페이지에서 **지금 등록** 링크를 선택하면 브라우저는 정의한 이미지가 아닌 기본 배경 이미지를 표시합니다. 이 동작은 등록 또는 로그인 페이지만 변경했기 때문에 발생합니다. 나머지 자체 어설션 콘텐츠 정의를 변경하려면 다음을 수행하십시오.
1. "2단계"로 돌아가서 다음을 수행합니다.

    a. *selfasserted* 파일을 다운로드합니다.

    b. 파일 내용을 복사합니다.

    다. 새 보기 *selfasserted*를 만듭니다.

    d. *selfasserted*를 **Home** 컨트롤러에 추가합니다.

2. "4단계"로 돌아가서 다음을 수행합니다.

    a. 확장 정책에서 `Id="api.selfasserted"`, `Id="api.localaccountsignup"` 및 `Id="api.localaccountpasswordreset"`를 포함하는 `<ContentDefinition>` 노드를 찾습니다.

    b. `LoadUri` 특성을 *selfasserted* URI로 설정합니다.

3. "8.2단계"로 돌아가서 쿼리 문자열 매개 변수를 수락하도록 코드를 변경합니다. 하지만 이번에는 *selfasserted* 함수로 변경합니다.

4. *TrustFrameworkExtensions.xml* 정책을 업로드하고 유효성 검사를 통과하는지 확인합니다.

5. 정책 테스트를 실행한 다음 **지금 등록**을 선택하여 결과를 확인합니다.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(선택 사항)완성 정책 파일 및 코드 다운로드
* [사용자 지정 정책 시작](custom-policy-get-started.md) 연습을 완료한 후에 고유한 사용자 지정 정책 파일을 사용하여 시나리오를 빌드하는 것이 좋습니다. 참조를 위한 [샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)이 제공됩니다.
* [참조를 위한 Visual Studio 솔루션 샘플](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)에서 전체 코드를 다운로드할 수 있습니다.
