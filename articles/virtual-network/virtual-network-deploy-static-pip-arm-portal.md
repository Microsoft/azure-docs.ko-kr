---
title: 고정 공용 IP 주소를 사용하는 VM 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 고정 공용 IP 주소를 사용하는 VM을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: 42e035b9dca6168fe77e6982505692cf18bfcb40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743175"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure Portal을 사용하여 고정 공용 IP 주소로 가상 머신 만들기

고정 공용 IP 주소를 사용하여 가상 머신을 만들 수 있습니다. 공용 IP 주소를 사용하면 인터넷에서 가상 머신과 통신할 수 있습니다. 동적 주소 대신 고정 공용 IP 주소를 할당하여 주소가 변경되지 않도록 합니다. [고정 공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#allocation-method)에 대해 자세히 알아봅니다. 기존 가상 머신에 할당된 공용 IP 주소를 동적에서 고정으로 변경하거나 사설 IP 주소를 사용하려면 [IP 주소 추가, 변경 또는 제거](virtual-network-network-interface-addresses.md)를 참조하세요. 공용 IP 주소에는 [명목 요금](https://azure.microsoft.com/pricing/details/ip-addresses)이 부과되며, 구독당 사용할 수 있는 공용 IP 주소의 수에 [제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)이 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

 [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **+ 리소스 만들기**를 선택합니다.
2. **계산**을 선택한 다음, **Windows Server 2016 VM** 또는 다른 운영 체제를 선택합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |이름|myVM|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |구독| 구독을 선택합니다.|
    |리소스 그룹| **기존 항목 사용**을 선택하고, **myResourceGroup**을 선택합니다.|
    |Location| **미국 동부**를 선택합니다.|

4. VM에 대한 크기를 선택한 다음, **선택**을 선택합니다.
5. **설정** 아래에서 **공용 IP 주소**를 선택합니다.
6. *myPublicIpAddress*를 입력하고, **고정**을 선택한 다음, 다음 그림에 나와 있는 것처럼 **확인**을 선택합니다.

   ![고정 선택](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   공용 IP 주소가 표준 SKU여야 하는 경우 **SKU** 아래에서 **표준**을 선택합니다. [공용 IP 주소 SKU](virtual-network-ip-addresses-overview-arm.md#sku)에 대해 자세히 알아봅니다. 가상 머신이 공용 Azure Load Balancer의 백 엔드 풀에 추가되는 경우 가상 머신 공용 IP 주소의 SKU는 부하 분산 장치의 공용 IP 주소의 SKU와 일치해야 합니다. 자세한 내용은 [Azure Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#skus)를 참조하세요.

6. **공용 인바운드 포트 선택** 아래에서 포트를 선택하거나 선택하지 않습니다. 인터넷에서 Windows Server 가상 머신에 원격 액세스할 수 있도록 포털 3389가 선택됩니다. 인터넷에서 포트 3389를 여는 것은 프로덕션 워크로드에 권장되지 않습니다.

   ![포트 선택](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. 나머지 기본 설정을 수락하고 **확인**을 선택합니다.
8. **요약** 페이지에서 **만들기**를 선택합니다. 가상 머신을 배포하는 데 몇 분 정도 걸립니다.
9. 가상 머신이 배포되면 포털의 맨 위에 있는 검색 상자에 *myPublicIpAddress*를 입력합니다. 검색 결과에 **myPublicIpAddress**가 표시되면 선택합니다.
10. 할당된 공용 IP 주소 및 다음 그림에 나와 있는 것처럼 **myVM** 가상 머신에 주소가 할당된 것을 볼 수 있습니다.

    ![공용 IP 주소 보기](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Azure는 가상 머신을 만든 지역에서 사용되는 주소에서 공용 IP 주소를 할당했습니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.

11. **구성**을 선택하여 할당이 **고정**인지 확인합니다.

    ![공용 IP 주소 보기](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> 가상 머신의 운영 체제 내에서 IP 주소 설정을 수정하지 마십시오. 운영 체제는 Azure 공용 IP 주소를 인식하지 않습니다. 운영 체제에 사설 IP 주소 설정을 추가할 수 있지만 필요하지 않는 한 그리고 [운영 체제에 사설 IP 주소 추가](virtual-network-network-interface-addresses.md#private)를 읽기 전까지 추가하지 않는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *myResourceGroup*을 입력합니다. 검색 결과에 **myResourceGroup**이 보이면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **리소스 그룹 이름 입력:** 에 *myResourceGroup*을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- Azure에서 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)에 대한 자세한 정보
- 모든 [공용 IP 주소 설정](virtual-network-public-ip-address.md#create-a-public-ip-address)에 대한 자세한 정보
- [사설 IP 주소](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) 및 Azure 가상 머신에 [고정 사설 IP 주소](virtual-network-network-interface-addresses.md#add-ip-addresses) 할당에 대한 자세한 정보
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신 만들기에 대한 자세한 정보