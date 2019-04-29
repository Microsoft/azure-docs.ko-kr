---
title: 감정 분석 모델 만들기
titleSuffix: Azure Machine Learning Studio
description: 텍스트 전처리, N-Gram 또는 특성 해시를 위한 모듈을 사용하여 Azure Machine Learning Studio에서 텍스트 분석 모델을 만드는 방법
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2018
ms.openlocfilehash: 08d62e7a6c9503d415fe144da57eee72ce3bfafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636615"
---
# <a name="create-a-sentiment-analysis-model-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio에서 감정 분석 모델 만들기

Azure Machine Learning Studio를 사용하여 텍스트 분석 모델을 빌드하고 작동할 수 있습니다. 예를 들어 이러한 모델은 문서 분류 또는 정서 분석 문제를 해결하는 데 유용할 수 있습니다.

텍스트 분석 실험에서는 일반적으로 다음을 수행합니다.

1. 텍스트 데이터 세트 정리 및 전처리
2. 전처리된 텍스트에서 숫자 특성 벡터 추출
3. 분류 또는 회귀 모델 학습
4. 모델 점수 매기기 및 유효성 검사
5. 모델을 프로덕션에 배포

이 자습서에서는 Amazon Book Reviews 데이터 세트를 사용하여 감정 분석 모델을 진행하면서 이러한 단계를 알아봅니다(John Blitzer, Mark Dredze 및 Fernando Pereira(Association of Computational Linguistics (ACL), 2007)의 연구 논문, "Biographies, Bollywood, boom-boxes and Blenders: Domain Adaptation for Sentiment Classification" 참조). 이 데이터 세트는 리뷰 점수(1-2 또는 4-5) 및 자유 형식 텍스트로 구성됩니다. 그 목표는 리뷰 점수: 낮음(1-2) 또는 높음(4-5)를 예측하는 것입니다.

Azure AI 갤러리에서 이 자습서에 나오는 실험을 찾을 수 있습니다.

[도서 리뷰 예측](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-1)

