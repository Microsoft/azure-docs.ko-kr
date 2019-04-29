---
title: Azure HDInsight 클러스터 모니터링을 사용 하 여 Azure Monitor 로그
description: Azure Monitor를 Azure 로그를 사용 하 여 HDInsight 클러스터에서 실행 하는 작업을 모니터링 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 0c37ff3c62817d238a94d5bbeae90c67663a6029
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761208"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>HDInsight 클러스터 모니터링을 사용 하 여 Azure Monitor 로그

HDInsight, Hadoop 클러스터 작업을 모니터링 하려면 Azure Monitor 로그를 사용 하는 방법 및 솔루션을 모니터링 하는 HDInsight를 추가 하는 방법에 알아봅니다.

[Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 는 클라우드를 모니터링 하 고 온-프레미스 환경의 해당 가용성 및 성능을 유지 하는 Azure Monitor의 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* **Log Analytics 작업 영역** - 이 작업 영역은 자체 데이터 리포지토리, 데이터 원본 및 솔루션을 사용 하 여 고유한 Azure Monitor 로그 환경으로 생각할 수 있습니다. 지침은 [Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)를 참조하세요.

* **Azure HDInsight 클러스터**. 현재 다음 HDInsight 클러스터 유형에 사용 하 여 Azure Monitor 로그를 사용할 수 있습니다.

  * Hadoop은
  * HBase
  * 대화형 쿼리
  * Kafka
  * Spark
  * Storm

  HDInsight 클러스터를 만드는 방법에 대한 지침은 [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.  

* **Azure PowerShell Az 모듈**합니다.  참조 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)합니다.

> [!NOTE]  
> 성능 향상을 위해 동일한 지역에 HDInsight 클러스터와 Log Analytics 작업 영역을 모두 배치하는 것이 좋습니다. 모든 Azure 지역에서는 azure Monitor 로그를 사용할 수 없습니다.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>포털을 사용 하 여 Azure Monitor 로그를 사용 하도록 설정

이 섹션에서는 Azure Log Analytics 작업 영역을 사용하여 작업, 디버그 로그 등을 모니터링하도록 기존 HDInsight Hadoop 클러스터를 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

3. **ANALYTICS** 아래에서 **HDInsight 클러스터**를 선택합니다.

4. 목록에서 클러스터를 선택 합니다.

5. 왼쪽의 **모니터링**에서 **Operations Management Suite**를 선택합니다.

6. 기본 보기의 **OMS 모니터링**에서 **사용**을 선택합니다.

7. **작업 영역 선택** 드롭다운 목록에서 기존 Log Analytics 작업 영역을 선택합니다.

8. **저장**을 선택합니다.  설정을 저장하는 데 몇 분 정도 걸립니다.

    ![HDInsight 클러스터에 대한 모니터링 사용](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "HDInsight 클러스터에 대한 모니터링 사용")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 Azure Monitor 로그를 사용 하도록 설정

Azure Monitor 로그 Az Azure PowerShell 모듈을 사용 하 여 설정할 수 있습니다 [Enable AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

사용을 해제 합니다 [사용 안 함-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) cmdlet:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight 클러스터 관리 솔루션 설치

HDInsight는 Azure Monitor 로그에 추가할 수 있는 클러스터와 관련 된 관리 솔루션을 제공 합니다. [관리 솔루션](../log-analytics/log-analytics-add-solutions.md) 추가 데이터 및 분석 도구를 제공 하는 로그를 Azure Monitor에 기능을 추가 합니다. 이러한 솔루션은 HDInsight 클러스터에서 중요한 성능 메트릭을 수집하고, 메트릭을 검색할 수 있는 도구를 제공합니다. 또한 HDInsight에서 지원되는 대부분의 클러스터 유형에 대해 시각화 및 대시보드를 제공합니다. 솔루션으로 수집한 메트릭을 사용하여 사용자 지정 모니터링 규칙 및 경고를 만들 수 있습니다.

다음은 사용 가능한 HDInsight 솔루션입니다.

* HDInsight Hadoop 모니터링
* HDInsight HBase 모니터링
* HDInsight 대화형 쿼리 모니터링
* HDInsight Kafka 모니터링
* HDInsight Spark 모니터링
* HDInsight Storm 모니터링

관리 솔루션을 설치하기 위한 지침은 [Azure의 관리 솔루션](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)을 참조하세요. 실험에 HDInsight Hadoop 모니터링 솔루션을 설치 합니다. 완료되면 **요약**에 **HDInsightHadoop** 타일이 나열됩니다. **HDInsightHadoop** 타일을 선택합니다. HDInsightHadoop 솔루션은 다음과 같습니다.

![HDInsight 모니터링 솔루션 보기](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

클러스터는 새로운 클러스터이므로 보고서에 작업이 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight 클러스터를 모니터링 하려면 로그 쿼리 Azure 모니터링](hdinsight-hadoop-oms-log-analytics-use-queries.md)
