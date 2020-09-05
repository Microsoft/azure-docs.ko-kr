---
title: Azure Service Fabric 클러스터 인증서 롤오버
description: 인증서 일반 이름으로 식별 되는 Service Fabric 클러스터 인증서를 롤오버 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853343"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서를 수동으로 롤오버
Service Fabric 클러스터 인증서가 만료될 시간이 다가오면 인증서를 업데이트해야 합니다.  클러스터가 지문 대신 [일반 이름을 기준으로 인증서를 사용하도록 설정](service-fabric-cluster-change-cert-thumbprint-to-cn.md)된 경우 인증서를 롤오버하는 방법은 간단합니다.  인증 기관에서 새 만료 날짜와 함께 새 인증서를 얻습니다.  자체 서명된 인증서를 사용하면 프로덕션 Service Fabric 클러스터가 Azure Portal 클러스터 만들기 워크플로 중에 생성된 인증서를 포함하는 기능이 지원되지 않습니다. 새 인증서의 일반 이름이 이전 인증서와 동일해야 합니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

호스트에 유효한 인증서가 두 개 이상 설치된 경우 Service Fabric 클러스터는 만료 날짜가 가장 많이 남은 선언된 인증서를 자동으로 사용합니다. 모범 사례는 Resource Manager 템플릿을 사용하여 Azure 리소스를 프로비전하는 것입니다. 비 프로덕션 환경의 경우 다음 스크립트를 사용하여 새 인증서를 키 자격 증명 모음에 업로드한 다음, 가상 머신 확장 집합에 인증서를 설치할 수 있습니다. 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Computes Virtual Machine Scale Set Secrets에서는 두 개의 개별 비밀에 같은 리소스 ID를 사용할 수 없습니다. 각 비밀은 버전이 지정된 고유한 리소스이기 때문입니다. 

## <a name="next-steps"></a>다음 단계

* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아봅니다.
* [클러스터 인증서 업데이트 및 관리](service-fabric-cluster-security-update-certs-azure.md)
