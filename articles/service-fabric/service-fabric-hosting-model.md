---
title: Azure Service Fabric 호스팅 모델
description: 배포된 Service Fabric 서비스 및 서비스 호스트 프로세스 복제본(또는 인스턴스) 간의 관계에 대해 설명합니다.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e14995b92e99e1a9695f81fb71bcab6dd62303a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011670"
---
# <a name="azure-service-fabric-hosting-model"></a>Azure Service Fabric 호스팅 모델
이 아티클에서는 Azure Service Fabric에서 제공하는 애플리케이션 호스팅 모델을 간략하게 설명하고 **공유 프로세스** 및 **단독 프로세스** 모델 간의 차이점을 설명합니다. 배포된 애플리케이션이 Service Fabric 노드에 표시되는 방식과 서비스 복제본(또는 인스턴스) 및 서비스-호스트 프로세스 간의 관계를 설명합니다.

계속하기 전에 [Service Fabric에서 애플리케이션 모델링][a1]에서 설명한 다양한 개념 및 관계를 이해해야 합니다. 

> [!NOTE]
> 이 아티클에서는 다른 의미로 명시적으로 언급되지 않으면:
>
> - *복제본*은 상태 저장 서비스 복제본 및 상태 비저장 서비스 인스턴스를 모두 가리킵니다.
> - *CodePackage*는 *ServiceType*을 등록하고 해당 *ServiceType*의 서비스 복제본을 호스트하는 *ServiceHost* 프로세스와 동일한 것으로 간주됩니다.
>

호스팅 모델을 이해하기 위해 한 가지 예를 살펴보겠습니다. *ServiceType* 'MyServiceType'이 있는 *ApplicationType* 'MyAppType'이 있다고 가정합니다. 'MyServiceType'은 *CodePackage* 'MyCodePackage'가 있는 *ServicePackage* 'MyServicePackage'에서 제공됩니다. 'MyCodePackage'는 실행될 때 *ServiceType* 'MyServiceType'을 등록합니다.

3개의 노드 클러스터가 있고, 'MyAppType' 형식의 *애플리케이션* **fabric:/App1**을 만든다고 가정합니다. 이 애플리케이션 **fabric:/App1** 내에서 'MyServiceType' 형식의 **fabric:/App1/ServiceA** 서비스를 만듭니다. 이 서비스에는 두 개의 파티션(예: **P1** 및 **P2**) 및 파티션당 세 개의 복제본이 있습니다. 다음 다이어그램은 이 애플리케이션이 노드에 배포된 상태 보기를 보여 줍니다.


![배포된 애플리케이션의 노드 보기 다이어그램][node-view-one]


Service Fabric은 'MyServicePackage'를 활성화했으며, 이 서비스 패키지가 두 파티션의 복제본을 호스트하는 'MyCodePackage'를 시작했습니다. 파티션당 복제본 수를 클러스터의 노드 수와 같도록 선택했기 때문에 클러스터에 있는 모든 노드 보기가 같습니다. 애플리케이션 **fabric:/App1**에서 **fabric:/App1/ServiceB**라는 다른 서비스를 만들어 보겠습니다. 이 서비스에는 한 개의 파티션(예: **P3**) 및 파티션당 세 개의 복제본이 있습니다. 다음 다이어그램은 새로운 노드 보기를 보여 줍니다.


![배포된 애플리케이션의 노드 보기 다이어그램][node-view-two]


Service Fabric이 **fabric:/App1/ServiceB** 서비스의 **P3** 파티션에 대한 새 복제본을 기존 'MyServicePackage' 활성화에 배치했습니다. 이제부터는 'MyAppType' 형식의 다른 애플리케이션 **fabric:/App2**를 만들어 보겠습니다. **fabric:/App2** 내에서 **fabric:/App2/ServiceA** 서비스를 만듭니다. 이 서비스에는 두 개의 파티션(**P4** 및 **P5**) 및 파티션당 세 개의 복제본이 있습니다. 다음 다이어그램은 새 노드 보기를 보여줍니다.


![배포된 애플리케이션의 노드 보기 다이어그램][node-view-three]


