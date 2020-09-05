---
title: Azure Monitor 로그를 사용 하 여 Azure Site Recovery 모니터링
description: Azure Monitor 로그 (Log Analytics)를 사용 하 여 Azure Site Recovery를 모니터링 하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 7d11fa8605d9cd5f335b6be56097caf7a5222bbd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006944"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 Site Recovery 모니터링

이 문서에서는 [Azure Monitor 로그](../azure-monitor/platform/data-platform-logs.md)및 [Log Analytics](../azure-monitor/log-query/log-query-overview.md)를 사용 하 여 Azure [Site Recovery](site-recovery-overview.md)에서 복제 된 컴퓨터를 모니터링 하는 방법을 설명 합니다.

Azure Monitor 로그는 다른 모니터링 데이터와 함께 작업 및 리소스 로그를 수집 하는 로그 데이터 플랫폼을 제공 합니다. Azure Monitor 로그 내에서 Log Analytics를 사용 하 여 로그 쿼리를 작성 및 테스트 하 고 로그 데이터를 대화형으로 분석할 수 있습니다. 로그 결과를 시각화 하 고 쿼리할 수 있으며 모니터링 되는 데이터에 따라 동작을 수행 하도록 경고를 구성할 수 있습니다.

Site Recovery Azure Monitor 로그를 사용 하 여 다음 작업을 수행할 수 있습니다.

- **Site Recovery 상태 및 상태를 모니터링**합니다. 예를 들어, 복제 상태, 테스트 장애 조치 상태, Site Recovery 이벤트, 보호 된 컴퓨터에 대 한 Rpo (복구 지점 목표) 및 디스크/데이터 변경 비율을 모니터링할 수 있습니다.
- **Site Recovery에 대 한 경고를 설정**합니다. 예를 들어 컴퓨터 상태, 테스트 장애 조치 (failover) 상태 또는 Site Recovery 작업 상태에 대 한 경고를 구성할 수 있습니다.

Site Recovery에서 Azure Monitor 로그 사용은 azure **에서 azure로** 복제, **VMware v m/물리적 서버에서 azure** 로 복제에 대해 지원 됩니다.

> [!NOTE]
> VMware 및 물리적 컴퓨터의 변동 데이터 로그 및 업로드 빈도 로그를 가져오려면 프로세스 서버에 Microsoft monitoring agent를 설치 해야 합니다. 이 에이전트는 복제 하는 컴퓨터의 로그를 작업 영역으로 보냅니다. 이 기능은 9.30 모바일 에이전트 버전 이상 에서만 사용할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

다음 항목이 필요합니다.

- Recovery Services 자격 증명 모음에 보호 된 컴퓨터가 하나 이상 있습니다.
- Site Recovery 로그를 저장할 Log Analytics 작업 영역입니다. 작업 영역을 설정 하는 [방법에 대해 알아봅니다](../azure-monitor/learn/quick-create-workspace.md) .
- Log Analytics에서 로그 쿼리를 작성, 실행 및 분석 하는 방법에 대 한 기본적인 이해 [자세히 알아보기](../azure-monitor/log-query/get-started-portal.md).

시작 하기 전에 [일반적인 모니터링 질문](monitoring-common-questions.md) 을 검토 하는 것이 좋습니다.

## <a name="configure-site-recovery-to-send-logs"></a>로그를 보내도록 Site Recovery 구성

1. 자격 증명 모음에서 **진단 설정**  >  **진단 설정 추가**를 클릭 합니다.

    ![진단 설정 추가 옵션을 보여 주는 스크린샷](./media/monitoring-log-analytics/add-diagnostic.png)

