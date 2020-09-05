---
title: FAQ - Avere vFXT for Azure
description: 이러한 질문과 대답을 사용 하 여 Azure에 대 한 Avere vFXT 요구 사항을 충족 하는지 결정 합니다. Azure의 Avere vFXT가 다른 Azure 구성 요소와 함께 작동 하는 방법을 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: c6459e2daf17772b28ee53289754deb33b4823ae
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272694"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure FAQ

이 문서에서는 Avere vFXT for Azure가 사용자의 요구 사항에 적합한지 판단하는 데 도움이 되는 질문과 답변을 참조할 수 있습니다. Avere vFXT에 대한 기본 정보를 참조하고, 다른 Azure 구성 요소 및 외부 공급업체의 제품에 Avere vFXT가 어떻게 작동하는지 이해할 수 있습니다.

## <a name="general"></a>일반

### <a name="what-is-avere-vfxt-for-azure"></a>Avere vFXT for Azure란?

Avere vFXT for Azure는 중요한 워크로드를 효율적으로 처리하기 위해 Azure Compute에서 활성 데이터를 캐시하는 고성능 파일 시스템입니다.

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT는 스토리지 솔루션인가요?

아니요. Avere vFXT for Azure는 EMC 또는 NetApp NAS 또는 Azure blob 컨테이너와 같은 저장소 환경에 연결 되는 파일 시스템 *캐시* 입니다. Avere vFXT는 클라이언트의 데이터 요청을 간소화하고, 규모와 시간 경과에 따라 성능을 향상하기 위해 제공되는 데이터를 캐시합니다. Avere vFXT 자체는 데이터를 저장하지 않습니다. 백그라운드에서 저장된 데이터의 양에 대한 정보가 없습니다.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT는 계층화 솔루션인가요?

Azure 용 Avere vFXT는 핫 및 쿨 계층 간에 데이터를 자동으로 계층화 하지 않습니다.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>환경이 Avere vFXT에 적합한지 확인하려면 어떻게 할까요?

이 질문에 답변하려면 워크로드가 캐시 가능한지 자문해보는 것이 좋습니다. 즉, 워크로드의 읽기/쓰기 비율이 높은지 따져보는 것입니다. 80/20 또는 70/30 읽기/쓰기 비율을 예로 들 수 있습니다.

많은 수의 Azure 가상 머신에서 실행되는 파일 기반 분석 파이프라인이 있고 다음 조건 중 하나 이상이 충족되는 경우 Avere vFXT for Azure를 사용하는 것이 좋습니다.

* 긴 파일 액세스 시간(요구 사항에 따라 수십 밀리초 또는 초)으로 인해 전체 성능이 느리거나 일관되지 않습니다. 이 대기 시간은 고객에게 허용되지 않습니다.

* 처리하는 데 필요한 데이터는 WAN 환경의 맨 끝에 있으며, 해당 데이터는 영구적으로 이동할 수 없습니다. 데이터는 다른 Azure 지역이나 고객 데이터 센터에 있을 수 있습니다.

* 많은 클라이언트, 예를 들어 HPC(고성능 컴퓨팅) 클러스터에서 데이터를 요청하고 있습니다. 동시 요청 수가 많으면 대기 시간이 늘어날 수 있습니다.

* 고객이 Azure 가상 머신에서 현재 파이프라인을 “있는 그대로” 실행하려고 하며, 확장성을 위해 POSIX 기반 공유 스토리지(또는 캐싱) 솔루션을 필요로 합니다. Avere vFXT for Azure를 사용하면 Azure Blob Storage를 기본적으로 호출하도록 작업 파이프라인을 다시 설계할 필요가 없습니다.

* HPC 애플리케이션은 NFSv3 클라이언트를 기반으로 합니다. (일부 경우에는 SMB 2.1 클라이언트를 사용할 수 있지만 성능이 제한됩니다.)

다음 다이어그램은이 질문에 대답 하는 데 도움이 될 수 있습니다. 워크플로는 오른쪽 위에 가까울수록 Avere vFXT for Azure 캐싱 솔루션이 사용자 환경에 적합 한 것일 가능성이 높습니다.

