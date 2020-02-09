---
title: Azure Migrate의 일반적인 질문-검색, 평가 및 종속성 분석
description: Azure Migrate에서 검색, 평가 및 종속성 분석에 대 한 일반적인 질문에 대 한 답변을 얻습니다.
ms.topic: conceptual
ms.date: 12/29/2019
ms.openlocfilehash: 201d67c38e9583de16cb6075cc1c4bf826c204fd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059490"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>검색, 평가 및 종속성 분석에 대 한 일반적인 질문

이 문서에서는 Azure Migrate의 검색, 평가 및 종속성 분석에 대 한 일반적인 질문에 답변 합니다. 이 문서를 읽은 후 추가 쿼리가 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시 하세요. 다른 질문이 있는 경우 다음 문서를 검토 하세요.

- Azure Migrate에 대 한 [일반적인 질문](resources-faq.md) 입니다.
- Azure Migrate 어플라이언스에 대 한 [질문](common-questions-appliance.md) 입니다.
- 서버 마이그레이션에 대 한 [질문](common-questions-server-migration.md) 입니다.



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>어플라이언스로 검색할 수 있는 Vm 수는 몇 개입니까?

최대 1만 VMware Vm, 최대 5000 Hyper-v Vm 및 단일 어플라이언스를 포함 하는 최대 250 서버를 검색할 수 있습니다. 온-프레미스 환경에 더 많은 컴퓨터가 있는 경우 [hyper-v](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) 및 [물리적](scale-physical-assessment.md) 평가를 확장 하는 방법을 참조 하세요.



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>VM 크기가 변경 되었습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대 한 정보를 지속적으로 수집 합니다. 하지만 평가는 온-프레미스 Vm의 특정 시점 스냅숏입니다. 평가 하려는 VM의 설정을 변경 하는 경우 다시 계산 옵션을 사용 하 여 평가를 최신 변경 내용으로 업데이트 합니다.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>다중 테넌트 환경에서 Vm을 검색 어떻게 할까요??

- VMware의 경우 사용자 환경이 테넌트 간에 공유 되 고 다른 테넌트의 구독에 있는 한 테넌트의 Vm을 검색 하지 않으려는 경우 검색 하려는 Vm에만 액세스할 수 있는 vCenter Server 자격 증명을 만듭니다. 그런 다음 Azure Migrate 어플라이언스에서 검색을 시작할 때 해당 자격 증명을 사용 합니다.
- Hyper-v의 경우 검색에서 Hyper-v 호스트 자격 증명을 사용 합니다. Vm이 동일한 Hyper-v 호스트를 공유 하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>VMWare VM 검색에 대 한 vCenter Server 필요 한가요?

예, Azure Migrate VMware 환경에서 검색을 수행 하는 vCenter Server 필요 합니다. VCenter Server으로 관리 되지 않는 ESXi 호스트의 검색은 지원 하지 않습니다.


## <a name="whats-the-difference-sizing-options"></a>크기 조정 옵션의 차이점은 무엇 인가요?

온-프레미스 크기 조정으로 Azure Migrate는 평가를 위해 VM 성능 데이터를 고려 하지 않습니다. 온-프레미스 구성에 따라 VM 크기를 평가 합니다. 성능 기반 크기 조정을 사용 하 여 크기는 사용률 데이터를 기반으로 합니다.

- 예를 들어 온-프레미스 VM에 4 개의 코어와 8 GB의 메모리가 50% CPU 사용률 및 50% 메모리 사용률에 있으면 다음이 발생 합니다.
    - 온-프레미스 크기 조정에서는 4 개의 코어와 8gb의 메모리가 있는 Azure VM SKU를 권장 합니다.
    - 성능 기반 크기 조정에서는 2 개 코어 및 4gb의 메모리가 있는 VM SKU를 사용 하는 것이 좋습니다 .이는 사용률 비율이 고려 되기 때문입니다.

- 마찬가지로 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 조건 및 저장소 유형에 따라 달라 집니다.
    - 크기 조정 기준이 성능 기반이 고 저장소 유형이 자동 인 경우, Azure Migrate 대상 디스크 유형 (standard 또는 premium)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려 합니다.
    - 크기 조정 기준이 성능 기반이 고 저장소 유형이 프리미엄 인 경우 Azure Migrate 온-프레미스 디스크의 크기에 따라 프리미엄 디스크 SKU를 권장 합니다. 크기 조정이 온-프레미스이 고 저장소 유형이 표준 또는 프리미엄 인 경우 동일한 논리가 디스크 크기 조정에 적용 됩니다.

## <a name="does-performance-historyutilization-impact-sizing"></a>성능 기록/사용률이 크기 조정에 영향을 미칩니까?

이러한 속성은 성능 기반 크기 조정에만 적용됩니다.

