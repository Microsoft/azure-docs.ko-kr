---
title: HDInsight에서 Hadoop 서비스에서 사용하는 포트 - Azure
description: HDInsight에서 실행 중인 Hadoop 서비스에 사용된 포트 목록입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 2d0b8aba95787f179733dd596e783f097cba4299
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761259"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>HDInsight의 Apache Hadoop 서비스에서 사용하는 포트

이 문서에서는 Linux 기반 HDInsight 클러스터에서 실행되는 Apache Hadoop 서비스에 사용되는 포트 목록을 제공합니다. 또한 SSH를 사용하여 클러스터에 연결하는 데 사용된 포트에 대한 정보도 제공합니다.

## <a name="public-ports-vs-non-public-ports"></a>공용 포트 및 비-공용 포트

Linux 기반 HDInsight 클러스터는 인터넷에서 세 포트(22, 23, 443)만 개방합니다. 이러한 포트는 SSH 및 보안 HTTPS 프로토콜을 통해 노출된 서비스를 사용하여 클러스터에 안전하게 액세스하는 데 사용됩니다.

내부적으로 HDInsight는 Azure Virtual Network에서 실행되는 여러 Azure Virtual Machines(클러스터 내의 노드)에 의해 구현됩니다. 가상 네트워크 내에서 인터넷을 통해 노출되지 않은 포트를 액세스할 수 있습니다. 예를 들어 SSH를 사용하여 헤드 노드 중 하나에 연결하는 경우 헤드 노드부터 시작하여 클러스터 노드에서 실행 중인 서비스에 직접 액세스할 수 있습니다.

> [!IMPORTANT]  
> HDInsight의 구성 옵션으로 Azure Virtual Network를 지정하지 않을 경우 하나는 자동으로 생성됩니다. 단, 다른 컴퓨터(예: 다른 Azure Virtual Machines 또는 클라이언트 개발 컴퓨터)는 이 가상 네트워크에 연결할 수 없습니다.


