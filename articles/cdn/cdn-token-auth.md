---
title: 보안 토큰 인증을 사용하여 Azure CDN 자산 보안 유지 | Microsoft Docs
description: 토큰 인증을 사용하여 Azure CDN 자산에 대한 액세스 보안을 유지하는 방법을 알아봅니다.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593384"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>보안 토큰 인증을 사용하여 Azure CDN 자산 보안 유지

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>개요

토큰 인증은 Azure CDN(Content Delivery Network)이 권한 없는 클라이언트에 자산을 제공하지 못하도록 할 수 있는 메커니즘입니다. 토큰 인증은 일반적으로 다른 웹 사이트(예: 메시지 보드)에서 권한 없이 사용자의 자산을 사용하는 콘텐츠 *핫링크*를 방지하기 위해 수행됩니다. 핫링크는 콘텐츠 배달 비용에 영향을 줄 수 있습니다. CDN에서 토큰 인증을 사용하도록 설정하면 CDN에서 콘텐츠를 전송하기 전에 요청이 CDN 에지 서버에서 인증됩니다. 

## <a name="how-it-works"></a>작동 방법

토큰 인증은 요청에 요청자에 대한 인코딩된 정보가 포함된 토큰 값을 포함하도록 요구하여 요청이 신뢰할 수 있는 사이트에서 생성되었는지 확인합니다. 인코딩된 정보가 요구 사항을 충족하는 경우에만 요청자에게 콘텐츠가 제공되며, 그렇지 않으면 요청이 거부됩니다. 다음 매개 변수 중 하나 이상을 사용하여 요구 사항을 설정할 수 있습니다.

- 국가: 지정 된 국가/지역에서 발생 하는 요청 허용 하거나 거부 자신의 [국가 코드](/previous-versions/azure/mt761717(v=azure.100))합니다.
- URL: 지정 된 자산 또는 경로 일치 하는 요청만 허용 합니다.
- 호스트: 허용 하거나 요청 헤더에 지정된 된 호스트를 사용 하는 요청을 거부 합니다.
- 참조 페이지: 허용 하거나 지정된 된 참조 페이지에서 요청을 거부 합니다.
- IP 주소: 특정 IP 주소 또는 IP 서브넷에서 시작 된 요청만 허용 합니다.
- 프로토콜: 허용 하거나 콘텐츠를 요청 하는 데 사용 된 프로토콜에 따라 요청을 거부 합니다.
- 만료 시간: 링크 제한 된 기간 동안만 유효한 상태로 유지 하기 위해 날짜 및 시간 기간을 할당 합니다.

자세한 내용은 [토큰 인증 설정](#setting-up-token-authentication)에서 각 매개 변수에 대한 자세한 구성 예를 참조하세요.

>[!IMPORTANT] 
> 이 계정의 모든 경로에 대해 토큰 권한 부여를 사용하도록 설정하면 표준 캐시 모드만 쿼리 문자열 캐시에 사용할 수 있습니다. 자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](cdn-query-string-premium.md)를 참조하세요.

## <a name="reference-architecture"></a>참조 아키텍처

다음 워크플로 다이어그램은 CDN이 토큰 인증을 사용하여 웹앱 작업을 하는 방법을 설명합니다.

