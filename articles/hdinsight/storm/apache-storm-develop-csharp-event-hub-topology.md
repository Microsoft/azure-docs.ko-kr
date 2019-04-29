---
title: Storm으로 Event Hubs에서 이벤트 처리 - Azure HDInsight
description: HDInsight Tools for Visual Studio를 사용하여 Visual Studio에서 만든 C# Storm 토폴로지로 Azure Event Hubs의 데이터를 처리하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: b02945197b20c7fe704d0f8cfa9201a5b9cbc292
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125207"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리(C#)

HDInsight의 [Apache Storm](https://storm.apache.org/)에서 Azure Event Hubs를 사용하는 방법에 대해 알아봅니다. 이 문서는 C# Storm 토폴로지를 사용하여 Event Hubs에서 데이터를 읽고 씁니다.

> [!NOTE]  
> 이 프로젝트의 Java 버전은 [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리(Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)를 참조하세요.

## <a name="scpnet"></a>SCP.NET

이 문서의 단계에서는 NuGet 패키지인 SCP.NET을 사용하여 HDInsight에서 Storm과 함께 사용할 C# 토폴로지 및 구성 요소를 쉽게 만들 수 있습니다.

> [!IMPORTANT]  
> 이 문서의 단계에서는 Visual Studio를 사용하는 Windows 개발 환경을 사용하지만 컴파일된 프로젝트는 Linux를 사용하는 HDInsight 클러스터의 Storm에 제출할 수 있습니다. 2016년 10월 28일 이후에 만든 Linux 기반 클러스터만 SCP.NET 토폴로지를 지원합니다.

HDInsight 3.4 이상은 Mono를 사용하여 C# 토폴로지를 실행합니다. 이 문서에 사용된 예제는 HDInsight 3.6에서 작동합니다. HDInsight용 .NET 솔루션을 만들 계획인 경우 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/) 문서에서 잠재적인 비호환성을 확인하세요.

### <a name="cluster-versioning"></a>클러스터 버전 관리

프로젝트에 사용하는 Microsoft.SCP.Net.SDK NuGet 패키지는 HDInsight에 설치된 Storm의 주 버전과 일치해야 합니다. HDInsight 버전 3.5 및 3.6은 Storm 1.x를 사용하므로 이 클러스터와 함께 SCP.NET 버전 1.0.x.x를 사용해야 합니다.

> [!IMPORTANT]  
> 이 문서의 예제는 HDInsight 3.5 또는 3.6 클러스터를 가정합니다.
>
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

C# 토폴로지도 .NET 4.5를 대상으로 해야 합니다.

## <a name="how-to-work-with-event-hubs"></a>Event Hubs 작동 방법

Microsoft는 Storm 토폴로지의 Event Hubs와 통신하는 데 사용할 수 있는 Java 구성 요소 집합을 제공합니다. [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)에서 이러한 구성 요소의 HDInsight 3.6 호환 버전이 포함된 JAR(Java archive) 파일을 찾을 수 있습니다.

> [!IMPORTANT]  
> 구성 요소는 Java로 작성되지만 C# 토폴로지에서 쉽게 사용할 수 있습니다.

이 예제에서는 다음 구성 요소가 사용됩니다.

* __EventHubSpout__: Event Hubs에서 데이터를 읽습니다.
* __EventHubBolt__: Event Hubs에 데이터를 씁니다.
* __EventHubSpoutConfig__: EventHubSpout를 구성하는 데 사용됩니다.
* __EventHubBoltConfig__: EventHubBolt를 구성하는 데 사용됩니다.

### <a name="example-spout-usage"></a>예제 Spout 사용

SCP.NET은 토폴로지에 EventHubSpout를 추가하기 위한 메서드를 제공합니다. 이러한 메서드를 사용하면 Java 구성 요소를 추가하기 위한 제네릭 메서드를 사용하는 것보다 더 쉽게 Spout를 추가할 수 있습니다. 다음 예제에서는 SCP.NET에서 제공하는 __SetEventHubSpout__ 및 **EventHubSpoutConfig**를 사용하여 Spout를 만드는 방법을 보여 줍니다.

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

앞의 예제에서는 __EventHubSpout__라는 새 Spout 구성 요소를 만들고 이벤트 허브와 통신하도록 구성합니다. 구성 요소에 대한 병렬 처리 힌트는 이벤트 허브의 파티션 수로 설정됩니다. 이 설정을 통해 Storm은 각 파티션에 대한 구성 요소의 인스턴스를 만들 수 있습니다.

### <a name="example-bolt-usage"></a>예제 Bolt 사용

**JavaComponmentConstructor** 메서드를 사용하여 Bolt의 인스턴스를 만듭니다. 다음 예제에서는 **EventHubBolt**의 새 인스턴스를 만들고 구성하는 방법을 보여 줍니다.

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> 이 예제에서는 Spout 예제와 같이 **JavaComponentConstructor**를 사용하여 **EventHubBoltConfig**를 만드는 대신 문자열로 전달되는 Clojure 식을 사용합니다. 어떤 방법이든 작동합니다. 자신에게 가장 좋은 것을 사용하세요.

## <a name="download-the-completed-project"></a>완성된 프로젝트 다운로드

이 자습서에서 만든 프로젝트의 전체 버전은 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)에서 다운로드할 수 있습니다. 그러나 이 자습서의 단계에 따라 구성 설정을 제공해야 합니다.

### <a name="prerequisites"></a>필수 조건

* [HDInsight 클러스터 버전 3.5 또는 3.6의 Apache Storm](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]  
    > 이 문서에서 사용되는 예제에는 HDInsight 버전 3.5 또는 3.6에서 Storm이 필요합니다. 이 내용은 주요 클래스 이름 변경 때문에 이전 버전의 HDInsight에는 적용되지 않습니다. 이전 클러스터에서 작동하는 이 예제의 버전은 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases)를 참조하세요.

