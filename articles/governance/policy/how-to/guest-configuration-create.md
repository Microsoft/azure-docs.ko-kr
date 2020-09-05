---
title: Windows용 게스트 구성 정책을 만드는 방법
description: Windows용 Azure Policy 게스트 구성 정책을 만드는 방법에 대해 알아봅니다.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 36e71f00a4613e1723645f48d9e57aed9e1e9a8a
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719396"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Windows용 게스트 구성 정책을 만드는 방법

사용자 지정 정책 정의를 만들기 전에 [Azure Policy 게스트 구성](../concepts/guest-configuration.md) 페이지에서 개념 개요 정보를 읽는 것이 좋습니다.
 
Linux용 게스트 구성 정책을 만드는 방법에 대한 자세한 내용은 [Linux용 게스트 구성 정책을 만드는 방법](./guest-configuration-create-linux.md) 페이지를 참조하세요.

Windows를 감사할 때 게스트 구성은 DSC([Desired State Configuration](/powershell/scripting/dsc/overview/overview)) 리소스 모듈을 사용하여 구성 파일을 만듭니다. DSC 구성은 컴퓨터가 충족해야 하는 조건을 정의합니다. 구성 평가에 실패하는 경우 정책 효과 **auditIfNotExists**가 트리거되고 컴퓨터를 **비준수**로 간주합니다.

[Azure Policy 게스트 구성](../concepts/guest-configuration.md)은 컴퓨터 내의 설정을 감사하는 데만 사용할 수 있습니다. 컴퓨터 내에서 설정 수정은 아직 사용할 수 없습니다.

다음 작업을 사용하여 Azure 또는 비 Azure 컴퓨터 상태의 유효성을 검사하는 고유한 구성을 만듭니다.

> [!IMPORTANT]
> 게스트 구성이 포함된 사용자 지정 정책은 미리 보기 기능입니다.
>
> 게스트 구성 확장은 Azure Virtual Machines에서 감사를 수행하는 데 필요합니다.
> 모든 Windows 컴퓨터에서 확장을 대규모로 배포 하려면 다음 정책 정의를 할당 합니다. `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`

## <a name="install-the-powershell-module"></a>PowerShell 모듈 설치

게스트 구성 모듈은 다음과 같은 사용자 지정 콘텐츠를 만드는 프로세스를 자동화합니다.

- 게스트 구성 콘텐츠 아티팩트 만들기(.zip)
- 자동화된 아티팩트 테스트
- 정책 정의 만들기
- 정책 게시

모듈은 로컬에서 실행되는 PowerShell 6.2 이상, [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell Core Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell-core)를 사용한 Windows, macOS 또는 Linux를 실행하는 컴퓨터에 설치할 수 있습니다.

> [!NOTE]
> 구성 컴파일은 Linux에서 아직 지원되지 않습니다.

### <a name="base-requirements"></a>기본 요구 사항

모듈을 설치할 수 있는 운영 체제는 다음과 같습니다.

- Linux
- macOS
- Windows

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

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Windows용 게스트 구성 아티팩트 및 정책

게스트 구성은 Windows에서 감사할 항목을 작성하기 위해 PowerShell Desired State Configuration을 언어 추상화로 사용합니다. 에이전트는 PowerShell 6.2의 독립 실행형 인스턴스를 로드하므로 Windows PowerShell 5.1에서 PowerShell DSC 사용과 충돌하지 않으며 PowerShell 6.2 이상을 미리 설치하지 않아도 됩니다.

DSC 개념 및 용어에 대한 개요는 [PowerShell DSC 개요](/powershell/scripting/dsc/overview/overview)를 참조하세요.

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>게스트 구성 모듈과 Windows PowerShell DSC 모듈의 차이점

게스트 구성에서 컴퓨터를 감사 하는 경우 이벤트 시퀀스는 Windows PowerShell DSC와는 다릅니다.

