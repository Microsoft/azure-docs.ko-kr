---
title: Azure PowerShell을 사용 하 여 DevTest Labs에서 가상 머신 만들기 | Microsoft Docs
description: Azure DevTest Labs Azure PowerShell을 사용 하 여 가상 머신 만들기 및 관리를 사용 하는 방법에 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622442"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 DevTest Labs를 사용 하 여 가상 머신 만들기
이 문서에서는 Azure PowerShell을 사용 하 여 Azure DevTest Labs에서 가상 컴퓨터를 만드는 방법을 보여 줍니다. Azure DevTest Labs에서 랩의 가상 머신 만들기를 자동화 하려면 PowerShell 스크립트를 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
시작하기 전에

- [랩 만들기](devtest-lab-create-lab.md) 기존 랩이 문서의 스크립트 또는 명령을 테스트 하려면 사용 하지 않으려는 경우. 
- [Azure PowerShell 설치](/powershell/azure/install-az-ps?view=azps-1.7.0) 하거나 Azure portal에 통합 된 Azure Cloud Shell을 사용 합니다. 

## <a name="powershell-script"></a>PowerShell 스크립트
이 섹션의 샘플 스크립트를 사용 합니다 [Invoke AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) cmdlet.  이 cmdlet은 랩의 리소스 ID를 수행 하는 작업의 이름 (`createEnvironment`), 및 필요한 매개 변수를 해당 작업을 수행 합니다. 매개 변수를 모든 가상 머신에 대 한 설명 속성을 포함 하는 해시 테이블에 있습니다. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

위의 스크립트에서 가상 컴퓨터의 속성을 OS로 Windows Server 2016 DataCenter를 사용 하 여 가상 컴퓨터를 만들려면 수 있습니다. 가상 컴퓨터의 각 형식에 대해 이러한 속성은 약간 다른 됩니다. [가상 머신 정의](#define-virtual-machine) 단원에서는이 스크립트에서 사용 하는 속성을 확인 하는 방법을 보여 줍니다.

다음 명령을 파일 이름에 저장 된 스크립트를 실행 예제를 제공 합니다. Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>가상 컴퓨터를 정의 합니다.
이 섹션을 만들려고 할 수 있는 가상 컴퓨터의 유형과 관련 된 속성을 가져오는 방법을 보여 줍니다. 

### <a name="use-azure-portal"></a>Azure Portal 사용
Azure portal에서 VM을 만들 때 Azure Resource Manager 템플릿을 생성할 수 있습니다. VM을 만드는 프로세스를 완료할 필요가 없습니다. 만 템플릿이 표시 될 때까지 단계를 수행 합니다. 이 가장 좋은 방법은 수 없는 랩 VM 생성 하는 경우 필요한 JSON 설명을 가져옵니다. 

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 선택 **모든 서비스** 왼쪽된 탐색 메뉴에서.
3. 검색 및 선택 **DevTest Labs** 서비스 목록에서. 
4. 에 **DevTest Labs** 페이지, 랩 목록에서 랩을 선택 합니다.
5. 랩에 대 한 홈 페이지에서 선택 **+ 추가** 도구 모음에서 합니다. 
6. 선택 된 **기본 이미지** VM에 대 한 합니다. 
7. 선택 **automation 옵션** 위 페이지의 맨 아래에 **제출** 단추입니다. 
8. 표시 된 **Azure Resource Manager 템플릿을** 가상 머신 만들기에 대 한 합니다. 
9. JSON 세그먼트에는 **리소스** 섹션 이전에 선택한 이미지 형식에 대 한 정의가 있습니다. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

이 예제에서는 Azure Market Place 이미지의 정의 확인 하는 방법을 볼 수 있습니다. 동일한 방식으로 사용자 지정 이미지, 수식 또는 환경 정의 가져올 수 있습니다. 가상 컴퓨터에 필요한 모든 아티팩트를 추가 하 고 필요한 모든 고급 설정을 설정 합니다. 필수 필드 및 선택적 필드에 대 한 값을 제공 하기 전에 선택 합니다 **Automation 옵션** 단추입니다.

### <a name="use-azure-rest-api"></a>Azure REST API 사용
다음 절차는 REST API를 사용 하 여 이미지의 속성을 가져오는 단계를 제공 합니다. 다음이 단계에서 랩에 기존 VM에 대해서만 작동 합니다. 

1. 로 이동 합니다 [Virtual Machines-목록](/rest/api/dtl/virtualmachines/list) 페이지에서 **사용해** 단추입니다. 
2. Azure **구독**을 선택합니다.
3. 입력 된 **랩에 대 한 리소스 그룹**합니다.
4. 입력 된 **랩 이름**합니다. 
5. **실행**을 선택합니다.
6. 표시 된 **이미지에 대 한 속성** VM을 만드는 데 기반 합니다. 



## <a name="next-steps"></a>다음 단계
다음 콘텐츠를 참조 하세요. [Azure DevTest Labs에 대 한 azure PowerShell 설명서](/powershell/module/az.devtestlabs/)
