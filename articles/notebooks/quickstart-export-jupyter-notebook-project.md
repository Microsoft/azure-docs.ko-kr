---
title: Azure Notebooks 미리 보기에서 Jupyter Notebook 프로젝트 내보내기
description: Jupyter Notebook 프로젝트를 빠르게 내보냅니다.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: a2e76f2c1956df4d0de59cfb429dc1c9dce85a62
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892102"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>빠른 시작: Azure Notebooks 미리 보기에서 Jupyter Notebook 프로젝트 내보내기

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

이 빠른 시작에서는 다른 Jupyter Notebook 솔루션에 사용할 Azure Notebooks 프로젝트를 다운로드합니다. 

## <a name="prerequisites"></a>필수 구성 요소

기존 Azure Notebooks 프로젝트

## <a name="export-an-azure-notebooks-project"></a>Azure Notebooks 프로젝트 내보내기

1. [Azure Notebooks](https://notebooks.azure.com)로 가서 로그인합니다. 자세한 내용은 [빠른 시작 - Azure Notebooks에 로그인](quickstart-sign-in-azure-notebooks.md)을 참조하세요.

1. 공개 프로필 페이지 맨 위에 있는 **내 프로젝트**를 선택합니다.

    ![브라우저 창 맨 위에 있는 내 프로젝트 링크](media/quickstarts/my-projects-link.png)

1. 프로젝트를 선택합니다.
1. "다운로드" 단추를 클릭하여 모든 프로젝트 파일이 포함된 Zip 파일을 다운로드합니다.
1. 또는 특정 프로젝트 페이지에서 "프로젝트 다운로드" 단추를 클릭하여 특정 프로젝트의 모든 파일을 다운로드합니다.

프로젝트 파일을 다운로드한 후에는 다른 Jupyter Notebook 솔루션에 사용할 수 있습니다. 아래 섹션에는 몇 가지 옵션이 설명되어 있습니다. 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Notebook 변수 만들기

Azure Notebooks 미리 보기와 일치하는 환경을 만들려면 GitHub에 제공된 스크립트 파일을 사용하면 됩니다.

1. Azure Notebooks [GitHub 리포지토리](https://github.com/microsoft/AzureNotebooks)로 이동하거나 [환경 폴더에 직접 액세스](https://aka.ms/aznbrequirementstxt)합니다.
1. 명령 프롬프트에서, 프로젝트에 사용하려는 디렉터리로 이동합니다.
1. 환경 폴더 콘텐츠를 다운로드하고 README 파일의 지침에 따라 Azure Notebooks 패키지 종속성을 설치합니다.


## <a name="use-notebooks-in-visual-studio-code"></a>Visual Studio Code에서 Notebook 사용

[VS Code](https://code.visualstudio.com/)는 로컬로 또는 원격 컴퓨팅에 연결하여 사용할 수 있는 무료 코드 편집기입니다. Python 확장과 함께 사용하면 Jupyter Notebooks 작업을 위한 풍부한 기본 환경을 포함하여 완전한 Python 개발용 환경을 제공합니다. 

![VS Code Jupyter Notebook 지원](media/vs-code-jupyter-notebook.png)

프로젝트 파일을 [다운로드](#export-an-azure-notebooks-project)한 후에는 VS Code에서 사용할 수 있습니다. Jupyter Notebook과 함께 VS Code를 사용하는 방법에 대한 참고 자료는 [Visual Studio Code에서 Jupyter Notebook 사용](https://code.visualstudio.com/docs/python/jupyter-support) 및 [Visual Studio Code의 데이터 과학](https://code.visualstudio.com/docs/python/data-science-tutorial) 자습서를 참조하세요.

Visual Studio Code에서 [Azure Notebooks 환경 스크립트](#create-an-environment-for-notebooks)를 사용하여 Azure Notebooks 미리 보기와 일치하는 환경을 만들 수도 있습니다.

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Visual Studio Codespaces에서 Notebook 사용

Visual Studio Codespaces는 Visual Studio Code 또는 웹 브라우저를 사용하여 Notebook을 편집할 수 있는 클라우드 호스팅 환경을 제공합니다. VS Code 못지 않게 뛰어난 Jupyter 환경을 제공하지만, 디바이스에 어떤 것도 설치할 필요가 없습니다. 로컬 환경을 설정하지 않고 클라우드 지원 솔루션을 사용하려면 Codespaces를 사용하는 것이 좋습니다. 시작하기:

1. 프로젝트 파일을 [다운로드](#export-an-azure-notebooks-project)합니다.
1. Notebook을 저장하기 위한 [GitHub 리포지토리를 만듭니다](https://help.github.com/github/getting-started-with-github/create-a-repo). 
1. 리포지토리에 [파일을 추가합니다](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository).
1. [브라우저](https://docs.microsoft.com/visualstudio/online/how-to/browser), [Visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside) 또는 [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode)에서 Visual Studio Codespaces를 설정합니다.

## <a name="use-notebooks-with-azure-machine-learning"></a>Azure Machine Learning에서 Notebook 사용

Azure Machine Learning은 사용자가 Azure에서 더 빠르게 모델을 빌드하고 배포할 수 있는 엔드투엔드 기계 학습 플랫폼을 제공합니다. Azure ML을 사용하면 VM 또는 공유 클러스터 컴퓨팅 환경에서 Jupyter Notebook을 실행할 수 있습니다. ML 워크로드에 사용할 실험 추적, 데이터 세트 관리 등의 기능이 포함된 클라우드 기반 솔루션이 필요한 경우 Azure Machine Learning을 권장합니다. Azure ML을 시작하는 방법은 다음과 같습니다.

1. 프로젝트 파일을 [다운로드](#export-an-azure-notebooks-project)합니다.
1. Azure Portal에서 [작업 영역을 만듭니다](../machine-learning/how-to-manage-workspace.md).

   ![작업 영역 만들기](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. [Azure Studio(미리 보기)](https://ml.azure.com/)를 엽니다.
1. 왼쪽에 있는 탐색 모음을 사용하여 **Notebook**을 선택합니다.
1. **파일 업로드** 단추를 클릭하고, Azure Notebooks에서 다운로드한 프로젝트 파일을 업로드합니다.

Azure ML 및 Jupyter Notebook 실행에 대한 자세한 내용은 [설명서](../machine-learning/how-to-run-jupyter-notebooks.md)를 검토하거나 Microsoft Learn에서 [Machine Learning 소개](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) 모듈을 사용해보세요.


## <a name="use-azure-lab-services"></a>Azure Lab Services 사용

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/)를 사용하여 교육자는 교실 전체에서 사용할 수 있도록 미리 구성된 VM에 대한 주문형 액세스를 쉽게 설정하고 제공할 수 있습니다. 맞춤형 교실 환경에서 Jupyter Notebook 및 클라우드 컴퓨팅을 사용할 방법을 찾고 있다면 Lab Services를 사용하는 것이 좋습니다.

![이미지](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 프로젝트 파일을 [다운로드](#export-an-azure-notebooks-project)한 후에는 Azure Lab Services에서 사용할 수 있습니다. 랩을 설정하는 방법에 대한 참고 자료는 [Python 및 Jupyter Notebook을 사용하여 데이터 과학을 교육하기 위한 랩 설정](../lab-services/class-type-jupyter-notebook.md)을 참조하세요.

## <a name="use-github"></a>GitHub 사용

GitHub는 Notebook(및 기타 파일)을 저장하고, Notebook을 다른 사람과 공유하고, 협업하는 데 사용할 수 있는 소스 제어를 지원하는 방법을 무료로 제공합니다. 프로젝트를 다른 사람과 공유하고 협업하는 방법을 찾고 있다면 GitHub는 훌륭한 옵션이며, [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)와 결합하여 뛰어난 개발 환경을 만들 수 있습니다. GitHub를 시작하는 방법

1. 프로젝트 파일을 [다운로드](#export-an-azure-notebooks-project)합니다.
1. Notebook을 저장하기 위한 [GitHub 리포지토리를 만듭니다](https://help.github.com/github/getting-started-with-github/create-a-repo). 
1. 리포지토리에 [파일을 추가합니다](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository).

## <a name="next-steps"></a>다음 단계

- [Visual Studio Code 내 Python에 대한 자세한 정보](https://code.visualstudio.com/docs/python/python-tutorial)
- [Azure Machine Learning 및 Jupyter Notebook에 대한 자세한 정보](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Visual Studio Codespaces에 대한 자세한 정보](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)
- [Azure Lab Services에 대한 자세한 정보](https://azure.microsoft.com/services/lab-services/)
- [GitHub에 대한 자세한 정보](https://help.github.com/github/getting-started-with-github/)
