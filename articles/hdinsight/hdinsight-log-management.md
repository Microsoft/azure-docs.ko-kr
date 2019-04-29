---
title: HDInsight 클러스터에 대한 로그 관리 - Azure HDInsight
description: HDInsight 활동 로그 파일의 형식, 크기 및 보존 정책을 결정합니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: b42eb51b510423ffc0d15ee3a646bca3d4392f7f
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766781"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>HDInsight 클러스터에 대한 로그 관리

HDInsight 클러스터는 다양한 로그 파일을 생성합니다. 예를 들어 Apache Hadoop 및 Apache Spark 같은 관련 서비스는 구체적인 작업 실행 로그를 생성합니다. 로그 파일 관리는 정상 HDInsight 클러스터를 유지 관리하는 업무의 일부입니다. 로그를 보관하도록 규정하는 요구 사항도 존재할 수 있습니다.  로그 파일의 수와 크기 때문에 로그 저장 및 보관을 최적화하면 서비스 비용 관리에 도움이 됩니다.

HDInsight 클러스터 로그 관리에는 클러스터 환경의 모든 측면에 대한 정보를 유지가 포함됩니다. 모든 관련 Azure 서비스 로그, 클러스터 구성, 작업 실행 정보, 모든 오류 상태 및 기타 필요한 데이터가 이 정보에 포함됩니다.

일반적인 HDInsight 로그 관리 단계는 다음과 같습니다.

* 1단계: 로그 보존 정책 결정
* 2단계: 클러스터 서비스 버전 구성 로그 관리
* 3단계: 클러스터 작업 실행 로그 파일 관리
* 4단계: 로그 볼륨 스토리지 크기 및 비용 예측
* 5단계: 로그 보관 정책 및 프로세스 결정

## <a name="step-1-determine-log-retention-policies"></a>1단계: 로그 보존 정책 결정

HDInsight 클러스터 로그 관리 전략을 만드는 첫 번째 단계는 비즈니스 시나리오 및 작업 실행 기록 저장소 요구 사항에 대한 정보를 수집하는 것입니다.

### <a name="cluster-details"></a>클러스터 세부 정보

다음 클러스터 세부 정보는 로그 관리 전략에 대한 정보를 수집하는 데 유용합니다. 특정 Azure 계정으로 만든 모든 HDInsight 클러스터에서 이 정보를 수집합니다.

* 클러스터 이름
* 클러스터 지역 및 Azure 가용성 영역
* 마지막 상태 변경 세부 정보를 포함한 클러스터 상태
* 마스터, 코어 및 작업 노드에 대해 지정된 HDInsight 인스턴스의 유형 및 수

Azure Portal을 사용하여 이 최상위 수준 정보를 대부분 얻을 수 있습니다.  사용할 수 있습니다 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 에 HDInsight 클러스터에 대 한 정보를 가져오려면:

