---
title: 기계 학습 알고리즘 치트 시트
titleSuffix: Azure Machine Learning Studio
description: 인쇄 가능한 기계 학습 알고리즘 치트 시트를 사용하면 Azure Machine Learning Studio에서 예측 모델에 적합한 알고리즘을 선택합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 51a743e7578ea5bbc2acb9094bbf704a09f3cd6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752104"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio용 기계 학습 알고리즘 치트 시트

**Azure Machine Learning Studio 알고리즘 치트 시트**를 사용하면 예측 분석 모델에 대한 적합한 알고리즘을 선택할 수 있습니다.

[Azure Machine Learning 스튜디오](https://studio.azureml.net/)에는 ***회귀***, ***분류***, ***클러스터링***, ***이상 감지*** 제품군 등의 대규모 알고리즘 라이브러리가 있습니다. 각 항목은 다양한 유형의 기계 학습 문제를 해결하기 위해 고안되었습니다.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>다운로드: 기계 학습 알고리즘 치트 시트

**여기서 치트 시트 다운로드: [기계 학습 알고리즘 치트 시트(11x17인치)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![기계 학습 알고리즘 치트 시트: 기계 학습 알고리즘을 선택하는 방법을 알아봅니다.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png)

간편하게 보관하면서 알고리즘을 선택할 수 있도록 tabloid 크기로 Machine Learning Studio 알고리즘 치트 시트를 다운로드하여 인쇄합니다.

> [!NOTE]
> 적합한 알고리즘을 선택하기 위해 이 치트 시트를 사용하는 방법, 다양한 유형의 기계 학습 알고리즘 및 사용 방법에 대한 자세한 논의는 [Microsoft Azure Machine Learning Studio에서 알고리즘을 선택하는 방법](algorithm-choice.md)을 참조하세요.

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Machine Learning Studio 알고리즘 치트 시트 관련 참고 사항 및 용어 정의

* 알고리즘 치트 시트에서 제공된 제안 사항은 대략적인 위치 조정 규칙입니다. 제안을 변형하거나 명백하게 위반할 수 있습니다. 시작점을 제안하기 위한 용도로만 제공됩니다. 데이터에 대해 여러 알고리즘을 실행하고 비교해 보세요. 각 알고리즘 및 데이터를 생성 하는 시스템의 원칙을 이해 하는 데 맬웨어에 단순히 있습니다.

* 모든 Machine Learning 알고리즘에는 자체 스타일이나 *귀납적 바이어스*가 있습니다. 특정 문제의 경우 여러 알고리즘이 적절할 수 있으며, 한 알고리즘이 다른 알고리즘보다 더 적합할 수 있습니다. 하지만 어떤 것이 가장 적합한지 항상 미리 알 수 없습니다. 이러한 경우 여러 알고리즘이 참고 자료에 함께 나열됩니다. 적절한 전략은 한 알고리즘을 시도하고, 결과가 만족스럽지 않을 경우 다른 알고리즘을 시도하는 것입니다. 동일한 데이터에 대해 여러 알고리즘을 시도하고 그 결과를 비교하는 실험의 예제는 [Azure AI Gallery](https://gallery.azure.ai/)에서 [다중 클래스 분류자 비교: 문자 인식](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92)을 참조하세요.

* Machine Learning의 세 가지 주요 범주는 **감독 학습**, **자율 학습** 및 **보충 학습**입니다.

  * **감독 학습**에서 각 데이터 요소는 관심 범주 또는 값과 연결되거나 해당 레이블이 지정됩니다.  범주 레이블의 예는 이미지를 'cat' 또는 'dog'로 할당하는 것입니다.  값 레이블의 예는 중고차와 연결된 판매 가격입니다. 감독 학습의 목표는 이와 같은 많은 레이블이 지정된 예제를 연구한 다음 향후 데이터 요소에 대한 예측을 수행할 수 있도록 하는 것입니다. 예를 들어 올바른 동물로 새 사진을 식별하거나 다른 중고 자동차에 정확한 판매 가격을 할당합니다. 이는 널리 사용되고 유용한 기계 학습 유형입니다. [K-평균 클러스터링][k-means-clustering]을 제외한 Azure Machine Learning Studio의 모든 모듈은 감독 학습 알고리즘입니다.

  * **자율 학습**에서 데이터 요소에는 연결된 레이블이 없습니다. 대신, 자율 학습 알고리즘의 목표는 어떤 방식으로든 데이터를 구성하거나 해당 구조를 설명하는 것입니다. 이는 K-Means처럼 클러스터로 그룹화하거나 더 간단하게 표시되도록 복잡한 데이터를 보는 다양한 방법을 찾는 것을 의미할 수 있습니다.

  * **보충 학습**에서 알고리즘은 각 데이터 요소에 대한 응답으로 작업을 선택합니다. 이는 지정 시간 센서 판독값 집합이 한 데이터 요소이며 알고리즘으로 로봇의 다음 동작을 선택해야 하는 로봇 공학의 일반적인 접근 방식입니다. 사물 인터넷의 애플리케이션에 적합한 학습이기도 합니다. 또한 학습 알고리즘은 짧은 시간 후에 결정이 얼마나 효율적이었는지를 나타내는 보상 신호를 받습니다. 이 신호에 따라 알고리즘은 가장 높은 보상을 달성하기 위해 해당 전략을 수정합니다. 현재 Azure Machine Learning Studio에는 보충 학습 알고리즘 모듈이 없습니다.

* **베이지안 분석법**은 통계적으로 독립적인 데이터 요소를 가정합니다. 즉, 한 데이터 요소의 모델링되지 않은 가변성이 다른 데이터 요소와 상호 관련이 없어 예측할 수 없음을 의미합니다. 예를 들어 기록되는 데이터가 다음 지하철이 도착할 때까지 남은 시간(분)인 경우 하루 간격으로 수행된 두 측정값은 통계적으로 독립적입니다. 그러나 1분 간격으로 측정한 두 값은 통계적으로 독립적이지 않습니다. 한 측정값을 보면 다른 측정값을 쉽게 예측할 수 있습니다.

* **승격된 의사 결정 트리 회귀 모델**은 기능 겹침 또는 기능 간의 상호 작용을 활용합니다. 즉, 지정된 데이터 요소에서 한 기능의 값은 다른 기능의 값을 다소 예측합니다. 예를 들어 일일 최고/최저 온도 데이터에서 최저 온도를 알면 최고 온도를 적절하게 추측할 수 있습니다. 두 기능에 포함된 정보는 다소 중복됩니다.

* 기본적으로 다중 클래스 분류자를 사용 하 여 또는 두 클래스 분류자 집합을 결합 하 여 데이터를 두 개 이상의 범주로 분류을 수행할 수 있습니다는 **앙상블**합니다. 앙상블 접근 방식에서는 각 클래스에 대해 별도의 두 클래스 분류자가 있습니다. 각 분류자는 데이터를 두 개의 범주, 즉 "이 클래스" 및 "이 클래스 아님"으로 구분합니다. 그런 다음 이러한 분류자는 데이터 요소의 올바른 할당에 투표합니다. 이는 [One-vs-All Multiclass][one-vs-all-multiclass]의 작동 원리입니다.

* 로지스틱 회귀 및 Bayes 지점 컴퓨터를 포함한 몇 가지 메서드는 **선형 클래스 경계**를 가정합니다. 즉, 클래스 간의 경계가 대략 직선(또는 보다 일반적인 경우 초평면)이라고 가정합니다. 대체는 이는 구분하려고 시도할 때까지 알 수 없는 데이터 특성이지만, 일반적으로 미리 시각화하여 학습할 수 있는 것입니다. 클래스 경계가 매우 불규칙한 경우 의사 결정 트리, 의사 결정 정글, 지원 벡터 컴퓨터 또는 신경망 방법을 유지합니다.

* 각 범주에 대한 **더미 변수**를 만든 다음 범주가 적용되는 경우 1로, 적용되지 않는 경우 0으로 설정하여 신경망을 범주 변수와 함께 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 알고리즘을 설명하고 예제를 제공하는 다운로드 가능한 인포그래픽은 [다운로드 가능한 인포그래픽: 알고리즘 예제를 포함한 Machine Learning 기본 사항](basics-infographic-with-algorithm-examples.md)을 참조하세요.

* Machine Learning Studio에서 사용할 수 있는 모든 기계 학습 알고리즘의 범주별 목록은 Machine Learning Studio Algorithm 및 모듈 도움말에서 [초기화 모델][initialize-model]을 참조하세요.

* Machine Learning Studio의 전체 알고리즘 및 모듈에 대한 알파벳 순서 목록은 Machine Learning Studio 알고리즘 및 모듈 도움말에서 [Machine Learning Studio 모듈의 A-Z 목록][a-z-list]을 참조하세요.



<!-- Module References -->
[a-z-list]: /azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: /azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: /azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: /azure/machine-learning/studio-module-reference/one-vs-all-multiclass
