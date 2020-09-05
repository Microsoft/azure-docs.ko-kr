---
title: ARM을 사용하여 ILB ASE 만들기
description: Azure Resource Manager 템플릿을 사용하여 내부 부하 분산 장치(ILB ASE)로 App Service 환경을 만드는 방법을 알아봅니다. 인터넷에서 앱을 완전히 격리합니다.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: f2124dd77e3e5d9828ea457a6bccdf7d1bc05405
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961774"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>내부 Load Balancer App Service Environment 만들기 및 사용 

Azure App Service Environment는 Azure App Service를 Azure VNet(가상 네트워크)의 서브넷에 배포한 것입니다. ASE(App Service Environment)에는 두 가지 배포 방법이 있습니다. 

- 외부 ASE라고도 하는 외부 IP 주소의 VIP 사용
- 내부 엔드포인트가 ILB(내부 부하 분산 장치)이기 때문에 ILB ASE라고도 하는 내부 IP 주소의 VIP 사용 

이 문서에서는 ILB ASE를 만드는 방법을 보여 줍니다. ASE의 개요는 [App Service Environment에 대한 소개][Intro]를 참조하세요. 외부 ASE를 만드는 방법을 알아보려면 [외부 ASE 만들기][MakeExternalASE]를 참조하세요.

## <a name="overview"></a>개요 

VNet의 인터넷 액세스 가능 엔드포인트 또는 IP 주소를 사용하여 ASE를 배포할 수 있습니다. IP 주소를 VNet 주소로 설정하려면 ASE는 ILB를 사용하여 배포되어야 합니다. ILB를 사용하여 ASE를 배포하는 경우 ASE 이름을 제공해야 합니다. ASE 이름은 ASE의 앱에 대한 도메인 접미사에 사용됩니다.  ILB ASE의 도메인 접미사는 &lt;ASE 이름&gt;.appserviceenvironment.net입니다. ILB ASE에서 만든 앱은 공용 DNS에 포함되지 않습니다. 

이전 버전의 ILB ASE에서는 도메인 접미사와 HTTPS 연결에 대한 기본 인증서를 제공해야 했습니다. 도메인 접미사는 ILB ASE를 만들 때 더 이상 수집되지 않으며, 기본 인증서도 더 이상 수집되지 않습니다. 지금 ILB ASE를 만들면 기본 인증서가 Microsoft에서 제공되며 브라우저에서 신뢰할 수 있습니다. 여전히 ASE의 앱에서 사용자 지정 도메인 이름을 설정하고 인증서를 해당 사용자 지정 도메인 이름으로 설정할 수 있습니다. 

ILB ASE를 사용하는 경우 수행할 수 있는 작업은 다음과 같습니다.

-   사이트 간 또는 ExpressRoute를 통해 액세스하는 클라우드에서 인트라넷 애플리케이션을 안전하게 호스팅
-   WAF 디바이스를 사용하여 앱 보호
-   공용 DNS 서버에 나열되지 않은 클라우드에 앱 호스트
-   프런트 엔드 앱이 안전하게 통합될 수 있는 인터넷 격리 백 엔드 앱 만들기

### <a name="disabled-functionality"></a>사용되지 않도록 설정된 기능 ###

ILB ASE를 사용하는 경우 수행할 수 없는 작업도 있습니다.

-   IP 기반 SSL 사용
-   특정 앱에 IP 주소 할당
-   Azure Portal을 통해 앱에서 인증서 구매 및 사용 직접 인증 기관에서 직접 인증서를 가져와서 앱에서 사용할 수 있습니다. Azure Portal을 통해 가져올 수 없습니다.

## <a name="create-an-ilb-ase"></a>ILB ASE 만들기 ##

ILB ASE를 만들려면

1. Azure Portal에서 **리소스 만들기** > **웹** > **App Service Environment**를 선택합니다.

2. 구독을 선택합니다.

3. 리소스 그룹을 선택하거나 만듭니다.

