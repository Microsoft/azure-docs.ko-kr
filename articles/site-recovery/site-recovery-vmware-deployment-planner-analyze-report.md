---
title: Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner 분석 | Microsoft Docs
description: 이 문서에서는 Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner에서 생성된 보고서를 분석하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: cbea6785239c70a3cdb229d0811497f051224238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472616"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner 보고서 분석

생성된 Microsoft Excel 보고서에는 다음과 같은 시트가 포함되어 있습니다.
## <a name="on-premises-summary"></a>온-프레미스 요약
온-프레미스 요약 워크시트에는 프로파일링된 VMware 환경에 대한 개요가 제공됩니다.

![VMware 환경의 온-프레미스 요약](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**시작 날짜** 및 **종료 날짜**: 보고서 생성에 고려된 프로파일링 데이터의 시작 날짜와 종료 날짜입니다. 기본적으로 시작 날짜는 프로파일링을 시작한 날짜이고, 종료 날짜는 프로파일링을 중지한 날짜입니다. 'StartDate' 및 'EndDate' 매개 변수를 사용하여 보고서를 생성하는 경우 이는 해당 매개 변수의 값일 수 있습니다.

**총 프로파일링 일 수**: 보고서를 생성한 시작 날짜와 종료 날짜 사이에 프로파일링한 총 일 수입니다.

**호환되는 가상 머신 수**: 필요한 네트워크 대역폭, 필요한 스토리지 계정 수, Microsoft Azure 코어, 구성 서버 및 추가 프로세스 서버를 계산하는 데 호환되는 VM의 총 수입니다.

**호환되는 모든가상 머신의 총 디스크 수**: 배포에서 사용되는 구성 서버 및 추가 프로세스 서버의 수를 결정하기 위한 입력 중 하나로 사용되는 숫자입니다.

**호환되는 가상 머신당 평균 디스크 수**: 호환되는 모든 VM에서 계산된 평균 디스크 수입니다.

**평균 디스크 크기(GB)**: 호환되는 모든 VM에서 계산된 평균 디스크 크기입니다.

**원하는 RPO(분)**: 기본 복구 지점 목표이거나 필요한 대역폭을 예측하기 위해 보고서 생성 시 ‘DesiredRPO’ 매개 변수에 대해 전달된 값입니다.

**원하는 대역폭(Mbps)**: 달성 가능한 RPO를 추정하기 위해 보고서 생성 시 'Bandwidth' 매개 변수에 대해 전달한 값입니다.

**하루에 관찰된 일반적인 데이터 변동(GB)**: 모든 프로파일링 기간(일) 동안 관찰된 평균 데이터 변동입니다. 이 숫자는 배포에서 사용되는 구성 서버 및 추가 프로세스 서버의 수를 결정하기 위한 입력 중 하나로 사용됩니다.

## <a name="recommendations"></a>권장 사항

>[!Note]
>디스크 관리에 직접 복제 하는 경우 저장소 계정의 수에 대 한 권장 사항을 무시 합니다.

VMware에서 Azure로 보고서의 권장 사항 시트에는 선택된 원하는 RPO에 따라 다음과 같은 세부 정보가 포함됩니다.

![VMware에서 Azure로 보고서에 대한 권장 사항](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>프로파일링된 데이터
![배포 계획에서 프로파일링된 데이터 보기](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**프로파일링된 데이터 기간**: 프로파일링이 실행된 기간입니다. 기본적으로 보고서 생성 중에 StartDate 및 EndDate 옵션을 사용하여 특정 기간 동안 보고서를 생성하지 않는 한 도구에서는 프로파일링된 모든 데이터를 계산에 포함합니다.

**서버 이름**: VM의 보고서가 생성된 VMware vCenter 또는 ESXi 호스트의 이름 또는 IP 주소입니다.

**원하는 RPO**: 배포에 대한 복구 지점 목표입니다. 기본적으로 필요한 네트워크 대역폭은 15, 30 및 60분의 RPO 값에 대해 계산됩니다. 선택에 따라 영향을 받은 값이 시트에서 업데이트됩니다. 보고서를 생성하는 동안 *DesiredRPOinMin* 매개 변수를 사용한 경우 이 값은 원하는 RPO 결과에 표시됩니다.

### <a name="profiling-overview"></a>프로파일링 개요

![Deployment Planner의 프로파일링 결과](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**프로파일링된 총 Virtual Machines**: 프로파일링된 데이터가 사용 가능한 VM의 총 수입니다. VMListFile에 프로파일링되지 않은 VM의 이름이 있는 경우 해당 VM은 보고서 생성에서 고려되지 않고 프로파일링된 VM의 총 수에서 제외됩니다.

**호환 가능 Virtual Machines**: Site Recovery를 사용하여 Azure로 보호할 수 있는 VM 개수입니다. 필요한 네트워크 대역폭, 저장소 계정 수, Azure 코어 수, 구성 서버 수 및 추가 프로세스 서버 수를 계산하는 데 호환되는 가상 컴퓨터의 총 수입니다. 모든 호환되는 VM의 세부 정보는 "호환 VM" 섹션에서 사용할 수 있습니다.

**호환되지 않는 가상 머신 수**: Site Recovery를 사용한 보호에 대해 호환되지 않는 프로파일링된 VM의 수입니다. 비호환성에 대한 이유는 "호환되지 않는 VM" 섹션에서 설명하고 있습니다. VMListFile에 프로파일링되지 않는 VM의 이름이 있는 경우 해당 VM은 호환되지 않는 VM 수에서 제외됩니다. 이러한 VM은 "호환되지 않는 VM" 섹션의 끝에 "데이터를 찾을 수 없음"으로 나열됩니다.

**원하는 RPO**: 원하는 복구 지점 목표(분 단위)입니다. 세 가지 RPO 값에 대해 보고서가 생성됩니다. 15(기본값), 30분 및 60분. 보고서의 대역폭 권장 사항은 시트의 오른쪽 위에 있는 원하는 RPO 드롭다운 목록의 선택에 따라 변경됩니다. 사용자 지정 값을 포함한 *-DesiredRPO* 매개 변수를 사용하여 보고서를 생성한 경우 이 사용자 지정 값은 원하는 RPO 드롭다운 목록에 기본값으로 표시됩니다.

### <a name="required-network-bandwidth-mbps"></a>필요한 네트워크 대역폭(Mbps)

![Deployment Planner에 필요한 네트워크 대역폭](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**RPO 시간을 100% 충족하려면:** 원하는 RPO 시간을 100% 충족하기 위해 할당하는 것이 권장되는 대역폭(Mbps 단위)입니다. 이 양의 대역폭은 RPO 위반을 방지하는 데 호환되는 모든 VM의 안정적인 상태 델타 복제 전용이어야 합니다.

**RPO 시간을 90% 충족하려면:** 광대역 가격 책정 또는 다른 이유로 인해 원하는 RPO 시간 100%를 충족시키는 데 필요한 대역폭을 설정할 수 없는 경우 원하는 RPO 시간 90%를 충족할 수 있는 더 낮은 대역폭을 설정하도록 선택할 수 있습니다. 이 낮은 대역폭을 설정하는 의미를 이해하기 위해 보고서에서 예상되는 RPO 위반 횟수 및 기간에 대한 가상(what-if) 분석을 제공합니다.

**달성된 처리량:** GetThroughput 명령을 실행한 서버에서 스토리지 계정이 있는 Microsoft Azure 지역으로의 처리량입니다. 이 처리량 숫자는 구성 서버 또는 프로세스 서버 저장소 및 네트워크 특성이 도구를 실행한 서버의 해당 항목과 동일하게 유지된다는 조건 하에 Site Recovery를 사용하여 호환되는 VM을 보호할 때 달성할 수 있는 예상되는 수준을 나타냅니다.

복제의 경우 RPO 시간 100%를 충족하기 위해 권장되는 대역폭을 설정해야 합니다. 대역폭을 설정한 후 도구에서 보고한 달성된 처리량이 증가하지 않으면 다음을 수행합니다.

1. Site Recovery 처리량을 제한하고 있는 네트워크 QoS(서비스 품질)가 있는지 확인합니다.

2. 네트워크 대기 시간을 최소화하기 위해 Site Recovery 자격 증명 모음이 물리적으로 지원되는 가장 가까운 Microsoft Azure 지역에 있는지 확인합니다.

3. 로컬 저장소 특성을 확인하여 하드웨어(예를 들어 SSD HDD)를 향상시킬 수 있는지 여부를 결정합니다.

4. 프로세스 서버의 Site Recovery 설정을 변경하여 [복제에 사용되는 네트워크 대역폭의 양을 증가](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)시킵니다.

이미 VM을 보호한 구성 서버 또는 프로세스 서버에서 이 도구를 실행하는 경우 도구를 몇 번 실행합니다. 달성된 처리량 숫자는 해당 시점에 처리 중인 변동의 양에 따라 변화합니다.

모든 엔터프라이즈 Site Recovery 배포의 경우 [ExpressRoute](https://aka.ms/expressroute)를 사용하는 것이 좋습니다.

### <a name="required-storage-accounts"></a>필요한 저장소 계정
다음 차트에서는 호환되는 모든 VM을 보호하는 데 필요한 저장소 계정(표준 및 프리미엄)의 총 수를 보여 줍니다. 각 VM에 사용할 저장소 계정에 대해 알아보려면 "VM 저장소 배치" 섹션을 참조하세요.

![Deployment Planner에 필요한 저장소 계정](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>필요한 Azure 코어 수
이 결과는 호환되는 모든 VM의 장애 조치 또는 테스트 장애 조치 전에 설정할 코어의 총 수입니다. 구독에서 사용할 수 있는 코어 수가 너무 적은 경우 Site Recovery에서 테스트 장애 조치 또는 장애 조치 시 VM을 만들지 못합니다.

![Deployment Planner에 필요한 Azure 코어 수](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>필요한 온-프레미스 인프라
이 숫자는 호환되는 모든 VM을 보호하기에 충분하도록 구성해야 할 구성 서버 및 추가 프로세스 서버의 총 수입니다. 지원되는 [구성 서버에 대한 크기 권장 사항](https://aka.ms/asr-v2a-on-prem-components)에 따라 도구가 추가 서버를 권장할 수 있습니다. 권장 사항은 구성 서버 또는 추가 프로세스 서버 중 어느 것에서 먼저 적중되든지 간에 일일 변동량 또는 보호되는 VM의 최대 수(가상 컴퓨터당 평균 3개의 디스크를 가정) 중 더 큰 수를 기반으로 합니다. 일일 총 변동량 및 보호된 디스크의 총 수에 대한 세부 정보는 "온-프레미스 요약" 섹션에 있습니다.

![Deployment Planner에 필요한 온-프레미스 인프라](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>가상 분석
이 분석은 원하는 RPO 시간 90%만 충족하도록 낮은 대역폭을 설정할 때 프로파일링 기간 동안 발생할 수 있는 위반의 수를 설명합니다. 지정한 날짜에 하나 이상의 RPO 위반이 발생할 수 있습니다. 그래프는 해당 날짜의 최대 RPO를 보여줍니다.
이 분석에 따라 일일 RPO 위반 횟수와 일일 RPO 최고 적중 횟수를 지정된 낮은 대역폭으로 허용할 수 있는지 결정할 수 있습니다. 허용되는 경우 복제를 위해 더 낮은 대역폭을 할당할 수 있으며, 그렇지 않고 원하는 RPO 시간 100%를 충족하려면 제안된 대로 더 높은 대역폭을 할당합니다.

![Deployment Planner의 가상 분석](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>초기 복제에 권장되는 VM 일괄 처리 크기
이 섹션에서는 설정하는 RPO 시간 100%를 충족하기 위해 권장되는 대역폭을 사용하여 72시간 이내에 초기 복제를 완료하기 위해 병렬로 보호할 수 있는 VM 수를 권장합니다. 이 값은 구성 가능한 값입니다. 보고서 생성 시 변경하려면 *GoalToCompleteIR* 매개 변수를 사용합니다.

여기서 그래프는 호환되는 모든 VM에서 검색된 평균적인 VM 크기에 따라 72시간 내에 초기 복제를 완료하기 위한 대역폭 값 범위와 계산된 VM 일괄 처리 크기를 보여 줍니다.

공개 미리 보기에서 보고서는 일괄 처리에 포함되어야 하는 VM을 지정하지 않습니다. "호환되는 VM" 섹션에 표시된 디스크 크기를 사용하여 각 VM의 크기를 찾거나 일괄 처리를 위한 VM을 선택하거나 알려진 워크로드 특성을 기반으로 하여 VM을 선택할 수 있습니다. 초기 복제 완료 시간은 실제 VM 디스크 크기, 사용된 디스크 공간 및 사용 가능한 네트워크 처리량에 따라 비례적으로 변경됩니다.

![권장 VM 일괄 처리 크기](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>비용 예측
이 그래프는 사용자가 선택한 대상 지역에 대해 예측한 총 DR(재해 복구) 비용 및 사용자가 보고서 생성을 위해 지정한 통화의 요약 보기를 보여줍니다.

![비용 예측 요약](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

이 요약은 Azure Site Recovery를 사용하여 호환 가능한 모든 VM을 Azure로 보호하는 경우에 스토리지, 계산, 네트워크 및 라이선스에 대해 지불해야 하는 비용을 이해하는 데 도움이 됩니다. 비용은 프로파일링된 모든 VM이 아닌 호환 가능한 VM에 대해 계산됩니다.  

월별 또는 연도별 비용을 볼 수 있습니다. [지원되는 대상 지역](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) 및 [지원되는 통화](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)에 대해 자세히 알아보세요.

**구성 요소별 비용** 총 DR 비용은 다음 네 가지 구성 요소로 구분됩니다. 컴퓨팅, 스토리지, 네트워크 및 Azure Site Recovery 라이선스 비용. 비용은 복제 중에 발생하는 사용량 및 DR 드릴 시간에 계산, 스토리지(프리미엄 및 표준), 온-프레미스 사이트와 Azure 사이에 구성된 ExpressRoute/VPN 및 Azure Site Recovery 라이선스에 대해 발생하는 사용량을 기반으로 계산됩니다.

**상태별 비용**: 총 DR(재해 복구) 비용은 복제 및 DR 드릴이라는 두 가지 다른 상태를 기반으로 분류됩니다.

**복제 비용**:  복제 중에 발생하는 비용입니다. 저장소, 네트워크 및 Azure Site Recovery 라이선스 비용이 포함됩니다.

**DR 드릴 비용**: 테스트 장애 조치(failover) 중에 발생하는 비용입니다. Azure Site Recovery는 테스트 장애 조치(failover) 중에 VM을 작동합니다. DR 드릴 비용은 VM의 계산 및 스토리지를 실행하는 비용을 포함합니다.

**월간/연간 Azure 저장소 비용**: 복제 및 DR 드릴을 위해 프리미엄 및 표준 저장소에 대해 발생할 총 저장소 비용을 보여줍니다.
VM당 자세한 비용 분석은 [비용 예측](site-recovery-vmware-deployment-planner-cost-estimation.md) 시트에서 볼 수 있습니다.

### <a name="growth-factor-and-percentile-values-used"></a>사용된 증가율 및 백분위 수 값
시트 아래쪽의 이 섹션에는 프로파일링된 VM의 모든 성능 카운터에 사용된 백분위수 값(기본값: 95번째 백분위수)과 모든 계산에 사용된 증가율(기본값: 30%)이 표시됩니다.

![사용된 증가율 및 백분위 수 값](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>입력으로 사용 가능한 대역폭을 사용하는 권장 사항

![입력으로 사용 가능한 대역폭을 사용하는 권장 사항](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Site Recovery 복제를 위해 x Mbps 이상의 대역폭을 설정할 수 없다는 것을 알고 있는 상황이 있을 수 있습니다. 이 도구를 사용하면 사용 가능한 대역폭을 입력하고(보고서 생성 중에 -Bandwidth 매개 변수 사용), 달성 가능한 RPO(분)를 가져올 수 있습니다. 이처럼 달성 가능한 RPO 값을 사용하여 추가 대역폭을 설정해야 하는지 또는 이 RPO로 재해 복구 솔루션을 사용하는 것이 좋은지 여부를 결정할 수 있습니다.

![500 Mbps 대역폭에 사용 가능한 RPO](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>VM-저장소 배치

>[!Note]
>디스크 관리에 직접 복제 하는 경우 저장소 계정의 수에 걱정할 필요가 없습니다. 저장소에 대 한 권장 구성만 storage (Standard 또는 Premium)의 형식에 사용 합니다. 형식이 같은 관리 디스크에 적용 됩니다.

![VM-저장소 배치](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**디스크 스토리지 유형**: **배치할 VM** 열에 언급된 해당 VM을 모두 복제하는 데 사용되는 표준 또는 프리미엄 스토리지 계정입니다.

**제안된 접두사**: 스토리지 계정의 이름을 지정하는 데 사용할 수 있는 3자로 구성된 접두사 제안입니다. 고유한 접두사를 사용할 수 있지만 도구의 제안은 [저장소 계정에 대 한 파티션 명명 규칙](https://aka.ms/storage-performance-checklist)을 따릅니다.

**제안된 계정 이름**: 제안된 접두사 다음에 포함시킬 스토리지 계정 이름입니다. 꺾쇠 괄호(< 및 >) 안의 이름을 사용자 지정 입력으로 바꿉니다.

**로그 스토리지 계정**: 모든 복제 로그는 표준 스토리지 계정에 저장됩니다. Premium Storage 계정에 복제하는 VM의 경우 로그 스토리지에 대한 추가 표준 스토리지 계정을 설정합니다. 여러 프리미엄 복제 저장소 계정에서 단일 표준 로그 저장소 계정을 사용할 수 있습니다. 표준 스토리지 계정에 복제된 VM은 로그에 대해 동일한 스토리지 계정을 사용합니다.

**제안된 로그 계정 이름**: 제안된 접두사 다음에 포함시킬 스토리지 로그 계정 이름입니다. 꺾쇠 괄호(< 및 >) 안의 이름을 사용자 지정 입력으로 바꿉니다.

**배치 요약**: 복제 및 테스트 장애 조치(failover) 또는 장애 조치 시 스토리지 계정의 총 VM 부하에 대한 요약입니다. 저장소 계정에 매핑된 총 VM 수, 이 저장소 계정에 배치되는 모든 VM의 총 읽기/쓰기 IOPS, 총 쓰기(복제) IOPS, 모든 디스크에 설정된 총 크기 및 총 디스크 수를 포함합니다.

**배치할 Virtual Machines**: 최적의 성능과 사용을 위해 지정된 스토리지 계정에 배치해야 하는 모든 VM의 목록입니다.

## <a name="compatible-vms"></a>호환되는 VM
![호환되는 VM에 대한 Excel 스프레드시트](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM 이름**: 보고서가 생성될 때 VMListFile에 사용되는 VM 이름 또는 IP 주소입니다. 또한 이 열은 VM에 연결된 디스크(VMDK)를 나열합니다. 중복 이름 또는 IP 주소와 vCenter VM을 구분하기 위해 이름에 ESXi 호스트 이름을 포함합니다. 나열된 ESXi 호스트는 도구가 프로 파일링 기간 중에 발견한 VM을 배치하는 호스트입니다.

**VM 호환성**: 값은 **예** 및 **예**\*입니다. **예**\*는 VM이 [프리미엄 SSD](../virtual-machines/windows/disks-types.md)에 적합한 인스턴스에 대한 값입니다. 여기서 프로파일링된 높은 변동량 또는 IOPS 디스크는 P20 또는 P30 범주에 적합하지만 디스크의 크기 때문에 P10 또는 P20에 낮게 매핑됩니다. 스토리지 계정 크기에 따라 디스크를 매핑할 Premium Storage 디스크 유형이 결정됩니다. 예를 들면 다음과 같습니다.
* 128GB 미만은 P10입니다.
* 128GB ~ 256GB는 P15입니다.
* 256GB ~ 512GB는 P20입니다.
* 512GB ~ 1,024GB는 P30입니다.
* 1,025GB ~ 2,048GB는 P40입니다.
* 2,049GB ~ 4,095GB는 P50입니다.

예를 들어, 디스크의 워크로드 특성이 P20 또는 P30 범주에 속하지만 크기가 더 낮은 Premium Storage 디스크 유형에 낮게 매핑되는 경우 도구에서 해당 VM이 **예**\*로 표시됩니다. 또한 도구에서는 원본 디스크 크기를 권장 Premium Storage 디스크 유형에 맞게 변경하거나 대상 디스크 유형 사후 장애 조치를 변경할 것을 권장합니다.

**스토리지 유형**: 표준 또는 프리미엄입니다.

**제안된 접두사**: 3자로 된 스토리지 계정 접두사입니다.

**스토리지 계정**: 제안된 스토리지 계정 접두사를 사용하는 이름입니다.

**최고 R/W IOPS(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 워크로드 읽기/쓰기 IOPS(기본값: 95번째 백분위수)입니다. 참고로 VM의 최고 읽기/쓰기 IOPS는 프로파일링 기간의 매분마다 개별 디스크의 읽기/쓰기 IOPS를 합친 최고값이기 때문에 VM의 총 읽기/쓰기 IOPS가 항상 VM에 속한 개별 디스크의 읽기/쓰기 IOPS 합계가 되는 것은 아닙니다.

**최고 데이터 변동률(Mbps)(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 변동률(기본값: 95번째 백분위수)입니다. VM의 총 데이터 변동은 프로파일링 기간의 매분마다 개별 디스크 변동 합계의 최고값이기 때문에 VM의 총 데이터 변동이 항상 VM에 속한 개별 디스크의 데이터 변동 합계가 되는 것은 아닙니다.

**Azure VM 크기**: 이 온-프레미스 VM에 대해 이상적으로 매핑된 Azure Cloud Services 가상 머신 크기입니다. 매핑은 온-프레미스 VM의 메모리, 디스크/코어/NIC 수 및 읽기/쓰기 IOPS를 기반으로 합니다. 항상 모든 온-프레미스 VM 특성과 일치하는 가장 낮은 Azure VM 크기를 권장합니다.

**디스크 수**: VM의 총 VMDK(가상 머신 디스크) 수입니다.

**디스크 크기(GB)**: VM의 모든 디스크의 총 설정 크기입니다. 또한 도구에서는 VM의 개별 디스크에 대한 디스크 크기도 보여 줍니다.

**코어**: VM의 CPU 코어 수입니다.

**메모리(MB)**: VM의 RAM 크기입니다.

**NIC**: VM의 NIC 수입니다.

**부팅 유형**: VM의 부팅 유형입니다. BIOS 또는 EFI일 수 있습니다.  현재 Azure Site Recovery는 부팅 디스크의 파티션 수가 4개 미만이고 부팅 섹터 크기가 512바이트인 경우 Windows Server EFI VM(Windows Server 2012, 2012 R2 및 2016)을 지원합니다. EFI VM을 보호하려면 Azure Site Recovery 모바일 서비스 버전이 9.13 이상이어야 합니다. EFI VM에서는 장애 조치만 지원되고, 장애 복구는 지원되지 않습니다.  

**OS 유형**: VM의 OS 유형입니다. Windows, Linux 또는 VM을 만드는 동안 VMware vSphere에서 선택된 템플릿을 기반으로 하는 다른 OS일 수도 있습니다.  

## <a name="incompatible-vms"></a>호환되지 않는 VM

![호환되지 않는 VM에 대한 Excel 스프레드시트
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM 이름**: 보고서가 생성될 때 VMListFile에 사용되는 VM 이름 또는 IP 주소입니다. 또한 이 열은 VM에 연결된 VMDK를 나열합니다. 중복 이름 또는 IP 주소와 vCenter VM을 구분하기 위해 이름에 ESXi 호스트 이름을 포함합니다. 나열된 ESXi 호스트는 도구가 프로 파일링 기간 중에 발견한 VM을 배치하는 호스트입니다.

**VM 호환성**: 지정된 VM을 Site Recovery에 사용할 수 없는 이유를 나타냅니다. 이유는 게시된 [저장소 한도](https://aka.ms/azure-storage-scalbility-performance)를 기반으로 VM의 각 호환되지 않는 디스크에 대해 설명되며 다음 중 하나일 수 있습니다.

* 디스크 크기가 4,095GB보다 큽니다. Azure Storage는 현재 4,095GB보다 큰 디스크 크기를 지원하지 않습니다.

* OS 디스크는 2,048GB보다 큽니다. Azure Storage는 현재 2,048GB보다 큰 OS 디스크 크기를 지원하지 않습니다.

* 총 VM 크기(복제 + TFO)가 지원되는 저장소 계정 크기 한도(35TB)를 초과합니다. 이러한 비호환성은 일반적으로 VM의 단일 디스크가 표준 저장소에 대해 지원되는 최대 Azure 또는 Site Recovery 한도를 초과하는 성능 특성을 가지고 있는 경우에 발생합니다. 이러한 인스턴스는 VM을 Premium Storage 영역에 푸시합니다. 그러나 Premium Storage 계정의 최대 지원 크기는 35TB이며, 보호된 단일 VM을 여러 스토리지 계정에서 보호할 수 없습니다. 또한 보호되는 VM에 대해 테스트 장애 조치를 실행하면 복제가 진행 중인 저장소 계정과 동일한 계정에서 실행된다는 점에 유의해야 합니다. 이 인스턴스에서 복제 진행과 테스트 장애 조치를 동시에 성공하려면 디스크 크기를 2배로 설정합니다.

* 원본 IOPS가 디스크당 지원되는 저장소 IOPS 한도(7500)를 초과합니다.

* 원본 IOPS가 VM당 지원되는 저장소 IOPS 한도(80,000)를 초과합니다.

* 평균 데이터 변동률이 디스크의 평균 I/O 크기에 대해 지원되는 Site Recovery 데이터 변동률 한도(10MB/s)를 초과합니다.

* 평균 데이터 변동률이 VM의 평균 I/O 크기(모든 디스크 변동률의 합계)에 지원되는 Site Recovery 데이터 변동률 한도(25MB/s)를 초과합니다.

* VM의 모든 디스크에 대한 최고 데이터 변동률이 지원되는 Site Recovery 최고 데이터 변동률 한도(54MB/s)를 초과합니다.

* 평균 유효 쓰기 IOPS가 디스크에 대해 지원되는 Site Recovery IOPS 한도(840)를 초과합니다.

* 계산된 스냅숏 저장소가 지원되는 스냅숏 저장소 한도(10TB)를 초과합니다.

* 1일 총 데이터 변동률이 프로세스 서버에서 지원되는 2TB의 1일 변동률 한도(2TB)를 초과합니다.


**최고 R/W IOPS(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 워크로드 IOPS(기본값: 95번째 백분위수)입니다. 참고로 VM의 최고 읽기/쓰기 IOPS는 프로파일링 기간의 매분마다 개별 디스크의 읽기/쓰기 IOPS를 합친 최고값이기 때문에 VM의 총 읽기/쓰기 IOPS가 항상 VM에 속한 개별 디스크의 읽기/쓰기 IOPS 합계가 되는 것은 아닙니다.

**최고 데이터 변동률(Mbps)(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 변동률(기본값: 95번째 백분위 수)입니다. VM의 총 데이터 변동은 프로파일링 기간의 매분마다 개별 디스크 변동 합계의 최고값이기 때문에 VM의 총 데이터 변동이 항상 VM에 속한 개별 디스크의 데이터 변동 합계가 되는 것은 아닙니다.

**디스크 수**: VM의 총 VMDK 수입니다.

**디스크 크기(GB)**: VM의 모든 디스크의 총 설정 크기입니다. 또한 도구에서는 VM의 개별 디스크에 대한 디스크 크기도 보여 줍니다.

**코어**: VM의 CPU 코어 수입니다.

**메모리(MB)**: VM의 RAM 크기입니다.

**NIC**: VM의 NIC 수입니다.

**부팅 유형**: VM의 부팅 유형입니다. BIOS 또는 EFI일 수 있습니다.  현재 Azure Site Recovery는 부팅 디스크의 파티션 수가 4개 미만이고 부팅 섹터 크기가 512바이트인 경우 Windows Server EFI VM(Windows Server 2012, 2012 R2 및 2016)을 지원합니다. EFI VM을 보호하려면 Azure Site Recovery 모바일 서비스 버전이 9.13 이상이어야 합니다. EFI VM에서는 장애 조치만 지원되고, 장애 복구는 지원되지 않습니다.

**OS 유형**:  VM의 OS 유형입니다. Windows, Linux 또는 VM을 만드는 동안 VMware vSphere에서 선택된 템플릿을 기반으로 하는 다른 OS일 수도 있습니다.

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery 제한
다음 테이블에는 Azure Site Recovery 제한이 제공됩니다. 이러한 한도는 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O 조합을 다룰 수는 없습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다. 최상의 결과를 얻으려면 배포를 계획한 후에도 항상 테스트 장애 조치를 통해 광범위한 애플리케이션 테스트를 수행하여 애플리케이션의 진정한 성능 상황을 이해하는 것이 좋습니다.

**복제 저장소 대상** | **평균 원본 디스크 I/O 크기** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB  | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |  디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 | 20 MB/s | 디스크당 1684 GB

**원본 데이터 변동률** | **최대 제한**
---|---
VM당 평균 데이터 변동률| 25MB/초
VM의 모든 디스크에 대한 최고 데이터 변동률 | 54MB/초
프로세스 서버에서 지원하는 1일 최대 데이터 변동률 | 2TB

여기서는 I/O가 30% 겹치고 있다고 가정하는 평균 숫자입니다. Site Recovery는 중첩 비율, 더 큰 쓰기 크기 및 실제 워크로드 I/O 동작에 따라 더 높은 처리량을 다룰 수 있습니다. 앞의 숫자는 약 5분의 일반적인 백로그가 있다고 가정합니다. 즉, 데이터를 업로드한 후에 처리되며 5분 내에 복구 지점이 생성됩니다.


## <a name="cost-estimation"></a>비용 예측
[비용 예측](site-recovery-vmware-deployment-planner-cost-estimation.md)에 대해 자세히 알아봅니다.


## <a name="next-steps"></a>다음 단계
[비용 예측](site-recovery-vmware-deployment-planner-cost-estimation.md)에 대해 자세히 알아봅니다.
