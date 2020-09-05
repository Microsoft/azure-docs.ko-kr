---
title: 온-프레미스 환경을 프라이빗 클라우드로 피어링
description: 이 Azure VMware Solution 자습서에서는 Azure VMware Solution의 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 만듭니다.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750457"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>자습서: 온-프레미스 환경을 프라이빗 클라우드로 피어링

ExpressRoute Global Reach는 온-프레미스 환경을 프라이빗 클라우드에 연결합니다. ExpressRoute Global Reach 연결은 프라이빗 클라우드 ExpressRoute 회로와 온-프레미스 환경에 대한 기존 ExpressRoute 연결 간에 설정됩니다.  Azure CLI 및 PowerShell을 사용하여 ExpressRoute Global Reach를 구성하는 방법에 대한 지침이 있으며, [CLI 명령](../expressroute/expressroute-howto-set-global-reach-cli.md)은 온-프레미스 환경 간에 Azure VMware Solution 프라이빗 클라우드에 대한 ExpressRoute Global Reach 피어링을 구성하는 데 도움이 되는 세부 정보 및 예제가 추가되었습니다.   

ExpressRoute Global Reach를 사용하여 두 ExpressRoute 회로 간에 연결을 설정하기 전에 [다른 Azure 구독에서 연결을 설정하는 방법](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)에 대한 설명서를 검토하세요.  [Azure-프라이빗 클라우드 네트워킹을 구성](tutorial-configure-networking.md)할 때 사용하는 ExpressRoute 회로의 경우 ExpressRoute 게이트웨이에 피어링할 때 또는 Global Reach를 사용하여 다른 ExpressRoute 회로를 통해 피어링할 때 권한 부여 키를 만들어서 사용해야 합니다. ExpressRoute 회로에서 권한 부여 키 하나를 사용할 것이며, 온-프레미스 ExpressRoute 회로를 통해 피어링하는 두 번째 권한 부여 키를 만들 것입니다.

> [!TIP]
> 이러한 지침과 관련하여 온-프레미스 ExpressRoute 회로는 _회로 1_이고, 프라이빗 클라우드 ExpressRoute 회로는 다른 구독에 있으며 _회로 2_라는 레이블이 지정됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * ExpressRoute 회로 및 ExpressRoute Global Reach 피어링 관리에 기존 지침 활용
> * 프라이빗 클라우드 ExpressRoute 회로인 _회로 2_에 대한 권한 부여 키 만들기
> * _회로 1_ 구독의 Cloud Shell에서 Azure CLI를 사용하여 온-프레미스-프라이빗 클라우드 ExpressRoute Global Reach 피어링 설정

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 수행하기 위한 필수 구성 요소는 다음과 같습니다.
- ExpressRoute 회로가 Azure VNet(가상 네트워크)에서 ExpressRoute 게이트웨이를 통해 피어링된 프라이빗 클라우드. 피어링 프로시저의 관점에서 보자면 _회로 2_입니다.
- 온-프레미스 환경을 Azure에 연결하는 데 사용되는 별도의 정상 작동하는 ExpressRoute 회로 - 피어링 프로시저의 관점에서 보자면 _회로 1_입니다.
- ExpressRoute Global Reach 피어링에 대한 /29 겹치지 않는 [네트워크 주소 블록](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Azure VMware Solution 프라이빗 클라우드에서 ExpressRoute 권한 부여 키 만들기

1. 프라이빗 클라우드 **개요**의 [관리]에서 **연결 > ExpressRoute > 권한 부여 키 요청**을 선택합니다.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="연결 > ExpressRoute > 권한 부여 키 요청을 선택하여 새 요청을 시작합니다.":::

2. 권한 부여 키의 이름을 입력하고 **만들기**를 선택합니다. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="[만들기]를 클릭하여 새 권한 부여 키를 만듭니다. ":::

   만들기가 완료되면 프라이빗 클라우드의 권한 부여 키 목록에 새 키가 표시됩니다. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="프라이빗 클라우드의 키 목록에 새 권한 부여 키가 표시되는지 확인합니다. ":::

3. /29 주소 블록과 함께 권한 부여 키와 ExpressRoute ID를 기록해 둡니다. 다음 단계에서 피어링을 완료하는 데 사용됩니다. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>권한 부여 키를 사용하여 프라이빗 클라우드를 온-프레미스에 피어링

프라이빗 클라우드 ExpressRoute 회로에 대한 권한 부여 키를 만들었으므로, 이제 온-프레미스 ExpressRoute 회로를 통해 피어링할 수 있습니다.  피어링은 Cloud Shell의 Azure CLI와 이전 단계에서 만든 프라이빗 클라우드 ExpressRoute 회로의 리소스 ID 및 권한 부여 키를 사용하여 온-프레미스 ExpressRoute 회로의 관점에서 수행됩니다.

> [!TIP]  
> Azure CLI 명령 출력을 간소화하기 위해 이러한 지침에서 [`–query` 인수를 사용하여 JMESPath 쿼리를 실행하고 필요한 결과만 표시할 수 있습니다](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. 온-프레미스 ExpressRoute 회로와 동일한 구독을 사용하여 Azure Portal에 로그인하고 Cloud Shell을 엽니다. 셸을 Bash로 유지합니다.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Azure Portal에 로그인하고 Cloud Shell을 엽니다.":::
 
2. 명령줄에서 Azure CLI 명령을 입력하고 해당하는 정보 및 리소스 ID, 권한 부여 키, /29 CIDR 네트워크 블록을 사용하여 피어링을 만듭니다. 

   다음은 우리가 사용할 명령 예제와 피어링이 성공했음을 나타내는 출력입니다. 예제 명령은 [“다른 Azure 구독에서 ExpressRoute 회로 간의 연결 사용"의 3단계](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)에 사용되는 명령을 기반으로 합니다.

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Cloud Shell에서 Azure CLI 명령을 사용하여 ExpressRoute Global Reach 피어링을 만듭니다.":::
 
   이제 ExpressRoute Global Reach 피어링을 통해 온-프레미스 환경에서 프라이빗 클라우드로 연결할 수 있습니다.

> [!TIP]
> [온-프레미스 네트워크 간 연결을 사용 안 함](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) 지침에 따라 방금 만든 피어링을 삭제할 수 있습니다.


## <a name="next-steps"></a>다음 단계

HCX(Hybrid Cloud Extension)를 사용하여 VM 워크로드를 프라이빗 클라우드로 마이그레이션할 계획이라면 [Azure VMware Solution용 HCX 설치](hybrid-cloud-extension-installation.md) 절차를 사용하세요.


<!-- LINKS - external-->

<!-- LINKS - internal -->
