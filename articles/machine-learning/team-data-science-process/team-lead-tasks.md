---
title: Team Data Science Process Team에서 팀 리더의 작업
description: 데이터 과학 팀 프로젝트의 팀 리더가 데이터 과학 팀을 위해 완료해야 하는 작업에 대해 간략히 설명합니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45be3d7f865c7b72ae62efbf99dbbb4594b1846f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812071"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>Team Data Science Process Team에서 팀 리더의 작업

이 항목에서는 팀 리더가 데이터 과학 팀을 위해 완료해야 하는 작업에 대해 간략히 설명합니다. 목표는 [TDSP](overview.md)(Team Data Science Process)를 표준화하는 공동 작업 팀 환경을 구축하는 것입니다. TDSP는 예측 분석 솔루션 및 지능형 애플리케이션을 효율적으로 제공하는 기민한 반복 데이터 과학 방법론입니다. 공동 작업 및 팀 학습을 향상시키는 데 도움이 되도록 디자인되었습니다. 이 프로세스는 Microsoft와 업계의 모범 사례 및 구조를 추출하고 기업이 분석 프로그램의 이점을 완전히 실현할 수 있도록 데이터 과학 이니셔티브를 성공적으로 구현하는 데 필요합니다. 이 프로세스를 표준화하는 데이터 과학 팀의 인력 역할 및 관련 작업의 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

**팀 리더**는 기업의 데이터 과학 단위 팀을 관리합니다. 팀은 여러 명의 데이터 과학자로 구성됩니다. 소수의 데이터 과학자로만 구성된 데이터 과학 단위의 경우 **그룹 관리자**와 **팀 리더**가 같은 사람이거나 대리인에게 자신의 작업을 위임할 수 있습니다. 하지만 작업 자체는 변경되지 않습니다. 다음 그림은 팀 리더가 이 환경을 설정하기 위해 완료해야 할 작업에 대한 워크플로입니다.

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Azure DevOps를 코드 호스팅 플랫폼으로 사용하고 팀에 별도의 Azure DevOps 프로젝트를 원할 경우 이 그림의 블록 1과 2의 작업이 필요합니다. 이러한 작업이 완료되면 팀의 모든 리포지토리를 이 프로젝트로 만들 수 있습니다. 

그룹 관리자가 다음 섹션에 지정된 여러 가지 필수 조건 작업을 수행한 후에는 이 자습서에 있는 다섯 가지 주요 작업(일부 선택 사항)을 완료해야 합니다. 이러한 작업은 이 항목의 주 번호가 매겨진 다음 섹션에 해당합니다.

1. 그룹의 Azure DevOps Services 및 프로젝트의 팀 리포지토리 두 개에 **프로젝트**를 만듭니다.
    - **ProjectTemplate 리포지토리** 
    - **TeamUtilities 리포지토리**
2. 그룹 관리자가 설정한 **GroupProjectTemplate** 리포지토리에서 팀 **ProjectTemplate** 리포지토리를 시드합니다. 
3. 팀 데이터 및 분석 리소스를 만듭니다.
    - 팀 전용 유틸리티를 **TeamUtilities** 리포지토리에 추가합니다. 
    - (선택 사항) 전체 팀에게 유용한 데이터 자산을 저장하는 데 사용할 **Azure File Storage**를 만듭니다. 
4. (선택 사항) Azure File Storage를 팀 리더의 **DSVM**(데이터 과학 Virtual Machine)에 탑재하고 여기에 데이터 자산을 추가합니다.
5. 팀 구성원을 추가하고 권한을 구성하여 **보안 제어**를 설정합니다.

>[AZURE.NOTE] 다음 지침에서 Azure DevOps를 사용하여 TDSP 팀 환경을 설정하는 데 필요한 단계를 설명합니다. Microsoft에서 TDSP를 구현하는 방법이기 때문에 Azure DevOps를 사용하여 이러한 작업을 수행하는 방법을 지정합니다. 그룹에 다른 코드 호스팅 플랫폼이 사용되는 경우 팀 리더가 수행해야 하는 작업은 일반적으로 변경되지 않습니다. 그러나 이러한 작업을 완료하는 방법은 다를 수 있습니다.

