---
title: 인증서를 사용하여 Windows에서 Azure Service Fabric 클러스터 보호 | Microsoft Docs
description: 클라이언트와 클러스터 간 통신 보호 및 Azure Service Fabric 독립 실행형 클러스터 또는 온-프레미스 클러스터 내 통신 보호에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: ee2ce03fccc3e6556f9d261687edb050c8cfa1cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628152"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>X.509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호
이 문서에서는 독립 실행형 Windows 클러스터의 다양한 노드 간 통신을 보호하는 방법에 대해 설명합니다. 또한 X.509 인증서를 사용하여 이 클러스터에 연결하는 클라이언트를 인증하는 방법에 대해서도 설명합니다. 인증을 통해 권한 있는 사용자만 클러스터 및 배포된 애플리케이션에 액세스하고 관리 작업을 수행할 수 있습니다. 인증서 보안은 클러스터가 만들어지기 전에 클러스터에서 사용되어야 합니다.  

노드 간 보안, 클라이언트-노드 보안 및 역할 기반 액세스 제어와 같은 클러스터 보안에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

## <a name="which-certificates-do-you-need"></a>어떤 인증서가 필요한가요?
우선 클러스터의 노드 중 하나에 [Windows Server 패키지용 Service Fabric을 다운로드합니다](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package). 다운로드한 패키지에서 ClusterConfig.X509.MultiMachine.json 파일을 찾을 수 있습니다. 파일을 열고 properties 섹션에 속한 security 섹션을 검토합니다.

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

이 섹션에서는 독립 실행형 Windows 클러스터를 보호하는 데 필요한 인증서에 대해 설명합니다. 클러스터 인증서를 지정하는 경우 ClusterCredentialType 값을 _X509_로 설정합니다. 외부 연결에 대한 서버 인증서를 지정하는 경우 ServerCredentialType을 _X509_로 설정합니다. 필수는 아니지만 적절히 보호된 클러스터에 대해 이러한 두 인증서를 모두 사용하는 것이 좋습니다. 이러한 값을 *X509*로 설정하면 해당 인증서를 지정해야 합니다. 그러지 않으면 Service Fabric에서 예외를 throw합니다. 일부 시나리오에서는 _ClientCertificateThumbprints_ 또는 _ReverseProxyCertificate_만 지정할 수 있습니다. 이러한 시나리오에서는 _ClusterCredentialType_ 또는 _ServerCredentialType_을 _X509_로 설정할 필요가 없습니다.


