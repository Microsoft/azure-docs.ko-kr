---
title: App Service Environment v1을 만드는 방법 - Azure
description: App Service Environment v1에 대한 만들기 흐름 설명
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 9bc796c4d0d449f72dc3234bc2825554eafaf77f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128891"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>App Service Environment v1을 만드는 방법 

> [!NOTE]
> 이 문서는 ASE(App Service Environment) v1에 관한 내용입니다. 사용하기가 더 쉽고 더 강력한 인프라에서 실행되는 최신 버전의 App Service Environment가 있습니다. 새 버전에 대한 자세한 내용은 [App Service Environment 소개](intro.md)를 참조하세요.
> 

### <a name="overview"></a>개요
ASE(App Service Environment)는 다중 테넌트 증명(stamp)에서 사용할 수 없는 향상된 구성 기능을 제공하는 Azure App Service의 프리미엄 서비스 옵션입니다. ASE 기능은 기본적으로 고객의 가상 네트워크에 Azure App Service를 배포합니다. App Service 환경에서 제공되는 기능에 대한 자세한 내용은 [App Service 환경 정의][WhatisASE] 설명서를 참조하세요.

### <a name="before-you-create-your-ase"></a>ASE를 만들기 전에
변경할 수 없다는 점을 알고 있어야 합니다. 만든 후에 ASE를 변경할 수 없는 항목은 다음과 같습니다.

* Location
* 구독
* 리소스 그룹
* 사용되는 VNet
* 사용되는 서브넷 
* 서브넷 크기

VNet을 선택하고 서브넷을 지정하는 경우 향후 성장을 수용하기에 충분한지 확인합니다. 

### <a name="creating-an-app-service-environment-v1"></a>App Service Environment v1 만들기
App Service Environment v1을 만들려면 Azure Marketplace에서 ***App Service Environment v1***을 검색하거나 **리소스 만들기** -> **웹 + 모바일** -> **App Service Environment**로 차례로 이동하여 검색할 수 있습니다. ASE v1을 만들려면 다음을 수행합니다.

1. ASE의 이름을 제공합니다. 지정한 ASE 이름은 ASE에서 만드는 앱에 사용됩니다. ASE 이름이 appsvcenvdemo인 경우 하위 도메인 이름은 *appsvcenvdemo.p.azurewebsites.net*입니다. 따라서 *mytestapp*이라는 앱을 만든 경우 해당 주소는 *mytestapp.appsvcenvdemo.p.azurewebsites.net*으로 지정될 수 있습니다. ASE의 이름에 공백을 사용할 수 없습니다. 이름에 대문자를 사용한 경우 전체 도메인 이름은 해당 이름의 소문자 버전이 됩니다. ILB를 사용하는 경우 ASE 이름이 하위 도메인에서 사용되지 않는 대신 ASE를 만드는 동안에 명시적으로 지정됩니다.
   
    ![][1]
2. 구독을 선택합니다. 사용 중인 ASE에 사용하는 구독은 해당 ASE에서 만드는 모든 앱에 적용됩니다. 다른 구독에 있는 VNet에 ASE를 배치할 수 없습니다.
3. 새 리소스 그룹을 선택하거나 지정합니다. ASE에 사용되는 리소스 그룹은 VNet에 사용되는 것과 동일해야 합니다. 기존 VNet을 선택하는 경우 ASE에 대한 리소스 그룹을 선택하는 작업은 VNet의 선택을 반영하도록 업데이트됩니다.
   
    ![][2]
4. Virtual Network 및 위치 선택을 확인합니다. 새 VNet을 만들거나 기존 VNet을 선택할 수 있습니다. 새 VNet을 선택하면 이름 및 위치를 지정할 수 있습니다. 새 VNet의 주소 범위는 192.168.250.0/23이고 192.168.250.0/24로 정의된 **default**이라는 서브넷이 있습니다. 또한 기존의 클래식 VNet 또는 Resource Manager VNet을 선택할 수 있습니다. VIP 유형은 ASE가 인터넷(외부)에서 직접 액세스할 수 있는지 또는 ILB(내부 부하 분산 장치)를 사용하는지를 결정합니다. 자세한 내용은 [App Service 환경에서 내부 부하 분산 장치 사용][ILBASE]을 참조하세요. 외부의 VIP 유형을 선택하면 시스템이 IPSSL 목적으로 만들 수 있는 외부 IP 주소의 개수를 선택할 수 있습니다. 내부를 선택하는 경우 ASE에서 사용할 하위 도메인을 지정해야 합니다. 공용 주소 범위 *또는* RFC1918 주소 공간(즉, 개인 주소) *중 하나*를 사용하는 가상 네트워크에 ASE를 배포할 수 있습니다. 공용 주소 범위를 가진 가상 네트워크를 사용하기 위해 사전에 VNet을 만들어야 합니다. 기존 VNet을 선택하면 ASE를 만드는 동안 새 서브넷을 만들어야 합니다. **포털에서 미리 생성된 서브넷을 사용할 수 없습니다. Resource Manager 템플릿을 사용하여 ASE를 만드는 경우 기존 서브넷으로 ASE를 만들 수 있습니다.** 템플릿에서 ASE를 만들려면 [템플릿에서 App Service 환경 만들기][ILBAseTemplate] 및 [템플릿에서 ILB App Service 환경 만들기][ASEfromTemplate]의 정보를 사용합니다.

### <a name="details"></a>세부 정보
ASE는 2개의 프런트 엔드 및 2개의 작업자를 사용하여 생성됩니다. 프런트 엔드는 HTTP/HTTPS 엔드포인트로 작동하여 앱 호스트 역할을 갖는 작업자로 트래픽을 전송합니다. ASE를 만든 후에 수량을 조정할 수 있으며 이러한 리소스 풀에서 자동 크기 조정 규칙을 설정할 수도 있습니다. App Service Environment의 수동 크기 조정, 관리 및 모니터링에 대한 자세한 내용은 다음을 참조하세요. [App Service Environment 구성 방법][ASEConfig] 

ASE에서 사용하는 서브넷에는 하나의 ASE만이 있을 수 있습니다. ASE 이외의 항목에는 서브넷을 사용할 수 없습니다.

### <a name="after-app-service-environment-v1-creation"></a>App Service Environment v1을 만든 이후
ASE를 만든 후 다음을 조정할 수 있습니다.

* 프런트 엔드 수(최소: 2개)
* 작업자 수(최소: 2개)
* IP SSL에 사용 가능한 IP 주소 수
* 프런트 엔드 또는 작업자에서 사용되는 Compute 리소스 크기(프런트 엔드 최소 크기는 P2)

App Service Environment의 수동 크기 조정, 관리 및 모니터링에 대한 자세한 내용은 다음을 참조하세요. [App Service Environment 구성 방법][ASEConfig] 

자동 크기 조정에 대한 자세한 내용은 다음 가이드를 참조하세요. [App Service Environment에 대해 자동 크기 조정을 구성하는 방법][ASEAutoscale]

데이터베이스 및 저장소와 같은 사용자 지정 항목에 사용할 수 없는 추가 종속성이 있습니다. 이러한 종속성은 Azure에 의해 처리되며 시스템과 함께 제공됩니다. 시스템 저장소는 전체 App Service Environment에 대해 최대 500GB를 지원하고 데이터베이스는 시스템 규모의 필요에 따라 Azure에서 조정됩니다.

## <a name="getting-started"></a>시작
App Service Environment v1을 시작하려면 [App Service Environment v1 소개][WhatisASE]를 참조하세요.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
