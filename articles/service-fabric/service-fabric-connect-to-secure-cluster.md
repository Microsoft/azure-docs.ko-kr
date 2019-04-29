---
title: Azure Service Fabric 클러스터에 안전하게 연결 | Microsoft Docs
description: Service Fabric 클러스터에 대한 클라이언트 액세스를 인증하는 방법 및 클라이언트와 클러스터 간의 통신을 보호하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2019
ms.author: aljo
ms.openlocfilehash: 42c8fa15c6b1e7c98ae47180bec5cc61236a7c44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881388"
---
# <a name="connect-to-a-secure-cluster"></a>보안 클러스터에 연결

클라이언트가 Service Fabric 클러스터 노드에 연결하는 경우 클라이언트는 인증서 보안 또는 Azure Active Directory(AAD)를 사용하여 인증을 받고 보안 통신이 설정될 수 있습니다. 이 인증을 통해 권한이 있는 사용자만 클러스터 및 배포된 애플리케이션에 액세스할 수 있으며 관리 작업을 수행할 수 있습니다.  인증서 또는 AAD 보안은 클러스터가 만들어지기 전에 클러스터에서 설정되어 있어야 합니다.  클러스터 보안 시나리오에 대한 자세한 내용은 [보안 클러스터](service-fabric-cluster-security.md)를 참조하세요. 인증서로 보호되는 클러스터에 연결하는 경우 클러스터에 연결할 컴퓨터에서 [클라이언트 인증서를 설정](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert)하세요. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI(sfctl)를 사용하여 보안 클러스터에 연결

몇 가지 방법으로 Service Fabric CLI(sfctl)를 사용하는 보안 클러스터에 연결할 수 있습니다. 인증에 클라이언트 인증서를 사용하는 경우 인증서 세부 정보는 클러스터 노드에 배포된 인증서와 일치해야 합니다. 인증서에 CA(인증 기관)가 있으면 신뢰할 수 있는 CA를 추가적으로 지정해야 합니다.

`sfctl cluster select` 명령을 사용하여 클러스터에 연결할 수 있습니다.

클라이언트 인증서는 인증서 및 키 쌍 또는 단일 pem 파일의 두 가지 형태로 지정할 수 있습니다. 암호로 보호되는 PEM 파일의 경우 암호를 입력하라는 메시지가 자동으로 표시됩니다. 클라이언트 인증서를 PFX 파일로 받은 경우 먼저 다음 명령을 사용하여 PFX 파일을 PEM 파일로 변환합니다. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

.pfx 파일이 암호로 보호되어 있지 않으면 마지막 매개 변수에 -passin pass:를 사용합니다.

클라이언트 인증서를 pem 파일로 지정하려면 `--pem` 인수에 파일 경로를 지정합니다. 예를 들면 다음과 같습니다.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

명령을 실행하기 전에 암호로 보호되는 pem 파일에 암호를 묻는 메시지가 나타납니다.

인증서, 키 쌍을 지정하려면 `--cert` 및 `--key` 인수를 사용하여 각 파일에 대한 파일 경로를 지정합니다.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

경우에 따라 테스트 또는 개발 클러스터 보안에 사용된 인증서가 인증서 유효성 검사에 실패하는 경우가 있습니다. 인증서 유효성 검사를 무시하려면 `--no-verify` 옵션을 지정합니다. 예를 들면 다음과 같습니다.

> [!WARNING]
> 프로덕션 Service Fabric 클러스터에 연결할 때 `no-verify` 옵션을 사용하지 마세요.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

또한 신뢰할 수 있는 CA 인증서 또는 개별 인증서의 디렉터리 경로를 지정할 수 있습니다. 이러한 경로를 지정하려면 `--ca` 인수를 사용합니다. 예를 들면 다음과 같습니다.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

