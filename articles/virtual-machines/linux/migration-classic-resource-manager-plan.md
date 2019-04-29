---
title: 클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획 | Microsoft Docs
description: 클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: de2279d7f24400142f9d47ecf25378e7e4c47f9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474050"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획
Azure Resource Manager는 수많은 놀라운 기능을 제공하지만, 유연하게 이전할 수 있도록 마이그레이션 과정을 계획하는 것이 매우 중요합니다. 계획에 시간을 들이면 마이그레이션 활동을 수행하는 동안 문제가 발생하지 않습니다. 

> [!NOTE] 
> 다음 지침은 Azure 고객 자문 팀과 클라우드 솔루션 설계자가 고객과 협력하여 대규모 환경을 마이그레이션하는 데 크게 기여했습니다. 따라서 새로운 성공 패턴이 알려지면서 이 문서를 계속 업데이트할 예정이므로 수시로 다시 확인하여 새로운 권장 사항이 있는지 확인합니다.

마이그레이션 과정에는 다음과 같이 4가지 일반적인 단계가 있습니다.

![마이그레이션 단계](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>계획

### <a name="technical-considerations-and-tradeoffs"></a>기술적 고려 사항 및 장단점

기술 요구 사항 크기, 지역 및 운영 방식에 따라 다음과 같이 고려할 수 있습니다.

1. 조직에 Azure Resource Manager가 필요한 이유는 무엇인가요?  업무적 측면에서 마이그레이션을 수행하는 이유는 무엇인가요?
2. Azure Resource Manager에 대한 기술적인 이유는 무엇인가요?  추가로 활용하려는 Azure 서비스는 무엇인가요(있는 경우)?
3. 마이그레이션에 포함되는 애플리케이션(또는 가상 머신 집합)은 무엇인가요?
4. 마이그레이션 API로 지원되는 시나리오는 무엇인가요?  [지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations)을 검토하세요.
5. 운영 팀이 이제 클래식 및 Azure Resource Manager에서 애플리케이션/VM을 지원하나요?
6. Azure Resource Manager에서는 VM 배포, 관리, 모니터링 및 보고 프로세스를 어떻게 변경하나요(변경할 수 있는 경우)?  배포 스크립트를 업데이트해야 하나요?
7. 이해 관계자(최종 사용자, 애플리케이션 소유자 및 인프라 소유자)에게 알릴 통신 계획은 무엇인가요?
8. 환경의 복잡성에 따라 최종 사용자와 애플리케이션 소유자가 애플리케이션을 사용할 수 없는 유지 관리 기간이 있어야 하나요?  그 기간은 얼마나 되나요?
9. 이해 관계자가 Azure Resource Manager에 정통하고 숙련되었는지 확인하기 위한 학습 계획은 무엇인가요?
10. 마이그레이션을 위한 프로그램 관리 또는 프로젝트 관리 계획은 무엇인가요?
11. Azure Resource Manager 마이그레이션 및 기타 관련 기술 로드맵의 일정은 무엇인가요?  최적으로 정렬되어 있나요?

### <a name="patterns-of-success"></a>성공 패턴

성공적인 고객은 이전 질문에 대해 토론하고, 문서화하며, 관리하는 세부 계획을 세웁니다.  마이그레이션 계획을 스폰서와 이해 관계자에게 널리 알릴 수 있는지 확인합니다.  마이그레이션 옵션에 대한 지식을 갖추기 위해 아래의 마이그레이션 관련 문서를 참조하는 것이 좋습니다.

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>문제 방지

- 계획 실패  이 마이그레이션의 기술 단계를 검증하고 결과를 예측할 수 있습니다.
- 플랫폼에서 지원하는 마이그레이션 API가 모든 시나리오를 설명한다고 가정 지원되는 시나리오를 이해하려면 [지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations)을 참조하세요.
- 최종 사용자에게 잠재적인 애플리케이션 중단을 계획하지 않음  잠재적으로 사용할 수 없는 애플리케이션 시간을 최종 사용자에게 적절하게 경고할 수 있을 만큼 충분한 버퍼를 계획합니다.


## <a name="lab-test"></a>랩 테스트 

**환경 복제 및 테스트 마이그레이션 수행**
  > [!NOTE]
  > 기존 환경의 정확한 복제는 Microsoft 지원에서 공식적으로 지원하지 않는 커뮤니티 제공 도구를 사용하여 실행됩니다. 따라서 **선택적** 단계이지만 프로덕션 환경을 변경하지 않고도 문제를 찾아내는 가장 좋은 방법입니다. 커뮤니티에서 제공하는 도구를 사용하도록 선택할 수 없으면 아래의 유효성 검사/준비/시험 실행 중단 권장 사항을 참조합니다.
  >
  
  정확한 시나리오(계산, 네트워킹 및 스토리지)에 대한 랩 테스트를 수행하는 것이 원활한 마이그레이션을 보장하는 가장 좋은 방법입니다. 이렇게 하면 다음과 같은 이점을 얻을 수 있습니다.

- 완전히 분리된 랩 또는 테스트할 기존의 비프로덕션 환경 반복적으로 마이그레이션할 수 있고 안전하게 수정할 수 있는 완전히 분리된 랩을 사용하는 것이 좋습니다.  실제 구독에서 메타데이터를 수집/하이드레이션하는 스크립트는 다음과 같습니다.
- 별도의 구독으로 랩을 만드는 것이 좋습니다. 이는 별도의 분리된 구독을 통해 반복적으로 손상되는 랩에서 실제로 무언가를 실수로 삭제할 가능성을 줄일 수 있기 때문입니다.

  이 작업은 AsmMetadataParser 도구를 사용하여 수행할 수 있습니다. [이 도구에 대해 자세히 알아보기 ](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>성공 패턴

다음은 다양한 대규모 마이그레이션에서 발견된 문제입니다. 전체적인 목록이 아니므로 자세한 내용에 대해서는 [지원되지 않는 기능 및 구성](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations)을 참조해야 합니다. 이러한 기술적인 문제가 발생할 수도 있고 그렇지 않을 수도 있지만, 마이그레이션을 시도하기 전에 문제를 해결하면 더 원활한 환경을 갖출 수 있습니다.

- **유효성 검사/준비/시험 실행 중단 수행** - 클래식에서 Azure Resource Manager로의 마이그레이션 성공 여부를 확인하는 가장 중요한 단계일 것입니다. 마이그레이션 API에는 유효성 검사, 준비 및 커밋의 세 가지 주요 단계가 있습니다. 유효성 검사는 기존 환경의 상태를 파악하고 모든 문제의 결과를 반환합니다. 그러나 Azure Resource Manager 스택에 일부 문제가 있을 수 있으므로 유효성 검사에서 모든 것을 파악하지는 않습니다. 마이그레이션 프로세스의 다음 단계인 준비에서는 이러한 문제를 노출시키는 데 도움이 됩니다. 준비는 메타데이터를 클래식에서 Azure Resource Manager로 이동하지만, 이동을 커밋하지 않으며 클래식 쪽에서 아무 것도 제거하거나 변경하지 않습니다. 시험 실행은 마이그레이션을 준비한 후 마이그레이션 준비를 중단합니다(**커밋 안 함**). 유효성 검사/준비/시험 실행 중단 단계는 Azure Resource Manager 스택의 모든 메타데이터를 보고(*프로그래밍 방식으로 또는 포털에서*), 검사하고, 모든 것이 올바르게 마이그레이션되었는지 확인하고, 기술적인 문제를 통해 작동하기 위한 것입니다.  또한 마이그레이션 기간도 제공하므로 이에 따라 가동 중지 시간을 적절하게 계획할 수도 있습니다.  유효성 검사/준비/시험 실행 중단으로 인해 사용자 가동 중지 시간이 발생하지는 않습니다. 따라서 애플리케이션 사용이 중단되지 않습니다.
  - 시험 실행을 수행하기 전에 아래의 항목을 해결해야 하지만, 시험 실행 테스트는 누락된 경우에도 안전하게 이 준비 단계를 플러시합니다. 엔터프라이즈 마이그레이션 중에 시험 실행이 마이그레이션 준비를 보장하는 데 있어 안전하고 가치 있는 방법이라는 것을 알게 되었습니다.
  - 준비가 실행 중일 때는 제어 평면(Azure 관리 작업)이 가상 네트워크 전체에서 잠겨 있으므로 유효성 검사/준비/시험 실행 중단 중에는 VM 메타데이터를 변경할 수 없습니다.  그러나 그렇지 않은 경우 모든 애플리케이션 기능(RD, VM 사용 등)에 영향을 주지 않습니다.  VM 사용자는 시험 실행이 실행되고 있는지 알 수 없습니다.

- **ExpressRoute 회로 및 VPN**. 현재 권한 부여 링크가 있는 ExpressRoute 게이트웨이는 가동 중지 시간을 통해 마이그레이션할 수 있습니다. 해결 방법은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션](../../expressroute/expressroute-migration-classic-resource-manager.md)을 참조하세요.

- **VM 확장** - Virtual Machine 확장은 실행 중인 VM을 마이그레이션하는 데 있어 잠재적으로 가장 큰 장애물 중 하나입니다. VM 확장을 재구성하는 데 1-2일이 걸릴 수 있으므로 이에 따라 적절히 계획해야 합니다.  실행 중인 VM의 VM 확장 상태를 다시 보고하려면 작업 Azure 에이전트가 필요합니다. 실행 중인 VM의 상태가 나쁘다고 반환되면 마이그레이션이 중지됩니다. 에이전트 자체는 마이그레이션에 사용할 수 있도록 제대로 작동하지 않아도 되지만, VM에 확장이 있으면 마이그레이션을 진행하는 데 있어 작업 에이전트와 아웃바운드 인터넷 연결(DNS 사용)이 모두 필요합니다.
  - 마이그레이션 중에 DNS 서버에 대한 연결이 끊어지면, 마이그레이션을 준비하기 전에 먼저 모든 VM에서 BGInfo v1.\*을 제외한 모든 VM 확장을 제거한 다음, Azure Resource Manager 마이그레이션 후에 해당 확장을 VM에 다시 추가해야 합니다.  **이 작업은 실행 중인 VM에만 해당됩니다.**  VM의 할당 취소가 중지되면 VM 확장을 제거할 필요가 없습니다. **참고:** 다양한 확장 프로그램(예: Azure 진단 및 보안 센터 모니터링)은 마이그레이션 후에 다시 설치되므로 제거하더라도 문제가 되지 않습니다.
  - 또한 네트워크 보안 그룹이 아웃바운드 인터넷 액세스를 제한하지 않는지 확인합니다. 이는 일부 네트워크 보안 그룹 구성에서 발생할 수 있습니다. VM 확장을 Azure Resource Manager로 마이그레이션하려면 아웃바운드 인터넷 액세스(및 DNS)가 필요합니다. 
  - BGInfo 확장에는 두 가지 버전, 즉 v1과 v2가 있습니다.  Azure Portal이나 PowerShell을 사용하여 VM을 만들었으면 VM에 v1 확장이 있을 것입니다. 이 확장은 제거할 필요가 없으며, 마이그레이션 API에서 건너뜁니다(마이그레이션되지 않음). 그러나 새 Azure Portal을 사용하여 클래식 VM을 만든 경우 에이전트가 작동하고 아웃바운드 인터넷 액세스(및 DNS)가 있으면 Azure Resource Manager로 마이그레이션할 수 있는 JSON 기반 BGInfo v2 버전이 있을 것입니다. 
  - **재구성 옵션 1**: VM에 아웃바운드 인터넷 액세스, 작업 DNS 서비스 및 작업 Azure 에이전트가 없음을 알고 있으면, 준비하기 전에 마이그레이션의 일환으로 모든 VM 확장을 제거한 다음, 마이그레이션 후에 VM 확장을 다시 설치합니다. 
  - **재구성 옵션 2**: VM 확장이 너무 큰 장애물이라면 마이그레이션 이전에 모든 VM을 종료/할당 취소할 수 있습니다. 할당 취소된 VM을 마이그레이션한 다음 Azure Resource Manager 쪽에서 해당 VM을 다시 시작합니다. 여기서 이점은 VM 확장이 마이그레이션된다는 것입니다. 단점은 모든 공용 가상 IP가 손실된다는 것이며(시동 장치가 아닐 수도 있음), 분명히 VM이 종료되어 작업 애플리케이션에 매우 큰 영향을 미치게 됩니다.

    > [!NOTE] 
    > 마이그레이션될 실행 중인 VM에 대해 Azure Security Center 정책을 구성한 경우에는 확장을 제거하기 전에 해당 보안 정책을 중지해야 합니다. 그렇지 않으면 보안 모니터링 확장을 제거한 후 VM에 자동으로 다시 설치합니다.

- **가용성 집합** - 가상 네트워크(vNet)를 Azure Resource Manager로 마이그레이션하려면, VM이 포함된 클래식 배포(즉, 클라우드 서비스)가 모두 하나의 가용성 집합에 있어야 하거나 VM이 모두 가용성 집합에 있지 않아야 합니다. 클라우드 서비스에 둘 이상의 가용성 집합이 있으면 Azure Resource Manager와 호환되지 않으므로 마이그레이션이 중지됩니다.  또한 가용성 집합에 일부 VM이 없는 한편 가용성 집합에 없는 일부 VM이 있을 수 있습니다. 이를 해결하려면 클라우드 서비스를 재구성하거나 재조정해야 합니다.  여기에는 시간이 오래 걸릴 수 있으므로 적절히 계획합니다. 

- **웹/작업자 역할 배포** - 웹 및 작업자 역할이 포함된 Cloud Services는 Azure Resource Manager로 마이그레이션할 수 없습니다. 마이그레이션을 시작하려면 먼저 웹/작업자 역할을 가상 네트워크에서 제거해야 합니다.  일반적인 해결 방법은 웹/작업자 역할 인스턴스를 ExpressRoute 회로에 연결된 별도의 클래식 가상 네트워크로 이동하거나 코드를 최신 PaaS App Services로 마이그레이션하는 것입니다(이 문서의 범위를 벗어난 토론 내용임). 이전 재배포의 경우 새 클래식 가상 네트워크를 만들고, 웹/작업자 역할을 해당 가상 네트워크로 이동/재배포한 다음, 이동될 가상 네트워크에서 해당 배포를 삭제했습니다. 코드 변경이 필요 없습니다. 새 [Virtual Network 피어링](../../virtual-network/virtual-network-peering-overview.md) 기능을 사용하여 웹/작업자 역할이 포함된 가상 네트워크와 마이그레이션될 가상 네트워크와 동일한 Azure 지역에 있는 다른 가상 네트워크를 피어링할 수 있으므로(**피어링된 가상 네트워크를 마이그레이션할 수 없으므로 가상 네트워크 마이그레이션이 완료된 후**), 성능 손실과 대기 시간/대역폭 패널티 없이 동일한 기능을 제공합니다. [Virtual Network 피어링](../../virtual-network/virtual-network-peering-overview.md)을 추가하면 웹/작업자 역할 배포를 쉽게 완화할 수 있으며 Azure Resource Manager에서 마이그레이션을 차단할 수 없습니다.

- **Azure Resource Manager 할당량** - Azure 지역에는 클래식 및 Azure Resource Manager 모두에 대해 별도의 할당량/제한이 있습니다. 마이그레이션 시나리오에서 새 하드웨어가 사용되지는 않지만 *(기존의 VM을 클래식에서 Azure Resource Manager로 전환 중이므로)* Azure Resource Manager 할당량은 마이그레이션을 시작하기 전에 충분한 용량으로 계속 유지해야 합니다. 문제를 일으키는 주요 제한 사항은 아래와 같습니다.  할당량 지원 티켓을 열어 한도를 높입니다. 

    > [!NOTE]
    > 이러한 제한 사항은 마이그레이션될 현재 환경과 동일한 지역에서 발생해야 합니다.
    >

  - 네트워크 인터페이스
  - 부하 분산 장치
  - 공용 IP
  - 고정 공용 IP
  - 코어 수
  - 네트워크 보안 그룹
  - 경로 테이블

    Azure CLI의 최신 버전에서 다음 명령을 사용하여 현재 Azure Resource Manager 할당량을 확인할 수 있습니다.

    **Compute** *(코어, 가용성 집합)*

    ```bash
    az vm list-usage -l <azure-region> -o jsonc 
    ```

    **네트워크** *(Virtual Network, 고정 공용 IP, 공용 IP, 네트워크 보안 그룹, 네트워크 인터페이스, 부하 분산 장치, 경로 테이블)*
    
    ```bash
    az network list-usages -l <azure-region> -o jsonc
    ```

    **저장소** *(Storage 계정)*
    
    ```bash
    az storage account show-usage
    ```

- **Azure Resource Manager API 조정 제한** - 충분히 큰 환경이 있는 경우(예: VNet에서 400대의 VM보다 큰 환경) Azure Resource Manager에서 쓰기에 대한 기본 API 조정 제한에 도달할 수 있습니다(현재 **1200 쓰기/시간**). 마이그레이션을 시작하기 전에 지원 티켓을 늘려 구독 한도를 높여야 합니다.

- **프로비전 시간 초과 VM 상태** - VM이 **프로비전 시간 초과** 상태에 있으면 마이그레이션하기 전에 이 문제를 해결해야 합니다. 해결하는 유일한 방법은 VM을 프로비전 해제/다시 프로비전하여(삭제, 디스크 유지 및 VM 다시 만들기) 가동 중지 시간과 함께 하는 것입니다. 

- **RoleStateUnknown VM 상태** - **작업 상태 알 수 없음** 오류 메시지로 인해 마이그레이션이 중지되면 포털을 사용하여 VM을 검사하고 실행 중인지 확인합니다. 이 오류는 일반적으로 몇 분 후에 자체적으로 해결되며(재구성 필요 없음), 종종 Virtual Machine **시작**, **중지**, **다시 시작** 작업 중에 자주 나타나는 일시적인 유형입니다. **권장 사항:** 몇 분 후에 마이그레이션을 다시 시도합니다. 

- **패브릭 클러스터가 존재하지 않음** - 경우에 따라 뜻밖의 다양한 이유로 특정 VM을 마이그레이션할 수 없습니다. 이러한 알려진 사례 중 하나는 VM이 최근에(지난 1주일 이내) 만들어져 Azure Resource Manager 워크로드에 아직 준비되지 않은 Azure 클러스터를 배치한 경우입니다.  **패브릭 클러스터가 존재하지 않음**이라는 오류 메시지가 표시되고 VM을 마이그레이션할 수 없습니다. 머지 않아 클러스터에서 Azure Resource Manager를 사용할 수 있게 되므로 대개 며칠 정도 기다리면 이 특정 문제가 해결됩니다. 그러나 즉각적인 해결 방법 중 하나는 VM을 `stop-deallocate`한 다음, 마이그레이션을 계속 진행하고, 마이그레이션 후에 Azure Resource Manager에서 VM 백업을 시작하는 것입니다.

### <a name="pitfalls-to-avoid"></a>문제 방지

- 바로 가기를 사용하지 않고, 유효성 검사/준비/시험 실행 중단 마이그레이션을 생략하지 않습니다.
- 잠재적인 문제 중 전부는 아니지만 대부분이 유효성 검사/준비/시험 실행 중단 단계 중에 나타납니다.

## <a name="migration"></a>마이그레이션

### <a name="technical-considerations-and-tradeoffs"></a>기술적 고려 사항 및 장단점

환경과 관련된 알려진 문제를 통해 작업했으므로 이제 준비가 되었습니다.

실제 마이그레이션의 경우 다음 사항을 고려해야 합니다.

1. 우선 순위가 높아짐에 따라 가상 네트워크(최소 마이그레이션 단위)를 계획하고 예약합니다.  간단한 가상 네트워크를 먼저 수행한 다음 더 복잡한 가상 네트워크로 진행합니다.
2. 대부분의 고객은 비프로덕션 환경과 프로덕션 환경을 갖추게 되므로  프로덕션 환경을 마지막으로 예약합니다.
3. **(선택 사항)** 예기치 않은 문제가 발생할 경우에 대비하여 충분한 양의 버퍼로 유지 관리 가동 중지 시간을 예약합니다.
4. 문제가 발생할 경우 지원 팀과 연락하여 조정합니다.

### <a name="patterns-of-success"></a>성공 패턴

앞서의 랩 테스트 섹션에서 언급한 기술 지침은 실제 마이그레이션 이전에 고려되고 완화되어야 합니다.  적절한 테스트를 수행하기 때문에 마이그레이션은 실제로 이벤트가 아닙니다.  프로덕션 환경의 경우 신뢰할 수 있는 Microsoft 파트너 또는 Microsoft 프리미어 서비스와 같은 추가 지원이 도움이 될 수 있습니다.

### <a name="pitfalls-to-avoid"></a>문제 방지

완전히 테스트하지 않으면 문제가 발생할 수 있으며 마이그레이션이 지연될 수 있습니다.  

## <a name="beyond-migration"></a>마이그레이션 외

### <a name="technical-considerations-and-tradeoffs"></a>기술적 고려 사항 및 장단점

Azure Resource Manager에 있으므로 이제 플랫폼을 극대화합니다.  [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하여 추가 혜택에 대해 알아봅니다.

고려 사항은 다음과 같습니다.

- 다른 활동과 함께 마이그레이션을 묶습니다.  대부분의 고객은 애플리케이션 유지 관리 기간을 선택합니다.  그렇다면 이 가동 중지 시간을 사용하여 Managed Disks에 암호화 및 마이그레이션과 같은 다른 Azure Resource Manager 기능을 사용하도록 설정하는 것이 좋습니다.
- Azure Resource Manager의 기술적 이유와 비즈니스상 이유를 다시 살펴봅니다. 환경에 적용되는 Azure Resource Manager에서만 사용할 수 있는 추가 서비스를 사용하도록 설정합니다.
- PaaS 서비스를 사용하여 환경을 현대화합니다.

### <a name="patterns-of-success"></a>성공 패턴

이제 Azure Resource Manager에서 사용하도록 설정하려는 서비스에 대해 분명한 목적이 있어야 합니다.  많은 고객들이 Azure 환경에서 다음과 같은 강력한 요소를 찾고 있습니다.

- [역할 기반 Access Control](../../role-based-access-control/overview.md)
- [쉽고 제어 가능한 배포를 위한 Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-overview.md#template-deployment)
- [태그](../../azure-resource-manager/resource-group-using-tags.md).
- [활동 제어](../../azure-resource-manager/resource-group-audit.md)
- [Azure 정책](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>문제 방지

이 클래식을 Azure Resource Manager 마이그레이션 과정으로 시작한 이유를 기억합니다.  원래의 비즈니스상 이유는 무엇입니까? 비즈니스상 이유를 달성했습니까?


## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
