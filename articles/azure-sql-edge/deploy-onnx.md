---
title: ONNX를 사용 하 여 배포 및 예측
description: 모델을 학습 하 고, ONNX로 변환 하 고, Azure sql Edge (미리 보기) 또는 Azure SQL Managed Instance (미리 보기)에 배포 하 고, 업로드 된 ONNX 모델을 사용 하 여 데이터에 대 한 기본 예측을 실행 하는 방법에 대해 알아봅니다.
keywords: SQL Edge 배포
ms.prod: sql
ms.technology: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 07/14/2020
ms.openlocfilehash: eeb50f682c8b3b225c6574b5276722b79465a511
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718784"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model"></a>ONNX 모델을 사용 하 여 배포 및 예측

이 빠른 시작에서는 모델을 학습 하 고, ONNX로 변환 하 고, [azure Sql Edge (미리 보기](onnx-overview.md) ) 또는 [azure sql Managed Instance (미리 보기)](../azure-sql/managed-instance/machine-learning-services-overview.md)에 배포 하 고, 업로드 된 onnx 모델을 사용 하 여 데이터에 대 한 기본 예측을 실행 하는 방법을 알아봅니다.

이 빠른 시작은 **scikit-learn**을 기반으로 하며, [보스턴 하우징 데이터 세트](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html)를 사용합니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure SQL Edge를 사용 하는 경우 Azure SQL Edge 모듈을 배포 하지 않은 경우 [Azure Portal를 사용 하 여 SQL edge 배포 (미리 보기)](deploy-portal.md)의 단계를 수행 합니다.

* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download)를 설치합니다.

* 이 빠른 시작에 필요한 Python 패키지를 설치 합니다.

  1. Python 3 커널에 연결된 [새 Notebook](https://docs.microsoft.com/sql/azure-data-studio/sql-notebooks)을 엽니다. 
  1. **패키지 관리** 클릭
  1. **설치 됨** 탭의 설치 된 패키지 목록에서 다음 Python 패키지를 찾습니다. 이러한 패키지 중 하나라도 설치 되지 않은 경우 **새로 추가** 탭을 선택 하 고 패키지를 검색 한 다음 **설치**를 클릭 합니다.
     - **scikit-learn**
     - **numpy**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **setuptools**
     - **skl2onnx**
     - **sqlalchemy**

* 아래 각 스크립트 부분에 대해 Azure Data Studio Notebook의 셀에 입력하고 셀을 실행합니다.

## <a name="train-a-pipeline"></a>파이프라인 학습

기능을 사용하여 집의 중앙값을 예측하는 데이터 세트를 분할합니다.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**출력**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

LinearRegression 모델을 학습하는 파이프라인을 만듭니다. 다른 회귀 모델을 사용할 수도 있습니다.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

모델의 정확도를 확인한 다음, R2 점수와 평균 제곱 오차를 계산합니다.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**출력**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>모델을 ONNX로 변환

데이터 형식을 지원되는 SQL 데이터 형식으로 변환합니다. 이 변환은 다른 데이터프레임에서도 필요합니다.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

`skl2onnx`를 사용하여 LinearRegression 모델을 ONNX 형식으로 변환하고 로컬로 저장합니다.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train))
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>ONNX 모델 테스트

모델을 ONNX 형식으로 변환한 후 모델의 점수를 매겨 성능 저하를 최소화합니다.

> [!NOTE]
> ONNX Runtime은 double 대신 float을 사용하므로 소규모 불일치가 가능합니다.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**출력**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>ONNX 모델 삽입

모델을 Azure SQL Edge 또는 Azure SQL Managed Instance 데이터베이스의 테이블에 저장 `models` `onnx` 합니다. 연결 문자열에서 **서버 주소**, **사용자 이름**및 **암호**를 지정합니다.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>데이터 로드

SQL에 데이터를 로드 합니다.

먼저 **features** 및 **target**이란 두 개의 테이블을 만들어 보스턴 하우징 데이터 세트의 하위 집합을 저장합니다.

* **features**에는 대상(중앙값)을 예측하는 데 사용되는 모든 데이터가 포함됩니다. 
* **target**에는 데이터 세트의 각 레코드에 대한 중앙값이 포함됩니다. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

마지막으로 `sqlalchemy`를 사용하여 `x_train` 및 `y_train` pandas 데이터프레임를 테이블 `features` 및 `target`에 각각 삽입합니다. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

이제 데이터베이스에서 데이터를 볼 수 있습니다.

## <a name="run-predict-using-the-onnx-model"></a>ONNX 모델을 사용하여 PREDICT 실행

SQL의 모델을 사용 하 여 업로드 된 ONNX 모델을 사용 하 여 데이터에서 기본 PREDICT를 실행 합니다.

> [!NOTE]
> 나머지 셀을 실행하려면 Notebook 커널을 SQL로 변경합니다.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>다음 단계

* [SQL Edge에서 ONNX를 통한 Machine Learning 및 AI](onnx-overview.md)
* [Azure SQL Managed Instance (미리 보기)의 Machine Learning Services](../azure-sql/managed-instance/machine-learning-services-overview.md)
