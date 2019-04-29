---
title: Azure Service Fabric에서 신뢰할 수 있는 컬렉션에 대한 지침 및 권장 사항 | Microsoft Docs
description: Service Fabric의 신뢰할 수 있는 컬렉션을 사용하기 위한 지침 및 권장 사항
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
ms.date: 12/10/2017
ms.author: aljo
ms.openlocfilehash: 810427c394c3912142e0a21cf1b5c29b81620afb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774100"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Azure Service Fabric에서 신뢰할 수 있는 컬렉션에 대한 지침 및 권장 사항
이 섹션에서는 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션을 사용하기 위한 지침을 제공합니다. 목표는 사용자에게 일반적인 문제가 발생하지 않도록 방지하는 것입니다.

지침은 *~하세요*, *~을(를) 고려하세요*, *~을(를) 피하세요* 및 *~하지 마세요.* 용어가 맨 끝에 추가된 간단한 권장 사항으로 구성됩니다.

* 읽기 작업에 의해 반환되는 사용자 지정 형식의 개체(예: `TryPeekAsync` 또는 `TryGetValueAsync`)는 수정하지 마세요. 신뢰할 수 있는 컬렉션은 동시 컬렉션처럼 개체에 대한 복사본이 아닌 참조를 반환합니다.
* 수정하기 전에 사용자 지정 형식의 반환된 개체에 대한 전체 복사를 수행합니다. 구조 및 기본 제공 형식이 값에 의한 전달이므로 수정하려는 참조 형식의 필드 또는 속성이 포함되지 않으면 전체 복사를 수행할 필요가 없습니다.
* 시간제한에 `TimeSpan.MaxValue` 를 사용하지 마세요. 시간 제한은 교착 상태를 감지하는 데 사용되어야 합니다.
* 트랜잭션을 커밋, 중단 또는 삭제한 후에는 사용하지 마십시오.
* 열거형이 만들어진 트랜잭션 범위 외부에서는 해당 열거형을 사용하지 마세요.
* 다른 트랜잭션의 `using` 문 내에 트랜잭션을 만들지 마세요. 교착 상태가 발생할 수 있습니다.
* 사용 하 여 신뢰할 수 있는 상태를 만들지 마십시오 `IReliableStateManager.GetOrAddAsync` 동일한 트랜잭션에서 신뢰할 수 있는 상태를 사용 합니다. 이 인해 InvalidOperationException 발생 합니다.
* `IComparable<TKey>` 구현이 올바른지 확인하세요. 시스템은 검사점 및 행 병합을 위해 `IComparable<TKey>`에 대한 종속성을 보유합니다.
* 특정 유형의 교착 상태를 방지하기 위해 항목을 업데이트하려는 경우에는 항목을 읽을 때 업데이트 잠금을 사용하지 마세요.
* 파티션당 신뢰할 수 있는 컬렉션 수를 1,000개 미만으로 유지하세요. 항목 수가 더 적은 신뢰할 수 있는 컬렉션보다 항목 수가 더 많은 신뢰할 수 있는 컬렉션이 우선됩니다.
* 항목(예: 신뢰할 수 있는 사전에 대한 TKey + TValue)을 80KB 미만으로 유지하는 것이 좋으며, 작을수록 더 좋습니다. 이렇게 하면 디스크 및 네트워크 IO 요구 사항뿐만 아니라 큰 개체 힙 사용량도 줄어듭니다. 주로 값의 작은 부분만 업데이트할 때 중복 데이터 복제도 줄어듭니다. 신뢰할 수 있는 사전에서 이를 달성하는 일반적인 방법은 행을 여러 행으로 나누는 것입니다.
* 재해 복구를 위해 백업 및 복원 기능을 사용하는 것이 좋습니다.
* 격리 수준이 다르기 때문에 동일한 트랜잭션 내에서 단일 엔터티 작업 및 다중 엔터티 작업을 혼합하지 마세요(예: `GetCountAsync`, `CreateEnumerableAsync`).
* InvalidOperationException을 처리합니다. 여러 가지 이유로 시스템에서 사용자 트랜잭션이 중단될 수 있습니다. 예를 들어 신뢰할 수 있는 상태 관리자가 해당 역할을 기본 역할에서 다른 역할로 변경하거나 장기 실행 트랜잭션이 트랜잭션 로그 잘림을 차단하는 경우가 여기에 해당합니다. 이러한 경우 트랜잭션이 이미 종료되었다는 InvalidOperationException이 표시될 수 있습니다. 트랜잭션의 종료를 사용자가 요청하지 않다고 가정할 경우 이 예외를 처리하는 가장 좋은 방법은 트랜잭션을 삭제하고, 취소 토큰이 신호로 제공되었는지(또는 복제본의 역할이 변경) 확인하고, 그러한 경우에는 새 트랜잭션을 만든 후 다시 시도하는 것입니다.  

이때

* 모든 신뢰할 수 있는 컬렉션 API의 기본 제한 시간은 4초입니다. 대부분의 사용자는 기본 제한 시간을 사용하는 것이 좋습니다.
* 기본 취소 토큰은 신뢰할 수 있는 모든 컬렉션 API의 `CancellationToken.None` 입니다.
* 신뢰할 수 있는 사전의 키 형식 매개 변수(*TKey*)는 `GetHashCode()` 및 `Equals()`을 정확히 구현해야 합니다. 키는 변경하지 않아야 합니다.
* 신뢰할 수 있는 컬렉션의 높은 가용성을 달성하려면 각 서비스에 하나 이상의 대상 및 최소 복제본 세트 크기 3이 있어야 합니다.
* 보조에서 읽기 작업은 쿼럼 커밋되지 않는 버전을 읽을 수 있습니다.
  즉, 단일 보조에서 읽은 데이터 버전은 거짓 처리될 수 있습니다.
  주에서 읽은 내용은 항상 안정적이며 거짓 처리될 수 없습니다.
* 애플리케이션에서 유지되는 데이터의 보안/개인 정보 보호는 사용자가 결정하게 되며, 저장소 관리에서 제공하는 보호 기능이 적용됩니다. 운영 체제 디스크 암호화는 미사용 데이터를 보호하는 데 사용될 수 있습니다.  

### <a name="next-steps"></a>다음 단계
* [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
* [트랜잭션 및 잠금](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* 데이터 관리
  * [Backup 및 복원](service-fabric-reliable-services-backup-restore.md)
  * [Notifications](service-fabric-reliable-services-notifications.md)
  * [Serialization 및 업그레이드](service-fabric-application-upgrade-data-serialization.md)
  * [신뢰할 수 있는 상태 관리자 구성](service-fabric-reliable-services-configuration.md)
* 기타
  * [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
  * [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
