---
title: Azure Portal를 통해 Azure Stack Edge Pro에서 Vm 네트워크 인터페이스를 관리 하는 방법
description: Azure Portal를 통해 Azure Stack Edge Pro GPU에 배포 된 Vm에서 네트워크 인터페이스를 관리 하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027651"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Portal를 사용 하 여 Azure Stack Edge Pro GPU에서 Vm의 네트워크 인터페이스를 관리 합니다.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Portal, 템플릿, Azure PowerShell cmdlet을 사용 하 고 Azure CLI/Python 스크립트를 통해 Azure Stack Edge 장치에서 Vm (가상 컴퓨터)을 만들고 관리할 수 있습니다. 이 문서에서는 Azure Portal를 사용 하 여 Azure Stack Edge 장치에서 실행 되는 VM에서 네트워크 인터페이스를 관리 하는 방법을 설명 합니다. 

VM을 만들 때 만들 가상 네트워크 인터페이스를 하나 지정 합니다. 가상 컴퓨터를 만든 후에 가상 컴퓨터에 네트워크 인터페이스를 하나 이상 추가할 수 있습니다. 기존 네트워크 인터페이스에 대 한 기본 네트워크 인터페이스 설정을 변경 해야 할 수도 있습니다.

이 문서에서는 기존 VM에 네트워크 인터페이스를 추가 하 고, IP 유형 (정적 및 동적)과 같은 기존 설정을 변경 하 고, 마지막으로 기존 인터페이스를 제거 하거나 분리 하는 방법을 설명 합니다. 

        
## <a name="about-network-interfaces-on-vms"></a>Vm의 네트워크 인터페이스 정보

네트워크 인터페이스를 사용 하면 Azure Stack Edge Pro 장치에서 실행 되는 VM (가상 컴퓨터)에서 Azure 및 온-프레미스 리소스와 통신할 수 있습니다. 장치에서 계산 네트워크에 대 한 포트를 사용 하도록 설정 하면 해당 네트워크 인터페이스에 가상 스위치가 생성 됩니다. 그런 다음이 가상 스위치를 사용 하 여 장치에 Vm 또는 컨테이너 화 된 응용 프로그램과 같은 계산 워크 로드를 배포 합니다. 

장치는 가상 스위치를 하나만 지원 하지만 가상 네트워크 인터페이스는 여러 개 지원 합니다. VM의 각 네트워크 인터페이스에는 고정 또는 동적 IP 주소가 할당 되어 있습니다. VM의 여러 네트워크 인터페이스에 할당 된 IP 주소를 사용 하 여 VM에서 특정 기능을 사용할 수 있습니다. 예를 들어 VM은 단일 서버에서 여러 IP 주소 및 SSL 인증서를 사용 하 여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다. 장치의 VM은 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용할 수 있습니다. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>사전 요구 사항

Azure Portal를 통해 장치에서 Vm 관리를 시작 하기 전에 다음 사항을 확인 합니다.

1. 장치에서 계산에 대 한 네트워크 인터페이스를 사용 하도록 설정 했습니다. 이 작업은 VM의 해당 네트워크 인터페이스에서 가상 스위치를 만듭니다. 
    1. 장치의 로컬 UI에서 **Compute** 로 이동 합니다. 가상 스위치를 만드는 데 사용할 네트워크 인터페이스를 선택합니다.

        > [!IMPORTANT] 
        > 컴퓨팅용 포트는 하나만 구성할 수 있습니다.

    1. 네트워크 인터페이스에서 컴퓨팅을 사용하도록 설정합니다. Azure Stack Edge Pro GPU는 해당 네트워크 인터페이스에 해당 하는 가상 스위치를 만들고 관리 합니다.

1. 장치에 하나 이상의 VM이 배포 되어 있습니다. 이 VM을 만들려면 [Azure Portal를 통해 Azure Stack Edge Pro에 Vm 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)의 지침을 참조 하세요.