Service Fabric은 'MyCodePackage'의 새 복사본을 시작하는 'MyServicePackage'의 새 복사본을 활성화합니다. **fabric:/App2/ServiceA** 서비스의 두 파티션에서 복제본(**P4** 및 **P5**)은 이 새 복사본 'MyCodePackage'에 배치됩니다.

## <a name="shared-process-model"></a>공유 프로세스 모델
앞의 섹션에서는 Service Fabric에서 제공하는 공유 프로세스 모델이라는 기본 호스팅 모델을 설명했습니다. 이 모델에서는 지정된 애플리케이션에 대해 지정된 *ServicePackage*의 복사본 하나만이 노드에서 활성화됩니다(모두 포함된 *CodePackages*를 시작). 지정된 *ServiceType*이라는 모든 서비스의 모든 복제본은 해당 *ServiceType*을 등록한 *CodePackage*에 배치됩니다. 즉, 지정된 *ServiceType*의 노드에 있는 모든 서비스 복제본은 모두 동일한 프로세스를 공유합니다.

## <a name="exclusive-process-model"></a>단독 프로세스 모델
Service Fabric에서 제공하는 다른 호스팅 모델은 단독 프로세스 모델입니다. 이 모델에서 지정된 노드에 대해 각 복제본이 고유한 전용 프로세스에 상주합니다. Service Fabric은 *ServicePackage*의 새 복사본을 활성화합니다(포함된 모든 *CodePackages*를 시작). 복제본은 복제본이 속해 있는 서비스의 *ServiceType*을 등록한 *CodePackage*에 배치됩니다. 

Service Fabric 버전 5.6 이상을 사용하는 경우 서비스를 만들 때 [PowerShell][p1], [REST][r1] 또는 [FabricClient][c1]를 사용하여 단독 프로세스 모델을 선택할 수 있습니다. **ServicePackageActivationMode**를 'ExclusiveProcess'으로 지정합니다.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

애플리케이션 매니페스트에 기본 서비스가 있는 경우 **ServicePackageActivationMode** 특성을 지정하여 단독 프로세스 모델을 선택할 수 있습니다.

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
이제 애플리케이션 **fabric:/App1**에서 **fabric:/App1/ServiceC**라는 다른 서비스를 만들어 보겠습니다. 이 서비스에는 두 개의 파티션(예: **P6** 및 **P7**) 및 파티션당 세 개의 복제본이 있습니다. **ServicePackageActivationMode**를 'ExclusiveProcess'로 설정합니다. 다음 다이어그램은 새로운 노드 보기를 보여줍니다.


![배포된 애플리케이션의 노드 보기 다이어그램][node-view-four]


보시는 것처럼 Service Fabric은 **P6** 및 **P7** 파티션의 각 복제본에 대해 하나씩 두 개의 새로운 'MyServicePackage' 복제본을 활성화했습니다. Service Fabric은 *CodePackage*의 전용 복사본에 각 복제본을 배치했습니다. 단독 프로세스 모델을 사용하는 경우 지정된 애플리케이션에 대해 지정된 *ServicePackage*의 여러 복사본을 노드에서 활성화할 수 있습니다. 앞의 예제에서 'MyServicePackage'의 세 개 복사본이 **fabric:/App1**에 대해 활성화되었습니다. 'MyServicePackage'의 각 활성 복사본에는 **ServicePackageActivationId**가 연결되어 있습니다. 이 ID는 **fabric:/App1** 애플리케이션 내에서 해당 복사본을 식별합니다.

애플리케이션에 공유 프로세스 모델을 사용하는 경우 노드에는 *ServicePackage*의 활성 복사본이 하나만 있습니다. 이 활성화에서 *ServicePackage*의 **ServicePackageActivationId**는 빈 문자열입니다. 예를 들어 **fabric:/App2**를 사용하는 경우입니다.

> [!NOTE]
>- 공유 프로세스 호스팅 모델은 **ServicePackageActivationMode** = **SharedProcess**에 해당합니다. 이것이 기본 호스팅 모델이며, 서비스를 만들 때 **ServicePackageActivationMode**를 지정할 필요가 없습니다.
>
>- 단독 프로세스 호스팅 모델은 **ServicePackageActivationMode** = **ExclusiveProcess**에 해당합니다. 이 설정을 사용하려면 서비스를 만들 때 명시적으로 지정해야 합니다. 
>
>- 서비스의 호스팅 모델을 보려면 [서비스 설명][p2]을 쿼리하고 **ServicePackageActivationMode**의 값을 확인합니다.
>
>

