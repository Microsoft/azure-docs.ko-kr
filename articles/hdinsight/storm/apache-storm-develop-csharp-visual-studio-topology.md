---
title: Visual Studio 및 C#을 사용하는 Apache Storm 토폴로지 - Azure HDInsight
description: C#으로 Storm 토폴로지를 만드는 방법을 알아봅니다. Visual Studio 용 Hadoop 도구를 사용하여 Visual Studio에서 간단한 단어 카운트 토폴로지를 만듭니다.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: ''
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 11/27/2017
ms.date: 04/01/2019
ms.author: v-yiso
ms.openlocfilehash: 14aa45808f44f7ca6fe34b70ef282a99f230bf0d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125224"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Data Lake tools for Visual Studio를 사용하여 Apache Storm의 C# 토폴로지 개발

Visual Studio용 Azure Data Lake(Apache Hadoop) 도구를 사용하여 C# Apache Storm 토폴로지를 만드는 방법을 알아봅니다. 이 문서는 Visual Studio에서 Storm 프로젝트를 만들고, 프로젝트를 로컬로 테스트하고, Azure HDInsight 클러스터에서 Apache Storm에 배포하는 과정을 보여 줍니다.

또한 C# 및 Java 구성 요소를 사용하는 하이브리드 토폴로지를 만드는 방법에 대해서도 배웁니다.

> [!NOTE]
> 이 문서의 단계는 Visual Studio가 설치된 Windows 개발 환경을 사용하지만 컴파일된 프로젝트는 Linux 또는 Windows 기반 HDInsight 클러스터로 전송될 수 있습니다. 2016년 10월 28일 이후에 만든 Linux 기반 클러스터만 SCP.NET 토폴로지를 지원합니다.

Linux 기반 클러스터에 C# 토폴로지를 사용하려면 프로젝트에 사용되는 Microsoft.SCP.Net.SDK NuGet 패키지를 0.10.0.6 버전 이상으로 업데이트해야 합니다. 패키지 버전은 HDInsight에 설치된 Storm의 주 버전과도 일치해야 합니다.

| HDInsight 버전 | Apache Storm 버전 | SCP.NET 버전 | 기본 모노 버전 |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(Windows 기반 HDInsight만 해당) | 해당 없음 |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> Linux 기반 클러스터의 C# 토폴로지는 .NET 4.5를 사용해야 하며 Mono를 사용하여 HDInsight 클러스터에서 실행해야 합니다. [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)에서 잠재적인 비호환성을 확인하세요.

## <a name="install-visual-studio"></a>Visual Studio 설치

다음 버전의 Visual Studio 중 하나를 사용하여 SCP.NET으로 C# 토폴로지를 개발할 수 있습니다.

* Visual Studio 2012 업데이트 4

