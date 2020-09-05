---
title: Azure Service Fabric를 사용 하 여 상태 보고 및 확인
description: Azure 서비스 패브릭에서 제공하는 상태 모니터링 도구를 사용하여 서비스 코드에서 상태 보고서를 보내고 서비스의 상태를 확인하는 방법에 대해 알아보세요.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 59c8202b03bf1be2be5a68b75a1d7c7404b2213d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020204"
---
# <a name="report-and-check-service-health"></a>서비스 상태 보고 및 확인
서비스에 문제가 발생할 때 인시던트 및 중단에 응답하고 수정하는 능력은 문제를 빠르게 검색할 수 있는 능력과 밀접한 관련이 있습니다. 서비스 코드에서 Azure 서비스 패브릭 상태 관리자로 문제 및 오류를 보고하면 서비스 패브릭이 제공하는 표준 상태 모니터링 도구를 사용하여 상태를 확인할 수 있습니다.

세 가지 방법으로 서비스에서 상태를 보고할 수 있습니다.

* [Partition](/dotnet/api/system.fabric.istatefulservicepartition) 또는 [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) 개체를 사용합니다.  
  `Partition` 및 `CodePackageActivationContext` 개체를 사용하여 현재 컨텍스트의 일부인 요소의 상태를 보고할 수 있습니다. 예를 들어 복제본의 일부로 실행되는 코드는 해당 복제본, 복제본이 속하는 파티션 및 복제본을 포함하는 애플리케이션에 대해서만 상태를 보고할 수 있습니다.
* `FabricClient`을 사용합니다.   
  `FabricClient` 를 사용하면 클러스터가 [보안](service-fabric-cluster-security.md) 상태가 아니거나 서비스가 관리자 권한으로 실행되는 경우에 서비스 코드에서 상태를 보고할 수 있습니다. 대부분의 실제 시나리오에서는 보안되지 않은 클러스터를 사용하거나 관리자 권한을 제공하지 않습니다. `FabricClient`를 사용하면 클러스터의 일부인 모든 엔터티의 상태를 보고할 수 있습니다. 하지만 이상적으로 서비스 코드는 자체 상태와 관련된 보고서만 보내야 합니다.
* 클러스터, 애플리케이션, 배포된 애플리케이션, 서비스, 서비스 패키지, 파티션, 복제본 또는 노드 수준에서 REST API를 사용합니다. 이는 컨테이너 내에서 상태를 보고하는 데 사용할 수 있습니다.

이 문서에서는 서비스 코드에서 상태를 보고하는 예제를 보여 줍니다. 또한 이 예제에서는 Service Fabric에서 제공하는 도구를 사용하여 상태를 확인할 수 있는 방법을 보여 줍니다. 이 문서는 서비스 패브릭의 상태 모니터링 기능을 신속하게 소개할 목적으로 작성되었습니다. 보다 자세한 내용은 이 문서의 마지막 부분에 있는 링크를 누르면 시작되는 상태에 대한 심화 문서 시리즈를 통해 알아볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
다음이 설치되어 있어야 합니다.

* Visual Studio 2015 또는 Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>로컬 보안 개발자 클러스터를 만들려면
* 관리자 권한으로 PowerShell을 열고 다음 명령을 실행합니다.

