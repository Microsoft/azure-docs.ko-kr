---
title: 사용자 지정 정책을 사용 하 여 앱의 사용자 인터페이스 사용자 지정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 사용자 인터페이스를 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42426e8249f3b6ceb592254833f38ab0df99c83c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848968"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서를 완료 하면 브랜드와 모양을 사용 하 여 등록 및 로그인 사용자 지정 정책을 사용할 수 있습니다. Azure AD B2C(Azure Active Directory B2C)를 사용하면 사용자에게 제공되는 HTML 및 CSS 콘텐츠를 거의 완벽하게 제어할 수 있습니다. 사용자 지정 정책을 사용하는 경우 Azure Portal의 컨트롤을 사용하는 대신 XML로 UI 사용자 지정을 구성합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.

## <a name="page-ui-customization"></a>페이지 UI 사용자 지정

페이지 UI 사용자 지정 기능을 사용하면 사용자 지정 정책의 모양과 느낌을 사용자 지정할 수 있습니다. 또한 애플리케이션과 Azure AD B2C 간에 브랜드와 시각적 개체 일관성을 유지할 수 있습니다.

작동 방식은 다음과 같습니다. Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)라는 최신의 방법을 사용합니다. 먼저, 사용자 지정된 HTML 콘텐츠가 있는 사용자 지정 정책에서 URL을 지정합니다. Azure AD B2C는 UI 요소를 URL에서 로드된 HTML 콘텐츠와 병합한 다음 고객에게 해당 페이지를 표시합니다.

## <a name="create-your-html5-content"></a>HTML5 콘텐츠 만들기

제목에 제품의 브랜드 이름이 포함된 HTML 콘텐츠를 만듭니다.

1. 다음 HTML 코드 조각을 복사합니다. *\<body\>* 태그 내에 있는 *\<div id="api"\>\</div\>* 라는 빈 요소와 함께 잘 구성된 HTML5입니다. 이 요소는 Azure AD B2C 콘텐츠가 삽입되는 위치를 나타냅니다.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. 복사한 코드 조각을 텍스트 편집기에 붙여넣은 다음 *customize-ui.html*이라는 파일로 저장합니다.

> [!NOTE]
> Login.microsoftonline.com를 사용 하는 경우 보안 제한으로 인해 HTML 양식 요소가 제거 됩니다. 사용자 지정 HTML 콘텐츠에서 HTML 양식 요소를 사용 하려면 b2clogin.com를 사용 하세요. 다른 혜택은 [B2clogin.com 사용](b2clogin.md) 을 참조 하세요.

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 만들기

>[!NOTE]
> 이 가이드에서는 Azure Blob Storage를 사용하여 콘텐츠를 호스팅합니다. 웹 서버에서 콘텐츠를 호스팅하도록 선택할 수 있지만 [웹 서버에서 CORS를 사용하도록 설정](https://enable-cors.org/server.html)(영문)해야 합니다.

Blob storage에서이 HTML 콘텐츠를 호스팅하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **허브** 메뉴에서 **새로 만들기** > **스토리지** > **스토리지 계정**을 차례로 선택합니다.
1. 스토리지 계정에 대한 **구독**을 선택합니다.
1. **리소스 그룹**을 만들거나 기존 그룹을 선택합니다.
1. 스토리지 계정의 고유한 **이름**을 입력합니다.
1. 스토리지 계정에 대한 **지리적 위치**를 선택합니다.
1. **배포 모델**은 **Resource Manager**로 유지하면 됩니다.
1. **성능**은 **표준**으로 유지하면 됩니다.
1. **계정 종류**를 **Blob Storage**로 변경합니다.
1. **복제**는**RA-GRS**로 유지하면 됩니다.
1. **액세스 계층**은 **핫**으로 유지하면 됩니다.
1. **검토 + 만들기** 를 클릭 하 여 저장소 계정을 만듭니다.
    배포가 완료 되 면 **저장소 계정** 페이지가 자동으로 열립니다.

## <a name="create-a-container"></a>컨테이너 만들기

Blob storage에서 공용 컨테이너를 만들려면 다음 단계를 수행 합니다.

1. 왼쪽 메뉴의 **Blob service** 에서 **blob**을 선택 합니다.
1. **+ 컨테이너**를 클릭 합니다.
1. **이름**에 *root*를 입력 합니다. 이 이름은 선택한 이름 (예: *wingtiptoys*) 일 수 있지만이 예제에서는 *루트* 를 사용 하는 것이 간단 합니다.
1. **공용 액세스 수준**에서 **Blob**, **확인**을 차례로 선택 합니다.
1. **루트** 를 클릭 하 여 새 컨테이너를 엽니다.
1. **업로드**를 클릭합니다.
1. **파일 선택** 옆에 있는 폴더 아이콘을 클릭합니다.
1. 페이지 UI 사용자 지정 섹션에서 이전에 만든 **customize-ui** 을 탐색 하 고 선택 합니다.
1. 하위 폴더에 업로드 하려는 경우 **고급** 을 확장 하 고 **폴더에 업로드**폴더에 폴더 이름을 입력 합니다.
1. **업로드**를 선택합니다.
1. 업로드 한 **customize-ui** blob을 선택 합니다.
1. **Url** 텍스트 상자의 오른쪽에서 **클립보드에 복사** 아이콘을 선택 하 여 url을 클립보드에 복사 합니다.
1. 웹 브라우저에서 복사한 URL로 이동 하 여 업로드 한 blob에 액세스할 수 있는지 확인 합니다. 액세스할 수 없는 경우, 예를 들어 `ResourceNotFound` 오류가 발생 한 경우 컨테이너 액세스 형식이 **blob**으로 설정 되어 있는지 확인 합니다.

## <a name="configure-cors"></a>CORS 구성

다음 단계를 수행 하 여 원본 간 리소스 공유에 대한 Blob 저장소를 구성 합니다.

1. 메뉴에서 **CORS**를 선택합니다.
1. **허용된 원본**에 `https://your-tenant-name.b2clogin.com`을 입력합니다. `your-tenant-name`은 Azure AD B2C 테넌트의 이름으로 바꿉니다. `https://fabrikam.b2clogin.com`)을 입력합니다. 테넌트 이름을 입력할 때는 모두 소문자를 사용해야 합니다.
1. **허용된 메소드**에서 `GET`과 `OPTIONS`를 모두 선택합니다.
1. **허용된 헤더**에 별표(*)를 입력합니다.
1. **노출된 헤더**에 별표(*)를 입력합니다.
1. **최대 기간**에 200을 입력합니다.
1. **저장**을 클릭합니다.

