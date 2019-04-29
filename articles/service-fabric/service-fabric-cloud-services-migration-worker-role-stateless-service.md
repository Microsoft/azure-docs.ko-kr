---
title: Azure Cloud Services 앱을 Service Fabric으로 변환 | Microsoft Docs
description: 이 가이드에서는 Cloud Services에서 서비스 패브릭으로 마이그레이션할 수 있도록 Cloud Services 웹과 작업자 역할 및 서비스 패브릭 상태 비저장 서비스를 비교합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 10fb44b0e76282ad78e7687beaa2e50e819e5cd9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110012"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>웹 및 작업자 역할을 서비스 패브릭 상태 비저장 서비스로 변환하기 위한 가이드
이 문서에서는 Cloud Services 웹 및 작업자 역할을 서비스 패브릭 상태 비저장 서비스로 마이그레이션하는 방법을 설명합니다. Cloud Services에서 전반적인 아키텍처를 대략적으로 동일하게 유지하는 애플리케이션에 대한 Service Fabric으로의 가장 간단한 마이그레이션 경로입니다.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>클라우드 서비스 프로젝트에서 Service Fabric 애플리케이션 프로젝트
 클라우드 서비스 프로젝트 및 Service Fabric 애플리케이션 프로젝트는 유사한 구조를 가지며 모두 애플리케이션에 대한 배포 단위를 나타냅니다. 즉, 각각은 애플리케이션을 실행하도록 배포되는 완전한 패키지를 정의합니다. 클라우드 서비스 프로젝트는 하나 이상의 웹 또는 작업자 역할을 포함합니다. 유사하게 Service Fabric 애플리케이션 프로젝트에는 하나 이상의 서비스가 포함되어 있습니다. 

클라우드 서비스 프로젝트는 애플리케이션 배포를 VM 배포와 결합하므로 VM 구성 설정을 포함하고 반면에 서비스 패브릭 애플리케이션 프로젝트는 서비스 패브릭 클러스터의 기존 VM 집합에 배포되는 애플리케이션을 정의한다는 차이점이 있습니다. Service Fabric 클러스터 자체는 리소스 관리자 템플릿 또는 Azure Portal을 통해 한 번만 배포되고 여러 Service Fabric 애플리케이션을 배포할 수 있습니다.

![서비스 패브릭 및 Cloud Services 프로젝트 비교][3]

## <a name="worker-role-to-stateless-service"></a>작업자 역할에서 상태 비저장 서비스
개념적으로 작업자 역할은 모든 작업 인스턴스는 동일하고 언제든지 인스턴스에 요청을 라우팅할 수 있음을 의미하는 상태 비저장 작업을 나타냅니다. 각 인스턴스는 이전 요청을 기억하지 않습니다. 작업이 작동 중인 상태는 Azure Table Storage 또는 Azure Document DB와 같은 외부 상태 저장소에서 관리됩니다. 서비스 패브릭에서 이러한 유형의 작업은 상태 비저장 서비스에 의해 표시됩니다. 작업자 역할을 서비스 패브릭으로 마이그레이션하는 가장 간단한 방법은 작업자 역할 코드를 상태 비저장 서비스로 변환하여 수행할 수 있습니다.

![작업자 역할에서 상태 비저장 서비스][4]

## <a name="web-role-to-stateless-service"></a>웹 역할에서 상태 비저장 서비스
작업자 역할과 마찬가지로 웹 역할도 상태 비저장 작업을 나타내므로 개념적으로 서비스 패브릭 상태 비저장 서비스에 매핑할 수 있습니다. 그러나 웹 역할과 달리 서비스 패브릭은 IIS를 지원하지 않습니다. 웹 역할의 웹 애플리케이션을 상태 비저장 서비스에 마이그레이션하려면 자체 호스팅될 수 있고 ASP.NET Core 1과 같은 IIS 또는 System.Web에 의존하지 않는 웹 프레임워크로 이동이 필요합니다.

| **애플리케이션** | **지원됨** | **마이그레이션 경로** |
| --- | --- | --- |
| ASP.NET 웹 양식 |아닙니다. |ASP.NET Core 1 MVC로 변환 |
| ASP.NET MVC |마이그레이션 사용 |ASP.NET Core 1 MVC로 업그레이드 |
| ASP.NET Web API |마이그레이션 사용 |자체 호스팅된 서버 또는 ASP.NET Core 1 사용 |
| ASP.NET Core 1 |예 |N/A |

## <a name="entry-point-api-and-lifecycle"></a>진입점 API 및 수명 주기
작업자 역할 및 Service Fabric 서비스 API는 비슷한 진입점을 제공합니다. 

