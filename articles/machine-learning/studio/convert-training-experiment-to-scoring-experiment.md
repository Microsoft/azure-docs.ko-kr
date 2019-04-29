---
title: 배포 모델 준비
titleSuffix: Azure Machine Learning Studio
description: Machine Learning Studio 학습 실험을 예측 실험으로 변환하여 학습된 모델을 웹 서비스로 배포하기 위해 준비하는 방법
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: 2a318edada5cdc4124e221fdc8c441ab323a9289
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751973"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio에서 배포하기 위한 모델을 준비하는 방법

Azure Machine Learning Studio는 예측 분석 모델을 개발한 다음 Azure 웹 서비스로 배포하여 운영하는 데 필요한 도구를 제공합니다.

이를 위해 Studio를 사용하여 모델을 학습하고, 점수를 매기고, 편집하는 *학습 실험*이라는 실험을 만듭니다. 이 학습 실험에 만족하면 사용자 데이터에 대한 점수를 매기도록 구성된 *예측 실험*으로 변환함으로써 모델을 배포할 준비가 됩니다.

이 프로세스의 예를 확인할 수 있습니다 [자습서 1: 신용 위험 예측](tutorial-part1-credit-risk.md)에서 확인할 수 있습니다.

이 문서에서는 학습 실험을 예측 실험으로 변환하는 방법과 예측 실험을 배포하는 방법에 대해 자세히 설명합니다. 이러한 세부 정보를 이해하면 배포된 모델을 더 효과적으로 구성하는 방법을 알 수 있습니다.



## <a name="overview"></a>개요 

학습 실험을 예측 실험으로 변환하는 프로세스는 세 단계로 구성됩니다.

1. 기계 학습 알고리즘 모듈을 학습된 모델로 바꿉니다.
2. 점수 매기기에 필요한 모듈로만 실험을 자릅니다. 학습 실험에는 학습에 필요하지만, 모델을 학습한 후에는 필요 없는 여러 모듈이 포함되어 있습니다.
3. 모델에서 웹 서비스 사용자의 데이터를 받아들이는 방법과 반환할 데이터를 정의합니다.

> [!TIP]
> 학습 실험에서는 사용자 고유의 데이터를 사용하여 모델을 학습하고 점수를 매기는 데 관심이 있지만, 일단 배포되면 사용자는 모델에 새 데이터를 보내고, 모델에서는 예측 결과를 반환합니다. 따라서 학습 실험을 예측 실험으로 변환하여 배포할 준비가 되면 다른 사용자가 이 모델을 어떻게 사용할지를 명심해야 합니다.
> 
> 

## <a name="set-up-web-service-button"></a>웹 서비스 설정 단추
실험 캔버스 아래쪽의 **실행**을 클릭하여 실험을 실행한 후, **웹 서비스 설정** 단추를 클릭하여 **예측 웹 서비스** 옵션을 선택합니다. **웹 서비스 설정**에서는 학습 실험을 예측 실험으로 변환하는 세 단계를 수행합니다.

1. 모듈 팔레트의 **학습된 모델** 섹션(실험 캔버스의 왼쪽)에 학습된 모델을 저장합니다. 그런 다음 기계 학습 알고리즘과 [모델 학습][train-model] 모듈을 저장된 학습 모델로 바꿉니다.
2. 실험을 분석하고, 분명히 학습에만 사용되었지만 더 이상 필요하지 않은 모듈을 제거합니다.
3. _웹 서비스 입력_ 및 _출력_ 모듈을 실험의 기본 위치에 삽입합니다(이러한 모듈은 사용자 데이터를 수락하고 반환합니다).

예를 들어 다음 실험은 샘플 인구 조사 데이터를 사용하여 2클래스 향상된 의사 결정 트리 모델을 학습합니다.

