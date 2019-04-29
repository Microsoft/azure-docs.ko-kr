---
title: Visual Studio Code에서 Python, R 및 C#를 사용하여 Azure Data Lake Analytics용 U-SQL 개발
description: Python, R 및 C#에서 코드 숨김을 사용하여 Azure Data Lake에 작업을 제출하는 방법을 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: 6c234ad6756f4e65e172bf0ffc0ae5a1d35d109b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814075"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Visual Studio Code에서 Python, R 및 C#를 사용하여 Azure Data Lake Analytics용 U-SQL 개발
VSCode(Visual Studio Code)를 사용하여 U-SQL이 포함된 Python, R 및 C# 코드 숨김을 작성하고 Azure Data Lake 서비스에 작업을 제출하는 방법에 대해 알아봅니다. Azure Data Lake Tools for VSCode에 대한 자세한 내용은 [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)을 참조하세요.

코드 숨김 사용자 지정 코드를 작성하기 전에 VSCode에서 폴더 또는 작업 영역을 열어야 합니다.


## <a name="prerequisites-for-python-and-r"></a>Python 및 R에 대한 필수 구성 요소
ADL 계정에 대한 Python 및 R 확장 어셈블리를 등록합니다. 
1. 포털에서 계정을 엽니다.
   - **개요**를 선택합니다. 
   - **샘플 스크립트**를 클릭합니다.
2. **자세히**를 클릭합니다.
3. **U-SQL 확장 설치**를 선택합니다. 
4. U-SQL 확장이 설치된 후에 확인 메시지가 표시됩니다. 

   ![Python 및 R에 대한 환경 설정](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Python 및 R 언어 서비스에 대한 최상의 환경을 얻으려면 VSCode Python 및 R 확장을 설치하세요. 

## <a name="develop-python-file"></a>Python 파일 개발
1. 작업 영역에서 **새 파일**을 클릭합니다.
2. U-SQL에 코드를 작성합니다. 다음은 코드 샘플입니다.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
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
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. 스크립트 파일을 마우스 오른쪽 단추로 클릭 하 고 선택한 **ADL: Python 코드 숨김 파일을 생성**합니다. 
4. **xxx.usql.py** 파일이 작업 폴더에 생성됩니다. Python 파일에 코드를 작성합니다. 다음은 코드 샘플입니다.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. **USQL** 파일에서 마우스 오른쪽 단추로 클릭하면 작업 실행에 대한 **스크립트 컴파일** 또는 **작업 제출**을 클릭할 수 있습니다.

## <a name="develop-r-file"></a>R 파일 개발
1. 작업 영역에서 **새 파일**을 클릭합니다.
2. U-SQL 파일에 코드를 작성합니다. 다음은 코드 샘플입니다.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. 마우스 오른쪽 단추로 클릭 **USQL** 파일을 선택한 후 **ADL: R 코드 숨김 파일을 생성**합니다. 
4. **xxx.usql.r** 파일이 작업 폴더에 생성됩니다. R 파일에 코드를 작성합니다. 다음은 코드 샘플입니다.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. **USQL** 파일에서 마우스 오른쪽 단추로 클릭하면 작업 실행에 대한 **스크립트 컴파일** 또는 **작업 제출**을 클릭할 수 있습니다.

## <a name="develop-c-file"></a>C# 파일 개발
코드 숨김 파일은 단일 U-SQL 스크립트와 연결되는 C# 파일입니다. 코드 숨김 파일에서는 UDO, UDA, UDT 및 UDF 전용 스크립트를 정의할 수 있습니다. 먼저 어셈블리를 등록하지 않고도 스크립트에서 UDO, UDA, UDT 및 UDF를 직접 사용할 수 있습니다. 코드 숨김 파일은 피어링 U-SQL 스크립트 파일과 동일한 폴더에 저장됩니다. 스크립트 파일 이름이 xxx.usql이면 코드 숨김 파일 이름은 xxx.usql.cs가 됩니다. 코드 숨김 파일을 수동으로 삭제하면 연결된 U-SQL 스크립트의 코드 숨김 기능을 사용할 수 없게 됩니다. U-SQL 스크립트의 고객 코드를 작성 하는 방법에 대 한 자세한 내용은 참조 하세요. [작성 및 U-SQL에서 사용자 지정 코드 사용: 사용자 정의 함수]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)합니다.

1. 작업 영역에서 **새 파일**을 클릭합니다.
2. U-SQL 파일에 코드를 작성합니다. 다음은 코드 샘플입니다.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. 마우스 오른쪽 단추로 클릭 **USQL** 파일을 선택한 후 **ADL: Generate CS Code Behind 파일**합니다. 
4. **xxx.usql.cs** 파일이 작업 폴더에 생성됩니다. CS 파일에 코드를 작성합니다. 다음은 코드 샘플입니다.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. **USQL** 파일에서 마우스 오른쪽 단추로 클릭하면 작업 실행에 대한 **스크립트 컴파일** 또는 **작업 제출**을 클릭할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Data Lake Tools for Visual Studio Code 사용](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Visual Studio Code로 U-SQL 로컬 실행 및 로컬 디버그](data-lake-tools-for-vscode-local-run-and-debug.md)
* [PowerShell을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-powershell.md)
* [Azure 포털을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-portal.md)
* [U-SQL 애플리케이션 개발에 Visual Studio용 데이터 레이크 도구 사용하기](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics(U-SQL) 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)
