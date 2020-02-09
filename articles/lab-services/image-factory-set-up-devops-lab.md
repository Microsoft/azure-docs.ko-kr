---
title: Azure DevTest Labs의 Azure DevOps에서 이미지 팩터리를 실행 합니다.
description: 이 문서에서는 Azure DevOps (이전의 Visual Studio Team Services)에서 이미지 팩터리를 실행 하는 데 필요한 모든 준비 사항을 설명 합니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758685"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Azure DevOps에서 이미지 팩터리 실행
이 문서에서는 Azure DevOps (이전의 Visual Studio Team Services)에서 이미지 팩터리를 실행 하는 데 필요한 모든 준비 사항을 설명 합니다.

> [!NOTE]
> 모든 오케스트레이션 엔진은 작동 합니다. Azure DevOps는 필수가 아닙니다. 이미지 팩터리는 Azure PowerShell 스크립트를 사용 하 여 실행 되므로 Windows 작업 스케줄러, 다른 CI/CD 시스템 등을 사용 하 여 수동으로 실행할 수 있습니다.

## <a name="create-a-lab-for-the-image-factory"></a>이미지 팩터리의 랩 만들기
이미지 팩터리를 설정 하는 첫 번째 단계는 Azure DevTest Labs에서 랩을 만드는 것입니다. 이 랩은 가상 머신을 만들고 사용자 지정 이미지를 저장 하는 이미지 팩터리 랩입니다. 이 랩은 전체 이미지 팩터리 프로세스의 일부로 간주 됩니다. 랩을 만든 후에는 나중에 필요 하므로 이름을 저장 해야 합니다.

## <a name="scripts-and-templates"></a>스크립트 및 템플릿
팀에 이미지 팩터리를 채택 하는 다음 단계는 사용 가능한 기능을 이해 하는 것입니다. 이미지 팩터리 스크립트 및 템플릿은 [DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)리포지토리에서 공개적으로 사용할 수 있습니다. 다음은이에 대 한 개요입니다.

- 이미지 팩터리. 루트 폴더입니다.
    - 구성 이미지 팩터리에 대 한 입력입니다.
        - GoldenImages. 이 폴더에는 사용자 지정 이미지의 정의를 나타내는 JSON 파일이 포함 되어 있습니다.
        - 실습실. json. 팀이 특정 사용자 지정 이미지를 받도록 등록 하는 파일입니다.
- 스크립트만. 이미지 팩터리의 엔진입니다.

이 섹션의 문서에서는 이러한 스크립트 및 템플릿에 대 한 자세한 정보를 제공 합니다.

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps 팀 프로젝트 만들기
Azure DevOps를 사용 하 여 소스 코드를 저장 하 고 Azure PowerShell를 한 곳에서 실행할 수 있습니다. 이미지를 최신 상태로 유지 하도록 되풀이 실행을 예약할 수 있습니다. 결과를 기록 하 여 문제를 진단할 수 있는 좋은 기능이 있습니다.  그러나 Azure DevOps를 사용 하는 것은 요구 사항이 아니지만 Azure에 연결 하 여 Azure PowerShell를 실행할 수 있는 모든 프로그램/엔진을 사용할 수 있습니다.

대신 사용 하려는 기존 DevOps 계정 또는 프로젝트가 있는 경우이 단계를 건너뜁니다.

시작 하려면 Azure DevOps에서 무료 계정을 만듭니다. https://www.visualstudio.com/ 를 방문 하 여 **Azure DevOps** (이전 VSTS)에서 **무료로 시작** 을 선택 합니다. 고유한 계정 이름을 선택 하 고 Git를 사용 하 여 코드를 관리 하도록 선택 해야 합니다. 이를 만든 후에는 팀 프로젝트에 URL을 저장 합니다. 샘플 URL은 `https://<accountname>.visualstudio.com/MyFirstProject`입니다.

## <a name="check-in-the-image-factory-to-git"></a>Git에 대 한 이미지 팩터리 체크 인
이미지 팩터리의 모든 PowerShell, 템플릿 및 구성은 [공용 DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)에 있습니다. 새 팀 프로젝트에 코드를 가져오는 가장 빠른 방법은 리포지토리를 가져오는 것입니다. 이를 통해 전체 DevTest Labs 리포지토리를 가져와서 추가 문서 및 샘플을 얻을 수 있습니다.

