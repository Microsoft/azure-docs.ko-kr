---
title: '애플리케이션 업그레이드: 업그레이드 매개 변수 | Microsoft Docs'
description: 수행할 상태 확인 및 업그레이드를 자동으로 실행 취소하는 정책 등을 포함하여 서비스 패브릭 애플리케이션 업그레이드와 관련한 매개 변수를 설명합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: subramar
ms.openlocfilehash: 9a93c0993ee45e72b11b023982dfbbe8c6528272
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614391"
---
# <a name="application-upgrade-parameters"></a>애플리케이션 업그레이드 매개 변수
이 문서에서는 Azure Service Fabric 애플리케이션을 업그레이드하는 동안 적용되는 다양한 매개 변수를 설명합니다. 애플리케이션 업그레이드 매개 변수는 업그레이드 중에 적용되는 시간 제한 및 상태 확인을 제어하며, 업그레이드가 실패할 때 적용해야 하는 정책을 지정합니다. 애플리케이션 매개 변수는 다음을 사용하여 업그레이드에 적용됩니다.
- PowerShell
- Visual Studio
- SFCTL
- [REST (영문)](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

애플리케이션 업그레이드는 사용자가 선택할 수 있는 세 가지 업그레이드 모드 중 하나를 통해 시작됩니다. 각 모드에는 고유한 애플리케이션 매개 변수 집합이 있습니다.
- Monitored
- UnmonitoredAuto
- UnmonitoredManual

적용 가능한 필수 및 선택적 매개 변수는 다음과 같이 각 섹션에 설명되어 있습니다.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio 및 PowerShell 매개 변수

PowerShell을 사용하는 Service Fabric 애플리케이션 업그레이드는 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) 명령을 사용합니다. 업그레이드 모드는 **Monitored**, **UnmonitoredAuto** 또는 **UnmonitoredManual** 매개 변수를 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)로 전달하여 선택됩니다.

Visual Studio Service Fabric 애플리케이션 업그레이드 매개 변수는 [Visual Studio 업그레이드 설정 대화 상자]를 통해 설정됩니다. Visual Studio 업그레이드 모드는 **Upgrade Mode** 드롭다운 상자를 사용하여 **Monitored**, **UnmonitoredAuto** 또는 **UnmonitoredManual** 중 하나로 선택됩니다. 자세한 내용은 [Visual Studio에서 Service Fabric 애플리케이션의 업그레이드 구성](service-fabric-visualstudio-configure-upgrade.md)을 참조하세요.

### <a name="required-parameters"></a>필수 매개 변수
(PS=PowerShell, VS=Visual Studio)

