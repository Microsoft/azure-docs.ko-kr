---
title: Python 스크립트 실행
titleSuffix: ML Studio (classic) - Azure
description: Python 스크립트 실행 모듈을 사용 하 여 Machine Learning Studio (클래식) 실험 및 웹 서비스에서 Python 코드를 사용 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: ec210fcdf521413438edd256cc3ee988b67f293f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168655"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio에서 Python 기계 학습 스크립트 실행 (클래식)

Python은 다양 한 데이터 과학자 도구 상자에 유용한 도구입니다. 데이터 탐색, 기능 추출, 모델 학습 및 유효성 검사, 배포 등 일반적인 기계 학습 워크플로의 모든 단계에서 사용 됩니다.

이 문서에서는 Python 스크립트 실행 모듈을 사용 하 여 Azure Machine Learning Studio (클래식) 실험 및 웹 서비스에서 Python 코드를 사용 하는 방법을 설명 합니다.

## <a name="using-the-execute-python-script-module"></a>Python 스크립트 실행 모듈 사용

Studio에서 Python (클래식)에 대 한 기본 인터페이스는 [Python 스크립트 실행][execute-python-script] 모듈을 통하는 것입니다. 최대 3 개의 입력을 허용 하 고 [R 스크립트 실행][execute-r-script] 모듈과 유사한 출력을 최대 두 개까지 생성 합니다. Python 코드는 `azureml_main`라는 특수 하 게 명명 된 진입점 함수를 통해 매개 변수 상자에 입력 됩니다.

