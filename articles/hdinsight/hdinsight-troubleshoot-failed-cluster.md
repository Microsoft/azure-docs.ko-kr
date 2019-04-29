---
title: 속도가 느리거나 오류가 발생하는 HDInsight 클러스터 문제 해결 - Azure HDInsight
description: 속도가 느리거나 오류가 발생하는 HDInsight 클러스터 문제 진단 및 해결
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 785eac065e10c64b99839ab8667e9b613f62aeb9
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764032"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>속도가 느리거나 오류가 발생하는 HDInsight 클러스터 문제 해결

HDInsight 클러스터가 느리게 실행되거나 오류 코드와 함께 실패하는 경우 여러 가지 문제 해결 옵션이 있습니다. 작업 실행 시간이 예상보다 오래 걸리거나 응답 시간이 대체적으로 느려진 경우 클러스터가 실행되는 서비스처럼 클러스터에서 위로 올라가는 업스트림 오류가 있을 수 있습니다. 그러나 이러한 성능 저하의 가장 일반적인 원인은 부족한 크기 조정입니다. 새 HDInsight 클러스터를 만들 때 적절 한 선택 [가상 머신 크기](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)합니다.

속도가 느리거나 오류가 발생하는 클러스터를 진단하려면 관련된 Azure Services, 클러스터 구성, 작업 실행 정보 등 환경의 모든 측면에 대한 정보를 수집합니다. 다른 클러스터에서 오류 상태를 재현해 보면 진단에 도움이 됩니다.

* 1단계: 문제에 대 한 데이터를 수집 합니다.
* 2단계: HDInsight 클러스터 환경 유효성을 검사 합니다.
* 3단계: 클러스터의 상태를 봅니다.
* 4단계: 환경 스택 및 버전을 검토 합니다.
* 5단계: 클러스터 로그 파일을 검사 합니다.
* 6단계: 구성 설정을 확인 합니다.
* 7단계: 다른 클러스터에서 오류를 재현 합니다.

## <a name="step-1-gather-data-about-the-issue"></a>1단계: 문제에 대한 데이터 수집

HDInsight는 클러스터 문제를 식별하고 해결할 수 있는 여러 도구를 제공합니다. 다음 단계에서는 이러한 도구를 안내하고 문제를 정확히 찾아내기 위한 제안 사항을 제공합니다.

### <a name="identify-the-problem"></a>문제 식별

문제를 식별할 수 있도록 다음 질문을 고려합니다.

* 어떤 동작이 발생할 것으로 예상했습니까? 실제로 어떤 동작이 발생했습니까?
* 프로세스를 실행하는 데 시간이 얼마나 걸렸습니까? 얼마나 실행되어야 합니까?
* 내 작업이 이 클러스터에서 항상 느리게 실행됩니까? 다른 클러스터에서는 더 빠르게 실행되었습니까?
* 이 문제가 언제 처음 발생했습니까? 그 이후로 얼마나 자주 발생합니까?
* 내 클러스터 구성에서 변경된 사항이 있습니까?

### <a name="cluster-details"></a>클러스터 세부 정보

중요한 클러스터 정보로 다음과 같은 것이 있습니다.

