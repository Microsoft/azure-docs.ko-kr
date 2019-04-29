---
title: App Service environment 사용 - Azure
description: Azure App Service Environment에서 앱을 작성, 게시 및 확장하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d536e9d14edfa17e890480c07951eccb70e9eb9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228342"
---
# <a name="use-an-app-service-environment"></a>App Service Environment 사용 #

## <a name="overview"></a>개요 ##

Azure App Service Environment는 Azure App Service를 고객의 Azure Virtual Network에 있는 서브넷에 배포한 것입니다. ASE는 다음으로 구성됩니다.

- **프런트 엔드**: 프런트 엔드는 ASE(App Service Environment)에서 HTTP/HTTPS가 종료되는 위치입니다.
- **작업자**: 작업자는 앱을 호스트하는 리소스입니다.
- **데이터베이스**: 데이터베이스에는 환경을 정의하는 정보가 들어 있습니다.
- **저장소**: 스토리지는 고객이 게시한 앱을 호스트하는 데 사용됩니다.

> [!NOTE]
> App Service Environment에는 ASEv1 및 ASEv2라는 두 가지 버전이 있습니다. ASEv1에서는 사용하려는 리소스를 관리해야 합니다. ASEv1을 구성하고 관리하는 방법을 알아보려면 [App Service Environment v1 구성][ConfigureASEv1]을 참조하세요. 이 문서의 나머지 부분에서는 ASEv2에 대해 중점적으로 설명합니다.
>
>

앱 액세스를 위한 외부 또는 내부 VIP로 ASE(ASEv1 및 ASEv2)를 배포할 수 있습니다. 외부 VIP를 사용하는 배포를 대개 외부 ASE라고 합니다. 내부 버전은 내부 ILB(부하 분산 장치)를 사용하므로 ILB ASE라고 합니다. ILB ASE에 대해 자세히 알아보려면 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기 ##

ASE에서 앱을 만들려는 경우 일반적으로 앱을 만들 때 사용하는 것과 같은 프로세스를 사용합니다. 그러나 두 프로세스 간에는 몇 가지 세부적인 차이점이 있습니다. 새 App Service 계획을 만들 때는 다음을 수행합니다.

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만들어진 모든 App Service 계획은 격리 가격 책정 계층에 있어야 합니다.

ASE가 없는 경우 [App Service Environment 만들기][MakeExternalASE]의 지침에 따라 만들 수 있습니다.

ASE에서 앱을 만들려면

1. **리소스 만들기** > **웹 + 모바일** > **웹앱**을 선택합니다.

2. 앱의 이름을 입력합니다. ASE에서 이미 App Service 계획을 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영됩니다.

    ![앱 이름 선택][1]

1. 구독을 선택합니다.

1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용**을 선택하고 드롭다운 목록에서 이름을 선택합니다.

1. OS를 선택합니다. 

    * ASE에서 Linux 앱을 호스트하는 것은 새로운 미리 보기 기능이므로, 현재 프로덕션 워크로드를 실행 중인 ASE에는 Linux 앱을 추가하지 않는 것이 좋습니다. 
    * ASE에 Linux 앱을 추가하게 되면 ASE도 미리 보기 모드가 됩니다. 

1. ASE에서 기존 App Service 계획을 선택하거나 다음 단계를 통해 새 App Service 계획을 만듭니다.

    a. **새로 만들기**를 선택합니다.

    b. App Service 계획의 이름을 입력합니다.

    다. **위치** 드롭다운 목록에서 해당 ASE를 선택합니다. ASE의 Linux 앱 호스트는 현재 **미국 서부, 미국 동부, 유럽 서부, 유럽 북부, 오스트레일리아 동부, 동남 아시아**의 6개 지역에서만 가능합니다. 

    d. **격리** 가격 책정 계층을 선택합니다. **선택**을 선택합니다.

    e. **확인**을 선택합니다.
    
    ![격리 가격 책정 계층][2]

    > [!NOTE]
    > Linux 앱과 Windows 앱이 동일한 App Service 계획에는 있을 수 없지만 동일한 App Service Environment에는 있을 수 있습니다. 
    >

2. **만들기**를 선택합니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식 ##

모든 App Service 앱은 App Service 계획에서 실행됩니다. 컨테이너 모델 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱을 확장하면 App Service 계획이 확장되므로 같은 계획 내에 있는 모든 앱이 확장됩니다.

ASEv2에서는 App Service 계획을 확장하면 필요한 인프라가 자동으로 추가됩니다. 인프라가 추가되는 동안 확장 작업의 시간이 지연됩니다. 반면 ASEv1에서는 App Service 계획을 만들거나 규모 확장하려면 필요한 인프라를 추가해야 합니다. 