[도서 리뷰 예측 - 예측 실험](https://gallery.azure.ai/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>1단계: 텍스트 데이터 세트 정리 및 전처리
리뷰 점수를 범주별 하위 및 상위 버킷으로 나누어 문제를 2클래스 분류로 형식화함으로써 실험을 시작합니다. [메타데이터 편집](https://msdn.microsoft.com/library/azure/dn905986.aspx) 및 [범주 값 그룹화](https://msdn.microsoft.com/library/azure/dn906014.aspx) 모듈을 사용합니다.

![레이블 만들기](./media/text-analytics-module-tutorial/create-label.png)

그런 다음 [텍스트 전처리](https://msdn.microsoft.com/library/azure/mt762915.aspx) 모듈을 사용하여 텍스트를 정리합니다. 이렇게 정리를 수행하면 데이터 세트의 노이즈가 감소하고, 가장 중요 한 기능을 찾는 데 도움이 되며, 최종 모델의 정확도가 높아집니다. 중지 단어("the" 또는 "a"와 같은 일반 단어)와 숫자, 특수 문자, 중복된 문자, 전자 메일 주소 및 URL을 제거합니다. 또한 전처리된 텍스트에서 텍스트를 소문자로 변환하고, 단어를 분류하고, 문장 경계를 검색한 후 "|||" 기호로 표시합니다.

![텍스트 전처리](./media/text-analytics-module-tutorial/preprocess-text.png)

중지 단어의 사용자 지정 목록을 사용하려면 어떻게 해야 할까요? 선택적 입력으로 전달할 수 있습니다. 사용자 지정 C# 구문 정규식을 사용하여 부분 문자열을 바꾸고 음성 부분별로 단어(명사, 동사 또는 형용사)를 제거할 수도 있습니다.

전처리가 완료된 후에는 데이터를 학습 데이터 및 테스트 집합으로 분할합니다.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>2단계: 전처리된 텍스트에서 숫자 특성 벡터 추출
텍스트 데이터에 대한 모델을 작성하려면 일반적으로 자유 형식 텍스트를 숫자 특성 벡터로 변환해야 합니다. 이 예제에서는 [텍스트에서 N-Gram 특성 추출](https://msdn.microsoft.com/library/azure/mt762916.aspx) 모듈을 사용하여 텍스트 데이터를 이러한 형식으로 변환합니다. 이 모듈은 공백으로 구분된 단어 열을 가져온 후 데이터 세트에 나타나는 단어 사전 또는 단어 N-Gram을 계산합니다. 그런 다음 각 단어 또는 N-Gram이 이러한 레코드에 나오는 횟수를 계산하고 그 개수에서 특성 벡터를 만듭니다. 이 자습서에서는 N-Gram 크기를 2로 설정했으므로 특성 벡터에는 단일 단어와 연속된 두 단어의 조합이 포함됩니다.

![N-Gram 추출](./media/text-analytics-module-tutorial/extract-ngrams.png)

N-Gram 개수에 TF*IDF(용어 빈도와 문서 빈도 반비례) 가중치를 적용합니다. 이 접근 방법은 단일 레코드에는 자주 나타나지만 전체 데이터 세트에서는 드물게 발생하는 단어의 가중치를 추가합니다. 다른 옵션으로는 이진, TF 및 그래프 가중이 포함됩니다.

이러한 텍스트 특성은 종종 차원이 매우 높습니다. 예를 들어 모음에 100,000개의 고유 단어가 있는 경우 특성 공간은 100,000개 차원을 가지고, N-Gram이 사용되는 경우 더 큰 공간을 갖습니다. N-Gram 특성 추출 모듈은 차원을 줄일 수 있는 옵션 집합을 제공합니다. 짧거나 긴 단어 또는 너무 드물거나 너무 자주 나오는 단어를 제외하도록 선택하여 유의한 예측 값을 얻을 수 있습니다. 이 자습서에서는 5개보다 적은 레코드 또는 레코드의 80% 이상에서 나타나는 N-Gram을 제외합니다.

또한 특성 선택을 사용하여 예측 대상과 가장 상호 연관성이 높은 특성만 선택할 수 있습니다. 카이제곱 특성 선택을 사용하여 1000개의 특성을 선택합니다. N-Gram 추출 모듈의 올바른 출력을 클릭하여 선택한 단어의 어휘 또는 N-Gram을 확인할 수 있습니다.

N-Gram 특성 추출을 사용하는 대신, 특성 해시 모듈을 사용할 수 있습니다. 그러나 [특성 해시](https://msdn.microsoft.com/library/azure/dn906018.aspx) 는 기본 제공 특성 선택 기능이나 TF* IDF 가중 기능이 없습니다.

## <a name="step-3-train-classification-or-regression-model"></a>3단계: 분류 또는 회귀 모델 학습
이제 텍스트가 숫자 특성 열로 변환되었습니다. 데이터 세트에는 여전히 이전 단계의 문자열 열이 포함되어 있으므로 데이터 세트의 열 선택을 사용하여 제외시킵니다.

[2클래스 로지스틱 회귀](https://msdn.microsoft.com/library/azure/dn905994.aspx) 를 사용하여 목표: 높음 또는 낮음 리뷰 점수를 예측합니다. 현재 텍스트 분석 문제가 일반 분류 문제로 변환되었습니다. Azure Machine Learning Studio에서 사용할 수 있는 도구를 사용하여 모델을 개선할 수 있습니다. 예를 들어 다양한 분류자로 실험하여 얼마나 정확한 결과를 제공하는지 확인하거나, 하이퍼 매개 변수 조정을 사용하여 정확도를 향상시킬 수 있습니다.

![학습 및 점수 매기기](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>4단계: 모델 점수 매기기 및 유효성 검사
학습된 모델의 유효성은 어떻게 검사하나요? 테스트 데이터 세트를 기준으로 점수를 매기고 정확도를 평가합니다. 그러나 모델은 학습 데이터 세트에서 N-Gram의 어휘와 해당 가중치를 학습했습니다. 따라서 어휘를 새로 만드는 대신 테스트 데이터에서 특성을 추출할 때 해당 용어와 가중치를 사용해야 합니다. 따라서 N-Gram 특성 추출 모듈을 실험의 점수 매기기 분기에 추가하고, 학습 분기의 출력 어휘를 연결하고, 어휘 모드를 읽기 전용으로 설정합니다. 또한 최소값을 1개 인스턴스로, 최대값을 100%로 설정하여 빈도별 N-Gram 필터링을 사용하지 않도록 설정한 후 특성 선택을 해제합니다.

테스트 데이터의 텍스트 열이 숫자 특성 열로 변환된 후에는 학습 분기에서와 마찬가지로 이전 단계의 문자열 열을 제외합니다. 그런 다음 모델 점수 매기기 모듈을 사용하여 예측을 하고, 모델 평가 모듈을 사용하여 정확도를 평가합니다.

## <a name="step-5-deploy-the-model-to-production"></a>5단계: 모델을 프로덕션에 배포
모델을 프로덕션에 배포할 준비가 거의 되었습니다. 모델을 웹 서비스로 배포하면 자유 형식 텍스트 문자열을 입력으로 받은 후 예측 "높음" 또는 "낮음"을 반환합니다. 학습한 N-Gram 어휘를 사용하여 텍스트를 특성으로 변환하고, 학습된 회귀 모델을 사용하여 해당 특성에서 예측을 수행합니다. 

예측 실험을 설정하려면 먼저, N-Gram 어휘를 데이터 세트로 저장하고 실험의 학습 분기에서 학습된 로지스틱 회귀 모델을 저장합니다. 그런 다음 “다른 이름으로 저장”을 사용하여 실험을 저장한 후 예측 실험에 대한 실험 그래프를 만듭니다. 실험에서 데이터 분할 모듈 및 학습 분기를 제거합니다. 그런 후 앞서 저장한 N-Gram 어휘와 모델을 N-Gram 특성 추출 및 모델 점수 매기기 모듈에 각각 연결합니다. 또한 모델 평가 모듈을 제거합니다.

전처리 텍스트 모듈 앞에 데이터 세트의 열 선택 모듈을 삽입하여 레이블 열을 제거하고, 점수 매기기 모듈에서 "데이터 세트에 점수 열 추가" 옵션을 선택 취소합니다. 이렇게 하면 웹 서비스는 예측하려고 하는 레이블을 요청하지 않고, 응답에 입력 특성을 에코하지 않습니다.

![예측 실험](./media/text-analytics-module-tutorial/predictive-text.png)

이제 웹 서비스로 게시되고, 요청-응답 또는 일괄 처리 실행 API를 사용하여 호출할 수 있는 실험이 생성되었습니다.

## <a name="next-steps"></a>다음 단계
[MSDN 설명서](https://msdn.microsoft.com/library/azure/dn905886.aspx)에서 텍스트 분석 모듈에 대해 자세히 알아봅니다.

