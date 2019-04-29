---
title: Azure Data Lake에 대한 U-SQL 프로그래밍 기능 가이드
description: 클라우드 기반 빅 데이터 플랫폼을 만들 수 있는 Azure Data Lake Analytics의 서비스 집합에 대해 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: d1b230b40d1f880787334ebfd39e704e3a650baa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811606"
---
# <a name="u-sql-programmability-guide"></a>U-SQL 프로그래밍 기능 가이드

U-SQL은 빅 데이터 유형의 워크로드를 위해 설계된 쿼리 언어입니다. U-SQL의 고유한 기능 중 하나는 SQL과 같은 선언적 언어와 C#에서 제공하는 확장성 및 프로그래밍 기능을 결합한 것입니다. 이 가이드에서는 C#에서 사용할 수 있는 U-SQL 언어의 확장성과 프로그래밍 기능을 집중적으로 설명합니다.

## <a name="requirements"></a>요구 사항

[Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)를 다운로드하고 설치합니다.

## <a name="get-started-with-u-sql"></a>U-SQL 시작  

다음 U-SQL 스크립트를 살펴보겠습니다.

```
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

이 스크립트는 두 개의 행 집합, `@a`과 `@results`를 정의합니다. `@results` 행 집합은 `@a`에서 정의됩니다.

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 스크립트의 C# 형식 및 식

U-SQL 식은 `AND`, `OR` 및 `NOT`과 같은 U-SQL 논리 연산자와 결합된 C# 식입니다. U-SQL 식은 SELECT, EXTRACT, WHERE, HAVING, GROUP BY 및 DECLARE와 함께 사용할 수 있습니다. 예를 들어 다음 스크립트는 문자열을 DateTime 값으로 구문 분석합니다.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

다음 코드 조각은 DECLARE 문에서 문자열을 DateTime 값으로 구문 분석합니다.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>데이터 형식 변환에 C# 식 사용

다음 예제는 C# 식을 사용하여 datetime 데이터 변환을 수행하는 방법을 보여줍니다. 이 특정 시나리오에서 datetime 문자열 데이터는 자정 00:00:00 시간 표기법을 사용하는 표준 datetime으로 변환됩니다.

```
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>오늘 날짜에 C# 식 사용

오늘 날짜를 끌어오려면 `DateTime.Now.ToString("M/d/yyyy")` C# 식을 사용할 수 있습니다.

다음은 스크립트에서 이 식을 사용하는 방법의 예입니다.

```
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET 어셈블리 사용

U-SQL 확장성 모델은 .NET 어셈블리에서 사용자 지정 코드를 추가할 수 있는 기능에 크게 의존합니다. 

### <a name="register-a-net-assembly"></a>.NET 어셈블리 등록

`CREATE ASSEMBLY` 문을 사용하여 .NET 어셈블리를 U-SQL Database에 배치합니다. 그런 다음 U-SQL 스크립트에서 `REFERENCE ASSEMBLY` 문을 사용하여 이러한 어셈블리를 사용할 수 있습니다. 

다음 코드는 어셈블리를 등록하는 방법을 보여 줍니다.

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

다음 코드는 어셈블리를 참조하는 방법을 보여 줍니다.

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

이 항목을 더 자세히 설명하는 [어셈블리 등록 지침](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/)을 참조하세요.


### <a name="use-assembly-versioning"></a>어셈블리 버전 관리 사용
현재 U-SQL은 .NET Framework 버전 4.5를 사용합니다. 따라서 자신의 어셈블리가 해당 런타임 버전과 호환되는지 확인해야 합니다.

앞에서 언급했듯이 U-SQL은 64비트(x64) 형식 코드를 실행합니다. 따라서 코드가 x64에서 실행되도록 컴파일되었는지 확인합니다. 그렇지 않으면 앞에서 설명한 잘못된 형식 오류가 발생합니다.

업로드된 각 어셈블리 DLL, 리소스 파일(예: 다양한 런타임), 네이티브 어셈블리 또는 구성 파일의 크기는 최대 400MB입니다. DEPLOY RESOURCE를 통해 또는 참조 어셈블리를 통해 배포된 리소스 및 추가 파일의 총 크기는 3GB를 초과할 수 없습니다.

마지막으로 U-SQL 데이터베이스마다 지정된 어셈블리 버전을 하나만 포함할 수 있습니다. 예를 들어 버전 7과 버전 8 NewtonSoft Json.NET 라이브러리의 모두, 필요 하면 서로 다른 두 데이터베이스에 등록 해야 합니다. 또한 각 스크립트는 지정된 어셈블리 DLL의 한 가지 버전만 참조할 수 있습니다. 이러한 점에서 U-SQL은 C# 어셈블리 관리 및 버전 관리 의미 체계를 따릅니다.

## <a name="use-user-defined-functions-udf"></a>다음 사용자 정의 함수를 사용합니다. UDF
U-SQL UDF(사용자 정의 함수)는 매개 변수를 받아들이고, 작업(예: 복잡한 계산)을 수행하며, 해당 작업의 결과를 값으로 반환하는 프로그래밍 루틴입니다. UDF의 반환 값은 단지 단일 스칼라일 수 있습니다. U-SQL UDF는 다른 C# 스칼라 함수와 같이 U-SQL 기본 스크립트에서 호출할 수 있습니다.

U-SQL 사용자 정의 함수는 **public** 및 **static**으로 초기화하는 것이 좋습니다.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

먼저 UDF를 만드는 간단한 예제를 살펴보겠습니다.

이 사용 사례 시나리오에서는 특정 사용자에 대한 첫 번째 로그인의 회계 분기 및 회계 월을 포함하는 회계 기간을 결정해야 합니다. 시나리오에서 해당 연도의 첫 번째 회계 월은 6월입니다.

회계 기간을 계산하기 위해 다음 C# 함수를 도입합니다.

```
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