다중 테넌트 App Service에서는 대개 확장이 즉시 수행됩니다. 확장을 지원하는 리소스 풀을 즉시 사용할 수 있기 때문입니다. ASE에는 이러한 버퍼가 없으며 리소스는 필요 시에 할당됩니다.

ASE는 최대 100개의 인스턴스를 포함하도록 확장할 수 있습니다. 이러한 100개의 인스턴스는 모두 하나의 App Service 계획에 있거나 여러 App Service 계획에 분산될 수 있습니다.

## <a name="ip-addresses"></a>IP 주소 ##

App Service에는 앱에 전용 IP 주소를 할당하는 기능이 있습니다. [Azure App Service에 기존 사용자 지정 SSL 인증서 바인딩][ConfigureSSL]에 설명된 대로 IP 기반 SSL을 구성하고 나면 이 기능을 사용할 수 있습니다. 그러나 ASE에는 중요한 예외 사항이 있습니다. 즉, ILB ASE에서 IP 기반 SSL용으로 사용할 IP 주소를 더 추가할 수는 없습니다.

ASEv1에서는 IP 주소를 사용하기 전에 IP 주소를 리소스로 할당해야 합니다. ASEv2에서는 다중 테넌트 App Service에서와 마찬가지로 앱에서 IP 주소를 사용합니다. ASEv2에는 항상 여분의 주소가 하나 있습니다(최대 IP 주소 30개). IP 주소를 사용할 때마다 다른 주소가 추가되므로 주소 하나를 항상 즉시 사용할 수 있습니다. 다른 IP 주소를 할당할 때는 시간이 지연되므로, IP 주소를 연속해서 빠르게 추가할 수는 없습니다.

## <a name="front-end-scaling"></a>프런트 엔드 확장 ##

ASEv2에서 App Service 계획을 규모 확장할 때는 이러한 계획을 지원하도록 작업자가 자동으로 추가됩니다. 모든 ASE는 프런트 엔드 2개를 포함하는 상태로 작성됩니다. 또한 App Service 계획의 15개 인스턴스마다 프런트 엔드가 하나씩 생성되는 속도로 프런트 엔드가 자동으로 규모 확장됩니다. 예를 들어 인스턴스가 15개이면 프런트 엔드는 세 개입니다. 인스턴스 30개로 확장하면 프런트 엔드가 네 개 있는 식입니다.

대부분의 시나리오에서는 이 정도의 프런트 엔드만으로도 충분합니다. 그러나 더 빠른 속도로 규모를 확장할 수도 있습니다. 인스턴스 5개마다 프런트 엔드가 하나씩 생성되도록 비율을 더 낮게 변경할 수 있습니다. 비율 변경 시에는 요금이 발생합니다. 자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요.

프런트 엔드 리소스는 ASE의 HTTP/HTTPS 엔드포인트입니다. 기본 프런트 엔드 구성에서는 프런트 엔드당 메모리 사용량이 일관되게 약 60%입니다. 고객 작업은 프런트 엔드에서 실행되지 않습니다. 확장과 관련한 프런트 엔드의 주요 요인은 CPU입니다. CPU는 주로 HTTPS 트래픽에 의해 결정됩니다.

## <a name="app-access"></a>앱 액세스 ##

외부 ASE에서는 앱을 만들 때 사용되는 도메인이 다중 테넌트 App Service와 다릅니다. 이 도메인에는 ASE의 이름이 포함됩니다. 외부 ASE를 만드는 방법에 대한 자세한 내용은 [App Service Environment 만들기][MakeExternalASE]를 참조하세요. 외부 ASE의 도메인 이름은 *.&lt;asename&gt;.p.azurewebsites.net*과 같은 형식입니다. 예를 들어 ASE 이름이 _external-ase_이고 해당 ASE에서 _contoso_라는 앱을 호스트하는 경우 다음과 같은 URL에서 앱에 액세스하게 됩니다.

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL contoso.scm.external-ase.p.azurewebsites.net은 Kudu 콘솔에 액세스하거나 웹 배포를 사용하여 앱을 게시하는 데 사용됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

ILB ASE에서는 배포 시간에 도메인을 결정합니다. ILB ASE를 만드는 방법에 대한 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요. 도메인 이름 _ilb-ase.info_를 지정하면 해당 ASE의 앱은 앱을 만드는 동안 해당 도메인을 사용합니다. 이름이 _contoso_인 앱의 경우 URL은 다음과 같습니다.

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>게시 ##

다중 테넌트 App Service와 마찬가지로 ASE에서는 다음을 사용하여 게시할 수 있습니다.

