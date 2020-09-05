---
title: Traffic Manager를 배포 하 여 Azure VMware 솔루션 (AVS) 워크 로드 균형 조정
description: Azure VMware 솔루션 (AVS)과 Traffic Manager를 통합 하 여 여러 지역의 여러 끝점에서 응용 프로그램 워크 로드의 균형을 조정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: dc3107c6a237273b103fe52a91b3569d9e694e1a
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642984"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-avs-workloads"></a>Traffic Manager를 배포 하 여 Azure VMware 솔루션 (AVS) 워크 로드 균형 조정

이 문서에서는 Azure VMware 솔루션 (AVS)과 Traffic Manager를 통합 하 여 여러 끝점에서 응용 프로그램 워크 로드를 분산 하는 과정을 안내 합니다. Traffic Manager 미국 동부, 유럽 서부, 온-프레미스 등 여러 개의 AVS 지역에 걸친 세 개의 응용 프로그램 게이트웨이 간에 트래픽을 전달 하는 시나리오를 살펴보겠습니다. 

Azure Traffic Manager는 글로벌 Azure 지역에서 서비스에 최적의 트래픽을 분산할 수 있도록 하는 DNS 기반 트래픽 부하 분산 장치입니다. Azure에서 실행 중인 워크 로드와 외부 공용 끝점에서 응용 프로그램 트래픽의 부하를 분산 합니다. Traffic Manager에 대 한 자세한 내용은 [Traffic Manager 정의](../traffic-manager/traffic-manager-overview.md) 를 참조 하세요.