## <a name="work-with-a-deployed-service-package"></a>배포된 서비스 패키지 작업
노드에서 *ServicePackage*의 활성 복사본을 [배포된 서비스 패키지][p3]라고 합니다. 단독 프로세스 모델을 사용하여 서비스를 만든 경우 지정된 애플리케이션에 대해 동일한 *ServicePackage*에 배포된 서비스 패키지가 여러 개 있을 수 있습니다. 배포된 서비스 패키지에 특정된 작업을 수행하는 경우 **ServicePackageActivationId**를 제공하여 특정 배포된 서비스 패키지를 식별해야 합니다. 예를 들어 [배포된 서비스 패키지의 상태를 보고][p4]하거나 [배포된 서비스 패키지의 코드 패키지를 다시 시작][p5]하려는 경우 ID를 제공합니다.

노드에서 [배포된 서비스 패키지][p3] 목록을 쿼리하여 배포된 서비스 패키지의 **ServicePackageActivationId**를 확인할 수 있습니다. 노드에서 [배포된 서비스 형식][p6], [배포된 복제본][p7] 및 [배포된 코드 패키지][p8]를 쿼리하는 경우 쿼리 결과에는 배포된 부모 서비스 패키지의 **ServicePackageActivationId**도 포함됩니다.

> [!NOTE]
>- 공유 프로세스 호스팅 모델 아래의 지정된 노드에서 지정된 애플리케이션에 대해 *ServicePackage* 복사본 하나만 활성화됩니다. *빈 문자열*과 같은 **ServicePackageActivationId**가 있으며, 배포된 서비스 패키지와 관련된 작업을 수행할 때 지정할 필요가 없습니다. 
>
> - 단독 프로세스 호스팅 모델의 경우 지정된 노드에서 지정된 애플리케이션에 대해 하나 이상의 *ServicePackage* 복사본이 활성화될 수 있습니다. 각 활성화에는 배포 된 서비스 패키지와 관련 된 작업을 수행 하는 동안 지정 된 *비어 있지 않은* **ServicePackageActivationId**있습니다. 
>
> - **ServicePackageActivationId**를 생략하면 기본적으로 *빈 문자열*로 설정됩니다. 공유 프로세스 모델 아래에 활성화된 배포된 서비스 패키지가 있으면 해당 패키지에서 작업이 수행됩니다. 그렇지 않으면 작업이 실패합니다.
>
> - 한 번 쿼리하고 **ServicePackageActivationId**를 캐시하지 않습니다. ID는 동적으로 생성되며 다양한 이유로 변경할 수 있습니다. **ServicePackageActivationId**가 필요한 작업을 수행하기 전에 먼저 노드에서 [배포된 서비스 패키지][p3] 목록을 쿼리해야 합니다. 그런 다음, 쿼리 결과의 **ServicePackageActivationId**를 사용하여 원래 작업을 수행합니다.
>
>

## <a name="guest-executable-and-container-applications"></a>게스트 실행 파일 및 컨테이너 애플리케이션
Service Fabric은 [게스트 실행 파일][a2] 및 [컨테이너][a3] 애플리케이션을 자체 포함된 상태 비저장 서비스로 처리합니다. *ServiceHost*(프로세스 또는 컨테이너)에는 Service Fabric 런타임이 없습니다. 이러한 서비스가 자체 포함되기 때문에 *ServiceHost*당 복제본 수가 적용되지 않습니다. 이러한 서비스에 사용되는 가장 일반적인 구성은 [InstanceCount][c2]가 -1과 같은 단일 파티션입니다(클러스터의 각 노드에서 서비스 코드 복사본 하나가 실행되고 있음). 

