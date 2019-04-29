---
title: Azure DevTest Labs에서 Azure DevOps에서 이미지 팩터리 실행 | Microsoft Docs
description: Azure DevTest Labs에서 사용자 지정 이미지 팩터리를 만드는 방법에 알아봅니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: abb85d568e26e4b6f85b960a2560aae570daf201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61320614"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Azure DevOps에서 이미지 팩터리 실행
이 문서에서는 Azure DevOps (이전의 Visual Studio Team Services)에서 이미지 팩터리를 실행 하는 데 필요한 모든 준비를 다룹니다.

> [!NOTE]
> 모든 오케스트레이션 엔진이 작동 합니다. Azure DevOps 필수 아닙니다. 이미지 팩터리 고 다른 CI/CD 시스템의 경우 Windows 작업 Scheduler를 사용 하 여 수동으로 실행할 수 있도록 Azure PowerShell 스크립트를 사용 하 여 실행 됩니다.

## <a name="create-a-lab-for-the-image-factory"></a>이미지 팩터리에 대 한 랩 만들기
이미지 팩터리를 설정 하는 첫 번째 단계는 Azure DevTest Labs에서 랩을 만드는 것입니다. 이 랩에서 이미지 팩터리 랩 가상 컴퓨터를 생성 하 고 사용자 지정 이미지를 저장 위치입니다. 이 랩에서 전체 이미지 팩터리 프로세스의 일부로 간주 됩니다. 랩을 만든 후 나중에 필요 하므로 이름을 저장 해야 합니다.

## <a name="scripts-and-templates"></a>스크립트 및 템플릿
팀에 대 한 이미지 팩터리를 채택 하 고 다음 단계는 사용할 수 있는 것을 이해 하는 것입니다. 템플릿과 이미지 팩터리 스크립트에서 공개적으로 사용할 수는 [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)합니다. 구성 요소에 대 한 개요는 다음과 같습니다.

- 이미지 팩터리입니다. 이 루트 폴더입니다.
    - 구성 이미지 팩터리에 대 한 입력
        - GoldenImages. 이 폴더는 사용자 지정 이미지의 정의 나타내는 JSON 파일을 포함 합니다.
        - Labs.json 합니다. 팀에 특정 사용자 지정 이미지를 받기 위해 등록 하는 파일입니다.
- 스크립트입니다. 이미지 팩터리에 대 한 엔진입니다.

이 섹션의에서 문서에서는 이러한 템플릿과 스크립트에 대해 자세히 설명 합니다.

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps 팀 프로젝트 만들기
Azure DevOps를 사용 하 여 소스 코드를 저장, 한 곳에서 Azure PowerShell을 실행할 수 있습니다. 이미지를 최신 상태로 유지 하기 위해 되풀이 실행을 예약할 수 있습니다. 문제를 진단 하는 결과 기록 하기 위한 좋은 기능 있습니다.  그러나가 요구 사항이 Azure DevOps를 사용 하 여, Azure에 연결할 수 있으며 Azure PowerShell을 실행할 수 있는 모든 프로그램 엔진을 사용할 수 있습니다.

기존 DevOps 계정 또는 프로젝트를 대신 사용 하려는 경우이 단계를 건너뜁니다.

시작 하려면 Azure DevOps에 무료 계정을 만듭니다. 방문 https://www.visualstudio.com/ 선택한 **무료로** 바로 아래에서 **Azure DevOps** (이전의: VSTS). 고유한 계정 이름을 선택 하 고 Git를 사용 하 여 코드를 관리 하도록 선택 하도록 해야 합니다. 이 만들어진 후 팀 프로젝트에 URL을 저장 합니다. 다음은 샘플 URL: `https://<accountname>.visualstudio.com/MyFirstProject`합니다.

## <a name="check-in-the-image-factory-to-git"></a>Git으로 이미지 팩터리 체크 인
PowerShell, 템플릿 및 이미지 팩터리에 대 한 구성에는 [공용 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)합니다. 새 팀 프로젝트에 코드를 얻을 수 있는 가장 빠른 방법은 리포지토리를 가져오는 것입니다. 이 위치 (추가 문서 및 샘플 얻게) 따라서 전체 DevTest Labs 리포지토리에서 끌어옵니다.

