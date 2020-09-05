---
title: AutoML을 사용 하 여 배포 & 모델 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용하여 자동화된 Machine Learning 모델을 만들고, 검토하고, 배포합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: aniththa
ms.reviewer: nibaccam
ms.date: 07/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 09dd444d0d7409ca86955d2854aec82f07db0c4d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185403"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 자동화된 Machine Learning 모델 만들기, 검토 및 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning studio에서 코드를 한 줄 사용 하지 않고 자동화 된 기계 학습 모델을 만들고 탐색 하 고 배포 하는 방법에 대해 알아봅니다.

>[!IMPORTANT]
> Azure Machine Learning Studio의 자동화된 ML 환경은 미리 보기로 제공됩니다. 특정 기능은 지원되지 않거나 기능이 제한될 수 있습니다.

 자동화된 Machine Learning은 특정 데이터에 사용할 가장 적합한 기계 학습 알고리즘을 선택하는 프로세스입니다. 이 프로세스를 통해 기계 학습 모델을 빠르게 생성할 수 있습니다. [자동화된 Machine Learning에 대해 자세히 알아보세요](concept-automated-ml.md).
 
엔드투엔드 예제의 경우 [Azure Machine Learning의 자동화된 ML 인터페이스를 사용하여 분류 모델을 만드는 자습서](tutorial-first-experiment-automated-ml.md)를 사용해 보세요. 

