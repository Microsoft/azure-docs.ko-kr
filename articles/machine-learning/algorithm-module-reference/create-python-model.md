---
title: 'Python 모델 만들기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 Python 모델 만들기 모델을 사용 하 여 사용자 지정 모델링 또는 데이터 처리 모듈을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e26ae7ded47a235ecf1829946c1cc79af9a0fb8c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152400"
---
# <a name="create-python-model"></a>Python 모델 만들기

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

Python **모델 만들기** 모듈을 사용 하 여 python 스크립트에서 학습 되지 않은 모델을 만드는 방법에 대해 알아봅니다. Azure Machine Learning designer 환경의 Python 패키지에 포함 된 모든 학습자 모델을 기반으로 할 수 있습니다. 

모델을 만든 후에는 [모델 학습](train-model.md) 을 사용 하 여 Azure Machine Learning의 다른 학습자 같은 데이터 집합에 대 한 모델 학습을 수행할 수 있습니다. 모델을 사용 하 여 예측을 만드는 학습 된 모델을 [점수 매기기 모델](score-model.md) 에 전달할 수 있습니다. 그런 다음 학습된 모델을 저장할 수 있으며 점수 매기기 워크플로를 웹 서비스로 게시할 수 있습니다.

> [!WARNING]
> 현재는 Python 모델의 점수가 매겨진 결과를 전달 하 여 [모델을 평가할](evaluate-model.md)수 없습니다. 모델을 평가 해야 하는 경우 사용자 지정 Python 스크립트를 작성 하 고 [Python 스크립트 실행](execute-python-script.md) 모듈을 사용 하 여 실행할 수 있습니다.  


## <a name="how-to-configure-create-python-model"></a>Python 모델 만들기를 구성 하는 방법

이 모듈을 사용 하려면 Python에 대해 중급 또는 전문 지식이 필요 합니다. 이 모듈은 Azure Machine Learning에 이미 설치 된 Python 패키지에 포함 된 모든 학습자의 사용을 지원 합니다. [Python 스크립트 실행](execute-python-script.md)의 미리 설치 된 python 패키지 목록을 참조 하세요.
  

이 문서에서는 간단한 파이프라인에서 **Python 모델 만들기** 를 사용 하는 방법을 보여 줍니다. 파이프라인의 그래프는 다음과 같습니다.

![만들기-python-모델](./media/module/aml-create-python-model.png)

1.  **Python 모델 만들기**를 클릭 하 고, 스크립트를 편집 하 여 모델링 또는 데이터 관리 프로세스를 구현 합니다. Azure Machine Learning 환경의 Python 패키지에 포함 된 모든 학습자 모델을 기반으로 할 수 있습니다.


    다음 *은 인기 있는 기능을 사용 하* 는 Naive Bayes 분류자의 샘플 코드입니다.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. 방금 만든 **Python 모델 만들기** 모듈을 **모델 학습** 및 **점수 매기기 모델** 에 연결

3. 모델을 평가 해야 하는 경우 [python 스크립트 실행](execute-python-script.md) 을 추가 하 고 python 스크립트를 편집 하 여 평가를 구현 합니다.

다음은 샘플 평가 코드입니다.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
