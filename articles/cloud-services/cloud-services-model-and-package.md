---
title: 클라우드 서비스 모델 및 패키지 정의 | Microsoft Docs
description: Azure의 클라우드 서비스 모델(.csdef,.cscfg) 및 패키지(.cspkg)에 대해 설명합니다.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 9c9f7dfd9ecbf085da19fc010e497caef8c18629
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432639"
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>클라우드 서비스 모델 정의 및 패키지 방법
클라우드 서비스는 서비스 정의 *(.csdef)*, 서비스 구성 *(.cscfg)*, 서비스 패키지 *(.cspkg)* 의 세 구성 요소에서 생성됩니다. **ServiceDefinition.csdef** 및 **ServiceConfig.cscfg** 파일은 둘 다 XML 기반으로, 클라우드 서비스의 구조 및 구성 방법(합쳐서 모델이라고 함)을 설명합니다. **ServicePackage.cspkg**는 **ServiceDefinition.csdef** 및 다른 구성 요소에서 생성되는 zip 파일로, 필수 이진 기반 종속성을 모두 포함합니다. Azure는 **ServicePackage.cspkg**와 **ServiceConfig.cscfg**에서 모두 클라우드 서비스를 만듭니다.

Azure에서 클라우드 서비스가 실행 중이면 **ServiceConfig.cscfg** 파일을 통해 다시 구성할 수 있지만 정의는 변경할 수 없습니다.