이러한 서비스에 대한 기본 **ServicePackageActivationMode**는 **SharedProcess**입니다. 이 경우에 Service Fabric은 지정된 애플리케이션에 대해 노드에서 *ServicePackage* 복사본 하나만을 활성화합니다.  즉, 서비스 코드 복사본 하나만 노드를 실행합니다. 서비스 코드의 여러 복사본을 노드에서 실행하려는 경우 서비스를 만들 때 **ServicePackageActivationMode**를 **ExclusiveProcess**로 지정합니다. 예를 들어 *ServiceType*(*ServiceManifest*에 지정)의 여러 서비스(*Service1*에서 *ServiceN*)를 만들 때 또는 서비스를 다중 분할할 때 수행할 수 있습니다. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>기존 서비스의 호스팅 모델 변경
현재는 기존 서비스의 호스팅 모델을 공유 프로세스에서 단독 프로세스로 (또는 그 반대로) 변경할 수 없습니다.

## <a name="choose-between-the-hosting-models"></a>호스팅 모델 중에서 선택
요구 사항에 맞는 최상의 호스팅 모델을 평가해야 합니다. 더 적은 프로세스가 생성되고 동일한 프로세스의 여러 복제본이 포트를 공유할 수 있기 때문에 공유 프로세스 모델에서는 운영 체제 리소스를 많이 사용합니다. 그러나 복제본 중 하나에서 서비스 호스트를 종료해야 하는 오류가 발생할 경우 동일한 프로세스의 다른 모든 복제본에 영향을 주게 됩니다.

 단독 프로세스 모델은 해당 프로세스의 모든 복제본에 대한 격리 기능을 제공합니다. 하나의 복제본에 오류가 있는 경우 다른 복제본에 영향을 주지 않습니다. 이 모델은 통신 프로토콜에서 포트 공유를 지원하지 않는 경우에 유용합니다. 복제본 수준에서 리소스 관리를 쉽게 적용할 수 있습니다. 그러나 단독 프로세스는 노드의 각 복제본마다 프로세스 하나를 생성하기 때문에 더 많은 운영 체제 리소스를 사용합니다.

## <a name="exclusive-process-model-and-application-model-considerations"></a>단독 프로세스 모델 및 애플리케이션 모델 고려 사항
대부분의 애플리케이션의 경우 *ServicePackage*당 하나의 *ServiceType*을 유지하여 Service Fabric에서 애플리케이션을 모델링할 수 있습니다. 

특정한 경우에 Service Fabric은 *ServicePackage*당 둘 이상의 *ServiceType*을 허용하며 하나의 *CodePackage*는 둘 이상의 *ServiceType*을 등록할 수 있습니다. 다음은 이러한 구성이 유용할만한 몇 가지 시나리오입니다.

- 더 적은 프로세스를 생성하고 프로세스당 더 높은 복제본 밀도를 사용하여 리소스 사용률을 최적화하려고 합니다.
- 다른 *Servicetypes* 의 복제본은 초기화 또는 메모리 비용이 높은 몇 가지 공통 데이터를 공유 해야 합니다.
- 무료 서비스 제품이 있으며, 서비스의 모든 복제본을 동일한 프로세스에 배치하여 리소스 사용률에 제한을 적용하려고 합니다.

단독 프로세스 호스팅 모델은 *ServicePackage*당 여러 *ServiceTypes*가 있는 애플리케이션 모델과 일치하지 않습니다. *ServicePackage*당 여러 *ServiceTypes*가 복제본 간의 리소스 공유를 높이도록 설계되었으며 프로세스당 복제본 고밀도를 지원하기 때문입니다. 단독 프로세스 모델은 이와 다른 결과를 달성하도록 설계되었습니다.

다른 *CodePackage*가 각 *ServiceType*을 등록하는 *ServicePackage*당 여러 *ServiceTypes*의 경우를 고려해보세요. 가령, *CodePackages* 2개가 있는 *ServicePackage* 'MultiTypeServicePackage'가 있다고 가정합니다.

- 'MyCodePackageA'는 *ServiceType* 'MyServiceTypeA'를 등록합니다.
- *ServiceType* ' MyServiceTypeB '를 등록 하는 ' MyCodePackageB '입니다.

이제 **fabric:/SpecialApp**이라는 애플리케이션을 만든다고 가정하겠습니다. **fabric:/SpecialApp** 내에서 단독 프로세스 모델을 사용하여 다음 두 가지 서비스를 만듭니다.

