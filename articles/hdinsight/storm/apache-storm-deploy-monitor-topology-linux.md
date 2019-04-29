---
title: Azure HDInsight에서 Apache Storm 토폴로지 배포 및 관리
description: Linux 기반 HDInsight에서 Storm 대시보드를 사용하여 Apache Storm 토폴로지를 배포, 모니터링 및 관리하는 방법에 대해 배웁니다. Visual Studio용 Hadoop 도구를 사용합니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 8b27ad34bdc6fcbd7a1eb46515fbf33c96d02528
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125241"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Storm 토폴로지 배포 및 관리 

이 문서에서는 HDInsight의 Storm 클러스터에서 실행되는 [Apache Storm](https://storm.apache.org/) 토폴로지의 모니터링 및 관리에 관한 기본 사항을 알아봅니다.

> [!IMPORTANT]  
> 이 문서의 단계에는 HDInsight 클러스터의 Linux 기반 Storm이 필요합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요. 
>


## <a name="prerequisites"></a>필수 조건

* **HDInsight 클러스터의 Linux 기반 Storm**: 클러스터를 만드는 단계는 [HDInsight에서 Apache Storm 시작](apache-storm-tutorial-get-started-linux.md) 을 참조하세요.

* (선택 사항) **SSH 및 SCP 사용 경험**: 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* (선택 사항) **Visual Studio**: Azure SDK 2.5.1 이상 및 Data Lake Tools for Visual Studio. 자세한 내용은 [Data Lake Tools for Visual Studio 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

    다음과 같은 Visual Studio 버전 중 하나:

  * Visual Studio 2012 업데이트 4

  * Visual Studio 2013 업데이트 4 또는 [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015(모든 버전)

  * Visual Studio 2017(모든 버전) Data Lake Tools for Visual Studio 2017은 Azure 워크로드의 일부로 설치됩니다.

## <a name="submit-a-topology-visual-studio"></a>토폴로지 제출: Visual Studio

HDInsight 도구는 Storm 클러스터에 C# 또는 하이브리드 토폴로지를 제출하는 데 사용할 수 있습니다. 다음 단계는 샘플 애플리케이션을 사용합니다. HDInsight 도구를 사용하여 만들기에 대한 자세한 내용은 [Visual Studio용 HDInsight 도구를 사용하여 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

1. 최신 버전의 Data Lake Tools for Visual Studio를 아직 설치하지 않은 경우 [Data Lake Tools for Visual Studio 사용 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

    > [!NOTE]  
    > Data Lake Tools for Visual Studio는 예전에 HDInsight Tools for Visual Studio였습니다.
    >
    > Data Lake Tools for Visual Studio는 Visual Studio 2017의 __Azure 워크로드__에 포함됩니다.

2. Visual Studio를 열고 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

3. **새 프로젝트** 대화 상자에서 **설치됨** > **템플릿**을 확장하고 **HDInsight**를 선택합니다. 템플릿 목록에서 **Storm 샘플**을 선택합니다. 대화 상자 아래쪽에서 애플리케이션 이름을 입력합니다.

    ![Image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.

   > [!NOTE]  
   > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

5. **Storm 클러스터** 드롭다운 목록에서 HDInsight의 Storm 클러스터를 선택한 다음 **제출**을 선택합니다. **출력** 창을 사용하여 제출 성공 여부를 모니터링할 수 있습니다.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>토폴로지 제출: SSH 및 Storm 명령

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME** 을 HDInsight 클러스터 이름으로 바꿉니다.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    SSH를 사용하여 HDInsight 클러스터로 연결하는 방법에 대한 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령을 사용하여 예제 토폴로지를 시작합니다.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    이 명령은 클러스터에서 예제 WordCount 토폴로지를 시작합니다. 이 토폴로지는 임의로 문장을 생성한 다음 문장에서 각 단어의 발생 횟수를 계산합니다.

   > [!NOTE]  
   > 클러스터에 토폴로지를 제출할 때 `storm` 명령을 사용하기 전에 먼저 Jar 파일을 포함하는 클러스터를 복사해야 합니다. 클러스터에 파일을 복사하려면 `scp` 명령을 사용할 수 있습니다. 예를 들어 `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > WordCount 예제 및 다른 Storm 스타터 예제는 `/usr/hdp/current/storm-client/contrib/storm-starter/`에서 클러스터에 이미 포함되어 있습니다.

## <a name="submit-a-topology-programmatically"></a>토폴로지 제출: 프로그래밍 방식으로

Nimbus 서비스를 사용하여 토폴로지를 프로그래밍 방식으로 배포할 수 있습니다. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)는 Nimbus 서비스를 통해 토폴로지를 배포하고 시작하는 방법을 보여 주는 예제 Java 애플리케이션을 제공합니다.

## <a name="monitor-and-manage-visual-studio"></a>모니터링 및 관리: Visual Studio

Visual Studio를 사용하여 토폴로지를 제출하는 경우 **Storm 토폴로지** 보기가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 토폴로지를 선택합니다.

![VISUAL STUDIO 모니터](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> **Azure** > **HDInsight**를 확장한 다음 HDInsight의 Storm 클러스터를 마우스 오른쪽 단추로 클릭하고 **Storm 토폴로지 보기**를 선택하여 **서버 탐색기**에서 **Storm 토폴로지**를 볼 수도 있습니다.

이러한 구성 요소에 대한 정보를 보려면 Spout 또는 Bolt에 대한 셰이프를 선택합니다. 선택한 각 항목에 대해 새 창이 열립니다.

### <a name="deactivate-and-reactivate"></a>비활성화 및 다시 활성화

토폴로지를 비활성화하면 작업을 중단하거나 다시 활성화할 때까지 일시 중지합니다. 이러한 작업을 수행하려면 __토폴로지 요약__ 맨 위에 있는 __비활성화__ 및 __다시 활성화__ 단추를 사용합니다.

### <a name="rebalance"></a>균형 재조정

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. 예를 들어 클러스터 크기를 조정하고 더 많은 메모를 추가하려면 균형을 재조정하여 토폴로지가 새 노드를 확인할 수 있습니다.

토폴로지의 균형을 재조정하려면 __토폴로지 요약__ 맨 위에 있는 __균형 재조정__ 단추를 사용합니다.

> [!WARNING]  
> 먼저 토폴로지의 균형을 재조정하면 토폴로지를 비활성화하고 클러스터에 작업자를 균등하게 다시 배포한 다음 마지막으로 토폴로지를 균형 재조정이 발생하기 전의 상태로 반환합니다. 따라서 토폴로지가 활성화되었으면 작업이 다시 활성화됩니다. 비활성화되어 있다면 비활성화된 상태를 유지합니다.

### <a name="kill-a-topology"></a>토폴로지 종료

Storm 토폴로지는 중지되거나 클러스터가 삭제될 때까지 계속 실행됩니다. 토폴로지를 중지하려면 __토폴로지 요약__ 맨 위에 있는 __종료__ 단추를 사용합니다.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>모니터링 및 관리: SSH 및 Storm 명령

`storm` 유틸리티를 사용하면 명령줄에서 실행하는 토폴로지로 작업할 수 있습니다. 전체 명령 목록에는 `storm -h` 를 사용합니다.

### <a name="list-topologies"></a>목록 토폴로지

다음 명령을 사용하여 실행하는 토폴로지를 나열합니다.

    storm list

이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>비활성화 및 다시 활성화

토폴로지를 비활성화하면 작업을 중단하거나 다시 활성화할 때까지 일시 중지합니다. 다음 명령을 사용하여 비활성화 및 다시 활성화합니다.

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>실행 중인 토폴로지를 중단합니다.

Storm 토폴로지가 일단 시작되면 중지될 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>균형 재조정

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. 예를 들어 클러스터 크기를 조정하고 더 많은 메모를 추가하려면 균형을 재조정하여 토폴로지가 새 노드를 확인할 수 있습니다.

> [!WARNING]  
> 먼저 토폴로지의 균형을 재조정하면 토폴로지를 비활성화하고 클러스터에 작업자를 균등하게 다시 배포한 다음 마지막으로 토폴로지를 균형 재조정이 발생하기 전의 상태로 반환합니다. 따라서 토폴로지가 활성화되었으면 작업이 다시 활성화됩니다. 비활성화되어 있다면 비활성화된 상태를 유지합니다.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>모니터링 및 관리: Storm UI

Storm UI는 토폴로지를 실행하여 함께 작업하기 위한 웹 인터페이스를 제공하고 HDInsight 클러스터에 포함됩니다. Storm UI를 보려면 웹 브라우저를 사용하여 **https://CLUSTERNAME.azurehdinsight.net/stormui**를 엽니다. 여기서 **CLUSTERNAME**은 클러스터의 이름입니다.

> [!NOTE]  
> 사용자 이름 및 암호를 제공하도록 요청을 받으면  클러스터를 만들 때 사용한 클러스터 관리자(관리자) 및암호를 입력합니다.

### <a name="main-page"></a>기본 페이지

Storm UI의 기본 페이지에서는 다음 정보를 제공합니다.

* **클러스터 요약**: Storm 클러스터에 대한 기본 정보입니다.
* **토폴로지 요약**: 실행 중인 토폴로지 목록입니다. 특정 토폴로지에 대한 자세한 정보를 보려면 이 섹션의 링크를 사용하세요.
* **감독자 요약**: Storm 감독자에 대한 정보입니다.
* **Nimbus 구성**: 클러스터에 대한 Nimbus 구성입니다.

### <a name="topology-summary"></a>토폴로지 요약

**토폴로지 요약** 섹션의 링크를 선택하면 토폴로지에 대한 다음 정보가 표시됩니다.

* **토폴로지 요약**: 토폴로지에 대한 기본 정보입니다.
* **토폴로지 동작**: 토폴로지에 대해 수행할 수 있는 관리 작업입니다.

  * **활성화**: 비활성화된 토폴로지 처리를 다시 시작합니다.
  * **비활성화**: 실행 중인 토폴로지를 일시 중지합니다.
  * **균형 다시 맞추기**: 토폴로지의 병렬 처리를 조정합니다. 클러스터에서 노드 수를 변경한 후 실행 중인 토폴로지의 균형을 다시 맞추어야 합니다. 이 작업을 사용하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 증가하거나 감소한 노드 수를 보충할 수 있습니다.

    자세한 내용은 <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm 토폴로지의 병렬 처리 이해</a>를 참조하세요.
  * **종료**: 지정된 시간 제한 후 Storm 토폴로지를 종료합니다.
* **토폴로지 통계**: 토폴로지에 대한 통계입니다. 페이지에서 나머지 항목에 대한 시간 프레임을 설정하려면 **창** 열에 있는 링크를 사용합니다.
* **Spout**: 토폴로지에서 사용하는 Spout입니다. 이 섹션의 링크를 사용하면 특정 Spout에 대한 자세한 내용을 볼 수 있습니다.
* **Bolt**: 토폴로지에서 사용하는 Bolt입니다. 이 섹션의 링크를 사용하면 특정 Bolt에 대한 자세한 내용을 볼 수 있습니다.
* **토폴로지 구성**: 선택한 토폴로지의 구성입니다.

### <a name="spout-and-bolt-summary"></a>Spout 및 Bolt 요약

**Spouts** 또는 **Bolts** 섹션에서 Spout를 선택하면 선택한 항목에 대해 다음 정보가 표시됩니다.

* **구성 요소 요약**: Spout 또는 Bolt에 대한 기본 정보입니다.
* **Spout/Bolt 통계**: Spout 또는 Bolt에 대한 통계입니다. 페이지에서 나머지 항목에 대한 시간 프레임을 설정하려면 **창** 열에 있는 링크를 사용합니다.
* **입력 통계**(Bolt에만 해당): Bolt에서 사용하는 입력 스트림에 대한 정보입니다.
* **출력 통계**: Spout 또는 Bolt가 내보낸 스트림에 대한 정보입니다.
* **실행자**: Spout 또는 Bolt의 인스턴스에 대한 정보입니다. 특정 실행자에 대한 **Port** 항목을 선택하면 이 인스턴스에 대해 처리된 진단 정보의 로그를 볼 수 있습니다.
* **Errors**: Spout 또는 Bolt에 대한 오류 정보입니다.

## <a name="monitor-and-manage-rest-api"></a>모니터링 및 관리: REST API

Storm UI는 REST API의 맨 위에 기본 제공되므로 REST API를 사용하여 유사한 관리 및 모니터링 기능을 수행할 수 있습니다. REST API를 사용하여 Storm 토폴로지를 관리 및 모니터링하는 사용자 지정 도구를 만들 수 있습니다.

자세한 내용은 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)를 참조하세요. 다음 정보는 HDInsight에서 Apache Storm과 REST API 사용하기에 관한 것입니다.

> [!IMPORTANT]  
> Storm REST API는 인터넷을 통해 공개적으로 사용할 수 없고 HDInsight 클러스터 헤드 노드에 SSH 터널을 사용하여 액세스되어야 합니다. SSH 터널을 만들고 사용하는 방법에 대한 자세한 내용은 [SSH 터널링을 사용하여 Apache Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Apache Oozie 및 기타 웹 UI에 액세스](../hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

### <a name="base-uri"></a>기본 URI

Linux 기반 HDInsight 클러스터에서 REST API에 대 한 기본 URI가 있는 헤드 노드에서 사용할 수 있습니다 **https:\//HEADNODEFQDN:8744/api/v1/** 합니다. 헤드 노드의 도메인 이름은 클러스터를 만드는 동안 생성되고 고정적이지 않습니다.

다양한 방법으로 클러스터 헤드 노드의 정규화된 도메인 이름(FQDN)을 찾을 수 있습니다.

* **SSH 세션에서**: SSH 세션에서 클러스터로 `headnode -f` 명령을 사용합니다.
* **Ambari 웹에서**: 페이지 맨 위에서 **서비스**를 선택한 다음, **Storm**을 선택합니다. **요약** 탭에서 **Storm UI 서버**를 선택합니다. Storm UI 및 REST API가 호스팅하는 노드의 FQDN은 페이지 맨 위에 표시됩니다.
* **Ambari REST API에서**: `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 명령을 사용하여 Storm UI 및 REST API가 실행 중인 노드에 관한 정보를 검색합니다. **CLUSTERNAME** 을 클러스터 이름으로 바꿉니다. 메시지가 표시되면 로그인(관리자) 계정에 대한 암호를 입력합니다. 응답에서 "host_name" 항목에는 노드의 FQDN이 포함됩니다.

### <a name="authentication"></a>Authentication

REST API 요청에서는 **기본 인증**을 사용해야 하므로 HDInsight 클러스터 관리자 이름 및 암호를 사용합니다.

> [!NOTE]  
> 기본 인증은 일반 텍스트로 전송되기 때문에 클러스터와의 안전한 통신을 위해서는 **항상** HTTPS를 사용해야 합니다.

### <a name="return-values"></a>반환 값

REST API에서 반환되는 정보는 클러스터 내에서만 사용할 수 있습니다. 예를 들어 [Apache ZooKeeper](https://zookeeper.apache.org/) 서버에 대해 반환된 FQDN(정규화된 도메인 이름)은 인터넷에서 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Apache Maven을 사용하여 Java 기반 토폴로지를 개발](apache-storm-develop-java-topology.md)하는 방법을 알아봅니다.

추가 예제 토폴로지 목록은 [HDInsight의 Apache Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)를 참조하세요.
