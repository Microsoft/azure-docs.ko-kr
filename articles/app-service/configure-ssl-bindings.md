---
title: TLS/SSL 바인딩으로 사용자 지정 DNS 보호
description: 인증서로 TLS/SSL 바인딩을 만들어 사용자 지정 도메인에 대한 HTTPS 액세스를 보호합니다. HTTPS 또는 TLS 1.2를 적용하여 웹 사이트의 보안을 향상시킵니다.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 6c3f4cfe7c896b317edc5897098a1c18c98dc826
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962641"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호

이 문서에서는 인증서 바인딩을 만들어 [App Service 앱](./index.yml) 또는 [함수 앱](../azure-functions/index.yml)에서 [사용자 지정 도메인](app-service-web-tutorial-custom-domain.md)을 보호하는 방법을 보여 줍니다. 완료되면 사용자 지정 DNS 이름(예: `https://www.contoso.com`)의 `https://` 엔드포인트에서 App Service 앱에 액세스할 수 있습니다. 

![사용자 지정 TLS/SSL 인증서가 포함된 웹앱](./media/configure-ssl-bindings/app-with-custom-ssl.png)

인증서로 [사용자 지정 도메인](app-service-web-tutorial-custom-domain.md)을 보호하려면 다음 두 단계가 필요합니다.

