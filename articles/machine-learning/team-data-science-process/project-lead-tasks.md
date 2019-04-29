---
title: Team Data Science Process에서 프로젝트 리더의 작업
description: 프로젝트 리더가 데이터 과학 팀 프로젝트에서 수행해야 하는 작업에 대한 개요입니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4415759530985710c8376709db9955ba98066160
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61046166"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Team Data Science Process에서 프로젝트 리더의 작업

이 자습서에서는 프로젝트 리더가 데이터 과학 팀을 위해 수행해야 하는 작업에 대해 간략히 설명합니다. 목표는 [TDSP(Team Data Science Process)](overview.md)를 표준화하는 공동 작업 팀 환경을 구축하는 것입니다. TDSP는 Microsoft에서 개발하여 클라우드 기반 예측 분석 솔루션을 효율적으로 실행하는 일련의 구조화된 작업을 제공하는 프레임워크입니다. 이 프로세스를 표준화하는 데이터 과학 팀에서 처리하는 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

**프로젝트 리더**는 특정 데이터 과학 프로젝트에서 개별 데이터 과학자의 일상적인 작업을 관리합니다. 다음 그림은 이 환경을 설정하기 위해 프로젝트 리더가 수행하는 작업에 대한 워크플로입니다.

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

이 항목에서는 현재 프로젝트 리더에 대한 1, 2 및 6 워크플로 작업을 설명합니다.

> [!NOTE]
> 다음 지침에서 Azure DevOps를 사용하여 프로젝트를 위한 TDSP 팀 환경을 설정하는 데 필요한 단계를 설명합니다. Microsoft에서 TDSP를 구현하는 방법이기 때문에 Azure DevOps를 사용하여 이러한 작업을 수행하는 방법을 지정합니다. 그룹에 다른 코드 호스팅 플랫폼이 사용되는 경우 팀 리더가 수행해야 하는 작업은 일반적으로 변경되지 않습니다. 그러나 이러한 작업을 완료하는 방법은 다를 수 있습니다.


## <a name="repositories-and-directories"></a>리포지토리 및 디렉터리

이 자습서에서는 리포지토리 및 디렉터리의 약식 이름을 사용합니다. 이러한 이름을 사용하면 리포지토리와 디렉터리 간의 작업을 더 쉽게 수행할 수 있습니다. 다음 섹션에서는 이 표기법이 사용됩니다(Git 리포지토리에는 R 및 DSVM의 로컬 디렉터리에는 D).

- **R3**: 팀 리더가 설정한 팀 **ProjectTemplate** 리포지토리
- **R5**: 프로젝트에 대해 설정한 Git의 프로젝트 리포지토리
- **D3**: R3에서 복제된 로컬 디렉터리
- **D5**: R5에서 복제된 로컬 디렉터리


## <a name="0-prerequisites"></a>0. 필수 조건

[데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)에서 설명하는 그룹 관리자 및 [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)에서 설명하는 팀 리더에게 할당된 작업을 완료하면 필수 구성 요소가 충족됩니다. 

여기서 요약하면, 팀 리더 작업을 시작하기 전에 다음 요구 사항이 충족되어야 합니다. 

- 그룹 관리자가 **그룹 Azure DevOps Services**(또는 다른 일부 코드 호스팅 플랫폼의 그룹 계정)를 설정했습니다.
- 팀 리더가 사용하려는 코드 호스팅 플랫폼의 그룹 계정에 **TeamProjectTemplate 리포지토리**(R3)를 설정했습니다.
- 팀 리더가 **권한을 부여**하여 구성원인 사용자가 팀의 그룹 계정에 리포지토리를 만들었습니다.
- Git가 컴퓨터에 설치되어야 합니다. DSVM(데이터 과학 Virtual Machine)을 사용하는 경우 Git가 사전 설치되어 있으므로 계속 진행할 수 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.  
- **Windows DSVM**을 사용하는 경우 컴퓨터에 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)이 설치되어 있어야 합니다. README.md 파일에서 **다운로드 및 설치** 섹션으로 스크롤한 다음 *최신 설치 관리자*를 클릭합니다. 이렇게 하면 최신 설치 관리자 페이지로 이동합니다. 여기서 .exe 설치 관리자를 다운로드하여 실행합니다. 
- **Linux DSVM**을 사용하는 경우 DSVM에서 SSH 공개 키를 만들고 그룹 Azure DevOps Services 서버에 추가합니다. SSH에 대한 자세한 내용은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 공개 키 만들기** 섹션을 참조하세요. 


