---
title: HDInsight의 Apache Storm에서 Storm-starter예제 - Azure
description: HDInsight에서 Apache Storm 및 storm-starter 예제를 사용하여 빅 데이터 분석 및 데이터 처리를 수행하는 방법을 알아봅니다.
keywords: storm-starter, apache storm 예제
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 40757c80878ef5a06d3368d4c20f65ebfa11e47b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116390"
---
# <a name="get-started-with-apache-storm-on-hdinsight-using-the-storm-starter-examples"></a>storm-starter 예제를 사용하여 HDInsight의 Apache Storm 시작

storm-starter 예제를 사용하여 HDInsight의 [Apache Storm](https://storm.apache.org/)을 사용하는 방법을 알아봅니다.

Apache Storm은 데이터 스트림 처리용 확장 가능한 분산형 실시간 계산 시스템입니다. Azure HDInsight의 Storm을 사용하여 실시간 데이터 분석을 수행하는 클라우드 기반 Storm 클러스터를 만들 수 있습니다.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* **SSH 및 SCP 사용 경험**. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

## <a name="create-an-apache-storm-cluster"></a>Apache Storm 클러스터 만들기

다음 단계를 사용하여 HDInsight 클러스터에 Storm을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **+ 리소스 만들기** > **Analytics** > **HDInsight**로 이동합니다.

    ![HDInsight 클러스터 만들기](./media/apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. **기본** 섹션에서 다음 정보를 입력합니다.

    * **클러스터 이름**: HDInsight 클러스터의 이름입니다.
    * **구독**: 사용할 구독을 선택합니다.
    * **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**: HTTPS를 통해 클러스터에 액세스할 때의 로그인입니다. 이러한 자격 증명을 사용하여 Ambari Web UI 또는 REST API와 같은 서비스에 액세스합니다.
    * **SSH(Secure Shell) 사용자 이름**: SSH를 통해 클러스터에 액세스할 때 사용되는 로그인입니다. 기본적으로 암호는 클러스터 로그인 암호와 동일합니다.
    * **리소스 그룹**: 클러스터를 만들어 놓은 리소스 그룹입니다.
    * **위치**: 클러스터를 만들어 놓은 Azure 지역입니다.

   ![구독 선택](./media/apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. **클러스터 유형**을 선택한 다음 **클러스터 구성** 섹션에서 다음 값을 설정합니다.

    * **클러스터 유형**: Storm

    * **운영 체제**: Linux

    * **버전**: Storm 1.1.0(HDI 3.6)

   마지막으로 **선택** 단추를 사용하여 이러한 설정을 저장합니다.

    ![클러스터 유형 선택](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. 클러스터 유형을 선택한 후 __선택__ 단추를 사용하여 클러스터 유형을 설정합니다. 그 다음, __다음__ 단추를 사용하여 기본 구성을 완료합니다.

5. **저장소** 섹션에서 저장소 계정을 선택하거나 만듭니다. 이 문서의 단계에서는 이 섹션의 다른 필드를 기본값으로 둡니다. __다음__ 단추를 사용하여 저장소 구성을 저장합니다. Data Lake Storage Gen2를 사용하는 방법에 대한 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.

    ![HDInsight에 대한 저장소 계정 설정 지정](./media/apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. **요약** 섹션에서 클러스터에 대한 구성을 검토합니다. __편집__ 링크를 사용하여 올바르지 않은 설정을 변경합니다. 마지막으로 __만들기__ 단추를 사용하여 클러스터를 만듭니다.

    ![클러스터 구성 요약](./media/apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)

    > [!NOTE]
    > 클러스터를 만드는 데 최대 20분이 걸릴 수 있습니다.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>HDInsight에서 storm-starter 샘플 실행

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!TIP]  
    > SSH 클라이언트에서 호스트의 신뢰성을 설정할 수 없다고 표시될 수도 있습니다. 그럴 경우 `yes`를 입력하여 계속합니다.

    > [!NOTE]  
    > SSH 사용자 계정을 보호하는 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공개 키를 사용하는 경우, `-i` 매개 변수를 사용하여 일치하는 개인 키를 지정해야 할 수 있습니다. 예: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

    자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 예제 토폴로지를 시작합니다.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

    이 명령은 클러스터에서 예제 WordCount 토폴로지를 시작합니다. 이 토폴로지는 임의의 문장을 생성하고 단어 발생 횟수를 계산합니다. 토폴로지의 이름은 `wordcount`입니다.

    > [!NOTE]  
    > 클러스터에 고유한 토폴로지를 제출할 때 `storm` 명령을 사용하기 전에 먼저 jar 파일을 포함하는 클러스터를 복사해야 합니다. `scp` 명령을 사용하여 파일을 복사합니다. 예를 들어 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > WordCount 예제 및 다른 storm-starter 예제는 `/usr/hdp/current/storm-client/contrib/storm-starter/`에서 클러스터에 이미 포함되어 있습니다.

storm-starter 예제의 소스를 보려면 [https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.1.x-branch/examples/storm-starter)에서 코드를 찾아보세요. 이 링크는 Storm 1.1.x에 해당하며 HDInsight 3.6과 함께 제공됩니다. Storm의 다른 버전의 경우 페이지 맨 위에 있는 __분기__ 단추를 사용하여 다른 Storm 버전을 선택합니다.

## <a name="monitor-the-topology"></a>토폴로지 모니터링

Storm UI는 토폴로지를 실행하여 함께 작업하기 위한 웹 인터페이스를 제공하고 HDInsight 클러스터에 포함됩니다.

Storm UI를 사용하여 토폴로지를 모니터링하려면 다음 단계를 사용합니다.

1. Storm UI를 표시하려면 웹 브라우저를 열고 `https://CLUSTERNAME.azurehdinsight.net/stormui`로 이동합니다. **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.

    > [!NOTE]  
    > 사용자 이름 및 암호를 제공하도록 요청을 받으면  클러스터를 만들 때 사용한 클러스터 관리자(관리자) 및암호를 입력합니다.

2. **토폴로지 요약**의 **이름** 열에서 **wordcount** 항목을 선택합니다. 토폴로지에 대한 정보가 표시됩니다.

    ![storm-starter WordCount 토폴로지 정보가 있는 Storm 대시보드.](./media/apache-storm-tutorial-get-started-linux/topology-summary.png)

    이 페이지에서는 다음 정보를 제공합니다.

   * **토폴로지 통계** - 기간으로 구성된 토폴로지 성능에 대한 기본 정보입니다.

       > [!NOTE]  
       > 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

   * **Spout** - 각 Spout에서 반환된 마지막 오류를 포함하여 Spout에 대한 기본 정보입니다.

   * **Bolt** - Bolt에 대한 기본 정보입니다.

   * **토폴로지 구성** - 토폴로지 구성에 대한 자세한 정보입니다.

     이 페이지에서는 토폴로지에 대해 수행할 수 있는 작업도 제공합니다.

   * **활성화** - 비활성화된 토폴로지 처리를 다시 시작합니다.

   * **비활성화** - 실행 중인 토폴로지를 일시 중지합니다.

   * **균형 다시 맞추기** - 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 균형을 다시 맞추면 병렬 처리가 조정되어 클러스터에서 증가/감소한 노드 수가 보정됩니다. 자세한 내용은 [Apache Storm 토폴로지의 병렬 처리 이해](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

   * **중단** - 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.

3. 이 페이지의 **Spouts** 또는 **Bolts** 섹션에서 항목을 선택합니다. 선택한 구성 요소에 대한 정보가 표시됩니다.

    ![선택한 구성 요소에 대한 정보가 있는 스톰 대시보드.](./media/apache-storm-tutorial-get-started-linux/component-summary.png)

    이 페이지에는 다음 정보가 표시됩니다.

    * **Spout/Bolt 통계** - 기간으로 구성된 구성 요소 성능에 대한 기본 정보입니다.

        > [!NOTE]  
        > 특정 기간을 선택하면 페이지의 다른 섹션에 표시되는 정보에 대한 기간이 변경됩니다.

    * **입력 통계** (Bolt에만 해당) - Bolt에서 사용되는 데이터를 생성하는 구성 요소에 대한 정보입니다.

    * **출력 통계** - 이 Bolt에서 내보낸 데이터에 대한 정보입니다.

    * **실행자** - 이 구성 요소의 인스턴스에 대한 정보입니다.

    * **오류** - 이 구성 요소에 의해 생성된 오류입니다.

4. Spout 또는 Bolt에 대한 세부 정보를 볼 때 구성 요소의 특정 인스턴스에 대한 세부 정보를 확인하려면 **실행자** 섹션의 **포트** 열에서 항목을 선택합니다.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    이 예제의 경우 **seven**이라는 단어가1493957번 발생했습니다. 이 숫자는 이 토폴로지가 시작된 이후 단어가 발생한 횟수입니다.

## <a name="stop-the-topology"></a>토폴로지 중지

단어 개수 토폴로지에 대한 **토폴로지 요약** 페이지로 돌아가 **토폴로지 작업** 섹션에서 **Kill** 단추를 선택합니다. 메시지가 표시되면 토폴로지를 중지하기 전에 대기할 시간(초)으로 10을 입력합니다. 시간 제한이 지난 후에는 대시보드의 **Storm UI** 섹션을 방문한 경우 토폴로지가 더 이상 표시되지 않습니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](../hdinsight-hadoop-create-linux-clusters-portal.md)을 참조하세요.

## <a id="next"></a>다음 단계

Apache Storm 자습서에서는 HDInsight에서 Storm으로 작업하는 기본 사항을 알아보았습니다. 다음으로, [Apache Maven을 사용하여 Java 기반 토폴로지를 개발](apache-storm-develop-java-topology.md)하는 방법을 알아봅니다.

Java 기반 토폴로지를 개발하는 데 익숙한 경우 [HDInsight에서 Apache Storm 토폴로지 배포 및 관리](apache-storm-deploy-monitor-topology-linux.md) 문서를 참조하세요.

.NET 개발자인 경우 Visual Studio를 사용하여 C# 또는 하이브리드 C#/Java 토폴로지를 만들 수 있습니다. 자세한 내용은 [Visual Studio용 Apache Hadoop 도구를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

예를 들어 HDInsight에서 Storm과 함께 사용할 수 있는 토폴로지는 다음 예제를 참조하세요.

* [HDInsight의 Apache Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: https://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[preview-portal]: https://portal.azure.com/