4. App Service Environment 이름을 입력합니다.

5. 내부 가상 IP 유형을 선택합니다.

    ![ASE 만들기](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> App Service Environment 이름은 37자 이하여야 합니다.

6. 네트워킹을 선택합니다.

7. Virtual Network를 선택하거나 만듭니다. 여기서 새 VNet을 만들면 주소 범위가 192.168.250.0/23으로 정의됩니다. 다른 주소 범위 또는 ASE와 다른 리소스 그룹을 사용하여 VNet을 만들려면 Azure Virtual Network 만들기 포털을 사용합니다. 

8. 빈 서브넷을 선택하거나 만듭니다. 서브넷을 선택하려면 해당 서브넷이 비어 있고 위임되지 않아야 합니다. ASE를 만든 후에는 서브넷 크기를 변경할 수 없습니다. 256개의 주소가 있고 최대 크기의 ASE 및 확장 요구를 처리할 수 있는 `/24` 크기를 권장합니다. 

    ![ASE 네트워킹][1]

7. **검토 및 만들기**를 선택한 다음, **만들기**를 선택합니다.


## <a name="create-an-app-in-an-ilb-ase"></a>ILB ASE에 앱 만들기 ##

일반적으로 ASE에서 앱을 만드는 것과 동일한 방식으로 ILB ASE에서 앱을 만듭니다.

1. Azure Portal에서 **리소스 만들기** > **웹** > **웹앱**을 차례로 선택합니다.

1. 앱의 이름을 입력합니다.

1. 구독을 선택합니다.

1. 리소스 그룹을 선택하거나 만듭니다.

1. 게시, 런타임 스택 및 운영 체제를 선택합니다.

1. 위치가 기존 ILB ASE인 위치를 선택합니다.  또한 격리 계층 App Service 계획을 선택하여 앱을 만드는 동안 새 ASE를 만들 수도 있습니다. 새 ASE를 만들려면 ASE를 만들려는 지역을 선택합니다.

1. App Service 계획을 선택하거나 만듭니다. 

1. **검토 및 만들기**를 선택한 다음, 준비되면 **만들기**를 선택합니다.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>웹 작업, 함수 및 ILB ASE 

함수 및 웹 작업 모두 ILB ASE에서 지원되지만 포털에서 작동되려면 네트워크를 통해 SCM 사이트에 액세스할 수 있어야 합니다.  즉, 가상 네트워크에 있거나 가상 네트워크에 연결된 호스트에 브라우저가 있어야 합니다. ILB ASE의 도메인 이름이 *appserviceenvironment.net*으로 끝나지 않으면 경우 scm 사이트에서 사용되는 HTTPS 인증서를 브라우저에서 신뢰할 수 있도록 해야 합니다.

## <a name="dns-configuration"></a>DNS 구성 

외부 VIP를 사용하는 경우 DNS가 Azure에서 관리됩니다. ASE에서 만든 모든 앱은 공용 DNS에 해당하는 Azure DNS에 자동으로 추가됩니다. ILB ASE에서 자체 DNS를 관리해야 합니다. ILB ASE에서 사용되는 도메인 접미사는 ASE 이름에 따라 달라집니다. 도메인 접미사는 *&lt;ASE 이름&gt;.appserviceenvironment.net*입니다. ILB의 IP 주소는 포털의 **IP 주소** 아래에 있습니다. 

DNS를 구성하려면 다음을 수행합니다.

- *&lt;ASE 이름&gt;.appserviceenvironment.net* 영역 만들기
- 해당 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기
- 해당 영역에 @로 ILB IP 주소를 가리키는 A 레코드 만들기
- *&lt;ASE 이름&gt;.appserviceenvironment.net*에 scm이라는 영역 만들기
- scm 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기

## <a name="publish-with-an-ilb-ase"></a>ILB ASE로 게시

만든 모든 앱에는 두 개의 엔드포인트가 있습니다. ILB ASE에는 *&lt;앱 이름&gt;.&lt;ILB ASE 도메인&gt;* 및 *&lt;앱 이름&gt;.scm.&lt;ILB ASE 도메인&gt;* 이 있습니다. 

SCM 사이트 이름은 Azure Portal 내에서 **고급 포털**이라고 하는 Kudu 콘솔로 이동합니다. Kudu 콘솔을 통해 환경 변수를 확인하고, 디스크를 탐색하고, 콘솔을 사용할 수 있습니다. 자세한 내용은 [Azure App Service의 Kudu 콘솔][Kudu]을 참조하세요. 

GitHub 및 Azure DevOps와 같은 인터넷 기반 CI 시스템은 빌드 에이전트가 인터넷에 액세스할 수 있고 ILB ASE와 동일한 네트워크에 있는 경우 ILB ASE와 함께 작동합니다. 따라서 Azure DevOps의 경우 빌드 에이전트가 ILB ASE와 동일한 VNET에서 만들어지면(다른 서브넷은 문제 없음) Azure DevOps git에서 코드를 끌어오고 ILB ASE에 배포할 수 있습니다. 사용자 고유의 빌드 에이전트를 만들지 않으려면 Dropbox 등의 끌어오기 모델을 사용하는 CI 시스템을 사용해야 합니다.

ILB ASE의 앱에 대한 게시 엔드포인트에서는 ILB ASE가 만들어진 도메인을 사용합니다. 이 도메인은 앱의 게시 프로필과 앱의 포털 블레이드(**개요** > **Essentials** 및 **속성**)에서 표시됩니다. *&lt;ASE 이름&gt;.appserviceenvironment.net* 도메인 접미사가 있는 ILB ASE 및 *mytest*라는 앱이 있는 경우 FTP에는 *mytest.&lt;ASE 이름&gt;.appserviceenvironment.net*을 사용하고, 웹 배포에는 *mytest.scm.contoso.net*을 사용합니다.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>WAF 디바이스를 사용하여 ILB ASE 구성 ##

원하는 앱만 인터넷에 공개하고 나머지 앱은 VNet에서만 액세스할 수 있도록 WAF(웹 애플리케이션 방화벽) 디바이스를 ILB ASE와 결합할 수 있습니다. 이렇게 하면 다른 무엇보다도 안전한 다중 계층 애플리케이션을 빌드할 수 있습니다.

WAF 디바이스를 사용하여 ILB ASE를 구성하는 방법에 대한 자세한 내용은 [App Service Environment를 사용하여 웹 애플리케이션 방화벽 구성][ASEWAF]을 참조하세요. 이 문서에서는 ASE를 사용하여 Barracuda 가상 어플라이언스를 사용하는 방법을 보여줍니다. 다른 방법은 Azure Application Gateway를 사용하는 것입니다. Application Gateway는 이후에 배치할 모든 애플리케이션을 보호하기 위해 OWASP 핵심 규칙을 사용합니다. Application Gateway에 대한 자세한 내용은 [Azure 웹 애플리케이션 방화벽 소개][AppGW]를 참조하세요.

## <a name="ilb-ases-made-before-may-2019"></a>2019년 5월 이전에 만든 ILB ASE

2019년 5월 이전에 만든 ILB ASE를 사용하기 위해서는 ASE를 만드는 중에 도메인 접미사를 설정해야 했습니다. 해당 도메인 접미사를 기반으로 하는 기본 인증서도 업로드해야 했습니다. 또한 이전 ILB ASE를 사용하면 해당 ILB ASE의 앱을 사용하여 Kudu 콘솔에 대한 Single Sign-On을 수행할 수 없습니다. 이전 ILB ASE에 대한 DNS를 구성하는 경우 와일드카드 A 레코드를 도메인 접미사와 일치하는 영역에 설정해야 합니다. 

## <a name="get-started"></a>시작하기 ##

* ASE를 시작하려면 [App Service Environment 소개][Intro]를 참조하세요. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux