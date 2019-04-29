---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d14cfb82ae74f85425dbd3e8a365e8b99969641d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485186"
---
## <a name="using-vm-extensions"></a>VM 확장 사용
Azure VM 확장은 Azure VM의 타 프로그램 작업을 지원하거나(예: **WebDeployForVSDevTest** 확장은 Azure VM에서 Visual Studio의 웹 배포 솔루션 작업을 허용함) 일부 다른 동작을 지원하기 위해 VM과 상호 작용하는 기능을 제공(예: PowerShell, Azure CLI 및 REST 클라이언트에서 VM Access 확장을 사용하여 Azure VM에서의 원격 액세스 값을 재설정하거나 수정할 수 있음)하는 동작이나 기능을 구현합니다.

> [!IMPORTANT]
> 지원하는 기능별 전체 확장 목록은 [Azure VM 확장 및 기능](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. 각 VM 확장은 특정 기능을 지원하므로 확장에서 정확히 지원하는 기능과 지원하지 않는 기능은 확장마다 다릅니다. 따라서 VM을 수정하기 전에 사용하려는 VM 확장에 대한 설명서를 읽어야 합니다. 어떤 VM 확장은 제거가 지원되지 않으며 어떤 확장에서는 VM 동작을 근본적으로 변경하는 속성을 설정할 수 있습니다.
> 
> 

가장 일반적인 작업은 다음과 같습니다.

1. 사용 가능한 확장 찾기
2. 로드된 확장 업데이트
3. 확장 추가
4. 확장 제거

## <a name="find-available-extensions"></a>사용 가능한 확장 찾기
다음을 사용하여 확장 및 확장 정보를 찾을 수 있습니다.

* PowerShell
* Azure 플랫폼 간 명령줄 인터페이스(Azure CLI)
* 서비스 관리 REST API

### <a name="azure-powershell"></a>Azure PowerShell
일부 확장에는 PowerShell로부터의 구성 편의를 위한 특정 PowerShell cmdlet이 있지만 다음 cmdlet은 모든 VM 확장에서 작동합니다.

다음 cmdlet을 사용하여 사용 가능한 확장에 대한 정보를 구할 수 있습니다.

* 웹 역할 또는 작업자 역할 인스턴스의 경우 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet을 사용할 수 있습니다.
* Virtual Machines의 경우 [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) cmdlet을 사용할 수 있습니다.
  
   예를 들어, 다음 코드 예제에서는 PowerShell을 사용하여 **IaaSDiagnostics** 확장에 대한 정보를 나열하는 방법을 나타냅니다.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Azure CLI(Azure 명령줄 인터페이스)
일부 확장에는 구성 편의를 위한 고유의 Azure CLI 명령(예: Docker VM 확장)이 있지만 다음 명령은 모든 VM 확장에 적용됩니다.

**azure vm extension list** 명령을 사용하여 사용 가능한 확장에 대한 정보를 구하고 **–-json** 옵션을 사용하여 하나 이상의 확장에 대한 모든 사용 가능한 정보를 표시할 수 있습니다. 확장 이름을 사용하지 않는 경우 명령은 모든 사용 가능한 확장에 대해 JSON 설명을 반환합니다.

예를 들어 다음 코드 예제에서는 Azure CLI **azure vm extension list** 명령을 사용하여 **IaaSDiagnostics** 확장 정보를 나열하는 방법을 보여 주고 **–-json** 옵션을 사용하여 전체 정보를 반환합니다.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>서비스 관리 REST API
다음 REST API를 사용하여 사용 가능한 확장에 대한 정보를 구할 수 있습니다.

* 웹 역할 또는 작업자 역할 인스턴스의 경우 [사용 가능한 확장 나열](https://msdn.microsoft.com/library/dn169559.aspx) 작업을 사용할 수 있습니다. 사용 가능한 확장의 버전을 나열하려면 [확장 버전 나열](https://msdn.microsoft.com/library/dn495437.aspx)을 사용할 수 있습니다.
* Virtual Machines 인스턴스의 경우 [리소스 확장 나열](https://msdn.microsoft.com/library/dn495441.aspx) 작업을 사용할 수 있습니다. 사용 가능한 확장의 버전을 나열하려면 [리소스 확장 버전 나열](https://msdn.microsoft.com/library/dn495440.aspx)을 사용할 수 있습니다.

## <a name="add-update-or-disable-extensions"></a>확장 추가, 업데이트 또는 비활성화
확장은 인스턴스를 만들 때 추가하거나, 실행 중인 인스턴스에 추가할 수 있습니다. 확장은 업데이트, 비활성화 또는 제거할 수 있습니다. Azure PowerShell cmdlet 또는 서비스 관리 REST API 작업을 사용하여 이러한 작업을 수행할 수 있습니다. 일부 확장의 설치 및 설정에는 매개 변수가 필요합니다. 확장에는 공용 및 개인 매개 변수가 지원됩니다.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell cmdlet을 사용하면 가장 쉽게 확장을 추가 및 업데이트할 수 있습니다. 확장 cmdlet을 사용하면 대부분의 확장 구성이 수행됩니다. 때때로 프로그래밍 방식으로 확장을 추가해야 할 수 있습니다. 이 작업이 필요할 때는 확장의 구성을 제공해야 합니다.

확장에서 공용 또는 개인 매개 변수 구성의 필요 여부를 파악하기 위해 다음 cmdlet을 사용할 수 있습니다.

* 웹 역할 또는 작업자 역할 인스턴스의 경우 **Get-AzureServiceAvailableExtension** cmdlet을 사용할 수 있습니다.
* Virtual Machines의 경우 **Get-AzureVMAvailableExtension** cmdlet을 사용할 수 있습니다.

### <a name="service-management-rest-apis"></a>서비스 관리 REST API
REST Api를 사용하여 사용 가능한 확장 목록을 검색할 때 해당 확장이 어떻게 구성될 것인가에 대한 정보를 받게 됩니다. 반환되는 정보는 공용 스키마와 개인 스키마에서 제시하는 매개 변수 정보를 나타낼 수 있습니다. 공용 매개 변수 값은 인스턴스에 대한 쿼리에서 반환됩니다. 개인 매개 변수 값은 반환되지 않습니다.

확장에서 공용 또는 개인 매개 변수 구성의 필요 여부를 파악하기 위해 다음 REST API를 사용할 수 있습니다.

* 웹 역할 또는 작업자 역할 인스턴스의 경우 **PublicConfigurationSchema** 및**PrivateConfigurationSchema** 요소에 [사용 가능한 확장 나열](https://msdn.microsoft.com/library/dn169559.aspx) 작업으로부터의 응답에 있는 정보가 포함됩니다.
* Virtual Machines 인스턴스의 경우 **PublicConfigurationSchema** 및**PrivateConfigurationSchema** 요소에 [사용 가능한 확장 나열](https://msdn.microsoft.com/library/dn495441.aspx) 작업으로부터의 응답에 있는 정보가 포함됩니다.

> [!NOTE]
> 확장은 JSON으로 정의된 구성을 사용할 수도 있습니다. 이러한 유형의 확장을 사용할 때는 **SampleConfig** 요소만 사용됩니다.
> 
> 

