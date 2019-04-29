---
title: Azure HDInsight의 Storm에 대한 SCP.NET 프로그래밍 가이드
description: Azure HDInsight에서 실행되는 Storm과 함께 사용할 수 있도록 SCP.NET을 사용하여 .NET 기반 Storm 토폴로지를 만드는 방법에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: c85074a2b26a79dbf5e464972e7f82b5955d15f1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126888"
---
# <a name="scp-programming-guide"></a>SCP 프로그래밍 가이드
SCP는 안정적이며 일관성 있는 실시간 고성능 데이터 처리 애플리케이션을 빌드하기 위한 플랫폼입니다. 이 플랫폼은 OSS 커뮤니티에서 디자인한 스트림 처리 시스템인 [Apache Storm](https://storm.incubator.apache.org/)을 기반으로 구축되었습니다. Nathan Marz가 디자인한 Storm은 Twitter에서 오픈 소스 방식으로 제공되며, 매우 안정적인 분산 방식 조정과 상태 관리를 수행하는 데 사용할 수 있는 또 다른 Apache 프로젝트인 [Apache ZooKeeper](https://zookeeper.apache.org/)를 활용합니다. 

SCP 프로젝트를 통해 Windows에서 Storm을 포팅할 수 있을 뿐 아니라 Windows 에코시스템용 확장 및 사용자 지정도 추가할 수 있습니다. 이 확장에는 .NET 개발자 환경과 라이브러리가 포함되고 사용자 지정에는 Windows 기반 배포가 포함됩니다. 

확장과 사용자 지정은 OSS 프로젝트를 분기하지 않아도 되는 방식으로 수행되며, Storm을 기반으로 구축된 파생 에코시스템을 활용할 수 있습니다.

## <a name="processing-model"></a>처리 모델
SCP의 데이터는 튜플의 연속 스트림으로 모델링됩니다. 일반적으로 튜플은 먼저 큐로 이동한 다음 Storm 토폴로지 내에서 호스트되는 비즈니스 논리에 의해 선택 및 변환됩니다. 그리고 마지막으로 출력을 다른 SCP 시스템에 튜플로 파이프하거나, SQL Server 등의 데이터베이스 또는 분산 파일 시스템에서와 같이 저장소에 커밋할 수 있습니다.

![데이터 저장소를 피드하는 데이터 피드에서 처리로의 큐 다이어그램](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

Storm에서는 애플리케이션 토폴로지에 따라 계산 그래프가 정의됩니다. 토폴로지의 각 노드에는 처리 논리가 포함되며 노드 간의 링크는 데이터 흐름을 나타냅니다. 토폴로지에 입력 데이터를 삽입하는 노드인 _spout_는 데이터 순서를 지정하는 데 사용할 수 있습니다. 입력 데이터는 파일 로그, 트랜잭션 데이터베이스, 시스템 성능 카운터 등에 있을 수 있습니다. 입력 및 출력 데이터 흐름을 포함하는 노드는 실제 데이터 필터링과 선택 및 집계를 하는 _bolt_라고 합니다.

SCP는 최상의 노력, 최소한 한 번 및 정확히 한 번 방식의 데이터 처리를 지원합니다. 분산된 스트리밍 처리 애플리케이션에서 네트워크 중단, 머신 오류 또는 사용자 코드 오류 등과 같은 데이터 처리 중 다양한 오류가 발생할 수 있습니다. 최소한 한 번 방식의 처리는 오류 발생 시 동일한 데이터를 자동으로 재생하여 모든 데이터를 한 번 이상 처리되도록 합니다. 최소한 한 번 방식의 처리는 간단하고 안정적이며 대부분의 애플리케이션에 적합합니다. 그러나 애플리케이션에서 정확한 계산을 수행해야 하는 경우에는 애플리케이션 토폴로지에서 같은 데이터가 재생될 가능성이 있으므로 최소한 한 번 방식의 처리로는 부족합니다. 이러한 경우에는 데이터가 여러 번 재생 및 처리되어도 올바른 결과를 계산하는 정확히 한 번 방식의 처리 방식을 사용해야 합니다.

.NET 개발자는 SCP를 통해 JVM(Java Virtual Machine)에서 Storm을 백그라운드에서 활용하면서 실시간 데이터 프로세스 애플리케이션을 개발할 수 있습니다. .NET과 JVM은 TCP 로컬 소켓을 통해 통신합니다. 기본적으로 각 Spout/Bolt는.NET/Java 프로세스 쌍 이며 사용자 논리 플러그 인으로.NET 프로세스에서 실행 되는 위치

SCP를 기반으로 데이터 처리 애플리케이션을 빌드하려면 몇 가지 단계를 수행해야 합니다.

* 큐에서 데이터를 끌어오도록 Spout를 디자인 및 구현합니다.
* 입력 데이터를 처리한 다음 데이터베이스 등의 외부 저장소에 데이터를 저장하도록 Bolt를 디자인 및 구현합니다.
* 토폴로지를 디자인한 다음 제출하고 실행합니다. 토폴로지는 꼭짓점 및 꼭짓점 간의 데이터 흐름을 정의합니다. SCP는 토폴로지 사양을 가져온 다음 Storm 클러스터에 배포합니다. 이 클러스터의 각 논리 노드에서 개별 꼭짓점이 실행됩니다. Storm 작업 스케줄러가 장애 조치(failover) 및 확장을 처리합니다.

이 문서에서는 몇 가지 간단한 예를 통해 SCP를 사용하여 데이터 처리 애플리케이션을 빌드하는 방법을 안내합니다.

## <a name="scp-plugin-interface"></a>SCP 플러그 인 인터페이스
SCP 플러그 인(애플리케이션)은 개발 단계 중에 Visual Studio 내에서도 독립적으로 실행할 수 있고 프로덕션 환경에 배포한 후에 Storm 파이프라인에 연결할 수도 있습니다. SCP 플러그 인 작성 방법은 다른 표준 Windows 콘솔 애플리케이션 작성 방법과 같습니다. SCP.NET 플랫폼에서는 Spout/Bolt용 인터페이스를 선언하며, 사용자 플러그 인 코드는 이러한 인터페이스를 구현해야 합니다. 이러한 디자인은 기본적으로 사용자가 자신의 비즈니스 논리를 실행하는 데 주력하고 나머지 작업은 SCP.NET 플랫폼이 처리하도록 하기 위한 것입니다.

토폴로지의 유형(트랜잭션/비트랜잭션) 및 구성 요소의 유형(Spout/Bolt)에 따라 사용자 플러그 인 코드는 다음 인터페이스 중 하나를 구현해야 합니다.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin은 모든 종류의 플러그 인에 공통적으로 적용되는 인터페이스로, 현재는 더미 인터페이스입니다.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout는 비트랜잭션 Spout용 인터페이스입니다.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

`NextTuple()`가 호출되면 C\# 사용자 코드가 하나 이상의 튜플을 내보낼 수 있습니다. 내보낼 튜플이 없으면 아무 항목도 내보내지 않고 이 메서드가 반환되어야 합니다. `NextTuple()`, `Ack()` 및 `Fail()`은 모두 C\# 프로세스 내 단일 스레드의 조밀한 루프에서 호출됩니다. 내보낼 튜플이 없으면 CPU를 너무 많이 낭비하지 않도록 10밀리초 등의 짧은 시간 동안 NextTuple을 유휴 상태로 유지하는 것이 좋습니다.

`Ack()` 및 `Fail()`은 사양 파일에서 승인 메커니즘이 사용하도록 설정되어 있어야 호출됩니다. `seqId` 승인 되었거나 실패 한 튜플을 식별 하는 데 사용 됩니다. 따라서 비트랜잭션 토폴로지에서 승인이 사용하도록 설정되어 있으면 Spout에서 다음의 내보내기 함수를 사용해야 합니다.

    public abstract void Emit(string streamId, List<object> values, long seqId); 

비트랜잭션 토폴로지에서 승인이 지원되지 않으면 `Ack()` 및 `Fail()`은(는) 빈 함수로 유지될 수 있습니다.

이러한 함수의 `parms` 입력 매개 변수는 빈 사전이며 나중에 사용하도록 예약됩니다.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt는 비트랜잭션 Bolt용 인터페이스입니다.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

새 튜플을 사용할 수 있으면 `Execute()` 함수가 호출되어 해당 튜플을 처리합니다.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout는 트랜잭션 Spout용 인터페이스입니다.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

해당하는 비트랜잭션 함수와 마찬가지로 `NextTx()`, `Ack()` 및 `Fail()`은 모두 C\# 프로세스 내 단일 스레드의 조밀한 루프에서 호출됩니다. 내보낼 데이터가 없으면 CPU를 너무 많이 낭비하지 않도록 10밀리초 등의 짧은 시간 동안 `NextTx` 을(를) 유휴 상태로 유지하는 것이 좋습니다.

`NextTx()`을(를) 호출하여 새 트랜잭션을 시작하고 출력 매개 변수 `seqId`을(를) 사용하여 트랜잭션을 식별하며 `Ack()` 및 `Fail()`에서도 사용됩니다. 사용자는 `NextTx()`에서 Java 쪽으로 데이터를 내보낼 수 있습니다. 재생을 지원하기 위해 데이터는 ZooKeeper에 저장됩니다. ZooKeeper의 용량은 제한되어 있으므로 사용자는 트랜잭션 Spout에서 데이터를 대량으로 내보내서는 안 되며 메타데이터만 내보내야 합니다.

Storm에서는 트랜잭션이 실패하면 자동으로 재생하므로 일반적인 경우에는 `Fail()` 을(를) 호출하면 안 됩니다. 그러나 SCP는 트랜잭션 Spout에서 내보낸 메타데이터를 확인할 수 있으면 메타데이터가 잘못된 경우 `Fail()` 을(를) 호출할 수 있습니다.

이러한 함수의 `parms` 입력 매개 변수는 빈 사전이며 나중에 사용하도록 예약됩니다.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt는 트랜잭션 Bolt용 인터페이스입니다.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` 이(가) 호출됩니다. `FinishBatch()` 이(가) 호출됩니다. `parms` 입력 매개 변수는 나중에 사용하도록 예약됩니다.

트랜잭션 토폴로지에는 `StormTxAttempt`(이)라는 중요한 개념이 있습니다. 두 필드 `TxId` 및 `AttemptId`가 있습니다. `TxId`는 특정 트랜잭션을 식별하는 데 사용됩니다. 지정된 트랜잭션이 실패하여 재생되는 경우에는 해당 트랜잭션을 여러 번 시도할 수 있습니다. SCP.NET은 Java에서 Storm이 수행하는 것처럼 새 ISCPBatchBolt 개체를 만들어 각 `StormTxAttempt`를 처리합니다. 이 디자인을 통해 병렬 트랜잭션 처리가 지원됩니다. 사용자는 트랜잭션 시도가 완료되면 해당하는 ISCPBatchBolt 개체는 삭제되며 가비지 수집된다는 점을 기억해야 합니다.

## <a name="object-model"></a>개체 모델
SCP.NET에서는 개발자가 프로그래밍에 사용할 수 있는 주요 개체의 간단한 집합도 제공합니다. 이러한 개체는 **Context**, **StateStore** 및 **SCPRuntime**입니다. 이 섹션의 나머지 부분에서 각 개체에 대해 설명합니다.

### <a name="context"></a>Context
Context는 애플리케이션에 실행 환경을 제공합니다. 각 ISCPPlugin 인스턴스(ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt)에는 해당하는 Context 인스턴스가 있습니다. Context에서 제공하는 기능은 두 부분 즉 (1) 전체 C\# 프로세스에서 사용할 수 있는 정적 부분과 (2) 특정 Context 인스턴스에서만 사용할 수 있는 동적 부분으로 구분할 수 있습니다.

### <a name="static-part"></a>정적 부분
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` 은(는) 기록용으로 제공됩니다.

`pluginType`은 C\# 프로세스의 플러그 인 유형을 나타내는 데 사용됩니다. C\# 프로세스를 Java 없이 로컬 테스트 모드에서 실행하는 경우 플러그 인 유형은 `SCP_NET_LOCAL`입니다.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` 은(는) Java 쪽에서 구성 매개 변수를 가져오기 위한 용도로 제공됩니다. C\# 플러그 인을 초기화할 때 Java 쪽에서 매개 변수가 전달됩니다. `Config` 매개 변수는 `stormConf` 및 `pluginConf`의 두 부분으로 나뉩니다.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`은(는) Storm에서 정의하는 매개 변수이고 `pluginConf`은(는) SCP에서 정의하는 매개 변수입니다. 예를 들면 다음과 같습니다.

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` 은(는) 토폴로지 컨텍스트를 가져오기 위한 용도로 제공되며 여러 병렬 처리를 수행하는 구성 요소에 가장 유용합니다. 다음은 예제입니다.

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

### <a name="dynamic-part"></a>동적 부분
아래에는 특정 Context 인스턴스와 관련된 인터페이스가 나와 있습니다. Context 인스턴스는 SCP.NET 플랫폼에서 작성되어 사용자 코드로 전달됩니다.

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

승인을 지원하는 비트랜잭션 Spout의 경우에는 다음 메서드가 제공됩니다.

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

승인을 지원하는 비트랜잭션 Bolt는 수신한 튜플에 대해 명시적으로 `Ack()` 또는 `Fail()`을(를) 실행해야 합니다. 그리고 새 튜플을 내보낼 때는 새 튜플의 앵커도 지정해야 합니다. 다음과 같은 메서드가 제공됩니다.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` 은(는) 메타데이터 서비스, 단조 시퀀스 생성 및 비대기 조정 기능을 제공합니다. `StateStore`을(를) 기반으로 하여 분산 잠금, 분산 큐, 장벽 및 트랜잭션 서비스를 비롯한 높은 수준의 분산형 동시성 추상화를 작성할 수 있습니다.

SCP 애플리케이션은 `State` 개체를 사용하여 [Apache ZooKeeper](https://zookeeper.apache.org/)에 일부 정보를 영구 보존할 수 있습니다(특히 트랜잭션 토폴로지의 경우). 따라서 트랜잭션 Spout 작동이 중단되어 다시 시작되는 경우 응용 프로그램이 ZooKeeper에서 필요한 정보를 검색하여 파이프라인을 다시 시작할 수 있습니다.

`StateStore` 개체는 기본적으로 다음 메서드를 포함합니다.

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State` 개체는 기본적으로 다음 메서드를 포함합니다.

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

`Commit()` 메서드의 경우 simpleMode를 true로 설정하면 ZooKeeper에서 해당 ZNode만 삭제합니다. 그렇지 않은 경우에는 현재 ZNode를 삭제하고 COMMITTED\_PATH에 새 노드를 추가합니다.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime은 다음의 두 메서드를 제공합니다.

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` 은(는) SCP 런타임 환경을 초기화하는 데 사용됩니다. 이 메서드에서 C\# 프로세스는 Java 쪽에 연결되며 구성 매개 변수와 토폴로지 컨텍스트를 가져옵니다.

`LaunchPlugin()`은(는) 메시지 처리 루프를 시작하는 데 사용됩니다. 이 루프에서 C\# 플러그 인은 Java 쪽에서 메시지(튜플과 제어 신호 포함)를 받은 다음 처리합니다. 이때 사용자 코드에서 제공하는 인터페이스 메서드를 호출할 수 있습니다. `LaunchPlugin()` 메서드의 입력 매개 변수는 ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt 인터페이스를 구현하는 개체를 반환할 수 있는 대리자입니다.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

ISCPBatchBolt의 경우 `parms`에서 `StormTxAttempt`을(를) 가져와 해당 시도가 재생된 시도인지를 판단하는 데 사용할 수 있습니다. 재생 시도에 대한 확인은 종종 커밋 Bolt에서 수행되며 `HelloWorldTx` 예제에서 확인할 수 있습니다.

일반적으로 여기서 SCP 플러그 인은 두 가지 모드로 실행될 수 있습니다.

1. 로컬 테스트 모드: 이 모드에서 SCP 플러그 인(C\# 사용자 코드)은 개발 단계 중에 Visual Studio 내에서 실행됩니다. `LocalContext` 을(를) 사용할 수 있습니다.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. 일반 모드: 이 모드에서는 Storm Java 프로세스를 통해 SCP 플러그 인을 시작합니다.
   
    SCP 플러그 인을 시작하는 예는 다음과 같습니다.
   
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

## <a name="topology-specification-language"></a>토폴로지 사양 언어
SCP 토폴로지 사양은 SCP 토폴로지를 설명하고 구성하기 위한 도메인별 언어로, Storm의 Clojure DSL(<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>)를 기준으로 하며 SCP에 의해 확장됩니다.

***runspec*** 명령을 통해 토폴로지 사양을 실행용으로 Storm 클러스터에 직접 제출할 수 있습니다.

SCP.NET에는 트랜잭션 토폴로지를 정의하는 다음 함수가 추가되었습니다.

| **새 함수** | **매개 변수** | **설명** |
| --- | --- | --- |
| **tx-topolopy** |topology-name<br />spout-map<br />bolt-map |토폴로지 이름, &nbsp;Spout 정의 맵 및 Bolt 정의 맵으로 트랜잭션 토폴로지를 정의합니다. |
| **scp-tx-spout** |exec-name<br />args<br />fields |트랜잭션 Spout를 정의합니다. ***args***를 사용하여 ***exec-name***이라는 응용 프로그램을 실행합니다.<br /><br />***fields*** 는 Spout의 출력 필드입니다. |
| **scp-tx-batch-bolt** |exec-name<br />args<br />fields |트랜잭션 Batch Bolt를 정의합니다. ***args***를 사용하여 ***exec-name***이라는 애플리케이션을 실행합니다.<br /><br />Fields는 Bolt의 출력 필드입니다. |
| **scp-tx-commit-bolt** |exec-name<br />args<br />fields |트랜잭션 커밋 Bolt를 정의합니다. ***args***를 사용하여 ***exec-name***이라는 응용 프로그램을 실행합니다.<br /><br />***fields*** 는 Bolt의 출력 필드입니다. |
| **nontx-topolopy** |topology-name<br />spout-map<br />bolt-map |토폴로지 이름, &nbsp; Spout 정의 맵 및 Bolt 정의 맵으로 비트랜잭션 토폴로지를 정의합니다. |
| **scp-spout** |exec-name<br />args<br />fields<br />매개 변수 |비트랜잭션 Spout를 정의합니다. ***args***를 사용하여 ***exec-name***이라는 응용 프로그램을 실행합니다.<br /><br />***fields*** 는 Spout의 출력 필드입니다.<br /><br />***parameters***는 선택적 항목으로, "nontransactional.ack.enabled"와 같은 일부 매개 변수를 지정하는 데 사용됩니다. |
| **scp-bolt** |exec-name<br />args<br />fields<br />매개 변수 |비트랜잭션 Bolt를 정의합니다. ***args***를 사용하여 ***exec-name***이라는 응용 프로그램을 실행합니다.<br /><br />***fields*** 는 Bolt의 출력 필드입니다.<br /><br />***parameters***는 선택적 항목으로, "nontransactional.ack.enabled"와 같은 일부 매개 변수를 지정하는 데 사용됩니다. |

SCP.NET에는 다음 키워드가 정의되어 있습니다.

| **키워드** | **설명** |
| --- | --- |
| **:name** |토폴로지 이름을 정의합니다. |
| **:topology** |이전 함수와 기본 제공 함수를 사용하여 토폴로지를 정의합니다. |
| **:p** |각 Spout 또는 Bolt에 대해 병렬 처리 힌트를 정의합니다. |
| **:config** |구성 매개 변수를 정의하거나 기존 매개 변수를 업데이트합니다. |
| **:schema** |스트림의 스키마를 정의합니다. |

또한 다음과 같은 자주 사용되는 매개 변수를 정의합니다.

| **매개 변수** | **설명** |
| --- | --- |
| **"plugin.name"** |C# 플러그 인의 exe 파일 이름입니다. |
| **"plugin.args"** |플러그 인 인수입니다. |
| **"output.schema"** |출력 스키마입니다. |
| **"nontransactional.ack.enabled"** |비트랜잭션 토폴로지에 대해 승인이 사용하도록 설정되는지 여부입니다. |

runspec 명령은 비트와 함께 배포되며 사용 방법은 다음과 같습니다.

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

***resource-dir*** 매개 변수는 선택 사항입니다. C\# 애플리케이션을 연결하려는 경우 이 매개 변수를 지정해야 합니다. 이 디렉터리에는 애플리케이션, 종속성 및 구성이 포함됩니다.

***classpath*** 매개 변수도 선택 사항으로, 사양 파일에 Java Spout 또는 Bolt가 포함되는 경우 Java 클래스 경로를 지정하는 데 사용됩니다.

## <a name="miscellaneous-features"></a>기타 기능
### <a name="input-and-output-schema-declaration"></a>입력 및 출력 스키마 선언
사용자는 C\# 프로세스에서 튜플을 내보낼 수 있습니다. 플랫폼은 해당 튜플을 byte로 직렬화하고 Java 쪽으로 전송해야 하며, 그러면 Storm에서 이 튜플을 대상으로 전송합니다. 그와 동시에 C\# 프로세스는 다운스트림 구성 요소에서 Java 쪽으로부터 튜플을 다시 받아 플랫폼별 원래 형식으로 변환합니다. 이러한 모든 작업은 플랫폼에 의해 숨겨집니다.

직렬화와 역직렬화를 지원하려면 사용자 코드가 입력과 출력의 스키마를 선언해야 합니다.

입력/출력 스트림 스키마는 사전으로 정의됩니다. 키는 StreamId입니다. 값은 열의 형식입니다. 구성 요소에서 여러 스트림을 선언할 수 있습니다.

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


Context 개체에는 다음 API가 추가되었습니다.

    public void DeclareComponentSchema(ComponentStreamSchema schema)

개발자는 내보낸 튜플이 해당 스트림에 대해 정의된 스키마를 따르는지 확인해야 합니다. 그렇지 않으면 시스템에서 런타임 예외를 throw합니다.

### <a name="multi-stream-support"></a>여러 스트림 지원
SCP는 사용자 코드가 여러 고유 스트림에서 동시에 내보내기 또는 수신을 수행할 수 있도록 지원합니다. Emit 메서드가 선택적 스트림 ID 매개 변수를 가져올 때 Context 개체에서 이 지원이 반영됩니다.

SCP.NET Context 개체에는 두 개의 메서드가 추가되었습니다. 이러한 메서드는 StreamId를 지정하기 위해 하나 이상의 튜플을 내보내는 데 사용됩니다. StreamId는 C\#과 토폴로지 정의 사양에서 일치해야 하는 문자열입니다.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

존재하지 않는 스트림으로 내보내기를 수행하는 경우 런타임 예외가 발생합니다.

### <a name="fields-grouping"></a>필드 그룹화
Storm의 기본 제공 필드 그룹화는 SCP.NET에서 제대로 작동 하지 않습니다. Java 프록시 쪽에서는 모든 필드 데이터 형식은 실제로 byte[]이고 필드 그룹화는 byte[] 개체 해시 코드를 사용하여 그룹화를 수행합니다. byte[] 개체 해시 코드는 메모리에서 이 개체의 주소입니다. 따라서 같은 콘텐츠를 공유하지만 주소는 다른 두 byte[] 개체의 경우 그룹화가 올바르게 적용되지 않습니다.

SCP.NET에는 사용자 지정된 그룹화 메서드가 추가되었습니다. 이 메서드는 byte[]의 콘텐츠를 사용하여 그룹화를 수행합니다. **SPEC** 파일의 구문은 다음과 같습니다.

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


여기서 각 부분의 의미는 다음과 같습니다.

1. "scp-field-group"은 "SCP에서 구현하는 사용자 지정된 필드 그룹화"를 의미합니다.
2. ":tx" 또는 ":non-tx"는 트랜잭션 토폴로지인지 여부를 의미합니다. 트랜잭션 토폴로지와 비트랜잭션 토폴로지에서는 시작 인덱스가 서로 다르기 때문에 이 정보가 필요합니다.
3. [0,1]은 0부터 시작하는 필드 ID의 해시 집합을 의미합니다.

### <a name="hybrid-topology"></a>하이브리드 토폴로지
원시 Storm은 Java로 작성됩니다. SCP.NET C 수 있도록 향상 되었습니다\# 개발자가 C를 쓸\# 고객이 비즈니스 논리를 처리 하는 코드입니다. 그러나 C\# Spout/Bolt뿐 아니라 Java Spout/Bolt도 포함하는 하이브리드 토폴로지도 지원됩니다.

### <a name="specify-java-spoutbolt-in-spec-file"></a>사양 파일에서 Java Spout/Bolt 지정
사양 파일에서 "scp-spout" 및 "scp-bolt"를 사용하여 Java Spout 및 Bolt를 지정할 수도 있습니다. 해당 예제는 다음과 같습니다.

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

여기에서 `microsoft.scp.example.HybridTopology.Generator`은(는) Java Spout 클래스의 이름입니다.

### <a name="specify-java-classpath-in-runspec-command"></a>runSpec 명령에서 Java 클래스 경로 지정
Java Spout 또는 Bolt를 포함하는 토폴로지를 제출하려면 먼저 Java Spout 또는 Bolt를 컴파일한 다음 Jar 파일을 가져와야 합니다. 그런 후에 토폴로지를 제출할 때 Jar 파일을 포함하는 Java 클래스 경로를 지정해야 합니다. 다음은 예제입니다.

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

여기서 **examples\\HybridTopology\\java\\target\\**은 Java Spout/Bolt Jar 파일을 포함하는 폴더입니다.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java와 C\# 간의 직렬화 및 역직렬화
SCP 구성 요소는 Java 쪽과 C\# 쪽을 포함합니다. 네이티브 Java Spout/Bolt와 상호 작용을 하기 위해 다음 그래프에서 볼 수 있듯이 Java 쪽과 C\# 쪽 사이에서 직렬화/역직렬화를 수행해야 합니다.

![Java 구성 요소로 전송하는 SCP 구성 요소로 전송하는 java 구성 요소의 다이어그램](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Java 쪽의 직렬화 및 C\# 쪽의 역직렬화**
   
   먼저 Java 쪽의 직렬화와 C\# 쪽의 역직렬화를 위한 기본 구현이 제공됩니다. Java 쪽의 직렬화 메서드는 SPEC 파일에서 지정할 수 있습니다.
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   C\# 쪽의 역직렬화 메서드는 C\# 사용자 코드에서 지정해야 합니다.
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   데이터 형식이 너무 복잡하지 않으면 이 기본 구현에서 대부분의 경우를 처리할 수 있습니다. 사용자 데이터 형식이 너무 복잡하거나 기본 구현의 성능이 사용자 요구 사항을 충족하지 않는 등의 특정 경우에는 사용자가 고유한 구현을 연결할 수 있습니다.
   
   Java 쪽의 직렬화 인터페이스는 다음과 같이 정의됩니다.
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   C\# 쪽의 역직렬화 인터페이스는 다음과 같이 정의됩니다.
   
   공용 인터페이스 ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **C 쪽의 직렬화\# 및 Java 쪽의 역직렬화**
   
   C\# 쪽의 직렬화 메서드는 C\# 사용자 코드에서 지정해야 합니다.
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Java 쪽의 역직렬화 메서드는 SPEC 파일에서 지정해야 합니다.
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   여기에서 "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"는 역직렬화의 이름이며 "microsoft.scp.example.HybridTopology.Person"는 데이터가 역직렬화되는 대상 클래스입니다.
   
   사용자는 또한 C\# 직렬화 및 Java 역직렬화의 자체 구현을 플러그 인할 수 있습니다. 다음 코드는 C\# 직렬화에 대한 인터페이스입니다.
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   다음 코드는 Java 역직렬화에 대한 인터페이스입니다.
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP 호스트 모드
이 모드에서 사용자는 코드를 DLL로 컴파일한 다음 SCP에서 제공하는 SCPHost.exe를 사용하여 토폴로지를 제출할 수 있습니다. 사양 파일은 다음 코드와 같습니다.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

여기에서 `plugin.name`은(는) SCP SDK에서 제공된 `SCPHost.exe`(으)로 지정됩니다. SCPHost.exe는 다음 3개의 매개 변수를 사용합니다.

1. 첫 번째 매개 변수는 DLL 이름으로, 이 예에서는 `"HelloWorld.dll"` 입니다.
2. 두 번째 매개 변수는 Class 이름으로, 이 예에서는 `"Scp.App.HelloWorld.Generator"` 입니다.
3. 세 번째 매개 변수는 공용 정적 메서드의 이름으로, ISCPPlugin 인스턴스를 가져오기 위해 호출할 수 있습니다.

호스트 모드에서 사용자 코드는 DLL로 컴파일되며 SCP 플랫폼에 의해 호출됩니다. 따라서 SCP 플랫폼은 전체 처리 논리를 완전하게 제어할 수 있습니다. 따라서 고객은 SCP 호스트 모드에서 토폴로지를 제출하는 것이 좋습니다. 이렇게 하면 개발 환경을 간소화할 수 있으며 Microsoft가 향후 릴리스에서 유동성을 높이고 이전 버전과의 호환성을 보다 효율적으로 지원할 수 있습니다.

## <a name="scp-programming-examples"></a>SCP 프로그래밍 예제
### <a name="helloworld"></a>HelloWorld
**HelloWorld** 은 SCP.NET의 사용법을 확인할 수를 표시 하는 간단한 예제입니다. 여기에는 **generator**라는 Spout와 **splitter** 및 **counter**라는 Bolt 2개가 포함된 비트랜잭션 토폴로지가 사용됩니다. **generator** Spout는 문장을 임의로 생성하여 **splitter**로 내보냅니다. **splitter** Bolt는 이러한 문장을 단어로 분할한 다음 **counter** Bolt로 단어를 내보냅니다. "counter" Bolt는 사전을 사용하여 각 단어가 나오는 횟수를 기록합니다.

이 예제에는 **HelloWorld.spec** 및 **HelloWorld\_EnableAck.spec**의 두 가지 사양 파일이 있습니다. C\# 코드는 Java 쪽에서 pluginConf를 가져와 승인이 사용되는지 여부를 확인할 수 있습니다.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Spout ack를 사용 하는 경우 사전 승인 되지 않은 튜플을 캐시에 사용 됩니다. Fail()을 호출하면 오류가 발생한 튜플이 재생됩니다.

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

### <a name="helloworldtx"></a>HelloWorldTx
**HelloWorldTx** 예제에서는 트랜잭션 토폴로지 구현 방법을 보여 줍니다. 이 예제에는 **generator** Spout, **partial-count** Batch Bolt 및 **count-sum** Commit Bolt가 있습니다. 다음 세 개의 미리 작성된 txt 파일이 있습니다. **DataSource0.txt**, **DataSource1.txt** 및 **DataSource2.txt**.

각 트랜잭션에서 **generator** Spout는 미리 작성된 3개 파일 중 2개를 임의로 선택하여 이 두 파일 이름을 **partial-count** Bolt로 내보냅니다. 그러면 **partial-count** Batch Bolt는 수신된 튜플에서 파일 이름을 가져온 다음 파일을 열고 해당 파일의 단어 수를 계산합니다. 그리고 마지막으로 단어 수를 **count-sum** Bolt로 내보냅니다. **count-sum** Bolt는 총 단어 개수의 요약을 표시합니다.

**정확히 한 번** 의미 체계를 적용하려면 Commit Bolt **count-sum**은 해당 트랜잭션이 재생된 트랜잭션인지 여부를 판단해야 합니다. 이 예제에서는 "count-sum"에 정적 멤버 변수가 있습니다.

    public static long lastCommittedTxId = -1; 

ISCPBatchBolt 인스턴스를 만들면 입력 매개 변수에서 `txAttempt`를 가져옵니다.

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

`FinishBatch()`를 호출하면 `lastCommittedTxId`가 업데이트됩니다(재생된 트랜잭션이 아닌 경우).

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
이 토폴로지는 Java Spout와 C\# Bolt를 포함하며, SCP 플랫폼에서 제공하는 기본 직렬화 및 역직렬화 구현을 사용합니다. 사양 파일 세부 정보는 **examples\\HybridTopology** 폴더의 **HybridTopology.spec**을 참조하고 Java 클래스 경로를 지정하는 방법은 **SubmitTopology.bat**를 참조하세요.

### <a name="scphostdemo"></a>SCPHostDemo
이 예제는 기본적으로 HelloWorld와 같습니다. 차이점은 사용자 코드가 DLL로 컴파일되며 토폴로지가 SCPHost.exe를 사용하여 제출된다는 것뿐입니다. 자세한 설명은 "SCP 호스트 모드" 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
SCP를 사용하여 만든 Apache Storm 토폴로지 예제는 다음 문서를 참조하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)
* [HDInsight의 Apache Storm으로 Azure Event Hubs의 이벤트 처리](apache-storm-develop-csharp-event-hub-topology.md)
* [HDInsight의 Apache Storm을 사용하여 Event Hubs의 차량 센서 데이터 처리](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Azure Event Hubs에서 Apache HBase로 ETL(추출, 변환 및 로드)](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