![수천 개의 클라이언트가 포함 된 읽기-대량 로드가 Avere vFXT에 더 적합 함을 보여 주는 그래프 다이어그램](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Avere vFXT 솔루션이 가장 적합한 클라이언트의 규모는 어떻게 되나요?

Avere vFXT 캐시 솔루션은 수백, 수천 또는 수만 개의 컴퓨팅 코어를 처리하도록 빌드되었습니다. 간단한 작업을 실행하는 몇 개의 머신이 있는 경우 Avere vFXT는 적합한 솔루션이 아닙니다.

일반적인 Avere vFXT 고객은 약 1,000개의 CPU 코어에서 시작하는 까다로운 워크로드를 실행합니다. 이러한 환경은 50,000개 이상의 코어를 사용할 수 있습니다. Avere vFXT는 확장할 수 있으므로 더 많은 처리량 또는 더 많은 IOPS를 요구하면서 워크로드가 증가함에 따라 이를 지원하기 위해 노드를 추가할 수 있습니다.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Avere vFXT 환경에서 저장할 수 있는 데이터의 양은 어떻게 되나요?

Azure에 대 한 Avere vFXT는 캐시입니다. 특별히 데이터를 저장하지 않습니다. RAM과 SSD의 조합을 사용하여 캐시된 데이터를 저장합니다. 데이터는 백 엔드 스토리지 시스템(예: NetApp NAS 시스템 또는 Blob 컨테이너)에 영구적으로 저장됩니다. Avere vFXT 시스템에는 백그라운드에서 저장된 데이터 양에 대한 정보가 없습니다. Avere vFXT는 클라이언트가 요청하는 해당 데이터의 하위 집합만 캐시합니다.  

### <a name="what-regions-are-supported"></a>지원되는 지역은 어떻게 되나요?

Avere vFXT for Azure는 소 버린 지역 (중국, 독일)을 제외 하 고 모든 지역에서 지원 됩니다. 사용하려는 지역에서 Avere vFXT 클러스터를 만드는 데 필요한 대량의 컴퓨팅 코어와 VM 인스턴스를 지원할 수 있는지 확인합니다.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Avere vFXT 관련 지원을 받으려면 어떻게 할까요?

전문 지원 직원 그룹은 Avere vFXT for Azure에 대 한 도움말을 제공 합니다. [시스템 지원 받기](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)의 지침에 따라 Azure Portal에서 지원 티켓을 여세요.

### <a name="is-avere-vfxt-highly-available"></a>Avere vFXT는 고가용성 시스템인가요?

예, Avere vFXT는 독점적인 HA 솔루션으로 실행됩니다.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT for Azure에서 다른 클라우드 서비스도 지원하나요?

예, 고객은 둘 이상의 클라우드 공급자를 Avere vFXT 클러스터에 사용할 수 있습니다. AWS S3 표준 버킷, Google Cloud Services 표준 버킷 및 Azure Blob 컨테이너를 지원합니다.

> [!NOTE]
> 소프트웨어 요금은 AWS 또는 Google Cloud storage와 함께 Avere vFXT을 사용 하는 데 적용 됩니다. Azure blob storage 사용에 대 한 추가 소프트웨어 요금은 없습니다.

## <a name="technical-compute"></a>기술: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Avere vFXT 환경의 “모습”을 설명할 수 있나요?

Avere vFXT는 여러 개의 Azure 가상 머신으로 클러스터된 어플라이언스입니다. Python 라이브러리에서 클러스터 만들기, 삭제 및 수정을 처리합니다. 자세한 내용은 [Aere vFXT for Azure란?](avere-vfxt-overview.md)을 참조하세요.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Avere vFXT는 어떤 Azure 가상 머신의 종류에서 실행되나요?  

Avere vFXT for Azure 클러스터는 Microsoft Azure E32s_v3 가상 컴퓨터를 사용 합니다.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Avere vFXT 환경의 크기를 조정할 수 있나요?

Avere vFXT 클러스터는 3개의 가상 머신 노드만큼 작거나 24개의 노드만큼 클 수 있습니다. 클러스터에 9개가 넘는 노드가 필요한 경우 Azure 기술 지원 팀에 문의하여 계획을 수립하세요. 노드 수가 많을수록 더 큰 배포 아키텍처가 필요합니다.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Avere vFXT 환경은 "자동으로 크기 조정"할 수 있나요?

아니요. 클러스터 크기는 확장하거나 축소할 수 있지만, 클러스터 노드를 추가하거나 제거하는 작업은 수동 단계입니다.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Avere vFXT 클러스터를 가상 머신 확장 집합으로 실행할 수 있나요?

Avere vFXT는 가상 머신 확장 집합 배포를 지원하지 않습니다. 몇 가지 기본 제공 가용성 지원 메커니즘은 클러스터에 참여하는 원자성 VM에 대해서만 설계되었습니다.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>우선 순위가 낮은 VM에서 Avere vFXT 클러스터를 실행할 수 있나요?

아니요, 시스템에는 안정적인 가상 머신 집합이 필요합니다.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>컨테이너에서 Avere vFXT 클러스터를 실행할 수 있나요?

아니요, Avere vFXT는 독립적인 애플리케이션으로 배포해야 합니다.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Avere vFXT VM은 내 컴퓨팅 할당량에 불리하게 작용하나요?

예. 지역에 클러스터를 지원할 수 있을 만큼 충분한 할당량이 있는지 확인합니다.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>다른 가용성 영역에서 Avere vFXT 클러스터 머신을 실행할 수 있나요?

아니요. Avere vFXT의 고가용성 모델은 현재 서로 다른 가용성 영역에 있는 개별 Avere vFXT 클러스터 멤버를 지원하지 않습니다.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Avere vFXT 가상 머신을 복제할 수 있나요?

아니요, 지원되는 Python 스크립트를 사용하여 Avere vFXT 클러스터에서 노드를 추가하거나 제거해야 합니다. 자세한 내용은 [Avere vFXT 클러스터 관리](avere-vfxt-manage-cluster.md)를 참조하세요.

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>내 로컬 환경에서 실행할 수 있는 소프트웨어의 "VM" 버전이 있나요?

아니요, 시스템은 클러스터된 어플라이언스로 제공되며 특정 가상 머신 유형에서 테스트됩니다. 이 제한을 통해 고객이 일반적인 Avere vFXT 워크플로의 고성능 요구 사항을 지원할 수 없는 시스템을 만들지 않도록 방지할 수 있습니다.

## <a name="technical-disks"></a>기술: 디스크

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Azure VM에 지원되는 디스크 유형은 어떻게 되나요?

Avere vFXT for Azure는 1TB 또는 4TB 프리미엄 SSD 구성을 사용할 수 있습니다. 프리미엄 SSD 구성은 여러 개의 관리 디스크로 배포할 수 있습니다.

### <a name="does-the-cluster-support-unmanaged-disks"></a>클러스터에서 관리되지 않는 디스크를 지원하나요?

아니오, 클러스터에는 관리 디스크가 필요합니다.

### <a name="does-the-system-support-local-attached-ssds"></a>시스템에서 로컬(연결형) SSD를 지원하나요?

Avere vFXT for Azure는 현재 로컬 SSD를 지원하지 않습니다. Avere vFXT에 사용되는 디스크는 종료 및 다시 시작할 수 있어야 하지만, 이 구성에서 로컬로 연결된 SSD만 종료할 수 있습니다.

### <a name="does-the-system-support-ultra-ssds"></a>시스템에서 초고속 SSD를 지원하나요?

아니요, 시스템은 프리미엄 SSD 구성만 지원합니다.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>사용 간에 캐시 콘텐츠를 유지하기 위해 프리미엄 SSD를 분리한 다음, 나중에 다시 연결할 수 있나요?

SSD 분리 및 다시 연결은 지원되지 않습니다. 원본의 메타데이터 또는 파일 내용이 사용 간에 변경되어 데이터 무결성 문제가 발생할 수 있습니다.

### <a name="does-the-system-encrypt-the-cache"></a>시스템에서 캐시를 암호화하나요?

데이터는 디스크에서 스트라이핑되지만 암호화되지는 않습니다. 그러나 디스크 자체는 암호화할 수 있습니다. 자세한 내용은 [Azure의 가상 머신에서 보안 적용 및 정책 사용](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption)을 참조하세요.

## <a name="technical-networking"></a>기술: 네트워킹

### <a name="what-network-is-recommended"></a>권장되는 네트워크는 어떻게 되나요?

Avere vFXT를 사용 하 여 온-프레미스 저장소를 사용 하는 경우 저장소와 클러스터 간의 네트워크 연결을 1Gbps 이상으로 설정 해야 합니다. 데이터의 양이 작고 작업을 실행하기 전에 데이터를 클라우드에 복사하려는 경우 VPN 연결만으로 충분할 수 있습니다.

> [!TIP]
> 네트워크 링크가 느리기 때문에 초기 "콜드" 읽기가 느려집니다. 느린 읽기는 작업 파이프라인의 대기 시간을 늘립니다.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Avere vFXT는 컴퓨팅 클러스터와 다른 가상 네트워크에서 실행할 수 있나요?

예, 다른 가상 네트워크에서 Avere vFXT 시스템을 만들면 됩니다. 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md)을 참조하세요.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT에는 자체 서브넷이 필요한가요?