단순히 회계 월과 분기를 계산하고 문자열 값을 반환합니다. 첫 번째 회계 분기의 첫 번째 월인 6월에 대해서는 "Q1:P1"을 사용합니다. 7월에 대해서는 "Q1:P2"를 사용하고 이런 방식으로 계속 적용합니다.

이것은 U-SQL 프로젝트에 사용할 일반 C# 함수입니다.

이 시나리오의 코드 숨김 섹션은 다음과 같은 모양입니다.

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

이제 기본 U-SQL 스크립트에서 이 함수를 호출할 것입니다. 이렇게 하려면 함수에 대해 네임스페이스를 포함하는 정규화된 이름을 제공해야 합니다. 이 경우 NameSpace.Class.Function(매개 변수)입니다.

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

다음은 실제 U-SQL 기본 스크립트입니다.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

다음은 스크립트 실행의 출력 파일입니다.

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

이 예제에서는 U-SQL에서 인라인 UDF를 간단하게 사용하는 방법을 보여 줍니다.

### <a name="keep-state-between-udf-invocations"></a>UDF 호출 사이 상태 유지
U-SQL C# 프로그래밍 기능 개체는 코드 숨김 전역 변수를 통한 상호 작용을 활용하여 더 정교해질 수 있습니다. 다음과 같은 비즈니스 사용 사례 시나리오를 살펴보겠습니다.

대규모 조직에서는 사용자가 다양한 내부 애플리케이션 간에 전환할 수 있습니다. 여기에는 Microsoft Dynamics CRM, PowerBI 등이 포함될 수 있습니다. 고객을 위해 사용자가 다양한 애플리케이션 사이를 전환하는 방법, 사용 추세 등에 대한 원격 분석을 적용하는 것이 필요할 수 있습니다. 비즈니스의 목표는 애플리케이션 사용을 최적화하는 것입니다. 다양한 애플리케이션이나 특정 로그인 루틴을 결합하는 것이 필요할 수 있습니다.

이러한 목표를 달성하려면 세션 ID 및 마지막으로 발생한 세션 간의 지연 시간을 판단해야 합니다.

이전 로그인을 찾은 다음 이 로그인을 동일한 애플리케이션에 생성되는 모든 세션에 할당해야 합니다. 첫 번째 과제는 U-SQL 기본 스크립트를 사용하면 LAG 함수로 이미 계산된 열에 계산을 적용할 수 없다는 점입니다. 두 번째 과제는 같은 기간 내의 모든 세션에 대해 특정 세션을 유지해야 한다는 것입니다.

이 문제를 해결하기 위해 코드 숨김 섹션 내에 전역 변수 `static public string globalSession;`을 사용하겠습니다.

이 전역 변수는 스크립트를 실행하는 동안 전체 행 집합에 적용됩니다.

다음은 U-SQL 프로그램의 코드 숨김 섹션입니다.

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

이 예제는 `getStampUserSession` 함수 내에 사용되고 Session 매개 변수가 변경될 때마다 다시 초기화되는 전역 변수 `static public string globalSession;`을 보여줍니다.

U-SQL 기본 스크립트는 다음과 같습니다.

