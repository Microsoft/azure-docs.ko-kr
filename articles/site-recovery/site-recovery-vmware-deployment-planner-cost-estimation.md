---
title: Azure Site Recovery Deployment Planner에서 비용 예측 보고서 검토 | Microsoft Docs
description: 이 문서에서는 Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner에서 비용 예측 보고서를 검토하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/14/2019
ms.author: mayg
ms.openlocfilehash: 8a36a80903a47bb4163666baf86ed8dac13a00de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471132"
---
# <a name="review-the-cost-estimation-report-in-the-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위해 Site Recovery Deployment Planner에서 비용 예측 보고서 검토

Deployment Planner 보고서는 [권장 사항](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) 시트에 비용 예측 요약을 제공하고 비용 예측 시트에 자세한 비용 분석을 제공합니다. VM당 자세한 비용 분석이 있습니다. 

>[!Note]
>현재 버전의 Deployment planner 도구에서는 Vm에 대 한 비용 예측 Managed Disks로 복제 합니다.
>* DR 드릴 비용 예측 ' 계산 및 네트워크 ' 블레이드에서 ' Managed Disks 사용 ' 매개 변수가 "Yes" 설정 된 경우 저장소 계정 및 managed disks에 대 한 동일 합니다.
>* 복제에 대 한 대략적인 연간 비용 추정 하려면 다음 임시 설정에서 확인 하십시오 **비용 예측** 시트:
>    * "비용 기간" 매개 변수 설정 **설정을** "Year" 테이블
>    * **자세한 비용 분석** 테이블, 12 '연간에서 DR 드릴 횟수 번호' 열을 설정 하 고 "각 DR 드릴 기간 (일)" 30 
>    * 복제 비용의 연간 열 'R' 예: DR 드릴 저장소 비용에 채워진 비용 비슷해야 **연간 DR 드릴 비용** 하위 섹션입니다.

### <a name="cost-estimation-summary"></a>비용 예측 요약 
이 그래프는 사용자가 선택한 대상 지역에 대해 예측한 총 DR(재해 복구) 비용 및 사용자가 보고서 생성을 위해 지정한 통화의 요약 보기를 보여줍니다.
비용 예측 요약

