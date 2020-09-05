---
title: Azure DevTest Labs 가상 컴퓨터에서 브라우저 연결 사용
description: DevTest Labs는 이제 Azure 방호와 통합 됩니다. 랩의 소유자는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스 하도록 설정할 수 있습니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 28a3c11f3df578265f9746a173fcb3029a132b26
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870496"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Azure DevTest Labs 가상 컴퓨터에서 브라우저 연결 사용 
DevTest Labs는 브라우저를 통해 가상 컴퓨터에 연결할 수 있도록 하는 [Azure 방호](../bastion/index.yml)와 통합 됩니다. 먼저 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 해야 합니다.

랩의 소유자는 브라우저를 통해 모든 랩 가상 컴퓨터에 액세스 하도록 설정할 수 있습니다. 다른 클라이언트, 에이전트 또는 소프트웨어가 필요하지 않습니다. Azure 방호는 TLS를 통해 Azure Portal에서 직접 가상 머신에 안전 하 고 원활한 RDP/SSH 연결을 제공 합니다. Azure 방호를 통해 연결 하는 경우 가상 머신에 공용 IP 주소가 필요 하지 않습니다. 자세한 내용은 [Azure 방호 이란?](../bastion/bastion-overview.md) 을 참조 하세요.


이 문서에서는 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건 
- 기존 랩의 가상 네트워크에 요새 호스트를 배포 **하거나,** 가상 네트워크로 랩을 연결 합니다.
가상 네트워크에 요새 호스트를 배포 하는 방법을 알아보려면  [Azure 방호 호스트 만들기](../bastion/bastion-create-host-portal.md)를 참조 하세요. 요새 호스트를 만들 때 랩의 가상 네트워크를 선택 합니다. 
- 랩 사용자에 게는 요새 호스트와 요새가 구성 된 가상 네트워크에 대 한 **읽기 권한자** 역할이 있어야 합니다. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>요새 가상 네트워크에서 두 번째 하위 net 만들기
먼저, AzureBastionSubnet에서 비휘발성 리소스의 생성을 허용 하지 않기 때문에, 먼저 요새 가상 네트워크에서 두 번째 서브넷을 만들어야 합니다. 다음 그림에 표시 된 것 처럼 요새 가상 네트워크 내에 다른 서브넷을 만듭니다.

![Azure 방호 가상 네트워크의 두 번째 서브넷](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>서브넷에서 VM 만들기를 사용 하도록 설정
이제 다음 단계를 수행 하 여이 서브넷에서 Vm을 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스** 를 선택 합니다. 
1. 목록에서 **DevTest Labs** 를 선택 합니다. 
1. 랩 목록에서 *랩을*선택 합니다. 

    > [!NOTE]
    > Azure 방호는 현재 미국 서 부, 미국 동부, 유럽 서부, 미국 중부 중부, 오스트레일리아 동부 및 일본 동부 지역에서 일반적으로 사용할 수 있습니다. 따라서 랩을 이러한 지역 중 하나에 없는 경우 이러한 지역 중 하나에서 랩을 만듭니다. 
    
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책** 을 선택 합니다. 
1. **가상 네트워크**를 선택 합니다.
1. 도구 모음에서 **추가** 를 선택 합니다. 
1. 요새 호스트가 배포 된 **가상 네트워크** 를 선택 합니다. 
1. 이전에 만든 다른 **AzureBastionSubnet**아닌 vm에 대 한 서브넷을 선택 합니다. 페이지를 닫고 아래쪽의 목록에 서브넷이 표시 되지 않으면 다시 엽니다. 

    ![서브넷에서 VM 만들기를 사용 하도록 설정](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. **가상 컴퓨터 만들기 옵션에서 사용을** 선택 합니다. 
1. 도구 모음에서 **저장**을 선택합니다. 
1. 랩에 대 한 이전 가상 네트워크가 있는 경우 **...*  를 선택 하 여 제거 합니다.  **제거**합니다. 

## <a name="enable-browser-connection"></a>브라우저 연결 사용 

랩 내에서 가상 네트워크를 랩 소유자로 구성 하면 랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정할 수 있습니다.

랩 가상 컴퓨터에서 브라우저 연결을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 *랩*으로 이동 합니다.
1. **구성 및 정책**을 선택합니다.
1. **설정**에서 **브라우저 연결**을 선택 합니다. 이 옵션이 표시 되지 않으면 **구성 정책** 페이지를 닫고 다시 엽니다. 

    ![브라우저 연결 사용](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>다음 단계
브라우저를 사용 하 여 Vm에 연결 하는 방법을 알아보려면 브라우저를 [통해 가상 머신에](connect-virtual-machine-through-browser.md) 연결 하는 방법을 알아보려면 다음 문서를 참조 하세요.