Python 코드 기반 환경의 경우 Azure Machine Learning SDK를 사용하여 [자동화된 Machine Learning 실험을 구성](how-to-configure-auto-train.md)하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* **Enterprise 버전** 형식의 Azure Machine Learning 작업 영역. [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.  기존 작업 영역을 Enterprise 버전으로 업그레이드하려면 [Enterprise 버전으로 업그레이드](how-to-manage-workspace.md#upgrade)를 참조하세요.

## <a name="get-started"></a>시작하기

1. https://ml.azure.com 에서 Azure Machine Learning에 로그인합니다. 

1. 구독 및 작업 영역을 선택합니다. 

1. 왼쪽 창으로 이동합니다. **작성** 섹션 아래에서 **자동화된 ML**을 선택합니다.

[![Azure Machine Learning Studio 탐색 창](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 실험을 처음 수행하는 경우 자동화된 빈 목록과 설명서에 대한 링크가 표시됩니다. 

그렇지 않으면 SDK를 사용하여 만든 실험을 포함하여 최근에 자동화된 Machine Learning 실험의 목록이 표시됩니다. 

## <a name="create-and-run-experiment"></a>실험 만들기 및 실행

1. **+ 새 자동화된 ML 실행**을 선택하고 양식을 채웁니다.

1. 스토리지 컨테이너에서 데이터 세트를 선택하거나 새 데이터 세트를 만듭니다. 데이터 세트는 로컬 파일, 웹 URL, 데이터 저장소 또는 Azure 오픈 데이터 세트에서 만들 수 있습니다. [데이터 집합 생성](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.  

    >[!Important]
    > 학습 데이터 요구 사항:
    >* 데이터는 테이블 형식이어야 합니다.
    >* 예측하려는 값(대상 열)이 데이터에 있어야 합니다.

    1. 로컬 컴퓨터의 파일에서 새 데이터 집합을 만들려면 **+ 데이터 집합 만들기** 를 선택 하 고 **로컬 파일에서를**선택 합니다. 

    1. **기본 정보** 양식에서 데이터 집합에 고유한 이름을 지정 하 고 설명 (선택 사항)을 제공 합니다. 

    1. **다음**을 선택하여 **데이터 저장소 및 파일 선택 양식**을 엽니다. 이 양식에서 데이터 세트를 업로드할 위치 또는 작업 영역에서 자동으로 만들어지는 기본 스토리지 컨테이너를 선택하거나 실험에 사용하려는 스토리지 컨테이너를 선택합니다. 
    
        1. 데이터가 가상 네트워크 뒤에 있는 경우 **유효성 검사 함수 건너뛰기** 를 사용 하도록 설정 하 여 작업 영역에서 데이터에 액세스할 수 있도록 해야 합니다. [네트워크 격리 및 개인 정보](how-to-enable-virtual-network.md#machine-learning-studio)에 대해 자세히 알아보세요. 
    
    1. **찾아보기** 를 선택 하 여 데이터 집합에 대 한 데이터 파일을 업로드 합니다. 

    1. **설정 및 미리 보기** 양식을 검토하여 정확히 지정했는지 확인합니다. 양식은 파일 형식에 따라 지능적으로 채워집니다. 

        필드| Description
        ----|----
        파일 형식| 파일에 저장된 데이터의 레이아웃 및 유형을 정의합니다.
        구분 기호| 일반 텍스트 또는 다른 데이터 스트림에서 별도의 독립적인 지역 간의 경계를 지정하기 위한 하나 이상의 문자입니다.
        Encoding| 데이터 세트를 읽는 데 사용할 문자 스키마 테이블을 식별합니다.
        열 머리글| 데이터 세트의 헤더(있는 경우)가 처리되는 방법을 나타냅니다.
        행 건너뛰기 | 데이터 세트에서 건너뛴 행(있는 경우)의 수를 나타냅니다.
    
        **다음**을 선택합니다.

    1. **스키마** 양식은 **설정 및 미리 보기** 양식의 선택 사항에 따라 지능적으로 채워집니다. 여기서는 각 열의 데이터 형식을 구성하고, 열 이름을 검토하고, 실험에 **포함하지 않을** 열을 선택합니다. 
            
        **다음**을 선택합니다.

    1. **세부 정보 확인** 양식은 이전에 **기본 정보** 및 **설정 및 미리 보기** 양식에 채운 정보를 요약한 것입니다. 프로파일링을 사용하도록 설정된 컴퓨팅을 사용하여 데이터 세트에 대한 데이터 프로필을 만드는 옵션도 있습니다. [데이터 프로파일링](#profile)에 대한 자세한 정보

        **다음**을 선택합니다.
1. 새로 만든 데이터 세트가 표시되면 선택합니다. 또한 데이터 세트 및 샘플 통계 미리 보기도 볼 수 있습니다. 

1. **실행 구성** 양식에서 고유한 실험 이름을 입력합니다.

1. 대상 열을 선택합니다. 이 열은 예측을 수행하려는 열입니다.

1. 데이터 프로파일링 및 학습 작업에 대한 컴퓨팅을 선택합니다. 기존 컴퓨팅 목록은 드롭다운에서 사용할 수 있습니다. 새 컴퓨팅을 만들려면 7단계의 지침을 따릅니다.

1. **새 컴퓨팅 만들기**를 선택하여 이 실험에 대한 컴퓨팅 컨텍스트를 구성합니다.

    필드|Description
    ---|---
    컴퓨팅 이름| 컴퓨팅 컨텍스트를 식별하는 고유한 이름을 입력합니다.
    가상 컴퓨터 우선 순위| 낮은 우선 순위의 가상 머신은 저렴 하지만 계산 노드를 보장 하지 않습니다. 
    가상 머신 유형| 가상 컴퓨터 유형에 대해 CPU 또는 GPU를 선택 합니다.
    가상 머신 크기| 컴퓨팅에 사용할 가상 머신 크기를 선택합니다.
    최소 / 최대 노드| 데이터를 프로파일링하려면 하나 이상의 노드를 지정해야 합니다. 컴퓨팅에 사용할 최대 노드 수를 입력합니다. AML 컴퓨팅의 경우 기본값은 6개 노드입니다.
    고급 설정 | 이러한 설정을 사용 하 여 실험에 사용할 사용자 계정 및 기존 가상 네트워크를 구성할 수 있습니다. 
    
    **만들기**를 선택합니다. 새 컴퓨팅을 만드는 데 몇 분 정도 걸릴 수 있습니다.

    >[!NOTE]
    > 컴퓨팅 이름에는 선택하고 만드는 컴퓨팅에서 *프로파일링을 사용하도록 설정*했는지 여부가 표시됩니다. (자세한 내용은 [데이터 프로파일링](#profile) 섹션을 참조하세요.)

    **다음**을 선택합니다.

1. **작업 유형 및 설정** 양식에서 작업 유형(분류, 회귀 또는 예측)을 선택합니다. 자세한 내용은 [지원 되는 작업 형식](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) 을 참조 하세요.

    1. **분류**의 경우 텍스트 featurizations 사용 되는 심층 학습을 사용 하도록 설정할 수도 있습니다.

    1. **예측** 을 위해 다음을 수행할 수 있습니다. 
    
        1. 심층 학습 사용
    
        1. *시간 열*선택:이 열은 사용할 시간 데이터를 포함 합니다.

        1. *예측*간격 선택: 모델에서 미래를 예측할 수 있는 시간 단위 (분/시간/일/주/월/년) 수를 표시 합니다. 나중에 예측하는 데 더 많은 모델이 필요할수록 정확도가 떨어집니다. [예측 및 예측 구간에 대해 자세히 알아보세요](how-to-auto-train-forecast.md).

1. (선택 사항) 추가 구성 설정 보기: 학습 작업을 더 효율적으로 제어하는 데 사용할 수 있는 추가 설정입니다. 그렇지 않으면 실험 선택 및 데이터를 기반으로 기본값이 적용됩니다. 

    추가 구성|Description
    ------|------
    기본 메트릭| 모델의 점수를 매기는 데 사용되는 기본 메트릭입니다. [모델 메트릭에 대해 자세히 알아보세요](how-to-configure-auto-train.md#primary-metric).
    최상의 모델에 대한 설명 | 권장 되는 최상의 모델의 explainability을 표시 하려면 선택 합니다.
    차단된 알고리즘| 학습 작업에서 제외하려는 알고리즘을 선택합니다.
    종료 조건| 다음 조건 중 하나가 충족되면 학습 작업이 중지됩니다. <br> *학습 작업 시간(시간)* : 학습 작업을 실행할 수 있는 기간입니다. <br> *메트릭 점수 임계값*:  모든 파이프라인에 대한 최소 메트릭 점수입니다. 이렇게 하면 도달하려는 목표 메트릭이 정의되어 있는 경우 학습 작업에 필요한 시간보다 더 많은 시간을 소비하지 않습니다.
    유효성 검사| 학습 작업에 사용할 교차 유효성 검사 옵션 중 하나를 선택합니다. [교차 유효성 검사에 대해 자세히 알아보세요](how-to-configure-cross-validation-data-splits.md#prerequisites).
    동시성| *최대 동시 반복 횟수*: 학습 작업에서 테스트할 최대 파이프라인(반복) 수입니다. 작업이 지정된 반복 횟수를 초과하여 실행되지 않습니다.

1. 필드 기능화 설정 보기: **추가 구성 설정** 양식에서 **자동 기능화** 를 사용 하도록 선택 하는 경우 기본 기능화 기술이 적용 됩니다. **기능화 설정 보기** 에서 이러한 기본값을 변경 하 고 적절 하 게 사용자 지정할 수 있습니다. [Featurizations를 사용자 지정](#customize-featurization)하는 방법을 알아봅니다. 

    ![Azure Machine Learning studio 작업 형식 폼](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>데이터 프로파일링 및 요약 통계

데이터 세트 전체에 대한 다양한 요약 통계를 가져와서 데이터 세트가 ML 준비 상태인지 여부를 확인할 수 있습니다. 숫자가 아닌 열의 경우 최솟값, 최댓값 및 오류 수와 같은 기본 통계만 포함됩니다. 숫자 열의 경우 통계적 모멘트 및 예상 분위수를 검토할 수도 있습니다. 특히 데이터 프로필에 포함되는 항목은 다음과 같습니다.

>[!NOTE]
> 관련이 없는 형식의 기능은 빈 항목으로 표시되어 있습니다.

통계|Description
------|------
기능| 요약되는 열의 이름입니다.
프로필| 유추된 형식을 기반으로 하는 인라인 시각화입니다. 예를 들어 문자열, 술래 및 날짜에는 값 개수가 있고 소수점(숫자)에는 근사 히스토그램이 있습니다. 이를 통해 데이터의 분포를 빠르게 파악할 수 있습니다.
형식 분포| 열 내 유형의 인라인 값 개수입니다. Null은 고유 형식이므로 이 시각화는 홀수 또는 누락된 값을 탐지하는 데 유용합니다.
Type|열의 유추된 형식입니다. 가능한 값은 문자열, 부울, 날짜 및 10진수입니다.
최소값| 열의 최솟값입니다. 고유한 순서(예: 부울)가 없는 형식의 기능은 빈 항목으로 표시됩니다.
최대값| 열의 최댓값입니다. 
개수| 열에서 누락되거나 누락되지 않은 항목의 총 수입니다.
누락되지 않은 수| 열에서 누락되지 않은 항목의 수입니다. 빈 문자열과 오류는 값으로 처리되므로 "누락되지 않은 수"에 영향을 주지 않습니다.
분위수| 데이터 분포를 파악하기 위한 각 분위수의 근사 값입니다.
평균| 열의 산술 평균 또는 평균입니다.
표준 편차| 이 열의 데이터에 대한 분산 또는 편차를 측정한 값입니다.
Variance| 이 열의 데이터가 평균 값에서 분산된 정도를 측정한 값입니다. 
왜곡도| 이 열의 데이터가 정규 분포에서 치우쳐 있는 정도를 측정한 값입니다.
첨도| 이 열의 데이터가 정규 분포와 비교하여 뾰족하게 밀집된 정도를 측정한 값입니다.

## <a name="customize-featurization"></a>기능화 사용자 지정

**기능화** 폼에서 자동 기능화를 사용 하거나 사용 하지 않도록 설정 하 고 실험에 대 한 자동 기능화 설정을 사용자 지정할 수 있습니다. 이 양식을 열려면 [실험 만들기 및 실행](#create-and-run-experiment) 섹션의 10 단계를 참조 하세요. 

다음 표에서는 현재 스튜디오를 통해 사용할 수 있는 사용자 지정 항목을 요약 합니다. 

열| 사용자 지정
---|---
Included | 학습에 포함할 열을 지정 합니다.
기능 유형| 선택한 열에 대 한 값 유형을 변경 합니다.
돌립니다| 데이터에서 누락 된 값을 돌립니다 값을 선택 합니다.

![Azure Machine Learning studio 작업 형식 폼](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>실험 실행 및 결과 보기

**마침**을 선택하여 실험을 실행합니다. 실험 준비 프로세스는 최대 10분 정도 걸릴 수 있습니다. 각 파이프라인에서 실행을 완료하는 데 학습 작업에 2-3분 더 걸릴 수 있습니다.

### <a name="view-experiment-details"></a>실험 세부 정보 보기

**세부 정보** 탭에 **실행 세부 정보** 화면이 열립니다. 이 화면에서는 실행 번호 옆의 위쪽에 상태 표시줄이 포함된 실험 실행 요약이 표시됩니다. 

**모델** 탭에는 메트릭 점수를 기준으로 정렬하여 만든 모델 목록이 있습니다. 기본적으로 선택한 메트릭에 따라 가장 높은 점수를 획득한 모델이 목록 맨 위에 표시됩니다. 학습 작업에서 더 많은 모델을 시도하면 모델이 목록에 추가됩니다. 이 방법을 사용하여 지금까지 생성된 모델에 대한 메트릭을 신속하게 비교할 수 있습니다.

[![실행 세부 정보 대시보드](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>학습 실행 세부 정보 보기

**모델** 탭의 모델 요약 또는 **메트릭** 탭의 성능 메트릭 차트와 같은 학습 실행 세부 정보를 보려면 완성 된 모델을 드릴 다운 합니다. [차트에](how-to-understand-automated-ml.md)대해 자세히 알아보세요.

[![반복 세부 정보](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>모델 배포

최상의 모델이 있으면 이제 새 데이터를 예측하기 위해 이 모델을 웹 서비스로 배포합니다.

자동화된 ML을 사용하면 코드를 작성하지 않고도 모델을 배포할 수 있습니다.

1. 두 가지 배포 옵션은 다음과 같습니다. 

    + 옵션 1: 정의한 메트릭 조건에 따라 최상의 모델을 배포 합니다. 
        1. 실험이 완료 되 면 화면 맨 위에서 **실행 1** 을 선택 하 여 부모 실행 페이지로 이동 합니다. 
        1.  **최적 모델 요약** 섹션에 나열 된 모델을 선택 합니다. 
        1. 창의 왼쪽 위에서 **배포** 를 선택 합니다. 

    + 옵션 2:이 실험에서 특정 모델 반복을 배포 합니다.
        1. **모델** 탭에서 원하는 모델을 선택 합니다.
        1. 창의 왼쪽 위에서 **배포** 를 선택 합니다.

1. **모델 배포** 창을 채웁니다.

    필드| 값
    ----|----
    속성| 배포에 대한 고유한 이름을 입력합니다.
    Description| 이 배포의 용도를 더 잘 식별할 수 있는 설명을 입력합니다.
    컴퓨팅 형식| 배포하려는 엔드포인트 유형으로 *AKS(Azure Kubernetes Service)* 또는 *ACI(Azure Container Instance)* 를 선택합니다.
    컴퓨팅 이름| *AKS에만 적용:* 배포하려는 AKS 클러스터의 이름을 선택합니다.
    인증 사용 | 토큰 기반 또는 키 기반 인증을 허용할지를 선택합니다.
    사용자 지정 배포 자산 사용| 사용자 고유의 채점 스크립트 및 환경 파일을 업로드하려면 이 기능을 사용하도록 설정합니다. [점수 매기기 스크립트에 대해 자세히 알아보세요](how-to-deploy-and-where.md).

    >[!Important]
    > 파일 이름은 32자 미만이어야 하며 영숫자로 시작하고 끝나야 합니다. 대시, 밑줄, 점 및 영숫자를 포함할 수 있습니다. 공백은 허용되지 않습니다.

    *고급* 메뉴에서는 [데이터 수집](how-to-enable-app-insights.md) 및 리소스 사용률 설정과 같은 기본 배포 기능을 제공합니다. 이러한 기본값을 재정의하려면 이 메뉴에서 해당 작업을 수행합니다.

1. **배포**를 선택합니다. 배포가 완료되는 데 약 20분 정도 걸릴 수 있습니다.
    배포가 시작 되 면 **모델 요약** 탭이 나타납니다. 배포 **상태** 섹션에서 배포 진행률을 확인 하세요. 

이제 예측을 생성하는 운영 웹 서비스가 있습니다! [Power BI의 기본 제공 Azure Machine Learning 지원](how-to-consume-web-service.md#consume-the-service-from-power-bi)에서 서비스를 쿼리하여 예측을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [웹 서비스를 사용하는 방법을 알아봅니다](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [자동화된 Machine Learning 결과를 살펴봅니다](how-to-understand-automated-ml.md).
* Azure Machine Learning 및 [자동화된 Machine Learning에 대해 자세히 알아봅니다](concept-automated-ml.md).