## <a name="1-create-a-project-repository-r5"></a>1. 프로젝트 리포지토리(R5) 만들기

- 그룹 Azure DevOps Services(*https://\<Azure DevOps Services Name\>.visualstudio.com*)에 로그인합니다. 
- **최근 프로젝트 및 팀**에서 **찾아보기**를 클릭합니다. 표시되는 창에서 Azure DevOps Services의 모든 프로젝트가 나열됩니다. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- 프로젝트 리포지토리를 만들 프로젝트 이름을 클릭합니다. 이 예에서는 **MyTeam**을 클릭합니다. 
- 그런 다음 **탐색**을 클릭하여 **MyTeam** 프로젝트의 홈페이지로 이동합니다.

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- **코드에 대한 공동 작업**을 클릭하여 프로젝트의 git 홈페이지로 이동합니다.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- 왼쪽 위 모서리에서 아래쪽 화살표를 클릭하고 **+ 새 리포지토리**를 선택합니다. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- **새 리포지토리 만들기** 창에서 프로젝트 git 리포지토리의 이름을 입력합니다. 리포지토리 유형으로 **Git**를 선택해야 합니다. 이 예에서는 *DSProject1* 이름을 사용합니다. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- ***DSProject1*** 프로젝트 git 리포지토리를 만들려면 **만들기**를 클릭합니다.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. DSProject1 프로젝트 리포지토리 시드

여기서는 프로젝트 템플릿 리포지토리(R3)의 **DSProject1** 프로젝트 리포지토리(R5)를 시드하는 작업을 수행합니다. 시드 절차에서는 로컬 DSVM의 D3 및 D5 디렉터리를 중간 스테이징 사이트로 사용합니다. 요약하면, 시드 경로는 다음과 같습니다. R3 -> D3 -> D5 -> R5

특정 프로젝트의 요구 사항에 맞게 **DSProject1** 프로젝트 리포지토리를 사용자 지정해야 하는 경우, 다음 절차의 마지막 두 번째 단계에서 이 사용자 지정 작업을 수행합니다. 다음은 **DSProject1** 프로젝트 리포지토리의 콘텐츠를 시드하는 데 사용되는 단계를 요약하고 있습니다. 개별 단계는 시드 절차의 하위 섹션에 해당합니다.

- 프로젝트 템플릿 리포지토리를 로컬 디렉터리에 복제합니다(로컬 D3에 팀 R3 복제).
- DSProject1 리포지토리를 로컬 디렉터리에 복제합니다(로컬 D5에 팀 R5 복제).
- 복제된 프로젝트 템플릿 콘텐츠를 DSProject1 리포지토리의 로컬 복제본에 복사합니다.  D5에 D3 콘텐츠 복사
- (선택 사항) 로컬 D5를 사용자 지정합니다.
- 로컬 DSProject1 콘텐츠를 팀 리포지토리에 푸시합니다. 팀 R5에 D5 콘텐츠 추가


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>로컬 컴퓨터의 디렉터리(D3)에 팀 프로젝트 템플릿 리포지토리(R3)를 복제합니다.

로컬 컴퓨터에서 다음 디렉터리를 만듭니다.

- *C:\GitRepos\MyTeamCommon*(Windows의 경우) 
- *$home/GitRepos/MyTeamCommon*(Linux의 경우)

해당 디렉터리로 변경합니다. 그러고 나서 다음 명령을 실행하여 프로젝트 템플릿 리포지토리를 로컬 컴퓨터에 복제합니다. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Azure DevOps를 코드 호스팅 플랫폼으로 사용하는 경우 일반적으로 *프로젝트 템플릿 리포지토리의 HTTPS URL*은 다음과 같습니다.

 ***https://\<Azure DevOps Services 이름\>.visualstudio.com/\<프로젝트 이름\>/_git/\<프로젝트 템플릿 리포지토리 이름\>*** 

이 예제에서 사용한 URL은 다음과 같습니다.

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Azure DevOps를 코드 호스팅 플랫폼으로 사용하는 경우 일반적으로 *프로젝트 템플릿 리포지토리의 SSH URL*은 다음과 같습니다.

***ssh://\<Azure DevOps Services 이름\>\@\<Azure DevOps Services 이름\>.visualstudio.com:22/\<프로젝트 이름>/_git/\<프로젝트 템플릿 리포지토리 이름\>*** 

이 예제에서 사용한 URL은 다음과 같습니다.

***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>로컬 컴퓨터의 디렉터리(D5)에 DSProject1 리포지토리(R5) 복제

디렉터리를 **GitRepos**로 변경하고, 다음 명령을 실행하여 프로젝트 리포지토리를 로컬 컴퓨터에 복제합니다. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Azure DevOps를 코드 호스팅 플랫폼으로 사용하는 경우 일반적으로 _프로젝트 리포지토리의 HTTPS URL_은 ***https://\<Azure DevOps Services 이름\>.visualstudio.com/\<프로젝트 이름>/_git/<프로젝트 리포지토리 이름\>*** 입니다. 이 예제에서는 ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***을 사용합니다.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

코드 호스팅 플랫폼으로 Azure DevOps 일반적으로 사용 중인 경우는 _project 리포지토리의 SSH URL_ 은 _ssh: / / < Azure DevOps 서비스 이름\>@< Azure DevOps 서비스 이름\>.visualstudio.com:22/ < Your 프로젝트 Name\>/\_git / < 프로젝트 리포지토리 이름\>합니다. 이 예에는 ***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***합니다.

### <a name="copy-contents-of-d3-to-d5"></a>D5에 D3 콘텐츠 복사 

이제 로컬 컴퓨터에서 .git 디렉터리의 git 메타데이터를 제외한 _D3_의 콘텐츠를 _D5_에 복사해야 합니다. 다음 스크립트는 이 작업을 수행합니다. 디렉터리에 대한 전체 경로를 정확하게 입력해야 합니다. 원본 폴더는 팀에 대한 폴더(_D3_)이고, 대상 폴더는 프로젝트에 대한 폴더(_D5_)입니다.    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

이제 _DSProject1_ 폴더에서 볼 수 있습니다. .git를 제외한 모든 파일이 _MyTeamProjectTemplate_에서 복사됩니다.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

이제 _DSProject1_ 폴더에서 볼 수 있습니다. .git의 메타데이터를 제외한 모든 파일이 _MyTeamProjectTemplate_에서 복사됩니다.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>필요한 경우 D5 사용자 지정(선택 사항)

프로젝트 템플릿(이전 단계에서 D5 디렉터리에 복사됨)에서 가져온 것이 아닌 특정 디렉터리 또는 문서가 프로젝트에 필요한 경우 이제 D5의 콘텐츠를 사용자 지정할 수 있습니다. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>그룹 Azure DevOps Services의 R5에 D5의 DSProject1 콘텐츠 추가

이제 **_DSProject1_** 의 콘텐츠를 그룹의 Azure DevOps Services에 있는 프로젝트의_R5_ 리포지토리에 푸시해야 합니다. 


- **D5** 디렉터리로 변경합니다. 
- 다음 git 명령을 사용하여 **D5**의 콘텐츠를 **R5**에 추가합니다. 명령은 Windows 및 Linux 시스템 모두에서 동일합니다. 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- 변경 및 푸시를 커밋합니다. 

> [!NOTE]
> Git 리포지토리에 처음 커밋하는 경우 `git commit` 명령을 실행하기 전에 *user.name* 및 *user.email* 전역 매개 변수를 구성해야 합니다. 다음 두 명령을 실행합니다.
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> 여러 Git 리포지토리에 커밋하는 경우 모든 Git 리포지토리에 동일한 이름과 이메일 주소를 사용합니다. 동일한 이름과 이메일 주소를 사용하면 나중에 여러 리포지토리에서 Git 작업을 추적하기 위해 PowerBI 대시보드를 작성할 때 편리합니다.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. 프로젝트 리소스로 Azure File Storage 만들기 및 탑재(선택 사항)

프로젝트 원시 데이터 또는 프로젝트에 생성된 기능과 같은 데이터를 공유하기 위해 Azure File Storage를 만들어 모든 프로젝트 구성원이 여러 DSVM의 동일한 데이터 집합에 액세스할 수 있게 하려면 [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)의 섹션 3 및 4의 지침을 따릅니다. 


## <a name="next-steps"></a>다음 단계

다음은 Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명으로 연결되는 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 개별 참가자 작업](project-ic-tasks.md)
