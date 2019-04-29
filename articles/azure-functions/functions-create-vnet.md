---
title: Azure virtual network를 사용 하 여 Azure Functions 통합
description: 함수는 Azure 가상 네트워크에 연결 하는 방법을 보여 주는 단계별 자습서
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125680"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Azure virtual network를 사용 하 여 함수 앱 통합

이 자습서는 Azure Functions를 사용 하 여 Azure 가상 네트워크의 리소스에 연결할 방법을 보여 줍니다.

이 자습서에서는 WordPress 사이트는 인터넷에서 액세스할 수 없는 가상 네트워크에서 VM에 배포 됩니다. 가상 네트워크와 인터넷에 대 한 액세스를 사용 하 여 함수를 배포 다음 했습니다. 가상 네트워크 내에 배포 된 WordPress 사이트에서 리소스에 액세스 하는 함수를 사용 하겠습니다.

시스템의 작동 방식에 대 한 자세한 내용은 문제 해결 및 고급 구성 참조 [Azure virtual network와 앱 통합](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)합니다. 프리미엄 요금제에서 azure functions 웹 앱과 동일한 호스팅 기능이 없으므로 함수에 적용 하는 모든 기능 및 해당 문서의 제한 사항입니다.

## <a name="topology"></a>토폴로지

 ![가상 네트워크 통합을 위한 UI][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>가상 네트워크 내에서 VM 만들기

를 시작 하려면 가상 네트워크 내에서 WordPress를 실행 하는 미리 구성 된 VM을 만들겠습니다. 

가상 네트워크 내에서 배포할 수 있는 저렴 리소스 중 하나 이기 때문에 VM에서 WordPress를 선택 했습니다. 이 시나리오는 REST Api, App Service Environment 및 기타 Azure 서비스와 같은 가상 네트워크에서 모든 리소스를 사용할 수도 있습니다는 참고 합니다.

1. Azure Portal로 이동합니다.
2. 열어 새 리소스를 추가 합니다 **리소스 만들기** 블레이드입니다.
3. 검색할 "[CentOS의 WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)" 해당 만들기 블레이드를 엽니다. 
4. 에 **기본 사항** 탭에서 다음 정보를 사용 하 여 VM을 구성 합니다.
    1. 자습서의 끝에서 더 쉽게 리소스를 정리하도록 이 VM에 대한 새 리소스 그룹을 만듭니다. 여기에 예를 들어 "함수-VNET-Tutorial"를 사용 하는 것입니다.
    1. 가상 머신에 고유한 이름을 지정합니다. 예를 들어 "VNET-Wordpress"를 사용 하는 것입니다.
    1. 가장 가까운 지역을 선택 합니다.
    1. B1s로 크기를 선택 합니다. (1 개 vCPU, 메모리 1GB).
    1. 관리자 계정에 대해 암호 인증을 선택하고 고유한 사용자 이름과 암호를 입력합니다. 이 자습서에서는 문제를 해결 하려면 필요한 경우가 아니면 VM에 로그인 할 필요가 없습니다.
    
        ![VM을 만들기 위한 기본 사항 탭](./media/functions-create-vnet/create-vm-1.png)

1. 으로 이동 합니다 **네트워킹** 탭 하 고 다음 정보를 입력 합니다.
    1.  새 가상 네트워크를 만듭니다.
    1.  개인 주소 범위 및 주소 범위 내에서 서브넷을 입력 합니다. 서브넷 크기는 App Service 계획에서 사용할 수 있는 VM 수를 결정합니다. IP 주소 및 서브넷 지정을 하는 방법이 [기본 사항을 다루는 문서](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)합니다. IP 주소 및 서브넷 지정 되므로이 시나리오에서 중요 한 몇 가지 기사를 읽고 감지 될 때까지 몇 가지 비디오를 온라인를 시청 하는 것이 좋습니다. 
    
        예를 들어 10.10.1.0/24의 서브넷과 10.10.0.0/16 네트워크를 사용 하도록 선택 하는 것입니다. 오버 프로 비전 하 고 는/16을 사용 하 여 서브넷 10.10.0.0/16 네트워크에서 사용할 수 있는 서브넷을 계산 하 쉽기 때문입니다.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. 다시 합니다 **네트워킹** 탭에서 공용 IP로 **None**합니다. 이 단계는 가상 네트워크만에 대 한 액세스를 사용 하 여 VM을 배포 합니다.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. VM을 만듭니다. 프로세스는 약 5 분 정도 걸립니다.
8. 로 이동 하는 VM을 만든 후 해당 **네트워킹** 탭 하 고 나중에 대 한 개인 IP 주소를 기록해 둡니다. VM은 공용 IP가 없어야 합니다.

    ![14]

이제 WordPress 사이트 가상 네트워크 내에 완전히 배포 해야 합니다. 이 사이트는 공용 인터넷에서 액세스할 수 없습니다.

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 계획에서 함수 앱 만들기

다음 단계 Premium 계획에서 함수 앱을 만드는 것입니다. 프리미엄 계획은 전용된 App Service 계획의 모든 혜택을 사용 하 여 서버 리스 확장을 제공합니다. 함수 앱은 소비 계획을 통해 만든 가상 네트워크 통합을 지원 하지 않습니다.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>함수 앱을 가상 네트워크에 연결

가상 네트워크 내에서 파일을 호스팅하는 WordPress 사이트를 사용 하 여 가상 네트워크에 함수 앱을 이제 연결할 수 있습니다.

1.  이전 단계에서 함수 앱에 대 한 포털에서 선택 **플랫폼 기능**합니다. 선택한 **네트워킹**합니다.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  선택 **구성 하려면 여기를 클릭** 아래에서 **VNet 통합**합니다.

    ![네트워크 기능을 구성 하는 것에 대 한 상태](./media/functions-create-vnet/Networking-1.png)

1. 가상 네트워크 통합 페이지에서 선택 **VNet 추가 (미리 보기)** 합니다.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  함수 및 App Service 계획을 사용 하 여 새 서브넷을 만듭니다. 참고 서브넷 크기를 App Service 계획에 추가할 수 있는 Vm의 총 수를 제한 합니다. 가상 네트워크 기능은 VM에서 다른 서브넷에 있는 중요 하지 않습니다, 가상 네트워크의 서브넷 간에 트래픽을 자동으로 라우팅합니다. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>가상 네트워크의 리소스에 액세스 하는 함수 만들기

함수 앱에서 WordPress 사이트를 사용 하 여 가상 네트워크를 액세스할 수 있습니다. 따라서 함수를 사용 하 여 해당 파일에 액세스 하 고 다시 사용자에 게 제공 하려고 합니다. 예를 들어 사용 하겠습니다 WordPress 사이트 프록시 API로 호출 함수로 설정 하 고 시각화 하는 간단한 이기 때문에. 

가상 네트워크 내에 배포 된 다른 API를 손쉽게 사용할 수 있습니다. 또한 가상 네트워크 내에 배포 된 API에 API를 호출 하는 코드를 사용 하 여 다른 함수를 사용할 수 있습니다. 가상 네트워크 내에 배포 된 SQL Server 인스턴스를 완벽 한 예입니다.

1. 포털에서 이전 단계에서 함수 앱을 엽니다.
1. 선택 하 여 프록시를 만들 **프록시** > **+** 합니다.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. 프록시 이름 및 경로 구성 합니다. 사용 하 여 "/ 심"이 예제이에서는 경로를 합니다.
1. 앞에서 WordPress 사이트의 IP를 입력 하 고 설정 **백 엔드 URL** 를 `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

이제 새 브라우저 탭에 붙여 넣어서에서 직접 백 엔드 URL을 방문 하려고 하면 페이지 시간이 초과 해야 합니다. WordPress 사이트를 가상 네트워크만 및 인터넷이 아닌 연결할 때문입니다. 브라우저에 프록시 URL을 붙여넣으면 (WordPress 사이트에서 가져온) 공장 그림 가상 네트워크 내에서 표시 됩니다. 

함수 앱은 인터넷 및 가상 네트워크에 연결 됩니다. 프록시는 공용 인터넷을 통해 요청을 받은 다음, 단순한 HTTP 프록시로서 이 요청을 가상 네트워크에 전달합니다. 그런 다음, 프록시는 공용 인터넷을 통해 이 응답을 다시 사용자에게 중계해 줍니다. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>다음 단계

프리미엄 계획에서 실행 되는 functions PremiumV2 계획에서 웹 앱으로 동일한 기본 App Service 인프라를 공유 합니다. 웹 앱에 대 한 모든 설명서는 프리미엄 계획 함수에 적용 됩니다.

* [함수에서 네트워킹 옵션에 자세히 알아보기](./functions-networking-options.md)
* [읽기 네트워킹 FAQ 함수](./functions-networking-faq.md)
* [Azure에서 가상 네트워크에 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [추가 네트워킹 기능 및 App Service Environment를 사용 하 여 컨트롤을 사용 하도록 설정](../app-service/environment/intro.md)
* [하이브리드 연결을 사용 하 여 방화벽 변경 없이 개별 온-프레미스 리소스에 연결](../app-service/app-service-hybrid-connections.md)
* [Functions 프록시에 자세히 알아보기](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
