---
title: PyTorch 모델을 Azure Functions 애플리케이션으로 배포
description: Azure Functions와 함께 PyTorch에서 미리 학습된 ResNet 18 심층 신경망을 사용하여 1000개의 ImageNet 레이블 중 1개를 이미지에 할당합니다.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: dd8de1b56927e158a181df952ce0dbeed140d6b7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078659"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>자습서: PyTorch를 사용하여 Azure Functions에 미리 학습된 이미지 분류 모델 배포

이 문서에서는 Python, PyTorch 및 Azure Functions를 사용하여 해당 콘텐츠에 따라 이미지를 분류하기 위해 미리 학습된 모델을 로드하는 방법에 대해 설명합니다. 모든 작업을 로컬로 수행하고 클라우드에서 Azure 리소스를 만들지 않으므로 이 자습서를 수행하는 데 드는 비용이 없습니다.

> [!div class="checklist"]
> * Python에서 Azure Functions를 개발하기 위한 로컬 환경을 초기화합니다.
> * 미리 학습된 PyTorch 기계 학습 모델을 함수 앱으로 가져옵니다.
> * 1000개의 ImageNet [클래스](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) 중 하나로 이미지를 분류하는 서버리스 HTTP API를 빌드합니다.
> * 웹앱에서 API를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 이상](https://www.python.org/downloads/release/python-374/). (Python 3.8.x 및 Python 3.6.x도 Azure Functions로 확인됩니다.)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- [Visual Studio Code](https://code.visualstudio.com/) 같은 코드 편집기

### <a name="prerequisite-check"></a>필수 구성 요소 확인

1. 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 2.7.1846 버전 이상인지 확인합니다.
1. `python --version`(Linux/MacOS) 또는 `py --version`(Windows)을 실행하여 Python 버전 보고서 3.7.x를 확인합니다.

## <a name="clone-the-tutorial-repository"></a>자습서 리포지토리 복제

1. 터미널 또는 명령 창에서 Git을 사용하여 다음 리포지토리를 복제합니다.

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. 폴더로 이동하여 해당 내용을 확인합니다.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start*는 자습서의 작업 폴더입니다.
    - *end*는 참조에 대한 최종 결과 및 전체 구현입니다.
    - *resources*는 기계 학습 모델 및 도우미 라이브러리를 포함하고 있습니다.
    - *frontend*는 함수 앱을 호출하는 웹 사이트입니다.

## <a name="create-and-activate-a-python-virtual-environment"></a>Python 가상 환경 만들기 및 활성화

*start* 폴더로 이동하고, 다음 명령을 실행하여 `.venv`라는 가상 환경을 만들고 활성화합니다.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Python에서 venv 패키지를 Linux 배포에 설치하지 않은 경우 다음 명령을 실행합니다.

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

활성화된 가상 환경에서 이후의 모든 명령을 실행합니다. (가상 환경을 종료하려면 `deactivate`를 실행하세요.)


## <a name="create-a-local-functions-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 HTTP 엔드포인트를 제공하는 `classify`라는 단일 상용구 함수가 포함된 함수 프로젝트를 만듭니다. 이후 섹션에서 더 구체적인 코드를 추가합니다.

1. *start* 폴더에서 Azure Functions Core Tools를 사용하여 Python 함수 앱을 초기화합니다.

    ```
    func init --worker-runtime python
    ```

    초기화 후 *start* 폴더에는 [local.settings.json](functions-run-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 다양한 파일이 있습니다. *local.settings.json*은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.

    > [!TIP]
    > 함수 프로젝트는 특정 런타임에 연결되므로 프로젝트의 모든 함수는 동일한 언어로 작성해야 합니다.

1. 다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수의 고유 이름이고, `--template` 인수는 함수의 트리거를 지정합니다. `func new`는 프로젝트의 선택한 언어에 적합한 코드 파일과 *function.json*이라는 구성 파일을 포함하는 함수 이름과 일치하는 하위 폴더를 만듭니다.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    이 명령은 *classify* 함수 이름과 일치하는 폴더를 만듭니다. 이 폴더에는 함수 코드를 포함하는 *\_\_init\_\_.py*와 함수의 트리거 및 해당 입/출력 바인딩을 설명하는  *function.json*의 두 파일이 있습니다. 이러한 파일의 콘텐츠에 대한 자세한 내용은 Python 빠른 시작에서 [파일 콘텐츠 검토](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python#optional-examine-the-file-contents)를 참조하세요.


## <a name="run-the-function-locally"></a>로컬에서 함수 실행

1. *start* 폴더에서 로컬 Azure Functions 런타임 호스트를 시작하여 함수를 시작합니다.

    ```
    func start
    ```

1. 출력에 `classify` 엔드포인트가 표시되면 ```http://localhost:7071/api/classify?name=Azure``` URL로 이동합니다. 출력에 "Hello Azure!"라는 메시지가 표시됩니다.

1. **Ctrl**-**C**를 사용하여 호스트를 중지합니다.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>PyTorch 모델 가져오기 및 도우미 코드 추가

콘텐츠를 기반으로 이미지를 분류하도록 `classify` 함수를 수정하려면 미리 학습된 [ResNet](https://arxiv.org/abs/1512.03385) 모델을 사용합니다. [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/)에서 제공하는 미리 학습된 모델은 이미지를 1000개의 [ImageNet 클래스](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) 중 하나로 분류합니다. 그런 다음, 일부 도우미 코드와 종속성을 프로젝트에 추가합니다.

1. *start* 폴더에서 다음 명령을 실행하여 예측 코드 및 레이블을 *classify* 폴더에 복사합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. *classify* 폴더에 *predict.py* 및 *labels.txt*라는 파일이 포함되어 있는지 확인합니다. 그렇지 않으면 *start* 폴더에서 명령을 실행했는지 확인합니다.

1. 텍스트 편집기에서 *start/requirements.txt*를 열고, 도우미 코드에 필요한 종속성을 추가합니다. 이는 다음과 같습니다.

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. *requirements.txt*를 저장한 다음, *start* 폴더에서 다음 명령을 실행하여 종속성을 설치합니다.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

설치하는 데 몇 분 정도 걸릴 수 있으며, 이 기간 동안 다음 섹션의 함수를 계속 수정할 수 있습니다.
> [!TIP]
> >Windows에서 "EnvironmentError: [Errno 2] 해당 파일 또는 디렉터리가 없음으로 인해 패키지를 설치할 수 없습니다."라는 오류가 발생한 후에 *sharded_mutable_dense_hashtable.cpython-37.pyc*와 같이 파일에 대한 긴 경로 이름이 나타날 수 있습니다. 일반적으로 폴더 경로의 깊이가 너무 길어서 이 오류가 발생합니다. 이 경우 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` 레지스트리 키를 `1`로 설정하여 긴 경로를 사용하도록 설정합니다. 또는 Python 인터프리터가 설치된 위치를 확인합니다. 해당 위치의 경로가 긴 경우 경로가 짧은 폴더에 다시 설치해 보세요.

## <a name="update-the-function-to-run-predictions"></a>예측을 실행하도록 함수 업데이트

1. 텍스트 편집기에서 *classify/\_\_init\_\_.py*를 열고, 기존 `import` 문 뒤에 다음 줄을 추가하여 표준 JSON 라이브러리 및 *predict* 도우미를 가져옵니다.

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. `main` 함수의 전체 내용을 다음 코드로 바꿉니다.

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    이 함수는 `img`라는 쿼리 문자열 매개 변수의 이미지 URL을 받습니다. 그런 다음, 도우미 라이브러리에서 `predict_image_from_url`을 호출하여 PyTorch 모델을 통해 이미지를 다운로드하고 분류합니다. 그런 다음, 함수에서 결과가 포함된 HTTP 응답을 반환합니다.

    > [!IMPORTANT]
    > 이 HTTP 엔드포인트는 다른 도메인에서 호스팅되는 웹 페이지에서 호출하므로 응답에는 브라우저의 CORS(원본 간 리소스 공유) 요구 사항을 충족하기 위해 `Access-Control-Allow-Origin` 헤더가 포함됩니다.
    >
    > 프로덕션 애플리케이션에서 보안을 강화하기 위해 `*`를 웹 페이지의 특정 원본으로 변경합니다.

1. 변경 내용을 저장한 다음, 종속성 설치가 완료되었다고 가정하면 `func start`를 사용하여 로컬 함수 호스트를 다시 시작합니다. 가상 환경이 활성화된 *start* 폴더에서 호스트를 실행해야 합니다. 그렇지 않으면 호스트가 시작되지만, 함수를 호출할 때 오류가 표시됩니다.

    ```
    func start
    ```

1. 브라우저에서 다음 URL을 열어 Bernese Mountain Dog 이미지의 URL을 사용하여 함수를 호출하고, 반환된 JSON에서 이미지를 Bernese Mountain Dog로 분류하는지 확인합니다.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. 다음 단계에서 호스트를 사용하므로 해당 호스트를 계속 실행합니다.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>로컬 웹앱 프런트 엔드를 실행하여 함수 테스트

다른 웹앱에서 함수 엔드포인트를 호출하는 작업을 테스트하는 간단한 앱이 리포지토리의 *frontend* 폴더에 있습니다.

1. 앞서의 [Python 가상 환경 만들기 및 활성화](#create-and-activate-a-python-virtual-environment)에서 설명한 대로 새 터미널 또는 명령 프롬프트를 열고 가상 환경을 활성화합니다.

1. 리포지토리의 *frontend* 폴더로 이동합니다.

1. Python을 사용하여 HTTP 서버를 시작합니다.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. 브라우저에서 `localhost:8000`으로 이동하고, 다음 사진 URL 중 하나를 텍스트 상자에 입력하거나 공개적으로 액세스할 수 있는 이미지의 URL을 사용합니다.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. **제출**을 선택하여 이미지를 분류할 함수 엔드포인트를 호출합니다.

    ![완성된 프로젝트의 스크린샷](media/machine-learning-pytorch/screenshot.png)

    이미지 URL을 제출할 때 브라우저에서 오류를 보고하면 함수 앱이 실행되고 있는 터미널을 확인합니다. "'PIL' 모듈을 찾을 수 없습니다."와 같은 오류가 표시되면 이전에 만든 가상 환경을 먼저 활성화하지 않고 *start* 폴더에서 함수 앱을 시작했을 수 있습니다. 그래도 오류가 계속 표시되면 가상 환경이 활성화된 상태에서 `pip install -r requirements.txt`를 다시 실행하고 오류를 찾습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 전체가 머신에서 로컬로 실행되므로 정리할 Azure 리소스 또는 서비스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 PyTorch 모델을 사용하여 이미지를 분류하기 위해 Azure Functions에서 HTTP API 엔드포인트를 빌드하고 사용자 지정하는 방법을 알아보았습니다. 또한 웹앱에서 API를 호출하는 방법도 알아보았습니다. Azure Functions에서 제공하는 서버리스 컴퓨팅 모델에서 실행하는 동안 이 자습서의 기법을 사용하여 모든 복잡한 API를 빌드할 수 있습니다.

참고 항목:

- [Visual Studio Code를 사용하여 Azure에 함수 배포](https://code.visualstudio.com/docs/python/tutorial-azure-functions)
- [Azure Functions Python 개발자 가이드](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Azure CLI 가이드를 사용하여 Azure Functions에 함수 배포](./functions-run-local.md#publish)