1. 이전 단계에서 만든 Azure DevOps project를 방문 (URL 같습니다 **https:\//\<accountname >.visualstudio.com/MyFirstProject**).
2. 선택 **리포지토리를 가져올**합니다.
3. 입력 된 **복제 URL** DevTest Labs 리포지토리에 대 한: `https://github.com/Azure/azure-devtestlab`합니다.
4. **가져오기**를 선택합니다.

    ![Git 리포지토리 가져오기](./media/set-up-devops-lab/import-git-repo.png)

(이미지 팩터리 파일)을 정확 하 게 필요한에 체크인만 하려는 경우 단계에 따라 [여기](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) Git 리포지토리를 복제에 있는 파일만 푸시를 합니다 **스크립트/ImageFactory** 디렉터리입니다.

## <a name="create-a-build-and-connect-to-azure"></a>빌드를 만들고 Azure에 연결
이 시점에서 원본 파일 Azure DevOps의 Git 리포지토리에 저장 해야 합니다. 이제 Azure PowerShell을 실행 하는 파이프라인을 설정 해야 합니다. 다음이 단계를 수행 하는 옵션 많습니다. 이 문서에서는 간단히 하기 위해 빌드 정의 사용 하지만 빌드 DevOps, DevOps 릴리스 (단일 또는 여러 환경), Windows 작업 Scheduler와 같은 다른 실행 엔진 또는 Azure PowerShell을 실행할 수 있는 다른 모든 도구를 사용 하 여 작동 합니다.

> [!NOTE]
> 염두에서에 두 가지 중요 한 사항이 많은 (10 이상) 사용자 지정 이미지를 만드는 경우를 실행 하려면 시간이 오래 걸릴 PowerShell 파일 중 일부는 합니다. 사용 가능한 호스트 된 DevOps 빌드/릴리스 에이전트 30 분, 시간 제한이 없으므로 여러 이미지를 빌드하기 시작 했으면 사용 가능한 호스트 된 에이전트를 사용할 수 없습니다. 이 시간 초과 문제를 적용 하려면 어떤 도구를 사용 하려는 것이 좋습니다 일반적인 시간 제한을 장기 실행 중인 Azure PowerShell 스크립트를 확장할 수 있음을 미리 확인 합니다. Azure DevOps의 경우 유료 호스트 된 에이전트를 사용 하거나 사용자 지정 빌드 에이전트를 사용 합니다.

1. 시작 하려면 선택 합니다 **빌드 설정** DevOps 프로젝트의 홈 페이지에서:

    ![빌드 단추를 설정 합니다.](./media/set-up-devops-lab/setup-build-button.png)
2. 지정 된 **이름을** 빌드 (예를 들어: 빌드 및 DevTest Labs에 이미지 전송) 합니다.
3. 선택는 **빈** 빌드 정의 선택한 **적용** 빌드를 만들려면.
4. 이 단계에서 선택할 수 있습니다 **Hosted** 빌드 에이전트에 대 한 합니다.
5. **저장** 빌드 정의 합니다.

    ![빌드 정의](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>빌드 변수 구성
명령줄 매개 변수를 간단 하 게 하려면 빌드 변수 집합에 이미지 팩터리를 구동 하는 키 값을 캡슐화 됩니다. 선택 된 **변수** 탭을 몇 가지 기본 변수 목록이 표시 됩니다. Azure devops에 입력 하는 변수 목록에는 다음과 같습니다.


| 변수 이름 | 값 | 메모 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 이 저장소의 전체 경로 **구성** 폴더입니다. 위의 전체 리포지토리를 가져온 경우 왼쪽 값은 올바른. 그렇지 않으면 구성 위치를 가리키도록 업데이트 합니다. |
| DevTestLabName | MyImageFactory | 팩터리에서 사용 하 여 이미지를 생성 하는 Azure DevTest Labs에서 랩의 이름입니다. 사용자 계정이 없는 경우 만듭니다. 랩 서비스 끝점에 액세스할 수 있는 동일한 구독 인지 확인 합니다. |
| ImageRetention | 1 | 각 형식의 저장 하려는 이미지의 수입니다. 기본값 1로 설정 합니다. |
| MachinePassword | ******* | 가상 컴퓨터에 대 한 기본 제공 관리자 계정 암호입니다. 임시 계정입니다. 따라서 보안 인지를 확인 하세요. 보안 문자열 인지 확인 하려면 오른쪽에 작은 잠금 아이콘을 선택 합니다. |
| MachineUserName | ImageFactoryUser | 가상 컴퓨터에 대 한 기본 제공 관리자 계정 사용자 이름입니다. 이 임시 계정입니다. |
| StandardTimeoutMinutes | 30 | 에서는 일반적인 Azure 작업에 대 한 대기 해야 하는 시간 제한. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 여기서는 랩이 존재 하 고 서비스 끝점에 액세스할 수 있는 구독의 ID입니다. |
| VMSize | Standard_A3 | 에 사용할 가상 머신의 크기를 **만들기** 단계입니다. 만든 Vm는 일시적입니다. 크기는 것 이어야 합니다는 [랩에 대해 사용 하도록 설정](devtest-lab-set-lab-policy.md)합니다. 충분 한지 확인 [구독 코어 할당량](../azure-subscription-service-limits.md)합니다.

![빌드 변수](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Azure에 연결
다음 단계는 서비스 주체 설정 방법은입니다. DevOps 빌드 에이전트에서 사용자를 대신 하 여 Azure에서 작동할 수 있도록 Azure Active Directory의 id입니다. 이 설정 하려면 먼저 Azure PowerShell 빌드 단계 추가 시작 합니다.

1. 선택 **작업 추가**합니다.
2. 검색할 **Azure PowerShell**합니다.
3. 찾게 되 면 선택 **추가** 빌드 작업을 추가 합니다. 이렇게 하면 작업이 추가 왼쪽에 표시 되는 표시 됩니다.

![PowerShell 단계 설정](./media/set-up-devops-lab/set-up-powershell-step.png)

서비스 주체 설정에 대 한 가장 빠른 방법은 Azure DevOps에 수행 되도록 두는.

1. 선택 된 **태스크** 추가 했습니다.
2. 에 대 한 **Azure 연결 형식**, 선택 **Azure Resource Manager**합니다.
3. 선택 된 **관리** 서비스 주체를 설정 하는 링크입니다.

자세한 내용은 참조 [블로그 게시물](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)합니다. 선택 하는 경우는 **관리** DevOps에 적절 한 위치에 이동 링크 (블로그 게시물에 두 번째 스크린샷) Azure에 대 한 연결을 설정 하려면. 선택 해야 **Azure Resource Manager 서비스 끝점** 이를 설정 하는 경우.

## <a name="complete-the-build-task"></a>빌드 작업을 완료
빌드 작업을 선택 하면 입력 해야 하는 오른쪽 창에서 모든 세부 정보가 표시 됩니다.

1. 먼저 빌드 작업을 이름을 지정 합니다. **가상 컴퓨터를 만들**합니다.
2. 선택 된 **서비스 주체** 를 선택 하 여 만든 **Azure Resource Manager**
3. 선택 된 **서비스 끝점**합니다.
4. 에 대 한 **스크립트 경로**, 선택 **... (줄임표)**  오른쪽에 있습니다.
5. 이동할 **MakeGoldenImageVMs.ps1** 스크립트입니다.
6. 스크립트 매개 변수는 다음과 같습니다. `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![빌드 정의 완료 합니다.](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>큐 빌드
이제 모든 새 빌드를 큐에서 올바르게 설정 했는지 확인 합니다. 빌드가 실행 되는 동안 전환할 합니다 [Azure portal](https://portal.azure.com) 에서 선택한 **모든 Virtual Machines** 모두 올바르게 작동 하는지 확인 하려면 이미지 팩터리 랩에서. 세 개의 가상 머신을 랩에 생성 되 게 표시 됩니다.

![랩의 Vm](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>다음 단계
Azure DevTest Labs에 따라 이미지 팩터리를 설정 하는 첫 번째 단계는 완료 되었습니다. 시리즈의 다음 문서에서는 표시 된 Vm 일반화 및 사용자 지정 이미지에 저장 합니다. 그런 다음 해야 다른 모든 랩에 배포 합니다. 시리즈의 다음 문서를 참조 하세요. [사용자 지정 이미지를 저장 하 고 여러 개의 랩에 배포](image-factory-save-distribute-custom-images.md)합니다.