![비용 예측 요약](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

이 요약은 Azure Site Recovery를 사용하여 호환 가능한 모든 VM을 Azure로 보호하는 경우에 스토리지, 계산, 네트워크 및 라이선스에 대해 지불해야 하는 비용을 이해하는 데 도움이 됩니다. 비용은 프로파일링된 모든 VM이 아닌 호환 가능한 VM에 대해 계산됩니다.  
 
월별 또는 연도별 비용을 볼 수 있습니다. [지원되는 대상 지역](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) 및 [지원되는 통화](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)에 대해 자세히 알아보세요.

**구성 요소별 비용** 총 DR 비용은 다음 네 가지 구성 요소로 구분됩니다. 컴퓨팅, 스토리지, 네트워크 및 Azure Site Recovery 라이선스 비용. 비용은 복제 중에 발생하는 사용량 및 DR 드릴 시간에 계산, 스토리지(프리미엄 및 표준), 온-프레미스 사이트와 Azure 사이에 구성된 ExpressRoute/VPN 및 Azure Site Recovery 라이선스에 대해 발생하는 사용량을 기반으로 계산됩니다.

**상태별 비용**: 총 DR(재해 복구) 비용은 복제 및 DR 드릴이라는 두 가지 다른 상태를 기반으로 분류됩니다. 

**복제 비용**:  복제 중에 발생하는 비용입니다. 저장소, 네트워크 및 Azure Site Recovery 라이선스 비용이 포함됩니다. 

**DR 드릴 비용**: 테스트 장애 조치(failover) 중에 발생하는 비용입니다. Azure Site Recovery는 테스트 장애 조치(failover) 중에 VM을 작동합니다. DR 드릴 비용은 VM의 계산 및 스토리지를 실행하는 비용을 포함합니다. 

**월간/연간 Azure 저장소 비용**: 복제 및 DR 드릴을 위해 프리미엄 및 표준 저장소에 대해 발생할 총 저장소 비용을 보여줍니다.

## <a name="detailed-cost-analysis"></a>자세한 비용 분석
계산, 스토리지, 네트워크 등에 대한 Azure 가격은 Azure 지역마다 다릅니다. 구독, 구독과 연결된 제안 및 지정된 대상 Azure 지역에 기반하여 최신 Azure 가격을 사용하여 지정된 통화로 비용 예측 보고서를 생성할 수 있습니다. 도구에서는 기본적으로 미국 서부 2 Azure 지역과 USD(미국 달러) 통화가 사용됩니다. 다른 지역 및 통화를 사용한 경우, 다음 번에 구독 ID, 제안 ID, 대상 지역 및 통화가 없이 보고서를 생성하면 마지막으로 사용한 대상 지역과 마지막으로 사용한 통화가 비용 예측에 사용됩니다.
이 섹션에는 보고서 생성에 사용한 구독 ID 및 제안 ID가 표시됩니다.  사용하지 않은 경우 비어 있습니다.

전체 보고서에서 회색으로 표시된 셀은 읽기 전용입니다. 흰색 셀은 요구 사항에 따라 수정할 수 있습니다.

![비용 예측 세부 정보 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>구성 요소별 전체 DR 비용
첫 번째 섹션은 구성 요소별 전체 DR 비용과 상태별 DR 비용을 보여줍니다. 

**컴퓨팅**: DR 요구 사항에 대해 Azure에서 실행되는 IaaS VM 비용입니다. DR 드릴(테스트 장애 조치) 중에 Azure Site Recovery에서 만들어진 VM과 Always On 가용성 그룹과 도메인 컨트롤러/도메인 이름 서버가 있는 SQL Server와 같이 Azure에서 실행되는 VM이 포함됩니다.

**저장소**: DR 요구 사항에 대한 Azure Storage 사용 비용입니다. 복제를 위한 저장소 사용량 및 DR 드릴 중 저장소 사용량이 포함됩니다.
네트워크: DR 요구 사항에 대한 ExpressRoute 및 사이트 간 VPN 비용입니다. 

**ASR 라이선스**: 호환 가능한 모든 VM에 대한 Azure Site Recovery 라이선스 비용입니다. 자세한 비용 분석 테이블에 VM을 수동으로 입력하면 해당 VM에 대한 Azure Site Recovery 라이선스 비용도 포함됩니다.

### <a name="overall-dr-cost-by-states"></a>상태별 전체 DR 비용
전체 DR 비용은 복제 및 DR 드릴이라는 두 가지 상태를 기반으로 분류됩니다.

**복제 비용**: 복제 시 발생하는 비용입니다. 저장소, 네트워크 및 Azure Site Recovery 라이선스 비용이 포함됩니다. 

**DR 드릴 비용**: DR 드릴 시 발생하는 비용입니다. Azure Site Recovery는 DR 드릴 중에 VM을 작동합니다. DR 드릴 비용은 실행 중인 VM의 계산 및 스토리지 비용을 포함합니다.
연간 총 DR 드릴 지속 시간 = DR 드릴 횟수 x 각 DR 드릴 지속 시간(일) 평균 DR 드릴 비용(월간) = 총 DR 드릴 비용/12

### <a name="storage-cost-table"></a>저장소 비용 테이블:
이 테이블은 복제 및 DR 드릴에 대해 발생하는 프리미엄 및 표준 저장소 비용을 할인 여부에 관계없이 보여줍니다.

### <a name="site-to-azure-network"></a>사이트 - Azure 네트워크
요구 사항에 따라 적절한 설정을 선택합니다. 

**ExpressRoute**: 기본적으로 도구에서 델타 복제에 필요한 네트워크 대역폭과 일치하는 가장 가까운 ExpressRoute 계획이 선택됩니다. 계획은 요구 사항에 따라 변경할 수 있습니다.

**VPN Gateway**: 사용자 환경에 VPN Gateway가 있는 경우 선택합니다. 기본값은 NA입니다.

**대상 지역**: DR에 대해 지정된 Azure 지역입니다. 계산, 스토리지, 네트워크 및 라이선스에 대해 보고서에 사용된 가격은 해당 지역에 대한 Azure 가격을 기반으로 합니다. 

### <a name="vm-running-on-azure"></a>Azure에서 실행 중인 VM
DR을 위해 Azure에서 Always On 가용성 그룹이 실행되는 SQL Server나 도메인 컨트롤러 또는 DNS VM이 있는 경우 총 DR 비용에서 계산 비용을 고려하도록 VM 수와 크기를 제공할 수 있습니다. 

### <a name="apply-overall-discount-if-applicable"></a>해당되는 경우 전체 할인 적용
전체 Azure 가격에서 할인을 받을 자격이 부여된 Azure 파트너 또는 고객인 경우 이 필드를 사용할 수 있습니다. 도구에서 모든 구성 요소에 할인을 적용합니다(% 단위).

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>가상 머신 유형 수 및 계산 비용(연간)
이 테이블은 Windows VM 및 Windows 외 VM의 수와 DR 드릴 계산 비용을 보여줍니다.

### <a name="settings"></a>설정 
**관리형 디스크 사용**: DR 드릴 시 관리형 디스크 사용 여부를 지정합니다. 기본값은 yes입니다. -UseManagedDisks를 No로 설정하면 관리되지 않는 디스크 가격이 비용 계산에 사용됩니다.

**Currency**: 보고서 생성 시 사용되는 통화입니다. 비용 지속 시간:  월간 또는 연간 비용을 모두 볼 수 있습니다. 

## <a name="detailed-cost-analysis-table"></a>자세한 비용 분석 테이블
![자세한 비용 분석](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png): 이 테이블에는 호환 가능한 각 VM에 대해 자세한 비용이 나열됩니다. 이 테이블을 사용하여 VM을 수동으로 추가하면 프로파일링되지 않은 VM의 Azure DR 비용을 예측할 수 있습니다. 자세한 프로파일링을 수행하지 않고 새로운 재해 복구 배포에 대한 Azure 비용을 예측해야 하는 경우에 유용합니다.
VM을 수동으로 추가하려면: 
1.  'Insert row(행 삽입)' 단추를 클릭하여 시작 행과 끝 행 사이에 새 행을 삽입합니다.

2.  이 구성과 일치하는 대략적인 VM 크기 및 VM 수에 따라 다음 열을 채웁니다. 

* VM 수, IaaS 크기(사용자 선택)
* 저장소 유형(표준/프리미엄)
* VM 총 저장소 크기(GB)
* 연간 DR 드릴 횟수 
* 각 DR 드릴 지속 시간(일) 
* OS 종류
* 데이터 중복 
* Azure 하이브리드 혜택

1. 연간 DR 드릴 횟수, 각 DR 드릴 지속 시간(일), 데이터 중복성 및 Azure 하이브리드 사용 혜택의 경우, 'Apply to all(모든 항목에 적용)' 단추를 클릭하면 테이블의 모든 VM에 동일한 값을 적용할 수 있습니다.

1. 비용을 업데이트하려면 'Re-calculate cost'(비용 다시 계산)을 클릭합니다.

**VM 이름**: VM의 이름입니다.

**VM 수**: 구성과 일치하는 VM의 수입니다. 유사한 구성 VM이 프로파일링되지 않지만 보호되는 경우 기존 VM 수를 업데이트할 수 있습니다.

**IaaS 크기(권장)**: 도구에서 권장하는 호환 가능한 VM의 VM 역할 크기입니다. 

**IaaS 크기(사용자 선택)**: 기본적으로 권장 VM 역할 크기와 같습니다. 요구 사항에 따라 역할을 변경할 수 있습니다. 계산 비용은 사용자가 선택한 VM 역할 크기를 기반으로 합니다.

**스토리지 유형**: VM에 사용되는 스토리지 유형입니다. Standard Storage 또는 Premium Storage입니다.

**VM 총 스토리지 크기(GB)**: VM의 총 스토리지입니다.

**연간 DR 드릴 횟수**: 1년 동안 DR 드릴을 수행하는 횟수입니다. 기본적으로 1년에 4번입니다. 특정 VM의 기간을 수정하거나 맨 위 행에 새 값을 입력하고 'Apply to all(모든 항목에 적용)' 단추를 클릭하여 새 값을 모든 VM에 적용할 수 있습니다. 연간 DR 드릴 횟수와 각 DR 드릴 지속 시간에 따라 총 DR 드릴 비용이 계산됩니다.  

**각 DR 드릴 기간(일)**: 각 DR 드릴의 기간입니다. 기본적으로 [재해 복구 Software Assurance 혜택](https://azure.microsoft.com/pricing/details/site-recovery)당 90일마다 7일입니다. 특정 VM의 기간을 수정하거나 맨 위 행에 새 값을 입력하고 'Apply to all(모든 항목에 적용)' 단추를 클릭하여 새 값을 모든 VM에 적용할 수 있습니다. 총 DR 드릴 비용은 연간 DR 드릴 횟수와 각 DR 드릴 지속 시간을 기반으로 계산됩니다.
  
**OS 유형**: VM의 OS 유형입니다. Windows 또는 Linux입니다. OS 종류가 Windows이면 Azure 하이브리드 사용 혜택을 해당 VM에 적용할 수 있습니다. 

**데이터 중복성**: LRS(로컬 중복 스토리지), GRS(지역 중복 스토리지) 또는 RA-GRS(읽기 액세스 지역 중복 스토리지) 중 하나일 수 있습니다. 기본값은 LRS입니다. 특정 VM에 대한 저장소 계정을 기반으로 유형을 변경하거나 맨 위 행의 유형을 변경하고 'Apply to all'(모든 항목에 적용) 단추를 클릭하여 모든 VM에 새 유형을 적용할 수 있습니다.  복제에 대한 저장 비용은 선택한 데이터 중복성에 대한 가격을 기반으로 계산됩니다. 

**Azure 하이브리드 혜택**: 해당되는 경우 Azure 하이브리드 혜택을 Windows VM에 적용할 수 있습니다.  기본값은 [예]입니다. 특정 VM의 설정을 변경하거나 'Apply to all(모든 항목에 적용)' 단추를 클릭하여 모든 VM을 업데이트할 수 있습니다.

**총 Azure 사용량**: DR에 대한 컴퓨팅, 스토리지 및 Azure Site Recovery 라이선스 비용이 포함됩니다. 선택에 따라 월간 또는 연간 비용이 표시됩니다.

**안정적 상태 복제 비용**: 복제에 대한 스토리지 비용이 포함됩니다.

**총 DR 드릴 비용(평균)**: DR 드릴에 대한 컴퓨팅 및 스토리지 비용이 포함됩니다.

**ASR 라이선스 비용**: Azure Site Recovery 라이선스 비용입니다.

## <a name="supported-target-regions"></a>지원되는 대상 지역
Azure Site Recovery Deployment Planner는 다음 Azure 지역에 대한 비용 예측을 제공합니다. 원하는 지역이 아래 나열되지 않은 경우 다음 지역 중 해당 지역에 가장 가까운 지역을 사용할 수 있습니다.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>지원되는 통화
Azure Site Recovery Deployment Planner는 다음 통화 중 하나로 비용 보고서를 생성할 수 있습니다.

|통화|이름||통화|이름||통화|이름|
|---|---|---|---|---|---|---|---|
|ARS|아르헨티나 페소($)||AUD|호주 달러($)||BRL|브라질 헤알(R$)|
|CAD|캐나다 달러($)||CHF|스위스 프랑. (chf)||DKK|덴마크 크론(kr)|
|EUR|유로(€)||GBP|영국 파운드(£)||HKD|홍콩 달러(HK$)|
|IDR|인도네시아 루피아(Rp)||INR|인도 루피(₹)||JPY|일본 엔(¥)|
|KRW|한국 원(₩)||MXN|멕시코 페소(MX$)||MYR|말레이시아 링깃(RM$)|
|NOK|노르웨이 크론(kr)||NZD|뉴질랜드 달러($)||RUB|러시아 루블(руб)|
|SAR|사우디아라비아 리얄(SR)||SEK|스웨덴 크로나(kr)||TWD|대만 달러(NT$)|
|TRY|터키 리라(TL)||USD| 미국 달러($)||ZAR|남아프리카공화국 랜드(R)|

## <a name="next-steps"></a>다음 단계
[Azure Site Recovery를 사용하여 VMware VM을 Azure로](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure) 보호하는 내용에 대해 자세히 알아보세요.
