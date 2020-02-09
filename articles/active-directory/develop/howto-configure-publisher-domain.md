---
title: 앱의 게시자 도메인 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 응용 프로그램의 게시자 도메인을 구성 하 여 사용자가 자신의 정보가 전송 되는 위치를 알 수 있도록 하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697135"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>방법: 응용 프로그램의 게시자 도메인 구성

응용 프로그램의 [동의 프롬프트](application-consent-experience.md) 에서 사용자에 게 해당 정보를 보낼 위치를 알려 주는 응용 프로그램의 게시자 도메인이 표시 됩니다. 5 월 21 일 이후에 등록 된 다중 테넌트 응용 **프로그램 (게시자**도메인이 없는 2019)은 확인 되지 않음으로 표시 됩니다. 다중 테넌트 응용 프로그램은 단일 조직 디렉터리 외부의 계정을 지 원하는 응용 프로그램입니다. 예를 들어 모든 Azure AD 계정을 지원 하거나 모든 Azure AD 계정 및 개인 Microsoft 계정을 지원 합니다.

## <a name="new-applications"></a>새 응용 프로그램

새 앱을 등록 하는 경우 앱의 게시자 도메인을 기본값으로 설정할 수 있습니다. 이 값은 앱이 등록 된 위치, 특히 앱이 테넌트에 등록 되었는지 여부 및 테넌트에 테넌트 확인 된 도메인이 있는지 여부에 따라 달라 집니다.

테넌트 확인 도메인이 있는 경우 앱의 게시자 도메인은 기본적으로 테넌트의 확인 된 기본 도메인으로 변환 됩니다. 테넌트 확인 도메인이 없는 경우 (응용 프로그램이 테넌트에 등록 되지 않은 경우) 앱의 게시자 도메인은 null로 설정 됩니다.

다음 표에서는 게시자 도메인 값의 기본 동작을 요약 하 여 보여 줍니다.  

| 테넌트 확인 도메인 | 게시자 도메인의 기본값 |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (주) | domain2.com |

다중 테넌트 응용 프로그램의 게시자 도메인이 설정 되지 않았거나. onmicrosoft.com로 끝나는 도메인으로 설정 된 경우 앱의 동의 확인 표시는 게시자 도메인 대신 확인 되지 않음 **으로 표시 됩니다** .

## <a name="grandfathered-applications"></a>Grandfathered 응용 프로그램

앱이 2019 년 5 월 21 일 이전에 등록 된 경우 게시자 도메인을 설정 하지 않은 **경우 응용** 프로그램의 동의 확인 표시가 확인 되지 않습니다. 사용자가 앱의 동의 프롬프트에서이 정보를 볼 수 있도록 게시자 도메인 값을 설정 하는 것이 좋습니다.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Azure Portal를 사용 하 여 게시자 도메인 구성

앱의 게시자 도메인을 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.

1. 계정이 여러 Azure AD 테넌트에 있는 경우:
   1. 페이지의 오른쪽 위 모서리에 있는 메뉴에서 프로필을 선택한 다음, **디렉터리를 전환**합니다.
   1. 애플리케이션을 만들려는 Azure AD 테넌트로 세션을 변경합니다.

