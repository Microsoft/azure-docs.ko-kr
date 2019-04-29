---
title: 모델을 웹 서비스를가 하는 방법
titleSuffix: Azure Machine Learning Studio
description: 개요 개발에서 Azure Machine Learning Studio 모델 진행 하는 웹 서비스에 실험 하는 방법에 대 한 메커니즘입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 28bb96099acb800d9095325b8c7b46a6b5124b4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066054"
---
# <a name="how-a-machine-learning-studio-model-progresses-from-an-experiment-to-a-web-service"></a>Machine Learning Studio 모델을 실험에서 웹 서비스에 진행 하는 방법
Azure Machine Learning Studio는 예측 분석 모델을 대신하는 ***실험***을 개발, 실행, 테스트 및 반복할 수 있는 대화형 캔버스를 제공합니다. 다음 작업에 사용할 수 있는 모듈을 매우 다양하게 갖추고 있습니다.

* 실험에 대한 데이터 입력
* 데이터 조작
* 기계 학습 알고리즘을 통한 모델 학습
* 모델 점수 매기기
* 결과 평가
* 최종 값 출력

실험에 만족했으면 사용자가 새 데이터를 전송하고 그 결과를 다시 받을 수 있도록 해당 실험을 ***기존 Azure Machine Learning 웹 서비스*** 또는 ***새 Azure Machine Learning 웹 서비스***로 배포할 수 있습니다.

이 문서에서는 Machine Learning 모델을 통해 개발 실험에서 조작 가능한 웹 서비스까지 진행하는 메커니즘을 간략히 살펴봅니다.

> [!NOTE]
> 다른 방법으로도 기계 학습 모델을 개발 및 배포할 수 있지만 이 문서에서는 Machine Learning Studio를 사용하는 방법을 중점적으로 설명합니다. 예를 들어, R을 사용하여 기존 예측 웹 서비스를 만드는 방법에 대한 논의 사항은 [Build & Deploy Predictive Web Apps Using RStudio and Azure Machine Learning Studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)(RStudio 및 Azure Machine Learning Studio를 사용하여 Predictive Web Apps 빌드 및 배포) 블로그 게시물을 참조하세요.
>
>

Azure Machine Learning Studio는 *예측 분석 모델*을 개발 및 배포하는 데 도움이 되도록 설계되었지만 Studio를 사용하여 예측 분석 모델을 포함하지 않은 실험을 개발할 수도 있습니다. 예를 들어 실험으로 단지 데이터를 입력 및 조작한 후 결과를 출력할 수 있습니다. 예측 분석 실험과 마찬가지로 이러한 비예측 실험을 웹 서비스로 배포할 수 있지만, 이 실험은 기계 학습 모델을 학습하거나 점수를 매기지 않으므로 보다 간단한 프로세스입니다. 일반적으로 Studio를 이런 방식으로 사용하지는 않지만 Studio 작동 방식을 완벽하게 설명할 수 있도록 계속 논의할 것입니다.

## <a name="developing-and-deploying-a-predictive-web-service"></a>예측 웹 서비스 개발 및 배포
다음은 일반적인 솔루션에서 Machine Learning Studio를 사용하여 개발 및 배포하는 데 따르는 단계입니다.

