---
title: Azure Service Fabric 보안 모범 사례
description: Azure Service Fabric 클러스터와 응용 프로그램의 보안을 유지 하기 위한 모범 사례 및 디자인 고려 사항입니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551763"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric 보안 

[Azure 보안 모범 사례](https://docs.microsoft.com/azure/security/)에 대한 자세한 내용은 [Azure Service Fabric 보안 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)를 참조하세요.

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)는 Azure Service Fabric 애플리케이션 및 클러스터에 추천되는 비밀 관리 서비스입니다.
> [!NOTE]
> Virtual Machine Scale Set에 Key Vault의 인증서/비밀을 Virtual Machine Scale Set 비밀로 배포하는 경우 Key Vault와 Virtual Machine Scale Set를 함께 배치해야 합니다.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>인증 기관에서 발급한 Service Fabric 인증서 만들기

Azure Key Vault 인증서는 만들거나 Key Vault로 가져올 수 있습니다. Key Vault 인증서가 만들어지면 프라이빗 키가 Key Vault 내에 만들어지며 인증서 소유자에게 공개되지 않습니다. Key Vault에 인증서를 만드는 방법은 다음과 같습니다.

- 자체 서명된 인증서를 만들어퍼블릭-프라이빗 키 쌍을 만들고 이를 인증서와 연결합니다. 인증서는 자체 키에 의해 서명됩니다. 
- 새 인증서를 수동으로 만들어퍼블릭-프라이빗 키 쌍을 만들고 X.509 인증서 서명 요청을 생성합니다. 등록 기관 또는 인증 기관에서 서명 요청에 서명할 수 있습니다. 서명된 x509 인증서를 보류 중인 키 쌍과 병합하여 Key Vault에서 KV 인증서를 완료할 수 있습니다. 이 방법은 더 많은 단계를 필요로 하지만 프라이빗 키가 Key Vault에서 만들어지고 또 Key Vault로 제한되기 때문에 보안이 더욱 강화됩니다. 이 방법을 아래 다이어그램에서 설명합니다. 

