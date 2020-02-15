---
title: ML Studio(클래식)란?
titleSuffix: Azure
description: Azure Machine Learning Studio(클래식)는 알고리즘 및 모듈의 사용할 준비가 되어 있는 라이브러리에서 신속하게 모델을 빌드하기 위한 끌어서 놓기 도구입니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: be3a07eb2d4232d27a5b41ddc4258c89b79729bf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169030"
---
# <a name="what-is-machine-learning-studio-classic"></a>Machine Learning Studio(클래식)란?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio(클래식)는 데이터에 대한 예측 분석 솔루션을 빌드, 테스트, 배포할 수 있는 공동 끌어서 놓기 도구입니다. Azure Machine Learning Studio(클래식)는 Excel과 같은 BI 도구 또는 사용자 지정 앱에서 쉽게 사용할 수 있는 웹 서비스로 모델을 게시합니다.

Machine Learning Studio(클래식)는 데이터 과학, 예측 분석, 클라우드 리소스 및 데이터가 만나는 장소입니다.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Machine Learning Studio(클래식) 대화형 작업 영역
예측 분석 모델을 개발하려면 일반적으로 하나 이상의 소스에서 얻은 데이터를 사용하고, 다양한 데이터 조작과 통계 함수를 통해 해당 데이터를 변환 및 분석하고, 결과 세트를 생성합니다. 이와 같은 모델을 개발하는 과정은 반복 프로세스이며, 다양한 함수와 해당 매개 변수를 수정할 때 학습된 효과적인 모델을 마련했다고 만족할 때까지 결과가 수렴됩니다.

Azure Machine Learning Studio(클래식)에서는 예측 분석 모델을 간편하게 빌드, 테스트, 반복할 수 있는 대화형 시각적 작업 영역을 제공합니다. ***데이터 세트***와 분석 ***모듈***을 대화형 캔버스로 끌어서 놓고 함께 연결하여 ***실험***을 생성하고 Machine Learning Studio(클래식)에서 실행합니다. 모델 디자인을 반복하려면 실험을 편집하고 필요에 따라 복사본을 저장하고 실험을 다시 실행합니다. 준비가 되면 ***학습 실험***을 ***예측 실험***으로 변환한 다음 다른 사용자가 모델에 액세스할 수 있도록 ***웹 서비스***로 게시할 수 있습니다.

프로그래밍이 필요하지 않으며, 데이터 세트와 모듈을 시각적으로 연결하여 예측 분석 모델을 구성할 수 있습니다.

