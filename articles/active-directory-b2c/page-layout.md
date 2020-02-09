---
title: 페이지 레이아웃 선택-Azure Active Directory B2C
description: Azure Active Directory B2C에서 페이지 레이아웃을 선택 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 452687f3886a85bea796e3899410667ee1d592fa
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840318"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>사용자 지정 정책을 사용 하 여 Azure Active Directory B2C에서 페이지 레이아웃 선택

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

사용자 흐름이 나 사용자 지정 정책을 사용 하 고 있는지 여부에 관계 없이 Azure Active Directory B2C (Azure AD B2C) 정책에서 JavaScript 클라이언트 쪽 코드를 사용 하도록 설정할 수 있습니다. 응용 프로그램에 대해 JavaScript를 사용 하도록 설정 하려면 [사용자 지정 정책](custom-policy-overview.md)에 요소를 추가 하 고, 페이지 레이아웃을 선택 하 고, 요청에 [b2clogin.com](b2clogin.md) 를 사용 해야 합니다.

페이지 레이아웃은 Azure AD B2C에서 제공 하는 요소와 사용자가 제공 하는 콘텐츠를 연결 하는 것입니다.

이 문서에서는 사용자 지정 정책에서 페이지 레이아웃을 구성 하 여 Azure AD B2C에서 페이지 레이아웃을 선택 하는 방법을 설명 합니다.

> [!NOTE]
> 사용자 흐름에 JavaScript를 사용 하도록 설정 하려면 [Azure Active Directory B2C의 javascript 및 페이지 레이아웃 버전](user-flow-javascript-overview.md)을 참조 하세요.

## <a name="replace-datauri-values"></a>DataUri 값 바꾸기

사용자 지정 정책에서 사용자 경험에 사용되는 HTML 템플릿을 정의하는 [ContentDefinitions](contentdefinitions.md)이 있을 수 있습니다. **ContentDefinition**에는 Azure AD B2C에서 제공하는 페이지 요소를 참조하는 **DataUri**가 포함되어 있습니다. **LoadUri**는 사용자가 제공하는 HTML 및 CSS 콘텐츠의 상대 경로입니다.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

페이지 레이아웃을 선택 하려면 정책에서 [Contentdefinitions](contentdefinitions.md) 의 **datauri** 값을 변경 합니다. 이전 **DataUri** 값을 새 값을 전환하면 변경할 수 없는 패키지를 선택하는 것입니다. 이 패키지를 사용 하면 변경 되지 않으며 페이지에서 예기치 않은 동작이 발생할 수 있다는 것을 알 수 있습니다.

이전 **Datauri** 값을 사용 하는 사용자 지정 정책에서 페이지 레이아웃을 지정 하려면 `elements`와 페이지 유형 (예: `selfasserted`) 사이에 `contract`을 삽입 하 고 버전 번호를 지정 합니다. 예:

| 이전 DataUri 값 | 새 DataUri 값 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>버전 변경 로그

페이지 레이아웃 패키지는 주기적으로 업데이트 되어 페이지 요소에 수정 및 향상 된 기능이 포함 됩니다. 다음 변경 로그는 각 버전에 도입 된 변경 내용을 지정 합니다.

### <a name="200"></a>2.0.0

- 자체 어설션 페이지 (`selfasserted`)
  - 사용자 지정 정책의 [표시 컨트롤](display-controls.md) 에 대한 지원이 추가 되었습니다.

### <a name="120"></a>1.2.0

- 모든 페이지
  - 내게 필요한 옵션 수정
  - 이제 HTML 태그에 `data-preload="true"` 특성을 추가 하 여 CSS 및 JavaScript에 대한 로드 순서를 제어할 수 있습니다. 시나리오에는 다음이 포함됩니다.
    - CSS 링크를 사용 하 여 HTML과 동시에 CSS를 로드 하 여 파일 로드 사이에 ' 깜박임 '를 하지 않도록 합니다.
    - 이 특성을 사용 하면 페이지를 로드 하기 전에 스크립트 태그를 가져오고 실행 하는 순서를 제어할 수 있습니다.
  - 이제 전자 메일 필드가 `type=email` 되 고 모바일 키보드가 올바른 제안을 제공 합니다.
  - Chrome 변환 지원
- 통합 및 자체 어설션된 페이지
  - 이제 사용자 이름/전자 메일 및 암호 필드에 양식 HTML 요소가 사용 됩니다.  이제 Edge 및 IE가이 정보를 제대로 저장할 수 있습니다.

### <a name="110"></a>1.1.0

- 예외 페이지 (globalexception)
  - 내게 필요한 옵션 수정
  - 정책의 연락처가 없을 때 기본 메시지가 제거 됨
  - 기본 CSS 제거 됨
- MFA 페이지 (다단계)
  - ' 코드 확인 ' 단추 제거 됨
  - 이제 코드에 대한 입력 필드에 최대 6 자까지 입력을 사용 합니다.
  - 페이지는 단추를 클릭 하지 않고 6 자리 코드를 입력할 때 입력 한 코드를 자동으로 확인 하려고 시도 합니다.
  - 코드가 잘못 된 경우 입력 필드가 자동으로 지워집니다.
  - 잘못 된 코드를 사용 하 여 3 번 시도 하면 B2C는 신뢰 당사자에 게 오류를 다시 보냅니다.
  - 내게 필요한 옵션 수정
  - 기본 CSS 제거 됨
- 자체 어설션 페이지 (selfasserted)
  - 제거 경고 취소
  - 오류 요소에 대한 CSS 클래스
  - 향상 된 오류 논리 표시/숨기기
  - 기본 CSS 제거 됨
- 통합 SSP (unifiedssp)
  - KMSI (로그인 유지) 컨트롤이 추가 됨

### <a name="100"></a>1.0.0

- 초기 릴리스

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C의 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](custom-policy-ui-customization.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.
