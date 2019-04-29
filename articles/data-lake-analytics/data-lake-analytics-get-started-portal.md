---
title: Azure Portal을 사용하여 Azure Data Lake Analytics 시작
description: Azure Portal을 사용하여 Azure Data Lake Analytics 계정을 만들고 U-SQL 작업을 제출합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 25d58bdc5791de868c6302b4d2763fa34e98af17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615035"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Data Lake Analytics 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

이 문서에서는 Azure Portal을 사용하여 Azure Data Lake Analytics 계정을 만들고, [U-SQL](data-lake-analytics-u-sql-get-started.md)로 작업을 정의하고, 작업을 Data Lake Analytics 서비스에 제출하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 **Azure 구독**이 있어야 합니다. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기

이제 Data Lake Analytics 및 Azure Data Lake Storage Gen1 계정을 동시에 만듭니다.  이 단계는 간단하며 완료하는 데 약 60초가 소요됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 만들기** >  **데이터 + 분석** > **Data Lake Analytics**를 클릭합니다.
3. 다음 항목에 대한 값을 선택합니다.
   * **이름**: (소문자와 숫자만 허용) Data Lake Analytics 계정의 이름을 지정 합니다.
   * **구독**: 분석 계정에 사용할 Azure 구독을 선택 합니다.
   * **리소스 그룹**. 기존 Azure 리소스 그룹을 선택하거나 리소스 그룹을 새로 만듭니다.
   * **위치** - 데이터 레이크 분석 계정에 대한 Azure 데이터 센터를 선택합니다.
   * **Data Lake Storage Gen1**: 새 Data Lake 저장소 Gen1 계정을 만들려면 지시 하거나 기존 항목을 선택 합니다. 
4. 필요에 따라 Data Lake Analytics 계정에 대한 가격 책정 계층을 선택합니다.
5. **만들기**를 클릭합니다. 


## <a name="your-first-u-sql-script"></a>첫 번째 U-SQL 스크립트

다음 텍스트는 매우 간단한 U-SQL 스크립트입니다. 스크립트 내에서 작은 데이터 세트를 정의한 다음, 해당 데이터 세트를 `/data.csv`라는 파일로 기본 Data Lake Storage Gen1 계정에 기록하는 것이 전부입니다.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>U-SQL 작업 제출

1. Data Lake Analytics 계정에서 **새 작업**을 선택합니다.
2. 이전 U-SQL 스크립트의 텍스트에 붙여넣습니다. 작업의 이름을 지정합니다. 
3. **전송** 단추를 선택하여 작업을 시작합니다.   
4. 작업의 **상태**를 모니터링하고, 작업 상태가 **성공**으로 변경될 때까지 기다립니다.
5. **데이터** 탭을 선택한 다음, **출력** 탭을 선택합니다. `data.csv`라는 출력 파일을 선택하고 출력 데이터를 확인합니다.

## <a name="see-also"></a>참고 항목

* U-SQL 애플리케이션 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
