---
title: FAQ(질문과 대답)
description: Azure Container Instances 서비스와 관련 된 faq (질문과 대답)
author: dkkapur
ms.topic: article
ms.date: 01/07/2020
ms.openlocfilehash: 4a3fb4c1818d86f7fe2913790fd9e573c630cbfd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888025"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure Container Instances에 대한 질문과 대답

이 문서에서는 Azure Container Instances에 대한 자주 묻는 질문을 해결 합니다.

## <a name="deployment"></a>배포

### <a name="how-large-can-my-container-image-be"></a>컨테이너 이미지의 크기는 얼마나 되나요?

Azure Container Instances에서 배포 가능한 컨테이너 이미지의 최대 크기는 15 GB입니다. 배포 하는 순간에 정확한 가용성에 따라 더 큰 이미지를 배포할 수 있지만이는 보장 되지 않습니다.

컨테이너 이미지의 크기는 배포 하는 데 걸리는 시간에 영향을 주므로 일반적으로 컨테이너 이미지를 최대한 작게 유지 하려고 합니다.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>내 컨테이너 배포를 가속화 하려면 어떻게 해야 하나요?

배포 시간의 주요 택배 중 하나는 이미지 크기 이므로 크기를 줄이는 방법을 찾아보십시오. 필요 하지 않은 계층을 제거 하거나 더 밝은 기본 OS 이미지를 선택 하 여 이미지의 계층 크기를 줄입니다. 예를 들어 Linux 컨테이너를 실행 하는 경우 전체 Ubuntu 서버가 아닌 기본 이미지로 알파인를 사용 하는 것이 좋습니다. 마찬가지로 Windows 컨테이너의 경우 가능 하면 Nano Server 기본 이미지를 사용 합니다. 

또한 [캐시 된 이미지 나열](/rest/api/container-instances/listcachedimages) API를 통해 사용할 수 있는 Azure Container images의 미리 캐시 된 이미지 목록을 확인 해야 합니다. 미리 캐시 된 이미지 중 하나에 대한 이미지 계층을 전환할 수 있습니다. 

컨테이너 시작 시간을 줄이는 방법에 대한 자세한 [지침](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) 을 참조 하세요.

