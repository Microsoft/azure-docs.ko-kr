---
title: Windows 용 Azure 사용자 지정 스크립트 확장
description: 사용자 지정 스크립트 확장을 사용하여 Windows VM 구성 작업 자동화
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 80b13cb9a926837604e2a10fed75b976ba3393b6
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934912"
---
# <a name="custom-script-extension-for-windows"></a>Windows용 사용자 지정 스크립트 확장

사용자 지정 스크립트 확장은 Azure 가상 머신에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성 또는 관리 작업에 유용 합니다. 스크립트를 Azure Storage 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다. 사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합 되며 Azure CLI, PowerShell, Azure Portal 또는 Azure Virtual Machine REST API를 사용 하 여 실행할 수 있습니다.

이 문서에서는 Azure PowerShell 모듈, Azure Resource Manager 템플릿을 사용하는 사용자 지정 스크립트 확장을 사용하는 방법과 Windows 시스템에서 문제 해결 단계를 자세히 설명하고 있습니다.

## <a name="prerequisites"></a>필수 조건

> [!NOTE]  
> 해당 매개 변수와 동일한 VM을 사용하여 Update-AzVM을 실행하려는 경우에는 사용자 지정 스크립트 확장을 사용하지 마세요. 대기 시간이 길어집니다.  

### <a name="operating-system"></a>운영 체제

Windows 용 사용자 지정 스크립트 확장은 지원 되는 확장 확장 OSs에서 실행 됩니다. 자세한 내용은이 [Azure 확장 지원 운영 체제](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)를 참조 하세요.

### <a name="script-location"></a>스크립트 위치

Azure blob storage 자격 증명을 사용 하 여 Azure Blob storage에 액세스 하도록 확장을 구성할 수 있습니다. VM이 GitHub 또는 내부 파일 서버와 같은 해당 엔드포인트으로 라우팅할 수 있으면 스크립트 위치는 어디에 나 있을 수 있습니다.

### <a name="internet-connectivity"></a>인터넷 연결

