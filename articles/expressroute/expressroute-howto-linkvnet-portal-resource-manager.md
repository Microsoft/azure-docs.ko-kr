---
title: 'Express 경로: 회로에 VNet 연결: Azure Portal'
description: Azure Portal를 사용 하 여 Azure Express 경로 회로에 가상 네트워크를 연결 하는 연결을 만드는 방법을 알아봅니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 09/17/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: ad222e55f1e3051266c1398d76c8d246e342aba9
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190923"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>포털을 사용하여 ExpressRoute 회로에 가상 네트워크 연결
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (클래식)](expressroute-howto-linkvnet-classic.md)
> 

이 문서에서는 Azure Portal을 사용하여 Azure ExpressRoute 회로에 가상 네트워크를 연결하는 연결을 만들 수 있습니다. Azure ExpressRoute 회로에 연결한 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

* 활성화된 ExpressRoute 회로가 있어야 합니다.
  * 지침을 수행하여 [ExpressRoute 회로를 만들고](expressroute-howto-circuit-portal-resource-manager.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다.
  * 회로에 구성된 Azure 프라이빗 피어링이 있는지 확인합니다. 피어 링 및 라우팅 지침은 [express 경로 회로에 대 한 피어 링 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md) 문서를 참조 하세요.
  * Azure 개인 피어링이 구성되어 있고 네트워크와 Microsoft 간의 BGP 피어링이 엔드투엔드 연결을 사용하도록 작동 중이어야 합니다.
  * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. 지침에 따라 [ExpressRoute에 대한 가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-resource-manager.md). ExpressRoute의 가상 네트워크 게이트웨이는 GatewayType으로 VPN이 아닌 'ExpressRoute'를 사용합니다.

* 최대 10개의 가상 네트워크를 표준 ExpressRoute 회로에 연결할 수 있습니다. 표준 ExpressRoute 회로를 사용하는 경우 모든 가상 네트워크는 동일한 지역에 있어야 합니다.

* 단일 VNet을 최대 4개의 ExpressRoute 회로에 연결할 수 있습니다. 아래 프로세스를 사용하여 각 ExpressRoute 회로에 대한 새 연결 개체를 만듭니다. ExpressRoute 회로는 동일한 구독, 서로 다른 구독 또는 두 가지가 혼합된 상태로 존재할 수 있습니다.

* ExpressRoute 프리미엄 추가 기능을 사용하도록 설정하면 ExpressRoute 회로의 지역 외부에서 가상 네트워크를 연결하거나 ExpressRoute 회로에 많은 수의 가상 네트워크를 연결할 수 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md) 에서 확인하세요.

* 시작 전에 [비디오 보기](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)로 단계를 더 잘 이해할 수 있습니다.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>회로에 VNet 연결 - 동일한 구독

> [!NOTE]
> 3계층 공급자가 피어링을 구성한 경우 BGP 구성 정보가 표시되지 않습니다. 회로가 프로비전된 상태인 경우 연결을 만들 수 있어야 합니다.
>

### <a name="to-create-a-connection"></a>연결을 만들려면

