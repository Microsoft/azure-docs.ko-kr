---
title: Azure Service Fabric의 신뢰할 수 있는 컬렉션 개체 serialization | Microsoft Docs
description: Azure Service Fabric 신뢰할 수 있는 컬렉션 개체 serialization
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: aljo
ms.openlocfilehash: ee19be45915b3ff1253ec721f4334fead19647b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723602"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Azure Service Fabric의 신뢰할 수 있는 컬렉션 개체 serialization
신뢰할 수 있는 컬렉션은 해당 항목을 복제하고 유지하여 컴퓨터 장애 및 정전이 발생해도 지속되도록 합니다.
항목을 복제하고 유지하기 위해 신뢰할 수 있는 컬렉션에서 항목을 직렬화해야 합니다.

신뢰할 수 있는 컬렉션은 Reliable State Manager로부터 지정된 형식에 적합한 직렬 변환기를 가져옵니다.
Reliable State Manager에는 기본 제공 직렬 변환기가 포함되어 있으며, 지정된 형식에 대한 사용자 지정 직렬 변환기를 등록할 수 있도록 허용합니다.

## <a name="built-in-serializers"></a>기본 제공 직렬 변환기

Reliable State Manager에는 일부 공용 형식에 대한 기본 제공 직렬 변환기가 포함되어 있으므로 기본적으로 해당 형식을 효율적으로 직렬화할 수 있습니다. 다른 형식의 경우 Reliable State Manager에서 [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx)를 사용합니다.
기본 제공 직렬 변환기는 해당 형식이 변경될 수 없음을 알고 있으며 형식 이름 등의 형식 정보를 포함할 필요가 없으므로 더 효율적입니다.

Reliable State Manager에는 다음 형식에 대한 기본 제공 직렬 변환기가 있습니다. 
- Guid
- bool
- byte
- sbyte
- byte[]
- char
- 문자열
- decimal
- double
- float
- int
- uint
- long
- ulong
- short
- ushort

## <a name="custom-serialization"></a>사용자 지정 serialization

사용자 지정 직렬 변환기는 성능 향상이나 네트워크 및 디스크의 데이터 암호화에 주로 사용됩니다. 무엇보다, 사용자 지정 직렬 변환기는 형식 정보를 직렬화해야 할 필요가 없기 때문에 일반적으로 제네릭 직렬 변환기보다 더 효율적입니다. 

[IReliableStateManager.TryAddStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer)는 지정된 형식 T에 대한 사용자 지정 직렬 변환기를 등록하는 데 사용됩니다. 이 등록은 StatefulServiceBase 생성 시 수행되어 복구가 시작되기 전에 신뢰할 수 있는 모든 컬렉션이 적절한 직렬 변환기에 액세스하여 영구 데이터를 읽을 수 있도록 해야 합니다.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> 사용자 지정 직렬 변환기가 기본 제공 직렬 변환기보다 우선 적용됩니다. 예를 들어 int에 대한 사용자 지정 직렬 변환기를 등록하면 int에 대한 기본 제공 직렬 변환기 대신 이 직렬 변환기가 정수 직렬화에 사용됩니다.

### <a name="how-to-implement-a-custom-serializer"></a>사용자 지정 직렬 변환기를 구현하는 방법

사용자 지정 직렬 변환기는 [IStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) 인터페이스를 구현해야 합니다.

> [!NOTE]
> IStateSerializer<T>에는 기준 값이라는 추가 T를 사용하는 쓰기 및 읽기 오버로드가 포함되어 있습니다. 이 API는 차등 serialization에 사용됩니다. 현재 차등 serialization 기능은 노출되지 않습니다. 따라서 이러한 두 오버로드는 차등 serialization이 노출되고 사용될 때까지 호출되지 않습니다.

다음은 4가지 속성을 포함하는 OrderKey라는 사용자 지정 형식 예제입니다.

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

다음은 IStateSerializer의 예제 구현은\<OrderKey >.
baseValue를 사용하는 읽기 및 쓰기 오버로드는 이후 버전과의 호환성을 위해 해당 오버로드를 호출합니다.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>업그레이드 가능성
[롤링 애플리케이션 업그레이드](service-fabric-application-upgrade.md)에서는 한번에 하나의 업그레이드 도메인에서 노드의 하위 집합에 업그레이드가 적용됩니다. 이 과정에서 일부 업그레이드는 애플리케이션의 최신 버전에, 일부 업그레이드 도메인은 애플리케이션의 이전 버전에 적용됩니다. 롤아웃 동안 최신 버전의 애플리케이션에서 이전 버전의 데이터를 읽을 수 있고 이전 버전의 애플리케이션에서 최신 버전의 데이터를 읽을 수 있어야 합니다. 데이터 서식이 최신 버전과 이전 버전에서 호환되지 않으면 업그레이드가 실패하거나 데이터가 손실되거나 손상될 수도 있습니다.

기본 제공 직렬 변환기를 사용하는 경우 호환성에 대해 염려할 필요가 없습니다.
그러나 사용자 지정 직렬 변환기 또는 DataContractSerializer를 사용하는 경우 데이터가 이전 버전 및 이후 버전과 무기한 호환되어야 합니다.
즉, 각 버전의 직렬 변환기가 해당 형식의 모든 버전을 직렬화 및 deserialize할 수 있어야 합니다.

데이터 계약 사용자는 필드의 추가, 제거 및 변경에 대해 잘 정의된 버전 관리 규칙을 따라야 합니다. 데이터 계약은 알 수 없는 필드를 처리하는 기능, serialization 및 deserialization 프로세스에 연결하는 기능, 클래스 상속을 처리하는 기능도 지원합니다. 자세한 내용은 [데이터 계약 사용](https://msdn.microsoft.com/library/ms733127.aspx)을 참조하세요.

사용자 지정 직렬 변환기 사용자는 이전 버전 및 이후 버전과의 호환성을 유지하기 위해 사용 중인 직렬 변환기의 지침을 준수해야 합니다.
모든 버전을 지원하는 일반적인 방법은 시작 부분에 크기 정보를 추가하고 선택적 속성만 추가하면 됩니다.
그러면 각 버전이 가능한 양만큼 읽고 스트림의 나머지 부분을 건너뛸 수 있습니다.

## <a name="next-steps"></a>다음 단계
  * [serialization 및 업그레이드](service-fabric-application-upgrade-data-serialization.md)
  * [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.
  * [Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.
  * [업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.
  * [고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.
  * [애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.
