---
title: Team Data Science Process에서 개별 기여자에 대한 작업
description: 개별 참가자가 데이터 과학 팀 프로젝트에서 수행하는 작업에 대한 개요입니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a52907fa6c0e2483479031fbb3d1ad68a121d95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043355"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process에서 개별 기여자에 대한 작업

이 항목에서는 개별 참가자가 데이터 과학 팀을 위해 수행해야 하는 작업에 대해 간략히 설명합니다. 목표는 [TDSP(Team Data Science Process)](overview.md)를 표준화하는 공동 작업 팀 환경을 구축하는 것입니다. 이 프로세스를 표준화하는 데이터 과학 팀에서 처리하는 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

프로젝트 개별 참가자(데이터 과학자)가 프로젝트에 대한 TDSP 환경을 설정하는 작업은 다음과 같습니다. 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities**는 그룹 전체에서 유용한 유틸리티를 공유하기 위해 그룹에서 유지 관리하는 리포지토리입니다. 
- **TeamUtilities**는 팀에서 팀을 위해 특별히 유지 관리하는 리포지토리입니다. 

TDSP에서 데이터 과학 프로젝트를 실행하는 방법에 대한 지침은 [데이터 과학 프로젝트 실행](project-execution.md)을 참조하세요. 

>[AZURE.NOTE] 다음 지침에서 Azure DevOps를 사용하여 TDSP 팀 환경을 설정하는 데 필요한 단계를 설명합니다. Microsoft에서 TDSP를 구현하는 방법이기 때문에 Azure DevOps를 사용하여 이러한 작업을 수행하는 방법을 지정합니다. 그룹에 다른 코드 호스팅 플랫폼이 사용되는 경우 팀 리더가 수행해야 하는 작업은 일반적으로 변경되지 않습니다. 그러나 이러한 작업을 완료하는 방법은 다를 수 있습니다.


## <a name="repositories-and-directories"></a>리포지토리 및 디렉터리

이 자습서에서는 리포지토리 및 디렉터리의 약식 이름을 사용합니다. 이러한 이름을 사용하면 리포지토리와 디렉터리 간의 작업을 더 쉽게 수행할 수 있습니다. 다음 섹션에서는 이 표기법이 사용됩니다(Git 리포지토리에는 **R** 및 DSVM의 로컬 디렉터리에는 **D**).

- **R2**: 그룹 관리자가 Azure DevOps 그룹 서버에 설정한 Git의 GroupUtilities 리포지토리
- **R4**: 팀 리더가 설정한 Git의 TeamUtilities 리포지토리
- **R5**: 프로젝트 리더가 설정한 Git의 Project 리포지토리
- **D2**: R2에서 복제된 로컬 디렉터리
- **D4**: R4에서 복제된 로컬 디렉터리
- **D5**: R5에서 복제된 로컬 디렉터리


## <a name="step-0-prerequisites"></a>0단계: 필수 조건

[데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)에서 설명하는 그룹 관리자에게 할당된 작업을 완료하면 필수 구성 요소가 충족됩니다. 여기서 요약하면, 팀 리더 작업을 시작하기 전에 다음 요구 사항이 충족되어야 합니다. 
- 그룹 관리자가 **GroupUtilities** 리포지토리를 설정했습니다(있는 경우). 
- 팀 리더가 **TeamUtilities** 리포지토리를 설정했습니다(있는 경우).
- 프로젝트 리더가 프로젝트 리포지토리를 설정했습니다. 
- 프로젝트 리포지토리에서 복제하고 이 리포지토리로 다시 밀어 넣을 수 있는 권한이 있는 프로젝트 리더가 설정한 프로젝트 리포지토리에 구성원으로 추가되었습니다.

두 번째의 **TeamUtilities** 리포지토리 필수 구성 요소는 팀에 팀 고유의 유틸리티 리포지토리가 있는지 여부에 따른 선택 사항입니다. 다른 세 가지 필수 구성 요소 중 하나라도 완료되지 않은 경우 팀 리더, 프로젝트 리더 또는 해당 대리자에게 문의하여 [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md) 또는 [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)에 대한 지침에 따라 설정합니다.

- Git가 컴퓨터에 설치되어야 합니다. DSVM(데이터 과학 Virtual Machine)을 사용하는 경우 Git가 사전 설치되어 있으므로 계속 진행할 수 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.  
- **Windows DSVM**을 사용하는 경우 컴퓨터에 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)이 설치되어 있어야 합니다. README.md 파일에서 **다운로드 및 설치** 섹션으로 스크롤한 다음 *최신 설치 관리자*를 클릭합니다. 이렇게 하면 최신 설치 관리자 페이지로 이동합니다. 여기서 .exe 설치 관리자를 다운로드하여 실행합니다. 
- **Linux DSVM**을 사용하는 경우 DSVM에서 SSH 공개 키를 만들고 그룹 Azure DevOps Services 서버에 추가합니다. SSH에 대한 자세한 내용은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 공개 키 만들기** 섹션을 참조하세요. 
- 팀 리더 및/또는 프로젝트 리더가 DSVM에 탑재해야 하는 Azure File Storage를 만든 경우 해당 Azure 파일 스토리지 정보를 가져와야 합니다. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>1-3단계: 로컬 머신에 그룹, 팀 및 프로젝트 리포지토리 복제

이 섹션에서는 프로젝트 개별 참가자의 처음 세 가지 작업을 수행하는 방법에 대해 설명합니다. 

- R2 **GroupUtilities** 리포지토리를 D2에 복제합니다.
- R4 **TeamUtilities** 리포지토리를 D4에 복제합니다. 
- R5 **Project** 리포지토리를 D5에 복제합니다.

로컬 컴퓨터에서 ***C:\GitRepos***(Windows의 경우) 또는 ***$home/GitRepos***(Linux의 경우) 디렉터리를 만든 다음 해당 디렉터리로 변경합니다. 

다음 명령 중 하나를 실행하여(OS에 맞게) **GroupUtilities**, **TeamUtilities** 및 **Project** 리포지토리를 로컬 컴퓨터의 디렉터리에 복제합니다. 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

프로젝트 디렉터리 아래에 세 개의 폴더가 표시되는지 확인합니다.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

프로젝트 디렉터리 아래에 세 개의 폴더가 표시되는지 확인합니다.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>4-5단계: DSVM에 Azure 파일 스토리지 탑재(선택 사항)

Azure File Storage를 DSVM에 탑재하려면 [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)의 섹션 4에서 설명하는 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음은 Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명으로 연결되는 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 개별 참가자 작업](project-ic-tasks.md)