| 매개 변수 | 적용 대상 | 설명 |
| --- | --- | --- |
ApplicationName |PS| 업그레이드될 애플리케이션의 이름입니다. 예: fabric:/VisualObjects, fabric:/ClusterMonitor |
ApplicationTypeVersion|PS|업그레이드 대상인 애플리케이션 형식의 버전입니다. |
FailureAction |PS, VS|허용되는 값은 **Rollback**, **Manual** 및 **Invalid**입니다. *Monitored* 업그레이드에서 모니터링 정책 또는 상태 정책 위반이 발생하면 수행할 보정 작업입니다. <br>**Rollback**은 업그레이드가 업그레이드 이전 버전으로 자동으로 롤백되도록 지정합니다. <br>**Manual**은 업그레이드가 *UnmonitoredManual* 업그레이드 모드로 전환됨을 나타냅니다. <br>**Invalid**는 실패 작업이 유효하지 않음을 나타냅니다.|
Monitored |PS|업그레이드 모드가 모니터링됨을 나타냅니다. cmdlet에서 업그레이드 도메인에 대한 업그레이드를 완료한 후 업그레이드 도메인 및 클러스터의 상태가 정의한 상태 정책을 충족하는 경우, Service Fabric에서 다음 업그레이드 도메인을 업그레이드합니다. 업그레이드 도메인 또는 클러스터에서 상태 정책이 충족되지 않으면, 업그레이드가 실패하고 Service Fabric에서 업그레이드 도메인에 대한 업그레이드를 롤백하거나 지정한 정책에 따라 수동 모드로 되돌아갑니다. 이는 프로덕션 환경의 애플리케이션 업그레이드에 권장되는 모드입니다. |
UpgradeMode | VS | 허용되는 값은 **Monitored**(기본값), **UnmonitoredAuto** 또는 **UnmonitoredManual**입니다. 자세한 내용은 이 문서의 각 모드에 대한 PowerShell 매개 변수를 참조하세요. |
UnmonitoredAuto | PS | 업그레이드 모드가 자동으로 모니터링되지 않음을 나타냅니다. Service Fabric에서 업그레이드 도메인을 업그레이드한 후에 애플리케이션 상태와 관계없이 다음 업그레이드 도메인을 업그레이드합니다. 이 모드는 프로덕션에 권장되지 않으며 애플리케이션 개발 동안에만 유용합니다. |
UnmonitoredManual | PS | 업그레이드 모드가 수동으로 모니터링되지 않음을 나타냅니다. Service Fabric에서 업그레이드 도메인을 업그레이드한 후에 *Resume-ServiceFabricApplicationUpgrade* cmdlet을 사용하여 다음 업그레이드 도메인을 업그레이드할 때까지 기다립니다. |

### <a name="optional-parameters"></a>선택적 매개 변수

상태 평가 매개 변수는 선택 사항입니다. 업그레이드 시작 시 상태 평가 조건이 지정되지 않으면 Service Fabric은 애플리케이션 인스턴스의 ApplicationManifest.xml에 지정된 애플리케이션 상태 정책을 사용합니다.

표 아래쪽의 가로 스크롤 막대를 사용하여 전체 설명 필드를 봅니다.

(PS=PowerShell, VS=Visual Studio)