연결 후에는 클러스터와 상호 작용하기 위해 [다른 sfctl 명령을 실행](service-fabric-cli.md)할 수 있습니다.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>PowerShell을 사용하여 클러스터에 연결
PowerShell을 통해 클러스터에서 작업을 수행하기 전에 먼저 클러스터와 연결을 설정합니다. 클러스터 연결은 지정된 PowerShell 세션에서 모든 후속 명령에 사용됩니다.

### <a name="connect-to-an-unsecure-cluster"></a>비보안 클러스터에 연결

비보안 클러스터에 연결하려면 **Connect-ServiceFabricCluster** 명령에 클러스터 엔드포인트 주소를 제공합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory를 사용하여 보안 클러스터에 연결

클러스터 관리자 액세스 권한을 부여하기 위해 Azure Active Directory를 사용하는 보안 클러스터에 연결하려면 클러스터 인증서 지문을 제공하고 *AzureActiveDirectory* 플래그를 사용합니다.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결
다음 PowerShell 명령을 실행하여 관리자 액세스 권한을 부여하는 데 클라이언트 인증서를 사용하는 보안 클러스터에 연결합니다. 

#### <a name="connect-using-certificate-common-name"></a>인증서 일반 이름을 사용하는 커넥터
클러스터 관리에 대한 권한이 부여된 클러스터 인증서 일반 이름 및 클라이언트 인증서의 일반 이름을 제공합니다. 인증서 세부 정보는 클러스터 노드의 인증서와 일치해야 합니다.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName*은 클러스터 노드에 설치된 서버 인증서의 일반 이름입니다. *FindValue*는 관리자 클라이언트 인증서의 일반 이름입니다. 매개 변수가 입력되면 명령은 다음 예와 같아집니다.
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>인증서 지문을 사용하는 연결
클러스터 관리에 대한 권한이 부여된 클러스터 인증서 지문과 클라이언트 인증서 지문을 제공합니다. 인증서 세부 정보는 클러스터 노드의 인증서와 일치해야 합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* 는 클러스터 노드에 설치된 서버 인증서의 지문입니다. *FindValue* 는 관리자 클라이언트 인증서의 지문입니다.  매개 변수가 입력되면 명령은 다음 예와 같아집니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Windows Active Directory를 사용하여 보안 클러스터에 연결
독립 실행형 클러스터가 AD 보안을 사용하여 배포되는 경우 "WindowsCredential" 스위치를 추가하여 클러스터에 연결합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>FabricClient API를 사용하여 클러스터에 연결
Service Fabric SDK는 클러스터 관리를 위해 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) 클래스를 제공합니다. FabricClient API를 사용하려면 Microsoft.ServiceFabric NuGet 패키지를 가져옵니다.

### <a name="connect-to-an-unsecure-cluster"></a>비보안 클러스터에 연결

원격 비보안 클러스터에 연결하려면 FabricClient 인스턴스를 만들고 클러스터를 제공합니다.

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

클러스터 내에서 실행되는 코드(예를 들어 Reliable Service에서)의 경우 클러스터 주소를 지정하지 *않고* FabricClient를 만듭니다. FabricClient는 현재 코드가 실행 중인 노드의 로컬 관리 게이트웨이로 연결되어 추가 네트워크 홉이 방지됩니다.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결

클러스터의 노드에는 해당 일반 이름 또는 SAN의 DNS 이름이 [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)에 설정된 [RemoteCommonNames 속성](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials)에 표시되는 유효한 인증서가 있어야 합니다. 이 과정에 따라 클라이언트와 클러스터 노드 간 상호 인증이 가능해집니다.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Azure Active Directory를 사용하여 대화형으로 보안 클러스터에 연결

다음 예제에서는 클라이언트 ID에는 Azure Active Directory를, 서버 ID에는 서버 인증서를 사용합니다.

클러스터를 연결할 때는 대화형 로그인을 위한 대화 상자 창이 자동으로 표시됩니다.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Azure Active Directory를 사용하여 비대화형으로 보안 클러스터에 연결

다음 예제에서는 Microsoft.IdentityModel.Clients.ActiveDirectory 버전 2.19.208020213을 사용합니다.