![Python 스크립트 실행 모듈](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![모듈 매개 변수 상자의 샘플 python 코드](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>입력 매개 변수

Python 모듈에 대 한 입력은 Pandas 데이터 프레임로 노출 됩니다. `azureml_main` 함수는 최대 두 개의 선택적 Pandas 데이터 프레임을 매개 변수로 받아들입니다.

입력 포트와 함수 매개 변수 간의 매핑은 위치와 관련됩니다.

- 첫 번째로 연결된 입력 포트는 함수의 첫 번째 매개 변수에 매핑됩니다.
- 두 번째 입력(연결된 경우)은 함수의 두 번째 매개 변수에 매핑됩니다.
- 세 번째 입력은 [추가 Python 모듈을 가져오는](#import-modules)데 사용 됩니다.

입력 포트가 `azureml_main` 함수의 매개 변수에 매핑되는 방법에 대 한 자세한 의미 체계는 다음과 같습니다.

![입력 포트 구성 및 결과 Python 서명의 표](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>반환 값 출력

`azureml_main` 함수는 튜플, list 또는 NumPy 배열과 같은 Python [시퀀스](https://docs.python.org/2/c-api/sequence.html) 에서 패키지 된 단일 Pandas 데이터 프레임를 반환 해야 합니다. 이 시퀀스의 첫 번째 요소는 모듈의 첫 번째 출력 포트에 반환 됩니다. 모듈의 두 번째 출력 포트는 [시각화](#visualizations) 에 사용 되며 반환 값이 필요 하지 않습니다. 이 스키마는 다음과 같습니다.

![입력 포트를 매개 변수에 매핑하고 출력 포트에 값 반환](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>입력 및 출력 데이터 형식의 변환

스튜디오 데이터 집합은 Panda 데이터 프레임와 다릅니다. 따라서 Studio (클래식)의 입력 데이터 집합은 Pandas 데이터 프레임로 변환 되 고 출력 데이터 프레임는 다시 Studio (클래식) 데이터 집합으로 변환 됩니다. 이 변환 프로세스 중에는 다음과 같은 번역도 수행 됩니다.

 **Python 데이터 형식** | **Studio 변환 프로시저** |
| --- | --- |
| 문자열 및 숫자| 있는 그대로 변환 |
| Pandas ' NA ' | ' 누락 값 '으로 변환 됨 |
| 인덱스 벡터 | 않음 |
| 문자열이 아닌 열 이름 | 열 이름에 대 한 `str` 호출 |
| 중복 열 이름 | 숫자 접미사 (1), (2), (3) 등을 추가 합니다.

*Python 함수의 모든 입력 데이터 프레임 *에는 항상 0부터 행 수에서 1을 뺀 64 비트 숫자 인덱스가 있습니다* .

## <a id="import-modules"></a>기존 Python 스크립트 모듈 가져오기

Python을 실행 하는 데 사용 되는 백 엔드는 널리 사용 되는 과학 Python [Anaconda](https://www.anaconda.com/distribution/)을 기반으로 합니다. 데이터 중심 워크 로드에서 사용 되는 가장 일반적인 Python 패키지의 200에 근접 하 게 제공 됩니다. Studio (클래식)는 현재 Pip 또는 Conda와 같은 패키지 관리 시스템을 사용 하 여 외부 라이브러리를 설치 하 고 관리 하는 것을 지원 하지 않습니다.  추가 라이브러리를 통합 해야 하는 경우 다음 시나리오를 지침으로 사용 합니다.

일반적인 사용 사례는 기존 Python 스크립트를 스튜디오 (클래식) 실험에 통합 하는 것입니다. [Python 스크립트 실행][execute-python-script] 모듈은 세 번째 입력 포트에서 python 모듈을 포함 하는 zip 파일을 허용 합니다. 이 파일은 런타임에 입력 프레임워크에서 압축이 풀리며, 파일의 내용은 Python 인터프리터의 라이브러리 경로에 추가됩니다. 그러면 `azureml_main` 진입점 함수가 이러한 모듈을 직접 가져올 수 있습니다. 

예를 들어, 간단한 “Hello, World” 함수를 포함하는 Hello.py라는 파일을 살펴보겠습니다.

![Hello.py 파일의 사용자 정의 함수](./media/execute-python-scripts/figure4.png)

다음으로 Hello.py을 포함하는 Hello.zip이라는 파일을 만듭니다.

![사용자 정의 Python 코드를 포함 하는 Zip 파일](./media/execute-python-scripts/figure5.png)

Zip 파일을 데이터 집합으로 Studio (클래식)에 업로드 합니다. 그런 다음, 다음 그림에 표시 된 것 처럼 **Python 스크립트 실행** 모듈의 세 번째 입력 포트에 연결 하 여 Hello .zip 파일에서 python 코드를 사용 하는 실험을 만들고 실행 합니다.

![Python 스크립트 실행 모듈에 대 한 입력으로 Hello .zip을 사용한 샘플 실험](./media/execute-python-scripts/figure6a.png)

![Zip 파일로 업로드 된 사용자 정의 Python 코드](./media/execute-python-scripts/figure6b.png)

모듈 출력에는 zip 파일의 패키지가 해제되었으며 `print_hello` 함수가 실행되었음이 표시됩니다.

![사용자 정의 함수를 보여 주는 모듈 출력](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure Storage Blob 액세스

다음 단계를 사용 하 여 Azure Blob Storage 계정에 저장 된 데이터에 액세스할 수 있습니다.

1. [Python 용 Azure Blob Storage 패키지](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) 를 로컬로 다운로드 합니다.
1. Zip 파일을 Studio (클래식) 작업 영역에 데이터 집합으로 업로드 합니다.
1. `protocol='http'`를 사용 하 여 BlobService 개체 만들기

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 저장소 **구성** 설정 탭에서 **보안 전송** 사용 안 함

![Azure Portal에서 보안 전송 사용 안 함](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python 스크립트 운영 가능화

점수 매기기 실험에 사용 되는 모든 [Python 스크립트 실행][execute-python-script] 모듈은 웹 서비스로 게시 될 때 호출 됩니다. 예를 들어 아래 이미지에서는 단일 Python 식을 평가 하는 코드가 포함 된 점수 매기기 실험을 보여 줍니다.

![웹 서비스의 스튜디오 작업 영역](./media/execute-python-scripts/figure3a.png)

![Python Pandas 식](./media/execute-python-scripts/python-script-with-python-pandas.png)

이 실험에서 만든 웹 서비스는 다음 작업을 수행 합니다.

1. Python 식을 입력으로 사용 (문자열)
1. Python 인터프리터에 python 식 보내기
1. 식과 계산 된 결과가 모두 포함 된 테이블을 반환 합니다.

## <a id="visualizations"></a>시각화 작업

MatplotLib를 사용 하 여 만든 그림은 [Python 스크립트 실행][execute-python-script]에서 반환 될 수 있습니다. 그러나 플롯은 R을 사용 하는 경우 이미지에 자동으로 리디렉션되지 않습니다. 따라서 사용자는 모든 플롯을 PNG 파일에 명시적으로 저장 해야 합니다.

MatplotLib에서 이미지를 생성 하려면 다음 단계를 수행 해야 합니다.

1. 기본 Qt 기반 렌더러에서 백 엔드를 "AGG"로 전환 합니다.
1. 새 그림 개체를 만듭니다.
1. 축을 가져오고 모든 플롯을 생성 합니다.
1. 그림을 PNG 파일에 저장 합니다.

이 프로세스는 Pandas에서 scatter_matrix 함수를 사용 하 여 산 점도 행렬을 만드는 다음 그림에 나와 있습니다.

![이미지에 MatplotLib 그림을 저장 하는 코드](./media/execute-python-scripts/figure-v1-8.png)

![Python 스크립트 실행 모듈에서 시각화를 클릭 하 여 그림을 봅니다.](./media/execute-python-scripts/figure-v2-9a.png)

![Python 코드를 사용 하 여 샘플 실험을 위한 플롯 시각화](./media/execute-python-scripts/figure-v2-9b.png)

여러 그림을 여러 이미지에 저장 하 여 반환할 수 있습니다. Studio (클래식) 런타임은 모든 이미지를 선택 하 고 시각화를 위해 연결 합니다.

## <a name="advanced-examples"></a>고급 예제

Studio (클래식)에 설치 된 Anaconda 환경에는 NumPy, SciPy 및 Scikits-learn와 같은 일반적인 패키지가 포함 됩니다. 이러한 패키지는 machine learning 파이프라인에서 데이터를 처리 하는 데 효과적으로 사용할 수 있습니다.

예를 들어 다음 실험 및 스크립트는 Scikits-learn에서 앙상블 학습자를 사용 하는 방법을 보여 줍니다. 데이터 집합에 대 한 기능 중요도 점수를 계산 하는 방법을 알아봅니다. 점수를 사용 하 여 다른 모델로 피드를 전환 하기 전에 감독 된 기능 선택을 수행할 수 있습니다.

중요도 점수를 계산하고 점수에 따라 기능의 순서를 지정하는 데 사용되는 Python 함수는 다음과 같습니다.

![점수를 기준으로 기능 순위를 결정 하는 함수](./media/execute-python-scripts/figure8.png)

다음 실험에서는 Azure Machine Learning Studio (클래식)의 "Pima indian diabetes 인디언 당뇨병" 데이터 집합에 있는 기능의 중요도 점수를 계산 하 고 반환 합니다.

![Python을 사용 하 여 Pima indian diabetes 인디언 당뇨병 데이터 집합의 기능에 대 한 순위를 시험해 보세요.](./media/execute-python-scripts/figure9a.png)

![Python 스크립트 실행 모듈의 출력 시각화](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>제한 사항

[Python 스크립트 실행][execute-python-script] 모듈에는 현재 다음과 같은 제한 사항이 있습니다.

### <a name="sandboxed-execution"></a>샌드박스 실행

Python 런타임은 현재 샌드 박싱 되며 네트워크 또는 로컬 파일 시스템에 대 한 액세스를 영구적으로 허용 하지 않습니다. 모듈이 완료되면 로컬에 저장된 모든 파일이 격리되어 삭제됩니다. Python 코드는 현재 디렉터리와 하위 디렉터리를 제외하고, 코드가 실행되는 컴퓨터에 있는 대부분의 디렉터리에 액세스할 수 없습니다.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>정교한 개발 및 디버깅 지원 부족

현재 Python 모듈에서는 Intellisense와 디버깅 같은 IDE 기능을 지원하지 않습니다. 또한 모듈이 런타임에서 실패하는 경우 전체 Python 스택 추적을 사용할 수 있습니다. 그러나 스택 추적은 모듈의 출력 로그에서 확인해야 합니다. 현재로서는 IPython과 같은 환경에서 Python 스크립트를 개발하고 디버그한 다음 모듈에 코드를 가져오는 것이 좋습니다.

### <a name="single-data-frame-output"></a>단일 데이터 프레임 출력

Python 진입점은 단일 데이터 프레임을 출력으로 반환할 수만 있습니다. 현재는 학습 된 모델 등의 임의의 Python 개체를 스튜디오 (클래식) 런타임에 직접 반환할 수 없습니다. 동일한 제한이 적용 되는 [R 스크립트 실행과][execute-r-script]마찬가지로 개체를 바이트 배열로 pickle 하 고 데이터 프레임 내에서 반환 하는 경우가 많습니다.

### <a name="inability-to-customize-python-installation"></a>Python 설치를 사용자 지정할 수 없음

현재 사용자 지정 Python 모듈을 추가하는 유일한 방법은 이전에 설명한 zip 파일 메커니즘을 통한 것입니다. 이는 작은 모듈에 사용할 수 있지만, 많은 모듈 (특히 네이티브 Dll이 있는 모듈) 또는 많은 수의 모듈에 대해 복잡 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Python 개발자 센터](https://azure.microsoft.com/develop/python/)를 참조하세요.

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
