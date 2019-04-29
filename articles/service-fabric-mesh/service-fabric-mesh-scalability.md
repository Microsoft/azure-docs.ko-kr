---
title: Azure Service Fabric Mesh 앱의 확장성 | Microsoft Docs
description: Azure Service Fabric Mesh에서 서비스 크기 조정에 대해 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811096"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Service Fabric Mesh 애플리케이션 크기 조정

Service Fabric Mesh에 애플리케이션을 배포하여 얻을 수 있는 주요 이점 중 하나는 서비스를 쉽게 확장 또는 축소하는 기능입니다. 이 기능을 사용하여 서비스에서 다양한 크기의 부하를 처리하거나 가용성을 향상할 수 있습니다. 수동으로 서비스를 확대 또는 축소하거나 자동 크기 조정 정책을 설정할 수 있습니다.

## <a name="manual-scaling-instances"></a>인스턴스 수동 크기 조정

애플리케이션 리소스에 대한 배포 템플릿에서, 각 서비스에는 서비스 배포 횟수를 설정하는 데 사용할 수 있는 *replicaCount* 속성이 있습니다. 애플리케이션은 함께 배포되고 관리되는 여러 서비스로 구성될 수 있으며, 각 서비스에는 고유한 *replicaCount* 수가 있습니다. 서비스 복제본의 수를 조정하려면 배포 템플릿 또는 매개 변수 파일에서 조정하려는 서비스의 *replicaCount* 값을 수정합니다. 그런 후 애플리케이션을 업그레이드합니다.

서비스 인스턴스 수동 크기 조정의 예제에 대한 자세한 내용은 [수동으로 서비스 크기 확대 또는 축소](service-fabric-mesh-tutorial-template-scale-services.md)를 참조하세요.

## <a name="autoscaling-service-instances"></a>서비스 인스턴스 자동 크기 조정
자동 크기 조정을 사용하여 서비스 인스턴스의 수를 동적으로 조정하는 Service Fabric의 추가 기능입니다(수평 크기 조정). 자동 크기 조정은 탄력성이 뛰어나 CPU 또는 메모리 사용률에 따라 서비스 인스턴스를 프로비전하거나 제거할 수 있습니다.  자동 크기 조정을 사용하여 워크로드에 대한 적절한 수의 서비스 인스턴스를 실행하고 비용을 최적화할 수 있습니다.

자동 크기 조정 정책은 서비스 리소스 파일에서 서비스별로 정의됩니다. 각 크기 조정 정책은 두 부분으로 구성됩니다.

- 서비스의 크기 조정이 수행되는 시기에 대해 설명하는 크기 조정 트리거입니다. 서비스의 크기 조정 시기를 결정하는 세 가지 요소가 있습니다. *하위 로드 임계값*은 서비스가 규모 감축되는 시기를 결정하는 값입니다. 파티션에 있는 모든 인스턴스의 평균 로드가 이 값 미만이면 서비스가 규모 감축됩니다. *상위 로드 임계값*은 서비스가 규모 확장되는 시기를 결정하는 값입니다. 파티션에 있는 모든 인스턴스의 평균 로드가 이 값보다 높으면 서비스가 규모 확장됩니다. *크기 조정 간격*은 트리거를 검사할 빈도를 결정합니다. 트리거를 확인하여 크기 조정이 필요한 경우 메커니즘이 적용됩니다. 크기 조정이 필요하지 않으면 아무 작업도 수행되지 않습니다. 두 가지 경우 모두 크기 조정 간격이 만료되기 전에는 트리거를 다시 확인할 수 없습니다.

- 크기 조정 메커니즘은 크기 조정이 트리거될 때 수행되는 방법을 설명합니다. *규모 증분*은 메커니즘이 트리거될 때 추가 또는 제거할 인스턴스 수를 결정합니다. *최대 인스턴스 수*는 크기 조정 상한을 정의합니다. 인스턴스 수가 이 한계에 도달하는 경우 로드에 관계없이 서비스 규모는 확대되지 않습니다. *최소 인스턴스 수*는 크기 조정의 하한을 정의합니다. 파티션의 인스턴스 수가 이 한계에 도달하면 로드에 관계없이 서비스가 규모 감축되지 않습니다.

서비스에 대한 자동 크기 조정 정책을 설정하는 방법에 알아보려면 [서비스 자동 크기 조정](service-fabric-mesh-howto-auto-scale-services.md)을 읽으세요.

## <a name="next-steps"></a>다음 단계

애플리케이션 모델에 대한 자세한 내용은 [Service Fabric 리소스](service-fabric-mesh-service-fabric-resources.md)를 참조하세요.
