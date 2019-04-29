---
title: Azure DevOps Projects - Azure IoT Edge를 사용한 CI/CD 파이프라인 | Microsoft Docs
description: Azure DevOps Projects를 사용하면 Azure를 쉽게 시작할 수 있습니다. 몇 단계로 원하는 Azure IoT Edge 앱을 시작할 수 있습니다.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 02977032c5975de4098600ddbebccfcbb9b0fafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595321"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Azure DevOps Projects(미리 보기)를 사용하여 IoT Edge에 대한 CI/CD 파이프라인 만들기

DevOps 프로젝트를 사용하여 IoT Edge 애플리케이션에 대한 지속적인 통합(CI) 및 지속적인 업데이트(CD)를 구성합니다. DevOps Projects는 Azure Pipelines에서 빌드 및 릴리스 파이프라인의 초기 구성을 간소화합니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만드세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

DevOps Projects는 Azure DevOps에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 DevOps Projects는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 **리소스 만들기**를 선택하고 **DevOps Projects**를 검색합니다.  

1.  **만들기**를 선택합니다.

## <a name="create-a-new-application-pipeline"></a>새 애플리케이션 파이프라인 만들기 

1. Azure IoT Edge 모듈은[C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) 및 [Java](tutorial-java-module.md)로 작성할 수 있습니다. **.NET**, **Node.js**, **Python**, **C** 또는 **Java** 중에 원하는 언어를 선택하여 새 애플리케이션을 시작합니다. **다음**을 선택하여 계속합니다.

   ![언어를 선택하여 새 애플리케이션을 만듭니다.](./media/how-to-devops-project/select-language.png)

2. 애플리케이션 프레임워크로 **단순 IoT(미리 보기)** 를 선택하고, **다음**을 선택합니다.

   ![Simple IoT 프레임 워크 선택](media/how-to-devops-project/select-iot.png)

3. 애플리케이션을 배포하는 Azure 서비스로 **IoT Edge**를 선택하고, **다음**을 선택합니다.

   ![IoT Edge 서비스 선택](media/how-to-devops-project/select-iot-edge.png)

4. 새 무료 Azure DevOps 조직을 만들거나 기존 조직을 선택합니다.

   1. 프로젝트 이름을 입력합니다. 

   2. Azure DevOps 조직을 선택합니다. 기존 조직이 없는 경우 **추가 설정**을 선택하여 새로 만듭니다. 

   3. Azure 구독을 선택합니다.

   4. 프로젝트 이름으로 생성된 IoT Hub를 사용하거나 직접 이름을 입력합니다.

   5. **추가 설정**을 선택하여 DevOps Projects에서 자동으로 만드는 Azure 리소스를 구성합니다.

   6. **완료**를 선택하여 프로젝트 만들기를 마칩니다. 

   ![애플리케이션 이름 지정 및 만들기](media/how-to-devops-project/select-devops.png)