예. Avere vFXT는 엄격 하 게 고가용성 (HA) 클러스터로 실행 되며 여러 IP 주소가 작동 해야 합니다. 클러스터가 자체 서브넷에 있으면 IP 주소가 충돌할 수 있는 위험을 방지하여 설치 및 정상 작동에 문제가 발생할 수 있습니다. 클러스터의 서브넷은 IP 주소가 겹치지 않는 한 다른 리소스에서 사용 하는 가상 네트워크 내에 있을 수 있습니다.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Avere vFXT는 InfiniBand에서 실행할 수 있나요?

아니요, Avere vFXT는 이더넷/IP만 사용합니다.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Avere vFXT에서 온-프레미스 NAS 환경에 액세스하려면 어떻게 할까요??

Avere vFXT 환경은 네트워크 게이트웨이 또는 VPN을 통해 고객 데이터 센터(및 백 엔드)로 라우팅되는 액세스가 필요하다는 점에서 다른 Azure VM과 유사합니다. 환경에서 Azure ExpressRoute 연결을 사용할 수 있는 경우 이 연결을 사용하는 것이 좋습니다.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Avere vFXT에 대한 대역폭 요구 사항은 무엇인가요?

전체 대역폭 요구 사항은 다음 두 가지 요인에 따라 달라집니다.

