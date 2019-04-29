---
title: Python 기계 학습 스크립트 실행
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio에서 Python을 사용 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750824"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio에서 Python 기계 학습 스크립트 실행

Python은 많은 데이터 과학자 들의 도구 상자에 유용한 도구입니다. 데이터 탐색, 기능 추출, 모델 학습 및 유효성 검사 및 배포를 포함 하 여 일반적인 machine learning 워크플로의 모든 단계에서 사용 됩니다.

이 문서에서는 Azure Machine Learning Studio 실험 및 웹 서비스에서 Python 코드를 사용 하는 Python 스크립트 실행 모듈을 사용 하는 방법을 설명 합니다.

## <a name="using-the-execute-python-script-module"></a>Python 스크립트 실행 모듈을 사용 하 여

Studio에서 Python 기본 인터페이스를 사용 하는 것은 [Python 스크립트 실행] [ execute-python-script] 모듈입니다. 최대 세 개의 입력을 수락 하 고 유사한 최대 두 개의 출력을 생성 합니다 [R 스크립트 실행] [ execute-r-script] 모듈입니다. Python 코드를 통해 호출 되는 특별 하 게 명명 된 진입점 함수로 매개 변수 상자를 사용 하 여 입력 `azureml_main`합니다.

![Python 스크립트 모듈 실행](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![모듈 매개 변수 상자에서 python 코드 샘플](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>입력 매개 변수

Python 모듈에 대 한 입력은 Pandas 데이터 프레임으로 노출 됩니다. `azureml_main` 함수 매개 변수로 최대 두 개의 선택적 Pandas Dataframe을 허용 합니다.

입력 포트와 함수 매개 변수 간의 매핑은 위치와 관련됩니다.

- 첫 번째로 연결된 입력 포트는 함수의 첫 번째 매개 변수에 매핑됩니다.
- 두 번째 입력(연결된 경우)은 함수의 두 번째 매개 변수에 매핑됩니다.
- 세 번째 입력이 하는 데 [추가 Python 모듈을 가져올](#import-modules)합니다.

자세한 내용을 보려면 입력된 포트 매개 변수에 매핑되는 방법을의 의미 체계는 `azureml_main` 함수 아래에 표시 됩니다.

![입력된 포트 구성 및 결과 Python 서명 테이블](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>출력 반환 값

합니다 `azureml_main` 함수는 python에서 패키지는 단일 Pandas 데이터 프레임을 반환 해야 합니다 [시퀀스](https://docs.python.org/2/c-api/sequence.html) 튜플, 목록 또는 NumPy 배열과 같은 합니다. 이 시퀀스의 첫 번째 요소는 모듈의 첫 번째 출력 포트에 반환 됩니다. 모듈의 두 번째 출력 포트에 사용 됩니다 [시각화](#visualizations) 반환 값이 필요 하지 않습니다. 이 체계는 다음과 같습니다.

![매핑 포트 매개 변수를 입력 및 출력 포트에 값을 반환 합니다.](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>입력 및 출력 데이터 형식 변환

Studio 데이터 집합은 Panda 데이터 프레임으로 동일 합니다. Studio에서 입력된 데이터 집합의 Pandas 데이터 프레임으로 변환 됩니다 및 출력 DataFrames 결과적으로, Studio 데이터 집합으로 다시 변환 됩니다. 이 변환 프로세스 중에 다음 번역도 수행 됩니다.

 **Python 데이터 형식** | **Studio 번역 프로시저** |
| --- | --- |
| 문자열 및 숫자| 있는 그대로 변환 |
| Pandas 'NA' | '누락 된 값'으로 변환 |
| 인덱스 벡터 | 지원 되지 않는 * |
| 문자열이 아닌 열 이름 | 호출 `str` 열 이름 |
| 중복 된 열 이름 | 숫자 접미사를 추가 합니다. (1), (2), (3), 등입니다.

**Python 함수의 모든 입력된 데이터 프레임에 로드가 64 비트 숫자 인덱스를 0에서 1 뺀 값 행의 수*

## <a id="import-modules"></a>기존 Python 스크립트 모듈 가져오기

Python을 실행 하는 데 사용 하는 백 엔드를 기반으로 [Anaconda](https://store.continuum.io/cshop/anaconda/), 널리 과학 Python 배포를 사용 합니다. 거의 200 개 데이터 중심 워크 로드에 사용 되는 가장 일반적인 Python 패키지를 사용 하 여 제공 됩니다. Studio 현재 Pip 또는 Conda를 설치 하 여 외부 라이브러리를 관리와 같은 패키지 관리 시스템의 사용을 지원 하지 않습니다.  추가 라이브러리를 통합 해야 할 경우, 다음 시나리오를 가이드로 사용 합니다.

일반적인 사용 사례 Studio 실험에 기존 Python 스크립트를 통합 하는 것입니다. 합니다 [Python 스크립트 실행] [ execute-python-script] 모듈에서는 세 번째 입력된 포트에서 Python 모듈을 포함 한 zip 파일을 허용 합니다. 이 파일은 런타임에 입력 프레임워크에서 압축이 풀리며, 파일의 내용은 Python 인터프리터의 라이브러리 경로에 추가됩니다. 그러면 `azureml_main` 진입점 함수가 이러한 모듈을 직접 가져올 수 있습니다. 

예를 들어, 간단한 “Hello, World” 함수를 포함하는 Hello.py라는 파일을 살펴보겠습니다.

![Hello.py 파일의 사용자 정의 함수](./media/execute-python-scripts/figure4.png)

다음으로 Hello.py을 포함하는 Hello.zip이라는 파일을 만듭니다.

![사용자 정의 Python 코드가 포함 된 zip 파일](./media/execute-python-scripts/figure5.png)

데이터 집합으로 스튜디오로 zip 파일을 업로드 합니다. 만들고의 세 번째 입력된 포트에 연결 하 여 Hello.zip 파일에 Python 코드를 사용 하는 실험을 실행 합니다 **Python 스크립트 실행** 다음 그림에 표시 된 것과 같이 모듈입니다.

![Hello.zip Python 스크립트 실행 모듈의 입력으로 사용 하 여 샘플 실험](./media/execute-python-scripts/figure6a.png)

![사용자 정의 Python 코드를 zip 파일로 업로드](./media/execute-python-scripts/figure6b.png)

모듈 출력에는 zip 파일의 패키지가 해제되었으며 `print_hello` 함수가 실행되었음이 표시됩니다.

![사용자 정의 함수를 보여 주는 모듈 출력](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure Storage Blob에 액세스

다음이 단계를 사용 하 여 Azure Blob Storage 계정에 저장 된 데이터를 액세스할 수 있습니다.

1. 다운로드 합니다 [Python 용 Azure Blob Storage 패키지](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) 로컬로 합니다.
1. 데이터 집합으로 Studio 작업 영역에 zip 파일을 업로드 합니다.
1. 사용 하 여 BlobService 개체 만들기 `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. 사용 안 함 **보안 전송 필요** 저장소에서 **구성** 설정 탭

![Azure portal에서 필요한 보안 전송이 사용 하지 않도록 설정](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Python 스크립트 운영 가능화

점수 매기기 실험에서 모든 [Python 스크립트 실행][execute-python-script] 모듈은 웹 서비스로 게시될 때 호출됩니다. 예를 들어 아래 이미지에는 단일 Python 식을 평가 하는 코드가 포함 된 점수 매기기 실험을 보여 줍니다.

![웹 서비스에 대 한 스튜디오 작업 영역](./media/execute-python-scripts/figure3a.png)

![Python Pandas 식](./media/execute-python-scripts/python-script-with-python-pandas.png)

이 실험에서 만든 웹 서비스에는 다음 작업을 수행 합니다.

1. Python 식을 사용할으로 입력 (예: string)
1. Python 인터프리터 Python 식을 보내기
1. 식과 계산된 된 결과 모두 포함 하는 테이블을 반환 합니다.

## <a id="visualizations"></a>시각화 작업

MatplotLib를 사용 하 여 만든 그림에서 반환할 수는 [Python 스크립트 실행][execute-python-script]합니다. 그러나 그림 자동으로 리디렉션되지 이미지에 R을 사용 하는 때와 같이 따라서 사용자 PNG 파일에 모든 그림을 저장 명시적으로 해야 합니다.

MatplotLib에서 이미지를 생성 하려면 다음 단계를 수행 해야 합니다.

1. 기본 Qt 기반 렌더러에서 "AGG"로 백 엔드를 전환 합니다.
1. 새 그림 개체를 만듭니다.
1. 축을 가져오고 모든 플롯을 생성 합니다.
1. 그림을 PNG 파일로 저장 합니다.

이 과정은 Pandas에서 scatter_matrix 함수를 사용 하 여 산 점도 행렬을 만든 다음 이미지에 나와 있습니다.

![MatplotLib 그림을 이미지에 저장 하는 코드](./media/execute-python-scripts/figure-v1-8.png)

![클릭 그림을 보려는 Python 스크립트 실행 모듈에 시각화](./media/execute-python-scripts/figure-v2-9a.png)

![Python 코드를 사용 하 여 샘플 실험을 도표를 시각화 합니다.](./media/execute-python-scripts/figure-v2-9b.png)

다른 이미지에 저장 하 여 여러 수치를 반환 하는 것이 가능 합니다. Studio 런타임 시 모든 이미지를 선택 하 고 시각화에 대 한 연결 합니다.

## <a name="advanced-examples"></a>고급 예제

Studio에 설치 된 Anaconda 환경에는 NumPy, SciPy, Scikits-learn 등 일반적인 패키지를 포함 합니다. 이러한 패키지는 기계 학습 파이프라인의에서 데이터 처리에 대 한 효과적으로 사용할 수 있습니다.

예를 들어 다음 실험과 스크립트를 데이터 집합의 기능 중요도 점수를 계산 Scikits-learn의 앙상블 학습자 사용을 방법을 보여 줍니다. 다른 모델에 공급 하기 전에 감독 모드 기능 선택을 수행 하는 점수를 사용할 수 있습니다.

중요도 점수를 계산하고 점수에 따라 기능의 순서를 지정하는 데 사용되는 Python 함수는 다음과 같습니다.

![점수 별로 기능의 순위를 지정 하려면 함수](./media/execute-python-scripts/figure8.png)

다음 실험에서는 Azure Machine Learning Studio의 “Pima Indian Diabetes” 데이터 세트에 있는 기능의 중요도 점수를 계산하여 반환합니다.

![Python을 사용 하 여 Pima Indian Diabetes 데이터 집합의 순위 기능 실험](./media/execute-python-scripts/figure9a.png)

![Python 스크립트 실행 모듈의 출력의 시각화](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>제한 사항

합니다 [Python 스크립트 실행] [ execute-python-script] 모듈에는 현재 다음과 같은 제한이 있습니다.

### <a name="sandboxed-execution"></a>샌드박스 실행

Python 런타임이 현재 샌드박스가 적용 된 되며 영구적 방식으로 네트워크나 로컬 파일 시스템에 대 한 액세스를 허용 하지 않습니다. 모듈이 완료되면 로컬에 저장된 모든 파일이 격리되어 삭제됩니다. Python 코드는 현재 디렉터리와 하위 디렉터리를 제외하고, 코드가 실행되는 컴퓨터에 있는 대부분의 디렉터리에 액세스할 수 없습니다.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>정교한 개발 및 디버깅 지원이 부족

현재 Python 모듈에서는 Intellisense와 디버깅 같은 IDE 기능을 지원하지 않습니다. 또한 모듈이 런타임에서 실패하는 경우 전체 Python 스택 추적을 사용할 수 있습니다. 그러나 스택 추적은 모듈의 출력 로그에서 확인해야 합니다. 현재로서는 IPython과 같은 환경에서 Python 스크립트를 개발하고 디버그한 다음 모듈에 코드를 가져오는 것이 좋습니다.

### <a name="single-data-frame-output"></a>단일 데이터 프레임 출력

Python 진입점은 단일 데이터 프레임을 출력으로 반환할 수만 있습니다. Studio 런타임 돌아가기 직접 학습 된 모델과 같은 임의의 Python 개체를 반환할 현재 불가능 합니다. 동일한 제한 사항이 있는 [R 스크립트 실행][execute-r-script]과 마찬가지로, 대부분의 경우 개체를 바이트 배열로 만든 다음 데이터 프레임 내부에서 반환할 수 있습니다.

### <a name="inability-to-customize-python-installation"></a>Python 설치를 사용자 지정할 수 없음

현재 사용자 지정 Python 모듈을 추가하는 유일한 방법은 이전에 설명한 zip 파일 메커니즘을 통한 것입니다. 작은 모듈에 대 한 가능한 상태인 동안 작업은 많은 모듈 (특히 네이티브 Dll 사용 하 여 모듈) 또는 많은 수의 모듈에 대 한 번거롭습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Python 개발자 센터](https://azure.microsoft.com/develop/python/)를 참조하세요.

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script