```
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

여기서 `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` 함수는 두 번째 메모리 행 집합을 계산하는 동안 호출됩니다. `UserSessionTimestamp` 열을 전달하고 `UserSessionTimestamp`가 변경될 때까지 값을 반환합니다.

출력 파일은 다음과 같습니다.

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

이 예제는 전체 메모리 행 집합에 적용된 코드 숨김 섹션 내부에 전역 변수를 사용하는 보다 세밀한 사용 사례 시나리오를 보여줍니다.

## <a name="use-user-defined-types-udt"></a>다음 사용자 정의 형식을 사용합니다. UDT
UDT(사용자 정의 형식)는 U-SQL의 또 다른 프로그래밍 기능입니다. U-SQL UDT는 일반 C# 사용자 정의 형식처럼 작동합니다. C#는 기본 제공 및 사용자 지정 UDT를 사용할 수 있는 강력한 형식의 언어입니다.

U-SQL은 UDT가 행 집합의 꼭짓점 간에 전달되는 동안 임의의 UDT를 암시적으로 직렬화하거나 역직렬화할 수 없습니다. 다시 말해 사용자가 IFormatter 인터페이스를 사용하여 명시적인 포맷터를 제공해야 합니다. 이렇게 하면 U-SQL에 UDT에 대한 직렬화 및 역직렬화 메서드가 제공됩니다.

> [!NOTE]
> U-SQL의 기본 제공 추출기 및 출력기는 현재 IFormatter 집합이 있는 파일 사이에서 UDT 데이터를 직렬화하거나 역직렬화할 수 없습니다. 따라서 OUTPUT 문이 있는 파일에 UDT 데이터를 작성하는 경우 또는 추출기로 UDT 데이터를 읽는 경우 UDT 데이터를 문자열 또는 바이트 배열로 전달해야 합니다. 그런 다음 직렬화 및 역직렬화 코드(즉 UDT의 ToString() 메서드)를 명시적으로 호출합니다. 반면 사용자 정의 추출기 및 출력자는 UDT를 읽고 쓸 수 있습니다.

다음과 같이 (이전 SELECT의 외부에 있는) EXTRACTOR 또는 OUTPUTTER에서 UDT를 사용하려고 시도하면:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

다음 오류가 발생합니다.

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Outptutter에서 UDT를 사용하려면 ToString() 메서드를 사용하여 문자열로 직렬화하거나 사용자 지정 Outputter를 만들어야 합니다.

UDT는 현재 GROUP BY에서 사용할 수 없습니다. GROUP BY에 UDT를 사용하면 다음과 같은 오류가 발생합니다.

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

UDT를 정의하려면 다음을 수행해야 합니다.

* 다음 네임스페이스를 추가합니다.

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* UDT 인터페이스에 필요한 `Microsoft.Analytics.Interfaces`를 추가합니다. 더불어 `System.IO`가 IFormatter 인터페이스를 정의하는 데 필요할 수 있습니다.

* SqlUserDefinedType 특성을 사용하여 사용자 정의 형식을 정의합니다.

**SqlUserDefinedType**은 어셈블리의 형식 정의를 U-SQL UDT로 표시하는 데 사용됩니다. 특성의 속성에는 UDT의 물리적 특성이 반영됩니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedType은 UDT 정의에 필요한 특성입니다.

클래스 생성자:  

* SqlUserDefinedTypeAttribute(형식 포맷터)

* 형식 포맷터: UDT 포맷터를 정의하는 필수 매개 변수이며 구체적으로 `IFormatter` 인터페이스 형식이 여기로 전달되어야 합니다.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 다음 예제와 같이, 일반적인 UDT도 IFormatter 인터페이스에 대한 정의가 필요합니다.

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` 인터페이스는 \<typeparamref name="T">의 루트 형식으로 개체 그래프를 직렬화하고 역직렬화합니다.

\<typeparamref name="T"> - 직렬화/역직렬화할 개체 그래프의 루트 형식입니다.

* **Deserialize**: 제공된 스트림의 데이터를 역직렬화하고 개체 그래프를 다시 구성합니다.

* **Serialize**: 제공된 스트림에 지정된 루트를 사용하여 개체 또는 개체 그래프를 직렬화합니다.

`MyType` 인스턴스: 해당 형식 인스턴스입니다.  
`IColumnWriter` writer / `IColumnReader` reader: 기본 열 스트림입니다.  
`ISerializationContext` context: 직렬화하는 동안 스트림에 대한 원본 또는 대상 컨텍스트를 지정하는 플래그 집합을 정의하는 열거형입니다.

* **Intermediate**: 원본 또는 대상 컨텍스트가 지속형 저장소가 아님을 지정합니다.

* **Persistence**: 원본 또는 대상 컨텍스트가 지속형 저장소임을 지정합니다.

일반 C# 형식으로 U-SQL UDT 정의는 +/==/!= 등의 연산자에 대한 재정의를 포함할 수 있습니다. 정적 메서드도 포함할 수 있습니다. 예를 들어 U-SQL MIN 집계 함수에 이 UDT를 매개 변수로 사용하는 경우 < 연산자 override를 정의해야 합니다.

이 가이드의 앞부분에서 `Qn:Pn (Q1:P10)` 형식의 특정 날짜를 회계 기간으로 식별하는 예제를 보여 주었습니다. 다음 예제에서는 회계 기간 값에 대한 사용자 지정 형식을 정의하는 방법을 보여 줍니다.

다음은 사용자 지정 UDT 및 IFormatter 인터페이스가 포함된 코드 숨김 섹션의 예제입니다.

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

정의된 형식에는 두 개의 숫자(Quarter 및 Month)가 포함되어 있습니다. 여기서는 `==/!=/>/<` 연산자 및 `ToString()` 정적 메서드가 정의됩니다.

앞에서 설명한 대로 UDT는 SELECT 식에서 사용할 수 있지만, 사용자 지정 직렬화 없이는 OUTPUTTER/EXTRACTOR에서 사용할 수 없습니다. `ToString()`을 사용하여 문자열로 직렬화하거나 사용자 지정 OUTPUTTER/EXTRACTOR와 함께 사용해야 합니다.

이제 UDT 사용법에 대해 살펴보겠습니다. 코드 숨김 섹션에서 GetFiscalPeriod 함수를 다음과 같이 변경했습니다.

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

여기서 볼 수 있듯이 FiscalPeriod 형식의 값을 반환합니다.

다음은 U-SQL 기본 스크립트에서 이 값을 사용하는 방법에 대한 예제입니다. 이 예제에서는 U-SQL 스크립트에서 UDT를 호출하는 다른 형식을 보여줍니다.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

다음은 전체 코드 숨김 섹션의 예제입니다.

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>다음 사용자 정의 집계를 사용합니다. UDAGG
UDAGG(사용자 정의 집계)는 U-SQL에 제공되지 않는 집계 관련 함수입니다. 예제에는 사용자 지정 수학 계산, 문자열 연결 또는 문자열 조작 등을 수행하는 집계가 포함됩니다.