자세한 내용은 [Azure Key Vault 인증서를 만드는 방법](https://docs.microsoft.com/azure/key-vault/create-certificate)을 검토하세요.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Service Fabric 클러스터 가상 머신 확장 집합에 Key Vault 인증서 배포

인증서를 함께 배치된 키 자격 증명 모음에서 Virtual Machine Scale Set로 배포하려면 [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) Virtual Machine Scale Set를 사용합니다. Resource Manager 템플릿 속성은 다음과 같습니다.

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Resource Manager 템플릿을 배포하려면 자격 증명 모음을 사용하도록 설정해야 합니다.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Service Fabric 클러스터의 인증서에 ACL(액세스 제어 목록) 적용

[Virtual Machine Scale Set 확장](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) 게시자인 Microsoft.Azure.ServiceFabric은 노드 보안을 구성하는 데 사용됩니다.
Service Fabric 클러스터 프로세스에 대한 인증서에 ACL을 적용하려면 다음 Resource Manager 템플릿 속성을 사용합니다.

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>일반 이름으로 Service Fabric 클러스터 인증서 보호

인증서 `Common Name`으로 Service Fabric 클러스터를 보호하려면 다음과 같이 [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames) Resource Manager 템플릿 속성을 사용합니다.

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric 클러스터는 호스트의 인증서 저장소에서 찾은 첫 번째 유효한 인증서를 사용합니다. Windows에서는 일반 이름 및 발급자 지문과 일치하는 최신 만료 날짜가 있는 인증서입니다.

*\<YOUR SUBDOMAIN\>.cloudapp.azure.com 또는 \<YOUR SUBDOMAIN\>.trafficmanager.net과 같은 Azure 도메인은 Microsoft에서 소유하고 있습니다. 인증 기관은 인증되지 않은 사용자에게 도메인에 대한 인증서를 발급하지 않습니다. 대부분의 사용자는 인증 기관에서 해당 일반 이름의 인증서를 발급할 수 있도록 등록 기관으로부터 도메인을 구입하거나 권한이 있는 도메인 관리자여야 합니다.

도메인을 Microsoft IP 주소로 확인하도록 DNS 서비스를 구성하는 방법에 대한 자세한 내용은 [도메인을 호스팅하도록 Azure DNS를 구성하는 방법](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)을 검토하세요.

> [!NOTE]
> 도메인 이름 서버를 Azure DNS 영역 이름 서버에 위임한 후 다음 두 레코드를 DNS 영역에 추가하세요.
> - 사용자 지정 도메인에서 확인할 모든 IP 주소에 대한 `Alias record set`가 아닌 APEX 도메인에 대한 'A' 레코드입니다.
> - `Alias record set`가 아닌 프로비전한 Microsoft 하위 도메인에 대한 'C' 레코드입니다. 예를 들어 Traffic Manager 또는 Load Balancer의 DNS 이름을 사용할 수 있습니다.

Service Fabric 클러스터에 대한 사용자 지정 DNS 이름(`"managementEndpoint"`)을 표시하도록 포털을 업데이트하려면 다음 Service Fabric 클러스터 Resource Manager 템플릿 속성을 업데이트합니다.

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Service Fabric 패키지의 비밀 값 암호화

Service Fabric 패키지에서 암호화되는 공통 값에는 ACR(Azure Container Registry) 자격 증명, 환경 변수, 설정 및 Azure 볼륨 플러그 인 스토리지 계정 키가 포함됩니다.

[Windows 클러스터에서 암호화 인증서를 설정하고 비밀을 암호화](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)하려면 다음을 수행합니다.

비밀을 암호화하기 위한 자체 서명된 인증서를 생성합니다.

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

[Service Fabric 클러스터 가상 머신 확장 집합에 Key Vault 인증서 배포](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets)의 지침에 따라 Key Vault 인증서를 Service Fabric 클러스터의 Virtual Machine Scale Sets에 배포합니다.

다음 PowerShell 명령을 사용하여 비밀을 암호화한 다음, Service Fabric 애플리케이션 매니페스트를 암호화된 값으로 업데이트합니다.

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

[Linux 클러스터에서 암호화 인증서를 설정하고 비밀을 암호화](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)하려면 다음을 수행합니다.

비밀을 암호화하기 위한 자체 서명된 인증서를 생성합니다.

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

[Service Fabric 클러스터 가상 머신 확장 집합에 Key Vault 인증서 배포](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets)의 지침을 Service Fabric 클러스터의 Virtual Machine Scale Sets에 사용합니다.

다음 명령을 사용하여 비밀을 암호화한 다음, Service Fabric 애플리케이션 매니페스트를 암호화된 값으로 업데이트합니다.

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

보호되는 값이 암호화되면 [Service Fabric 애플리케이션에서 암호화된 비밀을 지정](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)하고 [서비스 코드에서 암호화된 비밀을 해독](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code)합니다.

## <a name="include-certificate-in-service-fabric-applications"></a>Service Fabric 응용 프로그램에 인증서 포함

응용 프로그램에 비밀에 대 한 액세스 권한을 부여 하려면 응용 프로그램 매니페스트에 **SecretsCertificate** 요소를 추가 하 여 인증서를 포함 합니다.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>MSI(관리 서비스 ID)를 사용하여 Azure 리소스에 Service Fabric 애플리케이션 인증

Azure 리소스에 대한 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)을 참조하세요.
Azure Service Fabric 클러스터는 [관리 서비스 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)를 지원하는 Virtual Machine Scale Sets에서 호스팅됩니다.
MSI를 인증하는 데 사용할 수 있는 서비스 목록을 가져오려면 [Azure Active Directory 인증을 지원하는 Azure 서비스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication)를 참조하세요.


가상 머신 확장 집합 또는 기존 가상 머신 확장 집합을 만드는 동안 시스템 할당 관리 ID를 사용하도록 설정하려면 다음 `"Microsoft.Compute/virtualMachinesScaleSets"` 속성을 선언합니다.

```json
"identity": { 
    "type": "SystemAssigned"
}
```
자세한 내용은 [Azure 리소스에 대한 관리 ID란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)을 참조하세요.

[사용자 할당 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)를 만든 경우 템플릿에서 다음 리소스를 선언하여 가상 머신 확장 집합에 할당합니다. `\<USERASSIGNEDIDENTITYNAME\>`을 직접 만든 사용자 할당 관리 ID의 이름으로 바꿉니다.

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Service Fabric 애플리케이션에서 관리 ID를 사용하려면 먼저 인증하는 데 필요한 Azure 리소스에 권한을 부여해야 합니다.
다음 명령은 Azure 리소스에 대한 액세스 권한을 부여합니다.

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

Service Fabric 응용 프로그램 코드에서 나머지를 모두 다음과 비슷하게 만들어 Azure Resource Manager에 대 한 [액세스 토큰을 가져옵니다](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) .

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

