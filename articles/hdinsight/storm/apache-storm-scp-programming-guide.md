---
title: Azure HDInsight의 Storm에 대한 SCP.NET 프로그래밍 가이드
description: Azure HDInsight에서 실행되는 Storm과 함께 사용할 수 있도록 SCP.NET을 사용하여 .NET 기반 Storm 토폴로지를 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186872"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight의 Apache Storm에 대 한 SCP 프로그래밍 가이드

SCP는 실시간이 고 안정적 이며 일관 된 고성능 데이터 처리 응용 프로그램을 빌드하기 위한 플랫폼입니다. 오픈 소스 소프트웨어 커뮤니티에서 설계 된 스트림 처리 시스템 인 [Apache Storm](https://storm.incubator.apache.org/)위에 빌드됩니다. 네. Twitter에서 오픈 소스로 게시 되었습니다. 스톰은 매우 안정적인 분산 조정 및 상태 관리를 가능 하 게 하는 다른 Apache 프로젝트인 [Apache ZooKeeper](https://zookeeper.apache.org/)를 사용 합니다.

SCP 프로젝트는 Windows의 스톰 뿐만 아니라 Windows 환경에 대 한 프로젝트 추가 확장 및 사용자 지정을 통해 이식 되었습니다. 확장에는 .NET 개발자 환경 및 .NET 라이브러리가 포함 됩니다. 사용자 지정에는 Windows 기반 배포가 포함 됩니다.

확장 및 사용자 지정을 사용 하 여 오픈 소스 소프트웨어 프로젝트를 포크 하지 않아도 됩니다. 스톰 위에 구축 된 파생 환경을 사용할 수 있습니다.

## <a name="processing-model"></a>처리 모델

SCP의 데이터는 튜플의 연속 스트림으로 모델링됩니다. 일반적으로 튜플을 다음과 같이 합니다.

1. 큐로 이동 합니다.
1. 는 스톰 토폴로지 내에서 호스트 되는 비즈니스 논리에 의해 선택 되 고 변환 됩니다.
1. 다른 SCP 시스템에 튜플로 파이프 하 여 출력을 파이프 하거나 분산 파일 시스템 및 SQL Server 같은 데이터베이스와 같은 저장소에 커밋됩니다.

![처리할 데이터를 공급 하는 큐의 다이어그램으로 데이터 저장소를 피드 합니다.](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

스톰에서 응용 프로그램 토폴로지는 계산 그래프를 정의 합니다. 토폴로지의 각 노드에는 처리 논리가 포함 됩니다. 노드 간의 링크는 데이터 흐름을 표시 합니다.

토폴로지에 입력 데이터를 삽입 하는 노드를 _spout_이라고 합니다. 데이터를 시퀀싱 하는 데 사용할 수 있습니다. 입력 데이터는 파일 로그, 트랜잭션 데이터베이스, 시스템 성능 카운터 등의 원본에서 가져올 수 있습니다.

입력 및 출력 데이터 흐름이 모두 있는 노드를 _볼트_라고 합니다. 실제 데이터 필터링, 선택 및 집계를 수행 합니다.

SCP는 데이터를 한 번 이상 처리 하는 최상의 노력을 지원 합니다.

분산 스트림 처리 응용 프로그램에서는 데이터를 처리 하는 동안 오류가 발생할 수 있습니다. 이러한 오류에는 네트워크 중단, 컴퓨터 오류 또는 코드 오류 등이 포함 됩니다. 한 번 이상 처리 하면 오류가 발생할 때 동일한 데이터를 자동으로 재생 하 여 모든 데이터가 한 번 이상 처리 되도록 합니다.

최소 한 번 처리는 간단 하 고 안정적 이며 많은 응용 프로그램에 적합 합니다. 그러나 응용 프로그램에서 정확한 계산을 수행 해야 하는 경우에는 응용 프로그램 토폴로지에서 같은 데이터가 재생 될 수 있으므로 최소 한 번 처리에 충분 하지 않습니다. 이 경우 정확히 한 번 처리를 수행 하면 데이터가 여러 번 재생 되 고 처리 되는 경우에도 결과가 정확 하 게 유지 됩니다.

.NET 개발자는 SCP를 통해 JVM (Java Virtual Machine)을 사용 하면서 실시간 데이터 처리 응용 프로그램을 만들 수 있습니다. JVM 및 .NET은 TCP 로컬 소켓을 통해 통신 합니다. 각 spout/볼트는 .net/Java 프로세스 쌍으로, 사용자 논리는 .NET 프로세스에서 플러그 인으로 실행 됩니다.

SCP 위에 데이터 처리 응용 프로그램을 빌드하려면 다음 단계를 수행 합니다.

1. Spout을 설계 하 고 구현 하 여 큐에서 데이터를 가져옵니다.
1. 입력 데이터를 처리 하 고 데이터베이스와 같은 외부 저장소에 저장 하는 볼트를 디자인 하 고 구현 합니다.
1. 토폴로지를 디자인 한 다음 제출 하 고 실행 합니다.

토폴로지는 꼭 짓 점 및 서로 흐르는 데이터를 정의 합니다. SCP는 토폴로지 사양을 사용 하 여 각 꼭 짓 점이 하나의 논리 노드에서 실행 되는 스톰 클러스터에 배포 합니다. 스톰 작업 스케줄러는 장애 조치 (failover) 및 크기 조정을 담당 합니다.

이 문서에서는 몇 가지 간단한 예제를 사용 하 여 SCP로 데이터 처리 응용 프로그램을 빌드하는 방법을 안내 합니다.

## <a name="scp-plug-in-interface"></a>SCP 플러그 인 인터페이스

SCP 플러그 인은 독립 실행형 응용 프로그램입니다. 개발 중에 Visual Studio 내에서 실행 되 고 프로덕션 배포 후에는 스톰 파이프라인에 연결 될 수 있습니다.

SCP 플러그 인을 작성 하는 것은 다른 Windows 콘솔 응용 프로그램을 작성 하는 것과 같습니다. SCP.NET 플랫폼은 spout/볼트에 대 한 일부 인터페이스를 선언 합니다. 플러그 인 코드는 이러한 인터페이스를 구현 합니다. 이 디자인의 주요 목적은 SCP.NET 플랫폼에서 다른 작업을 처리할 수 있도록 하는 동시에 비즈니스 논리에 집중할 수 있도록 하는 것입니다.

플러그 인 코드는 다음 인터페이스 중 하나를 구현 합니다. 토폴로지가 트랜잭션 또는 비트랜잭션 인지 여부와 구성 요소가 spout 또는 볼트 인지 여부에 따라 달라 지는 인터페이스입니다.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** 는 대부분의 플러그 인에 대 한 공통 인터페이스 이며, 현재 더미 인터페이스입니다.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** 는 비트랜잭션 spout의 인터페이스입니다.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

**Nexttuple** 가 호출 되 면 코드 C# 에서 하나 이상의 튜플을 내보낼 수 있습니다. 내보낼 항목이 없는 경우이 메서드는 아무것도 내보내지 않고를 반환 해야 합니다.

**Nexttuple**, **Ack**및 **Fail** 메서드는 모두 C# 프로세스의 단일 스레드에서 긴밀 한 루프에서 호출 됩니다. 내보낼 튜플이 없는 경우 10 밀리초와 같이 짧은 시간 동안 **Nexttuple** 일시 중지 합니다. 이 절전 모드는 CPU 가용성 낭비를 방지 하는 데 도움이 됩니다.

**Ack** 및 **Fail** 메서드는 사양 파일이 승인 메커니즘을 사용 하도록 설정 하는 경우에만 호출 됩니다. *SeqId* 매개 변수는 승인 되거나 실패 한 튜플을 식별 합니다. 비트랜잭션 토폴로지에서 승인이 사용 하도록 설정 된 경우 spout에서 다음 **내보내기** 함수를 사용 해야 합니다.

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

비트랜잭션 토폴로지가 승인을 지원 하지 않는 경우 **Ack** 및 **Fail** 은 빈 함수로 남겨둘 수 있습니다.

이러한 함수의 *parms* 입력 매개 변수는 빈 사전을 지정 하 고 나중에 사용 하도록 예약 됩니다.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** 는 비트랜잭션 볼트의 인터페이스입니다.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

새 튜플을 사용할 수 있는 경우 **Execute** 함수를 호출 하 여 처리 합니다.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** 는 트랜잭션 spout의 인터페이스입니다.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

비트랜잭션와 마찬가지로 **Nexttx**, **Ack**및 **Fail** 은 모두 C# 프로세스의 단일 스레드에서 긴밀 한 루프에서 호출 됩니다. 내보낼 튜플이 없는 경우 10 밀리초와 같이 짧은 시간 동안에는 **Nexttx** 를 중지 합니다. 이 절전 모드는 CPU 가용성 낭비를 방지 하는 데 도움이 됩니다.

새 트랜잭션을 시작 하기 위해 **Nexttx** 를 호출 하면 *seqId* output 매개 변수가 트랜잭션을 식별 합니다. 트랜잭션은 **Ack** 에도 사용 되며 **실패**합니다. **Nexttx** 메서드는 Java 쪽으로 데이터를 내보낼 수 있습니다. 재생을 지원하기 위해 데이터는 ZooKeeper에 저장됩니다. 사육 사가 용량을 제한 하므로 코드는 트랜잭션 spout 대량 데이터가 아닌 메타 데이터만 내보냅니다.

폭풍이 실패 한 트랜잭션을 자동으로 재생 하기 때문에 **실패** 는 일반적으로 호출 되지 않습니다. 그러나 SCP가 트랜잭션 spout에서 내보낸 메타 데이터를 확인할 수 있는 경우 메타 데이터가 잘못 된 경우 **Fail** 을 호출할 수 있습니다.

이러한 함수의 *parms* 입력 매개 변수는 빈 사전을 지정 하 고 나중에 사용 하도록 예약 됩니다.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** 는 트랜잭션 볼트의 인터페이스입니다.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

**Execute** 메서드는 새 튜플이 볼트에 도착할 때 호출 됩니다. 이 트랜잭션이 종료 되 면 완료 된 **일괄 처리** 메서드가 호출 됩니다. *Parms* 입력 매개 변수는 나중에 사용 하도록 예약 되어 있습니다.

트랜잭션 토폴로지의 경우 **StormTxAttempt** 은 중요 한 클래스입니다. 이 클래스에는 두 개의 멤버 (트랜잭션 **id** 및 **AttemptId**)가 있습니다. 트랜잭션 **id** 멤버는 특정 트랜잭션을 식별 합니다. 트랜잭션이 실패 하 여 재생 되는 경우 여러 번 시도할 수 있습니다.

SCP.NET은 Java에서 수행 하는 폭풍이와 마찬가지로 각 **StormTxAttempt** 개체를 처리 하는 새 **ISCPBatchBolt** 개체를 만듭니다. 이 디자인의 목적은 병렬 트랜잭션 처리를 지 원하는 것입니다. 트랜잭션 시도가 완료 되 면 해당 **ISCPBatchBolt** 개체가 제거 되 고 가비지 수집 됩니다.

## <a name="object-model"></a>개체 모델

SCP.NET에서는 개발자가 프로그래밍에 사용할 수 있는 주요 개체의 간단한 집합도 제공합니다. 개체는 **Context**, **StateStore**및 **scpruntime은**입니다. 이러한 항목에 대해서는이 섹션에서 설명 합니다.

### <a name="context"></a>Context

**컨텍스트** 개체는 응용 프로그램에 실행 중인 환경을 제공 합니다. **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**또는 **ISCPBatchBolt** 의 각 **ISCPPlugin** 인스턴스에는 해당 하는 **컨텍스트** 인스턴스가 있습니다. **Context** 에서 제공 하는 기능은 다음과 같은 두 부분으로 구분 됩니다.

* 전체 C# 프로세스에서 사용할 수 있는 정적 부분입니다.
* 특정 **컨텍스트** 인스턴스에 대해서만 사용할 수 있는 동적 부분입니다.

### <a name="static-part"></a>정적 부분

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**로 거** 개체는 로깅을 위해 제공 됩니다.

**PluginType** 개체는 C# 프로세스의 플러그 인 유형을 나타냅니다. 프로세스를 Java 없이 로컬 테스트 모드에서 실행 하는 경우 플러그 인 유형이 **SCP_NET_LOCAL**됩니다.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Config** 속성은 Java 쪽에서 구성 매개 변수를 가져오며, C# 플러그 인이 초기화 될 때이를 전달 합니다. **구성** 매개 변수는 **stormconf** 및 **pluginconf**의 두 부분으로 나뉩니다.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**Stormconf** 파트는 스톰에 의해 정의 된 매개 변수 이며, **PLUGINCONF** 부분은 SCP에서 정의한 매개 변수입니다. 예를 들면 다음과 같습니다.

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**TopologyContext** 형식은 토폴로지 컨텍스트를 가져옵니다. 여러 병렬 구성 요소에 가장 유용 합니다. 예를 들면 다음과 같습니다.

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>동적 파트

다음 인터페이스는 SCP.NET 플랫폼에서 만들고 코드에 전달 되는 특정 **컨텍스트** 인스턴스와 관련이 있습니다.

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

승인을 지 원하는 비트랜잭션 spout의 경우 다음 메서드가 제공 됩니다.

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

승인을 지 원하는 비트랜잭션 볼트는 **Ack** 를 명시적으로 호출 하거나 수신 된 튜플로 **실패** 합니다. 새 튜플을 내보낼 때 볼트가 튜플 앵커도 지정 해야 합니다. 제공 되는 메서드는 다음과 같습니다.

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore** 개체는 메타 데이터 서비스, 단조 시퀀스 생성 및 대기 없는 조정을 제공 합니다. **StateStore**에서 더 높은 수준의 분산 동시성 추상화를 빌드할 수 있습니다. 이러한 추상화는 분산 잠금, 분산 큐, 장벽 및 트랜잭션 서비스를 포함 합니다.

SCP 응용 프로그램은 **상태** 개체를 사용 하 여 [Apache ZooKeeper](https://zookeeper.apache.org/)정보를 직렬화 할 수 있습니다. 이 기능은 트랜잭션 토폴로지에 특히 유용 합니다. 트랜잭션 spout가 응답을 중지 하 고 다시 시작 하는 경우 **상태** 는 사육 사에서 필요한 정보를 검색 하 고 파이프라인을 다시 시작할 수 있습니다.

**StateStore** 개체에는 다음과 같은 주요 방법이 있습니다.

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**상태** 개체에는 다음과 같은 주요 방법이 있습니다.

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

**Simplemode** 를 **true**로 설정 하면 **Commit** 메서드는 사육 사에서 해당 znode를 삭제 합니다. 그렇지 않으면 메서드는 현재 ZNode를 삭제 하 고 COMMITTED\_경로에 새 노드를 추가 합니다.

### <a name="scpruntime"></a>SCPRuntime

**Scpruntime은** 클래스는 다음과 같은 두 가지 메서드를 제공 합니다.

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

**Initialize** 메서드는 SCP 런타임 환경을 초기화 합니다. 이 메서드에서 프로세스는 C# Java 쪽에 연결 하 여 구성 매개 변수와 토폴로지 컨텍스트를 가져옵니다.

**Launchplugin** 메서드는 메시지 처리 루프를 시작 합니다. 이 루프에서 플러그 인 C# 은 Java 쪽에서 메시지를 받습니다. 이러한 메시지는 튜플 및 제어 신호를 포함 합니다. 그러면 플러그 인에서 코드에서 제공 하는 인터페이스 메서드를 호출 하 여 메시지를 처리 합니다.

**Launchplugin** 의 입력 매개 변수는 대리자입니다. 메서드는 **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**또는 **ISCPBatchBolt** 인터페이스를 구현 하는 개체를 반환할 수 있습니다.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

**ISCPBatchBolt**의 경우 *parms* 매개 변수에서 **StormTxAttempt** 개체를 가져온 후이 개체를 사용 하 여 재생이 재생 된 시도 인지 여부를 판단할 수 있습니다. 재생 시도 확인은 종종 커밋 볼트에서 수행 됩니다. 이 문서의 뒷부분에 나오는 HelloWorldTx 예제에서는 이러한 검사를 보여 줍니다.

SCP 플러그 인은 일반적으로 로컬 테스트 모드와 일반 모드의 두 가지 모드로 실행 될 수 있습니다.

#### <a name="local-test-mode"></a>로컬 테스트 모드

이 모드에서 C# 코드의 SCP 플러그 인은 개발 단계 중에 Visual Studio 내에서 실행 됩니다. 이 모드에서는 **Ilocalcontext** 인터페이스를 사용할 수 있습니다. 인터페이스는 내보낸 튜플을 로컬 파일에 serialize 하 고 다시 RAM으로 읽어 주는 메서드를 제공 합니다.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>일반 모드

이 모드에서 스톰 Java 프로세스는 SCP 플러그 인을 실행 합니다. 예를 들면 다음과 같습니다.

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>토폴로지 사양 언어

SCP 토폴로지 사양은 SCP 토폴로지를 설명 하 고 구성 하기 위한 DSL (도메인 특정 언어)입니다. 이는 [스톰의 CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) 을 기반으로 하며 SCP에 의해 확장 됩니다.

**Runspec** 명령을 통해 실행 하기 위해 스톰 클러스터에 직접 토폴로지 사양을 제출할 수 있습니다.

SCP.NET는 다음과 같은 함수를 추가 하 여 트랜잭션 토폴로지를 정의 합니다.

| 새 함수 | 매개 변수 | Description |
| --- | --- | --- |
| **tx-topolopy** |*토폴로지-이름*<br />*spout-매핑*<br />*볼트-지도* |토폴로지 이름, spout 정의 맵 및 볼트 정의 맵으로 트랜잭션 토폴로지를 정의 합니다. |
| **scp-tx-spout** |*exec-이름*<br />*args*<br />*fields* |트랜잭션 spout를 정의 합니다. 함수는 *exec 이름* 으로 지정 된 응용 프로그램을 실행 하 고 *args*를 사용 합니다.<br /><br />*Fields* 매개 변수는 spout의 출력 필드를 지정 합니다. |
| **scp-tx-batch-bolt** |*exec-이름*<br />*args*<br />*fields* |트랜잭션 일괄 처리 볼트를 정의 합니다. 함수는 *exec 이름* 으로 지정 된 응용 프로그램을 실행 하 고 args를 사용 *합니다.*<br /><br />*Fields* 매개 변수는 볼트의 출력 필드를 지정 합니다. |
| **scp-tx-commit-bolt** |*exec-이름*<br />*args*<br />*fields* |트랜잭션 커밋 볼트를 정의 합니다. 함수는 *exec 이름* 으로 지정 된 응용 프로그램을 실행 하 고 *args*를 사용 합니다.<br /><br />*Fields* 매개 변수는 볼트의 출력 필드를 지정 합니다. |
| **비 tx-토폴로지** |*토폴로지-이름*<br />*spout-매핑*<br />*볼트-지도* |토폴로지 이름, spout 정의 맵 및 볼트 정의 맵으로 비트랜잭션 토폴로지를 정의 합니다. |
| **scp-spout** |*exec-이름*<br />*args*<br />*fields*<br />*parameters* |비트랜잭션 spout를 정의 합니다. 함수는 *exec 이름* 으로 지정 된 응용 프로그램을 실행 하 고 *args*를 사용 합니다.<br /><br />*Fields* 매개 변수는 spout의 출력 필드를 지정 합니다.<br /><br />*Parameters* 매개 변수는 선택 사항입니다. 이를 사용 하 여 "비트랜잭션. ack"와 같은 매개 변수를 지정 합니다. |
| **scp-bolt** |*exec-이름*<br />*args*<br />*fields*<br />*parameters* |비트랜잭션 볼트를 정의 합니다. 함수는 *exec 이름* 으로 지정 된 응용 프로그램을 실행 하 고 *args*를 사용 합니다.<br /><br />*Fields* 매개 변수는 볼트의 출력 필드를 지정 합니다.<br /><br />*Parameters* 매개 변수는 선택 사항입니다. 이를 사용 하 여 "비트랜잭션. ack"와 같은 매개 변수를 지정 합니다. |

SCP.NET는 다음 키워드를 정의 합니다.

| 키워드 | Description |
| --- | --- |
| **:name** |토폴로지 이름 |
| **:topology** |이전 테이블 및 기본 제공 함수의 함수를 사용 하는 토폴로지 |
| **:p** |각 spout 또는 볼트에 대 한 병렬 처리 힌트 |
| **:config** |매개 변수를 구성 하거나 기존 매개 변수를 업데이트할지 여부 |
| **:schema** |스트림의 스키마입니다. |

또한 SCP.NET는 이러한 자주 사용 되는 매개 변수를 정의 합니다.

| 매개 변수 | Description |
| --- | --- |
| "plugin.name" |C# 플러그 인의 .exe 파일 이름입니다. |
| "plugin. args" |플러그 인 인수 |
| "output. schema" |출력 스키마 |
| "비트랜잭션 사용" |비트랜잭션 토폴로지에 대해 승인이 사용 되는지 여부 |

**Runspec** 명령은 비트와 함께 배포 됩니다. 명령 사용법은 다음과 같습니다.

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

*리소스-dir* 매개 변수는 선택 사항입니다. C# 응용 프로그램에 연결 하려는 경우이를 지정 합니다. 지정 된 디렉터리에는 응용 프로그램, 종속성 및 구성이 포함 됩니다.

*classpath* 매개 변수도 선택 사항으로, 사양 파일에 Java spout 또는 볼트가 포함 된 경우 Java 클래스 경로를 지정 합니다.

## <a name="miscellaneous-features"></a>기타 기능

### <a name="input-and-output-schema-declarations"></a>입력 및 출력 스키마 선언

프로세스 C# 에서 튜플을 내보낼 수 있습니다. 이렇게 하기 위해 플랫폼은 튜플을 **byte []** 개체로 직렬화 하 고 개체를 Java 쪽으로 전송 합니다. 그런 다음 이러한 튜플을 대상으로 전송 합니다.

다운스트림 구성 요소에서 C# 프로세스는 Java 쪽에서 튜플을 다시 받아 플랫폼의 원래 형식으로 변환 합니다. 이러한 모든 작업은 플랫폼에 의해 숨겨집니다.

Serialization 및 deserialization을 지원 하려면 코드에서 입력 및 출력의 스키마를 선언 해야 합니다. 스키마는 사전으로 정의 됩니다. 스트림 ID는 사전 키입니다. 키 값은 열의 유형입니다. 구성 요소는 여러 스트림을 선언할 수 있습니다.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

다음 함수는 **컨텍스트** 개체에 추가 됩니다.

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

개발자는 내보낸 튜플이 스트림에 대해 정의 된 스키마를 준수 하는지 확인 해야 합니다. 그렇지 않으면 시스템에서 런타임 예외를 throw 합니다.

### <a name="multistream-support"></a>Multistream 지원

SCP를 사용 하면 코드를 여러 개의 고유 스트림에서 동시에 내보내거나 받을 수 있습니다. **컨텍스트** 개체는이 지원을 **내보내기** 메서드의 선택적 스트림 ID 매개 변수로 반영 합니다.

SCP.NET **Context** 개체의 두 메서드가 추가 되었습니다. 하나 이상의 튜플을 특정 스트림으로 내보냅니다. *Streamid* 매개 변수는 문자열입니다. 이 값은 C# 코드와 토폴로지 정의 사양에서 동일 해야 합니다.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

존재 하지 않는 스트림으로 내보내면 런타임 예외가 발생 합니다.

### <a name="fields-grouping"></a>필드 그룹화

SCP.NET에서 기본 제공 필드 그룹화가 제대로 작동 하지 않습니다. Java 프록시 쪽에서는 모든 필드의 데이터 형식이 실제로 **byte []** 입니다. 필드 그룹화는 **byte []** 개체의 해시 코드를 사용 하 여 그룹화를 수행 합니다. 해시 코드는 RAM에서이 개체의 주소입니다. 따라서 같은 콘텐츠를 공유 하지만 동일한 주소를 공유 하지 않는 멀티 바이트 개체의 경우에는 그룹화가 올바르지 않습니다.

SCP.NET 사용자 지정 된 grouping 메서드를 추가 하 고 **byte []** 개체의 내용을 사용 하 여 그룹화를 수행 합니다. 사양 파일에서 구문은 다음 예제와 같습니다.

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

위의 사양 파일에서 다음을 수행 합니다.

* `scp-field-group` 그룹화가 SCP에서 구현 하는 사용자 지정 필드 그룹화 임을 지정 합니다.
* `:tx` 또는 `:non-tx` 토폴로지가 트랜잭션인지 여부를 지정 합니다. 시작 인덱스는 트랜잭션 토폴로지와 비트랜잭션 사이에서 다르기 때문에이 정보가 필요 합니다.
* `[0,1]`는 0으로 시작 하는 필드 Id의 해시 집합을 지정 합니다.

### <a name="hybrid-topology"></a>하이브리드 토폴로지

네이티브 스톰 코드는 Java로 작성 됩니다. SCP.NET에는 비즈니스 논리를 처리 하 C# 는 코드를 작성할 수 있도록 향상 된 폭풍이 있습니다. 그러나 SCP.NET는 C# spouts/볼트 뿐만 아니라 Java spouts/볼트를 포함 하는 하이브리드 토폴로지도 지원 합니다.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>사양 파일에서 Java spout/볼트 지정

사양 파일에서 **spout** 및 **scp (** scp)를 사용 하 여 Java spout 및 볼트를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

여기서 `microsoft.scp.example.HybridTopology.Generator`는 Java spout 클래스의 이름입니다.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>RunSpec 명령에 Java 클래스 경로 지정

Java spout 또는 볼트가 포함 된 토폴로지를 제출 하려면 먼저이를 컴파일하여 JAR 파일을 생성 합니다. 그런 다음 토폴로지를 제출할 때 JAR 파일이 포함 된 java 클래스 경로를 지정 합니다. 예를 들면 다음과 같습니다.

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

여기서 `examples\HybridTopology\java\target\`는 Java spout/볼트 JAR 파일이 포함 된 폴더입니다.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java와 간 Serialization 및 deserializationC#

SCP 구성 요소는 Java 쪽 및 C# 측면을 포함 합니다. 네이티브 Java spouts/볼트와 상호 작용 하려면 다음 그래프에 표시 된 것 처럼 Java 쪽과 C# 측면 사이에서 serialization 및 deserialization이 발생 해야 합니다.

![다른 Java 구성 요소로 전송 하는 SCP 구성 요소에 보내는 Java 구성 요소 다이어그램](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 쪽의 직렬화 및 C# 측의 deserialization

먼저 Java 쪽의 serialization에 대 한 기본 구현을 제공 하 고 C# 측면에서 deserialization 합니다.

사양 파일에서 Java 쪽의 직렬화 메서드를 지정 합니다.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

코드에서 C# 변의 deserialization 메서드를 지정 합니다. C#

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

데이터 형식이 너무 복잡 하지 않으면이 기본 구현에서 대부분의 경우를 처리 해야 합니다. 다음은 사용자 고유의 구현을 연결할 수 있는 경우입니다.

* 데이터 형식이 너무 복잡 하 여 기본 구현을 구현할 수 없습니다.
* 기본 구현의 성능이 요구 사항을 충족 하지 않습니다.

Java 쪽의 serialization 인터페이스는 다음과 같이 정의 됩니다.

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 쪽의 deserialization 인터페이스는 다음과 같이 정의 됩니다.

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# Java 쪽에서의 Serialization 및 deserialization

코드에서 C# 변의 serialization 메서드를 지정 합니다. C#

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

사양 파일에서 Java 쪽의 deserialization 메서드를 지정 합니다.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

여기서 `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"`는 역직렬 변환기의 이름이 고 `"microsoft.scp.example.HybridTopology.Person"`는 데이터가 deserialize 되는 대상 클래스입니다.

C# Serializer 및 Java 역직렬 변환기의 고유한 구현을 연결할 수도 있습니다.

이 코드는 C# 직렬 변환기에 대 한 인터페이스입니다.

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

이 코드는 Java 역직렬화에 대 한 인터페이스입니다.

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 호스트 모드

이 모드에서는 코드를 DLL로 컴파일하고 SCP에서 제공 하는 Scphost.exe을 사용 하 여 토폴로지를 제출할 수 있습니다. 사양 파일은 다음 코드와 같습니다.

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

여기서 `"plugin.name"`은 SCP SDK에서 제공 하는 `"SCPHost.exe"`로 지정 됩니다. Scphost.exe는 다음과 같은 순서로 세 개의 매개 변수를 허용 합니다.

1. 이 예제에서 `"HelloWorld.dll"` DLL 이름입니다.
1. 이 예제에서 `"Scp.App.HelloWorld.Generator"` 된 클래스 이름입니다.
1. **ISCPPlugin**의 인스턴스를 가져오기 위해 호출할 수 있는 공용 정적 메서드의 이름입니다.

호스트 모드에서 SCP 플랫폼에의 한 호출을 위해 코드를 DLL로 컴파일합니다. 그러면 플랫폼이 전체 처리 논리를 완전히 제어할 수 있으므로 SCP 호스트 모드에서 토폴로지를 제출 하는 것이 좋습니다. 이렇게 하면 개발 환경이 간단해 집니다. 또한 이후 릴리스에서 더 유연 하 고 이전 버전과의 호환성을 향상 시킬 수 있습니다.

## <a name="scp-programming-examples"></a>SCP 프로그래밍 예제

### <a name="helloworld"></a>HelloWorld

다음의 간단한 HelloWorld 예는 SCP.NET의 맛을 보여 줍니다. Spout 라는 **생성기** 와 **splitter** 및 **counter**라는 두 개의 볼트가 있는 비트랜잭션 토폴로지를 사용 합니다. **생성자** 는 문장을 임의로 생성 하 고 이러한 문장을 **분할자**로 spout 합니다. **분할자** 볼트는 문장을 단어로 분할 하 고 이러한 단어를 **카운터** 볼트에 내보냅니다. **Counter** 볼트는 사전을 사용 하 여 각 단어의 발생을 기록 합니다.

이 예제에는 두 개의 사양 파일이 있습니다. HelloWorld. spec 및 HelloWorld\_EnableAck. spec 이 C# 코드는 Java 쪽에서 `pluginConf` 개체를 가져와 승인이 사용 되는지 여부를 확인할 수 있습니다.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Spout에서 승인을 사용 하는 경우 사전이 승인 되지 않은 튜플을 캐시 합니다. `Fail`를 호출 하면 오류가 발생 한 튜플이 재생 됩니다.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

다음 HelloWorldTx 예제에서는 트랜잭션 토폴로지를 구현 하는 방법을 보여 줍니다. 이 예제에는 **생성기**라는 spout 하 나와, **부분 카운트**라는 배치 볼트와 **count-sum**이라는 커밋 볼트가 있습니다. 또한이 예제에는 Datasource0.txt, DataSource1 및 DataSource2의 세 가지 기존 텍스트 파일이 있습니다.

각 트랜잭션에서 **생성기** 는 기존 3 개의 파일에서 두 개의 파일을 임의로 선택 하 고 두 개의 파일 이름을 **부분 카운트** spout 내보냅니다. **부분 카운트** 볼트:

1. 받은 튜플에서 파일 이름을 가져옵니다.
1. 해당 파일을 엽니다.
1. 파일의 단어 수를 계산 합니다.
1. 단어 수를 **개수 합계** 볼트에 내보냅니다.

**count-sum** Bolt는 총 단어 개수의 요약을 표시합니다.

정확히 한 번 의미 체계를 실현 하려면 **카운트-sum** 커밋 볼트가 재생 된 트랜잭션 인지 여부를 판단 해야 합니다. 이 예제에는 다음 정적 멤버 변수가 있습니다.

```csharp
public static long lastCommittedTxId = -1; 
```

**ISCPBatchBolt** 인스턴스를 만들면 입력 매개 변수에서 `txAttempt` 개체의 값을 가져옵니다.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

`FinishBatch`를 호출 하면 재생 된 트랜잭션이 아닌 `lastCommittedTxId` 업데이트 됩니다.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

이 토폴로지에는 Java spout와 C# 볼트가 포함 되어 있습니다. SCP 플랫폼에서 제공 하는 기본 serialization 및 deserialization 구현을 사용 합니다. 사양 파일 정보는 예제\\HybridTopology 폴더에서 HybridTopology 파일을 참조 하세요. 또한 Java 클래스 경로를 지정 하는 방법에 대해서는 SubmitTopology를 참조 하세요.

### <a name="scphostdemo"></a>SCPHostDemo

이 예제는 기본적으로 HelloWorld와 동일 합니다. 유일한 차이점은 코드를 DLL로 컴파일하고 Scphost.exe를 사용 하 여 토폴로지를 전송 한다는 것입니다. 자세한 설명은 SCP 호스트 모드 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

SCP를 사용 하 여 만든 Apache Storm 토폴로지의 예는 다음 문서를 참조 하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight의 Apache Storm을 사용하여 Event Hubs의 차량 센서 데이터 처리](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs에서 Apache HBase로 ETL (추출, 변환 및 로드)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