| 매개 변수 | 적용 대상 | 설명 |
| --- | --- | --- |
| ApplicationParameter |PS, VS| 애플리케이션 매개 변수에 대한 재정의를 지정합니다.<br>PowerShell 애플리케이션 매개 변수는 해시 테이블 이름/값 쌍으로 지정합니다. 예를 들어 @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }입니다.<br>Visual Studio 애플리케이션 매개 변수는 **애플리케이션 매개 변수 파일** 필드의 [Service Fabric 애플리케이션 게시] 대화 상자에서 지정할 수 있습니다.
| Confirm |PS| 허용되는 값은 **True** 및 **False**입니다. cmdlet를 실행하기 전에 확인 메시지를 표시합니다. |
| ConsiderWarningAsError |PS, VS |허용되는 값은 **True** 및 **False**입니다. 기본값은 **False**입니다. 업그레이드하는 동안 애플리케이션의 상태를 평가할 때 애플리케이션에 대한 경고 상태 이벤트를 오류로 처리합니다. 기본적으로 서비스 패브릭은 경고 상태 이벤트를 실패(오류)로 평가하지 않으므로 경고 이벤트가 발생해도 업그레이드를 진행할 수 있습니다. |
| DefaultServiceTypeHealthPolicy | PS, VS |모니터링되는 업그레이드에 사용할 기본 서비스 유형에 대한 상태 정책을 MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices 형식으로 지정합니다. 예를 들어 5,10,15는 MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15의 값을 나타냅니다. |
| Force | PS, VS | 허용되는 값은 **True** 및 **False**입니다. 업그레이드 프로세스에서 경고 메시지를 건너뛰고, 버전 번호가 변경되지 않은 경우에도 업그레이드를 강제 적용한다는 것을 나타냅니다. 로컬 테스트에는 유용하지만, 가동 중단 및 잠재적인 데이터 손실을 유발하는 기존 배포를 제거해야 하므로 프로덕션 환경에서는 사용하지 않는 것이 좋습니다. |
| ForceRestart |PS, VS |서비스 코드를 업데이트하지 않고 구성이나 데이터 패키지를 업그레이드하는 경우, ForceRestart 속성이 **True**로 설정된 경우에만 서비스가 다시 시작됩니다. 업데이트가 완료되면 서비스 패브릭은 새 구성 패키지 또는 데이터 패키지를 사용할 수 있음을 서비스에 알립니다. 서비스는 변경 내용의 적용을 담당합니다. 필요한 경우 서비스는 자체적으로 다시 시작할 수 있습니다. |
| HealthCheckRetryTimeoutSec |PS, VS |업그레이드 실패를 선언하기 전에 서비스 패브릭이 상태 평가를 계속 수행하는 소요 시간(초)입니다. 기본값은 600초입니다. 이 기간은 *HealthCheckWaitDurationSec*에 도달한 후에 시작됩니다. Service Fabric은 이 *HealthCheckRetryTimeout* 내에서 애플리케이션 상태에 대한 여러 상태 확인을 수행할 수 있습니다. 기본값은 10분이며, 애플리케이션에 맞게 적절히 사용자 지정해야 합니다. |
| HealthCheckStableDurationSec |PS, VS |다음 업그레이드 도메인으로 이행하거나 업그레이드를 종료하기 전에 애플리케이션이 안정적인지 확인하기 위한 소요 시간(초)입니다. 이 대기 소요 시간은 상태 검사를 수행한 직후에 보호되지 않은 상태 변경을 방지하기 위해 사용됩니다. 기본값은 120초이며, 애플리케이션에 맞게 적절히 사용자 지정해야 합니다. |
| HealthCheckWaitDurationSec |PS, VS | 업그레이드 후에 Service Fabric이 애플리케이션의 상태를 평가하기 전에 업그레이드 도메인에서 완료되는 대기 시간(초)입니다. 이 소요 시간은 정상 상태로 간주되기 전에 애플리케이션을 실행해야 하는 시간으로 간주될 수도 있습니다. 상태 검사를 통과하면 업그레이드 프로세스는 다음 업그레이드 도메인으로 진행합니다.  상태 확인이 실패하면 Service Fabric에서 [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) 동안 기다린 후에 *HealthCheckRetryTimeoutSec*에 도달할 때까지 상태 확인을 다시 시도합니다. 기본 및 권장값은 0초입니다. |
| MaxPercentUnhealthyDeployedApplications|PS, VS |기본 및 권장값은 0입니다. 애플리케이션이 비정상이고 업그레이드에 실패했다고 간주하기 전에 비정상이 될 수 있는 배포된 애플리케이션의 최대 수를 지정합니다( [상태 섹션](service-fabric-health-introduction.md)참조). 이 매개 변수는 노드는 애플리케이션 상태를 정의하고 업그레이드 동안 문제를 감지하는 데 도움을 줍니다. 일반적으로 애플리케이션의 복제본은 다른 노드에 부하를 분담하기 때문에 애플리케이션이 정상으로 나타나고 업그레이드를 진행할 수 있습니다. strict *MaxPercentUnhealthyDeployedApplications* 상태를 지정하면 Service Fabric에서 애플리케이션 패키지 관련 문제를 빠르게 감지하고 빠른 실패 업그레이드를 수행할 수 있습니다. |
| MaxPercentUnhealthyServices |PS, VS |*DefaultServiceTypeHealthPolicy* 및 *ServiceTypeHealthPolicyMap*에 대한 매개 변수입니다. 기본 및 권장값은 0입니다. 애플리케이션이 비정상으로 간주되고 업그레이드에 실패하기 전에 비정상이 될 수 있는 애플리케이션 인스턴스의 서비스 최대 수를 지정합니다. |
| MaxPercentUnhealthyPartitionsPerService|PS, VS |*DefaultServiceTypeHealthPolicy* 및 *ServiceTypeHealthPolicyMap*에 대한 매개 변수입니다. 기본 및 권장값은 0입니다. 서비스가 비정상으로 간주되기 전에 비정상이 될 수 있는 서비스의 파티션 최대 수를 지정합니다. |
| MaxPercentUnhealthyReplicasPerPartition|PS, VS |*DefaultServiceTypeHealthPolicy* 및 *ServiceTypeHealthPolicyMap*에 대한 매개 변수입니다. 기본 및 권장값은 0입니다. 파티션이 비정상으로 간주되기 전에 비정상이 될 수 있는 파티션의 복제본 최대 수를 지정합니다. |
| ServiceTypeHealthPolicyMap | PS, VS | 서비스 유형에 속한 서비스의 상태를 평가하는 데 사용되는 상태 정책을 나타냅니다. 해시 테이블 입력은 @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} 형식으로 사용합니다. 예: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
| TimeoutSec | PS, VS | 작업에 대한 시간 제한 기간(초)을 지정합니다. |
| UpgradeDomainTimeoutSec |PS, VS |단일 업그레이드 도메인을 업그레이드 하는 최대 시간(초)입니다. 이 시간 제한에 도달하면 *FailureAction*에 대한 설정에 따라 업그레이드가 중지 및 진행됩니다. 기본값은 없음(무한)이며, 애플리케이션에 맞게 적절히 사용자 지정해야 합니다. |
| UpgradeReplicaSetCheckTimeoutSec |PS, VS |초 단위로 측정됩니다.<br>**상태 비저장 서비스**- 단일 업그레이드 도메인에서 서비스 패브릭은 서비스의 추가 인스턴스를 사용할 수 있는지 확인하려고 시도합니다. 대상 인스턴스의 수가 둘 이상인 경우 서비스 패브릭은 사용 가능한 인스턴스를 둘 이상, 최대 시간 제한 값까지 기다립니다. 이 시간 제한은 *UpgradeReplicaSetCheckTimeoutSec* 속성을 사용하여 지정합니다. 시간 제한이 만료되면 서비스 패브릭은 서비스 인스턴스의 수에 관계 없이 업그레이드를 진행합니다. 대상 인스턴스의 수가 하나인 경우 서비스 패브릭은 대기하지 않고 업그레이드를 즉시 진행합니다.<br><br>**상태 저장 서비스**- 단일 업그레이드 도메인에서 서비스 패브릭은 복제본 세트에 쿼럼이 있는지 확인하려고 합니다. Service Fabric에서 최대 시간 제한 값(*UpgradeReplicaSetCheckTimeoutSec* 속성으로 지정됨)까지 쿼럼을 사용할 수 있을 때까지 기다립니다. 시간 제한이 만료되면 서비스 패브릭은 쿼럼에 관계 없이 업그레이드를 진행합니다. 이 설정은 롤포워드 시 사용 안 함(무한)으로 설정되고, 롤백 시 1200초로 설정됩니다. |
| UpgradeTimeoutSec |PS, VS |전체 업그레이드에 적용되는 시간 제한(초)입니다. 이 시간 제한에 도달하면 업그레이드가 중지되고 *FailureAction*이 트리거됩니다. 기본값은 없음(무한)이며, 애플리케이션에 맞게 적절히 사용자 지정해야 합니다. |
| WhatIf | PS | 허용되는 값은 **True** 및 **False**입니다. cmdlet이 실행되는 경우 발생할 상황이 표시됩니다. cmdlet은 실행되지 않습니다. |

*MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* 및 *MaxPercentUnhealthyReplicasPerPartition* 조건은 애플리케이션 인스턴스에 대한 서비스 유형별로 지정할 수 있습니다. 이러한 매개 변수를 서비스별로 설정하면 애플리케이션이 서로 다른 평가 정책을 갖는 다른 서비스 형식을 포함할 수 있습니다. 예를 들어 상태 비저장 게이트웨이 서비스 유형에는 특정 애플리케이션 인스턴스에 대한 상태 저장 엔진 서비스 유형과 다른 *MaxPercentUnhealthyPartitionsPerService*가 있을 수 있습니다.

## <a name="sfctl-parameters"></a>SFCTL 매개 변수

Service Fabric CLI를 사용하는 Service Fabric 애플리케이션 업그레이드는 [sfctl application upgrade](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) 명령과 함께 다음 필수 및 선택적 매개 변수를 사용합니다.

### <a name="required-parameters"></a>필수 매개 변수

| 매개 변수 | 설명 |
| --- | --- |
| application-id  |업그레이드될 애플리케이션의 ID입니다. <br> 일반적으로 'fabric:' URI 구성표가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 '\~' 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 ' fabric: / myapp/app1 ', 응용 프로그램 id 수 ' myapp\~app1' 6.0 + 및 이전 버전에서는 ' myapp/app1 '.|
application-version |업그레이드 대상인 애플리케이션 형식의 버전입니다.|
매개 변수  |JSON 인코딩된 애플리케이션 매개 변수 목록은 애플리케이션을 업그레이드할 때 적용되기 위해 재정의합니다.|