* 클러스터 이름.
* 클러스터 지역 - [지역 중단](https://azure.microsoft.com/status/)을 확인합니다.
* HDInsight 클러스터 유형 및 버전.
* 헤드 및 작업자 노드에 대해 지정된 HDInsight 인스턴스의 유형 및 수입니다.

Azure Portal에서 이 정보를 제공할 수 있습니다.

![HDInsight Azure Portal 정보](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

사용할 수도 있습니다 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

또 다른 옵션은 PowerShell입니다. 자세한 내용은 [Azure PowerShell을 사용하여 HDInsight에서 Apache Hadoop 클러스터 관리](hdinsight-administer-use-powershell.md)를 참조하세요.

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>2단계: HDInsight 클러스터 환경 유효성 검사

각 HDInsight 클러스터는 다양한 Azure 서비스와 Apache HBase 및 Apache Spark 같은 오픈 소스 소프트웨어를 사용합니다. 또한 HDInsight 클러스터는 Azure Virtual Network 같은 다른 Azure 서비스를 호출할 수 있습니다.  클러스터에서 실행 중인 서비스 또는 외부 서비스 때문에 클러스터 오류가 발생할 수 있습니다.  클러스터 서비스 구성 변경 때문에 클러스터 오류가 발생할 수도 있습니다.

### <a name="service-details"></a>서비스 세부 정보

* 오픈 소스 라이브러리 릴리스 버전을 확인 합니다.
* 확인할 [Azure 서비스 중단](https://azure.microsoft.com/status/)합니다.  
* Azure 서비스 사용 제한 확인 합니다. 
* Azure Virtual Network 서브넷 구성을 확인 합니다.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ambari UI를 사용하여 클러스터 구성 설정 보기

Apache Ambari는 웹 UI와 REST API를 사용하여 HDInsight 클러스터를 관리하고 모니터링할 수 있습니다. Ambari는 Linux 기반 HDInsight 클러스터에 포함되어 있습니다. Azure Portal HDInsight 페이지에서 **클러스터 대시보드** 창을 선택합니다.  **HDInsight 클러스터 대시보드** 창을 선택하여 Ambari UI를 열고, 클러스터 로그인 자격 증명을 입력합니다.  

![Ambari UI](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

서비스 보기 목록을 열려면 Azure Portal 페이지에서 **Ambari Views**를 선택합니다.  이 목록은 설치된 라이브러리에 따라 달라집니다. 예를 들어 YARN 큐 관리자, Hive 보기 및 Tez 보기가 표시될 수 있습니다.  구성 및 서비스 정보를 볼 서비스 링크를 선택합니다.

#### <a name="check-for-azure-service-outages"></a>Azure 서비스 중단 확인

HDInsight는 여러 Azure 서비스를 사용합니다. Azure HDInsight에서 가상 서버를 실행하고 Azure Blob Storage 또는 Azure Data Lake Store에서 데이터와 스크립트를 저장하고 Azure Table Storage의 로그 파일을 인덱싱합니다. 매우 드물기는 하지만 이러한 서비스가 중단되면 HDInsight에서 문제가 발생할 수 있습니다. 클러스터가 예기치 않게 느려지거나 실패할 경우 [Azure 상태 대시보드](https://azure.microsoft.com/status/)를 확인하세요. 각 서비스의 상태는 지역별로 나열됩니다. 클러스터가 있는 지역 및 관련 서비스가 있는 지역을 확인하세요.

#### <a name="check-azure-service-usage-limits"></a>Azure 서비스 사용 제한 확인

대규모 클러스터를 시작하거나 많은 클러스터를 동시에 시작한 경우 Azure 서비스 제한을 초과하면 클러스터가 실패할 수 있습니다. 서비스 제한은 Azure 구독에 따라 다릅니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits)을 참조하세요.
[리소스 관리자 코어 할당량 증가 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)을 통해 Microsoft에 사용 가능한 HDInsight 리소스(예: VM 코어 및 VM 인스턴스) 수를 늘려 달라고 요청할 수 있습니다.

#### <a name="check-the-release-version"></a>릴리스 버전 확인

클러스터 버전을 최신 HDInsight 릴리스와 비교합니다. 각 HDInsight 릴리스는 새로운 애플리케이션, 기능, 패치, 버그 수정 등의 개선 사항을 포함하고 있습니다. 클러스터에 영향을 미치는 문제가 최신 릴리스 버전에서는 해결되었을 수 있습니다. 가능하다면 최신 버전의 HDInsight와 Apache HBase, Apache Spark 같은 관련 라이브러리를 사용하여 클러스터를 다시 실행해 봅니다.

#### <a name="restart-your-cluster-services"></a>클러스터 서비스 다시 시작

클러스터가 느려지면 Ambari UI 또는 Azure 클래식 CLI를 통해 서비스를 다시 시작하는 방안을 고려해 봅니다. 클러스터에서 일시적인 오류가 발생할 수 있으며, 재시작은 환경을 안정시키고 성능을 향상하는 가장 빠른 방법입니다.

## <a name="step-3-view-your-clusters-health"></a>3단계: 클러스터 상태 확인

HDInsight 클러스터는 가상 머신 인스턴스에서 실행되는 여러 가지 유형의 노드로 구성됩니다. 각 노드를 모니터링하여 리소스 부족, 네트워크 연결 문제 및 클러스터 성능을 저하할 수 있는 기타 문제를 확인할 수 있습니다. 각 클러스터는 두 개의 헤드 노드를 포함하고 있으며, 대부분의 클러스터는 작업자 노드와 에지 노드의 조합으로 구성됩니다. 

각 클러스터 유형이 사용하는 다양한 노드에 대한 설명을 보려면 [Apache Hadoop, Apache Spark, Apache Kafka 등을 사용하여 HDInsight에서 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

다음 섹션에서는 각 노드 및 전체 클러스터의 상태를 확인하는 방법을 설명합니다.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Ambari UI 대시보드를 사용하여 클러스터 상태 스냅숏 가져오기

[Ambari UI 대시보드](#view-cluster-configuration-settings-with-the-ambari-ui)(`https://<clustername>.azurehdinsight.net`)는 가동 시간, 메모리, 네트워크 및 CPU 사용량, HDFS 디스크 사용량 등 클러스터 상태에 대한 개요를 제공합니다. Ambari의 호스트 섹션을 사용하여 호스트 수준에서 리소스를 볼 수 있습니다. 서비스를 중지하고 다시 시작할 수도 있습니다.

### <a name="check-your-webhcat-service"></a>WebHCat 서비스 확인

Apache Hive, Apache Pig 또는 Apache Sqoop 작업이 실패하는 일반적인 시나리오 중 하나는 [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)(또는 *Templeton*) 서비스 오류입니다. WebHCat은 Hive, Pig, Scoop, MapReduce 같은 원격 작업 실행을 위한 REST 인터페이스입니다. WebHCat은 작업 제출 요청을 Apache Hadoop YARN 애플리케이션으로 변환하고, YARN 애플리케이션 상태에서 파생된 상태를 반환합니다.  다음 섹션에서는 일반적인 WebHCat HTTP 상태 코드를 설명합니다.

#### <a name="badgateway-502-status-code"></a>잘못된 게이트웨이(502 상태 코드)

게이트웨이 노드에서 반환하는 일반적인 메시지이며, 가장 일반적인 오류 상태 코드입니다. 이 코드가 표시되는 이유 중 하나는 활성 헤드 노드에서 WebHCat 서비스가 중지되는 것입니다. 이 가능성을 확인하려면 다음 CURL 명령을 사용합니다.

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari는 WebHCat 서비스가 중지된 호스트를 보여 주는 경고를 표시합니다. 호스트에서 서비스를 다시 시작하여 WebHCat 서비스를 다시 온라인 상태로 되돌릴 수 있습니다.

![WebHCat 서버 다시 시작](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

여전히 WebHCat 서버가 돌아오지 않으면 오류 메시지의 작업 로그를 확인합니다. 자세한 내용은 노드에서 참조되는 `stderr` 및 `stdout` 파일을 확인하세요.

#### <a name="webhcat-times-out"></a>WebHCat 시간 초과

HDInsight 게이트웨이는 응답 시간이 2분을 초과하면 시간 초과로 처리하고 `502 BadGateway`를 반환합니다. WebHCat이 YARN 서비스의 작업 상태를 쿼리했는데, YARN의 응답 시간이 2분을 초과할 경우 해당 요청이 시간 초과로 처리될 수 있습니다.

이 경우 `/var/log/webhcat` 디렉터리에서 다음 로그를 검토합니다.

* **webhcat.log** - 서버가 로그를 기록하는 log4j 로그
* **webhcat-console.log** - 서버가 시작될 때 서버의 stdout
* **webhcat-console-error.log** - 서버 프로세스의 stderr

> [!NOTE]  
> 각 `webhcat.log`가 매일 롤오버되어 `webhcat.log.YYYY-MM-DD`라는 파일을 생성합니다. 조사하려는 시간 범위에 적합한 파일을 선택합니다.

다음 섹션에서는 WebHCat 시간 제한의 몇 가지 원인을 설명합니다.

##### <a name="webhcat-level-timeout"></a>WebHCat 수준 시간 제한

WebHCat에 부하가 걸리고 소켓이 10개 넘게 열려 있으면 새 소켓 연결을 설정하는 데 더 긴 시간이 걸리고, 이로 인해 시간 제한이 발생할 수 있습니다. WebHCat과의 네트워크 연결을 나열하려면 현재 활성 헤드 노드에서 `netstat` 명령을 사용합니다.

```bash
netstat | grep 30111
```

30111은 WebHCat이 수신 대기하는 포트입니다. 열려 있는 소켓 수가 10개 미만이어야 합니다.

열려 있는 소켓이 없으면 이전 명령이 결과를 생성하지 않습니다. Templeton이 작동 중이고 30111 포트에서 수신 대기 중인지 확인하려면 다음 명령을 사용합니다.

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN 수준 시간 제한

Templeton이 작업을 실행하기 위해 YARN을 호출할 때 Templeton과 YARN 사이의 통신에서 시간 제한이 발생할 수 있습니다.

YARN 수준에서 발생할 수 있는 시간 제한은 두 가지입니다.

1. YARN 작업 제출이 오래 걸려 시간 제한이 발생할 수 있습니다.

    `/var/log/webhcat/webhcat.log` 로그 파일을 열고 "큐에 대기 중인 작업"을 검색하면 실행 시간이 지나치게 긴(> 2000ms) 항목이 여러 개 표시되고, 항목의 대기 시간이 점점 길어지는 것을 볼 수 있습니다.

    새 작업이 제출되는 속도가 기존 작업이 완료되는 시간보다 빠르기 때문에 큐에 대기 중인 작업의 시간이 점점 길어집니다. YARN 메모리가 100% 사용되면 *joblauncher 큐*가 더 이상 *기본 큐*에서 용량을 빌릴 수 없습니다. 따라서 더 이상 joblauncher 큐에 새 작업을 수락할 수 없습니다. 이 동작 때문에 대기 시간이 점점 길어질 수 있으며, 일반적으로 여러 오류 후에 시간 제한 오류가 발생할 수 있습니다.

    다음 이미지는 초과 사용률이 714.4%인 joblauncher 큐입니다. 기본 큐에 빌려올 수 있는 용량이 남아 있는 한 계속 허용됩니다. 그러나 클러스터가 남김 없이 사용되고 YARN 메모리가 100% 용량에 도달하면 새 작업은 대기해야 하고, 결국 시간 제한이 발생합니다.

    ![Joblauncher 큐](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    이 문제를 해결하는 두 가지 방법이 있습니다. 하나는 새 작업이 제출되는 속도를 낮추는 것이고, 다른 하나는 클러스터를 확장하여 이전 작업의 처리 속도를 높이는 것입니다.

2. YARN 처리 시간이 오래 걸릴 수 있고, 이로 인해 시간 제한이 발생할 수 있습니다.

    * 모든 작업 나열: 이것은 시간이 오래 걸리는 호출입니다. 이 호출은 YARN ResourceManager의 애플리케이션을 열거하고, 완료된 각 애플리케이션에 대해 YARN JobHistoryServer에서 상태를 가져옵니다. 작업 수가 많으면 이 호출이 시간 초과될 수 있습니다.

    * 7일 초과 작업 나열: HDInsight YARN JobHistoryServer는 완료된 작업 정보를 7일간 보관하도록 구성됩니다(`mapreduce.jobhistory.max-age-ms` 값). 제거된 작업 결과를 열거하려고 시도하면 시간 제한이 발생합니다.

이러한 문제를 진단하려면:

1. 문제를 해결할 UTC 시간 범위 결정
2. 적절한 `webhcat.log` 파일 선택
3. 해당 시간의 경고 및 오류 메시지 확인

#### <a name="other-webhcat-failures"></a>기타 WebHCat 오류

1. HTTP 상태 코드 500

    대부분의 경우 WebHCat이 500 코드를 반환하면 오류 메시지에 실패에 대한 세부 정보가 포함됩니다. 세부 정보가 없으면 `webhcat.log`에서 경고 및 오류 메시지를 확인합니다.

2. 작업 실패

    WebHCat와의 상호 작용은 성공하지만 작업이 실패하는 경우가 있습니다.

    Templeton은 작업 콘솔 출력을 `statusdir`에 `stderr`로 수집하며, 이 정보는 종종 문제 해결에 유용하게 활용됩니다. `stderr`에는 실제 쿼리의 YARN 애플리케이션 식별자가 포함됩니다.

## <a name="step-4-review-the-environment-stack-and-versions"></a>4단계: 환경 스택 및 버전 검토

Ambari UI **스택 및 버전** 페이지는 클러스터 서비스 구성 및 서비스 버전 기록에 대한 정보를 제공합니다.  Hadoop 서비스 라이브러리 버전이 잘못되면 클러스터가 실패할 수 있습니다.  Ambari UI에서 **Admin** 메뉴를 선택한 다음 **스택 및 버전**을 선택합니다.  해당 페이지에서 **버전** 탭을 선택하여 서비스 버전 정보를 봅니다.

![스택 및 버전](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>5단계: 로그 파일 검사

HDInsight 클러스터를 구성하는 여러 서비스 및 구성 요소에서 생성되는 여러 가지 종류의 로그가 있습니다. [WebHCat 로그 파일](#check-your-webhcat-service)은 앞에서 설명했습니다. 그 외에도 다음 섹션에 설명된 것처럼 조사하여 문제의 원인을 좁힐 수 있는 여러 가지 유용한 로그 파일이 있습니다.

* HDInsight 클러스터는 여러 노드로 구성되며, 대부분의 노드는 제출된 작업을 실행하는 업무를 수행합니다. 작업은 동시에 실행되지만, 로그 파일은 결과를 선형적으로만 표시할 수 있습니다. HDInsight는 새 작업을 실행하고, 완료되지 않은 다른 작업을 먼저 종료합니다. 이 모든 동작이 `stderr` 및 `syslog` 파일에 기록됩니다.

* 스크립트 동작 로그 파일은 클러스터의 생성 프로세스 동안 발생한 오류 또는 예기치 않은 구성 변경을 보여 줍니다.

* Hadoop 단계 로그는 오류가 포함된 단계의 일부로 실행된 Hadoop 작업을 식별합니다.

### <a name="check-the-script-action-logs"></a>스크립트 동작 로그 확인

HDInsight [스크립트 동작](hdinsight-hadoop-customize-cluster-linux.md)은 수동으로 또는 지정된 경우 클러스터에서 스크립트를 실행합니다. 예를 들어 스크립트 동작을 사용하여 클러스터에 추가 소프트웨어를 설치하거나 구성 설정을 기본값에서 다른 값으로 변경할 수 있습니다. 스크립트 동작 로그를 확인하면 클러스터 설치 및 구성 중에 발생한 오류에 대한 정보를 제공할 수 있습니다.  Ambari UI에서 **작업** 단추를 선택하거나 기본 스토리지 계정에서 로그에 액세스하여 스크립트 동작의 상태를 볼 수 있습니다.

스크립트 동작 로그는 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` 디렉터리에 있습니다.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Ambari 빠른 링크를 사용하여 HDInsight 로그 보기

HDInsight Ambari UI에는 많은 **빠른 링크** 섹션이 있습니다.  HDInsight 클러스터의 특정 서비스에 대한 로그 링크에 액세스하려면 클러스터의 Ambari UI를 열고 왼쪽 목록에서 서비스 링크를 선택합니다. **빠른 링크** 드롭다운을 선택하고, 원하는 HDInsight 노드를 선택한 다음 관련된 로그의 링크를 선택합니다.

HDFS 로그를 예로 들면 다음과 같습니다.

![로그 파일에 대한 Ambari 빠른 링크](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Hadoop에서 생성된 로그 파일 보기

HDInsight 클러스터는 Azure 테이블 및 Azure Blob Storage에 기록된 로그를 생성합니다. YARN은 자체적인 실행 로그를 만듭니다. 자세한 내용은 [HDInsight 클러스터에 대한 로그 관리](hdinsight-log-management.md#access-the-hadoop-log-files)를 참조하세요.

### <a name="review-heap-dumps"></a>힙 덤프 검토

힙 덤프는 애플리케이션의 메모리 스냅숏과 해당 시점의 변수 값을 포함하고 있으며, 이 정보는 런타임에 발생하는 문제를 진단하는 데 유용합니다. 자세한 내용은 [Linux 기반 HDInsight에서 Apache Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)을 참조하세요.

## <a name="step-6-check-configuration-settings"></a>6단계: 구성 설정 확인

HDInsight 클러스터는 기본 설정을 사용하여 Hadoop, Hive, HBase 등의 관련 서비스에 대해 미리 구성됩니다. 클러스터 종류, 하드웨어 구성, 노드 수, 실행 중인 작업 유형, 작업하는 데이터(및 해당 데이터가 처리되는 방식)에 따라 구성 최적화가 필요할 수 있습니다.

대부분의 시나리오에 대해 성능 구성을 최적화하는 방법에 대한 자세한 내용은 [Apache Ambari를 사용하여 클러스터 구성 최적화](hdinsight-changing-configs-via-ambari.md)를 참조하세요. Spark를 사용할 때 [성능을 위해 Apache Spark 작업 최적화](spark/apache-spark-perf.md)를 참조하세요. 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>7단계: 다른 클러스터에서 오류 재현

클러스터 오류의 원인을 진단하려면 동일한 구성으로 새 클러스터를 시작한 후 실패한 작업의 단계를 하나씩 다시 제출합니다. 각 단계의 결과를 확인한 후 다음 단계를 처리합니다. 이 방법을 사용하면 실패한 각 단계를 수정하고 다시 실행할 수 있습니다. 또한 입력 데이터를 한 번만 로드하면 된다는 장점이 있습니다.

1. 실패한 클러스터와 동일한 구성으로 새 테스트 클러스터를 만듭니다.
2. 첫 번째 작업 단계를 테스트 클러스터에 제출합니다.
3. 단계에서 처리가 완료되면 단계 로그 파일에서 오류를 확인합니다. 테스트 클러스터의 마스터 노드에 연결하고 거기서 로그 파일을 봅니다. 단계 로그 파일은 단계가 어느 정도 실행된 후에야 나타나고, 완료되거나 실패합니다.
4. 첫 번째 단계가 성공하면 그 다음 단계를 실행합니다. 오류가 있으면 로그 파일의 오류를 조사합니다. 코드 오류인 경우 오류를 수정하고 단계를 다시 실행합니다.
5. 모든 단계가 오류 없이 실행될 때까지 계속 진행합니다.
6. 테스트 클러스터 디버깅이 완료되면 테스트 클러스터를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [HDInsight 로그 분석](hdinsight-debug-jobs.md)
* [Linux 기반 HDInsight에서 Apache Hadoop YARN 응용 프로그램 로그인 액세스](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Linux 기반 HDInsight에서 Apache Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight의 Apache Spark 클러스터에 대한 알려진 문제](hdinsight-apache-spark-known-issues.md)
