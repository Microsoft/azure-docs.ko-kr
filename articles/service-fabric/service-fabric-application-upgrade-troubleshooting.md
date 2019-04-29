---
title: 애플리케이션 업그레이드 문제 해결 | Microsoft Docs
description: 이 문서에서는 서비스 패브릭 애플리케이션 업그레이드에 관한 일반적인 문제와 이를 해결하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 19ad152e-ec50-4327-9f19-065c875c003c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e393eb92e11dc8dc296f1dc5f1c0036566c285c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616027"
---
# <a name="troubleshoot-application-upgrades"></a>애플리케이션 업그레이드 문제 해결

이 문서에서는 Azure 서비스 패브릭 애플리케이션 업그레이드에 관한 일반적인 문제와 이를 해결하는 방법을 설명합니다.

## <a name="troubleshoot-a-failed-application-upgrade"></a>애플리케이션 업그레이드 실패 문제 해결

업그레이드에 실패하는 경우, **Get-ServiceFabricApplicationUpgrade** 명령의 출력에 오류 디버깅에 관한 추가 정보가 포함됩니다.  다음 목록은 추가 정보는 사용하는 방버을 지정합니다.

1. 오류 형식을 식별합니다.
2. 오류 원인을 식별합니다.
3. 추가 조사를 위해 하나 이상의 오류 구성 요소를 분리합니다.

이 정보는 서비스 패브릭이 오류를 탐지하면 **FailureAction** 이 롤백하거나 업그레이드를 일시 중단하는 여부에 관계 없이 사용할 수 있습니다.

### <a name="identify-the-failure-type"></a>오류 형식 식별

**Get-ServiceFabricApplicationUpgrade**의 출력에서 **FailureTimestampUtc**는 Service Fabric에 의해 업그레이드 오류가 감지되고 **FailureAction**이 트리거된 시점의 타임 스탬프를 식별합니다(UTC에서). **FailureReason** 은 오류의 잠재적인 상위 수준 원인 세 가지 중 하나를 식별합니다.

1. UpgradeDomainTimeout - 완료하는 데 시간이 너무 오래 걸려서 **UpgradeDomainTimeout** 이 만료된 특정 업그레이드 도메인을 나타냅니다.
2. OverallUpgradeTimeout - 완료하는 데 시간이 너무 오래 걸려서 **UpgradeTimeout** 이 만료된 전체 업그레이드를 나타냅니다.
3. HealthCheck - 업그레이드 도메인을 업그레이드한 후 지정된 상태 정책 및 **HealthCheckRetryTimeout** 만료에 따라 비정상으로 남겨진 애플리케이션을 나타냅니다.

이 항목들은 업그레이드가 실패하고 롤백이 시작된 경우의 출력에서만 표시됩니다. 자세한 정보는 오류의 유형에 따라 표시됩니다.

### <a name="investigate-upgrade-timeouts"></a>업그레이드 시간 제한 조사

업그레이드 시간 제한 오류는 서비스 가용성 문제로 인해 가장 일반적으로 발생합니다. 이 단락 다음에 나오는 출력은 서비스 복제본 또는 인스턴스가 새 코드 버전에서 시작하는 것에 실패한 업그레이드에서 일반적입니다. **UpgradeDomainProgressAtFailure** 필드는 오류가 발생했을 때 보류 중인 모든 업그레이드 작업의 스냅숏을 캡처합니다.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

이 예제에서는 업그레이드 도메인 *MYUD1*에서 업그레이드가 실패하고 두 파티션(*744c8d9f-1d26-417e-a60e-cd48f5c098f0* 및 *4b43f4d8-b26b-424e-9307-7a7a62e79750*)이 중단되었습니다. 런타임이 대상 노드 *Node1* 및 *Node4*에서 주 복제본(*WaitForPrimaryPlacement*)을 배치할 수 없기 때문에 파티션이 중단되었습니다.

**Get ServiceFabricNode** 명령은 이 두 노드가 업그레이드 도메인 *MYUD1*에 있는지 확인하는 데 사용할 수 있습니다. *UpgradePhase*는 업그레이드 도메인의 모든 노드가 업그레이드를 마친 후에 이 안전 검사가 발생하였음을 의미하는 *PostUpgradeSafetyCheck*입니다. 이 모든 정보가 애플리케이션 코드의 새 버전의 잠재적인 문제를 가리킵니다. 가장 일반적인 문제는 열 때 또는 기본 코드 경로의 프로모션에서의 서비스 오류입니다.

*PreUpgradeSafetyCheck*의 *UpgradePhase*는 업그레이드를 수행하기 전에 업그레이드 도메인을 준비하는 문제를 의미합니다. 이 경우의 가장 일반적인 문제는 닫을 때 또는 기본 코드 경로의 수준 내리기에서의 서비스 오류입니다.

