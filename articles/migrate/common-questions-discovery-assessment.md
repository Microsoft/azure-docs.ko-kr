---
title: Azure Migrate의 검색, 평가 및 종속성 분석에 대 한 질문
description: Azure Migrate에서 검색, 평가 및 종속성 분석에 대 한 일반적인 질문에 대 한 답변을 얻습니다.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: d4f833926541bafbae211caff37e5974b57e1047
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019014"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>검색, 평가 및 종속성 분석-일반적인 질문

이 문서에서는 Azure Migrate의 검색, 평가 및 종속성 분석에 대 한 일반적인 질문에 답변 합니다. 다른 질문이 있는 경우 다음 리소스를 확인 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md)
- [Azure Migrate 어플라이언스](common-questions-appliance.md) 에 대 한 질문
- [서버 마이그레이션](common-questions-server-migration.md) 에 대 한 질문
- [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum) 에서 질문에 대 한 답변 받기


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure Migrate에서 검색 및 평가에 대해 지원 되는 지역은 무엇 인가요?

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>어플라이언스로 검색할 수 있는 Vm 수는 몇 개입니까?

단일 어플라이언스를 사용 하 여 최대 1만 VMware Vm, 최대 5000 Hyper-v Vm 및 최대 1000 개의 물리적 서버를 검색할 수 있습니다. 컴퓨터가 더 많은 경우 [hyper-v 평가를 확장](scale-hyper-v-assessment.md)하거나 [VMware 평가를 확장](scale-vmware-assessment.md)하거나 [물리적 서버 평가](scale-physical-assessment.md)를 확장 하는 방법을 참조 하세요.

## <a name="how-do-i-choose-the-assessment-type"></a>평가 유형을 선택하려면 어떻게 해야 하나요?

- Azure vm으로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md), [hyper-v vm](how-to-set-up-appliance-hyper-v.md)및 [물리적 서버](how-to-set-up-appliance-physical.md) 를 평가 하려는 경우 **azure vm 평가** 를 사용 합니다. [자세한 내용](concepts-assessment-calculation.md)

- 이 평가 유형을 사용 하 여 [Azure Vmware 솔루션 (avs)](../azure-vmware/introduction.md) 으로 마이그레이션하기 위한 온-프레미스 [VMware vm](how-to-set-up-appliance-vmware.md) 을 평가 하려는 경우에는 **azure vmware solution (avs)** 평가를 사용 합니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

- 두 가지 유형의 평가를 실행하는 경우에만 VMware 컴퓨터에서 공통 그룹을 사용할 수 있습니다. Azure Migrate에서 AVS 평가를 처음 실행하는 경우 새 VMware 컴퓨터 그룹을 만드는 것이 좋습니다.
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>평가 보고서에서 일부 또는 모든 VM의 성능 데이터가 누락된 이유는 무엇인가요?

