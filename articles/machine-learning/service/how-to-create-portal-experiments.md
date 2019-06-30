---
title: 만들고 실험 포털에서 탐색
titleSuffix: Azure Machine Learning service
description: 만들기 및 자동화 된 machine learning 실험 포털을 관리 하는 방법을 알아봅니다
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 714283628e1b2ac445d36d0b07fe299b589a1cf0
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312813"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>작성 및 자동화 된 machine learning 실험 (미리 보기)는 Azure 포털을 탐색 합니다.

 이 문서에서는 만들고, 실행, 자동화 된 machine learning 실험 코드를 전혀 없이 Azure portal에서 탐색 하는 방법을 알아봅니다. 자동화 된 기계 학습 machine learning 모델을 신속 하 게 생성할 수 있습니다 특정 데이터에 사용할 최상의 알고리즘 선택 프로세스를 자동화 합니다. [자동화 된 기계 학습에 자세히 알아보려면](concept-automated-ml.md)합니다.

 많은 코드를 기반으로 하는 환경을 원한다 면 수도 있습니다 [자동화 된 machine learning 실험에서 Python 구성](how-to-configure-auto-train.md) 사용 하 여 합니다 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* Azure Machine Learning 서비스 작업 영역. 참조 [Azure Machine Learning 서비스 작업 영역 만들기](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)합니다.

## <a name="get-started"></a>시작하기

작업 영역의 왼쪽된 창으로 이동 합니다. 제작 (미리 보기) 섹션에서 자동화 된 Machine Learning을 선택 합니다.

![Azure portal 탐색 창](media/how-to-create-portal-experiments/nav-pane.png)

 자동화 된 Machine Learning을 사용 하 여 모든 실험을 수행 하 여 처음 이면 다음 표시 됩니다.

![Azure 포털 실험 방문 페이지](media/how-to-create-portal-experiments/landing-page.png)

그렇지 않으면 모든 자동화 된 machine learning 실험에서 SDK를 사용 하 여 만든 라이선스 포함에 대 한 개요를 사용 하 여 자동화 된 machine learning 대시보드에 표시 됩니다. 다음 필터링 할 수 있습니다 및 날짜별으로 실행 하 고 탐색, 이름, 실험 및 상태를 실행 합니다.

