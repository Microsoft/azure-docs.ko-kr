---
title: '자습서: Jupyter Notebook 실험(Python)'
titleSuffix: Machine Learning - Azure
description: 이 자습서에서는 Jupyter Notebook에서 실행되는 Azure Machine Learning Python SDK를 시작합니다.  1부에서는 실험 및 ML 모델을 관리할 작업 영역을 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: ff23a42d9b96b8411d8b2f82ab8303e2a8a69953
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852721"
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

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>작업 영역에서 Notebook 실행

이 자습서에서는 작업 영역의 클라우드 Notebook 서버를 설치하지 않는 미리 구성된 환경으로 사용합니다. 환경, 패키지 및 종속성을 제어하려면 [사용자 고유의 환경](how-to-configure-environment.md#local)을 사용합니다.

 이 비디오를 따르거나 아래의 상세 단계를 사용하여 작업 영역에서 자습서를 복제하고 실행합니다. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Notebook 폴더 복제

모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 인터페이스인 Azure Machine Learning Studio에서 다음 실험 설정 및 실행 단계를 완료합니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. 왼쪽에서 **Notebook**을 선택합니다.

1. 맨 위에 있는 **샘플** 탭을 선택합니다.

1. **Python** 폴더를 엽니다.

1. 버전 번호가 있는 폴더를 엽니다.  이 번호는 Python SDK의 현재 릴리스를 나타냅니다.

1. **tutorials** 폴더의 오른쪽에 있는 **“...”** 을 선택한 다음, **복제**를 선택합니다.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="자습서 폴더 복제":::

1. 작업 영역에 액세스하는 각 사용자를 표시하는 폴더 목록이 표시됩니다.  **tutorials** 폴더를 복제할 폴더를 선택합니다.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>복제된 Notebook 열기

1. **사용자 파일** 섹션에 방금 닫은 **자습서** 폴더를 엽니다.

    > [!IMPORTANT]
    > **samples** 폴더에서 Notebook을 볼 수 있지만 해당 폴더에서 Notebook을 실행할 수는 없습니다.  Notebook을 실행하려면 **사용자 파일** 섹션에서 복제된 버전의 Notebook을 열어야 합니다.
    
1. **tutorials/create-first-ml-experiment** 폴더에서 **tutorial-1st-experiment-sdk-train.ipynb** 파일을 선택합니다.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="자습서 폴더 열기":::


1. 위쪽 바에서 Notebook을 실행하는 데 사용할 컴퓨팅 인스턴스를 선택합니다. 이러한 VM은 [Azure Machine Learning을 실행하는 데 필요한 모든 항목](concept-compute-instance.md#contents)을 사용하여 미리 구성됩니다. 

1. VM을 찾을 수 없는 경우 **+ 추가**를 선택하여 컴퓨팅 인스턴스 VM을 만듭니다. 

    1. VM을 만들 경우 다음 규칙을 따릅니다.  
        + 이름은 필수이며 비워 둘 수 없습니다.
        + 작업 영역/컴퓨팅 인스턴스의 Azure 지역에 있는 모든 기존 컴퓨팅 인스턴스에서 고유한 이름(대/소문자 구분 안 함)이어야 합니다. 선택한 이름이 고유하지 않으면 경고를 받게 됩니다.
        + 유효한 문자는 대/소문자, 숫자(0~9) 및 대시 문자(-)입니다.
        + 이름은 3~24자 사이여야 합니다.
        + 이름은 문자(숫자 또는 대시 문자 아님)로 시작해야 합니다.
        + 대시 문자를 사용하는 경우 대시 뒤에 문자가 하나 이상 있어야 합니다. 예제: Test-, test-0, test-01은 유효하지 않지만 test-a0, test-0a는 유효한 인스턴스입니다.

    1.  사용 가능한 선택 항목에서 Virtual Machine 크기를 선택합니다. 이 자습서에서는 기본 VM을 선택하는 것이 좋습니다.

    1. 그런 다음 **만들기**를 선택합니다. VM을 설정하는 데 약 5분이 걸릴 수 있습니다.

1. VM을 사용할 수 있게 되면 맨 위 도구 모음에 표시됩니다.  이제 도구 모음에서 **모두 실행**을 사용하거나 Notebook의 코드 셀에 **Shift + Enter**를 사용하여 Notebook을 실행할 수 있습니다.

사용자 지정 위젯이 있거나 Jupyter/JupyterLab을 사용하려는 경우 맨 오른쪽에 있는 **Jupyter** 드롭다운을 선택한 다음, **Jupyter** 또는 **JupyterLab**을 선택합니다. 새 브라우저 창이 열립니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

* Azure Machine Learning 작업 영역을 만들었습니다.
* 작업 영역에서 클라우드 Notebook 서버 만들기 및 구성

자습서의 **2부**에서 `tutorial-1st-experiment-sdk-train.ipynb`의 코드를 실행하여 기계 학습 모델을 학습합니다. 

> [!div class="nextstepaction"]
> [자습서: 첫 번째 모델 학습](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> 이 자습서 또는 다른 자습서의 2부를 따르지 않을 경우 비용을 절감하기 위해 사용하지 않을 때는 [클라우드 Notebook 서버 VM](tutorial-1st-experiment-sdk-train.md#clean-up-resources)을 중지해야 합니다.


