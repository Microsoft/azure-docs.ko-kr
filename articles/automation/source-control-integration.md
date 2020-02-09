---
title: Azure Automation에서 원본 제어 통합
description: 이 문서에서는 Azure Automation에서 GitHub를 사용하는 원본 제어 통합을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 837ebd71886e9435a44080b06c079623c3936c69
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417063"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation에서 원본 제어 통합

원본 제어를 사용 하면 GitHub 또는 Azure Repos 소스 제어 리포지토리에서 스크립트를 사용 하 여 Automation 계정의 runbook을 최신 상태로 유지할 수 있습니다. 원본 제어를 사용하면 팀과 쉽게 공동 작업하고 변경 내용을 추적하며 이전 버전의 Runbook으로 롤백할 수 있습니다. 예를 들어, 소스 제어를 사용하면 소스 제어의 여러 다른 분기를 개발, 테스트 또는 프로덕션 Automation 계정과 동기화할 수 있습니다. 이렇게 하면 개발 환경에서 테스트한 코드를 프로덕션 Automation 계정으로 쉽게 승격할 수 있습니다. 소스 제어와 자동화의 통합은 소스 제어 리포지토리에서 단일 방향 동기화를 지원 합니다.

Azure Automation는 다음과 같은 세 가지 형식의 원본 제어를 지원 합니다.

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>필수 조건

* 소스 제어 리포지토리 (GitHub 또는 Azure Repos)
* [실행 계정](manage-runas-account.md)
* **AzureRM** 모듈을 포함 하 여 Automation 계정에 [최신 Azure 모듈이](automation-update-azure-modules.md) 있는지 확인 합니다. 

> [!NOTE]
> 소스 제어 동기화 작업은 사용자 Automation 계정에서 실행되며 다른 Automation 작업과 동일한 요금으로 청구됩니다.

## <a name="configure-source-control---azure-portal"></a>소스 제어 구성-Azure Portal

Automation 계정 내에서 **원본 제어** 를 선택 하 고 **+ 추가** 를 클릭 합니다.

![소스 제어 선택](./media/source-control-integration/select-source-control.png)

**소스 제어 형식**을 선택하고 **인증**을 클릭합니다. 브라우저 창이 열리고 로그인 하 라는 메시지가 표시 되 면 프롬프트에 따라 인증을 완료 합니다.

**소스 제어 요약** 페이지에서 정보를 입력하고 **저장**을 클릭합니다. 다음 표는 사용 가능한 필드에 대한 설명을 보여 줍니다.

|속성  |Description  |
|---------|---------|
|소스 제어 이름     | 소스 컨트롤에 대한 친숙 한 이름입니다. *이 이름에는 문자와 숫자만 포함 되어야 합니다.*        |
|소스 제어 형식     | 소스 제어 소스의 형식입니다. 사용 가능한 옵션은 다음과 같습니다.</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|리포지토리     | 리포지토리 또는 프로젝트의 이름입니다. 처음 200 리포지토리가 반환 됩니다. 리포지토리를 검색 하려면 필드에 이름을 입력 하 고 **GitHub에서 검색**을 클릭 합니다.|
|Branch     | 소스 파일을 끌어올 분기입니다. TFVC 소스 제어 형식에 대해 분기 대상 지정을 사용할 수 없습니다.          |
|폴더 경로     | 동기화 할 runbook을 포함 하는 폴더입니다. 예:/Runbooks </br>*지정 된 폴더의 runbook만 동기화 됩니다. 재귀는 지원 되지 않습니다.*        |
|Auto Sync<sup>1</sup>     | 소스 제어 리포지토리에서 커밋이 이루어질 때 자동 동기화를 설정 또는 해제합니다.         |
|Runbook 게시     | **On**으로 설정 된 경우 runbook이 원본 제어에서 동기화 된 후 자동으로 게시 됩니다.         |
|Description     | 추가 정보를 제공하는 텍스트 필드        |

<sup>1</sup> Azure Repos와 원본 제어 통합을 구성할 때 자동 동기화를 사용 하도록 설정 하려면 프로젝트 관리자 여야 합니다.

