---
title: Avere vFXT 시스템 계획 - Azure
description: 사용자 요구에 적합 한 Azure 클러스터에 대 한 Avere vFXT를 계획 합니다. Azure Marketplace 이동 하거나 가상 머신을 만들기 전에 확인 해야 하는 질문에 대해 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: e5c33144dcebe491757dfafb2879fe51545827ce
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272793"
---
# <a name="plan-your-avere-vfxt-system"></a>Avere vFXT 시스템 계획

이 문서에서는 요구 사항에 맞게 적절 하 게 배치 하 고 크기를 조정 하는 새로운 Azure 클러스터 용 Avere vFXT를 계획 하는 방법을 설명 합니다.

Azure Marketplace로 이동 하거나 Vm을 만들기 전에 다음 정보를 고려 하세요.

* 클러스터가 다른 Azure 리소스와 상호 작용 하는 방법
* 클러스터 요소를 개인 네트워크 및 서브넷에 어디에 배치 해야 하나요?
* 사용할 백 엔드 저장소 유형 및 클러스터에서 어떻게 액세스 하나요?
* 클러스터 노드를 통해 워크플로를 지원 해야 하는 이유는 무엇 인가요?

더 알아보려면 계속 읽어 보세요.

## <a name="learn-the-components-of-the-system"></a>시스템의 구성 요소에 대해 알아보기

계획을 시작할 때 Avere vFXT for Azure system의 구성 요소를 이해 하면 도움이 될 수 있습니다.

* 클러스터 노드-클러스터가 클러스터 노드로 구성 된 세 개 이상의 Vm으로 구성 됩니다. 더 많은 노드를 통해 시스템 처리량이 증가 하 고 캐시가 커집니다.

* Cache-캐시 용량이 클러스터 노드 간에 균등 하 게 분할 됩니다. 클러스터를 만들 때 노드당 캐시 크기를 설정 합니다. 총 캐시 크기가 되도록 노드 크기가 추가 됩니다.

* 클러스터 컨트롤러-클러스터 컨트롤러는 클러스터 노드와 동일한 서브넷 내에 있는 추가 VM입니다. 컨트롤러는 클러스터를 만들고 진행 중인 관리 작업을 수행 하는 데 필요 합니다.

* 백 엔드 저장소-캐시 하려는 데이터는 하드웨어 저장소 시스템 또는 Azure Blob 컨테이너에 장기간 저장 됩니다. Azure 클러스터에 대해 Avere vFXT를 만든 후 저장소를 추가 하거나, Blob 저장소를 사용 하는 경우 클러스터를 만드는 동안 컨테이너를 추가 및 구성할 수 있습니다.

* 클라이언트-캐시 된 파일을 사용 하는 클라이언트 컴퓨터는 저장소 시스템에 직접 액세스 하는 대신 가상 파일 경로를 사용 하 여 클러스터에 연결 합니다. 자세한 내용은 [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)를 참조 하세요.

## <a name="subscription-resource-group-and-network-infrastructure"></a>구독, 리소스 그룹 및 네트워크 인프라

Avere vFXT for Azure 배포의 요소 위치를 고려합니다. 아래 다이어그램에서는 Avere vFXT for Azure 구성 요소에 대한 가능한 배치를 보여 줍니다.

![하나의 서브넷에 있는 클러스터 컨트롤러와 클러스터 VM을 보여 주는 다이어그램입니다. 서브넷 경계 주위에는 vnet 경계가 있습니다. vnet 내부에는 스토리지 서비스 엔드포인트를 나타내는 육각형이 있으며, vnet 외부의 Blob Storage에 파선 모양 화살표로 연결됩니다.](media/avere-vfxt-components-option.png)

Avere vFXT 클러스터의 네트워크 인프라를 계획 하는 경우 다음 지침을 따르세요.

