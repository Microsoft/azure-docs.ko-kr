---
title: 템플릿을 사용 하 여 다중 VM 환경 및 PaaS 리소스 만들기
description: Azure Resource Manager 템플릿에서 Azure DevTest Labs에 다중 VM 환경 및 PaaS 리소스를 만드는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182411"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기

Azure DevTest Labs 환경을 사용 하면 사용자가 랩의 범위 내에서 일관 된 방식으로 복잡 한 인프라를 쉽게 배포할 수 있습니다. [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/template-syntax.md) 사용 하 여 DevTest Labs에서 리소스 집합으로 환경을 만들 수 있습니다. 이러한 환경에는 리소스 관리자 템플릿에서 만들 수 있는 모든 Azure 리소스가 포함 될 수 있습니다.

[Azure Portal](https://portal.azure.com)를 사용 하 여 랩에 [한 번에 하나의 VM (가상 머신)](devtest-lab-add-vm.md) 을 쉽게 추가할 수 있습니다. 그러나 다중 계층 웹 앱 또는 SharePoint 팜과 같은 시나리오에는 단일 단계에서 여러 Vm을 만드는 메커니즘이 필요 합니다. Azure Resource Manager 템플릿을 사용 하면 Azure 솔루션의 인프라와 구성을 정의 하 고 여러 Vm을 일관 된 상태로 반복적으로 배포할 수 있습니다.

또한 Azure Resource Manager 템플릿은 다음과 같은 이점을 제공 합니다.

- Azure Resource Manager 템플릿은 GitHub 또는 Azure Repos 소스 제어 리포지토리에서 직접 로드 됩니다.
- 사용자는 다른 유형의 [VM 기반이](devtest-lab-comparing-vm-base-image-types.md)되는 것 처럼 Azure Portal에서 구성 된 Azure Resource Manager 템플릿을 선택 하 여 환경을 만들 수 있습니다.
- Azure Resource Manager 템플릿에서 Azure PaaS 리소스 및 IaaS Vm을 환경에서 프로 비전 할 수 있습니다.
- 랩에서 환경의 비용을 추적할 수 있으며, 다른 종류의 기반이 만든 개별 Vm도 추적할 수 있습니다. PaaS 리소스가 만들어지고 비용 추적에 표시 됩니다. 하지만 VM 자동 종료는 PaaS 리소스에 적용되지 않습니다.

단일 작업에서 여러 랩 리소스를 배포, 업데이트 또는 삭제 하는 리소스 관리자 템플릿을 사용 하는 이점에 대 한 자세한 내용은 [리소스 관리자 템플릿 사용의 이점](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)을 참조 하세요.

> [!NOTE]
> 리소스 관리자 템플릿을 기반으로 사용 하 여 랩 Vm을 만들 때 여러 Vm을 만드는 것과 단일 VM을 만드는 것 사이에는 몇 가지 차이점이 있습니다. 자세한 내용은 [가상 컴퓨터의 Azure Resource Manager 템플릿 사용](devtest-lab-use-resource-manager-template.md)을 참조 하세요.
>

## <a name="use-devtest-labs-public-environments"></a>DevTest Labs 공용 환경 사용
Azure DevTest Labs에는 외부 GitHub 원본에 직접 연결할 필요 없이 환경을 만드는 데 사용할 수 있는 [Azure Resource Manager 템플릿의 공용 리포지토리가](https://github.com/Azure/azure-devtestlab/tree/master/Environments) 있습니다. 이 공용 리포지토리는 만드는 모든 랩에서 Azure Portal에서 사용할 수 있는 아티팩트의 공용 리포지토리와 비슷합니다. 환경 리포지토리를 사용 하면 입력 매개 변수가 거의 없는 미리 작성 된 환경 템플릿을 빠르게 시작할 수 있습니다. 이러한 템플릿은 랩 내에서 PaaS 리소스에 대 한 원활한 시작 환경을 제공 합니다.

공용 리포지토리의 DevTest Labs 팀은 Azure Web Apps, Service Fabric 클러스터 및 개발 SharePoint 팜 환경과 같은 자주 사용 되는 템플릿을 만들고 공유 했습니다. 이러한 템플릿을 직접 사용 하거나 필요에 맞게 사용자 지정할 수 있습니다. 자세한 내용은 [DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조하세요. 사용자 고유의 템플릿을 만든 후이 리포지토리에 저장 하 여 다른 사용자와 공유 하거나 고유한 Git 리포지토리를 설정할 수 있습니다.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>사용자 고유의 템플릿 리포지토리 만들기

코드를 사용 하 여 코드를 구성 하 고 코드를 구성 하는 모범 사례 중 하나는 소스 제어에서 환경 템플릿을 관리 하는 것입니다. Azure DevTest Labs이 연습을 수행 하 고 GitHub 또는 Azure Repos 리포지토리에서 모든 Azure Resource Manager 템플릿을 직접 로드 합니다. 따라서 테스트 환경에서 프로덕션 환경으로 전체 릴리스 주기 전체에서 리소스 관리자 템플릿을 사용할 수 있습니다.

리포지토리에서 Azure Resource Manager 템플릿을 구성 하기 위해 수행할 수 있는 몇 가지 규칙이 있습니다.

- 마스터 템플릿 파일의 이름을 *azuredeploy.js설정*해야 합니다.

- 매개 변수 파일에 정의 된 매개 변수 값을 사용 하려면 매개 변수 파일의 이름을 *azuredeploy.parameters.js설정*해야 합니다.

  DevTest Labs에서 중첩된 템플릿을 자동으로 관리할 수 있도록 매개 변수 `_artifactsLocation` 및 `_artifactsLocationSasToken`을 사용하여 parametersLink URI 값을 생성합니다. 자세한 내용은 [테스트 환경을 위한 중첩 된 Azure Resource Manager 템플릿 배포](deploy-nested-template-environments.md)를 참조 하세요.

- 다음과 같이 *metadata.js에 있는*파일에서 템플릿 표시 이름 및 설명을 지정 하는 메타 데이터를 정의할 수 있습니다.

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![주요 Azure Resource Manager 템플릿 파일](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>랩에 템플릿 리포지토리 추가

리포지토리를 만들고 구성한 후 Azure Portal를 사용 하 여 랩에 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 원하는 랩을 선택 합니다.
1. 랩의 **개요** 창에서 **구성 및 정책**을 선택 합니다.

   ![구성 및 정책](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **구성 및 정책** 설정 목록에서 **리포지토리**를 선택 합니다. **공용 아티팩트** 리포지토리 리포지토리는 모든 랩에 대해 자동으로 생성 되 고 [DevTest labs 공용 GitHub 리포지토리에](https://github.com/Azure/azure-devtestlab)연결 됩니다.

1. Azure Resource Manager 템플릿 리포지토리를 추가 하려면 **추가**를 선택 합니다.

   ![공용 리포지토리](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **리포지토리** 창에서 다음 정보를 입력 합니다.

   - **이름**: 랩에서 사용할 리포지토리 이름을 입력 합니다.
   - **Git 복제 url**: GitHub 또는 Azure Repos에서 git HTTPS 복제 url을 입력 합니다.
   - **분기** (선택 사항): Azure Resource Manager 템플릿 정의에 액세스 하는 분기 이름을 입력 합니다.
   - **개인용 액세스 토큰**: 리포지토리에 안전 하 게 액세스 하는 데 사용 되는 개인 액세스 토큰을 입력 합니다.
     - Azure Repos에서 토큰을 가져오려면 사용자 프로필에서 **사용자 설정**  >  **보안**  >  **개인용 액세스 토큰**을 선택 합니다.
     - GitHub에서 토큰을 가져오려면 프로필에서 **설정**  >  **개발자 설정**  >  **개인 액세스 토큰**을 선택 합니다.
   - **폴더 경로**: 아티팩트 정의 또는 Azure Resource Manager 템플릿 정의에 대 한 GIT 클론 URI에 상대적인 폴더 경로를 입력 합니다.

1. **저장**을 선택합니다.

   ![새 리포지토리 추가](./media/devtest-lab-create-environment-from-arm/repo-values.png)

랩에 Azure Resource Manager 템플릿을 추가 하면 랩 사용자가 템플릿을 사용 하 여 환경을 만들 수 있습니다.

## <a name="configure-access-rights-for-lab-users"></a>랩 사용자에 대 한 액세스 권한 구성

랩 사용자에 게는 기본적으로 **독자** 역할이 있으므로 환경 리소스 그룹의 리소스를 변경할 수 없습니다. 예를 들어 리소스를 중지 하거나 시작할 수 없습니다.

환경에서 리소스를 편집할 수 있도록 랩 사용자 **참가자** 역할을 부여 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 랩 **개요** 창에서 **구성 및 정책**을 선택 하 고 **랩 설정**을 선택 합니다.

1. **랩 설정** 창에서 **참가자**를 선택 하 고 **저장** 을 선택 하 여 랩 사용자에 게 쓰기 권한을 부여 합니다.

   ![랩 사용자 액세스 권한 구성](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

다음 섹션에서는 Azure Resource Manager 템플릿에서 환경을 만드는 과정을 안내 합니다.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Azure Portal 템플릿에서 환경 만들기

랩에 Azure Resource Manager 템플릿을 추가 하면 랩 사용자는 다음 단계를 수행 하 여 Azure Portal 환경을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 선택 합니다.

1. 랩의 페이지에서 **추가**를 선택 합니다.

1. **기본 선택** 창에는 사용할 수 있는 기본 이미지가 표시 되 고 Azure Resource Manager 템플릿이 먼저 나열 됩니다. 원하는 Azure Resource Manager 템플릿을 선택 합니다.

   ![기본 선택](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. **추가** 창에서 환경 사용자에 게 표시할 **환경 이름** 값을 입력 합니다.

   Azure Resource Manager 템플릿은 나머지 입력 필드를 정의 합니다. 파일 *에azuredeploy.parameter.js* 템플릿이 기본값을 정의 하는 경우 입력 필드에 해당 값이 표시 됩니다.

   *보안 문자열*형식의 매개 변수는 Azure Key Vault의 비밀을 사용할 수 있습니다. 키 자격 증명 모음에 암호를 저장 하 고 랩 리소스를 만들 때 암호를 사용 하는 방법에 대 한 자세한 내용은 [Azure Key Vault에 암호 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조 하세요.  

   ![창 추가](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 다음 매개 변수 값은 템플릿에서 지정 하는 경우에도 입력 필드에 표시 되지 않습니다. 대신 랩 사용자가 환경을 만들 때 값을 입력 해야 하는 빈 입력 필드가 폼에 표시 됩니다.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. **추가**를 선택하여 환경을 만듭니다.

   환경에서 즉시 프로 비전을 시작 하며, 상태는 **내 가상 머신** 목록에 표시 됩니다. 랩에서는 Azure Resource Manager 템플릿에 정의 된 모든 리소스를 프로 비전 하는 새 리소스 그룹을 자동으로 만듭니다.

1. 환경이 만들어지면 **내 가상 컴퓨터** 목록에서 환경을 선택 하 여 리소스 그룹 창을 열고 환경이 프로 비전 된 모든 리소스를 검색 합니다.

   ![환경 리소스](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   환경을 확장 하 여 환경이 프로 비전 된 Vm 목록만 볼 수도 있습니다.

   ![내 가상 머신 목록](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 아티팩트를 적용 하 고, 데이터 디스크를 연결 하 고, 자동 종료 시간을 변경 하는 등 사용 가능한 작업을 보려면 환경을 선택 합니다.

   ![환경 작업](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>PowerShell을 사용 하 여 환경 만들기 자동화

Azure Portal를 사용 하 여 랩에 단일 환경을 추가할 수 있지만 개발 또는 테스트 시나리오에서 여러 환경을 만들어야 하는 경우 자동화 된 배포가 더 나은 환경을 제공 합니다.

계속 하기 전에 만들 리소스를 정의 하는 Azure Resource Manager 템플릿이 있는지 확인 합니다. [Git 리포지토리에서 템플릿을 추가 및 구성](#configure-your-own-template-repositories)하 고 [랩에 리포지토리를 추가](#add-template-repositories-to-the-lab)합니다.

다음 샘플 스크립트는 랩에서 환경을 만듭니다. 주석은 스크립트를 더 잘 이해 하는 데 도움이 됩니다.

1. 다음 샘플 PowerShell 스크립트를 하드 드라이브에 *deployenv.ps1*로 저장 합니다.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. SubscriptionId, Event.pushnotification.repositoryname, ResourceGroupName,, TemplateName (Git 리포지토리의 폴더) 및 환경 이름에 대 한 특정 값을 사용 하 여 다음과 같이 스크립트를 실행 합니다.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Azure CLI를 사용 하 여 리소스 관리자 템플릿으로 리소스를 배포할 수도 있습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

> [!NOTE]
> 랩 소유자 권한을 가진 사용자만 Azure PowerShell을 사용하여 Resource Manager 템플릿에서 VM을 만들 수 있습니다. 리소스 관리자 템플릿을 사용 하 여 VM 만들기를 자동화 하 고 사용자 권한만 있는 경우 CLI 명령 [az lab VM create](/cli/azure/lab/vm#az-lab-vm-create)를 사용할 수 있습니다.

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>DevTest Labs의 리소스 관리자 템플릿 제한 사항

DevTest Labs에서 리소스 관리자 템플릿을 사용할 때 다음 제한 사항을 고려 하세요.

- 리소스 관리자 템플릿에서 생성 된 랩 Vm에서 수식 또는 사용자 지정 이미지를 만들 수 없습니다.

- 리소스 관리자 템플릿을 배포할 때는 대부분의 정책이 평가 되지 않습니다.

예를 들어 사용자가 5 개의 Vm만 만들 수 있는 랩 정책이 있을 수 있습니다. 그러나 사용자는 수십 개의 VM을 만드는 Resource Manager 템플릿을 배포할 수 있습니다. 평가 되지 않는 정책은 다음과 같습니다.

  - 사용자당 VM 수

  - 랩 사용자당 프리미엄 VM 수

  - 랩 사용자당 프리미엄 디스크 수

## <a name="next-steps"></a>다음 단계
- Vm을 만든 후에는 VM의 관리 창에서 **연결** 을 선택 하 여 vm에 연결할 수 있습니다.
- 랩의 **내 가상 머신** 목록에서 환경을 선택하여 환경에서 리소스를 보고 관리합니다.
- [Azure 빠른 시작 템플릿 갤러리에서 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)을 탐색 합니다.
