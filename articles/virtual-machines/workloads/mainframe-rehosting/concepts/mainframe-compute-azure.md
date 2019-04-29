---
title: Azure Virtual Machines에 메인프레임 계산 이동
description: 마이그레이션 및 IBM z14 응용 프로그램을 현대화 할 수 있도록 azure 리소스와 비교할 때 특성과 메인프레임 용량을 계산 합니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485649"
---
# <a name="move-mainframe-compute-to-azure"></a>메인프레임 계산을 Azure로 이동

메인프레임 높은 안정성 및 가용성에 대해 있고 대부분의 기업에서는의 신뢰할 수 있는 핵심을 계속 합니다. 해당 하는 거의 무제한 확장 기능도 있고 컴퓨팅 기능에도 종종 간주 됩니다. 그러나 일부 기업 사항이 사용 가능한 가장 큰 메인프레임의 기능을 초과 한다면 합니다. 것이 있다면 Azure 민첩성, 고객층 및 인프라 비용 절감을 제공 합니다.

메인프레임 워크 로드를 Microsoft Azure에서 실행 하려면 메인프레임의 azure 기능 비교를 계산 하는 방법을 알아야 합니다. IBM z14 메인프레임의 (이 문서의 작성일 기준 최신 모델)에 따라이 문서에서는 하는 방법을 알려줍니다 Azure에서 유사한 결과 가져옵니다.

시작 하려면 나란히 환경을 고려 합니다. 다음 그림에는 Azure 호스팅 환경에 응용 프로그램을 실행 하는 메인프레임 환경을 비교 합니다.

![Azure 서비스와 비교할 수 있는 에뮬레이션 환경 제품 지원 하 고 마이그레이션 간소화](media/mainframe-compute-azure.png)

메인프레임의 온라인 트랜잭션 처리 (OLTP) 시스템 수백만 명의 사용자에 대 한 업데이트를 처리 하는 자주 사용 됩니다. 종종 이러한 응용 프로그램 트랜잭션 처리, 화면 처리 및 형식 항목에 대 한 소프트웨어를 사용합니다. 고객 정보 제어 시스템 (CICS), 정보 관리 시스템 (IMS) 또는 인터페이스 패키지 TIP (Transaction)를 사용할 수 있습니다.

그림에서 알 수 있듯이, Azure에는 TPM 에뮬레이터 CICS 및 IMS 워크 로드를 처리할 수 있습니다. 작업 제어 언어 (JCL)의 역할을 수행 하는 Azure에서 일괄 처리 시스템 에뮬레이터. 메인프레임 데이터는 Azure SQL Database와 같은 Azure 데이터베이스에 마이그레이션됩니다. 시스템 관리에 대 한 azure 서비스 또는 Azure Virtual Machines에서 호스트 하는 다른 소프트웨어를 사용할 수 있습니다.

## <a name="mainframe-compute-at-a-glance"></a>한 눈에 메인프레임 계산

Z14 메인프레임 프로세서에서 최대 4 개 탭에 정렬 됩니다 *서랍*합니다. A *서랍* 프로세서 및 칩셋 클러스터 하기만 하면 됩니다. 각 서랍 여섯 개의 active 중앙 프로세서 (CP) 칩을 가질 수 있습니다 있고 각 CP 10 시스템 컨트롤러 (SC) 칩입니다. Intel x86 용어에서 6 개의 소켓 서랍, 소켓 당 10 개의 코어 및 4 서랍 작업 수 있습니다. 이 아키텍처 제공 24 소켓 및 240 개 코어, 최대, 한 z14 대략적으로 동일 합니다.

빠른 z14 CP에 5.2 GHz 클럭 속도입니다. 일반적으로 z14 상자에서 모든 CPs로 전달 됩니다. 필요에 따라 활성화 되어 있습니다. 고객은 일반적으로 실제 사용량에도 불구 하 고 월별 계산 시간이 4 시간 이상에 대 한 요금이 청구 됩니다.

