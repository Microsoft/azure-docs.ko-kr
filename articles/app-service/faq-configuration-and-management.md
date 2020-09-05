---
title: 구성 Faq
description: Azure App Service에 대 한 구성 및 관리 문제에 대 한 자주 묻는 질문에 대 한 답을 가져옵니다.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5545acbfd6bb239b9518fbe352b819f300dafaf0
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962352"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Azure의 Web Apps에 대한 구성 및 관리 FAQ

이 문서에는 [Azure App Service Web Apps 기능](https://azure.microsoft.com/services/app-service/web/)의 구성 및 관리 문제에 대한 FAQ(질문과 대답)가 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>App Service 리소스를 이동하려는 경우 알아야 할 제한 사항이 있나요?

App Service 리소스를 새 리소스 그룹이나 구독으로 이동할 계획인 경우 알아야 할 몇 가지 제한 사항이 있습니다. 자세한 내용은 [App Service 제한 사항](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)을 참조하세요.

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>내 웹앱에 대한 사용자 지정 도메인 이름을 어떻게 사용할 수 있나요?

Azure 웹앱에서 사용자 지정 도메인 이름을 사용하는 방법에 대한 일반적인 질문과 대답은 7분 비디오 [Add a custom domain name](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)(사용자 지정 도메인 이름 추가)을 참조하세요. 이 비디오에서는 사용자 지정 도메인 이름을 추가하는 방법에 대한 연습을 제공합니다. App Service 웹앱에서 *.azurewebsites.net URL 대신 고유한 URL을 사용하는 방법을 설명합니다. 또한 [사용자 지정 도메인 이름을 매핑하는 방법](app-service-web-tutorial-custom-domain.md)의 자세한 연습을 확인할 수 있습니다.


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>내 웹앱에 대한 새로운 사용자 지정 도메인을 어떻게 구매할 수 있나요?

App Service 웹앱에 대한 사용자 지정 도메인을 구매 및 설정하는 방법을 알아보려면 [App Service에서 사용자 지정 도메인 이름 구매 및 구성](manage-custom-dns-buy-domain.md)을 참조하세요.


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>내 웹 앱에 대 한 기존 TLS/SSL 인증서를 업로드 하 고 구성 어떻게 할까요??

기존 사용자 지정 TLS/SSL 인증서를 업로드 하 고 설정 하는 방법에 대 한 자세한 내용은 [App Service 앱에 TLS/ssl 인증서 추가](configure-ssl-certificate.md)를 참조 하세요.


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Azure에서 내 웹 앱에 대 한 새 TLS/SSL 인증서를 구매 하 고 구성할 어떻게 할까요? 있나요?

App Service 웹 앱에 대 한 TLS/SSL 인증서를 구매 하 고 설정 하는 방법을 알아보려면 [App Service 앱에 tls/ssl 인증서 추가](configure-ssl-certificate.md)를 참조 하세요.


## <a name="how-do-i-move-application-insights-resources"></a>Application Insights 리소스를 어떻게 이동할 수 있나요?

현재 Azure Application Insights에서는 이동 작업을 지원하지 않습니다. 원래 리소스 그룹에 Application Insights 리소스가 포함된 경우 해당 리소스를 이동할 수 없습니다. App Service 앱을 이동할 때 Application Insights 리소스를 포함하는 경우 전체 이동 작업이 실패합니다. 하지만 Application Insights와 App Service 계획의 경우 앱이 제대로 작동하려면 해당 앱과 같은 리소스 그룹에 있지 않아도 됩니다.

자세한 내용은 [App Service 제한 사항](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)을 참조하세요.

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>어디에서 지침 검사 목록을 찾고 리소스 이동 작업을 알아볼 수 있나요?

[App Service 제한 사항](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)에서는 새 구독 또는 동일한 구독의 새 리소스 그룹으로 리소스를 이동하는 방법을 보여 줍니다. 리소스 이동 검사 목록에 대한 정보를 확인하고, 어떤 서비스가 이동 작업을 지원하는지 알아보고, App Service 제한 사항 및 기타 항목을 자세히 알아볼 수 있습니다.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>내 웹앱에 대한 서버 표준 시간대를 어떻게 설정할 수 있나요?

웹앱에 대한 서버 표준 시간대를 설정하려면:

1. Azure Portal의 App Service 구독에서 **애플리케이션 설정** 메뉴로 이동합니다.
2. **앱 설정**에서 이 설정을 추가합니다.
    * 키 = WEBSITE_TIME_ZONE
    * 값 = *원하는 표준 시간대*
3. **저장**을 선택합니다.

Windows에서 실행 되는 앱 서비스의 경우 [기본 표준 시간대](/windows-hardware/manufacture/desktop/default-time-zones) 문서의 **표준** 시간대 열에서 허용 되는 값을 참조 하세요. Linux에서 실행 되는 앱 서비스에 대해 [TZ 데이터베이스 이름을](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) 표준 시간대 값으로 설정 합니다. 다음은 TZ 데이터베이스 이름: 아메리카/Adak의 예입니다.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>내 연속 WebJobs가 때때로 실패하는 이유는 무엇인가요?

기본적으로 웹앱은 일정 기간 유휴 상태인 경우 언로드됩니다. 이를 통해 시스템 리소스가 절약됩니다. 기본 및 표준 계획에서 **무중단** 설정을 켜서 항상 웹앱을 계속 로드할 수 있습니다. 웹앱에서 연속 WebJobs를 실행하는 경우 **무중단**을 켜야 합니다. 그렇지 않으면 WebJobs가 안정적으로 실행되지 않을 수 있습니다. 자세한 내용은 [연속형 WebJob 만들기](webjobs-create.md#CreateContinuous)를 참조하세요.

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>내 웹앱의 아웃바운드 IP 주소를 어떻게 확인할 수 있나요?

웹앱의 아웃바운드 IP 주소 목록을 확인하려면:

1. Azure Portal의 웹앱 블레이드에서 **속성** 메뉴로 이동합니다.
2. **아웃바운드 IP 주소**를 검색합니다.

아웃바운드 IP 주소 목록이 표시됩니다.

웹 서비스가 App Service 환경에서 호스트되는 경우 아웃 바운드 IP 주소를 불러오는 방법을 알아보려면 [아웃바운드 네트워크 주소](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)를 참조하세요.

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>내 웹앱에 대한 예약되거나 전용인 인바운드 IP 주소를 어떻게 확인하나요?

Azure 앱 웹 사이트에 대 한 인바운드 호출에 대 한 전용 또는 예약 된 IP 주소를 설정 하려면 IP 기반 TLS/SSL 인증서를 설치 하 고 구성 합니다.

인바운드 호출에 대한 전용이거나 예약된 IP 주소를 사용하려면 App Service 계획이 기본 이상의 서비스 계획에 포함되어야 합니다.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>다른 곳에서 호스트되는 웹 사이트와 같은 Azure 외부에서 사용하도록 내 App Service Certificate를 내보낼 수 있나요? 

예, Azure 외부에서 사용할 수 있도록 내보낼 수 있습니다. 자세한 내용은 [FAQs for App Service certificates and custom domains](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)(App Service Certificate 및 사용자 지정 도메인에 대한 FAQ)를 참조하세요.

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>다른 Azure Cloud Services에서 사용하도록 내 App Service Certificate를 내보낼 수 있나요?

포털에서는 Azure Key Vault를 통해 App Service Certificate를 App Service 앱에 배포하기 위한 최고의 환경을 제공합니다. 하지만 이러한 인증서를 Azure Virtual Machines와 같은 App Service 플랫폼 외부에서 사용하려고 하는 고객의 요청이 있었습니다. 다른 Azure 리소스에서 인증서를 사용할 수 있도록 App Service 인증서의 로컬 PFX 복사본을 만드는 방법을 알아보려면 [App Service 인증서의 로컬 PFX 복사본 만들기](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)를 참조하세요.

자세한 내용은 [FAQs for App Service certificates and custom domains](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)(App Service Certificate 및 사용자 지정 도메인에 대한 FAQ)를 참조하세요.


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>내 웹앱을 백업하려고 할 때 “부분적으로 성공” 메시지가 표시되는 이유는 무엇인가요?

백업 실패의 일반적인 원인은 애플리케이션에서 일부 파일을 사용하고 있다는 것입니다. 백업을 수행하는 동안 사용 중인 파일이 잠깁니다. 이로 인해 이러한 파일이 백업되지 않아 “부분적으로 성공” 상태가 될 수 있습니다. 백업 프로세스에서 파일을 제외하면 이 문제가 발생하지 않을 수 있습니다. 필요한 항목만 백업하도록 선택할 수 있습니다. 자세한 내용은 [Backup just the important parts of your site with Azure web apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)(Azure Web Apps를 사용하여 사이트의 중요한 부분만 백업)를 참조하세요.

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>HTTP 응답에서 헤더를 어떻게 제거할 수 있나요?

HTTP 응답에서 헤더를 제거 하려면 사이트의 web.config 파일을 업데이트 합니다. 자세한 내용은 [Remove standard server headers on your Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)(Azure Websites에서 표준 서버 헤더 제거)를 참조하세요.

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>App Service는 PCI Standard 3.0 및 3.1 규격인가요?

현재 Azure App Service의 Web Apps 기능은 PCI DSS(Data Security Standard) 버전 3.0 수준 1 규격입니다. PCI DSS 버전 3.1은 계획되어 있습니다. 최신 표준의 채택이 진행되는 방식에 대한 계획은 이미 진행 중입니다.

PCI DSS 버전 3.1 인증을 적용하려면 TLS(전송 계층 보안) 1.0을 사용하지 않도록 설정해야 합니다. 현재 TLS 1.0을 사용하지 않도록 설정하는 것은 대부분 App Service 계획에 대한 옵션입니다. 그러나 App Service Environment를 사용하거나 워크로드를 App Service Environment로 마이그레이션하려는 경우 현재 환경을 더욱 세밀하게 제어할 수 있습니다. 여기에는 Azure 지원에 문의하여 TLS 1.0을 사용하지 않도록 설정하는 작업이 포함됩니다. 앞으로 이러한 설정을 사용자에게 제공할 계획입니다.

자세한 내용은 [Microsoft Azure App Service web app compliance with PCI Standard 3.0 and 3.1](https://support.microsoft.com/help/3124528)(Microsoft Azure App Service 웹앱과 PCI Standard 3.0 및 3.1의 호환 여부)을 참조하세요.

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>스테이징 환경 및 배포 슬롯을 어떻게 사용하나요?

표준 및 프리미엄 App Service 계획에서 웹앱을 App Service에 배포할 때 기본 프로덕션 슬롯 대신 개별 배포 슬롯으로 배포할 수 있습니다. 배포 슬롯은 자체 호스트 이름을 갖춘 라이브 웹앱입니다. 웹앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다.

배포 슬롯 사용에 대한 자세한 내용은 [App Service에서 스테이징 환경 설정](deploy-staging-slots.md)을 참조하세요.

## <a name="how-do-i-access-and-review-webjob-logs"></a>WebJob 로그에 액세스하고 이를 검토하려면 어떻게 해야 하나요?

WebJob 로그를 검토하려면:

1. **Kudu 웹 사이트** ()에 로그인 `https://*yourwebsitename*.scm.azurewebsites.net` 합니다.
2. WebJob을 선택합니다.
3. **출력 설정/해제** 단추를 선택합니다.
4. 출력 파일을 다운로드하려면 **다운로드** 링크를 선택합니다.
5. 개별 실행의 경우 **Individual Invoke**(개별 호출)를 선택합니다.
6. **출력 설정/해제** 단추를 선택합니다.
7. 다운로드 링크를 선택합니다.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>SQL Server에서 하이브리드 연결을 사용하려고 합니다. “System.OverflowException: 산술 연산으로 인해 오버플로가 발생했습니다.”라는 메시지가 표시되는 이유는 무엇인가요?

하이브리드 연결을 사용하여 SQL Server에 액세스하면 2016년 5월 10일의 Microsoft .NET 업데이트로 인해 연결에 실패할 수 있습니다. 다음 메시지가 표시될 수 있습니다.

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>해결 방법

이 예외는 수정된 하이브리드 연결 관리자의 문제로 인해 야기되었습니다. 이 문제를 해결하려면 [하이브리드 연결 관리자를 업데이트](https://go.microsoft.com/fwlink/?LinkID=841308)해야 합니다.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>URL 다시 쓰기 규칙을 추가 어떻게 할까요??

URL 다시 쓰기 규칙을 추가 하려면 **wwwroot** 폴더에 관련 된 구성 항목을 사용 하 여 web.config 파일을 만듭니다. 자세한 내용은 [Azure 앱 Services: URL 재작성 이해](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)를 참조 하세요.

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>App Service에 대한 인바운드 트래픽을 제어하려면 어떻게 하나요?

사이트 수준에서 App Service에 대한 인바운드 트래픽을 제어하는 데는 두 가지 옵션이 있습니다.

* 동적 IP 제한을 켭니다. 동적 IP 제한을 켜는 방법을 알아보려면 [IP and domain restrictions for Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)(Azure Websites에 대한 IP 및 도메인 제한)를 참조하세요.
* 모듈 보안을 켭니다. 모듈 보안을 켜는 방법에 대한 자세한 내용은 [Azure Websites의 ModSecurity 웹 애플리케이션 방화벽](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)을 참조하세요.

App Service Environment를 사용할 경우 [Barracuda 방화벽](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)을 사용할 수 있습니다.

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>App Service 웹앱에서 포트를 어떻게 차단하나요?

App Service 공유 테넌트 환경에서는 인프라의 특성 때문에 특정 포트를 차단할 수 없습니다. Visual Studio 원격 디버깅을 위해 TCP 포트 4020, 4022 및 4024도 열릴 수 있습니다.

App Service Environment에서는 인바운드 및 아웃바운드 트래픽을 완벽하게 제어할 수 있습니다. 네트워크 보안 그룹을 사용하여 특정 포트를 제한하거나 차단할 수 있습니다. App Service Environment에 대한 자세한 내용은 [Introducing App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/)(App Service Environment 소개)를 참조하세요.

## <a name="how-do-i-capture-an-f12-trace"></a>F12 추적을 어떻게 캡처하나요?

F12 추적을 캡처할 수 있는 두 가지 옵션이 있습니다.

* F12 HTTP 추적
* F12 콘솔 출력

### <a name="f12-http-trace"></a>F12 HTTP 추적

1. Internet Explorer에서 해당 웹 사이트로 이동합니다. 다음 단계를 수행하기 전에 로그인해야 합니다. 로그인하지 않으면 F12 추적이 중요한 로그인 데이터를 캡처합니다.
2. F12 키를 누릅니다.
3. **네트워크** 탭이 선택되어 있는지 확인하고 녹색 **재생** 단추를 선택합니다.
4. 문제를 재현하는 단계를 수행합니다.
5. 빨간색 **중지** 단추를 선택합니다.
6. **저장** 단추(디스크 아이콘)를 선택하고 HAR 파일을 저장합니다(Internet Explorer 및 Microsoft Edge의 경우). *또는* HAR 파일을 마우스 오른쪽 단추로 클릭하고 **콘텐츠와 함께 HAR 파일로 저장**(Chrome의 경우)을 선택합니다.

### <a name="f12-console-output"></a>F12 콘솔 출력

1. **콘솔** 탭을 선택합니다.
2. 하나 이상의 항목이 포함된 각 탭에 대해 탭(**오류**, **경고** 또는 **정보**)을 선택합니다. 탭이 선택 되어 있지 않으면 커서를 커서를 밖으로 이동 하면 탭 아이콘이 회색 이나 검은색입니다.
3. 창의 메시지 영역을 마우스 오른쪽 단추로 클릭하고 **모두 복사**를 선택합니다.
4. 복사된 텍스트를 파일에 붙여넣고 파일을 저장합니다.

HAR 파일을 보려면 [HAR 뷰어](http://www.softwareishard.com/har/viewer/)를 사용할 수 있습니다.

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>App Service 웹앱을 ExpressRoute에 연결된 가상 네트워크에 연결하려고 할 때 오류가 발생하는 이유는 무엇인가요?

Azure 웹앱을 Azure ExpressRoute에 연결된 가상 네트워크에 연결하려고 하면 연결에 실패합니다. “게이트웨이가 VPN 게이트웨이가 아닙니다.”라는 메시지가 표시됩니다.

현재는 ExpressRoute에 연결된 가상 네트워크에 지점 및 사이트 간 VPN을 연결할 수 없습니다. 동일한 가상 네트워크에 대해 지점 및 사이트 간 VPN 및 ExpressRoute를 함께 사용할 수 없습니다. 자세한 내용은 [ExpressRoute 및 사이트 간 VPN 연결 제한 및 제한 사항](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)을 참조하세요.

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>App Service 웹앱을 정적 라우팅(정책 기반) 게이트웨이가 있는 가상 네트워크에 연결하려면 어떻게 하나요?

현재 App Service 웹앱을 정적 라우팅(정책 기반) 게이트웨이가 있는 가상 네트워크에 연결하는 기능은 지원되지 않습니다. 대상 가상 네트워크가 이미 있는 경우 동적 라우팅 게이트웨이와의 지점 및 사이트 간 VPN을 사용하도록 설정해야 해당 가상 네트워크를 웹앱에 연결할 수 있습니다. 게이트웨이가 정적 라우팅으로 설정되면 지점 및 사이트 간 VPN을 사용하도록 설정할 수 없습니다. 

자세한 내용은 [Azure 가상 네트워크에 앱 통합](web-sites-integrate-with-vnet.md)을 참조하세요.

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>내 App Service Environment에서 두 명의 작업자를 사용할 수 있는데 하나의 App Service 계획만 만들 수 있는 이유는 무엇인가요?

내결함성을 제공하려면 App Service Environment에서 각 작업자 풀에 하나 이상의 추가 컴퓨팅 리소스가 필요합니다. 추가 컴퓨팅 리소스에는 워크로드를 할당할 수 없습니다.

자세한 내용은 [App Service Environment를 만드는 방법](environment/app-service-web-how-to-create-an-app-service-environment.md)을 참조하세요.

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>App Service Environment를 만들려고 할 때 시간 제한이 표시되는 이유는 무엇인가요?

경우에 따라 App Service Environment 만들기에 실패합니다. 이 경우 활동 로그에서 다음 오류를 확인할 수 있습니다.
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

이 문제를 해결하려면 다음 중 해당하는 조건이 없는지 확인합니다.
* 서브넷이 너무 작습니다.
* 서브넷이 비어 있지 않습니다.
* ExpressRoute가 App Service Environment에서 네트워크 연결 요구 사항을 허용하지 않습니다.
* 잘못된 네트워크 보안 그룹이 App Service Environment에서 네트워크 연결 요구 사항을 허용하지 않습니다.
* 강제 터널링이 켜져 있습니다.

자세한 내용은 [Frequent issues when deploying (creating) a new Azure App Service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase)(새 Azure App Service Environment를 배포할(만들) 때 자주 발생하는 문제)를 참조하세요.

## <a name="why-cant-i-delete-my-app-service-plan"></a>내 App Service 계획을 삭제할 수 없는 이유는 무엇인가요?

App Service 앱이 App Service 계획과 연결된 경우 App Service 앱을 삭제할 수 없습니다. App Service 계획을 삭제하기 전에 App Service 계획에서 모든 연결된 App Service 앱을 제거합니다.

## <a name="how-do-i-schedule-a-webjob"></a>WebJob을 예약하려면 어떻게 하나요?

Cron 식을 사용하여 예약된 WebJob을 만들 수 있습니다.

1. settings.job 파일을 만듭니다.
2. 이 JSON 파일에서 Cron 식을 사용하여 일정 속성을 포함합니다. 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

예약된 WebJob에 대한 자세한 내용은 [Cron 식을 사용하여 예약된 WebJob 만들기](webjobs-create.md#CreateScheduledCRON)를 참조하세요.

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>내 App Service 앱에 대한 침투 테스트를 수행하려면 어떻게 하나요?

침투 테스트를 수행하려면 [요청을 제출](https://portal.msrc.microsoft.com/engage/pentest)합니다.

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Traffic Manager를 사용하는 App Service 웹앱에 대한 사용자 지정 도메인 이름을 구성하려면 어떻게 하나요?

부하 분산을 위해 Azure Traffic Manager를 사용하는 App Service 앱에서 사용자 지정 도메인 이름을 사용하는 방법을 알아보려면 [Traffic Manager를 사용하여 Azure 웹앱에 대한 사용자 지정 도메인 이름 구성](configure-domain-traffic-manager.md)을 참조하세요.

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>내 App Service Certificate에 사기 플래그가 지정되었습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service Certificate 구매의 도메인을 확인하는 동안 다음 메시지가 표시될 수 있습니다.

“인증서가 사기성이 있을 수 있다고 플래그 지정되었습니다. 요청을 현재 검토하는 중입니다. 24시간 내에 인증서를 사용할 수 있게 되지 않으면 Azure 지원에 문의하세요."

메시지가 표시되면 이 사기 확인 프로세스를 완료하는 데는 24시간까지 걸릴 수 있습니다. 이 시간 동안 메시지가 계속 표시됩니다.

App Service Certificate가 24시간 후에도 이 메시지를 계속 표시하면 다음 PowerShell 스크립트를 실행하세요. 스크립트가 문제를 해결하기 위해 [인증서 공급자](https://www.godaddy.com/)에 직접 연결합니다.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>App Service에서 인증 및 권한 부여가 어떻게 작동하나요?

App Service의 인증 및 권한 부여에 대한 자세한 문서는 다양한 ID 공급자 로그인에 대한 문서를 참조하세요.
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft 계정](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>기본 *.azurewebsites.net 도메인을 내 Azure 웹앱의 사용자 지정 도메인으로 리디렉션하려면 어떻게 하나요?

Azure에서 Web Apps를 사용하여 새 웹 사이트를 만들 경우 기본 *사이트 이름*.azurewebsites.net 도메인이 사이트에 할당됩니다. 사용자 지정 호스트 이름을 사이트에 추가 하 고 사용자가 기본 *. azurewebsites.net 도메인에 액세스할 수 없도록 하려는 경우 기본 URL을 리디렉션할 수 있습니다. 웹 사이트의 기본 도메인에서 사용자 지정 도메인으로 모든 트래픽을 리디렉션하는 방법을 알아보려면 [Redirect the default domain to your custom domain in Azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/)(Azure Web Apps에서 사용자 지정 도메인으로 기본 도메인 리디렉션)를 참조하세요.

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>App Service에 어떤 .NET 버전이 설치되어 있는지 확인하려면 어떻게 하나요?

App Service에 설치된 Microsoft .NET 버전을 찾는 가장 빠른 방법은 Kudu 콘솔을 사용하는 것입니다. Kudu 콘솔은 포털에서 액세스하거나 App Service 앱의 URL을 사용하여 액세스할 수 있습니다. 자세한 내용은 [Determine the installed .NET version in App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services)(App Service에 설치된 .NET 버전 확인)를 참조하세요.

## <a name="why-isnt-autoscale-working-as-expected"></a>자동 크기 조정이 예상대로 작동하지 않는 이유는 무엇인가요?

Azure 자동 크기 조정이 웹앱 인스턴스의 크기를 예상대로 축소 또는 확장하지 않는 경우, “플래핑”으로 인한 무한 반복을 피하기 위해 의도적으로 크기 조정이 되지 않을 수 있습니다. 이 문제는 일반적으로 확장 및 축소 임계값 사이에 적절한 여유가 없는 경우 발생합니다. "플래핑"을 방지하는 방법을 알아보고 다른 자동 크기 조정 모범 사례를 읽으려면 [자동 크기 조정 모범 사례](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)를 참조하세요.

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>때때로 자동 크기 조정이 부분적으로만 크기를 조정하는 이유는 무엇인가요?

자동 크기 조정은 메트릭이 미리 구성된 경계를 초과할 경우 트리거됩니다. 경우에 따라 용량이 예상한 것에 비해 부분적으로만 채워지는 것을 확인할 수 있습니다. 이 문제는 원하는 인스턴스 수를 사용할 수 없는 경우 발생할 수 있습니다. 이 시나리오에서 자동 크기 조정은 사용 가능한 수의 인스턴스를 사용하여 부분적으로 채웁니다. 그다음에 자동 크기 조정은 리밸런스 논리를 실행하여 더 많은 용량을 확보합니다. 나머지 인스턴스를 할당합니다. 이 작업에는 몇 분이 걸릴 수 있습니다.

몇 분 후 인스턴스 수가 예상대로 표시되지 않는다면 부분 다시 채우기가 경계 내에서 메트릭을 불러오는 데 충분했기 때문일 수 있습니다. 또는 자동 크기 조정이 메트릭 하한에 도달했기 때문에 규모가 축소되었을 수 있습니다.

이러한 조건에 해당하지 않고 문제가 지속되면 지원 요청을 제출하세요.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>내 콘텐츠에 대해 HTTP 압축을 켜려면 어떻게 하나요?

정적 및 동적 콘텐츠 형식에 대한 압축을 둘 다 켜려면 애플리케이션 수준 web.config 파일에 다음 코드를 추가합니다.

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

압축할 특정 동적 및 정적 MIME 형식을 지정할 수도 있습니다. 자세한 내용은 [단순 Azure 웹 사이트의 httpCompression 설정](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)에서 포럼 질문에 대한 응답을 참조하세요.

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>온-프레미스 환경에서 App Service로 마이그레이션하려면 어떻게 하나요?

Windows 및 Linux 웹 서버에서 App Service로 마이그레이션하려면 Azure App Service Migration Assistant를 사용하면 됩니다. 이 마이그레이션 도구는 필요에 따라 Azure에서 웹앱 및 데이터베이스 만들고 나서 콘텐츠를 게시합니다. 자세한 내용은 [Azure App Service Migration Assistant](https://appmigration.microsoft.com/)를 참조하세요.