### <a name="optional-parameters"></a>선택적 매개 변수

| 매개 변수 | 설명 |
| --- | --- |
default-service-health-policy | 기본적으로 서비스 유형의 상태를 평가하는 데 사용되는 [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) 인코딩된 상태 정책 사양입니다. 맵은 기본적으로 비어 있습니다. |
failure-action | 허용되는 값은 **Rollback**, **Manual** 및 **Invalid**입니다. *Monitored* 업그레이드에서 모니터링 정책 또는 상태 정책 위반이 발생하면 수행할 보정 작업입니다. <br>**Rollback**은 업그레이드가 업그레이드 이전 버전으로 자동으로 롤백되도록 지정합니다. <br>**Manual**은 업그레이드가 *UnmonitoredManual* 업그레이드 모드로 전환됨을 나타냅니다. <br>**Invalid**는 실패 작업이 유효하지 않음을 나타냅니다.|
force-restart | 서비스 코드를 업데이트하지 않고 구성이나 데이터 패키지를 업그레이드하는 경우, ForceRestart 속성이 **True**로 설정된 경우에만 서비스가 다시 시작됩니다. 업데이트가 완료되면 서비스 패브릭은 새 구성 패키지 또는 데이터 패키지를 사용할 수 있음을 서비스에 알립니다. 서비스는 변경 내용의 적용을 담당합니다. 필요한 경우 서비스는 자체적으로 다시 시작할 수 있습니다. |
health-check-retry-timeout | 애플리케이션이나 클러스터가 정상 상태가 아닌 경우 *FailureAction*이 실행되기 전에 상태 평가를 다시 시도하는 기간입니다. 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. Default: PT0H10M0S |
health-check-stable-duration | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 애플리케이션 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다. 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. Default: PT0H2M0S |
health-check-wait-duration | 업그레이드 도메인을 완료한 후 상태 정책을 적용하기 전에 대기하는 시간입니다. 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. Default: 0.|
max-unhealthy-apps | 기본 및 권장값은 0입니다. 애플리케이션이 비정상이고 업그레이드에 실패했다고 간주하기 전에 비정상이 될 수 있는 배포된 애플리케이션의 최대 수를 지정합니다( [상태 섹션](service-fabric-health-introduction.md)참조). 이 매개 변수는 노드는 애플리케이션 상태를 정의하고 업그레이드 동안 문제를 감지하는 데 도움을 줍니다. 일반적으로 애플리케이션의 복제본은 다른 노드에 부하를 분담하기 때문에 애플리케이션이 정상으로 나타나고 업그레이드를 진행할 수 있습니다. strict *max-unhealthy-apps* 상태를 지정하면 Service Fabric에서 애플리케이션 패키지 관련 문제를 빠르게 감지하고 페일 패스트 업그레이드를 수행할 수 있습니다. 0과 100 사이의 숫자로 표시합니다. |
모드 | 허용되는 값은 **Monitored**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**입니다. 기본값은 **UnmonitoredAuto**입니다. 이러한 값에 대한 설명은 Visual Studio 및 PowerShell ‘필수 매개 변수’ 섹션을 참조하세요.|
replica-set-check-timeout |초 단위로 측정됩니다. <br>**상태 비저장 서비스**- 단일 업그레이드 도메인에서 서비스 패브릭은 서비스의 추가 인스턴스를 사용할 수 있는지 확인하려고 시도합니다. 대상 인스턴스의 수가 둘 이상인 경우 서비스 패브릭은 사용 가능한 인스턴스를 둘 이상, 최대 시간 제한 값까지 기다립니다. 이 시간 제한은 *replica-set-check-timeout* 속성을 사용하여 지정합니다. 시간 제한이 만료되면 서비스 패브릭은 서비스 인스턴스의 수에 관계 없이 업그레이드를 진행합니다. 대상 인스턴스의 수가 하나인 경우 서비스 패브릭은 대기하지 않고 업그레이드를 즉시 진행합니다.<br><br>**상태 저장 서비스**- 단일 업그레이드 도메인에서 서비스 패브릭은 복제본 세트에 쿼럼이 있는지 확인하려고 합니다. Service Fabric은 최대 시간 제한 값(*replica-set-check-timeout* 속성으로 지정됨)까지 쿼럼이 사용 가능해지기를 기다립니다. 시간 제한이 만료되면 서비스 패브릭은 쿼럼에 관계 없이 업그레이드를 진행합니다. 이 설정은 롤포워드 시 사용 안 함(무한)으로 설정되고, 롤백 시 1200초로 설정됩니다. |
service-health-policy | 서비스 형식 이름 단위 서비스 형식 상태 정책으로 JSON 인코딩된 맵입니다. 맵은 기본적으로 비어 있습니다. [매개 변수 JSON 형식](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap)입니다. “값” 부분의 JSON에는 **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** 및 **MaxPercentUnhealthyReplicasPerPartition**이 포함됩니다. 이러한 매개 변수에 대한 설명은 Visual Studio 및 PowerShell 선택적 매개 변수 섹션을 참조하세요.
시간 제한 | 작업에 대한 시간 제한 기간(초)을 지정합니다. Default: 60 |
upgrade-domain-timeout | *FailureAction*이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다. 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. 기본값은 없음(무한)이며, 애플리케이션에 맞게 적절히 사용자 지정해야 합니다. Default: P10675199DT02H48M05.4775807S |
upgrade-timeout | *FailureAction*이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다. 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. 기본값은 없음(무한)이며, 애플리케이션에 맞게 적절히 사용자 지정해야 합니다. Default: P10675199DT02H48M05.4775807S|
warning-as-error | 허용되는 값은 **True** 및 **False**입니다. 기본값은 **False**입니다. 플래그로 전달될 수 있습니다. 업그레이드하는 동안 애플리케이션의 상태를 평가할 때 애플리케이션에 대한 경고 상태 이벤트를 오류로 처리합니다. 기본적으로 서비스 패브릭은 경고 상태 이벤트를 실패(오류)로 평가하지 않으므로 경고 이벤트가 발생해도 업그레이드를 진행할 수 있습니다. |

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[Linux에서 Service Fabric CLI를 사용하여 애플리케이션 업그레이드](service-fabric-application-lifecycle-sfctl.md#upgrade-application)에서는 Service Fabric CLI를 사용하여 애플리케이션 업그레이드를 진행하는 과정을 안내합니다.

[Service Fabric Eclipse 플러그 인을 사용하여 애플리케이션 업그레이드](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 애플리케이션을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.
