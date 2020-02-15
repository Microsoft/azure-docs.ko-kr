---
title: 템플릿에서 배포 스크립트 사용 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 배포 스크립트를 사용 합니다.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: jgao
ms.openlocfilehash: a67f360aa08f306d6462342d96f59e06a4d3b501
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251858"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>템플릿에서 배포 스크립트 사용 (미리 보기)

Azure 리소스 템플릿에서 배포 스크립트를 사용 하는 방법에 대해 알아봅니다. `Microsoft.Resources/deploymentScripts`이라는 새 리소스 유형을 사용 하 여 사용자는 템플릿 배포에서 배포 스크립트를 실행 하 고 실행 결과를 검토할 수 있습니다. 이러한 스크립트는 다음과 같은 사용자 지정 단계를 수행 하는 데 사용할 수 있습니다.

- 디렉터리에 사용자 추가
- 앱 등록 만들기
- 데이터 평면 작업 (예: blob 복사 또는 시드 데이터베이스)을 수행 합니다.
- 라이선스 키 조회 및 유효성 검사
- 자체 서명된 인증서 만들기
- Azure AD에서 개체 만들기
- 사용자 지정 시스템에서 IP 주소 블록 조회

배포 스크립트의 이점은 다음과 같습니다.

- 쉽게 코딩 하 고, 사용 하 고, 디버그할 수 있습니다. 즐겨 사용 하는 개발 환경에서 배포 스크립트를 개발할 수 있습니다. 스크립트는 템플릿 또는 외부 스크립트 파일에 포함 될 수 있습니다.
- 스크립트 언어 및 플랫폼을 지정할 수 있습니다. 현재는 Linux 환경에서 Azure PowerShell 배포 스크립트만 지원 됩니다.
- 스크립트를 실행 하는 데 사용 되는 id를 지정할 수 있습니다. 현재 [Azure 사용자 할당 관리 id](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 만 지원 됩니다.
- 스크립트에 명령줄 인수를 전달 하도록 허용 합니다.
- 는 스크립트 출력을 지정 하 고 배포에 다시 전달할 수 있습니다.

> [!NOTE]
> 배포 스크립트는 현재 미리 보기로 있습니다. 이를 사용하려면 [미리 보기에 가입](https://aka.ms/armtemplatepreviews)해야 합니다.

> [!IMPORTANT]
> 스크립트를 실행하고 문제를 해결하기 위해 두 개의 배포 스크립트 리소스, 즉 스토리지 계정과 컨테이너 인스턴스가 동일한 리소스 그룹에 만들어집니다. 이러한 리소스는 일반적으로 배포 스크립트 실행이 터미널 상태가 될 때 스크립트 서비스에 의해 삭제 됩니다. 리소스가 삭제될 때까지 해당 리소스에 대한 요금이 청구됩니다. 자세히 알아보려면 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **구독 수준에서 기여자 역할이 있는 사용자가 할당한 관리 ID**. 이 ID는 배포 스크립트를 실행하는 데 사용됩니다. 하나를 만들려면 Azure Portal를 사용 하거나 [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 사용 하거나 [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)를 사용 하 여 [사용자 할당 관리 id 만들기](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)를 참조 하세요. 이 ID는 템플릿을 배포할 때 필요합니다. ID의 형식은 다음과 같습니다.

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  다음 PowerShell 스크립트를 통해 리소스 그룹 이름과 ID 이름을 제공하여 ID를 가져옵니다.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **합니다, 2.8.0 또는 3.0.0 버전을 Azure PowerShell**합니다. 템플릿을 배포 하는 데 이러한 버전이 필요 하지 않습니다. 그러나 이러한 버전은 배포 스크립트를 로컬로 테스트 하는 데 필요 합니다. [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 미리 구성 된 Docker 이미지를 사용할 수 있습니다.  [개발 환경 구성](#configure-development-environment)을 참조 하세요.

## <a name="sample-template"></a>샘플 템플릿

다음 json은 예입니다.  최신 템플릿 스키마는 [여기](/azure/templates/microsoft.resources/deploymentscripts)에서 찾을 수 있습니다.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> 예제는 데모용입니다.  **Scriptcontent** 및 **primary를** 템플릿에서 공존할 수 없습니다.

속성 값 정보:

- **Id**: 배포 스크립트 서비스는 사용자 할당 관리 id를 사용 하 여 스크립트를 실행 합니다. 현재 사용자 할당 관리 id만 지원 됩니다.
- **kind**: 스크립트의 유형을 지정 합니다. 현재 Azure PowerShell 스크립트만 지원 됩니다. 값은 **Azurepowershell**입니다.
- **Forceupdatetag**: 템플릿 배포 간에이 값을 변경 하면 배포 스크립트가 강제로 다시 실행 됩니다. 매개 변수의 defaultValue로 설정 해야 하는 newGuid () 또는 utcNow () 함수를 사용 합니다. 자세한 내용은 [스크립트를 두 번 이상 실행](#run-script-more-than-once)을 참조하세요.
- **azPowerShellVersion**: 사용할 Azure PowerShell 모듈 버전을 지정 합니다. 배포 스크립트는 현재 합니다, 2.8.0 및 3.0.0 버전을 지원 합니다.
- **인수**: 매개 변수 값을 지정 합니다. 값은 공백으로 구분됩니다.
- **scriptcontent**: 스크립트 콘텐츠를 지정 합니다. 외부 스크립트를 실행 하려면 대신 `primaryScriptUri`를 사용 합니다. 예제는 [인라인 스크립트 사용](#use-inline-scripts) 및 [외부 스크립트 사용](#use-external-scripts)을 참조 하세요.
- **primaryScriptUri**: 지원 되는 powershell 파일 확장명을 사용 하 여 기본 powershell 스크립트에 공개적으로 액세스할 수 있는 Url을 지정 합니다.
- **Supportingscripturis**: `ScriptContent` 또는 `PrimaryScriptUri`에서 호출 되는 powershell 파일을 지원 하기 위해 공개적으로 액세스할 수 있는 url의 배열을 지정 합니다.
- **timeout**: [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)으로 지정 된 최대 허용 스크립트 실행 시간을 지정 합니다. 기본값은 **P1D**입니다.
- **Cleanuppreference 설정**입니다. 스크립트 실행이 터미널 상태가 될 때 배포 리소스 정리의 기본 설정을 지정 합니다. 기본 설정은 **항상**입니다. 즉, 터미널 상태 (성공, 실패, 취소 됨)에도 불구 하 고 리소스를 삭제 합니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.
- **보존 기간**: 배포 스크립트 실행이 터미널 상태에 도달한 후 서비스에서 배포 스크립트 리소스를 유지 하는 간격을 지정 합니다. 이 기간이 만료 되 면 배포 스크립트 리소스가 삭제 됩니다. 기간은 [ISO 8601 패턴](https://en.wikipedia.org/wiki/ISO_8601)을 기반으로 합니다. 기본값은 7 일을 의미 하는 **P1D**입니다. 이 속성은 cleanupPreference 설정이 *Onexpiration*로 설정 된 경우에 사용 됩니다. *Onexpiration* 속성이 현재 활성화 되어 있지 않습니다. 자세한 내용은 [배포 스크립트 리소스 정리](#clean-up-deployment-script-resources)를 참조하세요.

## <a name="use-inline-scripts"></a>인라인 스크립트 사용

다음 템플릿에는 `Microsoft.Resources/deploymentScripts` 유형을 사용 하 여 정의 된 리소스가 하나 있습니다.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> 인라인 배포 스크립트는 큰따옴표로 묶여 있으므로 배포 스크립트 내의 문자열을 작은따옴표로 묶어야 합니다. PowerShell의 이스케이프 문자는 **&#92;** 입니다. 이전 JSON 샘플에 표시 된 대로 문자열 대체를 사용 하는 것을 고려할 수도 있습니다. Name 매개 변수의 기본값을 참조 하세요.

스크립트는 하나의 매개 변수를 사용 하 고 매개 변수 값을 출력 합니다. **Deploymentscriptoutputs** 는 출력을 저장 하는 데 사용 됩니다.  출력 섹션에서 **값** 줄은 저장 된 값에 액세스 하는 방법을 보여 줍니다. `Write-Output`은 디버깅 목적으로 사용 됩니다. 출력 파일에 액세스 하는 방법에 대 한 자세한 내용은 [배포 스크립트 디버그](#debug-deployment-scripts)를 참조 하세요.  속성 설명은 [샘플 템플릿](#sample-template)을 참조 하세요.

스크립트를 실행 하려면 **시도** 를 선택 하 여 Cloud shell을 열고 다음 코드를 셸 창에 붙여넣습니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

출력은 다음과 같이 표시됩니다.

![리소스 관리자 템플릿 배포 스크립트 hello 세계 출력](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>외부 스크립트 사용

인라인 스크립트 외에도 외부 스크립트 파일을 사용할 수 있습니다. 현재는 **ps1** 파일 확장명이 있는 PowerShell 스크립트만 지원 됩니다. 외부 스크립트 파일을 사용 하려면 `scriptContent`를 `primaryScriptUri`으로 바꿉니다. 다음은 그 예입니다.

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

예제를 보려면 [여기](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)를 선택 합니다.

외부 스크립트 파일에 액세스할 수 있어야 합니다.  Azure storage 계정에 저장 된 스크립트 파일의 보안을 유지 하려면 [자습서: 보안 아티팩트 Azure Resource Manager 템플릿 배포](./template-tutorial-secure-artifacts.md)를 참조 하세요.

## <a name="use-supporting-scripts"></a>지원 스크립트 사용

복잡 한 논리를 하나 이상의 지원 스크립트 파일로 분리할 수 있습니다. `supportingScriptURI` 속성을 사용 하면 필요한 경우 지원 스크립트 파일에 Uri 배열을 제공할 수 있습니다.

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

지원 스크립트 파일은 인라인 스크립트와 기본 스크립트 파일 모두에서 호출 될 수 있습니다.

지원 파일은 런타임에 azscripts/azscriptinput에 복사 됩니다. 인라인 스크립트 및 기본 스크립트 파일에서 지원 파일을 참조 하려면 상대 경로를 사용 합니다.

## <a name="work-with-outputs-from-deployment-scripts"></a>배포 스크립트에서 출력 작업

다음 템플릿에서는 두 deploymentScripts 리소스 간에 값을 전달 하는 방법을 보여 줍니다.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

첫 번째 리소스에서는 **$DeploymentScriptOutputs**라는 변수를 정의 하 고이 변수를 사용 하 여 출력 값을 저장 합니다. 템플릿 내의 다른 리소스에서 출력 값에 액세스 하려면 다음을 사용 합니다.

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>배포 스크립트 디버그

스크립트 서비스는 스크립트 실행을 위한 [저장소 계정](../../storage/common/storage-account-overview.md) 및 [컨테이너 인스턴스](../../container-instances/container-instances-overview.md) 를 만듭니다. 두 리소스 모두 리소스 이름에 **azscripts** 접미사가 있습니다.

![리소스 관리자 템플릿 배포 스크립트 리소스 이름](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

사용자 스크립트, 실행 결과 및 stdout 파일은 저장소 계정의 파일 공유에 저장 됩니다. **Azscripts**라는 폴더가 있습니다. 폴더에는 입력 및 출력 파일에 대 한 두 개의 폴더 ( **azscriptinput** 및 **azscriptoutput**)가 있습니다.

출력 폴더에는 **executionresult.json** 및 스크립트 출력 파일이 포함되어 있습니다. **Executionresult. json**에서 스크립트 실행 오류 메시지를 볼 수 있습니다. 출력 파일은 스크립트가 성공적으로 실행 되는 경우에만 생성 됩니다. 입력 폴더에는 시스템 PowerShell 스크립트 파일과 사용자 배포 스크립트 파일이 포함되어 있습니다. 사용자 배포 스크립트 파일을 수정 된 것으로 바꾸고 Azure container instance에서 배포 스크립트를 다시 실행할 수 있습니다.

REST API를 사용 하 여 리소스 그룹 수준 및 구독 수준에서 배포 스크립트 리소스 배포 정보를 가져올 수 있습니다.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

다음 예제에서는 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용 합니다.

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

다음과 유사하게 출력됩니다.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

출력은 배포 상태와 배포 스크립트 리소스 Id를 표시 합니다.

다음 REST API은 로그를 반환 합니다.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

배포 스크립트 리소스를 삭제 하기 전에만 작동 합니다.

포털에서 deploymentScripts 리소스를 보려면 **숨겨진 유형 표시**를 선택 합니다.

![리소스 관리자 템플릿 배포 스크립트, 숨겨진 유형 표시, 포털](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>배포 스크립트 리소스 정리

배포 스크립트는 배포 스크립트를 실행 하 고 디버그 정보를 저장 하는 데 사용 되는 저장소 계정 및 컨테이너 인스턴스를 만듭니다. 이러한 두 리소스는 프로 비전 된 리소스와 동일한 리소스 그룹에 만들어지고 스크립트가 만료 될 때 스크립트 서비스에 의해 삭제 됩니다. 이러한 리소스의 수명 주기를 제어할 수 있습니다.  삭제 될 때까지 두 리소스에 대해 요금이 청구 됩니다. 가격 정보는 [Container Instances 가격 책정](https://azure.microsoft.com/pricing/details/container-instances/) 및 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

이러한 리소스의 수명 주기는 템플릿의 다음 속성에 의해 제어 됩니다.

- **Cleanuppreference**설정: 스크립트 실행이 터미널 상태가 될 때의 우선 순위를 정리 합니다.  지원되는 값은

  - **항상**: 스크립트 실행이 터미널 상태에서 가져온 후 리소스를 삭제 합니다. 리소스를 정리 하 고 나면 deploymentScripts 리소스가 계속 존재할 수 있으므로 시스템 스크립트는 리소스를 삭제 하기 전에 stdout, output, return value 등의 스크립트 실행 결과를 DB에 복사 합니다.
  - **Onsuccess**: 스크립트 실행에 성공한 경우에만 리소스를 삭제 합니다. 리소스에 계속 액세스 하 여 디버그 정보를 찾을 수 있습니다.
  - **Onexpiration**: **보존 기간** 설정이 만료 된 경우에만 리소스를 삭제 합니다. 이 속성은 현재 사용할 수 없습니다.

- 보존 **기간**: 스크립트 리소스가 유지 되는 시간 간격을 지정 합니다 .이 시간 후에 만료 되 고 삭제 됩니다.

> [!NOTE]
> 다른 용도로는 배포 스크립트 리소스를 사용 하지 않는 것이 좋습니다.

## <a name="run-script-more-than-once"></a>스크립트를 두 번 이상 실행

배포 스크립트 실행은 idempotent 작업입니다. 모든 deploymentScripts 리소스 속성 (인라인 스크립트 포함)이 변경 되지 않은 경우 템플릿을 다시 배포할 때 스크립트가 실행 되지 않습니다. 배포 스크립트 서비스는 템플릿의 리소스 이름을 같은 리소스 그룹에 있는 기존 리소스와 비교 합니다. 동일한 배포 스크립트를 여러 번 실행 하려는 경우 두 가지 옵션이 있습니다.

- DeploymentScripts 리소스의 이름을 변경 합니다. 예를 들어 리소스 이름 또는 리소스 이름의 일부로 [utcNow](./template-functions-string.md#utcnow) 템플릿 함수를 사용 합니다. 리소스 이름을 변경 하면 새 deploymentScripts 리소스가 생성 됩니다. 스크립트 실행 기록을 유지 하는 것이 좋습니다.

    > [!NOTE]
    > UtcNow 함수는 매개 변수의 기본값에만 사용할 수 있습니다.

- `forceUpdateTag` template 속성에 다른 값을 지정 하십시오.  예를 들어 utcNow를 값으로 사용 합니다.

> [!NOTE]
> Idempotent 배포 스크립트를 작성 합니다. 이렇게 하면 실수로 다시 실행 되는 경우 시스템 변경이 발생 하지 않습니다. 예를 들어 배포 스크립트를 사용 하 여 Azure 리소스를 만드는 경우 리소스를 만들기 전에 존재 하지 않는지 확인 합니다. 그러면 스크립트가 성공 하거나 리소스를 다시 만들지 않습니다.

## <a name="configure-development-environment"></a>개발 환경 구성

현재 배포 스크립트는 Azure PowerShell 버전 합니다, 2.8.0 및 3.0.0를 지원 합니다.  Windows 컴퓨터가 있는 경우 지원 되는 Azure PowerShell 버전 중 하나를 설치 하 고 배포 스크립트 개발 및 테스트를 시작할 수 있습니다.  Windows 컴퓨터가 없거나 이러한 Azure PowerShell 버전 중 하나가 설치 되어 있지 않으면 미리 구성 된 docker 컨테이너 이미지를 사용할 수 있습니다. 다음 절차에서는 Windows에서 docker 이미지를 구성 하는 방법을 보여 줍니다. Linux 및 Mac의 경우 인터넷에서 정보를 찾을 수 있습니다.

1. 개발 컴퓨터에 [Docker Desktop](https://www.docker.com/products/docker-desktop) 을 설치 합니다.
1. Docker Desktop을 엽니다.
1. 표시줄에서 Docker 바탕 화면 아이콘을 선택 하 고 **설정**을 선택 합니다.
1. **공유 드라이브**를 선택 하 고 컨테이너에 사용할 수 있는 로컬 드라이브를 선택한 다음 **적용** 을 선택 합니다.

    ![리소스 관리자 템플릿 배포 스크립트 docker 드라이브](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. 프롬프트에 windows 자격 증명을 입력 합니다.
1. 명령 프롬프트 또는 Windows PowerShell (PowerShell ISE 사용 안 함) 중 하나를 사용 하 여 터미널 창을 엽니다.
1. 배포 스크립트 컨테이너 이미지를 로컬 컴퓨터로 끌어옵니다.

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    이 예에서는 버전 합니다를 사용 합니다.

1. Docker 이미지를 로컬로 실행 합니다.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **&lt;호스트 드라이버 문자 >** 및 **&lt;호스트 디렉터리 이름 >** 를 공유 드라이브의 기존 폴더로 바꿉니다.  컨테이너의 **/data** 폴더에 폴더를 매핑합니다. 예를 들어 D:\docker를 매핑하려면 다음을 수행 합니다.

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-** 컨테이너 이미지를 활성 상태로 유지 하는 것을 의미 합니다.

1. 메시지가 표시 되 면 **공유** 를 선택 합니다.
1. 다음 스크린샷에 표시 된 것 처럼 PowerShell 스크립트를 실행 합니다 (d:\docker 폴더에 helloworld. ps1 파일이 있는 경우).

    ![리소스 관리자 템플릿 배포 스크립트 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

PowerShell 스크립트를 성공적으로 테스트 한 후에는 배포 스크립트로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 배포 스크립트를 사용 하는 방법을 알아보았습니다. 배포 스크립트 자습서를 진행 하려면 다음을 수행 합니다.

> [!div class="nextstepaction"]
> [자습서: Azure Resource Manager 템플릿에서 배포 스크립트 사용](./template-tutorial-deployment-script.md)