| **진입점** | **작업자 역할** | **서비스 패브릭 서비스** |
| --- | --- | --- |
| 처리 중 |`Run()` |`RunAsync()` |
| VM 시작 |`OnStart()` |N/A |
| VM 중지 |`OnStop()` |N/A |
| 클라이언트 요청에 대한 수신기 열기 |N/A |<ul><li> 상태 비저장인 경우 `CreateServiceInstanceListener()`</li><li>상태 저장인 경우 `CreateServiceReplicaListener()`</li></ul> |

### <a name="worker-role"></a>작업자 역할
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>서비스 패브릭 상태 비저장 서비스
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

둘 모두는 처리를 시작하는 기본 "실행" 재정의를 갖습니다. Service Fabric 서비스는 `Run`, `Start` 및 `Stop`을 단일 액세스 지점, `RunAsync`로 결합합니다. 서비스는 `RunAsync`가 시작될 때 작동을 시작해야 하고 `RunAsync` 메서드의 CancellationToken이 신호될 때 작동을 중지해야 합니다. 

작업자 역할 및 서비스 패브릭 서비스의 수명 주기 및 수명 간에는 몇 가지 주요 차이점이 있습니다.

* **수명 주기:** 가장 큰 차이점은 작업자 역할은 VM의 수명 주기 VM 시작 및 중지 시기에 대 한 이벤트를 포함 하는 VM에 연결 되어 있으므로. 서비스 패브릭 서비스는 VM 수명 주기와 별개의 수명 주기를 가지므로 관련되지 않으므로 호스트 VM 또는 컴퓨터의 시작 및 중지 시기에 대한 이벤트를 포함하지 않습니다.
* **수명:** 작업자 역할 인스턴스를 재활용 하는 경우는 `Run` 메서드 종료 됩니다. 하지만 서비스 패브릭 서비스의 `RunAsync` 메서드는 완료될 때까지 실행할 수 있고 서비스 인스턴스는 제공됩니다. 

서비스 패브릭은 클라이언트 요청을 수신 대기하는 서비스에 대한 선택적 통신 설정 진입점을 제공합니다. RunAsync와 통신 진입점은 서비스 패브릭 서비스에서 선택적 재정의입니다. 서비스는 클라이언트 요청을 수신 대기하거나 처리 루프를 실행하거나 둘 다 수행하도록 선택할 수 있습니다. 클라이언트 요청을 계속해서 수신 대기할 수 있기 때문에 RunAsync 메서드가 서비스 인스턴스를 다시 시작하지 않고 종료하도록 허용되는 것은 이 때문입니다.

## <a name="application-api-and-environment"></a>애플리케이션 API 및 환경
Cloud Services 환경 API는 현재 VM 인스턴스에 대한 정보 및 기능 뿐만 아니라 다른 VM 역할 인스턴스에 대한 정보를 제공합니다. 서비스 패브릭은 해당 런타임과 관련된 정보 및 서비스가 현재 실행 중인 노드에 대한 일부 정보를 제공합니다. 

| **환경 작업** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| 구성 설정 및 변경 알림 |`RoleEnvironment` |`CodePackageActivationContext` |
| 로컬 저장소 |`RoleEnvironment` |`CodePackageActivationContext` |
| 엔드포인트 정보 |`RoleInstance` <ul><li>현재 인스턴스: `RoleEnvironment.CurrentRoleInstance`</li><li>다른 역할 및 인스턴스: `RoleEnvironment.Roles`</li> |<ul><li>현재 노드 주소의 경우 `NodeContext`</li><li>서비스 엔드포인트 검색의 경우 `FabricClient` 및 `ServicePartitionResolver`</li> |
| 환경 에뮬레이션 |`RoleEnvironment.IsEmulated` |N/A |
| 동시 변경 이벤트 |`RoleEnvironment` |N/A |

## <a name="configuration-settings"></a>구성 설정
Cloud Services의 구성 설정은 VM 역할에 대해 설정되고 해당 VM 역할의 모든 인스턴스에 적용합니다. 이러한 설정은 ServiceConfiguration.*.cscfg 파일에서 설정된 키-값 쌍이며 RoleEnvironment를 통해 직접 액세스할 수 있습니다. Service Fabric에서 VM은 여러 서비스 및 애플리케이션을 호스팅할 수 있으므로 설정은 VM이 아닌 각 서비스 및 각 애플리케이션에 개별적으로 적용됩니다. 서비스는 세 가지 패키지로 구성됩니다.

* **코드:** 서비스를 실행하는 데 필요한 서비스 실행 파일, 이진 파일, DLL 및 다른 모든 파일을 포함합니다.
* **Config:** 서비스에 대한 모든 구성 파일 및 설정.
* **데이터:** 서비스와 관련된 정적 데이터 파일.

