---
title: Azure Monitor의 성능 카운터 수집 및 분석 | Microsoft Docs
description: 성능 카운터는 Windows 및 Linux 에이전트에서 성능을 분석하기 위해 Azure Monitor에 의해 수집됩니다.  이 문서는 Windows 및 Linux 에이전트에 대한 성능 카운터 컬렉션을 구성하는 방법과, 작업 영역에 저장하는 방식에 대한 자세한 내용과, Azure Portal에서 분석하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 93f47529e3be44ff1db4e089bdcdca3eb1b4dea3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363411"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Azure Monitor의 Windows 및 Linux 성능 데이터 원본
Windows와 Linux의 성능 카운터는 하드웨어 구성 요소, 운영 체제 및 애플리케이션의 성능에 대한 정보를 자세히 제공합니다.  Azure Monitor는 장기적인 분석 및 보고를 위한 성능 데이터 집계는 물론 거의 실시간에 가까운(NRT) 분석을 위해 빈번한 간격으로 성능 카운터를 수집할 수 있습니다.

![성능 카운터](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>성능 카운터 구성
성능 카운터는 [고급 설정의 데이터 메뉴](agent-data-sources.md#configuring-data-sources)에서 구성합니다.

새 작업 영역에 대한 Windows 또는 Linux 성능 카운터를 처음으로 구성하는 경우, 몇 가지 공용 카운터를 신속하게 만드는 옵션이 제공됩니다.  각 항목은 옆에 확인란과 함께 나열됩니다.  초기에 만들 카운터를 모두 선택한 후 **Add the selected performance counters**(선택한 성능 카운터 추가)를 클릭합니다.

Windows 성능 카운터의 경우, 각 성능 카운터에 대해 특정 인스턴스를 선택할 수 있습니다. Linux 성능 카운터의 경우, 선택하는 각 카운터의 인스턴스는 부모 카운터의 모든 자식 카운터에 적용됩니다. 다음 테이블은 Linux와 Windows 성능 카운터 모두에서 사용할 수 있는 공통 인스턴스를 보여줍니다.

| 인스턴스 이름 | 설명 |
| --- | --- |
| \_합계 |모든 인스턴스의 총계 |
| \* |모든 인스턴스 |
| (/&#124;/var) |/ 또는 /var로 명명된 인스턴트와 일치 |

### <a name="windows-performance-counters"></a>Windows 성능 카운터

![Windows 성능 카운터 구성](media/data-sources-performance-counters/configure-windows.png)

이 절차에 따라 수집할 새 Windows 성능 카운터를 추가합니다.

1. 텍스트 상자에 *object(instance)\counter* 형식으로 카운트 이름을 입력합니다.  입력을 시작하면 일치하는 공용 카운터 목록이 나타납니다.  목록에서 카운터를 선택하거나 원하는 항목을 입력할 수 있습니다.  *object\counter*를 지정하면 특정 카운터에 대한 모든 인스턴스를 반환할 수도 있습니다.  

    명명된 인스턴스에서 SQL Server 성능 카운터를 수집할 때 모든 명명된 인스턴스 카운터는 *MSSQL$* 로 시작하며 인스턴스 이름이 이어집니다.  예를 들어 명명된 SQL 인스턴스 INST2의 경우 데이터베이스 성능 개체에서 모든 데이터베이스에 대한 로그 캐시 적중률 카운터를 수집하려면 `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`를 지정합니다.

2. **+** 를 클릭하거나 **Enter**를 눌러서 카운터를 목록에 추가합니다.
3. 카운터를 추가할 때에는 해당 **샘플 간격**에 기본적으로 10초가 사용됩니다.  수집된 성능 데이터의 저장소 요구 사항을 줄이려면 높은 값으로, 최대 1800초(30분)까지 값을 변경할 수 있습니다.
4. 카운터 추가를 완료했으면 화면 맨 위에서 **저장** 단추를 눌러서 구성을 저장합니다.

### <a name="linux-performance-counters"></a>Linux 성능 카운터

![Linux 성능 카운터 구성](media/data-sources-performance-counters/configure-linux.png)

이 절차에 따라 수집할 새 Linux 성능 카운터를 추가합니다.

1. 기본적으로, 모든 구성 변경은 모든 에이전트로 자동 푸시됩니다.  Linux 에이전트에서, 구성 파일은 Fluentd 데이터 수집기로 전송됩니다.  각 Linux 에이전트에서 이 파일을 수동으로 수정하려면, *Apply below configuration to my Linux machines*(아래 구성을 내 Linux 컴퓨터에 적용) 확인란 선택을 해제하고 아래 지침을 따릅니다.
2. 텍스트 상자에 *object(instance)\counter* 형식으로 카운트 이름을 입력합니다.  입력을 시작하면 일치하는 공용 카운터 목록이 나타납니다.  목록에서 카운터를 선택하거나 원하는 항목을 입력할 수 있습니다.  
3. **+** 를 클릭하거나 **Enter**를 눌러서 카운터를 개체의 다른 카운터 목록에 추가합니다.
4. 개체에 대한 모든 카운터에는 동일한 **샘플 간격**이 사용됩니다.  기본값은 10초입니다.  수집된 성능 데이터의 저장소 요구 사항을 줄이려면 이 값을 최대 1800초(30분)까지 변경할 수 있습니다.
5. 카운터 추가를 완료했으면 화면 맨 위에서 **저장** 단추를 눌러서 구성을 저장합니다.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>구성 파일에서 Linux 성능 카운터 구성
Azure Portal을 사용하여 Linux 성능 카운터를 구성하는 대신 Linux 에이전트의 구성 파일을 편집하는 옵션도 있습니다.  수집할 성능 메트릭은 **/etc/opt/microsoft/omsagent/\<workspace id\>/conf/omsagent.conf**의 구성으로 제어됩니다.

수집할 성능 메트릭의 각 개체나 범주는 구성 파일 내에 단일 `<source>` 요소로 정의되어야 합니다. 구문은 아래와 같은 패턴을 따릅니다.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


이 요소의 매개 변수를 다음 테이블에서 설명합니다.

| 매개 변수 | 설명 |
|:--|:--|
| object\_name | 수집하는 개체의 이름입니다. |
| instance\_regex |  수집할 인스턴스를 정의하는 *정규식*입니다. `.*` 값은 모든 인스턴스를 지정합니다. \_Total 인스턴스에 대해서만 프로세서 메트릭을 수집하려면 `_Total`을 지정합니다. crond 또는 sshd 인스턴스에 대해서만 프로세서 메트릭을 수집하려면 `(crond\|sshd)`를 지정합니다. |
| counter\_name\_regex | 수집할 (개체에 대한) 카운터를 정의하는 *정규식*입니다. 개체에 대한 모든 카운터를 수집하려면 `.*`를 지정합니다. 메모리 개체에 대한 스왑 공간 카운터만 수집하려면 예를 들어 다음을 지정할 수 있습니다. `.+Swap.+` |
| interval | 개체의 카운터가 수집되는 빈도입니다. |


다음 테이블은 구성 파일에서 지정할 수 있는 개체 및 카운터를 나열합니다.  [Azure Monitor에서 Linux 애플리케이션에 대한 성능 카운터 수집](data-sources-linux-applications.md)에서 설명된 대로 특정 애플리케이션에 사용할 수 있는 추가 카운터가 있습니다.

| 개체 이름 | 카운터 이름 |
|:--|:--|
| 논리 디스크 | % 사용 가능한 Inodes |
| 논리 디스크 | % 사용 가능한 공간 |
| 논리 디스크 | % 사용된 Inodes |
| 논리 디스크 | % 사용된 공간 |
| 논리 디스크 | 디스크 읽기 바이트/초  |
| 논리 디스크 | 디스크 읽기/초  |
| 논리 디스크 | 디스크 전송/초 |
| 논리 디스크 | 디스크 쓰기 바이트/초 |
| 논리 디스크 | 디스크 쓰기/초 |
| 논리 디스크 | 사용 가능한 메가바이트 |
| 논리 디스크 | 논리 디스크 바이트/초 |
| 메모리 | % 사용 가능한 메모리 |
| 메모리 | % 사용 가능한 스왑 공간 |
| 메모리 | % 사용된 메모리 |
| 메모리 | % 사용된 스왑 공간 |
| 메모리 | 사용 가능한 MB 메모리 |
| 메모리 | 사용 가능한 MB 스왑 |
| 메모리 | 페이지 읽기/초 |
| 메모리 | 페이지 쓰기/초 |
| 메모리 | 페이지/초 |
| 메모리 | 사용된 MB 스왑 공간 |
| 메모리 | 사용된 메모리 MB |
| 네트워크 | 전송된 총 바이트 |
| 네트워크 | 받은 총 바이트 |
| 네트워크 | 총 바이트 |
| 네트워크 | 전송된 총 패킷 |
| 네트워크 | 받은 총 패킷 |
| 네트워크 | 총 Rx 오류 |
| 네트워크 | 총 Tx 오류 |
| 네트워크 | 총 충돌 |
| 물리적 디스크 | 평균 디스크 초/읽기 |
| 물리적 디스크 | 평균 디스크 초/전송 |
| 물리적 디스크 | 평균 디스크 초/쓰기 |
| 물리적 디스크 | 물리적 디스크 바이트/초 |
| Process | Pct 권한이 부여된 시간 |
| Process | Pct 사용자 시간 |
| Process | 사용된 메모리 KB |
| Process | 가상 공유 메모리 |
| 프로세서 | % DPC 시간 |
| 프로세서 | % 유휴 시간 |
| 프로세서 | % 인터럽트 시간 |
| 프로세서 | % IO 대기 시간 |
| 프로세서 | % Nice 시간 |
| 프로세서 | % 권한이 부여된 시간 |
| 프로세서 | % Processor Time |
| 프로세서 | % 사용자 시간 |
| 시스템 | 사용 가능한 실제 메모리 |
| 시스템 | 페이징 파일에 사용 가능한 공간 |
| 시스템 | 사용 가능한 가상 메모리 |
| 시스템 | 프로세스 |
| 시스템 | 페이징 파일에 저장된 크기 |
| 시스템 | 작동 시간 |
| 시스템 | 사용자 |


다음은 성능 메트릭에 대한 기본 구성입니다.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>데이터 수집
Azure Monitor는 카운터가 설치된 모든 에이전트에서 지정된 모든 성능 카운터를 지정된 샘플 간격으로 수집합니다.  데이터는 집계되지 않으며 구독에서 지정한 기간 동안 모든 로그 쿼리 보기에서 원시 데이터를 사용할 수 있습니다.

## <a name="performance-record-properties"></a>성능 레코드 속성
성능 레코드에는 **Perf**라는 type과 다음 테이블의 속성이 포함됩니다.

| 자산 | 설명 |
|:--- |:--- |
| Computer |이벤트가 수집된 컴퓨터입니다. |
| CounterName |성능 카운터의 이름입니다. |
| CounterPath |카운터의 전체 경로이며 형식은 \\\\\<Computer>\\object(instance)\\counter입니다. |
| CounterValue |카운터의 숫자 값입니다. |
| InstanceName |이벤트 인스턴스의 이름입니다.  인스턴스가 없으면 비어 있게 됩니다. |
| ObjectName |성능 개체의 이름입니다. |
| SourceSystem |데이터가 수집된 에이전트의 유형입니다. <br><br>OpsManager – Windows 에이전트, 직접 연결 또는 SCOM <br> Linux – 모든 Linux 에이전트  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |데이터가 샘플링된 날짜와 시간입니다. |

## <a name="sizing-estimates"></a>예상 크기 조정
 10초 간격으로 특정 카운터가 수집되는 양은 인스턴스당 일별 약 1MB입니다.  다음 수식을 사용하여 특정 카운터의 저장소 요구 사항을 예측할 수 있습니다.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>성능 레코드를 통한 로그 쿼리
다음 표에서는 성능 레코드를 검색하는 로그 쿼리의 다양한 예제를 제공합니다.

| 쿼리 | 설명 |
|:--- |:--- |
| Perf |모든 성능 데이터 |
| Perf &#124; where Computer == "MyComputer" |특정 컴퓨터의 모든 성능 데이터 |
| Perf &#124; where CounterName == "Current Disk Queue Length" |특정 컴퓨터에 대한 모든 성능 데이터 |
| Perf &#124; where ObjectName == "Processor" and CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AVGCPU = avg(Average) by Computer |모든 컴퓨터의 평균 CPU 사용률 |
| Perf &#124; where CounterName == "% Processor Time" &#124; summarize AggregatedValue = max(Max) by Computer |모든 컴퓨터의 최대 CPU 사용률 |
| Perf &#124; where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and Computer == "MyComputerName" &#124; summarize AggregatedValue = avg(Average) by InstanceName |지정된 컴퓨터의 모든 인스턴스의 평균 현재 디스크 큐 길이 |
| Perf &#124; where CounterName == "DiskTransfers/sec" &#124; summarize AggregatedValue = percentile(Average, 95) by Computer |모든 컴퓨터에 대한 디스크 전송/초의 95 백분위수 |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |모든 컴퓨터에서 시간별 평균 CPU 사용량 |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | 특정 컴퓨터에 대한 % 백분율 카운터당 시간별 70백분위수 |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |특정 컴퓨터의 시간별 평균, 최소, 최대, 75백분위수 CPU 사용량 |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | 명명된 SQL Server 인스턴스 INST2에서 마스터 데이터베이스에 대한 데이터베이스 성능 개체의 모든 성능 데이터.  




## <a name="next-steps"></a>다음 단계
* MySQL 및 Apache HTTP 서버를 포함하여 [Linux 애플리케이션에서 성능 카운터를 수집](data-sources-linux-applications.md)합니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다.  
* 추가적인 시각화 및 분석을 위해, 수집된 데이터를 [Power BI](powerbi.md) 로 내보냅니다.