![소스 제어 요약](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 원본 제어 리포지토리의 로그인이 Azure Portal 로그인과 다를 수 있습니다. 원본 제어를 구성할 때 소스 제어 리포지토리의 올바른 계정으로 로그인 했는지 확인 합니다. 계정이 올바른지 확실치 않으면 브라우저에서 새 탭을 열고 visualstudio.com 또는 github.com에서 로그아웃한 다음 소스 제어를 다시 연결해 봅니다.

## <a name="configure-source-control---powershell"></a>소스 제어 구성-PowerShell

PowerShell을 사용 하 여 Azure Automation에서 소스 제어를 구성할 수도 있습니다. PowerShell cmdlet을 사용 하 여 원본 제어를 구성 하려면 PAT (개인용 액세스 토큰)가 필요 합니다. [AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) 를 사용 하 여 소스 제어 연결을 만듭니다. 보안 문자열을 만드는 방법을 알아보려면 cmdlet에 개인 액세스 토큰의 보안 문자열이 필요 합니다. [convertto-html](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)를 참조 하세요.

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>개인용 액세스 토큰 사용 권한

소스 제어에는 개인용 액세스 토큰에 대한 최소 사용 권한이 필요합니다. 다음 표에는 GitHub 및 Azure Repos에 필요한 최소 사용 권한이 포함 되어 있습니다.

#### <a name="github"></a>GitHub

GitHub에서 개인용 액세스 토큰을 만드는 방법에 대한 자세한 내용은 [명령줄에 대한 개인용 액세스 토큰 만들기](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)를 참조 하세요.

|범위  |Description  |
|---------|---------|
|**리포지토리**     |         |
|repo:status     | 커밋 상태 액세스         |
|repo_deployment      | 배포 상태 액세스         |
|public_repo     | 공용 리포지토리 액세스         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 리포지토리 후크 쓰기         |
|read:repo_hook|리포지토리 후크 읽기|

#### <a name="azure-repos"></a>Azure Repos

Azure Repos에서 개인용 액세스 토큰을 만드는 방법에 대한 자세한 내용은 [개인 액세스 토큰을 사용 하 여 액세스 인증](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)을 참조 하세요.

|범위  |
|---------|
|코드(읽기)     |
|프로젝트 및 팀(읽기)|
|ID(읽기)      |
|사용자 프로필(읽기)     |
|작업 항목(읽기)    |
|서비스 연결(읽기, 쿼리 및 관리)<sup>1</sup>    |

<sup>1</sup> 서비스 연결 권한은 autosync를 사용 하도록 설정한 경우에만 필요 합니다.

## <a name="syncing"></a>동기화 중

**원본 제어** 페이지의 테이블에서 원본을 선택 합니다. **동기화 시작**을 클릭하여 동기화 프로세스를 시작합니다.

**동기화 작업** 탭을 클릭 하 여 현재 동기화 작업 또는 이전 작업의 상태를 볼 수 있습니다. **소스 제어** 드롭다운에서 원본 제어를 선택 합니다.

![동기화 상태](./media/source-control-integration/sync-status.png)

작업을 클릭하면 작업 출력을 볼 수 있습니다. 다음은 소스 제어 동기화 작업의 출력 예입니다.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

**소스 제어 동기화 작업 요약** 페이지에서 **모든 로그** 를 선택 하 여 추가 로깅을 사용할 수 있습니다. 이러한 추가 로그 항목은 원본 제어를 사용할 때 발생할 수 있는 문제를 해결 하는 데 도움이 될 수 있습니다.

## <a name="disconnecting-source-control"></a>원본 제어 연결 끊기

소스 제어 리포지토리에서 연결을 끊으려면 Automation 계정의 **계정 설정** 에서 **소스 제어** 를 엽니다.

제거하려는 소스 제어를 선택합니다. **소스 제어 요약** 페이지에서 **삭제**를 클릭합니다.

## <a name="encoding"></a>Encoding

여러 사용자가 서로 다른 편집기를 사용 하 여 소스 제어 리포지토리에서 runbook을 편집 하는 경우에는 인코딩 문제가 발생할 수 있습니다. 이 경우 runbook에서 잘못 된 문자가 발생할 수 있습니다. 이에 대해 자세히 알아보려면 [인코딩 문제의 일반적인 원인](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues) 을 참조 하세요.

## <a name="updating-the-access-token"></a>액세스 토큰 업데이트

현재는 포털에서 원본 제어의 액세스 토큰을 업데이트할 방법이 없습니다. 개인용 액세스 토큰이 만료 되거나 해지 된 후 다음과 같은 방법으로 새 액세스 토큰을 사용 하 여 소스 제어를 업데이트할 수 있습니다.

* [REST Api](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)를 통해.
* [AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet 사용

## <a name="next-steps"></a>다음 단계

Runbook 형식, 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