1. ExpressRoute 회로 및 Azure 프라이빗 피어링이 성공적으로 구성되었는지 확인합니다. [Express 경로 회로 만들기](expressroute-howto-circuit-arm.md) 의 지침에 따라 [express 경로 회로에 대 한 피어 링을 만들고 수정](expressroute-howto-routing-arm.md)합니다. ExpressRoute 회로가 다음 이미지와 같이 표시됩니다.

   [![ExpressRoute 회로 스크린샷](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png "회로 보기")](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1-exp.png#lightbox)
2. 이제 연결 프로비전을 시작하여 가상 네트워크 게이트웨이를 ExpressRoute 회로에 연결할 수 있습니다. **연결**  >  **추가** 를 클릭 하 여 **연결 추가** 페이지를 연 다음 값을 구성 합니다.

   [![연결 추가 스크린샷](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png "연결 추가 스크린샷")](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1-exp.png#lightbox)
3. 연결이 성공적으로 구성되면 연결 개체가 연결에 대한 정보를 표시합니다.

   ![연결 개체 스크린샷](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>회로에 VNet 연결 - 다른 구독

여러 구독에서 ExpressRoute 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 ExpressRoute 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

![구독 간 연결](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- 큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다.
- 조직 내의 각 부서는 자체 구독을 사용하여 서비스를 배포하되, 단일 ExpressRoute 회로를 공유하여 온-프레미스 네트워크로 다시 연결할 수 있습니다.
- 단일 부서(이 예제에서는 IT)가 ExpressRoute 회로를 소유할 수 있습니다. 다른 Azure Active Directory 테넌트 및 기업계약 등록에 연결된 구독을 포함하여 조직 내 다른 구독에서 ExpressRoute 회로 및 해당 회로와 연결된 권한 부여를 사용할 수 있습니다.

  > [!NOTE]
  > 전용 회로에 대한 연결 및 대역폭 요금은 ExpressRoute 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
  >
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>관리 - 회로 소유자 및 회로 사용자 정보

’회로 소유자’는 ExpressRoute 회로 리소스의 인증된 고급 사용자입니다. 회로 소유자는 '회로 사용자'가 사용할 수 있는 권한 부여를 만들 수 있습니다. 회로 사용자는 ExpressRoute 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. 회로 사용자는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**연결 권한 부여를 만들려면**

회로 소유자가 권한 부여를 만듭니다. 그러면 회로 사용자가 ExpressRoute 회로에 가상 네트워크 게이트웨이를 연결하는 데 사용할 수 있는 권한 부여 키가 만들어집니다. 권한 부여는 하나의 연결에만 유효합니다.

> [!NOTE]
> 각 연결에는 별도의 권한이 필요합니다.
>

1. ExpressRoute 페이지에서 **권한 부여**를 클릭한 다음, 권한 부여에 대한 **이름**을 입력하고 **저장**을 클릭합니다.

   ![권한 부여](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)
2. 구성이 저장되면 **리소스 ID** 및 **권한 부여 키**를 복사합니다.

   ![인증 키](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

**연결 권한 부여를 삭제하려면**

연결에 대한 페이지에서 **삭제** 아이콘을 선택하여 연결을 삭제할 수 있습니다.

### <a name="circuit-user-operations"></a>회로 사용자 작업

회로 사용자는 회로 소유자로부터 권한 부여 키를 받아야 하며 리소스 ID가 필요합니다.

**연결 권한 부여를 사용하려면**

1. **+새로 만들기** 단추를 클릭합니다.

   ![새로 만들기 클릭](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)
2. Marketplace에서 **“연결”** 을 검색하고 선택한 후 **만들기**를 클릭합니다.

   ![연결 검색](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)
3. **연결 유형**이 "ExpressRoute"로 설정되어 있는지 확인합니다.
4. 세부 정보를 입력한 다음, 기본 사항 페이지에서 **확인**을 클릭합니다.

   ![기본 페이지](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)
5. **설정** 페이지에서 **가상 네트워크 게이트웨이**를 선택하고 **권한 부여 사용** 확인란을 선택합니다.
6. **권한 부여 키** 및 **피어 회로 URI**를 입력한 후 연결 이름을 지정합니다. **확인**을 클릭합니다. **피어 회로 URI** 는 express 경로 회로의 리소스 ID입니다 .이는 Express 경로 회로의 속성 설정 창에서 찾을 수 있습니다.

   ![설정 페이지](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)
7. **요약** 페이지에서 정보를 검토하고 **확인**을 클릭합니다.

**연결 권한 부여를 해제하려면**

ExpressRoute 회로와 가상 네트워크의 연결을 삭제하여 권한 부여를 해제할 수 있습니다.

## <a name="delete-a-connection-to-unlink-a-vnet"></a>VNet 연결을 해제한 연결 삭제

연결에 대한 페이지에서 **삭제** 아이콘을 선택하여 연결을 삭제하고 ExpressRoute 회로에 대한 VNet 연결을 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
