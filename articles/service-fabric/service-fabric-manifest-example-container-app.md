---
title: Azure Service Fabric 컨테이너 애플리케이션 매니페스트 예제 | Microsoft Docs
description: 다중 컨테이너 Service Fabric 애플리케이션에 대한 애플리케이션 및 서비스 매니페스트 설정을 구성하는 방법을 알아봅니다.
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
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 622e6f7552d91cdb9ccf3668c302496c68a5920f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719246"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>다중 컨테이너 애플리케이션 및 서비스 매니페스트 예제
다음은 다중 컨테이너 Service Fabric 애플리케이션에 대한 애플리케이션 및 서비스 매니페스트의 예제입니다. 이러한 예제의 목적은 사용 가능한 설정 및 사용 방법을 보여주는 것입니다. 이러한 애플리케이션 및 서비스 매니페스트는 [Windows Server 2016 컨테이너 샘플](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) 매니페스트를 기반으로 합니다.

다음 기능이 표시됩니다.

|매니페스트|기능|
|---|---|
|[애플리케이션 매니페스트](#application-manifest)| [환경 변수 재정의](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [컨테이너 포트-호스트 간 매핑 구성](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [컨테이너 레지스트리 인증 구성](service-fabric-get-started-containers.md#configure-container-registry-authentication), [리소스 거버넌스](service-fabric-resource-governance.md), [격리 모드 설정](service-fabric-get-started-containers.md#configure-isolation-mode), [OS 빌드별 컨테이너 이미지 지정](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService 서비스 매니페스트](#frontendservice-service-manifest)| [환경 변수 설정](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [엔드포인트 구성](service-fabric-get-started-containers.md#configure-communication), 컨테이너에 명령 전달, [컨테이너로 인증서 가져오기](service-fabric-securing-containers.md)| 
|[BackEndService 서비스 매니페스트](#backendservice-service-manifest)|[환경 변수 설정](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [엔드포인트 구성](service-fabric-get-started-containers.md#configure-communication), [볼륨 드라이버 구성](service-fabric-containers-volume-logging-drivers.md)| 

특정 XML 요소에 대한 자세한 내용은 [애플리케이션 매니페스트 요소](#application-manifest-elements), [FrontEndService 서비스 매니페스트 요소](#frontendservice-service-manifest-elements) 및 [BackEndService 서비스 매니페스트 요소](#backendservice-service-manifest-elements)를 참조하세요.

## <a name="application-manifest"></a>애플리케이션 매니페스트

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService 서비스 매니페스트

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService 서비스 매니페스트

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="application-manifest-elements"></a>애플리케이션 매니페스트 요소
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

### <a name="policies-element"></a>Policies 요소
가져온 서비스 매니페스트에 적용할 정책(끝점 바인딩, 패키지 공유, 실행 및 보안 액세스)을 설명합니다. 자세한 내용은 [Policies 요소](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)를 참조하세요.

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy 요소
전체 서비스 패키지 수준에서 적용되는 리소스 관리 정책을 정의합니다. 자세한 내용은 [ServicePackageResourceGovernancePolicy 요소](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)를 참조하세요.

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy 요소
코드 패키지에 대한 리소스 제한을 지정합니다. 자세한 내용은 [ResourceGovernancePolicy 요소](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)를 참조하세요.

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies 요소
컨테이너 호스트를 사용하도록 설정하기 위한 정책을 지정합니다. 자세한 내용은 [ContainerHostPolicies 요소](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)를 참조하세요.

### <a name="repositorycredentials-element"></a>RepositoryCredentials 요소
이미지를 끌어올 컨테이너 이미지 리포지토리에 대한 자격 증명입니다. 자세한 내용은 [RepositoryCredentials 요소](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)를 참조하세요.

### <a name="portbinding-element"></a>PortBinding 요소
노출된 컨테이너 포트에 바인딩할 엔드포인트 리소스를 지정합니다. 자세한 내용은 [PortBinding 요소](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)를 참조하세요.

### <a name="volume-element"></a>Volume 요소
컨테이너에 바인딩할 볼륨을 지정합니다. 자세한 내용은 [Volume 요소](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)를 참조하세요.

### <a name="driveroption-element"></a>DriverOption 요소
드라이버에 전달할 드라이버 옵션입니다. 자세한 내용은 [DriverOption 요소](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)를 참조하세요.

### <a name="imageoverrides-element"></a>ImageOverrides 요소
Windows Server 컨테이너는 여러 OS 버전에서 호환되지 않을 수 있습니다.  컨테이너마다 여러 OS 이미지를 지정하고 OS 빌드 버전을 태그로 지정할 수 있습니다. Windows 명령 프롬프트에서 "winver"를 실행하여 OS 빌드 버전을 가져옵니다. 기본 OS가 빌드 버전 16299(Windows Server 버전 1709)이면 Service Fabric은 Os="16299"라는 태그가 지정된 컨테이너 이미지를 선택합니다. 태그가 지정되지 않은 컨테이너 이미지는 모든 OS 버전에서 작동하는 것으로 간주되어 서비스 매니페스트에 지정된 이미지를 재정의합니다. 자세한 내용은 [ImageOverrides 요소](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)를 참조하세요.

### <a name="image-element"></a>Image 요소
실행할 OS 빌드 버전 번호에 해당하는 컨테이너 이미지 이름입니다. OS 특성을 지정하지 않으면 컨테이너 이미지가 모든 OS 버전에서 작동하는 것으로 간주되어 서비스 매니페스트에 지정된 이미지를 재정의합니다. 자세한 내용은 [Image 요소](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)를 참조하세요.

### <a name="environmentoverrides-element"></a>EnvironmentOverrides 요소
 자세한 내용은 [EnvironmentOverrides 요소](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)를 참조하세요.

### <a name="environmentvariable-element"></a>EnvironmentVariable 요소
환경 변수입니다. 자세한 내용은 [EnvironmentVariable 요소](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)를 참조하세요.

### <a name="certificateref-element"></a>CertificateRef 요소
컨테이너 환경에 노출해야 하는 X509 인증서에 대한 정보를 지정합니다. 모든 클러스터 노드의 LocalMachine 저장소에 인증서를 설치해야 합니다.
애플리케이션이 시작되면 런타임이 인증서를 읽고 PFX 파일 및 암호를 생성하거나(Windows인 경우) PEM 파일을 생성합니다(Linux인 경우).
PFX 파일과 암호는 Certificates_ServicePackageName_CodePackageName_CertName_PFX 및 Certificates_ServicePackageName_CodePackageName_CertName_Password 환경 변수를 사용하여 컨테이너에서 액세스할 수 있습니다. PEM 파일은 Certificates_ServicePackageName_CodePackageName_CertName_PEM 및 Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey 환경 변수를 사용하여 컨테이너에서 액세스할 수 있습니다. 자세한 내용은 [CertificateRef 요소](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)를 참조하세요.

### <a name="defaultservices-element"></a>DefaultServices 요소
이 애플리케이션 유형에 대해 애플리케이션이 인스턴스화될 때마다 자동으로 만드는 서비스 인스턴스를 선언합니다. 자세한 내용은 [DefaultServices 요소](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)를 참조하세요.

### <a name="service-element"></a>Service 요소
애플리케이션이 인스턴스화될 때 자동으로 만드는 서비스를 선언합니다. 자세한 내용은 [Service 요소](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)를 참조하세요.

### <a name="statelessservice-element"></a>StatelessService 요소
상태 비저장 서비스를 정의합니다. 자세한 내용은 [StatelessService 요소](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)를 참조하세요.


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService 서비스 매니페스트 요소
### <a name="servicemanifest-element"></a>ServiceManifest 요소
서비스 유형 및 버전을 선언적으로 설명합니다. 하나 이상의 서비스 유형을 지원하도록 서비스 패키지를 함께 구성하는 독립적으로 업그레이드 가능한 코드, 구성 및 데이터 패키지를 나열합니다. 리소스, 진단 설정 및 서비스 메타데이터(예: 서비스 유형, 상태 속성 및 부하 분산 메트릭)도 지정합니다. 자세한 내용은 [ServiceManifest 요소](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)를 참조하세요.

### <a name="servicetypes-element"></a>ServiceTypes 요소
이 매니페스트의 CodePackage에서 지원하는 서비스 유형을 정의합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 서비스 유형은 코드 패키지 수준이 아니라 매니페스트 수준에서 선언됩니다. 자세한 내용은 [ServiceTypes 요소](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="statelessservicetype-element"></a>StatelessServiceType 요소
상태 비저장 서비스 유형을 설명합니다. 자세한 내용은 [StatelessServiceType 요소](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)를 참조하세요.

### <a name="codepackage-element"></a>CodePackage 요소
정의된 서비스 유형을 지원하는 코드 패키지를 설명합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 결과 프로세스는 런타임에 지원되는 서비스 유형을 등록합니다. 여러 코드 패키지가 있는 경우 시스템에서 선언된 서비스 유형 중 하나를 찾을 때마다 모두 활성화됩니다. 자세한 내용은 [CodePackage 요소](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)를 참조하세요.

### <a name="entrypoint-element"></a>EntryPoint 요소
EntryPoint에서 지정한 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. SetupEntryPoint가 성공적으로 종료된 후에 EntryPoint에서 지정한 실행 파일이 실행됩니다. 결과 프로세스를 모니터링하고, 종료되거나 충돌하는 경우 다시 시작합니다(SetupEntryPoint를 사용하여 다시 시작). 자세한 내용은 [EntryPoint 요소](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="containerhost-element"></a>ContainerHost 요소
 자세한 내용은 [ContainerHost 요소](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)를 참조하세요.

### <a name="imagename-element"></a>ImageName 요소
https://hub.docker.com의 리포지토리 및 이미지 또는 Azure Container Registry입니다. 자세한 내용은 [ImageName 요소](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)를 참조하세요.

### <a name="environmentvariables-element"></a>EnvironmentVariables 요소
환경 변수를 컨테이너 또는 exe.에 전달합니다.  자세한 내용은 [EnvironmentVariables 요소](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="environmentvariable-element"></a>EnvironmentVariable 요소
환경 변수입니다. 자세한 내용은 [EnvironmentVariable 요소](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)를 참조하세요.

### <a name="configpackage-element"></a>ConfigPackage 요소
Name 특성으로 명명되고 Settings.xml 파일이 포함된 폴더를 선언합니다. 이 파일은 런타임에 프로세스에서 다시 읽을 수 있는 사용자 정의 키-값 쌍 설정의 섹션을 포함합니다. 업그레이드하는 동안 ConfigPackage 버전만 변경되면 실행 중인 프로세스가 다시 시작되지 않습니다. 대신, 콜백에서는 구성 설정이 변경되어 동적으로 다시 로드할 수 있음을 프로세스에 알립니다. 자세한 내용은 [ConfigPackage 요소](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)를 참조하세요.

### <a name="datapackage-element"></a>DataPackage 요소
Name 특성으로 명명되고 정적 데이터 파일이 포함된 폴더를 선언합니다. Service Fabric에서 서비스 매니페스트에 나열된 데이터 패키지를 업그레이드할 때 호스트 및 지원 패키지에 지정된 모든 EXE 및 DLLHOST를 재활용합니다. 자세한 내용은 [DataPackage 요소](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)를 참조하세요.

### <a name="resources-element"></a>Resources 요소
이 서비스에서 사용하는 리소스를 설명합니다. 이 리소스는 컴파일된 코드를 수정하지 않고 선언할 수 있으며, 서비스를 배포할 때 변경할 수 있습니다. 이러한 리소스에 대한 액세스는 애플리케이션 매니페스트의 Principals 및 Policies 섹션을 통해 제어됩니다. 자세한 내용은 [Resources 요소](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="endpoints-element"></a>Endpoints 요소
서비스에 대한 엔드포인트를 정의합니다. 자세한 내용은 [Endpoints 요소](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)를 참조하세요.

### <a name="endpoint-element"></a>Endpoint 요소
자세한 내용은 [Endpoint 요소](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)를 참조하세요.


## <a name="backendservice-service-manifest-elements"></a>BackEndService 서비스 매니페스트 요소
### <a name="servicemanifest-element"></a>ServiceManifest 요소
서비스 유형 및 버전을 선언적으로 설명합니다. 하나 이상의 서비스 유형을 지원하도록 서비스 패키지를 함께 구성하는 독립적으로 업그레이드 가능한 코드, 구성 및 데이터 패키지를 나열합니다. 리소스, 진단 설정 및 서비스 메타데이터(예: 서비스 유형, 상태 속성 및 부하 분산 메트릭)도 지정합니다. 자세한 내용은 [ServiceManifest 요소](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)를 참조하세요.

### <a name="servicetypes-element"></a>ServiceTypes 요소
이 매니페스트의 CodePackage에서 지원하는 서비스 유형을 정의합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 서비스 유형은 코드 패키지 수준이 아니라 매니페스트 수준에서 선언됩니다. 자세한 내용은 [ServiceTypes 요소](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="statelessservicetype-element"></a>StatelessServiceType 요소
상태 비저장 서비스 유형을 설명합니다. 자세한 내용은 [StatelessServiceType 요소](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)를 참조하세요.

### <a name="codepackage-element"></a>CodePackage 요소
정의된 서비스 유형을 지원하는 코드 패키지를 설명합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 결과 프로세스는 런타임에 지원되는 서비스 유형을 등록합니다. 여러 코드 패키지가 있는 경우 시스템에서 선언된 서비스 유형 중 하나를 찾을 때마다 모두 활성화됩니다. 자세한 내용은 [CodePackage 요소](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)를 참조하세요.

### <a name="entrypoint-element"></a>EntryPoint 요소
EntryPoint에서 지정한 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. SetupEntryPoint가 성공적으로 종료된 후에 EntryPoint에서 지정한 실행 파일이 실행됩니다. 결과 프로세스를 모니터링하고, 종료되거나 충돌하는 경우 다시 시작합니다(SetupEntryPoint를 사용하여 다시 시작). 자세한 내용은 [EntryPoint 요소](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="containerhost-element"></a>ContainerHost 요소
자세한 내용은 [ContainerHost 요소](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)를 참조하세요.

### <a name="imagename-element"></a>ImageName 요소
https://hub.docker.com의 리포지토리 및 이미지 또는 Azure Container Registry입니다. 자세한 내용은 [ImageName 요소](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)를 참조하세요.

### <a name="commands-element"></a>Commands 요소
쉼표로 구분된 명령 목록을 컨테이너에 전달합니다. 자세한 내용은 [Commands 요소](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)를 참조하세요.

### <a name="environmentvariables-element"></a>EnvironmentVariables 요소
환경 변수를 컨테이너 또는 exe.에 전달합니다.  자세한 내용은 [EnvironmentVariables 요소](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)를 참조하세요.

### <a name="environmentvariable-element"></a>EnvironmentVariable 요소
환경 변수입니다. 자세한 내용은 [EnvironmentVariable 요소](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)를 참조하세요.

### <a name="configpackage-element"></a>ConfigPackage 요소
Name 특성으로 명명되고 Settings.xml 파일이 포함된 폴더를 선언합니다. 이 파일은 런타임에 프로세스에서 다시 읽을 수 있는 사용자 정의 키-값 쌍 설정의 섹션을 포함합니다. 업그레이드하는 동안 ConfigPackage 버전만 변경되면 실행 중인 프로세스가 다시 시작되지 않습니다. 대신, 콜백에서는 구성 설정이 변경되어 동적으로 다시 로드할 수 있음을 프로세스에 알립니다. 자세한 내용은 [ConfigPackage 요소](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)를 참조하세요.

### <a name="resources-element"></a>Resources 요소
이 서비스에서 사용하는 리소스를 설명합니다. 이 리소스는 컴파일된 코드를 수정하지 않고 선언할 수 있으며, 서비스를 배포할 때 변경할 수 있습니다. 이러한 리소스에 대한 액세스는 애플리케이션 매니페스트의 Principals 및 Policies 섹션을 통해 제어됩니다. 자세한 내용은 [Resources 요소](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)를 참조하세요.

### <a name="endpoints-element"></a>Endpoints 요소
서비스에 대한 엔드포인트를 정의합니다. 자세한 내용은 [Endpoints 요소](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)를 참조하세요.

### <a name="endpoint-element"></a>Endpoint 요소
 자세한 내용은 [Endpoint 요소](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)를 참조하세요.

