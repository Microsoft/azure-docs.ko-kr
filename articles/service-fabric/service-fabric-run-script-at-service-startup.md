---
title: Azure Service Fabric 서비스가 시작될 때 스크립트 실행 | Microsoft Docs
description: Service Fabric 서비스 설치 진입점에 대한 정책을 구성하고 서비스 시작 시간에 스크립트를 실행하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: 76be814e0dd4c054fc3a873716dbfe395eeeb2dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837801"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>로컬 사용자 또는 시스템 계정으로 서비스 시작 스크립트 실행
Service Fabric 서비스 실행 파일이 시작되기 전에 일부 구성 또는 설치 작업을 실행해야 할 수도 있습니다.  예를 들어 환경 변수를 구성합니다. 서비스 실행 파일이 서비스의 서비스 매니페스트에서 시작되기 전에 실행할 스크립트를 지정할 수 있습니다. 서비스 설치 진입점에 대한 RunAs 정책을 구성하면 설치 실행 파일 실행에 사용되는 계정을 변경할 수 있습니다.  별도의 설치 진입점을 통해 짧은 기간 동안 높은 권한의 구성을 실행할 수 있으므로 서비스 호스트 실행 파일을 오랜 기간 동안 높은 권한으로 실행할 필요가 없습니다.

설치 진입점([서비스 매니페스트](service-fabric-application-and-service-manifests.md)의 **SetupEntryPoint**)은 기본적으로 Service Fabric과 동일한 자격 증명(일반적으로 *NetworkService* 계정)을 사용하여 다른 진입점보다 먼저 실행되는 권한 있는 진입점입니다. **EntryPoint**로 지정되는 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. **EntryPoint** 실행 파일은 **SetupEntryPoint** 실행 파일이 성공적으로 종료된 후 실행됩니다. 종료되지 않거나 충돌하는 경우 결과 프로세스를 모니터링하여 다시 시작합니다(**SetupEntryPoint**를 사용하여 다시 시작). 

## <a name="configure-the-service-setup-entry-point"></a>서비스 설치 진입점 구성
다음은 서비스 **SetupEntryPoint**에서 설치 스크립트 *MySetup.bat*를 지정하는 상태 비저장 서비스에 대한 간단한 서비스 매니페스트 예입니다.  **인수**는 실행 시 스크립트에 인수를 전달하는 데 사용됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>서비스 설치 진입점에 대한 정책 구성
기본적으로 서비스 설치 진입점 실행 파일은 Service Fabric과 동일한 자격 증명(일반적으로 *NetworkService* 계정)으로 실행됩니다.  애플리케이션 매니페스트에서 로컬 시스템 계정이나 관리자 계정으로 시작 스크립트를 실행하도록 보안 권한을 변경할 수 있습니다.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>로컬 시스템 계정을 사용하여 정책 구성
다음 애플리케이션 매니페스트 예제에서는 사용자 관리자 계정(SetupAdminUser)으로 실행되도록 서비스 설치 진입점을 구성하는 방법을 보여 줍니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

먼저, 사용자 이름(예: SetupAdminUser)을 사용하여 **주체** 섹션을 만듭니다. SetupAdminUser 사용자 계정은 관리자 시스템 그룹의 구성원입니다.

다음으로, **ServiceManifestImport** 섹션에서 정책을 구성하여 이 주체를 **SetupEntryPoint**에 적용합니다. 이 정책은 **MySetup.bat** 파일이 실행될 때 SetupAdminUser(관리자 권한 있음)로 실행되도록 Service Fabric에 지시합니다. 기본 진입점에 정책을 적용하지 *않았기* 때문에 **MyServiceHost.exe**의 코드가 시스템 **NetworkService** 계정으로 실행됩니다. 이 계정은 모든 서비스 진입점이 run as인 기본 계정입니다.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>로컬 시스템 계정을 사용하여 정책 구성
관리자 계정이 아닌 로컬 시스템 계정을 사용하여 스크립트의 시작을 실행하는 것이 일반적으로 더 좋습니다. 컴퓨터는 기본적으로 UAC(사용자 액세스 제어)를 사용하도록 설정되어 있기 때문에 관리자 그룹의 구성원으로 RunAs 정책을 실행하면 일반적으로 잘 작동하지 않습니다. 이러한 경우, 관리자 그룹에 추가된 로컬 사용자 대신 LocalSystem으로 SetupEntryPoint를 실행하는 것이 좋습니다. 다음 예제에서는 LocalSystem으로 실행하도록 SetupEntryPoint를 설정합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Linux 클러스터의 경우 서비스 또는 설정 진입점을 **root**로 실행하려면 **AccountType**을 **LocalSystem**으로 지정해야 합니다.