다음 형식 중 하나로 메인프레임 프로세서를 구성할 수 있습니다.

- 일반적인 용도 (GP) 프로세서
- Z 시스템 통합 정보 프로세서 (zIIP)
- Linux (IFL) 프로세서에 대 한 통합된 기능
- 시스템 지원 프로세서 (SAP)
- 통합된 기능 결합 (ICF) 프로세서

## <a name="scaling-mainframe-compute-up-and-out"></a>크기 조정 메인프레임 계산 및 수직 확장

IBM 메인프레임 최대 240 개 코어 (단일 시스템에 대 한 현재 z14 크기)의 크기를 조정 하는 기능을 제공 합니다. 또한 IBM 메인프레임 결합 기능 (CF) 라는 기능을 통해 확장할 수 있습니다. CF 여러 메인프레임 시스템을 같은 데이터를 동시에 액세스할 수 있습니다. CF, 클러스터의 메인프레임 병렬 Sysplex 기술 그룹 메인프레임 프로세서를 사용합니다. 이 가이드에 기록 될 때 병렬 Sysplex 기능은 64 프로세서의 32 그룹화를 지원 합니다. 2,048 프로세서 최대 계산 용량을 확장 하려면이 방식으로 그룹화 할 수 있습니다.

CF는 계산 클러스터를 직접 액세스를 사용 하 여 데이터를 공유할 수 있습니다. 잠금 정보를 캐시 정보 및 공유 데이터 리소스 목록에 사용 됩니다. 하나 이상의 CFs를 사용 하 여 병렬 Sysplex는 생각할 수 있습니다는 "공유" 모든 항목으로 스케일 아웃 계산 클러스터. 이러한 기능에 대 한 자세한 내용은 참조 하세요. [IBM Z에 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) IBM 웹 사이트입니다.

응용 프로그램 스케일 아웃 성능과 고가용성을 제공 하 이러한 기능을 사용할 수 있습니다. CICS 병렬 Sysplex을 사용 하 여 CF를 사용 하 여는 방법에 대 한 내용은 다운로드는 [IBM CICS 및 결합 기능: 향상 된 기능](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) 레드북 합니다.

## <a name="azure-compute-at-a-glance"></a>한 눈에 azure 계산

일부 사용자는 실수로 Intel 기반 서버 메인프레임 만큼 강력 하지는 생각 합니다. 그러나 새 코어 밀도, Intel 기반 시스템에 훨씬 메인프레임으로 용량을 계산 합니다. 이 섹션에서는 컴퓨팅 및 저장소에 대 한 Azure 인프라-as a service (IaaS) 옵션을 설명 합니다. Azure 플랫폼-as a service (PaaS) 옵션도 제공 하지만이 문서에서는 비슷한 메인프레임 용량을 제공 하는 IaaS 선택 항목에 중점을 둡니다.

Azure Virtual Machines는 다양 한 유형과 크기의에서 계산 능력을 제공 합니다. Azure에서 가상 CPU (vCPU) 메인프레임에서 코어로 약와 같습니다.

현재 Azure 가상 컴퓨터의 범위 크기를 1에서 128 개의 Vcpu를 제공 합니다. 가상 머신 (VM) 형식은 특정 워크 로드에 최적화 됩니다. 예를 들어, 다음 목록은 (이 문서의 작성 시점 현재 현재) VM 유형 및 권장 되는 용도:

| 크기     | 유형 및 설명                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D 시리즈 | 3.5 GHz 클럭 속도 최대 64 vCPU와 범용                           |
| E 시리즈 | 최대 64 개의 Vcpu를 사용 하 여 액세스에 최적화 된 메모리                                                 |
| F 시리즈 | 최대 64 개의 Vcpu와 3..7 GHz 클럭 속도 사용 하 여 최적화 된 계산                       |
| H 시리즈 | 고성능 컴퓨팅 (HPC) 응용 프로그램에 대 한 최적화                          |
| L 시리즈 | 같은 NoSQL 데이터베이스에서 지 원하는 처리량이 높은 응용 프로그램에 대 한 액세스에 최적화 된 저장소 |
| M 시리즈 | 최대 128 개의 Vcpu를 사용 하 여 가장 큰 계산 및 메모리 최적화 Vm                        |