> [!NOTE]
> [지문](https://en.wikipedia.org/wiki/Public_key_fingerprint) 은 인증서의 기본 ID입니다. 만들 인증서의 지문을 찾으려면 [인증서의 지문 검색](https://msdn.microsoft.com/library/ms734695.aspx)을 참조하세요.
> 
> 

다음 테이블에서는 클러스터 설치에 필요한 인증서를 나열합니다.

| **CertificateInformation 설정** | **설명** |
| --- | --- |
| ClusterCertificate |테스트 환경에 권장됩니다. 이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다. 업그레이드에는 별도의 두 인증서, 기본 및 보조 인증서를 사용할 수 있습니다. 지문 섹션에서 기본 인증서의 지문 및 ThumbprintSecondary 변수에서 보조 인증서의 지문을 설정합니다. |
| ClusterCertificateCommonNames |프로덕션 환경에 권장됩니다. 이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다. 하나 또는 두 개의 클러스터 인증서 일반 이름을 사용할 수 있습니다. CertificateIssuerThumbprint는 이 인증서의 발급자 지문에 해당합니다. 동일한 일반 이름이 포함된 인증서가 둘 이상 사용되더라도 여러 발급자의 지문을 지정할 수 있습니다.|
| ClusterCertificateIssuerStores |프로덕션 환경에 권장됩니다. 이 인증서는 클러스터 인증서의 발급자에 해당합니다. ClusterCertificateCommonNames에서 발급자 지문을 지정하는 대신 이 섹션에서 발급자 일반 이름 및 해당 저장소 이름을 제공할 수 있습니다.  따라서 클러스터 발급자 인증서를 쉽게 롤오버할 수 있습니다. 둘 이상의 클러스터 인증서를 사용할 경우 여러 발급자를 지정할 수 있습니다. 빈 IssuerCommonName은 X509StoreNames에 지정된 해당 저장소에 있는 모든 인증서를 허용 목록에 추가합니다.|
| ServerCertificate |테스트 환경에 권장됩니다. 이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. 편의상 ClusterCertificate 및 ServerCertificate에 동일한 인증서를 사용하도록 선택할 수 있습니다. 업그레이드에는 별도의 두 서버 인증서, 기본 및 보조 인증서를 사용할 수 있습니다. 지문 섹션에서 기본 인증서의 지문 및 ThumbprintSecondary 변수에서 보조 인증서의 지문을 설정합니다. |
| ServerCertificateCommonNames |프로덕션 환경에 권장됩니다. 이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. CertificateIssuerThumbprint는 이 인증서의 발급자 지문에 해당합니다. 동일한 일반 이름이 포함된 인증서가 둘 이상 사용되더라도 여러 발급자의 지문을 지정할 수 있습니다. 편의상 ClusterCertificateCommonNames 및 ServerCertificateCommonNames에 동일한 인증서를 사용하도록 선택할 수 있습니다. 하나 또는 두 개의 서버 인증서 일반 이름을 사용할 수 있습니다. |
| ServerCertificateIssuerStores |프로덕션 환경에 권장됩니다. 이 인증서는 서버 인증서의 발급자에 해당합니다. ServerCertificateCommonNames에서 발급자 지문을 지정하는 대신 이 섹션에서 발급자 일반 이름 및 해당 저장소 이름을 제공할 수 있습니다.  따라서 서버 발급자 인증서를 쉽게 롤오버할 수 있습니다. 둘 이상의 서버 인증서를 사용하는 경우 여러 발급자를 지정할 수 있습니다. 빈 IssuerCommonName은 X509StoreNames에 지정된 해당 저장소에 있는 모든 인증서를 허용 목록에 추가합니다.|
| ClientCertificateThumbprints |이 인증서 집합을 인증된 클라이언트에 설치합니다. 클러스터에 대한 액세스를 허용하려는 컴퓨터에 다양한 클라이언트 인증서가 설치되었을 수도 있습니다. CertificateThumbprint 변수에 각 인증서의 지문을 설정합니다. IsAdmin을 *true*로 설정하면 이 인증서가 설치된 클라이언트에서 클러스터에 대한 관리자 관리 작업을 수행할 수 있습니다. IsAdmin이 *false*이면 이 인증서가 있는 클라이언트에서 사용자 액세스 권한(일반적으로 읽기 전용)에 허용되는 작업만 수행할 수 있습니다. 역할에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)](service-fabric-cluster-security.md#role-based-access-control-rbac)를 참조하세요. |
| ClientCertificateCommonNames |CertificateCommonName에 대한 첫 번째 클라이언트 인증서의 일반 이름을 설정합니다. CertificateIssuerThumbprint는 이 인증서의 발급자 지문입니다. 일반 이름과 발급자에 대한 자세한 내용은 [인증서 작업](https://msdn.microsoft.com/library/ms731899.aspx)을 참조하세요. |
| ClientCertificateIssuerStores |프로덕션 환경에 권장됩니다. 이 인증서는 클라이언트 인증서의 발급자에 해당합니다(관리자 및 비관리자 역할 둘 다). ClientCertificateCommonNames에서 발급자 지문을 지정하는 대신 이 섹션에서 발급자 일반 이름 및 해당 저장소 이름을 제공할 수 있습니다.  따라서 클라이언트 발급자 인증서를 쉽게 롤오버할 수 있습니다. 둘 이상의 클라이언트 인증서를 사용하는 경우 여러 발급자를 지정할 수 있습니다. 빈 IssuerCommonName은 X509StoreNames에 지정된 해당 저장소에 있는 모든 인증서를 허용 목록에 추가합니다.|
| ReverseProxyCertificate |테스트 환경에 권장됩니다. 이 선택적 인증서는 [역방향 프록시](service-fabric-reverseproxy.md)를 보호하려는 경우에 지정할 수 있습니다. 이 인증서를 사용하는 경우 reverseProxyEndpointPort가 nodeTypes로 설정되어 있는지 확인합니다. |
| ReverseProxyCertificateCommonNames |프로덕션 환경에 권장됩니다. 이 선택적 인증서는 [역방향 프록시](service-fabric-reverseproxy.md)를 보호하려는 경우에 지정할 수 있습니다. 이 인증서를 사용하는 경우 reverseProxyEndpointPort가 nodeTypes로 설정되어 있는지 확인합니다. |

다음은 클러스터, 서버 및 클라이언트 인증서가 제공된 클러스터 구성 예제입니다. cluster/server/reverseProxy 인증서의 경우 동일한 인증서 종류에 지문과 일반 이름을 함께 구성할 수 없습니다.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>인증서 롤오버
지문 대신 인증서 일반 이름을 사용하는 경우 인증서 롤오버에 클러스터 구성 업그레이드가 필요하지 않습니다. 발급자 지문 업그레이드의 경우 새 지문 목록이 이전 목록과 어긋나는지 확인합니다. 먼저 새 발급자 지문을 사용하여 구성 업그레이드를 수행한 다음, 저장소에서 새 인증서(클러스터/서버 인증서 및 발급자 인증서 둘 모두)를 설치해야 합니다. 새 발급자 인증서를 설치한 후 적어도 두 시간 동안은 이전 발급자 인증서를 인증서 저장소에 보관합니다.
발급자 저장소를 사용하는 경우에는 발급자 인증서를 롤오버하는 데 구성 업그레이드가 필요하지 않습니다. 해당 인증서 저장소에 만료 날짜가 더 긴 새 발급자 인증서를 설치하고 몇 시간 후에 이전 발급자 인증서를 제거합니다.

## <a name="acquire-the-x509-certificates"></a>X.509 인증서를 획득합니다.
클러스터 내 통신을 보호하려면 먼저 클러스터 노드에 대한 X.509 인증서를 가져와야 합니다. 또한 이 클러스터에 대한 연결을 권한 있는 컴퓨터/사용자로 제한하려면 클라이언트 컴퓨터에 대한 인증서를 가져와서 설치해야 합니다.

프로덕션 워크로드를 실행하는 클러스터의 경우 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)로 서명된 X.509 인증서를 사용하여 클러스터를 보호합니다. 이러한 인증서를 가져오는 방법에 대한 자세한 내용은 [인증서를 가져오는 방법](https://msdn.microsoft.com/library/aa702761.aspx)을 참조하세요.

테스트 목적으로 사용하는 클러스터의 경우 자체 서명된 인증서를 사용하도록 선택할 수 있습니다.

## <a name="optional-create-a-self-signed-certificate"></a>선택 사항: 자체 서명된 인증서 만들기
올바르게 보호할 수 있는 자체 서명된 인증서를 만드는 한 가지 방법은 C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure 디렉터리의 Service Fabric SDK 폴더에 있는 CertSetup.ps1 스크립트를 사용하는 것입니다. 이 파일을 편집하여 인증서의 기본 이름을 변경합니다. (CN=ServiceFabricDevClusterCert 값을 찾습니다.) 이 스크립트를 `.\CertSetup.ps1 -Install`로 실행합니다.

이제 보호된 암호로 .pfx 파일에 인증서를 내보냅니다. 먼저 인증서의 지문을 가져옵니다. 
1. **시작** 메뉴에서 **컴퓨터 인증서 관리**를 실행합니다. 

2. **로컬 컴퓨터/개인** 폴더로 이동하고 만든 인증서를 찾습니다. 

3. 인증서를 두 번 클릭하여 열고, **세부 정보** 탭을 선택하고, **지문** 필드로 스크롤합니다. 

4. 공백을 제거하고, 다음 PowerShell 명령에 지문 값을 복사합니다. 

5. `String` 값을 적절한 보안 암호로 변경하여 보호하고, PowerShell에서 다음을 실행합니다.

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. 컴퓨터에 설치된 인증서의 세부 정보를 확인하려면 다음 PowerShell 명령을 실행합니다.

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

또는 Azure 구독이 있는 경우 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)의 단계를 따릅니다.

## <a name="install-the-certificates"></a>인증서를 설치합니다.
인증서가 있으면 클러스터 노드에 해당 인증서를 설치할 수 있습니다. 노드에 최신 Windows PowerShell 3.x가 설치되어 있어야 합니다. 클러스터/서버 인증서 및 보조 인증서 모두에 대해 각 노드에서 이러한 단계를 반복합니다.

1. .pfx 파일을 노드에 복사합니다.

2. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 입력합니다. *$pswd*를 이 인증서를 만드는 데 사용한 암호로 바꿉니다. *$PfxFilePath*를 이 노드에 복사된 .pfx의 전체 경로로 바꿉니다.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. 이제 네트워크 서비스 계정에서 실행되는 Service Fabric 프로세스가 다음 스크립트를 실행하여 사용할 수 있도록 이 인증서에 액세스 제어를 설정해야 합니다. 서비스 계정에 인증서의 지문 및 **네트워크 서비스**를 제공합니다. **시작** > **컴퓨터 인증서 관리**에서 인증서를 열고 **모든 작업** > **개인 키 관리**를 보고 인증서의 ACL이 올바른지 확인할 수 있습니다.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. 각 서버 인증서에 대해 이전 단계를 반복합니다. 또한 이러한 단계를 사용하여 클러스터에 대한 액세스를 허용하려는 컴퓨터에 클라이언트 인증서를 설치할 수 있습니다.

## <a name="create-the-secure-cluster"></a>보안 클러스터 만들기
ClusterConfig.X509.MultiMachine.json 파일의 security 섹션을 구성한 후에 [클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) 섹션으로 이동하여 노드를 구성하고 독립 실행형 클러스터를 만들 수 있습니다. 클러스터를 만드는 동안 ClusterConfig.X509.MultiMachine.json 파일을 사용해야 합니다. 예를 들어, 명령은 다음과 같을 수 있습니다.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

독립 실행형 Windows 보안 클러스터를 성공적으로 실행하고 인증된 클라이언트를 연결하도록 설정했으면 [PowerShell을 사용하여 보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) 섹션의 단계에 따라 연결합니다. 예를 들면 다음과 같습니다.

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

그런 다음 다른 PowerShell 명령을 실행하면 이 클러스터에 대해 작업할 수 있습니다. 예를 들어 [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps)를 실행하여 이 보안 클러스터의 노드 목록을 표시할 수 있습니다.


클러스터를 제거하려면 Service Fabric 패키지를 다운로드한 클러스터의 노드에 연결하고, 명령줄을 열고, 패키지 폴더로 이동합니다. 이제 다음 명령을 실행합니다.

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> 인증서 구성이 올바르지 않으면 배포 중에 클러스터가 시작되지 않을 수 있습니다. 보안 문제를 자체적으로 진단하려면 **애플리케이션 및 서비스 로그** > **Microsoft-Service Fabric**에서 이벤트 뷰어 그룹을 찾아보세요.
> 
> 