## <a name="repositories-and-directories"></a>리포지토리 및 디렉터리

이 항목에서는 리포지토리 및 디렉터리의 약식 이름을 사용합니다. 이 이름을 사용하면 리포지토리와 디렉터리 간의 작업을 쉽게 수행할 수 있습니다. 다음 섹션에서는 이 표기법이 사용됩니다(Git 리포지토리에는 **R** 및 DSVM의 로컬 디렉터리에는 **D**).

- **R1**: 그룹 관리자가 Azure DevOps 그룹 서버에 설정한 Git의 **GroupProjectTemplate** 리포지토리
- **R3**: 사용자가 설정한 팀 Git의 **ProjectTemplate** 리포지토리
- **R4**: 사용자가 설정한 Git의 **TeamUtilities** 리포지토리
- **D1**: R1에서 복제되어 D3에 복사된 로컬 디렉터리
- **D3**: R3에서 복제되어, 사용자 지정하고, 다시 R3에 복사된 로컬 디렉터리
- **D4**: R4에서 복제되어, 사용자 지정하고, 다시 R4에 복사된 로컬 디렉터리

이 자습서의 리포지토리 및 디렉터리에 지정된 이름은 대규모 데이터 과학 그룹 내에서 자신의 팀에 대해 별도의 프로젝트를 설정하는 것을 전제로 제공되었습니다. 그러나 팀 리더에게는 다른 옵션이 있습니다.

- 전체 그룹은 단일 프로젝트를 만들 수 있습니다. 그런 다음 모든 데이터 과학 팀의 모든 프로젝트가 이 단일 프로젝트 하에 있게 됩니다. 이를 위해 git 관리자가 이 지침에 따라 단일 프로젝트를 만들도록 지정할 수 있습니다. 이 시나리오는 다음과 같은 경우에 유효할 수 있습니다.
    -  여러 데이터 과학 팀으로 구성되지 않은 소규모 데이터 과학 그룹 
    -  여러 데이터 과학 팀으로 구성되고 그룹 수준의 스프린트 계획과 같은 작업과 팀 간 공동 작업을 최적화하려는 대규모 데이터 과학 그룹 
- 팀은 전체 그룹에 대해 단일 프로젝트에서 팀별 프로젝트 템플릿 또는 팀별 유틸리티를 선택할 수 있습니다. 이 경우 팀 리더는 프로젝트 템플릿 리포지토리 및/또는 팀 유틸리티 리포지토리를 동일한 프로젝트 아래에 만들어야 합니다. 이러한 리포지토리 이름을 *<TeamName\>ProjectTemplate* 및 *<TeamName\>Utilities*로 지정합니다. 예를 들어 *TeamJohnProjectTemplate* 및 *TeamJohnUtilities*입니다. 

모든 경우, 팀 리더는 프로젝트 및 유틸리티 리포지토리를 설정 및 복제할 때 채택할 템플릿 및 유틸리티 리포지토리를 팀 구성원이 알 수 있도록 해야 합니다. 프로젝트 리더는 [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)을 따라야 별도의 프로젝트 또는 단일 프로젝트 여부에 상관없이 프로젝트 리포지토리를 만들 수 있습니다. 


## <a name="0-prerequisites"></a>0. 필수 조건

[데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)에서 설명하는 그룹 관리자에게 할당된 작업을 완료하면 필수 구성 요소가 충족됩니다. 여기서 요약하면, 팀 리더 작업을 시작하기 전에 다음 요구 사항이 충족되어야 합니다. 

