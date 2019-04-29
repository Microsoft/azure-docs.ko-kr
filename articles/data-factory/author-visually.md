---
title: Azure Data Factory의 시각적 작성 | Microsoft Docs
description: Azure Data Factory에서 시각적 작성을 사용하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: d2e51551643e154714b9d2368e63d7af9ebfa204
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593097"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성
Azure Data Factory UX(사용자 인터페이스 환경)을 사용하면 코드를 작성할 필요 없이 데이터 팩터리에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다. 시각적 작성을 수행하기 위해 UX를 사용하는 두 가지 방법이 있습니다.

- Data Factory 서비스로 직접 작성합니다.
- 공동 작업, 원본 제어 또는 버전 관리용으로 Azure Repos Git 통합 기능을 사용하여 작성합니다.

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory 서비스로 직접 작성
Data Factory 서비스를 통한 시각적 작성은 Git 통합 기능을 사용한 시각적 작성과는 두 가지 면에서 다릅니다.

- Data Factory 서비스는 사용자의 변경 사항을 위한 JSON 엔터티를 저장하는 리포지토리를 포함하지 않습니다.
- Data Factory 서비스는 공동 작업 또는 버전 제어에 최적화되지 않습니다.

![Data Factory 서비스 구성](media/author-visually/configure-data-factory.png)

UX **제작 캔버스**를 사용하여 Data Factory 서비스를 통해 직접 작성하는 경우 **모두 게시** 모드만 사용할 수 있습니다. 변경한 내용은 Data Factory 서비스에 직접 게시됩니다.

