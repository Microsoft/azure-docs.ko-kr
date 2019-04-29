---
title: Azure 가상 네트워크 (미리 보기)에 대 한 IPv6 개요
titlesuffix: Azure Virtual Network
description: IPv6 끝점 및 경로의 데이터를 Azure 가상 네트워크에서 IPv6에 대 한 설명.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131043"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Azure Virtual Network에 대 한 IPv6 란? (미리 보기)

IPv6에 대 한 Azure VNET (Virtual Network)을 사용 하면 모두 가상 네트워크 내 고 인터넷에서 IPv6 및 IPv4 연결을 사용 하 여 응용 프로그램을 Azure에서 호스트를 할 수 있습니다. 공용 IPv4 주소 고갈,으로 인해 모바일 및 사물 인터넷 (IoT)에 대 한 새 네트워크는 IPv6 제공 경우가 많습니다. 도 장기 ISP 및 모바일 네트워크 IPv6으로 변환 되는 설정입니다. IPv4 전용 서비스에서 찾을 수 자체가 실제 단점은 기존 및 새로운 시장에서. 이중 스택 IPv4/IPv6 연결에는 Azure 호스팅 서비스를 모두 기존 IPv4 및 이러한 새 IPv6 장치 및 네트워크와 쉽게 연결 하는 전역적으로 사용할 수 있는, 이중 스택 된 (ipv6+ipv4 서비스를 사용 하 여이 기술 차이 이동할 수 있습니다.

Azure의 원래 IPv6 연결 쉽게 Azure에서 호스팅되는 응용 프로그램에 대 한 이중 스택 (IPv4/IPv6) 인터넷 연결을 제공 합니다. 인바운드 및 아웃 바운드 시작 된 연결에 대 한 부하 분산 된 IPv6 연결을 사용 하 여 Vm의 간단한 배포 허용합니다. 이 기능은 계속 사용할 수 있으며 자세한 내용은 [여기](../load-balancer/load-balancer-ipv6-overview.md)합니다.
Azure 가상 네트워크에 대 한 IPv6은 훨씬 더 완전 한 기능을 갖춘-Azure에 배포 하는 전체 IPv6 솔루션 아키텍처를 사용 하도록 설정 합니다.

> [!Important]
> Azure Virtual Network에 대 한 IPv6는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

다음 다이어그램에서는 Azure에 간단한 이중 스택 (IPv4/IPv6) 배포를 보여 줍니다.

![IPv6 네트워크 배포 다이어그램](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>이점

Azure 가상 네트워크 IPv6 이점:

- Azure 호스팅 응용 프로그램 증가에 모바일 및 사물 인터넷의 시장 도달 범위를 확장 하는 데 도움이 됩니다.
- 이중 누적된 IPv4/i p v 6 Vm 최대 서비스 배포 유연성을 제공합니다. IPv4 및 IPv6 가능 인터넷 클라이언트를 사용 하 여 단일 서비스 인스턴스에 연결할 수 있습니다.
- 오래 된, 안정적인 Azure VM-에-인터넷 기반 IPv6 연결 합니다.
- 배포에 명시적으로 요청 하면 해야만 IPv6 연결을 인터넷이 설정 되므로 기본적으로 보호 합니다.

## <a name="capabilities"></a>기능

다음 기능을 포함 하는 가상 머신에 대 한 IPv6 지원:

- Azure 고객은 고객에 게 응용 프로그램의 요구를 충족 하거나 자신의 온-프레미스 IP 공간에 원활 하 게 통합 고유한 IPv6 가상 네트워크 주소 공간을 정의할 수 있습니다.
- 이중 스택 (예: IPv4 및 IPv6) 서브넷으로 가상 네트워크 이중 스택 IPv4 및 IPv6 둘 다 리소스는 가상 네트워크 또는 인터넷에 연결 하도록 응용 프로그램을 사용 하도록 설정 합니다.
- 네트워크 보안 그룹에 대 한 IPv6 규칙을 사용 하 여 리소스를 보호 합니다.
- 응용 프로그램을 확장할 수 있는 네트워크 가상 어플라이언스를 활용 하는 경우에 특히 사용자 정의 경로 사용 하 여 가상 네트워크에서 IPv6 트래픽의 라우팅을 사용자 지정 합니다.
- IPv6 공용 Ip에 대 한 AAAA 레코드에 대 한 Azure DNS 지원이 포함 된 복원 력 있는 확장 가능한 응용 프로그램을 만들려면 IPv6 부하 분산 장치 지원 합니다.
- 전체 업그레이드를 사용 하 여 기존 IPv4 전용 배포에 IPv6 연결을 쉽게 추가 합니다.
- 가상 머신 확장 집합을 사용 하 여 부하에 맞게 자동으로 규모가 조정 되는 이중 스택 응용 프로그램을 만듭니다.

## <a name="limitations"></a>제한 사항
Azure 가상 네트워크에 대 한 미리 보기 릴리스의 IPv6에는 다음과 같은 제한이 있습니다.
- Azure 가상 네트워크 (미리 보기)에 대 한 IPv6은 모든 글로벌 Azure 지역에서 글로벌 Azure-만 제공 됩니다 정부 클라우드가 없습니다.   
- IPv6 가상 네트워크에 대 한 Azure Powershell 및 명령줄 인터페이스 (CLI)를 사용 하 여 IPv6 배포에 대 한 전체 지원과 (샘플)를 사용한 문서에 있지만 미리 보기에 대 한 포털 지원이 전부는 아니지만 많은 IPv6 구성에 대해서만 보려는 제한 됩니다.
- 네트워크 패킷 캡처 및 미리 보기에 network Watcher 지원을 NSG 흐름 로그로 제한 됩니다.
- 부하 분산 지원 미리 보기에 대 한 기본 Load Balancer를 처음부터 제한 됩니다.
- 인스턴스 수준 공용 Ip (VM에서 직접 공용 Ip) 미리 보기에서 지원 되지 않습니다.  
- 가상 네트워크 피어 링 (지역적으로 또는 전역) 지원 되지 않습니다 미리 보기. 

## <a name="pricing"></a>가격

IPv6 Azure 리소스 및 대역폭 IPv4와 동일한 요율로 요금이 청구 됩니다. IPv6에 대 한 추가 요금이 없습니다 있습니다. 가격에 대 한 세부 정보를 찾을 수 있습니다 [공용 IP 주소](https://azure.microsoft.com/pricing/details/ip-addresses/)를 [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/), 또는 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)합니다.

## <a name="next-steps"></a>다음 단계

- 설명 하는 방법 [Azure PowerShell을 사용 하 여 IPv6 이중 스택 응용 프로그램 배포](virtual-network-ipv4-ipv6-dual-stack-powershell.md)합니다.
- 설명 하는 방법 [Azure CLI를 사용 하 여 IPv6 이중 스택 응용 프로그램 배포](virtual-network-ipv4-ipv6-dual-stack-cli.md)합니다.
