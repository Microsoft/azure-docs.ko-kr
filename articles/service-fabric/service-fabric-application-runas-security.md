---
title: 시스템 및 보안 로컬 계정에서 Azure Service Fabric 서비스 실행 | Microsoft Docs
description: 시스템 및 보안 로컬 계정에서 Service Fabric 애플리케이션을 실행하는 방법에 대해 알아봅니다.  보안 주체를 만들고, 실행 정책을 적용하여 서비스를 안전하게 실행합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: aljo
ms.openlocfilehash: 28cd1162d7cae2b3a16062bdf18a2971e1f05aad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621176"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>로컬 사용자 계정 또는 시스템 계정으로 서비스 실행
Azure Service Fabric을 사용하여 다른 사용자 계정으로 클러스터에서 실행 중인 애플리케이션을 보호할 수 있습니다. 기본적으로 서비스 패브릭 애플리케이션은 Fabric.exe 프로세스가 실행하는 계정을 통해 실행됩니다. 또한 Service Fabric은 로컬 사용자 또는 시스템 계정에서 애플리케이션을 실행하는 기능을 제공합니다. 지원되는 로컬 시스템 계정 유형은 **LocalUser**, **NetworkService**, **LocalService** 및 **LocalSystem**입니다.  Windows 독립 실행형 클러스터에서 Service Fabric을 실행할 경우 [Active Directory 도메인 계정](service-fabric-run-service-as-ad-user-or-group.md) 또는 [그룹 관리 서비스 계정](service-fabric-run-service-as-gmsa.md)에서 서비스를 실행할 수 있습니다.

애플리케이션 매니페스트에서 **주체** 섹션의 서비스 또는 보안 리소스를 실행하는 데 필요한 사용자 계정을 정의합니다. 사용자 그룹을 정의하고 만들 수 있으므로 한 명 이상의 사용자를 함께 관리할 수 있습니다. 이는 다른 서비스 진입점에 사용자가 여러 명 있고, 그들에게 그룹 수준에서 사용할 수 있는 공통 권한이 필요한 경우에 유용합니다.  사용자는 애플리케이션의 특정 서비스 또는 모든 서비스에 적용되는 RunAs 정책에서 참조됩니다. 

기본적으로 RunAs 정책은 주 진입점에 적용됩니다.  또한 RunAs 정책을 [시스템 계정에서 특정 권한이 높은 설치 작업을 실행](service-fabric-run-script-at-service-startup.md)해야 하는 경우 설정 진입점에 적용하거나 주 진입점 및 설정 진입점 모두에 적용할 수 있습니다.  

> [!NOTE] 
> 서비스에 RunAs 정책을 적용하고 서비스 매니페스트가 HTTP 프로토콜로 엔드포인트 리소스를 선언하면 **SecurityAccessPolicy**를 지정해야 합니다.  자세한 내용은 [HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당](service-fabric-assign-policy-to-endpoint.md)을 참조하세요. 
>

## <a name="run-a-service-as-a-local-user"></a>로컬 사용자로 서비스 실행
애플리케이션 내에서 서비스를 보호하는 데 사용할 수 있는 로컬 사용자를 만들 수 있습니다. 애플리케이션 매니페스트의 주체 섹션에서 **LocalUser** 계정 형식이 지정되면 Service Fabric에서는 애플리케이션이 배포되는 컴퓨터에 로컬 사용자 계정을 만듭니다. 기본적으로 이러한 계정은 애플리케이션 매니페스트(예: 다음 애플리케이션 매니페스트 예제의 *Customer3*)에 지정된 것과 동일한 이름을 갖지 않습니다. 대신 동적으로 생성되며 임의 암호를 포함합니다.

**ServiceManifestImport**에 대한 **RunAsPolicy** 섹션의 **주체** 섹션에서 사용자 계정을 지정하여 서비스 코드 패키지를 실행합니다.  다음 예제에는 로컬 사용자를 만들고 주 진입점에 RunAs 정책을 적용하는 방법이 나와 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>로컬 사용자 그룹 만들기
사용자 그룹을 만들고 한 명 이상의 사용자를 그룹에 추가할 수 있습니다. 이 기능은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우 유용합니다. 다음 애플리케이션 매니페스트 예제는 관리자 권한이 있는 *LocalAdminGroup*이라는 로컬 그룹을 보여 줍니다. 두 사용자 *Customer1*과 *Customer2*가 이 로컬 그룹의 구성원이 됩니다. **ServiceManifestImport** 섹션에서 RunAs 정책이 *Stateful1Pkg* 코드 패키지를 *Customer2*로 실행하는 데 적용됩니다.  다른 RunAs 정책은 *Web1Pkg* 코드 패키지를 *Customer1*로 실행하는 데 적용됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>모든 서비스 코드 패키지에 기본 정책 적용
**DefaultRunAsPolicy** 섹션은 특정 **RunAsPolicy**가 정의되지 않은 모든 코드 패키지에 대해 기본 사용자 계정을 지정하는 데 사용됩니다. 애플리케이션에서 사용하는 서비스 매니페스트에 지정된 대부분의 코드 패키지가 동일한 사용자를 통해 실행되어야 하는 경우 애플리케이션에서 해당 사용자 계정을 사용하여 기본 RunAs 정책을 정의할 수 있습니다. 다음은 코드 패키지의 **RunAsPolicy**가 지정되지 않으면 주체 섹션에 지정된 **MyDefaultAccount** 사용자에서 코드 패키지가 수행되어야 함을 보여 주는 예제입니다.  지원되는 계정 유형은 LocalUser, NetworkService, LocalSystem 및 LocalService입니다.  또한 로컬 사용자 또는 서비스를 사용하는 경우 계정 이름 및 암호를 지정합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>콘솔 리디렉션을 사용하여 코드 패키지를 로컬로 디버그
디버깅 목적으로 서비스를 실행하여 얻은 콘솔 출력을 확인하는 것이 유용할 때가 있습니다. 서비스 매니페스트에서 진입점에 대해 출력을 파일에 기록하는 콘솔 리디렉션 정책을 설정할 수 있습니다. 파일 출력은 애플리케이션이 배포되고 실행되는 클러스터 노드에서 **log**라는 애플리케이션 폴더에 기록됩니다. 

> [!WARNING]
> 절대 프로덕션에 배포된 애플리케이션의 콘솔 리디렉션 정책은 사용하지 마세요. 애플리케이션 장애 조치(failover)에 영향을 줄 수 있기 때문입니다. 로컬 개발 및 디버깅 목적으로*만* 사용하세요.  
> 
> 

다음 서비스 매니페스트 예제에서는 콘솔 리디렉션을 FileRetentionCount 값으로 설정합니다.

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* [애플리케이션 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