이러한 각 패키지는 서비스를 독립적으로 버전 지정 및 업그레이드할 수 있습니다. Cloud Services와 마찬가지로 config 패키지는 API를 통해 프로그래밍 방식으로 액세스할 수 있으며 이벤트는 config 패키지 변경의 서비스를 알리기 위해 사용할 수 있습니다. App.config 파일의 앱 설정 섹션과 유사한 키-값 구성 및 프로그래밍 방식 액세스에 Settings.xml 파일을 사용할 수 있습니다. 그러나 Cloud Services와는 달리 서비스 패브릭 config 패키지는 XML, JSON, YAML 또는 사용자 지정 이진 형식이든 모든 형식의 구성 파일을 포함할 수 있습니다. 

### <a name="accessing-configuration"></a>구성 액세스
#### <a name="cloud-services"></a>Cloud Services
ServiceConfiguration.*.cscfg의 구성 설정은 `RoleEnvironment`을(를) 통해 액세스할 수 있습니다. 이러한 설정은 동일한 클라우드 서비스 배포 내에서 모든 역할 인스턴스에 전역적으로 제공됩니다.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
각 서비스에는 자체 개별 구성 패키지가 있습니다. 클러스터의 모든 애플리케이션에서 액세스할 수 있는 전역 구성 설정에 대한 기본 제공 메커니즘이 없습니다. 구성 패키지 내에서 Service Fabric의 특별한 Settings.xml 구성 파일을 사용할 때 가능한 애플리케이션 수준에서 애플리케이션 수준 구성 설정을 가능하도록 하는 Settings.xml의 값을 덮어쓸 수 있습니다.

구성 설정은 서비스의 `CodePackageActivationContext`을(를) 통한 각 서비스 인스턴스 내의 액세스입니다.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>구성 업데이트 이벤트
#### <a name="cloud-services"></a>Cloud Services
`RoleEnvironment.Changed` 이벤트는 환경에서 구성 변경과 같은 변경이 발생할 때 모든 역할 인스턴스를 알리는 데 사용됩니다. 역할 인스턴스를 재활용하거나 작업자 프로세스를 다시 시작하지 않고 구성 업데이트를 사용하는 데 사용됩니다.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
서비스의 각 세 가지 패키지 유형(코드, 구성 및 데이터)은 패키지를 업데이트, 추가 또는 제거하는 경우 서비스 인스턴스를 알리는 이벤트를 가집니다. 서비스는 각 유형의 여러 패키지를 포함할 수 있습니다. 예를 들어 서비스에는 각각 개별적으로 버전이 지정되고 업그레이드 가능한 여러 config 패키지가 있을 수 있습니다. 

이러한 이벤트는 서비스 인스턴스를 다시 시작하지 않고 서비스 패키지의 변경 내용을 사용하는 데 사용할 수 있습니다.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>시작 작업
시작 작업은 애플리케이션이 시작되기 전에 수행되는 작업입니다. 시작 작업은 상승된 권한을 사용하여 설치 스크립트를 실행하는 데 일반적으로 사용됩니다. Cloud Services와 서비스 패브릭은 시작 작업을 지원합니다. 주요 차이점은 Cloud Services에서 시작 작업은 역할 인스턴스의 일부이므로 VM에 연결되는 반면 서비스 패브릭에서 시작 작업은 특정 VM에 연결되지 않은 서비스에 연결된다는 점입니다.

| Service Fabric | Cloud Services |
| --- | --- |
| 구성 위치 |ServiceDefinition.csdef |
| 권한 |"제한된" 또는 "상승된" |
| 시퀀싱 |"간단", "백그라운드", "포그라운드" |

### <a name="cloud-services"></a>Cloud Services
Cloud Services에서 시작 진입점은 ServiceDefinition.csdef에서 역할별로 구성됩니다. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
서비스 패브릭에서 시작 진입점은 ServiceManifest.xml에서 서비스별로 구성됩니다.

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>개발 환경에 대한 정보
Cloud Services와 서비스 패브릭은 프로젝트 템플릿 및 로컬과 Azure 모두로 디버깅, 구성 및 배포에 대한 지원을 사용하여 Visual Studio와 통합됩니다. Cloud Services와 서비스 패브릭은 로컬 개발 런타임 환경도 제공합니다. 차이점은 클라우드 서비스 개발 런타임은 실행하는 Azure 환경을 에뮬레이션하는 반면 서비스 패브릭은 에뮬레이터를 사용하지 않고 전체 서비스 패브릭 런타임을 사용한다는 점입니다. 로컬 개발 컴퓨터에서 실행하는 서비스 패브릭 환경은 프로덕션 환경에서 실행하는 동일한 환경입니다.

## <a name="next-steps"></a>다음 단계
Service Fabric 기능의 전체 집합을 활용하는 방법을 이해하려면 Service Fabric 신뢰할 수 있는 서비스 및 Cloud Services와 Service Fabric 애플리케이션 아키텍처 간의 기본적인 차이점에 대해 자세히 알아보세요.

* [서비스 패브릭 신뢰할 수 있는 서비스 시작](service-fabric-reliable-services-quick-start.md)
* [Cloud Services와 서비스 패브릭 간의 차이점에 대한 개념 가이드](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