## <a name="what-would-you-like-to-know-more-about"></a>자세하게 알고 싶은 내용이 무엇인가요?
* [ServiceDefinition.csdef](#csdef) 및 [ServiceConfig.cscfg](#cscfg) 파일에 대해 자세하게 알고 싶습니다.
* 이미 내용을 알고 있으므로 무엇을 구성할 수 있는지에 대한 [몇 가지 예](#next-steps) 를 보여 주세요.
* [ServicePackage.cspkg](#cspkg)를 만들려고 합니다.
* Visual Studio를 사용하여 다음 작업을 수행하려고 합니다.
  * [클라우드 서비스 만들기][vs_create]
  * [기존 클라우드 서비스 재구성][vs_reconfigure]
  * [클라우드 서비스 프로젝트 배포][vs_deploy]
  * [원격 데스크톱에서 클라우드 서비스 인스턴스에 연결][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** 파일은 클라우드 서비스를 구성하기 위해 Azure에서 사용되는 설정을 지정합니다. [Azure 서비스 정의 스키마(.csdef 파일)](/previous-versions/azure/reference/ee758711(v=azure.100)) 는 서비스 정의 파일에 허용되는 형식을 제공합니다. 다음 예제에서는 웹 및 작업자 역할에 정의할 수 있는 설정을 보여 줍니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

여기에 사용되는 XML 스키마를 더 잘 이해하려면 [서비스 정의 스키마](/previous-versions/azure/reference/ee758711(v=azure.100))를 참조하면 됩니다. 그러나 여기서 간략하게 몇 가지 요소를 설명합니다.

**Sites**  
IIS7에서 호스트되는 웹 사이트 또는 웹 애플리케이션에 대한 정의를 포함합니다.

**InputEndpoints**  
클라우드 서비스에 연결하는 데 사용되는 엔드포인트에 대한 정의를 포함합니다.

**InternalEndpoints**  
서로 통신하기 위해 역할 인스턴스에서 사용되는 엔드포인트에 대한 정의를 포함합니다.

**ConfigurationSettings**  
 특정 역할의 기능에 대한 설정 정의를 포함합니다.

**Certificates**  
 역할에 필요한 인증서에 대한 정의를 포함합니다. 앞의 코드 예제에서는 Azure Connect 구성에 사용되는 인증서를 보여 줍니다.

**LocalResources**  
 로컬 저장소 리소스에 대한 정의를 포함합니다. 로컬 저장소 리소스는 역할의 인스턴스가 실행 중인 가상 머신의 파일 시스템에 예약된 디렉터리입니다.

**Imports**  
 가져온 모듈에 대한 정의를 포함합니다. 앞의 코드 예제에서는 원격 데스크톱 연결 및 Azure Connect에 대한 모듈을 보여 줍니다.

**Startup**  
 역할이 시작될 때 실행되는 작업을 포함합니다. 작업은 .cmd 또는 실행 파일에 정의됩니다.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
클라우드 서비스에 대한 설정 구성은 **ServiceConfiguration.cscfg** 파일의 값에 따라 결정됩니다. 이 파일의 각 역할에 배포하려는 인스턴스 수를 지정합니다. 서비스 정의 파일에서 정의한 구성 설정에 대한 값이 서비스 구성 파일에 추가됩니다. 클라우드 서비스와 연결된 모든 관리 인증서의 지문도 파일에 추가됩니다. [Azure 서비스 구성 스키마(.cscfg 파일)](/previous-versions/azure/reference/ee758710(v=azure.100)) 에서 서비스 구성 파일에 허용되는 형식을 제공합니다.

서비스 구성 파일은 애플리케이션과 함께 패키지되지 않지만 별도 파일로 Azure에 업로드되어 클라우드 서비스를 구성하는 데 사용됩니다. 클라우드 서비스를 다시 배포하지 않고 새 서비스 구성 파일을 업로드할 수 있습니다. 클라우드 서비스가 실행되는 동안 클라우드 서비스에 대한 구성 값을 변경할 수 있습니다. 다음 예제에서는 웹 및 작업자 역할에 정의할 수 있는 구성 설정을 보여 줍니다.

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

여기에 사용되는 XML 스키마를 더 잘 이해하려면 [서비스 구성 스키마](/previous-versions/azure/reference/ee758710(v=azure.100))를 참조하면 됩니다. 그러나 여기서 간략하게 요소를 설명합니다.

**인스턴스**  
역할에 대해 실행 중인 인스턴스 수를 구성합니다. 업그레이드하는 동안 잠재적으로 클라우드 서비스를 사용할 수 없게 되는 것을 방지하려면 웹과 관련된 역할의 인스턴스를 두 개 이상 배포하는 것이 좋습니다. 둘 이상의 인스턴스를 배포하면 [Azure Compute SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/)의 지침을 준수하게 되므로 서비스를 위해 둘 이상의 역할 인스턴스가 배포될 때 인터넷 연결 역할에 대한 99.95%의 외부 연결을 보증합니다.

**ConfigurationSettings**  
 역할에 대해 실행 중인 인스턴스의 설정을 구성합니다. `<Setting>` 요소의 이름은 서비스 정의 파일에 있는 설정 정의와 일치해야 합니다.

**Certificates**  
 서비스에서 사용되는 인증서를 구성합니다. 앞의 코드 예제에서는 RemoteAccess 모듈의 인증서를 정의하는 방법을 보여 줍니다. *thumbprint* 특성 값은 사용할 인증서의 지문으로 설정되어야 합니다.

<p/>

> [!NOTE]
> 인증서의 지문은 텍스트 편집기를 사용하여 구성 파일에 추가할 수 있습니다. 또는 Visual Studio에서 역할 **속성** 페이지의 **인증서** 탭에서 값을 추가할 수 있습니다.
> 
> 

## <a name="defining-ports-for-role-instances"></a>역할 인스턴스에 대한 포트 정의
Azure는 웹 역할에 하나의 진입점만 허용합니다. 하나의 IP 주소를 통해 모든 트래픽이 발생한다는 의미입니다. 올바른 위치로 요청을 전송하도록 호스트 헤더를 구성하여 포트를 공유하도록 웹 사이트를 구성할 수 있습니다. IP 주소에서 잘 알려진 포트를 수신 대기하도록 애플리케이션을 구성할 수도 있습니다.

다음 샘플은 웹 사이트 및 웹 애플리케이션에서 웹 역할 구성 방법을 보여 줍니다. 웹 사이트는 포트 80에서 기본 입력 위치로 구성되어 있고 웹 애플리케이션은 “mail.mysite.cloudapp.net”이라는 대체 호스트 헤더에서 요청을 수신하도록 구성되어 있습니다.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>역할의 구성 변경
클라우드 서비스가 Azure에서 실행 중인 상태에서 서비스를 오프라인으로 전환하지 않고 클라우드 서비스의 구성을 업데이트할 수 있습니다. 구성 정보를 변경하려면 새 구성 파일을 업로드하거나 가동 중인 구성 파일을 편집하고 실행 중인 서비스에 적용하면 됩니다. 서비스 구성에서 변경할 수 있는 사항은 다음과 같습니다.

* **구성 설정 값 변경**  
   구성 설정 변경 시 인스턴스가 온라인인 상태에서 역할 인스턴스가 변경 사항을 적용하도록 선택하거나, 인스턴스가 오프라인인 상태에서 인스턴스를 정상적으로 재활용하여 변경 사항을 적용할 수 있습니다.
* **역할 인스턴스의 서비스 토폴로지 변경**  
  인스턴스가 제거되는 경우를 제외하고 토폴로지 변경은 실행 중인 인스턴스에 영향을 주지 않습니다. 일반적으로 나머지 모든 인스턴스는 재활용하지 않아도 됩니다. 하지만 토폴로지 변경에 대한 응답으로 역할 인스턴스를 재활용하도록 선택할 수는 있습니다.
* **인증서 지문 변경**  
   역할 인스턴스가 오프라인인 상태에서는 인증서만 업데이트할 수 있습니다. 역할 인스턴스가 온라인인 상태에서 인증서를 추가, 삭제 또는 변경하면 Azure가 정상적으로 인스턴스를 오프라인 상태로 전환하여 인증서를 업데이트하고 변경이 완료되면 온라인 상태로 다시 전환합니다.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>서비스 런타임 이벤트를 사용하여 구성 변경 사항 처리
[Azure 런타임 라이브러리](/previous-versions/azure/reference/mt419365(v=azure.100))는 [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) 네임스페이스를 포함하여 역할에서 Azure 환경과 상호 작용하기 위한 클래스를 제공합니다. [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) 클래스는 구성 변경 전후 발생하는 다음 이벤트를 정의합니다.

* **[Changing](/previous-versions/azure/reference/ee758134(v=azure.100)) 이벤트**  
  필요한 경우 구성 변경이 지정된 역할 인스턴스에 적용되어 역할 인스턴스가 작동 중지하는 기회가 생기기 전에 발생합니다.
* **[Changed](/previous-versions/azure/reference/ee758129(v=azure.100)) 이벤트**  
   지정된 역할 인스턴스에 구성 변경이 적용된 후 발생합니다.

> [!NOTE]
> 인증서 변경 사항이 있으면 항상 역할 인스턴스가 오프라인으로 전환되기 때문에 RoleEnvironment.Changing 또는 RoleEnvironment.Changed 이벤트가 발생하지 않습니다.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
애플리케이션을 Azure에서 클라우드 서비스로 배포하려면 먼저 적절한 형식으로 애플리케이션을 패키지해야 합니다. **CSPack** 명령줄 도구( [Azure SDK](https://azure.microsoft.com/downloads/)와 함께 설치됨)를 사용하여 Visual Studio 대신 패키지 파일을 만들 수 있습니다.

**CSPack** 은 서비스 정의 파일 및 서비스 구성 파일의 콘텐츠를 사용하여 패키지의 콘텐츠를 정의합니다. **CSPack**은 [Azure Portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy)을 사용하여 Azure에 업로드할 수 있는 애플리케이션 패키지 파일(.cspkg)을 생성합니다. 기본적으로 패키지의 이름은 `[ServiceDefinitionFileName].cspkg`이지만 **CSPack**의 `/out` 옵션을 사용하여 다른 이름을 지정할 수 있습니다.

**CSPack**은 다음 위치에 있습니다.  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe(Windows)는 SDK로 설치되는 **Microsoft Azure 명령 프롬프트** 바로 가기를 실행하여 사용할 수 있습니다.  
> 
> 가능한 모든 스위치 및 명령에 대한 설명서를 보려면 자체적으로 CSPack.exe 프로그램을 실행합니다.
> 
> 

<p />

> [!TIP]
> **Microsoft Azure Compute 에뮬레이터**에서 로컬로 클라우드 서비스를 실행하고 **/copyonly** 옵션을 사용합니다. 이 옵션을 사용하면 컴퓨팅 에뮬레이터에서 실행할 수 있는 디렉터리 레이아웃에 애플리케이션의 이진 파일을 복사합니다.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>클라우드 서비스를 패키지하는 예제 명령
다음 예제에서는 웹 역할에 대한 정보를 포함하는 애플리케이션 패키지를 만듭니다. 명령으로 사용할 서비스 정의 파일, 이진 파일을 찾을 수 있는 디렉터리 및 패키지 파일의 이름을 지정합니다.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

애플리케이션에 웹 역할 및 작업자 역할이 포함된 경우 다음 명령이 사용됩니다.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

여기서는 변수가 다음과 같이 정의됩니다.

| 변수 | 값 |
| --- | --- |
| \[DirectoryName\] |Azure 프로젝트의 .csdef 파일을 포함하는 루트 프로젝트 디렉터리의 하위 디렉터리입니다. |
| \[ServiceDefinition\] |서비스 정의 파일의 이름입니다. 기본적으로 이 파일의 이름은 ServiceDefinition.csdef입니다. |
| \[OutputFileName\] |생성된 패키지 파일의 이름입니다. 일반적으로 애플리케이션의 이름으로 설정됩니다. 파일 이름이 지정되지 않은 경우 애플리케이션 패키지는 \[ApplicationName\].cspkg로 만들어집니다. |
| \[RoleName\] |서비스 정의 파일에서 정의된 역할 이름입니다. |
| \[RoleBinariesDirectory] |역할에 대한 이진 파일의 위치입니다. |
| \[VirtualPath\] |서비스 정의의 Sites 섹션에서 정의된 각 가상 경로에 대한 실제 디렉터리입니다. |
| \[PhysicalPath\] |서비스 정의의 사이트 노드에서 정의된 각 가상 경로에 대한 콘텐츠의 실제 디렉터리입니다. |
| \[RoleAssemblyName\] |역할에 대한 이진 파일의 이름입니다. |

## <a name="next-steps"></a>다음 단계
클라우드 서비스 패키지를 만들고 있으며 다음 작업을 수행하려고 합니다.

* [클라우드 서비스 인스턴스에 대해 원격 데스크톱 설정][remotedesktop]
* [클라우드 서비스 프로젝트 배포][deploy]

Visual Studio를 사용하여 다음 작업을 수행하려고 합니다.

* [새 클라우드 서비스 만들기][vs_create]
* [기존 클라우드 서비스 재구성][vs_reconfigure]
* [클라우드 서비스 프로젝트 배포][vs_deploy]
* [클라우드 서비스 인스턴스에 대해 원격 데스크톱 설정][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