* [Azure Event Hub](../../event-hubs/event-hubs-create.md)

* [Azure .NET SDK](https://azure.microsoft.com/downloads/)

* [HDInsight Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* 개발 환경에서 Java JDK 1.8 이상. JDK 다운로드는 [Oracle](https://aka.ms/azure-jdks)에서 사용할 수 있습니다.

  * **JAVA_HOME** 환경 변수가 Java를 포함하고 있는 디렉터리를 가리켜야 합니다.
  * **%JAVA_HOME%/bin** 디렉터리가 경로에 있어야 합니다.

## <a name="download-the-event-hubs-components"></a>Event Hubs 구성 요소 다운로드

[https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)에서 Event Hubs Spout 및 Bolt 구성 요소를 다운로드합니다.

`eventhubspout`라는 디렉터리를 만들고 이 디렉터리에 파일을 저장합니다.

## <a name="configure-event-hubs"></a>Event Hubs 구성

Event Hubs는 이 예제의 데이터 원본입니다. [Event Hubs 시작](../../event-hubs/event-hubs-create.md)의 "이벤트 허브 만들기" 섹션에 있는 정보를 참조하세요.

1. 이벤트 허브가 생성된 후에는 Azure Portal에서 **EventHub** 설정을 보고 **공유 액세스 정책**을 선택합니다. **+ 추가**를 선택하여 다음 정책을 추가합니다.

   | 이름 | 권한 |
   | --- | --- |
   | 기록기 |보내기 |
   | 판독기 |수신 대기 |

    ![공유 액세스 정책 창의 스크린샷](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. **판독기** 및 **기록기** 정책을 선택합니다. 이러한 값은 나중에 사용되므로 두 정책에 대한 기본 키 값을 복사 및 저장합니다.

## <a name="configure-the-eventhubwriter"></a>EventHubWriter 구성

1. 최신 버전의 HDInsight Tools for Visual Studio를 아직 설치하지 않은 경우 [HDInsight Tools for Visual Studio 사용 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

2. [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)에서 솔루션을 다운로드합니다.

3. **EventHubWriter** 프로젝트에서 **App.config** 파일을 엽니다. 앞에서 구성한 이벤트 허브에 대한 정보를 다음 키에 대한 값에 입력합니다.

   | 키 | 값 |
   | --- | --- |
   | EventHubPolicyName |기록기(*보내기* 권한이 있는 정책에 다른 이름을 사용한 경우 대신 사용) |
   | EventHubPolicyKey |기록기 정책에 대한 키 |
   | EventHubNamespace |이벤트 허브가 포함된 네임스페이스 |
   | EventHubName |이벤트 허브 이름 |
   | EventHubPartitionCount |이벤트 허브의 파티션 수 |

4. **App.config** 파일을 저장하고 닫습니다.

## <a name="configure-the-eventhubreader"></a>EventHubReader 구성

1. **EventHubReader** 프로젝트를 엽니다.

2. **EventHubReader**에 대한 **App.config** 파일을 엽니다. 앞에서 구성한 이벤트 허브에 대한 정보를 다음 키에 대한 값에 입력합니다.

   | 키 | 값 |
   | --- | --- |
   | EventHubPolicyName |판독기(*수신 대기* 권한이 있는 정책에 다른 이름을 사용한 경우 대신 사용) |
   | EventHubPolicyKey |판독기 정책에 대한 키 |
   | EventHubNamespace |이벤트 허브가 포함된 네임스페이스 |
   | EventHubName |이벤트 허브 이름 |
   | EventHubPartitionCount |이벤트 허브의 파티션 수 |

3. **App.config** 파일을 저장하고 닫습니다.

## <a name="deploy-the-topologies"></a>토폴로지 배포

1. **솔루션 탐색기**에서 **EventHubReader** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight의 Storm에 제출**을 선택합니다.

    ![HDInsight의 Storm에 제출이 강조 표시된 솔루션 탐색기 스크린샷](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. **토폴로지 제출** 대화 상자에서 **Storm 클러스터**를 선택합니다. **추가 구성**을 확장하고 **Java 파일 경로**, **...** 을 차례로 선택한 다음 이전에 다운로드한 JAR 파일이 포함된 디렉터리를 선택합니다. 마지막으로 **제출**을 클릭합니다.

    ![토폴로지 제출 대화 상자의 스크린샷](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. 토폴로지가 제출되었으면 **Storm Topologies Viewer** 가 나타납니다. 토폴로지 정보를 보려면 왼쪽 창에서 **EventHubReader** 토폴로지를 선택합니다.

    ![Storm Topologies Viewer의 스크린샷](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. **솔루션 탐색기**에서 **EventHubWriter** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight의 Storm에 제출**을 선택합니다.

5. **토폴로지 제출** 대화 상자에서 **Storm 클러스터**를 선택합니다. **추가 구성**을 확장하고 **Java 파일 경로**, **...** 을 차례로 선택한 다음 이전에 다운로드한 JAR 파일이 포함된 디렉터리를 선택합니다. 마지막으로 **제출**을 클릭합니다.

6. 토폴로지가 제출되었으면 **Storm Topologies Viewer** 에서 토폴로지 목록을 새로 고쳐 두 토폴로지 모두 클러스터에서 실행되는지 확인합니다.

7. **Storm Topologies Viewer**에서 **EventHubReader** 토폴로지를 선택합니다.

8. Bolt에 대한 구성 요소 요약을 열려면 다이어그램에서 **LogBolt** 구성 요소를 두 번 클릭합니다.

9. **Executors** 섹션에서 **Port** 열의 링크 중 하나를 선택합니다. 그러면 구성 요소가 기록한 정보가 표시됩니다. 기록된 정보는 다음 텍스트와 유사합니다.

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>토폴로지 중지

토폴로지를 중지하려면 **Storm Topology Viewer**에서 각 토폴로지를 선택한 다음 **중단**을 클릭합니다.

![종료 단추가 강조 표시된 Storm Topologies Viewer의 스크린샷](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 C# 토폴로지에서 Java Event Hubs Spout 및 Bolt를 사용하여 Azure Event Hubs의 데이터로 작업하는 방법을 배웠습니다. C# 토폴로지 만들기에 대해 자세히 알아보려면 다음을 참조하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP 프로그래밍 가이드](apache-storm-scp-programming-guide.md)
* [HDInsight의 Apache Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)