- Azure Migrate 온-프레미스 컴퓨터의 성능 기록을 수집 하 고이를 사용 하 여 Azure에서 VM 크기 및 디스크 형식을 권장 합니다.
- 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 실시간 사용률 데이터를 수집 합니다.
- 어플라이언스는 20 초 샘플을 롤업 하 고 15 분 마다 단일 데이터 요소를 만듭니다.
- 어플라이언스는 데이터 요소를 만들기 위해 20 초의 모든 샘플에서 최고 값을 선택 합니다.
- 어플라이언스는 Azure에이 데이터 요소를 보냅니다.

성능 기간 및 성능 기록 백분위 수 값을 기준으로 Azure에서 평가를 만들 때 Azure Migrate는 유효한 사용률 값을 계산 하 고 크기 조정에 사용 합니다.

- 예를 들어 성능 기간을 1 일로 설정 하 고 백분위 수 값을 95 백분위 수로 설정 하는 경우, Azure Migrate는 수집기에서 지난 날짜의 15 분 샘플 지점을 오름차순으로 정렬 하 고 95 번째 백분위 수 값을 유효 하 게 선택 합니다. 효율.
- 95 번째 백분위 수 값을 사용 하면 이상 값이 무시 됩니다. 99 번째 백분위 수를 사용 하는 경우 이상 값이 포함 될 수 있습니다. 이상 값을 누락 하지 않고 기간의 피크 사용을 선택 하려면 99 번째 백분위 수를 선택 합니다.

## <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하 여 마이그레이션을 위한 Vm 그룹을 보다 확실 하 게 평가 합니다. 종속성 시각화는 평가를 실행 하기 전에 컴퓨터 종속성을 교차 확인 합니다. 이 기능을 사용 하면 아무것도 유지 되지 않으므로 Azure로 마이그레이션할 때 예기치 않은 중단을 방지할 수 있습니다. Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다. [자세히 알아보세요](concepts-dependency-visualization.md).

> [!NOTE]
> Azure Government에서 종속성 시각화를 사용할 수 없습니다.

## <a name="do-i-pay-for-dependency-visualization"></a>종속성 시각화에 대해 비용을 지불 하나요?
No. Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-dependency-visualization"></a>종속성 시각화에는 무엇을 설치 해야 하나요?

종속성 시각화를 사용하려면 평가할 각 온-프레미스 머신에 에이전트를 다운로드하여 설치해야 합니다.

각 컴퓨터에 다음 에이전트를 설치 해야 합니다.
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent).
- 인터넷에 연결 되지 않은 컴퓨터가 있는 경우 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다.

종속성 시각화를 사용 하지 않는 경우 이러한 에이전트가 필요 하지 않습니다.

## <a name="can-i-use-an-existing-workspace"></a>기존 작업 영역을 사용해도 되나요?

예, 기존 작업 영역을 마이그레이션 프로젝트에 연결 하 고 종속성 시각화에 사용할 수 있습니다. [자세히 알아봅니다](concepts-dependency-visualization.md#how-does-it-work).

## <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 종속성 시각화를 내보낼 수 없습니다. 그러나 Azure Migrate 서비스 맵를 사용 하 고 [서비스 맵 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 를 사용 하 여 종속성을 JSON 형식으로 검색할 수 있습니다.

## <a name="can-i-automate--mmadependency-agent-installation"></a>MMA/종속성 에이전트 설치를 자동화할 수 있나요?

이 [스크립트를 사용 하 여 종속성 에이전트를 설치](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)합니다. 명령줄 또는 자동화를 사용 하 여 [MMA을 설치 하려면 다음 지침을](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 따르세요. MMA의 경우 [이 스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 사용 합니다.

스크립트 외에도 Microsoft Endpoint Configuration Manager 및 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 와 같은 배포 도구를 사용 하 여 에이전트를 배포할 수 있습니다.


## <a name="what-operating-systems-does-mma-support"></a>MMA에서 지 원하는 운영 체제는 무엇 인가요?

- [MMA에서 지 원하는 Windows 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)목록을 봅니다.
- [MMA에서 지 원하는 Linux 운영 체제](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)목록을 봅니다.

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>1 시간 이상 종속성을 시각화할 수 있나요?
No. 최대 1 시간 동안 종속성을 시각화할 수 있습니다. 기록의 특정 날짜로 다시 이동할 수 있습니다 .이 날짜는 한 달에 다시 표시 되지만 시각화의 최대 기간은 1 시간입니다. 예를 들어 종속성 맵의 기간을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 종속성을 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용 하 여 더 긴 기간 동안 [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) 수 있습니다.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>10 개 이상의 Vm 그룹에 대 한 종속성을 시각화할 수 있나요?
최대 10 개의 Vm을 포함 하는 그룹에 대 한 [종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 10 개 이상의 Vm이 포함 된 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할 한 다음 종속성을 시각화 하는 것이 좋습니다.




## <a name="next-steps"></a>다음 단계
[Azure Migrate 개요](migrate-services-overview.md)를 참조 하세요.
