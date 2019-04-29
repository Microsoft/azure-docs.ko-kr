---
title: Apache HBase 및 Apache Phoenix 백업 및 복제 설정 - Azure HDInsight
description: HBase 및 Phoenix에 대한 백업 및 복제를 설정합니다.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: e60aef7b1848197f41f96a1b5f5414bb0c8f4a15
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114528"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>HDInsight에서 Apache HBase 및 Apache Phoenix에 대한 백업 및 복제 설정

Apache HBase는 데이터 손실을 방지하기 위한 몇 가지 방법을 지원합니다.

* `hbase` 폴더 복사
* 내보낸 후 가져오기
* 테이블 복사
* 스냅숏
* 복제

> [!NOTE]  
> Apache Phoenix는 메타데이터를 HBase 테이블에 저장하므로, HBase 시스템 카탈로그 테이블을 백업할 때 메타데이터가 백업됩니다.

다음 섹션에서는 이러한 방법 각각에 대한 사용 시나리오를 설명합니다.

## <a name="copy-the-hbase-folder"></a>hbase 폴더 복사

이 방법을 사용하면 테이블 또는 열 패밀리의 하위 집합을 선택할 수 없어도 모든 HBase 데이터를 복사할 수 있습니다. 이후의 방법은 더 많은 제어를 제공합니다.

HDInsight의 HBase는 클러스터를 만들 때 선택한 기본 스토리지인 Azure Storage Blob 또는 Azure Data Lake Storage를 사용합니다. 두 경우 모두 HBase는 데이터 및 메타데이터 파일을 다음 경로 아래에 저장합니다.

    /hbase

* Azure Storage 계정에서 `hbase` 폴더는 Blob 컨테이너의 루트에 있습니다.

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* Azure Data Lake Storage에서 `hbase` 폴더는 클러스터를 프로비전할 때 지정한 루트 경로 아래에 있습니다. 이 루트 경로에는 일반적으로 `clusters` 폴더가 있으며, 여기에는 HDInsight 클러스터 뒤에 이름이 지정된 하위 폴더가 있습니다.

    ```
    /clusters/<clusterName>/hbase
    ```

두 경우 모두 `hbase` 폴더에는 HBase에서 디스크에 플러시한 모든 데이터가 포함되지만, 메모리 내 데이터는 포함되지 않을 수 있습니다. 이 폴더를 HBase 데이터의 정확한 표현으로 사용하려면 먼저 클러스터를 종료해야 합니다.

클러스터를 삭제한 후에는 데이터를 그대로 두거나 새 위치에 복사할 수 있습니다.

* 현재 저장소 위치를 가리키는 새 HDInsight 인스턴스를 만듭니다. 새 인스턴스는 기존의 모든 데이터로 만들어집니다.

* `hbase` 폴더를 다른 Azure Storage Blob 컨테이너 또는 Data Lake Storage 위치에 복사한 다음, 해당 데이터로 새 클러스터를 시작합니다. Azure Storage의 경우 [AzCopy](../../storage/common/storage-use-azcopy.md)를 사용하고, Data Lake Storage의 경우 [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)를 사용합니다.

## <a name="export-then-import"></a>내보낸 후 가져오기

원본 HDInsight 클러스터에서 Export 유틸리티(HBase에 포함)를 사용하여 원본 테이블에서 기본 연결된 저장소로 데이터를 내보냅니다. 그런 다음, 내보낸 폴더를 대상 저장소 위치에 복사하고, 대상 HDInsight 클러스터에서 Import 유틸리티를 실행하면 됩니다.

테이블을 내보내려면 먼저 원본 HDInsight 클러스터의 헤드 노드에 SSH를 연결하고, 다음 `hbase` 명령을 실행합니다.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

테이블을 가져오려면 먼저 대상 HDInsight 클러스터의 헤드 노드에 SSH를 연결하고, 다음 `hbase` 명령을 실행합니다.

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

기본 저장소 또는 연결된 저장소 옵션 중 하나에 대한 전체 내보내기 경로를 지정합니다. 예를 들어 Azure Storage에서 구문은 다음과 같습니다.

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Azure Data Lake Storage Gen2에서 구문은 다음과 같습니다.

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

Azure Data Lake Storage Gen1에서 구문은 다음과 같습니다.

    adl://<accountName>.azuredatalakestore.net:443/<path>

이 방법은 테이블 수준의 세분성을 제공합니다. 포함할 행에 대해 날짜 범위를 지정하여 프로세스를 증분 방식으로 수행할 수 있습니다. 각 날짜는 Unix epoch 이후의 밀리초 단위입니다.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