UDAGG(사용자 정의 집계) 기본 클래스 정의는 다음과 같습니다.

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate**는 UDAGG로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedType 특성은 UDAGG 정의의 **선택 사항**입니다.


기본 클래스를 사용하면 세 개의 추상 매개 변수(두 개는 입력 매개 변수로, 하나는 결과로)를 전달할 수 있습니다. 데이터 형식은 변수이며, 클래스를 상속하는 동안 정의해야 합니다.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init**는 계산하는 동안 각 그룹에 대해 한 번씩 호출됩니다. 각 집계 그룹에 대한 초기화 루틴을 제공합니다.  
* **Accumulate**는 각 값에 대해 한 번씩 실행됩니다. 집계 알고리즘의 주요 기능을 제공합니다. 클래스를 상속하는 동안 정의된 다양한 데이터 형식으로 값을 집계하는 데 사용할 수 있습니다. 변수 데이터 형식의 매개 변수 두 개를 사용할 수 있습니다.
* **Terminate**는 처리가 끝날 때 집계 그룹당 한 번씩 실행되어 각 그룹에 대한 결과를 출력합니다.

올바른 입력 및 출력 데이터 형식을 선언하려면 다음과 같이 클래스 정의를 사용합니다.

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Accumulate의 첫 번째 매개 변수
* T2: Accumulate의 두 번째 매개 변수
* TResult: Terminate의 반환 형식

예를 들면 다음과 같습니다.

```
public class GuidAggregate : IAggregate<string, int, int>
```

또는

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>U-SQL에서 UDAGG 사용
UDAGG를 사용하려면 먼저 코드 숨김에 정의하거나 앞에서 설명한 대로 기존 프로그래밍 기능 DLL에서 참조합니다.

그런 다음, 다음 구문을 사용합니다.

```
AGG<UDAGG_functionname>(param1,param2)
```

UDAGG의 예제는 다음과 같습니다.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

기본 U-SQL 스크립트:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

이 사용 사례 시나리오에서는 특정 사용자에 대한 GUID 클래스를 연결합니다.

## <a name="use-user-defined-objects-udo"></a>다음 사용자 정의 개체를 사용합니다. UDO
U-SQL을 사용하면 UDO(사용자 정의 개체)라는 사용자 지정 프로그래밍 기능 개체를 정의할 수 있습니다.

U-SQL의 UDO 목록은 다음과 같습니다.

* 사용자 정의 추출기
    * 행 단위 추출
    * 사용자 지정 구조화된 파일에서 데이터 추출을 구현하는 데 사용됨

* 사용자 정의 출력자
    * 행 단위 출력
    * 사용자 지정 데이터 형식 또는 사용자 지정 파일 형식을 출력하는 데 사용됨

* 사용자 정의 처리기
    * 한 행씩 가져와 한 행씩 생성
    * 열 수를 줄이거나 기존 열 집합에서 파생된 값으로 새 열을 생성하는 데 사용됨

* 사용자 정의 적용자
    * 한 행씩 가져와 0-n 개 행 생성
    * OUTER/CROSS APPLY 사용

* 사용자 정의 결합자
    * 행 집합 결합--사용자 정의 JOIN

* 사용자 정의 리듀서
    * n개 행을 가져와 한 행씩 생성
    * 행 수를 줄이는 데 사용됨

UDO는 일반적으로 다음 U-SQL 문의 일부로 U-SQL 스크립트에서 명시적으로 호출됩니다.

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO는 0.5Gb 메모리를 사용하도록 제한됩니다.  이 메모리 제한은 로컬 실행에 적용되지 않습니다.

## <a name="use-user-defined-extractors"></a>UDE(사용자 정의 추출기) 사용
U-SQL을 사용하면 EXTRACT 문을 사용하여 외부 데이터를 가져올 수 있습니다. EXTRACT 문은 기본 제공 UDO 추출기를 사용할 수 있습니다.  

* *Extractors.Text()*: 다른 인코딩의 구분 기호로 분리된 텍스트 파일에서 데이터를 추출합니다.

* *Extractors.Csv()*: 다른 인코딩의 쉼표로 구분된 값(CSV) 파일에서 데이터를 추출합니다.

* *Extractors.Tsv()*: 다른 인코딩의 탭으로 구분된 값(TSV) 파일에서 데이터를 추출합니다.

사용자 지정 추출기를 개발하는 것이 유용할 수 있습니다. 다음 작업 중 하나를 수행하려는 경우 데이터를 가져오는 동안 도움이 될 수 있습니다.

* 열을 분할하고 개별 값을 수정하여 입력 데이터를 수정합니다. PROCESSOR 기능이 열을 결합하는 데 더 좋습니다.
* 구조화되지 않은 데이터(예: 웹 페이지 및 전자 메일) 또는 반 구조화되지 않은 데이터(예: XML/JSON)를 구문 분석합니다.
* 지원되지 않는 인코딩 데이터를 구문 분석합니다.

