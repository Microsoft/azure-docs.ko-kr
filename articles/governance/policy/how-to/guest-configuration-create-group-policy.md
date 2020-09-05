---
title: Windows에 대 한 그룹 정책 기준에서 게스트 구성 정책 정의를 만드는 방법
description: 그룹 정책 Windows Server 2019 보안 기준에서 정책 정의로 변환 하는 방법에 대해 알아봅니다.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 58fe4fa3e5056192fa5febe4883a1457d130871b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547771"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Windows에 대 한 그룹 정책 기준에서 게스트 구성 정책 정의를 만드는 방법

사용자 지정 정책 정의를 만들기 전에 [Azure Policy 게스트 구성](../concepts/guest-configuration.md)에서 개념 개요 정보를 읽는 것이 좋습니다. Linux에 대 한 사용자 지정 게스트 구성 정책 정의를 만드는 방법에 대 한 자세한 내용은 [linux 용 게스트 구성 정책을 만드는 방법](./guest-configuration-create-linux.md)을 참조 하세요. Windows에 대 한 사용자 지정 게스트 구성 정책 정의를 만드는 방법에 대 한 자세한 내용은 [windows 용 게스트 구성 정책을 만드는 방법](./guest-configuration-create.md)을 참조 하세요.

Windows를 감사할 때 게스트 구성은 DSC([Desired State Configuration](/powershell/scripting/dsc/overview/overview)) 리소스 모듈을 사용하여 구성 파일을 만듭니다. DSC 구성은 컴퓨터가 충족해야 하는 조건을 정의합니다. 구성 평가가 **비준수**인 경우 정책 효과 *auditIfNotExists* 이 트리거됩니다.
[Azure Policy 게스트 구성은](../concepts/guest-configuration.md) 컴퓨터 내의 설정만 감사 합니다.