내보낼 각 행의 버전 수를 지정해야 합니다. 날짜 범위에 속한 모든 버전을 포함하려면 `<numberOfVersions>`을 가능한 최대 행 버전보다 큰 값(예: 100000)으로 설정합니다.

## <a name="copy-tables"></a>테이블 복사

CopyTable 유틸리티는 원본 테이블에서 원본과 동일한 스키마를 사용하여 기존 대상 테이블로 데이터를 행 단위로 복사합니다. 대상 테이블은 동일한 클러스터 또는 다른 HBase 클러스터에 있을 수 있습니다.

클러스터 내에서 CopyTable을 사용하려면 먼저 원본 HDInsight 클러스터의 헤드 노드에 SSH를 연결하고, 다음 `hbase` 명령을 실행합니다.

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

CopyTable을 사용하여 다른 클러스터의 테이블에 복사하려면 `peer` 스위치에 대상 클러스터의 주소를 추가합니다.

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

대상 주소는 다음 세 부분으로 구성됩니다.

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`은 쉼표로 구분된 Apache ZooKeeper 노드 목록입니다. 예를 들어 다음과 같습니다.

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* HDInsight에 대한 `<Port>`는 기본적으로 2181로 설정되고 `<ZnodeParent>`는 `/hbase-unsecure`이므로 전체 `<destinationAddress>`는 다음과 같습니다.

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

HDInsight 클러스터에 대해 이러한 값을 검색하는 방법에 대한 자세한 내용은 이 문서의 [수동으로 Apache ZooKeeper 쿼럼 목록 수집](#manually-collect-the-apache-zookeeper-quorum-list)을 참조하세요.

또한 CopyTable 유틸리티는 복사할 행의 시간 범위를 지정하고 복사할 테이블의 열 패밀리의 하위 집합을 지정하는 매개 변수도 지원합니다. CopyTable에서 지원하는 매개 변수의 전체 목록을 보려면 CopyTable을 매개 변수 없이 실행합니다.

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable은 대상 테이블에 복사될 원본 테이블 내용 전체를 스캔합니다. 이렇게 하면 CopyTable이 실행되는 동안 HBase 클러스터의 성능이 저하될 수 있습니다.

> [!NOTE]  
> 테이블 간의 데이터 복사를 자동화하려면 GitHub의 [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) 리포지토리에 있는 `hdi_copy_table.sh` 스크립트를 참조하세요.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>수동으로 Apache ZooKeeper 쿼럼 목록 수집

앞에서 설명한 대로 두 HDInsight 클러스터가 동일한 가상 네트워크에 있는 경우 내부 호스트 이름 확인이 자동으로 수행됩니다. VPN Gateway로 연결된 별도의 두 개 가상 네트워크에 있는 HDInsight 클러스터에 대해 CopyTable을 사용하려면, 쿼럼에 있는 Zookeeper 노드의 호스트 IP 주소를 제공해야 합니다.

쿼럼 호스트 이름을 얻으려면 다음 curl 명령을 실행합니다.

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

curl 명령은 HBase 구성 정보를 사용하여 JSON 문서를 검색하고, grep 명령은 "hbase.zookeeper.quorum" 항목만 반환합니다. 예를 들어 다음과 같습니다.

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

쿼럼 호스트 이름 값은 콜론 오른쪽에 있는 전체 문자열입니다.

이러한 호스트에 대한 IP 주소를 검색하려면 이전 목록의 각 호스트에 대해 다음 curl 명령을 사용합니다.

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

이 curl 명령에서 `<zookeeperHostFullName>`은 ZooKeeper 호스트의 전체 DNS 이름입니다(예: `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`). 명령의 출력에는 지정된 호스트에 대한 IP 주소가 포함되어 있습니다. 예를 들어 다음과 같습니다.

    100    "ip" : "10.0.0.9",

쿼럼의 모든 ZooKeeper 노드에 대한 IP 주소를 수집한 후 대상 주소를 다시 작성합니다.

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

이 예제에서는 다음과 같습니다.

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>스냅숏

스냅숏을 사용하면 HBase 데이터 저장소에서 데이터에 대한 특정 시점 백업을 수행할 수 있습니다. 스냅숏 작업은 실질적으로 해당 시점에서 저장소에 있는 모든 파일의 이름을 캡처하는 메타데이터 작업이기 때문에 스냅숏은 최소한의 오버헤드를 가지며 수초 내에 완료됩니다. 스냅숏이 만들어지는 시점에서 실제 데이터는 복사되지 않습니다. 스냅숏은 업데이트, 삭제 및 삽입이 모두 새 데이터로 표시되는 HDFS에 저장된 데이터의 변경 불가능 특성을 사용합니다. 스냅숏은 동일한 클러스터에서 복원(*복제*)하거나 다른 클러스터로 내보낼 수 있습니다.

스냅숏을 만들려면 HDInsight HBase 클러스터의 헤드 노드에 SSH를 연결하고 `hbase` 셸을 시작합니다.

    hbase shell

hbase 셸 내에서 테이블 및 이 스냅숏의 이름이 포함된 snapshot 명령을 사용합니다.

    snapshot '<tableName>', '<snapshotName>'

`hbase` 셸 내에서 스냅숏을 이름으로 복원하려면, 먼저 테이블을 사용하지 않도록 설정하고, 스냅숏을 복원한 다음, 테이블을 다시 사용하도록 설정합니다.

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

스냅숏을 새 테이블에 복원하려면 clone_snapshot을 사용합니다.

    clone_snapshot '<snapshotName>', '<newTableName>'

다른 클러스터에서 사용할 수 있도록 스냅숏을 HDFS로 내보내려면, 먼저 앞에서 설명한 대로 스냅숏을 만든 다음, ExportSnapshot 유틸리티를 사용합니다. 이 유틸리티는 SSH 세션 내에서 `hbase` 셸이 아닌 헤드 노드로 실행합니다.

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

`<hdfsHBaseLocation>`은 원본 클러스터에 액세스할 수 있는 저장소 위치 중 하나일 수 있으며, 대상 클러스터에서 사용하는 hbase 폴더를 가리켜야 합니다. 예를 들어 원본 클러스터에 보조 Azure Storage 계정이 연결되어 있고 해당 계정이 대상 클러스터의 기본 스토리지에서 사용하는 컨테이너에 대한 액세스를 제공하는 경우 다음 명령을 사용할 수 있습니다.

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

스냅숏을 내보낸 후 SSH를 대상 클러스터의 헤드 노드에 연결하고, 앞에서 설명한 대로 restore_snapshot 명령을 사용하여 스냅숏을 복원합니다.

스냅숏은 `snapshot` 명령을 실행할 때 테이블의 전체 백업을 제공합니다. 스냅숏은 시간 범위에 따라 증분 스냅숏을 수행하거나 스냅숏에 포함할 열 패밀리의 하위 집합을 지정하는 기능을 제공하지 않습니다.

## <a name="replication"></a>복제

HBase 복제는 원본 클러스터에서 최소한의 오버헤드로 비동기 메커니즘을 사용하여 원본 클러스터에서 대상 클러스터로 트랜잭션을 자동으로 푸시합니다. HDInsight에서 다음과 같은 클러스터 간에 복제를 설정할 수 있습니다.

* 원본 및 대상 클러스터가 동일한 가상 네트워크에 있습니다.
* 원본 및 대상 클러스터가 VPN 게이트웨이에서 연결된 서로 다른 가상 네트워크에 있지만, 모두 동일한 지리적 위치에 있습니다.
* 원본 및 대상 클러스터가 VPN 게이트웨이에서 연결된 서로 다른 가상 네트워크에 있고, 각 클러스터가 서로 다른 지리적 위치에 있습니다.

복제를 설정하는 일반적인 단계는 다음과 같습니다.

1. 원본 클러스터에서 테이블을 만들고 데이터를 채웁니다.
2. 대상 클러스터에서 원본 테이블의 스키마를 사용하여 빈 대상 테이블을 만듭니다.
3. 대상 클러스터를 원본 클러스터의 피어로 등록합니다.
4. 원하는 원본 테이블에서 복제를 사용하도록 설정합니다.
5. 원본 테이블에서 대상 테이블로 기존 데이터를 복사합니다.
6. 복제는 원본 테이블의 새 데이터 수정 내용을 대상 테이블에 자동으로 복사합니다.

HDInsight에서 복제를 사용하도록 설정하려면 실행 중인 원본 HDInsight 클러스터에 스크립트 동작을 적용합니다. 클러스터에서 복제를 사용하도록 설정하거나 Azure 리소스 관리 템플릿을 사용하여 가상 네트워크에서 만든 샘플 클러스터에서 복제를 실험하는 연습은 [Apache HBase 복제 구성](apache-hbase-replication.md)을 참조하세요. 이 문서에는 Phoenix 메타데이터의 복제를 사용하도록 설정하기 위한 지침도 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

* [Apache HBase 복제 구성](apache-hbase-replication.md)