현재 **UpgradeState**는 *RollingBackCompleted*이므로 원래 업그레이드는 실패 시 업그레이드를 자동으로 롤백하는 롤백 **FailureAction**과 함께 수행되어야 합니다. 원래 업그레이드가 수동 **FailureAction**과 함께 수행되었으면 그 업그레이드는 일시 중단 상태 대신 애플리케이션의 라이브 디버깅을 가능하게 합니다.

드문 경우지만 시스템이 현재 업그레이드 도메인에 대한 모든 작업을 완료하는 것처럼 전체 업그레이드 시간이 초과되는 경우 **UpgradeDomainProgressAtFailure** 필드가 비어 있을 수 있습니다. 이 경우 **UpgradeTimeout** 및 **UpgradeDomainTimeout** 업그레이드 매개 변수 값을 늘리고 업그레이드를 다시 시도합니다.

### <a name="investigate-health-check-failures"></a>상태 확인 오류 조사

상태 확인 오류는 업그레이드 도메인의 모든 노드가 업그레이드를 완료하고 모든 안전 검사를 통과한 후 발생할 수 있는 다양한 문제로 인해 트리거될 수 있습니다. 이 단락 다음에 나오는 출력은 실패한 상태 검사로 인한 일반적인 업그레이드 오류입니다. **UnhealthyEvaluations** 필드는 지정된 [상태 정책](service-fabric-health-introduction.md)에 따라 업그레이드 시 실패한 상태 검사의 스냅숏을 캡처합니다.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

상태 검사 오류 조사에는 우선 서비스 패브릭 상태 모델에 대한 이해가 필요합니다. 하지만 깊은 이해가 없더라도 오류 상태 보고서(이 경우 "InjectedFault")와 함께 제공되는 *fabric:/DemoApp/Svc3* 및 *fabric:/DemoApp/Svc2*의 두 서비스가 비정상임은 알 수 있습니다. 이 예제에서는 4개의 서비스 중 2개는 비정상이며, 다음 0%의 기본 대상이 비정상입니다(*MaxPercentUnhealthyServices*).

업그레이드 시작 시 **FailureAction** 을 manual로 지정했으므로 실패 시 업그레이드가 일시 중단되었습니다. 이 모드에서는 추가 작업을 수행하기 전에 오류 상태에 있는 라이브 시스템을 조사할 수 있습니다.

### <a name="recover-from-a-suspended-upgrade"></a>일시 중단된 업그레이드 복구

롤백 **FailureAction**을 사용하면 실패 시 업그레이드가 자동으로 롤백되므로 복구가 필요 없습니다. 수동 **FailureAction**을 사용하면 몇 가지 복구 옵션이 존재합니다.

1.  롤백 트리거
2. 수동으로 업그레이드의 남은 부분까지 진행합니다.
3. 모니터링된 업그레이드 다시 시작

**Start-ServiceFabricApplicationRollback** 명령은 애플리케이션 롤백을 시작하는 데 언제든지 사용할 수 있습니다. 명령이 성공적으로 반환되면 롤백 요청은 시스템에 등록되고 금방 시작됩니다.

**Resume-ServiceFabricApplicationUpgrade** 명령은 업그레이드의 나머지 부분까지 수동으로 한 번에 업그레이드 도메인 하나씩 진행하는 데 사용할 수 있습니다. 이 모드에서는 안전 검사만 시스템에 의해 수행됩니다. 더 이상의 상태 검사는 수행되지 않습니다. 이 명령은 *UpgradeState*가 현재 업그레이드 도메인의 업그레이드가 끝났으나 다음 업그레이드 도메인이 시작되지 않았음(보류 중)을 의미하는 *RollingForwardPending*을 나타낼 때만 사용됩니다.

**Update-ServiceFabricApplicationUpgrade** 명령은 안전 및 상태 검사가 수행된 모니터링된 업그레이드를 다시 시작하는 데 사용합니다.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

업그레이드는 마지막으로 일시 중단되고 이전과 동일한 업그레이드 매개 변수 및 상태 정책을 사용하는 업그레이드 도메인에서 계속됩니다. 필요한 경우, 앞의 출력에서 보이는 업그레이드 매개 변수 및 상태 정책 중 무엇이든 업그레이드를 다시 시작했을 때 동일한 명령에서 변경될 수 있습니다. 이 예제에서는 매개 변수 및 상태 정책이 변경되지 않은 상태로 모니터링 모드에서 업그레이드가 다시 시작되었습니다.

## <a name="further-troubleshooting"></a>추가 문제 해결

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>서비스 패브릭이 지정된 상태 정책을 따르지 않습니다.

가능한 원인 1:

