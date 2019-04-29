---
title: Azure DevTest Labs에서 이미지 팩터리 만들기 | Microsoft Docs
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
ms.openlocfilehash: 48412b3006a462fcc9c77219f42fb41d08f2df61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622584"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Azure DevTest Labs에서 사용자 지정 이미지 팩터리를 만듭니다.
이 문서에서는, 보존 정책을 설정 하 고, 팩토리를 정리 하 고, 조직에서 모든 다른 DevTest Labs에서 이전 이미지를 사용 중지에 대해 설명 합니다. 

## <a name="prerequisites"></a>필수 조건
계속 하기 전에 다음이 문서를 수행 해야 합니다.

- [이미지 팩터리 만들기](image-factory-create.md)
- [Azure DevOps에서 이미지 팩터리를 실행 합니다.](image-factory-set-up-devops-lab.md)
- [사용자 지정 이미지를 저장 하 고 여러 개의 랩에 배포](image-factory-save-distribute-custom-images.md)

다음 항목이 위치에 이미 있어야 합니다.

- Azure DevTest Labs에서 이미지 팩터리에 대 한 랩
- 하나 이상의 대상 Azure DevTest Labs 팩터리는 골든 이미지를 배포 하는 위치
- Azure DevOps 프로젝트 이미지 팩터리를 자동화 하는 데 사용 합니다.
- 스크립트 및 구성 (이 예를 들어 위의 동일한 DevOps 프로젝트를) 포함 된 소스 코드 위치
- Azure Powershell 작업을 오케스트레이션 하는 빌드 정의
 
## <a name="setting-the-retention-policy"></a>보존 정책 설정
정리 단계를 구성 하기 전에 DevTest Labs에서 유지 하려는 기록 이미지 수를 정의 합니다. 수행한 경우는 [Azure DevOps에서 이미지 팩터리를 실행](image-factory-set-up-devops-lab.md) 구성한 문서에서는 다양 한 빌드 변수입니다. 그 중 하나 였습니다 **ImageRetention**합니다. 이 변수를 설정 `1`, 즉, DevTest Labs 사용자 지정 이미지의 기록을 유지 하지 않을 예정입니다. 최신 분산된 이미지만 사용할 수 있습니다. 이 변수를 변경 하는 경우 `2`, 최신 이미지를 배포 및 이전 유지 됩니다. DevTest Labs의 유지 관리 하려면 이전 이미지의 수를 정의 하려면이 값을 설정할 수 있습니다.

## <a name="cleaning-up-the-factory"></a>팩터리 정리
팩터리를 정리 하는 첫 번째 단계 이미지 공장에서 골든 이미지 Vm을 제거 하는 것입니다. 이전 스크립트와 마찬가지로이 작업을 수행 하는 스크립트를 있습니다. 첫 번째 단계는 다른 항목 추가 **Azure Powershell** 다음 그림과에서 같이 빌드 정의에 작업:

![PowerShell 단계](./media/set-retention-policy-cleanup/powershell-step.png)

목록에서 새 작업을 만든 후 항목을 선택 하 고 다음 그림에 나와 있는 것 처럼 모든 세부 정보를 입력:

![이전 이미지 PowerShell 작업 정리](./media/set-retention-policy-cleanup/configure-powershell-task.png)

스크립트 매개 변수는: `-DevTestLabName $(devTestLabName)`합니다.

## <a name="retire-old-images"></a>이전 이미지를 사용 중지 
이 작업에만 기록 일치 하는 유지 하는 모든 이전 이미지를 제거 합니다 **ImageRetention** 변수를 작성 합니다. 추가 **Azure Powershell** 빌드는 빌드 정의에 작업입니다. 추가 되 면 작업을 선택 하 고 다음 이미지와 같이 세부 정보를 입력 합니다. 

![이전 이미지 PowerShell 태스크를 사용 중지](./media/set-retention-policy-cleanup/retire-old-image-task.png)

스크립트 매개 변수는 다음과 같습니다. `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>큐 빌드
빌드 정의 완료 했으므로 모든 항목이 작동 하는지 확인 하려면 새 빌드를 큐 대기 합니다. 빌드 대상 환경에서 새 사용자 지정 이미지 표시를 성공적으로 완료 하 고 프로 비전 된 Vm이 없는 경우 이미지 팩터리 lab을 선택 하면 표시 합니다. 또한 이후 빌드를 대기열에서 사용이 중지 이전 사용자 지정 이미지 아웃 DevTest Labs에 따라 빌드 변수에 설정 된 보존 값을 정리 작업이 표시 됩니다.

> [!NOTE]
> 시리즈의 마지막 문서 끝에 빌드 파이프라인을 실행 했으면, 새 빌드를 큐 대기 하기 전에 이미지 팩터리 랩에서 만들어진 가상 컴퓨터를 수동으로 삭제 합니다.  수동 정리 단계는 모든 항목을 설정 하 고 작동 하는지 확인 하는 동안에 필요 합니다.



## <a name="summary"></a>요약
이제 실행 중인 이미지 팩터리를 생성 하 고 주문형 랩에 사용자 지정 이미지를 배포할 수 있습니다. 이 지점, 해당 이미지를 가져오는 단순히 적절 하 게 설정 및 대상 실습을 식별 합니다. 이전 문서에서 설명 했 듯이 합니다 **Labs.json** 에 있는 파일에 **구성** 폴더 지정는 이미지는 각 대상 실습에서 사용할 수 있어야 합니다. 조직에 다른 DevTest Labs를 추가 하면 간단히 새 랩 Labs.json에 항목을 추가 해야 합니다.

팩터리에 새 이미지를 추가 하는 작업도 간단 합니다. 열면 팩터리의 새 이미지를 포함 하려는 경우는 [Azure portal](https://portal.azure.com), DevTest Labs 팩터리로 이동, VM을 추가 하려면 단추를 선택 및 아티팩트 확인 하 고 원하는 marketplace 이미지를 선택 합니다. 선택 하는 대신 합니다 **만들기** 단추를 새 VM을 선택 합니다 **Azure Resource Manager 템플릿 보기**"템플릿을 내 어딘가에.json 파일로 저장 하 고는 **GoldenImages** 리포지토리에서 폴더입니다. 이미지 팩터리에 실행 하면 다음에 사용자 지정 이미지를 만듭니다.


## <a name="next-steps"></a>다음 단계
1. [빌드/릴리스가 예약](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) 이미지 팩터리를 주기적으로 실행 되도록 합니다. 정기적으로 팩터리 생성 이미지를 새로 고칩니다.
2. 팩터리에 대 한 자세한 골든 이미지를 확인 합니다. 사용할 수도 있습니다 [아티팩트를 만드는](devtest-lab-artifact-author.md) 추가 가지 VM 설치 태스크 스크립트 및 출하 시 이미지에서 아티팩트를 포함 합니다.
4. 만들기를 [빌드/릴리스 분리](/azure/devops/pipelines/overview?view=azure-devops-2019) 실행 하는 **DistributeImages** 개별적으로 스크립트. Labs.json 변경 하 고 다시 모든 이미지를 다시 만들 필요 없이 대상 labs에 복사 하는 이미지를 가져올 때이 스크립트를 실행할 수 있습니다.

