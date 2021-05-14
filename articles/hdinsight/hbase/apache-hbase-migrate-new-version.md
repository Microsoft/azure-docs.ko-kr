---
title: HBase 클러스터를 최신 버전으로 마이그레이션 - Azure HDInsight
description: Azure HDInsight에서 Apache HBase 클러스터를 최신 버전으로 마이그레이션하는 방법입니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 43b46d19503856f5eae38272299f73d9c80055b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868888"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Apache HBase 클러스터를 최신 버전으로 마이그레이션

이 문서에서는 Azure HDInsight의 Apache HBase 클러스터를 최신 버전으로 업데이트하는 데 필요한 단계를 설명합니다.

업그레이드하는 동안 가동 중지 시간은 몇 분 정도로 최소이어야 합니다. 이 가동 중지 시간은 모든 메모리 내 데이터를 플러시하는 단계로 인해 발생하며, 그런 후에 새 클러스터에서 서비스를 구성하고 다시 시작합니다. 결과는 노드 수, 데이터 양 및 기타 변수에 따라 달라집니다.

## <a name="review-apache-hbase-compatibility"></a>Apache HBase 호환성 검토

Apache HBase를 업그레이드하기 전에 원본 및 대상 클러스터의 HBase 버전이 호환되는지 확인합니다. 자세한 내용은 [HDInsight에서 사용할 수 있는 Apache Hadoop 구성 요소 및 버전](../hdinsight-component-versioning.md)을 참조하세요.

> [!NOTE]  
> [HBase 도서](https://hbase.apache.org/book.html#upgrading)에서 버전 호환성 매트릭스를 검토하는 것이 좋습니다. 모든 주요 비호환성은 HBase 버전 릴리스 정보에 설명되어 있습니다.

예제 버전 호환성 매트릭스는 다음과 같습니다. Y는 호환성을 나타내고 N은 잠재적인 비호환성을 나타냅니다.

| 호환성 유형 | 주 버전| 부 버전 | 패치 |
| --- | --- | --- | --- |
| 클라이언트-서버 통신 호환성 | N | Y | Y |
| 서버 간 호환성 | N | Y | Y |
| 파일 형식 호환성 | N | Y | Y |
| 클라이언트 API 호환성 | N | Y | Y |
| 클라이언트 이진 호환성 | N | N | Y |
| **서버 쪽 제한 API 호환성** |  |  |  |
| Stable | N | Y | Y |
| 진화 | N | N | Y |
| 불안정 | N | N | N |
| 종속성 호환성 | N | Y | Y |
| 운영 호환성 | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>동일한 Apache HBase 주 버전으로 업그레이드

Azure HDInsight에서 Apache HBase 클러스터를 업그레이드하려면 다음 단계를 완료합니다.

1. HBase 호환성 매트릭스 및 릴리스 정보에서 표시한 대로 애플리케이션이 새 버전과 호환되는지 확인합니다. HDInsight 및 HBase의 대상 버전을 실행하는 클러스터에서 애플리케이션을 테스트합니다.

1. 동일한 스토리지 계정을 사용하지만 다른 컨테이너 이름으로 [새 대상 HDInsight 클러스터를 설정합니다](../hdinsight-hadoop-provision-linux-clusters.md).

   :::image type="content" source="./media/apache-hbase-migrate-new-version/same-storage-different-container.png" alt-text="동일한 스토리지 계정을 사용하지만 다른 컨테이너 만들기" border="true":::

1. 업그레이드하는 클러스터인 원본 HBase 클러스터를 플러시합니다. HBase는 들어오는 데이터를 _memstore_ 라는 메모리 내 저장소에 씁니다. memstore가 특정 크기에 도달한 후 HBase는 장기 저장을 위해 클러스터의 스토리지 계정에 있는 디스크로 플러시됩니다. 이전 클러스터를 삭제하는 경우 memstore가 재활용되어 데이터가 잠재적으로 손실될 수 있습니다. 각 테이블에 대한 memstore를 디스크로 수동으로 플러시하려면 다음 스크립트를 실행합니다. 이 스크립트의 최신 버전은 Azure의 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)에 있습니다.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. 이전 HBase 클러스터에 대한 수집을 중지합니다.

1. memstore의 최근 데이터가 플러시되도록 하려면 이전 스크립트를 다시 실행합니다.

