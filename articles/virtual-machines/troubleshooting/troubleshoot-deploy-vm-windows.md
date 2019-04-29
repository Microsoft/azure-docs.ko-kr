---
title: Azure에서 Windows 가상 머신 배포 문제 해결 | Microsoft Docs
description: Azure Resource Manager 배포 모델에서 배포 Windows 가상 머신 문제를 해결 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 5752731f08a7dc9ae8661e698aef9655837c6220
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593257"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Azure에서 Windows 가상 머신 배포 문제 해결

Azure의 VM(가상 머신) 배포 문제를 해결하려면 일반적인 오류 및 해결 방법에 대한 [주요 문제](#top-issues)를 검토합니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.

## <a name="top-issues"></a>주요 문제
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>클러스터가 요청된 VM 크기를 지원할 수 없습니다.
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- 더 작은 VM 크기를 사용하여 요청을 다시 시도합니다.
- 요청한 VM의 크기를 변경할 수 없으면:
    - 가용성 집합의 VM을 모두 중지합니다. **리소스 그룹** > 사용자의 리소스 그룹 > **리소스** > 사용자의 가용성 집합 > **Virtual Machines** > 사용자의 가상 머신 > **중지**를 클릭합니다.
    - VM을 모두 중지한 후에, 원하는 크기로 VM을 만듭니다.
    - 먼저 VM을 시작한 후에 중지된 각각의 VM을 선택하고 시작을 클릭합니다.


## <a name="the-cluster-does-not-have-free-resources"></a>클러스터에 여유 리소스가 없습니다.
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- 요청을 나중에 다시 시도하세요.
- 새 VM이 다른 가용성 집합의 일부가 될 수 있다면
    - 동일한 지역의 다른 가용성 집합에 VM을 만듭니다.
    - 새 VM을 동일한 가상 네트워크에 추가합니다.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Windows 클라이언트 이미지를 사용하고 Azure에 배포하려면 어떻게 하나요?

적절한 Visual Studio(이전의 MSDN) 구독이 있으면 Azure에서 개발/테스트 시나리오에 Windows 7, Windows 8 또는 Windows 10을 사용할 수 있습니다. 이 [문서](../windows/client-images.md)에서는 Azure에서 Windows 클라이언트를 실행하고 Azure 갤러리 이미지를 사용하기 위한 적격성 요구 사항에 대해 대략적으로 설명합니다.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>HUB(Hybrid Use Benefit)를 사용하여 가상 머신을 배포하려면 어떻게 하나요?

Azure 하이브리드 사용 혜택을 사용하여 Windows 가상 머신을 배포하는 몇 가지 다른 방법이 있습니다.

기업 계약 구독의 경우:

• Azure 하이브리드 사용 혜택으로 미리 구성되어 있는 특정 Marketplace 이미지에서 VM을 배포합니다.

기업 계약의 경우:

• 사용자 지정 VM을 업로드하거나 Resource Manager 템플릿 또는 Azure PowerShell을 사용하여 배포합니다.

자세한 내용은 다음 리소스를 참조하세요.

 - [Azure Hybrid Use Benefit 개요](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [다운로드 가능한 FAQ](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Window Server 및 Windows 클라이언트용 Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md)

 - [Azure에서 Hybrid Use Benefit을 사용하는 방법](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Visual Studio Enterprise(BizSpark)에 대한 월간 크레딧을 활성화하는 방법

월별 크레딧을 활성화하려면 이 [문서](https://azure.microsoft.com/offers/ms-azr-0064p/)를 참조하세요.

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>내 EA(기업 계약)에 Enterprise 개발/테스트를 추가하여 Windows 클라이언트 이미지에 액세스하는 방법

Enterprise 개발/테스트 제안을 기준으로 구독을 생성하는 작업은 엔터프라이즈 관리자에게 그러한 권한을 받은 계정 소유자만 할 수 있습니다. 계정 소유자는 Azure 계정 포털을 통해 구독을 생성한 다음 Visual Studio 활성 구독자를 공동 관리자로 추가해야 합니다. 그러면 개발 및 테스트에 필요한 리소스를 관리하고 사용할 수 있습니다. 자세한 내용은 [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)를 참조하세요.

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>내 드라이버가 Windows N 시리즈 VM에 누락되었습니다.

Windows 기반 VM용 드라이버가 [여기](../windows/n-series-driver-setup.md)에 위치합니다.

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>N 시리즈 VM 내에서 GPU 인스턴스를 찾을 수 없습니다.

Windows Server 2016 또는 Windows Server 2012 R2를 실행하는 Azure N 시리즈 VM의 GPU 기능을 이용하려면 배포 후 각 VM에 NVIDIA 그래픽 드라이버를 설치해야 합니다. [Windows VM](../windows/n-series-driver-setup.md) 및 [Linux VM](../linux/n-series-driver-setup.md)에 대한 드라이버 설치 정보를 사용할 수 있습니다.

## <a name="is-n-series-vms-available-in-my-region"></a>N 시리즈 VM을 내 지역에서 사용할 수 있나요?

가용성을 [지역에서 사용할 수 있는 제품 표](https://azure.microsoft.com/regions/services)에서 확인할 수 있고 가격 책정을 [여기](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)에서 확인할 수 있습니다.

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>어떤 클라이언트 이미지를 사용하여 Azure에 배포하고 어떻게 가져올 수 있나요?

적절한 Visual Studio(이전의 MSDN) 구독이 있으면 Azure에서 개발/테스트 시나리오에 Windows 7, Windows 8 또는 Windows 10을 사용할 수 있습니다. 

- [적격 개발/테스트 제품](../windows/client-images.md#eligible-offers) 내에서 Azure 갤러리의 특정 Windows 10 이미지를 사용할 수 있습니다. 
- Visual Studio 구독자(사용 중인 제품 유형은 관계없음)는 64비트 Windows 7, Windows 8 또는 Windows 10 이미지를 [적절하게 준비하고 작성](../windows/prepare-for-upload-vhd-image.md)한 다음, [Azure에 업로드](../windows/upload-generalized-managed.md)할 수도 있습니다. 이렇게 업로드하는 이미지 역시 활성 Visual Studio 구독자가 개발/테스트용으로만 사용할 수 있습니다.

이 [문서](../windows/client-images.md)에서는 Azure에서 Windows 클라이언트를 실행하고 Azure 갤러리 이미지를 사용하기 위한 적격성 요구 사항에 대해 대략적으로 설명합니다.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>VM 크기를 조정할 때 원하는 VM 크기 제품군이 표시되지 않습니다.

VM을 실행하면 해당 VM이 실제 서버에 배포됩니다. Azure 지역의 물리적 서버는 일반적인 실제 하드웨어의 클러스터에서 그룹화됩니다. VM을 다른 하드웨어 클러스터에 이동해야 하는 VM 크기 조정은 VM을 배포하는 데 사용되는 배포 모델에 따라 달라집니다.

- 클래식 배포 모델에서 배포된 VM의 경우 클라우드 서비스 배포를 제거하고 다시 배포하여 다른 크기 제품군의 크기로 VM을 변경해야 합니다.

- Resource Manager 배포 모델에서 배포된 VM의 경우 가용성 집합에 있는 VM의 크기를 변경하기 전에 모든 VM을 중지해야 합니다.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>가용성 집합에서 배포하는 동안 나열된 VM 크기는 지원되지 않습니다.

가용성 집합의 클러스터에서 지원되는 크기를 선택합니다. 가용성 집합을 만들 때 필요할 수 있는 가장 큰 VM 크기를 선택하고 가용성 집합에 첫 번째로 배포하는 것이 좋습니다.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>가용성 집합에 기존 클래식 VM을 추가할 수 있나요?

예. 새 가용성 집합 또는 기존 가용성 집합에 기존 클래식 VM을 추가할 수 있습니다. 자세한 내용은 [가용성 집합에 기존 가상 머신 추가](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)를 참조하세요.


## <a name="next-steps"></a>다음 단계
이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
