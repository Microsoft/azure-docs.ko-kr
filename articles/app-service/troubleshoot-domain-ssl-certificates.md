---
title: 도메인 및 TLS/SSL 인증서 문제 해결
description: Azure App Service에서 도메인 또는 TLS/SSL 인증서를 구성할 때 발생할 수 있는 일반적인 문제에 대 한 해결 방법을 찾습니다.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 1cefb5a7b554b9a477f6a51eab3b22b0e8f55378
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958442"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Azure App Service의 도메인 및 TLS/SSL 인증서 문제 해결

이 문서에는 Azure App Service에서 웹 앱에 대 한 도메인 또는 TLS/SSL 인증서를 구성할 때 발생할 수 있는 일반적인 문제가 나열 되어 있습니다. 또한 이러한 문제에 대한 가능한 원인 및 해결 방법을 설명합니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동 하 여 **지원 받기**를 선택 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>인증서 문제

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>TLS/SSL 인증서 바인딩을 앱에 추가할 수 없습니다. 

#### <a name="symptom"></a>증상

TLS 바인딩을 추가 하면 다음과 같은 오류 메시지가 표시 됩니다.

"SSL 바인딩을 추가하지 못했습니다. 다른 VIP에서 해당 인증서를 이미 사용하므로 기존 VIP에 대한 인증서를 설정할 수 없습니다."

#### <a name="cause"></a>원인

여러 앱에서 동일한 IP 주소에 대한 IP 기반 SSL 바인딩이 여러 개 있는 경우 이 문제가 발생할 수 있습니다. 예를 들어 앱 A는 IP 기반 SSL과 기존 인증서를 사용합니다. 앱 B는 동일한 IP 주소에 IP 기반 SSL과 새 인증서를 사용합니다. 새 인증서를 사용 하 여 앱 TLS 바인딩을 업데이트 하는 경우 다른 앱에 동일한 IP 주소를 사용 하 고 있기 때문에이 오류로 인해 실패 합니다. 

#### <a name="solution"></a>솔루션 

이 문제를 해결하려면 다음 방법 중 하나를 사용합니다.

- 기존 인증서를 사용하는 앱에서 IP 기반 SSL 바인딩을 삭제합니다. 
- 새 인증서를 사용하는 새 IP 기반 SSL 바인딩을 만듭니다.

### <a name="you-cant-delete-a-certificate"></a>인증서를 삭제할 수 없음 

#### <a name="symptom"></a>증상

인증서를 삭제하려고 하면 다음과 같은 오류 메시지가 나타납니다.

"인증서가 현재 TLS/SSL 바인딩에서 사용 되 고 있으므로 삭제할 수 없습니다. 인증서를 삭제 하려면 먼저 TLS 바인딩을 제거 해야 합니다. "

#### <a name="cause"></a>원인

다른 앱에서 인증서를 사용하는 경우 이 문제가 발생할 수 있습니다.

#### <a name="solution"></a>솔루션

앱에서 해당 인증서에 대 한 TLS 바인딩을 제거 합니다. 그 후 인증서를 삭제해 봅니다. 여전히 인증서를 삭제할 수 없으면 인터넷 브라우저 캐시를 지우고, 새 브라우저 창에서 Azure Portal을 다시 엽니다. 그 후 인증서를 삭제해 봅니다.

### <a name="you-cant-purchase-an-app-service-certificate"></a>App Service 인증서를 구입할 수 없음 

