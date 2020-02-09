---
title: Azure DevTest Labs |에서 이미지 팩터리 만들기 Microsoft Docs
description: 이 문서에서는 Git 리포지토리에서 사용할 수 있는 샘플 스크립트 (Azure DevTest Labs)를 사용 하 여 사용자 지정 이미지 팩터리를 설정 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759451"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Azure DevTest Labs에서 사용자 지정 이미지 팩터리 만들기
이 문서에서는 [Git 리포지토리에서](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)사용할 수 있는 샘플 스크립트를 사용 하 여 사용자 지정 이미지 팩터리를 설정 하는 방법을 보여 줍니다.

## <a name="whats-an-image-factory"></a>이미지 팩터리 란?
이미지 팩터리는 원하는 모든 구성을 사용 하 여 정기적으로 이미지를 자동으로 빌드 및 배포 하는 코드의 구성 솔루션입니다. 이미지 팩터리의 이미지는 항상 최신 상태를 유지 하며, 전체 프로세스가 자동화 된 후에는 지속적인 유지 관리가 거의 0입니다. 필요한 모든 구성이 이미 이미지에 있기 때문에 기본 OS를 사용 하 여 VM을 만든 후 시스템을 수동으로 구성 하는 시간을 절약할 수 있습니다.

DevTest Labs에서 개발자 데스크톱을 준비 상태로 가져오는 중요 한 액셀러레이터는 사용자 지정 이미지를 사용 하는 것입니다. 사용자 지정 이미지의 단점은 랩에서 유지 관리할 추가 사항이 있다는 것입니다. 예를 들어 평가판 제품의 평가판 버전은 시간이 지남에 따라 만료 되거나 새로 릴리스된 보안 업데이트가 적용 되지 않으므로 정기적으로 사용자 지정 이미지를 새로 고쳐야 합니다. 이미지 팩터리를 사용 하면 소스 코드 제어에 체크 인 된 이미지를 정의 하 고 정의에 따라 사용자 지정 이미지를 생성 하는 자동화 된 프로세스를 사용할 수 있습니다.

이 솔루션을 사용 하면 사용자 지정 이미지에서 가상 컴퓨터를 만드는 속도가 향상 되 고 지속적인 유지 관리 비용이 추가로 제거 됩니다. 이 솔루션을 사용 하면 사용자 지정 이미지를 자동으로 만들고, 다른 DevTest Labs에 배포 하 고, 이전 이미지를 사용 중지할 수 있습니다. 다음 비디오에서는 이미지 팩터리에서 DevTest Labs를 사용 하 여 구현 하는 방법에 대해 알아봅니다.  모든 Azure Powershell 스크립트는 [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)에서 무료로 사용할 수 있으며 여기에 있습니다.

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>솔루션에 대 한 개략적인 보기
이 솔루션을 사용 하면 사용자 지정 이미지에서 가상 컴퓨터를 만드는 속도가 향상 되 고 지속적인 유지 관리 비용이 추가로 제거 됩니다. 이 솔루션을 사용 하면 사용자 지정 이미지를 자동으로 만들고 다른 DevTest Labs에 배포할 수 있습니다. DevTest Labs의 모든 작업을 자동화 하기 위한 오케스트레이션 엔진으로 Azure DevOps (이전의 Visual Studio Team Services)를 사용 합니다.

![솔루션에 대 한 개략적인 보기](./media/create-image-factory/high-level-view-of-solution.png)

이러한 개별 단계를 실행할 수 있도록 하는 [DevTest Labs 용 VSTS 확장이](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 있습니다.

- 사용자 지정 이미지 만들기
- VM 만들기
- VM 삭제
- 환경 만들기
- 환경 삭제
- 환경 채우기

DevTest Labs 확장을 사용 하면 DevTest Labs에서 사용자 지정 이미지를 자동으로 만드는 작업을 쉽게 시작할 수 있습니다.

더 복잡 한 시나리오의 경우 PowerShell 스크립트를 사용 하는 대체 구현이 있습니다. PowerShell을 사용 하 여 지속적인 통합 및 지속적인 업데이트 (CI/CD) 도구 체인에 사용할 수 있는 DevTest Labs 기반 이미지 팩터리를 완전히 자동화할 수 있습니다. 이 대체 솔루션에서 따르는 원칙은 다음과 같습니다.

- 일반적인 업데이트는 이미지 팩터리를 변경할 필요가 없습니다. (예를 들어 새 형식의 사용자 지정 이미지를 추가 하 고, 이전 이미지를 자동으로 사용 중지 하 고, 새 ' 엔드포인트 ' DevTest Labs를 추가 하 여 사용자 지정 이미지를 수신 하는 등)
- 일반적인 변경 내용은 소스 코드 제어 (인프라 코드)에서 지원 됩니다.
- 사용자 지정 이미지를 수신 하는 DevTest Labs가 동일한 Azure 구독 (랩 범위 구독)에 없을 수 있습니다.
- 필요에 따라 추가 팩터리를 실행 하려면 PowerShell 스크립트를 다시 사용할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계
[Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md) 항목의 다음 문서로 이동 합니다.
