---
title: 질문과 대답
description: Azure VMware 솔루션에 대 한 일반적인 질문에 대 한 답변을 제공 합니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: cffa31bb66adfde2af24ab2542322479639ed9dd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752185"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Azure VMware Solution preview에 대 한 질문과 대답

Azure VMware 솔루션에 대 한 자주 묻는 질문에 대 한 대답입니다.

## <a name="general"></a>일반

**Azure VMware 솔루션 이란?**

기업은 비즈니스 민첩성을 개선하고, 비용을 절감하고, 혁신을 가속화하는 IT 현대화 전략을 찾고 있으며, 고객의 디지털 변환을 가능하게 하는 핵심 요소로 하이브리드 클라우드 플랫폼이 등장했습니다. Azure VMware 솔루션은 VMware의 SDDC (소프트웨어 정의 데이터 센터) 소프트웨어와 Microsoft Azure 글로벌 클라우드 서비스 에코 시스템을 결합 합니다. Azure VMware 솔루션은 성능, 가용성, 보안 및 규정 준수 요구 사항을 충족 하도록 관리 됩니다.

## <a name="azure-vmware-solution-service"></a>Azure VMware 솔루션 서비스

**현재 Azure VMware 솔루션은 어디에서 사용할 수 있나요?**

서비스는 계속 해 서 새 지역에 추가 되므로 [최신 서비스 가용성 정보](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) 를 확인 하 여 자세한 내용을 확인 하세요. 

**Azure VMware 솔루션 인스턴스에서 실행 되는 워크 로드는 Azure 서비스를 사용 하거나 통합할 수 있나요?**

모든 Azure 서비스는 Azure VMware 솔루션 고객에 게 제공 됩니다. 서비스의 성능 및 가용성 제한은 서비스마다 다르게 접근해야 합니다.

**현재 프라이빗 클라우드 리소스를 관리하는 데 사용하는 것과 동일한 도구를 사용하나요?**

예. 배포 및 여러 관리 작업에는 Azure Portal을 사용합니다. vSphere 및 NSX-T 리소스 관리에는 vCenter 및 NSX Manager가 사용됩니다.

**온-프레미스 vCenter를 사용하여 프라이빗 클라우드를 관리할 수 있나요?**

Azure VMware 솔루션은 시작 시 온-프레미스 및 사설 클라우드 환경에서 단일 관리 환경을 지원 하지 않습니다. 프라이빗 클라우드 클러스터는 프라이빗 클라우드에 로컬인 vCenter 및 NSX Manager를 통해 관리됩니다.

**온-프레미스에서 실행되는 vRealize Suite를 사용할 수 있나요?** 

구체적인 통합 및 사용 사례는 건별로 평가할 수 있습니다.

**온-프레미스 환경에서 Azure VMware 솔루션 사설 클라우드로 vSphere Vm을 마이그레이션할 수 있나요?**