## <a name="run-a-script-from-the-setup-entry-point"></a>설치 진입점에서 스크립트 실행
이제 관리자 권한으로 실행할 시작 스크립트를 프로젝트에 추가합니다. 

Visual Studio에서 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 새 파일 *MySetup.bat*를 추가합니다.

그런 다음, *MySetup.bat* 파일이 서비스 패키지에 포함되도록 합니다. 기본적으로 아닙니다. 파일을 선택하고 상황에 맞는 메뉴를 마우스 오른쪽 단추로 클릭하며 **속성**을 선택합니다. 속성 대화 상자에서 **출력 디렉터리로 복사**가 **변경된 내용만 복사**로 설정되도록 합니다. 다음 스크린샷이 표시됩니다.

![SetupEntryPoint 배치 파일에 대한 Visual Studio CopyToOutput][image1]

이제 *MySetup.bat* 파일을 편집하고 다음 명령을 추가하여 시스템 환경 변수를 설정한 다음, 텍스트 파일을 출력합니다.

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

다음으로 솔루션을 빌드하여 로컬 개발 클러스터에 배포합니다. 서비스가 시작되면 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에 표시된 대로 MySetup.bat 파일이 두 가지 방법으로 성공한 것을 볼 수 있습니다. Azure PowerShell 명령 프롬프트를 열고 입력합니다.

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

그런 다음, 서비스가 배포되고 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에서 시작된 노드의 이름을 적어둡니다. 예를 들어 노드 2 등입니다. 다음으로 애플리케이션 인스턴스 작업 폴더로 이동하여 **TestVariable**값을 보여주는 out.txt 파일을 찾습니다. 예를 들어 이 서비스가 노드 2에 배포된 경우 **MyApplicationType**에 대한 이 경로로 이동할 수 있습니다.

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>설치 진입점에서 PowerShell 명령 실행
**SetupEntryPoint** 지점에서 PowerShell을 실행하려면 PowerShell 파일을 가리키는 배치 파일에서 **PowerShell.exe**를 실행하면 됩니다. 먼저 서비스 프로젝트(예: **MySetup.ps1**)에 PowerShell 파일을 추가합니다. 이 파일도 서비스 패키지에 포함되도록 *변경된 내용만 복사* 속성을 설정해야 합니다. 다음은 **TestVariable**이라는 시스템 환경 변수를 설정하는 PowerShell 파일 MySetup.ps1을 시작하는 간단한 배치 파일을 보여 주는 예제입니다.

PowerShell 파일을 시작하기 위한 MySetup.bat입니다.

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

PowerShell 파일에서 다음을 추가하여 시스템 환경 변수를 설정합니다.

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> 기본적으로 배치 파일이 실행될 때 **work**라는 애플리케이션 폴더에서 파일을 찾습니다. 이 경우 MySetup.bat가 실행될 때 같은 폴더(애플리케이션 **코드 패키지** 폴더)에서 MySetup.ps1 파일을 찾으려고 합니다. 이 폴더를 변경하려면 아래와 같이 작업 폴더를 설정합니다.
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>콘솔 리디렉션을 사용하여 로컬로 시작 스크립트 디버그
디버깅 목적으로 설치 스크립트를 실행하여 얻은 콘솔 출력을 확인하는 것이 유용할 때가 있습니다. 서비스 매니페스트에서 설치 진입점에 대해 출력을 파일에 기록하는 콘솔 리디렉션 정책을 설정할 수 있습니다. 파일 출력은 애플리케이션이 배포되고 실행되는 클러스터 노드에서 **log**라는 애플리케이션 폴더에 기록됩니다. 

> [!WARNING]
> 절대 프로덕션에 배포된 애플리케이션의 콘솔 리디렉션 정책은 사용하지 마세요. 애플리케이션 장애 조치(failover)에 영향을 줄 수 있기 때문입니다. 로컬 개발 및 디버깅 목적으로*만* 사용하세요.  
> 
> 

다음 서비스 매니페스트 예제에서는 콘솔 리디렉션을 FileRetentionCount 값으로 설정합니다.

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

이제 MySetup.ps1 파일을 변경하여 **Echo** 명령을 작성하면 디버깅 목적으로 출력 파일에 기록합니다.

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> 스크립트를 디버그한 후 즉시 이 콘솔 리디렉션 정책을 제거합니다.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* [애플리케이션 및 서비스 보안에 대한 자세한 정보](service-fabric-application-and-service-security.md)
* [애플리케이션 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
