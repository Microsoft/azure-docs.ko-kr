---
title: 자습서 - Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성
description: Azure에서 프라이빗 클라우드를 배포하는 데 필요한 네트워킹을 만들고 구성하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: ff071e0d6eaf1552634433a76e4eade530c603b6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750494"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>자습서: Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹 구성

Azure VMware Solution 프라이빗 클라우드에는 Azure Virtual Network가 필요합니다. 미리 보기 중에는 Azure VMware Solution가 온-프레미스 vCenter를 지원하지 않으므로 온-프레미스 환경과 통합하기 위한 추가 단계가 필요합니다. ExpressRoute 회로와 가상 네트워크 게이트웨이도 설정해야 하며 이 자습서에서 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결
> * vCenter 및 NSX 관리자 URL 찾기

## <a name="prerequisites"></a>필수 구성 요소 
가상 네트워크를 만들기 전에 [Azure VMware Solution 프라이빗 클라우드](tutorial-create-private-cloud.md)를 만들었는지 확인합니다. 

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. [프라이빗 클라이드 만들기 자습서](tutorial-create-private-cloud.md)에서 만든 리소스 그룹으로 이동하고 **+ 추가**를 선택하여 새 리소스를 정의합니다. 

1. **Marketplace 검색** 텍스트 상자에 **Virtual Network**를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

1. **가상 네트워크** 페이지에서 **만들기**를 선택하여 프라이빗 클라우드에 대한 가상 네트워크를 설정합니다.

1. **Virtual Network 만들기** 페이지에서 가상 네트워크에 대한 세부 정보를 입력합니다.

1. **기본** 탭에서 가상 네트워크 이름을 입력하고, 적절한 지역을 선택하고, **다음: IP 주소**를 선택합니다.

1. **IP 주소** 탭의 **IPv4 주소 공간** 아래에서 이전 자습서에서 만든 주소 공간을 입력합니다.

   > [!IMPORTANT]
   > 이전 자습서에서 프라이빗 클라우드를 만들 때 사용한 주소 공간과 **겹치지 않는** 주소 공간을 사용해야 합니다.

1. **+ 서브넷 추가**를 선택하고, **서브넷 추가** 페이지에서 이름과 적절한 주소 범위를 서브넷에 지정합니다. 마쳤으면 **추가**를 선택합니다.

1. **검토 + 만들기**를 선택합니다.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="검토 + 만들기를 선택합니다." border="true":::

1. 정보를 확인하고 **만들기**를 선택합니다. 배포가 완료되면 리소스 그룹에 가상 네트워크가 표시됩니다.

## <a name="create-a-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

가상 네트워크를 만들었으므로 이제 가상 네트워크 게이트웨이를 만듭니다.

1. 리소스 그룹에서 **+ 추가**를 선택하여 새 리소스를 추가합니다.

1. **Marketplace 검색** 텍스트 상자에서 **가상 네트워크 게이트웨이**를 입력합니다. 가상 네트워크 리소스를 찾아서 선택합니다.

1. **가상 네트워크 게이트웨이** 페이지에서 **만들기**를 선택합니다.

1. **가상 네트워크 게이트웨이 만들기** 페이지의 [기본] 탭에서 필드 값을 제공한 다음, **검토 + 만들기**를 선택합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **구독** | 이 값은 이미 리소스 그룹이 속한 구독으로 채워져 있습니다. |
   | **리소스 그룹** | 이 값은 현재 리소스 그룹에 대해 이미 채워져 있습니다. 이전 테스트에서 만든 리소스 그룹입니다. |
   | **이름** | 가상 네트워크 게이트웨이에 대한 고유한 이름을 입력합니다. |
   | **지역** | 가상 네트워크 게이트웨이의 지리적 위치를 선택합니다. |
   | **게이트웨이 유형** | **ExpressRoute**를 선택합니다. |
   | **SKU** | 기본값(**표준**)을 그대로 둡니다. |
   | **가상 네트워크** | 이전에 만든 가상 네트워크를 선택합니다. 가상 네트워크가 표시되지 않으면 게이트웨이의 지역이 가상 네트워크의 지역과 일치하는지 확인합니다. |
   | **게이트웨이 서브넷 주소 범위** | 이 값은 가상 네트워크를 선택할 때 채워집니다. 기본값을 변경하지 마세요. |
   | **공용 IP 주소** | **새로 만들기**를 선택합니다. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="가상 네트워크 게이트웨이 만들기 페이지의 기본 탭에서 필드 값을 제공한 다음, 검토 + 만들기를 선택합니다." border="true":::

