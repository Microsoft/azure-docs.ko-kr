---
title: '애플리케이션 업그레이드: 데이터 직렬화 | Microsoft Docs'
description: 데이터 직렬화 모범 사례 및 애플리케이션 업그레이드 배포에 미치는 영향
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 55cbd869e7434469ebddd7af493c91bfedafc594
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614441"
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>데이터 serialization이 애플리케이션 업그레이드에 미치는 영향
[롤링 애플리케이션 업그레이드](service-fabric-application-upgrade.md)에서는 한번에 하나의 업그레이드 도메인에서 노드의 하위 집합에 업그레이드가 적용됩니다. 이 과정에서 일부 업그레이드 도메인은 애플리케이션의 최신 버전에, 일부 업그레이드 도메인은 애플리케이션의 이전 버전에 적용됩니다. 롤아웃 동안 최신 버전의 애플리케이션에서 이전 버전의 데이터를 읽을 수 있고 이전 버전의 애플리케이션에서 최신 버전의 데이터를 읽을 수 있어야 합니다. 데이터 서식이 최신 버전과 이전 버전에서 호환되지 않으면 업그레이드가 실패하거나 데이터가 손실되거나 손상될 수도 있습니다. 이 문서에서는 데이터 서식을 구성하는 요소에 대해 설명하며 데이터가 상위 버전 및 하위 버전과 호환되도록 하는 모범 사례를 제공합니다.

## <a name="what-makes-up-your-data-format"></a>데이터 형식을 구성하는 요소
Azure 서비스 패브릭에서 유지 및 복제되는 데이터는 C# 클래스에서 제공됩니다. [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)을 사용하는 애플리케이션의 경우 데이터는 신뢰할 수 있는 사전 및 큐의 개체입니다. [Reliable Actors](service-fabric-reliable-actors-introduction.md)를 사용하는 애플리케이션의 경우 행위자에 대한 백업 상태입니다. 이러한 C# 클래스를 유지 및 복제하려면 직렬화해야 합니다. 따라서 직렬화된 필드 및 속성을 통해 데이터 형식과 직렬화 방법을 정의합니다. 예를 들어 `IReliableDictionary<int, MyClass>`에서 데이터는 직렬화된 `int` 및 직렬화된 `MyClass`입니다.

### <a name="code-changes-that-result-in-a-data-format-change"></a>데이터 형식 변경이 발생하는 코드 변경
데이터 형식은 C# 클래스에 의해 결정되기 때문에 클래스를 변경하면 데이터 형식이 변경될 수 있습니다. 롤링 업그레이드에서 데이터 서식 변경을 처리할 수 있도록 주의를 기울여야 합니다. 다음은 데이터 형식이 변경될 수 있는 예입니다.

* 필드/속성 추가 또는 제거
* 필드 또는 속성의 이름 변경
* 필드 또는 속성의 유형 변경
* 클래스 이름 또는 네임스페이스 변경

### <a name="data-contract-as-the-default-serializer"></a>기본 직렬 변환기인 데이터 계약
직렬 변환기는 일반적으로 데이터가 이전 버전이든 *최신* 버전이든 데이터를 읽고 현재 버전으로 역직렬화하는 역할을 담당합니다. 기본 직렬 변환기는 버전 관리 규칙이 잘 정의된 [데이터 계약 직렬 변환기](https://msdn.microsoft.com/library/ms733127.aspx)입니다. 신뢰할 수 있는 컬렉션을 사용하면 직렬 변환기를 재정의할 수 있지만 Reliable Actors를 사용하면 직렬 변환기를 재정의할 수 없습니다. 데이터 직렬 변환기는 롤링 업그레이드에서 중요한 역할을 합니다. 데이터 계약 직렬 변환기는 서비스 패브릭 애플리케이션에 권장되는 직렬 변환기입니다.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>데이터 서식이 롤링 업그레이드에 미치는 영향
롤링 업그레이드가 진행되는 동안 직렬 변환기가 이전 또는 *최신* 버전의 데이터와 만나는 두 가지 시나리오가 있습니다.

1. 노드가 업그레이드되고 백업이 시작된 후 새로운 직렬 변환기가 이전 버전에서 디스크에 보관하던 데이터를 로드합니다.
2. 롤링 업그레이드가 진행되는 동안 클러스터에 이전 버전 코드와 새 버전 코드가 동시에 포함됩니다. 복제본이 서로 다른 업그레이드 도메인에 있을 수 있고 복제본은 서로 간에 데이터를 보내기 때문에 신규 및/또는 구 데이터 버전이 직렬 변환기의 신규 및/또는 구 버전과 만날 수 있습니다.

> [!NOTE]
> 여기서 "최신 버전" 및 "이전 버전"이란 실행 중인 코드의 버전을 말합니다. "최신 직렬 변환기"는 최신 버전의 애플리케이션에서 실행되는 직렬 변환기 코드를 말합니다. "최신 데이터"는 최신 버전의 애플리케이션에서 직렬화된 C# 클래스를 말합니다.
> 
> 

코드와 데이터 형식의 두 버전 모두 앞뒤로 호환되어야 합니다. 버전 간에 호환되지 않을 경우 롤링 업그레이드가 실패하거나 데이터가 손실될 수 있습니다. 다른 버전을 만났을 때 코드 또는 직렬 변환기에서 예외를 throw하거나 실패할 경우 롤링 업그레이드가 실패할 수 있습니다. 예를 들어 새 속성이 추가되었지만 이전 직렬 변환기가 역직렬화 과정에서 해당 속성을 삭제할 경우 데이터가 손실될 수 있습니다.

데이터 계약은 데이터 호환성을 보장하기 위해 권장되는 솔루션입니다. 데이터 계약에는 필드의 추가, 제거 및 변경에 대해 잘 정의된 버전 관리 규칙이 있습니다. 또한 알 수 없는 필드를 처리하는 기능, serialization 및 역직렬화 프로세스에 연결하는 기능 및 클래스 상속을 처리하는 기능을 지원합니다. 자세한 내용은 [데이터 계약 사용](https://msdn.microsoft.com/library/ms733127.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.

