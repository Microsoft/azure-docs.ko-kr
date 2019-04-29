---
title: Azure Service Fabric 서비스에서 상태 관리| Microsoft Docs
description: Service Fabric 서비스에서 서비스 상태를 정의하고 관리하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870896"
---
# <a name="service-state"></a>서비스 상태
**서비스 상태**는 서비스가 작동하기 위해 필요한 메모리 내 또는 디스크의 데이터를 나타냅니다. 예를 들어 서비스가 작업을 수행하기 위해 읽고 쓰는 데이터 구조 및 멤버 변수를 포함합니다. 서비스의 구성 방식에 따라 디스크에 저장되어 있는 파일이나 기타 리소스도 포함할 수 있습니다. 예를 들어 데이터베이스가 데이터 및 트랜잭션 로그를 저장하는 데 사용하는 파일이 있습니다.

예제 서비스로 계산기를 생각해 보겠습니다. 기본 계산기 서비스는 두 개의 숫자를 받아 그 합계를 반환합니다. 이러한 계산 수행에는 멤버 변수나 기타 정보가 관련되지 않습니다.

이제 계산한 마지막 합계를 저장하고 반환하는 추가 메서드를 포함하는 동일한 계산기가 있다고 가정해 보겠습니다. 이 서비스는 이제 상태를 저장합니다. 상태 저장은 새 합계를 계산할 때 쓰고, 마지막으로 계산한 합계를 반환하라는 요청을 받을 때 읽는 상태를 포함함을 의미합니다.

Azure 서비스 패브릭에서 첫 번째 서비스는 상태 비저장 서비스라고 합니다. 두 번째 서비스는 상태 저장 서비스라고 합니다.

## <a name="storing-service-state"></a>서비스 상태 저장
상태는 외장화될 수도 있고 상태를 조작하는 코드와 함께 위치할 수도 있습니다. 상태의 외장화는 네트워크를 통해 여러 다른 컴퓨터에서 또는 같은 컴퓨터에서 out of process로 실행되는 기타 데이터 저장소 또는 외부 데이터베이스를 사용하여 수행됩니다. 계산기 예제에서는 SQL Database 또는 Azure Table Store의 인스턴스가 데이터 저장소일 수 있습니다. 모든 합계 컴퓨팅 요청은 이 데이터에 대해 업데이트를 수행하고, 서비스에 대한 값 반환 요청은 저장소에서 현재 값을 가져오게 합니다. 

상태는 해당 상태를 조작하는 코드와 함께 위치할 수도 있습니다. Service Fabric의 상태 저장 서비스는 일반적으로 이 모델을 사용하여 빌드됩니다. Service Fabric은 이 상태를 항상 사용 가능하게 하고, 일관되고 지속 가능하게 하고 이러한 방식으로 구축된 서비스를 쉽게 확장할 수 있도록 하는 인프라를 제공합니다.

## <a name="next-steps"></a>다음 단계
Service Fabric 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

* [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
* [서비스 패브릭 서비스의 확장성](service-fabric-concepts-scalability.md)
* [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
