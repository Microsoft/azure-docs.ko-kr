---
title: Azure Service Fabric 상태 저장 서비스의 신뢰할 수 있는 컬렉션 소개 | Microsoft Docs
description: Service Fabric 상태 저장 서비스는 가용성 높고, 확장 가능하며, 대기 시간이 낮은 클라우드 애플리케이션을 작성할 수 있는 안정적인 컬렉션을 제공합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: aljo
ms.openlocfilehash: 4ed76b207db4712058b5524cd1b31fd65b0e19a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773646"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Azure 서비스 패브릭 상태 저장 서비스의 신뢰할 수 있는 컬렉션 소개

신뢰할 수 있는 컬렉션을 사용하면 단일 컴퓨터 애플리케이션을 작성하는 것처럼 가용성이 높고, 확장 가능하며, 대기 시간이 낮은 클라우드 애플리케이션을 작성할 수 있습니다. **Microsoft.ServiceFabric.Data.Collections** 네임스페이스의 클래스는 상태를 자동으로 항상 사용할 수 있도록 하는 컬렉션 집합을 제공합니다. 개발자는 신뢰할 수 있는 컬렉션 API로 프로그래밍하고 신뢰할 수 있는 컬렉션이 복제된 로컬 상태를 관리하도록 하기만 하면 됩니다.

신뢰할 수 있는 컬렉션과 다른 고가용성 기술 (예: Redis, Azure Table service 및 Azure 큐 서비스)의 주요 차이점은 상태가 서비스 인스턴스에서 로컬로 유지되지만 가용성도 높다는 점입니다. 이는 다음을 의미합니다.

* 모든 읽기가 로컬이므로 대기 시간이 짧고 처리량이 많습니다.
* 모든 쓰기가 최소한의 네트워크 IO 수를 유발하므로 대기 시간이 짧고 처리량이 많습니다.

![컬렉션 진화 이미지](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

신뢰할 수 있는 컬렉션은 **System.Collections** 클래스의 자연스러운 진화로 생각할 수 있습니다. 즉, 개발자의 복잡성을 늘리지 않고 클라우드 및 다중 컴퓨터 애플리케이션을 위해 설계된 컬렉션의 새 집합입니다. 따라서 신뢰할 수 있는 컬렉션은 다음과 같습니다.

* 복제됨: 고가용성을 위해 상태 변경 내용이 복제됩니다.
* 유지됨: 대규모 정전에 대한 내구성을 위해 데이터가 디스크에 유지됩니다(예: 데이터 센터 전원 정전).
* 쓰기는 유지되고 복제되므로 메모리 내 데이터만 유지하는 휘발성 ReliableDictionary, ReliableQueue 또는 기타 신뢰할 수 있는 컬렉션은 만들 수 없습니다.
* 비동기: API는 IO 발생 시 스레드가 차단되지 않도록 비동기적입니다.
* 트랜잭션: API가 트랜잭션 추상화를 활용하므로 서비스 내에서 신뢰할 수 있는 여러 컬렉션을 쉽게 관리할 수 있습니다.

신뢰할 수 있는 컬렉션은 기본적으로 강력한 일관성을 보장하여 애플리케이션 상태에 대한 추론을 보다 쉽게 해줍니다.
강력한 일관성은 주 복제본을 포함한 복제본의 과반수 쿼럼에 전체 트랜잭션이 기록된 후에만 트랜잭션 커밋을 완료하여 수행됩니다.
약한 일관성을 달성하려면 비동기 커밋이 반환되기 전에 애플리케이션이 클라이언트/요청자에 다시 승인할 수 있습니다.

신뢰할 수 있는 컬렉션 API는 동시 컬렉션 API( **System.Collections.Concurrent** 네임스페이스에 있음)의 진화입니다.

* 비동기: 동시 컬렉션과 달리 작업이 복제 및 유지되므로 작업을 반환합니다.
* 출력 매개 변수 없음: `ConditionalValue<T>`를 사용하여 출력 매개 변수 대신 `bool` 및 값을 반환합니다. `ConditionalValue<T>`는 `Nullable<T>`과 유사하지만 T가 구조체일 필요는 없습니다.
* 트랜잭션: 트랜잭션 개체를 사용하여 사용자가 트랜잭션의 신뢰할 수 있는 여러 컬렉션에 대한 작업을 그룹화하도록 지원합니다.

오늘날 **Microsoft.ServiceFabric.Data.Collections** 은 다음과 같은 3가지 컬렉션을 포함합니다.

* [신뢰할 수 있는 사전](https://msdn.microsoft.com/library/azure/dn971511.aspx): 키/값 쌍의 복제, 트랜잭션 및 비동기 컬렉션을 나타냅니다. **ConcurrentDictionary**와 유사하게 키와 값은 모든 형식일 수 있습니다.
* [신뢰할 수 있는 큐](https://msdn.microsoft.com/library/azure/dn971527.aspx): 복제, 트랜잭션 및 비동기의 엄격한 FIFO(선입 선출) 큐를 나타냅니다. **ConcurrentQueue**와 유사하게 값은 어떤 형식일 수 있습니다.
* [신뢰할 수 있는 동시 큐](service-fabric-reliable-services-reliable-concurrent-queue.md): 높은 처리량을 위해 최고의 순서로 대기되는 복제, 트랜잭션 및 비동기 큐를 나타냅니다. **ConcurrentQueue**와 유사하게 값은 어떤 형식일 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [신뢰할 수 있는 컬렉션 지침 및 권장 사항](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
* [트랜잭션 및 잠금](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* 데이터 관리
  * [Backup 및 복원](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [신뢰할 수 있는 컬렉션 serialization](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serialization 및 업그레이드](service-fabric-application-upgrade-data-serialization.md)
  * [신뢰할 수 있는 상태 관리자 구성](service-fabric-reliable-services-configuration.md)
* 기타
  * [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
  * [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
