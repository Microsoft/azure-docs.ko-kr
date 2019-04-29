---
title: 다중 테넌트 호스팅 권한으로 Azure에서 Windows 10을 배포하는 방법
description: Azure에 온-프레미스 라이선스를 가져오기 위해 Windows Software Assurance 혜택을 최대화하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 1/24/2018
ms.author: xujing
ms.openlocfilehash: 7f43528c55cd22c2649ca0f1208da6f41695b98e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485480"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>다중 테넌트 호스팅 권한으로 Azure에서 Windows 10을 배포하는 방법 
Windows 10 Enterprise E3/E5 사용자 단위 또는 Windows Virtual Desktop Access 사용자 단위(사용자 구독 라이선스 또는 추가 기능 사용자 구독 라이선스)를 사용하는 사용자의 경우, Windows 10용 다중 테넌트 호스팅 권한을 사용하면 클라우드로 Windows 10 라이선스를 가져오고, 다른 라이선스에 비용을 지불하지 않으면서 Azure에서 Windows 10 Virtual Machines를 실행할 수 있습니다. 자세한 내용은 [Windows 10용 다중 테넌트 호스팅](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)을 참조하세요.

> [!NOTE]
> 이 문서에서는 Azure Marketplace에서 Windows 10 Pro Desktop 이미지에 대한 라이선스 혜택을 구현하는 방법을 보여줍니다.
> - MSDN 구독용 Azure Marketplace의 Windows 7, 8.1, 10 Enterprise(x64) 이미지는 [개발/테스트 시나리오용 Azure의 Windows 클라이언트](client-images.md)를 참조하세요.
> - Windows Server 라이선스 혜택은 [Windows Server 이미지에 Azure Hybrid를 사용하는 이점](hybrid-use-benefit-licensing.md)을 참조하세요.
>

## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Azure Marketplace에서 Windows 10 이미지 배포 
Powershell, CLI 및 Azure Resource Manager 템플릿 배포의 경우 Windows 10 이미지를 다음 publishername, 제품, SKU로 찾을 수 있습니다.

| OS  |      PublisherName      |  제안 | SKU |
|:----------|:-------------:|:------|:------|
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS2-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS2-ProN  |
| Windows 10 Pro    | MicrosoftWindowsDesktop | Windows-10  | RS3-Pro   |
| Windows 10 Pro N  | MicrosoftWindowsDesktop | Windows-10  | RS3-ProN  |

## <a name="uploading-windows-10-vhd-to-azure"></a>Azure에 Windows 10 VHD 업로드
일반화된 Windows 10 VHD를 업로드하는 경우 Windows 10에는 기본적으로 활성화된 기본 제공 관리자 계정이 없습니다. 기본 제공 관리자 계정을 사용하도록 설정하려면 사용자 지정 스크립트 확장의 일환으로 다음 명령을 포함합니다.

```powershell
Net user <username> /active:yes
```

다음 powershell 코드 조각은 기본 제공 관리자를 포함하여 모든 관리자 계정을 활성화하기 위한 것입니다. 이 예제는 기본 제공 관리자 사용자 이름을 알 수 없는 경우에 유용합니다.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
자세한 내용은 다음을 참조하세요. 
* [Azure에 VHD를 업로드하는 방법](upload-generalized-managed.md)
* [Azure에 업로드할 Windows VHD를 준비하는 방법](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>다중 테넌트 호스팅 권한으로 Windows 10 배포
먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azure/overview)했는지 확인합니다. VHD를 준비했으면 다음과 같이 `Add-AzVhd` cmdlet을 사용하여 Azure Storage 계정에 VHD를 업로드합니다.

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Azure Resource Manager 템플릿 배포를 사용하여 배포** Resource Manager 템플릿 내에서 `licenseType`에 대한 추가 매개 변수는 지정할 수 있습니다. [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)에 대해 자세히 알아볼 수 있습니다. Azure에 VHD를 업로드하고 나면 Resource Manager 템플릿을 편집하여 계산 공급자의 일부로 라이선스 유형을 포함하고 정상적으로 템플릿을 배포합니다.
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**PowerShell을 통해 배포** PowerShell을 통해 Windows Server VM을 배포할 때 `-LicenseType`에 대한 추가 매개 변수가 있습니다. Azure에 VHD를 업로드하고 나면 다음과 같이 `New-AzVM`을 사용하여 VM을 만들고 라이선싱 형식을 지정합니다.
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM이 라이선싱 혜택을 사용하고 있는지 확인
PowerShell 또는 Resource Manager 배포 메서드를 통해 VM을 배포한 후 다음과 같이 `Get-AzVM` 을 사용하여 라이선스 형식을 확인합니다.
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

올바른 라이선스 형식이 있는 Windows 10의 경우 다음 예제와 유사하게 출력됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

이 출력은 Azure 갤러리에서 바로 배포된 VM과 같이 Azure 하이브리드 사용 혜택 라이선싱 없이 배포된 다음 VM과는 대조됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Azure AD 가입에 대한 추가 정보
>[!NOTE]
>Azure는 AAD 가입에 사용될 수 없는 기본 제공 관리자 계정을 사용하여 모든 Windows VM을 프로비전합니다. 예를 들어 *설정 > 계정 > 회사 또는 학교 액세스 > + 연결*은 작동하지 않습니다. Azure AD에 수동으로 가입하려면 두 번째 관리자 계정을 만들어 로그온해야 합니다. 또한 프로비저닝 패키지를 사용하여 Azure AD를 구성할 수 있습니다. *다음 단계* 섹션에 대한 링크를 사용하여 자세한 내용을 알아보세요.
>
>

## <a name="next-steps"></a>다음 단계
- [Windows 10용 VDA 구성](https://docs.microsoft.com/windows/deployment/vda-subscription-activation)에 대해 자세히 알아봅니다.
- [Windows 10용 다중 테넌트 호스팅](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)에 대해 자세히 알아봅니다.