추가 컴퓨터를 가상 네트워크에 조인하려면 먼저 가상 네트워크를 만든 후 HDInsight 클러스터를 만들 때 이를 지정해야 합니다. 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 기능 확장](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>공용 포트

HDInsight 클러스터의 모든 노드는 Azure Virtual Network에 있으며 인터넷에서 직접 액세스할 수 없습니다. 공용 게이트웨이는 모든 HDInsight 클러스터 유형에 대해 일반적인 다음 포트에 대한 인터넷 액세스를 제공합니다.

| 서비스 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- |
| sshd |22 |SSH |기본 헤드 노드에서 sshd에 클라이언트를 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. |
| sshd |22 |SSH |에지 노드에서 클라이언트를 sshd에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. |
| sshd |23 |SSH |보조 헤드 노드에서 sshd에 클라이언트를 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. |
| Ambari |443 |HTTPS |Ambari 웹 UI. [Apache Ambari 웹 UI를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요. |
| Ambari |443 |HTTPS |Ambari REST API. [Apache Ambari REST API를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari-rest-api.md)를 참조하세요. |
| WebHCat |443 |HTTPS |HCatalog REST API. [Curl에서 Apache Hive 사용](hadoop/apache-hadoop-use-pig-curl.md), [Curl에서 Apache Pig 사용](hadoop/apache-hadoop-use-pig-curl.md), [Curl에서 MapReduce 사용](hadoop/apache-hadoop-use-mapreduce-curl.md)을 참조하세요. |
| HiveServer2 |443 |ODBC |ODBC를 사용하여 Hive에 연결합니다. [Microsoft ODBC 드라이버로 HDInsight에 Excel 연결](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)을 참조하세요. |
| HiveServer2 |443 |JDBC |JDBC를 사용하여 Apache Hive에 연결합니다. [Hive JDBC 드라이버를 사용하여 HDInsight에서 Apache Hive에 연결](hadoop/apache-hadoop-connect-hive-jdbc-driver.md)을 참조하세요. |

다음은 특정 클러스터 유형에 대해 사용할 수 있습니다.

| 서비스 | 포트 | 프로토콜 | 클러스터 유형 | 설명 |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |HBase REST API. [Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)을 참조하세요. |
| Livy |443 |HTTPS |Spark |Spark REST API. [Apache Livy를 사용하여 원격으로 Apache Spark 작업 제출](spark/apache-spark-livy-rest-interface.md)을 참조하세요. |
| Spark Thrift 서버 |443 |HTTPS |Spark |Hive 쿼리를 전송하는 데 사용되는 Spark Thrift 서버입니다. [HDInsight의 Apache Hive에 Beeline 사용](hadoop/apache-hadoop-use-hive-beeline.md)을 참조하세요. |
| Storm |443 |HTTPS |Storm |Storm 웹 UI. [HDInsight에서 Apache Storm 토폴로지 배포 및 관리](storm/apache-storm-deploy-monitor-topology-linux.md)를 참조하세요. |

### <a name="authentication"></a>Authentication

인터넷에서 공개적으로 노출되는 모든 서비스를 인증해야 합니다.

| 포트 | 자격 증명 |
| --- | --- |
| 22 또는 23 |클러스터를 만드는 동안 지정된 SSH 사용자 자격 증명 |
| 443 |클러스터를 만드는 동안 설정된 로그인 이름(기본값: admin) 및 암호 |

## <a name="non-public-ports"></a>비-공용 포트

> [!NOTE]  
> 일부 서비스는 특정 클러스터 형식에서만 사용할 수 있습니다. 예를 들어 HBase는 HBase 클러스터 형식에서만 사용할 수 있습니다.

> [!IMPORTANT]  
> 일부 서비스는 한 번에 하나의 헤드 노드에서만 실행됩니다. 기본 헤드 노드에서 서비스에 연결하려고 하는데 오류가 수신되면 보조 헤드 노드에 사용하여 다시 시도하세요.

### <a name="ambari"></a>Ambari

| 서비스 | 노드 | 포트 | URL 경로 | 프로토콜 | 
| --- | --- | --- | --- | --- |
| Ambari 웹 UI | 헤드 노드 | 8080 | / | HTTP |
| Ambari REST API | 헤드 노드 | 8080 | /api/v1 | HTTP |

예제:

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| NameNode 웹 UI |헤드 노드 |30070 |HTTPS |상태를 보기 위한 웹 UI |
| NameNode 메타데이터 서비스 |헤드 노드 |8020 |IPC |파일 시스템 메타데이터 |
| DataNode |모든 작업자 노드 |30075 |HTTPS |상태, 로그 등을 보기 위한 웹 UI |
| DataNode |모든 작업자 노드 |30010 |&nbsp; |데이터 전송 |
| DataNode |모든 작업자 노드 |30020 |IPC |메타데이터 작업 |
| 보조 NameNode |헤드 노드 |50090 |HTTP |NameNode 메타데이터에 대한 검사점 |

### <a name="yarn-ports"></a>YARN 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| Resource Manager 웹 UI |헤드 노드 |8088 |HTTP |Resource Manager용 웹 UI |
| Resource Manager 웹 UI |헤드 노드 |8090 |HTTPS |Resource Manager용 웹 UI |
| Resource Manager 관리 인터페이스 |헤드 노드 |8141 |IPC |애플리케이션 제출용(Hive, Hive server, Pig 등) |
| Resource Manager 스케줄러 |헤드 노드 |8030 |HTTP |관리 인터페이스 |
| Resource Manager 애플리케이션 인터페이스 |헤드 노드 |8050 |HTTP |애플리케이션 관리자 인터페이스의 주소 |
| NodeManager |모든 작업자 노드 |30050 |&nbsp; |컨테이너 관리자의 주소 |
| NodeManager 웹 UI |모든 작업자 노드 |30060 |HTTP |Resource Manager 인터페이스 |
| 타임라인 주소 |헤드 노드 |10200 |RPC |타임라인 서비스 RPC 서비스. |
| 타임라인 웹 UI |헤드 노드 |8181 |HTTP |타임라인 서비스 웹 UI |

### <a name="hive-ports"></a>Hive 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| HiveServer2 |헤드 노드 |10001 |Thrift |Hive에 연결하기 위한 서비스(Thrift/JDBC) |
| Hive Metastore |헤드 노드 |9083 |Thrift |Hive 메타데이터에 연결하기 위한 서비스(Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| WebHCat 서버 |헤드 노드 |30111 |HTTP |HCatalog 및 기타 Hadoop 서비스 맨 위의 웹 API |

### <a name="mapreduce-ports"></a>MapReduce 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| JobHistory |헤드 노드 |19888 |HTTP |MapReduce JobHistory 웹 UI |
| JobHistory |헤드 노드 |10020 |&nbsp; |MapReduce JobHistory 서버 |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |중간 맵 출력을 요청 리듀서에 전송 |

### <a name="oozie"></a>Oozie

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| Oozie 서버 |헤드 노드 |11000 |HTTP |Oozie 서비스에 대한 URL |
| Oozie 서버 |헤드 노드 |11001 |HTTP |Oozie 관리자에 대한 포트 |

### <a name="ambari-metrics"></a>Ambari 메트릭

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| 타임라인(애플리케이션 기록) |헤드 노드 |6188 |HTTP |타임라인 서비스 웹 UI |
| 타임라인(애플리케이션 기록) |헤드 노드 |30200 |RPC |타임라인 서비스 웹 UI |

### <a name="hbase-ports"></a>HBase 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| HMaster |헤드 노드 |16000 |&nbsp; |&nbsp; |
| HMaster 정보 웹 UI |헤드 노드 |16010 |HTTP |HBase 마스터 웹 UI에 대한 포트 |
| Region 서버 |모든 작업자 노드 |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |클라이언트가 ZooKeeper 연결에 사용하는 포트 |

### <a name="kafka-ports"></a>Kafka 포트

| 서비스 | 노드 | 포트 | 프로토콜 | 설명 |
| --- | --- | --- | --- | --- |
| Broker |작업자 노드 |9092 |[Kafka 유선 프로토콜](https://kafka.apache.org/protocol.html) |클라이언트 통신에 사용됨 |
| &nbsp; |Zookeeper 노드 |2181 |&nbsp; |클라이언트가 ZooKeeper 연결에 사용하는 포트 |

### <a name="spark-ports"></a>Spark 포트

| 서비스 | 노드 | 포트 | 프로토콜 | URL 경로 | 설명 |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift 서버 |헤드 노드 |10002 |Thrift | &nbsp; | Spark SQL에 연결하기 위한 서비스(Thrift/JDBC) |
| Livy 서버 | 헤드 노드 | 8998 | HTTP | &nbsp; | 문, 작업 및 애플리케이션을 실행하기 위한 서비스 |
| Jupyter Notebook | 헤드 노드 | 8001 | HTTP | &nbsp; | Jupyter Notebook 웹 사이트 |

예제:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. 이 예제에서 `10.0.0.11`은 Livy 서비스를 호스트하는 헤드 노드의 IP 주소입니다.