#### <a name="symptom"></a>증상
Azure Portal에서 [Azure App Service 인증서](./configure-ssl-certificate.md#import-an-app-service-certificate)를 구입할 수 없습니다.

#### <a name="cause-and-solution"></a>원인 및 해결 방법
이 문제는 다음과 같은 이유로 발생할 수 있습니다.

- App Service 계획이 무료 또는 공유입니다. 이러한 가격 책정 계층은 TLS를 지원 하지 않습니다. 

    **해결**방법: 앱에 대 한 App Service 요금제를 표준으로 업그레이드 합니다.

- 구독에 유효한 신용 카드가 없습니다.

    **해결 방법**: 구독에 유효한 신용 카드를 추가합니다. 

- 구독 제안은 Microsoft Student 같은 App Service 인증서 구매를 지원하지 않습니다.  

    **해결 방법**: 구독을 업그레이드합니다. 

- 구독에서 허용되는 구매 한도에 도달했습니다.

    **해결 방법**: App Service 인증서의 종량제 및 EA 구독 유형에 대한 인증서 구매 한도는 10개입니다. 다른 구독 유형의 한도는 3개입니다. 한도를 늘리려면 [Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.
- App Service 인증서가 사기로 표시되었습니다. 다음과 같은 오류 메시지를 받았습니다. "귀하의 인증서에 사기 가능성에 대한 플래그가 지정되었습니다. 요청을 현재 검토하는 중입니다. 24 시간 내에 인증서를 사용할 수 없게 되 면 Azure 지원에 문의 하세요. "

    **해결 방법**: 인증서가 사기로 표시되고 24시간 후에도 해결되지 않으면 다음 단계를 수행합니다.

    1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
    2. **App Service 인증서**로 이동하고, 인증서를 선택합니다.
    3. **인증서 구성**  >  **2 단계:**  >  **도메인 확인**확인을 선택 합니다. 이 단계는 문제를 해결하라는 이메일 알림을 Azure 인증서 공급자에게 전송합니다.

## <a name="custom-domain-problems"></a>사용자 지정 도메인 문제

### <a name="a-custom-domain-returns-a-404-error"></a>사용자 지정 도메인에서 404 오류 반환 

#### <a name="symptom"></a>증상

사용자 지정 도메인 이름을 사용하여 사이트를 탐색할 때 다음과 같은 오류 메시지가 나타납니다.

"오류 404-웹앱을 찾을 수 없습니다."

#### <a name="cause-and-solution"></a>원인 및 해결 방법

**원인 1** 

구성된 사용자 지정 도메인에 CNAME 또는 A 레코드가 없습니다. 

**원인 1의 해결 방법**

- A 레코드를 추가한 경우 TXT 레코드도 추가해야 합니다. 자세한 내용은 [A 레코드 만들기](./app-service-web-tutorial-custom-domain.md#create-the-a-record)를 참조하세요.
- 앱에 루트 도메인을 사용하지 않아도 되는 경우 A 레코드 대신 CNAME 레코드를 사용하는 것이 좋습니다.
- CNAME 및 A 레코드를 동일한 도메인에 사용하지 마세요. 이 문제로 인해 충돌이 발생 하 고 도메인을 확인 하지 못할 수 있습니다. 

**원인 2** 

인터넷 브라우저가 여전히 도메인의 이전 IP 주소를 캐싱하고 있습니다. 

**원인 2의 해결 방법**

브라우저를 지웁니다. Windows 디바이스의 경우 `ipconfig /flushdns` 명령을 실행하면 됩니다. [WhatsmyDNS.net](https://www.whatsmydns.net/)을 사용하여 도메인이 앱의 IP 주소를 가리키는지 확인합니다. 

### <a name="you-cant-add-a-subdomain"></a>하위 도메인을 추가할 수 없음 

#### <a name="symptom"></a>증상

앱에 새 호스트 이름을 추가하여 하위 도메인을 할당할 수 없습니다.

#### <a name="solution"></a>솔루션

- 구독 관리자에게 문의하여 앱에 호스트 이름을 추가할 권한을 갖고 있는지 확인합니다.
- 더 많은 하위 도메인이 필요한 경우 도메인 호스팅을 Azure DNS (Domain Name Service)로 변경 하는 것이 좋습니다. Azure DNS를 사용하여 앱에 호스트 이름 500개를 추가할 수 있습니다. 자세한 내용은 [하위 도메인 추가](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)를 참조하세요.

### <a name="dns-cant-be-resolved"></a>DNS를 확인할 수 없음

#### <a name="symptom"></a>증상

다음과 같은 오류 메시지를 받았습니다.

"DNS 레코드를 찾을 수 없습니다."

#### <a name="cause"></a>원인
이 문제는 다음과 같은 이유로 발생합니다.

- TTL(Time to Live)이 만료되지 않았습니다. 도메인의 DNS 구성을 검사하여 TTL 값을 확인한 다음, 기간이 만료될 때까지 기다립니다.
- DNS 구성이 올바르지 않습니다.

#### <a name="solution"></a>솔루션
- 이 문제가 스스로 해결될 때까지 48시간 동안 기다립니다.
- DNS 구성에서 TTL 설정을 변경할 수 있는 경우 값을 5분으로 변경하고 문제가 해결되는지 확인합니다.
- [WhatsmyDNS.net](https://www.whatsmydns.net/)을 사용하여 도메인이 앱의 IP 주소를 가리키는지 확인합니다. 가리키지 않으면 A 레코드를 앱의 올바른 IP 주소로 구성합니다.

### <a name="you-need-to-restore-a-deleted-domain"></a>삭제된 도메인을 복원해야 함 

#### <a name="symptom"></a>증상
도메인이 더 이상 Azure Portal에 표시되지 않습니다.

#### <a name="cause"></a>원인 
구독 소유자가 실수로 도메인을 삭제했을 가능성이 있습니다.

#### <a name="solution"></a>솔루션
도메인이 7일 이내에 삭제된 경우 도메인에서 아직 삭제 프로세스가 시작되지 않았습니다. 이 경우 동일한 구독에서 Azure Portal을 통해 동일한 도메인을 다시 구입할 수 있습니다. 검색 상자에 정확한 도메인 이름을 입력 해야 합니다. 이 도메인에 대 한 요금이 다시 청구 되지 않습니다. 도메인이 7 일 이상 전에 삭제 된 경우 [Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 에 문의 하 여 도메인 복원에 대 한 도움을 요청 하세요.

## <a name="domain-problems"></a>도메인 문제

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>잘못 된 도메인에 대 한 TLS/SSL 인증서를 구매 했습니다.

#### <a name="symptom"></a>증상

잘못된 도메인에 대한 App Service 인증서를 구입했습니다. 올바른 도메인을 사용하도록 인증서를 업데이트할 수 없습니다.

#### <a name="solution"></a>솔루션

해당 인증서를 삭제하고 새 인증서를 구입합니다.

잘못된 도메인을 사용하는 현재 인증서의 상태가 "발급됨"이면 해당 인증서에 대한 요금도 청구됩니다. App Service 인증서는 환불이 불가능하지만, [Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)으로 문의하여 다른 옵션이 있는지 확인할 수 있습니다. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>App Service 인증서가 갱신되었지만 앱이 기존 인증서를 표시 

#### <a name="symptom"></a>증상

App Service 인증서가 갱신되었지만 App Service 인증서를 사용하는 앱에서 여전히 기존 인증서를 사용합니다. 또한 HTTPS 프로토콜이 필요하다는 경고를 받았습니다.

#### <a name="cause"></a>원인 
App Service는 48 시간 이내에 인증서를 자동으로 동기화 합니다. 인증서를 회전하거나 업데이트해도 애플리케이션이 새로 업데이트된 인증서가 아닌 기존 인증서를 검색하는 경우가 가끔 있습니다. 인증서 리소스를 동기화하는 작업이 아직 실행되지 않았기 때문입니다. 동기화를 클릭 합니다. 동기화 작업은 앱에 가동 중지 시간을 발생 시 키 지 않고 App Service에서 인증서에 대 한 호스트 이름 바인딩을 자동으로 업데이트 합니다.
 
#### <a name="solution"></a>솔루션

인증서를 강제로 동기화하면 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **App Service 인증서**를 선택한 다음, 인증서를 선택합니다.
2. 키 다시 생성 **및 동기화**를 선택 하 고 **동기화**를 선택 합니다. 동기화를 완료 하는 데 약간의 시간이 걸립니다. 
3. 동기화가 완료되면 "모든 리소스가 최신 인증서로 업데이트되었습니다."라는 알림이 표시됩니다.

### <a name="domain-verification-is-not-working"></a>도메인 확인이 작동하지 않음 

#### <a name="symptom"></a>증상 
App Service 인증서에서 도메인 확인을 거쳐야만 인증서를 사용할 수 있습니다. **확인**을 선택하면 프로세스가 실패합니다.

#### <a name="solution"></a>솔루션
TXT 레코드를 추가하여 수동으로 도메인을 확인합니다.
 
1.  도메인 이름을 호스트하는 DNS(도메인 이름 서비스) 공급자로 이동합니다.
2.  Azure Portal에 표시되는 도메인 토큰 값을 사용하는 도메인의 TXT 레코드를 추가합니다. 

DNS 전파가 실행될 때까지 잠시 기다린 다음, **새고 고침** 단추를 선택하여 확인을 트리거합니다. 

대신 HTML 웹 페이지 메서드를 사용하여 수동으로 도메인을 확인할 수 있습니다. 이 메서드를 사용하여 인증 기관이 인증서가 발급된 도메인의 도메인 소유권을 확인하도록 할 수 있습니다.

1.  {domain verification token}.html이라는 HTML 파일을 만듭니다. 이 파일의 내용은 도메인 확인 토큰 값이어야 합니다.
3.  이 파일을 도메인을 호스팅하는 웹 서버의 루트에서 업로드합니다.
4.  **새로 고침**을 선택하여 인증서 상태를 확인합니다. 확인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

예를 들어 도메인 확인 토큰 1234abcd로 azure.com에 대한 표준 인증서를 구입하는 경우 https://azure.com/1234abcd.html에 대해 만들어진 웹 요청은 1234abcd를 반환해야 합니다. 

> [!IMPORTANT]
> 인증서 주문 15일 내에 도메인 확인 작업을 완료해야 합니다. 15일이 지나면 인증 기관에서 인증서를 거부하고, 인증서 비용이 청구되지 않습니다. 이 경우 해당 인증서를 삭제하고 다시 시도해야 합니다.
>
> 

### <a name="you-cant-purchase-a-domain"></a>도메인을 구입할 수 없음

#### <a name="symptom"></a>증상
Azure Portal에서 App Service 도메인을 구입할 수 없습니다.

#### <a name="cause-and-solution"></a>원인 및 해결 방법

이 문제는 다음과 같은 이유로 발생합니다.

- Azure 구독에 신용 카드가 없거나 신용 카드가 유효하지 않습니다.

    **해결 방법**: 구독에 유효한 신용 카드를 추가합니다.

- 구독 소유자가 아니므로 도메인을 구입할 수 있는 권한이 없습니다.

    **해결 방법**: 계정에 [소유자 역할을 할당](../role-based-access-control/role-assignments-portal.md)합니다. 또는 구독 관리자에게 문의하여 도메인을 구입할 수 있는 권한을 가져옵니다.
- 구독의 도메인 구매 한도에 도달했습니다. 현재 한도는 20개입니다.

    **해결 방법**: 한도를 늘리려면 [Azure 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하세요.
- 사용하는 Azure 구독 유형에서 App Service 도메인 구입을 지원하지 않습니다.

    **해결 방법**: Azure 구독을 종량제 구독 등의 다른 구독 유형으로 업그레이드합니다.

### <a name="you-cant-add-a-host-name-to-an-app"></a>앱에 호스트 이름을 추가할 수 없음 

#### <a name="symptom"></a>증상

호스트 이름을 추가할 때 도메인의 유효성을 검사하고 확인하는 프로세스가 실패합니다.

#### <a name="cause"></a>원인 

이 문제는 다음과 같은 이유로 발생합니다.

- 호스트 이름을 추가할 권한이 없습니다.

    **해결 방법**: 구독 관리자에게 호스트 이름을 추가할 수 있는 권한을 요청합니다.
- 도메인 소유권을 확인할 수 없습니다.

    **해결 방법**: CNAME 또는 A 레코드가 올바르게 구성되었는지 확인합니다. 사용자 지정 도메인을 앱에 매핑하려면 CNAME 레코드 또는 A 레코드를 만듭니다. 루트 도메인을 사용하려면 A 및 TXT 레코드를 사용해야 합니다.

    |레코드 형식|호스트|대상|
    |------|------|-----|
    |A|@|앱의 IP 주소|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>FAQ

**웹 사이트를 구입한 후 내 웹 사이트에 대 한 사용자 지정 도메인을 구성 해야 하나요?**

Azure Portal에서 도메인을 구입 하는 경우 App Service 응용 프로그램은 해당 사용자 지정 도메인을 사용 하도록 자동으로 구성 됩니다. 추가 단계를 수행할 필요가 없습니다. 자세한 내용은 Channel9에서 [사용자 지정 도메인 이름 추가](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) 를 참조 Azure App Service.

**Azure Portal 구매한 도메인을 대신 사용 하 여 Azure VM을 가리킬 수 있나요?**

예, VM에 대 한 도메인을 가리킬 수 있습니다. 자세한 내용은 [Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공](../dns/dns-custom-domain.md)을 참조하세요.

**내 도메인이 GoDaddy 또는 Azure DNS에서 호스트 되나요?**

App Service 도메인은 도메인 등록에 GoDaddy를 사용 하 고 도메인을 호스트 하는 Azure DNS 합니다. 

**자동 갱신을 사용 하도록 설정 했지만 여전히 전자 메일을 통해 내 도메인에 대 한 갱신 알림을 받았습니다. 제가 뭘 해야 하나요?**

자동 갱신을 사용 하도록 설정한 경우에는 아무 작업도 수행할 필요가 없습니다. 자동 갱신을 사용 하도록 설정 하지 않은 경우 도메인의 만료 예정 및 수동으로 갱신에 대 한 알림 전자 메일이 제공 됩니다.

**도메인을 호스팅하는 Azure DNS에 대 한 요금이 청구 되나요?**

도메인 구매의 초기 비용은 도메인 등록에만 적용 됩니다. 등록 비용 외에도 사용량에 따라 Azure DNS에 대 한 요금이 발생 합니다. 자세한 내용은 [가격 책정 Azure DNS](https://azure.microsoft.com/pricing/details/dns/) 를 참조 하세요.

**Azure Portal에서 이전에 도메인을 구입 하 고 GoDaddy 호스팅에서 Azure DNS 호스팅을 이동 하려고 합니다. 이 작업을 수행 하려면 어떻게 해야 하나요?**

Azure DNS 호스팅을 반드시 마이그레이션해야 하는 것은 아닙니다. Azure DNS로 마이그레이션하려는 경우에는 Azure Portal의 도메인 관리 환경에서 Azure DNS으로 이동 하는 데 필요한 단계에 대 한 정보를 제공 합니다. App Service를 통해 도메인을 구매한 경우 GoDaddy에서 Azure DNS로의 마이그레이션은 비교적 원활한 절차입니다.

**App Service 도메인에서 도메인을 구입 하지만 Azure DNS 대신 GoDaddy에서 도메인을 호스트할 수 있나요?**

2017 년 7 월 24 일부 터 포털에서 구매한 App Service 도메인은 Azure DNS에서 호스팅됩니다. 다른 호스팅 공급자를 사용 하려는 경우 해당 웹 사이트로 이동 하 여 도메인 호스팅 솔루션을 가져와야 합니다.

**도메인에 대 한 개인 정보 보호에 대 한 요금을 지불 해야 하나요?**

Azure Portal를 통해 도메인을 구입 하는 경우 추가 비용 없이 개인 정보를 추가 하도록 선택할 수 있습니다. 이는 Azure App Service을 통해 도메인을 구입 하는 경우의 이점 중 하나입니다.

**도메인을 더 이상 원하지 않는 경우에는 돈을 다시 얻을 수 있나요?**

도메인을 구입 하는 경우 5 일 동안 요금이 청구 되지 않으며,이 기간 동안 도메인을 원하지 않도록 결정할 수 있습니다. 5 일 이내에 도메인을 원하지 않는 경우에는 요금이 청구 되지 않습니다. . uk 도메인은이에 대 한 예외입니다. 영국의 도메인을 구입 하는 경우 즉시 청구 되며 환불 수 없습니다.

**내 구독의 다른 Azure App Service 앱에서 도메인을 사용할 수 있나요?**

예. Azure Portal에서 사용자 지정 도메인 및 TLS 블레이드에 액세스 하면 구매한 도메인이 표시 됩니다. 이러한 도메인을 사용 하도록 앱을 구성할 수 있습니다.

**한 구독에서 다른 구독으로 도메인을 전송할 수 있나요?**

[AzResource](/powershell/module/az.Resources/Move-azResource) PowerShell cmdlet을 사용 하 여 도메인을 다른 구독/리소스 그룹으로 이동할 수 있습니다.

**현재 Azure App Service 앱이 없는 경우 내 사용자 지정 도메인을 관리 하려면 어떻게 해야 하나요?**

App Service 웹 앱이 없는 경우에도 도메인을 관리할 수 있습니다. 도메인은 가상 머신, 저장소 등과 같은 Azure 서비스에 사용할 수 있습니다. App Service Web Apps에 대해 도메인을 사용 하려는 경우 웹 앱에 도메인을 바인딩하기 위해 무료 App Service 계획에 없는 웹 앱을 포함 해야 합니다.

**사용자 지정 도메인을 사용 하는 웹 앱을 다른 구독으로 이동 하거나 v1에서 V2로 App Service Environment 수 있나요?**

예, 웹 앱을 구독 간에 이동할 수 있습니다. [Azure에서 리소스를 이동 하는 방법](../azure-resource-manager/management/move-resource-group-and-subscription.md)의 지침을 따르세요. 웹 앱을 이동할 때 몇 가지 제한 사항이 있습니다. 자세한 내용은 [App Service 리소스 이동에 대 한 제한 사항](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)을 참조 하세요.

웹 앱을 이동한 후에는 사용자 지정 도메인 설정 내에서 도메인의 호스트 이름 바인딩이 동일 하 게 유지 되어야 합니다. 호스트 이름 바인딩을 구성 하는 데 필요한 추가 단계는 없습니다.