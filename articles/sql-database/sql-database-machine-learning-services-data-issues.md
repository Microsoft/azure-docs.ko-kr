---
title: R 및 SQL 데이터 형식 및 개체 작업
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 데이터 형식 및 발생할 수 있는 일반적인 문제를 포함 하 여 Machine Learning 서비스 (미리 보기)를 사용 하 여 Azure SQL Database를 사용 하 여 R에서 데이터 개체를 사용 하는 방법에 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 069a2a5b3b26bf517b57034f05ab7080ab392319
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702523"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Azure SQL Database Machine Learning 서비스 (미리 보기)에서 R 및 SQL 데이터 작업

이 문서에서는 R 및 SQL Database 간에 데이터를 이동할 때 발생 하는 일반적인 문제 중 일부를 설명 [Machine Learning 서비스 (R)을 사용한 Azure SQL Database에서](sql-database-machine-learning-services-overview.md)합니다. 이 연습을 통해 얻게 환경을 사용자 지정 스크립트에서 데이터로 작업할 때 필수 배경을 제공 합니다.

발생할 수 있는 일반적인 문제는 다음과 같습니다.

- 경우에 따라 데이터 형식이 일치 하지 않습니다.
- 암시적 변환이 일어날 수 있음
- CAST 및 Convert 연산이 필요함 
- R 및 SQL이 서로 다른 데이터 개체를 사용함

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

- 이러한 연습에서 예제 코드를 실행하려면 먼저 Machine Learning Services(R 포함)를 사용하도록 설정된 Azure SQL 데이터베이스가 있어야 합니다. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="working-with-a-data-frame"></a>데이터 프레임을 사용 하 여 작업

스크립트 결과 R에서 SQL로 반환 될 때 데이터를 반환 해야 합니다는 **data.frame**합니다. 다른 유형의 개체가 든 관계 목록, 요소, 벡터 또는 이진 데이터--스크립트에서 생성 하는 저장된 프로시저 결과의 일부로 출력 하려면 데이터 프레임으로 변환할 수 있어야 합니다. 다행히도 기타 개체를 데이터 프레임으로 변경하는 기능을 지원하는 여러 R 함수가 있습니다. 도 이진 모델을 serialize 하 고이 문서의 뒷부분에서 수행 하는 데이터 프레임에서 반환할 수 있습니다.

먼저 몇 가지 기본 R 개체인 벡터, 행렬 및 목록-를 사용 하 여 실험 하 고 데이터 프레임으로 변환 SQL로 전달 되는 출력이 어떻게 변경 되는지 확인 하겠습니다.

R에서 다음 두 "Hello World" 스크립트를 비교하세요. 스크립트는 거의 동일 하지만 첫 번째는 세 개의 값을 단일 열로 반환하는 반면, 두 번째는 각각 단일 값을 가진 3개의 열을 반환합니다. 

**예제 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**예 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

왜 결과가 다를까요?

일반적으로 R `str()` 명령을 사용하여 그 대답을 찾을 수 있습니다. R 스크립트 임의 위치에 `str(object_name)` 함수를 추가하여 지정된 R 개체의 데이터 스키마를 정보성 메시지로 반환합니다. 메시지를 볼 수는 **메시지** SSMS의 탭 합니다.

예 1 및 예 2 있는 결과가 다른 이유를 파악, 삽입할 줄 `str(OutputDataSet)` 끝에 `@script` 다음과 같이 각 문에서 변수 정의:

**추가 str 함수를 사용 하 여 예제 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**추가 str 함수를 사용 하 여 예제 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

이제 **Message**에 텍스트를 검토하여 출력이 다른 이유를 확인합니다. 

**결과 - 예제 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**결과 - 예제 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

