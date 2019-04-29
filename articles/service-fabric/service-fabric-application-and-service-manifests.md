---
title: Azure Service Fabric 앱 및 서비스 설명 | Microsoft Docs
description: Service Fabric 애플리케이션 및 서비스를 설명하는 데 매니페스트를 사용하는 방법에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/19/2018
ms.author: atsenthi
ms.openlocfilehash: 5e93bb3b206fbef6beb09b7aca6df0742a80ccf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621516"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric 애플리케이션 및 서비스 매니페스트
이 문서에서는 Service Fabric 애플리케이션 및 서비스가 ApplicationManifest.xml 및 ServiceManifest.xml 파일을 사용하여 정의되고 버전화되는 방법에 대해 설명합니다.  자세한 예제는 [응용 프로그램 및 서비스 매니페스트 예제](service-fabric-manifest-examples.md)를 참조하세요.  이러한 매니페스트 파일의 XML 스키마는 [ServiceFabricServiceModel.xsd 스키마 설명서](service-fabric-service-model-schema.md)에 설명되어 있습니다.

> [!WARNING]
> 매니페스트 XML 파일 스키마는 자식 요소의 올바른 순서를 적용합니다.  부분적인 해결 방법으로는 Service Fabric 매니페스트를 작성하거나 수정하는 동안 Visual Studio에서 “C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd”를 엽니다. 이렇게 하면 자식 요소의 순서를 확인하고 IntelliSense를 제공할 수 있습니다.

## <a name="describe-a-service-in-servicemanifestxml"></a>ServiceManifest.xml의 서비스 설명
서비스 매니페스트는 서비스 유형 및 버전을 선언적으로 정의합니다. 서비스 유형, 상태 속성, 부하 분산 메트릭, 서비스 바이너리, 구성 파일 등의 서비스 메타데이터를 지정합니다.  다시 말해서 하나 이상의 서비스 유형을 지원하는 서비스 패키지를 구성하는 코드, 구성 및 데이터 패키지를 설명합니다. 서비스 매니페스트에는 독립적으로 버전화할 수 있는 여러 코드, 구성 및 데이터 패키지가 포함될 수 있습니다. 다음은 [투표 응용 프로그램 예제](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)의 ASP.NET Core 웹 프런트 엔드 서비스에 대한 서비스 매니페스트입니다(여기에 몇 가지 [자세한 예제](service-fabric-manifest-examples.md)도 나와 있음).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**버전** 특성은 구조화되지 않은 문자열이며 시스템에서 구문 분석하지 않습니다. 버전 특성은 업그레이드에 대한 각 구성 요소의 버전을 지정하는 데 사용됩니다.

**ServiceTypes**는 이 매니페스트의 **CodePackages**에서 지원하는 서비스 유형을 선언합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 결과 프로세스는 런타임에 지원되는 서비스 유형을 등록합니다. 서비스 유형은 코드 패키지 수준이 아니라 매니페스트 수준에서 선언됩니다. 따라서 코드 패키지가 여러 개인 경우 시스템에서 선언된 서비스 유형 중 어떤 하나를 조회하면 모든 코드 패키지가 활성화됩니다.

**EntryPoint** 를 통해 지정되는 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. **SetupEntryPoint** 는 서비스 패브릭과 같은 자격 증명(일반적으로 *LocalSystem* 계정)을 사용하여 다른 진입점보다 먼저 실행되는 권한 있는 진입점입니다.  별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. **EntryPoint**를 통해 지정된 실행 파일은 **SetupEntryPoint**가 성공적으로 종료된 후 실행됩니다. 프로세스가 종료되지 않거나 충돌하는 경우 결과 프로세스를 모니터링하여 다시 시작합니다( **SetupEntryPoint**를 사용하여 다시 시작).  

**SetupEntryPoint** 를 사용하는 일반적인 시나리오는 서비스를 시작하기 전에 실행 파일을 실행하는 경우 또는 높은 권한을 사용하여 작업을 수행하는 경우입니다. 예를 들면 다음과 같습니다.

* 서비스 실행 파일에 필요한 환경 변수를 설정하고 초기화합니다. 이것은 서비스 패브릭 프로그래밍 모델을 통해 작성된 실행 파일에만 국한되지는 않습니다. 예를 들어 npm.exe 파일에는 node.js 애플리케이션 배포를 위해 구성되는 환경 변수가 필요합니다.
* 보안 인증서를 설치하여 액세스 제어를 설정합니다.

SetupEntryPoint를 구성하는 방법에 대한 자세한 내용은 [서비스 설치 진입점에 대한 정책 구성](service-fabric-application-runas-security.md)을 참조하세요.

**EnvironmentVariables**(이전 예제에서 설정되지 않음)는 이 코드 패키지에 대해 설정된 환경 변수 목록을 제공합니다. `ApplicationManifest.xml`에서 환경 변수를 재정의하여 다른 서비스 인스턴스의 다른 값을 제공할 수 있습니다. 

이전 예제에서 설정되지 않은 **DataPackage**는 **Name** 특성을 통해 이름이 지정되고 런타임에 프로세스에서 사용할 임의의 정적 데이터를 포함하는 폴더를 선언합니다.

