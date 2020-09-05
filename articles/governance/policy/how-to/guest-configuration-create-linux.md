---
title: Linux용 게스트 구성 정책을 만드는 방법
description: Linux용 Azure Policy 게스트 구성 정책을 만드는 방법에 대해 알아봅니다.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4f49732aa2be50b0d8be6f1f3af974121dc9f363
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076364"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Linux용 게스트 구성 정책을 만드는 방법

사용자 지정 정책을 만들기 전에 [Azure Policy 게스트 구성](../concepts/guest-configuration.md)에서 개요 정보를 읽어 보세요.
 
Windows용 게스트 구성 정책을 만드는 방법에 대한 자세한 내용은 [Windows용 게스트 구성 정책을 만드는 방법](./guest-configuration-create.md) 페이지를 참조하세요.

Linux를 감사할 때 게스트 구성은 [Chef InSpec](https://www.inspec.io/)를 사용합니다. InSpec 프로필은 컴퓨터가 충족해야 하는 조건을 정의합니다. 구성을 평가하지 못한 경우 **auditIfNotExists** 정책 효과가 트리거되고 컴퓨터를 **미준수**로 간주합니다.

[Azure Policy 게스트 구성](../concepts/guest-configuration.md)은 컴퓨터 내의 설정을 감사하는 데에만 사용할 수 있습니다. 컴퓨터 내 설정 수정은 아직 사용할 수 없습니다.

다음 작업을 사용하여 Azure 또는 비 Azure 컴퓨터 상태의 유효성을 검사하는 고유한 구성을 만듭니다.

> [!IMPORTANT]
> 게스트 구성이 포함된 사용자 지정 정책은 미리 보기 기능입니다.
>
> 게스트 구성 확장은 Azure Virtual Machine에서 감사를 수행하는 데 필요합니다. 모든 Linux 컴퓨터에서 확장을 대규모로 배포 하려면 다음 정책 정의를 할당 합니다. `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

게스트 구성 모듈은 다음과 같은 사용자 지정 콘텐츠를 만드는 프로세스를 자동화합니다.

- 게스트 구성 콘텐츠 아티팩트 만들기(.zip)
- 자동화된 아티팩트 테스트
- 정책 정의 만들기
- 정책 게시

모듈은 로컬에서 실행되는 PowerShell 6.2 이상 또는 [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell Core Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell-core)를 사용하는 Windows, macOS 또는 Linux를 실행하는 컴퓨터에 설치할 수 있습니다.

> [!NOTE]
> 구성 컴파일은 Linux에서 지원되지 않습니다.

### <a name="base-requirements"></a>기본 요구 사항

모듈을 설치할 수 있는 운영 체제는 다음과 같습니다.

- Linux
- macOS
- Windows

> [!NOTE]
> ' GuestConfigurationPackage ' cmdlet에는 OMI에 대 한 종속성으로 인해 OpenSSL 버전 1.0이 필요 합니다. 이로 인해 OpenSSL 1.1 이상의 환경에서 오류가 발생 합니다.

게스트 구성 리소스 모듈에는 다음 소프트웨어가 필요합니다.

- PowerShell 6.2 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/scripting/install/installing-powershell)을 따릅니다.
- Azure PowerShell 1.5.0 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
  - Az modules ' Az. Accounts ' 및 ' Az .Resources '만 필요 합니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell에서 **GuestConfiguration** 모듈을 설치하려면 다음을 수행합니다.

1. PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 모듈을 가져왔는지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux용 게스트 구성 아티팩트 및 정책

Linux 환경에서도 게스트 구성은 Desired State Configuration을 언어 추상화로 사용합니다. 구현은 네이티브 코드(C++)를 기반으로 하므로 PowerShell을 로드할 필요가 없습니다. 하지만 환경에 대한 세부 정보를 설명하는 구성 MOF가 필요합니다.
DSC는 InSpec에서 실행 방법, 매개 변수가 제공되는 방법 및 출력이 서비스에 반환되는 방법을 표준화하기 위한 래퍼 역할을 합니다. 사용자 지정 InSpec 콘텐츠로 작업하는 경우 DSC에 대한 지식이 거의 필요하지 않습니다.

#### <a name="configuration-requirements"></a>구성 요구 사항

사용자 지정 구성의 이름은 모든 위치에서 일관되어야 합니다. 콘텐츠 패키지용 .zip 파일의 이름, MOF 파일의 구성 이름 및 Azure Resource Manager 템플릿 (ARM 템플릿)의 게스트 할당 이름이 동일 해야 합니다.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux에서 사용자 지정 게스트 구성

Linux에서 게스트 구성 시 `ChefInSpecResource` 리소스를 사용하여 엔진에 [InSpec 프로필](https://www.inspec.io/docs/reference/profiles/)의 이름을 제공합니다. **이름**은 유일하게 필요한 리소스 속성입니다. 아래에 설명된 바와 같이 YaML 파일과 Ruby 스크립트 파일을 만듭니다.

먼저, InSpec에서 사용하는 YaML 파일을 만듭니다. 이 파일은 환경에 대한 기본 정보를 제공합니다. 예를 들면 다음과 같습니다.

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

프로젝트 디렉터리의 `linux-path`라는 폴더에 이름이 `inspec.yml`인 이 파일을 저장합니다.

그런 다음, 컴퓨터를 감사하는 데 사용되는 InSpec 언어 추상화를 사용하여 Ruby 파일을 만듭니다.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

`linux-path` 디렉터리 내 `controls`라는 새 폴더에 이름이 `linux-path.rb`인 이 파일을 저장합니다.

마지막으로 구성을 만들고, **PSDesiredStateConfiguration** 리소스 모듈을 가져오며, 구성을 컴파일합니다.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

프로젝트 폴더에 이름이 `config.ps1`인 이 파일을 저장합니다. 터미널에서 `./config.ps1`을 실행하여 PowerShell에서 실행합니다. 새 mof 파일이 만들어집니다.

`Node AuditFilePathExists` 명령은 기술적으로 필요하지 않지만, 기본값 `localhost.mof`가 아닌 `AuditFilePathExists.mof`라는 파일을 생성합니다. .mof 파일 이름을 구성에 따라 지정하면 대규모 작업을 수행할 때 많은 파일을 쉽게 구성할 수 있습니다.

이제 프로젝트 구조가 다음과 같이 표시됩니다.

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

지원 파일은 한 패키지에 포함되어야 합니다. 완료된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다.

`New-GuestConfigurationPackage` cmdlet은 패키지를 만듭니다. Linux 콘텐츠를 만들 때 `New-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **Name**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 구성 문서 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정하지 않으면 패키지가 현재 디렉터리에 만들어집니다.
- **ChefProfilePath**: InSpec 프로필의 전체 경로입니다. 이 매개 변수는 Linux를 감사할 콘텐츠를 만드는 경우에만 지원됩니다.

다음 명령을 실행하여 이전 단계에 지정된 구성을 사용하는 패키지를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

구성 패키지를 만든 후 Azure에 게시하기 전에 워크스테이션 또는 CI/CD 환경에서 패키지를 테스트할 수 있습니다. GuestConfiguration cmdlet `Test-GuestConfigurationPackage`에는 Azure 컴퓨터 내에서 사용되는 것과 동일한 에이전트가 개발 환경에 포함되어 있습니다. 이 솔루션을 사용하여 청구되는 클라우드 환경에 릴리스하기 전 로컬에서 통합 테스트를 수행할 수 있습니다.

에이전트는 실제로 로컬 환경을 평가하므로, 대부분의 경우 감사할 계획인 OS 플랫폼과 동일한 플랫폼에서 Test- cmdlet을 실행해야 합니다.

`Test-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **Name**: 게스트 구성 정책 이름입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.
- **경로**: 게스트 구성 패키지의 전체 경로입니다.

다음 명령을 실행하여 이전 단계에서 만든 패키지를 테스트합니다.

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

cmdlet은 PowerShell 파이프라인의 입력도 지원합니다. `New-GuestConfigurationPackage` cmdlet의 출력을 `Test-GuestConfigurationPackage` cmdlet으로 파이프합니다.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

다음 단계는 Blob Storage에 파일을 게시하는 과정입니다. 아래 스크립트에는 이 작업을 자동화하는 데 사용할 수 있는 함수가 포함되어 있습니다. `publish` 함수에 사용되는 명령에는 `Az.Storage` 모듈이 필요합니다.

```azurepowershell-interactive
function publish {
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

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
```
게스트 구성 사용자 지정 정책 패키지를 만들고 업로드한 후에는 게스트 구성 정책 정의를 만듭니다. `New-GuestConfigurationPolicy` cmdlet은 사용자 지정 정책 패키지를 사용하고 정책 정의를 만듭니다.

`New-GuestConfigurationPolicy` cmdlet의 매개 변수는 다음과 같습니다.

- **ContentUri**: 게스트 구성 콘텐츠 패키지의 공용 http(s) uri입니다.
- **DisplayName**: 정책 표시 이름입니다.
- **설명**: 정책 설명입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.
- **버전**: 정책 버전입니다.
- **경로**: 정책 정의가 만들어지는 대상 경로입니다.
- **Platform**: 게스트 구성 정책 및 콘텐츠 패키지용 대상 플랫폼(Windows/Linux)입니다.
- **Tag**는 정책 정의에 하나 이상의 태그 필터를 추가합니다.
- **Category**는 정책 정의의 범주 메타데이터 필드를 설정합니다.

다음 예제에서는 사용자 지정 정책 패키지에서 지정된 경로에 정책 정의를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy`에서 만들어지는 파일은 다음과 같습니다.

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

cmdlet 출력은 정책 파일의 이니셔티브 표시 이름과 경로가 포함된 개체를 반환합니다.

마지막으로 `Publish-GuestConfigurationPolicy` cmdlet을 사용하여 정책 정의를 게시합니다. cmdlet에는 `New-GuestConfigurationPolicy`에서 만든 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다.

Publish 명령을 실행하려면 Azure에서 정책을 만들기 위한 액세스 권한이 필요합니다. 특정 권한 부여 요구 사항은 [Azure Policy 개요](../overview.md) 페이지에 설명되어 있습니다. 가장 적합한 기본 제공 역할은 **리소스 정책 기여자**입니다.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy` cmdlet은 PowerShell 파이프라인의 경로를 허용합니다. 이 기능은 정책 파일을 만들어 단일 파이프 명령 집합에 게시할 수 있음을 의미합니다.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Azure에서 만든 정책을 사용하는 마지막 단계는 이니셔티브를 할당하는 과정입니다. [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) 및 [Azure PowerShell](../assign-policy-powershell.md)을 사용하여 이니셔티브를 할당하는 방법을 참조하세요.

> [!IMPORTANT]
> 게스트 구성 정책은 **항상** _AuditIfNotExists_와 _DeployIfNotExists_ 정책을 결합하는 이니셔티브를 사용하여 할당해야 합니다. _AuditIfNotExists_ 정책만 할당된 경우 필수 구성 요소가 배포되지 않으며 정책에 항상 '0' 서버가 규정을 준수함을 표시합니다.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>사용자 지정 게스트 구성 정책에서 매개 변수 사용

게스트 구성은 런타임 시 구성 속성 재정의를 지원합니다. 이 기능은 패키지의 MOF 파일에 있는 값을 정적으로 간주할 필요가 없음을 의미합니다. 재정의 값은 Azure Policy를 통해 제공되며 구성을 작성하거나 컴파일하는 방법에 영향을 주지 않습니다.

InSpec을 사용하는 매개 변수는 일반적으로 런타임 시 입력으로 또는 특성을 사용하는 코드로 처리됩니다. 게스트 구성은 정책이 할당될 때 입력을 제공할 수 있도록 이 프로세스를 난독 처리합니다. 특성 파일은 컴퓨터 내에 자동으로 만들어집니다. 프로젝트에서 파일을 만들고 추가할 필요가 없습니다. Linux 감사 프로젝트에 매개 변수를 추가하는 두 가지 단계가 있습니다.

컴퓨터에서 감사할 항목을 스크립팅하는 Ruby 파일에 대한 입력을 정의합니다. 예를 들면 다음과 같습니다.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdlet은 `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` **매개**변수 라는 매개 변수를 포함 합니다. 이 매개 변수는 각 매개 변수에 대한 세부 정보를 모두 포함하는 해시 테이블을 사용하며, 각 Azure Policy 정의를 만드는 데 사용되는 파일의 필수 섹션을 모두 자동으로 만듭니다.

다음 예에서는 사용자가 정책 할당 시 경로를 제공 하는 파일 경로를 감사 하는 정책 정의를 만듭니다.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

Linux 정책의 경우 구성에 **AttributesYmlContent** 속성을 포함하고 필요에 따라 값을 덮어씁니다. 게스트 구성 에이전트는 InSpec에서 특성을 저장하는 데 사용되는 YAML 파일을 자동으로 만듭니다. 아래 예제를 참조하세요.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>정책 수명 주기

정책 정의에 대한 업데이트를 릴리스하려면 주의해야 하는 필드가 두 가지 있습니다.

- **버전**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 현재 게시된 것보다 큰 버전 번호를 지정해야 합니다. 속성은 에이전트가 업데이트된 패키지를 인식하도록 게스트 구성 할당의 버전을 업데이트합니다.
- **contentHash**: 이 속성은 `New-GuestConfigurationPolicy` cmdlet에 의해 자동으로 업데이트됩니다. 이는 `New-GuestConfigurationPackage`에서 만든 패키지의 해시 값입니다. 게시하는 `.zip` 파일에 대한 속성이 정확해야 합니다. **contentUri** 속성만 업데이트된 경우 확장에서 콘텐츠 패키지를 수락하지 않습니다.

업데이트된 패키지를 릴리스하는 가장 쉬운 방법은 이 문서에 설명된 프로세스를 반복하고 업데이트된 버전 번호를 제공하는 것입니다. 해당 프로세스는 모든 속성이 올바르게 업데이트되었음을 보장합니다.


### <a name="filtering-guest-configuration-policies-using-tags"></a>태그를 사용하여 게스트 구성 정책 필터링

게스트 구성 모듈에서 cmdlet에 의해 만들어진 정책에는 선택적으로 태그에 대한 필터가 포함될 수 있습니다. `New-GuestConfigurationPolicy`의 **-Tag** 매개 변수는 개별 태그 전체를 포함하는 해시 테이블의 배열을 지원합니다. 태그는 정책 정의의 `If` 섹션에 추가되며 정책 할당으로 수정할 수 없습니다.

태그를 필터링하는 정책 정의의 예제 조각은 다음과 같습니다.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>선택 사항: 게스트 구성 패키지 서명

게스트 구성 사용자 지정 정책은 SHA256 해시를 사용하여 변경되지 않은 정책 패키지의 유효성을 검사합니다.
또한 필요에 따라 고객은 인증서를 사용하여 패키지에 서명하고 게스트 구성 확장에서 강제로 서명된 콘텐츠만 허용하도록 할 수 있습니다.

이 시나리오를 사용하려면 두 단계를 완료해야 합니다. cmdlet을 실행하여 콘텐츠 패키지에 서명하고 코드에 서명해야 하는 컴퓨터에 태그를 추가합니다.

서명 유효성 검사 기능을 사용하려면 `Protect-GuestConfigurationPackage` cmdlet을 실행하여 게시하기 전 패키지에 서명합니다. 이 cmdlet에는 '코드 서명' 인증서가 필요합니다.

`Protect-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **PublicGpgKeyPath**: 공개 GPG 키 경로입니다. 이 매개 변수는 Linux용 콘텐츠에 서명하는 경우에만 지원됩니다.

Linux 컴퓨터에서 사용할 GPG 키를 만드는 방법에 대한 좋은 참고 자료는 GitHub의 문서, [ 새 GPG 키 만들기](https://help.github.com/en/articles/generating-a-new-gpg-key)에 나와 있습니다.

GuestConfiguration 에이전트는 Linux 컴퓨터의 `/usr/local/share/ca-certificates/extra` 경로에 인증서 공개 키가 있어야 합니다. 노드에서 서명된 콘텐츠를 확인하려면 사용자 지정 정책을 적용하기 전 컴퓨터에 인증서 공개 키를 설치합니다. 이 프로세스는 VM 내에서 또는 Azure Policy를 사용하여 수행할 수 있습니다. 예제 템플릿이 [여기에 나와](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows) 있습니다.
Key Vault 액세스 정책은 배포하는 동안 Compute 리소스 공급자가 인증서에 액세스할 수 있도록 허용해야 합니다. 자세한 단계는 [Azure Resource Manager에서 가상 머신에 대한 Key Vault 설정](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)을 참조하세요.

콘텐츠를 게시한 후에는 이름이 `GuestConfigPolicyCertificateValidation`이고 값이 `enabled`인 태그를 코드 서명이 필요한 모든 가상 머신에 추가합니다. Azure Policy를 사용하여 태그를 대규모로 제공할 수 있는 방법은 [태그 샘플](../samples/built-in-policies.md#tags)을 참조하세요. 이 태그가 준비되면 `New-GuestConfigurationPolicy` cmdlet을 이용하여 생성된 정책 정의에서 게스트 구성 확장을 통해 요구 사항을 사용합니다.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>게스트 구성 정책 할당 문제 해결(미리 보기)

Azure Policy 게스트 구성 할당 문제를 해결하는 데 도움이 되는 도구가 미리 보기로 제공됩니다. 이 도구는 미리 보기 상태이며, PowerShell 갤러리에 [게스트 구성 문제 해결사](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)라는 모듈로 게시되었습니다.

이 도구의 cmdlet에 대한 자세한 내용은 PowerShell에서 Get-help 명령을 사용하여 기본 제공 지침을 확인하세요. 이 도구를 자주 업데이트하는 것이 최신 정보를 얻는 가장 좋은 방법입니다.

## <a name="next-steps"></a>다음 단계

- [게스트 구성](../concepts/guest-configuration.md)을 사용하여 VM을 감사하는 방법을 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](./programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](./get-compliance-data.md) 방법을 알아봅니다.