- 파티션 2개(예: **P1** 및 **P2**)와 파티션당 복제본 3개가 있는 'MyServiceTypeA' 형식의 **fabric:/SpecialApp/ServiceA** 서비스
- 파티션 2개(**P3** 및 **P4**)와 파티션당 복제본 3개가 있는 'MyServiceTypeB' 형식의 **fabric:/SpecialApp/ServiceB** 서비스

지정된 노드에서 두 서비스에 각각 복제본 2개가 있습니다. 단독 프로세스 모델을 사용하여 서비스를 만들었기 때문에 Service Fabric은 각 복제본에 대해 새로운 'MyServicePackage' 복사본을 활성화합니다. 'MultiTypeServicePackage'를 활성화할 때마다 'MyCodePackageA' 및 'MyCodePackageB'의 복사본이 시작됩니다. 그러나 'MyCodePackageA' 또는 'MyCodePackageB' 중 하나만 'MultiTypeServicePackage'가 활성화된 복제본을 호스트합니다. 다음 다이어그램은 노드 보기를 보여줍니다.


![배포된 애플리케이션의 노드 보기 다이어그램][node-view-five]


**fabric:/SpecialApp/ServiceA** 서비스의 **P1** 파티션 복제본에 대한 'MultiTypeServicePackage' 활성화에서 'MyCodePackageA'는 복제본을 호스트합니다. 'MyCodePackageB'는 실행됩니다. 비슷하게 **fabric:/SpecialApp/ServiceB** 서비스의 **P3** 파티션 복제본에 대한 'MultiTypeServicePackage' 활성화에서 'MyCodePackageB'는 복제본을 호스트합니다. 'MyCodePackageA'는 실행됩니다. 따라서 *ServicePackage*당 다른 *ServiceTypes*를 등록하는 *CodePackages* 수가 많을수록 중복 리소스 사용량이 증가합니다. 
 
 그러나 공유 프로세스 모델을 사용하여 **fabric:/SpecialApp/ServiceA** 및 **fabric:/SpecialApp/ServiceB** 서비스를 만드는 경우 Service Fabric은 애플리케이션 **fabric:/SpecialApp**에 대해 'MultiTypeServicePackage' 복사본 하나만 활성화합니다. 'MyCodePackageA'는 **fabric:/SpecialApp/ServiceA** 서비스에 대한 모든 복제본을 호스팅합니다. 'MyCodePackageB'는 **fabric:/SpecialApp/ServiceB** 서비스에 대한 모든 복제본을 호스팅합니다. 다음 다이어그램은 이 설정의 노드 보기를 보여 줍니다. 


![배포된 애플리케이션의 노드 보기 다이어그램][node-view-six]


위 예제에서 'MyCodePackageA'가 'MyServiceTypeA'와 'MyServiceTypeB'를 둘 다 등록하고 'MyCodePackageB'가 없을 경우 중복 *CodePackage*가 실행되지 않는다고 간주할 수 있습니다. 이것이 맞더라도 이 애플리케이션 모델은 단독 프로세스 호스팅 모델과 일치하지 않습니다. 각 복제본을 고유한 전용 프로세스에 배치하려는 경우 동일한 *CodePackage*에서 *ServiceTypes*를 모두 등록할 필요는 없습니다. 대신 고유한 *ServicePackage*에 *ServiceType*을 각각 배치하면 됩니다.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services 및 작업자 포크 하위 프로세스

Service Fabric은 신뢰할 수 있는 서비스 및 이후의 신뢰할 수 있는 작업자 포크 하위 프로세스를 지원하지 않습니다. [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet)를 사용하여 지원되지 않는 하위 프로세스를 등록할 수는 없는데, 취소 토큰은 등록된 프로세스로만 전송되므로 상위 프로세스가 취소 토큰을 수신한 후 하위 프로세스가 닫히지 않으면 업그레이드 오류 등의 많은 문제가 발생하기 때문입니다.

## <a name="next-steps"></a>다음 단계
[애플리케이션을 패키지][a4]하고 배포를 준비합니다.

[애플리케이션을 배포하고 제거][a5]합니다. 이 아티클에서는 PowerShell을 사용하여 애플리케이션 인스턴스를 관리하는 방법을 설명합니다.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
