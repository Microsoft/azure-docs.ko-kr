---
title: Kestrel을 사용하여 HTTPS 엔드포인트 추가
description: 이 자습서에서는 Kestrel을 사용하여 ASP.NET Core 프런트 엔드 웹 서비스에 HTTPS 엔드포인트를 추가하고 클러스터에 애플리케이션을 배포하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 6b8587982a8cbe496427960495055adbd68a07a7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997288"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>자습서: Kestrel을 사용하여 ASP.NET Core Web API 프런트 엔드 서비스에 HTTPS 엔드포인트 추가

이 자습서는 시리즈의 3부입니다.  Service Fabric에서 실행되는 ASP.NET Core 서비스에서 HTTPS를 사용하는 방법을 알아봅니다. 완료되면 포트 443에서 수신 대기하는 HTTPS 사용 ASP.NET Core 웹 프런트 엔드를 사용하는 투표 애플리케이션이 생성됩니다. [.NET Service Fabric 애플리케이션 빌드](service-fabric-tutorial-deploy-app-to-party-cluster.md)에서 수동으로 투표 애플리케이션을 만들지 않으려면 완성된 애플리케이션의 [소스 코드를 다운로드](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)할 수 있습니다.

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 서비스에서 HTTPS 엔드포인트 정의
> * HTTPS를 사용하도록 Kestrel 구성
> * 원격 클러스터 노드에 TLS/SSL 인증서 설치
> * NETWORK SERVICE에 인증서의 프라이빗 키에 대한 액세스 권한 부여
> * Azure 부하 분산 장치에서 포트 443 열기
> * 애플리케이션을 원격 클러스터에 배포

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [.NET Service Fabric 애플리케이션 빌드](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [애플리케이션을 원격 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * ASP.NET Core 프런트 엔드 서비스에 HTTPS 엔드포인트 추가
> * [Azure Pipelines를 사용하여 CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [애플리케이션에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드가 포함된 [Visual Studio 2019 설치](https://www.visualstudio.com/) 버전 16.5 이상.
* [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>인증서를 받거나 자체 서명된 개발 인증서 만들기

프로덕션 애플리케이션의 경우 [CA(인증 기관)](https://wikipedia.org/wiki/Certificate_authority)의 인증서를 사용합니다. 개발 및 테스트 목적으로 자체 서명된 인증서를 만들어 사용할 수 있습니다. Service Fabric SDK는 *CertSetup.ps1* 스크립트를 제공하며, 자체 서명된 인증서를 만들어 `Cert:\LocalMachine\My` 인증서 저장소로 가져옵니다. 관리자 권한으로 명령 프롬프트를 열고 다음 명령을 실행하여 주체가 "CN=mytestcert"인 인증서를 만듭니다.

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

인증서 PFX 파일이 이미 있는 경우 다음을 실행하여 인증서를 `Cert:\LocalMachine\My` 인증서 저장소로 가져옵니다.

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>서비스 매니페스트에서 HTTPS 엔드포인트 정의

**관리자** 권한으로 Visual Studio를 시작하고 투표 솔루션을 엽니다. 솔루션 탐색기에서 *VotingWeb/PackageRoot/ServiceManifest.xml*을 엽니다. 이 서비스 매니페스트는 서비스 엔드포인트를 정의합니다.  **엔드포인트** 섹션을 찾고 기존 "ServiceEndpoint" 엔드포인트를 편집합니다.  이름을 "EndpointHttps"로 변경하고 프로토콜을 *https*로, 유형을 *입력*으로, 포트를 *443*으로 설정합니다.  변경 내용을 저장합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>HTTPS를 사용하도록 Kestrel 구성

솔루션 탐색기에서 *VotingWeb/VotingWeb.cs* 파일을 엽니다.  HTTPS를 사용하고 `Cert:\LocalMachine\My` 저장소에서 인증서를 조회하도록 Kestrel을 구성합니다. 다음 using 명령문을 추가합니다.

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

새로운 *EndpointHttps* 엔드포인트를 사용하고 포트 443에서 수신 대기하도록 `ServiceInstanceListener`를 업데이트합니다. Kestrel 서버를 사용하도록 웹 호스트를 구성할 때는 Kestrel이 모든 네트워크 인터페이스에 대해 IPv6 주소를 수신 대기하도록 구성해야 합니다(`opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`).

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(FindMatchingCertificateBySubject());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

또한 Kestrel이 해당 주체를 사용하여 `Cert:\LocalMachine\My` 저장소에서 인증서를 찾을 수 있도록 다음 메서드를 추가합니다.  

이전 PowerShell 명령으로 자체 서명된 인증서를 만든 경우 "&lt;your_CN_value&gt;"를 "mytestcert"로 바꾸거나 인증서의 CN을 사용합니다.
`localhost`에 로컬을 배포하는 경우 인증 예외를 방지하기 위해 "CN=localhost"를 사용하는 것이 좋습니다.

```csharp
private X509Certificate2 FindMatchingCertificateBySubject(string subjectCommonName)
{
    using (var store = new X509Store(StoreName.My, StoreLocation.LocalMachine))
    {
        store.Open(OpenFlags.OpenExistingOnly | OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var matchingCerts = new X509Certificate2Collection();
    
    foreach (var enumeratedCert in certCollection)
    {
      if (StringComparer.OrdinalIgnoreCase.Equals(subjectCommonName, enumeratedCert.GetNameInfo(X509NameType.SimpleName, forIssuer: false))
        && DateTime.Now < enumeratedCert.NotAfter
        && DateTime.Now >= enumeratedCert.NotBefore)
        {
          matchingCerts.Add(enumeratedCert);
        }
    }

        if (matchingCerts.Count == 0)
    {
        throw new Exception($"Could not find a match for a certificate with subject 'CN={subjectCommonName}'.");
    }
        
        return matchingCerts[0];
    }
}


```

## <a name="grant-network-service-access-to-the-certificates-private-key"></a>NETWORK SERVICE에 인증서의 프라이빗 키에 대한 액세스 권한 부여

이전 단계에서 개발 컴퓨터의 `Cert:\LocalMachine\My` 저장소로 인증서를 가져온 상태입니다.  또한 서비스를 실행하는 계정(기본적으로 NETWORK SERVICE)에 인증서의 프라이빗 키에 대한 액세스 권한을 명시적으로 부여해야 합니다. 이 단계는 수동으로(certlm.msc 도구 사용) 수행할 수 있지만 서비스 매니페스트의 **SetupEntryPoint**에 [시작 스크립트를 구성](service-fabric-run-script-at-service-startup.md)하여 PowerShell 스크립트를 자동으로 실행하는 것이 좋습니다.

>[!NOTE]
> Service Fabric은 지문 또는 주체 일반 이름으로 엔드포인트 인증서를 선언하는 것을 지원합니다. 이 경우 런타임은 서비스를 실행하는 ID에 대한 인증서의 프라이빗 키를 바인딩하고 ACL을 설정합니다. 또한 런타임은 인증서의 변경/갱신을 모니터링하고 해당하는 프라이빗 키를 적절하게 다시 ACL합니다.

### <a name="configure-the-service-setup-entry-point"></a>서비스 설치 진입점 구성

솔루션 탐색기에서 *VotingWeb/PackageRoot/ServiceManifest.xml*을 엽니다.  **CodePackage** 섹션에 **SetupEntryPoint** 노드를 추가한 다음, **ExeHost** 노드를 추가합니다.  **ExeHost**에서 **Program**을 "Setup.bat"로 설정하고 **WorkingFolder**을 "CodePackage"로 설정합니다.  VotingWeb 서비스가 시작되면 VotingWeb.exe 가 시작되기 전에 Setup.bat 스크립트가 CodePackage 폴더에서 실행됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>일괄 처리 및 PowerShell 설치 스크립트 추가

**SetupEntryPoint** 지점에서 PowerShell을 실행하려면 PowerShell 파일을 가리키는 일괄 처리 파일에서 PowerShell.exe를 실행하면 됩니다. 먼저 서비스 프로젝트에 일괄 처리 파일을 추가합니다.  솔루션 탐색기에서 **VotingWeb**을 마우스 오른쪽 단추로 클릭하고 **추가**->**새 항목**을 선택하여 "Setup.bat" 라는 새 파일을 추가합니다.  *Setup.bat* 파일을 편집하고 다음 명령을 추가합니다.

```bat
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

*Setup.bat* 파일 속성을 수정하여 **출력 디렉터리로 복사**를 "변경된 내용만 복사"로 설정합니다.

![파일 속성 설정][image1]

솔루션 탐색기에서 **VotingWeb**을 마우스 오른쪽 단추로 클릭하고 **추가**->**새 항목**을 선택하여 "SetCertAccess.ps1" 이라는 새 파일을 추가합니다.  *SetCertAccess.ps1* 파일을 편집하고 다음 스크립트를 추가합니다.

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

*SetCertAccess.ps1* 파일 속성을 수정하여 **출력 디렉터리로 복사**를 "변경된 내용만 복사"로 설정합니다.

### <a name="run-the-setup-script-as-a-local-administrator"></a>로컬 관리자 권한으로 설치 스크립트 실행

기본적으로 서비스 설치 진입점 실행 파일은 Service Fabric과 동일한 자격 증명(일반적으로 NetworkService 계정)으로 실행됩니다. *SetCertAccess.ps1*을 실행하려면 관리자 권한이 필요합니다. 애플리케이션 매니페스트에서 로컬 관리자 계정으로 시작 스크립트를 실행하도록 보안 권한을 변경할 수 있습니다.

솔루션 탐색기에서 *Voting/ApplicationPackageRoot/ApplicationManifest.xml*을 엽니다. 먼저 **보안 주체** 섹션을 만들고 새 사용자를 추가합니다(예: "SetupAdminUser"). SetupAdminUser 사용자 계정을 관리자 시스템 그룹에 추가합니다.
그런 다음, VotingWebPkg **ServiceManifestImport** 섹션에서 **RunAsPolicy**를 구성하여 SetupAdminUser 보안 주체를 설치 진입점에 적용합니다. 이 정책은 Service Fabric에 Setup.bat 파일이 Service SetupAdminUser(관리자 권한 있음) 권한으로 실행됨을 알립니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
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

## <a name="run-the-application-locally"></a>로컬에서 애플리케이션 실행

솔루션 탐색기에서 **Voting** 애플리케이션을 선택하고 **애플리케이션 URL** 속성을 “https:\//localhost:443”으로 설정합니다.

모든 파일을 저장하고 F5 키를 눌러 로컬에서 애플리케이션을 실행합니다.  애플리케이션이 배포되면 웹 브라우저에 https:\//localhost:443이 열립니다. 자체 서명된 인증서를 사용하는 경우 PC가 이 웹 사이트의 보안을 신뢰하지 않는다는 경고가 표시됩니다.  웹 페이지로 계속 이동합니다.

![투표 애플리케이션][image2]

## <a name="install-certificate-on-cluster-nodes"></a>클러스터 노드에 인증서 설치

Azure에 애플리케이션을 배포하기 전에 모든 원격 클러스터 노드의 `Cert:\LocalMachine\My` 저장소에 인증서를 설치합니다.  서비스는 클러스터의 다른 노드로 이동할 수 있습니다.  클러스터 노드에서 프런트 엔드 웹 서비스가 시작되면 시작 스크립트는 인증서를 조회하고 액세스 권한을 구성합니다.

먼저 인증서를 PFX 파일로 내보냅니다. certlm.msc 애플리케이션을 열고 **개인**>**인증서**로 이동합니다.  *mytestcert* 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**>**내보내기**를 선택합니다.

![인증서 내보내기][image4]

내보내기 마법사에서 **예, 프라이빗 키를 내보냅니다**를 선택하고, PFX(개인 정보 교환) 형식을 선택합니다.  파일을 *C:\Users\sfuser\votingappcert.pfx*로 내보냅니다.

그런 다음, [제공된 Powershell 스크립트](./scripts/service-fabric-powershell-add-application-certificate.md)를 사용하여 원격 클러스터에 인증서를 설치합니다.

> [!Warning]
> 개발 및 테스트 애플리케이션은 자체 서명된 인증서로 충분합니다. 프로덕션 애플리케이션의 경우 자체 서명된 인증서 대신 [CA(인증 기관)](https://wikipedia.org/wiki/Certificate_authority)의 인증서를 사용합니다.

## <a name="open-port-443-in-the-azure-load-balancer"></a>Azure 부하 분산 장치에서 포트 443 열기

아직 열지 않은 경우 부하 분산 장치에서 포트 443을 엽니다.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

## <a name="deploy-the-application-to-azure"></a>Azure에 애플리케이션 배포

모든 파일을 저장하고 디버그에서 릴리스로 전환한 다음, F6 키를 눌러 다시 빌드합니다.  솔루션 탐색기에서 **투표**를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. [클러스터에 애플리케이션 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)에서 만든 클러스터의 연결 엔드포인트를 선택하거나, 다른 클러스터를 선택합니다.  애플리케이션을 원격 클러스터에 게시하려면 **게시**를 클릭합니다.

애플리케이션이 배포되면 웹 브라우저를 열고 `https://mycluster.region.cloudapp.azure.com:443`(클러스터의 연결 엔드포인트로 URL 업데이트)으로 이동합니다. 자체 서명된 인증서를 사용하는 경우 PC가 이 웹 사이트의 보안을 신뢰하지 않는다는 경고가 표시됩니다.  웹 페이지로 계속 이동합니다.

![투표 애플리케이션][image3]

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 서비스에서 HTTPS 엔드포인트 정의
> * HTTPS를 사용하도록 Kestrel 구성
> * 원격 클러스터 노드에 TLS/SSL 인증서 설치
> * NETWORK SERVICE에 인증서의 프라이빗 키에 대한 액세스 권한 부여
> * Azure 부하 분산 장치에서 포트 443 열기
> * 애플리케이션을 원격 클러스터에 배포

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure Pipelines를 사용하여 CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
