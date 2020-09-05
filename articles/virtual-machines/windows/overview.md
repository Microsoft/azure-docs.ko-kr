---
title: Azure의 Windows VM 개요
description: Azure의 Windows 가상 머신 개요.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4e955a51906f160264fc4e81f263fe7677be91de
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589625"
---
# <a name="windows-virtual-machines-in-azure"></a>Azure의 Windows 가상 머신

Azure Virtual Machines(VM)는 Azure에서 제공하는 여러 유형의 [확장성 있는 주문형 컴퓨팅 리소스](/azure/architecture/guide/technology-choices/compute-decision-tree) 중 하나입니다. 일반적으로 컴퓨팅 환경에서 다른 선택 옵션에서 제공하는 것보다 더 많이 제어해야 하는 경우에 VM을 선택합니다. 이 문서에서는 VM을 만들기 전에 고려해야 하는 요구 사항, 만드는 방법 및 관리하는 방법에 대해 설명합니다.

Azure VM은 가상 컴퓨터를 실행하는 실제 하드웨어를 구입 및 유지 관리하지 않고도 가상화의 유연성을 제공합니다. 하지만 가상 컴퓨터에서 실행하는 소프트웨어의 구성, 패치 및 설치와 같은 작업을 수행하여 VM을 계속 유지 관리할 필요가 있습니다.

Azure 가상 머신은 다양한 방식으로 사용할 수 있습니다. 예는 다음과 같습니다.

* **개발 및 테스트** – Azure VM은 애플리케이션의 코딩과 테스트에 필요한 특정 구성을 갖춘 컴퓨터를 만드는 쉽고 빠른 방법을 제공합니다.
* **클라우드의 애플리케이션** – 애플리케이션에 대한 수요가 변동할 수 있으므로 Azure의 VM에서 애플리케이션을 실행하는 것이 경제적입니다. 필요할 경우 여분의 VM에 대해 비용을 지불하고, 그렇지 않은 경우에는 해당 VM을 종료합니다.
* **확장된 데이터 센터** – Azure 가상 네트워크의 가상 머신은 조직의 네트워크에 쉽게 연결할 수 있습니다.

애플리케이션에서 사용하는 VM의 수는 요구 사항을 충족하는 데 필요한 만큼 늘리거나 줄일 수 있습니다.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM을 만들기 전의 고려 사항
Azure에서 애플리케이션 인프라를 구축하는 경우에는 언제나 다양한 [디자인 고려 사항](/azure/architecture/reference-architectures/n-tier/windows-vm)이 있습니다. VM의 이러한 양상으로 인해 시작하기 전에 다음 항목을 중요하게 고려해야 합니다.

* 애플리케이션 리소스 이름
* 리소스가 저장되어 있는 위치
* VM 크기
* 만들 수 있는 VM의 최대 수
* VM에서 실행하는 운영 체제
* 시작 이후의 VM 구성
* VM에 필요한 관련 리소스

