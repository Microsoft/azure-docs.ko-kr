---
title: Azure에서 기계 학습에 Python 및 TensorFlow 사용
description: 기계 학습 모델에서 Python, TensorFlow 및 Azure Functions를 사용하여 해당 콘텐츠에 따라 이미지를 분류합니다.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: e9bbfd311d6a05d0dd328a63c7d11e14ab0d7e4a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069615"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>자습서: Azure Functions에서 Python 및 TensorFlow를 사용하여 기계 학습 모델 적용

이 문서에서는 기계 학습 모델에서 Python, TensorFlow 및 Azure Functions를 사용하여 해당 콘텐츠에 따라 이미지를 분류하는 방법에 대해 설명합니다. 모든 작업을 로컬로 수행하고 클라우드에서 Azure 리소스를 만들지 않으므로 이 자습서를 수행하는 데 드는 비용이 없습니다.

> [!div class="checklist"]
> * Python에서 Azure Functions를 개발하기 위한 로컬 환경을 초기화합니다.
> * 사용자 지정 TensorFlow 기계 학습 모델을 함수 앱으로 가져옵니다.
> * 개 또는 고양이를 포함하는 것으로 이미지를 분류하는 서버리스 HTTP API를 빌드합니다.
> * 웹앱에서 API를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). Python 3.7.4 및 Python 3.6.x는 Azure Functions를 통해 확인되며, Python 3.8 이상 버전은 아직 지원되지 않습니다.
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- [Visual Studio Code](https://code.visualstudio.com/) 같은 코드 편집기

### <a name="prerequisite-check"></a>필수 구성 요소 확인

1. 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 2.7.1846 버전 이상인지 확인합니다.
1. `python --version`(Linux/MacOS) 또는 `py --version`(Windows)을 실행하여 Python 버전 보고서 3.7.x를 확인합니다.

## <a name="clone-the-tutorial-repository"></a>자습서 리포지토리 복제

1. 터미널 또는 명령 창에서 Git을 사용하여 다음 리포지토리를 복제합니다.

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. 폴더로 이동하여 해당 내용을 확인합니다.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start*는 자습서의 작업 폴더입니다.
    - *end*는 참조에 대한 최종 결과 및 전체 구현입니다.
    - *resources*는 기계 학습 모델 및 도우미 라이브러리를 포함하고 있습니다.
    - *frontend*는 함수 앱을 호출하는 웹 사이트입니다.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Python 가상 환경 만들기 및 활성화

*start* 폴더로 이동하고, 다음 명령을 실행하여 `.venv`라는 가상 환경을 만들고 활성화합니다. Azure Functions에서 지원하는 Python 3.7을 사용해야 합니다.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Python에서 venv 패키지를 Linux 배포에 설치하지 않은 경우 다음 명령을 실행합니다.

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
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


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>TensorFlow 모델 가져오기 및 도우미 코드 추가

해당 콘텐츠를 기준으로 이미지를 분류하도록 `classify` 함수를 수정하려면 Azure Custom Vision Service에서 학습하고 내보낸 미리 작성된 TensorFlow 모델을 사용합니다. 이전에 복제한 샘플의 *resources* 폴더에 포함된 모델은 개 또는 고양이를 포함하고 있는지 여부에 따라 이미지를 분류합니다. 그런 다음, 일부 도우미 코드와 종속성을 프로젝트에 추가합니다.

Custom Vision Service의 체험 계층을 사용하여 사용자 고유의 모델을 빌드하려면 [샘플 프로젝트 리포지토리](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)의 지침을 따르세요.

> [!TIP]
> 함수 앱과 독립적으로 TensorFlow 모델을 호스팅하려는 경우 모델이 포함된 파일 공유를 Linux 함수 앱에 탑재할 수 있습니다. 자세히 알아보려면 [Azure CLI를 사용하여 Python 함수 앱에 파일 공유 탑재](./scripts/functions-cli-mount-files-storage-linux.md)를 참조하세요.

1. *start* 폴더에서 다음 명령을 실행하여 모델 파일을 *classify* 폴더에 복사합니다. 명령에 `\*`을 포함해야 합니다. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. *classify* 폴더에 *model.pb* 및 *labels.txt*라는 파일이 포함되어 있는지 확인합니다. 그렇지 않으면 *start* 폴더에서 명령을 실행했는지 확인합니다.

1. *start* 폴더에서 다음 명령을 실행하여 도우미 코드가 있는 파일을 *classify* 폴더에 복사합니다.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. 이제 *classify* 폴더에 *predict.py*라는 파일이 포함되어 있는지 확인합니다.

1. 텍스트 편집기에서 *start/requirements.txt*를 열고, 도우미 코드에 필요한 다음 종속성을 추가합니다.

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. *requirements.txt*를 저장합니다.

1. *start* 폴더에서 다음 명령을 실행하여 종속성을 설치합니다. 설치하는 데 몇 분 정도 걸릴 수 있으며, 이 기간 동안 다음 섹션의 함수를 계속 수정할 수 있습니다.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    Windows에서 "EnvironmentError: [Errno 2] 해당 파일 또는 디렉터리가 없음으로 인해 패키지를 설치할 수 없습니다."라는 오류가 발생한 후에 *sharded_mutable_dense_hashtable.cpython-37.pyc*와 같이 파일에 대한 긴 경로 이름이 나타날 수 있습니다. 일반적으로 폴더 경로의 깊이가 너무 길어서 이 오류가 발생합니다. 이 경우 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` 레지스트리 키를 `1`로 설정하여 긴 경로를 사용하도록 설정합니다. 또는 Python 인터프리터가 설치된 위치를 확인합니다. 해당 위치의 경로가 긴 경우 경로가 짧은 폴더에 다시 설치해 보세요.

> [!TIP]
> 첫 번째 예측을 수행하기 위해 *predict.py*를 호출하는 경우 `_initialize`라는 함수가 디스크에서 TensorFlow 모델을 로드하고 글로벌 변수에 캐시합니다. 이렇게 캐시하면 이후의 예측 속도가 향상됩니다. 글로벌 변수를 사용하는 방법에 대한 자세한 내용은 [Azure Functions Python 개발자 가이드](functions-reference-python.md#global-variables)를 참조하세요.

## <a name="update-the-function-to-run-predictions"></a>예측을 실행하도록 함수 업데이트

1. 텍스트 편집기에서 *classify/\_\_init\_\_.py*를 열고, 기존 `import` 문 뒤에 다음 줄을 추가하여 표준 JSON 라이브러리 및 *predict* 도우미를 가져옵니다.

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. `main` 함수의 전체 내용을 다음 코드로 바꿉니다.

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    이 함수는 `img`라는 쿼리 문자열 매개 변수의 이미지 URL을 받습니다. 그런 다음, 도우미 라이브러리에서 `predict_image_from_url`을 호출하여 TensorFlow 모델을 통해 이미지를 다운로드하고 분류합니다. 그런 다음, 함수에서 결과가 포함된 HTTP 응답을 반환합니다. 

    > [!IMPORTANT]
    > 이 HTTP 엔드포인트는 다른 도메인에서 호스팅되는 웹 페이지에서 호출하므로 응답에는 브라우저의 CORS(원본 간 리소스 공유) 요구 사항을 충족하기 위해 `Access-Control-Allow-Origin` 헤더가 포함됩니다.
    >
    > 프로덕션 애플리케이션에서 보안을 강화하기 위해 `*`를 웹 페이지의 특정 원본으로 변경합니다.

1. 변경 내용을 저장한 다음, 종속성 설치가 완료되었다고 가정하면 `func start`를 사용하여 로컬 함수 호스트를 다시 시작합니다. 가상 환경이 활성화된 *start* 폴더에서 호스트를 실행해야 합니다. 그렇지 않으면 호스트가 시작되지만, 함수를 호출할 때 오류가 표시됩니다.

    ```
    func start
    ```

1. 브라우저에서 다음 URL을 열어 고양이 이미지의 URL을 사용하여 함수를 호출하고, 반환된 JSON에서 이미지를 고양이로 분류하는지 확인합니다.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. **제출**을 선택하여 이미지를 분류할 함수 엔드포인트를 호출합니다.

    ![완성된 프로젝트의 스크린샷](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    이미지 URL을 제출할 때 브라우저에서 오류를 보고하면 함수 앱이 실행되고 있는 터미널을 확인합니다. "'PIL' 모듈을 찾을 수 없습니다."와 같은 오류가 표시되면 이전에 만든 가상 환경을 먼저 활성화하지 않고 *start* 폴더에서 함수 앱을 시작했을 수 있습니다. 그래도 오류가 계속 표시되면 가상 환경이 활성화된 상태에서 `pip install -r requirements.txt`를 다시 실행하고 오류를 찾습니다.

> [!NOTE]
> 이 모델은 항상 이미지에 포함되어 있는지 여부에 관계없이 이미지의 내용을 고양이 또는 개로 분류하며, 기본적으로 개로 분류합니다. 예를 들어 호랑이와 표범의 이미지는 일반적으로 고양이로 분류되지만, 코끼리, 당근 또는 비행기의 이미지는 개로 분류됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 전체가 머신에서 로컬로 실행되므로 정리할 Azure 리소스 또는 서비스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 TensorFlow 모델을 사용하여 이미지를 분류하기 위해 Azure Functions에서 HTTP API 엔드포인트를 빌드하고 사용자 지정하는 방법을 알아보았습니다. 또한 웹앱에서 API를 호출하는 방법도 알아보았습니다. Azure Functions에서 제공하는 서버리스 컴퓨팅 모델에서 실행하는 동안 이 자습서의 기법을 사용하여 모든 복잡한 API를 빌드할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure CLI 가이드를 사용하여 Azure Functions에 함수 배포](./functions-run-local.md#publish)

참고 항목:

- [Visual Studio Code를 사용하여 Azure에 함수 배포](https://code.visualstudio.com/docs/python/tutorial-azure-functions)
- [Azure Functions Python 개발자 가이드](./functions-reference-python.md)
- [Azure CLI를 사용하여 Python 함수 앱에 파일 공유 탑재](./scripts/functions-cli-mount-files-storage-linux.md)
