---
title: Powershell의 클러스터에 애플리케이션 인증서 추가
description: Azure PowerShell 스크립트 샘플 - Service Fabric 클러스터에 애플리케이션 인증서 추가
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 3d26bc33ca6679b80b858f50d212366d326207fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078982"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 애플리케이션 인증서 추가

이 샘플 스크립트는 Key Vault에서 인증서를 만든 다음, 클러스터가 실행되는 가상 머신 확장 집합 중 하나에 배포하는 방법을 안내합니다. 이 시나리오는 Service Fabric을 직접 사용하지 않고 Key Vault 및 가상 머신 확장 집합에 따라 달라집니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 

## <a name="create-a-certificate-in-key-vault"></a>Key Vault에서 인증서 만들기

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>또는 Key Vault에 기존 인증서 업로드

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>인증서를 사용하여 가상 머신 확장 집합 프로필 업데이트

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>가상 머신 확장 집합 업데이트
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> 클러스터의 여러 노드 형식에 인증서를 배치하려는 경우 인증서를 가지고 있어야 하는 각 노드 형식에 대해 이 스크립트의 두 번째와 세 번째 부분을 반복해야 합니다.

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | 인증서를 나타내는 메모리 내 정책 만들기 |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Key Vault 인증서에 정책 배포 |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| Key Vault 비밀에 정책 배포 |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | VM에서 인증서를 나타내는 메모리 내 구성 만들기 |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | 가상 머신 확장 집합의 메모리 내 정의에 인증서 추가 |
| [업데이트 AzVmss](/powershell/module/az.compute/Update-AzVmss) | 가상 머신 확장 집합의 새 정의 배포 |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
