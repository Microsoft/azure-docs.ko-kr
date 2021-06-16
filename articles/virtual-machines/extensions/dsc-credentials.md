---
title: 필요한 상태 구성을 사용하여 Azure에 자격 증명 전달
description: PowerShell DSC(필요한 상태 구성)를 사용하여 Azure 가상 머신에 자격 증명을 안전하게 전달하는 방법을 알아봅니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ad4b22916b5e602367fa26359e92ea4f52c05b13
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663041"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Azure DSCExtension 처리기에 자격 증명 전달

이 문서에서는 Azure의 DSC(필요한 상태 구성) 확장을 설명합니다. DSC 확장 처리기에 대한 개요는 [Azure 필요한 상태 구성 확장 처리기 소개](dsc-overview.md)를 참조하세요.

 

## <a name="pass-in-credentials"></a>자격 증명 전달

구성 프로세스의 일부로, 사용자 계정을 설정하거나, 서비스에 액세스하거나, 사용자 컨텍스트에서 프로그램을 설치해야 할 수 있습니다. 이러한 작업을 수행하려면 자격 증명을 제공해야 합니다.

DSC를 사용하여 매개 변수가 있는 구성을 설정할 수 있습니다. 매개 변수가 있는 구성에서 자격 증명은 구성에 전달되어 .mof 파일에 안전하게 저장됩니다. Azure 확장 처리기는 인증서 자동 관리를 제공하여 자격 증명 관리를 간소화합니다.

아래 DSC 구성 스크립트는 지정된 암호로 로컬 사용자 계정을 만듭니다.

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

**node localhost** 를 구성의 일부로 포함하는 것이 중요합니다. 확장 처리기는 특히 **node localhost** 문을 찾습니다. 이 구문이 없으면 다음 단계가 작동하지 않습니다. typecast **[PsCredential]** 을 포함하는 것도 중요합니다. 이 특정 형식은 확장을 트리거하여 자격 증명을 암호화합니다.

이 스크립트를 Azure Blob Storage에 게시하려면:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Azure DSC 확장을 설정하고 자격 증명을 제공하려면:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>자격 증명의 보안 유지 방법

이 코드를 실행하면 자격 증명을 묻는 메시지가 나타납니다. 자격 증명이 제공되면 메모리에 간단히 저장됩니다. **Set-AzVMDscExtension** cmdlet을 사용하여 자격 증명이 게시되면, 자격 증명은 HTTPS를 통해 VM에 전송됩니다. VM에서 Azure는 로컬 VM 자격 증명을 사용하여 디스크에 암호화된 자격 증명을 저장합니다. 자격 증명은 메모리에서 간단히 암호 해독된 다음, DSC로 전달하기 위해 다시 암호화됩니다.

이러한 프로세스는 [확장 처리기 없이 보안 구성을 사용](/powershell/scripting/dsc/pull-server/securemof)하는 방법과는 다릅니다. Azure 환경에는 자격 증명을 통해 구성 데이터를 안전하게 전송하는 방법이 제공됩니다. DSC 확장 처리기를 사용할 때는 **ConfigurationData** 에 **$CertificatePath** 또는 **$CertificateID**/ **$Thumbprint** 항목을 제공할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure DSC 확장 처리기에 대한 소개](dsc-overview.md)를 참조하세요.
- [DSC 확장에 대한 Azure Resource Manager 템플릿](dsc-template.md)을 검토합니다.
- PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터](/powershell/scripting/dsc/overview/overview)를 참조하세요.
- PowerShell DSC로 관리할 수 있는 더 많은 기능 및 더 많은 DSC 리소스는 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)를 참조하세요.