UDE(사용자 정의 추출기)를 정의하려면 `IExtractor` 인터페이스를 만들어야 합니다. 열/행 구분 기호 및 인코딩 등과 같은 추출기에 대한 모든 입력 매개 변수는 클래스의 생성자에서 정의해야 합니다. 다음과 같이 `IExtractor` 인터페이스는 `IEnumerable<IRow>` override에 대한 정의도 포함해야 합니다.

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** 특성은 UDE(사용자 정의 추출기)로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedExtractor는 UDE 정의의 선택적 특성이며, UDE 개체의 AtomicFileProcessing 속성을 정의하는 데 사용됩니다.

* bool AtomicFileProcessing   

* **true**는 추출기에 원자성 입력 파일(JSON, XML 등)이 필요함을 나타냅니다.
* **false**는 추출기에서 분할/분산 파일(CSV, SEQ 등)을 처리할 수 있음을 나타냅니다.

주 UDE 프로그래밍 개체는 **input** 및 **output**입니다. input 개체는 입력 데이터를 `IUnstructuredReader`로 열거하는 데 사용됩니다. output 개체는 출력 데이터를 추출기 작업의 결과로 설정하는 데 사용됩니다.

입력 데이터는 `System.IO.Stream` 및 `System.IO.StreamReader`를 통해 액세스됩니다.

입력 열 열거형의 경우 먼저 행 구분 기호를 사용하여 입력 스트림을 분할합니다.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

그런 다음 입력 행을 열 파트로 세분합니다.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

출력 데이터를 설정하려면 `output.Set` 메서드를 사용합니다.

사용자 지정 추출기는 출력에 정의된 열 및 값만 출력한다는 점을 이해하는 것이 중요합니다. 메서드 호출을 설정합니다.

```
output.Set<string>(count, part);
```

실제 추출기 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 추출기 예제입니다.

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

이 사용 사례 시나리오에서 추출기는 “guid” 열에 대한 GUID를 다시 생성하고 “user” 열의 값을 대문자로 변환합니다. 사용자 지정 추출기는 입력 데이터를 구문 분석하고 이를 조작하여 더 복잡한 결과를 생성할 수 있습니다.

다음은 사용자 지정 추출기를 사용하는 기본 U-SQL 스크립트입니다.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>사용자 정의 출력자 사용
사용자 정의 출력자는 기본 제공 U-SQL 기능을 확장할 수 있는 또 다른 U-SQL UDO입니다. 추출기와 마찬가지로 여러 개의 기본 제공 출력자가 있습니다.

* *Outputters.Text()*: 다른 인코딩의 구분 기호로 분리된 텍스트 파일에 데이터를 씁니다.
* *Outputters.Csv()*: 다른 인코딩의 쉼표로 구분된 값(CSV) 파일에 데이터를 씁니다.
* *Outputters.Tsv()*: 다른 인코딩의 탭으로 구분된 값(TSV) 파일에 데이터를 씁니다.

사용자 지정 출력자를 사용하면 사용자 정의된 형식으로 데이터를 쓸 수 있습니다. 이 기능은 다음 작업에 유용할 수 있습니다.

* 반 구조화되거나 구조화되지 않은 파일에 데이터 쓰기
* 지원되지 않는 인코딩 데이터 쓰기
* 출력 데이터 수정 또는 사용자 지정 특성 추가

사용자 정의 출력자를 정의하려면 `IOutputter` 인터페이스를 생성해야 합니다.

다음은 기본 `IOutputter` 클래스 구현입니다.

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

열/행 구분 기호, 인코딩 등과 같은 출력자에 대한 모든 입력 매개 변수는 클래스의 생성자에서 정의해야 합니다. `IOutputter` 인터페이스는 `void Output` override에 대한 정의도 포함해야 합니다. `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 특성은 원자성 파일 처리를 위해 선택적으로 설정될 수 있습니다. 자세한 내용은 다음 세부 정보를 참조하세요.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output`은 각 입력 행에 대해 호출됩니다. `IUnstructuredWriter output` 행 집합을 반환합니다.
* 생성자 클래스는 사용자 정의 출력자에 매개 변수를 전달하는 데 사용됩니다.
* `Close`는 비용이 높은 상태를 해제하거나 마지막 행이 기록된 시점을 판단하기 위해 선택적으로 재정의하는 데 사용됩니다.

**SqlUserDefinedOutputter** 특성은 사용자 정의 출력자로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedOutputter는 사용자 정의 출력자 정의의 선택적 특성이며, AtomicFileProcessing 속성을 정의하는 데 사용됩니다.

* bool AtomicFileProcessing   

* **true**는 출력자에 원자성 출력 파일(JSON, XML 등)이 필요함을 나타냅니다.
* **false**는 출력자에서 분할/분산 파일(CSV, SEQ 등)을 처리할 수 있음을 나타냅니다.

주요 프로그래밍 개체는 **row** 및 **output**입니다. **row** 개체는 출력 데이터를 `IRow` 인터페이스로 나열하는 데 사용됩니다. **Output**은 대상 파일에 출력 데이터를 설정하는 데 사용됩니다.

출력 데이터는 `IRow` 인터페이스를 통해 액세스됩니다. 출력 데이터는 한 번에 한 행씩 전달됩니다.

개별 값은 IRow 인터페이스의 Get 메서드를 호출하여 열거됩니다.

```
row.Get<string>("column_name")
```

개별 열 이름은 `row.Schema`를 호출하여 결정될 수 있습니다.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

