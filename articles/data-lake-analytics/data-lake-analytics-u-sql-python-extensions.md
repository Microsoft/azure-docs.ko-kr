---
title: Azure Data Lake Analytics에서 Python으로 U-SQL 스크립트 확장
description: Azure Data Lake Analytics를 사용하여 U-SQL 스크립트로 Python 코드를 실행하는 방법 알아보기
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813411"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics에서 Python 코드로 U-SQL 스크립트 확장

## <a name="prerequisites"></a>필수 조건

시작하기 전에 Azure Data Lake Analytics 계정에 Python 확장이 설치되어 있는지 확인하세요.

* Azure Portal에서 Data Lake Analytics 계정으로 이동합니다.
* 왼쪽 메뉴의 **시작**에서 **샘플 스크립트**를 클릭합니다.
* **U-SQL Extensions 설치**, **확인** 순으로 클릭합니다.

## <a name="overview"></a>개요 

U-SQL용 Python 확장을 사용하면 개발자가 Python 코드를 대량으로 병렬 실행할 수 있습니다. 다음 예제에서는 기본 단계를 설명합니다.

* `REFERENCE ASSEMBLY` 문을 사용하여 U-SQL 스크립트에 대한 Python 확장을 사용하도록 설정합니다.
* `REDUCE` 연산을 사용하여 키의 입력 데이터 분할
* U-SQL용 Python 확장에는 감속기에 할당된 각 꼭짓점에서 Python 코드를 실행하는 기본 제공 감속기(`Extension.Python.Reducer`)가 포함되어 있습니다
* U-SQL 스크립트에는 pandas DataFrame을 입력으로 받아들이고 pandas DataFrame을 출력으로 반환하는 `usqlml_main`이라는 함수가 있는 포함된 Python 코드가 포함되어 있습니다.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Python과 U-SQL 통합 방법

### <a name="datatypes"></a>데이터 형식

* U-SQL의 문자열 및 숫자 열은 Pandas와 U-SQL 간에 있는 그대로 변환됩니다.
* U-SQL Null은 Pandas `NA` 값으로 변환됩니다.

### <a name="schemas"></a>스키마

* Pandas의 인덱스 벡터는 U-SQL에서 지원되지 않습니다. Python 함수의 모든 입력 데이터 프레임에는 항상 0부터 시작하여 행 수에서 1을 뺀 인덱스까지 64비트의 숫자 인덱스가 있습니다. 
* U-SQL 데이터 세트에는 중복된 열 이름을 사용할 수 없습니다.
* 문자열이 아닌 U-SQL 데이터 세트 열 이름입니다. 

### <a name="python-versions"></a>Python 버전
Python 3.5.1(Windows용으로 컴파일)만 지원됩니다. 

### <a name="standard-python-modules"></a>표준 Python 모듈
표준 Python 모듈이 모두 포함되어 있습니다.

### <a name="additional-python-modules"></a>추가 Python 모듈
표준 Python 라이브러리 외에도 일반적으로 사용되는 몇 가지 Python 라이브러리가 포함되어 있습니다.

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>예외 메시지
현재 Python 코드의 예외는 일반적인 정점 오류로 나타납니다. 앞으로 U-SQL 작업 오류 메시지는 Python 예외 메시지를 표시합니다.

### <a name="input-and-output-size-limitations"></a>입력 및 출력 크기 제한
모든 정점에는 할당되는 메모리 양이 제한되어 있습니다. 현재 이 제한은 AU의 경우 6GB입니다. 입력 및 출력 DataFrames는 Python 코드의 메모리에 있어야 하므로 입력 및 출력의 총 크기는 6GB를 초과할 수 없습니다.

## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
* [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)
