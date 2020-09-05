---
title: 개념-사설 클라우드 및 클러스터
description: Vmware의 Azure vmware 솔루션에 있는 Azure VMware 소프트웨어 정의 데이터 센터 및 vSphere 클러스터의 주요 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 06161d2ce95415ae3309d58ad18ad0d40b3782fb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752284"
---
# <a name="azure-vmware-solution-preview-private-cloud-and-cluster-concepts"></a>Azure VMware Solution Preview 사설 클라우드 및 클러스터 개념

Azure VMware 솔루션은 Azure에서 VMware 기반 사설 클라우드를 제공 합니다. 사설 클라우드는 전용 운영 체제 미 설치 호스트의 클러스터에서 빌드되고 Azure Portal를 통해 배포 및 관리 됩니다. 사설 클라우드의 클러스터는 VMware vSphere, vCenter, vSAN 및 NSX 소프트웨어로 프로 비전 됩니다. Azure VMware 솔루션 사설 클라우드 하드웨어 및 소프트웨어 배포는 Azure에서 완벽 하 게 통합 되 고 자동화 됩니다.

Azure 구독, Azure VMware 솔루션 사설 클라우드, vSAN 클러스터 및 호스트 간에는 논리적 관계가 있습니다. 다이어그램에서 단일 Azure 구독에 두 개의 사설 클라우드가 표시 됩니다. 사설 클라우드는 각자의 사설 클라우드를 포함 하는 개발 및 프로덕션 환경을 나타냅니다. 이러한 각 사설 클라우드에는 두 개의 클러스터가 있습니다. 개발 환경의 잠재적 요구를 더 적게 보여 주기 위해 용량이 낮은 호스트를 사용 하는 소규모 클러스터가 사용 됩니다. 이러한 모든 개념은 아래 섹션에 설명 되어 있습니다.

![고객 구독의 두 사설 클라우드 이미지](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>프라이빗 클라우드

사설 클라우드에는 완전 한 운영 체제 미 설치 Azure 호스트로 빌드된 vSAN 클러스터가 포함 됩니다. 각 사설 클라우드는 모두 동일한 vCenter 서버에서 관리 되는 여러 클러스터와 NSX manager를 사용할 수 있습니다. 포털, CLI 또는 PowerShell을 사용 하 여 사설 클라우드를 배포 하 고 관리할 수 있습니다. 다른 리소스와 마찬가지로 사설 클라우드는 Azure 구독 내에서 설치 및 관리 됩니다.

구독 내의 사설 클라우드 수는 확장할 수 있습니다. 처음에는 구독 당 하나의 사설 클라우드로 제한 됩니다.

## <a name="clusters"></a>클러스터

각 사설 클라우드에 vSAN 클러스터를 하나 이상 만듭니다. 사설 클라우드를 만들 때 기본적으로 하나의 클러스터가 있습니다. Azure Portal 또는 API를 사용 하 여 사설 클라우드에 추가 클러스터를 추가할 수 있습니다. 모든 클러스터의 기본 크기는 세 개의 호스트로, 3 개에서 16 개의 호스트로 확장할 수 있습니다. 클러스터에서 사용 되는 호스트 유형은 동일한 유형 이어야 합니다. 호스트 유형에 대해서는 다음 섹션에서 설명 합니다.

평가판 클러스터는 평가용으로 제공 되며, 사설 클라우드 당 세 개의 호스트 및 단일 평가판 클러스터로 제한 됩니다. 평가 기간 중 단일 호스트를 기준으로 평가판 클러스터의 크기를 조정할 수 있습니다.

포털 또는 API를 통해 클러스터를 만들고 삭제 하 고 크기를 조정 합니다. 여전히 vSphere NSX Manager를 사용 하 여 클러스터 구성 또는 작업의 다른 측면을 관리 합니다. 클러스터의 각 호스트에 대 한 모든 로컬 저장소는 vSAN의 제어를 받고 있습니다.

## <a name="hosts"></a>호스트

하이퍼 수렴 형, 운영 체제 미 설치 인프라 노드는 Azure VMware 솔루션 사설 클라우드 클러스터에서 사용 됩니다. 호스트의 RAM, CPU 및 디스크 용량은 아래 표에 나와 있습니다. 

| 호스트 유형              |             CPU             |   RAM(GB)   |  vSAN NVMe 캐시 계층 (TB, 원시)  |  vSAN SSD 용량 계층 (TB, 원시)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| 고가          |  듀얼 Intel 18 코어 2.3 g h z  |     576      |                3.2               |                15.20               |

클러스터를 빌드하거나 크기를 조정 하는 데 사용 되는 호스트는 격리 된 호스트 풀에서 가져옵니다. 이러한 호스트는 하드웨어 테스트를 통과 했으며 모든 데이터가 플래시 디스크에서 안전 하 게 삭제 되었습니다. 클러스터에서 호스트를 제거 하면 내부 디스크가 안전 하 게 초기화 되 고 호스트가 격리 된 호스트 풀에 배치 됩니다. 클러스터에 호스트를 추가 하면 격리 된 풀에서 삭제 된 호스트가 사용 됩니다.

## <a name="vmware-software-versions"></a>VMware 소프트웨어 버전

Azure VMware 솔루션 사설 클라우드 클러스터에서 사용 되는 현재 소프트웨어 버전의 VMware 소프트웨어는 다음과 같습니다.

| 소프트웨어              |    버전   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U2    | 
| ESXi                  |    6.7 U2    | 
| vSAN                  |    6.7 U2    |
| NSX-T                 |      2.5     |

사설 클라우드의 모든 새 클러스터에 대해 소프트웨어의 버전은 현재 사설 클라우드에서 실행 중인 것과 일치 합니다. 고객 구독의 새 사설 클라우드의 경우 소프트웨어 스택의 최신 버전이 설치 됩니다.

Azure VMware 솔루션 플랫폼 소프트웨어에 대 한 일반적인 업그레이드 정책 및 프로세스는 업그레이드 개념 문서에 설명 되어 있습니다.

## <a name="host-maintenance-and-lifecycle-management"></a>호스트 유지 관리 및 수명 주기 관리

호스트 유지 관리 및 수명 주기 관리는 사설 클라우드 클러스터의 용량 또는 성능에 영향을 주지 않고 수행 됩니다. 자동화 된 호스트 유지 관리의 예로는 펌웨어 업그레이드, 하드웨어 복구 또는 교체 등이 있습니다.

Microsoft는 NSX Manager 및 NSX Edge와 같은 NSX-T 어플라이언스의 수명 주기 관리를 담당 합니다. Microsoft는 계층 0 게이트웨이를 만들고 북쪽 남부 라우팅을 사용 하도록 설정 하는 등의 네트워크 구성 부트스트래핑도 담당 합니다. Azure VMware 솔루션 사설 클라우드의 관리자는 네트워크 세그먼트, 분산 된 방화벽 규칙, 계층 1 게이트웨이 및 부하 분산 장치와 같은 NSX-T SDN 구성을 담당 합니다.

> [!IMPORTANT]
> Azure VMware 솔루션 관리자는 NSX-T Edge 또는 계층 0 게이트웨이의 구성을 수정 하면 안 됩니다. 이로 인해 서비스가 손실 될 수 있습니다.

## <a name="backup-and-restoration"></a>백업 및 복원

사설 클라우드 vCenter 및 NSX 구성은 매시간 백업 됩니다. 백업은 3 일 동안 유지 됩니다. 백업에서의 복원은 Azure Portal의 서비스 요청을 통해 요청 됩니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 [네트워킹 및 연결 간 개념](concepts-networking.md)을 학습 하는 것입니다.

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