## <a name="test-cors"></a>CORS 테스트

다음 단계를 수행 하 여 준비가 되었는지 확인 합니다.

1. [www.test-cors.org](https://www.test-cors.org/) 웹 사이트로 이동한 다음, **원격 URL** 상자에 URL을 붙여넣습니다.
1. **요청 보내기**를 클릭합니다.
    오류가 발생하는 경우 [CORS 설정](#configure-cors)이 올바른지 확인합니다. Ctrl+Shift+P를 눌러 브라우저 캐시를 비우거나 개인 검색 세션을 열어야 할 수도 있습니다.

## <a name="modify-the-extensions-file"></a>확장 파일 수정

UI 사용자 지정을 구성하려면 **ContentDefinition** 및 해당 자식 요소를 기본 파일에서 확장 파일로 복사합니다.

1. 정책의 기본 파일을 엽니다(예: 예를 들어 <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> 합니다. 사용자 지정 정책 시작 팩에 포함 된 정책 파일 중 하나로, 필수 구성 요소, [사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)에서 가져와야 합니다.
1. **ContentDefinitions** 요소의 전체 내용을 검색한 후 복사합니다.
1. 확장 파일을 엽니다(예: 예: *TrustFrameworkExtensions.xml* **BuildingBlocks** 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 **ContentDefinitions**의 전체 내용을 **BuildingBlocks** 요소의 자식으로 붙여 넣습니다.
1. 복사한 XML에서 `Id="api.signuporsignin"`을 포함하는 **ContentDefinition** 요소를 검색합니다.
1. **LoadUri** 값을 스토리지에 업로드한 HTML 파일의 URL로 변경합니다. `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`)을 입력합니다.

    사용자 지정 정책이 다음과 비슷해야 합니다.

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 확장 파일을 저장합니다.

## <a name="upload-and-test-your-updated-custom-policy"></a>업데이트 된 사용자 지정 정책 업로드 및 테스트

### <a name="upload-the-custom-policy"></a>사용자 지정 정책 업로드

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure AD B2C**를 검색 하 고 선택 합니다.
1. **정책**에서 **Id 경험 프레임 워크**를 선택 합니다.
1. **사용자 지정 정책 업로드**를 선택 합니다.
1. 이전에 변경한 확장 파일을 업로드합니다.

### <a name="test-the-custom-policy-by-using-run-now"></a>**지금 실행**을 사용하여 사용자 지정 정책 테스트

1. 업로드 한 정책을 선택 하 고 **지금 실행**을 선택 합니다.
1. 이메일 주소를 사용하여 등록할 수 있습니다.

## <a name="reference"></a>참조

### <a name="sample-templates"></a>샘플 템플릿
UI 사용자 지정을 위한 샘플 템플릿은 다음에서 찾을 수 있습니다.

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

sample_templates/wingtip 폴더에는 다음과 같은 HTML 파일이 있습니다.

| HTML5 템플릿 | Description |
|----------------|-------------|
| *phonefactor.html* | 다단계 인증 페이지의 템플릿으로 사용합니다. |
| *resetpassword.html* | 암호 찾기 페이지의 템플릿으로 사용합니다. |
| *selfasserted.html* | 소셜 계정 등록 페이지, 로컬 계정 등록 페이지 또는 로컬 계정 로그인 페이지의 템플릿으로 사용합니다. |
| *unified.html* | 통합 등록 또는 로그인 페이지의 템플릿으로 사용합니다. |
| *updateprofile.html* | 프로필 업데이트 페이지의 템플릿으로 사용합니다. |

샘플을 사용 하는 방법에 대한 단계는 다음과 같습니다.

1. 로컬 컴퓨터에서 리포지토리를 복제 합니다. Sample_templates에서 템플릿 폴더를 선택 합니다. `wingtip` 또는 `contoso`를 사용할 수 있습니다.
1. 이전 섹션에 설명 된 대로 `css`, `fonts`및 `images` 폴더에 있는 모든 파일을 Blob 저장소에 업로드 합니다.
1. 그런 다음 `wingtip` 또는 `contoso` (첫 번째 단계에서 선택한 항목 중 하나)의 루트에서 각 \*파일을 열고 "http://localhost"의 모든 인스턴스를 2 단계에서 업로드 한 css, 이미지 및 글꼴 파일의 Url로 바꿉니다.
1. \*파일을 저장 하 고 Blob 저장소에 업로드 합니다.
1. 이제 [확장 파일 수정](#modify-the-extensions-file)의 앞에서 설명한 대로 확장 파일을 수정 합니다.
1. 누락 된 글꼴, 이미지 또는 CSS가 표시 되 면 확장 정책 및 \*파일에서 참조를 확인 합니다.

### <a name="content-definition-ids"></a>콘텐츠 정의 ID

등록 또는 로그인 사용자 지정 정책 수정 섹션에서 `api.idpselections`에 대한 콘텐츠 정의를 구성했습니다. 다음 표에서는 Azure AD B2C ID 환경 프레임워크에서 인식되는 콘텐츠 정의 ID 및 해당 설명에 대한 전체 집합을 보여 줍니다.

| 콘텐츠 정의 ID | Description |
|-----------------------|-------------|
| *api.error* | **오류 페이지**입니다. 예외 또는 오류가 발생하면 이 페이지가 표시됩니다. |
| *api.idpselections* | **ID 공급자 선택 페이지**입니다. 이 페이지는 로그인하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 옵션은 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.idpselections.signup* | **등록을 위한 ID 공급자 선택 페이지**입니다. 이 페이지는 등록하는 동안 사용자가 선택할 수 있는 ID 공급자의 목록을 포함합니다. 이러한 옵션은 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook, Google+) 또는 로컬 계정입니다. |
| *api.localaccountpasswordreset* | **암호 찾기 페이지**입니다. 사용자가 암호 재설정을 시작하기 위해 완성해야 하는 양식이 있습니다.  |
| *api.localaccountsignin* | **로컬 계정 로그인 페이지**입니다. 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정으로 로그인하기 위한 양식이 있습니다. 양식에는 텍스트 입력 상자 및 암호 입력란이 포함될 수 있습니다. |
| *api.localaccountsignup* | **로컬 계정 등록 페이지**입니다. 이메일 주소 또는 사용자 이름을 기반으로 하는 로컬 계정을 등록하기 위한 양식이 있습니다. 양식에는 텍스트 입력란, 암호 입력란, 라디오 단추, 단일 선택 드롭다운 상자 및 다중 선택 확인란과 같이 다양한 입력 컨트롤이 포함될 수 있습니다. |
| *api.phonefactor* | **Multi-Factor Authentication 페이지**입니다. 등록 또는 로그인 중에 사용자가 이 페이지에서 텍스트 또는 음성을 사용하여 전화 번호를 확인할 수 있습니다. |
| *api.selfasserted* | **소셜 계정 등록 페이지**입니다. 소셜 ID 공급자(예: Facebook 또는 Google+)에서 기존 계정을 사용하여 등록할 때 사용자가 완성해야 하는 등록 양식이 있습니다. 암호 입력 필드를 제외하고는 위의 소셜 계정 등록 페이지와 비슷합니다. |
| *api.selfasserted.profileupdate* | **프로필 업데이트 페이지**입니다. 사용자가 자신의 프로필을 업데이트하는 데 사용할 수 있는 양식이 있습니다. 암호 입력 필드를 제외하고는 소셜 계정 등록 페이지와 비슷합니다. |
| *api.signuporsignin* | **통합 등록 또는 로그인 페이지**입니다. 엔터프라이즈 ID 공급자, 소셜 ID 공급자(예: Facebook 또는 Google+) 또는 로컬 계정을 사용할 수 있는 사용자의 등록과 로그인을 모두 처리합니다.  |

## <a name="next-steps"></a>다음 단계

사용자 지정할 수 있는 UI 요소에 대한 자세한 내용은 [사용자 흐름에 대한 ui 사용자 지정을 위한 참조 가이드](customize-ui-overview.md)를 참조 하세요.