![CDN 토큰 인증 워크플로](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN 엔드포인트의 토큰 유효성 검사 논리
    
다음 순서도에서는 CDN 엔드포인트에 토큰 인증이 구성된 경우 Azure CDN 클라이언트에서 요청의 유효성을 검사하는 방법을 설명합니다.

![CDN 토큰 유효성 검사 논리](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>토큰 인증 설정

1. [Azure Portal](https://portal.azure.com)에서 CDN 프로필로 이동한 다음, **관리**를 선택하여 보조 포털을 시작합니다.

    ![CDN 프로필 관리 단추](./media/cdn-token-auth/cdn-manage-btn.png)

2. **HTTP Large**를 가리킨 다음, 플라이아웃에서 **토큰 인증**을 선택합니다. 그런 다음 암호화 키와 암호화 매개 변수를 다음과 같이 설정할 수 있습니다.

   1. 하나 이상의 암호화 키를 만듭니다. 암호화 키는 대/소문자를 구분하고 모든 영숫자 조합을 포함할 수 있습니다. 공백을 비롯한 다른 형식의 문자는 허용되지 않습니다. 최대 길이는 250자입니다. 암호화 키가 임의의 값이 되도록 하려면 [OpenSSL 도구](https://www.openssl.org/)를 사용하여 만드는 것이 좋습니다. 

      OpenSSL 도구에는 다음 구문이 있습니다.

      ```rand -hex <key length>```

      예를 들어:

      ```OpenSSL> rand -hex 32``` 

      가동 중지 시간을 방지하려면 기본 및 백업 키를 모두 만듭니다. 백업 키는 기본 키를 업데이트하는 경우 콘텐츠에 중단 없는 액세스를 제공합니다.
    
   2. **기본 키** 상자에 고유한 암호화 키를 입력하고 선택적으로 **백업 키** 상자에 백업 키를 입력합니다.

   3. 해당 **최소 암호화 버전** 목록에서 각 키에 사용할 최소 암호화 버전을 선택한 다음, **업데이트**를 클릭합니다.
      - **V2**: 버전 2.0과 3.0 토큰을 생성할 키를 사용할 수 있음을 나타냅니다. 레거시 버전 2.0 암호화 키에서 버전 3.0 키로 전환하는 경우에만 이 옵션을 사용합니다.
      - **V3**: (권장) 버전 3.0 토큰을 생성할 키를 사용할만 수 있음을 나타냅니다.

      ![CDN 토큰 인증 설정 키](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. 암호화 도구를 사용하여 암호화 매개 변수를 설정하고 토큰을 생성합니다. 암호화 도구를 사용 하 여 허용 하거나 만료 시간, 국가/지역, 참조 페이지, 프로토콜 및 클라이언트 IP (조합)을 기반으로 요청을 거부할 수 있습니다. 토큰을 구성하기 위해 결합될 수 있는 매개 변수의 수 및 조합은 제한되지 않지만 토큰의 총 길이는 512자로 제한됩니다. 

      ![CDN 암호화 도구](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      **암호화 도구** 섹션에서 다음 암호화 매개 변수 중 하나 이상에 대한 값을 입력합니다. 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>매개 변수 이름</th> 
      >   <th>Description</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>토큰이 만료되는 토큰 만료 시간을 할당합니다. 만료 시간이 지나면 제출된 요청이 거부됩니다. 이 매개 변수는 `1/1/1970 00:00:00 GMT` 표준 Unix Epoch 이후의 초 수를 기반으로 하는 Unix 타임스탬프를 사용합니다. 온라인 도구를 사용하여 표준시와 Unix 시간 간을 변환할 수 있습니다.> 
      >    예를 들어 토큰이 `12/31/2016 12:00:00 GMT`에 만료되도록 하려면 Unix 타임스탬프 값 `1483185600`을 입력합니다. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>특정 자산 또는 경로에 맞게 토큰을 조정할 수 있습니다. 해당 URL이 특정 상대 경로로 시작하는 요청에 대한 액세스를 제한합니다. URL은 대/소문자를 구분합니다. 각 경로를 쉼표로 구분하여 여러 경로를 입력하고 공백을 추가하지 않도록 합니다. 요구 사항에 따라 서로 다른 값을 설정하여 서로 다른 수준의 액세스를 제공할 수 있습니다.> 
      >    예를 들어 URL `http://www.mydomain.com/pictures/city/strasbourg.png`의 경우 다음 입력 값에 대한 요청이 허용됩니다. 
      >    <ul>
      >       <li>입력 값 `/`: 모든 요청이 허용 됩니다.</li>
      >       <li>입력 값 `/pictures`: 다음 요청이 허용됩니다. <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>입력 값 `/pictures/`: 포함 된 요청만 `/pictures/` 경로 허용 됩니다. `http://www.mydomain.com/pictures/city/strasbourg.png` )을 입력합니다.</li>
      >       <li>입력 값 `/pictures/city/strasbourg.png`: 이 특정 경로 및 자산에 대한 요청만 허용 됩니다.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>하나 이상의 지정 된 국가/지역에서 시작 된 요청만 허용 합니다. 모든 다른 국가/지역에서 발생 하는 요청이 거부 됩니다. 각 국가에 대해 두 문자 [ISO 3166 국가 코드](/previous-versions/azure/mt761717(v=azure.100))를 사용하고 각각을 쉼표로 구분하며 공백은 추가하지 않도록 합니다. 예를 들어 미국 및 프랑스에서만 액세스를 허용하려면 `US,FR`을 입력합니다.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>하나 이상의 지정 된 국가/지역에서 발생 하는 요청을 거부 합니다. 모든 다른 국가/지역에서 시작 된 요청만 허용 됩니다. 구현은 <b>ec_country_allow</b> 매개 변수와 동일합니다. 국가 코드가 <b>ec_country_allow</b> 및 <b>ec_country_deny</b> 매개 변수 둘 다로 표시되면 <b>ec_country_allow</b> 매개 변수가 우선합니다.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>지정된 참조 페이지의 요청만 허용합니다. 참조 페이지는 요청 중인 리소스에 연결된 웹 페이지의 URL을 식별합니다. 매개 변수 값에 프로토콜을 포함하지 않아야 합니다.> 
      >    다음 유형의 입력이 허용됩니다.
      >    <ul>
      >       <li>호스트 이름 또는 호스트 이름과 경로</li>
      >       <li>여러 참조 페이지. 여러 참조 페이지를 추가하려면 각 참조 페이지를 쉼표로 구분하고 공백을 추가하지 않도록 합니다. 참조 페이지 값을 지정했지만 브라우저 구성으로 인해 요청에서 참조 페이지 정보가 전송되지 않는 경우 해당 요청은 기본적으로 거부됩니다.</li> 
      >       <li>참조 페이지 정보가 누락되었거나 비어 있는 요청. 기본적으로 <b>ec_ref_allow</b> 매개 변수는 이러한 유형의 요청을 차단합니다. 이러한 요청을 허용하려면 텍스트 "missing"을 입력하거나 빈 값(후행 쉼표 사용)을 입력합니다.</li> 
      >       <li>하위 도메인. 하위 도메인을 허용하려면 별표(\*)를 입력합니다. 예를 들어 `contoso.com`의 모든 하위 도메인을 허용하려면 `*.contoso.com`을 입력합니다.</li>
      >    </ul> 
      >    예를 들어 `www.contoso.com`의 요청, `contoso2.com`의 모든 하위 도메인 및 공백이나 누락된 참조 페이지의 요청에 대한 액세스를 허용하려면 `www.contoso.com,*.contoso.com,missing`을 입력합니다.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>지정된 참조 페이지의 요청을 거부합니다. 구현은 <b>ec_ref_allow</b> 매개 변수와 동일합니다. 참조 페이지가 <b>ec_ref_allow</b> 및 <b>ec_ref_deny</b> 매개 변수 둘 다로 표시되면 <b>ec_ref_allow</b> 매개 변수가 우선합니다.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>지정된 프로토콜의 요청만 허용합니다. 유효한 값은 `http`, `https` 또는 `http,https`입니다.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>지정된 프로토콜의 요청을 거부합니다. 구현은 <b>ec_proto_allow</b> 매개 변수와 동일합니다. 프로토콜이 <b>ec_proto_allow</b> 및 <b>ec_proto_deny</b> 매개 변수 둘 다로 표시되면 <b>ec_proto_allow</b> 매개 변수가 우선합니다.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>지정된 요청자의 IP 주소에 대한 액세스를 제한합니다. IPV4 및 IPV6 모두 지원됩니다. 단일 요청 IP 주소 또는 특정 서브넷과 연결된 IP 주소를 지정할 수 있습니다. 예를 들어 `11.22.33.0/22`는 IP 주소 11.22.32.1~11.22.35.254의 요청을 허용합니다.</td>
      > </tr>
      > </table>

   5. 암호화 매개 변수 값 입력을 마친 후에는 **암호화할 키** 목록에서 암호화할 키(기본 키와 백업 키를 모두 만든 경우)를 선택합니다.
    
   6. 암호화 버전을 선택 합니다 **암호화 버전** 목록: **V2** 버전 2 용 또는 **V3** 버전 3 (권장). 

   7. **암호화**를 선택하여 토큰을 생성합니다.

      토큰을 생성하면 **생성된 토큰** 상자에 표시됩니다. 토큰을 사용하려면 쿼리 문자열로 URL 경로의 파일 끝에 추가합니다. `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b` )을 입력합니다.
        
   8. 필요에 따라 토큰의 매개 변수를 볼 수 있도록 암호 해독 도구로 토큰을 테스트합니다. **암호를 해독할 토큰** 상자에 토큰 값을 붙여넣습니다. **해독할 키** 목록에서 사용할 암호화 키를 선택한 다음, **암호 해독**을 선택합니다.

      토큰의 암호를 해독한 후에 해당 매개 변수는 **원래 매개 변수** 상자에 표시됩니다.

   9. 필요에 따라 요청이 거부될 때 반환되는 응답 코드 유형을 사용자 지정할 수 있습니다. **사용**을 선택하고 **응답 코드** 목록에서 응답 코드를 선택합니다. **헤더 이름**이 자동으로 **위치**로 설정됩니다. **저장**을 선택하여 새 응답 코드를 구현합니다. 특정 응답 코드의 경우 **헤더 값** 상자에 오류 페이지의 URL도 입력해야 합니다. **403** 응답 코드(사용할 수 없음)가 기본적으로 선택됩니다. 

3. **HTTP Large**에서 **규칙 엔진**을 선택합니다. 규칙 엔진을 사용하여 기능을 적용할 경로를 정의하고, 토큰 인증 기능을 사용하도록 설정하고, 기능과 관련된 추가 토큰 인증을 사용하도록 설정합니다. 자세한 내용은 [규칙 엔진 참조](cdn-rules-engine-reference.md)를 참조하세요.

   1. 기존 규칙을 선택하거나 새 규칙을 만들어 토큰 인증을 적용할 자산 또는 경로를 정의합니다. 
   2. 규칙에서 토큰 인증을 사용하도록 설정하려면 **기능** 목록에서 **[토큰 인증](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** 을 선택한 다음 **사용**을 선택합니다. 규칙을 업데이트하는 경우 **업데이트**를 선택하고, 규칙을 만드는 경우 **추가**를 선택합니다.
        
      ![CDN 규칙 엔진 토큰 인증 사용 예제](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. 규칙 엔진에서 추가 토큰 인증 관련 기능을 사용하도록 설정할 수도 있습니다. 다음 기능 중 하나를 사용하도록 설정하려면 **기능** 목록에서 해당 기능을 선택한 다음 **사용**을 선택합니다.
    
   - **[토큰 인증 거부 코드](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)** : 요청이 거부 되는 경우 사용자에 게 반환할 응답 유형을 결정 합니다. 여기서 설정된 규칙은 토큰 기반 인증 페이지의 **사용자 지정 거부 처리** 섹션에 설정된 응답 코드를 재정의합니다.

   - **[토큰 인증 URL 대/소문자를 무시](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** : 토큰의 유효성을 검사 하는 데 URL은 대/소문자 구분 여부를 결정 합니다.

   - **[토큰 인증 매개 변수](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)** : 요청된 된 URL에 표시 되는 토큰 인증 쿼리 문자열 매개 변수의 이름을 바꿉니다. 
        
     ![CDN 규칙 엔진 토큰 인증 설정 예제](./media/cdn-token-auth/cdn-rules-engine2.png)

5. [GitHub](https://github.com/VerizonDigital/ectoken)의 소스 코드에 액세스하여 토큰을 사용자 지정할 수 있습니다.
   사용 가능한 언어는 다음과 같습니다.
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN 기능 및 공급자 가격 책정

기능에 대한 정보는 [Azure CDN 제품 기능](cdn-features.md)을 참조하세요. 가격 책정에 대한 정보는 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.