![Azure 포털 실험 대시보드](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>실험 만들기

다음 폼을 채우는 데 실험 만들기 단추를 선택 합니다.

![실험 양식 만들기](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. 실험 이름을 입력 합니다.

1. 데이터 프로 파일링 및 교육 작업에 대 한 계산을 선택 합니다. 기존에 계산의 목록은입니다 드롭다운 목록에서 사용할 수 있습니다. 새 계산을 만들려면 3 단계의 지침을 따릅니다.

1. 만들기를 열려면 새 계산 단추를 선택 합니다 창 아래 및이 실험에 대 한 계산 컨텍스트를 구성 합니다.

    ![실험에 대 한 새 계산 만들기](media/how-to-create-portal-experiments/create-new-compute.png)

    필드|설명
    ---|---
    계산 이름| 계산 컨텍스트를 식별 하는 고유한 이름을 입력 합니다.
    가상 머신 크기| 컴퓨터에 대 한 가상 머신 크기를 선택 합니다.
    추가 설정| *최소 노드*: 컴퓨터에 대 한 최소 노드 수를 입력 합니다. AML 계산에 대 한 노드의 최소 수는 0입니다. 데이터 프로 파일링을 사용 하려면 하나 이상의 노드가 있어야 합니다. <br> *최대 노드*: 컴퓨터에 대 한 최대 노드 수를 입력 합니다. 기본값은 6 개 노드는 AML Compute에 대 한 합니다.

      새 컴퓨터 만들기를 시작 하려면 선택 **만들기**합니다. 몇 분 정도 걸릴 수 있습니다.

      >[!NOTE]
      > 계산 이름 선택/만든 계산 인지 표시 됩니다 *프로 파일링이 활성화*합니다. (데이터 프로 파일링에 대 한 자세한 내용은 7b 참조).

1. 데이터에 대 한 저장소 계정을 선택 합니다. 공개 미리 보기 로컬 파일 업로드 및 Azure Blob Storage 계정에만 지원합니다.

1. 저장소 컨테이너를 선택 합니다.

1. 저장소 컨테이너에서 데이터 파일을 선택 하거나 컨테이너에 로컬 컴퓨터에서 파일을 업로드 합니다.

    ![실험에 대 한 데이터 파일 선택](media/how-to-create-portal-experiments/select-file.png)

1. 이 실험에 대 한 데이터를 추가로 구성 하는 미리 보기 및 프로필 탭을 사용 합니다.

    1. 미리 보기 탭에서 데이터에 헤더를 포함 하는 경우 되도록 하 고 학습에 사용 하기 위한 기능 (열)을 선택 합니다 **포함 된** 각 기능 열에서 단추를 전환 합니다.

        ![데이터 미리 보기](media/how-to-create-portal-experiments/data-preview.png)

    1. 프로필 탭에서 볼 수 있습니다 합니다 [데이터 프로필](#profile) 기능 뿐만 아니라 배포, 유형 및 각 요약 통계 (평균, 중앙값, 최대/최소, 및 등)로 합니다.

        ![데이터 프로필 탭](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > 계산 컨텍스트는 경우 다음 오류 메시지가 나타납니다 **되지** 프로 파일링을 사용 합니다. *데이터 프로 파일링 에서만 사용 가능 이미 실행 중인 계산 대상에 대 한*합니다.

1. 학습 작업 유형 선택: 분류, 회귀 또는 예측 합니다.

1. 대상 열을 선택 합니다. 예측을 수행 하려고 하는 열입니다.

1. 예측:
    1. 시간 열을 선택 합니다. 이 열에 사용할 데이터를 포함 합니다.
    1. 예측된 기간을 선택 합니다. 얼마나 많은 시간 (분/시간/일/주/월/년) 단위 될 것임을 나타내려면 모델 미래를 예측할 수 있습니다. 할수록 모델은 덜 정확 하 게 될 것으로, 예측 해야 합니다. [에 대 한 예측 및 자세한 예측 기간](how-to-auto-train-forecast.md)합니다.

1. (선택 사항) 고급 설정: 추가 설정을 사용 하면 교육 작업을 더 잘 제어할 수 있습니다.

    고급 설정|설명
    ------|------
    기본 메트릭| 모델 점수 매기기에 사용 되는 주요 메트릭. [모델 메트릭에 대 한 자세한](how-to-configure-auto-train.md#explore-model-metrics)합니다.
    종료 기준| 이러한 조건 중 하나라도 충족 되 면 전체 완료 되기 전에 교육 작업을 종료 합니다. <br> *작업 시간 (분)을 학습*: 학습 작업을 실행할 수 있도록 기간입니다.  <br> *최대 반복 횟수*: 파이프라인 (반복) 교육 작업에서 테스트의 최대 수입니다. 작업에 지정 된 반복 횟수 이상 실행 되지 않습니다. <br> *메트릭 점수 임계값*:  모든 파이프라인에 대 한 최소 메트릭 점수입니다. 이 통해 정의 된 대상 메트릭에 연결 하려는 경우 있습니다 수행 하지 시간이 더 필요한 것 보다 교육 작업에 있습니다.
    전처리| 자동화 된 기계 학습을 수행한 전처리을 사용할지를 선택 합니다. 전처리 자동 데이터 정리, 준비 하 고 변환 가상 기능을 생성할 포함 되어 있습니다. [전처리 하는 방법에 대 한 자세한](#preprocess)합니다.
    유효성 검사| 학습 작업에 사용 하 여 교차 유효성 검사 옵션 중 하나를 선택 합니다. [교차 유효성 검사에 대 한 자세한 내용은](how-to-configure-auto-train.md)합니다.
    동시성| 다중 코어 계산을 사용 하는 경우 사용 하려는 다중 코어 한도 선택 합니다.
    차단 된 알고리즘| 학습 작업에서 제외 하려면 알고리즘을 선택 합니다.

   ![고급 설정 양식](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> 필드에 대 한 자세한 내용은 정보 도구 팁을 클릭 합니다.

<a name="profile"></a>

### <a name="data-profiling"></a>데이터 프로 파일링

데이터 집합은 ML 준비 여부를 확인 하려면 데이터 집합에서 다양 한 요약 통계를 가져올 수 있습니다. 숫자가 아닌 열의 경우 min, max 및 오류 수와 같은 기본 통계 포함 됩니다. 숫자 열에 대 한 예상된 변 위치와 통계 분을 검토할 수 있습니다. 특히, 데이터 프로필 포함 됩니다.

* **기능**: 요약 되는 열의 이름입니다.

* **프로필**:는 인라인 시각화 유형을 기반으로 유추 합니다. 예를 들어, 문자열, 부울 및 날짜는 경우 값의 개수, 소수 자릿수 (숫자)가 히스토그램의 근사치를 계산 하는 동안 이 옵션을 사용 하면 데이터의 분포에 대 한 빠른 이해를 얻을 수 있습니다.

* **배포 입력**: 열 내의 형식에 대 한 인라인 값 카운트 합니다. Null 자체 형식 되므로이 시각화 홀수 또는 누락 된 값을 검색 하는 데 유용 합니다.

* **형식**: 유추 된 형식의 열입니다. 가능한 값: 문자열, 부울, 날짜 및 10 진수입니다.

* **Min**: 열의 최소값입니다. 빈 항목 형식이 없는 내재 된 순서 (예: 부울) 기능에 대 한 표시 됩니다.

* **최대**: 열의 최대 값입니다. "최소값"와 같은 기능과 관련이 없는 형식에 대 한 빈 항목이 나타납니다.

* **개수**: 열에 누락 된 및 누락 되지 않은 항목의 총 수입니다.

* **개수를 누락 되지**: 누락 된 열에 있는 항목의 수입니다. 빈 문자열 및 오류는 값을 오류로 처리 "하지 누락 수입니다."에 기여 하지는 않으므로

* **변 위치** (간격 0.1, 1, 5, 25, 50, 75, 95, 99 및 99.9%): 데이터 분포의 의미를 제공 하려면 각 변 위치에 있는 대략적인된 값입니다. 빈 항목 형식과 관련이 없는 기능에 대 한 표시 됩니다.

* **의미**: 열의 산술 평균입니다. 빈 항목 형식과 관련이 없는 기능에 대 한 표시 됩니다.

* **표준 편차**: 열의 표준 편차입니다. 빈 항목 형식과 관련이 없는 기능에 대 한 표시 됩니다.

* **분산**: 열에 대 한 분산 합니다. 빈 항목 형식과 관련이 없는 기능에 대 한 표시 됩니다.

* **왜곡도**: 열의 왜곡도 합니다. 빈 항목 형식과 관련이 없는 기능에 대 한 표시 됩니다.

* **첨도**: 열의 첨도 합니다. 빈 항목 형식과 관련이 없는 기능에 대 한 표시 됩니다.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>전처리 고급

고급 설정을 설정할 수 있습니다. 실험을 구성할 때 `Preprocess`합니다. 따라서 수행 하는 다음 데이터 전처리 및 기능화 (featurization) 단계를 자동으로 수행 됩니다 의미 합니다.

|전처리&nbsp;단계| 설명 |
| ------------- | ------------- |
|높은 카디널리티 또는 분산 없는 기능 삭제|이러한 기능을 비롯 하 여 모든 값이 누락, 매우 높은 카디널리티 (예를 들어, 해시, Id 또는 Guid) 또는 모든 행에 동일한 값을 사용 하 여 학습 및 유효성 검사 집합에서 삭제 합니다.|
|누락 값 입력|숫자 기능에 대 한 열에서 값의 평균으로 대체 합니다.<br/><br/>범주 기능에 대 한 가장 자주 나타나는 값으로 돌립니다.|
|추가 기능 생성|DateTime 기능: 연도, 월, 일, 요일, 연간 일자, 분기, 연간 주, 시간, 분, 초<br/><br/>텍스트 기능: 유 니 그램, bi-그램, 트라이-문자-그램에 기반 하는 용어 빈도입니다.|
|변환 및 인코딩 |몇 개의 고유 값을 사용 하 여 숫자 기능 범주 기능으로 변환 됩니다.<br/><br/>낮은 카디널리티 범주;에 대해 수행 됩니다 원 핫 인코딩 높은 카디널리티가 하나 핫 해시 인코딩.|
|단어 포함|미리 학습 된 모델을 사용 하 여 문장 벡터를 텍스트 토큰의 벡터를 변환 하는 텍스트 featurizer 합니다. 문서에서 각 단어의 포함 벡터 문서 기능 벡터를 생성 하기 위해 한꺼번에 집계 됩니다.|
|대상 인코딩|범주 기능에 대 한 분류 문제에 대 한 각 클래스에 대 한 클래스 확률 및 회귀 문제에 대 한 평균된 대상 값을 사용 하 여 각 범주를 매핑합니다. 빈도 기반 가중치 및 k 접기 교차 유효성 검사 된 매핑 및 스파스 데이터 범주별으로 인 한 노이즈의 맞춤을 통한 줄어듭니다에 적용 됩니다.|
|텍스트 대상 인코딩|텍스트 입력에 대 한 각 클래스의 확률을 생성 하는 단어의 모음을 사용 하 여 누적된 선형 모델 사용 됩니다.|
|증명 정보 (슬픔)의 가중치|슬픔의 대상 열에 범주 열 상관 관계는 측정값으로 계산합니다. 클래스에서 vs 클래스 아웃 확률의 비율에 대 한 로그로 계산 됩니다. 이 단계는 클래스당 하나의 숫자 기능 열을 출력 하 고 누락 값 및 이상 값 처리를 명시적으로 돌립니다 필요가 없습니다.|
|클러스터 거리|모든 숫자 열에서 k-means 클러스터링 모델을 학습합니다.  출력 k 새로운 기능을 각 클러스터의 중심에 각 샘플의 거리를 클러스터당 새로운 숫자 기능 중 하나입니다.|

## <a name="run-experiment-and-view-results"></a>실험 실행 및 결과 보기

실험을 실행 하려면 시작을 클릭 합니다. 프로세스를 준비 하 고 실험에는 몇 분 정도 걸립니다.

### <a name="view-experiment-details"></a>실험 세부 정보 보기

실험 준비 단계 완료 되 면 실행 세부 정보 화면이 표시 됩니다. 만든 모델의 전체 목록을 제공 합니다. 기본적으로 가장 높은 점수를 매기는 모델 기반 매개 변수 목록의 위쪽에 있습니다. 자세한 모델 아웃 교육 작업 시도 반복 목록 및 차트에 추가 됩니다. 반복 차트를 사용 하 여 지금까지 생성 된 모델에 대 한 메트릭의 빠른 비교를 가져오려고 합니다.

학습 작업 실행을 완료 하려면 각 파이프라인에 대해 잠시를 걸릴 수 있습니다.

![실행된 세부 정보 대시보드](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>교육 실행 세부 정보 보기

성능 메트릭 및 배포 차트와 같은 실행된 세부 정보를 학습을 보려면 출력 모델 중 하나에서 드릴 다운 합니다. [차트에 자세히 알아보려면](how-to-track-experiments.md#understanding-automated-ml-charts)합니다.

![반복 세부 정보](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>모델 배포

에 가장 적합 한 모델을 만든 후 새 데이터에 예측 웹 서비스로 배포 하는 차례입니다.

자동화 된 기계 학습을 지 원하는 코드를 작성 하지 않고 모델을 배포 합니다.

1. 배포에 대 한 몇 가지 옵션이 있습니다. 
    1. 메트릭 조건에 따라 최상의 모델을 배포 하려는 경우 선택 실험에서 설정한 **최상의 모델 배포** 에서 합니다 **실행 세부 정보** 페이지입니다.

        ![배포 모델 단추](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. 특정 모델 반복을 배포 하려는 경우 해당 특정 실행된 세부 정보 페이지를 열고 선택 모델에서 드릴 **배포 모델**합니다.

        ![배포 모델 단추](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. 먼저는 서비스에 모델을 등록 하는 것입니다. "모델을 등록 합니다."를 선택 하 고 등록 프로세스를 완료 될 때까지 기다립니다.

    ![블레이드 모델 배포](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. 모델 등록 되 면 점수 매기기 스크립트 (scoring.py) 및 배포 하는 동안 사용할 환경 스크립트 (condaEnv.yml)를 다운로드할 수 있습니다.

1. 점수 매기기 스크립트 및 환경 스크립트를 다운로드 한 경우로 이동 합니다 **자산** 왼쪽된 탐색 창 및 선택 블레이드 **모델**합니다.

    ![탐색 창 모델](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 등록, 모델을 선택 하 고 "이미지 만들기"를 선택 합니다.

    다음 형식으로 실행된 하는 ID, 반복 번호를 포함 하는 해당 설명에 따라 모델을 식별할 수 있습니다: *< Run_ID > _ < Iteration_number > (_m)*

    ![모델: 이미지 만들기](media/how-to-create-portal-experiments/model-create-image.png)

1. 이미지의 이름을 입력 합니다. 
1. 선택 된 **찾아보기** 는 점수 매기기 (scoring.py) 이전에 다운로드 한 파일을 업로드 하려면 "점수 매기기 파일" 상자 옆에 있는 단추입니다.

1. 선택 된 **찾아보기** 이전에 다운로드 한 (condaEnv.yml) 환경에서 파일을 업로드할 "Conda 파일" 상자 옆에 있는 단추입니다.

    추가 파일 업로드 수 있을 뿐만 아니라 고유한 점수 매기기 스크립트 및 conda 파일을 사용할 수 있습니다. [점수 매기기 스크립트에 자세히 알아보려면](how-to-deploy-and-where.md#script)합니다.

      >[!Important]
      > 파일 이름에서 32 자가 하 여야 하 고 해야 시작 하 고 끝나야 영숫자를 사용 하 여 합니다. 대시, 밑줄, 마침표, 및 사이의 영숫자만 포함할 수 있습니다. 공백은 허용 되지 않습니다.

    ![이미지 만들기](media/how-to-create-portal-experiments/create-image.png)

1. 이미지 만들기를 시작 하려면 "만들기" 단추를 선택 합니다. 완료 되 면 완료 하는 데 몇 분 걸립니다, 그리고 위쪽 막대에서 메시지가 나타납니다.
1. "이미지" 탭으로 이동한 배포 하려는 이미지 옆의 확인란을 "배포 만들기"를 선택 합니다. [배포에 자세히 알아보려면](how-to-deploy-and-where.md)합니다.

    2 가지 방법으로 배포 합니다.
     + Azure Container Instance (ACI)-이 값은 대규모로 운영 배포가 아닌 범용 테스트에 더 사용 됩니다. 에 대 한 하나 이상의 코어에 대 한 값을 입력 해야 _CPU 예약 용량_, 및 적어도 하나의 기가바이트 (GB에 대 한) _메모리 예약 용량_
     + AKS (azure Kubernetes Service))-이 옵션은 대규모로 배포 됩니다. 준비는 AKS 기반 계산 하도록 해야 합니다.

     ![이미지: 배포 만들기](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 완료되면 **만들기**를 선택합니다. 모델을 배포 하면 각 파이프라인 실행을 완료 하려면 몇 분 정도 걸릴 수 있습니다.

1. 정말 간단하죠. 운영 웹 서비스를 예측을 생성 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [자동화 된 기계 학습에 자세히 알아보려면](concept-automated-ml.md) 및 Azure Machine Learning입니다.
* [웹 서비스를 사용 하는 방법을 알아봅니다](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)합니다.
