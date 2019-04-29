---
title: Azure HDInsight를 사용한 Storm 문제 해결
description: Azure HDInsight에서 Apache Storm을 사용할 때 제기되는 일반적인 질문에 답합니다.
keywords: Azure HDInsight, Storm, FAQ, 문제 해결 가이드, 일반적인 문제
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 3866e25cc3c87f569e84b2d5639d25aa9386cc78
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116451"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Azure HDInsight를 사용하여 Apache Storm 문제 해결

[Apache Ambari](https://ambari.apache.org/)에서 [Apache Storm](https://storm.apache.org/) 페이로드 작업에 대한 주요 문제 및 해결 방법을 알아봅니다.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>클러스터에서 Storm UI에 액세스하는 방법
브라우저에서 Storm UI에 액세스할 때는 다음 두 가지 옵션 중 하나를 사용할 수 있습니다.

### <a name="ambari-ui"></a>Ambari UI
1. Ambari 대시보드로 이동합니다.
2. 서비스 목록에서 **Storm**을 선택합니다.
3. **빠른 연결** 메뉴에서 **Storm UI**를 선택합니다.

### <a name="direct-link"></a>직접 링크
다음 URL에서 Storm UI에 액세스할 수 있습니다.

https://\<클러스터 DNS 이름\>/stormui

예제:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>한 토폴로지에서 다른 토폴로지로 Storm 이벤트 허브 Spout 검사점 정보를 전송하는 방법

HDInsight Storm 이벤트 허브 spout .jar 파일을 사용하여 Azure Event Hubs에서 읽은 토폴로지를 개발하는 경우 새 클러스터와 동일한 이름을 갖는 토폴로지를 배포해야 합니다. 그러나 이전 클러스터에서 [Apache ZooKeeper](https://zookeeper.apache.org/)에 커밋된 검사점 데이터를 유지해야 합니다.

### <a name="where-checkpoint-data-is-stored"></a>검사점 데이터 저장 위치
오프셋에 대한 검사점 데이터는 Zookeeper의 이벤트 허브 Spout를 통해 다음 두 루트 경로에 저장됩니다.
- 비트랜잭션 Spout 검사점 데이터는 /eventhubspout에 저장됩니다.
- 트랜잭션 Spout 검사점 데이터는 /transactional에 저장됩니다.

### <a name="how-to-restore"></a>복원하는 방법
ZooKeeper에서 데이터를 내보낸 다음 새 이름을 사용해서 다시 ZooKeeper로 가져오는 데 사용하는 스크립트 및 라이브러리를 가져오려면 [HDInsight Storm 예제](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0)를 참조하세요.

lib 폴더에는 가져오기/내보내기 작업에 대한 구현이 포함된 .jar 파일이 있습니다. bash 폴더에는 이전 클러스터의 ZooKeeper 서버에서 데이터를 내보낸 후 새 클러스터의 ZooKeeper 서버로 다시 가져오는 방법을 보여 주는 예제 스크립트가 있습니다.

데이터를 내보낸 후 가져오려면 ZooKeeper 노드에서 [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) 스크립트를 실행합니다. 올바른 HDP(Hortonworks Data Platform) 버전으로 스크립트를 업데이트합니다. (이러한 스크립트를 HDInsight에서 제네릭으로 만들기 위해 작업 중입니다. 제네릭 스크립트는 사용자가 수정하지 않고 클러스터의 모든 노드에서 실행할 수 있습니다.)

내보내기 명령은 설정한 Azure Blob Storage 또는 Azure Data Lake Storage의 Apache HDFS(Hadoop 분산 파일 시스템) 경로에 메타데이터를 씁니다.

### <a name="examples"></a>예

#### <a name="export-offset-metadata"></a>오프셋 메타데이터 내보내기
1. SSH를 사용하여 검사점 오프셋을 내보내야 하는 클러스터의 ZooKeeper 클러스터로 이동합니다.
2. HDP 버전 문자열을 업데이트한 후 다음 명령을 실행하여 Zookeeper 오프셋 데이터를 /stormmetadta/zkdata HDFS 경로로 내보냅니다.

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>오프셋 메타데이터 가져오기
1. SSH를 사용하여 검사점 오프셋을 내보내야 하는 클러스터의 ZooKeeper 클러스터로 이동합니다.
2. HDP 버전 문자열을 업데이트한 후 다음 명령을 실행하여 ZooKeeper 오프셋 데이터를 /stormmetadta/zkdata HDFS 경로에서 대상 클러스터의 ZooKeeper 서버로 가져옵니다.

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>토폴로지에서 맨 처음부터 또는 사용자가 선택하는 타임스탬프부터 데이터 처리를 시작할 수 있도록 오프셋 메타데이터 삭제
1. SSH를 사용하여 검사점 오프셋을 삭제해야 하는 클러스터의 ZooKeeper 클러스터로 이동합니다.
2. HDP 버전 문자열을 업데이트한 후 다음 명령을 실행하여 현재 클러스터의 모든 ZooKeeper 오프셋 데이터를 삭제합니다.

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>클러스터에서 Storm 이진을 찾는 방법
현재 HDP 스택에 대한 Storm 이진 파일은 /usr/hdp/current/storm-client에 있습니다. 위치는 헤드 노드 및 작업자 노드 둘 다에 대해 동일합니다.
 
/usr/hdp에 특정 HDP 버전에 대한 이진 파일이 여러 개 있을 수 있습니다(예: /usr/hdp/2.5.0.1233/storm). /usr/hdp/current/storm-client 폴더는 클러스터에서 실행되는 최신 버전과 기호화된 링크로 연결되어 있습니다.

자세한 내용은 [SSH를 사용하여 HDInsight 클러스터에 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 및 [Apache Storm](https://storm.apache.org/)을 참조하세요.
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Storm 클러스터의 배포 토폴로지를 확인하는 방법
먼저 HDInsight Storm과 함께 설치된 모든 구성 요소를 식별합니다. Storm 클러스터는 다음 4가지 노드 범주로 구성됩니다.

* 게이트웨이 노드
* 헤드 노드
* ZooKeeper 노드
* 작업자 노드
 
### <a name="gateway-nodes"></a>게이트웨이 노드
게이트웨이 노드는 활성 Ambari 관리 서비스에 공용으로 액세스할 수 있는 게이트웨이 및 역방향 프록시 서비스입니다. 이 노드는 Ambari 리더 투표도 처리합니다.
 
### <a name="head-nodes"></a>헤드 노드
Storm 헤드 노드에서 실행하는 서비스는 다음과 같습니다.
* Nimbus
* Ambari 서버
* Ambari 메트릭 서버
* Ambari 메트릭 수집기
 
### <a name="zookeeper-nodes"></a>ZooKeeper 노드
HDInsight는 3 노드 ZooKeeper 쿼럼을 제공합니다. 쿼럼 크기는 고정되어 있으며 다시 구성할 수 없습니다.
 
클러스터의 Storm 서비스는 ZooKeeper 쿼럼을 자동으로 사용하도록 구성됩니다.
 
### <a name="worker-nodes"></a>작업자 노드
Storm 작업자 노드에서 실행하는 서비스는 다음과 같습니다.
* 감독자
* 토폴로지를 실행하기 위한 작업자 JVM(Java Virtual Machines)
* Ambari 에이전트
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>개발용 Storm 이벤트 허브 spout 이진을 찾는 방법
 
토폴로지에 Storm 이벤트 허브 spout .jar 파일을 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
 
### <a name="java-based-topology"></a>Java 기반 토폴로지
[HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리(Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C# 기반 토폴로지(HDInsight 3.4 이상 Linux Storm 클러스터의 Mono)
[HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리(C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>HDInsight 3.5 이상 Linux Storm 클러스터용 최신 Apache Storm 이벤트 허브 Spout 이진 파일
HDInsight 3.5 이상의 Linux Storm 클러스터에서 작동하는 최신 Storm 이벤트 허브 spout을 사용하는 방법을 알아보려면 mvn-repo [추가 정보 파일](https://github.com/hdinsight/mvn-repo/blob/master/README.md)을 참조하세요.
 
### <a name="source-code-examples"></a>소스 코드 예제
Azure HDInsight 클러스터에서 Java로 작성된 Apache Storm 토폴로지를 사용하여 Azure 이벤트 허브에서 읽고 쓰기 방법에 대해서는 [예제](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)를 참조하세요.
 
## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>클러스터에서 Storm Log4J 2 구성 파일을 찾으려면 어떻게 해야 하나요?
 
Storm 서비스에 대한 [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) 구성 파일을 식별하려면
 
### <a name="on-head-nodes"></a>헤드 노드에서
Nimbus Log4J 구성은 /usr/hdp/\<HDP 버전\>/storm/log4j2/cluster.xml에서 읽을 수 있습니다.
 
### <a name="on-worker-nodes"></a>작업자 노드에서
감독자 Log4J 구성은 /usr/hdp/\<HDP 버전\>/storm/log4j2/cluster.xml에서 읽을 수 있습니다.
 
작업자 Log4J 구성은 /usr/hdp/\<HDP version\>/storm/log4j2/worker.xml에서 읽을 수 있습니다.
 
예제: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>관련 항목
[Azure HDInsight를 사용하여 문제 해결](../../hdinsight/hdinsight-troubleshoot-guide.md)