잠시 후에 DevOps Projects 대시보드가 Azure Portal에 표시됩니다. 진행률을 보려면 프로젝트 이름을 선택합니다. 페이지를 새로 고쳐야 할 수도 있습니다. 샘플 IoT Edge 애플리케이션이 Azure DevOps 조직의 리포지토리에서 설정되고, 빌드가 실행되고, 애플리케이션이 IoT Edge 디바이스에 배포됩니다. 이 대시보드에서는 코드 리포지토리, CI/CD 파이프라인 및 Azure의 애플리케이션에 가시성을 제공합니다.

   ![DevOps 포털에서 애플리케이션 보기](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>코드 변경 내용 커밋 및 CI/CD 실행

DevOps Projects에서 Azure Repos에 프로젝트의 Git 리포지토리를 만들었습니다. 이 섹션에서는 리포지토리를 살펴보고 애플리케이션의 코드를 변경합니다.

1. 프로젝트에 대해 생성된 리포지토리로 이동하려면 프로젝트 대시보드 메뉴에서 **리포지토리**를 선택합니다.  

   ![Azure Repos에서 생성된 리포지토리 보기](./media/how-to-devops-project/view-repositories.png)

2. 다음은 웹 브라우저를 사용하여 코드를 변경하는 단계입니다. 리포지토리를 로컬로 복제하려면 창의 오른쪽 위에서 **복제**를 선택합니다. 제공된 URL을 사용하여 Visual Studio Code 또는 원하는 개발 도구에 Git 리포지토리를 복제합니다. 

3. 리포지토리에는 사용자가 생성 프로세스에서 선택한 애플리케이션 언어를 기반으로 하는 **SampleModule**이라는 모듈의 코드가 이미 포함되어 있습니다. **modules/SampleModule/module.json** 파일을 엽니다.

   ![Azure Repos에서 module.json 파일 열기](./media/how-to-devops-project/open-module-json.png)

4. **편집**을 선택한 다음 `"tag"` 아래에 있는 `"version"`을 변경합니다. 예를 들어 `"version": "${BUILD_BUILDID}"`으로 업데이트하여 Azure IoT Edge 모듈 이미지 태그의 일부로 [Azure DevOps 빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables)를 사용할 수 있습니다.

   ![빌드 변수를 수락하도록 버전 편집](media/how-to-devops-project/update-module-json.png)

5. **커밋**을 선택하고 변경 내용을 저장합니다.

6. 브라우저에서 Azure Portal의 DevOps Projects 대시보드로 돌아갑니다. 이제 빌드가 진행되고 있음을 확인해야 합니다. 변경한 내용은 자동으로 빌드되며 CI/CD 파이프라인을 통해 배포됩니다.

    ![진행 중인 상태 보기](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>CD 파이프라인 검토

이전 섹션에서는 Azure DevOps Projects가 IoT Edge 애플리케이션에 대한 전체 CI/CD 파이프라인을 자동으로 구성했습니다. 그런 다음, 파일 중 하나에 변경 내용을 커밋하여 해당 빌드 파이프라인을 테스트했습니다. 이제 필요에 따라 파이프라인을 살펴보고 사용자 지정합니다. Azure DevOps 빌드 및 릴리스 파이프라인을 숙지하려면 다음을 수행합니다.

1. DevOps 프로젝트에서 빌드 파이프라인을 보려면 프로젝트 대시보드 메뉴에서 **빌드 파이프라인**을 선택합니다. 이 링크는 브라우저 탭 및 새 프로젝트에 대한 Azure DevOps 빌드 파이프라인을 엽니다.

   ![Azure Pipelines에서 빌드 파이프라인 보기](./media/how-to-devops-project/view-build-pipelines.png)

2. **편집**을 선택합니다.

    ![빌드 파이프라인 편집](media/how-to-devops-project/click-edit-button.png)

3. 열리는 패널에서 빌드 파이프라인이 실행될 때 발생하는 작업을 검사할 수 있습니다. 빌드 파이프라인은 Git 리포지토리에서 원본 가져오기, IoT Edge 모듈 이미지 빌드, 컨테이너 레지스트리에 IoT Edge 푸시, 배포에서 사용되는 출력 게시 등의 다양한 작업을 수행합니다. Azure DevOps의 Azure IoT Edge 작업에 대한 자세한 내용은 [지속적인 통합을 위한 Azure Pipelines 구성](how-to-ci-cd.md#configure-continuous-integration)을 참조하세요.

4. 파이프라인 세부 정보를 열려면 빌드 파이프라인의 맨 위에서 **파이프라인** 헤더를 선택합니다. 빌드 파이프라인의 이름을 좀 더 구체적인 이름으로 변경합니다.

   ![파이프라인 세부 정보 편집](./media/how-to-devops-project/edit-build-pipeline.png)

5. **저장 및 큐**를 선택한 다음, **저장**을 선택합니다.

6. 빌드 파이프라인에 대한 메뉴에서 **트리거**를 선택합니다. DevOps Projects는 CI 트리거를 자동으로 생성하며, 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다.  필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

7. **보존**을 선택합니다. 시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

8. **기록**을 선택합니다. [기록] 창에는 최근 빌드 변경 내용의 감사 내역이 포함되어 있습니다. Azure Pipelines는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

9. 빌드 파이프라인을 모두 살펴본 후에는 해당 릴리스 파이프라인으로 이동합니다. **파이프라인** 아래에서 **릴리스**를 선택한 다음, **편집**을 선택하여 파이프라인 세부 정보를 봅니다.

    ![릴리스 파이프라인 보기](media/how-to-devops-project/release-pipeline.png)

10. **아티팩트** 아래에서 **드롭**을 선택합니다. 이 아티팩트가 감시하는 소스는 이전 단계에서 검사한 빌드 파이프라인의 출력입니다. 

11. **드롭** 아이콘 옆에서 번개처럼 생긴 **지속적인 배포 트리거**를 선택합니다. 이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 트리거를 사용하도록 설정했습니다. 필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다.  

12. 릴리스 파이프라인에 대한 메뉴에서 **작업**을 선택한 다음, 드롭다운 목록에서 **개발** 단계를 선택합니다. DevOps Projects에서는 IoT Hub를 만들고, 해당 허브에서 IoT Edge 디바이스를 만들고, 빌드 파이프라인의 샘플 모듈을 배포하고, IoT Edge 디바이스로 실행될 가상 머신을 프로비전하는 릴리스 단계를 만들었습니다. CD에 대한 Azure IoT Edge 작업의 자세한 내용은 [지속적인 배포를 위한 Azure Pipelines 구성](how-to-ci-cd.md#configure-continuous-deployment)을 참조하세요.

    ![지속적인 배포 작업 보기](media/how-to-devops-project/dev-release.png)

13. 오른쪽에서 **릴리스 보기**를 선택합니다. 이 보기에는 릴리스의 기록이 표시됩니다.

14. 자세한 정보를 보려면 릴리스 이름을 선택합니다.


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 경우 만든 Azure App Service 및 기타 관련 리소스를 삭제할 수 있습니다. DevOps Projects 대시보드에서 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계
* [Azure IoT Edge에 대한 지속적인 통합 및 지속적인 배포](how-to-ci-cd.md)에서 Azure DevOps에 대한 Azure IoT Edge 작업에 대해 배우기
* [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-monitor.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.
