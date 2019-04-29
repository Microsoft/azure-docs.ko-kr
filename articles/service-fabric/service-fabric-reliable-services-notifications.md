---
title: Reliable Services 알림 | Microsoft Docs
description: 서비스 패브릭 Reliable Services 알림에 대한 개념 설명서
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: masnider,vturecek
ms.assetid: cdc918dd-5e81-49c8-a03d-7ddcd12a9a76
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/29/2017
ms.author: mcoskun
ms.openlocfilehash: a3df5f28475b03f1799dc1e245c3a7e904b49cb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772927"
---
# <a name="reliable-services-notifications"></a>Reliable Services 알림
알림을 사용하면 클라이언트에서 관심 있는 개체에 대한 변경 내용을 추적할 수 있습니다. 두 가지 유형의 개체에는 알림을 지원합니다. *신뢰할 수 있는 상태 관리자* 하 고 *신뢰할 수 있는 사전*합니다.

알림을 사용하는 일반적인 이유:

* 보조 인덱스 또는 복제본 상태에 대한 집계된 필터링 보기와 같은 구체화된 보기를 구축하기 위해서입니다. 신뢰할 수 있는 사전에 있는 모든 키의 정렬된 인덱스가 그 예입니다.
* 지난 1시간 동안 추가된 사용자 수와 같은 모니터링 데이터를 전송하기 위해서입니다.

알림은 작업 적용의 일부로 실행됩니다. 이 때문에 알림은 가능한 한 빨리 처리되어야 하며 동기 이벤트는 광범위한 작업을 포함하지 않아야 합니다.

## <a name="reliable-state-manager-notifications"></a>신뢰할 수 있는 상태 관리자 알림
신뢰할 수 있는 상태 관리자는 다음 이벤트에 대한 알림을 제공합니다.

* 트랜잭션
  * 커밋
* 상태 관리자
  * 다시 빌드
  * 신뢰할 수 있는 상태 추가
  * 신뢰할 수 있는 상태 제거

신뢰할 수 있는 상태 관리자는 현재 처리 중인 트랜잭션을 추적합니다. 알림이 실행되도록 하는 유일한 트랜잭션 상태 변경은 커밋 중인 트랜잭션입니다.

신뢰할 수 있는 상태 관리자는 신뢰할 수 있는 사전 및 신뢰할 수 있는 큐와 같은 신뢰할 수 있는 상태 컬렉션을 유지 관리합니다. 신뢰할 수 있는 상태 관리자는 이 컬렉션이 변경될 때, 즉 신뢰할 수 있는 상태가 추가 또는 제거되거나 전체 컬렉션이 다시 작성될 때 알림을 실행합니다.
신뢰할 수 있는 상태 관리자 컬렉션은 다음 세 가지 경우에 다시 작성됩니다.

* 복구: 복제본 시작 되 면 디스크에서 이전 상태로 복구 합니다. 복구가 끝나면 **NotifyStateManagerChangedEventArgs** 를 사용하여 복구된 신뢰할 수 있는 상태 집합이 포함된 이벤트를 실행합니다.
* 전체 복사: 복제본 수 구성 집합에 가입 하기 전에를 구축할 수 있습니다. 경우에 따라 주 복제본의 신뢰할 수 있는 상태 관리자 상태의 전체 복사본을 유휴 보조 복제본에 적용해야 합니다. 보조 복제본의 신뢰할 수 있는 상태 관리자는 **NotifyStateManagerChangedEventArgs** 를 사용하여 주 복제본에서 획득한 신뢰할 수 있는 상태 집합이 포함된 이벤트를 실행합니다.
* 복원: 재해 복구 시나리오에는 복제본의 상태를 통해 백업에서 복원할 수 있습니다 **RestoreAsync**합니다. 이러한 경우 주 복제본의 신뢰할 수 있는 상태 관리자는 **NotifyStateManagerChangedEventArgs** 를 사용하여 백업에서 복원한 신뢰할 수 있는 상태 집합이 포함된 이벤트를 실행합니다.

트랜잭션 알림 및/또는 상태 관리자 알림을 등록하려면 신뢰할 수 있는 상태 관리자의 **TransactionChanged** 또는 **StateManagerChanged** 이벤트에 등록해야 합니다. 이러한 이벤트 처리기에 등록하는 일반적인 위치는 상태 저장 서비스의 생성자입니다. 생성자에 등록하면 **IReliableStateManager**의 수명 동안 변경 내용으로 발생되는 모든 알림을 놓치지 않게 됩니다.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** 이벤트 처리기는 **NotifyTransactionChangedEventArgs**를 사용하여 이벤트에 대한 세부 정보를 제공합니다. 변경 유형을 지정하는 작업 속성(예: **NotifyTransactionChangedAction.Commit**)을 포함합니다. 또한 변경된 트랜잭션에 대한 참조를 제공하는 트랜잭션 속성을 포함합니다.

> [!NOTE]
> 오늘날 **TransactionChanged** 이벤트는 트랜잭션이 커밋되는 경우에만 발생합니다. 작업은 **NotifyTransactionChangedAction.Commit**와 동일합니다. 하지만 나중에 다른 유형의 트랜잭션 상태 변경에 대해 이벤트가 발생할 수 있습니다. 작업을 확인하고, 예상되는 작업인 경우에만 이벤트를 처리하는 것이 좋습니다.
> 
> 

