---
title: 사설 클라우드 VMware 구성 요소
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 서비스를 사용 하 여 Azure 위치에 VMware를 기본적으로 배포 하는 방법을 알아봅니다. 사설 클라우드는 Azure 클라우드의 나머지와 통합 됩니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142505"
---
# <a name="private-cloud-vmware-components"></a>사설 클라우드 VMware 구성 요소

사설 클라우드는 관리 도메인의 vCenter 서버에서 관리 하는 격리 된 VMware stack (ESXi 호스트, vCenter, vSAN 및 NSX) 환경입니다.  CloudSimple 서비스를 사용 하면 azure 위치의 azure 운영 체제 미 설치 인프라에 VMware를 기본적으로 배포할 수 있습니다.  사설 클라우드는 Azure 클라우드의 나머지와 통합 됩니다.  사설 클라우드는 다음 VMware stack 구성 요소와 함께 배포 됩니다.

* **VMware ESXi-** Azure 전용 노드의 하이퍼바이저
* **VMware vCenter-** 사설 클라우드 vSphere 환경을 중앙에서 관리 하기 위한 어플라이언스
* **VMware vSAN-** 하이퍼 수렴 형 인프라 솔루션
* **VMWARE NSX 데이터 센터-** 네트워크 가상화 및 보안 소프트웨어  

## <a name="vmware-component-versions"></a>VMware 구성 요소 버전

사설 클라우드 VMware 스택은 다음 소프트웨어 버전과 함께 배포 됩니다.

| 구성 요소 | 버전 | 사용이 허가 된 버전 |
|-----------|---------|------------------|
| ESXi | 6.7 u2 | Enterprise Plus |
| vCenter | 6.7 u2 | vCenter 표준 |
| vSAN | 6.7 | Enterprise |
| NSX 데이터 센터 | 2.4.1 | 고급 |

## <a name="esxi"></a>ESXi

사설 클라우드를 만들 때 프로 비전 된 CloudSimple 노드에 VMware ESXi 설치 됩니다.  ESXi는 워크 로드 Vm (가상 머신)을 배포 하기 위한 하이퍼바이저를 제공 합니다.  노드는 사설 클라우드에서 하이퍼 수렴 형 인프라 (계산 및 저장소)를 제공 합니다.  노드는 사설 클라우드의 vSphere 클러스터의 일부입니다.  각 노드에는 언더레이 네트워크에 연결 된 네 개의 실제 네트워크 인터페이스가 있습니다.  VCenter에서 **Vsphere 분산 된 스위치 (vds)** 를 만드는 데 사용 되는 두 개의 실제 네트워크 인터페이스와 **NSX 관리 되는 가상 분산 스위치 (N VDS)** 를 만드는 데 사용 됩니다.  네트워크 인터페이스는 고가용성을 위해 활성-활성 모드로 구성 됩니다.

VMware ESXi에 대 한 자세한 정보

## <a name="vcenter-server-appliance"></a>vCenter server 어플라이언스

vCenter server 어플라이언스 (VCSA)는 CloudSimple로 VMware 솔루션에 대 한 인증, 관리 및 오케스트레이션 함수를 제공 합니다. 포함 된 PSC (Platform Services Controller)를 사용 하는 VCSA는 사설 클라우드를 만들 때 배포 됩니다.  VCSA는 사설 클라우드를 배포할 때 생성 된 vSphere 클러스터에 배포 됩니다.  각 사설 클라우드에는 자체 VCSA가 있습니다.  사설 클라우드의 확장은 사설 클라우드의 VCSA에 노드를 추가 합니다.

### <a name="vcenter-single-sign-on"></a>vCenter Single Sign-On

VCSA의 Embedded Platform Services 컨트롤러는 **VCenter Single sign-on 도메인**에 연결 됩니다.  도메인 이름은 **cloudsimple. local**입니다.  VCenter에 액세스할 수 있는 기본 사용자 **CloudOwner@cloudsimple.com** 가 만들어집니다.  [VCenter에 대 한](set-vcenter-identity.md)온-프레미스/Azure active directory id 원본을 추가할 수 있습니다.

## <a name="vsan-storage"></a>vSAN 저장소

사설 클라우드는 클러스터에 로컬인 완전히 구성 된 모든 플래시 vSAN 저장소로 생성 됩니다.  Vsphere 데이터 저장소를 사용 하 여 vSphere 클러스터를 만들려면 동일한 SKU의 최소 3 개 노드가 필요 합니다.  중복 제거 및 압축은 기본적으로 vSAN 데이터 저장소에서 사용 하도록 설정 됩니다.  VSphere 클러스터의 각 노드에 두 개의 디스크 그룹이 만들어집니다. 각 디스크 그룹에는 하나의 캐시 디스크와 3 개의 용량 디스크가 포함 되어 있습니다.

기본 vSAN 저장소 정책은 Vsan 클러스터에 생성 되 고 vSAN 데이터 저장소에 적용 됩니다.  이 정책은 필요한 수준의 서비스를 보장 하기 위해 데이터 저장소 내에서 VM 저장소 개체를 프로 비전 하 고 할당 하는 방법을 결정 합니다.  저장소 정책은 오류 허용 **(FTT)** 및 **실패 허용 오차 방법을**정의 합니다.  새 저장소 정책을 만들고 Vm에 적용할 수 있습니다. SLA를 유지 하기 위해 vSAN 데이터 저장소에서 25%의 예비 용량을 유지 해야 합니다.  