```azurecli
    az hdinsight list --resource-group <ResourceGroup>
    az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

PowerShell을 사용하여 이 정보를 볼 수도 있습니다.  자세한 내용은 [Apache에서 Azure PowerShell을 사용하여 HDInsight의 Hadoop 클러스터 관리](hdinsight-administer-use-powershell.md)를 참조하세요.

### <a name="understand-the-workloads-running-on-your-clusters"></a>클러스터에서 실행되는 워크로드 이해

HDInsight 클러스터에서 실행 중인 워크로드 유형을 이해하여 각 유형에 적합한 로깅 전략을 설계하는 것이 중요합니다.

* 워크로드가 실험적입니까(예: 개발 또는 테스트) 아니면 프로덕션 품질입니까?
* 프로덕션 품질 워크로드가 일반적으로 얼마나 자주 실행됩니까?
* 리소스를 많이 사용하고/하거나 오래 실행되는 워크로드가 있습니까?
* 여러 유형의 로그가 생성되는 복잡한 Hadoop 서비스 세트를 사용하는 워크로드가 있습니까?
* 관련 규정 실행 계보 요구 사항이 있는 워크로드가 있습니까?

### <a name="example-log-retention-patterns-and-practices"></a>예제 로그 보존 패턴 및 사례

* 각 로그 항목에 식별자를 추가하거나 다른 기술을 통해 데이터 계보 추적을 유지하는 방안을 고려해 봅니다. 이를 통해 데이터 및 작업의 원래 원본을 추적하고, 데이터를 따라 각 단계를 통과하여 일관성 및 유효성을 이해할 수 있습니다.

* 클러스터에서 또는 둘 이상의 클러스터에서 로그를 수집하고 감사, 모니터링, 계획, 경고 등의 목적에 사용할 수 있도록 정렬하는 방안을 고려해 봅니다. 사용자 정의 솔루션을 사용하여 정기적으로 로그 파일에 액세스 및 다운로드하고, 로그 파일을 결합 및 분석하여 대시보드 표시를 제공할 수 있습니다. 보안 또는 실패 감지에 대해 경고하는 추가 기능을 추가할 수도 있습니다. 이러한 기능은 PowerShell, HDInsight SDK 또는 Azure 클래식 배포 모델에 액세스하는 코드를 사용하여 빌드할 수 있습니다.

* 모니터링 솔루션 또는 서비스가 유용한 이점이 될 수 있는지 여부를 고려합니다. Microsoft System Center에서는 [HDInsight 관리 팩](https://www.microsoft.com/download/details.aspx?id=42521)을 제공합니다. Apache Chukwa 및 Ganglia 같은 타사 도구를 사용하여 로그를 수집하고 중앙 집중화할 수도 있습니다. 많은 회사에서는 Hadoop 기반 빅 데이터 솔루션을 모니터링하기 위해 Centerity, Compuware APM, Sematext SPM 및 Zettaset Orchestrator와 같은 서비스를 제공합니다.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>2단계: 클러스터 서비스 버전 관리 및 스크립트 작업 로그 보기

일반적인 HDInsight 클러스터는 여러 서비스와 오픈 소스 소프트웨어 패키지를 사용합니다(Apache HBase, Apache Spark 등). 생물정보학 같은 일부 워크로드의 경우 작업 실행 로그 외에도 서비스 구성 로그 기록을 의무적으로 유지해야 할 수도 있습니다.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Ambari UI를 사용하여 클러스터 구성 설정 보기

Apache Ambari는 웹 UI 및 REST API를 제공하므로 HDInsight 클러스터의 관리, 구성 및 모니터링이 간단합니다. Ambari는 Linux 기반 HDInsight 클러스터에 포함되어 있습니다. 선택 합니다 **클러스터 대시보드** 창을 열려면 Azure portal HDInsight 페이지의 **클러스터 대시보드** 링크 페이지.  다음으로 **HDInsight 클러스터 대시보드** 창을 선택하여 Ambari UI를 엽니다.  클러스터 로그인 자격 증명을 입력하라는 메시지가 나타납니다.

서비스 보기 목록을 열려면 HDInsight의 Azure Portal 페이지에서 **Ambari 보기** 창을 선택합니다.  이 목록은 설치된 라이브러리에 따라 달라집니다.  예를 들어 YARN 큐 관리자, Hive 보기 및 Tez 보기가 표시될 수 있습니다.  아무 서비스 링크를 선택하여 구성 및 서비스 정보를 살펴봅니다.  Ambari UI **스택 및 버전** 페이지는 클러스터 서비스의 구성 및 서비스 버전 기록에 대한 정보를 제공합니다. Ambari UI에서 이 섹션으로 이동하려면 **관리자** 메뉴를 선택한 다음 **스택 및 버전**을 선택합니다.  **버전** 탭을 선택하여 서비스 버전 정보를 봅니다.

![스택 및 버전](./media/hdinsight-log-management/stack-versions.png)

Ambari UI를 사용하여 클러스터의 특정 호스트(또는 노드)에서 실행 중인 특정 서비스 또는 모든 서비스에 대한 구성을 다운로드할 수 있습니다.  **호스트** 메뉴를 선택한 다음 원하는 호스트의 링크를 선택합니다. 해당 호스트의 페이지에서 **호스트 작업** 단추를 선택한 다음 **클라이언트 구성 다운로드**를 선택합니다. 

![호스트 클라이언트 구성](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>스크립트 동작 로그 보기

HDInsight [스크립트 동작](hdinsight-hadoop-customize-cluster-linux.md)은 수동으로 또는 지정된 경우 클러스터에서 스크립트를 실행합니다. 예를 들어 스크립트 동작을 사용하여 클러스터에 추가 소프트웨어를 설치하거나 구성 설정을 기본값에서 다른 값으로 변경할 수 있습니다. 스크립트 동작 로그는 클러스터를 설치하는 동안 발생한 오류와 클러스터 성능 및 가용성에 영향을 줄 수 있는 구성 설정 변경에 대한 정보를 제공할 수 있습니다.  스크립트 동작의 상태를 보려면 Ambari UI에서 **작업** 단추를 선택하거나 기본 스토리지 계정에서 상태 로그에 액세스합니다. 스토리지 로그는 `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`에서 지원됩니다.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>3단계: 클러스터 작업 실행 로그 파일 관리

다음 단계는 다양한 서비스에 대한 작업 실행 로그 파일을 검토하는 것입니다.  Apache HBase, Apache Spark 및 기타 여러 서비스가 포함될 수 있습니다. Hadoop 클러스터는 자세한 정보 표시 로그를 대량으로 생성하므로 유용한 로그와 그렇지 않은 로그를 확인하는 데 시간이 오래 걸릴 수 있습니다.  로그 파일 대상 관리가 가능하도록 로깅 시스템을 이해하는 것이 중요합니다.  다음은 로그 파일 예제입니다.

![HDInsight 로그 파일 예](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Hadoop 로그 파일에 액세스

HDInsight는 로그 파일을 클러스터 파일 시스템과 Azure 저장소에 모두 저장합니다. 열어 클러스터의 로그 파일을 검사할 수 있습니다는 [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) 클러스터 및 파일 시스템을 탐색 하거나 원격 헤드 노드 서버에서 Hadoop YARN 상태 포털을 사용 하 여 연결 합니다. Azure 저장소에서 데이터에 액세스 및 다운로드할 수 있는 도구 중 하나를 사용하여 Azure 저장소에 있는 로그 파일을 검사할 수 있습니다. 예로 [AzCopy](../storage/common/storage-use-azcopy.md)를 [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer), 및 Visual Studio 서버 탐색기. PowerShell 및 Azure Storage 클라이언트 라이브러리를 사용하거나 Azure .NET SDK를 사용하여 Azure Blob Storage의 데이터에 액세스할 수 있습니다.

Hadoop은 클러스터의 다양한 노드에서 작업을 *작업 시도*로 실행합니다. HDInsight는 추측성 작업 시도를 시작한 후 완료되지 않는 다른 작업 시도를 먼저 종료할 수 있습니다. 이로 인해 컨트롤러, stderr 및 syslog 로그 파일에 즉시 기록되는 활동이 상당히 많이 생성됩니다. 뿐만 아니라 여러 작업 시도가 동시에 실행되지만 로그 파일은 결과를 선형적으로만 표시할 수 있습니다.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Azure Blob Storage에 기록된 HDInsight 로그

HDInsight 클러스터는 Azure PowerShell cmdlet 또는 .NET 작업 제출 API를 사용하여 제출된 모든 작업에 대한 Azure Blob Storage 계정에 작업 로그를 기록하도록 구성됩니다.  SSH 통해 작업을 클러스터에 제출하면 이전 섹션에서 설명한 대로 Azure 테이블에 실행 로깅 정보가 저장됩니다.

HDInsight에서 생성하는 핵심 로그 파일 외에도 YARN을 비롯한 설치된 서비스에서 작업 실행 로그 파일을 생성합니다.  로그 파일의 수와 종류는 설치된 서비스에 따라 다릅니다.  많이 사용되는 서비스로 Apache HBase, Apache Spark 등이 있습니다.  각 서비스에 대한 작업 로그 실행 파일을 조사하여 클러스터에서 사용할 수 있는 전체 로깅 파일을 이해합니다.  각 서비스는 고유의 로깅 방법과 로그 파일 저장 위치를 사용합니다.  예를 들어 YARN에서 가장 일반적인 서비스 로그 파일에 액세스하는 방법에 대한 자세한 내용이 다음 섹션에 설명되어 있습니다.

### <a name="hdinsight-logs-generated-by-yarn"></a>YARN에서 생성한 HDInsight 로그

YARN은 작업자 노드의 모든 컨테이너에서 로그를 집계한 후 작업자 노드당 하나의 집계된 로그 파일로 저장합니다. 해당 로그는 애플리케이션이 완료된 후 기본 파일 시스템에 저장됩니다. 애플리케이션은 수백 수천 개의 컨테이너를 사용할 수 있지만 단일 작업자 노드에서 실행되는 모든 컨테이너에 대한 로그는 항상 단일 파일로 집계됩니다. 애플리케이션에서 사용하는 작업자 노드당 하나의 로그만 있습니다. 로그 집계는 HDInsight 클러스터 버전 3.0 이상에서 기본적으로 사용됩니다. 집계된 로그는 클러스터에 대한 기본 스토리지에 있습니다.

```
    /app-logs/<user>/logs/<applicationId>
