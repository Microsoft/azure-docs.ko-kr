---
title: 사용자 지정 Service Fabric 상태 보고서 추가 | Microsoft Docs
description: Azure 서비스 패브릭 상태 엔터티에 사용자 지정 상태 보고서를 보내는 방법을 설명합니다. 고품질 상태 보고서 설계 및 구현에 대한 권장 사항을 제공합니다.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 49ebf4ab95816a3da2f74a464b12b46de6228456
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723450"
---
# <a name="add-custom-service-fabric-health-reports"></a>사용자 지정 서비스 패브릭 상태 보고서 추가
Azure Service Fabric은 특정 엔터티의 비정상 클러스터 및 애플리케이션 상태에 플래그를 적용하도록 설계된 [상태 모델](service-fabric-health-introduction.md)을 도입했습니다. 상태 모델은 **Health 보고서** (시스템 구성 요소 및 Watchdog)를 사용합니다. 쉽고 빠른 진단을 목표로 합니다. 서비스 작성자는 상태를 미리 고려해야 합니다. 상태에 영향을 줄 수 있는 모든 조건이 보고되어야 하며, 특히 근본 원인에 가까운 문제를 플래깅하는 데 도움이 되는 경우에는 반드시 보고가 이루어져야 합니다. 상태 정보는 디버깅 및 조사에 소요되는 시간과 노력을 절감할 수 있습니다. 특히 서비스가 클라우드에서 대용량으로 가동 및 실행될 때 확실히 유용합니다(사설 또는 Azure).