- 그룹 관리자가 **그룹 Azure DevOps Services**(또는 다른 일부 코드 호스팅 플랫폼의 그룹 계정)를 설정했습니다.
- 사용하려는 코드 호스팅 플랫폼의 그룹 관리자가 **GroupProjectTemplate 리포지토리**(R1)를 그룹 계정에 설정합니다.
- 팀의 리포지토리를 만들기 위해 그룹 계정에 대한 **권한**을 얻습니다.
- Git가 컴퓨터에 설치되어야 합니다. DSVM(데이터 과학 Virtual Machine)을 사용하는 경우 Git가 사전 설치되어 있으므로 계속 진행할 수 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.  
- **Windows DSVM**을 사용하는 경우 컴퓨터에 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)이 설치되어 있어야 합니다. README.md 파일에서 **다운로드 및 설치** 섹션으로 스크롤한 다음 *최신 설치 관리자*를 클릭합니다. 이렇게 하면 최신 설치 관리자 페이지로 이동합니다. 여기서 .exe 설치 관리자를 다운로드하여 실행합니다. 
- **Linux DSVM**을 사용하는 경우 DSVM에서 SSH 공개 키를 만들고 그룹 Azure DevOps Services 서버에 추가합니다. SSH에 대한 자세한 내용은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 공개 키 만들기** 섹션을 참조하세요. 
    
## <a name="1-create-a-project-and-repositories"></a>1. 프로젝트 및 리포지토리 만들기

버전 관리 및 공동 작업을 위한 코드 호스팅 플랫폼으로 Azure DevOps를 사용하는 경우 이 단계를 완료합니다. 이 섹션에서는 그룹의 Azure DevOps Services에 세 가지 아티팩트를 만듭니다.

- Azure DevOps **MyTeam** 프로젝트
- Git의 **MyProjectTemplate** 리포지토리(**R3**)
- Git의 **MyTeamUtilities** 리포지토리(**R4**)

### <a name="create-the-myteam-project"></a>MyTeam 프로젝트 만들기

- 그룹의 Azure DevOps Services 홈페이지 URL(`https://<Azure DevOps Services Name\>.visualstudio.com`)로 이동합니다. 
- **새로 만들기**를 클릭하여 프로젝트를 만듭니다. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- 프로젝트 만들기 창에 프로젝트 이름(이 예에서는 **MyTeam**)을 입력하라는 메시지가 표시됩니다. **Agile**을 **프로세스 템플릿**으로 선택하고 **Git**를 **버전 제어**로 선택합니다. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- **프로젝트 만들기**를 클릭합니다. 프로젝트 **MyTeam**이 1분 내에 만들어집니다. 

- 프로젝트 **MyTeam**을 만든 후 **프로젝트로 탐색** 단추를 클릭하여 프로젝트의 홈페이지로 이동합니다. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- **축하합니다!** 라는 팝업 창이 표시되면 **코드 추가**(빨간색 상자의 단추)를 클릭합니다. 그렇지 않은 경우 **코드**(노란색 상자)를 클릭합니다. 그러면 프로젝트의 Git 리포지토리 페이지로 이동합니다. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Git에 MyProjectTemplate 리포지토리(R3) 만들기

- 프로젝트의 Git 리포지토리 페이지에서 리포지토리 이름 **MyTeam** 옆의 아래쪽 화살표를 클릭하고 **리포지토리 관리...** 를 선택합니다.

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- 프로젝트의 제어판에 있는 **버전 제어** 탭에서 **MyTeam**을 클릭한 다음 **리포지토리 이름 바꾸기...** 를 선택합니다. 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- **MyTeam 리포지토리 이름 바꾸기** 창에서 리포지토리에 대한 새 이름을 입력합니다. 이 예제에서는 *MyTeamProjectTemplate*을 입력합니다. *<팀 이름\>ProjectTemplate*과 같이 선택할 수 있습니다. 계속하려면 **이름 바꾸기**를 클릭합니다.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Git에서 MyTeamUtilities 리포지토리(R4) 만들기

