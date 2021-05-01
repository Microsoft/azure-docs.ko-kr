---
title: Azure HDInsight에서 Apache Storm 토폴로지 배포 및 관리
description: Linux 기반 HDInsight에서 Storm 대시보드를 사용하여 Apache Storm 토폴로지를 배포, 모니터링하고 관리하는 방법을 알아봅니다. Visual Studio용 Hadoop 도구를 사용합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: fb57992c8e26560061faf68443c4993801f9713d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871574"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Azure HDInsight에서 Apache Storm 토폴로지 배포 및 관리

이 문서에서는 HDInsight의 Storm 클러스터에서 실행되는 [Apache Storm](https://storm.apache.org/) 토폴로지의 모니터링 및 관리에 관한 기본 사항을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Storm 클러스터. [Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하고 **클러스터 유형** 에 **Storm** 을 선택합니다.

* (선택 사항) SSH(Secure Shell) 및 SCP(Secure Copy)에 대한 이해. 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* (선택 사항) Visual Studio, Azure SDK 2.5.1 이상 및 Data Lake Tools for Visual Studio. 자세한 내용은 [Apache Hadoop & Visual Studio Data Lake 도구](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)를 참조하세요.

## <a name="submit-a-topology-using-visual-studio"></a>Visual Studio를 사용하여 토폴로지 제출

Data Lake Tools for Visual Studio를 사용하여 C# 또는 하이브리드 토폴로지를 Storm 클러스터에 제출할 수 있습니다. 다음 단계는 샘플 애플리케이션을 사용합니다. Data Lake Tools를 사용하여 토폴로지를 만드는 방법에 대한 자세한 내용은 [Visual Studio 및 C#을 사용하는 Apache Storm 토폴로지](apache-storm-develop-csharp-visual-studio-topology.md)를 참조하세요.

1. 최신 버전의 Data Lake Tools for Visual Studio를 아직 설치하지 않은 경우 [Data Lake Tools for Visual Studio 사용](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

    > [!NOTE]  
    > Azure Data Lake 및 Stream Analytics Tools는 이전에 HDInsight Tools for Visual Studio라고 했습니다.
    >
    > Azure Data Lake 및 Stream Analytics Tools for Visual Studio는 Visual Studio 2019용 **Azure 개발** 워크로드에 포함되어 있습니다.

1. Visual Studio를 시작합니다.

1. **시작** 창에서 **새 프로젝트 만들기** 를 선택합니다.

1. **새 프로젝트 만들기** 창에서 검색 상자를 선택하고 `Storm`을(를) 입력합니다. 그런 다음 결과 목록에서 **Storm 샘플** 을 선택한 후 **다음** 을 선택합니다.

1. **새 프로젝트 구성** 창에서 **프로젝트 이름** 을 입력하고 새 프로젝트를 저장할 **위치** 로 이동하거나 해당 위치를 만듭니다. 그런 다음 **생성** 를 선택합니다.

    :::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png" alt-text="새 프로젝트 구성, Visual Studio" border="true":::

1. **서버 탐색기** 에서 **Azure** 를 마우스 오른쪽 단추로 클릭하고 **Microsoft Azure 구독에 연결...** 을 선택하여 로그인 프로세스를 완료합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출** 을 선택합니다.

    > [!NOTE]  
    > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

1. **토폴로지 제출** 대화 상자의 **Storm 클러스터** 드롭다운 목록에서 HDInsight 클러스터에 있는 Storm을 선택한 후 **제출** 을 선택합니다. **출력** 창을 확인하여 제출 성공 여부를 모니터링할 수 있습니다.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>SSH 및 Storm 명령을 사용하여 토폴로지 제출

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. ssh 세션에서 다음 명령을 사용하여 **WordCount** 예제 토폴로지를 시작합니다.

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    이 명령은 클러스터에서 예제 WordCount 토폴로지를 시작합니다. 이 토폴로지는 임의로 문장을 생성한 다음 문장에서 각 단어의 발생 횟수를 계산합니다.

    > [!NOTE]  
    > 클러스터에 토폴로지를 제출할 때 `storm` 명령을 사용하기 전에 먼저 클러스터가 포함된 .jar 파일을 복사해야 합니다. 클러스터에 파일을 복사하려면 `scp` 명령을 사용할 수 있습니다. 예를 들어 다음과 같이 입력합니다. `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > *WordCount* 예제 및 다른 Storm 스타터 예제는 `/usr/hdp/current/storm-client/contrib/storm-starter/`에 있는 클러스터에 이미 포함되어 있습니다.

## <a name="submit-a-topology-programmatically"></a>프로그래밍 방식으로 토폴로지 제출

Nimbus 서비스를 사용하여 토폴로지를 프로그래밍 방식으로 배포할 수 있습니다. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)는 Nimbus 서비스를 통해 토폴로지를 배포하고 시작하는 방법을 보여 주는 예제 Java 애플리케이션을 제공합니다.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Visual Studio에서 토폴로지 모니터링 및 관리

Visual Studio를 사용하여 토폴로지를 제출하면 **Storm 토폴로지 보기** 창이 표시됩니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 토폴로지를 선택합니다.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png" alt-text="Monitor 토폴로지, Storm 토폴로지 보기 창, Visual Studio" border="true":::

> [!NOTE]  
> **서버 탐색기** 에서 **Storm 토폴로지** 를 볼 수도 있습니다. **Azure** > **HDInsight** 를 확장하고 HDInsight 클러스터에서 Storm을 마우스 오른쪽 단추로 클릭한 다음 **Storm 토폴로지 보기** 를 선택합니다.

이러한 구성 요소에 대한 정보를 보려면 Spout 또는 Bolt에 대한 셰이프를 선택합니다. 선택한 항목에 대한 구성 요소 정보가 포함된 도구 설명이 표시됩니다.

### <a name="deactivate-and-reactivate-a-topology"></a>토폴로지 비활성화 및 재활성화

토폴로지를 비활성화하면 토폴로지를 종료하거나 재활성화할 때까지 일시 중지됩니다. 이러한 작업을 수행하려면 **Storm 토폴로지 보기** 창의 맨 위에 있는 **작업** 영역에서 **비활성화** 및 **재활성화** 단추를 사용합니다.

### <a name="rebalance-a-topology"></a>토폴로지 리밸런스

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. 예를 들어 클러스터의 크기를 조정하여 다른 메모를 추가하는 경우 리밸런싱하면 토폴로지에서 새 노드를 볼 수 있습니다.

토폴로지를 리밸런스하려면 **Storm 토폴로지 보기** 창의 **작업** 영역에서 **리밸런스** 단추를 사용합니다.

> [!WARNING]  
> 토폴로지를 리밸런싱하면, 토폴로지를 비활성화하고 클러스터에 작업자를 균등하게 다시 배포한 후 토폴로지를 리밸런싱하기 전의 상태로 반환합니다. 토폴로지가 활성화된 경우, 다시 활성화됩니다. 토폴로지가 비활성화된 경우, 비활성화된 상태를 유지합니다.

### <a name="kill-a-running-topology"></a>실행 중인 토폴로지를 중단합니다.

Storm 토폴로지는 중지되거나 클러스터가 삭제될 때까지 계속 실행됩니다. 토폴로지를 중지하려면 **작업** 영역에서 **종료** 단추를 사용합니다.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>SSH 및 Storm 명령을 사용하여 토폴로지 모니터링 및 관리

`storm` 유틸리티를 사용하면 명령줄에서 실행하는 토폴로지로 작업할 수 있습니다. 전체 명령 목록에는 `storm -h` 를 사용합니다.

### <a name="list-topologies"></a>목록 토폴로지

다음 명령을 사용하여 실행하는 토폴로지를 나열합니다.

```shell
storm list
```

이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>토폴로지 비활성화 및 재활성화

토폴로지를 비활성화하면 토폴로지를 종료하거나 재활성화할 때까지 일시 중지됩니다. 다음 명령을 사용하여 비활성화하거나 재활성화합니다.

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>실행 중인 토폴로지를 중단합니다.

Storm 토폴로지가 일단 시작되면 중지될 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>토폴로지 리밸런스

토폴로지의 균형을 재조정하면 시스템이 토폴로지의 병렬 처리를 수정할 수 있습니다. 예를 들어 클러스터의 크기를 조정하여 다른 메모를 추가하는 경우 리밸런싱하면 토폴로지에서 새 노드를 볼 수 있습니다.

> [!WARNING]  
> 토폴로지를 리밸런싱하면, 토폴로지를 비활성화하고 클러스터에 작업자를 균등하게 다시 배포한 후 토폴로지를 리밸런싱하기 전의 상태로 반환합니다. 토폴로지가 활성화된 경우, 다시 활성화됩니다. 비활성화되어 있다면 비활성화된 상태를 유지합니다.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Storm UI를 사용하여 토폴로지 모니터링 및 관리

Storm UI는 토폴로지를 실행하여 함께 작업하기 위한 웹 인터페이스를 제공하고, HDInsight 클러스터에 포함됩니다. Storm UI를 보려면 웹 브라우저를 사용하여 `https://CLUSTERNAME.azurehdinsight.net/stormui`를 엽니다. 여기서 *CLUSTERNAME* 은 클러스터의 이름입니다.

> [!NOTE]  
> 사용자 이름 및 암호를 입력하라는 메시지가 표시되면 클러스터를 만들 때 사용한 클러스터 관리자 사용자 이름 및 암호를 입력합니다.

### <a name="storm-ui-main-page"></a>Storm UI 기본 페이지

Storm UI의 기본 페이지에서는 다음 정보를 제공합니다.

| 섹션 | Description |
| --- | --- |
| 클러스터 요약| Storm 클러스터에 대한 기본 정보입니다. |
| Nimbus 요약 | 기본 Nimbus 정보 목록입니다. |
| 토폴로지 요약 | 실행 중인 토폴로지 목록입니다. 특정 토폴로지에 대한 자세한 정보를 보려면 **이름** 열에서 해당 링크를 선택합니다. |
| 감독자 요약 | Storm 감독자에 대한 정보입니다. 특정 감독자와 연결된 작업자 리소스를 보려면 **호스트** 또는 **Id** 열에서 해당 링크를 선택합니다. |
| Nimbus 구성 | 클러스터에 대한 Nimbus 구성입니다. |

Storm UI 기본 페이지는 다음 웹 페이지와 유사합니다.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png" alt-text="기본 페이지, Storm UI, Apache Storm 토폴로지, Azure" border="true":::

#### <a name="topology-summary"></a>토폴로지 요약

**토폴로지 요약** 섹션의 링크를 선택하면 토폴로지에 대한 다음 정보가 표시됩니다.

| 섹션 | Description |
| --- | --- |
| 토폴로지 요약 | 토폴로지에 대한 기본 정보입니다. |
| 토폴로지 작업| 토폴로지에 대해 수행할 수 있는 관리 작업입니다. 사용 가능한 작업은 이 섹션의 뒷부분에서 설명합니다. |
| 토폴로지 통계 | 토폴로지에 대한 통계입니다. 이 섹션의 항목에 대한 시간 프레임을 설정하려면 **창** 열에서 해당 링크를 선택합니다. |
| Spouts *(시간 프레임)* | 토폴로지에서 사용하는 Spout입니다. 특정 Spout에 대한 자세한 정보를 보려면 **Id** 열에서 해당 링크를 선택합니다. |
| Bolt *(시간 프레임)* | 토폴로지에서 사용하는 Bolt입니다. 특정 Bolt에 대한 자세한 정보를 보려면 **Id** 열에서 해당 링크를 선택합니다. |
| 작업자 리소스 | 작업자 리소스의 목록입니다. 특정 작업자 리소스에 대한 자세한 정보를 보려면 **호스트** 열에서 해당 링크를 선택합니다. |
| 토폴로지 시각화 | 토폴로지의 시각화를 표시하는 **시각화 표시** 단추입니다. |
| 토폴로지 구성 | 선택한 토폴로지의 구성입니다. |

Storm 토폴로지 요약 페이지는 다음 웹 페이지와 유사합니다.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png" alt-text="토폴로지 요약 페이지, Storm UI, Apache Storm, Azure" border="true":::

**토폴로지 작업** 섹션에서 다음 단추를 선택하여 작업을 수행할 수 있습니다.

| 단추 | 설명 |
| --- | --- |
| 활성화 | 비활성화된 토폴로지 처리를 다시 시작합니다. |
| 비활성화 | 실행 중인 토폴로지를 일시 중지합니다. |
| 균형 재조정 | 토폴로지의 병렬 처리를 조정합니다. 클러스터의 노드 수를 변경한 후 실행 중인 토폴로지를 리밸런싱해야 합니다. 이 작업을 사용하면 토폴로지가 병렬 처리를 조정하여 클러스터에서 추가되거나 감소된 노드 수를 보정할 수 있습니다.<br/><br/>자세한 내용은 <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Apache Storm 토폴로지의 병렬 처리 이해</a>를 참조하세요.
| 종료 | 지정된 시간 제한 후 Storm 토폴로지를 종료합니다. |
| 디버그 | 실행 중인 토폴로지에 대한 디버깅 세션을 시작합니다. |
| 디버그 중지 | 실행 중인 토폴로지에 대한 디버깅 세션을 종료합니다. |
| 로그 수준 변경 | 디버깅 로그 수준을 수정합니다. |

##### <a name="spout-and-bolt-summary"></a>Spout 및 Bolt 요약

**Spouts** 또는 **Bolts** 섹션에서 Spout를 선택하면 선택한 항목에 대해 다음 정보가 표시됩니다.

| 섹션 | Description |
| --- | --- |
| 구성 요소 요약 | Spout 또는 Bolt에 대한 기본 정보입니다. |
| 구성 요소 작업 | **디버그** 및 **디버그 중지** 단추입니다. |
| Spout 통계 또는 Bolt 통계 | Spout 또는 Bolt에 대한 통계입니다. 이 섹션의 항목에 대한 시간 프레임을 설정하려면 **창** 열에서 해당 링크를 선택합니다. |
| (볼트 전용)<br/>입력 통계 *(시간 프레임)* | Bolt에서 사용하는 입력 스트림에 대한 정보입니다. |
| 출력 통계 *(시간 프레임)* | Spout 또는 Bolt가 내보낸 스트림에 대한 정보입니다. |
| 프로파일링 및 디버깅 | 이 페이지의 구성 요소를 프로파일링/디버깅하기 위한 컨트롤입니다. **상태/시간 제한(분)** 값을 설정하고 **JStack**, **작업자 다시 시작** 및 **힙** 단추를 선택할 수 있습니다. |
| 실행기 *(시간 프레임)* | Spout 또는 Bolt의 인스턴스에 대한 정보입니다. 이 인스턴스용으로 생성된 진단 정보 로그를 보려면 특정 실행기에 대한 **포트** 항목을 선택합니다. 또한 **호스트** 열에서 해당 링크를 선택하여 특정 실행기와 연결된 작업자 리소스를 볼 수 있습니다. |
| 오류 | Spout 또는 Bolt에 대한 오류 정보입니다. |

Storm Bolt 요약 페이지는 다음 웹 페이지와 유사합니다.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png" alt-text="Bolt 요약 페이지, Storm UI, Apache Storm, Azure" border="true":::

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>REST API를 사용하여 토폴로지 모니터링 및 관리

Storm UI는 REST API를 기반으로 빌드되므로, REST API를 사용하여 유사한 관리/모니터링 작업을 수행할 수 있습니다. REST API를 사용하여 Storm 토폴로지를 관리 및 모니터링하는 사용자 지정 도구를 만들 수 있습니다.

자세한 내용은 [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html)를 참조하세요. 다음 정보는 HDInsight에서 Apache Storm과 REST API 사용하기에 관한 것입니다.

> [!IMPORTANT]  
> Storm REST API는 인터넷을 통해 공개적으로 사용할 수 없습니다. SSH 터널을 사용하여 HDInsight 클러스터 헤드 노드에 액세스해야 합니다. SSH 터널을 만들고 사용하는 방법에 대한 자세한 내용은 [SSH 터널링을 사용하여 Azure HDInsight에 액세스](../hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

### <a name="base-uri"></a>기본 URI

Linux 기반 HDInsight 클러스터의 REST API에 대한 기본 URI는 URL 주소 `https://HEADNODEFQDN:8744/api/v1/`에서 사용할 수 있습니다. 여기서 *HEADNODEFQDN* 을 헤드 노드로 바꿉니다. 헤드 노드의 도메인 이름은 클러스터를 만드는 동안 생성되고, 고정적이지 않습니다.

다양한 방법으로 클러스터 헤드 노드에 대한 FQDN(정규화된 도메인 이름)을 찾을 수 있습니다.

| FQDN 검색 방법 | Description |
| --- | --- |
| SSH 세션 | SSH 세션에서 클러스터로 `headnode -f` 명령을 사용합니다. |
| Ambari Web | Ambari 클러스터 웹 페이지(`https://CLUSTERNAME.azurehdinsight.net`)의 맨 위에서 **서비스** 를 선택한 후 **Storm** 을 선택합니다. **요약** 탭에서 **Storm UI 서버** 를 선택합니다. Storm UI 및 REST API가 호스팅하는 노드의 FQDN은 페이지 맨 위에 표시됩니다. |
| Ambari REST API | `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 명령을 사용하여 Storm UI 및 REST API가 실행 중인 노드에 관한 정보를 검색합니다. *CLUSTERNAME* 의 두 인스턴스를 클러스터 이름으로 바꿉니다. 메시지가 표시되면 사용자(관리자) 계정의 암호를 입력합니다. 응답에서 JSON 출력의 "host_name" 항목에는 노드의 FQDN이 포함됩니다. |

### <a name="authentication"></a>인증

REST API에 대한 요청은 *기본 인증* 을 사용해야 하므로, HDInsight 클러스터에 대한 관리자 이름과 암호를 사용해야 합니다.

> [!NOTE]  
> 기본 인증은 일반 텍스트로 전송되기 때문에 클러스터와의 안전한 통신을 위해서는 *항상* HTTPS를 사용해야 합니다.

### <a name="return-values"></a>반환 값

REST API에서 반환되는 정보는 클러스터 내에서만 사용할 수 있습니다. 예를 들어, [Apache ZooKeeper](https://zookeeper.apache.org/) 서버에 반환된 FQDN(정규화된 도메인 이름)은 인터넷에서 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Apache Maven을 사용하여 Java 기반 토폴로지를 개발](apache-storm-develop-java-topology.md)하는 방법을 알아봅니다.

추가 예제 토폴로지 목록은 [Azure HDInsight의 예제 Apache Storm 토폴로지](apache-storm-example-topology.md)를 참조하세요.