1. 에이전트는 먼저 `Test-TargetResource`를 실행하여 구성이 올바른 상태인지 확인합니다.
1. 함수에서 반환하는 부울 값이 게스트 할당에 대한 Azure Resource Manager 상태가 규정 준수/비준수 여부를 결정합니다.
1. 공급자는 `Get-TargetResource`를 실행하여 각 설정의 현재 상태를 반환하므로 컴퓨터가 규정을 준수하지 않는 이유 및 현재 상태가 규정을 준수하는지 확인하기 위한 세부 정보를 사용할 수 있습니다.

게스트 구성 할당에 값을 전달 하는 Azure Policy의 매개 변수는 _문자열_ 유형 이어야 합니다. DSC 리소스가 배열을 지 원하는 경우에도 매개 변수를 통해 배열을 전달할 수 없습니다.

### <a name="get-targetresource-requirements"></a>Get-TargetResource 요구 사항

함수 `Get-TargetResource`에는 Windows Desired State Configuration에 필요하지 않은 게스트 구성에 대한 특별 요구 사항이 있습니다.

- 반환되는 hashtable에 **Reasons**라는 속성이 포함되어야 합니다.
- Reasons 속성은 배열이어야 합니다.
- 배열의 각 항목은 **Code** 및 **Phrase**라는 키가 있는 hashtable이어야 합니다.

서비스에서 Reasons 속성을 사용하여 컴퓨터가 규정을 준수하지 않는 경우 정보를 표시하는 방식을 표준화합니다. Reasons의 각 항목은 리소스가 규정을 준수하지 않는 "이유"라고 간주할 수 있습니다. 두 가지 이상의 이유로 리소스가 규정을 준수하지 않을 수 있으므로 속성은 배열입니다.

**Code** 및 **Phrase** 속성이 서비스에 필요합니다. 사용자 지정 리소스를 작성하는 경우 리소스가 규정을 준수하지 않는 이유를 표시하려는 텍스트(일반적으로 stdout)를 **Phrase** 값으로 설정합니다. **Code**에는 특정 형식 요구 사항이 있으므로 감사를 수행하는 데 사용되는 리소스에 대한 정보를 명확하게 보고할 수 있습니다. 이 솔루션은 게스트 구성을 확장 가능하게 만듭니다. 출력을 **Phrase** 속성에 대한 문자열 값으로 반환할 수 있는 한 모든 명령을 실행할 수 있습니다.

- **Code**(문자열): 리소스의 이름은 반복되고 이유에 대한 식별자로 공백이 없는 짧은 이름입니다. 이 세 값은 공백 없이 콜론으로 구분되어야 합니다.
  - 예를 들어 `registry:registry:keynotpresent`입니다.
- **Phrase**(문자열): 설정이 규정을 준수하지 않는 이유를 설명하는 사람이 읽을 수 있는 텍스트입니다.
  - 예를 들어 `The registry key $key is not present on the machine.`입니다.

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

이유 속성은 리소스에 대 한 스키마 MOF에 포함 클래스로 추가 되어야 합니다.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>구성 요구 사항

사용자 지정 구성의 이름은 모든 위치에서 일관되어야 합니다. 콘텐츠 패키지용 .zip 파일의 이름, MOF 파일의 구성 이름 및 Azure Resource Manager 템플릿 (ARM 템플릿)의 게스트 할당 이름이 동일 해야 합니다.

### <a name="scaffolding-a-guest-configuration-project"></a>게스트 구성 프로젝트 스캐폴딩