1. [Azure Active Directory > 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 으로 이동 하 여 구성 하려는 앱을 찾아 선택 합니다.

   앱을 선택 하면 앱의 **개요** 페이지가 표시 됩니다.

1. 앱의 **개요** 페이지에서 **브랜딩** 섹션을 선택 합니다.

1. **게시자 도메인** 필드를 찾고 다음 옵션 중 하나를 선택 합니다.

   - 도메인을 아직 구성 하지 않은 경우 **도메인 구성을** 선택 합니다.
   - 도메인이 이미 구성 되어 있으면 **도메인 업데이트** 를 선택 합니다.

앱이 테넌트에 등록 된 경우 선택 하는 두 개의 탭이 표시 됩니다. **확인 된 도메인을 선택** 하 고 **새 도메인을 확인**합니다.

앱이 테넌트에 등록 되지 않은 경우 응용 프로그램에 대 한 새 도메인을 확인 하는 옵션만 표시 됩니다.

### <a name="to-verify-a-new-domain-for-your-app"></a>앱에 대 한 새 도메인을 확인 하려면

1. `microsoft-identity-association.json` 라는 파일을 만들고 다음 JSON 코드 조각을 붙여 넣습니다.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. 자리 표시자 *{앱 id-여기}* 를 앱에 해당 하는 응용 프로그램 (클라이언트) id로 바꿉니다.

1. `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`에서 파일을 호스팅합니다. 확인 된 도메인과 일치 하도록 자리 표시자 *{도메인-여기}* 를 바꿉니다.

1. **도메인 확인 및 저장** 단추를 클릭 합니다.

### <a name="to-select-a-verified-domain"></a>확인 된 도메인을 선택 하려면

- 테넌트가 확인 된 도메인을 확인 하는 경우 **확인 된 도메인 선택** 드롭다운에서 도메인 중 하나를 선택 합니다.

>[!Note]
> 반환 되어야 하는 ' Content-type ' 헤더는 `application/json`합니다. 이와 같은 다른 항목을 사용 하는 경우 아래에 설명 된 대로 오류가 발생할 수 있습니다 `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>앱 동의 프롬프트의 의미

게시자 도메인을 구성 하면 앱 동의 프롬프트에서 사용자에 게 표시 되는 항목에 영향을 줍니다. 동의 프롬프트의 구성 요소를 완전히 이해 하려면 [응용 프로그램 동의 환경 이해](application-consent-experience.md)를 참조 하세요.

다음 표에서는 2019 년 5 월 21 일 이전에 만든 응용 프로그램의 동작에 대해 설명 합니다.

![2019 년 5 월 21 일 이전에 만든 앱에 대 한 동의 확인](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

5 월 21 2019 일 이후에 만들어진 새 응용 프로그램의 동작은 게시자 도메인 및 응용 프로그램 유형에 따라 달라 집니다. 다음 표에서는 다양 한 구성 조합을 사용 하 여 확인할 수 있는 변경 사항을 설명 합니다.

![5 월 21 일 이후에 생성 된 앱에 대 한 동의 확인 메시지 (2019)](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>리디렉션 Uri에 대 한 영향

회사 또는 학교 계정 또는 개인 Microsoft 계정 ([다중 테넌트](single-and-multi-tenant-apps.md))을 사용 하 여 사용자를 로그인 하는 응용 프로그램은 리디렉션 uri를 지정할 때 몇 가지 제한 사항이 적용 됩니다.

### <a name="single-root-domain-restriction"></a>단일 루트 도메인 제한

다중 테넌트 앱에 대 한 게시자 도메인 값이 null로 설정 되 면 앱은 리디렉션 Uri에 대해 단일 루트 도메인을 공유 하도록 제한 됩니다. 예를 들어 루트 도메인 contoso.com가 fabrikam.com과 일치 하지 않기 때문에 다음과 같은 값을 조합 하 여 사용할 수 없습니다.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>하위 도메인 제한

하위 도메인은 허용 되지만 루트 도메인을 명시적으로 등록 해야 합니다. 예를 들어, 다음 Uri는 단일 루트 도메인을 공유 하지만 조합은 허용 되지 않습니다.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

그러나 개발자가 루트 도메인을 명시적으로 추가 하는 경우 조합이 허용 됩니다.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>예외

다음 경우에는 단일 루트 도메인 제한이 적용 되지 않습니다.

- 단일 디렉터리에서 계정을 대상으로 하는 단일 테넌트 앱 또는 앱
- Localhost를 리디렉션 Uri로 사용
- 사용자 지정 스키마를 사용 하 여 Uri 리디렉션 (HTTP 또는 HTTPS 아님)

## <a name="configure-publisher-domain-programmatically"></a>프로그래밍 방식으로 게시자 도메인 구성

현재는 게시자 도메인을 프로그래밍 방식으로 구성 하는 REST API 또는 PowerShell 지원이 없습니다.
