---
title: Azure Service Fabric 신뢰할 수 있는 컬렉션의 트랜잭션 및 잠금 모드 | Microsoft Docs
description: Azure Service Fabric 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션 트랜잭션 및 잠금.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774066"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric 신뢰할 수 있는 컬렉션의 트랜잭션 및 잠금 모드

## <a name="transaction"></a>트랜잭션
트랜잭션은 작업의 단일 논리적 단위로 수행되는 작업 시퀀스입니다.
트랜잭션은 다음 ACID 속성을 나타내야 합니다. (참조: https://technet.microsoft.com/library/ms190612)
* **원자성**: 트랜잭션은 작업의 원자 단위여야 합니다. 즉, 모든 데이터 수정 작업이 수행되거나, 또는 하나도 수행되지 않아야 합니다.
* **일관성**: 완료되면 트랜잭션은 모든 데이터를 일관된 상태로 유지해야 합니다. 모든 내부 데이터 구조는 트랜잭션이 끝날 때 정확해야 합니다.
* **격리**: 동시 트랜잭션에 의한 수정은 다른 동시 트랜잭션에 의한 수정과 격리되어야 합니다. ITransaction 내에서 작업에 사용되는 격리 수준은 작업을 수행하는 IReliableState에 의해 결정됩니다.
* **내구성**: 트랜잭션이 완료되면 그 영향이 시스템에 영구적으로 유지됩니다. 수정은 시스템 오류가 발생하는 경우에도 지속됩니다.

### <a name="isolation-levels"></a>격리 수준
격리 수준은 트랜잭션은 다른 트랜잭션에 의해 수정되지 않도록 격리해야 하는 정도를 정의합니다.
신뢰할 수 있는 컬렉션에서 지원되는 두 격리 수준이 있습니다.

* **반복 가능한 읽기**: 명령문은 수정되었지만 다른 트랜잭션에서 아직 커밋되지 않은 데이터를 읽을 수 없으며 현재 트랜잭션이 완료될 때까지 다른 트랜잭션은 현재 트랜잭션에서 읽은 데이터를 수정할 수 없음을 지정합니다. 자세한 내용은 [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)를 참조하세요.
* **스냅숏**: 트랜잭션의 명령문에서 읽은 데이터가 트랜잭션 시작 부분에 존재하는 데이터의 트랜잭션이 일치되는 버전이 되도록 지정합니다.
  트랜잭션은 트랜잭션 시작 전에 커밋된 데이터 수정 내용만 인식할 수 있습니다.
  현재 트랜잭션이 시작된 후 다른 트랜잭션에서 수행한 데이터 수정 내용은 현재 트랜잭션에서 실행 중인 문에 표시되지 않습니다.
  그 결과 트랜잭션의 문이 트랜잭션 시작 당시 커밋된 데이터의 스냅숏을 가져오는 것처럼 됩니다.
  스냅숏은 신뢰할 수 있는 컬렉션 간에 일관됩니다.
  자세한 내용은 [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)를 참조하세요.

신뢰할 수 있는 컬렉션은 트랜잭션을 만들 당시의 작업 및 복제본의 역할에 따라 자동으로 특정 읽기 작업에 사용할 격리 수준을 선택합니다.
다음 테이블에는 신뢰할 수 있는 사전 및 큐 작업에 대한 기본 격리 수준이 나와 있습니다.

| 작업 \ 역할 | 보조 | 주 |
| --- |:--- |:--- |
| 단일 엔터티 읽기 |반복 가능한 읽기 |스냅숏 |
| 열거형, 개수 |스냅숏 |스냅숏 |

> [!NOTE]
> 단일 엔터티 작업에 대한 일반적인 예제는 `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`입니다.
> 

신뢰할 수 있는 사전 및 신뢰할 수 있는 큐는 모두 읽기 프로그램 작성을 지원합니다.
즉, 특정 트랜잭션 내 모든 쓰기가 동일한 트랜잭션에 속하는 다음 읽기에 표시됩니다.

## <a name="locks"></a>잠금
신뢰할 수 있는 컬렉션의 모든 트랜잭션은 엄격한 2단계 잠금을 구현합니다. 트랜잭션은 중단 또는 커밋으로 인해 종료되어야만 확보한 잠금을 해제합니다.

신뢰할 수 있는 사전은 모든 단일 엔터티 작업에 대해 행 수준 잠금을 사용합니다.
신뢰할 수 있는 큐와 엄격한 트랜잭션 FIFO 속성의 동시성은 서로 균형을 유지합니다.
신뢰할 수 있는 큐는 작업 수준 잠금을 사용하여 한 번에 한 트랜잭션에는 `TryPeekAsync` 및/또는 `TryDequeueAsync`를 허용하고, 다른 트랜잭션에는 `EnqueueAsync`를 허용합니다.
FIFO를 유지하기 위해 `TryPeekAsync` 또는 `TryDequeueAsync`는 신뢰할 수 있는 큐가 비어 있음을 확인하면 `EnqueueAsync`도 잠급니다.

쓰기 작업은 항상 배타적 잠금을 수행합니다.
읽기 작업의 경우 몇 가지 요인에 따라 잠금이 적용됩니다.
스냅숏 격리를 사용하여 수행된 모든 읽기 작업은 잠금이 없습니다.
모든 반복 가능한 읽기 작업은 기본적으로 공유 잠금을 수행합니다.
그러나 사용자는 반복 가능한 읽기를 지원하는 모든 읽기 작업에 대해 공유 잠금 대신 업데이트 잠금을 요청할 수 있습니다.
업데이트 잠금에는 이후의 잠재적 업데이트를 위해 여러 트랜잭션이 리소스를 잠글 때 발생하는 일반적인 형태의 교착 상태를 방지하는 데 사용되는 비대칭 잠금입니다.

잠금 호환성 매트릭스는 다음 테이블에서 확인할 수 있습니다.

| 요청 \ 부여 | 없음 | 공유됨 | 주 지역에서 | 단독 |
| --- |:--- |:--- |:--- |:--- |
| 공유됨 |충돌 없음 |충돌 없음 |충돌 |충돌 |
| 주 지역에서 |충돌 없음 |충돌 없음 |충돌 |충돌 |
| 단독 |충돌 없음 |충돌 |충돌 |충돌 |

신뢰할 수 있는 컬렉션 API의 시간 제한 인수는 교착 상태 감지를 위해 사용됩니다.
예를 들어 두 트랜잭션(T1과 T2)은 K1을 읽고 업데이트하려고 합니다.
둘 다 결국 공유된 잠금을 가지게 되기 때문에 교착 상태가 될 수 있습니다.
이 경우 하나 또는 두 작업이 시간 초과됩니다.

이 교착 상태 시나리오는 업데이트 잠금이 교착 상태를 방지하는 방법을 보여주는 좋은 예입니다.

## <a name="next-steps"></a>다음 단계
* [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 알림](service-fabric-reliable-services-notifications.md)
* [Reliable Services 백업 및 복원(재해 복구)](service-fabric-reliable-services-backup-restore.md)
* [신뢰할 수 있는 상태 관리자 구성](service-fabric-reliable-services-configuration.md)
* [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

