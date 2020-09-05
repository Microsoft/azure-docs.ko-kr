---
title: '자습서: 기존 사용자 지정 DNS 이름 매핑'
description: Azure App Service의 웹앱, 모바일 앱 백 엔드 또는 API 앱에 기존 사용자 지정 DNS 도메인 이름(베니티 도메인)을 추가하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 도메인 매핑, 도메인 이름, 기존 도메인, 호스트 이름
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/13/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: 1496f46eb29831dfb858f061ccc00c9e3dbc2e75
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782314"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>자습서: Azure App Service에 기존 사용자 지정 DNS 이름 매핑

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 기존 사용자 지정 DNS 이름을 Azure App Service에 매핑하는 방법을 보여줍니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * CNAME 레코드를 사용하여 하위 도메인(예: `www.contoso.com`) 매핑
> * A 레코드를 사용하여 루트 도메인(예: `contoso.com`) 매핑
> * CNAME 레코드를 사용하여 와일드카드 도메인(예: `*.contoso.com`) 매핑
> * 사용자 지정 디렉터리로 기본 URL 리디렉션
> * 스크립트로 도메인 매핑 자동화

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* [App Service 앱을 만들거나](/azure/app-service/) 다른 자습서에서 만든 앱을 사용합니다.
* 도메인 이름을 구입하고 도메인 공급자(예: GoDaddy)의 DNS 레지스트리에 대한 액세스 권한이 있는지 확인합니다.

  예를 들어 `contoso.com` 및 `www.contoso.com`에 대한 DNS 항목을 추가하려면 `contoso.com` 루트 도메인에 대한 DNS 설정을 구성할 수 있어야 합니다.

  > [!NOTE]
  > 기존 도메인 이름이 없으면 [Azure Portal을 사용하여 도메인을 구입](manage-custom-dns-buy-domain.md)하는 것이 좋습니다.

## <a name="prepare-the-app"></a>앱 준비