1. VM이 **중지** 된 상태 여야 합니다. VM을 중지 하려면 **Virtual machines > 개요** 로 이동 하 고 중지할 vm을 선택 합니다. VM 속성 페이지에서 **중지** 를 선택 하 고 확인 메시지가 표시 되 면 **예** 를 선택 합니다. 네트워크 인터페이스를 추가, 편집 또는 삭제 하기 전에 VM을 중지 해야 합니다.

    ![VM 속성 페이지에서 VM 중지](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>네트워크 인터페이스 추가

장치에 배포 된 가상 컴퓨터에 네트워크 인터페이스를 추가 하려면 다음 단계를 수행 합니다. 

1. 중지 한 가상 머신으로 이동한 다음 **VM 속성** 페이지로 이동 합니다. **네트워킹** 을 선택합니다.
    
    ![VM 속성 페이지에서 네트워킹 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. **네트워킹** 블레이드의 명령 모음에서 **+ 네트워크 인터페이스 추가** 를 선택 합니다.

    ![네트워크 인터페이스 추가 선택](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. **네트워크 인터페이스 추가** 블레이드에서 다음 매개 변수를 입력 합니다.

    
    |열1  |Column2  |
    |---------|---------|
    |속성     | 리소스 그룹의 고유한 이름입니다. 네트워크 인터페이스를 만든 후에는 이름을 변경할 수 없습니다. 여러 네트워크 인터페이스를 쉽게 관리 하려면 [명명 규칙](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)에 제공 된 제안 사항을 사용 합니다.     |
    |가상 네트워크| 네트워크 인터페이스에서 compute를 사용 하도록 설정한 경우 장치에서 만든 가상 스위치와 연결 된 가상 네트워크입니다. 장치와 연결 된 가상 네트워크는 하나 뿐입니다.         |         
    |서브넷     | 선택한 가상 네트워크 내의 서브넷입니다. 이 필드는 compute를 사용 하도록 설정한 네트워크 인터페이스와 연결 된 서브넷으로 자동으로 채워집니다.         |       
    |IP 할당   | 네트워크 인터페이스에 대 한 정적 또는 동적 IP입니다. 고정 IP는 지정 된 서브넷 범위에서 사용 가능한 사용 가능한 IP 여야 합니다. 환경에 DHCP 서버가 있으면 동적을 선택 합니다.        | 

    ![네트워크 인터페이스 블레이드 추가](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. 네트워크 인터페이스 만들기가 진행 되 고 있음을 알리는 알림이 표시 됩니다.

    ![네트워크 인터페이스가 생성 될 때 알림](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  네트워크 인터페이스가 성공적으로 만들어지면 네트워크 인터페이스 목록이 새로 고쳐 새로 생성 된 인터페이스가 표시 됩니다.

    ![업데이트 된 네트워크 인터페이스 목록](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>네트워크 인터페이스 편집

장치에 배포 된 가상 컴퓨터와 연결 된 네트워크 인터페이스를 편집 하려면 다음 단계를 수행 합니다.

1. 중지 한 가상 머신으로 이동 하 여 **VM 속성** 페이지로 이동 합니다. **네트워킹** 을 선택합니다.

1. 네트워크 인터페이스 목록에서 편집 하려는 인터페이스를 선택 합니다. 선택한 네트워크 인터페이스의 맨 오른쪽에 있는 편집 아이콘 (연필)을 선택 합니다.  

    ![편집할 네트워크 인터페이스를 선택 하십시오.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. **네트워크 인터페이스 편집** 블레이드에서 네트워크 인터페이스의 IP 할당만 변경할 수 있습니다. 네트워크 인터페이스와 연결 된 이름, 가상 네트워크 및 서브넷은 만든 후에는 변경할 수 없습니다. **IP 할당** 을 정적으로 변경 하 고 변경 내용을 저장 합니다.

    ![네트워크 인터페이스에 대 한 IP 할당 변경](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. 네트워크 인터페이스 목록이 새로 고쳐 업데이트 된 네트워크 인터페이스를 표시 합니다.


## <a name="detach-a-network-interface"></a>네트워크 인터페이스 분리

장치에 배포 된 가상 컴퓨터와 연결 된 네트워크 인터페이스를 분리 하거나 제거 하려면 다음 단계를 수행 합니다.

1. 중지 한 가상 머신으로 이동 하 여 **VM 속성** 페이지로 이동 합니다. **네트워킹** 을 선택합니다.

1. 네트워크 인터페이스 목록에서 편집 하려는 인터페이스를 선택 합니다. 선택한 네트워크 인터페이스의 오른쪽 끝에서 분리 아이콘 (분리)을 선택 합니다.  

    ![분리할 네트워크 인터페이스를 선택 하십시오.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. 인터페이스가 완전히 분리 된 후에는 나머지 인터페이스를 표시 하도록 네트워크 인터페이스 목록이 새로 고쳐집니다.

## <a name="next-steps"></a>다음 단계

Azure Stack Edge Pro 장치에 가상 컴퓨터를 배포 하는 방법을 알아보려면 [Azure Portal를 통해 가상 컴퓨터 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)를 참조 하세요.
