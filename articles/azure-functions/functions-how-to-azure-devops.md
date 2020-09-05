---
title: Azure DevOps를 사용 하 여 지속적으로 함수 앱 코드 업데이트
description: Azure Functions를 대상으로 하는 Azure DevOps 파이프라인을 설정 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: d503d71cf44446f93fab3d292d4c26d9b7b0941d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210225"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Azure DevOps를 사용한 지속적인 업데이트

[Azure Pipelines](/azure/devops/pipelines/)를 사용 하 여 Azure Functions 앱에 함수를 자동으로 배포할 수 있습니다.

파이프라인을 정의 하는 두 가지 옵션이 있습니다.

- **Yaml 파일**: yaml 파일은 파이프라인을 설명 합니다. 이 파일에는 빌드 단계 섹션과 릴리스 섹션이 있을 수 있습니다. YAML 파일은 앱과 동일한 리포지토리에 있어야 합니다.
- **템플릿**: 템플릿은 앱을 빌드하거나 배포 하는 미리 만들어진 작업입니다.

## <a name="yaml-based-pipeline"></a>YAML 기반 파이프라인

YAML 기반 파이프라인을 만들려면 먼저 앱을 빌드한 다음 앱을 배포 합니다.

### <a name="build-your-app"></a>앱 빌드

Azure Pipelines에서 앱을 빌드하는 방법은 앱의 프로그래밍 언어에 따라 달라 집니다. 각 언어에는 배포 아티팩트를 만드는 특정 빌드 단계가 있습니다. 배포 아티팩트는 Azure에서 함수 앱을 배포 하는 데 사용 됩니다.

# <a name="c"></a>[C\#](#tab/csharp)

다음 샘플을 사용 하 여 .NET 앱을 빌드하는 YAML 파일을 만들 수 있습니다.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 샘플을 사용 하 여 JavaScript 앱을 빌드하는 YAML 파일을 만들 수 있습니다.

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

다음 샘플 중 하나를 사용 하 여 특정 Python 버전용 앱을 빌드하는 YAML 파일을 만들 수 있습니다. Python은 Linux에서 실행 되는 함수 앱에 대해서만 지원 됩니다.

**버전 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**버전 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 샘플을 사용 하 여 PowerShell 앱을 패키징하는 YAML 파일을 만들 수 있습니다. PowerShell은 Windows Azure Functions 에서만 지원 됩니다.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>앱 배포

호스팅 OS에 따라 YAML 파일에 다음 YAML 샘플 중 하나를 포함 해야 합니다.

#### <a name="windows-function-app"></a>Windows 함수 앱

다음 코드 조각을 사용 하 여 Windows 함수 앱을 배포할 수 있습니다.

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux 함수 앱

다음 코드 조각을 사용 하 여 Linux 함수 앱을 배포할 수 있습니다.

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>템플릿 기반 파이프라인

Azure DevOps의 템플릿은 앱을 빌드하거나 배포 하는 미리 정의 된 작업 그룹입니다.

### <a name="build-your-app"></a>앱 빌드

Azure Pipelines에서 앱을 빌드하는 방법은 앱의 프로그래밍 언어에 따라 달라 집니다. 각 언어에는 배포 아티팩트를 만드는 특정 빌드 단계가 있습니다. 배포 아티팩트는 Azure에서 함수 앱을 업데이트 하는 데 사용 됩니다.

기본 제공 빌드 템플릿을 사용 하려면 새 빌드 파이프라인을 만들 때 **클래식 편집기를 사용** 하 여 디자이너 템플릿을 사용 하 여 파이프라인 만들기를 선택 합니다.

![Azure Pipelines 클래식 편집기를 선택 합니다.](media/functions-how-to-azure-devops/classic-editor.png)

코드 소스를 구성한 후 Azure Functions 빌드 템플릿을 검색 합니다. 앱 언어와 일치 하는 템플릿을 선택 합니다.

![Azure Functions 빌드 템플릿 선택](media/functions-how-to-azure-devops/build-templates.png)

경우에 따라 빌드 아티팩트에는 특정 폴더 구조가 있습니다. **보관 경로에 루트 폴더 이름 앞** 에 추가 확인란을 선택 해야 할 수도 있습니다.

![루트 폴더 이름 앞에 추가 하는 옵션](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 앱

JavaScript 앱이 Windows 네이티브 모듈에 의존 하는 경우 에이전트 풀 버전을 **호스트 된 VS2017**로 업데이트 해야 합니다.

![에이전트 풀 버전 업데이트](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>앱 배포

새 릴리스 파이프라인을 만들 때 Azure Functions 릴리스 템플릿을 검색 합니다.

![Azure Functions 릴리스 템플릿 검색](media/functions-how-to-azure-devops/release-template.png)

배포 슬롯에 배포 하는 것은 릴리스 템플릿에서 지원 되지 않습니다.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 빌드 파이프라인 만들기

Azure에서 빌드 파이프라인을 만들려면 `az functionapp devops-pipeline create` [명령을](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)사용 합니다. 빌드 파이프라인이 만들어져 리포지토리에 적용 되는 모든 코드 변경 내용을 빌드하고 릴리스 합니다. 이 명령은 빌드 및 릴리스 파이프라인을 정의 하는 새 YAML 파일을 생성 한 다음 리포지토리에 커밋합니다. 이 명령에 대 한 필수 구성 요소는 코드의 위치에 따라 달라 집니다.

- 코드가 GitHub에 있는 경우:

    - 구독에 대 한 **쓰기** 권한이 있어야 합니다.

    - Azure DevOps의 프로젝트 관리자 여야 합니다.

    - 충분 한 권한이 있는 GitHub PAT (개인용 액세스 토큰)를 만들 수 있는 권한이 있어야 합니다. 자세한 내용은 [GITHUB PAT 권한 요구 사항](https://aka.ms/azure-devops-source-repos) 을 참조 하세요.

    - 자동 생성 된 YAML 파일을 커밋할 수 있도록 GitHub 리포지토리에서 마스터 분기에 커밋할 수 있는 권한이 있어야 합니다.

- 코드가 Azure Repos에 있는 경우:

    - 구독에 대 한 **쓰기** 권한이 있어야 합니다.

    - Azure DevOps의 프로젝트 관리자 여야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Functions 개요](functions-overview.md)를 검토 합니다.
- [Azure DevOps 개요](/azure/devops/pipelines/)를 검토 합니다.
