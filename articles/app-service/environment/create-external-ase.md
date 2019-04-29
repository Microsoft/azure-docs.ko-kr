---
title: 외부 App Service Environment 만들기 - Azure
description: 앱 또는 독립 실행형을 만드는 동안 App Service Environment를 만드는 방법 설명
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: eef13c5a4e3757b0eafd77c0915717175c2dbd8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769115"
---
# <a name="create-an-external-app-service-environment"></a>외부 App Service Environment 만들기

Azure App Service Environment는 Azure App Service를 Azure VNet(Virtual Network)의 서브넷에 배포한 것입니다.

> [!NOTE]
> 각 App Service Environment에는 가상 IP (VIP), App Service Environment에 연결할 수는 있습니다.

ASE(App Service Environment)에는 두 가지 배포 방법이 있습니다.

- 외부 ASE라고도 하는 외부 IP 주소의 VIP 사용
- 내부 엔드포인트가 ILB(내부 부하 분산 장치)이기 때문에 ILB ASE라고도 하는 내부 IP 주소의 VIP 사용

이 문서에서는 외부 ASE를 만드는 방법을 보여줍니다. ASE의 개요는 [App Service Environment에 대한 소개][Intro]를 참조하세요. ILB ASE를 만드는 방법에 대한 자세한 내용은 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="before-you-create-your-ase"></a>ASE를 만들기 전에

ASE를 만든 후에는 다음을 변경할 수 없습니다.

- Location
- 구독
- 리소스 그룹
- 사용되는 VNet
- 사용되는 서브넷
- 서브넷 크기

> [!NOTE]
> VNet을 선택하고 서브넷을 지정할 때 향후 성장 및 확장 요구 사항을 수용하기에 충분한지 확인합니다. 주소 256개를 포함할 수 있는 `/24` 크기를 사용하는 것이 좋습니다.
>

## <a name="three-ways-to-create-an-ase"></a>ASE를 만드는 세 가지 방법

ASE를 만드는 세 가지 방법은 다음과 같습니다.

- **App Service 계획을 만들 때**. 이 메서드는 한 번에 ASE 및 App Service 계획을 만듭니다.
- **독립 실행형 작업으로**. 이 메서드는 내부에 아무것도 포함되지 않은 ASE인 독립 실행형 ASE를 만듭니다. 이 메서드는 ASE를 만드는 더 고급 수준의 프로세스입니다. ILB를 사용하여 ASE를 만들 때 사용합니다.
- **Azure Resource Manager 템플릿에서**. 이 메서드는 고급 사용자를 위한 것입니다. 자세한 내용은 [템플릿에서 ASE 만들기][MakeASEfromTemplate]를 참조하세요.

외부 ASE에는 공용 VIP가 있습니다. 즉, ASE의 앱에 대한 모든 HTTP/HTTPS 트래픽이 인터넷 액세스가 가능한 IP 주소에 도달합니다. ILB를 사용하는 ASE에는 ASE에서 사용하는 서브넷의 IP 주소가 있습니다. ILB ASE에 호스팅된 앱은 인터넷에 직접 노출되지 않습니다.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>ASE 및 App Service 계획 만들기

App Service 계획은 앱의 컨테이너입니다. App Service에서 앱을 만드는 경우 App Service 계획을 선택하거나 만듭니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다.

App Service 계획을 만들면서 ASE를 만드는 경우 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **웹 + 모바일** > **웹앱**을 선택합니다.

    ![웹앱 만들기][1]

2. 구독을 선택합니다. 앱 및 ASE는 동일한 구독에 생성됩니다.

3. 리소스 그룹을 선택하거나 만듭니다. 리소스 그룹을 사용하여 관련된 Azure 리소스를 하나의 단위로 관리할 수 있습니다. 리소스 그룹은 앱에 대해 역할 기반 Access Control 규칙을 설정하려는 경우 유용합니다. 자세한 내용은 [Azure Resource Manager 개요][ARMOverview]를 참조하세요.

4. OS(Windows, Linux 또는 Docker)를 선택합니다. 

5. App Service 계획을 선택한 다음 **새로 만들기**를 선택합니다. Linux 웹앱 및 Windows 웹앱은 동일한 App Service 계획에는 있을 수 없지만 동일한 App Service 환경에는 있을 수 있습니다. 

    ![새 App Service 계획][2]

6. **위치** 드롭다운 목록에서 ASE를 만들려는 영역을 선택합니다. 기존 ASE를 선택하는 경우 새 ASE가 생성되지 않습니다. App Service 계획이 선택한 ASE에 만들어집니다. 