```

집계된 로그는 컨테이너별로 인덱싱된 이진 형식인 TFile로 작성되므로 직접 읽을 수 없습니다. 관심 있는 애플리케이션 또는 컨테이너에 대한 이러한 로그를 일반 텍스트로 보려면 YARN ResourceManager 로그 또는 CLI 도구를 사용합니다.

#### <a name="yarn-cli-tools"></a>YARN CLI 도구

YARN CLI 도구를 사용하려면 먼저 SSH를 사용하여 HDInsight 클러스터에 연결해야 합니다. 이러한 명령을 실행할 때 `<applicationId>`, `<user-who-started-the-application>`, `<containerId>` 및 `<worker-node-address>` 정보를 지정합니다. 다음 명령 중 하나를 실행하여 이러한 로그를 일반 텍스트로 볼 수 있습니다.

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI는 클러스터 헤드 노드에서 실행되며 Ambari 웹 UI를 통해 액세스할 수 있습니다. 다음 단계를 사용하여 YARN 로그를 봅니다.

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net`으로 이동합니다. CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.
2. 왼쪽에 있는 서비스 목록에서 YARN을 선택합니다.
3. 빠른 링크 드롭다운에서 클러스터 헤드 노드 중 하나를 선택한 다음 **ResourceManager 로그**를 선택합니다. YARN 로그에 대한 링크 목록이 나타납니다.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>4단계: 로그 볼륨 스토리지 크기 및 비용 예측