예. 표준 cross vCenter [vMotion 요구 사항이](https://kb.vmware.com/s/article/210695) 충족 되는 경우 vm 마이그레이션 및 vMotion를 사용 하 여 사설 클라우드로 vm을 이동할 수 있습니다.

**온-프레미스 환경에서 특정 버전의 vSphere가 필요한가요?**

모든 클라우드 환경은 HCX가 함께 제공되므로vMotion에 대한 온-프레미스 환경에서 vSphere 5.5 이상이 필요합니다.

**변경 제어 프로세스는 어떻게 생겼나요?**

서비스 자체에 대한 업데이트는 Microsoft Azure의 표준 변경 관리 프로세스를 따릅니다. 고객은 워크로드 관리 작업 및 관련된 변경 관리 프로세스를 담당합니다.

**Azure VMware Solution by CloudSimple과 어떻게 다른가요?**

새 Azure VMware Solution과 관련하여 Microsoft와 VMware는 직접 클라우드 공급자 파트너십을 체결했습니다. 새로운 솔루션은 Microsoft에서 완전히 설계, 구축 및 지원 되며 VMware에 의해 보증 됩니다. Azure 전용 인프라에서 실행되는 VMware 기술 스택을 사용하므로 솔루션의 아키텍처는 동일합니다.

**기존 Azure VMware Solution 고객에게 이 미리 보기는 어떤 의미인가요?**

기존 Azure VMware Solution by CloudSimple은 아무 것도 바뀌지 않습니다. Azure에서 솔루션을 계속 지원합니다. Azure VMware Solution은 [SLA(서비스 수준 계약)](https://aka.ms/CSVMwareSLA)를 통해 지원됩니다. 고객은 서비스를 프로덕션 워크로드에 계속 사용하면 됩니다. 이 솔루션은 [Microsoft 서비스 팀](https://azure.microsoft.com/support/legal/)에서 관리합니다.

**Azure VMware Solution by CloudSimple에서 이 새 솔루션으로 마이그레이션할 수 있나요?**

예, Azure VMware Solution은 HCX와 같은 익숙한 VMware 도구를 사용하여 마이그레이션하는 것을 지원합니다. 새 솔루션으로 마이그레이션하는 데 관심이 있는 고객의 경우 Microsoft 계정 팀과 협력 하 여 옵션 및 사용 가능한 지원을 살펴보세요.



## <a name="compute-network-and-storage"></a>컴퓨팅, 네트워크 및 스토리지

**사용 가능한 호스트 유형이 2개 이상 있나요?**

사용 가능한 호스트 유형은 하나뿐입니다.

**각 호스트 유형의 CPU 사양이 어떻게 되나요?**

서버에는 듀얼 18코어 2.3GHz Intel CPU를 사용합니다.

**각 호스트의 메모리는 얼마인가요?**

서버의 RAM은 576GB입니다.

**각 호스트의 스토리지 용량은 얼마인가요?**

각 ESXi 호스트에는 15.2 TB의 용량 계층을 포함 하는 두 개의 vSAN diskgroups와 3.2 TB NVMe 캐시 계층 (각 diskgroups의 1.6 TB)이 있습니다.

**각 ESXi 호스트에서 사용할 수 있는 네트워크 대역폭은 얼마나 되나요?**

각 ESXi 호스트는 ESXi 시스템 트래픽에 대해 프로 비전 된 두 개의 Nic와 워크 로드 트래픽에 대해 프로 비전 된 두 개의 nic를 사용 하 여 4 25 Gbps Nic로 구성 됩니다. 

**미사용 암호화 된 vSAN 데이터 저장소에 저장 된 데이터 입니까?**

예, 모든 vSAN 데이터는 Azure Key Vault에 저장 된 키를 사용 하 여 기본적으로 암호화 됩니다.

## <a name="hosts-clusters-and-private-clouds"></a>호스트, 클러스터 및 프라이빗 클라우드

**기본 인프라를 공유하나요?**

아니요, 프라이빗 클라우드 호스트와 클러스터는 전용이며 사용 전과 후에 안전하게 삭제됩니다.

**클러스터당 최소/최대 호스트 수는 얼마인가요?**

ESXi 호스트 3~16개 사이에서 클러스터를 스케일링할 수 있습니다. 평가판 클러스터는 호스트 3개로 제한됩니다.

**프라이빗 클라우드 클러스터를 스케일링할 수 있나요?**

예, 최소~최대 ESXi 호스트 수 사이에서 클러스터를 스케일링할 수 있습니다. 평가판 클러스터는 호스트 3개로 제한됩니다.

**평가판 클러스터란 무엇인가요?**

평가판 클러스터는 Azure VMware 솔루션 사설 클라우드의 1 개월 평가에 사용 되는 세 개의 호스트 클러스터입니다.

**평가판 클러스터에 고성능 호스트를 사용할 수 있나요?**

아니요. 고성능 ESXi 호스트는 프로덕션 클러스터에 사용하도록 예약되어 있습니다.

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware 솔루션 및 VMware 소프트웨어

**프라이빗 클라우드에 사용되는 VMware 소프트웨어 버전은 무엇인가요?**

프라이빗 클라우드에서는 vSphere 6.7, vSAN 6.7, HCX 및 NSX-T 2.5 버전을 사용합니다.  

**프라이빗 클라우드는 VMware NSX를 사용하나요?**

예, NSX-T 2.5은 Azure VMware 솔루션 사설 클라우드의 소프트웨어 정의 네트워킹에 사용 됩니다.

**프라이빗 클라우드에서 VMware NSX-V를 사용할 수 있나요?**

아니요. 현재 지원되는 유일한 NSX 버전은 NSX-T입니다.

**프라이빗 클라우드에 연결하는 온-프레미스 환경 또는 네트워크에서 NSX가 필요한가요?**

아니요, 온-프레미스에서는 NSX를 사용할 필요가 없습니다.

**프라이빗 클라우드에서 VMware 소프트웨어를 업그레이드 및 업데이트하는 일정이 어떻게 되나요?**

사설 클라우드 소프트웨어 번들 업그레이드는 VMware에서 소프트웨어 번들의 최신 릴리스 중 한 버전의 소프트웨어를 유지 하기 위해 수행 됩니다. 사설 클라우드 소프트웨어 버전은 개별 소프트웨어 구성 요소의 최신 버전 (ESXi, NSX, vCenter, vSAN)과 다를 수 있습니다.

**프라이빗 클라우드 소프트웨어 스택은 얼마나 자주 업데이트되나요?**

프라이빗 클라우드 소프트웨어는 VMware의 소프트웨어 번들 릴리스를 따르는 일정에 따라 업그레이드 됩니다. 프라이빗 클라우드는 업그레이드로 인한 가동 중지 시간이 없습니다.

## <a name="connectivity"></a>연결

**프라이빗 클라우드를 온-프레미스 환경과 통합하는 데 필요한 네트워크 IP 주소 계획은 무엇인가요?**

Azure VMware 솔루션 사설 클라우드를 배포 하려면 개인 네트워크/22 주소 공간이 필요 합니다. 이 프라이빗 주소 공간은 구독의 다른 가상 네트워크 또는 온-프레미스 네트워크와 겹치지 않아야 합니다.
 
**온-프레미스 환경에서 Azure VMware 솔루션 사설 클라우드로 연결 어떻게 할까요??**

다음 두 가지 방법 중 하나로 서비스에 연결할 수 있습니다. 

- ExpressRoute를 통해 프라이빗 클라우드에 피어링되는 Azure 가상 네트워크에 배포된 VM 또는 애플리케이션 게이트웨이를 사용합니다.
- ExpressRoute Global Reach를 통해 온-프레미스 데이터 센터에서 Azure ExpressRoute 회로로 연결합니다.

**워크로드 VM을 인터넷 또는 Azure 서비스 엔드포인트에 연결하려면 어떻게 할까요?**

Azure Portal에서 프라이빗 클라우드에 인터넷 연결을 사용하도록 설정합니다. NSX-T 관리자를 사용하여 NSX-T T1 라우터와 논리 스위치를 만듭니다. 그런 다음, vCenter를 사용하여 논리 스위치가 정의한 네트워크 세그먼트에 VM을 배포합니다. 해당 VM은 네트워크를 통해 인터넷 및 Azure 서비스에 액세스할 수 있습니다.

**프라이빗 클라우드의 논리 네트워크에 있는 VM에 대한 인터넷 액세스를 제한해야 하나요?**

아니요. 인터넷에서 프라이빗 클라우드로 직접 들어오는 네트워크 트래픽은 허용되지 않습니다.

**논리 네트워크의 VM에서 인터넷으로 액세스하는 것을 제한해야 하나요?**

예. NSX-T 관리자를 사용하여 VM의 인터넷 액세스를 제한하는 방화벽을 만들어야 합니다.

## <a name="accounts-and-privileges"></a>계정 및 권한

**새 Azure VMware 솔루션 사설 클라우드를 사용 하 여 얻을 수 있는 계정과 권한은 무엇 인가요?**

vCenter의 cloudadmin 사용자에 대한 자격 증명과 NSX-T 관리자에 대한 관리자 액세스 권한이 제공됩니다. Azure Active Directory를 통합하는 데 사용할 수 있는 CloudAdmin 그룹도 있습니다. 자세한 내용은 [액세스 및 ID 개념](concepts-identity.md)을 참조하세요.

**ESXi 호스트에 관리자로 액세스할 수 있나요?**

아니요, 솔루션의 보안 요구 사항을 충족하기 위해 ESXi에 대한 관리자 권한 액세스는 제한되어 있습니다.

**vCenter에서 어떤 권한을 얻게 되나요?**

CloudAdmin 그룹 권한을 얻게 됩니다. 자세한 내용은 [액세스 및 ID 개념](concepts-identity.md)을 참조하세요.

**NSX-T 관리자에서 어떤 권한을 얻게 되나요?**

NSX-T에 대한 모든 관리자 권한이 부여되며 NSX-T 데이터 센터 온-프레미스와 마찬가지로 역할 기반 액세스 제어를 관리할 수 있습니다. 자세한 내용은 [액세스 및 ID 개념](concepts-identity.md)을 참조하세요.

> [!NOTE]
> 프라이빗 클라우드 배포의 한 과정으로 T0 라우터가 생성 및 구성됩니다. 이 논리 라우터 또는 NSX-T 에지 노드 VM을 수정하면 프라이빗 클라우드에 연결하는 데 영향을 줄 수 있습니다.

## <a name="billing-and-support"></a>청구 및 지원

**Azure VMware 솔루션 미리 보기 중에 요금이 청구 되는 방식**

미리 보기 중에 Azure VMware 솔루션에 대 한 요금은 종 량 제 기준으로 매달 청구 됩니다. 추가 옵션은 일반 공급으로 전환되는 시점에 사용할 수 있습니다.

**Azure VMware 솔루션 미리 보기 중에는 가격 책정을 어떻게 구성 하나요?**

가격 책정에 대한 일반적인 질문은 Azure VMware 솔루션Solution [가격 책정](https://azure.microsoft.com/pricing/details/azure-vmware) 페이지를 참조하세요. 요청에 따라 미리 보기 가격 책정을 사용할 수 있습니다. 계정 팀에 문의 하거나 가격 책정 페이지의 링크를 따라 판매 담당자에 게 문의 하세요.

**누가 Azure VMware 솔루션을 지원 하나요?**

Azure VMware 솔루션에 대 한 지원은 Microsoft에서 제공 합니다. 미리 보기 지침에 따라 9 ~ 5 pm PST 업무 시간 월요일-금요일에 지원을 제공 합니다. [이 링크](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에서 지원 티켓을 제출할 수 있습니다.

**Azure VMware 솔루션 사설 클라우드를 만들려면 어떤 계정이 필요 한가요?**

Azure 구독의 Azure 계정이 필요합니다.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Azure VMware 솔루션에 대 한 호스트 할당량 증가를 요청 어떻게 할까요??**

[지원 요청을 제출](..\azure-portal\supportability\how-to-create-azure-support-request.md)하여 할당량 증가를 요청할 수 있습니다. 할당량 관리 팀이 3영업일 이내에 요청을 평가하여 승인합니다.  

> [!IMPORTANT]
> 할당량 증가를 요청하려면 먼저 Azure Portal에서  **Microsoft.AVS** 리소스 공급자를 등록해야 합니다.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> 리소스 공급자를 등록하는 추가 방법은 [리소스 공급자 및 유형](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요.

1. Azure Portal의 **도움말 + 지원**에서 **새 지원 요청**을 작성하고, 티켓에 대한 다음 정보를 제공합니다.
   - **문제 유형:** 기술
   - **구독:** 구독 ID
   - **서비스:**  Azure VMware 솔루션 
   - **요약:** 할당량 증가
   - **문제 유형:** 용량 관리 문제
   - **문제 하위 유형:** 호스트 할당량/용량을 추가하기 위한 고객 요청

1. 지원 티켓의 [설명]에 있는 [세부 정보] 탭에서 다음 정보를 입력합니다.
   - 추가 노드 수   
   - 노드 SKU
   - 지역

   > [!NOTE] 
   > 기본적으로 최소 4개 노드가 부여됩니다.

1. **검토 + 만들기**를 클릭하여 요청을 제출합니다.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