서비스 패브릭은 상태 평가에 대한 모든 백분율을 엔터티(예: 복제본, 파티션 및 서비스)의 실제 숫자로 변환하고, 항상 정수 엔터티 값으로 반올림합니다. 예를 들어, 최대 *MaxPercentUnhealthyReplicasPerPartition*이 21%이고 5개의 복제본이 있는 경우 Serivce Fabric은 최대 2개의 비정상 복제본(즉, `Math.Ceiling (5*0.21)`)을 허용합니다. 따라서 상태 정책도 그에 따라 설정해야 합니다.

가능한 원인 2:

상태 정책은 특정 서비스 인스턴스가 아닌 총 서비스의 백분율을 기준으로 지정됩니다. 예를 들어 업그레이드 전에 애플리케이션이 4개의 서비스 인스턴스 A, B, C, D를 가지고, 서비스 D가 비정상이지만 애플리케이션에 심각한 영향을 미치지는 않는다고 가정합니다. 업그레이드하는 동안 알려진 비정상 서비스 D를 무시하고자 하며, A, B, C만 정상이라고 가정하면 매개 변수 *MaxPercentUnhealthyServices* 를 25%로 설정해야 합니다.

그러나 업그레이드하는 동안 C가 비정상이 되고 D가 정상이 될 수 있습니다. 이 경우 서비스의 25%만이 비정상이므로 업그레이드는 여전히 성공적으로 완료되지만 D 대신 C가 예상치 못하게 비정상이 됨으로 인해 예기치 못한 오류가 발생할 수 있습니다. 이 상황에서 D는 다른 A, B,C에서 다른 서비스 유형으로 모델링되어야 합니다. 상태 정책은 서비스 유형별로 지정되므로 다른 서비스에 다른 비정상 백분율 임계값을 적용할 수 있게 됩니다. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>애플리케이션 업그레이드에 대한 상태 정책을 지정하지 않았는데도 지정한 적이 없는 시간 제한 때문에 업그레이드에 실패합니다.

업그레이드 요청에 상태 정책이 제공되지 않는 경우, 현재 애플리케이션 버전의 *ApplicationManifest.xml*에서 가져옵니다. 예를 들어 Application X를 버전 1.0에서 버전 2.0으로 업그레이드하는 경우 버전 1.0에 대해 지정된 애플리케이션 상태 정책이 사용됩니다. 다른 상태 정책을 업그레이드에 사용해야 하는 경우, 정책은 애플리케이션 업그레이드 API 호출의 일부로 지정되어야 합니다. API 호출의 일부로 지정된 정책은 업그레이드 중에만 적용됩니다. 업그레이드가 일단 완료되면 *ApplicationManifest.xml*에 지정된 정책이 사용됩니다.

### <a name="incorrect-time-outs-are-specified"></a>잘못된 제한 시간 지정

시간 초과가 일관되지 않게 설정될 때 어떤 문제가 발생할지 궁금할 수 있습니다. 예를 들어 *UpgradeDomainTimeout*보다 작은 *UpgradeTimeout*이 있을 수 있습니다. 그 이유는 오류가 반환되었기 때문입니다. *UpgradeDomainTimeout*이 *HealthCheckWaitDuration*과 *HealthCheckRetryTimeout*의 합계보다 작거나 *UpgradeDomainTimeout*이 *HealthCheckWaitDuration*과 *HealthCheckStableDuration*의 합계보다 작으면 오류가 반환됩니다.

### <a name="my-upgrades-are-taking-too-long"></a>업그레이드에 시간이 너무 오래 걸립니다.

업그레이드 완료 시간은 상태 검사 및 지정된 제한 시간에 따라 다릅니다. 상태 검사 및 제한 시간은 애플리케이션을 복사, 배포 및 안정화하는 데 걸리는 시간에 따라 다릅니다. 제한 시간을 너무 촉박하게 지정하면 업그레이드에 더 많이 실패할 수 있으므로 더 긴 제한 시간으로 신중하게 시작하는 것이 바람직합니다.

업그레이드 시간과 제한 시간의 관계에 대해 간단히 정리하면 다음과 같습니다.

업그레이드 도메인에 대한 업그레이드는 *HealthCheckWaitDuration* + *HealthCheckStableDuration*보다 빠르게 완료할 수 없습니다.

*HealthCheckWaitDuration* + *HealthCheckRetryTimeout*보다 빠르면 업그레이드 오류가 발생할 수 있습니다.

업그레이드 도메인에 대한 업그레이드 시간은 *UpgradeDomainTimeout*으로 제한됩니다.  *HealthCheckRetryTimeout* 및 *HealthCheckStableDuration*이 모두 0이 아니고 애플리케이션이 앞뒤로 전환되는 상태를 유지하는 경우, 업그레이드는 궁극적으로 *UpgradeDomainTimeout*의 제한 시간이 됩니다. *UpgradeDomainTimeout* 은 현재 업그레이드 도메인에 대한 업그레이드가 일단 시작되면 카운트다운을 시작합니다.

## <a name="next-steps"></a>다음 단계

[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.