* Azure 배포에 대 한 각 Avere vFXT에 대 한 새 구독을 만듭니다. 이 구독의 모든 구성 요소를 관리 합니다.

  각 배포에 새 구독을 사용 하는 이점은 다음과 같습니다.
  * 더욱 간편한 비용 추적 - 리소스, 인프라 및 컴퓨팅 주기의 모든 비용을 구독 하나에서 확인하고 감사할 수 있습니다.
  * 더욱 쉬운 정리 - 프로젝트를 마친 후에는 전체 구독을 제거할 수 있습니다.
  * 리소스 할당량의 편리한 분할-Avere vFXT 클라이언트와 클러스터를 단일 구독에 격리 하 여 가능한 리소스 제한에서 다른 중요 한 작업을 보호 합니다. 이러한 분리는 고성능 컴퓨팅 워크플로에 대해 많은 수의 클라이언트를 가져올 때 충돌을 방지 합니다.

* 클라이언트 컴퓨팅 시스템을 vFXT 클러스터와 가까운 곳에 배치합니다. 백 엔드 스토리지는 더 원격적일 수 있습니다.  

* VFXT 클러스터 및 클러스터 컨트롤러 VM을 함께 찾습니다. 특히 다음을 수행 해야 합니다.

  * 동일한 가상 네트워크에
  * 동일한 리소스 그룹에서
  * 동일한 저장소 계정 사용
  
  클러스터 만들기 템플릿은 대부분의 경우이 구성을 처리 합니다.

* 클라이언트 또는 다른 계산 리소스와의 IP 주소가 충돌 하지 않도록 클러스터는 자체 서브넷에 있어야 합니다.

* 클러스터 만들기 템플릿을 사용 하 여 리소스 그룹, 가상 네트워크, 서브넷 및 저장소 계정을 포함 하 여 클러스터에 필요한 대부분의 인프라 리소스를 만듭니다.

  이미 존재 하는 리소스를 사용 하려는 경우이 테이블의 요구 사항을 충족 하는지 확인 합니다.

  | 리소스 | 기존를 사용 하 시겠습니까? | 요구 사항 |
  |----------|-----------|----------|
  | Resource group | 예 (비어 있는 경우) | 비어 있어야 합니다.|
  | 스토리지 계정 | 클러스터를 만든 후 기존 Blob 컨테이너를 연결 하는 경우 **예** <br/>  클러스터를 만드는 동안 새 Blob 컨테이너를 만드는 경우 **아니요** | 기존 Blob 컨테이너는 비어 있어야 합니다. <br/> &nbsp; |
  | 가상 네트워크 | 예 | 새 Azure Blob 컨테이너를 만드는 경우 저장소 서비스 끝점을 포함 해야 합니다. |
  | 서브넷 | 예 | 다른 리소스를 포함할 수 없음 |

## <a name="ip-address-requirements"></a>IP 주소 요구 사항

클러스터의 서브넷에 클러스터를 지원할 수 있을 만큼 충분한 IP 주소 범위가 있는지 확인합니다.

Avere vFXT 클러스터에서 사용하는 IP 주소는 다음과 같습니다.

* 하나의 클러스터 관리 IP 주소. 이 주소는 항상 사용할 수 있도록 필요에 따라 클러스터의 노드에서 노드로 이동할 수 있습니다. 이 주소를 사용 하 여 Avere 제어판 구성 도구에 연결 합니다.
* 각 클러스터 노드:
  * 하나 이상의 클라이언트 측 IP 주소. 모든 클라이언트 지향 주소는 클러스터의 *vserver*에서 관리 하며, 필요에 따라 노드 간에 IP 주소를 이동할 수 있습니다.
  * 클러스터 통신을 위한 하나의 IP 주소
  * 하나의 인스턴스 IP 주소(VM에 할당됨)

Azure Blob storage를 사용 하는 경우 클러스터의 가상 네트워크에서 IP 주소를 요구할 수도 있습니다.  

* Azure Blob Storage 계정에는 5개 이상의 IP 주소가 필요합니다. 클러스터와 동일한 가상 네트워크에서 Blob 저장소를 찾았으면이 요구 사항을 염두에 두어야 합니다.
* 클러스터의 가상 네트워크 외부에 있는 Azure Blob storage를 사용 하는 경우 가상 네트워크 내에 저장소 서비스 끝점을 만듭니다. 끝점은 IP 주소를 사용 하지 않습니다.

클러스터와 다른 리소스 그룹에 네트워크 리소스와 Blob Storage(사용된 경우)를 배치할 수 있는 옵션이 있습니다.