7. **가격 책정 계층**을 선택하고, **격리** 가격 책정 SKU 중 하나를 선택합니다. **격리** SKU 카드를 선택하고 ASE가 아닌 위치를 선택하면 새 ASE가 해당 위치에 만들어집니다. ASE를 만드는 프로세스를 시작하려면 **선택**을 선택합니다. **격리** SKU는 ASE에서만 사용할 수 있습니다. 또한 **격리**가 아닌 ASE의 다른 가격 책정 SKU를 사용할 수 없습니다. 

    ![가격 책정 계층 선택][3]

8. ASE의 이름을 입력합니다. 이 이름은 앱에 주소를 지정할 수 있는 이름에 사용됩니다. ASE의 이름이 _appsvcenvdemo_인 경우 도메인 이름은 *.appsvcenvdemo.p.azurewebsites.net*입니다. *mytestapp*이라는 앱을 만든 경우 mytestapp.appsvcenvdemo.p.azurewebsites.net으로 주소를 지정할 수 있습니다. 이름에 공백을 사용할 수 없습니다. 대문자를 사용한 경우 도메인 이름은 해당 이름의 소문자 버전이 됩니다.

    ![새 App Service 계획 이름][4]

9. Azure 가상 네트워킹 세부 정보를 지정합니다. **새로 만들기** 또는 **기존 항목 선택** 중 하나를 선택합니다. 선택한 영역에 VNet이 있는 경우에만 기존 VNet을 선택할 수 있는 옵션을 사용할 수 있습니다. **새로 만들기**를 선택한 경우 VNet에 대한 이름을 입력합니다. 해당 이름으로 새로운 Resource Manager VNet이 만들어집니다. 선택한 지역의 주소 공간 `192.168.250.0/23`을 사용합니다. **기존 항목 선택**을 선택하는 경우 다음 항목이 필요합니다.

    a. 여러 개의 항목이 있는 경우 VNet 주소 블록을 선택합니다.

    b. 새 서브넷 이름을 입력합니다.

    다. 서브넷의 크기를 선택합니다. *ASE의 향후 증가에 맞게 충분히 큰 크기를 설정해야 합니다.* 권장되는 크기는 `/25`입니다. 여기에는 128개의 주소가 있고 최대 크기의 ASE를 다룰 수 있습니다. 예를 들어 16개의 주소만을 사용할 수 있기 때문에 `/28`은 권장되지 않습니다. 인프라는 적어도 7개의 주소를 사용하고 Azure Networking은 다른 5개를 사용합니다. `/28` 서브넷에서는 외부 ASE에 대해 최대 4개의 App Service 계획 인스턴스를, ILB ASE에 대해서는 3개의 App Service 계획 인스턴스만 확장할 수 있습니다.

    d. 서브넷 IP 범위를 선택합니다.

10. **만들기**를 선택하여 ASE를 만듭니다. App Service 계획 및 앱이 만들어집니다. ASE, App Service 계획 및 앱은 동일한 구독 및 동일한 리소스 그룹에 위치합니다. ASE에 별도 리소스 그룹이 필요하거나 ILB ASE가 필요한 경우 단계에 따라 단독으로 ASE를 만듭니다.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>사용자 지정 Docker 이미지를 함께 사용하여 ASE 및 Linux 웹앱 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **웹 + 모바일** > **컨테이너용 웹앱** 

    ![웹앱 만들기][7]

1. 구독을 선택합니다. 앱 및 ASE는 동일한 구독에 생성됩니다.

1. 리소스 그룹을 선택하거나 만듭니다. 리소스 그룹을 사용하여 관련된 Azure 리소스를 하나의 단위로 관리할 수 있습니다. 리소스 그룹은 앱에 대해 역할 기반 Access Control 규칙을 설정하려는 경우 유용합니다. 자세한 내용은 [Azure Resource Manager 개요][ARMOverview]를 참조하세요.

1. App Service 계획을 선택한 다음 **새로 만들기**를 선택합니다. Linux 웹앱 및 Windows 웹앱은 동일한 App Service 계획에는 있을 수 없지만 동일한 App Service 환경에는 있을 수 있습니다. 

    ![새 App Service 계획][8]

1. **위치** 드롭다운 목록에서 ASE를 만들려는 영역을 선택합니다. 기존 ASE를 선택하는 경우 새 ASE가 생성되지 않습니다. App Service 계획이 선택한 ASE에 만들어집니다. 

