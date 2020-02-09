---
title: Azure(대규모 인스턴스)에서 SAP HANA 네트워크 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 네트워크 아키텍처입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3777180a4d62f8b253ac4cd096bff15613f33565
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206617"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA(대규모 인스턴스) 네트워크 아키텍처

Azure 네트워크 서비스의 아키텍처는 SAP 애플리케이션을 HANA 대규모 인스턴스에 성공적으로 배포하는 핵심 구성 요소입니다. 일반적으로 Azure(큰 인스턴스)에서 SAP HANA 배포에는 다양한 데이터베이스 크기, CPU 리소스 사용량 및 메모리 사용률이 있는 서로 다른 여러 SAP 솔루션을 갖춘 더 큰 SAP 환경이 포함됩니다. 모든 IT 시스템이 Azure에 이미 있는 것은 아닐 수 있습니다. SAP 환경은 NetWeaver 및 S/4HANA 및 SAP HANA와 기타 DBMS를 혼합 하 여 DBMS 지점 및 SAP 응용 프로그램 관점에서 하이브리드 인 경우가 많습니다. Azure는 다양 한 DBMS, NetWeaver 및 S/4HANA 시스템을 Azure에서 실행할 수 있는 다양 한 서비스를 제공 합니다. Azure는 Azure를 온-프레미스 소프트웨어 배포에 대 한 가상 데이터 센터 처럼 보이도록 하는 네트워크 기술도 제공 합니다.

Azure에서 호스트 되는 전체 IT 시스템이 아닌 경우. Azure 네트워킹 기능을 사용 하 여 온-프레미스 환경을 Azure 자산에 연결 하 여 Azure가 자신의 가상 데이터 센터 처럼 보이도록 할 수 있습니다. 사용 되는 Azure 네트워크 기능은 다음과 같습니다. 