1. 이전 단계에서 만든 Azure DevOps 프로젝트를 방문 합니다 (URL은 **https:\//\<accountname >. visualstudio/MyFirstProject**).
2. **리포지토리 가져오기를**선택 합니다.
3. DevTest Labs 리포지토리의 **복제 URL** (`https://github.com/Azure/azure-devtestlab`)을 입력 합니다.
4. **가져오기**를 선택합니다.

    ![Git 리포지토리 가져오기](./media/set-up-devops-lab/import-git-repo.png)

필요한 것만 정확 하 게 확인 하려면 (이미지 팩터리 파일) [여기](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) 에 나오는 단계에 따라 Git 리포지토리를 복제 하 고 **Scripts/imagefactory** 디렉터리에 있는 파일만 푸시합니다.

## <a name="create-a-build-and-connect-to-azure"></a>빌드를 만들고 Azure에 연결
이 시점에서 Azure DevOps의 Git 리포지토리에 원본 파일이 저장 됩니다. 이제 Azure PowerShell를 실행 하는 파이프라인을 설정 해야 합니다. 이러한 단계를 수행 하는 다양 한 옵션이 있습니다. 이 문서에서는 간소화를 위해 빌드 정의를 사용 하지만 DevOps Build, DevOps 릴리스 (단일 또는 여러 환경), Windows 작업 스케줄러 같은 기타 실행 엔진 또는 Azure PowerShell 실행할 수 있는 다른 도구와 함께 작동 합니다.

> [!NOTE]
> 하나 이상의 사용자 지정 이미지가 생성 될 때 PowerShell 파일 중 일부를 실행 하는 데 시간이 오래 걸릴 수 있다는 점을 명심 해야 합니다. 사용 가능한 호스트 DevOps 빌드/릴리스 에이전트의 시간 제한은 30 분 이므로, 많은 이미지를 빌드하기 시작한 후에는 무료 호스트 된 에이전트를 사용할 수 없습니다. 이러한 시간 제한 문제는 사용 하려는 모든 기능에 적용 됩니다. 장기 실행 Azure PowerShell 스크립트에 대 한 일반적인 시간 제한을 확장할 수 있는지 확인 하는 것이 좋습니다. Azure DevOps의 경우 유료 호스트 된 에이전트를 사용 하거나 고유한 빌드 에이전트를 사용할 수 있습니다.

1. 시작 하려면 DevOps 프로젝트의 홈페이지에서 **빌드 설정** 을 선택 합니다.

    ![빌드 단추를 설정 합니다.](./media/set-up-devops-lab/setup-build-button.png)
2. 빌드 **이름** (예: 빌드 및 이미지를 DevTest Labs에 배달)을 지정 합니다.
3. **빈** 빌드 정의를 선택 하 고 **적용** 을 선택 하 여 빌드를 만듭니다.
4. 이 단계에서 빌드 에이전트에 대해 **호스트** 를 선택할 수 있습니다.
5. 빌드 정의를 **저장** 합니다.

    ![빌드 정의](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>빌드 변수 구성
명령줄 매개 변수를 간소화 하기 위해는 이미지 팩터리를 구동 하는 키 값을 빌드 변수 집합으로 캡슐화 합니다. **변수** 탭을 선택 하면 여러 기본 변수 목록이 표시 됩니다. Azure DevOps에 입력할 변수 목록은 다음과 같습니다.


| 변수 이름 | 값 | 메모 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | **구성** 폴더에 대 한 리포지토리의 전체 경로입니다. 위의 전체 리포지토리를 가져온 경우 왼쪽에 있는 값이 올바릅니다. 그렇지 않으면 업데이트는 구성 위치를 가리킵니다. |
| DevTestLabName | MyImageFactory | 이미지를 생성 하기 위해 팩터리에서 사용 되는 Azure DevTest Labs의 랩 이름입니다. 없는 경우 하나 만듭니다. 랩이 서비스 엔드포인트에서 액세스할 수 있는 것과 동일한 구독에 있는지 확인 합니다. |
| ImageRetention | 1 | 각 형식에 대해 저장 하려는 이미지 수입니다. 기본값을 1로 설정 합니다. |
| MachinePassword | ******* | 가상 컴퓨터에 대 한 기본 제공 관리자 계정 암호입니다. 이 계정은 임시 계정 이므로 보안을 유지 해야 합니다. 오른쪽의 작은 잠금 아이콘을 선택 하 여 보안 문자열 인지 확인 합니다. |
| MachineUserName | ImageFactoryUser | 가상 컴퓨터에 대 한 기본 제공 관리자 계정 사용자 이름입니다. 임시 계정입니다. |
| StandardTimeoutMinutes | 30 | 정기 Azure 작업에 대해 기다려야 하는 시간 제한입니다. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 랩이 존재 하 고 서비스 엔드포인트이 액세스할 수 있는 구독의 ID입니다. |
| VMSize | Standard_A3 | **만들기** 단계에 사용할 가상 머신의 크기입니다. 생성 된 Vm은 일시적입니다. 크기는 [랩에 사용할](devtest-lab-set-lab-policy.md)수 있는 크기 여야 합니다. [구독 코어 할당량이](../azure-resource-manager/management/azure-subscription-service-limits.md)충분 한지 확인 합니다.

![빌드 변수](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Azure에 연결
다음 단계는 서비스 주체를 설정 하는 것입니다. 이 id는 DevOps 빌드 에이전트가 사용자를 대신 하 여 Azure에서 작동할 수 있도록 하는 Azure Active Directory의 id입니다. 설정 하려면 먼저 Azure PowerShell 빌드 단계를 추가 하 여 시작 합니다.

1. **작업 추가**를 선택 합니다.
2. **Azure PowerShell**를 검색 합니다.
3. 찾은 후 **추가** 를 선택 하 여 작업을 빌드에 추가 합니다. 이렇게 하면 작업이 왼쪽에 추가 된 것 처럼 보입니다.

![PowerShell 단계 설정](./media/set-up-devops-lab/set-up-powershell-step.png)

서비스 주체를 설정 하는 가장 빠른 방법은 Azure DevOps에서이 작업을 수행할 수 있도록 하는 것입니다.

1. 방금 추가한 **작업** 을 선택 합니다.
2. **Azure 연결 형식**에 대해 **Azure Resource Manager**를 선택 합니다.
3. **관리** 링크를 선택 하 여 서비스 주체를 설정 합니다.

자세한 내용은이 [블로그 게시물](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)을 참조 하세요. **관리** 링크를 선택 하면 Azure에 대 한 연결을 설정 하는 devops (블로그 게시물의 두 번째 스크린샷)의 올바른 위치가 됩니다. 설정 하는 경우 **서비스 엔드포인트 Azure Resource Manager** 선택 해야 합니다.

## <a name="complete-the-build-task"></a>빌드 작업 완료
빌드 작업을 선택 하면 오른쪽 창에 입력 해야 하는 모든 세부 정보가 표시 됩니다.

1. 먼저 빌드 작업의 이름을 **Virtual Machines 만듭니다**.
2. **Azure Resource Manager** 를 선택 하 여 만든 **서비스 주체** 를 선택 합니다.
3. **서비스 엔드포인트**을 선택 합니다.
4. **스크립트 경로**에 **대해 ...를 선택 합니다. (줄임표)** 오른쪽에 있습니다.
5. **MakeGoldenImageVMs** 스크립트로 이동 합니다.
6. 스크립트 매개 변수는 다음과 같습니다. `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![빌드 정의 완료](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>빌드 큐 대기
새 빌드를 큐에 대기 시켜 모든 것이 올바르게 설정 되었는지 확인 합니다. 빌드를 실행 하는 동안 [Azure Portal](https://portal.azure.com) 로 전환 하 고 이미지 팩터리 랩에서 **모든 Virtual Machines** 를 선택 하 여 모든 것이 제대로 작동 하는지 확인 합니다. 랩에서 생성 된 세 개의 가상 머신이 표시 됩니다.

![랩의 Vm](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>다음 단계
Azure DevTest Labs 기반으로 이미지 팩터리를 설정 하는 첫 번째 단계는 완료입니다. 시리즈의 다음 문서에서는 이러한 Vm을 일반화 하 고 사용자 지정 이미지에 저장 합니다. 그런 다음 다른 모든 랩에 배포 합니다. 이 시리즈의 다음 문서를 참조 하세요. [사용자 지정 이미지를 저장 하 고 여러 랩에 배포](image-factory-save-distribute-custom-images.md)합니다.