1. **가격 책정 계층**을 선택하고, **격리** 가격 책정 SKU 중 하나를 선택합니다. **격리** SKU 카드를 선택하고 ASE가 아닌 위치를 선택하면 새 ASE가 해당 위치에 만들어집니다. ASE를 만드는 프로세스를 시작하려면 **선택**을 선택합니다. **격리** SKU는 ASE에서만 사용할 수 있습니다. 또한 **격리**가 아닌 ASE의 다른 가격 책정 SKU를 사용할 수 없습니다. 

    ![가격 책정 계층 선택][3]

1. ASE의 이름을 입력합니다. 이 이름은 앱에 주소를 지정할 수 있는 이름에 사용됩니다. ASE의 이름이 _appsvcenvdemo_인 경우 도메인 이름은 *.appsvcenvdemo.p.azurewebsites.net*입니다. *mytestapp*이라는 앱을 만든 경우 mytestapp.appsvcenvdemo.p.azurewebsites.net으로 주소를 지정할 수 있습니다. 이름에 공백을 사용할 수 없습니다. 대문자를 사용한 경우 도메인 이름은 해당 이름의 소문자 버전이 됩니다.

    ![새 App Service 계획 이름][4]

1. Azure 가상 네트워킹 세부 정보를 지정합니다. **새로 만들기** 또는 **기존 항목 선택** 중 하나를 선택합니다. 선택한 영역에 VNet이 있는 경우에만 기존 VNet을 선택할 수 있는 옵션을 사용할 수 있습니다. **새로 만들기**를 선택한 경우 VNet에 대한 이름을 입력합니다. 해당 이름으로 새로운 Resource Manager VNet이 만들어집니다. 선택한 지역의 주소 공간 `192.168.250.0/23`을 사용합니다. **기존 항목 선택**을 선택하는 경우 다음 항목이 필요합니다.

    a. 여러 개의 항목이 있는 경우 VNet 주소 블록을 선택합니다.

    b. 새 서브넷 이름을 입력합니다.

    다. 서브넷의 크기를 선택합니다. *ASE의 향후 증가에 맞게 충분히 큰 크기를 설정해야 합니다.* 권장되는 크기는 `/25`입니다. 여기에는 128개의 주소가 있고 최대 크기의 ASE를 다룰 수 있습니다. 예를 들어 16개의 주소만을 사용할 수 있기 때문에 `/28`은 권장되지 않습니다. 인프라는 적어도 7개의 주소를 사용하고 Azure Networking은 다른 5개를 사용합니다. `/28` 서브넷에서는 외부 ASE에 대해 최대 4개의 App Service 계획 인스턴스를, ILB ASE에 대해서는 3개의 App Service 계획 인스턴스만 확장할 수 있습니다.

    d. 서브넷 IP 범위를 선택합니다.

1.  “컨테이너 구성”을 선택합니다.
    * 사용자 지정 이미지 이름을 입력합니다(Azure Container Registry, Docker 허브 및 개인 레지스트리를 사용할 수 있음). 사용자 고유의 사용자 지정 컨테이너를 사용하지 않으려는 경우, 코드를 가져오고, 위 지침에 따라 Linux의 App Service로 기본 제공 이미지를 사용할 수 있습니다. 

    ![컨테이너 구성][9]

1. **만들기**를 선택하여 ASE를 만듭니다. App Service 계획 및 앱이 만들어집니다. ASE, App Service 계획 및 앱은 동일한 구독 및 동일한 리소스 그룹에 위치합니다. ASE에 별도 리소스 그룹이 필요하거나 ILB ASE가 필요한 경우 단계에 따라 단독으로 ASE를 만듭니다.


## <a name="create-an-ase-by-itself"></a>단독으로 ASE 만들기

ASE 독립 실행형을 만드는 경우 내부에는 아무것도 없습니다. 빈 ASE도 여전히 인프라에 대한 월별 요금이 청구됩니다. ILB를 사용하여 ASE를 만들거나 고유한 리소스 그룹에서ASE를 만들려면 다음 단계를 수행합니다. ASE를 만든 후에 기본 프로세스를 사용하여 앱을 만들 수 있습니다. 위치로 새 ASE를 선택합니다.

1. Azure Marketplace에서 **App Service Environment**를 검색하거나 **리소스 만들기** > **웹 모바일** > **App Service Environment**를 선택합니다. 