> [!IMPORTANT]
> 게스트 구성을 사용 하는 사용자 지정 정책 정의는 미리 보기 기능입니다.
>
> 게스트 구성 확장은 Azure Virtual Machines에서 감사를 수행하는 데 필요합니다. 모든 Windows 컴퓨터에서 확장을 대규모로 배포하려면 다음 정책 정의를 할당합니다.
> - [Windows VM에서 게스트 구성 정책을 사용하도록 설정하기 위한 필수 조건 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

DSC 커뮤니티는 내보낸 그룹 정책 템플릿을 DSC 형식으로 변환 하기 위해 [BaselineManagement 모듈](https://github.com/microsoft/BaselineManagement) 을 게시 했습니다. GuestConfiguration cmdlet과 함께 BaselineManagement 모듈은 그룹 정책 콘텐츠에서 Windows 용 Azure Policy 게스트 구성 패키지를 만듭니다. BaselineManagement 모듈을 사용 하는 방법에 대 한 자세한 내용은 [빠른 시작: DSC로 그룹 정책 변환](/powershell/scripting/dsc/quickstarts/gpo-quickstart)문서를 참조 하세요.

이 가이드에서는 GPO (그룹 정책 개체)에서 Azure Policy 게스트 구성 패키지를 만드는 프로세스를 안내 합니다. 이 연습에서는 Windows Server 2019 보안 기준의 변환을 간략하게 설명 하는 동안 다른 Gpo에 동일한 프로세스를 적용할 수 있습니다.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Windows Server 2019 보안 기준 다운로드 및 관련 PowerShell 모듈 설치

PowerShell에서 **DSC**, **GuestConfiguration**, **기준 관리**및 관련 Azure 모듈을 설치 하려면 다음을 수행 합니다.

1. PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. 에 대 한 디렉터리를 만들고 Windows 보안 준수 도구 키트에서 Windows Server 2019 보안 기준을 다운로드 합니다.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. 다운로드 된 서버 2019 기준의 차단을 해제 하 고 확장 합니다.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. **MapGuidsToGpoNames.ps1**를 사용 하 여 서버 2019 기준 콘텐츠의 유효성을 검사 합니다.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>그룹 정책에서 Azure Policy 게스트 구성으로 변환

그런 다음 게스트 구성 및 기준 관리 모듈을 사용 하 여 다운로드 한 서버 2019 기준을 게스트 구성 패키지로 변환 합니다.

1. 기본 관리 모듈을 사용 하 여 그룹 정책를 필요한 상태 구성으로 변환 합니다.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. 정책 콘텐츠 패키지를 만들기 전에 변환 된 스크립트의 이름을 바꾸고 다시 포맷 하 고 실행 합니다.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Azure Policy 게스트 구성 콘텐츠 패키지를 만듭니다.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Azure Policy 게스트 구성 만들기

다음 단계는 Blob Storage에 파일을 게시하는 과정입니다. 

1. 아래 스크립트에는 이 작업을 자동화하는 데 사용할 수 있는 함수가 포함되어 있습니다. 함수에 사용 되는 명령에는 `publish` 모듈이 필요 합니다 `Az.Storage` .

   ```azurepowershell-interactive
    function Publish-Configuration {
        param(
        [Parameter(Mandatory=$true)]
        $resourceGroup,
        [Parameter(Mandatory=$true)]
        $storageAccountName,
        [Parameter(Mandatory=$true)]
        $storageContainerName,
        [Parameter(Mandatory=$true)]
        $filePath,
        [Parameter(Mandatory=$true)]
        $blobName
        )

        # Get Storage Context
        $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
            -Name $storageAccountName | `
            ForEach-Object { $_.Context }

        # Upload file
        $Blob = Set-AzStorageBlobContent -Context $Context `
            -Container $storageContainerName `
            -File $filePath `
            -Blob $blobName `
            -Force

        # Get url with SAS token
        $StartTime = (Get-Date)
        $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
        $SAS = New-AzStorageBlobSASToken -Context $Context `
            -Container $storageContainerName `
            -Blob $blobName `
            -StartTime $StartTime `
            -ExpiryTime $ExpiryTime `
            -Permission rl `
            -FullUri

        # Output
        return $SAS
    }
   ```

1. 고유한 리소스 그룹, 저장소 계정 및 컨테이너를 정의 하는 매개 변수를 만듭니다. 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

1. 지정 된 매개 변수와 함께 publish 함수를 사용 하 여 공용 blob 저장소에 게스트 구성 패키지를 게시 합니다.


   ```azurepowershell-interactive
   $PublishConfigurationSplat = @{
       resourceGroup = $resourceGroup
       storageAccountName = $storageAccount
       storageContainerName = $storageContainer
       filePath = $path
       blobName = $blob
       FullUri = $true
   }
   $uri = Publish-Configuration @PublishConfigurationSplat
    ```
1. 게스트 구성 사용자 지정 정책 패키지를 만들고 업로드한 후에는 게스트 구성 정책 정의를 만듭니다. Cmdlet을 사용 `New-GuestConfigurationPolicy` 하 여 게스트 구성을 만듭니다.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Cmdlet을 사용 하 여 정책 정의를 게시 합니다 `Publish-GuestConfigurationPolicy` . cmdlet에는 `New-GuestConfigurationPolicy`에서 만든 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다. Publish 명령을 실행 하려면 Azure에서 정책 정의를 만들기 위한 액세스 권한이 필요 합니다. 특정 권한 부여 요구 사항은 [Azure Policy 개요](../overview.md#getting-started) 페이지에 설명되어 있습니다. 가장 적합한 기본 제공 역할은 **리소스 정책 기여자**입니다.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>게스트 구성 정책 정의 할당

Azure에서 만든 정책을 사용하는 마지막 단계는 이니셔티브를 할당하는 과정입니다. [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) 및 [Azure PowerShell](../assign-policy-powershell.md)을 사용하여 이니셔티브를 할당하는 방법을 참조하세요.

> [!IMPORTANT]
> 게스트 구성 정책 정의는 **항상** _AuditIfNotExists_ 및 _deployifnotexists_ 정책을 결합 하는 이니셔티브를 사용 하 여 할당 되어야 합니다. _AuditIfNotExists_ 정책만 할당된 경우 필수 구성 요소가 배포되지 않으며 정책에 항상 '0' 서버가 규정을 준수함을 표시합니다.

_DeployIfNotExists_ 효과와 함께 정책 정의를 할당하려면 추가 액세스 수준이 필요합니다. 최소 권한을 부여하려면 **리소스 정책 기여자**를 확장하는 사용자 지정 역할 정의를 만들 수 있습니다. 아래 예제에서는 _Microsoft.Authorization/roleAssignments/write_ 추가 권한을 사용하여 **리소스 정책 기여자 DINE**이라는 역할을 만듭니다.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>다음 단계

- [게스트 구성](../concepts/guest-configuration.md)을 사용하여 VM을 감사하는 방법을 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](./programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](./get-compliance-data.md) 방법을 알아봅니다.