- 프로젝트에 새 리포지토리 *<팀 이름\>Utilities*를 만들려면 프로젝트 제어판의 **버전 제어** 탭에서 **새 리포지토리...** 를 클릭합니다.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- **새 리포지토리 만들기** 팝업 창에 이 리포지토리에 대한 이름을 제공합니다. 이 예제에서는 *MyTeamUtilities*로 이름을 지정합니다. 이 이름은 표기법상 **R4**입니다. *<팀 이름\>Utilities*와 같은 항목을 선택합니다. **형식**으로 **Git**를 선택합니다. 그런 다음 계속하려면 **만들기**를 클릭합니다.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- 프로젝트 **MyTeam**에서 만든 두 가지 새로운 Git 리포지토리가 있는지 확인합니다. 이 예제에서: 

- **MyTeamProjectTemplate**(R3) 
- **MyTeamUtilities**(R4)

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2. ProjectTemplate 및 TeamUtilities 리포지토리 시드

시드 프로시저에서는 로컬 DSVM의 디렉터리를 중간 스테이징 사이트로 사용합니다. 특정 팀 요구 사항을 충족시키기 위해 **ProjectTemplate** 및 **TeamUtilities** 리포지토리를 사용자 지정해야 하는 경우 다음 절차의 끝에서 두 번째 단계에서 수행합니다. 다음은 데이터 과학 팀의 **MyTeamProjectTemplate** 및 **MyTeamUtilities** 리포지토리 콘텐츠를 시드하는 데 사용된 단계에 대한 요약입니다. 개별 단계는 시드 절차의 하위 섹션에 해당합니다.

- 그룹 리포지토리를 로컬 디렉터리로 복제: 팀 R1 - 복제 -> 로컬 D1
- 팀 리포지토리를 로컬 디렉터리로 복제: 팀 R3 및 R4 - 복제 -> 로컬 R3 및 R4
- 그룹 프로젝트 템플릿 콘텐츠를 로컬 팀 폴더에 복사:  D3에 D1 콘텐츠 복사
- (선택 사항) 로컬 D3 및 D4 사용자 지정
- 팀 리포지토리에 로컬 디렉터리 콘텐츠 푸시: 팀 R3 및 R4에 D3 및 D4 콘텐츠 추가


### <a name="initialize-the-team-repositories"></a>팀 리포지토리 초기화

이 단계에서는 그룹 프로젝트 템플릿 리포지토리에서 프로젝트 템플릿 리포지토리를 초기화합니다.

- **GroupProjectTemplate**(**R1**) 리포지토리에서 **MyTeamProjectTemplate** 리포지토리(**R3**)


### <a name="clone-group-repositories-into-local-directories"></a>로컬 디렉터리에 그룹 리포지토리 복제

이 절차를 시작하려면 다음을 수행합니다.

- 로컬 컴퓨터에 디렉터리를 만듭니다.
    - **Windows**의 경우: **C:\GitRepos\GroupCommon** 및 **C:\GitRepos\MyTeam**
    - **Linux**의 경우: 홈 디렉터리의 **GitRepos\GroupCommon** 및 **GitRepos\MyTeam** 
- **GitRepos\GroupCommon** 디렉터리로 변경합니다.
- 로컬 시스템의 운영 체제에서 다음 명령을 적절하게 실행합니다.

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

이 명령은 그룹 Azure DevOps Services의 **GroupProjectTemplate**(R1) 리포지토리를 로컬 시스템의 **GitRepos\GroupCommon**에 있는 로컬 디렉터리에 복제합니다. 복제 후 디렉터리 **GroupProjectTemplate**(D1)이 **GitRepos\GroupCommon** 디렉터리에 작성됩니다. 여기에서는 그룹 관리자가 프로젝트 **GroupCommon**을 만들었으며 **GroupProjectTemplate** 리포지토리가 이 프로젝트 하에 있다고 가정합니다. 


### <a name="clone-your-team-repositories-into-local-directories"></a>로컬 디렉터리에 팀 리포지토리 복제