### <a name="what-windows-base-os-images-are-supported"></a>지원 되는 Windows 기반 OS 이미지는 무엇 인가요?

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 기본 이미지

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`, `10.0.14393.x`

> [!NOTE]
> 반기 채널 릴리스 1709 또는 1803을 기반으로 하는 Windows 이미지는 지원 되지 않습니다.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 및 클라이언트 기본 이미지 (미리 보기)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>컨테이너에서 사용 해야 하는 .NET 또는 .NET Core 이미지 계층은 무엇 인가요? 

요구 사항에 맞는 가장 작은 이미지를 사용 합니다. Linux의 경우 .NET Core 2.1의 릴리스 이후 지원 된 *런타임 알파인* .net Core 이미지를 사용할 수 있습니다. Windows의 경우 전체 .NET Framework를 사용 하는 경우 Windows Server Core 이미지 ( *4.7.2-windowsservercore-ltsc2016*와 같은 런타임 전용 이미지)를 사용 해야 합니다. 런타임 전용 이미지는 작지만 .NET SDK를 필요로 하는 워크 로드를 지원 하지 않습니다.

## <a name="availability-and-quotas"></a>가용성 및 할당량

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>컨테이너 또는 컨테이너 그룹에 할당 해야 하는 코어 및 메모리는 몇 개입니까?

이는 작업에 따라 크게 달라 집니다. 소규모 및 테스트 성능을 시작 하 여 컨테이너의 성능을 확인 합니다. [CPU 및 메모리 리소스 사용량을 모니터링](container-instances-monitor.md)한 다음 컨테이너에 배포 하는 프로세스 종류에 따라 코어 또는 메모리를 추가 합니다. 

또한 CPU 코어의 상한 및 컨테이너 그룹당 사용 가능한 메모리에 대해 배포 하는 지역의 [리소스 가용성](container-instances-region-availability.md#availability---general) 을 확인 해야 합니다. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI를 실행 하는 기본 인프라는 무엇 인가요?

Azure Container Instances는 서버를 사용 하지 않는 컨테이너 주문형 서비스를 목표로 하므로 인프라에 대해 걱정 하지 않고 컨테이너 개발에 집중 하고자 합니다. 성능에 대한 비교를 수행 하려는 경우 또는 주로 F 및 D 시리즈에서 다양 한 Sku의 Azure Vm 집합에 대해 ACI를 실행 합니다. 이는 서비스를 계속 개발 하 고 최적화 하기 위해 향후 변경 될 예정입니다. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>ACI에 수천 개의 코어를 배포 하려는 경우 할당량을 늘릴 수 있나요?
 
예 (경우에 따라). 현재 할당량에 대한 [할당량 및 제한](container-instances-quotas.md) 문서와 요청에 따라 증가할 수 있는 제한을 참조 하세요.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>4 개 이상의 코어와 16gb RAM을 사용 하 여 배포할 수 있나요?

아직 없습니다. 현재는 컨테이너 그룹에 대한 최대값입니다. 특정 요구 사항 또는 요청에 대한 Azure 지원에 문의 하세요. 

### <a name="when-will-aci-be-in-a-specific-region"></a>언제 특정 지역에 있나요?

현재 지역 가용성은 [여기](container-instances-region-availability.md#availability---general)에 게시 됩니다. 특정 지역에 대한 요구 사항이 있는 경우 Azure 지원에 문의 하세요.

## <a name="features-and-scenarios"></a>기능 및 시나리오

### <a name="how-do-i-scale-a-container-group"></a>컨테이너 그룹의 크기를 조정할 어떻게 할까요? 있나요?

현재 컨테이너 또는 컨테이너 그룹에 대해서는 크기 조정을 사용할 수 없습니다. 더 많은 인스턴스를 실행 해야 하는 경우 API를 사용 하 여 서비스에 대한 컨테이너 그룹 만들기에 대한 추가 요청을 자동화 하 고 만듭니다. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>사용자 지정 VNet에서 실행 되는 인스턴스에 사용할 수 있는 기능은 무엇 인가요?

선택한 [azure virtual network에 컨테이너 그룹을 배포](container-instances-vnet.md) 하 고, 개인 ip를 컨테이너 그룹에 위임 하 여 azure 리소스를 통해 VNet 내에서 트래픽을 라우팅할 수 있습니다. 컨테이너 그룹을 가상 네트워크에 배포 하는 작업은 현재 Azure 지역 하위 집합의 프로덕션 작업에 사용할 수 있습니다.

## <a name="pricing"></a>가격

### <a name="when-does-the-meter-start-running"></a>측정기가 실행을 시작 하는 시기는 언제 입니까?

컨테이너 그룹 기간은 컨테이너 그룹이 중지 될 때까지 첫 번째 컨테이너 이미지를 끌어올 때 (새 배포의 경우) 또는 컨테이너 그룹이 다시 시작 (이미 배포 된 경우) 될 때까지 계산 됩니다. [Container Instances 가격 책정](https://azure.microsoft.com/pricing/details/container-instances/)에서 세부 정보를 참조 하세요.

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>컨테이너가 중지 될 때 요금은 중지 됩니까?

전체 컨테이너 그룹이 중지 된 후에는 측정기 실행이 중지 됩니다. 컨테이너 그룹의 컨테이너가 실행 되는 동안 컨테이너를 다시 시작 하려는 경우에는 리소스를 보유 합니다. 

## <a name="next-steps"></a>다음 단계

* Azure Container Instances에 [대해 자세히 알아보세요](container-instances-overview.md) .
* Azure Container Instances의 [일반적인 문제를 해결](container-instances-troubleshooting.md) 합니다.