![배포 흐름](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*그림 1 - 일반적인 예측 분석 모델의 단계*

### <a name="the-training-experiment"></a>학습 실험
***학습 실험***은 Machine Learning Studio에서 웹 서비스를 개발하기 위한 초기 단계입니다. 학습 실험의 목적은 기계 학습 모델을 개발, 테스트, 반복하고 최종적으로 학습할 장소를 제공하는 것입니다. 최적의 솔루션을 찾으면서 여러 모델을 동시에 학습할 수도 있으나 실험을 완료한 후에는 하나의 학습 모델을 선택하고 나머지 모델은 실험에서 제거합니다. 예측 분석 실험 개발의 예제는 [Azure Machine Learning Studio의 신용 위험 평가에 대한 예측 분석 솔루션 개발](tutorial-part1-credit-risk.md)을 참조하세요.

### <a name="the-predictive-experiment"></a>예측 실험
학습 실험에서 모델을 학습한 후에 Machine Learning Studio에서 **웹 서비스 설정**을 클릭하여 **예측 웹 서비스**를 선택하면 학습 실험을 ***예측 실험***으로 변환하는 프로세스를 시작합니다. 예측 실험의 목적은 학습된 모델을 사용하여 새 데이터에 점수를 매기며, 최종적으로는 조작 가능한 Azure 웹 서비스가 되도록 하는 것입니다.

이 변환은 다음 단계를 통해 수행됩니다.

* 학습에 사용되는 모듈 집합을 단일 모듈로 변환하고 학습 모델로 저장
* 점수 매기기와 관련이 없는 모든 불필요한 모듈 제거
* 최종 웹 서비스에서 사용할 입력 및 출력 포트 추가

예측 실험을 웹 서비스로 배포할 준비를 위해 더 많은 내용을 변경할 수 있습니다. 예를 들어 웹 서비스에서 결과의 하위 집합만 출력하도록 하려는 경우 출력 포트 앞에 필터링 모듈을 추가할 수 있습니다.

이 변환 프로세스에서 학습 실험은 무시되지 않습니다. 프로세스가 완료되면 Studio에 두 개의 탭이 생깁니다. 하나는 학습 실험용이고, 다른 하나는 예측 실험용입니다. 이렇게 하면 웹 서비스를 배포하기 전에 학습 실험을 변경하고 예측 실험을 다시 빌드할 수 있습니다. 또는 학습 실험의 복사본을 저장하고 실험의 다른 줄을 시작할 수 있습니다.

> [!NOTE]
> **예측 웹 서비스**을 클릭하면 학습 실험을 예측 실험으로 변환하는 자동 프로세스를 시작하며, 대부분의 경우 잘 작동합니다. 동시에 참가할 학습에 대해 여러 경로가 있는 것처럼 학습 실험이 복잡한 경우에는 수동 변환을 선호할 수 있습니다. 자세한 내용은 [Azure Machine Learning Studio에서 배포하기 위한 모델을 준비하는 방법](convert-training-experiment-to-scoring-experiment.md)을 참조하세요.
>
>

### <a name="the-web-service"></a>웹 서비스
예측 실험이 준비되었으면 서비스를 기존 웹 서비스 또는 Azure Resource Manager에 기반한 새 웹 서비스로 배포할 수 있습니다. 모델을 *기존 Machine Learning 웹 서비스*로 배포하여 조작하려면 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[기존]** 를 선택합니다. *새 Machine Learning 웹 서비스*를 배포하려면 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[신규]** 를 선택합니다. 이제 사용자가 웹 서비스 REST API를 통해 데이터를 모델로 전송하고 그 결과를 다시 받을 수 있습니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>비일반적 경우: 비예측 웹 서비스 만들기
실험에서 예측 분석 모델을 학습하지 않은 경우 학습 실험 및 점수 매기기 실험을 모두 만들 필요가 없습니다. 하나의 실험만 있으며 이 실험을 웹 서비스로 배포할 수 있습니다. Machine Learning Studio에서는 사용한 모듈을 분석하여 예측 모델이 실험에 포함되는지를 감지합니다.

실험을 반복한 후 만족한 경우:

1. **웹 서비스 설정**을 클릭하고 **재학습 웹 서비스**를 선택합니다. 입력 및 출력 노드가 자동으로 추가됩니다.
2. **실행**을 클릭합니다.
3. **웹 서비스 배포**를 클릭하고 배포하려는 환경에 따라 **웹 서비스 배포[기존]** 또는 **웹 서비스 배포[신규]** 를 선택합니다.

이제 웹 서비스가 배포되고 예측 웹 서비스처럼 액세스하고 관리할 수 있습니다.

## <a name="updating-your-web-service"></a>웹 서비스 업데이트
이제 실험을 웹 서비스로 배포했습니다. 업데이트할 필요가 있으면 어떻게 하나요?

업데이트해야 하는 대상에 따라 다릅니다.

**입력 또는 출력을 변경하거나 웹 서비스에서 데이터를 조작하는 방법을 수정하려고 합니다.**

모델을 변경하지 않는 대신 웹 서비스에서 데이터를 처리하는 방법만 변경하는 경우에는 예측 실험을 편집한 후 또다시 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[기존]** 또는 **웹 서비스 배포[신규]** 를 선택합니다. 웹 서비스를 중지한 다음 업데이트된 예측 실험을 배포하고서 웹 서비스를 다시 시작합니다.

예를 들면 다음과 같습니다. 예측 실험이 예측된 결과와 함께 입력 데이터의 전체 행을 반환한다고 가정합니다. 웹 서비스에서 결과만 반환하도록 결정할 수 있습니다. 따라서 예측 실험에서 출력 포트 바로 앞에 **프로젝트 열** 모듈을 추가하여 결과 이외의 열을 제외할 수 있습니다. 또다시 **웹 서비스 배포**를 클릭하고 **웹 서비스 배포[기존]** 또는 **웹 서비스 배포[신규]** 를 선택하면 해당 웹 서비스가 업데이트됩니다.

**새 데이터로 모델을 재학습하려고 합니다.**

기계 학습 모델을 유지하려고 하지만 새 데이터로 재학습하려는 경우 두 옵션이 있습니다.

1. **웹 서비스 실행 중 모델 재학습** - 예측 웹 서비스를 실행하면서 모델을 재학습하려는 경우 학습 실험을 두 번 수정하여 ***재학습 실험***으로 만든 후 ***재학습 웹* 서비스**로 배포하면 됩니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](/azure/machine-learning/studio/retrain-machine-learning-model)을 참조하세요.
2. **원래 학습 실험으로 돌아가 다른 학습 데이터를 사용하여 모델 개발** - 예측 실험이 웹 서비스에 연결되지만 학습 실험은 이런 방식으로 직접 연결되지 않습니다. 원래 학습 실험을 수정하고 **웹 서비스 설정**을 클릭하면 *새* 예측 실험을 만들고, 이 실험이 배포될 때 *새* 웹 서비스를 만듭니다. 원래 웹 서비스는 업데이트되지 않습니다.

   학습 실험을 수정해야 하는 경우 해당 실험을 열고 **다른 이름으로 저장**을 클릭하여 복사본을 만듭니다. 이렇게 하면 원래 학습 실험, 예측 실험 및 웹 서비스가 그대로 유지됩니다. 이제 변경 내용을 포함한 새 웹 서비스를 만들 수 있습니다. 새 웹 서비스를 배포했으면 이전 웹 서비스를 중지할지 또는 새 웹 서비스와 함께 실행하도록 유지할지 여부를 결정할 수 있습니다.

