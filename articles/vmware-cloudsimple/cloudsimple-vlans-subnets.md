---
title: CloudSimple 별 Azure VMware 솔루션의 Vlan 및 서브넷
description: Cloudsimple 사설 클라우드의 Vlan 및 서브넷 및 cloudsimple 서비스를 배포 하는 위치에서 CloudSimple이 제공 하는 네트워크에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140669"
---
# <a name="vlans-and-subnets-overview"></a>Vlan 및 서브넷 개요

CloudSimple은 CloudSimple 서비스가 배포 되는 지역 당 네트워크를 제공 합니다.  네트워크는 기본적으로 라우팅이 활성화 된 단일 TCP 계층 3 주소 공간입니다.  이 지역에서 만든 모든 사설 클라우드 및 서브넷은 추가 구성 없이 서로 통신할 수 있습니다.  Vlan을 사용 하 여 vCenter에서 분산 포트 그룹을 만들 수 있습니다.

![CloudSimple 네트워크 토폴로지](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

각 사설 클라우드에 대해 VLAN (계층 2 네트워크)이 생성 됩니다.  계층 2 트래픽은 사설 클라우드의 경계 내에 유지 되므로 사설 클라우드 내에서 로컬 트래픽을 격리할 수 있습니다.  사설 클라우드에서 생성 된 VLAN을 사용 하 여 해당 사설 클라우드에서 분산 포트 그룹을 만들 수 있습니다.  사설 클라우드에서 만들어진 VLAN은 사설 클라우드의 호스트에 연결 된 모든 스위치에서 자동으로 구성 됩니다.

## <a name="subnets"></a>서브넷

서브넷의 주소 공간을 정의 하 여 VLAN을 만들 때 서브넷을 만들 수 있습니다. 주소 공간의 IP 주소는 서브넷 게이트웨이로 할당 됩니다. 단일 개인 계층 3 주소 공간은 고객과 지역에 따라 할당 됩니다. 네트워크 지역에서 온-프레미스 네트워크 또는 Azure 가상 네트워크와 겹치지 않는 모든 RFC 1918 주소 공간을 구성할 수 있습니다.

모든 서브넷은 기본적으로 서로 통신할 수 있으므로 사설 클라우드 간의 라우팅에 대 한 구성 오버 헤드를 줄일 수 있습니다. 동일한 지역에 있는 여러 Pc의 동-서 데이터는 동일한 계층 3 네트워크에 유지 되며 지역 내의 로컬 네트워크 인프라를 통해 전송 됩니다. 지역에서 사설 클라우드 간의 통신에는 송신이 필요 하지 않습니다. 이 방법을 사용 하면 다른 사설 클라우드에서 다양 한 워크 로드를 배포할 때 WAN/송신 성능 저하가 발생 하지 않습니다.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/Vsphere 서브넷 CIDR 범위

사설 클라우드는 vCenter server에서 관리 하는 격리 된 VMware stack (ESXi 호스트, vCenter, vSAN 및 NSX) 환경으로 만들어집니다.  관리 구성 요소는 vSphere/Vsphere 서브넷 CIDR에 대해 선택 된 네트워크에 배포 됩니다.  네트워크 CIDR 범위는 배포 하는 동안 다른 서브넷으로 나뉩니다.

* 최소 vSphere/Vsphere 서브넷 CIDR 범위 접두사: **/24**
* 최대 vSphere/Vsphere 서브넷 CIDR 범위 접두사: **/21**

> [!CAUTION]
> VSphere/Vsphere CIDR 범위의 IP 주소는 사설 클라우드 인프라에서 사용 하도록 예약 되어 있습니다.  가상 머신에서는이 범위의 IP 주소를 사용 하지 마세요.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/Vsphere 서브넷 CIDR 범위 제한

VSphere/Vsphere 서브넷 CIDR 범위 크기를 선택 하면 사설 클라우드의 크기에 영향을 줍니다.  다음 표에서는 vSphere/Vsphere 서브넷 CIDR의 크기에 따라 가질 수 있는 최대 노드 수를 보여 줍니다.

| 지정 된 vSphere/Vsphere 서브넷 CIDR 접두사 길이 | 최대 노드 수 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>사설 클라우드에서 만든 관리 서브넷

사설 클라우드를 만들 때 생성 되는 관리 서브넷은 다음과 같습니다.

* **시스템 관리**. ESXi 호스트의 관리 네트워크, DNS 서버, vCenter server에 대 한 VLAN 및 서브넷.
* **VMotion**. ESXi 호스트의 vMotion 네트워크에 대 한 VLAN 및 서브넷.
* **Vsan**. ESXi 호스트의 vSAN 네트워크에 대 한 VLAN 및 서브넷.
* **NsxtEdgeUplink1**. Vlan 및 외부 네트워크에 대 한 VLAN 업링크 서브넷
* **NsxtEdgeUplink2**. Vlan 및 외부 네트워크에 대 한 VLAN 업링크 서브넷
* **NsxtEdgeTransport**. 전송 영역에 대 한 VLAN 및 서브넷은 NSX의 계층 2 네트워크에 대 한 연결을 제어 합니다.
* **NsxtHostTransport**. 호스트 전송 영역에 대 한 VLAN 및 서브넷.

### <a name="management-network-cidr-range-breakdown"></a>관리 네트워크 CIDR 범위 분석

지정 된 vSphere/Vsphere 서브넷 CIDR 범위는 여러 서브넷으로 나뉩니다.  다음 표에서는 허용 된 접두사에 대 한 분석의 예를 보여 줍니다.  이 예에서는 CIDR 범위로 192.168.0.0을 사용 합니다.

예제:

| 지정 된 vSphere/Vsphere 서브넷 CIDR/접두사 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 시스템 관리 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 호스트 전송 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Edge 전송 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>다음 단계

* [VLAN 및 서브넷 만들기 및 관리](create-vlan-subnet.md)