1. 이전 클러스터(`https://OLDCLUSTERNAME.azurehdidnsight.net`)의 [Apache Ambari](https://ambari.apache.org/)에 로그인하고 HBase 서비스를 중지합니다. 서비스를 중지하려는 것인지 묻는 메시지가 표시되면 상자를 선택하여 HBase에 대한 유지 관리 모드를 켭니다. Ambari 연결 및 사용에 대한 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari.md)를 참조하세요.

    :::image type="content" source="./media/apache-hbase-migrate-new-version/stop-hbase-services1.png" alt-text="Ambari에서 서비스 > HBase > 서비스 작업에서 중지 클릭" border="true":::

    :::image type="content" source="./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png" alt-text="HBase에 대한 [유지 관리 모드 켜기] 확인란을 선택한 다음, 확인함" border="true":::

1. 향상된 쓰기 기능이 있는 HBase 클러스터를 사용하지 않는 경우 이 단계를 건너뜁니다. 향상된 쓰기 기능이 있는 HBase 클러스터에만 필요합니다.

   원래 클러스터의 모든 zookeeper 노드 또는 작업자 노드에 있는 ssh 세션에서 아래 명령을 실행하여 HDFS에서 WAL 디렉터리를 백업합니다.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup**
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup**
   ```
    
1. 새 HDInsight 클러스터에서 Ambari에 로그인합니다. 원래 클러스터에서 사용한 컨테이너 이름을 가리키도록 `fs.defaultFS` HDFS 설정을 변경합니다. 이 설정은 **HDFS > 구성> 고급 > 고급 코어 사이트** 아래에 있습니다.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="Ambari에서 서비스 > HDFS > 구성 > 고급 클릭" border="true":::

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name.png" alt-text="Ambari에서 컨테이너 이름을 변경함" border="true":::

1. 향상된 쓰기 기능이 있는 HBase 클러스터를 사용하지 않는 경우 이 단계를 건너뜁니다. 향상된 쓰기 기능이 있는 HBase 클러스터에만 필요합니다.

   원래 클러스터의 컨테이너를 가리키도록 `hbase.rootdir` 경로를 변경합니다.

   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Ambari에서 HBase rootdir의 컨테이너 이름 변경" border="true":::
    
1. 향상된 쓰기 기능이 있는 HBase 클러스터를 사용하지 않는 경우 이 단계를 건너뜁니다. 향상된 쓰기 기능이 있는 HBase 클러스터에만 필요하며 원래 클러스터가 향상된 쓰기 기능을 가진 HBase 클러스터인 경우에만 필요합니다.

   이 새 클러스터에 대한 zookeeper 및 WAL FS 데이터를 정리합니다. 모든 zookeeper 노드 또는 작업자 노드에서 다음 명령을 실행합니다.

   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit

   hdfs dfs -rm -r hdfs://mycluster/hbasewal**
   ```

1. 향상된 쓰기 기능이 있는 HBase 클러스터를 사용하지 않는 경우 이 단계를 건너뜁니다. 향상된 쓰기 기능이 있는 HBase 클러스터에만 필요합니다.
   
   새 클러스터의 모든 zookeeper 노드 또는 작업자 노드에 있는 ssh 세션에서 새 클러스터의 HDFS로 WAL 디렉터리를 복원합니다.
   
   ```bash
   hdfs dfs -cp /hbase-wal-backup/hbasewal hdfs://mycluster/**
   ```
   
1. HDInsight 3.6을 4.0으로 업그레이드하는 경우 아래 단계를 수행합니다. 그렇지 않으면 13단계로 건너뜁니다.

    1. **서비스** > **필요한 모든 서비스 다시 시작** 을 선택하여 Ambari에서 필요한 모든 서비스를 다시 시작합니다.
    1. HBase 서비스를 중지합니다.
    1. Zookeeper 노드에 대해 SSH를 수행하고 [zkCli](https://github.com/go-zkcli/zkcli) 명령 `rmr /hbase-unsecure`을 실행하여 Zookeeper에서 HBase 루트 znode를 제거합니다.
    1. HBase를 다시 시작합니다.

1. 4\.0 이외의 다른 HDInsight 버전으로 업그레이드하는 경우 다음 단계를 수행합니다.
    1. 변경 내용을 저장합니다.
    1. Ambari에서 표시한 대로 필요한 모든 서비스를 다시 시작합니다.

1. 애플리케이션이 새 클러스터를 가리키도록 합니다.

    > [!NOTE]  
    > 업그레이드할 때 애플리케이션에 대한 고정 DNS가 변경됩니다. 이 DNS를 하드 코딩하는 대신, 도메인 이름의 DNS 설정에서 클러스터 이름을 가리키는 CNAME을 구성할 수 있습니다. 또 다른 옵션은 다시 배포하지 않고 업데이트할 수 있는 애플리케이션에 대한 구성 파일을 사용하는 것입니다.

1. 수집을 시작하여 모든 항목이 예상대로 작동하는지 확인합니다.

1. 새 클러스터가 만족스러운 경우 원래 클러스터를 삭제합니다.

## <a name="next-steps"></a>다음 단계

[Apache HBase](https://hbase.apache.org/) 및 HDInsight 클러스터 업그레이드에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight 클러스터를 최신 버전으로 업그레이드](../hdinsight-upgrade-cluster.md)
* [Apache Ambari Web UI를 사용하여 Azure HDInsight 모니터링 및 관리](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop 구성 요소 및 버전](../hdinsight-component-versioning.md)
* [Apache HBase 최적화](../optimize-hbase-ambari.md)
