---
title: Azure Migrate에서 그룹 종속성 매핑을 사용하여 평가 그룹 구체화 | Microsoft Docs
description: Azure Migrate 서비스에서 그룹 종속성 매핑을 사용하여 평가를 구체화하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596880"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>그룹 종속성 매핑을 사용하여 그룹 구체화

이 문서에서는 그룹에 있는 모든 컴퓨터의 종속성을 시각화하여 그룹을 구체화하는 방법을 설명합니다. 일반적으로 이 방법은 평가를 실행하기 전에 그룹 종속성을 교차 확인하여 기존 그룹에 대한 멤버 자격을 구체화하려는 경우에 사용합니다. 종속성 시각화를 사용하여 그룹을 구체화하면 Azure로의 마이그레이션을 효과적으로 계획할 수 있습니다. 함께 마이그레이션해야 하는 모든 상호 종속적 시스템을 검색할 수 있습니다. 따라서 Azure로 마이그레이션할 때 하나도 남겨두지 않고 전부 가져올 수 있으며 예기치 않은 중단이 발생하지 않습니다.


> [!NOTE]
> 종속성을 시각화하려는 그룹에는 10개 이하의 컴퓨터만 포함되어 있어야 합니다. 그룹에 10개 이상의 컴퓨터가 있는 경우에는 작은 그룹으로 분할하여 종속성 시각화 기능을 활용하는 것이 좋습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>종속성 시각화 준비
Azure Migrate는 컴퓨터의 종속성 시각화를 사용 하도록 설정 하려면 Azure Monitor 로그에서 서비스 맵 솔루션을 활용 합니다.

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

### <a name="associate-a-log-analytics-workspace"></a>Log Analytics 작업 영역 연결
종속성 시각화를 활용하려면 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결해야 합니다. 작업 영역은 마이그레이션 프로젝트를 만든 것과 같은 구독에서만 만들거나 연결할 수 있습니다.

- 프로젝트에 Log Analytics 작업 영역을 연결하려면 **개요**에서 프로젝트의 **필수** 섹션으로 이동하여 **구성 필요**를 클릭합니다.

    ![Log Analytics 작업 영역 연결](./media/concepts-dependency-visualization/associate-workspace.png)

- 작업 영역을 연결하는 동안 새 작업 영역을 만들거나 기존 작업 영역을 연결하는 옵션이 제공됩니다.
    - 새 작업 영역을 만들 때는 작업 영역의 이름을 지정해야 합니다. 그러면 마이그레이션 프로젝트와 같은 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에 해당하는 지역에서 작업 영역이 작성됩니다.
    - 기존 작업 영역을 연결하는 경우에는 마이그레이션 프로젝트와 동일한 구독에서 사용 가능한 모든 작업 영역을 선택할 수 있습니다. [서비스 맵이 지원](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)되는 지역에서 만든 작업 영역만 나열됩니다. 작업 영역을 연결하려면 작업 영역에 대한 '읽기 권한자' 액세스 권한이 있어야 합니다.

> [!NOTE]
> 마이그레이션 프로젝트에 연결된 작업 영역은 변경할 수 없습니다.

### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치
그룹의 종속성을 보려면 그룹에 속한 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치해야 합니다. 또한 인터넷에 연결되지 않은 머신이 있으면 [Log Analytics 게이트웨이](../azure-monitor/platform/gateway.md)를 다운로드하여 설치해야 합니다.

1. **개요**에서 **관리** > **그룹**을 클릭하고 필요한 그룹으로 이동합니다.
2. 컴퓨터 목록의 **종속성 에이전트** 열에서 **설치 필요**를 클릭하여 에이전트를 다운로드하여 설치하는 방법과 관련된 지침을 확인합니다.
3. **종속성** 페이지에서 그룹에 속한 각 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 다운로드하여 설치합니다.
4. 작업 영역 ID와 키를 복사합니다. 온-프레미스 컴퓨터에 MMA를 설치할 때 필요합니다.

### <a name="install-the-mma"></a>MMA 설치

#### <a name="install-the-agent-on-a-windows-machine"></a>Windows 머신에 에이전트 설치

Windows 컴퓨터에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의함**을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다.
4. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다.
5. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음**을 클릭합니다.

명령줄 또는 System Center Configuration Manager와 같은 자동화 된 방법을 사용 하 여 에이전트를 설치할 수 있습니다. 이 방법을 사용하여 MMA 에이전트를 설치하는 방법을 [자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent).

#### <a name="install-the-agent-on-a-linux-machine"></a>Linux 머신에 에이전트 설치

Linux 컴퓨터에 에이전트를 설치하려면

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
2. --install 인수를 사용하여 번들을 설치합니다.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>System Center Operations Manager에서 모니터링하는 머신에 에이전트 설치

Operations Manager 2012 R2 이상에서 모니터링하는 머신의 경우 MMA 에이전트를 설치할 필요가 없습니다. 서비스 맵에 Operations Manager MMA를 이용하여 필요한 종속성 데이터를 수집하는 Operations Manager와 통합 기능이 있습니다. [여기](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)에서 지침에 따라 통합을 사용하도록 설정할 수 있습니다. 그러나 종속성 에이전트는 해당 머신에 설치해야 합니다.

### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치
1. Windows 컴퓨터에 종속성 에이전트를 설치하려면 설치 파일을 두 x 누르고 마법사를 따릅니다.
2. Linux 컴퓨터에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

    ```sh InstallDependencyAgent-Linux64.bin```

[Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) 및 [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) 운영 체제에 대한 종속성 에이전트 지원에 대해 자세히 알아보세요.

스크립트를 사용하여 종속성 에이전트를 설치하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples).

## <a name="refine-the-group-based-on-dependency-visualization"></a>종속성 시각화를 기반으로 그룹 구체화
그룹의 모든 시스템에 에이전트를 설치했으면 아래 단계에 따라 그룹의 종속성을 시각화하고 그룹을 구체화할 수 있습니다.

1. Azure Migrate 프로젝트의 **관리** 아래에서  **그룹**을 클릭하고 그룹을 선택합니다.
2. 그룹 페이지에서  **종속성 보기**를 클릭하여 그룹 종속성 맵을 엽니다.
3. 그룹의 종속성 맵은 다음과 같은 세부 정보를 보여 줍니다.
   - 그룹에 속한 모든 컴퓨터와 주고받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
       - MMA 및 종속성 에이전트가 설치되지 않은 종속 컴퓨터는 포트 번호별로 그룹화됩니다.
       - MMA 및 종속성 에이전트가 설치된 종속 컴퓨터는 별도의 상자로 표시됩니다.
   - 컴퓨터 내부에서 실행 중인 프로세스. 각 컴퓨터 상자를 확장하여 프로세스를 볼 수 있습니다.
   - 각 컴퓨터의 정규화된 도메인 이름, 운영 체제, MAC 주소 등과 같은 속성. 각 시스템 상자를 클릭하여 이러한 세부 정보를 볼 수 있습니다.

     ![그룹 종속성 보기](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. 더 세부적인 종속성을 보려면 시간 범위를 클릭하여 수정합니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.

   > [!NOTE]
   >    현재 종속성 시각화 UI에서는 1시간보다 긴 시간 범위를 선택할 수 없습니다. 로그를 사용 하 여 Azure Monitor [종속성 데이터를 쿼리할](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) 더 긴 기간 동안.

4. 종속 컴퓨터와 각 컴퓨터 내에서 실행 중인 프로세스를 확인하고 그룹에서 추가하거나 제거할 컴퓨터를 식별합니다.
5. <Ctrl> 키를 누른 채로 클릭하여 맵에서 그룹에 추가하거나 제거할 컴퓨터를 선택합니다.
    - 검색된 컴퓨터만 추가할 수 있습니다.
    - 그룹에 컴퓨터를 추가하거나 제거하면 해당 그룹에 대한 이전의 평가가 무효화됩니다.
    - 그룹을 수정할 때 필요에 따라 새 평가를 만들 수 있습니다.
5. **확인**을 클릭하여 그룹을 저장합니다.

    ![컴퓨터 추가 또는 제거](./media/how-to-create-group-dependencies/add-remove.png)

그룹 종속성 맵에 표시되는 특정 컴퓨터의 종속성을 확인하려면 [컴퓨터 종속성 맵핑을 설정합니다](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Azure Monitor 로그에서 종속성 데이터를 쿼리 합니다.

서비스 맵을 통해 캡처된 종속성 데이터가 Azure Migrate 프로젝트와 연결 된 Log Analytics 작업 영역에서 쿼리에 사용할 수 있습니다. [자세한](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) Azure Monitor에서 쿼리를 서비스 맵 데이터 테이블에 대 한 로그입니다. 

Kusto 쿼리를 실행 합니다.

1. 에이전트를 설치한 후 포털로 이동하고 **개요**를 클릭합니다.
2. **개요**에서 프로젝트의 **필수** 섹션으로 이동하고 **OMS 작업 영역** 옆에 제공된 작업 영역 이름을 클릭합니다.
3. Log Analytics 작업 영역 페이지에서 **일반** > **로그**를 클릭합니다.
4. Azure Monitor 로그를 사용 하 여 종속성 데이터를 수집 하도록 쿼리를 작성 합니다. 다음 섹션에서 샘플 쿼리를 찾습니다.
5. [실행]을 클릭하여 쿼리를 실행합니다. 

[자세한](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) Kusto 쿼리를 작성 하는 방법에 대 한 합니다. 

## <a name="sample-azure-monitor-logs-queries"></a>샘플 Azure Monitor 로그 쿼리

다음은 샘플 쿼리 종속성 데이터를 추출 하는 데 사용할 수 있습니다. 에 기본 데이터 요소를 추출 하기 위해 쿼리를 수정할 수 있습니다. 종속성 데이터 레코드의 필드 목록은 제품은 [여기](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)합니다. 자세한 샘플 쿼리를 찾으려면 [여기](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)합니다.

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>컴퓨터 집합에서 인바운드 연결 요약

연결 메트릭, VMConnection, 테이블에 레코드를 개별 물리적 네트워크 연결을 나타내지 않으면 참고 합니다. 여러 실제 네트워크 연결 연결을 논리적으로 그룹화 됩니다. [자세한](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) VMConnection의 단일 논리적 레코드에 데이터를 집계 하는 방법의 실제 네트워크 연결에 대 한 합니다. 

```
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>컴퓨터의 집합 간에 인바운드 연결에서 보내거나 받는 데이터의 볼륨 요약

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>다음 단계
- 종속성 시각화 관련 FAQ를 [자세히 확인](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)해 보세요.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