이전 단계에서는 HDInsight 클러스터가 생성하는 로그 파일의 종류와 볼륨을 알아보았습니다.

다음으로 일정 기간의 핵심 로그 저장소 위치에 있는 로그 데이터 볼륨을 분석합니다. 예를 들어 30-60-90일의 볼륨 및 성장을 분석할 수 있습니다.  이 정보를 스프레드시트에 기록하거나 Visual Studio, Azure Storage 탐색기, Microsoft Excel용 파워 쿼리 같은 다른 도구를 사용합니다. 자세한 내용은 [HDInsight 로그 분석](hdinsight-debug-jobs.md)을 참조하세요.  

핵심 로그에 대한 로그 관리 전략을 만드는 데 필요한 정보가 충분히 수집되었습니다.  스프레드시트(또는 원하는 도구)를 사용하여 향후 Azure 서비스에서 감당할 로그 크기 증가 및 로그 저장소를 예측합니다.  검사하는 로그 집합에 대한 로그 보존 요구 사항도 고려해야 합니다.  이제 삭제해도 되는 로그 파일과 보존해야 하는 로그를 확인하고 보다 저렴한 Azure 저장소에 보관한 후 미래의 로그 저장소 비용을 다시 예측할 수 있습니다.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>5단계: 로그 보관 정책 및 프로세스 결정

