---
title: PowerShell을 사용하여 Azure Cloud Services에 진단 사용 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure 진단 확장으로 Azure 클라우드 서비스에서 진단 데이터를 수집 하도록 설정 하는 방법을 알아봅니다.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: e3c4beac5bd88ba0bbefa91fc83976416b24d2fc
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141077"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>PowerShell을 사용하여 Azure Cloud Services에 진단 사용
Azure Diagnostics 확장을 사용하여 클라우드 서비스로부터 애플리케이션 로그, 성능 카운터 등과 같은 진단 데이터를 수집할 수 있습니다. 이 문서는 PowerShell을 사용하여 클라우드 서비스에 대해 Azure Diagnostics 확장을 사용하도록 설정하는 방법을 설명합니다.  이 문서에 필요한 필수 조건은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/) 을 참조하세요.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>클라우드 서비스 배포의 일부로 진단 확장을 사용하도록 설정
이 접근 방식은 진단 확장이 클라우드 서비스의 배포 중 일부로 사용될 수 있는 연속 통합 형식의 시나리오에 적용됩니다. 새 클라우드 서비스 배포를 만들 때 *Extensionconfiguration* 매개 변수를 [새-azuredeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-3.7.0) cmdlet에 전달 하 여 진단 확장을 사용 하도록 설정할 수 있습니다. *ExtensionConfiguration* 매개 변수는 [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet을 사용하여 만들 수 있는 진단 구성 배열을 사용합니다.

다음 예제는 각각 진단 구성이 다른 WebRole 및 WorkerRole을 사용하여 클라우드 서비스에 대해 진단을 사용하도록 설정하는 방법을 보여줍니다.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

진단 구성 파일이 스토리지 계정 이름으로 `StorageAccount` 요소를 지정할 경우 `New-AzureServiceDiagnosticsExtensionConfig` cmdlet에서 해당 스토리지 계정을 자동으로 사용합니다. 이렇게 작동하려면, 스토리지 계정이 배포된 클라우드 서비스와 동일한 구독에 있어야 합니다.

Azure SDK 2.6 이후부터 MSBuild 게시 대상 출력에 의해 생성된 확장 구성 파일은 서비스 구성 파일(.cscfg)에 지정된 진단 구성 문자열에 기반한 스토리지 계정 이름을 포함합니다. 아래의 스크립트에서는 게시 대상 출력에서 확장 구성 파일을 구문 분석하고 클라우드 서비스를 배포할 때 각 역할에 대한 진단 확장을 구성하는 방법을 보여줍니다.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online은 진단 확장으로 Cloud Services의 자동화된 배포에 대해 유사한 접근 방식을 사용합니다. 전체 예제는 [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) 을 참조하세요.

진단 구성에 `StorageAccount`가 지정되지 않은 경우 cmdlet에 *StorageAccountName* 매개 변수를 전달해야 합니다. *Storageaccountname* 매개 변수가 지정 된 경우 cmdlet은 진단 구성 파일에 지정 된 저장소 계정이 아닌 매개 변수에 지정 된 저장소 계정을 항상 사용 합니다.

진단 스토리지 계정이 클라우드 서비스와 다른 구독에 있는 경우 *StorageAccountName* 및 *StorageAccountKey* 매개 변수를 cmdlet에 명시적으로 전달해야 합니다. 진단 저장소 계정이 동일한 구독에 있는 경우 *StorageAccountKey* 매개 변수는 필요 하지 않습니다. cmdlet은 진단 확장을 사용 하도록 설정할 때 키 값을 자동으로 쿼리 및 설정할 수 있습니다. 그러나 진단 저장소 계정이 다른 구독에 있는 경우에는 cmdlet이 자동으로 키를 얻지 못할 수 있으며 *StorageAccountKey* 매개 변수를 통해 키를 명시적으로 지정 해야 합니다.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>기존 클라우드 서비스에 진단 확장을 사용하도록 설정
[Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet을 사용하여 이미 실행 중인 클라우드 서비스에 진단 구성을 사용하거나 업데이트할 수 있습니다.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>현재 진단 확장 구성 가져오기
[Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet을 사용하여 클라우드 서비스에 대한 현재 진단 구성을 가져올 수 있습니다.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>진단 확장 제거
클라우드 서비스에서 진단을 해제 하려면 [set-azureservicediagnosticsextension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet을 사용할 수 있습니다.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

*Set-azureservicediagnosticsextension* 또는 *new-azureservicediagnosticsextensionconfig* 를 사용 하 여 진단 확장을 사용 하도록 설정한 경우 *role* *매개 변수* 없이 *set-azureservicediagnosticsextension* 를 사용 하 여 확장을 제거할 수 있습니다. 확장을 사용 하도록 설정할 때 *Role* 매개 변수가 사용 된 경우 확장을 제거할 때에도이 매개 변수를 사용 해야 합니다.

각각의 개별 역할에서 진단 확장을 제거하려면:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>다음 단계
* 문제 해결을 위한 Azure 진단 및 기타 기법 사용에 대한 추가 지침은 [Azure Cloud Services 및 Virtual Machines에서 진단 사용](cloud-services-dotnet-diagnostics.md)을 참조하세요.
* [진단 구성 스키마](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) 는 진단 확장에 대한 다양한 XML 구성 옵션을 설명합니다.
* Virtual Machines에 대해 진단 확장을 사용하도록 설정하는 방법을 알아보려면 [Azure 리소스 관리자 템플릿을 사용한 모니터링 및 진단으로 Windows Virtual Machines 만들기(영문)](../virtual-machines/windows/extensions-diagnostics-template.md)