샘플 코드에서 시작 및 작업 프로세스를 가속화하려는 개발자는 **게스트 구성 프로젝트**라는 커뮤니티 프로젝트를 설치할 수 있습니다. 프로젝트는 [Plaster](https://github.com/powershell/plaster) PowerShell 모듈에 대한 템플릿을 설치합니다. 이 도구를 사용하여 작업 구성 및 샘플 리소스를 비롯한 프로젝트와 프로젝트의 유효성을 검사하는 [Pester](https://github.com/pester/pester) 테스트 집합을 스캐폴딩할 수 있습니다. 템플릿에는 게스트 구성 패키지의 빌드 및 유효성 검사를 자동화하는 Visual Studio Code용 작업 실행기도 포함되어 있습니다. 자세한 내용은 GitHub 프로젝트 [게스트 구성 프로젝트](https://github.com/microsoft/guestconfigurationproject)를 참조하세요.

일반적인 구성 작업에 대한 자세한 내용은 [구성 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)을 참조하세요.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>게스트 구성 아티팩트의 예상 콘텐츠

완성된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다. 패키지는 다음으로 구성됩니다.

- MOF로 컴파일된 DSC 구성
- 모듈 폴더
  - GuestConfiguration 모듈
  - DscNativeResources 모듈
  - (Windows) MOF에 필요한 DSC 리소스 모듈

PowerShell cmdlet은 패키지를 만드는 데 도움이 됩니다.
루트 수준 폴더 또는 버전 폴더는 필요하지 않습니다.
패키지 형식은 .zip 파일이어야 합니다.

### <a name="storing-guest-configuration-artifacts"></a>게스트 구성 아티팩트 저장

.zip 패키지는 관리되는 가상 머신이 액세스할 수 있는 위치에 저장해야 합니다.
이러한 예로는 GitHub 리포지토리, Azure Repo 또는 Azure Storage가 있습니다. 패키지를 공용으로 설정하지 않으려는 경우 URL에 [SAS 토큰](../../../storage/common/storage-sas-overview.md)을 포함시킬 수 있습니다. 이 구성은 패키지에 액세스하고 서비스와 통신하지 않는 경우에만 적용되지만 개인 네트워크에서 컴퓨터에 대한 [서비스 엔드포인트](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)를 구현할 수도 있습니다.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Windows에 대한 사용자 지정 게스트 구성 감사 정책 만들기 단계별 안내

설정을 감사할 DSC 구성을 만듭니다. 다음 PowerShell 스크립트 예에서는 **AuditBitLocker**라는 구성을 만들고 **PsDscResources** 리소스 모듈을 가져오며 `Service` 리소스를 사용하여 실행 중인 서비스를 감사합니다. 구성 스크립트는 Windows 또는 macOS 컴퓨터에서 실행할 수 있습니다.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

프로젝트 폴더에 `config.ps1`로 이 파일을 저장합니다. 터미널에서 `./config.ps1`을 실행하여 PowerShell에서 실행합니다. 새 mof 파일이 생성됩니다.

`Node AuditBitlocker` 명령은 기술적으로 필요하지 않지만 기본값 `localhost.mof`가 아닌 `AuditBitlocker.mof`라는 파일을 생성합니다. .mof 파일 이름을 구성에 따라 지정하면 대규모 작업을 수행할 때 많은 파일을 쉽게 구성할 수 있습니다.

MOF가 컴파일되면 지원 파일을 함께 패키지해야 합니다. 완성된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다.

`New-GuestConfigurationPackage` cmdlet은 패키지를 만듭니다. 구성에 필요한 모듈은 `$Env:PSModulePath`에서 사용할 수 있어야 합니다. Windows 콘텐츠를 만들 때 `New-GuestConfigurationPackage` cmdlet의 매개 변수:

- **Name**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 DSC 구성 문서의 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정하지 않으면 패키지가 현재 디렉터리에 만들어집니다.

다음 명령을 실행하여 이전 단계에서 지정한 구성을 사용한 패키지를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

구성 패키지를 만든 후 Azure에 게시하기 전에 워크스테이션 또는 CI/CD 환경에서 패키지를 테스트할 수 있습니다. GuestConfiguration cmdlet `Test-GuestConfigurationPackage`에는 개발 환경에서 Azure 컴퓨터에서 사용되는 것과 동일한 에이전트가 포함되어 있습니다. 이 솔루션을 사용하여 청구되는 클라우드 환경에 릴리스하기 전에 로컬에서 통합 테스트를 수행할 수 있습니다.

에이전트는 실제로 로컬 환경을 평가하므로 대부분의 경우 감사하려는 OS 플랫폼과 동일한 OS 플랫폼에서 테스트 cmdlet을 실행해야 합니다. 테스트는 콘텐츠 패키지에 포함된 모듈만 사용합니다.

`Test-GuestConfigurationPackage` cmdlet의 매개 변수:

- **Name**: 게스트 구성 정책 이름입니다.
- **Parameter**: hashtable 형식으로 제공되는 정책 매개 변수입니다.
- **경로**: 게스트 구성 패키지의 전체 경로입니다.

다음 명령을 실행하여 이전 단계에서 만든 패키지를 테스트합니다.

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

cmdlet은 PowerShell 파이프라인의 입력도 지원합니다. `New-GuestConfigurationPackage` cmdlet의 출력을 `Test-GuestConfigurationPackage` cmdlet으로 파이프합니다.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
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
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy`에서 만들어지는 파일은 다음과 같습니다.

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

cmdlet 출력은 정책 파일의 이니셔티브 표시 이름과 경로가 포함된 개체를 반환합니다.

마지막으로 `Publish-GuestConfigurationPolicy` cmdlet을 사용하여 정책 정의를 게시합니다. cmdlet에는 `New-GuestConfigurationPolicy`로 만든 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다.

게시 명령을 실행하려면 Azure에서 정책을 만들기 위한 액세스 권한이 필요합니다. 특정 권한 부여 요구 사항은 [Azure Policy 개요](../overview.md) 페이지에 설명되어 있습니다. 가장 적합한 기본 제공 역할은 **리소스 정책 기여자**입니다.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy` cmdlet은 PowerShell 파이프라인의 경로를 허용합니다. 이 기능은 정책 파일을 만들어 단일 파이프 명령 집합에 게시할 수 있음을 의미합니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Azure에서 만든 정책을 사용하는 마지막 단계는 이니셔티브를 할당하는 과정입니다. [Portal](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) 및 [Azure PowerShell](../assign-policy-powershell.md)을 사용하여 이니셔티브를 할당하는 방법을 참조하세요.

> [!IMPORTANT]
> 게스트 구성 정책은 **항상** _AuditIfNotExists_와 _DeployIfNotExists_ 정책을 결합하는 이니셔티브를 사용하여 할당해야 합니다. _AuditIfNotExists_ 정책만 할당된 경우 필수 구성 요소가 배포되지 않으며 정책에 항상 '0' 서버가 규정을 준수함을 표시합니다.

_DeployIfNotExists_ 효과와 함께 정책 정의를 할당하려면 추가 액세스 수준이 필요합니다. 최소 권한을 부여하려면 **리소스 정책 기여자**를 확장하는 사용자 지정 역할 정의를 만들 수 있습니다. 아래 예에서는 _Microsoft.Authorization/roleAssignments/write_ 추가 권한을 사용하여 **리소스 정책 기여자 DINE**이라는 역할을 만듭니다.

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

### <a name="filtering-guest-configuration-policies-using-tags"></a>태그를 사용하여 게스트 구성 정책 필터링

게스트 구성 모듈에서 cmdlet으로 생성되는 정책 정의에는 선택적으로 태그에 대한 필터가 포함될 수 있습니다. `New-GuestConfigurationPolicy`의 **Tag** 매개 변수는 개별 태그 항목을 포함하는 hashtable 배열을 지원합니다. 태그는 `If` 정책 정의의 섹션에 추가 되며 정책 할당으로 수정할 수 없습니다.

태그를 필터링하는 정책 정의의 예제 코드 조각은 아래에 제공되어 있습니다.

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
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>사용자 지정 게스트 구성 정책 정의에서 매개 변수 사용

게스트 구성은 런타임 시 구성의 속성 재정의를 지원합니다. 이 기능은 패키지의 MOF 파일에 있는 값을 정적으로 간주할 필요가 없음을 의미합니다. 재정의 값은 Azure Policy를 통해 제공되며 구성을 작성하거나 컴파일하는 방법에 영향을 주지 않습니다.

Cmdlet은 `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` **매개**변수 라는 매개 변수를 포함 합니다. 이 매개 변수는 각 매개 변수에 대한 모든 세부 정보를 포함하는 hashtable 정의를 사용하고 Azure Policy 정의에 사용되는 각 파일의 필수 섹션을 만듭니다.

다음 예에서는 서비스를 감사할 정책 정의를 만듭니다. 사용자는 정책 할당 시 이를 목록에서 선택할 수 있습니다.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>타사 도구를 사용하여 게스트 구성 확장

게스트 구성에 대한 아티팩트 패키지를 확장하여 타사 도구를 포함할 수 있습니다.
게스트 구성을 확장하려면 두 가지 구성 요소를 개발해야 합니다.

- 타사 도구 관리와 관련된 모든 작업을 처리하는 Desired State Configuration 리소스
  - 설치
  - 호출
  - 출력 변환
- 도구에서 기본적으로 사용하는 올바른 형식의 콘텐츠

커뮤니티 솔루션이 아직 없는 경우 DSC 리소스에는 사용자 지정 개발이 필요 합니다.
커뮤니티 솔루션은 PowerShell 갤러리에서 [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) 태그를 검색하여 찾을 수 있습니다.

> [!Note]
> 게스트 구성 확장성은 "사용자 라이선스 필요" 시나리오입니다. 사용하기 전에 타사 도구의 사용 약관을 충족하는지 확인합니다.

DSC 리소스를 개발 환경에 설치한 후에는 `New-GuestConfigurationPackage`에 대한 **FilesToInclude** 매개 변수를 사용하여 콘텐츠 아티팩트의 타사 플랫폼에 대한 콘텐츠를 포함합니다.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>타사 도구를 사용하는 콘텐츠 아티팩트 만들기 단계별 안내

`New-GuestConfigurationPackage` cmdlet만 DSC 콘텐츠 아티팩트에 대한 단계별 지침을 변경해야 합니다. 이 예에서는 `gcInSpec` 모듈을 사용하여 Linux에서 사용되는 기본 제공 모듈이 아닌 InSpec 플랫폼을 사용하여 Windows 컴퓨터를 감사하도록 게스트 구성을 확장합니다. 커뮤니티 모듈은 [GitHub에서 오픈 소스 프로젝트](https://github.com/microsoft/gcinspec)로 유지됩니다.

개발 환경에 필요한 모듈을 설치합니다.

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

먼저 InSpec에서 사용하는 YaML 파일을 만듭니다. 이 파일은 환경에 대한 기본 정보를 제공합니다. 예를 들면 다음과 같습니다.

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

이 파일 `wmi_service.yml` 을 프로젝트 디렉터리에 이름이 지정 된 폴더에 저장 `wmi_service` 합니다.

그런 다음, 컴퓨터를 감사하는 데 사용되는 InSpec 언어 추상화를 사용하여 Ruby 파일을 만듭니다.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

이 파일을 `wmi_service.rb` 디렉터리 내 이름이 지정 된 새 폴더에 저장 `controls` `wmi_service` 합니다.

마지막으로 구성을 만들고 **GuestConfiguration** 리소스 모듈을 가져오고 `gcInSpec` 리소스를 사용하여 InSpec 프로필의 이름을 설정합니다.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

이제 프로젝트 구조가 다음과 같이 표시됩니다.

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

지원 파일은 한 패키지에 포함되어야 합니다. 완료된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용됩니다.

`New-GuestConfigurationPackage` cmdlet은 패키지를 만듭니다. 타사 콘텐츠의 경우 **FilesToInclude** 매개 변수를 사용하여 패키지에 InSpec 콘텐츠를 추가합니다. Linux 패키지용으로 **ChefProfilePath** 을 지정할 필요가 없습니다.

- **Name**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 구성 문서 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정하지 않으면 패키지가 현재 디렉터리에 만들어집니다.
- **FilesoInclude**: InSpec 프로필의 전체 경로입니다.

다음 명령을 실행하여 이전 단계에서 지정한 구성을 사용한 패키지를 만듭니다.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>정책 수명 주기

정책에 대한 업데이트를 릴리스하려는 경우 주의가 필요한 두 개의 필드가 있습니다.

- **버전**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 현재 게시된 버전보다 큰 버전 번호를 지정해야 합니다. 속성은 에이전트가 업데이트된 패키지를 인식하도록 게스트 구성 할당의 버전을 업데이트합니다.
- **contentHash**: 이 속성은 `New-GuestConfigurationPolicy` cmdlet에 의해 자동으로 업데이트됩니다. 이는 `New-GuestConfigurationPackage`에서 만든 패키지의 해시 값입니다. 게시하는 `.zip` 파일에 대한 속성이 정확해야 합니다. **contentUri** 속성만 업데이트된 경우 확장에서 콘텐츠 패키지를 수락하지 않습니다.

업데이트된 패키지를 릴리스하는 가장 쉬운 방법은 이 문서에 설명된 프로세스를 반복하고 업데이트된 버전 번호를 제공하는 것입니다. 해당 프로세스는 모든 속성이 올바르게 업데이트되었음을 보장합니다.

## <a name="optional-signing-guest-configuration-packages"></a>선택 사항: 게스트 구성 패키지 서명

게스트 구성 사용자 지정 정책은 SHA256 해시를 사용하여 변경되지 않은 정책 패키지의 유효성을 검사합니다.
또한 필요에 따라 고객은 인증서를 사용하여 패키지에 서명하고 게스트 구성 확장에서 강제로 서명된 콘텐츠만 허용하도록 할 수 있습니다.

이 시나리오를 사용하려면 두 단계를 완료해야 합니다. cmdlet을 실행하여 콘텐츠 패키지에 서명하고 코드에 서명해야 하는 컴퓨터에 태그를 추가합니다.

서명 유효성 검사 기능을 사용하려면 `Protect-GuestConfigurationPackage` cmdlet을 실행하여 게시하기 전 패키지에 서명합니다. 이 cmdlet에는 '코드 서명' 인증서가 필요합니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` cmdlet의 매개 변수는 다음과 같습니다.

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **인증서**: 패키지에 서명하기 위한 코드 서명 인증서입니다. 이 매개 변수는 Windows 콘텐츠에 서명하는 경우에만 지원됩니다.

GuestConfiguration 에이전트는 인증서 공개 키가 Windows 컴퓨터의 "신뢰할 수 있는 루트 인증서 인증 기관" 및 Linux 컴퓨터의 `/usr/local/share/ca-certificates/extra` 경로에 있다고 간주합니다. 노드가 서명된 콘텐츠를 확인하려면 사용자 지정 정책을 적용하기 전에 컴퓨터에 인증서 공개 키를 설치합니다. 이 프로세스는 VM 내의 모든 기술 또는 Azure Policy를 사용하여 수행할 수 있습니다. 템플릿 예는 [여기에서 제공합니다](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Key Vault 액세스 정책은 배포하는 동안 Compute 리소스 공급자가 인증서에 액세스할 수 있도록 허용해야 합니다. 자세한 단계는 [Azure Resource Manager에서 가상 머신에 대한 Key Vault 설정](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)을 참조하세요.

다음은 서명 인증서에서 공개 키를 내보내 컴퓨터로 가져오는 예입니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

콘텐츠를 게시한 후에는 이름이 `GuestConfigPolicyCertificateValidation`이고 값이 `enabled`인 태그를 코드 서명이 필요한 모든 가상 머신에 추가합니다. Azure Policy를 사용하여 태그를 대규모로 제공할 수 있는 방법은 [태그 샘플](../samples/built-in-policies.md#tags)을 참조하세요. 이 태그가 준비되면 `New-GuestConfigurationPolicy` cmdlet을 이용하여 생성된 정책 정의에서 게스트 구성 확장을 통해 요구 사항을 사용합니다.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>게스트 구성 정책 할당 문제 해결(미리 보기)

Azure Policy 게스트 구성 할당 문제를 해결하는 데 도움이 되는 도구가 미리 보기로 제공됩니다. 이 도구는 미리 보기 상태이며, PowerShell 갤러리에 [게스트 구성 문제 해결사](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)라는 모듈로 게시되었습니다.

이 도구의 cmdlet에 대한 자세한 내용은 PowerShell에서 Get-help 명령을 사용하여 기본 제공 지침을 확인하세요. 이 도구를 자주 업데이트하는 것이 최신 정보를 얻는 가장 좋은 방법입니다.

## <a name="next-steps"></a>다음 단계

- [게스트 구성](../concepts/guest-configuration.md)을 사용하여 VM을 감사하는 방법을 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](./programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](./get-compliance-data.md) 방법을 알아봅니다.
