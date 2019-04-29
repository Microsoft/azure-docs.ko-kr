---
title: 클러스터 크기 조정 - Azure HDInsight
description: 워크로드에 맞게 HDInsight 클러스터의 크기를 조정합니다.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: ashish
ms.openlocfilehash: a172024e4662e647b39fe999f1be3cfcef04b5ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763612"
---
# <a name="scale-hdinsight-clusters"></a>HDInsight 클러스터 크기 조정

HDInsight는 클러스터의 작업자 노드 수를 증가 및 감소하는 옵션을 제공하여 유연성을 보장합니다. 따라서 몇 시간 후에 또는 주말에 클러스터를 축소했다가, 비즈니스 요구가 최대 수준일 때 확장할 수 있습니다.

예를 들어, 하루에 1번 또는 한 달에 1번 수행되는 일부 일괄 처리가 있을 때, 적절한 메모리 및 CPU 계산 능력을 확보하기 위해 예약된 이벤트보다 몇 분 전에 HDInsight 클러스터를 확장할 수 있습니다.  나중에, 처리가 완료된 후 사용량이 다시 줄어들면, HDInsight 클러스터를 더 적은 수의 작업자 노드로 축소할 수 있습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="utilities-to-scale-clusters"></a>클러스터 크기를 조정 하는 유틸리티

Microsoft는 클러스터 크기를 조정 하는 다음 유틸리티를 제공 합니다.

