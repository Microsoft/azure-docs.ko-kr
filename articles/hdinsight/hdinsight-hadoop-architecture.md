---
title: Apache Hadoop 아키텍처 - Azure HDInsight
description: HDInsight 클러스터의 Apache Hadoop 스토리지 및 처리에 대해 설명합니다.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: edf7655c20f1b3bad5ce4d337861bd2ff3e029db
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097185"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>HDInsight의 Apache Hadoop 아키텍처

[Apache Hadoop](https://hadoop.apache.org/)에는 스토리지를 제공하는 [Apache HDFS(Hadoop 분산 파일 시스템)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html)와 처리를 제공하는 [Apache Hadoop YARN(Yet Another Resource Negotiator)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)이라는 두 가지 핵심 구성 요소가 포함되어 있습니다. 스토리지 및 처리 기능을 사용하면 클러스터에서 [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) 프로그램을 실행하여 원하는 데이터 처리를 수행할 수 있게 됩니다.

> [!NOTE]  
> 일반적으로 HDFS는 HDInsight 클러스터 내에 배포되어 저장소를 제공하지 않습니다. 대신 Hadoop 구성 요소에서 HDFS 호환 인터페이스 계층을 사용합니다. 실제 스토리지 용량은 Azure Storage 또는 Azure Data Lake Storage에서 제공합니다. Hadoop의 경우 HDFS가 있는 것처럼 HDInsight 클러스터에서 실행되는 MapReduce 작업이 실행되므로 저장소 요구 사항을 지원하기 위해 변경할 필요가 없습니다. HDInsight의 Hadoop에서 저장소는 아웃소싱되지만 YARN 처리는 핵심 구성 요소로 남아 있습니다. 자세한 내용은 [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)를 참조하세요.

이 문서에서는 YARN과 HDInsight에서 애플리케이션 실행을 조정하는 방법에 대해 소개합니다.

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop YARN 기본 내용 

YARN은 Hadoop의 데이터 처리를 제어하고 오케스트레이션합니다. YARN에는 클러스터의 노드에서 프로세스로 실행되는 다음 두 가지 핵심 서비스가 있습니다. 

* ResourceManager 
* NodeManager

ResourceManager는 MapReduce 작업과 같은 애플리케이션에 클러스터 계산 리소스를 제공합니다. ResourceManager는 이러한 리소스를 컨테이너로 제공하며, 각 컨테이너는 CPU 코어 수 및 RAM 메모리의 할당으로 구성됩니다. 클러스터에서 사용할 수 있는 모든 리소스를 결합한 다음, 코어 및 메모리를 블록으로 분산한 경우 각 리소스 블록이 컨테이너가 됩니다. 클러스터의 각 노드에는 특정 수의 컨테이너에 대한 용량이 있으므로, 클러스터에는 사용할 수 있는 컨테이너 수에 대해 고정된 제한이 있습니다. 컨테이너에 있는 리소스의 할당은 구성할 수 있습니다. 

MapReduce 애플리케이션이 클러스터에서 실행될 때, ResourceManager는 실행할 컨테이너를 애플리케이션에 제공합니다. ResourceManager는 실행 중인 애플리케이션의 상태와 사용 가능한 클러스터 용량을 추적하고, 리소스가 완료되고 해제될 때 애플리케이션을 추적합니다. 

또한 ResourceManager는 애플리케이션의 상태를 모니터링하기 위한 웹 사용자 인터페이스를 제공하는 웹 서버 프로세스도 실행합니다.

사용자가 클러스터에서 실행할 MapReduce 애플리케이션을 제출하면 해당 애플리케이션이 ResourceManager에 제출됩니다. 이에 따라 ResourceManager는 사용 가능한 NodeManager 노드에 컨테이너를 할당합니다. NodeManager 노드는 실제로 애플리케이션이 실행되는 위치입니다. 할당된 첫 번째 컨테이너에서 ApplicationMaster라는 특별한 애플리케이션을 실행합니다. 이 ApplicationMaster는 제출된 애플리케이션을 실행하는 데 필요한 리소스를 후속 컨테이너의 형태로 확보해야 합니다. ApplicationMaster는 애플리케이션의 단계(예: 맵 단계 및 축소 단계)를 검사하고 처리해야 할 데이터의 양을 결정합니다. 그런 다음, ApplicationMaster는 애플리케이션을 대신하여 ResourceManager에서 리소스를 요청(*협상*)합니다. 이에 따라 ResourceManager는 애플리케이션을 실행하는 데 사용하도록 클러스터의 NodeManager에서 ApplicationMaster로 리소스를 제공합니다. 

NodeManager는 애플리케이션을 구성하는 작업을 실행한 다음, 진행 상황과 상태를 ApplicationMaster에 다시 보고합니다. ApplicationMaster는 다시 애플리케이션의 상태를 ResourceManager에 보고합니다. 이에 따라 ResourceManager는 모든 결과를 클라이언트에 반환합니다.

## <a name="yarn-on-hdinsight"></a>HDInsight의 YARN

모든 HDInsight 클러스터 유형은 YARN을 배포합니다. ResourceManager는 고가용성을 위해 클러스터 내의 첫 번째 및 두 번째 헤드 노드에서 각각 실행되는 주 인스턴스와 보조 인스턴스를 통해 배포됩니다. ResourceManager 인스턴스는 한 번에 하나씩 활성화됩니다. NodeManager 인스턴스는 클러스터의 사용 가능한 작업자 노드에서 실행됩니다.

![HDInsight의 YARN](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Hadoop에서 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)
* [Azure HDInsight 소개](hadoop/apache-hadoop-introduction.md)
