---
title: Service Fabric 애플리케이션 업그레이드 | Microsoft Docs
description: 이 문서에서는 업그레이드 모드 선택 및 상태 확인 수행 등을 포함하여 Service Fabric 애플리케이션 업그레이드를 소개합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e2b407733bcab7bc854e8e3703e53eb474f3425b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615061"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric 애플리케이션 업그레이드
Azure Service Fabric 애플리케이션은 서비스의 컬렉션입니다. 업그레이드가 진행되는 동안 Service Fabric은 새로운 [애플리케이션 매니페스트](service-fabric-application-and-service-manifests.md)를 이전 버전과 비교하여 애플리케이션의 어떤 서비스를 업데이트해야 하는지 결정합니다. 서비스 패브릭은 이전 버전의 버전 번호를 가진 서비스 매니페스트의 버전 번호를 비교합니다. 서비스가 변경되지 않으면 해당 서비스가 업그레이드되지 않습니다.

## <a name="rolling-upgrades-overview"></a>롤링 업그레이드 개요
롤링 애플리케이션 업그레이드에서 업그레이드는 여러 단계로 수행됩니다. 각 단계에서 업데이트 도메인이라고 하는 클러스터의 노드 하위 집합에 업그레이드가 적용됩니다. 결과적으로, 전체 업그레이드 과정에서 애플리케이션 가용성이 유지됩니다. 업그레이드가 진행되는 동안 클러스터에 이전 버전과 새 버전이 동시에 포함될 수 있습니다.

이러한 이유로 두 버전이 호환되어야 합니다. 호환되지 않을 경우 애플리케이션 관리자는 가용성을 유지하려면 다단계 업그레이드를 준비해야 합니다. 다단계 업그레이드에서 첫 번째 단계는 이전 버전과 호환되는 애플리케이션의 중간 버전으로 업그레이드하는 것입니다. 두 번째 단계는 최종 버전을 업그레이드하는 것입니다. 이러한 최종 버전은 업데이트 전 버전과는 호환되지 않지만 중간 버전과는 호환됩니다.

업데이트 도메인은 사용자가 클러스터를 구성할 때 클러스터 매니페스트에 지정됩니다. 업데이트 도메인은 업데이트를 특정 순서로 받지 않습니다. 업데이트 도메인은 애플리케이션에 대한 배포의 논리 단위입니다. 업데이트 도메인을 사용하면 서비스에 업그레이드가 진행되는 동안 고가용성을 유지합니다.

클러스터의 모든 노드에 업그레이드가 적용되는 경우 비롤링 업그레이드도 가능합니다. 애플리케이션에 업데이트 도메인이 하나뿐인 경우가 이에 해당합니다. 업그레이드가 진행되는 동안 서비스 제공이 중지되기 때문에 권장하지 않는 방법입니다. 또한 업데이트 도메인 하나로 클러스터를 설정할 경우 Azure에서 어떤 보증도 제공하지 않습니다.

업그레이드가 완료되면 모든 서비스 및 복제본(인스턴스)이 같은 버전 상태로 유지됩니다. 예를 들어 업그레이드가 성공하면 새 버전으로 업데이트되고, 업그레이드가 실패하여 롤백되면 이전 버전으로 롤백됩니다.

## <a name="health-checks-during-upgrades"></a>업그레이드 동안 상태 검사
업그레이드에 대해 상태 정책을 설정해야 합니다. 설정하지 않으면 기본값이 사용됩니다. 모든 업데이트 도메인이 지정된 시간 제한 내에 업그레이드되고 모든 업데이트 도메인이 정상 상태로 보이면 업그레이드에 성공했다고 합니다.  정상 업데이트 도메인이란 업데이트 도메인이 상태 정책에 지정된 모든 상태 검사를 통과했다는 의미입니다. 예를 들어 상태 정책에 따라 애플리케이션 인스턴스의 모든 서비스가 *정상*이어야 하고, 상태는 Service Fabric에서 정의됩니다.

업그레이드가 진행되는 동안 서비스 패브릭에서 수행하는 상태 정책 및 상태 검사는 서비스 및 애플리케이션을 구분하지 않습니다. 즉, 서비스별 테스트를 수행하지 않습니다.  예를 들어 서비스에는 처리량 요구 사항이 있을 수 있지만 서비스 패브릭에는 처리량을 확인하기 위한 정보가 없습니다. 수행되는 검사는 [상태 문서](service-fabric-health-introduction.md) 를 참조하세요. 업그레이드 중에 발생하는 확인은 애플리케이션 패키지가 올바르게 복사되었는지, 인스턴스가 시작되었는지 등과 같은 여부에 대한 테스트를 포함합니다.

애플리케이션 상태는 애플리케이션의 자식 엔터티를 집계한 것입니다. 간단히 말해서 Service Fabric에서는 애플리케이션에 대해 보고되는 상태를 통해 애플리케이션의 상태를 평가합니다. 또한 애플리케이션에 대한 모든 서비스의 상태 이러한 방식으로 평가합니다. Service Fabric은 서비스 복제본과 같은 자식의 상태를 집계하여 보다 구체적으로 애플리케이션 서비스의 상태를 평가합니다. 애플리케이션 상태 정책이 충족되면 업그레이드를 계속할 수 있습니다. 상태 정책이 위반되면 애플리케이션 업그레이드는 실패합니다.

