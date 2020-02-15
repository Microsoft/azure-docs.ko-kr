---
title: Azure Monitor 로그를 사용 하 여 Azure HDInsight 클러스터 모니터링
description: Azure Monitor 로그를 사용 하 여 HDInsight 클러스터에서 실행 중인 작업을 모니터링 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162789"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor 로그를 사용 하 여 HDInsight 클러스터 모니터링

Azure Monitor 로그를 사용 하 여 HDInsight에서 Hadoop 클러스터 작업을 모니터링 하는 방법 및 HDInsight 모니터링 솔루션을 추가 하는 방법에 대해 알아봅니다.

[Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 는 클라우드 및 온-프레미스 환경을 모니터링 하 여 가용성과 성능을 유지 하는 Azure Monitor 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Log Analytics 작업 영역. 이 작업 영역은 고유한 데이터 리포지토리, 데이터 원본 및 솔루션이 있는 고유한 Azure Monitor 로그 환경으로 생각할 수 있습니다. 지침은 [Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)를 참조하세요.

* Azure HDInsight 클러스터를 만듭니다. 현재 다음 HDInsight 클러스터 유형에 서 Azure Monitor 로그를 사용할 수 있습니다.

  * Hadoop은
  * HBase
  * 대화형 쿼리
  * Kafka
  * Spark
  * Storm

  HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.  

* Azure PowerShell Az module.  [새 Azure PowerShell Az Module 소개를](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)참조 하세요. 최신 버전이 있는지 확인 합니다. 필요한 경우 `Update-Module -Name Az`를 실행 합니다.

> [!NOTE]  
> 성능 향상을 위해 동일한 지역에 HDInsight 클러스터와 Log Analytics 작업 영역을 모두 배치하는 것이 좋습니다. Azure Monitor 로그는 일부 Azure 지역에서 사용할 수 없습니다.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>포털을 사용 하 여 Azure Monitor 로그 사용

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에서 클러스터를 선택 합니다.  지침에 대해서는 [클러스터 나열 및 표시](./hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요. 클러스터가 새 포털 페이지에서 열립니다.

1. 왼쪽의 **모니터링**아래에서 **Azure Monitor**을 선택 합니다.

1. 기본 보기의 **Azure Monitor 통합**에서 **사용**을 선택 합니다.

1. **작업 영역 선택** 드롭다운 목록에서 기존 Log Analytics 작업 영역을 선택합니다.

1. **저장**을 선택합니다.  설정을 저장하는 데 몇 분 정도 걸립니다.

    ![HDInsight 클러스터에 대 한 모니터링 사용](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "HDInsight 클러스터에 대 한 모니터링 사용")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure Monitor 로그 사용

Azure PowerShell Az module [AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet을 사용 하 여 Azure Monitor 로그를 사용 하도록 설정할 수 있습니다.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

사용 하지 않도록 설정 하려면 [AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) cmdlet을 사용 합니다.

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight 클러스터 관리 솔루션 설치

HDInsight는 Azure Monitor 로그에 추가할 수 있는 클러스터 특정 관리 솔루션을 제공 합니다. [관리 솔루션](../log-analytics/log-analytics-add-solutions.md) 은 Azure Monitor 로그에 기능을 추가 하 여 추가 데이터 및 분석 도구를 제공 합니다. 이러한 솔루션은 HDInsight 클러스터에서 중요한 성능 메트릭을 수집하고, 메트릭을 검색할 수 있는 도구를 제공합니다. 또한 HDInsight에서 지원되는 대부분의 클러스터 유형에 대해 시각화 및 대시보드를 제공합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다.

다음은 사용 가능한 HDInsight 솔루션입니다.

* HDInsight Hadoop 모니터링
* HDInsight HBase 모니터링
* HDInsight 대화형 쿼리 모니터링
* HDInsight Kafka 모니터링
* HDInsight Spark 모니터링
* HDInsight Storm 모니터링

관리 솔루션을 설치하기 위한 지침은 [Azure의 관리 솔루션](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)을 참조하세요. 실험 하려면 HDInsight Hadoop 모니터링 솔루션을 설치 합니다. 완료 되 면 **요약**아래에 **HDInsightHadoop** 타일이 표시 됩니다. **HDInsightHadoop** 타일을 선택합니다. HDInsightHadoop 솔루션은 다음과 같습니다.

![HDInsight 모니터링 솔루션 보기](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

클러스터는 새로운 클러스터이므로 보고서에 작업이 표시되지 않습니다.

## <a name="configuring-performance-counters"></a>성능 카운터 구성

또한 Azure monitor는 클러스터의 노드에 대 한 성능 메트릭을 수집 하 고 분석 하는 것을 지원 합니다. 이 기능을 사용 하도록 설정 하 고 구성 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 Linux 성능 데이터 원본](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)을 참조 하세요.

## <a name="cluster-auditing"></a>클러스터 감사

HDInsight는 다음 유형의 로그를 가져와 Azure Monitor 로그를 사용 하 여 클러스터 감사를 지원 합니다.

* `log_gateway_audit_CL`-이 테이블은 성공 및 실패 한 로그인 시도를 표시 하는 클러스터 게이트웨이 노드의 감사 로그를 제공 합니다.
* `log_auth_CL`-이 표에서는 성공 및 실패 한 로그인 시도와 함께 SSH 로그를 제공 합니다.
* `log_ambari_audit_CL`-이 테이블은 Ambari의 감사 로그를 제공 합니다.
* `log_ranger_audti_CL`-이 테이블은 ESP 클러스터에서 Apache 레인저의 감사 로그를 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그를 쿼리하여 HDInsight 클러스터 모니터링](hdinsight-hadoop-oms-log-analytics-use-queries.md)