이 명령은 그룹 Azure DevOps Services의 프로젝트 **MyTeam**에 있는 **MyTeamProjectTemplate**(R3) 및 **MyTeamUtilities**(R4) 리포지토리를 로컬 컴퓨터의 **GitRepos\MyTeam**에 있는 **MyTeamProjectTemplate**(D3) 및 **MyTeamUtilities**(D4) 디렉터리에 복제합니다. 

- **GitRepos\MyTeam** 디렉터리로 변경합니다.
- 로컬 시스템의 운영 체제에서 다음 명령을 적절하게 실행합니다. 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

복제 후 **MyTeamProjectTemplate**(D3) 및 **MyTeamUtilities**(D4) 디렉터리가 **GitRepos\MyTeam** 디렉터리에 만들어집니다. 여기서는 프로젝트 템플릿 및 유틸리티 리포지토리 **MyTeamProjectTemplate** 및 **MyTeamUtilities**로 명명했다고 가정합니다. 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>그룹 프로젝트 템플릿 콘텐츠를 로컬 프로젝트 템플릿 디렉터리에 복사

로컬 **GroupProjectTemplate**(D1) 폴더의 콘텐츠를 로컬 **MyTeamProjectTemplate**(D3)에 복사하려면 다음 셸 스크립트 중 하나를 실행합니다. 

#### <a name="from-the-powershell-command-line-for-windows"></a>Windows용 PowerShell 명령줄에서 다음을 실행합니다.       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>**Linux DSVM**용 Linux 셸에서 다음을 실행합니다.
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

스크립트는 .git 디렉터리의 콘텐츠를 제외합니다. 스크립트는 원본 디렉터리 D1과 대상 디렉터리 D3에 대한 **전체 경로**를 제공하라는 메시지를 표시합니다.
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>프로젝트 템플릿 또는 팀 유틸리티 사용자 지정(선택 사항)

필요한 경우 설치 프로세스의 이 단계에서 **MyTeamProjectTemplate**(D3) 및 **MyTeamUtilities**(D4)를 사용자 지정합니다. 

- 팀의 특정 요구 사항을 충족하기 위해 D3의 콘텐츠를 사용자 지정하려는 경우 템플릿 문서를 수정하거나 디렉터리 구조를 변경할 수 있습니다.

- 팀이 전체 팀과 공유하려는 일부 유틸리티를 개발한 경우 이러한 유틸리티를 복사하여 D4 디렉터리에 붙여넣습니다. 


### <a name="push-local-directory-content-to-team-repositories"></a>팀 리포지토리에 로컬 디렉터리 콘텐츠 푸시

필요에 따라 사용자 지정된 로컬 디렉터리 D3 및 D4의 콘텐츠를 팀 리포지토리 R3 및 R4에 추가하려면 Windows PowerShell 콘솔 또는 Linux 셸에서 다음 git bash 명령을 실행합니다. **GitRepos\MyTeam\MyTeamProjectTemplate** 디렉터리에서 명령을 실행합니다.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

이 스크립트가 실행되면 그룹의 Azure DevOps Services의 MyTeamProjectTemplate 리포지토리에 있는 파일이 거의 즉시 동기화됩니다.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

4개 Git 명령의 동일한 집합을 **GitRepos\MyTeam\MyTeamUtilities** 디렉터리에서 실행합니다. 

> [AZURE.NOTE]Git 리포지토리에 처음 커밋하는 경우 `git commit` 명령을 실행하기 전에 전역 매개 변수 *user.name* 및 *user.email*을 구성해야 합니다. 다음 두 명령을 실행합니다.
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> 여러 Git 리포지토리에 커밋하는 경우 각 Git 리포지토리에 커밋할 때 동일한 이름과 이메일 주소를 사용합니다. 동일한 이름과 이메일 주소를 사용하면 나중에 여러 리포지토리에서 Git 작업을 추적하기 위해 PowerBI 대시보드를 작성할 때 편리합니다.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. 팀 데이터 및 분석 리소스 만들기(선택 사항)

