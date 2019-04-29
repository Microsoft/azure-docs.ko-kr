---
title: Apache Storm이란? - Azure HDInsight
description: Apache Storm을 사용하면 데이터 스트림을 실시간으로 처리할 수 있습니다. Azure HDInsight를 사용하면 Azure 클라우드에 Storm 클러스터를 쉽게 만들 수 있습니다. Visual Studio를 사용하면 C#을 사용하여 Storm 솔루션을 만든 다음 HDInsight Storm 클러스터에 배포할 수 있습니다.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Apache Storm 사용 사례, Storm 클러스터, Apache Storm이란?
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 5688056bc0d371c780bce02254769792e9a95696
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116535"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Azure HDInsight의 Apache Storm이란?

[Apache Storm](https://storm.apache.org/)은 내결함성이 있는 분산형 오픈 소스 계산 시스템입니다. Storm을 사용하여 [Apache Hadoop](https://hadoop.apache.org/)에서 실시간으로 데이터 스트림을 처리할 수 있습니다. 또한 Storm 솔루션은 처음에 정상적으로 처리되지 않은 데이터를 재생하는 기능을 통해 데이터 처리를 보장할 수 있습니다.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="why-use-apache-storm-on-hdinsight"></a>HDInsight의 Apache Storm을 사용해야 하는 이유

HDInsight의 Storm은 다음과 같은 기능을 제공합니다.

* __Storm 작동 시간의 99% SLA(서비스 수준 약정)__: 자세한 내용은 [HDInsight에 대한 SLA 정보](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) 문서를 참조하세요.

* 생성 중 또는 생성 후에 Storm 클러스터에 대해 스크립트를 실행하여 손쉬운 사용자 지정을 지원합니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](../hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

* **여러 언어로 솔루션 만들기**: Java, C# 및 Python과 같은 사용자가 선택한 언어로 Storm 구성 요소를 작성할 수 있습니다.

    * C# 토폴로지의 개발, 관리 및 모니터링을 위해 HDInsight와 Visual Studio를 통합합니다. 자세한 내용은 [HDInsight Tools for Visual Studio를 사용하여 C# Storm 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

    * Trident Java 인터페이스를 지원합니다. 정확히 한 번의 메시지 처리, 트랜잭션 데이터 저장소 지속성 및 일반 Stream Analytics 작업 집합을 지원하는 Storm 토폴로지를 만들 수 있습니다.

* **동적 크기 조정**: 실행 중인 Storm 토폴로지에 영향을 주지 않고 작업자 노드를 추가하거나 제거할 수 있습니다.

    > [!NOTE]  
    > 크기 조정 작업을 통해 추가된 새 노드를 활용하기 위해 실행 중인 토폴로지를 비활성화하고 다시 활성화해야 합니다.

* **여러 Azure 서비스를 사용하여 스트리밍 파이프라인 만들기**: HDInsight의 Storm은 Event Hubs, SQL Database, Azure Storage 및 Azure Data Lake Storage 등 다른 Azure 서비스와 통합합니다.

    Azure 서비스와 통합되는 예제 솔루션은 [HDInsight의 Apache Storm으로 Event Hubs의 이벤트 처리](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)를 참조하세요.

실시간 분석 솔루션에 Apache Storm을 사용하는 회사 목록은 [Apache Storm을 사용하는 회사](https://storm.apache.org/documentation/Powered-By.html)(영문)를 참조하세요.

Storm 사용 시작을 참조 하세요 [HDInsight의 Apache Storm을 사용 하 여 시작][gettingstarted]합니다.

## <a name="how-does-apache-storm-work"></a>Apache Storm의 작동 방식

Storm에서는 친숙한 [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 작업 대신 토폴로지를 실행합니다. Storm 토폴로지는 DAG(방향성 비순환 그래프)에서 정렬된 여러 구성 요소로 구성됩니다. 그래프의 구성 요소 간에 데이터가 흐릅니다. 각 구성 요소는 하나 이상의 데이터 스트림을 사용하며, 선택적으로 하나 이상의 스트림을 내보낼 수 있습니다. 다음 다이어그램은 기본 단어 개수 토폴로지에 있는 구성 요소 간에 데이터가 흐르는 방식을 보여 줍니다.

![Storm 토폴로지에서 구성 요소 정렬 방법의 예제](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Spout 구성 요소는 데이터를 토폴로지로 가져옵니다. 하나 이상의 스트림을 토폴로지에 내보냅니다.

* Bolt 구성 요소는 Spout 또는 다른 Bolt에서 내보낸 스트림을 사용합니다. Bolt는 필요에 따라 스트림을 토폴로지로 내보낼 수 있습니다. 또한 Bolt는 HDFS, Kafka 또는 HBase와 같은 외부 서비스 또는 저장소에 데이터를 쓰는 역할을 수행합니다.

## <a name="reliability"></a>안정성

Apache Storm은 데이터 분석이 수백 개의 노드에 분산되어 있는 경우에도 들어오는 각 메시지를 항상 완전히 처리하도록 합니다.

Nimbus 노드는 Apache Hadoop JobTracker와 유사한 기능을 제공하며 [Apache ZooKeeper](https://zookeeper.apache.org/)를 통해 클러스터의 다른 노드에 작업을 할당합니다. Zookeeper 노드는 클러스터에 대한 조정을 제공하며, Nimbus와 작업자 노드의 감독자 프로세스 간의 통신을 용이하게 합니다. 하나의 처리 노드가 작동이 중지되면 Nimbus 노드에 알림이 제공되고 이 노드에서 작업 및 관련 데이터를 다른 노드에 할당합니다.

Apache Storm 클러스터의 기본 구성에는 Nimbus 노드 하나만 있습니다. HDInsight의 Storm은 두 개의 Nimbus 노드를 제공합니다. 주 노드에 장애가 발생하면 주 노드가 복구되는 동안 Storm 클러스터에서 보조 노드로 전환합니다. 다음 다이어그램은 HDInsight에서 Storm에 대한 작업 흐름 구성을 보여 줍니다.

![nimbus, zookeeper 및 감독자 다이어그램](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>만들기 편의성

HDInsight에서 새 Storm 클러스터를 몇 분 내에 만들 수 있습니다. Storm 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Storm 시작](apache-storm-tutorial-get-started-linux.md)을 참조하세요.

## <a name="ease-of-use"></a>사용 편의성

* __SSH(보안 셸) 연결__: SSH를 사용하여 인터넷을 통해 Storm 클러스터의 헤드 노드에 액세스할 수 있습니다. SSH를 사용하여 클러스터에서 명령을 직접 실행할 수 있습니다.

  자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* __웹 연결__: 모든 HDInsight 클러스터는 Ambari 웹 UI를 제공합니다. Ambari 웹 UI를 사용하여 클러스터에서 서비스를 쉽게 모니터링, 구성 및 관리할 수 있습니다. 또한 Storm 클러스터는 Storm UI도 제공합니다. Storm UI를 사용하여 브라우저에서 실행 중인 Storm 토폴로지를 모니터링 및 관리할 수 있습니다.

  자세한 내용은 [Apache Ambari 웹 UI를 사용하여 HDInsight 관리](../hdinsight-hadoop-manage-ambari.md) 및 [Apache Storm UI를 사용하여 모니터링 및 관리](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) 문서를 참조하세요.

* __Azure PowerShell 및 Azure 클래식 CLI__: PowerShell 및 클래식 CLI는 모두 HDInsight 및 다른 Azure 서비스를 사용하는 클라이언트 시스템에서 사용할 수 있는 명령줄 유틸리티를 제공합니다.

* __Visual Studio 통합__: Azure Data Lake Tools for Visual Studio에는 만드는 프로젝트 템플릿이 포함 되어 C# 는 SCP.NET framework를 사용 하 여 토폴로지를 Storm. 또한 Data Lake 도구는 HDInsight의 Storm을 사용하여 솔루션을 배포, 모니터링 및 관리하는 도구를 제공합니다.

  자세한 내용은 [HDInsight Tools for Visual Studio를 사용하여 C# Storm 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

## <a name="integration-with-other-azure-services"></a>다른 Azure 서비스와 통합

* __Azure Data Lake Storage__: Storm 클러스터에서 Data Lake Storage를 사용하는 예제는 [HDInsight의 Apache Storm에서 Azure Data Lake Storage 사용](apache-storm-write-data-lake-store.md)을 참조하세요.

* __Event Hubs__: Storm 클러스터에서 Event Hubs를 사용하는 예제는 다음 예제를 참조하세요.

    * [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리(Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리(C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ 및 __HBase__: 템플릿 예제는 Data Lake Tools for Visual Studio에 포함되어 있습니다. 자세한 내용은 [HDInsight의 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

## <a name="support"></a>지원

HDInsight의 Storm에는 완전한 엔터프라이즈 수준의 연속 지원이 제공됩니다. HDInsight의 Storm에는 99.9%의 SLA도 있습니다. 즉 Microsoft는 Storm 클러스터에서 적어도 99.9%의 외부 연결을 보장합니다.

자세한 내용은 [Azure 지원](https://azure.microsoft.com/support/options/)을 참조하세요.

## <a name="apache-storm-use-cases"></a>Apache Storm 사용 사례

다음은 HDInsight의 Storm을 사용할 수 있는 몇 가지 일반적인 시나리오입니다.

* IoT(사물 인터넷)
* 부정 행위 감지
* 소셜 분석
* 추출, 변환 및 로드(ETL)
* 네트워크 모니터링
* 검색
* 모바일 고객 관리

실제 시나리오에 대한 자세한 내용은 [기업에서 Apache Storm을 사용하는 방식](https://storm.apache.org/documentation/Powered-By.html) 문서를 참조하세요.

## <a name="development"></a>개발

Data Lake Tools for Visual Studio를 사용하면 .NET 개발자는 C#으로 토폴로지를 디자인하고 구현할 수 있습니다. 또한 Java 및 C# 구성 요소를 사용하는 하이브리드 토폴로지를 만들 수 있습니다.

자세한 내용은 [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

선택한 IDE를 사용하여 Java 솔루션을 개발할 수도 있습니다. 자세한 내용은 [HDInsight의 Apache Storm에 대한 Java 토폴로지 개발](apache-storm-develop-java-topology.md)을 참조하세요.

Python은 Storm 구성 요소를 개발하는 데에도 사용합니다. 자세한 내용은 [HDInsight에서 Python을 사용하여 Apache Storm 토폴로지 개발](apache-storm-develop-python-topology.md)을 참조하세요.

## <a name="common-development-patterns"></a>일반적인 개발 패턴

### <a name="guaranteed-message-processing"></a>메시지 처리 보장

Apache Storm은 다양한 수준의 보장된 메시지 처리를 제공할 수 있습니다. 예를 들어 기본적인 Storm 애플리케이션은 최소한 한 번 처리를 보장할 수 있고, [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html)는 정확히 한 번 처리를 보장할 수 있습니다.

자세한 내용은 apache.org에서 [데이터 처리 보장](https://storm.apache.org/about/guarantees-data-processing.html) 을 참조하세요.

### <a name="ibasicbolt"></a>IBasicBolt

입력된 튜플을 읽고 0 개 이상의 튜플을 내보내는 다음 실행 메서드 끝에 즉시 입력된 튜플을 승인 패턴이 일반적입니다. Storm은 이 패턴을 자동화하는 [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) 인터페이스를 제공합니다.

### <a name="joins"></a>조인

데이터 스트림이 조인되는 방식은 애플리케이션마다 다릅니다. 예를 들어 여러 스트림의 각 튜플을 새 스트림 하나에 조인할 수도 있고 특정 창에 대한 튜플 배치만 조인할 수도 있습니다. 어떤 방법을 사용하든 [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-)을 통해 조인을 수행할 수 있습니다. 필드 그룹화는 튜플을 Bolt로 라우팅하는 방식을 정의합니다.

다음 Java 예제에서는 fieldsGrouping을 사용하여 구성 요소 "1", "2", "3"에서 생성된 튜플을 MyJoiner Bolt로 라우팅합니다.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Batch

Apache Storm은 "틱 튜플(tick tuple)"이라고 하는 내부 타이밍 메커니즘을 제공합니다. 토폴로지에서 틱 튜플을 내보내는 빈도를 설정할 수 있습니다.

C# 구성 요소에서 틱 튜플을 사용하는 예제는 [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java)를 참조하세요.

### <a name="caches"></a>캐시

메모리 내 캐싱은 자주 사용되는 자산을 메모리에 저장하므로 처리 속도를 높이기 위한 메커니즘으로 사용되는 경우가 많습니다. 토폴로지는 여러 노드 및 각 노드 내의 여러 프로세스로 분산되므로 [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-)을 사용하는 것이 좋습니다. `fieldsGrouping`을 사용하여 캐시 조회에 사용되는 필드가 포함된 튜플은 항상 같은 프로세스로 라우팅될 수 있습니다. 이 그룹화 기능으로 프로세스 간 캐시 항목 중복을 방지할 수 있습니다.

### <a name="stream-top-n"></a>스트림 “상위 N”

토폴로지가 상위 N 값 계산에 따라 다른 경우 상위 N 값을 병렬로 계산합니다. 그런 다음 해당 계산의 출력을 전역 값으로 병합합니다. 이 작업은 병렬 처리를 위해 필드별로 라우팅하도록 [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-)을 사용하여 수행할 수 있습니다. 그런 다음 상위 N 값을 전역적으로 결정하는 Bolt로 라우팅할 수 있습니다.

상위 N 값을 계산하는 예제는 [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) 예제를 참조하세요.

## <a name="logging"></a>로깅

Storm은 [Apache Log4j 2](https://logging.apache.org/log4j/2.x/)를 사용하여 정보를 기록합니다. 기본적으로 많은 양의 데이터를 기록하면 정보를 정렬하기가 어려울 수 있습니다. Storm 토폴로지의 일부로 로깅 구성 파일을 포함하여 로깅 동작을 제어할 수 있습니다.

로깅을 구성하는 방법을 보여 주는 예제 토폴로지는 HDInsight에서 Storm에 대한 [Java 기반 WordCount](apache-storm-develop-java-topology.md) 예제를 참조하세요.

## <a name="next-steps"></a>다음 단계

HDInsight의 Apache Storm을 사용한 실시간 분석 솔루션에 대해 자세히 알아봅니다.

* [HDInsight의 Apache Storm 시작][gettingstarted]
* [HDInsight의 Apache Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: https://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: apache-storm-tutorial-get-started-linux.md
