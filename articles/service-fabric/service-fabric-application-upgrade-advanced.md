---
title: 고급 애플리케이션 업그레이드 항목 | Microsoft Docs
description: 이 문서에서는 서비스 패브릭 애플리케이션 업그레이드와 관련된 고급 항목을 다룹니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616537"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric 애플리케이션 업그레이드: 고급 항목
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>애플리케이션을 업그레이드하는 동안 서비스 유형 추가 또는 제거
게시된 애플리케이션에 업그레이드의 일부로 새 서비스 유형이 추가되는 경우 배포된 애플리케이션에도 새 서비스 유형이 추가됩니다. 이러한 업그레이드는 이미 애플리케이션에 포함된 서비스 인스턴스에 영향을 주지 않지만, 추가된 서비스 유형의 인스턴스를 만들어야만 새 서비스 유형이 활성화됩니다([New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps) 참조).

마찬가지로, 업그레이드의 일부로 애플리케이션에서 서비스 유형을 제거할 수도 있습니다. 그러나 제거하려는 서비스 유형의 모든 서비스 인스턴스를 제거한 후 업그레이드를 진행해야 합니다([Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps) 참조).

## <a name="manual-upgrade-mode"></a>수동 업그레이드 모드
> [!NOTE]
> *Monitored* 업그레이드 모드는 모든 Service Fabric 업그레이드에 권장하는 모드입니다.
> *UnmonitoredManual* 모드는 업그레이드가 실패 또는 일시 중단된 경우에만 고려해야 합니다. 
>
>

*Monitored* 모드에서는 업그레이드가 진행되는 동안 애플리케이션이 정상 상태를 유지하도록 Service Fabric에서 상태 정책을 적용합니다. 상태 정책을 위반하는 경우 지정된 *FailureAction*에 따라 업그레이드가 일시 중지되거나 자동으로 롤백됩니다.

*UnmonitoredManual* 모드에서는 애플리케이션 관리자가 업그레이드의 진행 상황을 총체적으로 제어합니다. 이 모드는 상태 모니터링을 우회하기 위해 사용자 지정 상태 평가 정책을 적용하거나 일반적이지 않은 업그레이드를 수행하는 경우에 유용합니다(예: 애플리케이션 데이터가 이미 손실). 이 모드에서 실행되는 업그레이드는 각 UD가 완료된 후 스스로 일시 중단되며 [Resume-servicefabricapplicationupgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps)를 사용하여 명시적으로 재개해야 합니다. 업그레이드가 일시 중단되고 사용자가 업그레이드를 다시 시작할 준비가 완료되면 업그레이드 상태가 *RollforwardPending*으로 표시됩니다([UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet) 참조).

마지막으로, *UnmonitoredAuto* 모드는 사용자 입력이 필요 없고 애플리케이션 상태 정책을 평가하지 않으므로 서비스를 개발하거나 테스트하는 동안 빠른 업그레이드 반복을 수행하는 데 유용합니다.

## <a name="upgrade-with-a-diff-package"></a>diff 패키지로 업그레이드
전체 애플리케이션 패키지를 프로비전하는 대신, 전체 애플리케이션 매니페스트 및 전체 서비스 매니페스트와 함께 업데이트된 코드/config/데이터 패키지만 포함하는 diff 패키지를 프로비전하여 업그레이드를 수행할 수도 있습니다. 전체 애플리케이션 패키지는 클러스터에 애플리케이션을 처음으로 설치하는 경우에만 필요합니다. 후속 업그레이드는 전체 애플리케이션 패키지 또는 diff 패키지에서 수행할 수 있습니다.  

애플리케이션 패키지에서 찾을 수 없는 diff 패키지의 애플리케이션 매니페스트 또는 서비스 매니페스트는 자동으로 현재 프로비전된 버전으로 대체됩니다.

diff 패키지를 사용하는 시나리오는 다음과 같습니다.

* 여러 서비스 매니페스트 파일 및/또는 여러 코드 패키지, config 패키지 또는 데이터 패키지를 참조하는 대형 애플리케이션 패키지가 있는 경우.
* 애플리케이션 빌드 프로세스에서 직접 빌드 레이아웃을 생성하는 배포 시스템을 사용하는 경우. 이 경우 코드가 변경되지 않았더라도 새로 빌드된 어셈블리는 다른 체크섬을 갖습니다. 전체 애플리케이션 패키지를 사용하려면 모든 코드 패키지의 버전을 업데이트해야 합니다. diff 패키지를 사용하면 변경된 파일과 버전이 변경된 매니페스트 파일만 제공하면 됩니다.

Visual Studio를 사용하여 애플리케이션이 업그레이드되는 경우 diff 패키지가 자동으로 게시됩니다. diff 패키지를 수동으로 만들려면 애플리케이션 매니페스트 및 서비스 매니페스트를 업데이트해야 하지만 변경된 패키지만 최종 애플리케이션 패키지에 포함되어야 합니다.

예를 들어 다음 애플리케이션을 시작하겠습니다(이해하기 쉽도록 버전 번호 제공).

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

diff 패키지를 사용하여 service1의 코드 패키지만 업데이트하려 한다고 가정해 봅시다. 업데이트된 애플리케이션은 다음과 같은 버전 변화를 거쳤습니다.

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

이 경우 애플리케이션 매니페스트를 2.0.0으로 업데이트하고 service1에 대한 서비스 매니페스트가 코드 패키지 업데이트를 반영하도록 업데이트합니다. 애플리케이션 패키지에 대한 폴더 구조는 다음과 같습니다.

```text
app1/
  service1/
    code/
```

즉, 일반적인 방법으로 전체 애플리케이션 패키지를 만든 다음, 버전이 변경되지 않은 코드/config/데이터 패키지 폴더를 제거합니다.

## <a name="rolling-back-application-upgrades"></a>애플리케이션 업그레이드 롤백

업그레이드를 세 가지 모드(*Monitored*, *UnmonitoredAuto* 또는 *UnmonitoredManual*) 중 하나로 롤포워드할 수 있지만, 롤백은 *UnmonitoredAuto* 또는 *UnmonitoredManual* 모드에서만 가능합니다. *UnmonitoredAuto* 모드에서 수행하는 롤백은 *UpgradeReplicaSetCheckTimeout*의 기본값이 다르다는 점을 제외하고 롤포워드와 동일한 방식으로 작동합니다([애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 참조). *UnmonitoredManual* 모드에서 수행하는 롤백은 롤포워드와 동일한 방식으로 작동합니다. 롤백은 각 UD가 완료되면 자체적으로 일시 중단한 후 [ Resume-servicefabricapplicationupgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps)를 사용하여 명시적으로 다시 시작하고 롤백을 계속 진행해야 합니다.

*Monitored* 모드에서 *롤백*의 *FailureAction*이 지정된 업그레이드 상태 정책을 위반하거나([애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 참조) 명시적으로 [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps)을 사용하는 경우 롤백을 자동으로 트리거할 수 있습니다.

롤백 중에 언제든지 [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps)를 사용하여 *UpgradeReplicaSetCheckTimeout* 값과 모드를 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.