### <a name="default-vsan-storage-policy"></a>기본 vSAN 저장소 정책

아래 표는 기본 vSAN 저장소 정책 매개 변수를 보여 줍니다.

| VSphere 클러스터에 있는 노드 수 | FTT | 실패 허용 시간 방법 |
|------------------------------------|-----|--------------------------|
| 3 및 4 노드 | 1 | RAID 1 (미러링)-복사본 2 개를 만듭니다. |
| 5 ~ 16 노드 | 2 | RAID 1 (미러링)-3 개의 복사본을 만듭니다. |

## <a name="nsx-data-center"></a>NSX 데이터 센터

NSX 데이터 센터는 사설 클라우드에서 네트워크 가상화, 마이크로 분할 및 네트워크 보안 기능을 제공 합니다.  NSX을 통해 사설 클라우드의 NSX 데이터 센터에서 지원 되는 모든 서비스를 구성할 수 있습니다.  사설 클라우드를 만들 때 다음 NSX 구성 요소를 설치 하 고 구성 합니다.

* NSXT 관리자
* 전송 영역
* 호스트 및에 지 업링크 프로필
* Edge 전송, Ext1 및 Ext2에 대 한 논리 스위치
* ESXi 전송 노드에 대 한 IP 풀
* Edge 전송 노드에 대 한 IP 풀
* 에 지 노드
* 컨트롤러 및에 지 Vm에 대 한 DRS 방지 규칙
* 계층 0 라우터
* Tier0 라우터에서 BGP 사용

## <a name="vsphere-cluster"></a>vSphere 클러스터

ESXi 호스트는 사설 클라우드의 고가용성을 보장 하기 위해 클러스터로 구성 됩니다.  사설 클라우드를 만들 때 vSphere 관리 구성 요소가 첫 번째 클러스터에 배포 됩니다.  관리 구성 요소에 대 한 리소스 풀이 만들어지고 모든 관리 Vm이이 리소스 풀에 배포 됩니다. 사설 클라우드를 축소 하기 위해 첫 번째 클러스터를 삭제할 수 없습니다.  vSphere 클러스터는 **VSPHERE HA**를 사용 하는 vm에 대해 고가용성을 제공 합니다.  허용 되는 오류는 클러스터에서 사용 가능한 노드 수를 기반으로 합니다.  수식을 사용할 수 있습니다 ```Number of nodes = 2N+1``` . 여기서 ```N``` 은 허용 되는 오류 수입니다.

### <a name="vsphere-cluster-limits"></a>vSphere 클러스터 제한

| 리소스 | 제한 |
|----------|-------|
| 사설 클라우드를 만들기 위한 최소 노드 수 (첫 번째 vSphere 클러스터) | 3 |
| 사설 클라우드의 vSphere 클러스터에 있는 최대 노드 수 | 16 |
| 사설 클라우드의 최대 노드 수 | 64 |
| 사설 클라우드의 최대 vSphere 클러스터 수 | 21 |
| 새 vSphere 클러스터에 있는 최소 노드 수 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 인프라 유지 관리

VMware 인프라의 구성을 변경 해야 하는 경우가 있습니다. 현재는 1-2 개월 마다 이러한 간격이 발생할 수 있지만 시간이 지남에 따라 빈도가 저하 될 것으로 예상 됩니다. 이러한 유형의 유지 관리는 일반적으로 CloudSimple 서비스의 정상적인 사용을 중단 하지 않고 수행할 수 있습니다. VMware 유지 관리 간격 동안 다음 서비스는 영향 없이 계속 작동 합니다.

* VMware management 평면 및 응용 프로그램
* vCenter 액세스
* 모든 네트워킹 및 저장소
* 모든 Azure 트래픽

## <a name="updates-and-upgrades"></a>업데이트 및 업그레이드

CloudSimple은 사설 클라우드에서 VMware 소프트웨어 (ESXi, vCenter, PSC 및 NSX)의 수명 주기 관리를 담당 합니다.

소프트웨어 업데이트는 다음과 같습니다.

* **패치**. VMware에서 릴리스된 보안 패치 또는 버그 수정
* **업데이트**. VMware stack 구성 요소의 부 버전 변경입니다.
* **업그레이드**. VMware stack 구성 요소의 주 버전 변경

CloudSimple은 VMware에서 사용할 수 있게 되는 즉시 중요 한 보안 패치를 테스트 합니다. SLA 당 CloudSimple은 1 주일 내 사설 클라우드 환경에 대 한 보안 패치를 롤업 합니다.

CloudSimple은 VMware 소프트웨어 구성 요소에 분기별 유지 관리 업데이트를 제공 합니다. 새 주 버전의 VMware 소프트웨어를 사용할 수 있는 경우 CloudSimple은 고객과 연동 하 여 업그레이드를 위한 적절 한 유지 관리 기간을 조정 합니다.  

## <a name="next-steps"></a>다음 단계

* [CloudSimple 유지 관리 및 업데이트](cloudsimple-maintenance-updates.md)
