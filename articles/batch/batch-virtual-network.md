---
title: 가상 네트워크에서 풀 프로비전 - Azure Batch | Microsoft Docs
description: 계산 노드가 파일 서버와 같은 네트워크의 다른 VM과 안전하게 통신할 수 있도록 Azure 가상 네트워크에 Batch 풀을 만드는 방법입니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 2583e7e218e765e0d7745978582e19a5a4fe17ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550204"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>가상 네트워크에서 Azure Batch 만들기

Azure Batch 풀을 만들 때는 지정한 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)(VNet)의 하위 집합에서 풀을 프로비전할 수 있습니다. 이 문서에서는 VNet에서 Batch 풀을 설정하는 방법을 설명합니다. 

## <a name="why-use-a-vnet"></a>VNet을 사용하는 이유

Azure Batch 풀에는 다중 인스턴스 작업 처리 등, 계산 노드가 서로 통신할 수 있게 하는 설정이 있습니다. 이러한 설정에는 별도의 VNet이 필요하지 않습니다. 그러나 기본적으로 노드는 라이선스 서버나 파일 서버 등, Batch 풀에 속하지 않는 가상 머신과는 통신할 수 없습니다. 풀 계산 노드가 다른 가상 머신 또는 온-프레미스 네트워크와 안전하게 통신하게 하기 위해 Azure VNet의 서브넷에서 풀을 프로비전할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* **인증**. Azure VNet을 사용하려면 Batch 클라이언트 API가 Azure AD(Active Directory) 인증을 사용해야 합니다. Azure AD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다. 

* **Azure VNet**. VNet 요구 사항 및 구성에 대한 다음 섹션을 참조하세요. 하나 이상의 서브넷으로 VNet을 미리 준비하기 위해 Azure Portal, Azure PowerShell, Azure CLI(명령줄 인터페이스) 또는 기타 방법을 사용할 수 있습니다.  
  * Azure Resource Manager VNet을 만들려면 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요. Resource Manager 기반 VNet은 새 배포에 권장되며, Virtual Machine 구성의 풀에서만 지원됩니다.
  * 클래식 VNet을 만들려면 [여러 서브넷이 있는 가상 네트워크(클래식) 만들기](../virtual-network/create-virtual-network-classic.md)를 참조하세요. 클래식 VNet은 Cloud Services 구성의 풀에서만 지원됩니다.

## <a name="vnet-requirements"></a>VNet 요구 사항

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>포털에서 VNet이 있는 풀 만들기

VNet을 만들고 서브넷을 할당한 후에는 해당 VNet으로 Batch 풀을 만들 수 있습니다. Azure Portal에서 풀을 만들려면 다음 단계를 수행합니다. 

1. Azure Portal에서 Batch 계정으로 이동합니다. 이 계정은 사용할 VNet이 포함된 리소스 그룹과 동일한 구독 및 지역에 있어야 합니다. 
2. 왼쪽의 **설정** 창에서 **풀** 메뉴 항목을 선택합니다.
3. **풀** 창에서 **추가** 명령을 선택합니다.
4. **풀 추가** 창의 **이미지 형식** 드롭다운에서 사용할 옵션을 선택합니다. 
5. 사용자 지정 이미지에 대해 올바른 **게시자/제품/Sku**를 선택합니다.
6. **노드 크기**, **대상 전용 노드** 및 **낮은 우선 순위 노드** 등 나머지 필수 설정과 선택 사항인 설정을 원하는 대로 지정합니다.
7. **가상 네트워크**에서 사용할 가상 네트워크와 서브넷을 선택합니다.
  
   ![가상 네트워크가 있는 풀 추가](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>강제 터널링을 위한 사용자 정의 경로

조직에서 검사와 로깅을 위해 서브넷에서 인터넷으로 가는 트래픽을 온-프레미스 위치로 다시 리디렉션(강제)해야 하는 요구 사항이 있을 수 있습니다. VNet에서 서브넷에 강제 터널링을 사용할 수 있습니다. 

강제 터널링을 사용하는 VNet에서 Azure Batch 풀 계산 노드가 작동하도록 해당 서브넷에 대해 다음 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md)를 추가해야 합니다.

* Batch 서비스는 작업 예약을 위해 풀 계산 노드와 통신해야 합니다. 이 통신을 사용하려면 Batch 계정이 존재하는 지역의 Batch 서비스에서 사용하는 각 IP 주소에 대해 사용자 정의 경로를 추가해야 합니다. Batch 서비스의 IP 주소 목록을 가져오려면 Azure 지원에 문의하세요.

* 온-프레미스 네트워크 장비에서 Azure Storage에 대한 아웃바운드 트래픽(특히 `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` 및 `<account>.blob.core.windows.net`)이 차단되지 않는지 확인합니다.

사용자 정의 경로를 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 **다음 홉 유형**을 **인터넷**으로 설정합니다. 다음 예제를 참조하세요.

![사용자 정의 경로](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>다음 단계

- Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.
- 사용자 정의 경로 만들기에 대한 자세한 내용은 [사용자 정의 경로 만들기 - Azure Portal](../virtual-network/tutorial-create-route-table-portal.md)을 참조하세요.
