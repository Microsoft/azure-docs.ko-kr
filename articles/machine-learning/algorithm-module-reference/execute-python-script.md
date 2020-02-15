---
title: 'Python 스크립트 실행: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 Python 스크립트 실행 모듈을 사용 하 여 Python 코드를 실행 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 1d82261d4b5c1a66498c33610670d7a38acbb197
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152332"
---
# <a name="execute-python-script-module"></a>Python 스크립트 실행 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 Python 코드를 실행 합니다. Python의 아키텍처 및 디자인 원칙에 대 한 자세한 내용은 [다음 문서](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)를 참조 하세요.

Python을 사용 하면 다음과 같은 기존 모듈에서 현재 지원 되지 않는 작업을 수행할 수 있습니다.

+ `matplotlib`를 사용 하 여 데이터 시각화
+ Python 라이브러리를 사용 하 여 작업 영역에서 데이터 집합 및 모델 열거
+ [데이터 가져오기](./import-data.md) 모듈에서 지원 하지 않는 소스에서 데이터 읽기, 로드 및 조작
+ 사용자 고유의 심층 학습 코드 실행 


Azure Machine Learning는 데이터 처리를 위한 여러 일반적인 유틸리티를 포함 하는 Python의 Anaconda 배포를 사용 합니다. Anaconda 버전이 자동으로 업데이트 됩니다. 현재 버전:
 -  Python 3.6에 대 한 Anaconda 4.5 + 배포 

사전 설치 된 패키지는 다음과 같습니다.
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi = = 2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- 암호화 = = 2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas = = 0.24.2
- Pillow = = 6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex = = 3.7.3
- pyrsistent==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- 요청 = = 2.21.0
- scikit-배우기 = = 0.20.3
- scipy==1.2.1
- setuptools = = 40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- wheel==0.33.1 

 사전 설치 된 목록에 없는 다른 패키지 (예: *scikit)* 를 설치 하려면 스크립트에 다음 코드를 추가 합니다. 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>사용 방법

**Python 스크립트 실행** 모듈에는 시작 지점으로 사용할 수 있는 샘플 python 코드가 포함 되어 있습니다. **Python 스크립트 실행** 모듈을 구성 하려면 **python 스크립트** 텍스트 상자에 실행할 입력 및 python 코드 집합을 제공 합니다.

1. 파이프라인에 **Python 스크립트 실행** 모듈을 추가 합니다.

2. **Dataset1** 에서 입력에 사용할 디자이너의 모든 데이터 집합을 추가 하 고 연결 합니다. Python 스크립트에서이 데이터 집합을 **DataFrame1**로 참조 합니다.

    Python을 사용 하 여 데이터를 생성 하려는 경우 또는 Python 코드를 사용 하 여 데이터를 모듈로 직접 가져오는 경우에는 데이터 집합을 사용 하는 것이 선택 사항입니다.

    이 모듈은 **Dataset2**에서 두 번째 데이터 집합 추가를 지원 합니다. Python 스크립트의 두 번째 데이터 집합을 DataFrame2로 참조 합니다.

    이 모듈을 사용 하 여 로드 하면 Azure Machine Learning에 저장 된 데이터 집합이 자동으로 **pandas** 데이터 프레임으로 변환 됩니다.

    ![Python 입력 맵 실행](media/module/python-module.png)

4. 새 Python 패키지 또는 코드를 포함 하려면 **스크립트 번들**에 이러한 사용자 지정 리소스를 포함 하는 zip 파일을 추가 합니다. **스크립트 번들** 에 대 한 입력은 작업 영역에 이미 업로드 된 압축 파일 이어야 합니다. 

    업로드 된 압축 된 보관 파일에 포함 된 모든 파일은 파이프라인 실행 중에 사용할 수 있습니다. 보관 파일에 디렉터리 구조가 포함 되어 있으면 구조가 유지 되지만 **src** 라는 디렉터리 앞에 경로를 추가 해야 합니다.

5. **Python 스크립트** 텍스트 상자에 올바른 Python 스크립트를 입력 하거나 붙여 넣습니다.

    **Python 스크립트** 텍스트 상자는 주석의 일부 지침과 데이터 액세스 및 출력에 대 한 샘플 코드를 사용 하 여 미리 채워져 있습니다. 이 코드를 편집 하거나 바꾸어야 합니다. 들여쓰기 및 대/소문자 구분에 대 한 Python 규칙을 준수 해야 합니다.

    + 스크립트는이 모듈에 대 한 진입점으로 `azureml_main` 이라는 함수를 포함 해야 합니다.
    + 진입점 함수는 `Param<dataframe1>` 및 `Param<dataframe2>`의 입력 인수를 최대 두 개까지 포함할 수 있습니다.
    + 세 번째 입력 포트에 연결 된 압축 파일은 압축이 풀린 후 Python `sys.path`에도 추가 되는 `.\Script Bundle`디렉터리에 저장 됩니다. 

    따라서 zip 파일에 `mymodule.py`포함 된 경우 `import mymodule`를 사용 하 여 가져옵니다.

    + 두 데이터 집합을 디자이너에 반환할 수 있으며이는 `pandas.DataFrame`형식의 시퀀스 여야 합니다. Python 코드에서 다른 출력을 만들고 Azure storage에 직접 쓸 수 있습니다.

6. 파이프라인을 실행 하거나 모듈을 선택 하 고 **선택 된 실행** 을 클릭 하 여 Python 스크립트만 실행 합니다.

    모든 데이터와 코드는 가상 컴퓨터에 로드 되 고 지정 된 Python 환경을 사용 하 여 실행 됩니다.

## <a name="results"></a>결과

포함 된 Python 코드에 의해 수행 된 모든 계산 결과는 pandas로 제공 되어야 합니다. 데이터 프레임는 자동으로 Azure Machine Learning 데이터 집합 형식으로 변환 되므로 파이프라인의 다른 모듈과 함께 결과를 사용할 수 있습니다.

이 모듈은 두 개의 데이터 집합을 반환 합니다.  
  
+ Python 스크립트에서 첫 번째 반환 된 pandas 데이터 프레임에 의해 정의 된 **결과 데이터 집합 1**

+ Python 스크립트에서 두 번째 반환 된 pandas 데이터 프레임으로 정의 된 **결과 데이터 집합 2**


## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 