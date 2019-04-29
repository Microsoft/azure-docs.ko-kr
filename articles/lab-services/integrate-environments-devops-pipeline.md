---
title: Azure DevTest Labs에서 Azure 파이프라인 환경을 통합할 | Microsoft Docs
description: Azure DevOps 연속 통합 (CI) 및 CD (지속적인 업데이트) 파이프라인에 Azure DevTest Labs 환경을 통합 하는 방법에 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318392"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>환경에 Azure DevOps CI/CD 파이프라인 통합
Azure DevOps 서비스 (이전의 Visual Studio Team Services)를 쉽게 연속 통합 (CI)를 통합 설치 된 Azure DevTest Labs 작업 확장을 사용 하면 Azure로 지속적인 업데이트 (CD) 빌드 및 릴리스 파이프라인 / DevTest Labs 합니다. 이와 같이이 확장을 사용 하면 신속 하 게 배포 하기 쉬운를 [환경](devtest-lab-test-env.md) 특정 작업을 테스트 하 고 테스트가 완료 되 면 삭제 됩니다. 

이 문서 만들기 환경에 배포 하 고 하나의 완전 한 파이프라인에서 모든 환경을 삭제 하는 방법을 보여 줍니다. 일반적으로 수행 각이 태스크 마다 개별적으로 사용자 고유의 사용자 지정 빌드-테스트-배포 파이프라인에서. 이 문서에서 사용 되는 확장명은 이러한 외에도 [DTL VM 작업 만들기/삭제](devtest-lab-integrate-ci-cd-vsts.md):

- 환경 만들기
- 환경 삭제

## <a name="before-you-begin"></a>시작하기 전에
Azure DevTest Labs를 사용 하 여 CI/CD 파이프라인에 통합 하려면, 먼저 설치할 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) Visual Studio Marketplace에서 확장 합니다. 

## <a name="create-and-configure-the-lab-for-environments"></a>만들기 및 환경에 대 한 랩 구성
이 섹션에서는 만들 랩 Azure 환경에 배포 될 위치를 구성 하는 방법을 설명 합니다.

1. [랩 만들기](devtest-lab-create-lab.md) 아직 없는 경우. 
2. 랩을 구성 하 고이 문서에서 지침에 따라 환경 템플릿을 만듭니다. [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)합니다.
3. 이 예제에서는 기존 Azure 빠른 시작 템플릿을 사용 하 여 [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)합니다.
4. 복사 합니다 **201-web-app-redis-cache-sql-database** 폴더를 **ArmTemplate** 2 단계에서 구성 된 리포지토리에서 폴더입니다.

## <a name="create-a-release-definition"></a>릴리스 정의 만들기
릴리스 정의를 만들려면 다음을 수행합니다.

1.  에 **릴리스** 탭을 **빌드 및 릴리스 허브**를 선택 합니다 **더하기 기호 (+)** 단추.
2.  **릴리스 정의 만들기** 창에서 **빈** 템플릿을 선택한 후, **다음**을 선택합니다.
3.  **나중에 선택**을 선택한 후 **만들기**를 선택하여 하나의 기본 환경이 있고 연결된 아티팩트가 없는 새 릴리스 정의를 만듭니다.
4.  새 릴리스 정의에서 바로 가기 메뉴를 열려면 선택 된 **줄임표 (...)**  옆에 환경 이름을 선택한 후 **변수를 구성**합니다.
5.  **구성 - 환경** 창에서 릴리스 정의 작업에 사용하는 변수에 다음 값을 입력합니다.
1.  에 대 한 **administratorLogin**를 SQL 관리자 로그인 이름을 입력 합니다.
2.  에 대 한 **administratorLoginPassword**를 SQL 관리자 로그인에 사용할 암호를 입력 합니다. 암호를 숨기고 보호하려면 “자물쇠” 아이콘을 사용합니다.
3.  에 대 한 **databaseName**, SQL Database 이름을 입력 합니다.
4.  이러한 변수는 특정 예제에서는 환경에 대 한, 다양 한 환경에는 다른 변수가 있을 수 있습니다.

## <a name="create-an-environment"></a>환경 만들기
배포의 다음 단계는 개발 또는 테스트 용도로 사용할 환경을 만드는 것입니다.

1. 릴리스 정의에서 **작업 추가**를 선택합니다.
2. 에 **작업** 탭에서 Azure DevTest Lab 만들기 환경 작업을 추가 합니다. 아래와 같이 작업을 구성합니다.
    1. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
2. 에 대 한 **랩 이름**, 앞서 만든 인스턴스의 이름을 선택 * 합니다.
3. 에 대 한 **리포지토리 이름**, 여기서 Resource Manager 템플릿 (201) 푸시된 리포지토리 선택 * 합니다.
4. 에 대 한 **템플릿 이름**, 프로그램 소스 코드 리포지토리 * 저장 환경의 이름을 선택 합니다. 
5. 합니다 **랩 이름**를 **리포지토리 이름**, 및 **템플릿 이름** Azure 리소스 Id의 친숙 한 표시 합니다. 이름을 수동으로 입력 오류를 발생 시킬을 드롭 다운 목록을 사용 하 여 정보를 선택 합니다.
6. 에 대 한 **환경 이름**, 랩 내에서 환경 인스턴스를 고유 하 게 식별 하는 이름을 입력 합니다.  랩 내에서 고유 해야 합니다.
7. 합니다 **매개 변수 파일** 하며 **매개 변수**, 환경에 전달할 사용자 지정 매개 변수를 허용 합니다. 하나 또는 둘 다 매개 변수 값을 설정할 수 있습니다. 예를 들어 매개 변수 섹션 사용 됩니다. 예를 들어 환경에 정의 된 변수의 이름을 사용 합니다. `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. 템플릿의 출력 섹션에서 환경 템플릿 내에서 정보를 통해 전달할 수 있습니다. 확인할 **환경 템플릿 출력을 기반으로 하는 Create 출력 변수** 태스크에서 데이터를 사용할 수 있도록 합니다. `$(Reference name.Output Name)` 패턴에 따라입니다. 예를 들어, 참조 이름 DTL 이며 템플릿에서 출력 이름을 위치 하는 경우 변수 것 `$(DTL.location)`입니다.

## <a name="delete-the-environment"></a>환경 삭제
최종 단계에 Azure DevTest Labs 인스턴스에 배포 된 환경을 삭제 하는 것입니다. 개발 작업을 실행 하거나 배포 된 리소스에서 필요한 테스트를 실행 한 후에 일반적으로 환경을 삭제 됩니다.

릴리스 정의에서 선택 **작업 추가**, 한 후 합니다 **배포** 탭에서 추가 **Azure DevTest Labs 삭제 환경** 작업 합니다. 다음과 같이 구성합니다.

1. VM을 삭제 하려면 참조 [Azure DevTest Labs 작업](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. **Azure RM 구독**의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
    2. 에 대 한 **랩 이름**, 랩 환경 존재 하는 위치를 선택 합니다.
    3. 에 대 한 **환경 이름**, 제거할 환경의 이름을 입력 합니다.
2. 릴리스 정의 대한 이름을 입력한 다음, 저장합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [Resource Manager 템플릿으로 다중 VM 환경 만들기](devtest-lab-create-environment-from-arm.md)합니다.
- DevTest Labs 자동화를 위한 빠른 시작 Resource Manager 템플릿을 합니다 [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)합니다.
- [VSTS 문제 해결 페이지](/azure/devops/pipelines/troubleshooting)