* Visual Studio 2013 업데이트 4 또는 [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 또는 [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017(모든 버전)

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio 설치

Data Lake tools for Visual Studio를 설치하려면 [Data Lake tools for Visual Studio 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

## <a name="install-java"></a>Java 설치

Visual Studio에서 Storm 토폴로지를 제출하면 SCP.NET은 토폴로지 및 종속성이 포함된 zip 파일을 생성합니다. Java는 Linux 기반 클러스터와 호환이 더 잘되는 형식을 사용하기 때문에 이러한 zip 파일을 만드는 데 사용됩니다.

1. 개발 환경에 JDK(Java Developer Kit) 7 이상을 설치합니다. Oracle JDK는 [Oracle](https://aka.ms/azure-jdks)에서 가져올 수 있습니다. [다른 Java 배포](https://openjdk.java.net/)를 사용할 수도 있습니다.

2. `JAVA_HOME` 환경 변수가 Java가 포함된 디렉터리를 가리켜야 합니다.

3. `PATH` 환경 변수는 `%JAVA_HOME%\bin` 디렉터리를 포함해야 합니다.

다음 C# 콘솔 애플리케이션을 사용하여 Java 및 JDK가 올바르게 설치되었는지 확인할 수 있습니다.

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm 템플릿

Data Lake Tools for Visual Studio는 다음 템플릿을 제공합니다.

| 프로젝트 형식 | 데모 |
| --- | --- |
| Storm 애플리케이션 |빈 Storm 토폴로지 프로젝트 |
| Storm Azure SQL 기록기 샘플 |Azure SQL Database에 쓰는 방법 |
| Storm Azure Cosmos DB 판독기 샘플 |Azure Cosmos DB에서 읽는 방법 |
| Storm Azure Cosmos DB 기록기 샘플 |Azure Cosmos DB에 기록하는 방법 |
| Storm 이벤트 허브 판독기 샘플 |Azure Event Hubs에서 읽는 방법 |
| Storm 이벤트 허브 기록기 샘플 |Azure Event Hubs에 쓰는 방법 |
| Storm HBase 판독기 샘플 |HDInsight 클러스터의 HBase에서 읽는 방법 |
| Storm HBase 기록기 샘플 |HDInsight 클러스터의 HBase에 쓰는 방법 |
| Storm 하이브리드 샘플 |Java 구성 요소를 사용하는 방법 |
| Storm 샘플 |기본 단어 카운트 토폴로지 |

> [!WARNING]  
> 일부 템플릿은 Linux 기반 HDInsight에서 작동합니다. 템플릿에서 사용하는 NuGet 패키지는 Mono와 호환되지 않을 수 있습니다. [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/) 문서를 확인하고 [.NET 이식성 분석기](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)를 사용하여 잠재적 문제를 식별합니다.

이 문서의 단계에서는 기본 Storm 애플리케이션 프로젝트 형식을 사용하여 토폴로지를 만들 수 있습니다.

### <a name="apache-hbase-templates-notes"></a>Apache HBase 템플릿 정보

HBase 판독기 및 기록기 템플릿은 HBase Java API가 아니라 HBase REST API를 사용하여 HDInsight 클러스터의 HBase와 통신합니다.

### <a name="eventhub-templates-notes"></a>EventHub 템플릿 정보

> [!IMPORTANT]
> EventHub 판독기 템플릿에 포함된 Java 기반 EventHub spout 구성 요소는 HDInsight 버전 3.5 이상의 Storm에서 작동하지 않을 수 있습니다. 이 구성 요소의 업데이트 된 버전은 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib)에 있습니다.

이 구성 요소를 사용하고 HDInsight 3.5의 Storm에서 작동하는 토폴로지의 예는 [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)를 참조하세요.

## <a name="create-a-c-topology"></a>C# 토폴로지 만들기

1. Visual Studio를 열고 **파일** > **새로 만들기**를 선택한 다음 **프로젝트**를 선택합니다.

2. **새 프로젝트** 창에서 **설치됨** > **템플릿**을 확장하고 **Azure Data Lake**를 선택합니다. 템플릿 목록에서 **Storm 애플리케이션**을 선택합니다. 화면 아래쪽에서 애플리케이션 이름으로 **WordCount**를 입력합니다.

    ![새 프로젝트 창의 스크린샷](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. 프로젝트를 만든 후에는 다음 파일이 있어야 합니다.

   * **Program.cs**: 이 파일은 프로젝트에 대한 토폴로지를 정의합니다. 기본적으로 하나의 Spout 및 하나의 Bolt로 구성된 기본 토폴로지가 만들어집니다.

   * **Spout.cs**: 난수를 내보내는 예제 Spout입니다.

   * **Bolt.cs**: Spout에서 내보낸 숫자의 개수를 유지하는 예제 Bolt입니다.

     프로젝트를 만들면 NuGet은 최신 [SCP.NET 패키지](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)를 다운로드합니다.

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>spout 만들기

1. **Spout.cs**를 엽니다. Spout는 외부 소스에서 토폴로지로 데이터를 읽는 데 사용됩니다. spout의 주요 구성 요소:

   * **NextTuple**: Spout에서 새 튜플을 내보낼 수 있도록 허용되면 Storm이 호출합니다.

   * **Ack**(트랜잭션 토폴로지만 해당): Spout에서 보낸 튜플의 경우 토폴로지에서 다른 구성 요소에 의해 시작된 승인을 처리합니다. 튜플을 승인하면 spout가 다운스트림 구성 요소에 의해 성공적으로 처리되었음을 알 수 있습니다.

   * **Fail**(트랜잭션 토폴로지만 해당): 토폴로지에서 다른 구성 요소 처리에 실패한 튜플을 처리합니다. Fail 메서드를 구현하면 튜플을 다시 처리할 수 있도록 튜플을 다시 내보낼 수 있습니다.

2. **Spout** 클래스의 내용을 다음 텍스트로 바꿉니다. 이 Spout는 문장을 토폴로지에 임의로 내보냅니다.

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>bolt 구현

1. 프로젝트에서 기존 **Bolt.cs** 를 삭제합니다.

2. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 선택합니다. 목록에서 **Storm Bolt**를 선택하고 이름으로 **Splitter.cs**를 입력합니다. 이 프로세스를 반복하여 **Counter.cs**라는 두 번째 Bolt를 만듭니다.

   * **Splitter.cs**: 문장을 개별 단어로 분리하는 Bolt를 구현하고 새로운 단어 스트림을 내보냅니다.

   * **Counter.cs**: 각 단어를 세는 Bolt를 구현하고 새로운 단어 스트림과 각 단어의 수를 내보냅니다.

     > [!NOTE]
     > 이러한 Bolt는 스트림을 읽고 쓰지만 데이터베이스 또는 서비스와 같은 소스와 통신하는 데 Bolt를 사용할 수 있습니다.

3. **Splitter.cs**를 엽니다. 기본적으로 **Execute**라는 하나의 메서드만 있습니다. Execute 메서드는 Bolt가 처리에 대한 튜플을 받을 때 호출됩니다. 여기에서 들어오는 튜플을 읽고 처리하며 나가는 튜플을 내보낼 수 있습니다.

4. **Splitter** 클래스의 내용을 다음 코드로 바꿉니다.

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. **Counter.cs**를 열고 클래스 내용을 다음 코드로 바꿉니다.

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>토폴로지 정의

Spout 및 bolt는 그래프로 정렬되며, 이는 구성 요소 간에 데이터의 흐름 방식을 정의합니다. 이 토폴로지의 경우 그래프는 다음과 같습니다.

![구성 요소 정렬 방식 다이어그램](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

문장이 Spout에서 내보내지며 Splitter Bolt의 인스턴스에 배포됩니다. Splitter bolt는 Counter bolt로 배포된 단어로 문장을 나눕니다.

단어 개수가 Counter 인스턴스에서 로컬로 처리되기 때문에 특정 단어가 동일한 Counter bolt 인스턴스로 전달되도록 하고자 합니다. 각 인스턴스는 특정 단어를 계속 추적합니다. Splitter bolt는 상태를 유지하지 않으므로 실제로 splitter의 어떤 인스턴스가 어떤 문장을 받는지 중요하지 않습니다.

**Program.cs**를 엽니다. 중요한 메서드는 Storm에 제출되는 토폴로지를 정의하는 데 사용하는 **GetTopologyBuilder**입니다. 위에서 설명한 토폴로지를 구성하려면 **GetTopologyBuilder**의 내용을 다음 코드로 바꿉니다.

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>토폴로지 제출

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.

   > [!NOTE]
   > 메시지가 표시되면 Azure 구독에 대한 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

2. **Storm 클러스터** 드롭다운 목록에서 HDInsight의 Storm 클러스터를 선택한 다음 **제출**을 선택합니다. **출력** 창을 통해 제출의 성공 여부를 모니터링할 수 있습니다.

3. 토폴로지 제출에 성공하면 클러스터에 대한 **Storm 토폴로지** 가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 **WordCount** 토폴로지를 선택합니다.

   > [!NOTE]
   > **서버 탐색기**에서 **Storm 토폴로지**를 볼 수도 있습니다. **Azure** > **HDInsight**를 확장하고 HDInsight 클러스터에서 Storm을 마우스 오른쪽 단추로 클릭한 다음 **Storm 토폴로지 보기**를 선택합니다.

    토폴로지에서 구성 요소에 대한 정보를 보려면 다이어그램에서 구성 요소를 두 번 클릭합니다.

4. 토폴로지를 중단하려면 **토폴로지 요약** 보기에서 **중단**을 클릭합니다.

   > [!NOTE]
   > Storm 토폴로지는 비활성화되거나 클러스터가 삭제될 때까지 계속 실행됩니다.

## <a name="transactional-topology"></a>트랜잭션 토폴로지

이전 토폴로지는 비트랜잭션입니다. 토폴로지의 구성 요소는 메시지 재생 기능을 구현하지 않습니다. 트랜잭션 토폴로지의 예를 보려면 프로젝트를 만들고 프로젝트 형식으로 **Storm 샘플**을 선택합니다.

트랜잭션 토폴로지는 데이터 재생을 지원하기 위해 다음과 같이 구현합니다.

* **메타데이터 캐싱**: Spout는 내보낸 데이터에 대한 메타데이터를 저장해야 하므로 오류가 발생하는 경우 해당 데이터를 검색하고 다시 내보낼 수 있습니다. 샘플로 내보낸 데이터는 작기 때문에 각 튜플의 원시 데이터는 재생을 위해 사전에 저장됩니다.

* **Ack**: 토폴로지의 각 Bolt는 `this.ctx.Ack(tuple)`를 호출하여 튜플을 성공적으로 처리했음을 승인할 수 있습니다. 모든 bolt가 튜플을를 승인 합니다 `Ack` spout의 메서드가 호출 됩니다. `Ack` 메서드를 통해 Spout가 재생용으로 캐시된 데이터를 제거할 수 있습니다.

* **Fail**: 각 Bolt는 `this.ctx.Fail(tuple)`을 호출하여 튜플 처리에 실패했음을 나타냅니다. 캐시된 메타데이터를 사용하여 튜플을 재생할 수 있는 Spout의 `Fail` 메서드로 오류가 전파됩니다.

* **시퀀스 ID**: 튜플을 내보낼 때 고유한 시퀀스 ID를 지정할 수 있습니다. 이 값은 재생(Ack 및 Fail) 처리를 위해 튜플을 식별합니다. 예를 들어 **Storm 샘플** 프로젝트의 Spout는 데이터를 내보낼 때 다음과 같이 사용합니다.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    이 코드는 **lastSeqId**에 포함된 시퀀스 ID 값과 함께 문장을 포함하는 튜플을 기본 스트림으로 내보냅니다. 이 예제에서 **lastSeqId** 는 제출된 모든 튜플마다 증가합니다.

**Storm 샘플** 프로젝트에 설명된 대로 구성 요소가 트랜잭션인지 여부는 구성에 따라 런타임으로 설정할 수 있습니다.

## <a name="hybrid-topology-with-c-and-java"></a>C# 및 Java를 통한 하이브리드 토폴로지

또한 Data Lake Tools for Visual Studio를 사용하여 일부 구성 요소가 C#이고 다른 구성 요소가 Java인 하이브리드 토폴로지를 만들 수 있습니다.

하이브리드 토폴로지의 예를 보려면 프로젝트를 만들고 **Storm 하이브리드 샘플**을 선택합니다. 이 샘플 형식은 다음 개념을 보여 줍니다.

* **Java Spout** 및 **C# Bolt**: **HybridTopology_javaSpout_csharpBolt**에서 정의됩니다.

    * 트랜잭션 버전은 **HybridTopologyTx_javaSpout_csharpBolt**에서 정의됩니다.

* **C# Spout** 및 **Java Bolt**: **HybridTopology_csharpSpout_javaBolt**에서 정의됩니다.

    * 트랜잭션 버전은 **HybridTopologyTx_csharpSpout_javaBolt**에서 정의됩니다.

  > [!NOTE]
  > 이 버전은 텍스트 파일에서 Clojure 코드를 Java 구성 요소로 사용하는 방법을 설명하기도 합니다.


프로젝트가 제출될 때 사용된 토폴로지를 전환하려면 클러스터에 제출하기 전에 사용하려는 토폴로지로 `[Active(true)]` 문을 이동합니다.

> [!NOTE]
> 필요한 모든 Java 파일이 **JavaDependency** 폴더에서 이 프로젝트의 일부로 제공됩니다.

다음은 하이브리드 토폴로지를 만들고 제출할 때 고려할 사항입니다.

* Spout 또는 Bolt에 대한 Java 클래스의 인스턴스를 만들려면 **JavaComponentConstructor**를 사용합니다.

* Java 개체에서 JSON으로 Java 구성 요소에서 데이터를 직렬화하려면 **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer**를 사용합니다.

* 서버에 토폴로지를 제출할 때 **Java 파일 경로**를 지정하려면 **추가 구성** 옵션을 사용해야 합니다. 지정된 경로는 Java 클래스를 포함하는 JAR 파일이 들어 있는 디렉터리여야 합니다.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.Net 버전 0.9.4.203은 Event Hub Spout(Event Hubs로부터 읽는 Java Spout)로 작업하기 위한 새로운 클래스와 메서드를 소개합니다. Event Hub Spout를 사용하는 토폴로지를 만들 때는 다음 메서드를 사용합니다.

* **EventHubSpoutConfig** 클래스: Spout 구성 요소에 대한 구성이 포함된 개체를 만듭니다.

* **TopologyBuilder.SetEventHubSpout** 메서드: 토폴로지에 이벤트 허브 Spout 구성 요소를 추가합니다.

> [!NOTE]
> 여전히 **CustomizedInteropJSONSerializer**를 사용하여 Spout에 의해 생성된 데이터를 직렬화해야 합니다.

## <a id="configurationmanager"></a>ConfigurationManager 사용

Bolt 및 Spout 구성 요소에서 구성 값을 검색하는 데 **ConfigurationManager**를 사용하지 마세요. 이렇게 하면 null 포인터 예외가 발생할 수 있습니다. 대신, 프로젝트에 대한 구성이 토폴로지 컨텍스트의 키 및 값 쌍으로 Storm 토폴로지에 전달됩니다. 구성 값을 사용하는 각 구성 요소는 초기화 중에 컨텍스트에서 값을 검색해야 합니다.

다음 코드에서는 이러한 값을 검색하는 방법을 보여 줍니다.

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

`Get` 메서드를 사용하여 구성 요소의 인스턴스를 반환하는 경우 `Context` 및 `Dictionary<string, Object>` 매개 변수가 생성자에 모두 전달되는지 확인해야 합니다. 다음 예제는 이러한 값을 적절히 전달하는 기본 `Get` 메서드입니다.

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>SCP.NET 업데이트 방법

SCP.NET의 최신 릴리스는 NuGet을 통해 패키지 업그레이드를 지원합니다. 새 업데이트를 사용할 수 있을 때 업그레이드 알림을 받게 됩니다. 업그레이드를 수동으로 확인하려면 다음 단계를 수행합니다.

1. **솔루션 탐색기**에서 프로젝트의 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 패키지 관리자에서 **업데이트**를 선택합니다. 사용할 수 있는 업데이트가 나열됩니다. 설치하려는 패키지의 **업데이트**를 클릭합니다.

> [!IMPORTANT]
> NuGet을 사용하지 않는 SCP.NET의 이전 버전으로 프로젝트를 만든 경우 다음 단계를 수행하여 최신 버전으로 업데이트해야 합니다.
>
> 1. **솔루션 탐색기**에서 프로젝트의 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
> 2. **검색** 필드를 사용하여 검색한 다음 **Microsoft.SCP.Net.SDK**를 프로젝트에 추가합니다.

## <a name="troubleshoot-common-issues-with-topologies"></a>토폴로지의 일반적인 문제 해결

### <a name="null-pointer-exceptions"></a>Null 포인터 예외

Linux 기반 HDInsight 클러스터에서 C# 토폴로지를 사용하는 경우 런타임에 구성 설정을 읽는 데 **ConfigurationManager**를 사용하는 Bolt 및 Spout 구성 요소에 Null 포인터 예외가 반환될 수 있습니다.

프로젝트에 대한 구성이 토폴로지 컨텍스트의 키 및 값 쌍으로 Storm 토폴로지에 전달됩니다. 구성 요소가 초기화될 때 구성 요소에 전달된 디렉터리 개체에서 검색할 수 있습니다.

자세한 내용은 이 문서의 [ConfigurationManager](#configurationmanager) 섹션을 참조하세요.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Linux 기반 HDInsight 클러스터에서 C# 토폴로지를 사용하는 경우 다음 오류가 발생할 수 있습니다.

    System.TypeLoadException: Failure has occurred while loading a type.

이 오류는 Mono에서 지원하는 .NET 버전과 호환되지 않는 이진 파일을 사용할 때 발생합니다.

Linux 기반 HDInsight 클러스터의 경우 프로젝트에서 .NET 4.5에 대해 컴파일된 이진 파일을 사용하는지 확인하십시오.

### <a name="test-a-topology-locally"></a>로컬로 토폴로지 테스트

토폴로지를 클러스터로 배포하는 것은 쉽지만, 일부 경우에 토폴로지를 로컬로 테스트해야 할 수 있습니다. 이 자습서의 예제 토폴로지를 사용자의 개발 환경에서 로컬로 실행 및 테스트하려면 다음 단계를 참조하세요.

> [!WARNING]
> 로컬 테스트는 기본 C# 전용 토폴로지에서만 작동합니다. 하이브리드 토폴로지나 여러 스트림을 사용하는 토폴로지에는 로컬 테스트를 사용할 수 없습니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 프로젝트 속성에서 **출력 유형**을 **콘솔 애플리케이션**으로 변경합니다.

    ![출력 유형이 강조 표시된 프로젝트 속성의 스크린샷](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > 토폴로지를 클러스터로 배포하기 전에 **출력 유형**을 **클래스 라이브러리**로 다시 변경해야 합니다.

2. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **추가** > **새 항목**을 차례로 선택합니다. **클래스**를 선택하고 클래스 이름으로 **LocalTest.cs**를 입력합니다. 마지막으로 **추가**를 클릭합니다.

3. **LocalTest.cs**를 열고 다음 **using** 문을 맨 위에 추가합니다.

    ```csharp
    using Microsoft.SCP;
    ```

4. **LocalTest** 클래스의 내용으로 다음 코드를 사용합니다.

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    코드 주석을 읽어보세요. 이 코드는 **LocalContext**를 사용하여 개발 환경에서 구성 요소를 실행하며, 구성 요소 간의 데이터 스트림을 로컬 드라이브에 텍스트 파일로 유지합니다.

1. **Program.cs**를 열고 **Main** 메서드에 다음을 추가합니다.

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. 변경 내용을 저장한 다음 **F5**를 클릭하거나 **디버그** > **디버깅 시작**을 선택하여 프로젝트를 시작합니다. 콘솔 창이 나타나며 로그 상태가 테스트로 진행됩니다. **테스트 완료** 가 나타나면 아무 키나 눌러 창을 닫습니다.

3. **Windows 탐색기**를 사용하여 프로젝트가 포함된 디렉터리를 찾습니다. 예를 들면 다음과 같습니다. **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. 이 디렉터리에서 **Bin**을 열고 **디버그**를 클릭합니다. 테스트가 실행될 때 생성된 텍스트 파일(sentences.txt, counter.txt 및 splitter.txt)이 표시됩니다. 각각의 텍스트 파일을 열고 데이터를 검사합니다.

   > [!NOTE]
   > 문자열 데이터는 이러한 파일에 10진수 값의 배열로 유지됩니다. 예를 들어, **splitter.txt** 파일의 \[[97,103,111]]은 *and*라는 단어입니다.

> [!NOTE]
> HDInsight의 Storm으로 배포하기 전에 **프로젝트 유형**을 **클래스 라이브러리**로 다시 설정해야 합니다.

### <a name="log-information"></a>로그 정보

`Context.Logger`를 사용하여 토폴로지 구성 요소에서 정보를 쉽게 로깅할 수 있습니다. 예를 들어 다음 명령은 정보 제공용 로그 항목을 만듭니다.

```csharp
Context.Logger.Info("Component started");
```

로깅된 정보는 **서버 탐색기**에서 찾을 수 있는 **Hadoop 서비스 로그**에서 볼 수 있습니다. HDInsight의 Storm 클러스터에 대한 항목을 확장한 다음 **Hadoop 서비스 로그**를 확장합니다. 마지막으로 보려는 로그 파일을 선택합니다.

> [!NOTE]
> 로그는 사용자의 클러스터에서 사용되는 Azure 스토리지 계정에 저장됩니다. Visual Studio에서 로그를 보려면 스토리지 계정을 소유하는 Azure 구독에 로그인해야 합니다.

### <a name="view-error-information"></a>오류 정보 보기

실행 중인 토폴로지에서 발행한 오류를 보려면 다음 단계를 따릅니다.

1. **서버 탐색기**에서 HDInsight의 Storm 클러스터를 마우스 오른쪽 단추로 클릭하고 **Storm 토폴로지 보기**를 선택합니다.

2. **Spout** 및 **Bolts**의 경우 **마지막 오류** 열에 마지막 오류에 대한 정보가 있습니다.

3. 오류가 나열된 구성 요소의 **Spout Id** 또는 **Bolt Id**를 선택합니다. 표시되는 세부 사항 페이지에서 추가 오류 정보는 페이지 아래쪽의 **오류** 섹션에 나열됩니다.

4. 더 많은 정보를 얻으려면 페이지의 **실행기** 섹션에서 **포트**를 선택하여 지난 몇 분 동안의 Storm 작업자 로그를 확인하십시오.

### <a name="errors-submitting-topologies"></a>토폴로지 제출 중 오류

HDInsight에 토폴로지를 제출하는 중에 오류가 발생하면 HDInsight 클러스터에서 토폴로지 제출을 처리하는 서버 쪽 구성 요소에 대한 로그를 찾아볼 수 있습니다. 이러한 로그를 검색하려면 명령줄에서 다음 명령을 사용합니다.

    scp sshuser@clustername-ssh.azurehdinsight.cn:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

__sshuser__를 클러스터의 SSH 사용자 계정으로 바꿉니다. __clustername__을 HDInsight 클러스터의 이름으로 바꿉니다. HDInsight에서 `scp` 및 `ssh` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

여러 가지 이유로 제출이 실패할 수 있습니다.

* JDK가 설치되지 않았거나 경로에 없습니다.
* 필요한 Java 종속성이 제출에 포함되지 않습니다.
* 종속성이 호환되지 않습니다.
* 토폴로지 이름이 중복되었습니다.

`hdinsight-scpwebapi.out` 로그에 `FileNotFoundException`이 포함되어 있으면 다음 조건에 의해 발생할 수 있습니다.

* JDK가 개발 환경의 경로에 없습니다. JDK가 개발 환경에 설치되어 있고 `%JAVA_HOME%/bin`이 경로에 있는지 확인합니다.
* Java 종속성이 누락되었습니다. 제출할 때 필요한 .jar 파일을 포함시켜야 합니다.

## <a name="next-steps"></a>다음 단계

Event Hubs에서 데이터를 처리하는 방법의 예는 [HDInsight의 Storm으로 Azure Event Hubs에서 이벤트 처리](apache-storm-develop-csharp-event-hub-topology.md)를 참조하세요.

스트림 데이터를 여러 스트림으로 분할하는 C# 토폴로지의 예제는 [C# Storm 예제](https://github.com/Blackmist/csharp-storm-example)를 참조하세요.

C# 토폴로지 생성에 대한 자세한 내용은 [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)를 참조하세요.

HDInsight 사용 방법 및 HDInsight의 Storm에 대한 추가 샘플은 다음 문서를 참조하세요.

**Microsoft SCP.NET**

* [SCP 프로그래밍 가이드](apache-storm-scp-programming-guide.md)

**HDInsight의 Apache Storm**

* [HDInsight에서 Apache Storm을 사용하는 토폴로지 배포 및 모니터링](apache-storm-deploy-monitor-topology.md)
* [HDInsight의 Apache Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)

**HDInsight의 Apache Hadoop**

* [HDInsight에서 Apache Hadoop과 함께 Apache Hive 사용](../hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Pig 사용](../hadoop/hdinsight-use-pig.md)
* [HDInsight에서 Apache Hadoop과 함께 Apache Hadoop MapReduce 사용](../hadoop/hdinsight-use-mapreduce.md)

**HDInsight의 Apache HBase**

* [HDInsight에서 Apache HBase 시작](../hbase/apache-hbase-tutorial-get-started-linux.md)