1. 세부 정보가 올바른지 확인하고 **만들기**를 선택하여 가상 네트워크 게이트웨이의 배포를 시작합니다. 
1. 배포가 완료되면 다음 섹션으로 이동하여 ExpressRoute 연결을 Azure VMware Solution 프라이빗 클라우드가 포함된 가상 네트워크 게이트웨이에 연결합니다.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이에 ExpressRoute 연결

가상 네트워크 게이트웨이를 배포했으므로 이제 게이트웨이와 Azure VMware Solution 프라이빗 클라우드 간에 연결을 추가합니다.

1. 이전 자습서에서 만든 프라이빗 클라우드로 이동하여 **관리** 아래에서 **연결**을 선택하고, **ExpressRoute** 탭을 선택합니다.

1. 인증 키를 복사합니다. 권한 부여 키가 없는 경우 해당 키를 만들어야 합니다. 이렇게 하려면 **+ 권한 부여 키 요청**을 선택합니다.

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="권한 부여 키를 복사합니다. 권한 부여 키가 없는 경우 해당 키를 만들어야 합니다. 이렇게 하려면 + 권한 부여 키 요청을 선택합니다." border="true":::

1. 이전 단계에서 만든 가상 네트워크 게이트웨이로 이동하여 **설정** 아래에서 **연결**을 선택합니다. **연결** 페이지에서 **+ 추가**를 선택합니다.

1. **연결 추가** 페이지에서 필드 값을 제공하고 **확인**을 선택합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **이름**  | 연결의 이름을 입력합니다.  |
   | **연결 형식**  | **ExpressRoute**를 선택합니다.  |
   | **인증 사용**  | 이 확인란이 선택되어 있는지 확인합니다.  |
   | **가상 네트워크 게이트웨이** | 이전에 만든 Virtual Network 게이트웨이입니다.  |
   | **인증 키**  | 리소스 그룹에 대한 ExpressRoute 탭에서 인증 키를 복사하여 붙여넣습니다. |
   | **피어 회로 URI**  | 리소스 그룹에 대한 ExpressRoute 탭에서 ExpressRoute ID를 복사하여 붙여넣습니다.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="연결 추가 페이지에서 필드 값을 제공하고 확인을 선택합니다." border="true":::

ExpressRoute 회로와 Virtual Network 간의 연결이 만들어집니다.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>vCenter 및 NSX 관리자 URL 찾기

vCenter 및 NSX 관리자에 로그인하려면 vCenter 웹 클라이언트 및 NSX-T 관리자 사이트에 대한 URL이 필요합니다. 

Azure VMware Solution 프라이빗 클라우드로 이동하여 **관리** 아래에서 **ID**를 선택합니다. 여기서 필요한 정보를 찾을 수 있습니다.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Azure VMware Solution 프라이빗 클라우드로 이동하여 관리 아래에서 ID를 선택합니다. 여기서 필요한 정보를 찾을 수 있습니다." border="true":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 가상 네트워크 게이트웨이 만들기
> * 게이트웨이에 ExpressRoute 회로 연결
> * vCenter 및 NSX 관리자 URL 찾기

프라이빗 클라우드를 로컬로 관리할 수 있도록 환경에 연결하는 데 사용되는 Jumpbox를 만드는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [프라이빗 클라우드 액세스](tutorial-access-private-cloud.md)