삭제해도 되는 로그 파일을 확인한 후에는 여러 Hadoop 서비스에서 로깅 매개 변수를 조정하여 지정된 시간이 지나면 자동으로 로그 파일을 삭제할 수 있습니다.

특정 로그 파일의 경우 보다 저렴한 로그 파일 보관 방법을 사용할 수 있습니다. Azure Resource Manager 활동 로그의 경우 Azure Portal을 사용하여 이 방법을 살펴볼 수 있습니다.  HDInsight 인스턴스에 대한 Azure Portal에서 **활동 로그** 링크를 선택하여 ARM 로그 보관을 설정합니다.  활동 로그 검색 페이지 맨 위에서 **내보내기** 메뉴 항목을 선택하여 **활동 로그 내보내기** 창을 엽니다.  구독, 지역, 스토리지 계정에 내보낼 것인지 여부, 로그를 보존할 일 수를 입력합니다. 이 창에서 이벤트 허브로 내보낼 것인지 여부도 나타낼 수 있습니다. 

![로그 파일 내보내기](./media/hdinsight-log-management/archive.png)

또는 PowerShell을 사용하여 로그 보관을 스크립팅할 수 있습니다.  PowerShell 스크립트 예제는 [Azure Blob Storage에 Azure Automation 로그 보관](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8)을 참조하세요.

### <a name="accessing-azure-storage-metrics"></a>Azure 저장소 메트릭 액세스

저장소 작업 및 액세스를 기록하도록 Azure 저장소를 구성할 수 있습니다. 이와 같은 매우 구체적인 로그는 용량을 모니터링하고 계획을 수립하고 스토리지에 대한 요청을 감사하는 데 사용할 수 있습니다. 기록되는 정보 중에는 솔루션의 성능을 모니터링하고 미세 조정할 수 있는 대기 시간 세부 정보가 포함됩니다.
Hadoop용 .NET SDK를 사용하여 HDInsight 클러스터에 대한 데이터를 보유하는 Azure 저장소에 대해 생성된 로그 파일을 검사할 수 있습니다.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>오래된 로그 파일의 백업 인덱스 크기 및 수 제어

보존된 로그 파일의 크기와 수를 제어하려면 `RollingFileAppender`의 다음 속성을 설정합니다.

* `maxFileSize`는 중요한 파일 크기로, 이 값보다 큰 파일은 제한됩니다. 기본값은 10MB입니다.
* `maxBackupIndex`는 만들 백업 파일 수를 지정하며 기본값은 1입니다.

### <a name="other-log-management-techniques"></a>기타 로그 관리 기술

디스크 공간 부족을 방지 하려면 따르면 일부 OS 도구와 같은 [logrotate](https://linux.die.net/man/8/logrotate) 로그 파일의 처리를 관리 합니다. 매일 실행되도록 `logrotate`를 구성하여 로그 파일을 압축하고 오래된 파일을 제거할 수 있습니다. 접근 방식은 로컬 노드에 로그 파일을 유지하는 시간 등의 요구 사항에 따라 달라집니다.  

또한 하나 이상의 서비스에 디버그 로깅을 사용하도록 설정되었는지 확인할 수 있습니다. 설정된 경우 출력 로그 크기가 급격하게 커집니다.  

모든 노드의 로그를 중앙의 한 위치에 수집하려면 모든 로그 항목을 Solr에 수집하는 것과 같은 데이터 흐름을 만들면 됩니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight의 모니터링 및 로깅 연습](https://msdn.microsoft.com/library/dn749790.aspx)
* [Linux 기반 HDInsight에서 Apache Hadoop YARN 응용 프로그램 액세스 로그](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [다양한 Apache Hadoop 구성 요소의 로그 파일 크기를 제어하는 방법](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