### <a name="locations"></a>위치
Azure에서 만든 리소스는 모두 전 세계의 여러 [지리적 지역](https://azure.microsoft.com/regions/)에 걸쳐 배포됩니다. 일반적으로 VM을 만들 때 지역을 **위치**라고 합니다. VM의 경우 가상 하드 디스크가 저장되는 위치가 지정됩니다.

아래 표에서는 사용할 수 있는 위치 목록을 가져올 수 있는 몇 가지 방법을 보여 줍니다.

| 방법 | Description |
| --- | --- |
| Azure portal |VM을 만들 때 목록에서 위치를 선택합니다. |
| Azure PowerShell |[Get-AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 사용합니다. |
| REST API |[위치 나열](/rest/api/resources/subscriptions) 작업을 사용합니다. |
| Azure CLI |[az account list-locations](/cli/azure/account?view=azure-cli-latest) 작업을 사용합니다. |

### <a name="singapore-data-residency"></a>싱가포르 데이터 상주

Azure에서 단일 지역에 고객 데이터를 저장할 수 있는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역(싱가포르)에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장됩니다. 자세한 내용은 [보안 센터](https://azuredatacentermap.azurewebsites.net/)를 참조하세요.

## <a name="availability"></a>가용성
Azure는 모든 디스크에 프리미엄 스토리지를 사용하여 VM을 배포하는 경우 업계 최고의 99.9% 단일 인스턴스 가상 머신 Service Level Agreement(서비스 수준 약정)를 발표했습니다.  배포에서 표준 99.95% VM 서비스 수준 약정을 충족하려면 가용성 집합 내부에서 워크로드를 실행하는 VM을 둘 이상 계속 배포해야 합니다. 가용성 집합을 사용하면 VM이 Azure 데이터 센터에서 여러 오류 도메인 간에 분산될 뿐만 아니라 다양한 유지 관리 창이 있는 호스트에 배포됩니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.


## <a name="vm-size"></a>VM 크기
사용할 VM의 [크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)는 실행하려는 워크로드를 기준으로 결정됩니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 스토리지 용량 등의 요소를 결정합니다. Azure는 다양한 크기를 제공하여 다양한 유형의 사용을 지원합니다.

Azure는 VM의 크기와 운영 체제에 기반한 [시간당 가격](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)을 청구합니다. 부분 시간의 경우 Azure는 사용 시간(분)에 대해서만 청구합니다. 스토리지는 가격이 책정되며 개별적으로 청구됩니다.

## <a name="vm-limits"></a>VM 제한
구독에는 프로젝트에 대해 많은 수의 VM을 배포하는 데 영향을 줄 수 있는 기본 [할당량 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md)이 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다. [증가를 요구하는 지원 티켓을 제출](../../azure-portal/supportability/resource-manager-core-quotas-request.md)하면 한도가 늘어날 수 있습니다.

### <a name="operating-system-disks-and-images"></a>운영 체제 디스크 및 이미지
가상 머신은 [VHD(가상 하드 디스크)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 해당 OS(운영 체제) 및 데이터를 저장합니다. VHD는 OS를 설치하도록 선택할 수 있는 이미지에도 사용됩니다. 

Azure에서는 다양한 버전과 종류의 Windows Server 운영 체제에서 사용할 수 있도록 많은 [마켓플레이스 이미지](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1)를 제공합니다. Marketplace 이미지는 이미지 게시자, 제안, SKU 및 버전(대개 최신으로 지정된 버전)으로 식별됩니다. 64비트 운영 체제만 지원됩니다. 지원되는 게스트 운영 체제, 역할 및 기능에 대한 자세한 내용은 [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)을 참조하세요.

아래 표에서는 이미지에 대한 정보를 찾을 수 있는 몇 가지 방법을 보여 줍니다.

| 방법 | Description |
| --- | --- |
| Azure portal |사용할 이미지를 선택할 때 사용자에 적합한 값이 자동으로 지정됩니다. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| REST API |[이미지 게시자 나열](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[이미지 제안 나열](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[이미지 SKU 나열](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](/cli/azure/vm/image?view=azure-cli-latest) --location *location*<BR>[az vm image list-offers](/cli/azure/vm/image?view=azure-cli-latest) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](/cli/azure/vm?view=azure-cli-latest) --location *location* --publisher *publisherName* --offer *offerName*|

[사용자 고유의 이미지를 업로드하고 사용하도록](upload-generalized-managed.md) 선택할 수 있습니다. 이렇게 하는 경우 게시자 이름, 제안 및 SKU는 사용되지 않습니다.

### <a name="extensions"></a>확장
VM [확장](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json)은 배포 후 구성 및 자동화 작업을 통해 VM 추가 기능을 제공합니다.

다음과 같은 일반 작업은 확장을 사용하여 수행할 수 있습니다.

* **사용자 지정 스크립트 실행** – [사용자 지정 스크립트 확장](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json)을 사용하면 VM을 프로비전할 때 스크립트를 실행하여 VM에 워크로드를 구성할 수 있습니다.
* **구성 배포 및 관리** – [PowerShell DSC(필요한 상태 구성) 확장](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)을 사용하면 구성과 환경을 관리하도록 VM에 DSC를 설정할 수 있습니다.
* **진단 데이터 수집** – [Azure Diagnostics 확장](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)을 사용하면 애플리케이션의 상태를 모니터링하는 데 사용할 수 있는 진단 데이터를 수집하도록 VM을 구성할 수 있습니다.

### <a name="related-resources"></a>관련 리소스
이 표에 있는 리소스는 VM에서 사용하며, VM을 만들 때 이미 존재하거나 만들어야 합니다.

| 리소스 | 필수 | Description |
| --- | --- | --- |
| [리소스 그룹](../../azure-resource-manager/management/overview.md) |예 |VM은 리소스 그룹에 포함되어야 합니다. |
| [스토리지 계정](../../storage/common/storage-account-create.md) |예 |가상 하드 디스크를 저장하기 위해 VM에 스토리지 계정이 필요합니다. |
| [가상 네트워크](../../virtual-network/virtual-networks-overview.md) |예 |VM은 가상 네트워크의 구성원이어야 합니다. |
| [공용 IP 주소](../../virtual-network/public-ip-addresses.md) |예 |원격으로 액세스하기 위해 VM에 할당된 공용 IP 주소가 있을 수 있습니다. |
| [네트워크 인터페이스](../../virtual-network/virtual-network-network-interface.md) |예 |네트워크에서 통신하기 위해 VM에 네트워크 인터페이스가 필요합니다. |
| [데이터 디스크](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |예 |VM은 스토리지 기능을 확장하기 위해 데이터 디스크를 포함할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

첫 번째 VM을 만듭니다!

- [포털](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure CLI](quick-create-cli.md)
