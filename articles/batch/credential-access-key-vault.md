---
title: Batch를 사용하여 Key Vault에 안전하게 액세스
description: Azure Batch를 사용하여 Key Vault의 자격 증명에 프로그래밍 방식으로 액세스하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6938d0fcd2357efcf03053b0c9b2bde3954270b7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079441"
---
# <a name="securely-access-key-vault-with-batch"></a>Batch를 사용하여 Key Vault에 안전하게 액세스

이 문서에서는 Azure Key Vault에 저장된 자격 증명에 안전하게 액세스하기 위해 Batch 노드를 설정하는 방법을 알아봅니다. Key Vault에 관리자 자격 증명을 입력한 다음, 스크립트에서 Key Vault 액세스를 위한 자격 증명을 하드 코딩할 이유는 없습니다. 이 솔루션은 Batch 노드에 Key Vault에 대한 액세스 권한을 부여하는 인증서를 사용하기 위한 것입니다. 몇 단계를 통해 Batch를 위한 안전한 키 스토리지를 구현할 수 있습니다.

Batch 노드에서 Azure Key Vault에 대해 인증하려면 다음이 필요합니다.

- Azure AD(Azure Active Directory) 자격 증명
- 인증서
- Batch 계정
- 노드가 하나 이상 있는 Batch 풀

## <a name="obtain-a-certificate"></a>인증서 받기

인증서가 아직 없는 경우 인증서를 받는 가장 간단한 방법은 `makecert` 명령줄 도구를 사용하여 자체 서명 인증서를 생성하는 것입니다.

일반적으로 `C:\Program Files (x86)\Windows Kits\10\bin\<arch>` 경로에서 `makecert`를 찾을 수 있습니다. 관리자 권한으로 명령 프롬프트를 열고 다음 예제를 사용하여 `makecert`로 이동합니다.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

그런 다음, `makecert` 도구를 사용하여 `batchcertificate.cer` 및 `batchcertificate.pvk`라는 자체 서명 인증서 파일을 만듭니다. 사용된 CN(일반 이름)은 이 애플리케이션에 중요하지 않지만 인증서의 용도를 파악하는 데 유용합니다.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch에는 `.pfx` 파일이 필요합니다. [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) 도구를 사용하여 `makecert`에서 만든 `.cer` 및 `.pvk` 파일을 단일 `.pfx` 파일로 변환합니다.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기

Key Vault에 대한 액세스 권한은 **사용자** 또는 **서비스 사용자**에게 부여됩니다. 프로그래밍 방식으로 Key Vault에 액세스하려면 이전 단계에서 만든 인증서와 서비스 사용자를 사용합니다.

Azure 서비스 사용자에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

> [!NOTE]
> 서비스 사용자는 Key Vault와 동일한 Azure AD 테넌트에 있어야 합니다.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

애플리케이션에 대한 URL은 Key Vault 액세스에만 사용하므로 중요하지 않습니다.

## <a name="grant-rights-to-key-vault"></a>Key Vault에 대한 권한 부여

이전 단계에서 만든 서비스 사용자는 Key Vault에서 비밀을 검색할 수 있는 권한이 필요합니다. 권한은 Azure Portal 또는 아래의 PowerShell 명령을 통해 부여할 수 있습니다.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Batch 계정에 인증서 할당

Batch 풀을 만든 다음, 풀에서 인증서 탭으로 이동하여 만든 인증서를 할당합니다. 이제 인증서가 모든 Batch 노드에 있습니다.

다음으로 Batch 계정에 인증서를 할당해야 합니다. 인증서를 계정에 할당하면 해당 인증서를 풀에 할당한 다음, 노드에 할당할 수 있습니다. 이를 위한 가장 간단한 방법은 포털에서 Batch 계정으로 이동한 다음, **인증서**로 이동하고 **추가**를 선택하는 것입니다. [인증서 가져오기](#obtain-a-certificate)에서 생성한 `.pfx` 파일을 업로드하고 암호를 입력합니다. 완료되면 인증서가 목록에 추가되고 지문을 확인할 수 있습니다.

이제 Batch 풀을 만들 때 풀 내의 **인증서**로 이동하여 해당 풀에 만든 인증서를 할당할 수 있습니다. 이렇게 하려면 저장소 위치에 대해 **LocalMachine**을 선택해야 합니다. 인증서는 풀의 모든 Batch 노드에 로드됩니다.

## <a name="install-azure-powershell"></a>Azure Powershell 설치

노드에서 PowerShell 스크립트를 사용하여 Key Vault에 액세스하려는 경우 Azure PowerShell 라이브러리를 설치해야 합니다. 몇 가지 방법으로 설치할 수 있습니다. 노드에 WMF(Windows Management Framework) 5가 설치되어 있다면 install-module 명령을 사용하여 다운로드할 수 있습니다. WMF 5가 없는 노드를 사용하는 경우 가장 간단한 설치 방법은 Azure PowerShell `.msi` 파일을 Batch 파일과 번들로 묶은 다음, Batch 시작 스크립트의 첫 번째 부분으로 설치 관리자를 호출하는 것입니다. 자세한 내용은 다음 예제를 참조하세요.

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault에 액세스합니다.

이제 Batch 노드에서 실행되는 스크립트에서 Key Vault에 액세스하기 위한 준비가 끝났습니다. 스크립트에서 Key Vault에 액세스하려면 스크립트가 인증서를 사용하여 Azure AD에 대해 인증하기만 하면 됩니다. PowerShell에서 이 작업을 수행하려면 다음 예제 명령을 사용합니다. **지문**, **앱 ID**(서비스 사용자의 ID) 및 **테넌트 ID**(서비스 사용자가 위치한 테넌트)에 적절한 GUID를 지정합니다.

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

인증 후에는 일반적인 방식으로 KeyVault에 액세스합니다.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

스크립트에서 사용하는 자격 증명입니다.