- Azure 가상 네트워크는 온-프레미스 네트워크 자산에 연결 하는 [express](https://azure.microsoft.com/services/expressroute/) 경로 회로에 연결 됩니다.
- 온-프레미스를 Azure에 연결 하는 Express 경로 회로는 최소 [1Gbps 이상의](https://azure.microsoft.com/pricing/details/expressroute/)대역폭을 포함 해야 합니다. 이 최소 대역폭을 통해 온-프레미스 시스템과 VM에서 실행되는 시스템 간의 데이터 전송에 적합한 대역폭이 허용됩니다. 또한 온-프레미스 사용자의 Azure 시스템에 연결할 수 있는 적절한 대역폭도 허용됩니다.
- Azure의 모든 SAP 시스템은 서로 통신할 수 있도록 가상 네트워크에 설정 됩니다.
- 온-프레미스에서 호스트 되는 Active Directory 및 DNS는 온-프레미스에서 Express 경로를 통해 Azure로 확장 되거나 Azure에서 완전 하 게 실행 됩니다.

HANA Large Instances를 Azure 데이터 센터 네트워크 패브릭에 통합 하는 특정 사례에 대해 Azure Express 경로 기술도 사용 됩니다.


> [!NOTE] 
> 하나의 Azure 구독만 특정 Azure 지역에 있는 HANA Large Instance 스탬프의 한 테넌트에만 연결할 수 있습니다. 반대로 단일 HANA Large Instance 스탬프 테넌트는 하나의 Azure 구독에만 연결할 수 있습니다. 이 요구 사항은 Azure에서 청구 가능한 다른 개체와 일치합니다.

Azure (Large Instances)에서 SAP HANA 여러 Azure 지역에 배포 되는 경우 별도의 테넌트가 HANA Large Instance 스탬프에 배포 됩니다. 이러한 인스턴스가 동일한 SAP 자산에 포함되면 둘 다 동일한 Azure 구독에서 실행할 수 있습니다. 

> [!IMPORTANT] 
> Azure (Large Instances)의 SAP HANA에서는 Azure Resource Manager 배포 메서드만 지원 됩니다.

 

## <a name="additional-virtual-network-information"></a>추가 가상 네트워크 정보

가상 네트워크를 Express 경로에 연결 하려면 Azure Express 경로 게이트웨이를 만들어야 합니다. 자세한 내용은 [express 경로에 대 한 express 경로 게이트웨이 정보](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조 하세요. 

Azure Express 경로 게이트웨이는 azure 외부의 인프라 또는 Azure Large Instance 스탬프에 대해 Express 경로를 사용 하는 데 사용 됩니다. Azure Express 경로 게이트웨이를 서로 다른 Microsoft 엔터프라이즈에 지 라우터에서 제공 하기만 하면 최대 4 개의 다른 Express 경로 회로에 연결할 수 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

> [!NOTE] 
> Express 경로 게이트웨이를 사용 하 여 달성할 수 있는 최대 처리량은 Express 경로 연결을 사용 하 여 10gbps입니다. 가상 네트워크에 있는 VM과 온-프레미스 시스템 간에 파일을 복사하는 경우(단일 복사 스트림으로), 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. Express 경로 게이트웨이의 전체 대역폭을 활용 하려면 여러 스트림을 사용 합니다. 또는 단일 파일의 병렬 스트림에서 다른 파일을 복사해야 합니다.


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 대규모 인스턴스에 대한 네트워킹 아키텍처
HANA 대규모 인스턴스에 대한 네트워킹 아키텍처는 다음 네 가지 부분으로 구분할 수 있습니다.

- Azure에 대한 온-프레미스 네트워킹 및 ExpressRoute 연결. 이 부분은 고객의 도메인이며 ExpressRoute 통해 Azure에 연결됩니다. 이 Express 경로 회로는 고객으로 서 완전히 지불 됩니다. 대역폭은 온-프레미스 자산과 연결 중인 Azure 지역 간의 네트워크 트래픽을 처리할 수 있을 만큼 커야 합니다. 다음 그림의 오른쪽 아래 부분을 참조하세요.
- 앞에서 설명한 것 처럼 가상 네트워크를 통해 Azure 네트워크 서비스를 다시 사용 하 여 Express 경로 게이트웨이를 추가 해야 합니다. 이 부분은 애플리케이션 요구 사항, 보안 및 규정 준수 요구 사항에 적합한 디자인을 찾아야 하는 영역입니다. HANA 대규모 인스턴스를 사용하는지 여부는 선택할 가상 네트워크와 Azure 게이트웨이 SKU의 수 측면에서 고려해야 할 또 다른 요소입니다. 그림의 오른쪽 위 부분을 참조하세요.
- Azure에 ExpressRoute 기술을 통해 HANA 대규모 인스턴스 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. HANA 대규모 인스턴스에 자산을 배포한 후 ExpressRoute 회로를 가상 네트워크에 연결하기 위한 일부 IP 주소 범위를 제공하기만 하면 됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. Azure 데이터 센터 네트워크 패브릭 및 HANA Large Instance 장치 간의 연결에 대 한 고객의 추가 요금은 없습니다.
- HANA Large Instance 스탬프 내의 네트워킹으로, 대부분 투명 합니다.

![SAP HANA on Azure(대규모 인스턴스) 및 온-프레미스에 연결된 가상 네트워크](./media/hana-overview-architecture/image1-architecture.png)

HANA 대규모 인스턴스를 사용하기 때문에 온-프레미스 자산에 있는 사용자가 ExpressRoute를 통해 Azure에 연결해야 한다는 요구 사항은 변경되지 않습니다. HANA 대규모 인스턴스 장치에서 호스팅되는 HANA 인스턴스에 연결하는 애플리케이션 계층을 호스팅하는 VM을 실행하는 하나 이상의 가상 네트워크가 있어야 한다는 요구 사항도 변경되지 않습니다. 

Azure에서 SAP 배포의 차이점은 다음과 같습니다.

- 고객 테넌트의 HANA 대규모 인스턴스 장치는 또 다른 ExpressRoute 회로 통해 가상 네트워크에 연결됩니다. 부하 조건을 분리 하기 위해 온-프레미스에서 Azure virtual network Express 경로 회로 및 Azure 가상 네트워크와 HANA Large Instances 간의 회로는 동일한 라우터를 공유 하지 않습니다.
- SAP 애플리케이션 계층과 HANA 대규모 인스턴스 간의 워크로드 프로필은 SAP HANA에서 애플리케이션 계층으로의 데이터 전송(결과 집합)과 같이 많은 작은 요청과 버스트와는 다른 특성을 가지고 있습니다.
- SAP 애플리케이션 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- Azure Express 경로 게이트웨이에는 두 개 이상의 Express 경로 연결이 있습니다. 온-프레미스에서 연결 되는 회로 하나 및 HANA Large Instances에서 연결 된 회로 그러면 MSEEs에서 다른 두 개의 추가 회로를 위한 공간을 확보 하 여 Express 경로 게이트웨이에 연결 합니다. 이 제한은 Express 경로 빠른 경로를 사용 하는 것과는 별개입니다. 연결 된 모든 회로는 Express 경로 게이트웨이의 들어오는 데이터에 대 한 최대 대역폭을 공유 합니다.

HANA 큰 인스턴스 스탬프의 수정 버전 3을 사용 하는 경우 vm과 HANA 큰 인스턴스 단위 간에 발생 하는 네트워크 대기 시간은 일반적인 VM 간 네트워크 왕복 대기 시간 보다 높을 수 있습니다. Azure 지역에 따라 측정값은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)에서 평균 미만으로 분류된 0.7ms 왕복 대기 시간을 초과할 수 있습니다. Azure VM 및 HANA 대규모 인스턴스 단위 간의 네트워크 왕복 대기 시간을 측정하는 Azure 지역 및 도구에 따라 측정된 대기 시간은 최대 약 2밀리초일 수 있습니다. 그럼에도 불구하고 고객은 SAP HANA 기반 프로덕션 SAP 애플리케이션을 SAP HANA 대규모 인스턴스에 성공적으로 배포합니다. Azure HANA 대규모 인스턴스에서 자신의 비즈니스 프로세스를 철저하게 테스트해야 합니다. Express 경로 빠른 경로 라는 새로운 기능을 통해 Azure의 HANA 큰 인스턴스 및 응용 프로그램 계층 Vm 간의 네트워크 대기 시간을 크게 줄일 수 있습니다 (아래 참조). 

HANA Large Instance 스탬프의 수정 버전 4를 사용 하면 [SAP Note #1100926-FAQ:](https://launchpad.support.sap.com/#/notes/1100926/E) Azure Express 경로 빠른 경로가 구성 된 경우 네트워크 성능 (아래 참조)에 설명 된 대로 평균을 초과 하는 것과 같은 평균 분류를 만족 하는 것이 좋습니다. 수정 버전 4의 HANA Large Instance 장치에 근접 하 게 Azure Vm을 배포 하려면 [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)을 활용 해야 합니다. [Sap 응용 프로그램을 사용 하 여 네트워크 대기 시간을 최적화 하려면 Azure 근접 배치 그룹에서 Azure 근접 배치](sap-proximity-placement-scenarios.md)그룹에 설명 된 것 처럼 근접 배치 그룹을 사용 하 여 동일한 azure 데이터 센터에서 sap 응용 프로그램 계층을 찾을 수 있는 방법을 설명 합니다.

Vm과 HANA Large Instance 간에 결정적인 네트워크 대기 시간을 제공 하기 위해 Express 경로 게이트웨이 SKU를 선택 하는 것이 필수적입니다. 온-프레미스와 VM 간의 트래픽 패턴과는 달리, VM과 HANA 대규모 인스턴스 간의 트래픽 패턴은 작지만 높은 버스트의 요청 및 데이터 볼륨을 전송하도록 개발할 수 있습니다. 이러한 버스트를 잘 처리하려면 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. HANA Large Instance Sku의 Type II 클래스에서 UltraPerformance 게이트웨이 SKU를 ExpressRotue 게이트웨이로 사용 하는 것은 필수입니다.

> [!IMPORTANT] 
> SAP 애플리케이션과 데이터베이스 계층 간의 전체 네트워크 트래픽을 고려할 때, SAP HANA on Azure(대규모 인스턴스) 연결에는 가상 네트워크용 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. HANA Large Instance Type II Sku의 경우 UltraPerformance 게이트웨이 SKU만 Express 경로 게이트웨이로 지원 됩니다. Express 경로 빠른 경로를 사용 하는 경우 예외 적용 (아래 참조)

### <a name="expressroute-fast-path"></a>Express 경로 빠른 경로
대기 시간을 줄이려면 Express 경로 빠른 경로가 도입 되어 SAP 응용 프로그램 Vm을 호스트 하는 Azure virtual network에 대 한 HANA Large 인스턴스의 특정 연결에 대해 2019 년 5 월에 릴리스 되었습니다. 지금까지 출시 된 솔루션의 주요 차이점은 Vm과 HANA 큰 인스턴스 간의 데이터 흐름이 Express 경로 게이트웨이를 통해 더 이상 라우팅되지 않는다는 것입니다. 대신, Azure 가상 네트워크의 서브넷에 할당 된 Vm이 전용 엔터프라이즈에 지 라우터와 직접 통신 합니다. 

> [!IMPORTANT] 
> Express 경로 빠른 경로 기능을 사용 하려면 SAP 응용 프로그램 Vm을 실행 하는 서브넷이 HANA Large Instances에 연결 된 동일한 Azure 가상 네트워크에 있어야 합니다. HANA Large Instance 장치에 직접 연결 된 Azure virtual network로 피어 링 Azure virtual network에 있는 Vm은 Express 경로 빠른 경로에서 기능과 되지 않습니다. 결과적으로, Express 경로 회로는 허브 가상 네트워크에 연결 되 고, SAP 응용 프로그램 계층 (스포크)을 포함 하는 가상 네트워크는 피어 링을 받고 있습니다. 경로가 작동 하지 않습니다. 이외에서 Express 경로 빠른 경로는 현재 UDR (사용자 정의 라우팅 규칙)을 지원 하지 않습니다. 자세한 내용은 [Express 경로 가상 네트워크 게이트웨이 및 fastpath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)를 참조 하세요. 


Express 경로 빠른 경로를 구성 하는 방법에 대 한 자세한 내용은 [HANA large instances에 가상 네트워크 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)문서를 참조 하세요.    

> [!NOTE]
> Express 경로 빠른 경로가 작동 하려면 UltraPerformance Express 경로 게이트웨이가 필요 합니다.


## <a name="single-sap-system"></a>단일 SAP 시스템

앞에서 보여 준 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결됩니다. Express 경로 회로는 Microsoft enterprise edge 라우터 (MSEE)에 연결 됩니다. 자세한 내용은 [ExpressRoute 기술 개요](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 경로를 설정 하 고 나면 Azure 백본에 연결 됩니다.

> [!NOTE] 
> Azure에서 SAP 자산을 실행하려면 SAP 자산에서 Azure 지역과 가장 가까운 엔터프라이즈 에지 라우터에 연결합니다. HANA Large Instance 스탬프는 전용 엔터프라이즈에 지 라우터 장치를 통해 연결 되어 Azure IaaS 및 HANA Large Instance 스탬프에서 Vm 간의 네트워크 대기 시간을 최소화 합니다.

SAP 응용 프로그램 인스턴스를 호스트 하는 Vm에 대 한 Express 경로 게이트웨이는 온-프레미스에 연결 하는 하나의 Express 경로 회로에 연결 됩니다. 동일한 가상 네트워크는 대규모 인스턴스 스탬프에 전용으로 연결하기 위해 별도의 엔터프라이즈 에지 라우터에 연결됩니다. Express 경로 빠른 경로를 사용 하면 HANA Large Instances에서 SAP 응용 프로그램 계층 Vm으로의 데이터 흐름이 Express 경로 게이트웨이를 통해 더 이상 라우팅되지 않으며 네트워크 왕복 대기 시간이 줄어듭니다.

이 시스템은 단일 SAP 시스템의 간단한 예입니다. SAP 애플리케이션 계층은 Azure에서 호스팅됩니다. SAP HANA 데이터베이스는 SAP HANA on Azure(대규모 인스턴스)에서 실행됩니다. 2-Gbps 또는 10gbps 처리량의 Express 경로 게이트웨이 대역폭이 병목 상태를 나타내지 않는 것으로 가정 합니다.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

Azure (Large Instances)의 SAP HANA에 연결 하기 위해 여러 SAP 시스템 또는 규모가 많은 SAP 시스템을 배포 하는 경우 Express 경로 게이트웨이의 처리량으로 인해 병목 상태가 발생할 수 있습니다. 또는 프로덕션 시스템과 비프로덕션 시스템을 서로 다른 Azure virtual network에 격리 하려고 합니다. 이 경우 애플리케이션 계층을 여러 가상 네트워크로 분할합니다. 또한 다음과 같은 경우 HANA 대규모 인스턴스에 연결하는 특수 가상 네트워크를 만들 수도 있습니다.

- HANA 대규모 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스팅하는 Azure의 VM으로 백업을 직접 수행합니다.
- 큰 백업 또는 HANA 큰 인스턴스 단위의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

별도의 가상 네트워크를 사용 하 여 HANA 대규모 인스턴스와 Azure 간에 데이터를 대량으로 전송 하기 위해 저장소를 관리 하는 Vm을 호스팅합니다. 이러한 정렬은 SAP 응용 프로그램 계층을 실행 하는 Vm을 제공 하는 Express 경로 게이트웨이에서 HANA Large Instance에서 Azure로의 대량 파일 또는 데이터 전송 효과를 방지 합니다. 

확장성 있는 네트워크 아키텍처를 위해서는 다음과 같이 합니다.

- 더 큰 단일 SAP 애플리케이션 계층에 대해 여러 가상 네트워크를 활용합니다.
- 동일한 가상 네트워크 하에서 별도의 서브넷에 있는 이러한 SAP 시스템을 결합하는 것과 비교하여 배포된 각 SAP 시스템에 대해 별도의 가상 네트워크를 하나씩 배포합니다.

  Azure(큰 인스턴스)에서 SAP HANA에 대한 보다 확장성 있는 네트워킹 아키텍처

![여러 가상 네트워크를 통해 SAP 애플리케이션 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

규칙 및 제한 사항에 따라, 다른 SAP 시스템의 Vm을 호스트 하는 여러 가상 네트워크 간에 적용 하려는 경우 해당 가상 네트워크를 피어 링 해야 합니다. 가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.


## <a name="routing-in-azure"></a>Azure에서 라우팅

기본적으로 배포 하는 경우 Azure (큰 인스턴스)의 SAP HANA에 대 한 세 가지 네트워크 라우팅 고려 사항이 중요 합니다.

* Azure (Large Instances)의 SAP HANA은 Azure Vm 및 전용 Express 경로 연결을 통해서만 액세스할 수 있으며 온-프레미스에서 직접 액세스할 수 없습니다. Microsoft에서 제공하는 것처럼 온-프레미스에서 HANA 대규모 인스턴스 장치에 직접적으로 즉시 액세스할 수 없습니다. 전이적 라우팅 제한은 SAP HANA 대규모 인스턴스에 사용되는 현재 Azure 네트워크 아키텍처로 인해 발생합니다. 온-프레미스에서 실행되는 SAP 솔루션 관리자와 같이 직접 액세스가 필요한 관리 클라이언트 및 애플리케이션 일부는 SAP HANA 데이터베이스에 연결할 수 없습니다. 예외는 ' HANA Large Instances로 직접 라우팅 ' 섹션을 참조 하세요.

* 재해 복구를 위해 서로 다른 두 Azure 지역에 배포 된 HANA 대량 인스턴스 단위가 있는 경우 과거에 동일한 일시적 라우팅 제한이 적용 됩니다. 즉, 한 지역 (예: 미국 서 부)에 있는 HANA 큰 인스턴스 단위의 IP 주소는 다른 지역 (예: 미국 동부)에 배포 된 HANA 큰 인스턴스 단위에 라우팅되지 않습니다. 이러한 제한 사항은 여러 지역에서 Azure 네트워크 피어 링을 사용 하거나 HANA Large Instance 장치를 가상 네트워크에 연결 하는 Express 경로 회로를 교차 연결 하는 것과 무관 합니다. 그래픽 표현은 "여러 지역에서 HANA 대규모 인스턴스 장치 사용" 섹션의 그림을 참조하세요. 배포 된 아키텍처와 함께 제공 되는 이러한 제한으로 인해 재해 복구 기능으로 HANA 시스템 복제를 즉시 사용할 때 금지 됩니다. 최근 변경 내용에 대해서는 ' 여러 지역에서 HANA Large Instance unit 사용 ' 섹션을 참조 하세요. 

* Azure (Large Instances)의 SAP HANA 장치에는 HANA Large Instance 배포를 요청할 때 제출한 서버 IP 풀 주소 범위에서 할당 된 IP 주소가 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 이 IP 주소는 azure virtual network를 HANA Large Instances에 연결 하는 Azure 구독 및 회로를 통해 액세스할 수 있습니다. 해당 서버 IP 풀 주소 범위 이외의 할당된 IP 주소는 하드웨어 장치에 직접 할당됩니다. 이 솔루션을 처음 배포하는 경우와 같이 더 이상 NAT를 통해 *할당되지 않습니다*. 

### <a name="direct-routing-to-hana-large-instances"></a>HANA Large Instances로 직접 라우팅

기본적으로 전이적 라우팅은 다음과 같은 시나리오에서 작동 하지 않습니다.

* HANA Large Instance 단위와 온-프레미스 배포 사이.

* 서로 다른 두 지역에 배포 된 HANA Large Instance 라우팅 사이

이러한 시나리오에서 전이적 라우팅을 사용 하도록 설정 하는 방법에는 세 가지가 있습니다.

- 데이터를 라우팅하는 역방향 프록시를 사용합니다. 예를 들어, NGINX는 Azure 가상 네트워크에 배포 되 고 HANA 큰 인스턴스 및 온-프레미스에 가상 방화벽/트래픽 라우팅 솔루션으로 연결 되는 Azure 가상 네트워크에 배포 된 Traffic Manager.
- Linux VM에서 [IPTables 규칙](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)을 사용하여 온-프레미스 위치와 HANA 대규모 인스턴스 장치 간 또는 다른 지역의 HANA 대규모 인스턴스 장치 간에 라우팅을 사용하도록 설정합니다. IPTables를 실행 하는 VM은 HANA Large Instances 및 온-프레미스에 연결 하는 Azure 가상 네트워크에 배포 해야 합니다. VM의 크기를 적절 하 게 조정 해야 하므로 VM의 네트워크 처리량이 예상 네트워크 트래픽에 충분 합니다. VM 네트워크 대역폭에 대 한 자세한 내용은 [Azure에서 Linux 가상 머신의 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)문서를 참조 하세요.
- [Azure 방화벽](https://azure.microsoft.com/services/azure-firewall/) 은 온-프레미스와 HANA Large instance 단위 간의 직접 트래픽을 허용 하는 다른 솔루션입니다. 

이러한 솔루션의 모든 트래픽은 Azure 가상 네트워크를 통해 라우팅됩니다. 따라서 사용 하는 소프트 어플라이언스 또는 Azure 네트워크 보안 그룹에서 트래픽을 추가로 제한할 수 있으므로 특정 IP 주소 또는 IP 주소 범위는 다음과 같습니다. 온-프레미스를 차단 하거나 명시적으로 HANA 대량 인스턴스에 액세스할 수 있습니다. 

> [!NOTE]  
> Microsoft에서는 타사 네트워크 어플라이언스 또는 IPTables와 관련된 사용자 지정 솔루션에 대한 구현 및 지원을 제공하지 않습니다. 지원은 사용되는 구성 요소의 공급업체 또는 통합자에서 제공해야 합니다. 

#### <a name="express-route-global-reach"></a>Express 경로 Global Reach
Microsoft는 [express 경로 Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach)라는 새로운 기능을 도입 했습니다. Global Reach는 다음과 같은 두 가지 시나리오에서 HANA 대량 인스턴스에 사용할 수 있습니다.

- 다른 지역에 배포 된 HANA 대량 인스턴스 단위로 온-프레미스에서 직접 액세스를 사용 하도록 설정
- 다른 지역에 배포 된 HANA 대량 인스턴스 단위 간의 직접 통신 사용


##### <a name="direct-access-from-on-premises"></a>온-프레미스에서 직접 액세스
Global Reach 제공 되는 Azure 지역에서 HANA Large Instance 장치에 연결 하는 Azure 가상 네트워크에 온-프레미스 네트워크를 연결 하는 Express 경로 회로에 대 한 Global Reach 기능을 사용 하도록 요청할 수 있습니다. Express 경로 회로의 온-프레미스 쪽에는 몇 가지 비용에 영향을 미칩니다. 가격에 대해서는 [Global Reach 추가 기능](https://azure.microsoft.com/pricing/details/expressroute/)에 대 한 가격을 확인 하세요. HANA Large Instance 단위를 Azure에 연결 하는 회로와 관련 된 추가 비용은 없습니다. 

> [!IMPORTANT]  
> HANA Large Instance 단위와 온-프레미스 자산 간의 직접 액세스를 사용 하도록 Global Reach를 사용 하는 경우 네트워크 데이터 및 제어 흐름은 **Azure virtual network를 통해 라우팅되지**않지만 Microsoft enterprise exchange 라우터 간에 직접 연결 됩니다. 결과적으로 NSG 또는 ASG 규칙 또는 Azure 가상 네트워크에 배포 된 모든 유형의 방화벽, NVA 또는 프록시는 작업을 수행 하지 않습니다. **Express 경로 Global Reach를 사용 하 여 온-프레미스에서 HANA 대량 인스턴스 단위로 직접 액세스할 수 있도록 설정 하는 경우 HANA large Instance 장치에 액세스할 수 있는 권한이 온-프레미스 쪽의 방화벽에서 정의 되어야 합니다.** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>다른 Azure 지역에서 HANA Large Instances 연결
동일한 방식으로 온-프레미스를 HANA Large Instance 장치에 연결 하는 데 사용할 수 있는 Global Reach Express 경로를 사용 하 여 두 개의 다른 지역에 배포 된 2 HANA Large Instance 테넌트를 연결할 수 있습니다. 격리는 HANA Large Instance 테넌트가 두 지역에서 Azure에 연결 하는 데 사용 하는 Express 경로 회로입니다. 두 개의 서로 다른 지역에 배포 된 두 개의 HANA Large Instance 테넌트를 연결 하는 데에는 추가 요금이 부과 되지 않습니다. 

> [!IMPORTANT]  
> 서로 다른 HANA Large instance 테넌트 간의 네트워크 트래픽 제어 흐름 및 데이터 흐름은 azure 네트워크를 통해 라우팅되지 않습니다. 따라서 Azure 기능 또는 Nva를 사용 하 여 두 HANA Large Instances 테넌트 간에 통신 제한을 적용할 수 없습니다. 

Global Reach Express 경로를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 문서에서 [가상 네트워크를 HANA large instances에 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)문서를 참조 하세요.


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 대규모 인스턴스의 인터넷 연결
HANA 대규모 인스턴스는 인터넷에 직접 *연결되지 않습니다*. 예를 들어 이 제한으로 인해 OS 공급업체에 OS 이미지를 직접 등록하는 기능이 제한될 수 있습니다. 로컬 SUSE Linux Enterprise Server 구독 관리 도구 서버 또는 Red Hat Enterprise Linux 구독 관리자를 사용해야 할 수도 있습니다.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 및 HANA 대규모 인스턴스 간 데이터 암호화
HANA 대규모 인스턴스 및 VM 간에 전송되는 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 애플리케이션 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. 자세한 내용은 [이 SAP 설명서](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)를 참조하세요.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 대규모 인스턴스 장치 사용

재해 복구 설정을 실현 하려면 여러 Azure 지역에 SHANA Large Instance 단위가 있어야 합니다. Azure [글로벌 Vnet 피어 링]을 사용 하는 경우에도 기본적으로 전이적 라우팅은 서로 다른 두 지역의 HANA Large Instance 테넌트 간에 작동 하지 않습니다. 그러나 Global Reach는 두 개의 서로 다른 지역에서 프로 비전 한 HANA Large Instance 장치 간의 통신 경로를 엽니다. Express 경로 Global Reach의 이러한 사용 시나리오에서는 다음을 수행할 수 있습니다.

 - 추가 프록시 또는 방화벽이 없는 HANA 시스템 복제
 - 시스템 복사본 또는 시스템 새로 고침을 수행 하기 위해 서로 다른 두 지역의 HANA 대량 인스턴스 단위 간 백업 복사


![다른 Azure 지역의 Azure 대규모 인스턴스 스탬프에 연결된 가상 네트워크](./media/hana-overview-architecture/image8-multiple-regions.png)

이 그림에서는 두 지역에 있는 서로 다른 가상 네트워크가 두 Azure 지역 (회색 선)의 Azure (Large Instances)에서 SAP HANA에 연결 하는 데 사용 되는 서로 다른 두 Express 경로 회로에 연결 되는 방법을 보여 줍니다. 이러한 두 개의 교차 연결에 대 한 이유는 한쪽에서 MSEEs 중단 으로부터 보호 하는 것입니다. 두 Azure 지역에 있는 두 가상 네트워크 간의 통신 흐름은 두 개의 서로 다른 지역에 있는 두 가상 네트워크의 [글로벌 피어 링](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) (파란색 점선)을 통해 처리 되어야 합니다. 굵은 빨간색 선은 두 개의 서로 다른 지역에 있는 테넌트의 HANA Large Instance 단위가 서로 통신할 수 있도록 하는 Express 경로 Global Reach 연결을 설명 합니다. 

> [!IMPORTANT] 
> 여러 개의 ExpressRoute 회로를 사용한 경우 트래픽을 적절하게 라우팅할 수 있도록 선행 AS 경로 추가 및 로컬 기본 BGP 설정을 사용해야 합니다.

**다음 단계**
- [SAP HANA(대규모 인스턴스) 스토리지 아키텍처](hana-storage-architecture.md) 참조
