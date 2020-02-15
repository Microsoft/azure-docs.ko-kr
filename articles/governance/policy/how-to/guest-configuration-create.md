---
title: 게스트 구성 정책을 만드는 방법
description: Azure PowerShell를 사용 하 여 Windows 또는 Linux Vm에 대 한 Azure Policy 게스트 구성 정책을 만드는 방법에 대해 알아봅니다.
ms.date: 12/16/2019
ms.topic: how-to
ms.openlocfilehash: 8bd769b61ed87c9ded45ceca11586cfe105740c9
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167178"
---
# <a name="how-to-create-guest-configuration-policies"></a>게스트 구성 정책을 만드는 방법

게스트 구성은 DSC ( [필요한 상태 구성](/powershell/scripting/dsc/overview/overview) ) 리소스 모듈을 사용 하 여 Azure 컴퓨터의 감사에 대 한 구성을 만듭니다. DSC 구성은 컴퓨터가 속해야 하는 조건을 정의 합니다. 구성 평가에 실패 하는 경우 정책 효과 **auditIfNotExists** 가 트리거되고 컴퓨터가 **비준수**로 간주 됩니다.

[Azure Policy 게스트 구성은](../concepts/guest-configuration.md) 컴퓨터 내의 설정을 감사 하는 데만 사용할 수 있습니다. 컴퓨터 내에서 설정의 재구성은 아직 사용할 수 없습니다.

다음 작업을 사용 하 여 Azure 컴퓨터의 상태를 확인 하는 고유한 구성을 만들 수 있습니다.

> [!IMPORTANT]
> 게스트 구성을 사용 하는 사용자 지정 정책은 미리 보기 기능입니다.

## <a name="add-the-guestconfiguration-resource-module"></a>GuestConfiguration 리소스 모듈을 추가 합니다.