이렇게 하면 모든 메타데이터 스키마에 대해 유연한 출력자를 빌드할 수 있습니다.

출력 데이터는 `System.IO.StreamWriter`를 사용하여 파일에 기록됩니다. Stream 매개 변수는 `IUnstructuredWriter output`의 일부로 `output.BaseStream`로 설정됩니다.

각 행을 반복한 후에 파일에 데이터 버퍼를 플러시하는 것이 중요합니다. 또한 Disposable 특성이 활성화된 상태에서(기본값) **using** 키워드와 함께 `StreamWriter` 개체를 사용해야 합니다.

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

그렇지 않으면 매번 반복 후에 Flush() 메서드를 명시적으로 호출합니다. 이 내용은 다음 예제에 표시되어 있습니다.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>사용자 정의 출력자의 머리글 및 바닥글 설정
헤더를 설정하려면 단일 반복 실행 흐름을 사용합니다.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

첫 번째 `if (isHeaderRow)` 블록의 코드는 한 번만 실행됩니다.

바닥글의 경우 `System.IO.Stream` 개체(`output.BaseStream`)의 인스턴스에 대한 참조를 사용합니다. `IOutputter` 인터페이스의 Close() 메서드에 바닥글을 작성합니다.  (자세한 내용은 다음 예제를 참조하세요.)

다음의 사용자 정의 출력자에 대한 예제입니다.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

U-SQL 기본 스크립트:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

HTML 출력자이며, 테이블 데이터로 HTML 파일을 만듭니다.

### <a name="call-outputter-from-u-sql-base-script"></a>U-SQL 기본 스크립트에서 출력자 호출
기본 U-SQL 스크립트에서 사용자 지정 출력자를 호출하려면 출력자 개체의 새 인스턴스를 만들어야 합니다.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

앞의 예에서 설명했듯이, 기본 스크립트로 개체 인스턴스를 작성하지 않기 위해 함수 래퍼를 만들 수 있습니다.

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

이 경우 원래 호출은 다음과 같은 모양입니다.

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>UDP(사용자 정의 처리기) 사용
UDP(사용자 정의 처리기)는 프로그래밍 기능을 적용하여 들어오는 행을 처리할 수 있는 U-SQL UDO의 형식입니다. UDP를 사용하면 열을 결합하고, 값을 수정하고, 필요한 경우 새 열을 추가할 수 있습니다. 기본적으로 필요한 데이터 요소를 생성하기 위해 행 집합을 처리하는 데 도움이 됩니다.

UDP를 정의하려면 UDP의 선택 사항인 `SqlUserDefinedProcessor` 특성을 포함한 `IProcessor` 인터페이스를 생성해야 합니다.

다음 예제와 같이 이 인터페이스는 `IRow` 인터페이스 행 집합 override에 대한 정의를 포함해야 합니다.

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor**는 UDP로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

SqlUserDefinedProcessor 특성은 UDP 정의의 **선택 사항**입니다.

주 프로그래밍 개체는 **input** 및 **output**입니다. input 개체는 입력 열을 열거하고, output 개체는 출력 데이터를 처리기 작업의 결과로 설정하는 데 사용됩니다.

입력 열을 열거하는 경우 `input.Get` 메서드를 사용합니다.

```
string column_name = input.Get<string>("column_name");
```

`input.Get` 메서드에 대한 매개 변수는 U-SQL 기본 스크립트의 `PROCESS` 문의 `PRODUCE` 절의 일부로 전달되는 열입니다. 여기서는 올바른 데이터 형식을 사용해야 합니다.

출력의 경우 `output.Set` 메서드를 사용합니다.

사용자 지정 생산자는 `output.Set` 메서드 호출로 정의된 열과 값만을 출력한다는 점을 아는 것이 중요합니다.

```
output.Set<string>("mycolumn", mycolumn);
```