그러면 Service Fabric 앱에서 액세스 토큰을 사용하여 Active Directory를 지원하는 Azure 리소스를 인증할 수 있습니다.
다음 예제에서는 Cosmos DB 리소스에 대해 이 작업을 수행하는 방법을 보여 줍니다.

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows 보안 기준
[기준을 직접 만드는 것이 아니라 Microsoft 보안 기준과 같이 널리 알려져 있고 잘 테스트 된 업계 표준 구성을 구현 하는 것이 좋습니다](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines). Virtual Machine Scale Sets에서 프로 비전 하는 옵션은 Azure DSC (필요한 상태 구성) 확장 처리기를 사용 하 여 Vm이 온라인 상태가 되 면 프로덕션 소프트웨어를 실행 하는 Vm을 구성 하는 것입니다.

## <a name="azure-firewall"></a>Azure Firewall
[Azure 방화벽은 azure Virtual Network 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다. 이는 기본 제공 고가용성 및 무제한 클라우드 확장성이 있는 완전 한 상태 저장 방화벽으로 서의 서비스입니다.](https://docs.microsoft.com/azure/firewall/overview) 이를 통해 와일드 카드를 포함 하 여 지정 된 FQDN (정규화 된 도메인 이름) 목록으로 아웃 바운드 HTTP/S 트래픽을 제한할 수 있습니다. 이 기능에는 SSL 종료가 필요하지 않습니다. Windows 업데이트에 대 한 [Azure 방화벽 FQDN 태그](https://docs.microsoft.com/azure/firewall/fqdn-tags) 를 활용 하 고 Microsoft Windows 업데이트 엔드포인트에 대 한 네트워크 트래픽을 방화벽을 통해 이동할 수 있도록 하는 것이 좋습니다. [템플릿을 사용 하 여 Azure 방화벽 배포](https://docs.microsoft.com/azure/firewall/deploy-template) 리소스 템플릿 정의에 대 한 샘플을 제공 합니다. Service Fabric 응용 프로그램에 공통적인 방화벽 규칙은 클러스터 가상 네트워크에 대해 다음을 허용 하는 것입니다.

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

이러한 방화벽 규칙은 가상 네트워크에서 허용 되는 대상으로 ServiceFabric 및 Storage를 포함 하는 허용 되는 아웃 바운드 네트워크 보안 그룹을 보완 합니다.

## <a name="tls-12"></a>TLS 1.2
[TSG](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

기본적으로 Windows Defender 바이러스 백신은 Windows Server 2016에 설치됩니다. 자세한 내용은 [Windows Server 2016의 Windows Defender 바이러스 백신](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)을 참조하세요. 사용자 인터페이스가 기본적으로 일부 SKU에 설치되지만 필요하지는 않습니다. Windows Defender에서 발생하는 성능 영향 및 리소스 사용 오버헤드를 줄이고 보안 정책에서 오픈 소스 소프트웨어에 대한 프로세스와 경로를 제외할 수 있도록 하려면, 다음 Virtual Machine Scale Set 확장 Resource Manager 템플릿 속성을 선언하여 검사에서 Service Fabric 클러스터를 제외시킵니다.


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Windows Defender를 사용하지 않는 경우 구성 규칙에 대한 맬웨어 방지 설명서를 참조하세요. Linux에서는 Windows Defender가 지원되지 않습니다.

## <a name="platform-isolation"></a>플랫폼 격리
기본적으로 Service Fabric 응용 프로그램에는 Service Fabric 런타임 자체에 대 한 액세스 권한이 부여 됩니다. 즉, 응용 프로그램 및 패브릭 파일에 해당 하는 호스트의 파일 경로를 가리키는 [환경 변수](service-fabric-environment-variables-reference.md) , 응용 프로그램별 요청을 수락 하는 프로세스 간 통신 엔드포인트, 패브릭에서 응용 프로그램을 인증 하는 데 사용 해야 하는 클라이언트 인증서를 각기 다른 형식으로 매니페스트 합니다. 서비스에서 신뢰 하지 않는 코드를 대비해 야 하는 경우 명시적으로 필요 하지 않은 경우에는 SF 런타임에 대 한이 액세스를 사용 하지 않도록 설정 하는 것이 좋습니다. 응용 프로그램 매니페스트의 정책 섹션에서 다음 선언을 사용 하 여 런타임에 대 한 액세스를 제거 합니다. 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>다음 단계

* Windows server를 실행 하는 Vm 또는 컴퓨터에서 클러스터 만들기: [Windows server에 대 한 클러스터 만들기를 Service Fabric](service-fabric-cluster-creation-for-windows-server.md)합니다.
* Vm 또는 Linux를 실행 하는 컴퓨터에서 클러스터 만들기: [linux 클러스터를 만듭니다](service-fabric-cluster-creation-via-portal.md).
* [Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