## <a name="upgrade-modes"></a>업그레이드 모드
애플리케이션 업그레이드에 대해 권장되는 모드는 모니터링된 모드이며 이는 일반적으로 사용됩니다. 모니터링 모드는 하나의 업데이트 도메인에서 업그레이드를 수행하고 정책에 지정된 모든 상태 검사를 통과하면 자동으로 다음 업데이트 도메인으로 이동합니다.  상태 검사가 실패하거나 시간 제한에 도달하면 업그레이드가 업데이트 도메인에 대해 롤백되거나 모드가 모니터링되지 않는 수동으로 변경됩니다. 실패한 업그레이드에 대해 이러한 두 가지 모드 중 하나를 선택하도록 업그레이드를 구성할 수 있습니다. 

모니터링되지 않는 수동 모드는 업데이트 도메인에서 업그레이드가 수행될 때마다 다음 업데이트 도메인에서 업그레이드가 시작하도록 수동 작업이 필요합니다. 서비스 패브릭 상태 검사가 수행되지 않습니다. 관리자는 다음 업데이트 도메인에서 업그레이드가 시작되기 전에 상태 또는 상태 검사를 수행합니다.

## <a name="upgrade-default-services"></a>기본 서비스 업그레이드
[애플리케이션 매니페스트](service-fabric-application-and-service-manifests.md)에 정의된 일부 기본 서비스 매개 변수는 애플리케이션 업그레이드의 일부로 업그레이드될 수도 있습니다. [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps)를 통해 변경되는 것을 지원하는 서비스 매개 변수만 업그레이드의 일환으로 변경될 수 있습니다. 애플리케이션 업그레이드 중 기본 서비스 변경의 동작은 다음과 같습니다.

1. 클러스터에 존재하지 않는 새 애플리케이션 매니페스트의 기본 서비스가 만들어집니다.
2. 이전 및 새 애플리케이션 매니페스트 모두에 존재하는 기본 서비스가 업데이트됩니다. 새 애플리케이션 매니페스트에 있는 기본 서비스의 매개 변수는 기존 서비스의 매개 변수를 덮어씁니다. 기본 서비스 업데이트에 실패하는 경우 애플리케이션 업그레이드가 자동으로 롤백됩니다.
3. 새 애플리케이션 매니페스트에 존재하지 않는 기본 서비스는 클러스터에 존재하는 경우 삭제됩니다. **기본 서비스 삭제로 인해 모든 해당 서비스의 상태가 삭제되며 실행 취소할 수 없습니다.**

애플리케이션 업그레이드가 롤백되면 기본 서비스 매개 변수는 업그레이드가 시작되기 전에 이전 값으로 되돌려지지만 삭제된 서비스를 이전 상태로 다시 생성할 수 없습니다.

> [!TIP]
> [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) 클러스터 구성 설정은 위의 규칙 2) 및 3)을 활성화하도록 *true*여야 합니다(기본 서비스 업데이트 및 삭제). 이 기능은 Service Fabric 버전 5.5부터 지원됩니다.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>HTTPS 엔드포인트로 여러 애플리케이션 업그레이드
HTTP**S**를 사용할 때는 동일한 애플리케이션의 서로 다른 인스턴스에 대해 **동일한 포트**를 사용하지 않도록 주의해야 합니다. Service Fabric에서 애플리케이션 인스턴스 중 하나에 대해 인증서를 업그레이드할 수 없기 때문입니다. 예를 들어, 애플리케이션 1 또는 애플리케이션 2 모두 해당 인증서 1을 인증서 2로 업그레이드하려고 합니다. 업그레이드가 발생할 때 Service Fabric은 다른 애플리케이션에서 아직 사용 중이라고 하더라도 http.sys에서 인증서 1 등록을 정리할 수 있습니다. 이러한 상황을 방지하기 위해 Service Fabric은 인증서와 함께 해당 포트에 등록된(http.sys에 따라) 다른 애플리케이션 인스턴스가 이미 있는지 검색하고 있는 경우 작업이 실패합니다.

따라서 Service Fabric은 서로 다른 애플리케이션 인스턴스에 **동일한 포트**를 사용하는 두 가지 다른 서비스의 업그레이드를 지원하지 않습니다. 즉, 동일한 포트에서 서로 다른 서비스에 동일한 인증서를 사용할 수 없습니다. 동일한 포트에서 공유 인증서를 포함해야 하는 경우 배치 제약 조건에 따라 서비스가 서로 다른 컴퓨터에 배치되는지 확인해야 합니다. 또는 각 애플리케이션 인스턴스에서 각 서비스에 대해 Service Fabric 동적 포트를 사용하는 것이 좋습니다(가능한 경우). 

https로 업그레이드 실패가 표시되면 “The Windows HTTP Server API does not support multiple certificates for applications that share a port(Windows HTTP 서버 API에서 포트를 공유하는 애플리케이션에 대해 여러 인증서를 지원하지 않습니다).”라는 오류 경고가 표시됩니다.

## <a name="application-upgrade-flowchart"></a>애플리케이션 업그레이드 순서도
이 단락 다음에 나오는 순서도는 Service Fabric 애플리케이션의 업그레이드 프로세스를 이해하는 데 도움이 될 수 있습니다. 특히 *HealthCheckStableDuration*, *HealthCheckRetryTimeout* 및 *UpgradeHealthCheckInterval*을 비롯한 시간 제한이 한 업데이트 도메인의 업그레이드를 성공 또는 실패로 간주하는 시간을 어떻게 제어하는지 잘 설명되어 있습니다.

![Service Fabric 애플리케이션의 업그레이드 프로세스][image]

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[PowerShell을 사용하여 응용 프로그램 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 응용 프로그램 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
