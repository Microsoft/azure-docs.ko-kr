---
title: 템플릿 배포 스크립트 사용 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 배포 스크립트를 사용하는 방법을 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5454d2f80d1febccb0c57ecf2e80d930bb5cb761
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988807"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>자습서: 배포 스크립트를 사용하여 자체 서명된 인증서 만들기(미리 보기)

Azure Resource Manager 템플릿에서 배포 스크립트를 사용하는 방법에 대해 알아봅니다. 배포 스크립트를 사용하여 Resource Manager 템플릿으로 수행할 수 없는 사용자 지정 단계를 수행할 수 있습니다. 예를 들어 자체 서명된 인증서를 만듭니다.  이 자습서에서는 Azure 키 자격 증명 모음을 배포하는 템플릿을 만들고, 동일한 템플릿의 `Microsoft.Resources/deploymentScripts` 리소스를 사용하여 인증서를 만든 다음, 이 인증서를 키 자격 증명 모음에 추가합니다. 배포 스크립트에 대한 자세한 내용은 [Azure Resource Manager 템플릿에서 배포 스크립트 사용](./deployment-script-template.md)을 참조하세요.

> [!NOTE]
> 배포 스크립트는 현재 미리 보기로 있습니다. 이를 사용하려면 [미리 보기에 가입](https://aka.ms/armtemplatepreviews)해야 합니다.

> [!IMPORTANT]
> 스크립트를 실행하고 문제를 해결하기 위해 두 개의 배포 스크립트 리소스, 즉 스토리지 계정과 컨테이너 인스턴스가 동일한 리소스 그룹에 만들어집니다. 일반적으로 스크립트 실행이 터미널 상태가 되면 스크립트 서비스에서 이러한 리소스를 삭제합니다. 리소스가 삭제될 때까지 해당 리소스에 대한 요금이 청구됩니다. 자세한 내용은 [배포 스크립트 리소스 정리](./deployment-script-template.md#clean-up-deployment-script-resources)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 편집
> * 템플릿 배포
> * 실패한 스크립트 디버그
> * 리소스 정리

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* **Resource Manager Tools 확장이 있는 [Visual Studio Code](https://code.visualstudio.com/)** . [Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./use-vs-code-to-create-template.md)를 참조하세요.

* **구독 수준에서 기여자 역할이 있는 사용자가 할당한 관리 ID**. 이 ID는 배포 스크립트를 실행하는 데 사용됩니다. ID를 만들려면 [사용자가 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)를 참조하세요. 이 ID는 템플릿을 배포할 때 필요합니다. ID의 형식은 다음과 같습니다.

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  다음 PowerShell 스크립트를 통해 리소스 그룹 이름과 ID 이름을 제공하여 ID를 가져옵니다.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

템플릿을 처음부터 만드는 대신 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에서 템플릿을 엽니다. Azure 빠른 시작 템플릿은 Resource Manager 템플릿용 리포지토리입니다.

이 빠른 시작에서 사용되는 템플릿을 [Create an Azure Key Vault and a secret(Azure Key Vault 및 비밀 만들기)](https://azure.microsoft.com/resources/templates/101-key-vault-create/)이라고 합니다. 템플릿은 키 자격 증명 모음을 만든 다음, 비밀을 이 키 자격 증명 모음에 추가합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. **열기**를 선택하여 파일을 엽니다.
4. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

템플릿을 다음과 같이 변경합니다.

### <a name="clean-up-the-template-optional"></a>템플릿 정리(선택 사항)

원본 템플릿은 비밀을 키 자격 증명 모음에 추가합니다.  자습서를 간소화하기 위해 다음 리소스를 제거합니다.

* **Microsoft.KeyVault/vaults/secrets**

다음 두 매개 변수 정의를 제거합니다.

* **secretName**
* **secretValue**

이러한 정의를 제거하지 않도록 선택하는 경우 배포 중에 매개 변수 값을 지정해야 합니다.

### <a name="configure-the-key-vault-access-policies"></a>키 자격 증명 모음 액세스 정책 구성

배포 스크립트는 인증서를 키 자격 증명 모음에 추가합니다. 권한을 관리 ID에 부여하도록 키 자격 증명 모음 액세스 정책을 구성합니다.

1. 관리 ID를 가져오는 매개 변수를 추가합니다.

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Visual Studio Code의 Resource Manager 템플릿 확장은 아직 배포 스크립트의 형식을 지정할 수 없습니다. 다음과 같이 [SHIFT]+[ALT]+F를 사용하여 deploymentScripts 리소스의 형식을 지정하지 마세요.

1. 관리 ID에서 인증서를 키 자격 증명 모음에 추가할 수 있도록 키 자격 증명 모음 액세스 정책을 구성하는 매개 변수를 추가합니다.

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. 기존 키 자격 증명 모음 액세스 정책을 다음으로 업데이트합니다.

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    두 가지 정책, 즉 로그인한 사용자에 대한 정책과 관리 ID에 대한 정책이 정의되어 있습니다.  로그인한 사용자에게는 배포를 확인할 수 있는 *list*(목록) 권한만 있으면 됩니다.  자습서를 간소화하기 위해 관리 ID와 로그인한 사용자 둘 모두에 동일한 인증서가 할당됩니다.

### <a name="add-the-deployment-script"></a>배포 스크립트 추가

1. 배포 스크립트에서 사용하는 세 개의 매개 변수를 추가합니다.

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. deploymentScripts 리소스를 추가합니다.

    > [!NOTE]
    > 인라인 배포 스크립트는 큰따옴표로 묶여 있으므로 배포 스크립트 내의 문자열을 작은따옴표로 묶어야 합니다. PowerShell의 이스케이프 문자는 **&#92;** 입니다.

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts` 리소스는 키 자격 증명 모음 리소스 및 역할 할당 리소스에 따라 달라집니다.  다음과 같은 속성이 있습니다.

    * **identity**: 배포 스크립트는 사용자가 할당한 관리 ID를 사용하여 스크립트를 실행합니다.
    * **kind**: 스크립트 유형을 지정합니다. 현재 PowerShell 스크립트만 지원됩니다.
    * **forceUpdateTag**: 스크립트 원본이 변경되지 않은 경우에도 배포 스크립트를 실행할지 여부를 결정합니다. 현재 타임스탬프 또는 GUID일 수 있습니다. 자세한 내용은 [스크립트를 두 번 이상 실행](./deployment-script-template.md#run-script-more-than-once)을 참조하세요.
    * **azPowerShellVersion**: 사용할 Azure PowerShell 모듈 버전을 지정합니다. 배포 스크립트는 현재 2.7.0, 2.8.0 및 3.0.0 버전을 지원합니다.
    * **timeout**: [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)에 지정된 최대 허용 스크립트 실행 시간을 지정합니다. 기본값은 **P1D**입니다.
    * **arguments**: 매개 변수의 값을 지정합니다. 값은 공백으로 구분됩니다.
    * **scriptContent**: 스크립트 콘텐츠를 지정합니다. 외부 스크립트를 실행하려면 **primaryScriptURI**를 대신 사용합니다. 자세한 내용은 [외부 스크립트 사용](./deployment-script-template.md#use-external-scripts)을 참조하세요.
        로컬 머신에서 스크립트를 테스트하는 경우에만 **$DeploymentScriptOutputs**를 선언해야 합니다. 이 변수를 선언하면 스크립트를 변경하지 않고도 로컬 머신과 deploymentScript 리소스에서 실행할 수 있습니다. $DeploymentScriptOutputs에 할당된 값은 배포에서 출력으로 사용할 수 있습니다. 자세한 내용은 [배포 스크립트에서 출력 작업](./deployment-script-template.md#work-with-outputs-from-deployment-scripts)을 참조하세요.
    * **cleanupPreference**: 배포 스크립트 리소스를 삭제할 시간에 대한 기본 설정을 지정합니다.  기본값은 **Always**(항상)이며, 이는 터미널 상태(성공, 실패, 취소됨)에도 불구하고 배포 스크립트 리소스가 삭제됨을 의미합니다. 이 자습서에서는 **OnSuccess**가 사용되므로 스크립트 실행 결과를 볼 수 있습니다.
    * **retentionInterval**: 서비스가 터미널 상태에 도달되면 스크립트 리소스를 유지하는 간격을 지정합니다. 이 기간이 만료되면 리소스가 삭제됩니다. 기간은 ISO 8601 패턴을 기반으로 합니다. 이 자습서에서는 하루를 의미하는 P1D를 사용합니다.  이 속성은 **cleanupPreference**가 **OnExpiration**으로 설정된 경우에 사용됩니다. 이 속성은 현재 사용하도록 설정되어 있지 않습니다.

    배포 스크립트에는 키 자격 증명 모음 이름, 인증서 이름 및 주체 이름의 세 가지 매개 변수가 사용됩니다.  인증서를 만든 다음, 인증서를 키 자격 증명 모음에 추가합니다.

    **$DeploymentScriptOutputs**는 출력 값을 저장하는 데 사용됩니다.  자세한 내용은 [배포 스크립트에서 출력 작업](./deployment-script-template.md#work-with-outputs-from-deployment-scripts)을 참조하세요.

    완성된 템플릿은 [여기](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)서 찾을 수 있습니다.

1. 디버깅 프로세스를 확인하려면 다음 줄을 배포 스크립트에 추가하여 오류를 코드에 배치합니다.

    ```powershell
    Write-Output1 $keyVaultName
    ```

    올바른 명령은 **Write-Output1** 대신 **Write-Output**입니다.

1. **파일**>**저장**을 차례로 선택하여 파일을 저장합니다.

## <a name="deploy-the-template"></a>템플릿 배포

Visual Studio Code 빠른 시작의 [템플릿 배포](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template) 섹션을 참조하여 Cloud Shell을 열고 템플릿 파일을 셸에 업로드합니다. 그런 다음, 다음 PowerShell 스크립트를 실행합니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

배포 스크립트 서비스에서 스크립트 실행을 위한 추가 배포 스크립트 리소스를 만들어야 합니다. 준비 및 정리 프로세스는 실제 스크립트 실행 시간 외에도 완료하는 데 최대 1분 정도 걸릴 수 있습니다.

스크립트에 **Write-Output1**이 사용되는 잘못된 명령으로 인해 배포에 실패했습니다. 다음과 같은 오류 메시지가 표시됩니다.

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

배포 스크립트 실행 결과는 문제 해결을 위해 배포 스크립트 리소스에 저장됩니다.

## <a name="debug-the-failed-script"></a>실패한 스크립트 디버그

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 리소스 그룹을 엽니다. **rg**가 추가된 프로젝트 이름입니다. 리소스 그룹에 두 개의 추가 리소스가 표시됩니다. 이러한 리소스는 *배포 스크립트 리소스*라고 합니다.

    ![Resource Manager 템플릿 배포 스크립트 리소스](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    두 파일에는 모두 **azscripts** 접미사가 있습니다. 하나는 스토리지 계정이고, 다른 하나는 컨테이너 인스턴스입니다.

    **숨겨진 형식 표시**를 선택하여 deploymentScripts 리소스를 나열합니다.

1. **azscripts** 접미사가 있는 스토리지 계정을 선택합니다.
1. **파일 공유** 타일을 선택합니다. **azscripts** 폴더가 표시됩니다.  이 폴더에는 배포 스크립트 실행 파일이 포함되어 있습니다.
1. **azscripts**를 선택합니다. **azscriptinput** 및 **azscriptoutput**의 두 폴더가 표시됩니다.  입력 폴더에는 시스템 PowerShell 스크립트 파일과 사용자 배포 스크립트 파일이 포함되어 있습니다. 출력 폴더에는 **executionresult.json** 및 스크립트 출력 파일이 포함되어 있습니다. **executionresult.json**에서 오류 메시지를 볼 수 있습니다. 실행이 실패하여 출력 파일이 없습니다.

**Write-Output1** 줄을 제거하고, 해당 템플릿을 다시 배포합니다.

두 번째 배포가 성공적으로 실행되면 **cleanupPreference** 속성이 **OnSuccess**로 설정되어 있으므로 스크립트 서비스에서 배포 스크립트 리소스를 제거합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Resource Manager 템플릿에서 배포 스크립트를 사용하는 방법을 알아보았습니다. 조건에 따라 Azure 리소스를 배포하는 방법을 배우려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [조건 사용](./template-tutorial-use-conditions.md)
