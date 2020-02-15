---
title: '자습서: 첫 번째 ML 실험 만들기'
titleSuffix: Azure Machine Learning
description: 이 자습서에서는 Jupyter Notebook에서 실행되는 Azure Machine Learning Python SDK를 시작합니다.  1부에서는 실험 및 ML 모델을 관리할 작업 영역을 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: 125cde85d3caa6fd1a33045e85c5383f83a669bf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982537"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>자습서: Python SDK로 첫 번째 ML 실험 만들기 시작
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 자습서에서는 Jupyter Notebook에서 실행되는 Azure Machine Learning Python SDK를 시작하기 위한 엔드투엔드 단계를 수행합니다. 이 자습서는 **2부로 구성된 자습서 시리즈 중 제1부**입니다. Python 환경 설정 및 구성에 대해 설명하고, 실험 및 기계 학습 모델을 관리하기 위한 작업 영역을 만듭니다. [**2부**](tutorial-1st-experiment-sdk-train.md)에서는 이를 기반으로 하여 여러 기계 학습 모델을 학습시키고 Azure Machine Learning Studio와 SDK를 모두 사용하여 모델 관리 프로세스를 소개합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 다음 자습서에서 사용할 [Azure Machine Learning 작업 영역](concept-workspace.md)을 만듭니다.
> * 자습서 Notebook을 작업 영역의 사용자 폴더로 복제합니다.
> * Azure Machine Learning Python SDK가 설치되고 미리 구성된 클라우드 기반 컴퓨팅 인스턴스를 만듭니다.


Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다. 

Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> **작업 영역** 및 **구독**을 적어 둡니다. 올바른 작업 영역에 실험을 만들려면 이 정보가 필요합니다. 

## <a name="azure"></a>작업 영역에서 Notebook 실행

이 자습서에서는 작업 영역의 클라우드 Notebook 서버를 설치하지 않는 미리 구성된 환경으로 사용합니다. 환경, 패키지 및 종속성을 제어하려면 [사용자 고유의 환경](how-to-configure-environment.md#local)을 사용합니다.

이 비디오를 따르거나 아래의 상세 단계를 사용하여 작업 영역에서 자습서를 복제하고 실행합니다. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Notebook 폴더 복제

모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 인터페이스인 Azure Machine Learning Studio에서 다음 실험 설정 및 실행 단계를 완료합니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. 왼쪽에서 **Notebook**을 선택합니다.

1. **Samples** 폴더를 엽니다.

1. **Python** 폴더를 엽니다.

1. 버전 번호가 있는 폴더를 엽니다.  이 번호는 Python SDK의 현재 릴리스를 나타냅니다.

1. **tutorials** 폴더의 오른쪽에 있는 **“...”** 을 선택한 다음, **복제**를 선택합니다.

    ![폴더 복제](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. 작업 영역에 액세스하는 각 사용자를 표시하는 폴더 목록이 표시됩니다.  **tutorials** 폴더를 복제할 폴더를 선택합니다.

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">복제된 Notebook 열기

1. **사용자 파일**에서 폴더를 연 다음, 복제된 **tutorials** 폴더를 엽니다.

    ![자습서 폴더 열기](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > **samples** 폴더에서 Notebook을 볼 수 있지만 해당 폴더에서 Notebook을 실행할 수는 없습니다.  Notebook을 실행하려면 **사용자 파일** 섹션에서 복제된 버전의 Notebook을 열어야 합니다.
    
1. **tutorials** 폴더에서 **tutorial-1st-experiment-sdk-train.ipynb** 파일을 선택합니다.

1. 위쪽 바에서 Notebook을 실행하는 데 사용할 컴퓨팅 인스턴스를 선택합니다. 이러한 VM은 [Azure Machine Learning을 실행하는 데 필요한 모든 항목](concept-compute-instance.md#contents)을 사용하여 미리 구성됩니다. 작업 영역의 사용자가 만든 VM 중에서 선택할 수 있습니다. 

1. VM을 찾을 수 없는 경우 **+ 추가**를 선택하여 컴퓨팅 인스턴스 VM을 만듭니다. 

    1. VM을 만들 때 이름을 입력합니다.  이름은 2~16자 사이여야 합니다. 유효한 문자는 문자, 숫자 및 문자이며 Azure 구독에서 고유해야 합니다.

    1.  사용 가능한 선택 항목에서 Virtual Machine 크기를 선택합니다.

    1. 그런 다음 **만들기**를 선택합니다. VM을 설정하는 데 약 5분이 걸릴 수 있습니다.

1. VM을 사용할 수 있게 되면 맨 위 도구 모음에 표시됩니다.  이제 도구 모음에서 **모두 실행**을 사용하거나 Notebook의 코드 셀에 **Shift + Enter**를 사용하여 Notebook을 실행할 수 있습니다.

사용자 지정 위젯이 있거나 Jupyter/JupyterLab을 사용하려는 경우 맨 오른쪽에 있는 **Jupyter** 드롭다운을 선택한 다음, **Jupyter** 또는 **JupyterLab**을 선택합니다. 새 브라우저 창이 열립니다.

> [!NOTE]
> 컴퓨팅 인스턴스(미리 보기)는 현재 서비스 예정인 다른 지역에 대한 지원을 포함하여 **미국 중북부**, **미국 동부 2**, **북유럽** 또는 **영국 남부** 지역이 있는 작업 영역에서만 사용할 수 있습니다.
>작업 영역이 다른 지역에 있으면 [Notebook VM](concept-compute-instance.md#notebookvm)을 계속 만들어 사용할 수 있습니다.  Notebook VM 또는 컴퓨팅 인스턴스를 사용하여 Notebook을 실행할 수 있습니다. VM을 중지하는 경우를 제외하고 VM은 계속 실행되고 비용을 발생시킵니다. Studio의 **Compute** 탭에서 수동으로 중지하거나, Azure VM에 대해 예약된 자동 종료를 사용하도록 설정하는 [블로그 게시물](https://techcommunity.microsoft.com/t5/educator-developer-blog/azure-virtual-machine-auto-shutdown/ba-p/379342)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

* Azure Machine Learning 작업 영역을 만들었습니다.
* 작업 영역에서 클라우드 Notebook 서버 만들기 및 구성

자습서의 **2부**에서 `tutorial-1st-experiment-sdk-train.ipynb`의 코드를 실행하여 기계 학습 모델을 학습합니다. 

> [!div class="nextstepaction"]
> [자습서: 첫 번째 모델 학습](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> 이 자습서 또는 다른 자습서의 2부를 따르지 않을 경우 비용을 절감하기 위해 사용하지 않을 때는 [클라우드 Notebook 서버 VM](tutorial-1st-experiment-sdk-train.md#clean-up-resources)을 중지해야 합니다.