게스트 구성 정책을 만들려면 리소스 모듈을 추가 해야 합니다. 이 리소스 모듈은 로컬에 설치 된 PowerShell, [Azure Cloud Shell](https://shell.azure.com)또는 [Azure PowerShell 핵심 Docker 이미지](https://hub.docker.com/r/azuresdk/azure-powershell-core)와 함께 사용할 수 있습니다.

> [!NOTE]
> **GuestConfiguration** 모듈이 위의 환경에서 작동 하는 동안 DSC 구성을 컴파일하는 단계는 Windows PowerShell 5.1에서 완료 해야 합니다.

### <a name="base-requirements"></a>기본 요구 사항

게스트 구성 리소스 모듈에는 다음 소프트웨어가 필요 합니다.

- PowerShell을 사용하여 키 백업 파일 복원 아직 설치되지 않은 경우 [다음 지침](/powershell/scripting/install/installing-powershell)을 따릅니다.
- Azure PowerShell 1.5.0 이상입니다. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.

### <a name="install-the-module"></a>모듈 설치

게스트 구성은 **GuestConfiguration** 리소스 모듈을 사용 하 여 DSC 구성을 만들고 Azure Policy에 게시 합니다.

1. PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 모듈을 가져왔는지 확인 합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>사용자 지정 게스트 구성 구성 및 리소스 만들기

게스트 구성에 대 한 사용자 지정 정책을 만드는 첫 번째 단계는 DSC 구성을 만드는 것입니다. DSC 개념 및 용어에 대 한 개요는 [POWERSHELL Dsc 개요](/powershell/scripting/dsc/overview/overview)를 참조 하세요.

구성에 게스트 구성 에이전트 설치와 함께 제공 되는 리소스만 필요한 경우에는 구성 MOF 파일을 작성 하기만 하면 됩니다. 추가 스크립트를 실행 해야 하는 경우 사용자 지정 리소스 모듈을 작성 해야 합니다.

### <a name="requirements-for-guest-configuration-custom-resources"></a>게스트 구성 사용자 지정 리소스에 대 한 요구 사항

게스트 구성에서 컴퓨터를 감사 하는 경우 먼저 `Test-TargetResource`를 실행 하 여 올바른 상태 인지 확인 합니다. 함수에서 반환 하는 부울 값은 게스트 할당에 대 한 Azure Resource Manager 상태를 준수/비준수로 지정 해야 하는지 여부를 결정 합니다. 구성의 모든 리소스에 대해 부울이 `$false` 되 면 공급자가 `Get-TargetResource`실행 됩니다. 부울이 `$true` 이면 `Get-TargetResource` 호출 되지 않습니다.

#### <a name="configuration-requirements"></a>구성 요구 사항

사용자 지정 구성을 사용 하기 위한 게스트 구성의 유일한 요구 사항은 구성의 이름이 사용 되는 모든 위치에서 일관성을 유지 하는 것입니다. 이 이름 요구 사항에는 콘텐츠 패키지에 대 한 .zip 파일의 이름, 콘텐츠 패키지 내에 저장 된 MOF 파일의 구성 이름 및 게스트 할당 이름으로 리소스 관리자 템플릿에 사용 되는 구성 이름이 포함 됩니다.

#### <a name="get-targetresource-requirements"></a>Test-targetresource 요구 사항

함수 `Get-TargetResource`에는 Windows 필요한 상태 구성에 필요 하지 않은 게스트 구성에 대 한 특별 한 요구 사항이 있습니다.

- 반환 되는 해시 테이블에는 **이유**라는 속성이 포함 되어야 합니다.
- 이유 속성은 배열 이어야 합니다.
- 배열의 각 항목은 **코드** 및 **구**라는 키가 있는 해시 테이블 이어야 합니다.

이유 속성은 서비스에서 컴퓨터가 정책을 준수 하지 않는 경우 정보가 표시 되는 방식을 표준화 하는 데 사용 됩니다. 각 항목에는 리소스가 호환 되지 않는 "이유"로 생각할 수 있습니다. 두 가지 이상의 이유로 리소스가 호환 되지 않을 수 있기 때문에 속성은 배열입니다.

서비스에서 속성 **코드** 및 **구가** 필요 합니다. 사용자 지정 리소스를 작성 하는 경우 리소스를 준수 하지 않는 원인으로 표시 하려는 텍스트 (일반적으로 stdout)를 **구의**값으로 설정 합니다. **코드** 에는 특정 형식 지정 요구 사항이 있으므로 보고에서 감사를 수행 하는 데 사용 된 리소스에 대 한 정보를 명확 하 게 표시할 수 있습니다. 이 솔루션은 게스트 구성을 확장 가능 하 게 만듭니다. 출력을 캡처하여 **구** 속성의 문자열 값으로 반환할 수 있는 경우 모든 명령을 실행 하 여 컴퓨터를 감사할 수 있습니다.

- **Code** (string): 리소스의 이름으로, 반복 된 후 이유에 대 한 식별자로 공백 없이 짧은 이름입니다. 이 세 값은 공백 없이 콜론으로 구분 되어야 합니다.
  - 예를 들어 `registry:registry:keynotpresent`
- **구** (문자열): 사람이 읽을 수 있는 텍스트로, 설정이 호환 되지 않는 이유를 설명 합니다.
  - 예를 들어 `The registry key $key is not present on the machine.`

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

#### <a name="scaffolding-a-guest-configuration-project"></a>게스트 구성 프로젝트 스 캐 폴딩

샘플 코드를 시작 하 고 작업 하는 프로세스를 가속화 하려는 개발자를 위해 **게스트 구성 프로젝트** 라는 커뮤니티 프로젝트는 [Plaster](https://github.com/powershell/plaster) PowerShell 모듈에 대 한 템플릿으로 존재 합니다. 이 도구를 사용 하 여 작업 구성 및 샘플 리소스를 비롯 한 프로젝트를 스 캐 폴드 프로젝트의 유효성을 검사 하는 일련의 [Pester](https://github.com/pester/pester) 테스트를 수행할 수 있습니다. 템플릿에는 게스트 구성 패키지의 빌드 및 유효성 검사를 자동화 하는 Visual Studio Code에 대 한 작업 러너도 포함 되어 있습니다. 자세한 내용은 GitHub 프로젝트 [게스트 구성 프로젝트](https://github.com/microsoft/guestconfigurationproject)를 참조 하세요.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux의 사용자 지정 게스트 구성 구성

Linux에서 게스트 구성에 대 한 DSC 구성은 `ChefInSpecResource` 리소스를 사용 하 여 엔진에 [Chef InSpec](https://www.chef.io/inspec/) 정의의 이름을 제공 합니다. **Name** 은 유일 하 게 필요한 리소스 속성입니다.

다음 예제에서는 **GuestConfiguration** 이라는 구성을 만들고 **,이 리소스**모듈을 가져오고, `ChefInSpecResource` 리소스를 사용 하 여 InSpec 정의의 이름을 **linux 패치 기준**으로 설정 합니다.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

자세한 내용은 [구성 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)을 참조 하세요.

### <a name="custom-guest-configuration-configuration-on-windows"></a>Windows의 사용자 지정 게스트 구성 구성

게스트 구성 에이전트에 Azure Policy 게스트 구성에 대 한 DSC 구성만 사용 되며 Windows PowerShell 필요한 상태 구성과 충돌 하지 않습니다.

다음 예제에서는 **Auditbitlocker**라는 구성을 만들고, **GuestConfiguration** 리소스 모듈을 가져오고, `Service` 리소스를 사용 하 여 실행 중인 서비스를 감사 합니다.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

자세한 내용은 [구성 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)을 참조 하세요.

## <a name="create-guest-configuration-custom-policy-package"></a>게스트 구성 사용자 지정 정책 패키지 만들기

MOF가 컴파일되면 지원 파일을 함께 패키지 해야 합니다. 완료 된 패키지는 게스트 구성에서 Azure Policy 정의를 만드는 데 사용 됩니다. 패키지는 다음으로 구성 됩니다.

- MOF로 컴파일된 DSC 구성
- 모듈 폴더
  - GuestConfiguration 모듈
  - DscNativeResources 모듈
  - 용 Chef InSpec 정의 및 추가 콘텐츠가 있는 폴더
  - Windows 기본 제공 되지 않는 DSC 리소스 모듈

`New-GuestConfigurationPackage` cmdlet은 패키지를 만듭니다. 사용자 지정 패키지를 만드는 데 사용 되는 형식은 다음과 같습니다.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` cmdlet의 매개 변수:

- **이름**: 게스트 구성 패키지 이름입니다.
- **구성**: 컴파일된 DSC 구성 문서 전체 경로입니다.
- **경로**: 출력 폴더 경로입니다. 이 매개 변수는 선택 사항입니다. 지정 하지 않으면 패키지가 현재 디렉터리에 만들어집니다.
- **ChefProfilePath**: InSpec profile의 전체 경로입니다. 이 매개 변수는 Linux를 감사 하는 콘텐츠를 만드는 경우에만 지원 됩니다.

완료 된 패키지는 관리 되는 가상 컴퓨터에서 액세스할 수 있는 위치에 저장 해야 합니다. 이러한 예로는 GitHub 리포지토리, Azure 리포지토리 또는 Azure storage가 있습니다. 패키지를 공용으로 설정 하지 않으려는 경우 [SAS 토큰](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 을 URL에 포함할 수 있습니다.
이 구성은 패키지에 액세스 하 고 서비스와 통신 하지 않는 경우에만 적용 되지만 개인 네트워크의 컴퓨터에 대 한 [서비스 엔드포인트](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) 를 구현할 수도 있습니다.

## <a name="test-a-guest-configuration-package"></a>게스트 구성 패키지 테스트

구성 패키지를 만든 후 Azure에 게시 하기 전에 워크스테이션 또는 CI/CD 환경에서 패키지의 기능을 테스트할 수 있습니다. GuestConfiguration 모듈에는 Azure 컴퓨터 내에서 사용 되는 것과 동일한 에이전트를 개발 환경에서 로드 하는 cmdlet `Test-GuestConfigurationPackage` 포함 되어 있습니다. 이 솔루션을 사용 하 여 청구 되는 테스트/QA/프로덕션 환경에 릴리스하기 전에 통합 테스트를 로컬로 수행할 수 있습니다.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` cmdlet의 매개 변수:

- **이름**: 게스트 구성 정책 이름입니다.
- **매개 변수**: 해시 테이블 형식으로 제공 되는 정책 매개 변수입니다.
- **경로**: 게스트 구성 패키지의 전체 경로입니다.

Cmdlet은 PowerShell 파이프라인의 입력도 지원 합니다. `New-GuestConfigurationPackage` cmdlet의 출력을 `Test-GuestConfigurationPackage` cmdlet으로 파이프 합니다.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

매개 변수를 사용 하 여 테스트 하는 방법에 대 한 자세한 내용은 [사용자 지정 게스트 구성 정책에서 매개 변수 사용](#using-parameters-in-custom-guest-configuration-policies)섹션을 참조 하세요.

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Azure Policy 정의 및 이니셔티브 배포 파일 만들기

게스트 구성 사용자 지정 정책 패키지를 만들고 컴퓨터에서 액세스할 수 있는 위치에 업로드 한 후에는 Azure Policy에 대 한 게스트 구성 정책 정의를 만듭니다. `New-GuestConfigurationPolicy` cmdlet은 공개적으로 액세스할 수 있는 게스트 구성 사용자 지정 정책 패키지를 사용 하 고 **auditIfNotExists** 및 **Deployifnotexists** 정책 정의를 만듭니다. 두 정책 정의를 모두 포함 하는 정책 이니셔티브 정의도 만들어집니다.

다음 예에서는 Windows 용 게스트 구성 사용자 지정 정책 패키지에서 지정 된 경로에 정책 및 이니셔티브 정의를 만들고 이름, 설명 및 버전을 제공 합니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

`New-GuestConfigurationPolicy` cmdlet의 매개 변수:

- **Contenturi**: 게스트 구성 콘텐츠 패키지의 공용 http (s) uri입니다.
- **DisplayName**: Policy 표시 이름입니다.
- **설명**: 정책 설명입니다.
- **매개 변수**: 해시 테이블 형식으로 제공 되는 정책 매개 변수입니다.
- **버전**: 정책 버전
- **경로**: 정책 정의가 생성 되는 대상 경로입니다.
- **Platform**: 게스트 구성 정책 및 콘텐츠 패키지를 위한 대상 플랫폼 (Windows/Linux)입니다.

`New-GuestConfigurationPolicy`에서 생성 되는 파일은 다음과 같습니다.

- **auditIfNotExists**
- **deployIfNotExists. json**
- **이니셔티브. json**

Cmdlet 출력은 정책 파일의 이니셔티브 표시 이름 및 경로를 포함 하는 개체를 반환 합니다.

이 명령을 사용 하 여 사용자 지정 정책 프로젝트를 스 캐 폴드 하는 경우 이러한 파일을 변경할 수 있습니다. 예를 들어 컴퓨터에 특정 태그가 있는지 여부를 평가 하는 ' If ' 섹션을 수정 합니다. 정책을 만드는 방법에 대 한 자세한 내용은 [프로그래밍 방식으로 정책 만들기](./programmatically-create.md)를 참조 하세요.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>사용자 지정 게스트 구성 정책에서 매개 변수 사용

게스트 구성에서는 런타임에 구성의 속성을 재정의할 수 있습니다. 이 기능은 패키지의 MOF 파일에 있는 값을 정적으로 간주할 필요가 없음을 의미 합니다. 재정의 값은 Azure Policy를 통해 제공 되며 구성을 작성 하거나 컴파일하는 방법에 영향을 주지 않습니다.

Cmdlet `New-GuestConfigurationPolicy` 및 `Test-GuestConfigurationPolicyPackage`에 **는 매개 변수 라는 매개**변수가 포함 됩니다. 이 매개 변수는 각 매개 변수에 대 한 모든 세부 정보를 포함 하는 해시 테이블 정의를 사용 하 고 Azure Policy 정의를 만드는 데 사용 되는 파일의 모든 필수 섹션을 자동으로 만듭니다.

다음 예에서는 사용자가 정책 할당 시 서비스 목록에서 선택 하는 서비스를 감사 하는 Azure Policy을 만듭니다.

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
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Linux 정책의 경우 구성에 **AttributesYmlContent** 속성을 포함 하 고 필요에 따라 값을 덮어씁니다. 게스트 구성 에이전트는 InSpec에서 특성을 저장 하는 데 사용 하는 YAML 파일을 자동으로 만듭니다. 아래 예제를 참조하세요.

```powershell
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

각 추가 매개 변수에 대해 해시 테이블을 배열에 추가 합니다. 정책 파일에는 리소스 종류, 이름, 속성 및 값을 식별 하는 게스트 구성 configurationName에 추가 된 속성이 표시 됩니다.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Azure Policy에 게시

**GuestConfiguration** 리소스 모듈은 Azure에서 `Publish-GuestConfigurationPolicy` cmdlet을 통해 한 단계로 정책 정의와 이니셔티브 정의를 만드는 방법을 제공 합니다.
Cmdlet에는 `New-GuestConfigurationPolicy`에서 만든 세 가지 JSON 파일의 위치를 가리키는 **Path** 매개 변수만 있습니다.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` cmdlet은 PowerShell 파이프라인의 경로를 허용 합니다. 이 기능은 정책 파일을 만들고 단일 파이프 된 명령 집합에 게시할 수 있음을 의미 합니다.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Azure에서 만든 정책 및 이니셔티브 정의를 사용 하 여 마지막 단계는 이니셔티브를 할당 하는 것입니다. [포털](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)및 [Azure PowerShell](../assign-policy-powershell.md)를 사용 하 여 이니셔티브를 할당 하는 방법을 참조 하세요.

> [!IMPORTANT]
> 게스트 구성 정책은 **항상** _AuditIfNotExists_ 및 _deployifnotexists_ 정책을 결합 하는 이니셔티브를 사용 하 여 할당 되어야 합니다. _AuditIfNotExists_ 정책만 할당 된 경우 필수 구성 요소가 배포 되지 않으며 정책에 항상 ' 0 ' 서버가 규정을 준수 하는 것으로 표시 됩니다.

## <a name="policy-lifecycle"></a>정책 수명 주기

사용자 지정 콘텐츠 패키지를 사용 하 여 사용자 지정 Azure Policy를 게시 한 후 새 릴리스를 게시 하려는 경우 두 개의 필드를 업데이트 해야 합니다.

- **버전**: `New-GuestConfigurationPolicy` cmdlet을 실행할 때 현재 게시 된 것 보다 큰 버전 번호를 지정 해야 합니다. 속성은 새 정책 파일에 있는 게스트 구성 할당의 버전을 업데이트 하 여 패키지가 업데이트 되었다는 것을 인식 합니다.
- **contentHash**:이 속성은 `New-GuestConfigurationPolicy` cmdlet에 의해 자동으로 업데이트 됩니다. `New-GuestConfigurationPackage`에서 만든 패키지의 해시 값입니다. 속성은 게시 하는 `.zip` 파일에 대해 정확 해야 합니다. 다른 사람이 포털에서 정책 정의를 수동으로 변경할 수 있는 경우와 같이 **Contenturi** 속성만 업데이트 된 경우에는 확장에서 콘텐츠 패키지를 수락 하지 않습니다.

업데이트 된 패키지를 해제 하는 가장 쉬운 방법은이 문서에 설명 된 프로세스를 반복 하 고 업데이트 된 버전 번호를 제공 하는 것입니다. 이 프로세스는 모든 속성이 올바르게 업데이트 되었는지 보장 합니다.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows 그룹 정책 콘텐츠를 Azure Policy 게스트 구성으로 변환

Windows 컴퓨터를 감사할 때 게스트 구성은 PowerShell 필요한 상태 구성 구문의 구현입니다. DSC 커뮤니티에서는 내보낸 그룹 정책 템플릿을 DSC 형식으로 변환 하는 도구를 게시 했습니다. 위에서 설명한 게스트 구성 cmdlet과 함께이 도구를 사용 하 여 Windows 그룹 정책 콘텐츠를 변환 하 고 Azure Policy 감사를 위해 패키지/게시할 수 있습니다. 도구를 사용 하는 방법에 대 한 자세한 내용은 [빠른 시작: DSC로 그룹 정책 변환](/powershell/scripting/dsc/quickstarts/gpo-quickstart)문서를 참조 하세요.
콘텐츠를 변환한 후에는 패키지를 만들고 Azure Policy로 게시 하는 단계는 모든 DSC 콘텐츠와 동일 합니다.

## <a name="optional-signing-guest-configuration-packages"></a>선택 사항: 게스트 구성 패키지 서명

게스트 구성 사용자 지정 정책은 기본적으로 SHA256 해시를 사용 하 여 감사 중인 서버에서 읽을 때 정책 패키지가 게시 된 시기에서 변경 되지 않았는지 확인 합니다.
필요에 따라 고객은 인증서를 사용 하 여 패키지에 서명 하 고 게스트 구성 확장에서 서명 된 콘텐츠를 허용 하도록 강제할 수도 있습니다.

이 시나리오를 사용 하려면 두 단계를 완료 해야 합니다. Cmdlet을 실행 하 여 콘텐츠 패키지에 서명 하 고 코드에 서명 해야 하는 컴퓨터에 태그를 추가 합니다.

서명 유효성 검사 기능을 사용 하려면 `Protect-GuestConfigurationPackage` cmdlet을 실행 하 여 패키지를 게시 하기 전에 서명 합니다. 이 cmdlet에는 ' 코드 서명 ' 인증서가 필요 합니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` cmdlet의 매개 변수:

- **경로**: 게스트 구성 패키지의 전체 경로입니다.
- **Certificate**: 패키지에 서명할 코드 서명 인증서입니다. 이 매개 변수는 Windows 콘텐츠에 서명 하는 경우에만 지원 됩니다.
- **PrivateGpgKeyPath**: 개인 GPG 키 경로입니다. 이 매개 변수는 Linux 용 콘텐츠에 서명 하는 경우에만 지원 됩니다.
- **PublicGpgKeyPath**: Public GPG key 경로입니다. 이 매개 변수는 Linux 용 콘텐츠에 서명 하는 경우에만 지원 됩니다.

GuestConfiguration 에이전트는 인증서 공개 키가 Windows 컴퓨터의 "신뢰할 수 있는 루트 인증 기관" 및 Linux 컴퓨터의 `/usr/local/share/ca-certificates/extra` 경로에 있어야 합니다. 노드가 서명 된 콘텐츠를 확인 하려면 사용자 지정 정책을 적용 하기 전에 컴퓨터에 인증서 공개 키를 설치 합니다. 이 프로세스는 VM 내에서 또는 Azure Policy를 사용 하 여 수행할 수 있습니다. 예제 템플릿이 여기에 [제공](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)됩니다.
Key Vault 액세스 정책은 배포 하는 동안 계산 리소스 공급자가 인증서에 액세스할 수 있도록 허용 해야 합니다. 자세한 단계는 [Azure Resource Manager에서 가상 컴퓨터에 대 한 Key Vault 설정](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)을 참조 하세요.

다음은 서명 인증서에서 공개 키를 내보내 컴퓨터로 가져오는 예제입니다.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Linux 컴퓨터에서 사용할 GPG 키를 만드는 방법에 대 한 좋은 참조는 GitHub의 문서에서 제공 하 고 [새 GPG 키를 생성](https://help.github.com/en/articles/generating-a-new-gpg-key)하는 것입니다.

콘텐츠를 게시 한 후에는 이름 `GuestConfigPolicyCertificateValidation` 및 값이 `enabled` 인 태그를 코드 서명이 필요한 모든 가상 컴퓨터에 추가 합니다. Azure Policy를 사용 하 여 태그를 대규모로 배달할 수 있는 방법에 대 한 [태그 샘플](../samples/built-in-policies.md#tags) 을 참조 하세요. 이 태그가 준비 되 면 `New-GuestConfigurationPolicy` cmdlet을 사용 하 여 생성 된 정책 정의를 통해 게스트 구성 확장을 통해 요구 사항을 설정할 수 있습니다.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>게스트 구성 정책 할당 문제 해결 (미리 보기)

도구는 게스트 구성 할당 Azure Policy 문제 해결을 지원 하기 위해 미리 보기에서 사용할 수 있습니다. 이 도구는 미리 보기 상태 이며 PowerShell 갤러리 모듈 이름 [게스트 구성 문제 해결사로](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)게시 되었습니다.

이 도구의 cmdlet에 대 한 자세한 내용은 PowerShell에서 Get-help 명령을 사용 하 여 기본 제공 지침을 표시 합니다. 이 도구를 자주 업데이트 하는 경우 가장 최근의 정보를 얻는 가장 좋은 방법입니다.

## <a name="next-steps"></a>다음 단계

- [게스트 구성을](../concepts/guest-configuration.md)사용 하 여 vm을 감사 하는 방법을 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](get-compliance-data.md)방법에 대해 알아봅니다.