**다른 모델을 학습하려고 합니다.**

원래 예측 모델을 변경하고 다른 기계 학습 알고리즘을 선택하는 것처럼 다른 학습 방법 등을 시도하려는 경우에는 모델 재학습을 위해 위에서 설명한 두 번째 절차를 수행해야 합니다. 즉 학습 실험을 열고 **다른 이름으로 저장**을 클릭하여 복사본을 만든 후 모델 개발, 예측 실험 만들기 및 웹 서비스 배포를 새롭게 다시 시작합니다. 그러면 원래 웹 서비스와 관련이 없는 새 웹 서비스가 만들어집니다. 이때 둘 중 하나를 사용할지 또는 둘 다 사용할지를 결정하여 계속 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
개발 및 실험 프로세스에 대한 자세한 내용은 다음 문서를 참조하세요.

* 실험 변환 - [Azure Machine Learning Studio에서 배포하기 위한 모델을 준비하는 방법](convert-training-experiment-to-scoring-experiment.md)
* 웹 서비스 배포 - [Azure Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)
* 모델 재학습 - [프로그래밍 방식으로 Machine Learning 모델 다시 학습](/azure/machine-learning/studio/retrain-machine-learning-model)

전체 프로세스에 대한 예는 다음을 참조하세요.

* [기계 학습 자습서: Azure Machine Learning Studio에서 첫 번째 실험 만들기](create-experiment.md)
* [연습: Azure Machine Learning의 신용 위험 평가에 대한 예측 분석 솔루션 개발](tutorial-part1-credit-risk.md)

