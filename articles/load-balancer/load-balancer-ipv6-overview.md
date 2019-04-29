---
title: Azure Load Balancer의 IPv6에 대한 개요
titlesuffix: Azure Load Balancer
description: Azure Load Balancer 및 부하 분산된 VM에 대한 IPv6 지원 이해하기.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, Azure Load Balancer, 이중 스택, 공용 IP, 기본 ipv6, 모바일, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 894a56c2e51e8fa8a2d72253563d218416ace4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861964"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Azure Load Balancer의 IPv6에 대한 개요


>[!NOTE] 
>Azure Load Balancer는 다음의 두 가지 형식을 지원합니다. 기본 및 표준 이 문서는 기본 Load Balancer에 대해 설명합니다. 표준 Load Balancer에 대한 자세한 내용은 [표준 Load Balancer 개요](load-balancer-standard-overview.md)를 참조하세요.

인터넷 연결 부하 분산 장치는 IPv6 주소를 사용해 배포할 수 있습니다. IPv4 연결 외에도 다음과 같은 기능을 사용할 수 있습니다.

* 부하 분산 장치를 통한 공용 인터넷 클라이언트와 Azure Virtual Machines(VM) 사이의 네이티브 종단 간 IPv6 연결.
* VM과 공용 인터넷 IPv6 사용 가능 클라이언트 사이의 네이티브 종단 간 IPv6 아웃바운드 연결.

다음 그림은 Azure Load Balancer에 대한 IPv6 기능을 보여 줍니다.

![IPv6를 사용하여 Azure Load Balancer](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

배포된 후 IPv4 또는 IPv6 사용 가능 인터넷 클라이언트는 Azure 인터넷 연결 Load Balancer의 공용 IPv4 또는 IPv6 주소(또는 호스트 이름)와 통신할 수 있습니다. 부하 분산 장치는 NAT(네트워크 주소 변환)를 사용하여 VM의 개인 IPv6 주소로 IPv6 패킷을 라우팅합니다. IPv6 인터넷 클라이언트는 VM의 IPv6 주소와 직접 통신할 수 없습니다.

## <a name="features"></a>기능

Azure Resource Manager를 통해 배포된 VM에 대한 네이티브 IPv6 지원은 다음을 제공합니다.

1. 인터넷에서 IPv6 클라이언트에 대해 부하 분산된 IPv6 서비스
2. VM의 네이티브 IPv6 및 IPv4 엔드포인트("이중 스택됨")
3. 인바운드 및 아웃바운드 시작 네이티브 IPv6 연결
4. TCP, UDP, HTTP(S)와 같은 지원되는 프로토콜은 서비스 아키텍처의 전체 범위를 사용하도록 설정합니다.

## <a name="benefits"></a>이점

이 기능을 통해 다음과 같은 주요 이점을 사용할 수 있습니다.

* 새 애플리케이션이 IPv6 전용 클라이언트에 액세스할 수 있도록 한 정부 규정을 충족합니다.
* 모바일 및 IoT(사물 인터넷) 개발자가 성장하는 모바일 및 IoT 시장에 대처하기 위해 이중 스택된(IPv6+IPv4) Azure Virtual Machines를 사용할 수 있도록 설정합니다.

## <a name="details-and-limitations"></a>세부 사항 및 제한 사항

세부 정보

* Azure DNS 서비스는 IPv4 및 IPv6 AAAA 이름 레코드를 모두 포함하며 부하 분산 장치에 대해 두 레코드와 반응합니다. 클라이언트는 어떤 주소(IPv4 또는 IPv6)와 통신할지 선택합니다.
* VM이 공용 인터넷 IPv6 연결 디바이스에 연결을 시작할 경우 VM의 원본 IPv6 주소는 부하 분산 디바이스의 공용 IPv6 주소에 대한 NAT(네트워크 주소 변환)입니다.
* Linux 운영 체제를 실행하는 VM은 DHCP 통해 IPv6 IP 주소를 수신하도록 구성되어야 합니다. Azure Gallery의 대부분 Linux 이미지는 수정하지 않고 IPv6를 지원하도록 이미 구성되어 있습니다. 자세한 내용은 [Linux VM에 대한 DHCPv6 구성](load-balancer-ipv6-for-linux.md)
* 부하 분산 장치에 상태 프로브를 사용하기로 선택한 경우 IPv4 프로브 만들어 IPv4 및 IPv6 엔드포인트 모두에 사용합니다. VM에서 서비스가 중단될 경우 IPv4 및 IPv6 엔드포인트는 모두 회전 중단됩니다.

제한 사항

* Azure Portal에 IPv6 부하 분산 규칙을 추가할 수 없습니다. 규칙은 CLI PowerShell 템플릿을 통해서만 만들 수 있습니다.
* IPv6 주소를 사용하도록 기존 VM을 업그레이드 하지 못할 수도 있습니다. 새 VM을 배포해야 합니다.
* 각 VM에서 단일 네트워크 인터페이스에 단일 IPv6 주소를 할당할 수 있습니다.
* 공용 IPv6 주소는 VM에 할당할 수 없습니다. 부하 분산 장치에만 할당할 수 있습니다.
* 공용 IPv6 주소에 대해 역방향 DNS 조회를 구성할 수 없습니다.
* IPv6 주소를 사용하는 VM은 Azure Cloud Service의 멤버가 될 수 없습니다. Azure Virtual Network (VNet)에 연결될 수 있으며 IPv4 주소를 통해 서로 통신합니다.
* 개인 IPv6 주소를 리소스 그룹의 개별 VM에 배포할 수 있지만 확장 집합을 통해 리소스 그룹에 배포할 수는 없습니다.
* Azure VM은 IPv6를 통해 다른 VM, 다른 Azure 서비스 또는 온-프레미스 디바이스에 연결할 수 없습니다. 단지 IPv6를 통해 Azure Load Balancer와 통신할 수 있습니다. 그러나 IPv4를 사용하면 이러한 다른 리소스와 통신할 수 있습니다.
* IPv4에 대한 Network Security Group (NSG) 보호는 이중 스택 (IPv6+IPv4) 배포에서 지원됩니다. NSG는 IPv6 엔드포인트에 적용되지 않습니다.
* VM의 IPv6 엔드포인트는 인터넷에 직접 노출되지 않습니다. 부하 분산 장치 뒤에.있습니다. 부하 분산 장치 규칙에 지정된 포트만 IPv6를 통해 액세스할 수 있습니다.
* IPv6에 대한 IdleTimeout 매개 변수 변경은 **현재 지원되지 않습니다**. 기본 값은 4분입니다.
* IPv6에 대한 loadDistributionMethod 매개 변수 변경은 **현재 지원되지 않습니다**.
* 예약된 IPv6 IP(여기서 IPAllocationMethod = static)는 **현재 지원되지 않습니다**.
* NAT64(IPv4로의 IPv6 변환)가 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

IPv6를 사용하여 부하 분산 장치를 배포하는 방법을 알아봅니다.

* [지역별 IPv6 가용성](https://go.microsoft.com/fwlink/?linkid=828357)
* [템플릿을 사용하여 IPv6와 함께 부하 분산 장치 배포하기](load-balancer-ipv6-internet-template.md)
* [Azure PowerShell을 사용하여 IPv6와 함께 부하 분산 장치 배포하기](load-balancer-ipv6-internet-ps.md)
* [Azure CLI를 사용하여 IPv6와 함께 부하 분산 장치 배포하기](load-balancer-ipv6-internet-cli.md)
