---
title: VS Code를 사용 하 여 Kusto 쿼리 언어 인라인 Python 디버그-Azure 데이터 탐색기
description: VS Code를 사용 하 여 KQL (Kusto query language) 인라인 Python을 디버그 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444463"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>VS Code를 사용 하 여 Kusto 쿼리 언어 인라인 Python 디버그

Azure 데이터 탐색기는 [python () 플러그 인](/azure/kusto/query/pythonplugin)을 사용 하 여 Kusto 쿼리 언어에 포함 된 python 코드 실행을 지원 합니다. 플러그 인 런타임은 격리 된 보안 Python 환경에서 샌드박스에서 호스팅됩니다. Python () 플러그 인 기능은 Kusto 쿼리 언어 기본 기능을 확장 하 여 OSS Python 패키지의 대규모 보관을 확장 합니다. 이 확장을 사용 하면 기계 학습, 인공 지능, 통계 및 시계열과 같은 고급 알고리즘을 쿼리의 일부로 실행할 수 있습니다.

Kusto 쿼리 언어 도구는 Python 알고리즘을 개발 하 고 디버깅 하는 데 편리 하지 않습니다. 따라서 Jupyter, PyCharm, VS 또는 VS Code와 같은 즐겨 사용 하는 Python 통합 개발 환경에서 알고리즘을 개발 합니다. 알고리즘이 완료 되 면 복사 하 여 KQL에 붙여넣습니다. 이 워크플로를 개선 하 고 간소화 하기 위해 Azure 데이터 탐색기는 KQL 인라인 Python 코드를 작성 하 고 디버그할 수 있도록 Kusto 탐색기나 웹 UI 클라이언트와 VS Code 간의 통합을 지원 합니다. 

> [!NOTE]
> 이 워크플로는 비교적 작은 입력 테이블을 디버그 하는 데만 사용할 수 있습니다 (최대 2MB). 따라서 디버깅을 위해 입력을 제한 해야 할 수 있습니다.  많은 테이블을 처리 해야 하는 경우 `| take`, `| sample`또는 `where rand() < 0.x`를 사용 하 여 디버깅 하도록 제한 합니다.

## <a name="prerequisites"></a>필수 조건

1. Python [Anaconda 배포](https://www.anaconda.com/distribution/#download-section)를 설치 합니다. **고급 옵션**에서 **내 경로 환경 변수에 Anaconda 추가**를 선택 합니다.
2. [Visual Studio Code](https://code.visualstudio.com/Download)를 설치합니다.
3. [Visual Studio Code 용 Python 확장을](https://marketplace.visualstudio.com/items?itemName=ms-python.python)설치 합니다.

## <a name="run-your-query-in-your-client-application"></a>클라이언트 응용 프로그램에서 쿼리를 실행 합니다.

1. 클라이언트 응용 프로그램에서 인라인 Python을 포함 하는 쿼리를 `set query_python_debug;` 접두사로 사용 합니다.
1. 쿼리를 실행합니다.
    * Kusto 탐색기: VS Code *debug_python py* 스크립트를 사용 하 여 자동으로 시작 됩니다.
    * Kusto 웹 UI: 
        1. Py, *df*및 *kargs*를 다운로드 하 고 *debug_python*저장 합니다. 창에서 **허용**을 선택 합니다. 선택한 디렉터리에 파일을 **저장** 합니다. 

            ![인라인 python 파일을 다운로드 하는 웹 UI](media/debug-inline-python/webui-inline-python.png)

        1. Py를 마우스 오른쪽 단추로 클릭 하 고 VS code를 사용 하 여 열기를 *debug_python* 클릭 합니다. 
        Py 스크립트에는 KQL 쿼리의 입력 데이터 프레임 및 *kargs* *에서 매개* 변수의 사전을 초기화 하는 템플릿 코드가 접두사로 추가 된 인라인 python 코드가 포함 됩니다. *debug_python*    
            
1. VS code에서 VS code 디버거를 시작 합니다. **디버그** > **디버깅 시작 (F5)** 을 선택 하 고 **Python** 구성을 선택 합니다. 디버거가 시작 되 고 자동으로 중단점이 인라인 코드를 디버깅 합니다.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS Code에서 인라인 Python 디버깅은 어떻게 작동 하나요?

1. 필요한 `| evaluate python()` 절에 도달할 때까지 쿼리가 서버에서 구문 분석 되 고 실행 됩니다.
1. Python 샌드박스는 호출 되지만 코드를 실행 하는 대신 입력 테이블, 매개 변수 사전 및 코드를 직렬화 하 고 다시 클라이언트에 보냅니다.
1. 이 세 개체는 선택 된 디렉터리 (웹 UI) 또는 클라이언트% TEMP% 디렉터리 (Kusto Explorer)에서 py, *kargs* *debug_python 및* 의 세 파일에 *저장 됩니다.*
1. VS code가 시작 되 고, 각 파일에서 df 및 kargs를 초기화 하는 접두사 코드를 포함 하는 py 파일로 미리 *debug_python* 로드 되며, KQL 쿼리에 포함 된 python 스크립트가 포함 됩니다.

## <a name="query-example"></a>쿼리 예

1. 클라이언트 응용 프로그램에서 다음 KQL 쿼리를 실행 합니다.

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    결과 표를 참조 하세요.

    | x  | 4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. `set query_python_debug;`를 사용 하 여 클라이언트 응용 프로그램에서 동일한 KQL 쿼리를 실행 합니다.

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code 시작 됨:

    ![VS code 시작](media/debug-inline-python/launch-vs-code.png)

1. VS Code 디버그 콘솔에서 ' result ' 데이터 프레임를 디버그 하 고 인쇄 합니다.

    ![VS 코드 디버그](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python sandbox 이미지와 로컬 설치 간에는 차이점이 있을 수 있습니다. [플러그 인을 쿼리하여 특정 패키지에 대한 샌드박스 이미지를 확인](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)합니다.