## <a name="vfxt-node-size"></a>vFXT 노드 크기

클러스터 노드 역할을 하는 VM은 캐시의 요청 처리량 및 스토리지 용량을 결정합니다. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

각 vFXT 노드는 동일합니다. 즉 3개 노드 클러스터를 만들면 동일한 유형과 크기의 VM 3개가 만들어집니다.

| 인스턴스 유형 | vCPU | 메모리  | 로컬 SSD 스토리지  | 최대 데이터 디스크 수 | 캐시되지 않은 디스크 처리량 | NIC(개수) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256GiB | 512GiB  | 32 | 51,200IOPS <br/> 768MBps | 16,000MBps(8)  |

노드당 디스크 캐시의 크기를 구성할 수 있으며, 범위는 1,000-8,000GB입니다. 노드당 4tb는 Standard_E32s_v3 노드에 대해 권장 되는 캐시 크기입니다.

이러한 Vm에 대 한 자세한 내용은 메모리 액세스에 최적화 된 [가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) Microsoft Azure 설명서를 참조 하세요.

## <a name="account-quota"></a>계정 할당량

사용되는 모든 컴퓨팅 또는 클라이언트 시스템뿐만 아니라 Avere vFXT 클러스터도 실행할 수 있는 용량이 구독에 있는지 확인합니다. 자세한 내용은 [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)을 참조하세요.

## <a name="back-end-data-storage"></a>백 엔드 데이터 스토리지

백 엔드 저장소 시스템은 클러스터의 캐시에 파일을 제공 하 고 캐시에서 변경 된 데이터를 수신 하기도 합니다. 작업 집합을 새 Blob 컨테이너 또는 기존 저장소 시스템 (클라우드 또는 하드웨어)에 장기간 저장할 것인지 결정 합니다. 이러한 백 엔드 저장소 시스템을 *핵심 필터*이라고 합니다.

### <a name="hardware-core-filers"></a>하드웨어 코어 필터

클러스터를 만든 후 vFXT 클러스터에 하드웨어 저장소 시스템을 추가 합니다. 클러스터의 서브넷에서 저장소 시스템에 연결할 수 있으면 온-프레미스 시스템을 비롯 한 다양 한 인기 있는 하드웨어 시스템을 사용할 수 있습니다.

Avere vFXT 클러스터에 기존 스토리지 시스템을 추가하는 방법에 대한 자세한 지침은 [스토리지 구성](avere-vfxt-add-storage.md)을 참조하세요.

### <a name="cloud-core-filers"></a>클라우드 핵심 필터

Avere vFXT for Azure 시스템은 백 엔드 저장소에 빈 Blob 컨테이너를 사용할 수 있습니다. 클러스터에 추가 될 때 컨테이너는 비어 있어야 합니다. vFXT 시스템은 기존 데이터를 유지할 필요 없이 개체 저장소를 관리할 수 있어야 합니다.

> [!TIP]
> 백 엔드에 대해 Azure Blob storage를 사용 하려는 경우 vFXT 클러스터를 만드는 과정의 일부로 새 컨테이너를 만듭니다. 클러스터 생성 템플릿은 클러스터를 사용할 수 있게 되는 즉시 사용할 수 있도록 새 Blob 컨테이너를 만들고 구성할 수 있습니다. 컨테이너를 나중에 추가 하는 것은 더 복잡 합니다.
>
> 자세한 내용은 [Avere vFXT for Azure만들기](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure)를 참조하세요.

빈 Blob 저장소 컨테이너를 핵심 필터 추가한 후에는 클러스터를 통해 데이터를 복사할 수 있습니다. 병렬 다중 스레드 복사 메커니즘을 사용 합니다. 클라이언트 머신과 Avere vFXT 캐시를 사용하여 데이터를 클러스터의 새 컨테이너에 효율적으로 복사하는 방법을 알아보려면 [vFXT 클러스터로 데이터 이동](avere-vfxt-data-ingest.md)을 참조하세요.

## <a name="cluster-access"></a>클러스터 액세스