사용자 지정 DNS 이름을 웹앱에 매핑하려면 웹앱의 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 유료 계층(**공유**, **기본**, **표준**, **프리미엄** 또는 Azure Functions의 경우 **사용량**)이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털](https://portal.azure.com)에서 Azure 계정으로 로그인합니다.

### <a name="select-the-app-in-the-azure-portal"></a>Azure Portal에서 앱 선택

**App Services**를 검색하여 선택합니다.

![App Service 선택](./media/app-service-web-tutorial-custom-domain/app-services.png)

**App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service 앱의 관리 페이지가 표시됩니다.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)** 를 선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **F1** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **F1** 계층에서는 지원되지 않습니다. 

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service 계획이 **F1** 계층이 아닌 경우 **스케일업** 페이지를 닫고 [CNAME 레코드 매핑](#cname)으로 건너뜁니다.

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

유료 계층(**D1**, **B1**, **B2**, **B3** 또는 **프로덕션** 범주의 모든 계층) 중 하나를 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기**를 클릭합니다.

**적용**을 클릭합니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

다음 알림이 표시되면 강화 작업이 완료됩니다.

![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-domain-verification-id"></a>도메인 확인 ID 가져오기

앱에 사용자 지정 도메인을 추가하려면 도메인 공급자를 사용하여 확인 ID를 TXT 레코드로 추가하여 도메인 소유권을 확인해야 합니다. 앱 페이지의 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 클릭합니다. 다음 단계에 대한 **사용자 지정 도메인** 페이지에서 **사용자 지정 도메인 확인 ID**를 복사합니다.

![사용자 지정 도메인 확인 ID 가져오기](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> 도메인 확인 ID를 사용자 지정 도메인에 추가하면 현수 DNS 항목을 방지하고 하위 도메인 인수를 피할 수 있습니다. 이 일반적인 높은 심각도 위협에 대한 자세한 내용은 [하위 도메인 인수](../security/fundamentals/subdomain-takeover.md)을 참조하세요.

## <a name="map-your-domain"></a>도메인 매핑

**CNAME 레코드** 또는 **A 레코드**를 사용하여 사용자 지정 DNS 이름을 App Service에 매핑할 수 있습니다. 각 단계를 따릅니다.

- [CNAME 레코드 매핑](#map-a-cname-record)
- [A 레코드 매핑](#map-an-a-record)
- [(CNAME 레코드를 사용하여) 와일드카드 도메인 매핑](#map-a-wildcard-domain)

> [!NOTE]
> 루트 도메인을 제외한 모든 사용자 지정 DNS 이름에 대해 CNAME 레코드를 사용해야 합니다(예를 들어 `contoso.com`). 루트 도메인의 경우 A 레코드를 사용합니다.

### <a name="map-a-cname-record"></a>CNAME 레코드 매핑

자습서 예제에서는 `www` 하위 도메인(예: `www.contoso.com`)에 대한 CNAME 레코드를 추가합니다.

`www` 이외의 하위 도메인이 있는 경우 `www`를 하위 도메인으로 바꿉니다(예: 사용자 지정 도메인이 `sub.constoso.com`인 경우 `sub`).

#### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

하위 도메인을 앱의 기본 도메인 이름(`<app-name>.azurewebsites.net`, 여기서 `<app-name>`은 사용자 앱의 이름)에 매핑합니다. `www` 하위 도메인에 대한 CNAME 매핑을 만들려면 다음과 같은 두 개의 레코드를 만듭니다.

| 레코드 형식 | 호스트 | 값 | 주석 |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | 도메인 자체 매핑. |
| TXT | `asuid.www` | [이전에 가져온 확인 ID](#get-domain-verification-id) | App Service는 `asuid.<subdomain>` TXT 레코드에 액세스하여 사용자 지정 도메인의 소유권을 확인합니다. |

CNAME 및 TXT 레코드를 추가하면 DNS 레코드 페이지는 다음 예제와 비슷합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Azure에서 CNAME 레코드 매핑 사용

Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다.

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

앱의 **사용자 지정 도메인** 페이지에서 정규화된 사용자 지정 DNS 이름(`www.contoso.com`)을 목록에 추가합니다.

**사용자 지정 도메인 추가** 옆에 있는 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

`www.contoso.com`과 같이 CNAME 레코드를 추가한 정규화된 도메인 이름을 입력합니다.

**유효성 검사**를 선택합니다.

**사용자 지정 도메인 추가** 페이지가 표시됩니다.

**호스트 이름 레코드 형식**이 **CNAME(www\.example.com 또는 하위 도메인)** 으로 설정되어 있는지 확인합니다.

**사용자 지정 도메인 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> 사용자 지정 도메인에 대한 **안전하지 않음** 레이블은 TLS/SSL 인증서에 아직 바인딩되지 않았음을 의미하며 브라우저에서 사용자 지정 도메인에 대한 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 받습니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.

이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

![확인 오류](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>A 레코드 매핑

자습서의 예제에서는 루트 도메인(예: `contoso.com`)에 대한 A 레코드를 추가합니다.

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>앱의 IP 주소 복사

A 레코드를 매핑하려면 앱의 외부 IP 주소가 필요합니다. 이 IP 주소는 Azure Portal에서 보여 주는 해당 앱의 **사용자 지정 도메인** 페이지에서 찾을 수 있습니다.

Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다.

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**사용자 지정 도메인** 페이지에서 앱의 IP 주소를 복사합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>A 레코드 만들기

A 레코드를 앱(일반적으로 루트 도메인)에 매핑하려면 두 개의 레코드를 만듭니다.

| 레코드 형식 | 호스트 | 값 | 주석 |
| - | - | - |
| A | `@` | [앱의 IP 주소 복사](#info)에서 가져온 IP 주소 | 도메인 자체 매핑(`@`는 일반적으로 루트 도메인을 나타냄). |
| TXT | `asuid` | [이전에 가져온 확인 ID](#get-domain-verification-id) | App Service는 `asuid.<subdomain>` TXT 레코드에 액세스하여 사용자 지정 도메인의 소유권을 확인합니다. 루트 도메인의 경우 `asuid`를 사용합니다. |

> [!NOTE]
> 권장되는 [CNAME 레코드](#map-a-cname-record) 대신 A 레코드를 사용하여 하위 도메인을 추가하려면(예: `www.contoso.com`) A 레코드와 TXT 레코드는 다음 표와 같이 표시되어야 합니다.
>
> | 레코드 형식 | 호스트 | 값 |
> | - | - | - |
> | A | `www` | [앱의 IP 주소 복사](#info)에서 가져온 IP 주소 |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

레코드를 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![DNS 레코드 페이지](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>앱에서 A 레코드 매핑 사용

Azure Portal에서 해당 앱의 **사용자 지정 도메인** 페이지로 돌아가서 정규화된 사용자 지정 DNS 이름(예: `contoso.com`)을 목록에 추가합니다.

**사용자 지정 도메인 추가** 옆에 있는 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

`contoso.com`과 같이 A 레코드를 구성한 정규화된 도메인 이름을 입력합니다.

**유효성 검사**를 선택합니다.

**사용자 지정 도메인 추가** 페이지가 표시됩니다.

**호스트 이름 레코드 형식**이 **A 레코드(example.com)** 로 설정되어 있는지 확인합니다.

**사용자 지정 도메인 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 A 레코드](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> 사용자 지정 도메인에 대한 **안전하지 않음** 레이블은 TLS/SSL 인증서에 아직 바인딩되지 않았음을 의미하며 브라우저에서 사용자 지정 도메인에 대한 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 받습니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.

이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

![확인 오류](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>와일드카드 도메인 매핑

이 자습서의 예제에서는 CNAME 레코드를 추가하여 [와일드카드 DNS 이름](https://en.wikipedia.org/wiki/Wildcard_DNS_record)(예: `*.contoso.com`)을 App Service 앱에 매핑합니다.

#### <a name="access-dns-records-with-domain-provider"></a>도메인 공급자로 DNS 레코드 액세스

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>CNAME 레코드 만들기

와일드카드 이름 `*`를 앱의 기본 도메인 이름(`<app-name>.azurewebsites.net`, 여기서 `<app-name>`은 앱 이름)에 매핑합니다. 와일드카드 이름을 매핑하려면 두 개의 레코드를 만듭니다.

| 레코드 형식 | 호스트 | 값 | 주석 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | 도메인 자체 매핑. |
| TXT | `asuid` | [이전에 가져온 확인 ID](#get-domain-verification-id) | App Service는 `asuid` TXT 레코드에 액세스하여 사용자 지정 도메인의 소유권을 확인합니다. |

`*.contoso.com` 도메인 예제의 경우 CNAME 레코드에서는 `*` 이름을 `<app-name>.azurewebsites.net`에 매핑합니다.

CNAME을 추가하면 DNS 레코드 페이지가 다음 예제와 비슷합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>앱에서 CNAME 레코드 매핑 사용

이제 와일드카드 이름과 일치하는 모든 하위 도메인을 앱에 추가할 수 있습니다(예: `sub1.contoso.com`, `sub2.contoso.com`, `*.contoso.com`은 모두 `*.contoso.com`과 일치).

Azure Portal의 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다.

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**사용자 지정 도메인 추가** 옆에 있는 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

와일드카드 도메인과 일치하는 정규화된 도메인 이름(예: `sub1.contoso.com`)을 입력한 다음 **유효성 검사**를 선택합니다.

**사용자 지정 도메인 추가** 단추가 활성화됩니다.

**호스트 이름 레코드 형식**이 **CNAME 레코드(www\.example.com or any subdomain)** 로 설정되어 있는지 확인합니다.

**사용자 지정 도메인 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

**+** 아이콘을 다시 선택하여 와일드카드 도메인과 일치하는 또 다른 사용자 지정 도메인을 추가합니다. 예를 들어 `sub2.contoso.com`을 추가합니다.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> 사용자 지정 도메인에 대한 **Note Secure** 레이블은 TLS/SSL 인증서에 아직 바인딩되지 않았음을 의미하며 브라우저에서 사용자 지정 도메인에 대한 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 받습니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.

## <a name="test-in-browser"></a>브라우저에서 테스트

이전에 구성한 DNS 이름(예: `contoso.com`, `www.contoso.com`, `sub1.contoso.com` 및 `sub2.contoso.com`)을 찾습니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>404 "찾을 수 없음" 오류 해결

사용자 지정 도메인의 URL를 찾아볼 때 HTTP 404(찾을 수 없음) 오류가 나타나는 경우, <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>을 사용하여 앱의 IP 주소로 도메인이 지정되어 있는지 확인합니다. 그렇지 않은 경우 다음 이유 중 하나가 원인일 수 있습니다.

- 구성된 사용자 지정 도메인에 A 레코드 및/또는 CNAME 레코드가 없습니다.
- 브라우저 클라이언트가 도메인의 이전 IP 주소를 캐시했습니다. 캐시 및 테스트 DNS 확인을 다시 지웁니다. Windows 컴퓨터에서 캐시를 `ipconfig /flushdns`로 지웁니다.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>활성 도메인 마이그레이션

라이브 사이트 및 해당 DNS 도메인 이름을 가동 중지 시간 없이 App Service로 마이그레이션하려면 [활성 DNS 이름을 Azure App Service로 마이그레이션](manage-custom-dns-migrate-domain.md)을 참조하세요.

## <a name="redirect-to-a-custom-directory"></a>사용자 지정 디렉터리로 리디렉션

App Service는 기본적으로 웹 요청을 앱 코드의 루트 디렉터리로 보냅니다. 그러나 특정 웹 프레임워크는 루트 디렉터리에 시작하지 않습니다. 예를 들어 [Laravel](https://laravel.com/)은 `public` 하위 디렉터리에서 시작합니다. `contoso.com` DNS 예제를 계속하려면 그러한 앱은 `http://contoso.com/public`에서 액세스할 수도 있지만, 대신 실제로 `http://contoso.com`을 `public` 디렉터리로 전달할 수 있습니다. 이 단계는 DNS 확인을 포함하지는 않으나, 가상 디렉터리를 사용자 지정합니다.

이를 수행하려면 웹앱 페이지의 왼쪽 탐색에서 **애플리케이션 설정**을 선택합니다. 

페이지의 하단에서 기본적으로 루트 가상 디렉터리`/`는 앱 코드의 루트 디렉터리인 `site\wwwroot`를 가리킵니다. 예를 들어 대신 `site\wwwroot\public`을 가리키도록 변경하고 변경 내용을 저장합니다.

![가상 디렉터리 사용자 지정](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

작업이 완료되면 앱은 루트 경로(예: `http://contoso.com`)에서 오른쪽 페이지를 반환해야 합니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/)을 사용하여 스크립트를 통해 사용자 지정 도메인의 관리를 자동화할 수 있습니다. 

### <a name="azure-cli"></a>Azure CLI 

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다. 

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

자세한 내용은 [웹앱에 사용자 지정 도메인 매핑](scripts/cli-configure-custom-domain.md)을 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

자세한 내용은 [Web App에 사용자 지정 도메인 할당](scripts/powershell-configure-custom-domain.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * CNAME 레코드를 사용하여 하위 도메인 매핑
> * A 레코드를 사용하여 루트 도메인 매핑
> * CNAME 레코드를 사용하여 와일드카드 도메인 매핑
> * 사용자 지정 디렉터리로 기본 URL 리디렉션
> * 스크립트로 도메인 매핑 자동화

다음 자습서로 이동하여 사용자 지정 TLS/SSL 인증서를 웹앱에 바인딩하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
