---
title: 자습서 - Jupyter Notebook 만들기 및 실행 - Azure Notebooks 미리 보기
description: 데이터 과학의 선형 회귀 프로세스를 보여주는 Azure Notebooks 미리 보기에서 Jupyter Notebook을 만들고 실행하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: cb8be1618de7e95a7096e4ea9dec78bf4aa277f6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589266"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>자습서: Python을 사용하여 Jupyter Notebook을 만들고 실행

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

이 자습서에서는 Azure Notebooks를 사용하여 간단한 선형 회귀를 보여주는 완전한 Jupyter Notebook을 만드는 프로세스를 안내합니다. 이 자습서를 진행하면 여러 셀을 만들고, 셀을 실행하고, Notebook을 슬라이드 쇼로 표시하는 방법을 포함하여 Jupyter Notebook UI를 익숙하게 다룰 수 있습니다.

완료된 Notebook은 [GitHub - Azure Notebooks 샘플](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)에서 찾을 수 있습니다. 하지만 이 자습서에서는 새 프로젝트와 빈 Notebook을 사용하므로 만드는 과정을 단계별로 경험할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 몇 가지 샘플 데이터를 사용하여 프로젝트 Notebook 만들기
> * Notebook 인터페이스를 사용하여 다양한 유형의 셀 생성
> * 노트북 실행
> * Notebook 저장
> * Visual Studio Code에서 Notebook 디버깅

## <a name="create-the-project"></a>프로젝트 만들기

