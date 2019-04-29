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
ms.openlocfilehash: cf1bb31614c04d6073bc40c510fc43b2f8e4e189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622643"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Azure DevTest Labs에서 사용자 지정 이미지 팩터리를 만듭니다.
이 문서에서 제공 되는 샘플 스크립트를 사용 하 여 사용자 지정 이미지 팩터리를 설정 하는 방법을 보여 줍니다.는 [Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)합니다.

## <a name="whats-an-image-factory"></a>이미지 팩터리 란?
이미지 팩터리는 빌드하고 원하는 모든 구성을 사용 하 여 정기적으로 자동으로 이미지를 배포 하는 코드와 구성 솔루션입니다. 이미지 팩터리 이미지는 항상 최신 이며 지속적인 유지 관리 거의 0 전체 프로세스가 자동화 되 면 합니다. 및 이미지에 필요한 모든 구성이 이미 이기 때문에 시간을 저장 합니다 기본 OS를 사용 하 여 VM을 만든 후 시스템을 수동으로 구성 합니다.

DevTest Labs에서 준비 상태가 개발자 데스크톱을 가져오려면 상당한 가속기는 사용자 지정 이미지를 사용 합니다. 사용자 지정 이미지의 단점은 점 랩을 유지 하기 위해 추가 하는 것입니다. 예를 들어, 시간이 지남에 따라 제품의 평가판이 만료 (또는) 사용자 지정 이미지를 주기적으로 새로 고침을 강제 적용 하는 새로 출시 된 보안 업데이트 적용 되지 않습니다. 이미지 팩터리를 사용 하 여 정의 기반으로 하는 사용자 지정 이미지를 생성 하는 자동화 된 프로세스를 소스 코드 제어 체크 인 이미지에 대 한 정의 해야 합니다.

솔루션 속도를 추가 유지 관리 비용을 제거 하는 동안 사용자 지정 이미지에서 가상 컴퓨터를 만들 수 있습니다. 이 솔루션을 사용 하 여 자동으로 사용자 지정 이미지 만들기, 다른 DevTest Labs를 보여주고자 하 수 이전 이미지를 사용 중지 합니다. 다음 비디오에서는 이미지 팩터리 및 DevTest Labs를 사용 하 여 구현 하는 방법을 배웁니다.  모든 Azure Powershell 스크립트를 자유롭게 사용 가능 하 고 있는 여기: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory)합니다.

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>솔루션의 상위 수준 보기
솔루션 속도를 추가 유지 관리 비용을 제거 하는 동안 사용자 지정 이미지에서 가상 컴퓨터를 만들 수 있습니다. 이 솔루션을 사용 하 여 자동으로 사용자 지정 이미지 만들기 하 수 다른 DevTest Labs에 게 배포 합니다. Azure DevOps (이전의 Visual Studio Team Services)를 사용 하 여 DevTest 랩에서 작업 모든 자동화에 대 한 오케스트레이션 엔진으로.

![솔루션의 상위 수준 보기](./media/create-image-factory/high-level-view-of-solution.png)

[DevTest Labs에 대 한 VSTS 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 개별 단계를 실행할 수 있도록 합니다.

- 사용자 지정 이미지 만들기
- VM 만들기
- VM 삭제
- 환경 만들기
- 환경 삭제
- 환경 채우기

DevTest Labs 확장을 사용 하 여 DevTest Labs에서 사용자 지정 이미지를 자동으로 만들기를 시작 하기 쉬운 경우

PowerShell 스크립트를 사용 하 여 복잡 한 시나리오에 대 한 대체 구현 방법이 있습니다. PowerShell을 사용 하 여 연속 통합 및 지속적인 업데이트 (CI/CD) 도구 체인에서 사용할 수 있는 DevTest Labs에 따라 이미지 팩터리를 완전히 자동화할 수 있습니다. 이 대체 솔루션의 원칙은 다음과 같습니다.

- 일반적인 업데이트 이미지 팩터리를 변경 하지 않고 해야 합니다. (예를 들어, 사용자 지정 이미지를 자동으로 이전 이미지를 사용 중지의 새 형식을 추가 끝점을 추가할 새 '' DevTest Labs 사용자 지정 이미지를 받고 등.)
- 일반적인 변경 내용은 원본 코드 제어 (코드로 서의 인프라)를 지 원하는
- DevTest Labs 사용자 지정 이미지를 수신 (labs 구독에 걸쳐) 하는 동일한 Azure 구독 수 없습니다.
- 필요에 따라에서는 추가 팩터리 실행 수 있도록 PowerShell 스크립트를 다시 사용할 수 있는 여야 합니다.

## <a name="next-steps"></a>다음 단계
이 섹션의 다음 문서로 이동 합니다. [Azure DevOps에서 이미지 팩터리를 실행 합니다.](image-factory-set-up-devops-lab.md)
