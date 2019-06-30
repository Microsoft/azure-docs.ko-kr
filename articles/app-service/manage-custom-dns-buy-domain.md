---
title: Azure에서 사용자 지정 도메인 이름 구입 - App Service
description: Azure App Service에서 웹앱으로 사용자 지정 도메인 이름을 구입하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6bba176a27cc70321915654e3e2e62320f22c16c
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310136"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Azure App Service의 사용자 지정 도메인 이름 구입

App Service 도메인은 Azure에서 직접 관리 되는 최상위 도메인입니다. 이 도메인을 통해 [Azure App Service](overview.md)의 사용자 지정 도메인을 쉽게 관리할 수 있습니다. 이 자습서에서는 App Service 도메인을 구입하고 DNS 이름을 Azure App Service에 할당하는 방법을 보여 줍니다.

Azure VM 또는 Azure Storage의 경우 [Azure VM 또는 Azure Storage에 App Service 도메인 할당](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/)을 참조하세요. Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [App Service 앱을 만들거나](/azure/app-service/) 다른 자습서에서 만든 앱을 사용합니다.
* [구독에 대한 지출 한도를 삭제합니다](../billing/billing-spending-limit.md#remove). 체험 구독 크레딧으로는 App Service 도메인을 구입할 수 없습니다.

## <a name="prepare-the-app"></a>앱 준비

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Azure App Service에서 사용자 지정 도메인을 사용하려면 앱의 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/)이 유료 계층(**공유**, **기본**, **표준** 또는 **프리미엄**)이어야 합니다. 이 단계에서는 앱이 지원되는 가격 책정 계층에 있음을 확인합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털](https://portal.azure.com)에서 Azure 계정으로 로그인합니다.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal에서 앱으로 이동합니다.

왼쪽 메뉴에서 **App Services**를 선택한 다음 앱 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service 앱의 관리 페이지가 표시됩니다.  

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)** 를 선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **F1** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **F1** 계층에서는 지원되지 않습니다. 

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service 계획이 **F1** 계층이 아닌 경우 **스케일업** 페이지를 닫고 [도메인 구입](#buy-the-domain)으로 건너뜁니다.

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

유료 계층(**D1**, **B1**, **B2**, **B3** 또는 **프로덕션** 범주의 모든 계층) 중 하나를 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기**를 클릭합니다.

**적용**을 클릭합니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

다음 알림이 표시되면 강화 작업이 완료됩니다.

![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>도메인 구입

### <a name="pricing-information"></a>가격 정보
Azure App Service 도메인에 대 한 정보를 가격에 대 한 참조를 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/) App Service 도메인까지 아래로 스크롤합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인
[Azure 포털](https://portal.azure.com/)에서 Azure 계정으로 로그인합니다.

### <a name="launch-buy-domains"></a>도메인 구입 시작
**App Services** 탭에서 앱의 이름을 클릭하고, **설정**을 선택한 다음, **사용자 지정 도메인**을 선택합니다.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**사용자 지정 도메인** 페이지에서 **도메인 구입**을 클릭합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> **App Service 도메인** 섹션이 표시되지 않으면 Azure 계정에 대한 지출 한도를 제거해야 합니다([필수 구성 요소](#prerequisites) 참조).
>
>

### <a name="configure-the-domain-purchase"></a>도메인 구매 구성

**App Service 도메인** 페이지의 **도메인 검색** 상자에 구입할 도메인 이름을 입력하고 `Enter`를 입력합니다. 사용 가능한 도메인이 텍스트 상자 아래에 나타납니다. 구입하려는 도메인을 하나 이상 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> App Service 도메인에서 지원되는 [최상위 도메인](https://wikipedia.org/wiki/Top-level_domain)은 _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ 및 _co.in_입니다.
>
>

**연락처 정보**를 클릭하고 도메인의 연락처 정보 양식을 작성합니다. 완료한 후 **확인**을 클릭하면 App Service 도메인 페이지로 돌아갑니다.

모든 필수 필드를 가능한 한 정확하게 기입해야 합니다. 잘못된 연락처 정보 데이터로 인해 도메인을 구입하지 못할 수 있습니다.

그런 다음 도메인에 대해 원하는 옵션을 선택합니다. 설명은 다음 표를 참조하세요.

| 설정 | 제안 값 | 설명 |
|-|-|-|
|개인 정보 보호 | 사용 | _체험_ 구매 가격에 포함된 "개인 정보 보호"를 선택합니다. 일부 최상위 도메인은 개인 정보 보호를 지원하지 않는 등록 기관에서 관리하며 **개인 정보 보호** 페이지에 나열됩니다. |
| 기본 호스트 이름 할당 | **www** 및 **\@** | 필요한 경우 원하는 호스트 이름 바인딩을 선택합니다. 도메인 구매 작업이 완료되면 선택한 호스트 이름에서 앱에 액세스할 수 있습니다. 앱이 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) 뒤에 있는 경우 Traffic Manager는 A 레코드를 지원하지 않으므로 루트 도메인을 할당하는 옵션이 표시되지 않습니다. 도메인 구매가 완료된 후 호스트 이름 할당을 변경할 수 있습니다. |

### <a name="accept-terms-and-purchase"></a>조건에 동의하고 구매

**약관**을 클릭하여 조건 및 요금을 검토한 후 **구입**을 클릭합니다.

> [!NOTE]
> App Service 도메인은 도메인을 호스트 하려면 GoDaddy 도메인 등록 및 Azure DNS를 사용 합니다. 도메인 등록 요금 외에도 Azure DNS에 대한 사용 요금이 적용됩니다. 자세한 내용은 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.
>
>

**App Service 도메인** 페이지로 돌아가서 **확인**을 클릭합니다. 작업이 진행되는 동안 다음과 같은 알림이 표시됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>호스트 이름 테스트

앱에 기본 호스트 이름을 할당한 경우 선택한 각 호스트 이름에 대해 성공 알림도 표시됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

또한 선택한 호스트 이름도 **사용자 지정 도메인** 페이지의 **사용자 지정 호스트 이름** 섹션에서 표시됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> A **의 보안을 유지할** 레이블 사용자 지정 도메인 SSL 인증서에 아직 바인딩되어 고 오류 또는 경고를 브라우저에 따라 사용자 지정 도메인 HTTPS 요청을 브라우저에서 전달 됨을 의미 합니다. SSL 바인딩을 구성 하려면 [구입 및 Azure App Service에 대 한 SSL 인증서 구성](web-sites-purchase-ssl-web-site.md)합니다.
>

호스트 이름을 테스트하려면 브라우저에서 나열된 호스트 이름으로 이동합니다. 이전 스크린샷의 예에서 탐색 하 려 _kontoso.net_ 하 고 _www\.kontoso.net_합니다.

## <a name="assign-hostnames-to-app"></a>앱에 호스트 이름 할당

구매 과정에서 앱에 하나 이상의 기본 호스트 이름을 할당 하지 않으려는 경우 또는 나열 되지 않은 호스트 이름을 할당 해야 하는 경우 언제 든 지 호스트 이름을 할당할 수 있습니다.

App Service 도메인에서 다른 앱으로 호스트 이름을 할당할 수도 있습니다. 이 단계는 App Service 도메인 및 앱이 동일한 구독에 속하는지 여부에 따라 달라집니다.

- 다른 구독: App Service 도메인에서 외부에서 구매한 도메인과 같은 앱으로 사용자 지정 DNS 레코드를 매핑합니다. 사용자 지정 DNS 이름을 App Service 도메인에 추가하는 방법에 대한 자세한 내용은 [사용자 지정 DNS 레코드 관리](#custom)를 참조하세요. 외부에서 구매한 도메인을 앱에 매핑하려면 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)을 참조하세요. 
- 동일한 구독: 다음 단계를 사용하세요.

### <a name="launch-add-hostname"></a>호스트 이름 추가 시작
**App Services** 페이지에서 호스트 이름을 할당할 앱 이름을 선택하고 **설정**, **사용자 지정 도메인**을 차례로 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

구매한 도메인이 **App Service 도메인** 섹션에 나열되는지 확인하되, 선택하지는 마세요. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> 같은 구독에 있는 모든 App Service 도메인은 앱의 **사용자 지정 도메인** 페이지에 표시됩니다. 도메인이 앱의 구독에 있지만 앱의 **사용자 지정 도메인** 페이지에서 볼 수 없는 경우 **사용자 지정 도메인** 페이지를 다시 열거나 웹 페이지를 새로 고칩니다. 또한 Azure Portal의 맨 위에서 진행 상황 또는 생성 실패에 대한 알림 벨을 확인합니다.
>
>

**호스트 이름 추가**를 선택합니다.

### <a name="configure-hostname"></a>호스트 이름 구성
**호스트 이름 추가** 대화 상자에서 App Service 도메인 또는 하위 도메인의 정규화된 도메인 이름을 입력합니다. 예를 들면 다음과 같습니다.

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

완료되면 **유효성 검사**를 선택합니다. 호스트 이름 레코드 형식이 자동으로 선택됩니다.

**호스트 이름 추가**를 선택합니다.

작업이 완료되면 할당된 호스트 이름에 대해 성공 알림이 표시됩니다.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>호스트 이름 추가 닫기
**호스트 이름 추가** 페이지에서 원하는 대로, 앱에 다른 호스트 이름을 할당합니다. 완료되면 **호스트 이름 추가** 페이지를 닫습니다.

이제 **사용자 지정 도메인** 페이지에 새로 할당된 호스트 이름이 표시됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>호스트 이름 테스트

브라우저에서 나열된 호스트 이름으로 이동합니다. 이전 스크린샷의 예에서 _abc.kontoso.net_으로 이동해 봅니다.

## <a name="renew-the-domain"></a>도메인 갱신

구입한 App Service 도메인은 구입 시점으로부터 1년 동안 유효합니다. 도메인은 기본적으로 다음 해의 결제 방법을 부과하여 자동으로 갱신되도록 구성됩니다. 도메인 이름을 수동으로 갱신할 수 있습니다.

자동 갱신을 해제하거나 도메인을 수동으로 갱신하려면 다음 단계를 따릅니다.

**App Services** 탭에서 앱의 이름을 클릭하고, **설정**을 선택한 다음, **사용자 지정 도메인**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

도메인의 왼쪽 탐색 영역에서 **도메인 갱신**을 선택합니다. 도메인에 대한 자동 갱신을 중지하려면 **끄기**를 선택한 다음 **저장**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

도메인을 수동으로 갱신하려면 **도메인 갱신**을 선택합니다. 그러나이 단추는 때까지 활성화 [도메인의 만료 전 90 일 동안](#when-domain-expires)합니다.

도메인 갱신에 성공 하면 24 시간 동안 전자 메일 알림을 받게 됩니다.

## <a name="when-domain-expires"></a>도메인이 만료 된 경우

Azure는 만료를 처리 또는 App Service 도메인을 다음과 같이 만료:

* 자동 갱신 사용 하지 않도록 설정 합니다. 도메인 만료 전 90 일 동안 갱신 알림 전자 메일을 보낼 수 및 **도메인 갱신** 포털에서 단추가 활성화 됩니다.
* 자동 갱신 사용 하는 경우: 하루 도메인 만료 날짜 후에 Azure 도메인 이름 갱신에 대 한 요금을 청구할 하려고 합니다.
* 자동 갱신 중에 오류가 발생 하는 경우 (예를 들어, 카드 파일에서 만료 된 경우), 또는 도메인이 만료 됩니다를 허용 하 고 자동 갱신 비활성화 된 경우 Azure 알림 도메인 만료 및 공원의 도메인 이름입니다. 할 수 있습니다 [수동으로 갱신](#renew-the-domain) 도메인입니다.
* 만료 후 4 ~ 12 일 하루에 Azure에서는 추가 알림 전자 메일입니다. 할 수 있습니다 [수동으로 갱신](#renew-the-domain) 도메인입니다.
* 만료 후 19 날 도메인을 대기 중으로 남아 있지만 상환 요금이 적용 됩니다. 도메인 이름에 적용 가능한 모든 갱신 및 상환 요금에 따라 갱신 고객 지원 서비스를 호출할 수 있습니다.
* 만료 된 후 25 일에 Azure 도메인 이름 업계 경매 서비스를 사용 하 여 경매에 대해 도메인을 배치합니다. 도메인 이름에 적용 가능한 모든 갱신 및 상환 요금에 따라 갱신 고객 지원 서비스를 호출할 수 있습니다.
* 만료 후 30 일에 여러분이 더 이상 도메인을 사용할 수 없습니다.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>사용자 지정 DNS 레코드 관리

Azure에서 App Service 도메인에 대한 DNS 레코드는 [Azure DNS](https://azure.microsoft.com/services/dns/)를 사용하여 관리됩니다. 외부에서 구매한 도메인처럼 DNS 레코드를 추가, 제거 및 업데이트할 수 있습니다.

### <a name="open-app-service-domain"></a>App Service 도메인 열기

Azure Portal의 왼쪽 메뉴에서 **모든 서비스** > **App Service 도메인**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

관리할 도메인을 선택합니다. 

### <a name="access-dns-zone"></a>DNS 영역 액세스

도메인의 왼쪽 메뉴에서 **DNS 영역**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

이 작업으로 Azure DNS에서 App Service 도메인의 [DNS 영역](../dns/dns-zones-records.md) 페이지가 열립니다. DNS 레코드를 편집하는 방법에 대한 자세한 내용은 [Azure Portal에서 DNS 영역을 관리하는 방법](../dns/dns-operations-dnszones-portal.md)을 참조하세요.

## <a name="cancel-purchase-delete-domain"></a>구매 취소(도메인 삭제)

App Service 도메인을 구매한 후 5일 이내에 구매를 취소하고 전액 환불 받을 수 있습니다. 5일이 지나면 App Service 도메인을 삭제할 수 있지만 환불은 불가능합니다.

### <a name="open-app-service-domain"></a>App Service 도메인 열기

Azure Portal의 왼쪽 메뉴에서 **모든 서비스** > **App Service 도메인**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

취소 또는 삭제할 도메인을 선택합니다. 

### <a name="delete-hostname-bindings"></a>호스트 이름 바인딩 삭제

도메인의 왼쪽 메뉴에서 **호스트 이름 바인딩**을 선택합니다. 모든 Azure 서비스의 호스트 이름 바인딩이 여기에 나열됩니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

모든 호스트 이름 바인딩이 삭제되어야 App Service 도메인을 삭제할 수 있습니다.

**...**  > **삭제**를 선택하여 각 호스트 이름 바인딩을 삭제합니다. 모든 바인딩을 삭제한 후 **저장**을 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>취소 또는 삭제

도메인의 왼쪽 메뉴에서 **개요**를 선택합니다. 

구매한 도메인에 대한 취소 기간이 경과되지 않은 경우 **구매 취소**를 선택합니다. 그렇지 않으면 **삭제** 단추가 대신 표시됩니다. 환불하지 않고 도메인을 삭제하려면 **삭제**를 선택합니다.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

작업을 확인하려면 **예**를 선택합니다.

작업이 완료되면 구독에서 도메인이 해제되어 누구든지 다시 구매할 수 있게 됩니다. 

## <a name="direct-default-url-to-a-custom-directory"></a>사용자 지정 디렉터리로 기본 URL 전달

App Service는 기본적으로 웹 요청을 앱 코드의 루트 디렉터리로 보냅니다. `public`과 같은 하위 디렉터리로 보내려면 [사용자 지정 디렉터리로 기본 URL 전달](app-service-web-tutorial-custom-domain.md#virtualdir)을 참조하세요.