전체 팀과 데이터 및 분석 리소스를 공유하면 성능 및 비용상의 이점을 얻을 수 있습니다. 팀 구성원은 공유 리소스에서 프로젝트를 실행하고 예산을 절약하며 보다 효율적으로 공동 작업을 수행할 수 있습니다. 이 섹션에서는 Azure File Storage를 만드는 방법에 대한 지침을 제공합니다. 다음 섹션에서는 Azure File Storage를 로컬 시스템에 탑재하는 방법에 대한 지침을 제공합니다. Azure 데이터 과학 Virtual Machine, Azure HDInsight Spark 클러스터 등 다른 리소스 공유에 대한 추가 정보는 [플랫폼 및 도구](platforms-and-tools.md)를 참조하세요. 이 항목에서는 데이터 과학 관점에서 요구 사항에 적합한 리소스를 선택하는 방법에 대한 지침과 제품 페이지 및 게시된 기타 유용한 관련 자습서에 대한 링크를 제공합니다.

>[AZURE.NOTE] 느리고 비용이 많이 드는 데이터 센터 간 데이터 전송을 방지하려면 리소스 그룹, 저장소 계정 및 Azure VM(예: DSVM)이 동일한 Azure 데이터 센터에 있는지 확인합니다. 

다음 스크립트를 실행하여 팀에 대한 Azure File Storage를 만듭니다. 팀의 Azure File Storage를 사용하여 전체 팀에 유용한 데이터 자산을 저장할 수 있습니다. 스크립트에서 Azure 계정 및 구독 정보를 묻는 메시지를 표시하면 이 자격 증명을 입력합니다. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Windows에서 PowerShell을 사용하여 Azure File Storage 만들기

PowerShell 명령줄에서 이 스크립트를 실행합니다.

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

메시지가 나타나면 Microsoft Azure 계정에 로그인합니다.

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

사용할 Azure 구독을 선택합니다.

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

선택한 구독에서 사용할 저장소 계정을 선택하거나 새 계정을 만듭니다.

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

만들려는 Azure File Storage의 이름을 입력합니다. 소문자, 숫자 및 -만 허용됩니다.

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

이 스토리지를 만든 후에 쉽게 탑재 및 공유할 수 있도록 하려면 Azure File Storage 정보를 텍스트 파일에 저장하고 해당 위치에 대한 경로를 기록합니다. 특히 다음 섹션에서 Azure File Storage를 Azure 가상 시스템에 탑재하려면 이 파일이 필요합니다. 

이 텍스트 파일을 ProjectTemplate 리포지토리에 체크인하는 것이 좋습니다. 권장 디렉터리는 **Docs\DataDictionaries**입니다. 따라서 이 데이터 자산은 팀의 모든 프로젝트에서 액세스할 수 있습니다. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Linux 스크립트로 Azure File Storage 만들기

Linux 셸에서 이 스크립트를 실행합니다.

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

이 화면의 지침에 따라 Microsoft Azure 계정에 로그인합니다.

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

사용할 Azure 구독을 선택합니다.

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

선택한 구독에서 사용할 저장소 계정을 선택하거나 새 계정을 만듭니다.

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

만들 Azure File Storage의 이름을 입력합니다. 소문자, 숫자 및 -만 허용됩니다.

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

이 스토리지를 만든 후에 쉽게 액세스할 수 있도록 하려면 Azure File Storage 정보를 텍스트 파일에 저장하고 해당 위치에 대한 경로를 기록합니다. 특히 다음 섹션에서 Azure File Storage를 Azure 가상 시스템에 탑재하려면 이 파일이 필요합니다.