2. **진단 설정**에서 이름을 지정 하 고 **Log Analytics로 보내기**상자를 선택 합니다.
3. Azure Monitor 로그 구독 및 Log Analytics 작업 영역을 선택 합니다.
4. 설정/해제에서 **Azure 진단** 를 선택 합니다.
5. 로그 목록에서 접두사가 **AzureSiteRecovery**인 모든 로그를 선택 합니다. 그런 다음 **확인**을 클릭합니다.

    ![진단 설정 화면의 스크린샷](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery 로그는 선택한 작업 영역에서 테이블 (**Azurediagnostics**)에 피드를 시작 합니다.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>프로세스 서버에서 변동 및 업로드 율 로그를 보내도록 Microsoft monitoring agent 구성

온-프레미스에서 VMware/물리적 컴퓨터에 대 한 데이터 변동 비율 정보 및 원본 데이터 업로드 속도 정보를 캡처할 수 있습니다. 이를 사용 하려면 프로세스 서버에 Microsoft monitoring agent를 설치 해야 합니다.

1. Log Analytics 작업 영역으로 이동한 다음 **고급 설정**을 클릭 합니다.
2. **연결 된 원본** 페이지를 클릭 하 고 **Windows 서버**를 추가로 선택 합니다.
3. 프로세스 서버에서 Windows 에이전트 (64 비트)를 다운로드 합니다. 
4. [작업 영역 ID 및 키 가져오기](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)
5. [TLS 1.2를 사용 하도록 에이전트 구성](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. 가져온 작업 영역 ID 및 키를 제공 하 여 [에이전트 설치를 완료](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard) 합니다.
7. 설치가 완료 되 면 Log Analytics 작업 영역으로 이동한 다음 **고급 설정**을 클릭 합니다. **데이터** 페이지로 이동 하 여 **Windows 성능 카운터**를 추가로 클릭 합니다. 
8. **' + '** 를 클릭 하 여 샘플 간격이 300 초인 다음 두 카운터를 추가 합니다.

    - ASRAnalytics (*) \SourceVmChurnRate
    - ASRAnalytics (*) \SourceVmThrpRate

변동 및 업로드 율 데이터는 작업 영역으로 공급 되기 시작 합니다.


## <a name="query-the-logs---examples"></a>로그 쿼리-예제

[Kusto 쿼리 언어로](../azure-monitor/log-query/get-started-queries.md)작성 된 로그 쿼리를 사용 하 여 로그에서 데이터를 검색 합니다. 이 섹션에서는 Site Recovery 모니터링에 사용할 수 있는 일반적인 쿼리의 몇 가지 예를 제공 합니다.

> [!NOTE]
> 일부 예제에서는 **A2A**로 설정 된 **replicationProviderName_s** 를 사용 합니다. Site Recovery를 사용 하 여 보조 Azure 지역에 복제 되는 Azure Vm을 검색 합니다. 이 예제에서는 Site Recovery를 사용 하 여 Azure에 복제 된 온-프레미스 VMware Vm 또는 물리적 서버를 검색 하려는 경우 **A2A** 를 **InMageAzureV2**로 바꿀 수 있습니다.


### <a name="query-replication-health"></a>복제 상태 쿼리

이 쿼리는 모든 보호 된 Azure Vm의 현재 복제 상태에 대 한 원형 차트를 그립니다. 표준, 경고 또는 위험의 세 가지 상태로 분류 됩니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>쿼리 모바일 서비스 버전

이 쿼리는 Site Recovery으로 복제 된 Azure Vm에 대 한 원형 차트를 그립니다 .이 차트는 실행 중인 모바일 에이전트의 버전으로 분류 됩니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>RPO 시간 쿼리

이 쿼리는 Site Recovery로 복제 되 고 RPO (복구 지점 목표)에 따라 분할 된 Azure Vm의 가로 막대형 차트를 표시 합니다. 15-30 15 분 이내에 30 분 이상 30 분이 소요 됩니다.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Site Recovery로 복제 된 Azure Vm의 가로 막대형 차트를 보여 주는 스크린샷](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Site Recovery 작업 쿼리

이 쿼리는 지난 72 시간 동안 트리거된 모든 Site Recovery 작업 (모든 재해 복구 시나리오의 경우) 및 완료 상태를 검색 합니다.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>쿼리 Site Recovery 이벤트

이 쿼리는 지난 72 시간 동안 발생 한 모든 Site Recovery 이벤트 (모든 재해 복구 시나리오의 경우)를 해당 심각도와 함께 검색 합니다. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>테스트 장애 조치 상태 쿼리 (원형 차트)

이 쿼리는 Site Recovery로 복제 된 Azure Vm의 테스트 장애 조치 (failover) 상태에 대 한 원형 차트를 그립니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>테스트 장애 조치 (failover) 상태 쿼리 (테이블)

이 쿼리는 Site Recovery로 복제 된 Azure Vm의 테스트 장애 조치 (failover) 상태에 대 한 테이블을 플롯 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>컴퓨터 RPO 쿼리

이 쿼리는 지난 72 시간 동안 특정 Azure VM (ContosoVM123)의 RPO를 추적 하는 추세 그래프를 그립니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![특정 Azure VM의 RPO를 추적 하는 추세 그래프 스크린샷](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Azure VM의 데이터 변경 률 (변동) 및 업로드 율 쿼리

이 쿼리는 데이터 변경 률 (초당 쓰기 바이트) 및 데이터 업로드 율을 나타내는 특정 Azure VM (ContosoVM123)에 대 한 추세 그래프를 그립니다. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![특정 Azure VM에 대 한 추세 그래프의 스크린샷](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>VMware 또는 물리적 컴퓨터에 대 한 쿼리 데이터 변경 률 (변동) 및 업로드 빈도

> [!Note]
> 프로세스 서버에서 이러한 로그를 가져오도록 모니터링 에이전트를 설정 했는지 확인 합니다. [모니터링 에이전트를 구성 하는 단계를](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)참조 하세요.

이 쿼리는 데이터 변경 률 (초당 쓰기 바이트) 및 데이터 업로드 율을 나타내는 복제 된 항목 **win-9r7sfh9qlru**의 특정 디스크 **disk0** 추세 그래프를 그립니다. Recovery services 자격 증명 모음에서 복제 된 항목 **의 디스크 이름 블레이드를** 찾을 수 있습니다. 쿼리에 사용할 인스턴스 이름은 다음 예제와 같이 _ 및 디스크 이름이 뒤에 나오는 컴퓨터의 DNS 이름입니다.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
프로세스 서버는이 데이터를 5 분 마다 Log Analytics 작업 영역에 푸시합니다. 이러한 데이터 요소는 5 분 동안 계산 된 평균을 나타냅니다.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>재해 복구 요약 쿼리 (Azure에서 Azure로)

이 쿼리는 보조 Azure 지역에 복제 된 Azure Vm에 대 한 요약 테이블을 플롯 합니다.  VM 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치 상태, 모바일 에이전트 버전, 모든 활성 복제 오류 및 원본 위치를 표시 합니다.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>재해 복구 요약 쿼리 (VMware/물리적 서버)

이 쿼리는 Azure에 복제 된 VMware Vm 및 물리적 서버에 대 한 요약 테이블을 플롯 합니다.  컴퓨터 이름, 복제 및 보호 상태, RPO, 테스트 장애 조치 상태, 모바일 에이전트 버전, 모든 활성 복제 오류 및 관련 프로세스 서버를 표시 합니다.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>경고 설정-예제

Azure Monitor 데이터를 기반으로 하 Site Recovery 경고를 설정할 수 있습니다. 로그 경고 설정에 [대해 자세히 알아보세요](../azure-monitor/platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) . 

> [!NOTE]
> 일부 예제에서는 **A2A**로 설정 된 **replicationProviderName_s** 를 사용 합니다. 이렇게 하면 보조 Azure 지역에 복제 되는 Azure Vm에 대 한 경고가 설정 됩니다. 이 예에서는 Azure로 복제 된 온-프레미스 VMware Vm 또는 물리적 서버에 대 한 경고를 설정 하려는 경우 **A2A** 를 **InMageAzureV2** 로 바꿀 수 있습니다.

### <a name="multiple-machines-in-a-critical-state"></a>위험 상태의 여러 컴퓨터

20 개가 넘는 복제 된 Azure Vm이 위험 상태로 전환 되는 경우 경고를 설정 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
경고에 대해 **임계값** 을 20으로 설정 합니다.

### <a name="single-machine-in-a-critical-state"></a>위험 상태의 단일 컴퓨터

특정 복제 된 Azure VM이 위험 상태가 되는 경우 경고를 설정 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에 대해 **임계값** 을 1로 설정 합니다.

### <a name="multiple-machines-exceed-rpo"></a>여러 컴퓨터가 RPO를 초과 합니다.

20 개 이상의 Azure Vm에 대 한 RPO가 30 분을 초과 하는 경우 경고를 설정 합니다.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
경고에 대해 **임계값** 을 20으로 설정 합니다.

### <a name="single-machine-exceeds-rpo"></a>단일 컴퓨터가 RPO를 초과 합니다.

단일 Azure VM에 대 한 RPO가 30 분을 초과 하는 경우 경고를 설정 합니다.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
경고에 대해 **임계값** 을 1로 설정 합니다.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>여러 컴퓨터의 테스트 장애 조치 (failover)가 90 일을 초과 함

20 개 이상의 Vm에 대해 마지막으로 성공한 테스트 장애 조치 (failover)가 90 일을 초과 하는 경우 경고를 설정 합니다. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에 대해 **임계값** 을 20으로 설정 합니다.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>단일 컴퓨터의 테스트 장애 조치 (failover)가 90 일을 초과 함

특정 VM에 대해 마지막으로 성공한 테스트 장애 조치 (failover)가 90 일 이전인 경우 경고를 설정 합니다.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
경고에 대해 **임계값** 을 1로 설정 합니다.

### <a name="site-recovery-job-fails"></a>Site Recovery 작업이 실패 합니다.

Site Recovery 작업 (이 경우 다시 보호 작업)이 지난 하루 동안 Site Recovery 시나리오에 대해 실패 하는 경우 경고를 설정 합니다. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

경고의 경우 **임계값** 을 1로 설정 하 고 **Period** 를 1440 분으로 설정 하 여 마지막 날의 실패를 확인 합니다.

## <a name="next-steps"></a>다음 단계

기본 제공 Site Recovery 모니터링 [에 대해 알아봅니다](site-recovery-monitor-and-troubleshoot.md) .