확인한 것처럼 R 구문을 약간 변경한 것이 결과의 스키마에 큰 영향을 미쳤습니다. 세부 정보, 모든 R 데이터 형식에서 차이점의 세부 정보에 설명 되어는 *데이터 구조* 단원의 [Hadley wickham "Advanced R"](http://adv-r.had.co.nz)합니다.

현재로서는 R 개체를 데이터 프레임으로 강제 변환할 때 예상되는 결과를 확인해야 합니다.

> [!TIP]
> 또한 R id 함수와 같은 사용할 수 `is.matrix`, `is.vector`, 내부 데이터 구조에 대 한 정보를 반환 합니다.

## <a name="implicit-conversion-of-data-objects"></a>데이터 개체의 암시적 변환

각 R 데이터 개체에는 두 개의 데이터 개체가 같은 수의 차원을 가지거나 데이터 개체에 다른 데이터 형식이 포함될 경우 다른 데이터 개체와 결합될 때 값을 처리하는 방법에 대한 자체 규칙이 있습니다.

예를 들어, R을 사용 하는 행렬 곱셈을 수행 한다고 가정 합시다 4 개의 값을 사용 하 여 배열에서 세 개의 값을 사용 하 여 단일 열 행렬 곱하기 및 결과적으로 4x3 행렬을 예상 하려면.

먼저 테스트 데이터의 작은 테이블을 만듭니다.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

이제 다음 스크립트를 실행 합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

내부적으로 3개의 값으로 구성된 단일 열이 단일-열 행렬로 변환됩니다. 행렬은 R에서 단지 배열의 특수한 형태이므로, 배열 `y`는 두 인수가 일치하도록 단일 열 행렬로 강제 변환됩니다.

**결과**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

하지만 `y` 배열 크기를 변경하면 어떻게 되는지 주목하세요.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

이제 R는 결과로 단일 값을 반환합니다.

**결과**
    
|Col1|
|---|
|1542|

이유가 무엇일까요? 이 경우 두 인수는 동일한 길이의 벡터로 처리될 수 있으므로 R이 내적(inner product)을 행렬로 반환합니다.  이것이 선형 대 수 규칙에 따라 예상 되는 동작입니다. 그러나 다운스트림 응용 프로그램에서 출력 스키마가 변경 되지 않습니다 필요한 경우 문제가 발생할 수 있습니다.

## <a name="merge-or-multiply-columns-of-different-length"></a>다른 길이의 열 병합 또는 곱하기

R은 다양한 크기의 벡터로 작업하고 이러한 열과 같은 구조를 데이터 프레임에 결합하는데 뛰어난 유연성을 제공합니다. 벡터 리스트는 테이블처럼 보일 수 있지만 데이터베이스 테이블을 관리하는 모든 규칙을 따르지 않습니다.

예를 들어 다음 스크립트는 길이가 6인 숫자 배열을 정의하고 R 변수 `df1`에 저장합니다. 숫자 배열을 (위에서 만든) RTestData 테이블의 정수와 함께 결합 한 다음 3 개의 값을 새 데이터 프레임을 포함 하는 `df2`합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

R은 데이터 프레임을 채우기 위해 RTestData에서 검색된 요소를 `df1` 배열의 요소 수와 일치하도록 필요한 횟수만큼 반복합니다.

**결과**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

데이터 프레임만 테이블 처럼 보이지만 실제로 목록은 벡터를 기억 합니다.

## <a name="cast-or-convert-sql-data"></a>Cast 또는 convert SQL 데이터

R과 SQL에는 동일한 데이터 형식을 사용 하지 않는의 일부 형식 데이터를 가져오고 R 런타임에 전달할 sql에서 쿼리를 실행 하므로 암시적 변환이 일반적으로 발생 합니다. 다른 변환 집합 SQL을 R에서 데이터를 반환할 때 수행이 됩니다.

- SQL R 프로세스로 쿼리의 데이터 푸시 및 효율성을 높이기 위한 내부 표현으로 변환 합니다.
- R 런타임이 데이터를 data.frame 변수에 로드하고 데이터에서 고유한 작업을 수행합니다.
- 데이터베이스 엔진의 내부 보안된 연결을 사용 하 여 SQL 데이터를 반환 하 고 SQL 데이터 형식에 따라 데이터를 제공 합니다.
- SQL 쿼리를 실행 하 고 테이블 형식 데이터 집합을 처리할 수 있는 클라이언트 또는 네트워크 라이브러리를 사용 하 여 하 SQL에 연결 하 여 데이터를 가져옵니다. 이 클라이언트 애플리케이션은 다른 방식으로 데이터에 영향을 미칠 수 있습니다.

이 과정을 보려면에서이 이와 같은 쿼리를 실행 합니다 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) 데이터 웨어하우스. 이 뷰는 예측 생성에 사용된 매출 데이터를 반환합니다.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 모든 버전의 AdventureWorks를 사용할 수 있으며, 자신의 데이터베이스를 사용해 다른 쿼리를 만들 수 있습니다. 점은 텍스트, 날짜/시간 및 숫자 값을 포함 하는 일부 데이터를 처리 하려고 합니다.

이제이 쿼리를 사용 하 여 저장된 프로시저에 대 한 입력으로 시도 하세요.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

오류가 발생하면 쿼리 텍스트를 약간 편집해야 할 수 있습니다. 예를 들어 WHERE 절의 문자열 조건자는 두 개의 작은따옴표 집합으로 묶어야 합니다.

쿼리를 실행한 후 `str` 함수의 결과를 검토하여 R에서 입력 데이터가 어떻게 처리되는지 확인합니다.

**결과**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- datetime 열은 R 데이터 형식 **POSIXct**를 사용하여 처리되었습니다.
- 텍스트 "ProductSeries"는 **팩터**로 인식되었으며 이는 범주 변수임을 의미합니다. 문자열 값은 기본적으로 팩터로 처리됩니다. 문자열 값은 기본적으로 요소로 처리됩니다. R에 문자열을 전달하면 문자열은 내부 사용을 위해 정수로 변환되고 다시 문자열에 매핑되어서 출력됩니다.

## <a name="summary"></a>요약

간단한 예제를 통해 입력으로 SQL 쿼리를 전달할 때 데이터 변환의 결과를 확인할 필요가 있음을 볼 수 있습니다. 일부 SQL 데이터 형식은 R에서 지원 되지 않으므로 오류를 방지 하는 이러한 방법을 고려 합니다.

- 데이터를 미리 테스트해서 R 코드에 전달할 때 문제가 될 수 있는 스키마의 열 혹은 값을 검사합니다.
- `SELECT *`를 사용하지 않고 입력 데이터 원본에서 열을 개별적으로 지정하고 각 열이 어떻게 처리되는지 알아봅니다.
- 문제를 방지하려면 입력 데이터를 준비할 때 필요에 따라 명시적 Cast를 수행합니다.
- 오류를 유발하거나 모델링에 유용하지 않은 데이터 열(예: GUID 또는 rowguids)은 전달하지 않습니다. 

지원 되거나 지원 되지 않는 R 데이터 형식에 대 한 자세한 내용은 참조 하세요. [R 라이브러리 및 데이터 형식](/sql/advanced-analytics/r/r-libraries-and-data-types.md)합니다.