GitHub 또는 Azure Storage와 같은 외부에서 스크립트를 다운로드 해야 하는 경우 추가 방화벽 및 네트워크 보안 그룹 포트를 열어야 합니다. 예를 들어 스크립트가 Azure Storage에 있는 경우 [저장소](../../virtual-network/security-overview.md#service-tags)에 대 한 AZURE Nsg 서비스 태그를 사용 하 여 액세스를 허용할 수 있습니다.

스크립트가 로컬 서버에 있는 경우에도 추가 방화벽이 필요 하며 네트워크 보안 그룹 포트를 열어야 할 수 있습니다.

### <a name="tips-and-tricks"></a>팁과 요령

* 이 확장에 대 한 최대 실패율은 스크립트에서 구문 오류가 발생 하 여 스크립트가 오류 없이 실행 되는지 테스트 하 고 스크립트에 추가 로깅을 설정 하 여 실패 한 위치를 쉽게 찾을 수 있기 때문입니다.
* Idempotent 스크립트를 작성 합니다. 이렇게 하면 실수로 다시 실행 되는 경우 시스템 변경이 발생 하지 않습니다.
* 스크립트를 실행할 때 사용자 입력이 필요하지 않도록 합니다.
* 스크립트를 실행하는 데 허용되는 시간은 90분입니다. 더 오래 걸리면 확장을 프로비전하는 데 실패합니다.
* 스크립트 내에서 다시 부팅하지 마세요. 이 작업으로 인해 설치 중인 다른 확장에 문제가 발생합니다. 다시 부팅 후 확장은 다시 시작한 후 계속되지 않습니다.
* 다시 부팅 하는 스크립트를 사용 하 여 응용 프로그램을 설치 하 고 스크립트를 실행 하는 경우 Windows 예약 된 작업을 사용 하 여 다시 부팅을 예약 하거나 DSC, Chef 또는 퍼핏 확장과 같은 도구를 사용할 수 있습니다.
* 확장은 스크립트를 한 번만 실행합니다. 부팅할 때마다 스크립트를 실행하려면 확장을 사용하여 Windows 예약된 작업을 만들어야 합니다.
* 스크립트를 실행할 시기를 예약하려면 확장을 사용하여 Windows 예약된 작업을 만들어야 합니다.
* 스크립트를 실행하는 경우 Azure Portal 또는 CLI에서 ‘전환 중’ 확장 상태만 표시됩니다. 실행 중인 스크립트의 상태 업데이트를 더 자주 수행하려는 경우 사용자 고유의 솔루션을 만들어야 합니다.
* 사용자 지정 스크립트 확장은 기본적으로 프록시 서버를 지원하지 않지만, 스크립트 내에서 프록시 서버를 지원하는 파일 전송 도구(예: *Curl*)를 사용할 수 있습니다.
* 스크립트 또는 명령에서 사용할 수 있는 기본 디렉터리가 아닌 위치를 알고 있어야 하고, 이 상황을 처리할 논리가 있어야 합니다.
* 사용자 지정 스크립트 확장이 LocalSystem 계정으로 실행 됩니다.

## <a name="extension-schema"></a>확장 스키마

사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 이 구성을 구성 파일에 저장하거나, 명령줄에 지정하거나, Azure Resource Manager 템플릿에 지정할 수 있습니다.

중요한 데이터는 보호된 구성에 저장하면 암호화된 후 가상 머신 내에서만 해독됩니다. 보호된 구성은 실행 명령에 암호와 같은 기밀 정보가 포함될 때 유용합니다.

이러한 항목은 중요한 데이터로 처리하고 확장으로 보호되는 설정 구성에 지정되어야 합니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> Microsoft.managedidentity 속성은 storageAccountName 또는 storageAccountKey 속성과 함께 사용할 수 **없습니다** .

> [!NOTE]
> 특정 시점에 하나의 확장 버전만 VM에 설치 될 수 있습니다. 동일한 VM에 대해 동일한 리소스 관리자 템플릿에서 사용자 지정 스크립트를 두 번 지정 하면 실패 합니다.

> [!NOTE]
> 이 스키마는 VirtualMachine 리소스 내에서 사용 하거나 독립 실행형 리소스로 사용할 수 있습니다. 이 확장이 ARM 템플릿에서 독립 실행형 리소스로 사용 되는 경우 리소스 이름은 "virtualMachineName/extensionName" 형식 이어야 합니다. 

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | 문자열 |
| type | CustomScriptExtension | 문자열 |
| typeHandlerVersion | 1.10 | int |
| fileUris(예) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp(예) | 123456789 | 32비트 정수 |
| commandToExecute(예) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | 문자열 |
| storageAccountName(예) | examplestorageacct | 문자열 |
| storageAccountKey(예) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 문자열 |
| Microsoft.managedidentity (예:) | {} 또는 {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} 또는 {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | json 개체 |

>[!NOTE]
>이러한 속성 이름은 대/소문자를 구분합니다. 배포 문제를 방지하려면 다음과 같이 이름을 사용합니다.

#### <a name="property-value-details"></a>속성 값 세부 정보

* `commandToExecute`: (**필수**, 문자열) 실행할 진입점 스크립트입니다. 명령에 암호와 같은 비밀이 포함되어 있거나 fileUris가 중요한 경우 이 필드를 대신 사용합니다.
* `fileUris`: (옵션, 문자열 배열) 다운로드할 파일에 대한 URL입니다.
* `timestamp`(옵션, 32비트 정수)는 이 필드의 값을 변경하여 스크립트의 다시 실행을 트리거하는 데만 이 필드를 사용합니다.  모든 정수 값을 사용할 수 있습니다. 단, 이전 값과 달라야 합니다.
* `storageAccountName`: (옵션, 문자열) 스토리지 계정에 대한 이름입니다. 스토리지 자격 증명을 지정하는 경우 모든 `fileUris`는 Azure Blob에 대한 URL이어야 합니다.
* `storageAccountKey`: (선택 사항, 문자열) 스토리지 계정의 액세스 키
* `managedIdentity`: (옵션, json 개체) 파일 다운로드를 위한 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
  * `clientId`: (옵션, 문자열) 관리 id의 클라이언트 ID
  * `objectId`: (옵션, 문자열) 관리 id의 개체 ID

다음 값은 공용 또는 보호된 설정 중 하나에서 설정할 수 있습니다. 확장은 공용 및 보호된 설정 모두에 아래 값이 설정된 모든 구성을 거부합니다.

* `commandToExecute`

공용 설정을 사용 하면 디버깅에 유용 하지만 보호 된 설정을 사용 하는 것이 좋습니다.

공용 설정은 스크립트를 실행할 VM에 일반 텍스트로 보내집니다.  보호된 설정은 Azure 및 VM에만 알려진 키를 사용하여 암호화됩니다. 설정은 전송 될 때 VM에 저장 됩니다. 즉, 설정이 암호화 된 경우 VM에 암호화 된 상태로 저장 됩니다. 암호화된 값의 암호를 해독하는 데 사용되는 인증서는 VM에 저장되고, 필요한 경우 런타임 시 설정의 암호를 해독하는 데 사용됩니다.

####  <a name="property-managedidentity"></a>속성: Microsoft.managedidentity

CustomScript (버전 1.10 이상)는 "fileUris" 설정에 제공 된 Url에서 파일을 다운로드 하 [는 관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 지원 합니다. 사용자가 SAS 토큰 또는 저장소 계정 키와 같은 암호를 전달 하지 않고도 CustomScript가 Azure Storage 개인 blob 또는 컨테이너에 액세스할 수 있습니다.

이 기능을 사용 하려면 사용자는 CustomScript가 실행 될 것으로 예상 되는 VM 또는 VMSS에 [시스템 할당](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-system-assigned-identity) 또는 [사용자 할당](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-user-assigned-identity) id를 추가 하 고 [Azure Storage 컨테이너 또는 blob에 대 한 관리 id 액세스 권한을 부여](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access)해야 합니다.

대상 VM/VMSS에서 시스템이 할당 한 id를 사용 하려면 "microsoft.managedidentity" 필드를 빈 json 개체로 설정 합니다. 

> 예:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

대상 VM/VMSS에서 사용자 할당 id를 사용 하려면 관리 id의 클라이언트 ID 또는 개체 ID를 사용 하 여 "microsoft.managedidentity" 필드를 구성 합니다.

> 예시:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> Microsoft.managedidentity 속성은 storageAccountName 또는 storageAccountKey 속성과 함께 사용할 수 **없습니다** .

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명 하는 JSON 스키마는 배포 하는 동안 사용자 지정 스크립트 확장을 실행 하기 위해 Azure Resource Manager 템플릿에서 사용할 수 있습니다. 다음 샘플은 사용자 지정 스크립트 확장을 사용하는 방법을 보여 줍니다.

* [자습서: Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 배포](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Windows 및 Azure SQL DB에 두 개의 계층 애플리케이션 배포](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzVMCustomScriptExtension` 명령을 사용하여 사용자 지정 스크립트 확장을 기존 가상 머신에 추가할 수 있습니다. 자세한 내용은 [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension)을 참조하세요.

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>추가 예

### <a name="using-multiple-scripts"></a>여러 스크립트 사용

이 예에서는 서버를 빌드하는 데 사용 되는 세 가지 스크립트가 있습니다. **Commandtoexecute** 는 첫 번째 스크립트를 호출한 다음 다른 스크립트를 호출 하는 방법에 대 한 옵션을 설정 합니다. 예를 들어, 올바른 오류 처리, 로깅 및 상태 관리를 사용 하 여 실행을 제어 하는 마스터 스크립트를 사용할 수 있습니다. 스크립트는를 실행 하기 위해 로컬 컴퓨터에 다운로드 됩니다. 예를 들어 `1_Add_Tools.ps1` 스크립트에 `.\2_Add_Features.ps1`를 추가 하 여 `2_Add_Features.ps1`를 호출 하 고 `$settings`에서 정의 하는 다른 스크립트에 대해이 프로세스를 반복 합니다.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>로컬 공유에서 스크립트 실행

이 예제에서는 스크립트 위치에 로컬 SMB 서버를 사용 하는 것이 좋습니다. 이렇게 하면 **Commandtoexecute**를 제외 하 고 다른 설정은 제공 하지 않아도 됩니다.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>CLI를 사용하여 두 번 이상 사용자 지정 스크립트를 실행하는 방법

사용자 지정 스크립트 확장을 두 번 이상 실행하려는 경우 다음 조건에서만 이 작업을 수행할 수 있습니다.

* 확장 **이름** 매개 변수는 확장의 이전 배포와 동일 합니다.
* 구성을 업데이트 합니다. 그렇지 않으면 명령을 다시 실행 하지 않습니다. 타임스탬프와 같은 명령으로 동적 속성을 추가할 수 있습니다.

또는 [Forceupdatetag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) 속성을 **true**로 설정할 수 있습니다.

### <a name="using-invoke-webrequest"></a>Invoke WebRequest 사용

스크립트에서 [호출 WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) 를 사용 하는 경우 `-UseBasicParsing` 매개 변수를 지정 해야 합니다. 그렇지 않으면 자세한 상태를 확인할 때 다음 오류가 표시 됩니다.

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

확장 집합에 사용자 지정 스크립트 확장을 배포 하려면 [AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0) 를 참조 하세요.

## <a name="classic-vms"></a>클래식 VM

클래식 Vm에서 사용자 지정 스크립트 확장을 배포 하려면 Azure Portal 또는 클래식 Azure PowerShell cmdlet을 사용할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

클래식 VM 리소스로 이동 합니다. **설정**아래에서 **확장** 을 선택 합니다.

**+ 추가** 를 클릭 하 고 리소스 목록에서 **사용자 지정 스크립트 확장**을 선택 합니다.

**설치 확장** 페이지에서 로컬 PowerShell 파일을 선택 하 고 인수를 입력 한 다음 **확인**을 클릭 합니다.

### <a name="powershell"></a>PowerShell

[AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet을 사용 하 여 사용자 지정 스크립트 확장을 기존 가상 머신에 추가할 수 있습니다.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 모듈을 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 다음 명령을 실행합니다.

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

확장 출력은 대상 가상 머신의 다음 폴더 아래에 있는 파일에 기록 됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

지정 된 파일이 대상 가상 컴퓨터의 다음 폴더에 다운로드 됩니다.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

여기서 `<n>`은 확장의 실행 간에 변경될 수 있는 10진수 정수입니다.  `1.*` 값은 확장의 현재 실제 `typeHandlerVersion` 값과 일치합니다.  예를 들어, 실제 디렉터리는 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`일 수 있습니다.  

`commandToExecute` 명령을 실행하는 경우 확장에서 이 디렉터리(예: `...\Downloads\2`)를 현재 작업 디렉터리로 설정합니다. 이 프로세스로 `fileURIs` 속성을 통해 다운로드된 파일을 배치하는 상대 경로를 사용할 수 있습니다. 예제는 아래 테이블을 참조하세요.

시간이 지남에 따라 절대 다운로드 경로가 달라질 수 있으므로 가능한 경우 `commandToExecute` 문자열에서 상대 스크립트/파일 경로를 옵트인하는 것이 좋습니다. 예:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

첫 번째 URI 세그먼트 뒤의 경로 정보는 `fileUris` 속성 목록을 통해 다운로드 된 파일에 대해 유지 됩니다.  아래 테이블에 표시된 대로 다운로드된 파일은 다운로드 하위 디렉터리에 매핑되어 `fileUris` 값의 구조를 반영합니다.  

#### <a name="examples-of-downloaded-files"></a>다운로드된 파일의 예

| fileUris의 URI | 다운로드된 상대 위치 | 다운로드 한 절대 위치 <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> 절대 디렉터리 경로는 VM의 수명 동안 변경 되지만 customscript 확장의 단일 실행 내에서는 변경 되지 않습니다.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