Avere vFXT for Azure 클러스터는 프라이빗 서브넷에 있으며 공용 IP 주소를 포함하지 않습니다. 클러스터 관리 및 클라이언트 연결을 위해 개인 서브넷에 액세스 하는 방법이 있어야 합니다.

액세스 옵션은 다음과 같습니다.

* 점프 호스트-개인 네트워크 내의 개별 VM에 공용 IP 주소를 할당 하 고이를 사용 하 여 클러스터 노드에 대 한 TLS 터널을 만듭니다.

  > [!TIP]
  > 클러스터 컨트롤러에서 공용 IP 주소를 설정하는 경우 해당 주소를 점프 호스트로 사용할 수 있습니다. 자세한 내용은 [클러스터 컨트롤러(점프 호스트)](#cluster-controller-as-jump-host)를 참조하세요.

* VPN (가상 사설망)-Azure 및 회사 네트워크의 개인 네트워크 간에 지점 및 사이트 간 또는 사이트 간 VPN을 구성 합니다.

* Azure ExpressRoute - ExpressRoute 파트너를 통해 프라이빗 연결을 구성합니다.

이러한 옵션에 대한 자세한 내용은 [인터넷 통신 관련 Azure Virtual Network 설명서](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)를 참조하세요.

### <a name="cluster-controller-as-jump-host"></a>클러스터 컨트롤러(점프 호스트)

클러스터 컨트롤러의 공용 IP 주소를 설정하는 경우에는 해당 주소를 점프 호스트로 사용하여 프라이빗 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다. 그러나 컨트롤러에는 클러스터 노드 수정 권한이 있으므로 이와 같이 연결하면 보안상 다소 위험해집니다.

공용 IP 주소가 있는 컨트롤러에 대 한 보안을 개선 하기 위해 배포 스크립트는 포트 22로만 인바운드 액세스를 제한 하는 네트워크 보안 그룹을 자동으로 만듭니다. 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 컴퓨터의 연결만 허용)하면 시스템을 추가로 보호할 수 있습니다.

클러스터를 만들 때 클러스터 컨트롤러에서 공용 IP 주소를 만들지 여부를 결정할 수 있습니다.

* **새 가상 네트워크** 또는 **새 서브넷**을 만드는 경우 클러스터 컨트롤러에 **공용** IP 주소가 할당 됩니다.
* 기존 가상 네트워크 및 서브넷을 선택 하는 경우 클러스터 컨트롤러에는 **개인** IP 주소만 있습니다.

## <a name="vm-access-roles"></a>VM 액세스 역할

Azure는 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/index.yml) 를 사용 하 여 특정 작업을 수행 하도록 클러스터 vm에 권한을 부여 합니다. 예를 들어 클러스터 컨트롤러에는 클러스터 노드 Vm을 만들고 구성 하기 위한 권한 부여가 필요 합니다. 클러스터 노드는 다른 클러스터 노드에 IP 주소를 할당 하거나 다시 할당할 수 있어야 합니다.

Avere vFXT virtual machines에는 두 개의 기본 제공 Azure 역할이 사용 됩니다.

* 클러스터 컨트롤러는 기본 제공 역할인 [Avere 기여자](../role-based-access-control/built-in-roles.md#avere-contributor)를 사용 합니다.
* 클러스터 노드는 기본 제공 역할인 [Avere 연산자](../role-based-access-control/built-in-roles.md#avere-operator)를 사용 합니다.

Avere vFXT 구성 요소에 대 한 액세스 역할을 사용자 지정 해야 하는 경우 사용자 고유의 역할을 정의 하 고 만들 때 Vm에 할당 해야 합니다. Azure Marketplace에서는 배포 템플릿을 사용할 수 없습니다. 시스템에 대 한 [도움말 보기](avere-vfxt-open-ticket.md)에 설명 된 대로 Azure Portal에서 티켓을 열어 Microsoft 고객 서비스 및 지원 센터에 문의 하세요.

## <a name="next-steps"></a>다음 단계

[배포 개요](avere-vfxt-deploy-overview.md) 는 Azure 시스템에 대 한 Avere vFXT를 만들고 데이터를 서비스할 준비를 하는 데 필요한 단계에 대 한 전체적인 그림 보기를 제공 합니다.