실제 처리기 출력은 `return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 처리기 예제입니다.

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

이 사용 사례 시나리오에서 처리기는 기존 열, 이 경우 “user” 대문자와 “des”를 결합하여 “full_description”이라는 새 열을 생성합니다. 또한 GUID를 다시 생성하고 원래 및 새 GUID 값을 반환합니다.

이전 예제에서 볼 수 있듯이 `output.Set` 메서드 호출 중에 C# 메서드를 호출할 수 있습니다.

다음은 사용자 지정 처리기를 사용하는 기본 U-SQL 스크립트의 예제입니다.

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>사용자 정의 적용자 사용
U-SQL 사용자 정의 적용자를 사용하면 쿼리의 외부 테이블 식에서 반환한 각 행에 대해 사용자 지정 C # 함수를 호출할 수 있습니다. 오른쪽 입력은 왼쪽 입력에서 각 행에 대해 평가되고, 생성된 행은 최종 출력을 위해 결합됩니다. APPLY 연산자에서 생성한 열 목록은 왼쪽 및 오른쪽 입력에 있는 열 집합의 조합입니다.

사용자 정의 적용자는 U-SQL SELECT 식의 일부로 호출됩니다.

사용자 정의 적용자에 대한 일반적인 호출은 다음과 같은 모양입니다.

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

SELECT 식의 적용자 사용에 대한 자세한 내용은 [U-SQL SELECT: CROSS APPLY 및 OUTER APPLY에서 선택](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)(영문)을 참조하세요.

사용자 정의 적용자 기본 클래스 정의는 다음과 같습니다.

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

사용자 정의 적용자를 정의하려면 사용자 정의 적용자 정의에서 선택적인 [`SqlUserDefinedApplier`] 특성을 포함한 `IApplier` 인터페이스를 생성해야 합니다.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply는 Outer 테이블의 각 행에 대해 호출되며, `IUpdatableRow` 출력 행 집합을 반환합니다.
* 생성자 클래스는 사용자 정의 적용자에 매개 변수를 전달하는 데 사용됩니다.

**SqlUserDefinedApplier**는 사용자 정의 적용자로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

**SqlUserDefinedApplier**는 사용자 정의 적용자 정의의 **선택 사항**입니다.


주요 프로그래밍 개체는 다음과 같습니다.

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

입력 행 집합은 `IRow` 입력으로 전달됩니다. 출력 행은 `IUpdatableRow` 출력 인터페이스로 생성됩니다.

개별 열 이름은 `IRow` 스키마 메서드를 호출하여 결정될 수 있습니다.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

들어오는 `IRow`에서 실제 데이터 값을 가져오려면 `IRow` 인터페이스의 Get() 메서드를 사용합니다.

```
mycolumn = row.Get<int>("mycolumn")
```

또는 스키마 열 이름을 사용합니다.

```
row.Get<int>(row.Schema[0].Name)
```

출력 값은 `IUpdatableRow` 출력으로 설정해야 합니다

```
output.Set<int>("mycolumn", mycolumn)
```

사용자 지정 적용자는 `output.Set` 메서드 호출로 정의된 열과 값만 출력한다는 점을 이해하는 것이 중요합니다.

실제 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

사용자 정의 적용자 매개 변수는 생성자에 전달될 수 있습니다. 적용자는 기본 U-SQL 스크립트에서 Applier를 호출하는 동안 정의되어야 하는 가변 개수의 열을 반환할 수 있습니다.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

다음은 사용자 정의 적용자 예제입니다.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

다음은 이 사용자 정의 적용자에 대한 기본 U-SQL 스크립트입니다.

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

이 사용 사례 시나리오에서 사용자 정의 적용자는 car fleet 속성에 대한 쉼표로 구분된 값 파서 역할을 수행합니다. 입력 파일 행은 다음과 같은 모양입니다.

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

이 파일은 제조업체 모델 등의 자동차 속성을 포함하는 속성 열이 탭으로 분리된 일반적인 TSV 파일입니다. 이러한 속성은 테이블 열로 구문 분석되어야 합니다. 제공된 적용자를 사용하면 전달되는 매개 변수를 기반으로 결과 행 집합에 동적인 수의 속성을 생성할 수 있습니다. 모든 속성을 생성하거나 특정 속성 집합만 생성할 수 있습니다.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

사용자 정의 적용자는 applier 개체의 새 인스턴스로 호출될 수 있습니다.

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

또는 래퍼 팩터리 메서드 호출을 통해 호출될 수 있습니다.

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>사용자 정의 결합자 사용
UDC(사용자 정의 결합자)를 사용하면 사용자 지정 논리를 기반으로 하여 왼쪽 및 오른쪽 행 집합의 행을 결합할 수 있습니다. UDC는 COMBINE 식으로 사용됩니다.

결합자는 입력 행 집합, 그룹화 열, 예상 결과 스키마 및 추가 정보에 대해 필요한 정보를 제공하는 COMBINE 식으로 호출됩니다.

기본 U-SQL 스크립트에서 결합자를 호출하려면 다음 구문을 사용합니다.

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

자세한 내용은 [COMBINE 식(U-SQL)](/u-sql/statements-and-expressions/combine-expression)을 참조하세요.

UDC를 정의하려면 UDC 정의에서 선택적인 [`SqlUserDefinedCombiner`] 속성을 포함한 `ICombiner` 인터페이스를 만들어야 합니다.

기본 `ICombiner` 클래스 정의:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

`ICombiner` 인터페이스의 사용자 지정 구현은 `IEnumerable<IRow>` Combine override에 대한 정의를 포함해야 합니다.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** 특성은 사용자 정의 결합자로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.

**SqlUserDefinedCombiner**는 결합자 모드 속성을 정의하는 데 사용됩니다. 사용자 정의 결합자 정의의 선택적 특성입니다.

CombinerMode Mode

CombinerMode 열거형은 다음 값을 포함할 수 있습니다.

* Full  (0) 모든 출력 행이 잠재적으로 왼쪽과 오른쪽에서 동일한 키 값을 가진 모든 입력 행에 따라 달라집니다.

* Left  (1) 모든 출력 행이 왼쪽에서 단일 입력 행(그리고 잠재적으로 오른쪽에서 동일한 키 값을 가진 모든 행)에 따라 달라집니다.

* Right (2)     모든 출력 행이 오른쪽에서 단일 입력 행(그리고 잠재적으로 왼쪽에서 동일한 키 값을 가진 모든 행)에 따라 달라집니다.

* Inner (3) 모든 출력 행이 왼쪽과 오른쪽에서 동일한 값을 가진 단일 입력 행에 따라 달라집니다.

예: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


주요 프로그래밍 개체:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

입력 행 집합은 **left** 및 **right** `IRowset` 형식의 인터페이스로 전달됩니다. 두 행 집합은 처리하기 위해 모두 열거되어야 합니다. 각 인터페이스를 한 번만 열거할 수 있으므로 필요에 따라 열거하고 캐시해야 합니다.

캐싱을 위해 LINQ 쿼리 실행의 결과로 List\<T\> 형식의 메모리 구조체를 만들 수 있습니다(구체적으로 List<`IRow`>). 익명 데이터 형식도 열거하는 동안에 사용할 수 있습니다.

LINQ 쿼리에 대한 자세한 내용은 [LINQ 쿼리 소개(C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)를 참조하고, IEnumerable\<T\> 인터페이스에 대한 자세한 내용은 [IEnumerable\<T\> 인터페이스](/dotnet/api/system.collections.generic.ienumerable-1)를 참조하세요.

들어오는 `IRowset`에서 실제 데이터 값을 가져오려면 `IRow` 인터페이스의 Get() 메서드를 사용합니다.

```
mycolumn = row.Get<int>("mycolumn")
```

개별 열 이름은 `IRow` 스키마 메서드를 호출하여 결정될 수 있습니다.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

또는 스키마 열 이름을 사용합니다.

```
c# row.Get<int>(row.Schema[0].Name)
```

LINQ를 사용한 일반적인 열거형은 다음과 같은 모양입니다.

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

두 가지 행 집합을 열거한 후 모든 행을 반복할 예정입니다. 왼쪽 행 집합의 각 행에 대해 결합자의 조건을 충족하는 모든 행을 찾을 예정입니다.

출력 값은 `IUpdatableRow` 출력으로 설정해야 합니다.

```
output.Set<int>("mycolumn", mycolumn)
```

실제 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 결합자 예제입니다.

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

이 사용 사례 시나리오에서는 판매점에 대한 분석 보고서를 빌드합니다. 목표는 $20,000이 넘고 특정 시간 프레임 내에 일반 판매점보다 인터넷 웹 사이트를 통해 빠르게 판매된 모든 제품을 찾는 것입니다.

다음은 기본 U-SQL 스크립트이며, 일반 JOIN과 결합자 사이의 논리를 비교할 수 있습니다.

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

사용자 정의 결합자는 applier 개체의 새 인스턴스로 호출될 수 있습니다.

```
USING new MyNameSpace.MyCombiner();
```


또는 래퍼 팩터리 메서드 호출을 통해 호출될 수 있습니다.

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>사용자 정의 리듀서 사용

U-SQL을 사용하면 IReducer 인터페이스를 구현하고 사용자 정의 연산자 확장성 프레임워크를 사용하여 C#으로 사용자 지정 행 집합 리듀서를 작성할 수 있습니다.

UDR(사용자 정의 리듀서)은 데이터를 추출하는(가져오기) 동안 불필요한 행을 제거하는 데 사용할 수 있습니다. 행 및 열을 조작하고 평가하는 데 사용할 수도 있습니다. 프로그래밍 기능 논리를 기반으로 추출해야 하는 행을 정의할 수도 있습니다.

UDR 클래스를 정의하려면 선택적 `SqlUserDefinedReducer` 특성을 사용하여 `IReducer` 인터페이스를 생성해야 합니다.

이 클래스 인터페이스는 `IEnumerable` 인터페이스 행 집합 override에 대한 정의를 포함해야 합니다.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** 특성은 사용자 정의 리듀서로 등록해야 하는 형식임을 나타냅니다. 이 클래스는 상속될 수 없습니다.
**SqlUserDefinedReducer**는 UDR(사용자 정의 리듀서) 정의의 선택적 특성이며, IsRecursive 속성을 정의하는 데 사용됩니다.

* bool IsRecursive    
* **true** = 이 리듀서가 결합적이고 교환 가능한지 여부를 나타냅니다.

주 프로그래밍 개체는 **input** 및 **output**입니다. input 개체는 입력 행을 열거하는 데 사용됩니다. Output 개체는 감소 작업의 결과로 출력 행을 설정하는 데 사용됩니다.

입력 행 열거형의 경우 `Row.Get` 메서드를 사용합니다.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` 메서드에 대한 매개 변수는 U-SQL 기본 스크립트의 `REDUCE` 문의 `PRODUCE` 클래스의 일부로 전달되는 열입니다. 여기서도 올바른 데이터 형식을 사용해야 합니다.

출력의 경우 `output.Set` 메서드를 사용합니다.

사용자 지정 리듀서는 `output.Set` 메서드 호출로 정의된 값만 출력한다는 것을 이해하는 것이 중요합니다.

```
output.Set<string>("mycolumn", guid);
```

실제 리듀서 출력은 `yield return output.AsReadOnly();`를 호출하여 트리거됩니다.

다음은 리듀서 예제입니다.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

이 사용 사례 시나리오에서 리듀서는 사용자 이름이 비어 있는 행을 건너 뜁니다. 행 집합의 각 행에 대해 각각의 필수 열을 읽은 다음 사용자 이름의 길이를 평가합니다. 사용자 이름 값 길이가 0보다 큰 경우에만 실제 행을 출력합니다.

다음은 사용자 지정 리듀서를 사용하는 기본 U-SQL 스크립트입니다.

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