다음은 예제 **TransactionChanged** 이벤트 처리기입니다.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**StateManagerChanged** 이벤트 처리기는 **NotifyStateManagerChangedEventArgs**를 사용하여 이벤트에 대한 세부 정보를 제공합니다.
**NotifyStateManagerChangedEventArgs** 두 서브 클래스에: **NotifyStateManagerRebuildEventArgs** 하 고 **NotifyStateManagerSingleEntityChangedEventArgs**합니다.
**NotifyStateManagerChangedEventArgs**의 작업 속성을 사용하여 **NotifyStateManagerChangedEventArgs**를 올바른 하위 클래스로 캐스트합니다.

* **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
* **NotifyStateManagerChangedAction.Add** 하 고 **NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

다음은 예제 **StateManagerChanged** 알림 처리기입니다.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>신뢰할 수 있는 사전 알림
신뢰할 수 있는 사전은 다음 이벤트에 대한 알림을 제공합니다.

* 다시 빌드: 될 때 호출 **ReliableDictionary** 복구 또는 복사 된 로컬 상태 또는 백업에서 해당 상태를 복구 했습니다.
* 선택을 취소 합니다. 될 때 호출 상태의 **ReliableDictionary** 통해 합니다 **ClearAsync** 메서드.
* 추가: 항목에 추가 될 때 호출 **ReliableDictionary**합니다.
* 업데이트: 항목을 호출 **IReliableDictionary** 업데이트 되었습니다.
* 제거 합니다. 항목을 호출 **IReliableDictionary** 삭제 되었습니다.

신뢰할 수 있는 사전 알림을 가져오려면 **IReliableDictionary**에서 **DictionaryChanaged** 이벤트 처리기에 등록해야 합니다. 이러한 이벤트 처리기를 등록하는 일반적인 위치는 **ReliableStateManager.StateManagerChanged** 추가 알림입니다.
**IReliableDictionary**가 **IReliableStateManager**에 추가되었을 때 등록하면 알림을 놓치지 않게 됩니다.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **ProcessStateManagerSingleEntityNotification**은 위의 **OnStateManagerChangedHandler** 예제에서 호출하는 샘플 메서드입니다.
> 
> 

위의 코드는 **DictionaryChanged**와 함께 **IReliableNotificationAsyncCallback** 인터페이스도 설정합니다. **NotifyDictionaryRebuildEventArgs**에는 비동기식으로 열거되어야 하는 **IAsyncEnumerable** 인터페이스가 포함되어 있으므로 **OnDictionaryChangedHandler** 대신 **RebuildNotificationAsyncCallback**을 통해 다시 작성 알림이 실행됩니다.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> 위의 코드에서 다시 작성 알림 처리의 일부로, 먼저 유지 관리되던 집계된 상태가 지워집니다. 신뢰할 수 있는 컬렉션은 새 상태로 다시 작성되므로 모든 이전 알림은 관련이 없어지기 때문입니다.
> 
> 

**DictionaryChanged** 이벤트 처리기는 **NotifyDictionaryChangedEventArgs**를 사용하여 이벤트에 대한 세부 정보를 제공합니다.
**NotifyDictionaryChangedEventArgs** 에는 5개의 하위 클래스가 있습니다. **NotifyDictionaryChangedEventArgs**의 작업 속성을 사용하여 **NotifyDictionaryChangedEventArgs**를 올바른 하위 클래스로 캐스트합니다.

* **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
* **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
* **NotifyDictionaryChangedAction.Add** 하 고 **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
* **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
* **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>권장 사항
* *하세요* .
* *마세요* .
* *하세요* . 새 작업 형식이 나중에 추가될 수 있습니다.

이때

* 알림이 작업 실행의 일부로 실행됩니다. 예를 들어 복원 작업의 마지막 단계로 복원 알림이 실행됩니다. 복원은 알림 이벤트가 처리될 때까지 완료되지 않습니다.
* 알림이 작업 적용의 일부로 실행되므로 클라이언트에는 로컬로 커밋된 작업에 대한 알림만 표시됩니다. 작업은 로컬로만 커밋(즉, 로깅)되도록 보장되므로 나중에 실행을 취소할 수도 있고 그렇지 않을 수도 있습니다.
* 다시 실행 경로에서는 적용된 각 작업에 대해 단일 알림이 실행됩니다. 즉, 트랜잭션 T1에 Create(X), Delete(X), Create(X)가 포함되는 경우 이 순서대로 X 생성에 대한 알림을 한 번 받고 삭제 알림을 받은 후 생성 알림을 한 번 더 받습니다.
* 여러 작업을 포함하는 트랜잭션의 경우 작업이 사용자로부터 주 복제본에 수신된 순서대로 적용됩니다.
* 거짓 진행률 처리의 일부로, 일부 작업이 실행 취소될 수 있습니다. 이러한 실행 취소 작업에 대해 알림이 발생하고 복제본의 상태가 안정적인 지점으로 롤백됩니다. 실행 취소 알림의 중요한 차이점은 중복 키가 있는 이벤트가 집계된다는 것입니다. 예를 들어 트랜잭션 T1이 실행 취소되는 경우 Delete(X)에 대한 단일 알림이 표시됩니다.

## <a name="next-steps"></a>다음 단계
* [신뢰할 수 있는 컬렉션](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
* [Reliable Services 백업 및 복원(재해 복구)](service-fabric-reliable-services-backup-restore.md)
* [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

