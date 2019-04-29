---
title: Azure의 Desired State Configuration 개요
description: PowerShell DSC(Desired State Configuration)의 Microsoft Azure 확장 처리기를 사용하는 방법을 알아봅니다. 이 문서에는 필수 구성 요소, 아키텍처 및 cmdlet이 포함되어 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: b3cfc33f435c6ddaabe8358c344b1944f7c271f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799559"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure 필요한 상태 구성 확장 처리기 소개

Azure VM 에이전트 및 연결된 확장은 Microsoft Azure 인프라 서비스의 일부입니다. VM 확장 기능은 VM 기능을 확장하고 다양한 VM 관리 작업을 단순화하는 소프트웨어 구성 요소입니다.

기본 사용 사례는 Azure Desired State Configuration (DSC) 확장은 VM을 부트스트랩 하는 [Azure Automation 상태 구성 (DSC) 서비스](../../automation/automation-dsc-overview.md)합니다.
서비스 제공 [혜택](/powershell/dsc/metaconfig#pull-service) VM 구성 및 Azure 모니터링 등과 같은 기타 운영 도구와 통합의 지속적인 관리를 포함 하는 합니다.
VM의 서비스에 등록 하는 확장을 사용 하 여도 Azure 구독에서 작동 하는 유연한 솔루션을 제공 합니다.

DSC 확장은 Automation DSC 서비스와 별도로 사용할 수 있습니다.
그러나 구성을 VM에만 푸시는이 합니다.
지속적인 보고 하지 않으려면 VM에서 로컬로 이외의 제공 됩니다.

이 문서에서는 두 가지 시나리오, 즉 Automation 온보딩을 위해 DSC 확장을 사용하는 경우와 Azure SDK를 사용하여 VM에 구성을 할당하기 위한 도구로 DSC 확장을 사용하는 경우에 대한 정보를 제공합니다.

## <a name="prerequisites"></a>필수 조건

- **로컬 머신**: Azure VM 확장과 상호 작용하려면 Azure Portal 또는 Azure PowerShell SDK를 사용해야 합니다.
- **게스트 에이전트**: DSC 구성을 통해 구성된 Azure VM은 WMF(Windows Management Framework) 4.0 이상을 지원하는 OS여야 합니다. 지원되는 OS 버전의 전체 목록은 [DSC 확장 버전 기록](/powershell/dsc/azuredscexthistory)을 참조하세요.

## <a name="terms-and-concepts"></a>용어 및 개념

이 가이드에서는 다음과 같은 개념에 익숙하다고 가정합니다.

- **구성**: DSC 구성 문서입니다.
- **노드**: DSC 구성에 대한 대상입니다. 이 문서에서 *노드*는 항상 Azure VM을 나타냅니다.
- **구성 데이터**: 구성에 대한 환경 데이터를 포함하는 .psd1 파일입니다.

## <a name="architecture"></a>아키텍처

Azure DSC 확장은 Azure VM 에이전트 프레임워크를 사용하여 Azure VM에서 실행되는 DSC 구성을 제공하고 적용하며 보고합니다. DSC 확장은 구성 문서 및 매개 변수 집합을 허용합니다. 파일을 제공하지 않으면 확장에 [기본 구성 스크립트](#default-configuration-script)가 포함됩니다. 기본 구성 스크립트는 [로컬 구성 관리자](/powershell/dsc/metaconfig)에서 메타데이터를 설정하는 데만 사용됩니다.

확장이 처음으로 호출되면 다음 논리를 사용하여 WMF의 버전을 설치합니다.

- Azure VM OS가 Windows Server 2016이면 아무 작업도 수행되지 않습니다. Windows Server 2016에는 이미 최신 버전의 PowerShell이 설치되어 있습니다.
- **wmfVersion** 속성을 지정한 경우 VM의 OS와 호환되지 않을 경우를 제외하고 해당 버전의 WMF가 설치됩니다.
- **wmfVersion** 속성을 지정하지 않은 경우 WMF의 적용 가능한 최신 버전이 설치됩니다.

WMF를 설치하려면 컴퓨터를 다시 시작해야 합니다. 다시 시작한 후에 확장은 **modulesUrl** 속성(제공된 경우)에 지정된 .zip 파일을 다운로드합니다. 이 위치가 Azure Blob Storage인 경우 **sasToken** 속성에 SAS 토큰을 지정하여 파일에 액세스할 수 있습니다. .zip을 다운로드하고 압축을 푼 후에 **configurationFunction**에 정의된 구성 함수를 실행하여 .mof 파일을 생성합니다. 그런 다음, 확장은 생성된 .mof 파일을 사용하여 `Start-DscConfiguration -Force`를 실행합니다. 확장은 출력을 캡처하고 Azure 상태 채널에 작성합니다.

### <a name="default-configuration-script"></a>기본 구성 스크립트

Azure DSC 확장에는 Azure Automation DSC 서비스에 VM을 온보딩할 때 사용할 수 있도록 기본 구성 스크립트가 포함되어 있습니다. 스크립트 매개 변수는 [로컬 구성 관리자](/powershell/dsc/metaconfig)의 구성 가능한 속성과 정렬됩니다. 스크립트 매개 변수에 대해서는 [Desired State Configuration 확장과 Azure Resource Manager 템플릿](dsc-template.md)에서 [기본 구성 스크립트](dsc-template.md#default-configuration-script)를 참조하세요. 전체 스크립트에 대해서는 [GitHub의 Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)을 참조하세요.

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Azure Automation 상태 구성 (DSC) 서비스를 사용 하 여 등록에 대 한 정보

상태 구성 서비스를 사용 하 여 노드를 등록 하려면 DSC 확장을 사용 하면 세 개의 값 제공 해야 합니다.

- RegistrationUrl-Azure Automation 계정의 https 주소
- RegistrationKey-서비스를 사용 하 여 노드를 등록 하는 데 사용 하는 공유 암호
- NodeConfigurationName-의 노드 구성 (MOF)에 서버 역할을 구성 하려면 서비스에서 풀 이름

이 정보를 볼 수 있습니다 합니다 [Azure portal](../../automation/automation-dsc-onboarding.md#azure-portal) 또는 PowerShell을 사용할 수 있습니다.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

노드 구성 이름에 대 한 이름을 사용 하 고 있는지 확인 합니다 *노드 구성* 및 구성은 없습니다.
구성을 사용 되는 스크립트에 정의 된 [노드 구성 (MOF 파일)를 컴파일하는 데](https://docs.microsoft.com/en-us/azure/automation/automation-dsc-compile)합니다.
이름을 마침표 뒤에 구성을 항상 `.` 고 `localhost` 또는 특정 컴퓨터 이름입니다.

## <a name="dsc-extension-in-resource-manager-templates"></a>Resource Manager 템플릿의 DSC 확장

대부분의 시나리오에서 ARM(Azure Resource Manager) 배포 템플릿은 DSC 확장을 사용하기 위한 예상되는 방법입니다. Azure Resource Manager 배포 템플릿에 DSC 확장을 포함하는 방법에 대한 자세한 내용 및 예제는 [Desired State Configuration 확장과 Azure Resource Manager 템플릿](dsc-template.md)을 참조하세요.

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 확장 PowerShell Cmdlet

DSC 확장 관리에 사용되는 PowerShell cmdlet은 대화형 문제 해결 및 정보 수집 시나리오에 가장 적합합니다. cmdlet을 사용하여 DSC 확장 배포를 패키징하고 게시하며 모니터링할 수 있습니다. DSC 확장용 cmdlet은 [기본 구성 스크립트](#default-configuration-script)에서 사용할 수 있도록 아직 업데이트되지 않았습니다.

**Publish-AzVMDscConfiguration** cmdlet은 구성 파일을 가져와, 종속 DSC 리소스를 검색한 다음, .zip 파일을 만듭니다. .zip 파일에는 구성과, 구성을 실행하는 데 필요한 DSC 리소스가 포함됩니다. 또한 이 cmdlet에서는 *-OutputArchivePath* 매개 변수를 사용하여 로컬로 패키지를 만들 수도 있습니다. 그렇지 않으면 이 cmdlet은 Blob Storage에 .zip 파일을 게시하고 이 파일을 SAS 토큰으로 보호합니다.

이 cmdlet에서 만든 .ps1 구성 스크립트는 보관 폴더 루트의 .zip 파일에 있습니다. 모듈 폴더는 리소스의 보관 폴더에 있습니다.

**Set-AzVMDscExtension** cmdlet은 PowerShell DSC 확장에 필요한 설정을 VM 구성 개체에 삽입합니다.

**Get-AzVMDscExtension** cmdlet은 특정 VM의 DSC 확장 상태를 가져옵니다.

**Get-AzVMDscExtensionStatus** cmdlet은 DSC 확장 처리기에 의해 실행되는 DSC 구성의 상태를 가져옵니다. 이 작업은 단일 VM 또는 VM 그룹에서 수행할 수 있습니다.

**Remove-AzVMDscExtension** cmdlet은 특정 VM에서 확장 처리기를 제거합니다. 이 cmdlet은 구성을 제거하거나 WMF를 제거하거나 VM에 적용된 설정을 변경하지 *않습니다*. 확장 처리기를 제거합니다. 

Resource Manager DSC 확장 cmdlet에 대한 중요 정보:

- Azure Resource Manager cmdlets는 동기입니다.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* 및 *Location* 매개 변수는 모두 필수입니다.
- *ArchiveResourceGroupName* 매개 변수는 선택 사항입니다. 저장소 계정이 VM을 만들 위치가 아닌 다른 리소스 그룹에 속해 있는 경우 이 매개 변수를 지정할 수 있습니다.
- **AutoUpdate** 스위치를 사용하면 최신 버전이 제공될 시 확장 처리기를 자동으로 업데이트할 수 있습니다. 이 매개 변수를 사용하면 새 버전의 WMF가 릴리스될 때 VM이 다시 시작될 수 있습니다.

### <a name="get-started-with-cmdlets"></a>cmdlet으로 시작

Azure DSC 확장은 DSC 구성 문서를 사용하여 배포하는 동안 Azure VM을 직접 구성할 수 있습니다. 이 단계에서는 노드가 Automation에 등록되지 않습니다. 노드는 중앙 집중식으로 관리되지 *않습니다*.

다음 예제에서는 구성의 간단한 예제를 보여줍니다. 구성을 로컬로 IisInstall.ps1으로 저장합니다.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

다음 명령을 실행하면 지정한 VM에 IisInstall.ps1 스크립트가 배치됩니다. 또한 이 명령은 구성을 실행한 후 상태를 다시 보고합니다.

```powershell
$resourceGroup = 'dscVmDemo'
$location = 'westus'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI는 기존 가상 머신에 DSC 확장을 배포 하 사용할 수 있습니다.

가상 컴퓨터의 Windows를 실행 합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Linux를 실행 중인 가상 컴퓨터:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure Portal 기능

포털에서 DSC를 설정하려면

1. VM으로 이동합니다.
2. **설정**에서 **확장**을 선택합니다.
3. 만들어진 새 페이지에서 **+ 추가**를 선택한 다음, **PowerShell Desired State Configuration**을 선택합니다.
4. 확장 정보 페이지의 아래쪽에서 **만들기**를 클릭합니다.

포털에서는 다음 입력을 수집합니다.

- **구성 모듈 또는 스크립트**: 이 필드는 필수입니다(양식이 [기본 구성 스크립트](#default-configuration-script)에 대해 업데이트되지 않음). 구성 모듈 및 스크립트에는 구성 스크립트가 있는 .ps1 파일 또는 루트에 .ps1 구성 스크립트가 있는 .zip 파일이 필요합니다. .zip 파일을 사용하는 경우 모든 종속 리소스를 .zip의 모듈 폴더에 포함해야 합니다. Azure PowerShell SDK에 포함된 **Publish-AzureVMDscConfiguration -OutputArchivePath** cmdlet을 사용하여 .zip 파일을 만들 수 있습니다. .zip 파일은 사용자 Blob Storage로 업로드되고 SAS 토큰에 의해 보호됩니다.

- **구성의 모듈 정규화된 이름**: .ps1 파일에 여러 개의 구성 함수를 포함할 수 있습니다. 구성 .ps1 스크립트의 이름 뒤에 \\ 및 구성 함수의 이름을 입력합니다. 예를 들어, .ps1 스크립트 이름이 configuration.ps1이고 구성이 **IisInstall**이면 **configuration.ps1\IisInstall**을 입력합니다.

- **구성 인수**: 구성 함수가 인수를 사용하는 경우 **argumentName1=value1,argumentName2=value2** 형식으로 여기에 입력합니다. 이 형식은 PowerShell cmdlet 또는 Resource Manager 템플릿에서 구성 인수가 수락되는 형식과는 다릅니다.

- **구성 데이터 PSD1 파일**: 이 필드는 선택 사항입니다. 구성에 .psd1의 구성 데이터 파일이 필요한 경우 이 필드를 사용하여 데이터 필드를 선택하고 사용자 Blob Storage에 업로드합니다. 구성 데이터 파일은 Blob Storage의 SAS 토큰에 의해 보호됩니다.

- **WMF 버전**: VM에 설치해야 하는 WMF(Windows Management Framework)의 버전을 지정합니다. 이 속성을 최신으로 설정하면 WMF의 가장 최신 버전이 설치됩니다. 현재, 이 속성에 대해 사용할 수 있는 값은 4.0, 5.0, 5.1 및 최신뿐입니다. 가능한 값은 업데이트에 따라 달라집니다. 기본값은 **latest**입니다.

- **데이터 수집**: 확장에서 원격 분석을 수집할지를 결정합니다. 자세한 내용은 [Azure DSC 확장 데이터 컬렉션](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)을 참조하세요.

- **버전**: 설치할 DSC 확장의 버전을 지정합니다. 버전에 대한 정보는 [DSC 확장 버전 기록](/powershell/dsc/azuredscexthistory)을 참조하세요.

- **부 버전 자동 업그레이드**: 이 필드는 cmdlet에서 **AutoUpdate** 스위치에 매핑하고 설치하는 동안 확장을 최신 버전으로 자동 업데이트할 수 있습니다. **예**는 확장 처리기가 최신 버전을 사용하도록 지시하고, **아니요**는 **버전**을 강제로 설치되도록 지정합니다. **예** 또는 **아니요**를 선택하지 않으면 **아니요**를 선택한 것과 동일합니다.

## <a name="logs"></a>로그

확장에 대한 로그는 다음 위치에 저장됩니다. `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>다음 단계

- PowerShell DSC에 대한 자세한 내용은 [PowerShell 설명서 센터](/powershell/dsc/overview)를 참조하세요.
- [DSC 확장에 대한 Resource Manager 템플릿](dsc-template.md)을 검토합니다.
- PowerShell DSC로 관리할 수 있는 더 많은 기능 및 더 많은 DSC 리소스는 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)를 참조하세요.
- 중요한 매개 변수를 구성에 전달하는 세부 정보는 [DSC 확장 처리기로 안전하게 자격 증명 관리](dsc-credentials.md)를 참조하세요.
