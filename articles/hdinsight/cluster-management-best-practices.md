---
title: 클러스터 관리 모범 사례 - Azure HDInsight
description: HDInsight 클러스터 관리 모범 사례를 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 40222b6a108976de9c82ffccee119b1c1c55f334
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505752"
---
# <a name="hdinsight-cluster-management-best-practices"></a>HDInsight 클러스터 관리 모범 사례

HDInsight 클러스터 관리 모범 사례를 알아봅니다.

## <a name="how-do-i-create-hdinsight-clusters"></a>HDInsight 클러스터를 어떻게 만드나요?

| 옵션 | 문서 |
|---|---|
| Azure Data Factory | [Azure Data Factory를 사용하여 HDInsight에서 주문형 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| 사용자 지정 리소스 관리자 템플릿 | [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| 빠른 시작 템플릿 | [HDInsight 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure 샘플 | [HDInsight Azure 샘플](/samples/browse/?products=azure-hdinsight) |
| Azure portal | [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Azure CLI를 사용하여 HDInsight 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Azure PowerShell을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Azure REST API를 사용하여 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK(.NET, Python, Java) | [.Net](/dotnet/api/overview/azure/hdinsight), [Python](/python/api/overview/azure/hdinsight), [Java](/java/api/overview/azure/hdinsight), [Go](./hdinsight-go-sdk-overview.md) |

> [!Note]
> 이전에 만든 클러스터의 이름을 재사용해 클러스터를 만드는 경우, 이전 클러스터가 다 삭제될 때까지 기다렸다가 클러스터를 만듭니다.

## <a name="how-do-i-customize-hdinsight-clusters"></a>HDInsight 클러스터 사용자 지정을 어떻게 하나요?

| 옵션 | 문서 |
|---|---|
| 스크립트 작업 | [스크립트 동작을 사용하여 Azure HDInsight 클러스터 사용자 지정](./hdinsight-hadoop-customize-cluster-linux.md) |
| 부트스트랩 | [부트스트랩을 사용하여 HDInsight 클러스터 사용자 지정](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| 외부 Metastore | [Azure HDInsight에서 외부 메타데이터 저장소 사용](./hdinsight-use-external-metadata-stores.md) |
| 사용자 지정 Ambari DB | [사용자 지정 Ambari DB를 사용하여 HDInsight 클러스터 설정](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>클러스터를 만들 때 어떤 오류가 발생할 수 있나요?

| Error | 자세한 정보 |
|---|---|
| 할당량 없음 | 각 지역의 구독에서 만들 수 있는 코어 수에 대한 할당량이 있습니다. 자세한 내용은 [용량 계획: 할당량](./hdinsight-capacity-planning.md)을 참조하세요. |
| 사용 가능한 IP 주소가 없음 | 각 VNet에는 제한된 수의 IP 주소가 있습니다. HDInsight 클러스터를 만들 때, 각 노드(zookeeper 및 게이트웨이 노드 포함)는 할당된 IP 주소 중 일부를 사용합니다. IP 주소를 전부 사용 중인 경우에 해당 오류가 발생합니다.  |
| NSG(네트워크 보안 그룹) 규칙은 HDInsight 리소스 공급자와의 통신을 허용하지 않음 | NSG 또는 UDR(사용자 정의 경로)를 사용하여 HDInsight 클러스터에 대한 인바운드 트래픽을 제어하는 경우, 클러스터가 중요한 Azure 상태 및 관리 서비스와 통신할 수 있는지 확인해야 합니다. 자세한 내용은 [Azure HDInsight용 NSG(네트워크 보안 그룹) 서비스 태그](./hdinsight-service-tags.md) 참조 |
| 클러스터 이름 재사용 | 이전에 사용한 적이 있는 클러스터 이름을 사용하는 경우, 클러스터를 다시 만들기 전에 몇 분 정도 기다려야 합니다. 그렇지 않으면 리소스가 이미 존재한다는 메시지가 표시됩니다. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>실행 중인 HDInsight 클러스터를 어떻게 관리하나요?

| 옵션 | 문서 |
|---|---|
| 자동 크기 조정 | [Azure HDInsight 클러스터 자동 크기 조정](./hdinsight-autoscale-clusters.md) |
| 수동 크기 조정 | [Azure HDInsight 클러스터 크기 조정](./hdinsight-scaling-best-practices.md) |
| Ambari를 사용하여 모니터링| [Azure HDInsight의 클러스터 성능 모니터링](./hdinsight-key-scenarios-to-monitor.md) |
| Azure Monitor 로그를 사용하여 모니터링 | [Azure Monitor Logs를 사용하여 HDInsight 클러스터 모니터링](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| 서비스 문제, 계획된 유지 관리, 상태 및 보안 공지 | [구독 특정 서비스 상태 알림 구독](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>삭제된 HDInsight 클러스터를 어떻게 확인하나요?

### <a name="azure-monitor-logs"></a>Azure Monitor 로그

다음 쿼리를 Azure Monitor 로그와 함께 사용하여 삭제된 클러스터를 모니터링할 수 있습니다.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>다음 단계

* [HDInsight 클러스터의 용량 계획](./hdinsight-capacity-planning.md)
* [Azure HDInsight의 기본 및 권장 노드 구성은 무엇인가요?](./hdinsight-supported-node-configuration.md)
