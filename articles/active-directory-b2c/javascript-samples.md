---
title: JavaScript 샘플
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 JavaScript를 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1381ddb16697b1e892794604bbfafda815bd6182
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149079"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용하기 위한 JavaScript 샘플

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

사용자 고유의 JavaScript 클라이언트 쪽 코드를 Azure Active Directory B2C (Azure AD B2C) 응용 프로그램에 추가할 수 있습니다.

응용 프로그램에 JavaScript를 사용 하도록 설정 하려면

* [사용자 지정 정책](custom-policy-overview.md) 에 요소 추가
* [페이지 레이아웃](page-layout.md) 선택
* 요청에서 [b2clogin.com](b2clogin.md) 사용

이 문서에서는 사용자 지정 정책을 변경 하 여 스크립트 실행을 사용 하도록 설정 하는 방법을 설명 합니다.

> [!NOTE]
> 사용자 흐름에 JavaScript를 사용 하도록 설정 하려면 [Azure Active Directory B2C의 javascript 및 페이지 레이아웃 버전](user-flow-javascript-overview.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="select-a-page-layout"></a>페이지 레이아웃 선택

* 응용 프로그램의 사용자 인터페이스 요소에 대 한 [페이지 레이아웃](contentdefinitions.md#select-a-page-layout) 을 선택 합니다.

    JavaScript를 사용 하려는 경우 사용자 지정 정책의 *모든* 콘텐츠 정의에 대해 페이지 `contract` 버전으로 [페이지 레이아웃 버전을 정의](contentdefinitions.md#migrating-to-page-layout) 해야 합니다.

## <a name="add-the-scriptexecution-element"></a>ScriptExecution 요소 추가

**RelyingParty** 요소에 [ScriptExecution](relyingparty.md) 요소를 추가하여 스크립트 실행을 가능하게 합니다.

1. 사용자 지정 정책 파일(예: *SignUpOrSignin.xml*)을 엽니다.
2. **RelyingParty**의 **UserJourneyBehaviors** 요소에 **ScriptExecution** 요소를 추가합니다.

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. 파일을 저장하고 업로드합니다.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript 샘플

### <a name="show-or-hide-a-password"></a>암호 표시 또는 숨기기

고객이 가입에 성공하도록 지원하는 일반적인 방법은 고객이 암호로 입력한 내용을 확인할 수 있도록 하는 것입니다. 이 옵션은 사용자가 필요한 경우 쉽게 암호를 보고 수정할 수 있도록 하여 가입을 지원합니다. 암호 유형의 필드에는 **암호 표시** 레이블이 지정된 확인란이 있습니다.  사용자는 이 확인란을 통해 암호를 일반 텍스트로 볼 수 있습니다. 자체 어설션된 페이지의 가입 또는 로그인 템플릿에 다음 코드 조각을 포함합니다.

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>사용 약관 추가

**사용 약관** 확인란을 포함하려는 페이지에 다음 코드를 포함합니다. 이 확인란은 일반적으로 로컬 계정 가입 및 소셜 계정 가입 페이지에 필요합니다.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

코드에서 `termsOfUseUrl`을 해당 사용 약관 링크로 바꿉니다. 디렉터리에 대해 **termsOfUse** 라는 새 사용자 특성을 만든 다음 **termsOfUse** 를 사용자 특성으로 포함 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C의 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](custom-policy-ui-customization.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.
