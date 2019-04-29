---
title: Azure Service Fabric Reliable Services 애플리케이션 매니페스트 예제 | Microsoft Docs
description: Reliable Services Service Fabric 응용 프로그램에 대한 응용 프로그램 및 서비스 매니페스트 설정을 구성하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6c4c8f0ee6aa12c58e02f71b42312cd6872076aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719167"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Reliable Services 애플리케이션 및 서비스 매니페스트 예제
다음은 ASP.NET Core 웹 프런트 엔드 및 상태 저장 백 엔드를 사용하는 Service Fabric 응용 프로그램용 응용 프로그램 및 서비스 매니페스트 예제입니다. 이러한 예제의 목적은 사용 가능한 설정 및 사용 방법을 보여주는 것입니다. 이러한 애플리케이션 및 서비스 매니페스트는 [Service Fabric .NET 빠른 시작](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) 매니페스트를 기반으로 합니다.

다음 기능이 표시됩니다.

|매니페스트|기능|
|---|---|
|[애플리케이션 매니페스트](#application-manifest)| [리소스 거버넌스](service-fabric-resource-governance.md), [로컬 관리자 계정으로 서비스 실행](service-fabric-application-runas-security.md), [모든 서비스 코드 패키지에 기본 정책 적용](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [사용자 및 그룹 보안 주체 만들기](service-fabric-application-runas-security.md), 서비스 인스턴스 간에 데이터 패키지 공유, [서비스 엔드포인트 재정의](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService 서비스 매니페스트| [서비스 시작 시 스크립트 실행](service-fabric-run-script-at-service-startup.md), [HTTPS 엔드포인트 정의](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService 서비스 매니페스트| [구성 패키지 선언](service-fabric-application-and-service-manifests.md), [데이터 패키지 선언](service-fabric-application-and-service-manifests.md), [엔드포인트 구성](service-fabric-service-manifest-resources.md)| 

특정 XML 요소에 대한 자세한 내용은 [응용 프로그램 매니페스트 요소](#application-manifest-elements), [VotingWeb 서비스 매니페스트 요소](#votingweb-service-manifest-elements) 및 [VotingData 서비스 매니페스트 요소](#votingdata-service-manifest-elements)를 참조하세요.

## <a name="application-manifest"></a>애플리케이션 매니페스트

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

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
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb 서비스 매니페스트

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
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData 서비스 매니페스트

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>응용 프로그램 매니페스트 요소
### <a name="applicationmanifest-element"></a>ApplicationManifest 요소
애플리케이션 유형 및 버전을 선언적으로 설명합니다. 구성 요소 서비스의 서비스 매니페스트 하나 이상에서 애플리케이션 유형을 작성하기 위해 참조됩니다. 구성 요소 서비스의 구성 설정은 매개 변수화된 애플리케이션 설정을 사용하여 재정의할 수 있습니다. 기본 서비스, 서비스 템플릿, 보안 주체, 정책, 진단 설정 및 인증서도 애플리케이션 수준에서 선언할 수 있습니다. 자세한 내용은 [ApplicationManifest 요소](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)를 참조하세요.

### <a name="parameters-element"></a>Parameters 요소
이 애플리케이션 매니페스트에 사용되는 매개 변수를 선언합니다. 애플리케이션이 인스턴스화되고 애플리케이션 또는 서비스 구성 설정을 재정의하는 데 사용될 수 있을 때 이러한 매개 변수의 값을 제공할 수 있습니다. 자세한 내용은 [Parameters 요소](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)를 참조하세요.

### <a name="parameter-element"></a>Parameter 요소
이 매니페스트에 사용할 애플리케이션 매개 변수입니다. 매개 변수 값은 애플리케이션 인스턴스하는 동안 변경될 수 있거나, 값이 제공되지 않으면 기본값이 사용됩니다. 자세한 내용은 [Parameter 요소](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)를 참조하세요.

### <a name="servicemanifestimport-element"></a>ServiceManifestImport 요소
서비스 개발자가 만든 서비스 매니페스트를 가져옵니다. 애플리케이션의 각 구성 요소 서비스에 대한 서비스 매니페스트를 가져와야 합니다. 서비스 매니페스트에 대한 구성 재정의 및 정책을 선언할 수 있습니다. 자세한 내용은 [ServiceManifestImport 요소](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)를 참조하세요.

### <a name="servicemanifestref-element"></a>ServiceManifestRef 요소
참조별로 서비스 매니페스트를 가져옵니다. 현재 이 빌드 패키지에 서비스 매니페스트 파일(ServiceManifest.xml)이 있어야 합니다. 자세한 내용은 [ServiceManifestRef 요소](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)를 참조하세요.

### <a name="resourceoverrides-element"></a>ResourceOverrides 요소
서비스 매니페스트 리소스에서 선언된 엔드포인트의 리소스 재정의를 지정합니다. 자세한 내용은 [ResourceOverrides 요소](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)를 참조하세요.

### <a name="endpoints-element"></a>Endpoints 요소
재정의할 엔드포인트입니다. 자세한 내용은 [Endpoints 요소](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)를 참조하세요.

### <a name="endpoint-element"></a>Endpoint 요소
재정의할 엔드포인트로, 서비스 매니페스트에서 선언되었습니다. 자세한 내용은 [Endpoint 요소](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)를 참조하세요.

### <a name="policies-element"></a>Policies 요소
가져온 서비스 매니페스트에 적용할 정책(끝점 바인딩, 패키지 공유, 실행 및 보안 액세스)을 설명합니다. 자세한 내용은 [Policies 요소](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)를 참조하세요.

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy 요소
전체 서비스 패키지 수준에서 적용되는 리소스 관리 정책을 정의합니다. 자세한 내용은 [ServicePackageResourceGovernancePolicy 요소](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)를 참조하세요.

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy 요소
코드 패키지의 리소스 제한을 지정합니다. 자세한 내용은 [ResourceGovernancePolicy 요소](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)를 참조하세요.

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy 요소
코드, 구성 또는 데이터 패키지를 동일한 서비스 유형의 서비스 인스턴스 간에 공유해야 하는지 여부를 나타냅니다. 자세한 내용은 [PackageSharingPolicy 요소](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)를 참조하세요.

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy 요소
서비스 매니페스트에 정의된 리소스(예: 엔드포인트)의 보안 주체에 대한 액세스 권한을 부여합니다. 일반적으로 보안 위험을 최소화하기 위해 여러 리소스에 대한 서비스 액세스를 제어하고 제한하는 것이 매우 유용합니다. 이는 다른 개발자가 개발한 마켓플레이스의 서비스 컬렉션에서 애플리케이션을 빌드하는 경우 특히 중요합니다. 자세한 내용은 [SecurityAccessPolicy 요소](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)를 참조하세요.

### <a name="runaspolicy-element"></a>RunAsPolicy 요소
서비스 코드 패키지가 실행될 로컬 사용자 또는 로컬 시스템 계정을 지정합니다. 도메인 계정은 Azure Active Directory를 사용할 수 있는 Windows Server 배포에서 지원됩니다. 기본적으로 애플리케이션은 Fabric.exe 프로세스가 실행되는 계정으로 실행됩니다. 애플리케이션은 다른 계정으로도 실행될 수 있으며, 이 계정은 Principals 섹션에서 선언해야 합니다. 서비스에 RunAs 정책을 적용하고 서비스 매니페스트에서 HTTP 프로토콜을 사용하여 엔드포인트 리소스를 선언하는 경우, 이러한 엔드포인트에 할당된 포트에서 서비스가 실행되는 RunAs 사용자 계정에 대해 나열된 액세스 제어를 올바르게 유지하도록 SecurityAccessPolicy를 지정해야 합니다. 또한 HTTPS 엔드포인트의 경우 클라이언트에 반환할 인증서 이름을 나타내도록 EndpointBindingPolicy를 정의해야 합니다. 자세한 내용은 [RunAsPolicy 요소](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)를 참조하세요.

### <a name="defaultservices-element"></a>DefaultServices 요소
이 애플리케이션 유형에 대해 애플리케이션이 인스턴스화될 때마다 자동으로 만드는 서비스 인스턴스를 선언합니다. 자세한 내용은 [DefaultServices 요소](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)를 참조하세요.

### <a name="service-element"></a>Service 요소
애플리케이션이 인스턴스화될 때 자동으로 만드는 서비스를 선언합니다. 자세한 내용은 [Service 요소](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)를 참조하세요.

### <a name="statefulservice-element"></a>StatefulService 요소
상태 저장 서비스를 정의합니다. 자세한 내용은 [StatefulService 요소](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)를 참조하세요.

### <a name="statelessservice-element"></a>StatelessService 요소
상태 비저장 서비스를 정의합니다. 자세한 내용은 [StatelessService 요소](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)를 참조하세요.

### <a name="principals-element"></a>Principals 요소
이 애플리케이션에서 서비스를 실행하고 리소스를 보호하는 데 필요한 보안 주체(사용자, 그룹)를 설명합니다. 보안 주체는 Policies 섹션에서 참조됩니다. 자세한 내용은 [Principals 요소](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)를 참조하세요.

### <a name="groups-element"></a>Groups 요소
정책에서 참조할 수 있는 그룹 집합을 보안 주체로 선언합니다. 그룹은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우에 유용합니다. 자세한 내용은 [Groups 요소](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)를 참조하세요.

### <a name="group-element"></a>Group 요소
정책에서 참조할 수 있는 그룹을 보안 주체로 선언합니다. 자세한 내용은 [Group 요소](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)를 참조하세요.

### <a name="membership-element"></a>Membership 요소
 자세한 내용은 [Membership 요소](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)를 참조하세요.

### <a name="systemgroup-element"></a>SystemGroup 요소
 자세한 내용은 [SystemGroup 요소](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)를 참조하세요.

### <a name="users-element"></a>Users 요소
정책에서 참조할 수 있는 사용자 집합을 보안 주체로 선언합니다. 자세한 내용은 [Users 요소](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)를 참조하세요.

### <a name="user-element"></a>User 요소
정책에서 참조할 수 있는 사용자를 보안 주체로 선언합니다. 자세한 내용은 [User 요소](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)를 참조하세요.

### <a name="memberof-element"></a>MemberOf 요소
사용자를 기본 멤버 자격 그룹에 추가할 수 있으며, 따라서 해당 멤버 그룹의 모든 속성 및 보안 설정을 상속할 수 있습니다. 멤버 그룹은 다른 서비스 또는 (다른 컴퓨터의) 동일한 서비스에서 액세스해야 하는 외부 리소스를 보호하는 데 사용할 수 있습니다. 자세한 내용은 [MemberOf 요소](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)를 참조하세요.

### <a name="systemgroup-element"></a>SystemGroup 요소
사용자를 추가할 시스템 그룹입니다.  시스템 그룹은 Groups 섹션에서 정의해야 합니다. 자세한 내용은 [SystemGroup 요소](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)를 참조하세요.

### <a name="group-element"></a>Group 요소
사용자를 추가할 그룹입니다.  그룹은 Groups 섹션에서 정의해야 합니다. 자세한 내용은 [Group 요소](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)를 참조하세요.

### <a name="policies-element"></a>Policies 요소
애플리케이션 수준에서 적용할 정책(로그 수집, 기본 실행, 상태 및 보안 액세스)을 설명합니다. 자세한 내용은 [Policies 요소](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)를 참조하세요.

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy 요소
ServiceManifestImport 섹션에 정의된 특정 RunAsPolicy가 없는 모든 서비스 코드 패키지에 대한 기본 사용자 계정을 지정합니다. 자세한 내용은 [DefaultRunAsPolicy 요소](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)를 참조하세요.




## <a name="votingweb-service-manifest-elements"></a>VotingWeb 서비스 매니페스트 요소
### <a name="servicemanifest-element"></a>ServiceManifest 요소
서비스 유형 및 버전을 선언적으로 설명합니다. 하나 이상의 서비스 유형을 지원하도록 서비스 패키지를 함께 구성하는 독립적으로 업그레이드 가능한 코드, 구성 및 데이터 패키지를 나열합니다. 리소스, 진단 설정 및 서비스 메타데이터(예: 서비스 유형, 상태 속성 및 부하 분산 메트릭)도 지정합니다. 자세한 내용은 [ServiceManifest 요소](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)를 참조하세요.

### <a name="servicetypes-element"></a>ServiceTypes 요소
이 매니페스트의 CodePackage에서 지원하는 서비스 유형을 정의합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 서비스 유형은 코드 패키지 수준이 아니라 매니페스트 수준에서 선언됩니다. 자세한 내용은 [ServiceTypes 요소](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="statelessservicetype-element"></a>StatelessServiceType 요소
상태 비저장 서비스 유형을 설명합니다. 자세한 내용은 [StatelessServiceType 요소](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)를 참조하세요.

### <a name="codepackage-element"></a>CodePackage 요소
정의된 서비스 유형을 지원하는 코드 패키지를 설명합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 결과 프로세스는 런타임에 지원되는 서비스 유형을 등록합니다. 여러 코드 패키지가 있는 경우 시스템에서 선언된 서비스 유형 중 하나를 찾을 때마다 모두 활성화됩니다. 자세한 내용은 [CodePackage 요소](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)를 참조하세요.

### <a name="setupentrypoint-element"></a>SetupEntryPoint 요소
기본적으로 Service Fabric(일반적으로 NETWORKSERVICE 계정)과 동일한 자격 증명을 사용하여 다른 진입점보다 먼저 실행되는 권한 있는 진입점입니다. EntryPoint에서 지정한 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. 자세한 내용은 [SetupEntryPoint 요소](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="exehost-element"></a>ExeHost 요소
 자세한 내용은 [ExeHost 요소](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)를 참조하세요.

### <a name="program-element"></a>Program 요소
실행 파일 이름입니다.  예를 들어 "MySetup.bat" 또는 "MyServiceHost.exe"입니다. 자세한 내용은 [Program 요소](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)를 참조하세요.

### <a name="arguments-element"></a>Arguments 요소
 자세한 내용은 [Arguments 요소](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)를 참조하세요.

### <a name="workingfolder-element"></a>WorkingFolder 요소
애플리케이션이 배포된 클러스터 노드에서 코드 패키지의 프로세스에 대한 작업 디렉터리입니다. 세 가지 값을 지정할 수 있습니다. 작업(기본값), CodePackage 또는 CodeBase CodeBase를 사용하면 작업 디렉터리는 코드 패키지에서 EXE가 정의되는 디렉터리로 설정됩니다. CodePackage를 사용하면 작업 디렉터리는 코드 패키지 디렉터리에서 EXE가 있는 위치에 관계 없이 코드 패키지의 루트로 설정됩니다. 작업을 사용하면 작업 디렉터리는 노드에 만들어진 고유 폴더로 설정됩니다.  이 폴더는 전체 애플리케이션 인스턴스에서 동일합니다. 기본적으로 애플리케이션에서 모든 프로세스의 작업 디렉터리가 애플리케이션 작업 폴더에 설정됩니다. 프로세스가 이 폴더에 데이터를 쓸 수 있습니다. 이러한 폴더는 여러 애플리케이션 인스턴스 간에 공유할 수 있고 삭제가 가능하므로 코드 패키지 또는 코드 베이스에 데이터를 쓰지 않는 것이 좋습니다. 자세한 내용은 [WorkingFolder 요소](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)를 참조하세요.

### <a name="consoleredirection-element"></a>ConsoleRedirection 요소

> [!WARNING]
> 프로덕션 애플리케이션에서 콘솔 리디렉션을 사용하지 마세요. 로컬 개발 및 디버깅에만 사용하세요. 시작 스크립트의 콘솔 출력을 애플리케이션이 배포되고 실행되는 클러스터 노드의 "log"라는 애플리케이션 폴더에 있는 출력 파일로 리디렉션합니다. 자세한 내용은 [ConsoleRedirection 요소](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)를 참조하세요.

### <a name="entrypoint-element"></a>EntryPoint 요소
EntryPoint에서 지정한 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. SetupEntryPoint가 성공적으로 종료된 후에 EntryPoint에서 지정한 실행 파일이 실행됩니다. 결과 프로세스를 모니터링하고, 종료되거나 충돌하는 경우 다시 시작합니다(SetupEntryPoint를 사용하여 다시 시작). 자세한 내용은 [EntryPoint 요소](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="exehost-element"></a>ExeHost 요소
 자세한 내용은 [ExeHost 요소](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)를 참조하세요.

### <a name="configpackage-element"></a>ConfigPackage 요소
Settings.xml 파일을 포함하는 PackageRoot 아래에 Name 특성을 통해 이름이 지정되는 폴더를 선언합니다. 이 파일은 런타임에 프로세스에서 다시 읽을 수 있는 사용자 정의 키-값 쌍 설정의 섹션을 포함합니다. 업그레이드하는 동안 ConfigPackage 버전만 변경되면 실행 중인 프로세스가 다시 시작되지 않습니다. 대신, 콜백에서는 구성 설정이 변경되어 동적으로 다시 로드할 수 있음을 프로세스에 알립니다. 자세한 내용은 [ConfigPackage 요소](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)를 참조하세요.

### <a name="resources-element"></a>Resources 요소
이 서비스에서 사용하는 리소스를 설명합니다. 이 리소스는 컴파일된 코드를 수정하지 않고 선언할 수 있으며, 서비스를 배포할 때 변경할 수 있습니다. 이러한 리소스에 대한 액세스는 애플리케이션 매니페스트의 Principals 및 Policies 섹션을 통해 제어됩니다. 자세한 내용은 [Resources 요소](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="endpoints-element"></a>Endpoints 요소
서비스에 대한 엔드포인트를 정의합니다. 자세한 내용은 [Endpoints 요소](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)를 참조하세요.

### <a name="endpoint-element"></a>Endpoint 요소
재정의할 엔드포인트로, 서비스 매니페스트에서 선언되었습니다. 자세한 내용은 [Endpoint 요소](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)를 참조하세요.



## <a name="votingdata-service-manifest-elements"></a>VotingData 서비스 매니페스트 요소
### <a name="servicemanifest-element"></a>ServiceManifest 요소
서비스 유형 및 버전을 선언적으로 설명합니다. 하나 이상의 서비스 유형을 지원하도록 서비스 패키지를 함께 구성하는 독립적으로 업그레이드 가능한 코드, 구성 및 데이터 패키지를 나열합니다. 리소스, 진단 설정 및 서비스 메타데이터(예: 서비스 유형, 상태 속성 및 부하 분산 메트릭)도 지정합니다. 자세한 내용은 [ServiceManifest 요소](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)를 참조하세요.

### <a name="servicetypes-element"></a>ServiceTypes 요소
이 매니페스트의 CodePackage에서 지원하는 서비스 유형을 정의합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 서비스 유형은 코드 패키지 수준이 아니라 매니페스트 수준에서 선언됩니다. 자세한 내용은 [ServiceTypes 요소](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="statefulservicetype-element"></a>StatefulServiceType 요소
상태 저장 서비스 유형을 설명합니다. 자세한 내용은 [StatefulServiceType 요소](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)를 참조하세요.

### <a name="codepackage-element"></a>CodePackage 요소
정의된 서비스 유형을 지원하는 코드 패키지를 설명합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 결과 프로세스는 런타임에 지원되는 서비스 유형을 등록합니다. 여러 코드 패키지가 있는 경우 시스템에서 선언된 서비스 유형 중 하나를 찾을 때마다 모두 활성화됩니다. 자세한 내용은 [CodePackage 요소](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)를 참조하세요.

### <a name="entrypoint-element"></a>EntryPoint 요소
EntryPoint에서 지정한 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. SetupEntryPoint가 성공적으로 종료된 후에 EntryPoint에서 지정한 실행 파일이 실행됩니다. 결과 프로세스를 모니터링하고, 종료되거나 충돌하는 경우 다시 시작합니다(SetupEntryPoint를 사용하여 다시 시작). 자세한 내용은 [EntryPoint 요소](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="exehost-element"></a>ExeHost 요소
 자세한 내용은 [ExeHost 요소](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)를 참조하세요.

### <a name="program-element"></a>Program 요소
실행 파일 이름입니다.  예를 들어 "MySetup.bat" 또는 "MyServiceHost.exe"입니다. 자세한 내용은 [Program 요소](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)를 참조하세요.

### <a name="workingfolder-element"></a>WorkingFolder 요소
애플리케이션이 배포된 클러스터 노드에서 코드 패키지의 프로세스에 대한 작업 디렉터리입니다. 세 가지 값을 지정할 수 있습니다. 작업(기본값), CodePackage 또는 CodeBase CodeBase를 사용하면 작업 디렉터리는 코드 패키지에서 EXE가 정의되는 디렉터리로 설정됩니다. CodePackage를 사용하면 작업 디렉터리는 코드 패키지 디렉터리에서 EXE가 있는 위치에 관계 없이 코드 패키지의 루트로 설정됩니다. 작업을 사용하면 작업 디렉터리는 노드에 만들어진 고유 폴더로 설정됩니다.  이 폴더는 전체 애플리케이션 인스턴스에서 동일합니다. 기본적으로 애플리케이션에서 모든 프로세스의 작업 디렉터리가 애플리케이션 작업 폴더에 설정됩니다. 프로세스가 이 폴더에 데이터를 쓸 수 있습니다. 이러한 폴더는 여러 애플리케이션 인스턴스 간에 공유할 수 있고 삭제가 가능하므로 코드 패키지 또는 코드 베이스에 데이터를 쓰지 않는 것이 좋습니다. 자세한 내용은 [WorkingFolder 요소](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)를 참조하세요.

### <a name="configpackage-element"></a>ConfigPackage 요소
Settings.xml 파일을 포함하는 PackageRoot 아래에 Name 특성을 통해 이름이 지정되는 폴더를 선언합니다. 이 파일은 런타임에 프로세스에서 다시 읽을 수 있는 사용자 정의 키-값 쌍 설정의 섹션을 포함합니다. 업그레이드하는 동안 ConfigPackage 버전만 변경되면 실행 중인 프로세스가 다시 시작되지 않습니다. 대신, 콜백에서는 구성 설정이 변경되어 동적으로 다시 로드할 수 있음을 프로세스에 알립니다. 자세한 내용은 [ConfigPackage 요소](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)를 참조하세요.

### <a name="datapackage-element"></a>DataPackage 요소
런타임에 프로세스에서 사용할 정적 데이터를 포함하는 PackageRoot 아래에 Name 특성을 통해 이름이 지정되는 폴더를 선언합니다. Service Fabric에서 서비스 매니페스트에 나열된 데이터 패키지를 업그레이드할 때 호스트 및 지원 패키지에 지정된 모든 EXE 및 DLLHOST를 재활용합니다. 자세한 내용은 [DataPackage 요소](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)를 참조하세요.

### <a name="resources-element"></a>Resources 요소
이 서비스에서 사용하는 리소스를 설명합니다. 이 리소스는 컴파일된 코드를 수정하지 않고 선언할 수 있으며, 서비스를 배포할 때 변경할 수 있습니다. 이러한 리소스에 대한 액세스는 애플리케이션 매니페스트의 Principals 및 Policies 섹션을 통해 제어됩니다. 자세한 내용은 [Resources 요소](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="endpoints-element"></a>Endpoints 요소
서비스에 대한 엔드포인트를 정의합니다. 자세한 내용은 [Endpoints 요소](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)를 참조하세요.

### <a name="endpoint-element"></a>Endpoint 요소
재정의할 엔드포인트로, 서비스 매니페스트에서 선언되었습니다. 자세한 내용은 [Endpoint 요소](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)를 참조하세요.