![학습 실험](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

이 실험의 모듈은 기본적으로 네 가지 기능을 수행합니다.

![모듈 함수](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

이 학습 실험을 예측 실험으로 변환하면 이러한 모듈 중 일부는 다음과 같이 더 이상 필요하지 않거나 다른 용도로 사용됩니다.

* **데이터** - 이 샘플 데이터 세트의 데이터는 점수를 매기는 동안 사용되지 않습니다. 웹 서비스 사용자가 점수를 매길 데이터를 제공합니다. 그러나 데이터 형식과 같은 이 데이터 세트의 메타데이터는 학습된 모델에서 사용됩니다. 따라서 이 메타데이터를 제공할 수 있도록 예측 실험에서 데이터 세트를 유지해야 합니다.

* **준비** - 점수 매기기를 위해 제출할 사용자 데이터에 따라 이러한 모듈이 들어오는 데이터를 처리하는 데 필요할 수도 있고 그렇지 않을 수도 있습니다. **웹 서비스 설정** 단추는 이러한 모듈을 수정하지 않지만, 이들을 처리할 방법은 결정해야 합니다.
  
    예를 들어 이 예제에서 샘플 데이터 세트에는 누락된 값이 있을 수 있으므로 이를 처리하기 위해 [누락된 데이터 정리][clean-missing-data] 모듈이 포함되어 있습니다. 또한 샘플 데이터 세트에는 모델을 학습하는 데 필요하지 않은 열도 포함되어 있습니다. 따라서 [데이터 세트의 열 선택][select-columns] 모듈이 데이터 흐름에서 이러한 여분의 열을 제외하기 위해 포함되었습니다. 점수 매기기를 위해 웹 서비스를 통해 제출할 데이터에 누락된 값이 있다는 것을 아는 경우 [누락된 데이터 정리][clean-missing-data] 모듈을 제거할 수 있습니다. 그러나 [데이터 세트의 열 선택][select-columns] 모듈은 학습된 모델에 필요한 데이터 열을 정의하는 데 유용하므로 해당 모듈을 유지해야 합니다.

* **학습** - 이러한 모듈은 모델 학습에 사용됩니다. **웹 서비스 설정**을 클릭하면 이러한 모듈이 학습한 모델을 포함하는 단일 모듈로 바뀝니다. 이 새 모듈은 모듈 팔레트의 **학습한 모델** 섹션에 저장됩니다.

* **점수** - 이 예제에서는 [분할 데이터][split] 모듈을 사용하여 데이터 스트림을 테스트 데이터와 학습 데이터로 나눕니다. 예측 실험에서는 더 이상 학습하지 않으므로 [분할 데이터][split]를 제거할 수 있습니다. 마찬가지로 두 번째 [모델 점수 매기기][score-model] 모듈 및 [모델 평가][evaluate-model] 모듈을 사용하여 테스트 데이터의 결과를 비교하므로 예측 실험에는 이러한 모듈도 필요하지 않습니다. 그러나 나머지 [모델 점수 매기기][score-model] 모듈은 웹 서비스를 통해 점수 결과를 반환하는 데 필요합니다.

다음은 **웹 서비스 설정**을 클릭한 후 이 예제의 변화된 모양입니다.

![변환된 예측 실험](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

**웹 서비스 설정**에서 수행한 작업으로 실험을 웹 서비스로 배포하도록 준비하는 데 충분할 수 있습니다. 그러나 실험에 특정한 몇 가지 추가 작업을 수행할 수도 있습니다.

### <a name="adjust-input-and-output-modules"></a>입력 및 출력 모듈 조정
학습 실험에서는 학습 데이터 집합을 사용한 다음 일부 처리를 수행하여 기계 학습 알고리즘에 필요한 형식의 데이터를 가져왔습니다. 웹 서비스를 통해 받아야 하는 데이터에 이 처리가 필요하지 않으면 무시할 수 있으며, **웹 서비스 입력 모듈**의 출력을 실험의 다른 모듈에 연결할 수 있습니다. 이제 사용자의 데이터가 이 위치의 모델에 도착합니다.

예를 들어 기본적으로 **웹 서비스 설정**은 위 그림과 같이 **웹 서비스 입력** 모듈을 데이터 흐름의 맨 위에 배치합니다. 그러나 다음과 같이 **웹 서비스 입력**을 데이터 처리 모듈 뒤에 수동으로 배치할 수 있습니다.

![웹 서비스 입력 이동](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

이제 웹 서비스를 통해 제공되는 입력 데이터가 전처리 없이 모델 점수 매기기 모듈로 직접 전달됩니다.

마찬가지로 기본적으로 **웹 서비스 설정** 은 웹 서비스 출력 모듈을 데이터 흐름의 맨 아래에 둡니다. 이 예제에서 웹 서비스는 사용자에게 전체 입력 데이터 벡터와 점수 매기기 결과가 포함된 [모델 점수 매기기][score-model] 모듈을 반환합니다.
그러나 다른 모듈을 반환하고 싶은 경우에는 **웹 서비스 출력** 모듈 앞에 모듈을 더 추가할 수 있습니다. 

예를 들어 입력 데이터의 전체 벡터가 아닌 점수 매기기 결과만 반환하려면 [데이터 세트의 열 선택][select-columns] 모듈을 추가하여 점수 매기기 결과를 제외한 모든 열을 제외합니다. 그런 다음, **웹 서비스 출력** 모듈을 [데이터 세트의 열 선택][select-columns] 모듈의 출력으로 이동합니다. 실험은 다음과 같습니다.

![웹 서비스 출력 이동](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>추가 데이터 처리 모듈 추가 또는 제거
점수를 매기는 동안 필요 없다는 것을 알고 있는 추가 모듈이 실험에 있는 경우 이러한 모듈을 제거할 수 있습니다. 예를 들어 **웹 서비스 입력** 모듈을 데이터 처리 모듈 이후의 시점으로 이동했기 때문에 예측 실험에서 [누락된 데이터 정리][clean-missing-data] 모듈을 제거할 수 있습니다.

이제 예측 실험은 다음과 같은 모양입니다.

![추가 모듈 제거](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>선택적 웹 서비스 매개 변수 추가
경우에 따라 서비스에 액세스할 때 웹 서비스 사용자가 모듈의 동작을 변경하도록 허용할 수 있습니다. *웹 서비스 매개 변수* 를 통해 이 작업을 수행할 수 있습니다.

일반적인 예는 배포된 웹 서비스에 액세스할 때 해당 웹 서비스의 사용자가 다른 데이터 원본을 지정할 수 있도록 [데이터 가져오기][import-data] 모듈을 설정하는 것입니다. 또는 다른 대상을 지정할 수 있도록 [데이터 내보내기][export-data] 모듈을 구성하는 것입니다.

웹 서비스 매개 변수를 정의하여 하나 이상의 모듈 매개 변수와 연결하고 이러한 매개 변수가 필수인지 또는 선택 사항인지 지정할 수 있습니다. 웹 서비스의 사용자는 서비스에 액세스할 때 이러한 매개 변수의 값을 제공하며, 이에 따라 모듈 작업이 수정됩니다.

웹 서비스 매개 변수 및 사용 방법에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 매개 변수 사용][webserviceparameters]을 참조하세요.

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>예측 실험을 웹 서비스로 배포
이제 예측 실험이 충분히 준비되었으므로 이를 Azure 웹 서비스로 배포할 수 있습니다. 웹 서비스를 사용하면 사용자가 모델에 데이터를 보낼 수 있으며, 이 경우 모델에서 해당 예측을 반환합니다.

전체 배포 프로세스에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 배포][deploy]를 참조하세요.

[deploy]: publish-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