![Azure Machine Learning Studio(클래식) 다이어그램: 실험을 만들고, 여러 원본에 대한 데이터 읽고, 점수 데이터를 쓰고, 모델을 작성합니다.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Azure Machine Learning은 Machine Learning Studio(클래식)와 어떻게 다른가요?

[Azure Machine Learning](../overview-what-is-azure-ml.md)은 신속하게 데이터를 준비하고 기계 학습 모델을 학습 및 배포할 수 있도록 SDK **및** Azure Machine Learning 디자이너(미리 보기)를 모두 제공합니다. 디자이너는 Studio(클래식)와 비슷한 끌어서 놓기 환경을 제공합니다. 그러나 Studio(클래식) 전용 컴퓨팅 플랫폼과는 달리, 디자이너는 사용자 고유의 컴퓨팅 리소스를 사용하며 Azure Machine Learning에 완전히 통합됩니다.

다음은 간략하게 비교한 내용입니다.

|| Machine Learning Studio(클래식) | Azure Machine Learning |
|---| --- | --- |
| 끌어서 놓기 인터페이스 | yes | 예 - [Azure Machine Learning 디자이너(미리 보기)](../concept-designer.md) |
| 실험 | 크기 조정 가능(10GB 학습 데이터 제한) | 컴퓨팅 대상으로 크기 조정 |
| 끌어서 놓기 인터페이스를 위한 모듈 | 다수 | 인기 있는 초기 [모듈](../algorithm-module-reference/module-reference.md) 세트|
|컴퓨팅 대상 학습| 전용 컴퓨팅 대상, CPU만 지원| Azure Machine Learning 컴퓨팅(GPU 또는 CPU) 및 Notebook VM 지원<br/>([다른 컴퓨팅은 SDK에서 지원](../concept-compute-target.md#train))|
|컴퓨팅 대상 추론| 전용 웹 서비스 형식, 사용자 지정 불가능 |  Azure Kubernetes Service 및 AML 컴퓨팅 <br/>([다른 컴퓨팅은 SDK에서 지원](../how-to-deploy-and-where.md)) |
| ML 파이프라인 | 지원되지 않음 | [파이프라인](../concept-ml-pipelines.md) 지원됨 |
| MLOps | 기본 모델 관리 및 배포 | 구성 가능한 배포 - 모델 및 파이프라인 버전 관리와 추적 |
| 모델 형식 | 전용 형식, Studio(클래식)만 해당 | 학습 작업 유형에 따른 표준 형식 |
|자동화된 모델 학습 및 하이퍼 매개변수 튜닝 | 예 | 아직 디자이너에 없음 <br/> ([SDK 및 작업 영역 방문 페이지에서 지원됨](../concept-automated-ml.md)) | 

디자이너를 사용해 보려면 [자습서: 디자이너를 사용하여 자동차 가격 예측](../tutorial-designer-automobile-price-train-score.md)을 참조하세요.

> [!NOTE]
> Studio(클래식)에서 만든 모델은 Azure Machine Learning으로 배포하거나 관리할 수 없습니다. 하지만 디자이너에서 만들고 배포한 모델은 Azure Machine Learning 작업 영역을 통해 관리할 수 있습니다.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Machine Learning Studio(클래식) 개요 다이어그램 다운로드
**Microsoft Azure Machine Learning Studio(클래식) 기능 개요** 다이어그램을 다운로드하여 Machine Learning Studio(클래식)의 기능을 개략적으로 봅니다. 옆에 두고 보려면, tabloid 크기(11 x 17인치)로 다이어그램을 인쇄하면 됩니다.

**여기서 다이어그램 다운로드: [Microsoft Azure Machine Learning Studio(클래식) 기능 개요](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Microsoft Azure Machine Learning Studio(클래식) 기능 개요](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Studio(클래식) 실험의 구성 요소
실험은 함께 연결하여 예측 분석 모델을 구성하는 분석 모듈에 데이터를 제공하는 데이터 세트로 구성됩니다. 특히 유효한 실험에는 다음 특성이 포함됩니다.

* 실험에는 하나 이상의 데이터 세트와 하나의 모듈이 있습니다.
* 데이터 세트는 모듈에만 연결될 수 있습니다.
* 모듈은 데이터 세트 또는 다른 모듈에 연결될 수 있습니다.
* 모듈에 대한 모든 입력 포트에는 데이터 흐름에 대한 연결이 포함되어야 합니다.
* 각 모듈에 대한 모든 필수 매개 변수를 설정해야 합니다.

실험을 처음부터 만들거나 기존 샘플 실험을 템플릿으로 사용할 수 있습니다. 자세한 내용은 [샘플 실험을 복사하여 새로운 기계 학습 실험 만들기](sample-experiments.md)를 참조하세요.

실험을 만드는 예는 [Azure Machine Learning Studio(클래식)에서 간단한 실험 만들기](create-experiment.md)를 참조하세요.

예측 분석 솔루션을 만드는 자세한 연습 과정은 [Azure Machine Learning Studio(클래식)를 사용한 예측 솔루션 개발](tutorial-part1-credit-risk.md)을 참조하세요.

### <a name="datasets"></a>데이터 세트
데이터 세트는 모델링 프로세스에서 사용할 수 있도록 Machine Learning Studio(클래식)에 업로드된 데이터입니다. Machine Learning Studio(클래식)에는 실험에 사용할 다양한 샘플 데이터 세트가 포함되고, 필요할 때 추가 데이터 세트를 업로드할 수 있습니다. 포함된 데이터 세트의 몇 가지 예제는 다음과 같습니다.

* **다양한 자동차에 대한 MPG 데이터** - 실린더 수, 마력 등으로 식별되는 자동차에 대한 MPG 값
* **유방암 데이터** - 유방암 진단 데이터
* **산불 데이터** - 포르투갈 북동부에서 발생한 산불 규모

실험을 빌드할 때 캔버스의 왼쪽에 제공되는 데이터 세트 목록에서 선택할 수 있습니다.

Machine Learning Studio(클래식)에 포함된 샘플 데이터 세트의 목록은 [Azure Machine Learning Studio(클래식)에서 예제 데이터 세트 사용](use-sample-datasets.md)을 참조하세요.

### <a name="modules"></a>모듈
모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. Azure Machine Learning Studio(클래식)에는 데이터 가져오기 함수부터 학습, 점수 매기기 및 유효성 검사 프로세스에 이르는 다양한 모듈이 있습니다. 포함된 모듈의 몇 가지 예제는 다음과 같습니다.

* [ARFF로 변환][convert-to-arff] - .NET 직렬화된 데이터 세트를 ARFF(Attribute-Relation File Format) 형식으로 변환합니다.
* [기본 통계 컴퓨팅][elementary-statistics] - 평균, 표준 편차 등의 기본 통계를 컴퓨팅합니다.
* [선형 회귀][linear-regression] - 온라인 기울기 하강 기반 선형 회귀 모델을 만듭니다.
* [모델 점수 매기기][score-model] - 학습된 분류 또는 회귀 모델의 점수를 매깁니다.

실험을 빌드할 때 캔버스의 왼쪽에서 사용할 수 있는 모듈의 목록에서 선택할 수 있습니다.

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택할 때 모듈 매개 변수가 캔버스 오른쪽의 **속성** 창에 표시됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다.

사용 가능한 기계 학습 알고리즘의 대규모 라이브러리를 탐색하는 방법에 대한 도움말은 [Microsoft Azure Machine Learning Studio(클래식)를 위한 알고리즘 선택 방법](algorithm-choice.md)을 참조하세요.

## <a name="deploying-a-predictive-analytics-web-service"></a>예측 분석 웹 서비스 배포
예측 분석 모델이 준비되면 Machine Learning Studio(클래식)에서 곧바로 해당 모델을 웹 서비스로 배포할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포](deploy-a-machine-learning-web-service.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[단계별 빠른 시작](create-experiment.md)을 사용하거나 [샘플 기반 빌드](sample-experiments.md)를 통해 예측 분석 및 기계 학습에 대한 기본 사항을 알아볼 수 있습니다.

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