* 원본에서 요청되는 데이터의 양
* 초기 데이터 로드 중의 대기 시간에 대한 클라이언트 시스템의 허용 오차  

대기 시간에 민감한 환경에서는 최소 연결 속도가 1Gbps인 파이버 솔루션을 사용해야 합니다. 사용 가능한 경우 ExpressRoute를 사용하세요.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>공용 IP 주소를 사용하여 Avere vFXT를 실행할 수 있나요?

아니요, Avere vFXT는 모범 사례를 적용하여 보호된 네트워크 환경 내에서 작동해야 합니다.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>클러스터의 가상 네트워크에서 인터넷 액세스를 제한할 수 있나요?

일반적으로 필요에 따라 가상 네트워크에서 추가 보안을 구성할 수 있지만 일부 제한 사항은 클러스터의 작업에 방해가 될 수 있습니다.

예를 들어 가상 네트워크에서 아웃 바운드 인터넷 액세스를 제한 하면 AzureCloud에 대 한 액세스를 명시적으로 허용 하는 규칙도 추가 하지 않는 한 클러스터에 문제가 발생 합니다. 이 상황은 [GitHub에 대 한 추가 설명서](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md)에 설명 되어 있습니다.

사용자 지정 보안에 대 한 도움말을 보려면 시스템에 대 한 [도움말 보기](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)에 설명 된 대로 지원 담당자에 게 문의 하세요.

## <a name="technical-back-end-storage-core-filers"></a>기술: 백 엔드 저장소 (핵심 filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>단일 Avere vFXT 환경을 지원하는 코어 파일러의 수는 어떻게 되나요?

Avere vFXT 클러스터는 최대 20개의 코어 파일러를 지원합니다.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Avere vFXT 환경에서 데이터는 어떻게 저장되나요?

Avere vFXT는 스토리지가 아닙니다. 즉, 코어 파일러라고 하는 여러 스토리지 대상에서 데이터를 읽고 쓰는 캐시입니다. Avere vFXT의 프리미엄 SSD 디스크에 저장된 데이터는 일시적이며, 결국에는 백 엔드 코어 파일러 스토리지로 플러시됩니다.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Avere vFXT에서 지원하는 코어 파일러는 어떻게 되나요?

일반적으로 Avere vFXT for Azure에서 코어 파일러로 지원하는 시스템은 다음과 같습니다.

* Dell EMC Isilon(OneFS 7.1, 7.2, 8.0 및 8.1) 
* NetApp ONTAP(Clustered Mode 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) 및 (7-Mode 7.*, 8.0-8.3)

* Azure Blob 컨테이너(로컬 중복 스토리지만 해당)
* AWS S3 버킷
* Google Cloud 버킷

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Avere vFXT에서 모든 NFS 파일러를 지원하지 않는 이유는 무엇인가요?

모든 NFS 플랫폼에서 동일한 IETF 표준이 충족되지만 실제로는 각 구현마다 고유한 특성이 있습니다. 이러한 세부 정보는 Avere vFXT에서 스토리지 시스템과 상호 작용하는 방식에 영향을 줍니다. 지원되는 시스템은 마켓플레이스에서 가장 널리 사용되는 플랫폼입니다.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT에서 프라이빗 개체 스토리지(예: SwiftStack)를 지원하나요?

