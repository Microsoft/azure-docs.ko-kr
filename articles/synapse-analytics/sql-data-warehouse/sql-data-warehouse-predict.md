---
title: PREDICT를 사용 하 여 기계 학습 모델 점수 매기기
description: Synapse SQL에서 T-sql PREDICT 함수를 사용 하 여 기계 학습 모델의 점수를 매기는 방법에 대해 알아봅니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: anjangsh
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ef56274e0bda3f1a9d494852520a77ecdfc25799
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048009"
---
# <a name="score-machine-learning-models-with-predict"></a>PREDICT를 사용 하 여 기계 학습 모델 점수 매기기

Synapse SQL은 익숙한 T-sql 언어를 사용 하 여 기계 학습 모델의 점수를 매기는 기능을 제공 합니다. T-sql [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)를 사용 하 여 기존 machine learning 모델을 기록 데이터로 학습 하 고 데이터 웨어하우스의 보안 경계 내에서 점수를 매길 수 있습니다. PREDICT 함수는 [Onnx (오픈 신경망 교환)](https://onnx.ai/) 모델 및 데이터를 입력으로 사용 합니다. 이 기능은 점수 매기기를 위해 데이터 웨어하우스 외부에서 중요 한 데이터를 이동 하는 단계를 제거 합니다. 데이터 전문가에 게 익숙한 T-sql 인터페이스를 사용 하 여 기계 학습 모델을 쉽게 배포 하 고, 해당 작업을 위한 올바른 프레임 워크로 작업 하는 데이터 과학자 원활 하 게 공동 작업을 수행 하는 것을 목표로 합니다.

> [!NOTE]
> 이 기능은 현재 주문형 SQL에서 지원 되지 않습니다.

이 기능을 사용 하려면 모델을 Synapse SQL 외부에서 학습 해야 합니다. 모델을 작성 한 후 데이터 웨어하우스로 로드 하 고 T-sql Predict 구문을 사용 하 여 점수를 매기고 데이터에서 정보를 얻습니다.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>모델 학습

Synapse SQL에는 미리 학습 된 모델이 필요 합니다. Synapse SQL에서 예측을 수행 하는 데 사용 되는 machine learning 모델을 학습 하는 경우 다음 요인을 염두에 두어야 합니다.

- Synapse SQL은 ONNX 형식 모델만 지원 합니다. ONNX는 다양 한 프레임 워크 간에 모델을 교환 하 여 상호 운용성을 가능 하 게 해 주는 오픈 소스 모델 형식입니다. 기본적으로 지원 하거나 패키지를 사용 가능 하 게 변환 하는 프레임 워크를 사용 하 여 기존 모델을 ONNX 형식으로 변환할 수 있습니다. 예를 들어, 비 [기능 학습-onnx](https://github.com/onnx/sklearn-onnx) 패키지 convert scikit-모델을 onnx로 변환 합니다. [Onnx GitHub 리포지토리](https://github.com/onnx/tutorials#converting-to-onnx-format) 는 지원 되는 프레임 워크 및 예제 목록을 제공 합니다.

   학습에 [자동 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 을 사용 하는 경우에는 *enable_onnx_compatible_models* 매개 변수를 TRUE로 설정 하 여 onnx 형식 모델을 생성 해야 합니다. [자동화 된 Machine Learning 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 에서는 automl을 사용 하 여 onnx 형식의 기계 학습 모델을 만드는 방법의 예를 보여 줍니다.

- 입력 데이터에 대해 지원 되는 데이터 형식은 다음과 같습니다.
    - int, bigint, real, float
    - char, varchar, nvarchar

- 점수 매기기 데이터는 학습 데이터와 동일한 형식 이어야 합니다. 다차원 배열과 같은 복합 데이터 형식은 PREDICT에서 지원 되지 않습니다. 따라서 학습을 위해 모든 입력을 포함 하는 단일 배열을 전달 하는 대신 모델의 각 입력이 점수 매기기 테이블의 단일 열에 해당 하는지 확인 합니다.

- 모델 입력의 이름과 데이터 형식이 새 예측 데이터의 열 이름과 데이터 형식과 일치 하는지 확인 합니다. 온라인으로 제공 되는 다양 한 오픈 소스 도구를 사용 하 여 ONNX 모델을 시각화 하면 디버깅 하는 데 도움이 될 수 있습니다.

## <a name="loading-the-model"></a>모델 로드

모델은 Synapse SQL 사용자 테이블에 16 진수 문자열로 저장 됩니다. 모델을 식별 하기 위해 ID 및 설명과 같은 추가 열을 모델 테이블에 추가할 수 있습니다. Model 열의 데이터 형식으로 varbinary (max)를 사용 합니다. 모델을 저장 하는 데 사용할 수 있는 테이블에 대 한 코드 예제는 다음과 같습니다.

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

모델이 16 진수 문자열로 변환 되 고 테이블 정의가 지정 되 면 [COPY 명령](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 또는 Polybase를 사용 하 여 Synapse SQL 테이블에 모델을 로드 합니다. 다음 코드 샘플에서는 복사 명령을 사용 하 여 모델을 로드 합니다.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>모델 점수 매기기

데이터 웨어하우스에서 모델 및 데이터가 로드 되 면 **T-SQL PREDICT** 함수를 사용 하 여 모델의 점수를 매길 수 있습니다. 새 입력 데이터가 모델을 작성 하는 데 사용 되는 학습 데이터와 동일한 형식 인지 확인 합니다. T-sql PREDICT은 모델 및 새 점수 매기기 입력 데이터 라는 두 가지 입력을 사용 하 고 출력에 대 한 새 열을 생성 합니다. 모델은 변수, 리터럴 또는 스칼라 sub_query 지정할 수 있습니다. [WITH common_table_expression](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql?view=sql-server-ver15) 를 사용 하 여 데이터 매개 변수에 대해 명명 된 결과 집합을 지정 합니다.

아래 예제에서는 예측 함수를 사용 하는 예제 쿼리를 보여 줍니다. 예측 결과를 포함 하는 이름 *점수* 와 데이터 형식이 *float* 인 추가 열이 생성 됩니다. 모든 입력 데이터 열과 출력 예측 열은 select 문을 사용 하 여 표시할 수 있습니다. 자세한 내용은 [PREDICT (transact-sql)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)를 참조 하세요.

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d) WITH (Score float) AS p;
```

## <a name="next-steps"></a>다음 단계

PREDICT 함수에 대해 자세히 알아보려면 [predict (transact-sql)](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)를 참조 하세요.