1. ASE의 이름을 입력합니다. 이 이름은 ASE에서 만든 앱에 사용됩니다. 이름이 *mynewdemoase*인 경우 하위 도메인 이름은 *.mynewdemoase.p.azurewebsites.net*입니다. *mytestapp*이라는 앱을 만든 경우 mytestapp.mynewdemoase.p.azurewebsites.net으로 주소를 지정할 수 있습니다. 이름에 공백을 사용할 수 없습니다. 대문자를 사용한 경우 도메인 이름은 해당 이름의 소문자 버전이 됩니다. ILB를 사용하는 경우 ASE 이름이 하위 도메인에서 사용되지 않는 대신 ASE를 만드는 동안에 명시적으로 지정됩니다.

    ![ASE 이름 지정][5]

1. 구독을 선택합니다. 또한 이 구독은 ASE의 모든 앱이 사용하는 것입니다. 다른 구독에 있는 VNet에 ASE를 배치할 수 없습니다.

1. 새 리소스 그룹을 선택하거나 지정합니다. ASE에 사용되는 리소스 그룹은 VNet에 사용되는 것과 동일해야 합니다. 기존 VNet을 선택하는 경우 ASE에 대한 리소스 그룹을 선택하는 작업은 VNet의 선택을 반영하도록 업데이트됩니다. *Resource Manager 템플릿을 사용하는 경우 VNet 리소스 그룹과 다른 리소스 그룹으로 ASE를 만들 수 있습니다.* 템플릿에서 ASE를 만들려면 [템플릿에서 App Service Environment 만들기][MakeASEfromTemplate]를 참조하세요.

    ![리소스 그룹 선택][6]

1. VNet 및 위치를 선택합니다. 새 VNet을 만들거나 기존 VNet을 선택할 수 있습니다. 

    * 새 VNet을 선택하면 이름 및 위치를 지정할 수 있습니다. 
    
    * 새 VNet의 주소 범위는 192.168.250.0/23이고 default라는 서브넷이 있습니다. 서브넷은 192.168.250.0/24로 정의되어 있습니다. Resource Manager VNet만 선택할 수 있습니다. **VIP 형식**을 선택하면 ASE가 인터넷(외부)에서 직접 액세스할 수 있는지 또는 ILB를 사용하는지를 결정합니다. 이러한 옵션에 대한 자세한 내용은 [App Service Environment에서 내부 부하 분산 장치 만들기 및 사용][MakeILBASE]을 참조하세요. 

      * **VIP 형식**에 **외부**를 선택하면 시스템이 IP 기반 SSL 목적으로 만들 수 있는 외부 IP 주소의 개수를 선택할 수 있습니다. 
    
      * **VIP 형식**에 **내부**를 선택하면 ASE가 사용하는 도메인을 지정해야 합니다. 공개 또는 개인 주소 범위를 사용하는 VNet으로 ASE를 배포할 수 있습니다. 공용 주소 범위를 가진 VNet을 사용하려면 사전에 VNet을 만들어야 합니다. 
    
    * 기존 VNet을 선택한 경우 새 서브넷은 ASE를 만들 때 만들어집니다. *포털에서 미리 생성된 서브넷을 사용할 수 없습니다. Resource Manager 템플릿을 사용하는 경우 기존 서브넷으로 ASE를 만들 수 있습니다.* 템플릿에서 ASE를 만들려면 [템플릿에서 App Service Environment 만들기][MakeASEfromTemplate]를 참조하세요.

## <a name="app-service-environment-v1"></a>App Service 환경 v1

여전히 ASEv1(첫 번째 버전의 App Service Environment)의 인스턴스를 만들 수 있습니다. 해당 프로세스를 시작하려면 Marketplace에서 **App Service Environment v1**을 검색합니다. 독립 실행형 ASE를 만드는 동일한 방식으로 ASE를 만듭니다. 완료되면 ASEv1에 두 개의 프런트 엔드 및 두 명의 작업자가 있습니다. ASEv1을 사용하면 프런트 엔드와 작업자를 관리해야 합니다. 이는 App Service 계획을 만들 때 자동으로 추가되지 않습니다. 프런트 엔드는 HTTP/HTTPS 엔드포인트로 작동하여 작업자로 트래픽을 전송합니다. 작업자는 사용자 앱을 호스트하는 역할입니다. ASE를 만든 후 프런트 엔드 및 작업자의 수량을 조정할 수 있습니다. 

ASEv1에 대해 자세히 알아보려면 [App Service Environment v1 소개][ASEv1Intro]를 참조하세요. ASEv1의 크기 조정, 관리 및 모니터링에 대한 자세한 내용은 [App Service Environment를 구성하는 방법][ConfigureASEv1]을 참조하세요.

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