사용 가능한 Vm에 대 한 자세한 내용은 참조 하세요 [가상 머신 시리즈](https://azure.microsoft.com/pricing/details/virtual-machines/series/)합니다.

Z14 메인프레임 최대 240 개 코어를 가질 수 있습니다. 그러나 z14 메인프레임 단일 응용 프로그램 또는 워크 로드에 대 한 모든 코어를 거의 사용합니다. 대신, 메인프레임을 논리적 파티션으로 (LPARs) 워크 로드 분리 있고는 LPARs 등급-MIPS (수백만 개의 초당 지침) 또는 MSU (백만 서비스 단위)입니다. Azure, MIPS (또는 MSU) 비율 등급에 메인프레임 워크 로드를 실행 하는 데 필요한 비교 가능한 VM 크기를 확인 합니다.

다음은 일반적인 예측입니다.

-   VCPU 당 150 MIPS

-   프로세서 당 1,000 MIPS

LPAR에서 지정된 된 워크 로드에 대 한 올바른 VM 크기를 확인 하려면 먼저 워크 로드에 대 한 VM을 최적화 합니다. 그런 다음 필요한 Vcpu 수를 결정 합니다. 일반적인 예상치는 vCPU 당 150 MIPS입니다. 이 예상 값에 따라 예를 들어 16 Vcpu 사용 하 여 F 시리즈 VM을 쉽게 지원할 수는 LPAR 2,400 MIPS를 사용 하 여에서 들어오는 IBM Db2 워크 로드에 있습니다.

## <a name="azure-compute-scale-up"></a>Azure 계산 규모

M 시리즈 Vm (이 문서가 작성 된 시간)에 최대 128 개의 Vcpu를 확장할 수 있습니다. M 시리즈 VM 19,000 MIPS 약 매월 vCPU 당 150 MIPS의 일반적인 예상치를 사용 합니다. 프로세서당는 메인프레임 MIPS 1,000에 대 한 MIP 예측에 대 한 일반 규칙입니다. Z14 메인프레임 최대 24 개의 프로세서를 제공 하 고 단일 메인프레임 시스템에 대 한 연간 24,000 MIPS 약 제공 수 있습니다.

가장 큰 단일 z14 메인프레임 Azure에서 사용할 수 있는 가장 큰 VM 보다 약 5,000 MIPS에 있습니다. 아직 워크 로드 배포 되는 방식을 비교 하는 것이 반드시 합니다. 메인프레임 시스템에 있는 경우 응용 프로그램 및 관계형 데이터베이스, 일반적으로 배포한 동일한 물리적 메인프레임에서-각각 자체 LPAR 합니다. Azure에서 동일한 솔루션에서 하나의 VM을 사용 하 여 응용 프로그램 및 데이터베이스에 대 한 별도, 적절 한 크기의 VM에 대 한 배포 종종 됩니다.

예를 들어 M64 vCPU 시스템이 응용 프로그램을 지 원하는 M96 vCPU를 데이터베이스에 사용 되 고 약 150 개의 Vcpu 필요한 경우-또는 다음 그림과 같이 연간 24,000 MIPS 약 합니다.

![연간 24,000 MIPS의 워크 로드 배포 비교](media/mainframe-compute-mips.png)

LPARs 개별 Vm에 마이그레이션하는 것이 좋습니다. 그런 다음 Azure 쉽게 규모 확장 단일 메인프레임 시스템에 배포 되는 대부분의 응용 프로그램에 필요한 크기입니다.

## <a name="azure-compute-scale-out"></a>Azure 계산 확장

Azure 기반 솔루션의 장점 중 하나는 확장 기능입니다. 거의 무제한 크기 조정 하면 응용 프로그램에 사용할 수 있는 용량을 계산합니다. Azure는 계산 능력을 확장 하는 여러 메서드를 지원 합니다.

- **클러스터에서 부하 분산.** 이 시나리오에서는 응용 프로그램이 사용할 수는 [부하 분산 장치](/azure/load-balancer/load-balancer-overview) 또는 클러스터의 여러 Vm 간에 워크 로드 분산을 위해 resource manager. 더 많은 계산 용량이 필요, 경우에 추가 Vm 클러스터에 추가 됩니다.

- **가상 머신 확장 집합입니다.** 이 버스트 시나리오에서는 응용 프로그램에 추가 확장할 수 있습니다 [계산 리소스](/azure/virtual-machine-scale-sets/overview) VM 사용량을 기준으로 합니다. 수요가 낮아지면 확장 집합의 Vm 수 또한 중단 될 수 있습니다, 계산 능력의 효율적인 사용을 보장 합니다.

- **PaaS를 확장 합니다.** 리소스를 계산 하는 azure PaaS 제품 조정 합니다. 예를 들어 [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) 요청의 볼륨이 증가 맞게 계산 리소스를 할당 합니다.

- **Kubernetes 클러스터입니다.** Azure에서 응용 프로그램에서 사용할 수 있습니다 [Kubernetes 클러스터](/azure/aks/concepts-clusters-workloads) 지정 된 리소스에 대 한 계산 서비스에 대 한 합니다. Azure Kubernetes Service (AKS)는 Kubernetes 노드, 풀 및 Azure에서 클러스터를 조정 하는 관리 되는 서비스입니다.

가 계산 리소스 크기 조정에 대 한 올바른 방법을 선택 하려면 Azure 및 메인프레임 차이점을 이해 해야 합니다. 키는 어떻게-또는-계산 리소스에서 데이터를 공유 합니다. Azure에서 데이터가 기본적으로 공유 되지 않으므로 일반적으로 여러 Vm에서. 에 필요한 데이터를 공유 하는 경우 스케일 아웃의 여러 Vm 계산 클러스터, 공유 데이터는이 기능을 지 원하는 리소스에 있어야 합니다. Azure에서 데이터 공유는 저장소 다음 섹션에 설명 합니다.

## <a name="azure-compute-optimization"></a>Azure 계산 최적화

Azure 아키텍처에서 처리의 각 계층을 최적화할 수 있습니다. 각 환경에 대 한 가장 적합 한 유형의 Vm 및 기능을 사용 합니다. 다음 그림 Db2를 사용 하는 CICS 응용 프로그램을 지원 하기 위해 Azure에서 Vm을 배포 하는 것에 대 한 한 가지 잠재적인 패턴을 보여 줍니다. 기본 사이트에서는 가용성이 높은 프로덕션, 사전 프로덕션 및 테스트 VM이 배포됩니다. 보조 사이트는 백업 및 재해 복구용입니다.

각 계층 적절 한 재해 복구 서비스를 제공할 수도 있습니다. 프로덕션 VM과 데이터베이스 VM에는 핫 복구나 웜 복구가 필요한 반면 개발 및 테스트 VM은 콜드 복구를 지원하는 경우를 예로 들 수 있습니다.

![재해 복구를 지 원하는 항상 사용 가능한 배포](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [메인프레임 재호스팅 Azure Virtual Machines에서](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [메인프레임 저장소를 Azure로 이동](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z에 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 결합 기능: 향상 된 기능](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 기능 설치에 대한 필수 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 클러스터형 데이터베이스 솔루션](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 응용 프로그램에 대 한 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 및 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>추가 마이그레이션 리소스

- [Platform Modernization Alliance: Azure에서 IBM Db2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