- 모든 [프라이빗 인증서 요구 사항](configure-ssl-certificate.md#private-certificate-requirements)을 충족하는 [프라이빗 인증서를 App Service에 추가](configure-ssl-certificate.md)합니다.
-  해당 사용자 지정 도메인에 대한 TLS 바인딩을 만듭니다. 이 두 번째 단계는 이 문서에서 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 앱의 가격 책정 계층 업그레이드
> * 인증서를 사용하여 사용자 지정 도메인 보호
> * HTTPS 적용
> * TLS 1.1/1.2 적용
> * 스크립트를 사용하여 TLS 관리 자동화

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 수행하려면 다음이 필요합니다.

- [App Service 앱 만들기](./index.yml)
- [도메인 이름을 앱에 매핑](app-service-web-tutorial-custom-domain.md)하거나 [Azure에서 구입하여 구성](manage-custom-dns-buy-domain.md)
- [앱에 프라이빗 인증서 추가](configure-ssl-certificate.md)

> [!NOTE]
> 프라이빗 인증서를 추가하는 가장 쉬운 방법은 [무료 App Service Managed Certificate 만들기](configure-ssl-certificate.md#create-a-free-certificate-preview)(미리 보기)입니다.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>사용자 지정 도메인 보호

다음 단계를 수행합니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색에서 다음을 수행하여 **TLS/SSL 바인딩** 대화 상자를 시작합니다.

- **사용자 지정 도메인** > **바인딩 추가**를 선택합니다.
- **TLS/SSL 설정** > **TLS/SSL 바인딩 추가**를 선택합니다.

![도메인에 바인딩 추가](./media/configure-ssl-bindings/secure-domain-launch.png)

**사용자 지정 도메인**에서 바인딩을 추가하려는 사용자 지정 도메인을 선택합니다.

앱에 선택한 사용자 지정 도메인에 대한 인증서가 이미 있는 경우 [바인딩 만들기](#create-binding)로 직접 이동합니다. 그렇지 않은 경우 계속 진행합니다.

### <a name="add-a-certificate-for-custom-domain"></a>사용자 지정 도메인에 대한 인증서 추가

앱에 선택한 사용자 지정 도메인에 대한 인증서가 없는 경우 다음 두 가지 옵션을 사용할 수 있습니다.

- **PFX 인증서 업로드** - [프라이빗 인증서 업로드](configure-ssl-certificate.md#upload-a-private-certificate)에서 워크플로를 따르고 여기에서 이 옵션을 선택합니다.
- **App Service Certificate 가져오기** - [App Service 인증서 가져오기](configure-ssl-certificate.md#import-an-app-service-certificate)의 워크플로를 따르고 여기에서 이 옵션을 선택합니다.

> [!NOTE]
> [무료 인증서 만들기](configure-ssl-certificate.md#create-a-free-certificate-preview)(미리 보기) 또는 [Key Vault 인증서 가져오기](configure-ssl-certificate.md#import-a-certificate-from-key-vault)를 수행할 수도 있지만 별도로 수행한 다음 **TLS/SSL 바인딩** 대화 상자로 돌아가야 합니다.

### <a name="create-binding"></a>바인딩 만들기

다음 표를 사용하여 **TLS/SSL 바인딩** 대화 상자에서 TLS 바인딩을 구성한 다음, **바인딩 추가**를 클릭합니다.

| 설정 | Description |
|-|-|
| 사용자 지정 도메인 | TLS/SSL 바인딩을 추가할 도메인 이름입니다. |
| 프라이빗 인증서 지문 | 바인딩할 인증서입니다. |
| TLS/SSL 유형 | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - 여러 개의 SNI SSL 바인딩을 추가할 수 있습니다. 이 옵션을 사용하면 여러 TLS/SSL 인증서로 같은 IP 주소의 여러 도메인을 보호할 수 있습니다. 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원합니다(자세한 내용은 [서버 이름 표시](https://wikipedia.org/wiki/Server_Name_Indication) 참조).</li><li>**IP SSL** - IP SSL 바인딩 하나만 추가할 수 있습니다. 이 옵션을 사용하면 전용 공용 IP 주소를 보호하는 데 하나의 TLS/SSL 인증서만 사용할 수 있습니다. 바인딩을 구성한 후에 [IP SSL에 대한 레코드 다시 매핑](#remap-records-for-ip-ssl)의 단계를 따릅니다.<br/>IP SSL은 **표준** 계층 이상에서만 지원됩니다. </li></ul> |

작업이 완료되면 사용자 지정 도메인의 TLS/SSL 상태가 **보안**으로 변경됩니다.

![TLS/SSL 바인딩 성공](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> **사용자 지정 도메인**의 **보호** 상태는 인증서를 사용하여 보호됨을 의미하지만, App Service는 인증서가 자체 서명되었는지 아니면 만료되었는지 확인하지 않습니다. 예를 들어 브라우저가 오류 또는 경고를 표시할 수도 있습니다.

## <a name="remap-records-for-ip-ssl"></a>IP SSL에 대한 레코드 다시 매핑

앱에서 IP SSL을 사용하지 않을 경우 [사용자 지정 도메인에 대한 HTTPS 테스트](#test-https)로 건너뜁니다.

다음과 같은 두 가지 변경 작업을 수행해야 합니다.

- 기본적으로 앱에서는 공유 공용 IP 주소를 사용합니다. IP SSL을 사용하여 인증서를 바인딩하면 App Service에서 앱에 대한 새로운 전용 IP 주소를 만듭니다. A 레코드를 앱에 매핑한 경우 이 새로운 전용 IP 주소로 도메인 레지스트리를 업데이트합니다.

    앱의 **사용자 지정 도메인** 페이지가 새로운 전용 IP 주소로 업데이트됩니다. [이 IP 주소를 복사](app-service-web-tutorial-custom-domain.md#info)하고 이 새로운 IP 주소에 [A 레코드를 다시 매핑](app-service-web-tutorial-custom-domain.md#map-an-a-record)합니다.

- `<app-name>.azurewebsites.net`에 대한 SNI SSL 바인딩이 있는 경우 [모든 CNAME 매핑을 다시 매핑](app-service-web-tutorial-custom-domain.md#map-a-cname-record)하여 대신 `sni.<app-name>.azurewebsites.net`(`sni` 접두사 추가)을 가리킵니다.

## <a name="test-https"></a>HTTPS 테스트

다양한 브라우저에서 `https://<your.custom.domain>`으로 이동하여 앱을 처리하는지 확인합니다.

![Azure 앱에 대한 포털 탐색](./media/configure-ssl-bindings/app-with-custom-ssl.png)

애플리케이션 코드는 "x-appservice-proto" 헤더를 통해 프로토콜을 검사할 수 있습니다. 헤더의 값은 `http` 또는 `https`입니다. 

> [!NOTE]
> 앱에서 인증서 유효성 검사 오류가 발생한 경우 자체 서명된 인증서를 사용하고 있을 수도 있습니다.
>
> 그렇지 않으면 인증서를 PFX 파일로 내보낼 때 중간 인증서를 생략했을 수도 있습니다.

## <a name="prevent-ip-changes"></a>IP 변경 방지

바인딩이 IP SSL이더라도 바인딩을 삭제하면 인바운드 IP 주소가 변경될 수 있습니다. 이것은 IP SSL 바인딩에 이미 있는 인증서를 갱신할 때 특히 중요합니다. 앱의 IP 주소 변경을 방지하려면 다음 단계를 순서대로 수행합니다.

1. 새 인증서 업로드
2. 기존 인증서를 삭제하지 않고 원하는 사용자 지정 도메인에 새 인증서를 바인딩합니다. 이 작업은 기존 인증서를 제거하지 않고 바인딩을 바꿉니다.
3. 기존 인증서를 삭제합니다. 

## <a name="enforce-https"></a>HTTPS 적용

기본적으로 누구나 HTTP를 사용하여 앱에 액세스할 수 있습니다. HTTPS 포트에 모든 HTTP 요청을 리디렉션할 수 있습니다.

앱 페이지의 왼쪽 탐색 영역에서 **SSL 설정**을 선택합니다. 그런 다음 **HTTPS에만 해당**에서 **켜기**를 선택합니다.

![HTTPS 적용](./media/configure-ssl-bindings/enforce-https.png)

작업이 완료되면 앱을 가리키는 HTTP URL 중 하나로 이동합니다. 다음은 그 예입니다. 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS 버전 적용

앱에는 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)와 같이 업계 표준에서 권장되는 TLS 수준인 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2가 기본적으로 허용됩니다. 다른 TLS 버전을 적용하려면 다음 단계를 수행합니다.

앱 페이지의 왼쪽 탐색 영역에서 **SSL 설정**을 선택합니다. 그런 다음, **TLS 버전**에서 원하는 최소 TLS 버전을 선택합니다. 이 설정은 인바운드 호출만 제어합니다. 

![TLS 1.1 또는 1.2 적용](./media/configure-ssl-bindings/enforce-tls1-2.png)

작업이 완료되면 앱에서 더 낮은 TLS 버전과의 모든 연결을 거부합니다.

## <a name="handle-tls-termination"></a>TLS 종료 처리

App Service에서, [TLS 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

[Linux Node.js 구성](configure-language-nodejs.md#detect-https-session) 가이드와 같은 언어별 구성 가이드에서는 애플리케이션 코드에서 HTTPS 세션을 검색하는 방법을 보여 줍니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>추가 리소스

* [Azure App Service의 코드에서 TLS/SSL 인증서 사용](configure-ssl-certificate-in-code.md)
* [FAQ: App Service Certificate](./faq-configuration-and-management.md)