AAD 토큰 획득에 대한 자세한 내용은 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx)를 참조하세요.

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>사전 메타데이터 지식 없이 Azure Active Directory를 사용하여 보안 클러스터에 연결

다음 예제에서는 비대화형 토큰 획득을 사용하지만 사용자 지정 대화형 토큰 획득 환경을 빌드하는 데 동일한 접근 방식을 사용할 수 있습니다. 토큰 획득에 필요한 Azure Active Directory 메타데이터는 클러스터 구성에서 읽습니다.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer를 사용하여 보안 클러스터에 연결
지정된 클러스터를 위한 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)에 도달하려면 브라우저를 다음으로 연결합니다.

`http://<your-cluster-endpoint>:19080/Explorer`

전체 URL은 Azure 포털의 클러스터 필수 창에서도 사용 가능합니다.

브라우저를 사용하여 Windows 또는 OS X의 보안 클러스터에 연결하기 위해 클라이언트 인증서를 가져올 수 있으며, 그러면 브라우저에서 클러스터에 연결하는 데 사용할 인증서에 대한 메시지를 표시합니다.  Linux 컴퓨터에서는 고급 브라우저 설정을 사용하여 인증서를 가져오고(각 브라우저에 서로 다른 메커니즘이 있는 경우) 디스크상의 인증서 위치로 지정해야 합니다. 자세한 내용은 [클라이언트 인증서 설정](#connectsecureclustersetupclientcert)을 참조하세요.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Azure Active Directory를 사용하여 보안 클러스터에 연결

AAD로 보호되는 클러스터에 연결하려면 브라우저를 다음으로 연결합니다.

`https://<your-cluster-endpoint>:19080/Explorer`

AAD에 로그인하라는 메시지가 자동으로 표시됩니다.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>클라이언트 인증서를 사용하여 보안 클러스터에 연결

인증서로 보호되는 클러스터에 연결하려면 브라우저를 다음으로 연결합니다.

`https://<your-cluster-endpoint>:19080/Explorer`

클라이언트 인증서를 선택하라는 메시지가 자동으로 표시됩니다.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>원격 컴퓨터에서 클라이언트 인증서 설정

클러스터를 보호하려면 적어도 두 개의 인증서(클러스터와 서버 인증서용 한 개 및 클라이언트 액세스용 또 한 개)를 사용해야 합니다.  추가 보조 인증서와 클라이언트 액세스 인증서도 사용하는 것이 좋습니다.  인증서 보안을 사용하여 클라이언트와 클러스터 노드 간 통신을 보호하려면 먼저 클라이언트 인증서를 획득하고 설치해야 합니다. 인증서는 로컬 컴퓨터의 개인(내) 저장소 또는 현재 사용자의 개인 저장소에 설치할 수 있습니다.  클라이언트가 클러스터를 인증할 수 있도록 하려면 서버 인증서의 지문도 필요합니다.

* Windows에서: PFX 파일을 두 번 클릭하고 프롬프트에 따라 개인 저장소 `Certificates - Current User\Personal\Certificates`에 인증서를 설치합니다. PowerShell 명령을 사용할 수도 있습니다.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    자체 서명된 인증서는 사용 중인 컴퓨터의 "신뢰할 수 있는 사용자" 저장소로 가져와야 보안 클러스터에 연결하는 데 사용할 수 있습니다.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac에서: PFX 파일을 두 번 클릭하고 프롬프트에 따라 키 집합에 인증서를 설치합니다.

## <a name="next-steps"></a>다음 단계

* [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
* [Visual Studio에서 Service Fabric 애플리케이션 관리](service-fabric-manage-application-in-visual-studio.md)
* [서비스 패브릭 상태 모델 소개](service-fabric-health-introduction.md)
* [애플리케이션 보안 및 RunAs](service-fabric-application-runas-security.md)
* [Service Fabric CLI 시작](service-fabric-cli.md)
