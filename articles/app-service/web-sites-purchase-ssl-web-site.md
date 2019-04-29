---
title: Azure에서 SSL 인증서 구입 및 구성 - App Service | Microsoft Docs
description: App Service Certificate를 구입하고 App Service 앱에 바인딩하는 방법을 알아봅니다
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: 3e113639dbe4220b943d49dc610ee22b6416e12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832888"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Azure App Service에 대한 SSL 인증서 구입 및 구성

이 자습서에서는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)에서 App Service 인증서를 작성(구매)한 다음, App Service 앱에 바인딩하여 [App Service 앱](https://docs.microsoft.com/azure/app-service/) 또는 [Function App](https://docs.microsoft.com/azure/azure-functions/)을 보호하는 방법을 보여 줍니다.

> [!TIP]
> App Service Certificates는 Azure 또는 비 Azure 서비스에 대해 사용할 수 있으며 App Services로 제한되지 않습니다. 그렇게 하려면 어디서든 사용할 수 있는 App Service Certificate의 로컬 PFX 복사본을 만들어야 합니다. 자세한 내용은 [App Service Certificate의 로컬 PFX 복사본 만들기](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)를 참고하세요.
>

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 수행하려면 다음이 필요합니다.

- [App Service 앱 만들기](/azure/app-service/)
- [도메인 이름을 앱에 매핑](app-service-web-tutorial-custom-domain.md)하거나 [Azure에서 구입하여 구성](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>인증서 주문 시작

<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service Certificate 만들기 페이지</a>에서 App Service Certificate 주문을 시작합니다.

![인증서 만들기](./media/app-service-web-purchase-ssl-web-site/createssl.png)

다음 표를 사용하여 인증서를 구성할 수 있습니다. 작업을 마쳤으면 **만들기**를 클릭합니다.

| 설정 | 설명 |
|-|-|
| 이름 | App Service Certificate에 대한 식별 이름입니다. |
| Naked 도메인 호스트 이름 | 여기에 루트 도메인을 지정하면 루트 도메인 및 `www` 하위 도메인을 ‘둘 다’ 보호하는 인증서를 얻게 됩니다. 하위 도메인만 보호하려면 여기에 하위 도메인의 정규화된 도메인 이름을 지정합니다(예: `mysubdomain.contoso.com`). |
| 구독 | 웹앱이 호스팅된 데이터 센터입니다. |
| 리소스 그룹 | 인증서를 포함하는 리소스 그룹입니다. 예를 들어, 새로운 리소스 그룹을 사용하거나 App Service 앱과 동일한 리소스 그룹을 선택할 수 있습니다. |
| 인증서 SKU | 표준 인증서 또는 [와일드 카드 인증서](https://wikipedia.org/wiki/Wildcard_certificate) 여부에 관계없이 만들려는 인증서 유형을 결정합니다. |
| 약관 | 약관에 동의하는지 확인하려면 클릭합니다. 인증서는 GoDaddy에서 가져옵니다. |

## <a name="store-in-azure-key-vault"></a>Azure Key Vault에 저장

인증서 구매 프로세스가 완료되고 나면 이 인증서를 사용하기 전에 완료해야 할 단계가 몇 가지 더 있습니다. 

[App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지에서 인증서를 선택한 다음, **인증서 구성** > **1단계: 저장**을 클릭합니다.

![KV에 저장할 준비 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 되는 Azure 서비스입니다. App Service Certificate에 대해 선택한 저장소입니다.

**Key Vault 상태** 페이지에서 **Key Vault 리포지토리**를 클릭하여 새 자격 증명 모음을 만들거나 기존 자격 증명 모음을 선택합니다. 새 자격 증명 모음을 만들려면 다음 표를 사용하여 자격 증명 모음을 구성하고 만들기를 클릭합니다. 동일한 구독 및 리소스 그룹 내에서 새로운 Key Vault 생성을 참조하세요.

| 설정 | 설명 |
|-|-|
| 이름 | 영숫자와 대시로 구성된 고유한 이름입니다. |
| 리소스 그룹 | 권장 사항으로, App Service Certificate과 동일한 리소스 그룹을 선택합니다. |
| Location | App Service 앱과 동일한 위치를 선택합니다. |
| 가격 책정 계층  | 자세한 내용은 [Azure Key Vault 가격 책정 정보](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요. |
| 액세스 정책| 애플리케이션 및 자격 증명 모음 리소스에 허용된 액세스를 정의합니다. 나중에 [키 자격 증명 모음에 여러 애플리케이션에 대한 액세스 권한 부여](../key-vault/key-vault-group-permissions-for-apps.md)의 단계에 따라 구성할 수 있습니다. |
| Virtual Network 액세스 | 특정 Azure Virtual Network에 대한 자격 증명 모음 액세스 액세스를 제한합니다. 나중에 [Azure Key Vault 방화벽 및 Virtual Network 구성](../key-vault/key-vault-network-security.md)의 단계에 따라 구성할 수 있습니다. |

자격 증명 모음을 선택한 후에는 **Key Vault 리포지토리** 페이지를 닫습니다. **저장소** 옵션에 성공을 나타내는 녹색 확인 표시가 나타납니다. 다음 단계를 위해 페이지를 열어둡니다.

## <a name="verify-domain-ownership"></a>도메인 소유권 확인

마지막 단계에서 사용한 것과 동일한 **인증서 구성** 페이지에서 **2단계: 확인**을 클릭합니다.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

**App Service 확인**을 선택합니다. 도메인을 웹앱에 이미 매핑했기 때문에([필수 구성 요소](#prerequisites) 참조), 이미 확인되었습니다. 간단히 **확인**을 클릭하여 단계를 마칩니다. **인증서 도메인이 확인됨** 메시지가 나타날 때까지 **Refresh** 단추를 누릅니다.

> [!NOTE]
> 네 가지 유형의 도메인 확인 방법이 지원됩니다. 
> 
> - **App Service**  - 도메인이 동일한 구독의 App Service 앱에 이미 매핑된 경우 가장 편리한 옵션입니다. App Service 앱이 도메인 소유권을 이미 확인했다는 사실을 활용합니다.
> - **도메인** - [Azure에서 구매한 App Service 도메인](manage-custom-dns-buy-domain.md)을 확인합니다. Azure는 사용자에게 자동으로 확인 TXT 레코드를 추가하고 프로세스를 완료합니다.
> - **이메일** - 도메인 관리자에게 이메일을 보내서 도메인을 확인합니다. 옵션을 선택하면 지침이 제공됩니다.
> - **수동** - HTML 페이지(**표준** 인증서만) 또는 DNS TXT 레코드를 사용하여 도메인을 확인합니다. 옵션을 선택하면 지침이 제공됩니다.

## <a name="bind-certificate-to-app"></a>앱에 인증서 바인딩

**[Azure Portal](https://portal.azure.com/)** 의 왼쪽 메뉴에서 **App Services** > **\<your_app>** 을 선택합니다.

앱의 왼쪽 탐색 영역에서 **SSL 설정** > **개인 인증서(.pfx)** > **App Service Certificate 가져오기**를 선택합니다.

![인증서 가져오기 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

방금 구입한 인증서를 선택합니다.

인증서를 가져왔으면 앱의 매핑된 도메인 이름에 인증서를 바인딩해야 합니다. **바인딩** > **SSL 바인딩 추가**를 선택합니다. 

![인증서 가져오기 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

다음 표를 사용하여 **SSL 바인딩** 대화 상자에서 바인딩을 구성한 다음, **바인딩 추가**를 클릭합니다.

| 설정 | 설명 |
|-|-|
| 호스트 이름 | SSL 바인딩을 추가할 도메인 이름입니다. |
| 개인 인증서 지문 | 바인딩할 인증서입니다. |
| SSL 형식 | <ul><li>**SNI SSL** - 여러 개의 SNI 기반 SSL 바인딩을 추가할 수 있습니다. 이 옵션을 사용하면 여러 SSL 인증서로 같은 IP 주소의 여러 도메인을 보호할 수 있습니다. 대부분의 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원합니다. [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)(서버 이름 표시)에서 더 포괄적인 브라우저 지원 정보를 찾을 수 있습니다.</li><li>**IP 기반 SSL** - IP 기반 SSL 바인딩 하나만 추가할 수 있습니다. 이 옵션을 사용하면 전용 공용 IP 주소를 보호하는 데 하나의 SSL 인증서만 사용할 수 있습니다. 바인딩을 구성한 후에 [IP SSL에 대한 A 레코드 다시 매핑](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl)의 단계를 따릅니다. </li></ul> |

## <a name="verify-https-access"></a>HTTPS 액세스 확인

`HTTP://<domain_name>` 대신 `HTTPS://<domain_name>`을 사용하여 앱에 방문하여 인증서가 올바르게 구성되었는지 확인합니다.

## <a name="rekey-certificate"></a>인증서 키 다시 생성

생각 하면 인증서의 개인 키가 손상 되 면 인증서를 다시 생성할 수 있습니다. 인증서를 선택 합니다 [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지를 선택한 다음 선택 **키 다시 생성 및 동기화** 왼쪽된 탐색 영역에서 합니다.

클릭 **키를 다시 생성** 프로세스를 시작 합니다. 이 프로세스는 완료하는 데 1-10분 정도 걸릴 수 있습니다.

![SSL 키 다시 생성 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

인증서 키를 다시 생성하면 인증서가 인증 기관에서 발급한 새 인증서로 롤링됩니다.

키 다시 생성 작업이 완료 되 면 클릭 **동기화**합니다. 동기화 작업이 앱 가동 중지 시간이 발생 하지 않고 App Service에서 인증서에 대 한 호스트 이름 바인딩을 자동으로 업데이트 합니다.

> [!NOTE]
> 클릭 하지 **동기화**, App Service 48 시간 이내에 인증서를 자동으로 동기화 합니다.

## <a name="renew-certificate"></a>인증서 갱신

언제든 인증서 자동 갱신을 켜려면 [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지에서 인증서를 선택한 다음, 왼쪽 탐색 영역에서 **자동 갱신 설정**을 클릭합니다.

**켜기**를 선택하고 **저장**을 클릭합니다. 자동 갱신을 켜 놓으면 인증서가 만료 60일 전에 자동으로 갱신됩니다.

![인증서를 자동으로 갱신](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

인증서를 수동으로 갱신하려면 **수동 갱신**을 클릭합니다. 만료 60일 전에 인증서를 자동으로 갱신하도록 요청할 수 있습니다.

갱신 작업이 완료 되 면 클릭 **동기화**합니다. 동기화 작업이 앱 가동 중지 시간이 발생 하지 않고 App Service에서 인증서에 대 한 호스트 이름 바인딩을 자동으로 업데이트 합니다.

> [!NOTE]
> 클릭 하지 **동기화**, App Service 48 시간 이내에 인증서를 자동으로 동기화 합니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>추가 리소스

* [HTTPS 적용](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [TLS 1.1/1.2 적용](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Azure App Service의 애플리케이션 코드에서 SSL 인증서 사용](app-service-web-ssl-cert-load.md)
* [FAQ : App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