1. [Azure Notebooks](https://notebooks.azure.com)로 가서 로그인합니다. (자세한 내용은 [빠른 시작 - Azure Notebooks에 로그인](quickstart-sign-in-azure-notebooks.md)을 참조하세요.)

1. 공개 프로필 페이지 맨 위에 있는 **내 프로젝트**를 선택합니다.

    ![브라우저 창 맨 위에 있는 내 프로젝트 링크](media/quickstarts/my-projects-link.png)

1. **내 프로젝트** 페이지에서 **+ 새 프로젝트**(바로 가기 키: n)를 선택합니다. 브라우저 창이 좁은 경우 이 단추가 **+** 기호로만 표시될 수 있습니다.

    ![내 프로젝트 페이지의 새 프로젝트 명령](media/quickstarts/new-project-command.png)

1. **새 프로젝트 만들기** 팝업이 나타나면 다음 세부 정보를 입력하거나 설정한 다음, **만들기**를 선택합니다.

   - **프로젝트 이름**: 선형 회귀 예제 - Cricket Chirps
   - **프로젝트 ID**: linear-regression-example
   - **공용 프로젝트**: (선택 취소)
   - **README.md 만들기**: (선택 해제됨)

1. 잠시 후 Azure Notebooks는 새 프로젝트로 이동합니다.

## <a name="create-the-data-file"></a>데이터 파일 만들기

Notebook에 만드는 선형 회귀 모델은 *cricket_chirps.csv*라는 프로젝트의 파일에서 데이터를 가져옵니다. 이 파일은 [GitHub - Azure Notebooks 샘플](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)에서 복사하여 또는 데이터를 직접 입력하여 만들 수 있습니다. 다음 섹션에서는 두 방법을 설명합니다.

### <a name="upload-the-data-file"></a>데이터 파일 업로드

1. Azure Notebooks의 프로젝트 대시보드에서 **업로드** > **URL에서**를 선택합니다.
1. 팝업에서 **파일 URL**에 다음 URL을 입력하고 **파일 이름**에 *cricket_chirps.csv*를 입력한 다음, **완료**를 선택합니다.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. 이제 프로젝트의 파일 목록에 *cricket_chirps.csv* 파일이 나타날 것입니다.

    ![새로 만든 CSV 파일이 프로젝트 파일 목록에 표시됨](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>파일을 새로 만들기

1. Azure Notebooks의 프로젝트 대시보드에서 **+ 새로 만들기** > **빈 파일**을 선택합니다.
1. 프로젝트의 파일 목록에 필드가 나타납니다. *cricket_chirps.csv*를 입력하고 Enter 키를 누릅니다.
1. *cricket_chirps.csv*를 마우스 오른쪽 단추로 클릭하고 **파일 편집**을 선택합니다.
1. 나타나는 편집기에서 다음 데이터를 입력합니다.

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. **파일 저장**을 선택하여 파일을 저장하고 프로젝트 대시보드로 돌아갑니다.

## <a name="install-project-level-packages"></a>프로젝트 수준 패키지 설치

Notebook 내에서, 항상 코드 셀에서 `!pip install` 같은 명령을 사용하여 필요한 패키지를 설치할 수 있습니다. 그러나 이러한 명령은 Notebook의 코드 셀을 실행할 때마다 실행되며 상당히 긴 시간이 걸릴 수 있습니다. 이러한 이유로, `requirements.txt` 파일을 사용하여 프로젝트 수준에서 패키지를 설치할 수도 있습니다.

1. [파일을 새로 만들기](#create-a-file-from-scratch)에 설명된 프로세스에 따라 다음 콘텐츠를 사용하여 `requirements.txt`라는 파일을 만듭니다.

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    원한다면 [데이터 파일 업로드](#upload-the-data-file)에 설명된 대로 로컬 컴퓨터에서 `requirements.txt` 파일을 업로드할 수도 있습니다.

1. 프로젝트 대시보드에서 **프로젝트 설정**을 선택합니다.
1. 나타나는 팝업에서 **환경** 탭을 선택한 다음, **+ 추가**를 선택합니다.
1. **환경 설정 단계** 아래의 첫 번째 드롭다운 목록 컨트롤(작업)에서 **Requirements.txt**를 선택합니다.
1. 두 번째 드롭다운 컨트롤(파일 이름)에서 *requirements.txt*(만든 파일)를 선택합니다.
1. 세 번째 드롭다운 목록 컨트롤(Python 버전)에서 **Python 버전 3.6**을 선택합니다.
1. **저장**을 선택합니다.

![requirements.txt 파일을 지정하는 프로젝트 설정 환경 탭](media/tutorial/tutorial-requirements-txt.png)

이 설치 단계를 사용하면 프로젝트에서 실행하는 모든 Notebook은 해당 패키지가 설치된 환경에서 실행됩니다.

## <a name="create-and-run-a-notebook"></a>Notebook 만들기 및 실행

데이터 파일이 준비되고 프로젝트 환경이 설정되면 Notebook을 만들어서 열 수 있습니다.

1. 프로젝트 대시보드에서 **+ 새로 만들기** > **Notebook**을 선택합니다.
1. 팝업에서 **항목 이름**으로 *선형 회귀 예제 - Cricket Chirps.ipynb*를 입력하고, 언어로 **Python 3.6**을 선택한 다음, **새로 만들기**를 선택합니다.
1. 새 Notebook이 파일 목록에 표시되면 해당 Notebook을 선택하여 시작합니다. 새 브라우저 탭이 자동으로 열립니다.
1. 환경 설정에 *requirements.txt* 파일이 있기 때문에 "컨테이너가 준비될 때까지 기다리십시오"라는 메시지가 표시됩니다. **확인**을 선택하여 메시지를 닫고 Notebook에서 계속 작업을 진행할 수 있지만, 환경이 완전히 설정되기 전에는 코드 셀을 실행할 수 없습니다.
1. Jupyter 인터페이스에서 Notebook이 열리고 기본적으로 코드 셀 하나가 비어 있습니다.

    [![Azure Notebooks의 새 Notebook 초기 보기](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Notebook 인터페이스 둘러보기

Notebook이 실행 중이면 코드 및 Markdown 셀을 추가하고, 해당 셀을 실행하고, Notebook 작업을 관리할 수 있습니다. 하지만 그 전에 잠시 인터페이스를 숙지하는 시간을 갖는 것이 좋습니다. 전체 설명서를 보려면 **도움말** > **Notebook 도움말** 메뉴 명령을 선택합니다.

창의 맨 위에는 다음과 같은 항목이 있습니다.

(A) Notebook 이름 - 클릭하여 편집할 수 있습니다.
(B) 포함된 프로젝트 및 프로젝트 대시보드로 이동하는 단수 - 브라우저에서 새 탭이 열립니다.
(C) Notebook을 사용하는 명령을 제공하는 메뉴.
(D) 일반적인 작업에 대한 바로 가기를 제공하는 도구 모음.
(E) 셀이 포함된 편집 캔버스.
(F) Notebook을 신뢰할 수 있는지 여부를 나타내는 표시기(기본값은 **신뢰할 수 없음**).
(G) 작업 표시기와 함께 Notebook을 실행하는 데 사용되는 커널.

[![Jupyter 인터페이스의 주 UI 영역](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter는 주 UI 요소를 둘러보는 기능을 기본적으로 제공합니다. 둘러보기를 시작하려면 **도움말** > **사용자 인터페이스 둘러보기** 명령을 선택하고 팝업을 클릭합니다.

메뉴 명령 그룹은 다음과 같습니다.

| 메뉴 | Description |
| --- | --- |
| 파일 | Notebook을 생성 및 복사하고, 인쇄 미리 보기를 표시하고, Notebook을 다양한 형식으로 다운로드하는 명령을 포함하여 Notebook 파일을 관리하는 명령입니다. |
| 편집 | 셀을 잘라내고/복사하고/붙여넣고, 값을 찾아서 바꾸고, 셀 첨부 파일을 관리하고, 이미지를 삽입하는 일반적인 명령입니다.  |
| 보기 | Jupyter UI의 여러 부분에 대한 가시성을 제어하는 명령입니다. |
| 삽입 | 현재 셀의 위 또는 아래에 새 셀을 삽입하는 명령입니다. 이러한 명령은 Notebook을 만들 때 자주 사용됩니다. |
| 셀 | 다양한 **실행** 명령이 하나 이상의 셀을 다양한 조합으로 실행합니다. **셀 형식** 명령은 셀 형식을 **코드**, **Markdown**, **원시 NBConvert**(일반 텍스트) 사이에서 변경합니다. **현재 출력** 및 **모든 출력** 명령은 실행 코드의 출력을 표시하는 방법을 제어하며, 모든 출력을 지우는 명령을 포함하고 있습니다. |
| 커널 | Notebook을 실행하는 데 사용되는 언어 또는 Python 버전을 변경하는 **커널 변경**과 함께, 커널에서 코드를 실행하는 방법을 관리하는 명령입니다. |
| 데이터 | 프로젝트 또는 세션에 파일을 업로드 및 다운로드하는 명령입니다. [프로젝트 데이터 파일을 사용하여 작업](work-with-project-data-files.md)을 참조하세요. |
| 위젯 | [Jupyter 위젯](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)을 관리하는 명령이며 시각화, 매핑 및 그리기를 위한 추가 기능을 제공합니다.|
| 도움말 | Jupyter 인터페이스에 대한 도움말과 설명서를 제공하는 명령입니다. |

도구 모음의 명령은 대부분 그와 동일한 메뉴 명령이 있습니다. **Enter/Edit RISE Slideshow**는 예외이며, [Notebook 공유 및 표시](present-jupyter-notebooks-slideshow.md)에서 살펴보겠습니다.

이어지는 섹션에서 Notebook을 채울 때 이처럼 다양한 명령을 사용하게 됩니다.

## <a name="create-a-markdown-cell"></a>Markdown 셀 만들기

1. Notebook 캔버스에 표시된 첫 번째 빈 셀을 클릭합니다. 기본적으로 셀은 **코드** 형식입니다. 즉, 선택한 커널(Python, R 또는 F#)에 대해 실행 가능한 코드를 포함하도록 설계됩니다. 현재 형식은 도구 모음의 형식 드롭다운에 표시됩니다.

    ![셀 형식 도구 모음 드롭다운](media/tutorial/tutorial-cell-type-drop-down.png)

1. 도구 모음 드롭다운을 사용하여 셀 형식을 **Markdown**으로 변경하거나 **셀** > **셀 형식** > **Markdown** 메뉴 명령을 사용합니다.

    ![셀 형식 메뉴 명령](media/tutorial/tutorial-cell-type-menu.png)

1. 셀을 클릭하여 편집을 시작하고, 다음 Markdown을 입력합니다.

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Markdown을 브라우저용 HTML로 렌더링하려면 도구 모음에서 **실행** 명령을 선택하거나 **셀** > **셀 실행** 명령을 사용합니다. 이제 서식 지정 및 링크용 Markdown 코드가 예상대로 브라우저에 표시됩니다.

1. Notebook의 마지막 셀을 실행하면 Jupyter는 사용자가 실행한 셀 아래에 자동으로 새 셀을 만듭니다. 이 셀에 더 많은 Markdown을 배치하려면 다음 Markdown을 사용하여 이 섹션의 단계를 반복합니다.

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Markdown을 다시 편집하려면 렌더링된 셀을 두 번 클릭합니다. 변경 작업을 수행한 후 HTML을 다시 렌더링하려면 셀을 실행합니다.

## <a name="create-a-code-cell-with-commands"></a>명령을 사용하여 코드 셀 만들기

이전 Markdown 셀에서 설명했듯이, Notebook에서 직접 명령을 포함할 수 있습니다. 명령을 사용하여 패키지를 설치하고, 데이터를 검색하는 curl 또는 wget을 실행하는 등의 작업을 할 수 있습니다. Jupyter Notebooks는 Linux 가상 머신 내에서 효과적으로 실행되므로 Linux 명령 집합 전체를 사용할 수 있습니다.

1. 이전 Markdown 셀에서 **실행**을 사용한 후에 나타난 코드 셀에 아래 명령을 입력합니다. 새 셀이 보이지 않으면 **삽입** > **아래에 셀 삽입**을 사용하거나 도구 모음에서 **+** 단추를 사용하여 새 셀을 만듭니다.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. 셀을 실행하기 전에, 도구 모음의 **+** 단추를 사용하여 새 셀을 만들고, Markdown으로 설정하고, 다음 설명을 입력합니다.

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. **셀** > **모두 실행** 명령을 선택하여 Notebook의 모든 셀을 실행합니다. Markdown 셀은 HTML로 렌더링되고, 명령은 커널에서 실행되고, Markdown 자체에 설명된 대로 커널 표시기를 관찰합니다.

    ![Notebook 커널의 사용 중 표시기](media/tutorial/tutorial-kernel-busy.png)

1. 또한 `pip install` 명령이 모두 실행되려면 약간 시간이 걸리며, 프로젝트 환경에 이러한 패키지를 이미 설치했기 때문에(그리고 기본적으로 Azure Notebooks에도 포함되어 있으므로) "요구 사항이 이미 충족되었습니다"라는 내용의 여러 메시지가 표시됩니다. 출력을 모두 표시하면 시각적으로 불편할 수 있으므로 해당 셀만 선택한 다음(단일 클릭 사용), **셀** > **셀 출력** > **토글**을 사용하여 출력을 숨깁니다. 동일한 하위 메뉴에서 **지우기** 명령을 사용하여 출력을 완전히 제거할 수도 있습니다.

    **토글** 명령은 셀에서 가장 최근의 출력만 숨깁니다. 셀을 다시 실행하면 출력이 다시 나타납니다.

1. 패키지가 프로젝트 환경에 설치되어 있으므로 `#`을 사용하여 `! pip install` 명령을 주석으로 처리합니다. 이렇게 하면 Notebook에 교육 자료로 남길 수 있지만, 실행에 시간이 걸리지 않으며 불필요한 출력을 생성하지 않습니다. 이 경우 주석 처리된 명령을 Notebook에서 유지하면 Notebook의 종속성이 표시됩니다.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>나머지 셀 만들기

Notebook의 나머지 부분을 채우려면 일련의 Markdown 및 코드 셀을 만들어야 합니다. 아래에 나열된 각 셀에 대한 새 셀을 만들고, 형식을 설정한 다음, 콘텐츠에 붙여넣습니다.

각 셀을 만든 후 Notebook이 실행될 때까지 기다려도 되지만, 각 셀을 만들 때 실행하는 것도 흥미로울 것입니다. 모든 셀이 출력을 표시하는 것은 아닙니다. 오류가 보이지 않으면 셀이 정상적으로 실행된 것으로 가정합니다.

각 코드 셀은 이전 셀에서 실행된 코드에 종속되며, 셀 중 하나를 실행하지 않으면 이후 셀에서 오류가 발생할 수 있습니다. 셀을 실행하는 것을 잊은 경우 현재 셀을 실행하기 전에 **셀** > **위의 셀 모두 실행**을 사용해 봅니다.

예기치 않은 결과가 표시되면(아마도 그럴 것입니다!) 각 셀이 필요에 따라 "코드" 또는 "Markdown"으로 설정되었는지 확인합니다. 예를 들어 "잘못된 구문" 오류는 코드 셀에 Markdown을 입력한 경우에 주로 발생합니다.

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. 코드 셀. 실행하면 출력으로 테이블 콘텐츠를 표시합니다. `print` 문을 주석으로 처리하여 출력을 표시하지 않을 수 있습니다.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > 이 코드에서 "numpy.dtype 크기가 변경되었습니다" 오류가 발생할 수 있습니다. 이 경고는 무시해도 됩니다.

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. 코드 셀. 이 셀은 실행해도 출력이 없습니다.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. 코드 셀. 이 셀은 실행하면 `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` 출력을 표시합니다.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. 코드 셀. 이 셀은 실행하면 `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`과 비슷한 결과를 표시합니다.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. 코드 셀. 이 셀은 실행하면 그래픽 플롯을 생성합니다. 처음 실행할 때 플롯이 보이지 않으면(그 대신 "Figure size 640x480 with 1 Axes"가 표시되면) 셀을 다시 실행합니다.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![matplotlib 코드의 플롯 출력](media/tutorial/tutorial-plot-output.png)

1. Markdown 셀은 다음과 같습니다.

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>출력을 지우고 모든 셀을 다시 실행

이전 섹션의 단계에 따라 Notebook 전체를 채운 후, 선형 회귀에 대한 전체 자습서의 컨텍스트에서 실행되는 코드 조각을 만들었습니다. 이 코드와 텍스트의 직접 조합은 Notebook의 커다란 장점 중 하나입니다.

이제 Notebook 전체를 다시 실행합니다.

1. **커널** > **다시 시작하고 출력 지우기**를 선택하여 모든 커널의 세션 데이터와 셀 출력을 지웁니다. Notebook을 완료한 후에는 항상 이 명령을 실행하여 코드 셀 간에 이상한 종속성이 없는지 확인하는 것이 좋습니다.

1. **셀** > **모두 실행**을 사용하여 Notebook을 다시 실행합니다. 코드가 실행되는 동안 커널 표시기가 채워집니다.

    코드가 너무 오래 실행되거나 중단된 경우 **커널** > **중단** 명령을 사용하여 커널을 중지할 수 있습니다.

1. Notebook을 스크롤하여 결과를 검사합니다. (또 다시 플롯이 표시되지 않으면 해당 셀을 다시 실행합니다.)

## <a name="save-halt-and-close-the-notebook"></a>Notebook을 저장하고, 중지하고, 닫기

Notebook을 편집하는 동안 **파일** > **저장 및 검사점** 명령 또는 도구 모음의 저장 단추를 사용하여 현재 상태를 저장할 수 있습니다. "검사점"은 세션 중 언제든지 되돌릴 수 있는 스냅샷을 만듭니다. 검사점을 사용하면 일련의 실험 변경 작업을 수행할 수 있으며, 변경 내용이 작동하지 않으면 **파일** > **검사점으로 되돌리기** 명령을 사용하여 간단하게 검사점으로 되돌릴 수 있습니다. 추가 셀을 만들고 실행하지 않을 코드를 주석으로 처리하는 방법도 있습니다. 어떤 방법을 사용해도 상관 없습니다.

또한 언제든지 **파일** > **복사본 만들기** 명령을 사용하여 Notebook의 현재 상태 복사본을 프로젝트에 새 파일로 만들 수 있습니다. 해당 복사본은 새 브라우저 탭에서 자동으로 열립니다.

Notebook 작업을 마쳤으면 **파일** > **닫기 및 중지** 명령을 사용하여 Notebook을 닫고 실행 중인 커널을 종료합니다. 그러면 Azure Notebooks가 자동으로 브라우저 탭을 닫습니다.

## <a name="debug-notebooks-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Notebook 디버그

Notebook의 코드 셀이 예상대로 동작하지 않는 경우 코드 버그 또는 다른 결함이 원인일 수 있습니다. 그러나 일반적인 Jupyter 환경에서는 `print` 문을 사용하여 변수 값을 표시하는 방법 외에는 디버깅 기능을 제공하지 않습니다.

Notebook의 *.ipynb* 파일을 다운로드하여 Python 확장을 사용해 Visual Studio Code에서 열 수 있습니다. 이 확장은 Notebook을 단일 코드 파일로 직접 가져오므로 주석에서 Markdown 셀이 유지됩니다. Notebook을 가져온 후에는 Visual Studio Code 디버거를 사용하여 코드를 단계별로 실행하고 중단점을 설정하고 상태를 점검하는 등의 작업을 수행할 수 있습니다. 코드를 수정한 후에 *.ipynb* 파일을 Visual Studio Code에서 내보낸 다음 Azure Notebooks에 다시 업로드합니다.

자세한 내용은 Visual Studio Code 설명서에서 [Jupyter Notebook 디버그](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook)를 참조하세요.

[Visual Studio Code - Jupyter 지원](https://code.visualstudio.com/docs/python/jupyter-support)에서 Visual Studio Code의 Juypter Notebooks용 추가 기능도 참조하세요.

## <a name="next-steps"></a>다음 단계

- [샘플 Notebook 살펴보기](azure-notebooks-samples.md)

방법 문서는 다음과 같습니다.

- [프로젝트 만들기 및 복제](create-clone-jupyter-notebooks.md)
- [프로젝트 구성 및 관리](configure-manage-azure-notebooks-projects.md)
- [Notebook 내에서 패키지 설치](install-packages-jupyter-notebook.md)
- [슬라이드 쇼 제공](present-jupyter-notebooks-slideshow.md)
- [데이터 파일 작업](work-with-project-data-files.md)
- [데이터 리소스에 액세스](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning 사용](use-machine-learning-services-jupyter-notebooks.md)