|유틸리티 | 설명|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)|[az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> --name \<Cluster Name> --target-instance-count \<NewSize>|
|[Azure 클래식 CLI](hdinsight-administer-use-command-line.md)|azure hdinsight 클러스터 크기 조정 \<clusterName > \<대상 인스턴스의 수가 >|
|[Azure Portal](https://portal.azure.com)|HDInsight 클러스터 창의 열을 선택 합니다 **클러스터 크기** 왼쪽 메뉴에서 클러스터 크기 창에서 작업자 노드 수를 입력 하 고 저장을 선택 합니다.|  

![클러스터 크기 조정](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

이러한 방법 중 하나를 사용하여 몇 분 만에 HDInsight 클러스터를 확장 또는 축소할 수 있습니다.

> [!IMPORTANT]  
> * Aure 클래식 CLI가 사용 되지 않으며 클래식 배포 모델로 사용 해야 합니다. 다른 모든 배포에 사용 된 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)합니다.  
> * PowerShell AzureRM 모듈을 사용 하는 사용 되지 않습니다.  사용 하십시오 합니다 [Az 모듈](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) 가능 합니다.

## <a name="scaling-impacts-on-running-jobs"></a>크기 조정이 실행 중인 작업에 미치는 영향

실행 중인 HDInsight 클러스터에 노드를 **추가**할 경우 보류 중이거나 실행 중인 작업에는 영향을 미치지 않습니다. 또한 크기 조정 프로세스가 실행되는 동안 새 작업을 안전하게 제출할 수 있습니다. 어떤 이유로든 크기 조정 작업이 실패할 경우 오류가 정상적으로 처리되며 클러스터는 작동 상태를 유지합니다.

그러나 노드를 **제거**하여 클러스터를 축소하는 경우, 크기 조정 작업이 완료될 때 보류 중이거나 실행 중인 작업은 모두 실패합니다. 이 오류는 이 프로세스 동안 일부 서비스가 다시 시작되기 때문입니다.

이 문제를 해결하기 위해 클러스터를 축소하기 전에 작업이 완료될 때까지 기다린 다음, 수동으로 작업을 종료하거나 크기 조정 작업이 완료된 후에 작업을 다시 제출할 수 있습니다.

보류 중이거나 실행 중인 작업 목록을 보려면 YARN ResourceManager UI를 사용하여 다음 단계를 수행할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽에서 이동 **모든 서비스** > **Analytics** > **HDInsight 클러스터**, 한 다음 클러스터를 선택 합니다.
3. 기본 보기에서로 이동 **클러스터 대시보드** > **Ambari 홈**합니다. 클러스터 로그인 자격 증명을 입력합니다.
4. Ambari UI에서 선택 **YARN** 왼쪽 메뉴의 서비스 목록에 있습니다.  
5. YARN 페이지에서 선택 **빠른 링크** 하 고 활성 헤드 노드를 마우스로 클릭 **ResourceManager UI**합니다.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`에서 ResourceManager UI에 직접 액세스할 수 있습니다.

현재 상태와 함께 작업 목록이 표시됩니다. 스크린샷에는 현재 실행 중인 하나의 작업만 나와 있습니다.

![ResourceManager UI 애플리케이션](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

실행 중인 해당 애플리케이션을 수동으로 종료하려면 SSH 셸에서 다음 명령을 실행합니다.

```bash
yarn application -kill <application_id>
```

예를 들면 다음과 같습니다.

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-apache-hbase-cluster"></a>Apache HBase 클러스터 부하 다시 분산

지역 서버는 크기 조정 작업을 완료한 후 몇 분 안에 자동으로 균형을 맞춥니다. 지역 서버를 수동으로 조정하려면 다음 단계를 사용합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. HBase 셸을 시작합니다.

        hbase shell

3. 다음 명령을 사용하여 지역 서버의 부하를 수동으로 분산합니다.

        balancer

## <a name="scale-down-implications"></a>규모 축소가 미치는 영향

앞에서 설명한 대로, 보류 중이거나 실행 중인 작업은 축소 작업이 완료될 때 종료됩니다. 그러나 축소에 따른 다른 잠재적인 영향도 있을 수 있습니다.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight 이름 노드가 축소 후에 안전 모드를 유지함

최소 1 명의 작업자 노드로 클러스터를 축소 하는 경우 작업자 노드가 패치로 인해 또는 크기 조정 작업 후 즉시 다시 부팅 하는 경우 Apache HDFS 안전 모드에서 고정 될 수 있습니다.

이 오류의 주요 원인은 Hive가 소수의 `scratchdir` 파일을 사용하고, 기본적으로 각 블록의 복제본이 3개 있다고 예상하지만, 최소 1개의 작업자 노드로 축소하는 경우 복제본이 1개로만 유지된다는 것입니다. 따라서 `scratchdir`의 파일은 *덜 복제*됩니다. 이로 인해 HDFS는 크기 조정 작업 후에 서비스가 다시 시작될 때 안전 모드를 유지할 수 있습니다.

축소 시도가 발생할 때 HDInsight는 Apache Ambari 관리 인터페이스를 먼저 다른 온라인 작업자 노드로 하 여 HDFS 블록 복제는 불필요 한 작업자 노드 서비스 해제 하 고 안전 하 게 축소할 클러스터에 의존 합니다. HDFS는 유지 관리 기간 동안 안전 모드가 되며, 크기 조정이 완료되면 안전 모드에서 해제됩니다. 이때 HDFS는 안전 모드에서 고정될 수 있습니다.

HDFS는 `dfs.replication` 값 3으로 구성됩니다. 따라서 사용 가능한 각 블록의 예상되는 복사본이 3개가 아니므로, 사용 가능한 각 블록의 스크래치 파일의 블록은 3개 미만의 작업자 노드가 온라인 상태가 될 때마다 덜 복제됩니다.

HDFS를 안전 모드에서 해제하는 명령을 실행할 수 있습니다. 예를 들어, 안전 모드가 설정되는 유일한 이유가 임시 파일이 덜 복제되기 때문이라는 사실이 분명할 경우 안전 모드를 해제해도 안전합니다. 덜 복제된 파일이 Hive 임시 스크래치 파일이기 때문입니다.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

안전 모드를 해제한 후에는 임시 파일을 수동으로 제거하거나 Hive에서 자동으로 정리할 때까지 기다릴 수 있습니다.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>안전 모드가 켜지는 오류 예제

* H070 Hive 세션을 열 수 없습니다. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0 **디렉터리를 만들 수 없습니다**. **이름 노드가 안전 모드 상태입니다**. 보고된 블록 75는 총 블록 87의 임계값 0.9900에 도달하려면 추가로 12개의 블록이 필요합니다. 라이브 데이터 노드의 수 10이 최소 수인 0에 도달했습니다. 이 임계값에 도달하면 안전 모드가 자동으로 해제됩니다.

* H100 show databases 문을 제출할 수 없습니다. org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1]에 대한 연결이 실패했습니다. **연결이 거부되었습니다.**

* H020 hn0-hdisrv.servername.bx.internal.cloudapp.net:10001에 대한 연결을 설정할 수 없습니다. org.apache.thrift.transport.TTransportException: Http에 대 한 http 연결을 만들지 못했습니다:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/ 합니다. org.apache.http.conn.HttpHostConnectException: hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28]에 대한 연결이 실패했습니다. 연결이 거부되었습니다. org.apache.thrift.transport.TTransportException: Http에 대 한 http 연결을 만들지 못했습니다:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/ 합니다. org.apache.http.conn.HttpHostConnectException: hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28]에 대한 연결이 실패했습니다. **연결이 거부되었습니다.**

* Hive 로그에서: WARN [main]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – 시도 21에서 HiveServer2를 시작하는 중 오류가 발생했습니다. 60초 이내에 다시 시도합니다. java.lang.RuntimeException: Hive 구성에서 권한 부여 정책을 적용하는 중 오류가 발생했습니다. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374 **디렉터리를 만들 수 없습니다**. **이름 노드가 안전 모드 상태입니다**.
    보고된 블록 0은 총 블록 9의 임계값 0.9900에 도달하려면 추가로 9개의 블록이 필요합니다.
    라이브 데이터 노드의 수 10이 최소 수인 0에 도달했습니다. **이 임계값에 도달하면 안전 모드가 자동으로 해제됩니다**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

클러스터 크기가 조정될 시간이 가까워질 때 `/var/log/hadoop/hdfs/` 폴더에서 이름 노드 로그를 검토하여 안전 모드가 시작되었는지 확인할 수 있습니다. 로그 파일의 이름은 `Hadoop-hdfs-namenode-hn0-clustername.*`입니다.

이전 오류의 근본 원인은 Hive가 쿼리를 실행하는 동안 HDFS에서 임시 파일을 사용하는 것입니다. HDFS가 안전 모드를 시작하면 Hive는 HDFS에 쓸 수 없으므로 쿼리를 실행할 수 없습니다. HDFS의 임시 파일은 개별 작업자 노드 VM에 탑재된 로컬 드라이브에 있으며, 다른 작업자 노드 간에 복제되어 최소 3개의 복제본이 유지됩니다.

Hive의 `hive.exec.scratchdir` 매개 변수는 `/etc/hive/conf/hive-site.xml` 내에서 구성됩니다.

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>HDFS 파일 시스템의 상태 보기

각 이름 노드의 상태 보고서를 확인하여 노드가 안전 모드에 있는지 알아볼 수 있습니다. 이 보고서를 보려면 각 헤드 노드에 대해 SSH를 수행하고 다음 명령을 실행합니다.

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![안전 모드 해제](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> `-D` 스위치는 HDInsight의 기본 파일 시스템이 Azure Storage 또는 Azure Data Lake Storage 중 하나이므로 필요합니다. `-D`는 명령이 로컬 HDFS 파일 시스템에 대해 실행되도록 지정합니다.

다음으로, HDFS 상태의 세부 정보를 표시하는 보고서를 볼 수 있습니다.

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

이 명령을 실행하면 모든 블록이 예상되는 수준만큼 복제되는 정상 클러스터 상태가 유지됩니다.

![안전 모드 해제](./media/hdinsight-scaling-best-practices/report.png)

HDFS는 파일의 블록이 누락되거나 블록이 덜 복제되는 것과 같은 다양한 파일의 불일치 상태를 확인하는 `fsck` 명령을 지원합니다. `scratchdir`(임시 스크래치 디스크) 파일에 대해 `fsck` 명령을 실행하려면

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

덜 복제된 블록이 없는 정상 HDFS 파일 시스템에 대해 실행할 경우 다음과 비슷한 출력이 표시됩니다.

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

반대로, `fsck` 명령이 일부 블록이 덜 복제된 HDFS 파일 시스템에 대해 실행되면 출력은 다음과 유사합니다.

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

또한 왼쪽에서 **HDFS** 서비스를 선택하거나 `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`를 사용하여 Ambari UI에서 HDFS 상태를 확인할 수도 있습니다.

![Ambari HDFS 상태](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

또한 활성 또는 대기 NameNode에서 하나 이상의 심각한 오류가 발생할 수도 있습니다. NameNode 블록 상태를 보려면 경고 옆의 NameNode 링크를 선택합니다.

![NameNode 블록 상태](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

블록 복제 오류를 제거 하는 스크래치 파일을 정리 하려면 SSH를 각 헤드 노드 및 다음 명령을 실행:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> 이 명령을 실행하면 일부 작업이 아직 실행 중인 경우 Hive가 중단될 수 있습니다.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>블록이 덜 복제된 이유로 인해 HDInsight가 안전 모드를 유지하지 못하게 하는 방법

다음과 같은 여러 가지 방법으로 HDInsight가 안전 모드를 유지하지 않도록 할 수 있습니다.

* HDInsight를 축소하기 전에 모든 Hive 작업을 중지합니다. 또는 실행 중인 Hive 작업과 충돌하지 않도록 축소 프로세스를 예약합니다.
* 축소하기 전에 HDFS에서 Hive의 스크래치 `tmp` 디렉터리 파일을 수동으로 정리합니다.
* HDInsight를 최소 3개의 작업자 노드로만 축소합니다. 작업자 노드 1개만큼 줄이지는 마세요.
* 필요한 경우 안전 모드를 종료하는 명령을 실행합니다.

다음 섹션에서는 이러한 옵션을 설명합니다.

#### <a name="stop-all-hive-jobs"></a>모든 Hive 작업 중지

하나의 작업자 노드를 축소하기 전에 모든 Hive 작업을 중지합니다. 워크로드가 예약되면 Hive 작업이 완료된 후에 축소를 실행합니다.

이렇게 하면 tmp 폴더에 있는 스크래치 파일(있는 경우) 수가 최소화됩니다.

#### <a name="manually-clean-up-hives-scratch-files"></a>Hive의 스크래치 파일을 수동으로 정리

Hive가 임시 파일을 남겨 두면 안전 모드를 피하기 위해 축소 전에 해당 파일을 수동으로 정리할 수 있습니다.

1. Hive 서비스를 중지하고 모든 쿼리 및 작업이 완료되었는지 확인합니다.

2. `hdfs://mycluster/tmp/hive/` 디렉터리의 내용을 나열하여 파일이 포함되어 있는지 검토합니다.

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    다음은 파일이 있는 경우의 샘플 출력입니다.

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. 이러한 파일에 대해 Hive가 완료된 것이 확인되면 제거해도 됩니다. Yarn ResourceManager UI 페이지에서 확인하여 Hive에 쿼리가 없는지 알아봅니다.

    HDFS에서 파일을 제거하는 명령줄 예제는 다음과 같습니다.

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>3개의 작업자 노드로 HDInsight 확장

안전 모드를 유지하는 상황이 지속적으로 발생하고 이전 단계를 옵션으로 사용할 수 없으면, 3개의 작업자 노드로 축소하여 문제를 피할 수 있습니다. 이 방법은 1개 노드로 축소하는 것과 비교해서 비용 제약 때문에 최선의 방법이 아닐 수 있습니다. 그러나 작업자 노드가 하나만 있는 경우 HDFS는 클러스터에서 데이터의 복제본 3개를 사용할 수 있다고 보장할 수 없습니다.

#### <a name="run-the-command-to-leave-safe-mode"></a>안전 모드를 종료하는 명령 실행

마지막 옵션은 HDFS가 안전 모드로 전환되는 드문 경우를 검토하고 leave safe mode 명령을 실행하는 것입니다. HDFS가 안전 모드로 전환된 이유가 Hive 파일이 덜 복제되는 문제 때문이라는 것이 확인되면 다음 명령을 실행하여 안전 모드를 종료합니다.

* Linux의HDInsight:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* Windows의 HDInsight:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)
* [클러스터 크기 조정](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