"성능 기반" 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 VM에 대한 성능 데이터를 수집할 수 없는 경우 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 VM의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터가 누락되었지만 Hyper-V VM을 평가하려는 경우 이러한 VM에서 동적 메모리를 사용하도록 설정했는지 확인합니다. 현재 Azure Migrate 어플라이언스가 이러한 VM의 메모리 사용률을 수집할 수 없기 때문에 알려진 문제가 있습니다.
- 모든 성능 카운터가 누락된 경우 포트 443(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

참고 - 임의의 성능 카운터가 누락된 경우 Azure Migrate: 서버 평가는 할당된 코어/메모리 온-프레미스로 대체하고 해당하는 VM 크기를 권장합니다.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>내 평가의 신뢰 등급이 낮은 이유는 무엇인가요?

신뢰 등급은 평가를 계산하는 데 필요한 [사용 가능한 데이터 요소](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#ratings)의 백분율을 기준으로 "성능 기반" 평가에 대해 계산됩니다. 아래에는 평가의 신뢰 등급이 낮아질 수 있는 이유가 나와 있습니다.

- 평가를 작성하는 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 일주일로 설정하여 평가를 만드는 경우 검색 시작 후 일주일 이상 기다려야 데이터 요소가 수집됩니다. 이 기간 동안 기다릴 수 없으면 성능 기간을 더 짧은 기간으로 변경하고 평가를 '다시 계산'하세요.
 
- 서버 평가는 평가 기간에 일부 또는 모든 VM에 대한 성능 데이터를 수집할 수 없습니다. 평가 기간 동안 VM의 전원이 켜져 있는지, 포트 443에 대한 아웃바운드 연결이 허용되는지 확인하세요. Hyper-V VM의 경우 동적 메모리를 사용하도록 설정하면 메모리 카운터가 누락되어 신뢰 등급이 낮아집니다. 신뢰 등급의 최신 변경 내용을 반영하려면 평가를 '다시 계산'하세요. 

- Server Assessment에서 검색이 시작된 후 VM 몇 개가 생성되었습니다. 예를 들어 마지막 1달의 성능 기록에 대한 평가를 만들려고 하는데, 일부 VM이 불과 일주일 전에 환경에서 생성되었습니다. 이 경우 새 VM의 성능 데이터를 전체 기간에 사용할 수 없으며 신뢰 등급이 낮아집니다.

신뢰 등급에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#confidence-ratings-performance-based).

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 솔루션 (AVS) 평가를 만들 때 일부 그룹을 볼 수 없습니다.

- VMware 컴퓨터만 있는 그룹에서 AVS 평가를 수행할 수 있습니다. AVS 평가를 수행하려는 경우 그룹에서 VMware가 아닌 컴퓨터를 모두 제거하세요.
- Azure Migrate에서 AVS 평가를 처음 실행하는 경우 새 VMware 컴퓨터 그룹을 만드는 것이 좋습니다.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Azure Government에서 일부 VM 유형을 볼 수 없습니다.

평가 및 마이그레이션에 지원 되는 VM 유형은 Azure Government 위치의 가용성에 따라 달라 집니다. Azure Government에서 VM 유형을 [검토 하 고 비교할](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) 수 있습니다.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>VM의 크기가 변경 되었습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집 합니다.  평가는 온-프레미스 Vm의 지정 시간 스냅숏입니다. 평가 하려는 VM의 설정을 변경 하는 경우 다시 계산 옵션을 사용 하 여 평가를 최신 변경 내용으로 업데이트 합니다.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>다중 테 넌 트 환경에서 Vm을 검색 어떻게 할까요??

- **VMware**: 환경이 테 넌 트 간에 공유 되 고 다른 테 넌 트의 구독에서 테 넌 트의 vm을 검색 하지 않으려는 경우 검색 하려는 vm에만 액세스할 수 있는 VMware vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure Migrate 어플라이언스에서 검색을 시작할 때 해당 자격 증명을 사용 합니다.
- **Hyper-v**: 검색에서 hyper-v 호스트 자격 증명을 사용 합니다. Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

## <a name="do-i-need-vcenter-server"></a>VCenter Server 필요 한가요?

예, Azure Migrate는 VMware 환경에서 vCenter Server를 사용 하 여 검색을 수행 해야 합니다. Azure Migrate는 vCenter Server으로 관리 되지 않는 ESXi 호스트의 검색을 지원 하지 않습니다.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM 평가의 크기 조정 옵션은 무엇 인가요?

온-프레미스 크기 조정으로 Azure Migrate는 평가를 위해 VM 성능 데이터를 고려 하지 않습니다. Azure Migrate은 온-프레미스 구성에 따라 VM 크기를 평가 합니다. 성능 기반 크기 조정을 사용 하 여 크기는 사용률 데이터를 기반으로 합니다.

예를 들어 온-프레미스 VM에 4 개의 코어와 8gb의 메모리가 50% CPU 사용률, 50%의 메모리 사용률을 포함 하는 경우:
- 온-프레미스 크기 조정에서는 4 개의 코어와 8gb의 메모리가 있는 Azure VM SKU를 권장 합니다.
- 성능 기반 크기 조정에서는 두 개의 코어와 4gb의 메모리가 있는 VM SKU를 사용 하는 것이 좋습니다.

마찬가지로 디스크 크기 조정은 크기 조정 조건 및 저장소 유형에 따라 달라 집니다.
- 크기 조정 기준이 성능 기반이 고 저장소 유형이 자동 인 경우, Azure Migrate 대상 디스크 유형 (Standard 또는 Premium)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려 합니다.
- 크기 조정 기준이 성능 기반이 고 저장소 유형이 프리미엄 인 경우 Azure Migrate 온-프레미스 디스크의 크기에 따라 프리미엄 디스크 SKU를 권장 합니다. 크기 조정이 온-프레미스이 고 저장소 유형이 Standard 또는 Premium 인 경우 동일한 논리가 디스크 크기 조정에 적용 됩니다.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>성능 기록 및 사용률이 Azure VM 평가의 크기 조정에 영향을 미칩니까?

예, 성능 기록 및 사용률이 Azure VM 평가의 크기 조정에 영향을 줍니다.

### <a name="performance-history"></a>성능 기록

성능 기반 크기 조정의 경우에만 Azure Migrate 온-프레미스 컴퓨터의 성능 기록을 수집 하 고이를 사용 하 여 Azure에서 VM 크기 및 디스크 형식을 권장 합니다.

1. 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 실시간 사용률 데이터를 수집 합니다.
2. 어플라이언스는 수집 된 20 초 샘플을 롤업 하 고이를 사용 하 여 15 분 마다 단일 데이터 요소를 만듭니다.
3. 어플라이언스는 데이터 요소를 만들기 위해 20 초의 모든 샘플에서 최고 값을 선택 합니다.
4. 어플라이언스는 데이터 요소를 Azure로 전송 합니다.

### <a name="utilization"></a>사용률

Azure에서 평가를 만들 때 성능 기간 및 성능 기록 백분위 수 값에 따라 Azure Migrate는 유효한 사용률 값을 계산 하 여 크기 조정에 사용 합니다.

예를 들어, 성능 기간을 1 일로 설정 하 고 백분위 수 값을 95 번째 백분위 수로 설정 하는 경우 Azure Migrate는 수집기에서 지난 날짜의 오름차순으로 보낸 15 분 샘플 지점을 정렬 합니다. 95 번째 백분위 수 값을 유효 사용률으로 선택 합니다.

95 번째 백분위 수 값을 사용 하면 이상 값이 무시 됩니다. Azure Migrate에서 99 번째 백분위 수를 사용 하는 경우 이상 값이 포함 될 수 있습니다. 이상 값이 누락 되지 않은 기간에 대 한 피크 사용량을 선택 하려면 99 번째 백분위 수를 사용 하도록 Azure Migrate를 설정 합니다.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>가져오기 기반 평가는 검색 원본을 어플라이언스로 사용 하는 평가와 어떻게 다르며?

가져오기 기반 Azure VM 평가는 CSV 파일을 사용 하 여 Azure Migrate로 가져온 컴퓨터로 생성 된 평가입니다. 서버 이름, 코어, 메모리 및 운영 체제 중에서 4 개의 필드만을 가져와야 합니다. 유의 해야 할 몇 가지 사항은 다음과 같습니다. 
 - 부팅 형식 매개 변수에 대 한 가져오기 기반 평가에서 준비 기준이 더 엄격 하지 않습니다. 부팅 유형을 제공 하지 않으면 컴퓨터에 BIOS 부팅 유형이 있고 컴퓨터가 **조건부로 준비**된 것으로 표시 되어 있지 않다고 가정 합니다. 검색 원본을 어플라이언스로 평가 하는 경우 부팅 유형이 없는 경우 준비 상태는 조건에 따라 **준비** 로 표시 됩니다. 준비 계산의 이러한 차이점은 사용자에 게 가져오기 기반 평가를 수행 하는 경우 마이그레이션 계획의 초기 단계에서 컴퓨터에 대 한 모든 정보가 없을 수 있기 때문입니다. 
 - 성능 기반 가져오기 평가에서는 사용자가 올바른 크기 조정 계산에 제공 하는 사용률 값을 사용 합니다. 사용률 값은 사용자가 제공 하므로 평가 속성에서 **성능 기록** 및 **백분위 수 사용률** 옵션을 사용할 수 없습니다. 검색 원본을 어플라이언스로 평가에서 선택 된 백분위 수 값은 어플라이언스에서 수집 된 성능 데이터에서 선택 됩니다.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>가져오기 기반 AVS 평가에서 제안 된 마이그레이션 도구가 알 수 없음으로 표시 되는 이유는 무엇 인가요?

CSV 파일을 통해 가져온 컴퓨터의 경우에는 AVS 평가의 기본 마이그레이션 도구를 알 수 없습니다. 그러나 VMware 컴퓨터의 경우에는 HCX (VMware 하이브리드 클라우드 확장) 솔루션을 사용 하는 것이 좋습니다. [자세한 정보](../azure-vmware/hybrid-cloud-extension-installation.md).


## <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하면 더 강력 하 게 마이그레이션할 Vm 그룹을 평가할 수 있습니다. 종속성 시각화는 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 합니다. 이 기능을 사용 하면 아무것도 유지 되지 않고 Azure로 마이그레이션할 때 예기치 않은 중단을 방지할 수 있습니다. Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다. [자세히 알아보기](concepts-dependency-visualization.md).

> [!NOTE]
> 에이전트 기반 종속성 분석은 Azure Government에서 사용할 수 없습니다. 에이전트 없는 종속성 분석을 사용할 수 있습니다.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>에이전트 기반 및 에이전트 없는의 차이점은 무엇 인가요?

에이전트 없는 시각화와 에이전트 기반 시각화 간의 차이점은 표에 요약 되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
지원 | 이 옵션은 현재 미리 보기 상태 이며 VMware Vm에 대해서만 사용할 수 있습니다. 지원 되는 운영 체제를 [검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 합니다. | GA (일반 공급)를 사용 합니다.
에이전트 | 교차 확인 하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. | [MMA (Microsoft Monitoring agent)](../azure-monitor/platform/agent-windows.md)및 [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)를 분석 하려는 각 온-프레미스 컴퓨터에 설치 되는 에이전트입니다. 
필수 구성 요소 | 필수 구성 요소 및 배포 요구 사항을 [검토](concepts-dependency-visualization.md#agentless-analysis) 합니다. | 필수 구성 요소 및 배포 요구 사항을 [검토](concepts-dependency-visualization.md#agent-based-analysis) 합니다.
Log Analytics | 필수 아님. | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)의 [서비스 맵](../azure-monitor/insights/service-map.md) 솔루션을 사용합니다. [자세히 알아보기](concepts-dependency-visualization.md#agent-based-analysis).
작동 방법 | 종속성 시각화에 사용 되는 컴퓨터에서 TCP 연결 데이터를 캡처합니다. 검색 후 5 분 간격으로 데이터를 수집 합니다. | 컴퓨터에 설치 된 서비스 맵 에이전트는 각 프로세스에 대 한 TCP 프로세스 및 인바운드/아웃 바운드 연결에 대 한 데이터를 수집 합니다.
데이터 | 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다. | 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보를 수집 하 고 Log Analytics 쿼리에 사용할 수 있습니다. 
시각화 | 단일 서버에 대 한 종속성 맵은 1 시간에서 30 일 동안 볼 수 있습니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 지도는 한 시간에 한 해 볼 수 있습니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/> 지도 보기에서 그룹의 서버를 추가 하 고 제거 합니다.
데이터 내보내기 | 지난 30 일간의 데이터는 CSV 형식으로 다운로드할 수 있습니다. | Log Analytics를 사용 하 여 데이터를 쿼리할 수 있습니다.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석용 어플라이언스를 배포 해야 하나요?

예, [Azure Migrate 어플라이언스](migrate-appliance.md) 를 배포 해야 합니다.

## <a name="do-i-pay-for-dependency-visualization"></a>종속성 시각화에 대해 비용을 지불 하나요?

아니요. [Azure Migrate 가격 책정](https://azure.microsoft.com/pricing/details/azure-migrate/)에 대해 자세히 알아보세요.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>에이전트 기반 종속성 시각화에는 무엇을 설치 해야 하나요?

에이전트 기반 종속성 시각화를 사용 하려면 평가 하려는 각 온-프레미스 컴퓨터에 에이전트를 다운로드 하 여 설치 합니다.

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)
- [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)
- 인터넷에 연결 되지 않은 컴퓨터가 있는 경우 해당 컴퓨터에 Log Analytics 게이트웨이를 다운로드 하 여 설치 합니다.

에이전트 기반 종속성 시각화를 사용 하는 경우에만 이러한 에이전트가 필요 합니다.

## <a name="can-i-use-an-existing-workspace"></a>기존 작업 영역을 사용해도 되나요?

예, 에이전트 기반 종속성 시각화의 경우 기존 작업 영역을 마이그레이션 프로젝트에 연결 하 고 종속성 시각화에 사용할 수 있습니다. 

## <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 에이전트 기반 시각화에서 종속성 시각화 보고서를 내보낼 수 없습니다. 그러나 Azure Migrate 서비스 맵를 사용 하 고 [서비스 맵 REST API](/rest/api/servicemap/machines/listconnections) 를 사용 하 여 종속성을 JSON 형식으로 검색할 수 있습니다.

## <a name="can-i-automate-agent-installation"></a>에이전트 설치를 자동화할 수 있나요?

에이전트 기반 종속성 시각화의 경우:

- 스크립트를 사용 [하 여 종속성 에이전트를 설치](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent)합니다.
- MMA의 경우 [명령줄 또는 자동화를 사용](../azure-monitor/platform/log-analytics-agent.md#installation-options)하거나 [스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 사용 합니다.
- 스크립트 외에도 Microsoft Endpoint Configuration Manager 및 [Intigua](https://www.intigua.com/intigua-for-azure-migration) 와 같은 배포 도구를 사용 하 여 에이전트를 배포할 수 있습니다.

## <a name="what-operating-systems-does-mma-support"></a>MMA에서 지 원하는 운영 체제는 무엇 인가요?

- [MMA에서 지 원하는 Windows 운영 체제](../azure-monitor/platform/log-analytics-agent.md#installation-options)목록을 봅니다.
- [MMA에서 지 원하는 Linux 운영 체제](../azure-monitor/platform/log-analytics-agent.md#installation-options)목록을 봅니다.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>1 시간 이상 종속성을 시각화할 수 있나요?

에이전트 기반 시각화의 경우 최대 1 시간 동안 종속성을 시각화할 수 있습니다. 기록의 특정 날짜까지 한 달 뒤로 이동할 수 있지만 시각화의 최대 기간은 1 시간입니다. 예를 들어, 종속성 맵의 기간을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 종속성을 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](./how-to-create-group-machine-dependencies.md) 수 있습니다.

에이전트 없는 시각화의 경우 1 시간에서 30 일 사이에 단일 서버의 종속성 맵을 볼 수 있습니다.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>10 개 이상의 Vm 그룹에 대 한 종속성을 시각화할 수 있나요?

Vm을 최대 10 개 포함 하는 그룹에 대 한 [종속성을 시각화할](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 수 있습니다. 10 개 이상의 Vm이 있는 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할 한 다음 종속성을 시각화 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.