![보안 개발자 클러스터를 만드는 방법을 보여 주는 명령](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>애플리케이션을 배포하고 해당 상태를 확인하려면
1. 관리자 권한으로 Visual Studio를 엽니다.
1. **상태 저장 서비스** 템플릿을 사용하여 프로젝트를 만듭니다.
   
    ![상태 저장 서비스를 사용하여 서비스 패브릭 애플리케이션 만들기](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. 디버그 모드에서 응용 프로그램을 실행 하려면 **f5** 키를 누릅니다. 애플리케이션이 로컬 클러스터에 배포됩니다.
1. 애플리케이션을 실행한 후 알림 영역에서 로컬 클러스터 관리자 아이콘을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **로컬 클러스터 관리**를 선택하여 Service Fabric 탐색기를 엽니다.
   
    ![알림 영역에서 서비스 패브릭 탐색기 열기](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. 애플리케이션의 상태는 이 이미지와 같이 표시되어야 합니다. 이때 애플리케이션은 오류 없이 정상이어야 합니다.
   
    ![서비스 패브릭 탐색기에서 정상적인 애플리케이션](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. PowerShell을 사용하여 상태를 확인할 수도 있습니다. ```Get-ServiceFabricApplicationHealth```를 사용하여 애플리케이션의 상태를 확인하고 ```Get-ServiceFabricServiceHealth```를 사용하여 서비스의 상태를 확인할 수 있습니다. 이 이미지에 동일한 애플리케이션에 대해 PowerShell에서 제공하는 상태 보고서가 있습니다.
   
    ![PowerShell에서 정상적인 애플리케이션](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>서비스 코드에 사용자 지정 상태 이벤트를 추가하려면
Visual Studio의 서비스 패브릭 프로젝트 템플릿에는 샘플 코드가 포함되어 있습니다. 다음 단계는 서비스 코드에서 사용자 지정 상태 이벤트를 보고할 수 있는 방법을 보여 줍니다. 이런 보고서는 Service Fabric 탐색기, Azure Portal 상태 보기 및 PowerShell과 같이 상태 모니터링을 위해 서비스 패브릭이 제공하는 표준 도구에 자동으로 표시됩니다.

1. Visual Studio에서 이전에 만든 애플리케이션을 다시 열거나 **상태 저장 서비스** Visual Studio 템플릿을 사용하여 새 애플리케이션을 만듭니다.
1. Stateful1.cs 파일을 열고 `RunAsync` 메서드의 `myDictionary.TryGetValueAsync` 호출을 찾습니다. 이 애플리케이션의 핵심 논리는 카운트 실행을 계속 유지하는 것이므로 이 메서드가 카운터의 현재 값을 보유하는 `result` 를 반환하는 것을 볼 수 있습니다. 이 응용 프로그램이 실제 응용 프로그램이 고 결과가 부족 하 여 오류가 표시 되는 경우 해당 이벤트에 플래그를 지정 하는 것이 좋습니다.
1. 결과 부족이 오류를 나타내는 경우 상태 이벤트를 보고하려면 다음 단계를 추가합니다.
   
    a. `System.Fabric.Health` 네임스페이스를 Stateful1.cs 파일에 추가합니다.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. `myDictionary.TryGetValueAsync` 호출 후 다음 코드를 추가합니다.
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    이 복제본 상태는 상태 저장 서비스에서 보고되므로 해당 상태를 보고합니다. `HealthInformation` 매개 변수가 보고되는 상태 문제에 대한 정보를 저장합니다.
   
    상태 비저장 서비스를 만든 경우 다음 코드를 사용합니다.
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. 서비스가 관리자 권한으로 실행되거나 클러스터가 [보안](service-fabric-cluster-security.md) 상태가 아닌 경우 다음 단계와 같이 `FabricClient`를 사용하여 상태를 보고할 수도 있습니다.  
   
    a. `var myDictionary` 선언 다음에 `FabricClient` 인스턴스를 만듭니다.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. `myDictionary.TryGetValueAsync` 호출 후 다음 코드를 추가합니다.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. 이 오류를 시뮬레이트하여 상태 모니터링 도구에서 오류가 표시되는지 확인합니다. 오류를 시뮬레이트하려면 이전에 추가한 상태 보고 코드의 첫 번째 줄을 주석 처리합니다. 첫 번째 줄을 주석 처리하고 나면 코드가 다음 예제와 같이 표시됩니다.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   이제 이 코드는 `RunAsync`가 실행될 때마다 이 상태 보고서를 발생시킵니다. 변경한 후 **F5** 키를 눌러 애플리케이션을 실행합니다.
1. 애플리케이션을 실행한 후에 서비스 패브릭 탐색기를 열어 애플리케이션의 상태를 확인합니다. 이번에는 Service Fabric 탐색기가 애플리케이션이 비정상이라고 표시합니다. 이전에 추가한 코드에서 보고 된 오류 때문에 응용 프로그램이 비정상 상태로 표시 됩니다.
   
    ![서비스 패브릭 탐색기에서 비정상적인 애플리케이션](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. 서비스 패브릭 탐색기의 트리 뷰에서 주 복제본을 선택하면 **성능 상태** 도 오류를 표시합니다. 서비스 패브릭 탐색기는 코드에서 `HealthInformation` 매개 변수에 추가된 상태 보고서 세부 정보도 표시합니다. PowerShell 및 Azure Portal에서도 동일한 상태 보고서를 볼 수 있습니다.
   
    ![서비스 패브릭 탐색기 내 복제본 상태](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

이 보고서는 다른 보고서로 대체되거나 이 복제본이 삭제될 때까지 상태 관리자에 그대로 남아 있습니다. `HealthInformation` 개체의 이 상태 보고서에 대해 `TimeToLive`를 설정하지 않았기 때문에 보고서는 만료되지 않습니다.

가장 세분화된 수준(이 경우에서는 복제본)에서 상태를 보고하는 것이 좋습니다. `Partition`에 대해 상태를 보고할 수도 있습니다.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

`Application`, `DeployedApplication` 및 `DeployedServicePackage`에 대한 상태를 보고하려면 `CodePackageActivationContext`를 사용합니다.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 상태 심층 분석](service-fabric-health-introduction.md)
* [서비스 상태를 보고하기 위한 REST API](/rest/api/servicefabric/report-the-health-of-a-service)
* [애플리케이션 상태를 보고하기 위한 REST API](/rest/api/servicefabric/report-the-health-of-an-application)
