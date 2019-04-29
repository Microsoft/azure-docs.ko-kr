---
title: Azure Portal을 사용하여 영역 설정된 Windows VM 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 가용성 영역에서 Windows VM 만들기
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e813b26a91d25fbaa1298acd455f27d2cac705f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403699"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Azure Portal을 사용하여 가용성 영역에서 Windows 가상 머신 만들기

이 문서는 Azure Portal을 사용하여 Azure 가용성 영역에서 가상 머신을 만드는 단계를 안내합니다. [가용성 영역](../../availability-zones/az-overview.md)은 Azure 지역에서 물리적으로 별도 영역입니다. 가용성 영역을 사용하여 가능성이 적은 실패 또는 전체 데이터 센터의 손실로부터 앱 및 데이터를 보호합니다.

가용성 영역을 사용하려면 [지원되는 Azure 지역](../../availability-zones/az-overview.md#services-support-by-region)에 가상 머신을 만들어야 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

2. **Compute**를 선택한 다음, **Windows Server 2016 Datacenter**를 선택합니다. 

3. 가상 머신 정보를 입력합니다. 여기에서 입력한 이사용자 이름과 암호는 가상 머신에 로그인하는 데 사용됩니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. 가용성 영역을 지원하는 미국 동부 2와 같은 위치를 선택합니다. 완료되면 **확인**을 클릭합니다.

    ![포털 블레이드에서 VM에 대한 기본 정보 입력](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. VM의 크기를 선택합니다. 권장된 크기를 선택하거나 기능을 기반으로 필터링합니다. 사용하려는 영역에서 크기를 사용할 수 있는지 확인합니다.

    ![VM 크기 선택](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. **설정** > **고가용성** 아래의 **가용성 영역** 드롭다운에서 번호가 매겨진 영역 중 하나를 선택하고 나머지 기본값을 유지하고 **확인**을 클릭합니다.

    ![가용성 영역 선택](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. 요약 페이지에서 **만들기**를 클릭하여 가상 머신 배포를 시작합니다.

7. Azure Portal 대시보드에 VM을 고정합니다. 배포가 완료되면 VM 요약이 자동으로 열립니다.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>관리 디스크 및 IP 주소에 대한 영역 확인

가용성 영역에 VM을 배포하는 경우 VM에 대한 관리 디스크는 동일한 가용성 영역에서 만들어집니다. 기본적으로 공용 IP 주소도 해당 영역에서 만들어집니다.

포털에서 이러한 리소스에 대한 영역 설정을 확인할 수 있습니다.  

1. **리소스 그룹**을 클릭한 다음, *myResourceGroup*과 같은 VM에 대한 리소스 그룹의 이름을 클릭합니다.

2. 디스크 리소스의 이름을 클릭합니다. **개요** 페이지는 리소스의 위치와 가용성 영역에 대한 세부 정보를 포함합니다.

    ![관리 디스크에 대한 가용성 영역](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. 공용 IP 주소 리소스의 이름을 클릭합니다. **개요** 페이지는 리소스의 위치와 가용성 영역에 대한 세부 정보를 포함합니다.

    ![IP 주소에 대한 가용성 영역](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역에서 VM을 만드는 방법을 배웠습니다. Azure VM에 대한 [영역 및 가용성](regions-and-availability.md)에 대해 자세히 알아봅니다.