- 웹 배포
- FTP
- 연속 통합
- Kudu 콘솔에서 끌어서 놓기
- IDE(예: Visual Studio, Eclipse 또는 IntelliJ IDEA)

외부 ASE에서는 이러한 게시 옵션이 모두 동일하게 동작합니다. 자세한 내용은 [Azure App Service의 배포][AppDeploy]를 참조하세요. 

게시에서 중요한 차이점은 ILB ASE와 관련된 내용입니다. ILB ASE에서 게시 엔드포인트는 모두 ILB를 통해서만 사용할 수 있습니다. ILB는 Virtual Network의 ASE 서브넷에 있는 개인 IP에 있습니다. ILB에 대한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다. [ILB ASE 만들기 및 사용][MakeILBASE]에 설명된 것처럼 시스템에서 앱에 대한 DNS를 구성해야 합니다. 여기에 SCM 엔드포인트가 포함됩니다. SCM 끝점이 올바르게 정의되어 있지 않으면 게시할 수 없습니다. 또한 직접 ILB에 게시하려면 IDE에 ILB에 대한 네트워크 액세스 권한이 있어야 합니다.

기본적으로 게시 끝점이 인터넷에 액세스할 수 없기 때문에 ILB ase에서는 GitHub 및 Azure DevOps와 같은 인터넷 기반 CI 시스템이 작동 하지 않습니다. Azure DevOps에 대 한 ILB 도달할 수 있는 내부 네트워크에서 자체 호스팅된 해제 에이전트를 설치 하 여이 문제를 해결 작업할 수 있습니다. 또는 Dropbox와 같은 끌어오기 모델을 사용 하는 CI 시스템을 사용할 수 있습니다.

ILB ASE의 앱에 대한 게시 엔드포인트에서는 ILB ASE가 만들어진 도메인을 사용합니다. 해당 도메인은 앱의 게시 프로필과 앱의 포털 블레이드(**개요** > **필수** 및 **속성**)에서 확인할 수 있습니다. 

## <a name="pricing"></a>가격 ##

가격 책정 SKU **격리**는 ASEv2에서만 사용하기 위해 만들어졌습니다. ASEv2에서 호스트되는 모든 App Service 계획에는 격리 가격 책정 SKU가 적용됩니다. 격리된 App Service 계획 요금은 지역마다 다를 수 있습니다. 

App Service 계획에 대한 가격 외에 ASE 자체에 대한 고정 요금이 있습니다. 고정 요금은 ASE 크기에 따라 변경되지 않으며, 15개의 모든 App Service 계획 인스턴스에 대해 1개의 추가 프런트 엔드에 대한 기본 크기 조정 요금으로 ASE 인프라 요금을 지불하게 됩니다.  

15개의 모든 App Service 계획 인스턴스에 대해 프런트 엔드 1개의 기본 확장 비율이 충분히 빠르지 않을 경우 프런트 엔드가 추가되는 속도 또는 프런트 엔드의 크기를 조정할 수 있습니다.  이 비율 또는 크기를 조정할 경우 기본적으로 추가되지 않는 프런트 엔드 코어에 대해 비용을 지불하게 됩니다.  

예를 들어 확장 비율을 10으로 조정하면 App Service 계획에서 인스턴스 10개마다 프런트 엔드 하나가 추가됩니다. 고정 비용을 지불하는 경우에는 인스턴스 15개마다 프런트 엔드 하나가 추가되는 확장 비율이 사용됩니다. 확장 비율이 10인 경우에는 App Service 계획 인스턴스 10개에 대해 추가되는 세 번째 프런트 엔드의 요금을 지불하게 됩니다. 인스턴스 수가 15개가 될 때는 요금을 지불하지 않아도 됩니다. 이 경우에는 프런트 엔드가 자동으로 추가되기 때문입니다.

프런트 엔드의 크기를 2개 코어로 조절했으나 해당 비율을 조절하지 않은 경우 추가 코어에 대한 요금을 지불하게 됩니다.  프런트 엔드 2개를 사용하여 ASE가 생성됩니다. 따라서 자동 크기 조정 임계값보다 낮더라도 해당 크기를 2 코어 프런트 엔드로 늘리면 2개의 추가 코어에 대해 요금을 지불하게 됩니다.

자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요.

## <a name="delete-an-ase"></a>ASE 삭제 ##

ASE를 삭제하려면 다음을 수행합니다. 

1. **App Service Environment** 블레이드 위쪽의 **삭제**를 사용합니다. 

1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제하면 해당 ASE 내의 콘텐츠도 모두 삭제됩니다. 

    ![ASE 삭제][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
