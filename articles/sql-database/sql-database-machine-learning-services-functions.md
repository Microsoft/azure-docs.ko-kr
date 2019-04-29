---
title: 고급 R 함수 작성
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Machine Learning 서비스 (미리 보기)를 사용 하 여 Azure SQL Database에서 고급 통계 계산을 위해 R 함수를 작성 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702455"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Machine Learning 서비스 (미리 보기)를 사용 하 여 Azure SQL Database의 고급 R 함수를 작성 합니다.

이 문서에서는 R 수학를 포함 하는 방법을 설명 하 고 유틸리티 함수는 SQL 저장 프로시저입니다. T-SQL에서 구현 하는 복잡 한 고급 통계 함수 코드 한 줄만을 사용 하 여 R에서 수행할 수 있습니다.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).

- 이러한 연습에서 예제 코드를 실행하려면 먼저 Machine Learning Services(R 포함)를 사용하도록 설정된 Azure SQL 데이터베이스가 있어야 합니다. Microsoft는 공개 미리 보기 기간에는 사용자를 온보딩하고 기존 또는 새 데이터베이스에 기계 학습을 사용하도록 설정합니다. [미리 보기에 가입](sql-database-machine-learning-services-overview.md#signup) 단계를 수행하세요.

- 최신 [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)를 설치했는지 확인합니다. 다른 데이터베이스 관리 또는 쿼리 도구를 사용하여 R 스크립트를 실행할 수 있지만, 이 빠른 시작에서는 SSMS를 사용합니다.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>난수를 생성 하는 저장된 프로시저 만들기

간단히 하기 위해 R을 사용 하겠습니다 `stats` 패키지를 설치 하 고 Machine Learning 서비스 (미리 보기)를 사용 하 여 Azure SQL Database를 사용 하 여 기본적으로 로드 합니다. 수백 개의 그중에서 일반 통계 태스크에 대 한 함수를 포함 하는 패키지는 `rnorm` 함수입니다. 이 함수는 지정된 된 수의 표준 편차 및 방법을 지정 된 정규 분포를 사용 하 여 난수를 생성 합니다.

예를 들어, 다음 R 코드를 50으로 3의 표준 편차를 지정 된 평균에 100 개의 숫자를 반환 합니다.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

T-SQL에서 R의이 줄을 호출 하려면 `sp_execute_external_script` 같이 R 스크립트 매개 변수에 R 함수를 추가 합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

다른 난수 집합을 더 쉽게 생성하려면 어떻게 하면 될까요?

SQL을 함께 사용 하면 간단 합니다. 사용자 로부터 인수를 가져오고 다음 변수로 R 스크립트에 이러한 인수를 전달 하는 저장된 프로시저를 정의할 수 있습니다.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 첫 번째 줄은 각 SQL 입력된 매개 변수를 필요한 저장된 프로시저가 실행 될 때를 정의 합니다.

- `@params`로 시작하는 줄은 R 코드에서 사용되는 변수들과 해당하는 SQL 데이터 형식을 정의합니다.

- 줄 바로 뒤에 있는 해당 R 변수 이름에 SQL 매개 변수 이름을 매핑합니다.

이제 저장된 프로시저에서 R 함수를 래핑 했으므로, 쉽게 함수를 호출 하 고 수 있는 다음과 같은 다른 값을 전달:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>문제 해결을 위한 R 유틸리티 함수 사용

`utils` 다양 한 유틸리티 함수는 현재 R 환경을 조사할를 제공 하는 패키지를 기본적으로 설치 합니다. 이러한 함수는 sql에서 및 외부 환경에서 R 코드에서 수행 하는 방식에 불일치를 찾는 경우 유용할 수 있습니다. 예를 들어 R의 `memory.limit()` 함수를 사용하여 현재 R 환경에 사용할 메모리를 가져올 수 있습니다.

`utils` 패키지는 기본적으로 로드되지 않으므로 먼저 `library()` 함수를 사용하여 로드해야 합니다.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> 많은 사용자가 성능 문제 분석을 위해 R 프로세스가 사용하는 시간을 캡처해주는 `sy`system.time` `proc.time` 같은 시스템 타이밍 함수를 사용합니다.