이 텍스트 파일을 ProjectTemplate 리포지토리에 체크인하는 것이 좋습니다. 권장 디렉터리는 **Docs\DataDictionaries**입니다. 따라서 이 데이터 자산은 팀의 모든 프로젝트에서 액세스할 수 있습니다. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Azure File Storage 탑재(선택 사항)

Azure File Storage가 성공적으로 만들어진 후에는 다음 PowerShell 또는 Linux 스크립트 중 하나를 사용하여 로컬 컴퓨터에 탑재할 수 있습니다.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Windows에서 PowerShell을 사용하여 Azure File Storage 탑재

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
로그인하지 않은 경우 먼저 로그인하라는 메시지가 나타납니다. 

Azure File Storage 정보 파일이 있는지 묻는 메시지가 나타나면 **Enter** 또는 **y**를 클릭하고 이전 단계에서 만든 파일의 ***전체 경로와 이름**을 입력합니다. Azure File Storage 탑재에 대한 정보는 해당 파일에서 직접 읽으므로 다음 단계를 진행합니다.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Azure File Storage 정보가 포함된 파일이 없으면 이 섹션의 끝에 제공된 키보드의 정보를 입력하는 단계를 참조하세요.

그런 다음 가상 머신에 추가할 드라이브의 이름을 입력하라는 메시지가 나타납니다. 기존 드라이브 이름 목록이 화면에 인쇄됩니다. 목록에 아직 없는 드라이브 이름을 제공해야 합니다.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

새 F 드라이브가 컴퓨터에 성공적으로 탑재되었는지 확인합니다.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Azure 파일 스토리지 정보를 수동으로 입력하는 방법:** 텍스트 파일에 Azure 파일 스토리지 정보가 없는 경우 다음 화면의 지침에 따라 필요한 구독, 스토리지 계정 및 Azure 파일 스토리지 정보를 입력할 수 있습니다.

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Azure 구독 이름을 입력하고 Azure File Storage가 생성된 스토리지 계정을 선택하고 Azure 파일 스토리지 이름을 입력합니다.

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Linux 스크립트로 Azure File Storage 탑재

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

로그인하지 않은 경우 먼저 로그인하라는 메시지가 나타납니다. 

Azure File Storage 정보 파일이 있는지 묻는 메시지가 나타나면 **Enter** 또는 **y**를 클릭하고 이전 단계에서 만든 파일의 ***전체 경로와 이름**을 입력합니다. Azure File Storage 탑재에 대한 정보는 해당 파일에서 직접 읽으므로 다음 단계를 진행합니다.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

그런 다음 가상 머신에 추가할 드라이브의 이름을 입력하라는 메시지가 나타납니다. 기존 드라이브 이름 목록이 화면에 인쇄됩니다. 목록에 아직 없는 드라이브 이름을 제공해야 합니다.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

새 F 드라이브가 컴퓨터에 성공적으로 탑재되었는지 확인합니다.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Azure 파일 스토리지 정보를 수동으로 입력하는 방법:** 텍스트 파일에 Azure 파일 스토리지 정보가 없는 경우 다음 화면의 지침에 따라 필요한 구독, 스토리지 계정 및 Azure 파일 스토리지 정보를 입력할 수 있습니다.

- **n**을 입력합니다.
- 이전 단계에서 Azure File Storage가 생성된 구독 이름의 인덱스를 선택합니다.

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- 구독에서 스토리지 계정을 선택하고 Azure File Storage 이름을 입력합니다.

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- 시스템에 추가할 드라이브의 이름을 입력합니다. 이 이름은 기존 드라이브와 구별되어야 합니다.

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. 보안 제어 정책 설정 

그룹 Azure DevOps Services의 홈페이지에서 오른쪽 상단의 사용자 이름 옆에 있는 **톱니 바퀴 아이콘**을 클릭하고 **보안** 탭을 선택합니다. 여기에서 다양한 권한을 사용하여 팀에 구성원을 추가할 수 있습니다.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>다음 단계

다음은 Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명으로 연결되는 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 개별 참가자 작업](project-ic-tasks.md)