**ConfigPackage**는 **Name** 특성을 통해 이름이 지정되고 *Settings.xml* 파일을 포함하는 폴더를 선언합니다. 설정 파일은 런타임에 프로세스에서 다시 읽을 수 있는 사용자 정의 키-값 쌍 설정의 섹션을 포함합니다. 업그레이드하는 동안 **ConfigPackage** **버전**만 변경되면 실행 중인 프로세스가 다시 시작되지 않습니다. 대신, 콜백에서는 구성 설정이 변경되어 동적으로 다시 로드할 수 있음을 프로세스에 알립니다. 다음은 *Settings.xml* 파일의 예입니다.

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Service Fabric 서비스 **엔드포인트**는 Service Fabric 리소스의 예입니다. 컴파일된 코드를 변경하지 않고 Service Fabric 리소스를 선언/변경할 수 있습니다. 서비스 매니페스트에 지정된 Service Fabric 리소스에 대한 액세스는 애플리케이션 매니페스트의 **SecurityGroup**을 통해 제어할 수 있습니다. 서비스 매니페스트에 엔드포인트 리소스가 정의되면 Service Fabric에서는 포트가 명시적으로 지정되지 않을 경우 예약된 애플리케이션 포트 범위에 포함되는 포트를 할당합니다. [엔드포인트 리소스 지정 또는 재정의](service-fabric-service-manifest-resources.md)에 대해 자세히 알아보세요.


<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>ApplicationManifest.xml의 애플리케이션 설명
애플리케이션 매니페스트는 애플리케이션 유형 및 버전을 선언적으로 설명합니다. 안정적인 이름, 파티션 구성표, 인스턴스 수/복제 요소, 보안/격리 정책, 배치 제약 조건, 구성 재정의, 구성 서비스 유형 등의 서비스 구성 메타데이터를 지정합니다. 또한 애플리케이션이 배치되는 부하 분산 도메인을 설명합니다.

따라서 애플리케이션 매니페스트는 애플리케이션 수준에서 요소를 설명하고 애플리케이션 유형을 구성하는 하나 이상의 서비스 매니페스트를 참조합니다. 다음은 [투표 응용 프로그램 예제](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)에 대한 응용 프로그램 매니페스트입니다(여기에 몇 가지 [자세한 예제](service-fabric-manifest-examples.md)도 나와 있음).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

서비스 매니페스트와 마찬가지로 **Version** 특성은 구조화되지 않은 문자열이며 시스템에서 구문을 분석하지 않습니다. 또한 업그레이드에 대한 각 구성 요소의 버전을 지정하는 데 사용됩니다.

**매개 변수**는 애플리케이션 매니페스트에서 사용되는 매개 변수를 정의합니다. 응용 프로그램이 인스턴스화되고 응용 프로그램 또는 서비스 구성 설정을 재정의할 수 있을 때 이러한 매개 변수의 값을 제공할 수 있습니다.  애플리케이션 인스턴스화 중에 값이 변경되지 않은 경우 기본 매개 변수 값이 사용됩니다. 개별 환경에 대해 서로 다른 애플리케이션 및 서비스 매개 변수를 유지 관리하는 방법을 알아보려면 [여러 환경에 대한 애플리케이션 매개 변수 관리](service-fabric-manage-multiple-environment-app-configuration.md)를 참조하세요.

**ServiceManifestImport** 는 이 애플리케이션 유형을 구성하는 서비스 매니페스트에 대한 참조를 포함합니다. 애플리케이션 매니페스트에는 여러 개의 서비스 매니페스트 가져오기가 포함될 수 있으며, 각 항목은 독립적으로 버전화될 수 있습니다. 가져온 서비스 매니페스트는 이 애플리케이션 유형 내에서 유효한 있는 서비스 유형을 결정합니다. ServiceManifestImport 내에서 Settings.xml의 구성 값과 ServiceManifest.xml 파일의 환경 변수를 재정의할 수 있습니다. 끝점 바인딩, 보안 및 액세스, 패키지 공유에 대한 **정책**(이전 예제에서 설정되지 않음)은 가져온 서비스 매니페스트에 설정할 수 있습니다.  자세한 내용은 [애플리케이션에 대한 보안 정책 구성](service-fabric-application-runas-security.md)을 참조하세요.

**DefaultServices** 는 이 애플리케이션 유형에 대해 애플리케이션이 인스턴스화할 때마다 자동으로 생성되는 서비스 인스턴스를 선언합니다. 기본 서비스는 편리하기는 하지만 생성된 후 모든 면에서 일반 서비스처럼 동작합니다. 애플리케이션 인스턴스의 다른 서비스와 함께 업그레이드되며 제거할 수도 있습니다. 애플리케이션 매니페스트에는 여러 기본 서비스가 포함될 수 있습니다.

**인증서**(이전 예제에서 설정되지 않음)는 [HTTPS 엔드포인트 설정](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) 또는 [애플리케이션 매니페스트의 비밀 암호화](service-fabric-application-secret-management.md)에 사용되는 인증서를 선언합니다.

**정책**(이전 예제에서 설정되지 않음)은 애플리케이션 레벨에서 설정되는 로그 컬렉션, [기본 실행](service-fabric-application-runas-security.md), [상태](service-fabric-health-introduction.md#health-policies) 및 [보안 액세스](service-fabric-application-runas-security.md) 정책을 설명합니다.

**보안 주체**(이전 예제에서 설정되지 않음)는 [서비스 및 보안 서비스 리소스 실행](service-fabric-application-runas-security.md)에 필요한 보안 주체(사용자 또는 그룹)를 설명합니다.  보안 주체는 **정책** 섹션에서 참조됩니다.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>다음 단계
- [애플리케이션을 패키지](service-fabric-package-apps.md)하고 배포를 준비합니다.
- [애플리케이션을 배포하고 제거](service-fabric-deploy-remove-applications.md)합니다.
- [여러 애플리케이션 인스턴스에 대해 매개 변수 및 환경 변수를 구성](service-fabric-manage-multiple-environment-app-configuration.md)합니다.
- [애플리케이션에 대한 보안 정책을 구성](service-fabric-application-runas-security.md)합니다.
- [HTTPS 엔드포인트를 설정](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)합니다.
- [애플리케이션 매니페스트에서 비밀을 암호화](service-fabric-application-secret-management.md)합니다.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png



