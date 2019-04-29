---
title: 설치 하 고 Azure HDInsight에서 Giraph를 사용 합니다.
description: 스크립트 동작을 사용 하 여 HDInsight 클러스터에 Giraph를 설치 하는 방법에 알아봅니다. 그래프는 Azure 클라우드에서 Apache Hadoop에서 처리를 위해 Giraph를 사용할 수 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: aa13d8dfc65f020f3f27183423913933cd0b9404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61395386"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>HDInsight Hadoop 클러스터에 Apache Giraph를 설치하고 Giraph를 사용하여 대규모 그래프를 처리합니다.

HDInsight 클러스터에 Apache Giraph를 설치하는 방법을 알아봅니다. Hdinsight의 스크립트 작업 기능을 사용하면 bash 스크립트를 실행하여 클러스터를 사용자 지정할 수 있습니다. 클러스터를 만드는 도중 및 만든 후에 클러스터를 사용자 지정하는 데 스크립트를 사용할 수 있습니다.

## <a name="whatis"></a>Giraph 정의

[Apache Giraph](https://giraph.apache.org/)를 통해 Hadoop을 사용하여 그래프 처리를 수행할 수 있으며, Azure HDInsight에서 이를 사용할 수도 있습니다. Graph는 개체 간의 관계를 모델링합니다. 예를 들어 인터넷 또는 소셜 네트워크 상의 사람들 간 관계와 같은 대규모 네트워크의 라우터 간의 연결입니다. 그래프 처리를 통해 그래프의 개체 간 관계를 추론하여 다음을 수행할 수 있습니다.

* 현재 관계를 기반으로 하여 잠재적인 친구 파악.

* 네트워크의 두 컴퓨터 간에 가장 짧은 경로 파악.

* 웹 페이지의 페이지 순위 계산.

> [!WARNING]  
> HDInsight 클러스터와 함께 제공되는 구성 요소는 완벽하게 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하는 데 도움이 됩니다.
>
> Giraph와 같은 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원은 문제를 해결할 수 있습니다. 그렇지 않으면 해당 기술에 대한 전문 지식을 찾을 수 있는 오픈 소스 커뮤니티를 참조해야 합니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [https://apache.org](https://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](https://hadoop.apache.org/)).


## <a name="what-the-script-does"></a>스크립트가 수행하는 작업

이 스크립트는 다음 작업을 수행합니다.

* Giraph를 `/usr/hdp/current/giraph`에 설치합니다.

* 클러스터에 대한 기본 저장소(WASB)에 `giraph-examples.jar` 파일을 복사합니다. `/example/jars/giraph-examples.jar`

## <a name="install"></a>스크립트 동작을 사용하여 Giraph 설치

HDInsight 클러스터에서 Giraph를 설치하는 샘플 스크립트는 다음 위치에서 사용할 수 있습니다.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

이 섹션에서는 Azure 포털을 사용하여 클러스터를 만들면서 샘플 스크립트를 사용하는 방법에 대한 지침을 제공합니다.

> [!NOTE]  
> 스크립트 작업은 다음 방법 중 하나를 사용하여 적용될 수 있습니다.
> * Azure PowerShell
> * Azure CLI
> * HDInsight .NET SDK
> * Azure 리소스 관리자 템플릿
> 
> 이미 실행 중인 클러스터에도 스크립트 동작을 적용할 수 있습니다. 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux 기반 HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)의 단계를 사용하여 클러스터를 만들기 시작하지만 완료하지 마세요.

2. **선택적 구성** 섹션에서 **스크립트 동작**을 선택하고 다음 정보를 제공합니다.

   * **이름**: 스크립트 동작의 이름을 입력합니다.

   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **HEAD**: 이 항목을 선택합니다.

   * **WORKER**: 이 항목을 선택 취소된 상태로 둡니다.

   * **ZOOKEEPER**: 이 항목을 선택 취소된 상태로 둡니다.

   * **PARAMETERS**: 이 필드는 공백으로 둡니다.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다. 마지막으로 **선택적 구성** 섹션의 아래 쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.

4. [Linux 기반 HDInsight 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터를 계속 만듭니다.

## <a name="usegiraph"></a>HDInsight에서 Giraph를 사용하는 방법

클러스터를 만든 후 다음 단계를 사용하여 Giraph에 포함된 SimpleShortestPathsComputation 예제를 실행합니다. 이 예제는 그래프의 개체 간 가장 짧은 경로를 찾기 위한 기본 [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) 구현을 사용합니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 **tiny_graph.txt**라는 파일을 만듭니다.

    ```bash
    nano tiny_graph.txt
    ```

    이 파일의 내용으로 다음 텍스트를 사용합니다.

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    이 데이터는 `[source_id, source_value,[[dest_id], [edge_value],...]]` 형식을 사용하여 유향 그래프에서 개체 간 관계를 설명합니다. 각 줄은 `source_id` 개체와 하나 이상의 `dest_id` 개체 간 관계를 나타냅니다. `edge_value`는 `source_id`와 `dest\_id` 간 연결의 강도 또는 거리로 생각할 수 있습니다.

    개체 간 거리로 값(또는 가중치)을 사용하여 그리면 데이터는 다음 다이어그램과 같을 수 있습니다.

    ![원과 거리가 다른 선으로 그린 tiny_graph.txt](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

3. 파일을 저장하려면 **Ctrl + X**, **Y**, 마지막으로 **Enter**를 차례로 사용하여 파일 이름을 적용합니다.

4. 다음을 사용하여 HDInsight 클러스터에 대한 주 저장소로 데이터를 저장합니다.

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. 다음 명령을 사용하여 SimpleShortestPathsComputation 예제를 실행합니다.

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    이 명령에 사용되는 매개 변수를 다음 테이블에서 설명합니다.

   | 매개 변수 | 기능 |
   | --- | --- |
   | `jar` |예를 포함하는 jar 파일입니다. |
   | `org.apache.giraph.GiraphRunner` |예를 시작하는 데 사용되는 클래스입니다. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |사용되는 예제입니다. 이 예제에서는 그래프에서 ID 1과 다른 모든 ID 사이의 최단 경로를 계산합니다. |
   | `-ca mapred.job.tracker` |클러스터에 대한 헤드 노드입니다. |
   | `-vif` |입력 데이터에 사용할 입력 형식입니다. |
   | `-vip` |입력 데이터 파일입니다. |
   | `-vof` |출력 형식입니다. 이 예제에서는 일반 텍스트인 ID 및 값입니다. |
   | `-op` |출력 위치입니다. |
   | `-w 2` |사용할 작업자의 수입니다. 이 예제에서는 2입니다. |

    이 밖에 Giraph 샘플과 함께 사용된 기타 매개 변수에 대한 자세한 내용은 [Giraph 빠른 시작](https://giraph.apache.org/quick_start.html)을 참조하세요.

6. 작업이 완료 되 면 결과에 저장 되므로 합니다 **/example/out/shortestpaths** 디렉터리입니다. 출력 파일 이름은 **part-m-** 으로 시작하고 첫 번째, 두 번째 파일 등을 나타내는 숫자로 끝납니다. 다음 명령을 사용하여 출력을 봅니다.

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    출력은 다음 텍스트와 유사합니다.

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    SimpleShortestPathComputation 예제는 개체 ID 1로 시작하도록 하드 코딩되며 다른 개체에 대한 가장 짧은 경로를 찾습니다. 출력은 `destination_id` 및 `distance` 형식입니다. `distance`는 가장자리를 기준으로 개체 ID 1과 대상 ID 간의 거리 값(또는 가중치)입니다.

    이 데이터를 시각화하면 ID 1과 다른 모든 개체 간의 가장 짧은 경로를 이동하여 결과를 확인할 수 있습니다. ID 1과 ID 4 간의 가장 짧은 경로는 5입니다. 이 값은 <span style="color:orange">ID 1과 3</span> 사이의 총 거리와 <span style="color:red">ID 3과 4</span> 사이의 총 거리입니다.

    ![가장 짧은 경로와 함께 원으로 그린 개체](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)

## <a name="next-steps"></a>다음 단계

* [HDInsight 클러스터에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)입니다.
