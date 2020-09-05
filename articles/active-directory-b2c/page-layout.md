---
title: 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: 사용자 지정 정책에서 UI 사용자 지정을 위한 페이지 레이아웃 버전 기록입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852068"
---
# <a name="page-layout-versions"></a>페이지 레이아웃 버전

페이지 레이아웃 패키지는 주기적으로 업데이트 되어 페이지 요소에 수정 및 향상 된 기능이 포함 됩니다. 다음 변경 로그는 각 버전에 도입 된 변경 내용을 지정 합니다.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>자체 어설션 페이지 (selfasserted)

**2.1.0**

- 지역화 및 내게 필요한 옵션 수정.

**2.0.0**

- 사용자 지정 정책의 [표시 컨트롤](display-controls.md) 에 대 한 지원이 추가 되었습니다.

**1.2.0**

- 이제 사용자 이름/전자 메일 및 암호 필드에 `form` HTML 요소를 사용 하 여 Edge 및 Internet Explorer (IE)가이 정보를 제대로 저장할 수 있도록 합니다.
- 사용자 환경을 개선 하기 위해 구성 가능한 사용자 입력 유효성 검사 지연을 추가 했습니다.
-  접근성 수정
- 이제 `data-preload="true"` [HTML 태그에](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 특성을 추가 하 여 CSS 및 JavaScript에 대 한 로드 순서를 제어할 수 있습니다.
  - 연결 된 CSS 파일을 HTML 템플릿과 동시에 로드 하 여 파일 로드 사이에 ' 깜박임 ' 하지 않도록 합니다.
  - `script`페이지를 로드 하기 전에 태그를 가져오고 실행 하는 순서를 제어 합니다.
- 전자 메일 필드는 지금 이며 `type=email` 모바일 키보드는 올바른 제안 사항을 제공 합니다.
- Chrome 변환 지원

**1.1.0**

- 제거 경고 취소
- 오류 요소에 대 한 CSS 클래스
- 향상 된 오류 논리 표시/숨기기
- 기본 CSS 제거 됨

**1.0.0**

- 초기 릴리스

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>암호 재설정 링크를 사용 하는 통합 로그인 등록 페이지 (unifiedssp)

**2.1.0**

- 여러 등록 링크에 대 한 지원이 추가 되었습니다.
- 정책에 정의 된 조건자 규칙에 따라 사용자 입력 유효성 검사에 대 한 지원이 추가 되었습니다.

**1.2.0**

- 이제 사용자 이름/전자 메일 및 암호 필드에 `form` HTML 요소를 사용 하 여 Edge 및 Internet Explorer (IE)가이 정보를 제대로 저장할 수 있도록 합니다.
-  접근성 수정
- 이제 `data-preload="true"` [HTML 태그에](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 특성을 추가 하 여 CSS 및 JavaScript에 대 한 로드 순서를 제어할 수 있습니다.
  - 연결 된 CSS 파일을 HTML 템플릿과 동시에 로드 하 여 파일 로드 사이에 ' 깜박임 ' 하지 않도록 합니다.
  - `script`페이지를 로드 하기 전에 태그를 가져오고 실행 하는 순서를 제어 합니다.
- 전자 메일 필드는 지금 이며 `type=email` 모바일 키보드는 올바른 제안 사항을 제공 합니다.
- Chrome 변환 지원

**1.1.0**

- KMSI (로그인 유지) 컨트롤이 추가 됨

**1.0.0**

- 초기 릴리스

## <a name="mfa-page-multifactor"></a>MFA 페이지 (다단계)

**1.2.1**

- 기본 템플릿에 대 한 내게 필요한 옵션 수정

**1.2.0**

-  접근성 수정
- 이제 `data-preload="true"` [HTML 태그에](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 특성을 추가 하 여 CSS 및 JavaScript에 대 한 로드 순서를 제어할 수 있습니다.
  - 연결 된 CSS 파일을 HTML 템플릿과 동시에 로드 하 여 파일 로드 사이에 ' 깜박임 ' 하지 않도록 합니다.
  - `script`페이지를 로드 하기 전에 태그를 가져오고 실행 하는 순서를 제어 합니다.
- 전자 메일 필드는 지금 이며 `type=email` 모바일 키보드는 올바른 제안 사항을 제공 합니다.
- Chrome 변환 지원

**1.1.0**

- ' 코드 확인 ' 단추 제거 됨
- 이제 코드에 대 한 입력 필드에 최대 6 자까지 입력을 사용 합니다.
- 페이지는 단추를 클릭 하지 않고 6 자리 코드를 입력할 때 입력 한 코드를 자동으로 확인 하려고 시도 합니다.
- 코드가 잘못 된 경우 입력 필드가 자동으로 지워집니다.
- 잘못 된 코드를 사용 하 여 3 번 시도 하면 B2C는 신뢰 당사자에 게 오류를 다시 보냅니다.
-  접근성 수정
- 기본 CSS 제거 됨

**1.0.0**

- 초기 릴리스

## <a name="exception-page-globalexception"></a>예외 페이지 (globalexception)

**1.2.0**

-  접근성 수정
- 이제 `data-preload="true"` [HTML 태그에](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 특성을 추가 하 여 CSS 및 JavaScript에 대 한 로드 순서를 제어할 수 있습니다.
  - 연결 된 CSS 파일을 HTML 템플릿과 동시에 로드 하 여 파일 로드 사이에 ' 깜박임 ' 하지 않도록 합니다.
  - `script`페이지를 로드 하기 전에 태그를 가져오고 실행 하는 순서를 제어 합니다.
- 전자 메일 필드는 지금 이며 `type=email` 모바일 키보드는 올바른 제안 사항을 제공 합니다.
- Chrome 변환 지원

**1.1.0**

- 내게 필요한 옵션 수정
- 정책의 연락처가 없을 때 기본 메시지가 제거 됨
- 기본 CSS 제거 됨

**1.0.0**

- 초기 릴리스

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>기타 페이지 (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

-  접근성 수정
- 이제 `data-preload="true"` [HTML 태그에](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) 특성을 추가 하 여 CSS 및 JavaScript에 대 한 로드 순서를 제어할 수 있습니다.
  - 연결 된 CSS 파일을 HTML 템플릿과 동시에 로드 하 여 파일 로드 사이에 ' 깜박임 ' 하지 않도록 합니다.
  - `script`페이지를 로드 하기 전에 태그를 가져오고 실행 하는 순서를 제어 합니다.
- 전자 메일 필드는 지금 이며 `type=email` 모바일 키보드는 올바른 제안 사항을 제공 합니다.
- Chrome 변환 지원

**1.0.0**

- 초기 릴리스

## <a name="next-steps"></a>다음 단계

사용자 지정 정책에서 응용 프로그램의 사용자 인터페이스를 사용자 지정 하는 방법에 대 한 자세한 내용은 [사용자 지정 정책을 사용 하 여 응용 프로그램의 사용자 인터페이스](custom-policy-ui-customization.md)사용자 지정을 참조 하세요.