![게시 모드](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 통합을 통한 작성
Azure Repos Git 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 공동 작업을 지원합니다. 사용자는 원본 제어, 공동 작업, 버전 관리 등을 위해 Azure Repos Git 조직 리포지토리와 데이터 팩터리를 연결할 수 있습니다. 단일 Azure Repos Git 계정이 여러 리포지토리를 포함할 수는 있지만 각 Azure Repos Git 리포지토리는 데이터 팩터리 하나에만 연결할 수 있습니다. Azure Repos 조직 또는 리포지토리가 없는 경우 [이러한 지침](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)에 따라 리소스를 만듭니다.

> [!NOTE]
> Azure Repos Git 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 Azure Repos Git 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 Azure Repos Git 리포지토리 구성
데이터 팩터리를 사용해서 두 가지 방법으로 Azure Repos Git 리포지토리를 구성할 수 있습니다.

#### <a name="method1"></a> 구성 방법 1(Azure Repos Git 리포지토리): 시작 페이지

Azure Data Factory에서 **시작하기** 페이지로 이동합니다. **코드 리포지토리 구성**을 선택합니다.

![Azure Repos 코드 리포지토리 구성](media/author-visually/configure-repo.png)

**리포지토리 설정** 구성 창이 나타납니다.

![코드 리포지토리 설정 구성](media/author-visually/repo-settings.png)

창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| 설정 | 설명 | 값 |
|:--- |:--- |:--- |
| **리포지토리 유형** | Azure Repos 코드 리포지토리의 유형입니다.<br/>**참고**: GitHub는 현재 지원되지 않습니다. | Azure Repos Git |
| **Azure Active Directory** | Azure AD 테넌트 이름입니다. | `<your tenant name>` |
| **Azure Repos 조직** | Azure Repos 조직 이름입니다. Azure Repos 조직 이름은 `https://{organization name}.visualstudio.com`에서 확인할 수 있습니다. [Azure Repos 조직에 로그인](https://www.visualstudio.com/team-services/git/)하여 Visual Studio 프로필에 액세스하고 리포지토리 및 프로젝트를 확인할 수 있습니다. | `<your organization name>` |
| **ProjectName** | Azure Repos 프로젝트 이름입니다. Azure Repos 프로젝트 이름은 `https://{organization name}.visualstudio.com/{project name}`에서 확인할 수 있습니다. | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos 코드 리포지토리 이름입니다. Azure Repos 프로젝트는 프로젝트가 확장됨에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용할 수 있습니다. | `<your Azure Repos code repository name>` |
| **공동 작업 분기** | 게시에 사용되는 Azure Repos 공동 작업 분기입니다. 기본값은 `master`입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다. | `<your collaboration branch name>` |
| **루트 폴더** | Azure Repos 공동 작업 분기의 루트 폴더입니다. | `<your root folder name>` |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | UX **제작 캔버스**에서 기존 데이터 팩터리 리소스를 Azure Repos Git 리포지토리로 가져올 것인지를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>구성 방법 2(Azure Repos Git 리포지토리): UX 제작 캔버스
Azure Data Factory UX **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다. **Data Factory** 드롭다운 메뉴를 선택한 다음, **코드 리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 <a href="#method1">구성 방법 1</a>의 설명을 참조하세요.

![UX 제작을 위한 코드 리포지토리 설정 구성](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>다른 Azure Active Directory 테넌트 사용

다른 Azure Active Directory 테넌트에서 Azure Repos Git 리포지토리를 만들 수 있습니다. 다른 Azure AD 테넌트를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다.

### <a name="use-your-personal-microsoft-account"></a>개인 Microsoft 계정 사용

Git 통합에 개인 Microsoft 계정을 사용하려면 개인 Azure 리포지토리를 조직의 Active Directory에 연결하면 됩니다.

1. 개인 Microsoft 계정을 조직의 Active Directory에 게스트로 추가합니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 공동 작업 사용자 추가](../active-directory/b2b/add-users-administrator.md)를 참조하세요.

2. 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다. 그런 다음, 조직의 Active Directory로 전환합니다.

3. Azure DevOps 섹션으로 이동하면 이제 개인 리포지토리가 보입니다. 리포지토리를 선택하고 Active Directory와 연결합니다.

이러한 구성 단계 후, Data Factory UI에서 Git 통합을 설정할 때 개인 리포지토리를 사용할 수 있습니다.

Azure Repos를 조직의 Active Directory에 연결하는 방법에 대한 자세한 내용은 [Azure DevOps 조직을 Azure Active Directory에 연결](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)을 참조하세요.

### <a name="switch-to-a-different-git-repo"></a>다른 Git 리포지토리로 전환

다른 Git 리포지토리로 전환하려면 다음 스크린샷에 표시된 대로 Data Factory 개요 페이지의 위쪽 오른쪽 모서리에서 아이콘을 찾습니다. 아이콘이 표시되지 않으면 로컬 브라우저 캐시의 선택을 취소합니다. 아이콘을 선택하여 현재 리포지토리와 연결을 제거합니다.

현재 리포지토리와 연결을 제거한 후에 다른 리포지토리를 사용하도록 Git 설정을 구성할 수 있습니다. 그러면 새로운 리포지토리로 기존 Data Factory 리소스를 가져올 수 있습니다.

![현재 Git 리포지토리와 연결 제거](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>사용자 버전 제어
_원본 제어_라고도 하는 버전 제어 시스템을 통해 개발자는 코드에 대해 공동 작업을 수행하고, 코드 베이스의 변경 내용을 추적할 수 있습니다. 소스 제어는 개발자가 여러 명인 프로젝트에 반드시 필요한 도구입니다.

데이터 팩터리와 연결된 각 Azure Repos Git 리포지토리에는 공동 작업 분기가 생성됩니다. (`master`는 기본 공동 작업 분기입니다.) 사용자는 **+ 새 분기**를 클릭하여 기능 분기를 만들고 기능 분기에서 개발을 수행할 수도 있습니다.

![동기화 또는 게시로 코드 변경](media/author-visually/sync-publish.png)

기능 분기에서 기능을 개발할 준비가 되면 **끌어오기 요청 만들기**를 클릭할 수 있습니다. 이 작업을 통해 끌어오기 요청을 수행하고, 코드 검토를 수행하고, 공동 작업 분기에 변경 내용을 병합할 수 있는 Azure Repos Git로 이동합니다. (기본값은 `master`입니다.) 공동 작업 분기에서 Data Factory 서비스에 게시할 수만 있습니다. 

![새 끌어오기 요청 만들기](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>게시 설정 구성

게시 분기(Resource Manager 템플릿이 저장되는 분기)를 구성하려면 공동 작업 분기의 루트 폴더에 `publish_config.json` 파일을 추가합니다. Data Factory는 이 파일을 읽고 `publishBranch` 필드를 찾은 다음 제공된 값을 사용하여 새 분기(없는 경우)를 만듭니다. 그런 다음 지정된 위치에 모든 Resource Manager 템플릿을 저장합니다. 예를 들면 다음과 같습니다.

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Git 모드에서 게시하는 경우 아래 스크린샷에 나와 있는 것처럼 Data Factory가 필요한 게시 분기를 사용 중임을 확인할 수 있습니다.

![올바른 게시 분기 확인](media/author-visually/configure-publish-branch.png)

새 게시 분기를 지정할 때 Data Factory는 이전 게시 분기를 삭제하지 않습니다. 이전 게시 분기를 제거하려는 경우에는 수동으로 해당 분기를 삭제합니다.

Data Factory는 팩터리를 로드할 때만 `publish_config.json` 파일을 읽습니다. 포털에 팩터리가 이미 로드되어 있으면 브라우저를 새로 고쳐서 변경 내용을 적용합니다.

### <a name="publish-code-changes"></a>코드 변경 내용 게시
공동 작업 분기에 변경 내용을 병합한 후에(기본값은 `master`) **게시**를 선택하여 마스터 분기의 코드 변경 내용을 Data Factory 서비스에 수동으로 게시합니다.

![Data Factory 서비스에 변경 내용 게시](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> 마스터 분기는 Data Factory 서비스에 배포된 내용을 반영하지 않습니다. 따라서 마스터 분기를 *반드시* Data Factory 서비스에 수동으로 게시해야 합니다.

### <a name="advantages-of-git-integration"></a>Git 통합의 장점

-   **소스 제어**. 데이터 팩터리 워크로드가 중요해지면서 팩터리를 Git에 통합하여 다음과 같은 여러 소스 제어 이점을 활용할 수 있습니다.
    -   변경 내용을 추적/감사할 수 있습니다.
    -   버그를 발생한 변경 내용을 되돌릴 수 있습니다.
-   **부분 저장**. 팩터리의 많은 부분이 변경되면 일반 라이브 모드에서 알 수 있게 되며, 저장할 준비가 되지 않았거나 컴퓨터 작동 중단이 발생할 경우 변경 내용을 손실할 수 있으므로, 변경 내용을 초안으로 저장할 수 없습니다. Git 통합을 사용하면 증분 변경 내용을 계속 저장하고, 준비가 되었을 때만 팩터리에 게시할 수 있습니다. Git은 사용자가 변경 내용이 만족스러운지 테스트할 때까지 작업의 준비 위치로 사용됩니다.
-   **공동 작업 및 제어**. 같은 팩터리에 참여하는 여러 팀 멤버에 있는 경우 팀원들이 코드 검토 프로세스를 통해 서로 공동으로 작업하도록 할 수 있습니다. 팩터리에 일부 참가자만 팩터리에 배포할 권한을 가지도록 팩터리를 설정할 수도 있습니다. 팀 멤버는 Git을 통해서만 변경할 수 있고, 팀의 특정 사람만 팩터리에 변경 내용을 “게시”하도록 허용됩니다.
-   **차이 표시**. Git 모드에서는 팩터리에 게시될 페이로드의 차이를 제대로 확인할 수 있습니다. 이러한 차이는 팩터리에 마지막으로 게시된 이후 수정/추가/삭제된 모든 리소스/엔터티를 보여 줍니다. 이 차이에 따라 향후 게시를 계속하거나 변경 내용을 확인한 후 다시 돌아올 수 있습니다.
-   **더 나아진 CI/CD**. Git 모드를 사용하는 경우 개발 팩터리가 변경되는 즉시, 자동으로 트리거하도록 릴리스 파이프라인을 구성할 수 있습니다. 또한 Resource Manager 템플릿에서 매개 변수로 사용할 수 있는 속성을 팩터리에서 사용자 지정해야 합니다. 필수 속성 세트만 매개 변수로 유지하고 다른 모든 항목은 하드 코딩하면 유용할 수 있습니다.
-   **향상된 성능**. 평균 팩터리는 일반 라이브 모드의 경우보다 Git에서 10배 더 빠르게 로드합니다. Git을 통해 리소스가 다운로드되기 때문입니다.

### <a name="best-practices-for-git-integration"></a>Git 통합에 대한 모범 사례

-   **사용 권한**. 일반적으로 모든 팀 멤버에게 팩터리를 업데이트하기 위한 권한을 부여하고 싶지 않을 것입니다.
    -   모든 팀 멤버는 데이터 팩터리에 대한 읽기 권한이 있어야 합니다.
    -   선택한 사용자들만 팩터리에 게시하도록 허용되며, 이를 위해 해당 사용자는 팩터리에서 "Data Factory 참가자" 역할에 속해야 합니다.
    -   소스 제어의 모범 사례 중 하나는 공동 작업 분기에 대해 직접 체크 인을 허용하지 않는 것입니다. 이 요구를 충족하면 체크 인할 때마다 끌어오기 요청 프로세스가 진행될 때 버그 발생이 방지됩니다.
-   **모드 전환**.
    -    라이브 모드에서 변경한 내용을 Git으로 전환하면 볼 수 없으므로 Git 모드에서는 라이브 모드와 Git 모드 간을 전환하는 것은 바람직하지 않습니다. Git 모드 자체에서 변경한 후 UI를 통해 게시해보세요.
    -   마찬가지로 Data Factory PowerShell cmdlet은 제공된 변경을 라이브 팩터리에 직접 적용하여 동일한 효과를 얻으므로 사용하지 않도록 합니다.
-   **Azure Key Vault의 암호 사용**입니다.
    -   AzureKeyVault를 사용하여 모든 연결 문자열 또는 암호를 DataFactory 연결된 서비스에 저장하는 것이 좋습니다.
    -   이러한 비밀 정보는 Git에 저장하지 않으므로(보안상의 이유로) 연결된 서비스의 모든 변경 내용은 라이브 팩터리에 즉시 게시됩니다. 변경 내용을 테스트하지 않았을 수 있으며, 이러한 상황은 Git의 목적에 맞지 않으므로 이러한 즉각적인 게시를 원치 않을 수 있습니다.
    -   결과적으로, 이러한 모든 비밀은 Azure Key Vault 기반을 사용하는 연결된 서비스에서 가져와야 합니다.
    -   Key Vault를 사용하는 다른 이점은 Resource Manager 템플릿 배포 동안 이러한 비밀을 제공하지 않아도 되므로 CICD가 더 간편하다는 것입니다.

## <a name="author-with-github-integration"></a>GitHub 통합을 통한 작성

GitHub 통합을 통한 시각적 작성은 데이터 팩터리 파이프라인에서 작업하기 위한 원본 제어 및 공동 작업을 지원합니다. 사용자는 원본 제어, 공동 작업 및 버전 관리를 위해 GitHub 계정 리포지토리에 데이터 팩터리를 연결할 수 있습니다. 단일 GitHub 계정은 여러 리포지토리를 가질 수 있지만 GitHub 리포지토리는 단 하나의 데이터 팩터리와 연결할 수 있습니다. GitHub 계정 또는 리포지토리가 없는 경우  [이러한 지침](https://github.com/join) 에 따라 리소스를 만듭니다.

Data Factory를 사용한 GitHub 통합은 공용 GitHub(즉, [https://github.com](https://github.com)) 및 GitHub Enterprise 모두를 지원합니다. GitHub의 리포지토리에 대한 읽기 및 쓰기 권한이 있다면 Data Factory를 사용하여 공용 및 개인 GitHub 리포지토리를 모두 사용할 수 있습니다.

GitHub 리포지토리를 지정하려면 사용하고 있는 Azure 구독에 대한 관리자 권한이 있어야 합니다.

9분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>제한 사항

- GitHub 리포지토리에 스크립트 및 데이터 파일을 저장할 수 있습니다. 그러나 Azure Storage에 파일을 수동으로 업로드해야 합니다. Data Factory 파이프라인은 GitHub 리포지토리에 저장된 스크립트 또는 데이터 파일을 자동으로 Azure Storage에 업로드하지 않습니다.

- 버전 2.14.0 이상의 GitHub Enterprise는 Microsoft Edge 브라우저에서 작동하지 않습니다.

- Data Factory 시각적 저작 도구를 사용한 GitHub 통합은 Data Factory의 일반적으로 사용 가능한 버전에서만 작동합니다.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 공용 GitHub 리포지토리 구성

데이터 팩터리를 사용해서 두 가지 방법으로 GitHub 리포지토리를 구성할 수 있습니다.

**구성 방법 1(공용 리포지토리): 시작 페이지**

Azure Data Factory에서  **시작하기**  페이지로 이동합니다. 코드  **리포지토리 구성**을 선택합니다.

![Data Factory 시작 페이지](media/author-visually/github-integration-image1.png)

리 **포지토리 설정**  구성 창이 나타납니다.

![GitHub 리포지토리 설정](media/author-visually/github-integration-image2.png)

창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| **설정**                                              | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                   | **값**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **리포지토리 유형**                                      | Azure Repos 코드 리포지토리의 유형입니다.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub 계정**                                       | 사용자 GitHub 계정 이름. Https에서이 이름을 찾을 수 있습니다:\//github.com/{account 이름} / {리포지토리 이름}. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다.                                                                                                                                                                                                                              |                    |
| **공동 작업 분기**                                 | 게시에 사용되는 GitHub 공동 작업 분기입니다. 기본값은 master입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다.                                                                                                                                                                                                                                                               |                    |
| **루트 폴더**                                          | GitHub 공동 작업 분기의 루트 폴더입니다.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | 기존 데이터 팩터리 리소스를 UX  **제작 캔버스** 에서 GitHub 리포지토리로 가져올지 여부를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기**                       | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 공동 작업 b. 새로 만들기 c. 기존 리소스 사용                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>구성 방법 2(공용 리포지토리): UX 제작 캔버스

Azure Data Factory UX  **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다.  **Data Factory** 드롭다운 메뉴를 선택한 다음, 코드  **리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 위의  *구성 방법 1*에 있는 설명을 참조하세요.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Azure Data Factory를 통한 GitHub Enterprise 리포지토리 구성

데이터 팩터리를 사용해서 두 가지 방법으로 GitHub Enterprise 리포지토리를 구성할 수 있습니다.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>구성 방법 1(Enterprise 리포지토리): 시작 페이지

Azure Data Factory에서  **시작하기**  페이지로 이동합니다. 코드  **리포지토리 구성**을 선택합니다.

![Data Factory 시작 페이지](media/author-visually/github-integration-image1.png)

리 **포지토리 설정**  구성 창이 나타납니다.

![GitHub 리포지토리 설정](media/author-visually/github-integration-image3.png)

창에 다음 Azure Repos 코드 리포지토리 설정이 표시됩니다.

| **설정**                                              | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                   | **값**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **리포지토리 유형**                                      | Azure Repos 코드 리포지토리의 유형입니다.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub Enterprise 사용**                                | GitHub Enterprise를 선택하기 위한 확인란                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise URL**                                | GitHub Enterprise 루트 URL입니다. 예: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub 계정**                                       | 사용자 GitHub 계정 이름. Https에서이 이름을 찾을 수 있습니다:\//github.com/{account 이름} / {리포지토리 이름}. 이 페이지로 이동하면 GitHub 계정에 GitHub OAuth 자격 증명을 입력하라는 메시지가 표시됩니다.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | GitHub 코드 리포지토리 이름입니다. GitHub 계정은 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 계정에 이미 있는 기존 리포지토리를 사용할 수 있습니다.                                                                                                                                                                                                                              |                    |
| **공동 작업 분기**                                 | 게시에 사용되는 GitHub 공동 작업 분기입니다. 기본값은 master입니다. 다른 분기에서 리소스를 게시하려는 경우 이 설정을 변경합니다.                                                                                                                                                                                                                                                               |                    |
| **루트 폴더**                                          | GitHub 공동 작업 분기의 루트 폴더입니다.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **리포지토리로 기존 Data Factory 리소스 가져오기** | 기존 데이터 팩터리 리소스를 UX  **제작 캔버스** 에서 GitHub 리포지토리로 가져올지 여부를 지정합니다. JSON 형식의 연결된 Git 리포지토리로 데이터 팩터리 리소스를 가져오려면 상자를 선택합니다. 이 작업은 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 세트를 별도 JSON으로 내보냄). 이 상자를 선택하지 않으면 기존 리소스를 가져오지 않습니다. | 선택됨(기본값) |
| **리소스를 가져올 분기**                       | 데이터 팩터리 리소스(파이프라인, 데이터 세트, 연결된 서비스 등)를 가져올 분기를 지정합니다. 다음 분기 중 하나로 리소스를 가져올 수 있습니다. a. 공동 작업 b. 새로 만들기 c. 기존 리소스 사용                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>구성 방법 2(Enterprise 리포지토리): UX 제작 캔버스

Azure Data Factory UX  **제작 캔버스**에서 사용자의 데이터 팩터리를 찾습니다.  **Data Factory** 드롭다운 메뉴를 선택한 다음, 코드  **리포지토리 구성**을 선택합니다.

구성 창이 나타납니다. 구성 설정에 대한 자세한 내용은 위의  *구성 방법 1*에 있는 설명을 참조하세요.

## <a name="use-the-expression-language"></a>식 언어 사용
Azure Data Factory에서 지원하는 식 언어를 사용하여 속성 값에 대한 식을 지정할 수 있습니다.

**동적 콘텐츠 추가**를 선택하여 속성 값에 대한 식을 지정합니다.

![식 언어 사용](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>함수 및 매개 변수 사용

Data Factory **식 작성기**에서 파이프라인 및 데이터 세트에 대해 함수를 사용하거나 매개 변수를 지정할 수 있습니다.

지원되는 식에 대한 자세한 내용은 [Azure Data Factory의 식 및 함수](control-flow-expression-language-functions.md)를 참조하세요.

![동적 콘텐츠 추가](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>피드백 제공
기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백**을 선택하세요.

![사용자 의견](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>다음 단계
파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