Avere vFXT는 프라이빗 개체 스토리지를 지원하지 않습니다.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>지원되는 특정 스토리지 제품을 얻으려면 어떻게 해야 하나요?

지원은 현장의 요구량을 기반으로 합니다. 지정된 NAS 솔루션을 지원하는 데 충분한 수익 기반 요청이 있는 경우 이를 고려합니다. Azure 지원을 통해 요청하세요.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Azure Blob Storage는 코어 파일러로 사용할 수 있나요?

예, Avere vFXT for Azure는 블록 Blob 컨테이너를 클라우드 코어 파일러로 사용할 수 있습니다.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Blob 코어 파일러에 대한 스토리지 계정 요구 사항은 무엇인가요?

스토리지 계정은 범용 v2(GPv2) 계정이어야 하며, 로컬 중복 스토리지로만 구성해야 합니다. 지역 중복 스토리지 및 영역 중복 스토리지는 지원되지 않습니다.

저장소 계정 요구 사항에 대 한 자세한 내용은 [cloud core 필터 Azure Blob Storage](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) 를 참조 하세요.

### <a name="can-i-use-archive-blob-storage"></a>보관 Blob Storage를 사용할 수 있나요?

아니요. 보관 스토리지에 대한 SLA(서비스 수준 약정)는 Avere vFXT 시스템의 실시간 디렉터리 및 파일 액세스 요구 사항과 호환되지 않습니다.

### <a name="can-i-use-cool-blob-storage"></a>쿨 Blob Storage를 사용할 수 있나요?

쿨 계층 blob 저장소는 일반적으로 Azure core 필터의 Avere vFXT에 권장 되지 않습니다. 쿨 계층은 낮은 저장소 비용을 제공 하지만 작업 비용은 더 높습니다. 자세한 내용은 [블록 blob 가격 책정](<https://azure.microsoft.com/pricing/details/storage/blobs/>) 을 참조 하세요. 데이터에 자주 액세스 하 고 수정 하거나 삭제 하는 경우에는 핫 계층을 사용 하십시오.

[액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) 은 vFXT core 필터로 쿨 계층 저장소를 사용 하는 것이 적합할 수 있는 경우에 대 한 자세한 정보를 제공 합니다.

### <a name="how-do-i-encrypt-the-blob-container"></a>Blob 컨테이너를 암호화하려면 어떻게 할까요?

Blob 암호화는 Azure(기본) 또는 Avere vFXT 코어 파일러 수준에서 구성할 수 있습니다.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Blob 코어 파일러에 대해 사용자 고유의 암호화 키를 사용할 수 있나요?

기본적으로 데이터는 Azure Blob, Table 및 Queue Storage와 Azure Files에 대한 Microsoft 관리 키를 통해 암호화됩니다. Blob Storage 및 Azure Files의 암호화를 위해 고유 키를 가져올 수 있습니다. Avere vFXT 암호화를 사용하려는 경우 Avere 생성 키를 사용하여 로컬로 저장해야 합니다.

## <a name="purchasing"></a>구매

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Avere vFXT for Azure 라이선싱을 얻으려면 어떻게 할까요?

Avere vFXT for Azure 라이선스는 Azure Marketplace를 통해 쉽게 얻을 수 있습니다. Azure 계정에 등록한 다음, [Avere vFXT 클러스터 배포](avere-vfxt-deploy.md)의 지침에 따라 Avere vFXT 클러스터를 만듭니다.

### <a name="how-much-does-avere-vfxt-cost"></a>Avere vFXT 비용은 어떻게 되나요?

Azure에서는 Avere vFXT 클러스터 사용에 대한 추가 라이선스 요금이 없습니다. 고객은 스토리지 및 기타 Azure 사용량 요금을 부담해야 합니다.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Avere vFXT VM을 낮은 우선 순위로 실행할 수 있나요?

아니요, vFXT 클러스터에는 "항상 사용" 서비스가 필요합니다. 필요하지 않은 경우 클러스터를 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Avere vFXT for Azure를 시작하려면 다음 문서를 참조하여 사용자 고유의 시스템을 계획하고 배포하는 방법을 알아봅니다.

* [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md)
* [배포 개요](avere-vfxt-deploy-overview.md)
* [Avere vFXT 클러스터 만들기 준비](avere-vfxt-prereqs.md)
* [Avere vFXT 클러스터 배포](avere-vfxt-deploy.md)

Avere vFXT의 기능 및 사용 사례에 대해 자세히 알아보려면 [Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/)를 참조하세요.