먼저 [필수 구성 요소](#prerequisites) 를 검토 합니다. 그런 다음에 대 한 절차를 살펴보겠습니다.

> [!div class="checklist"]
> * 응용 프로그램 게이트웨이의 구성 확인
> * NSX 세그먼트의 구성 확인
> * Traffic Manager 프로필 만들기
> * Traffic Manager 프로필에 외부 끝점 추가

## <a name="topology"></a>토폴로지

다음 그림에 표시 된 것 처럼 Azure Traffic Manager는 지역 끝점 간의 DNS 수준에서 응용 프로그램에 대 한 부하 분산을 제공 합니다. 응용 프로그램 게이트웨이에는 IIS 서버로 구성 된 백 엔드 풀 멤버가 있고,이를 AVS 외부 끝점으로 참조 합니다.

두 개의 AVS 사설 클라우드 지역, 미국 서 부 및 유럽 서부와 미국 동부에 있는 온-프레미스 서버 간에 가상 네트워크를 통해 연결할 때 Express 경로 게이트웨이를 사용 합니다.   

![AVS와 Traffic Manager 통합](media/traffic-manager/traffic-manager-topology.png)
 
## <a name="prerequisites"></a>사전 요구 사항

- 다른 AVS 지역에서 실행 되는 Microsoft IIS 서버로 구성 된 세 개의 가상 머신: 미국 서 부, 유럽 서부 및 온-프레미스 

- 미국 서 부, 유럽 서부 및 온-프레미스에서 외부 끝점을 사용 하는 응용 프로그램 게이트웨이입니다.

- 확인을 위해 인터넷에 연결 된 호스트입니다. 

## <a name="verify-configuration-of-your-application-gateways"></a>응용 프로그램 게이트웨이의 구성 확인

[Azure 애플리케이션 게이트웨이](https://azure.microsoft.com/services/application-gateway/) 는 웹 응용 프로그램에 대 한 트래픽을 관리할 수 있도록 하는 계층 7 웹 트래픽 부하 분산 장치입니다. Application Gateway에 대 한 자세한 내용은 [Azure 애플리케이션 게이트웨이의 정의](../application-gateway/overview.md) 를 참조 하세요. 

이 시나리오에서는 세 개의 응용 프로그램 게이트웨이 인스턴스가 외부 AVS 끝점으로 구성 됩니다. 응용 프로그램 게이트웨이에는 들어오는 계층 7 요청의 부하를 분산 하기 위해 백 엔드 풀 멤버로 구성 된 AVS 가상 머신이 있습니다. (AVS 가상 컴퓨터를 사용 하 여 백 엔드 풀로 Application Gateway를 구성 하는 방법을 알아보려면 [Azure 애플리케이션 Gateway를 사용 하 여 Azure VMware 솔루션에서 웹 앱 보호](protect-avs-web-apps-with-app-gateway.md)를 참조 하세요.)  

다음 단계는 응용 프로그램 게이트웨이의 올바른 구성을 확인 합니다.

1. Azure Portal를 열고 **응용 프로그램 게이트웨이** 를 선택 하 여 현재 응용 프로그램 게이트웨이의 목록을 봅니다. 

    이 시나리오에서는 세 가지 응용 프로그램 게이트웨이를 구성 했습니다.
    - GW-WUS
    - GW-EUS (온-프레미스)
    - GW-WEU

    :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="응용 프로그램 게이트웨이의 목록입니다." lightbox="media/traffic-manager/app-gateways-list-1.png":::

2. 이전에 배포 된 응용 프로그램 게이트웨이 중 하나를 선택 합니다. 응용 프로그램 게이트웨이에 대 한 다양 한 정보가 표시 된 창이 열립니다. 백 엔드 풀 중 하나의 구성을 확인 하려면 **백 엔드 풀** 을 선택 합니다.

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Application gateway 세부 정보입니다." lightbox="media/traffic-manager/backend-pool-config.png":::
 
3. 이 경우 IP 주소가 172.29.1.10 인 웹 서버로 구성 된 하나의 가상 머신 백 엔드 풀 멤버가 표시 됩니다.
 
     :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="백 엔드 풀을 편집 합니다.":::

    마찬가지로 다른 응용 프로그램 게이트웨이 및 백 엔드 풀 멤버의 구성을 확인할 수 있습니다. 

## <a name="verify-configuration-of-the-nsx-t-segment"></a>NSX 세그먼트의 구성 확인

NSX에서 만든 네트워크 세그먼트는 vCenter의 가상 컴퓨터에 대 한 네트워크로 사용 됩니다. 자세한 내용은 [Azure VMware 솔루션 (AVS)에서 NSX-T 네트워크 세그먼트 만들기](tutorial-nsx-t-network-segment.md)자습서를 참조 하세요.

이 시나리오에서 NSX-T 세그먼트는 백 엔드 풀 구성원 가상 머신이 연결 된 AVS 환경에서 구성 됩니다.

1. **세그먼트** 를 선택 하 여 구성 된 세그먼트를 표시 합니다. 이 경우 Contoso-segment1가 계층 1의 유연한 라우터인 Contoso-T01 gateway에 연결 된 것을 알 수 있습니다.

    :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="NSX-T 관리자의 세그먼트 프로필.":::    

2. **계층 1** 게이트웨이를 선택 하 여 연결 된 세그먼트 수와 함께 계층 1 게이트웨이의 목록을 표시 합니다. Contoso-T01에 연결 된 세그먼트를 선택 합니다. 계층 01 라우터에 구성 된 논리 인터페이스를 표시 하는 창이 열립니다. 이는 세그먼트에 연결 된 백 엔드 풀 구성원 가상 컴퓨터에 대 한 게이트웨이로 사용 됩니다.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="연결 된 세그먼트.":::    

3. VM vSphere 클라이언트에서 세부 정보를 볼 가상 컴퓨터를 선택 합니다. IP 주소는 이전 섹션의 3 단계에서 확인 한 것과 일치 합니다. 172.29.1.10.

    :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="가상 컴퓨터 정보.":::    

4. 가상 컴퓨터를 선택한 다음 작업 > 클릭 하 여 **설정 편집** 을 클릭 하 여 NSX 세그먼트에 대 한 연결을 확인 합니다.

## <a name="create-your-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. [Azure Portal](https://rc.portal.azure.com/#home)에 로그인합니다. **Azure 서비스 > 네트워킹**에서 **Traffic Manager 프로필**을 선택 합니다.

2. **+ 추가** 를 선택 하 여 새 Traffic Manager 프로필을 만듭니다.
 
3. 프로필 이름, 라우팅 메서드를 제공 합니다 (이 시나리오에서는 가중치가 적용 됨), 구독 및 리소스 그룹을 [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md)참조 하 고 **만들기**를 선택 합니다.

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Traffic Manager 프로필에 외부 끝점 추가

1. 검색 결과 창에서 Traffic Manager 프로필을 선택 하 고 **끝점** 을 선택한 다음 **+ 추가**를 선택 합니다.

2. 필요한 세부 정보를 입력 합니다. 형식, 이름, FQDN (정규화 된 도메인 이름) 또는 IP 및 가중치 (이 시나리오에서는 각 끝점에 가중치 1을 할당 하 고 있습니다.) **추가**를 선택합니다.

    :::image type="content" source="media/traffic-manager/traffic-manager-profile.png" alt-text="Traffic Manager 프로필-끝점을 추가 합니다.":::  
 
    이렇게 하면 외부 끝점이 만들어집니다. 모니터 상태는 **온라인**상태 여야 합니다. 

    동일한 단계를 반복 하 여 두 개의 외부 끝점을 만듭니다. 하나는 다른 지역에, 다른 하나는 온-프레미스에 있습니다. 만든 후에는 3 개가 모두 Traffic Manager 프로필에 표시 되 고, 세 상태 모두 **온라인**상태 여야 합니다.

3. **개요**를 선택합니다. **DNS 이름**에서 URL을 복사 합니다.

    :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="DNS 이름으로 개요를 Traffic Manager 합니다."::: 

4. DNS 이름 URL을 브라우저에 붙여 넣습니다. 다음 스크린샷은 유럽 서부 지역으로 전송 되는 트래픽을 보여 줍니다.

    :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="유럽 서부로 라우팅되는 트래픽입니다."::: 

5. 브라우저를 새로 고칩니다. 다음 스크린샷은 현재 미국 서 부 지역에서 다른 백 엔드 풀 구성원 집합으로 이동 하는 트래픽을 보여 줍니다.

    :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="미국 서 부로 라우팅되는 트래픽"::: 

6. 브라우저를 다시 새로 고칩니다. 다음 스크린샷은 온-프레미스에서 백 엔드 풀 멤버의 최종 집합으로 이동 하는 트래픽을 보여 줍니다.

    :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="온-프레미스로 라우팅되는 트래픽":::

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure VMware 솔루션 (AVS)에서 Azure 애플리케이션 Gateway 사용](protect-avs-web-apps-with-app-gateway.md)
- [Traffic Manager 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)
- [Azure에서 부하 분산 서비스 결합](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Traffic Manager 성능 측정](../traffic-manager/traffic-manager-performance-considerations.md)