서비스 패브릭 보고자는 식별된 관심 조건을 모니터링합니다. 보고자는 각자 로컬 보기를 기반으로 이러한 조건에 대한 정보를 보고합니다. [Health 스토어](service-fabric-health-introduction.md#health-store) 는 엔터티가 전체적으로 정상인지를 판단하기 위하여 모든 보고자가 보낸 상태 데이터를 수집합니다. 모델은 다양하고 유연하며 사용이 쉽도록 계획됩니다. 상태 보고서의 품질은 클러스터 상태 보기의 정확성을 결정합니다. 비정상 이슈를 잘못 표시하는 거짓 긍정은 상태 데이터를 사용하는 업그레이드 또는 기타 서비스에 부정적인 영향을 미칠 수 있습니다. 이러한 서비스의 예로는 복구 서비스 및 경고 메커니즘이 있습니다. 따라서 최선의 방식으로 관심 조건을 포착하는 보고서를 제공하기 위해서는 몇 가지를 고려해야 합니다.

상태 보고를 설계하고 구현하기 위하여 Watchdog 및 시스템 구성 요소는 다음을 수행해야 합니다.

* 관심 있는 조건, 해당 조건을 모니터링하는 방식, 클러스터 또는 애플리케이션 기능에 미치는 영향을 정의합니다. 이 정보를 기반으로 상태 보고서 속성 및 상태를 판단합니다.
* 보고서가 적용되는 [엔터티](service-fabric-health-introduction.md#health-entities-and-hierarchy) 를 정의합니다.
* 보고를 서비스 내에서 수행할 것인지 아니면 내부 또는 외부 Watchdog에서 수행할 것인지 보고 위치를 정의합니다.
* 보고자를 식별하는데 사용할 소스를 정의합니다.
* 주기적 또는 전환기 중에서 보고 전략을 선택합니다. 주기적 방식을 선택하면 코드가 더 간단하고 오류가 발생할 가능성이 적기 때문에 주기적 방식을 권장합니다.
* 비정상 조건에 대한 보고서를 Health 스토어에 보관할 기간과 보고서를 지우는 방식을 결정합니다. 이 정보를 사용하여 보고서의 지속 시간 및 만료 시 제거 동작을 결정합니다.

언급된 바와 같이, 보고가 가능한 위치는 다음과 같습니다.

* 모니터링되는 서비스 패브릭 서비스 복제본.
* Service Fabric 서비스로 배포되는 내부 Watchdog(예: 조건 및 문제 보고서를 모니터링하는 Service Fabric 상태 비저장 서비스). Watchdog는 모든 노드에 배포되거나 모니터링되는 서비스로 규합될 수 있습니다.
* 서비스 패브릭 노드에서 실행되지만 서비스 패브릭 서비스로 구현되지 *않는* 내부 watchdog.
* Service Fabric 클러스터 *외부* 의 리소스를 조사하는 외부 Watchdog(예: Gomez와 같은 모니터링 서비스).

> [!NOTE]
> 기본적으로, 클러스터는 시스템 구성 요소에 의해 전송되는 상태 보고서로 채워집니다. 추가 정보는 [시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)을 참조하세요. 사용자 보고서는 시스템에 의해 이미 생성된 [상태 엔터티](service-fabric-health-introduction.md#health-entities-and-hierarchy) 로 보내야 합니다.
> 
> 

상태 보고 설계가 명확하면 상태 보고서를 간편하게 보낼 수 있습니다. 클러스터가 [보안](service-fabric-cluster-security.md) 상태가 아니거나 패브릭 클라이언트에 관리자 권한이 있는 경우 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)를 사용하여 상태를 보고할 수 있습니다. 보고는 PowerShell 또는 REST를 통해 [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth)를 사용하여 API를 통해 수행할 수 있습니다. 성능 향상을 위한 구성 노브 배치 보고서가 있습니다.

> [!NOTE]
> 상태 보고서는 동기화되며 클라이언트 쪽의 유효성 검사 작업만 표시합니다. 상태 클라이언트나 `Partition` 또는 `CodePackageActivationContext` 개체에서 보고서를 수용한다고 해서 저장소에 적용된다는 의미는 아닙니다. 비동기적으로 전송되며 다른 보고서와 함께 일괄 처리될 수도 있습니다. 시퀀스 번호가 오래되었거나 보고서가 적용되어야 하는 엔터티가 삭제되는 등의 이유로 서버 쪽에서의 처리가 실패할 수도 있습니다.
> 
> 

## <a name="health-client"></a>상태 클라이언트
상태 보고서는 패브릭 클라이언트 내에 있는 상태 클라이언트를 통해 상태 관리자에 게 전송 됩니다. 상태 관리자는 health 스토어에서 보고서를 저장합니다. 상태 클라이언트는 다음 설정으로 구성될 수 있습니다.

* **HealthReportSendInterval**: 시간과 클라이언트에는 보고서가 추가 하는 시간 사이의 지연 상태 관리자에 게 전송 됩니다. 각 보고서에 메시지를 하나씩 전송하는 대신 보고서를 하나의 메시지로 일괄 처리하는데 사용됩니다. 일괄 처리를 하면 성능이 향상됩니다. Default: 30초
* **HealthReportRetrySendInterval**: 상태 클라이언트는 누적 된 상태를 다시 전송 간격을 상태 관리자에 게 보고 합니다. Default: 30 초, 최소: 1 초입니다.
* **HealthOperationTimeout**: 상태 관리자에 게 전송 된 보고서 메시지에 대 한 제한 시간입니다. 메시지 시간 초과 되 면 상태 관리자는 보고서가 처리 되었는지 확인할 때까지 상태 클라이언트 재시도 합니다. 기본값: 2분.

> [!NOTE]
> 보고서가 일괄 처리되는 경우 보고서가 전송될 수 있도록 적어도 HealthReportSendInterval 동안 패브릭 클라이언트가 유지되어야 합니다. 메시지가 손실 된 경우 상태 관리자를 일시적인 오류로 인해 적용할 수 없는 패브릭 클라이언트가 유지 되어야 합니다 더 이상 다시 시도 하 고 제공할 수 있도록 합니다.
> 
> 

클라이언트에서의 버퍼링은 보고서의 고유성을 고려합니다. 예를 들어 어떤 악성 보고자가 동일한 엔터티의 동일한 속성에 대해 초당 100개의 보고서를 보내는 경우 해당 보고서는 최신 버전으로 교체됩니다. 이러한 보고서는 기껏해야 클라이언트 큐에 하나 존재합니다. 일괄 처리를 구성 하는 경우 상태 관리자에 게 전송 되는 보고서 개수는 전송 간격당 하나 뿐입니다. 이 보고서가 엔터티의 최신 상태를 반영하는 맨 마지막으로 추가된 보고서입니다.
상태 관련 항목에 원하는 값을 포함한 [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings)를 전달하여 `FabricClient`를 만들 때 구성 매개 변수를 지정합니다.

다음 코드 예제에서는 패브릭 클라이언트를 생성하고 보고서가 추가되면 전송되도록 지정합니다. 시간이 초과되거나 재시도 가능한 오류가 발생할 경우 40초마다 재시도가 이뤄집니다.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

`HealthReportSendInterval`을 30초로 설정하는 기본 패브릭 클라이언트 설정을 유지하는 것이 좋습니다.  이 설정은 일괄 처리 때문에 최적 성능을 보장합니다. 가능한 즉시 내보내야 하는 중요 보고서의 경우 [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API에서 Immediate `true`와 함께 `HealthReportSendOptions`를 사용합니다. 즉시 보고서는 일괄 처리 간격을 무시합니다. 가능할 때마다 상태 클라이언트 일괄 처리의 장점을 활용하고자 하므로 이 플래그는 주의하여 사용합니다.  패브릭 클라이언트를 닫을 때(예: 프로세스에서 잘못된 상태를 확인했고 부작용을 막기 위해 종료해야 하는 경우)도 즉시 보내기가 도움이 됩니다. 누적 보고서는 최적의 보내기 작업입니다. 한 보고서에 Immediate 플래그가 추가되면 상태 클라이언트가 지난 번 전송 이후 모든 누적된 보고서를 일괄 처리합니다.

PowerShell을 통해 클러스터에 대한 연결을 생성할 때 동일한 매개 변수를 지정할 수 있습니다. 다음 예제에서는 로컬 클러스터에 대한 연결을 시작합니다.

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

API와 마찬가지로 `HealthReportSendInterval` 값에 관계없이 `-Immediate` 스위치를 사용하여 보고서를 즉시 보낼 수 있습니다. 

REST의 경우 보고서를 내부 패브릭 클라이언트가 있는 Service Fabric 게이트웨이로 보냅니다. 기본적으로 이 클라이언트는 30초마다 일괄 처리된 보고서를 보내도록 구성됩니다. `HttpGateway`에서 클러스터 구성 설정 `HttpGatewayHealthReportSendInterval`을 사용하여 일괄 처리 간격을 변경할 수 있습니다.  설명한 것처럼 `Immediate` true로 보고서를 보내는 옵션이 더 낫습니다. 

> [!NOTE]
> 권한이 없는 서비스가 클러스터 내의 엔터티에 대한 상태를 보고할 수 없도록 하려면 보안이 확인된 클라이언트에서 보내는 요청만을 받아들이도록 서버를 구성합니다. 보고에 사용되는 `FabricClient` 는 클러스터와 통신할 수 있도록 보안이 활성화되어야 합니다(예: Kerberos 또는 인증서 인증). [클러스터 보안](service-fabric-cluster-security.md)에 대해 자세히 알아봅니다.
> 
> 

## <a name="report-from-within-low-privilege-services"></a>권한이 낮은 서비스 내에서 보고
Service Fabric 서비스에 클러스터에 대한 관리 액세스 권한이 없는 경우 `Partition` 또는 `CodePackageActivationContext`를 통해 현재 컨텍스트에서 엔터티에 대한 상태를 보고할 수 있습니다.

* 상태 비저장 서비스의 경우에는 [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) 를 사용하여 현재 서비스 인스턴스에 대해 보고합니다.
* 상태 저장 서비스의 경우에는 [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) 를 사용하여 현재 복제본에 대해 보고합니다.
* 현재 파티션 엔터티에 대해 보고하려면 [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) 를 사용합니다.
* 현재 애플리케이션에 대해 보고하려면 [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) 를 사용합니다.
* 현재 노드에 배포된 현재 애플리케이션에 대해 보고하려면 [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) 를 사용합니다.
* 현재 노드에 배포된 애플리케이션의 서비스 패키지에 대해 보고하려면 [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth)를 사용합니다.

> [!NOTE]
> 내부적으로 `Partition` 및 `CodePackageActivationContext`는 기본 설정으로 구성된 상태 클라이언트를 포함합니다. [상태 클라이언트](service-fabric-report-health.md#health-client)에서 설명한 것처럼 보고서는 타이머에서 일괄 처리 및 전송됩니다. 보고서를 보낼 수 있게 개체는 활성 상태로 유지되어야 합니다.
> 
> 

`Partition` 및 `CodePackageActivationContext` 상태 API를 통해 보고서를 보낼 때 `HealthReportSendOptions`를 지정할 수 있습니다. 가능한 빨리 전송되어야 하는 중요 보고서의 경우 Immediate `true`와 함께 `HealthReportSendOptions`를 사용합니다. 즉시 보고서는 내부 상태 클라이언트의 일괄 처리 간격을 무시합니다. 앞서 설명한 것처럼 가능할 때마다 상태 클라이언트 일괄 처리의 장점을 활용하고자 하므로 이 플래그는 주의하여 사용합니다. 

## <a name="design-health-reporting"></a>상태 보고 설계
고품질 보고서를 생성하는 첫 단계는 서비스 상태에 영향을 미칠 수 있는 조건을 파악하는 것입니다. 문제가 시작될 때 또는 가장 이상적으로 문제가 발생하기 전에 서비스 또는 클러스터의 문제를 플래깅하는 데 도움이 되는 모든 조건을 파악하면 잠재적으로 수십억 달러를 절약할 수 있습니다. 그러면 중지 시간을 줄이고, 문제 조사와 복구에 소요되는 야간 시간을 절약하고, 고객 만족도를 높일 수 있습니다.

조건이 확인되면 Watchdog 작성자는 오버헤드와 유효성 사이에서 적절한 균형을 잡기 위한 최선의 모니터링 방법을 알아내야 합니다. 예를 들어 공유 위치에서 임시 파일을 사용하여 복잡한 계산을 수행하는 서비스가 있다고 가정하겠습니다. Watchdog는 공유 위치의 공간을 충분히 확보하기 위하여 공유 위치를 모니터링할 수 있습니다. 파일/디렉토리 변경에 대한 알림을 수신할 수 있습니다. 중요한 임계값에 도달하면 경고를, 공유 공간이 꽉 차면 오류를 보고할 수 있습니다. 경고가 보고되면 복구 시스템이 공유 공간에서 오래된 파일 정리를 시작할 수 있습니다. 오류가 보고되면 복구 시스템이 서비스 복제본을 다른 노드로 이동할 수 있습니다. 조건을 상태와 관련 지어 설명하는 방식, 다시 말해서 정상(양호) 또는 비정상(경고 또는 오류)으로 간주할 수 있는 조건의 상태에 유의해야 합니다.

모니터링 세부 정보가 설정되면 Watchdog 작성자는 Watchdog 구현 방법을 생각해야 합니다. 서비스 내에서 조건을 결정할 수 있으면 Watchdog는 모니터링되는 서비스의 일부가 될 수 있습니다. 예를 들어 서비스 코드는 공유 사용을 확인한 다음 파일 쓰기를 시도할 때마다 보고할 수 있습니다. 이러한 접근의 이점은 보고가 간단하다는 것입니다. Watchdog 버그가 서비스 기능에 영향을 미치지 않도록 주의를 기울여야 합니다.

모니터링되는 서비스 내에서의 보고를 선택할 수 없는 경우도 있습니다. 서비스 내의 watchdog가 조건을 감지하지 못할 수 있습니다. 결정에 필요한 논리 또는 데이터가 없을 수 있습니다. 조건 모니터링의 오버헤드가 높을 수 있습니다. 조건이 서비스에 한정되지는 않지만 그 대신 서비스 간의 상호 작용에 영향을 미칠 수 있습니다. 클러스터에 Watchdog를 별개의 프로세스로 두는 방법도 있습니다. Watchdog는 주 서비스에 전혀 영향을 미치지 않고 조건과 보고서 모니터링만 합니다. 예를 들어 이러한 Watchdog는 동일한 애플리케이션 내에서 상태 비저장 서비스로 구현되어 모든 노드에 배포되거나 서비스와 같은 노드에 배포될 수 있습니다.

클러스터에서 실행되는 Watchdog를 사용할 수 없는 경우가 종종 있습니다. 사용자가 서비스를 보는 동안 모니터링되는 조건이 서비스의 가용성 또는 기능인 경우 Watchdog를 사용자 클라이언트와 동일한 위치에 두는 것이 가장 좋습니다. 사용자가 서비스를 호출하는 것과 같은 방법으로 작업을 테스트할 수 있기 때문입니다. 예를 들어 클러스터 외부에 상주하면서 서비스에 요청을 보낸 후 대기 시간 및 결과의 정확성을 확인하는 watchdog를 둘 수 있습니다. (예: 계산기 서비스의 경우 2+2가 합당한 시간 내에 4를 반환합니까?)

Watchdog 세부 정보가 마무리되면 Watchdog를 고유하게 식별하는 소스 ID를 결정해야 합니다. 클러스터에 동일한 유형의 Watchdog가 여러 개 있는 경우 서로 다른 엔터티에 대해 보고하거나, 동일한 엔터티에 보고한다면 다른 소스 ID나 속성을 사용해야 합니다. 그래야만 보고서가 공존할 수 있습니다. 상태 보고서 속성은 모니터링되는 조건을 포착해야 합니다. 위의 예제에서 속성은 **ShareSize**일 수 있습니다. 같은 조건에 여러 보고서가 적용되는 경우 보고서가 공존할 수 있도록 속성에 동적 정보가 포함되어야 합니다. 예를 들어 여러 개의 공유를 모니터링해야 하는 경우 속성 이름으로 **ShareSize-sharename**을 사용할 수 있습니다.

> [!NOTE]
> 상태 저장소는 상태 정보를 보관하는 데 사용되지 *않아야* 합니다. 상태와 관련된 정보만 보고되어야 합니다. 이 정보는 엔터티 상태 평가에 영향을 미치기 때문입니다. Health 스토어는 범용 저장소로 설계되지 않았습니다. Health 스토어는 상태 평가 논리를 사용하여 모든 데이터를 성능 상태로 집계합니다. 상태와 무관한 정보(예: 정상 상태 보고)를 전송해도 집계된 성능 상태에는 영향을 미치지 않지만 Health 스토어의 성능에 부정적인 영향을 미칠 수 있습니다.
> 
> 

다음으로 결정할 내용은 보고할 엔터티를 결정하는 것입니다. 대부분 조건은 엔터티를 명확히 식별합니다. 최대한 세분화하여 엔터티를 선택해야 합니다. 조건이 파티션 내의 모든 복제본에 영향을 미치는 경우에는 서비스가 아닌 파티션에 대해 보고합니다. 그런데 세심한 주의가 필요한 사각 지대가 있습니다. 조건이 복제본 같은 하나의 엔터티에만 영향을 미치지만 복제본 수명 기간이 지난 후에도 조건에 플래그를 달고자 한다면 파티션에 대해 보고해야 합니다. 그렇지 않은 경우 복제본이 삭제되었을 때 상태 저장소의 모든 보고서가 정리됩니다. Watchdog 작성자는 엔터티 및 보고서의 수명을 고려해야 합니다. 저장소에서 보고서가 삭제되는 시점(예: 한 엔터티에 대해 보고된 오류가 더 이상 적용되는 않는 시점)도 명확해야 합니다.

설명한 요점을 모아놓은 예를 살펴보겠습니다. 모든 노드에 배포된 마스터 상태 저장 지속 서비스 및 보조 상태 비저장 서비스로 구성된 서비스 패브릭 애플리케이션이 있습니다(각 태스크 유형에 대한 한 가지 보조 서비스 유형). 마스터에는 보조에서 실행할 명령이 포함된 처리 큐가 있습니다. 보조는 들어오는 요청을 실행하고 승인 신호를 반환합니다. 모니터링이 가능한 한 가지 조건은 마스터 처리 큐의 길이입니다. 마스터 큐 길이가 임계값에 도달하면 경고가 보고됩니다. 경고는 보조가 부하를 처리할 수 없다는 뜻입니다. 큐가 최대 길이에 도달하고 명령이 삭제되면 서비스를 복구할 수 없으므로 오류가 보고됩니다. **QueueStatus**속성에 대한 보고서일 수도 있습니다. Watchdog는 서비스 내에 있으며 마스터 주 복제본에서 주기적으로 전송됩니다. TTL이 2분이며 30초마다 주기적으로 전송됩니다. 주 복제본이 다운되면, 보고서는 저장소에서 자동으로 삭제됩니다. 서비스 복제본이 실행 중이지만 교착 상태에 있거나 다른 문제가 있으면 보고서는 Health 스토어에서 만료됩니다. 이 경우 엔터티는 오류 상황에서 평가됩니다.

모니터링할 수 있는 또 다른 조건은 작업 수행 시간입니다. 마스터는 작업 유형을 기반으로 보조에 작업을 분배합니다. 설계 내용에 따라서 마스터는 작업 상태를 보조에 폴링하거나 작업 완료 후 보조가 승인 신호를 반환할 때까지 기다릴 수 있습니다. 후자의 경우 보조가 종료되거나 메시지가 손실되는 상황을 감지할 수 있도록 주의를 기울여야 합니다. 한 가지 방법은 마스터가 동일한 보조에 ping 요청을 보내는 것입니다. 그러면 보조가 상태를 반환합니다. 상태가 수신되지 않으면 마스터는 실패로 간주하고 작업 일정을 다시 세웁니다. 이러한 동작은 작업을 멱등 상태로 가정합니다.

모니터링된 조건은 일정 시간(**t1**, 예를 들어 10분) 내에 작업이 완료되지 않으면 경고로 해석될 수 있습니다. 작업이 시간(**t2**, 예를 들어 20분) 내에 완료되지 않으면 모니터링된 조건이 오류로 해석될 수 있습니다. 이러한 보고는 여러 가지 방법으로 수행될 수 있습니다.

* 마스터 주 복제본은 주기적으로 자체 보고합니다. 큐에 있는 모든 보류 중인 작업에 대해 하나의 속성을 사용할 수 있습니다. 하나 이상의 작업이 더 오래 걸리면 **PendingTasks** 속성에 대한 상태가 상황에 따라 경고 또는 오류로 보고됩니다. 보류 중인 작업이 없거나 모든 작업이 실행을 시작한 경우에는 보고서 상태가 정상입니다. 작업은 지속됩니다. 주 복제본이 다운되면 새로 승격된 주 복제본이 계속해서 제대로 보고할 수 있습니다.
* (클라우드 또는 외부에 존재하는) 다른 Watchdog 프로세스는 작업 완료 여부를 보기 위하여 (원하는 작업 결과를 기반으로 외부에서) 작업을 확인합니다. 이러한 프로세스가 임계값을 준수하지 않으면 마스터 서비스에 대한 보고서가 전송됩니다. 작업 식별자(예: **PendingTask + taskId**)가 들어 있는 각 작업에 대한 보고서도 전송됩니다. 비정상 상태에 대한 보고서만 전송되어야 합니다. TTL(Time To Live)을 몇 분 정도로 설정하고 만료되면 보고서가 제거되도록 표시해 두어, 확실히 제거되도록 합니다.
* 작업을 수행하는 보조는 작업 수행에 예상보다 긴 시간이 소요되면 보고합니다. **PendingTasks**속성의 서비스 인스턴스를 보고합니다. 보고서는 문제가 있는 서비스 인스턴스를 정확히 찾아내지만 인스턴스가 사라지는 상황을 포착하지는 않습니다. 보고서는 그때 삭제됩니다. 보조 서비스를 보고할 수 있습니다. 보조가 작업을 완료하면 보조 인스턴스가 저장소에서 보고서를 삭제합니다. 보고서는 인증 메시지가 손실되고 마스터의 관점에서 작업이 마무리되지 않은 상황을 포착하지 않습니다.

하지만 위에 설명된 상황에서 보고가 수행되며, 상태를 평가할 때 애플리케이션 상태에서 보고서가 포착됩니다.

## <a name="report-periodically-vs-on-transition"></a>주기적 보고 대 전환기 보고
상태 보고 모델을 사용하면 Watchdog에서 주기적으로 또는 전환기에 보고서를 보낼 수 있습니다. 코드가 훨씬 간단하고 오류 가능성이 적으므로 Watchdog 보고에는 주기적 보고를 권장합니다. 잘못된 보고서를 트리거하는 버그를 방지할 수 있도록 Watchdog는 최대한 간단해야 합니다. 잘못된 *비정상* 보고서는 상태 평가에 영향을 미치고 업그레이드를 비롯한 상태 기반 시나리오에도 영향을 미칩니다. 잘못된 *정상* 보고서는 클러스터의 문제를 숨기는데, 이것은 바람직하지 않습니다.

주기적인 보고를 위해 Watchdog를 타이머와 함께 구현할 수 있습니다. 타이머 콜백이 발생하면 Watchdog는 상태를 확인하고 현재 상태를 기반으로 보고서를 보냅니다. 이전에 어떤 보고서를 전송했는지 확인하거나 메시지 전송과 관련한 최적화를 수행할 필요가 없습니다. 상태 클라이언트에는 성능을 돕기 위한 일괄 처리 논리가 있습니다. 상태 클라이언트가 유지되는 동안 Health 스토어에서 보고서가 ACK될 때까지 또는 Watchdog가 동일한 엔터티, 속성 및 소스에 대한 새 보고서를 생성할 때가지 내부적으로 재시도합니다.

전환기 보고는 꼼꼼한 상태 처리가 필요합니다. Watchdog는 조건을 모니터링하면서 조건이 변경된 경우에만 보고합니다. 이 방식은 필요한 보고서의 수가 적다는 장점이 있지만 Watchdog의 논리가 복잡하다는 단점이 있습니다. Watchdog는 상태 변화를 판단하기 위해 조건이나 보고서를 검사할 수 있도록 조건이나 보고서를 유지해야 합니다. 장애 조치의 경우 추가되었으나 아직 상태 저장소로 보내지 않은 보고서에 대해 주의가 필요합니다. 시퀀스 번호는 계속 증가해야 합니다. 그렇지 않으면 시퀀스 번호가 오래되어 보고서가 거부됩니다. 드물지만 데이터 손실이 발생하는 경우에는 보고자의 상태와 Health 스토어의 상태 사이에 동기화가 필요할 수 있습니다.

전환에 대한 보고는 `Partition` 또는 `CodePackageActivationContext`를 통한 자체에 대한 서비스 보고에 적합합니다. 로컬 개체(복제본 또는 배포된 서비스 패키지/배포된 애플리케이션)가 제거되면 해당 보고서도 모두 제거됩니다. 이러한 자동 정리는 보고자와 Health 스토어 간을 동기화할 필요가 없도록 합니다. 부모 파티션 또는 부모 애플리케이션에 대한 보고에서는 Health 스토어에 사용되지 않는 보고서가 생성되지 않도록 주의해서 장애 조치(failover)가 진행되어야 합니다. 올바른 상태를 유지하고 더 이상 필요하지 않은 경우 스토어에서 보고서를 지우는 논리를 추가해야 합니다.

## <a name="implement-health-reporting"></a>상태 보고 구현
엔터티와 보고서 세부 사항이 명확해지면 API, PowerShell 또는 REST를 통해 상태 보고서를 보낼 수 있습니다.

### <a name="api"></a>API
API를 통해 보고하려면 보고하려는 엔터티 유형에 맞는 상태 보고서를 만들어서 상태 클라이언트에 보고서를 제공해야 합니다. 또는 상태 정보를 만들고 `Partition` 또는 `CodePackageActivationContext`에 대한 올바른 보고 메서드에 전달하여 현재 엔터티에 대해 보고합니다.

다음은 클러스터 내의 Watchdog에서 보내는 주기적 보고서의 예입니다. Watchdog는 노드 내에서 외부 리소스를 액세스할 수 있는지 여부를 확인합니다. 리소스는 애플리케이션 내의 서비스 매니페스트에 필요합니다. 리소스를 사용할 수 없더라도 애플리케이션 내의 다른 서비스는 여전히 정상적으로 작동할 수 있습니다. 따라서 배포된 서비스 패키지 엔터티에 대한 보고서가 주기적으로 30초마다 전송됩니다.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
**Send-ServiceFabric*EntityType*HealthReport**를 사용하여 상태 보고서를 보냅니다.

다음은 노드의 CPU 값에 대한 주기적인 보고의 사례입니다. 보고서는 30초마다 전송되어야 하며, 보고서의 TTL은 2분입니다. 보고서가 만료되면 보고자에게 문제가 있다는 뜻이므로 노드가 오류로 평가됩니다. CPU가 임계값을 초과하면 보고서의 성능 상태는 경고가 됩니다. CPU가 구성된 시간보다 더 긴 시간 동안 임계값을 초과하면 오류로 보고합니다. 그렇지 않으면 보고자가 정상 상태로 보고합니다.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

다음 예는 복제본에 대한 일시적인 경고를 보고합니다. 우선 파티션 ID를 가져온 다음 알고 싶은 서비스의 복제본 ID를 가져옵니다. 그런 다음 **PowershellWatcher**에서 **ResourceDependency** 속성에 대한 보고서를 보냅니다. 보고서는 2분 동안 지속된 후 저장소에서 자동으로 삭제됩니다.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST (영문)
원하는 엔터티로 이동하는 POST 요청과 REST를 사용하고 본문에 상태 보고서 설명을 넣어서 상태 보고서를 보냅니다. 예를 들어, REST [클러스터 상태 보고서](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) 또는 [서비스 상태 보고서](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)를 보내는 방법을 참조하세요. 모든 엔터티가 지원됩니다.

## <a name="next-steps"></a>다음 단계
상태 데이터를 기반으로 서비스 작성자 및 클러스터/응용 프로그램 관리자는 정보를 소비하는 방식에 대해 생각할 수 있습니다. 예를 들어 성능 상태를 기반으로 경고를 설정하면 서비스가 중단되기 전에 심각한 문제를 포착할 수 있습니다. 또한 관리자는 자동으로 문제를 해결하는 복구 시스템을 설정할 수 있습니다.

[서비스 패브릭 상태 모니터링 소개](service-fabric-health-introduction.md)

[서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

[서비스 상태를 보고 및 확인하는 방법](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[시스템 상태 보고서를 문제 해결에 사용](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[로컬로 서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric 애플리케이션 업그레이드](service-fabric-application-upgrade.md)

