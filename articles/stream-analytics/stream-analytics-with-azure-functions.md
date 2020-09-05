---
title: 자습서 - Azure Stream Analytics 작업에서 Azure Functions 실행
description: 이 자습서에서는 Stream Analytics 작업에 대한 출력 싱크로 Azure Functions를 구성하는 방법에 대해 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 70ea5ec9ee91fdba8023b9c6af1ce65b691a17fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006893"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>자습서: Azure Stream Analytics 작업에서 Azure Functions 실행 

Functions를 Stream Analytics 작업에 대한 출력 싱크 중 하나로 구성하면 Azure Stream Analytics에서 Azure Functions를 실행할 수 있습니다. Functions는 Azure 또는 타사 서비스에서 발생하는 이벤트로 트리거되는 코드를 구현할 수 있게 해주는 이벤트 중심의 컴퓨팅 온 디맨드 환경입니다. 트리거에 응답하는 이러한 Functions 기능 때문에 Stream Analytics 작업에 대한 출력이 자연스럽게 제공됩니다.

Stream Analytics는 HTTP 트리거를 통해 Functions를 호출합니다. Functions 출력 어댑터를 통해 사용자는 Functions를 Stream Analytics에 연결할 수 있으므로, Stream Analytics 쿼리를 기준으로 그러한 이벤트를 트리거할 수 있습니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Stream Analytics 작업 만들기 및 실행
> * Azure Cache for Redis 인스턴스 만들기
> * Azure Function 만들기
> * Azure Cache for Redis에서 결과 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Stream Analytics 작업을 구성하여 함수 실행 

이 섹션에서는 Azure Cache for Redis에 데이터를 쓰는 함수를 실행하도록 Stream Analytics 작업을 구성하는 방법을 보여 줍니다. Stream Analytics 작업은 Azure Event Hubs에서 이벤트를 읽고 이 함수를 호출하는 쿼리를 실행합니다. 이 함수는 Stream Analytics 작업에서 데이터를 읽고, 이를 Azure Cache for Redis에 씁니다.

![Azure 서비스 간 관계를 보여주는 다이어그램](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Event Hubs에서 입력으로 사용할 Stream Analytics 작업 만들기

[실시간 사기 감지](stream-analytics-real-time-fraud-detection.md) 자습서에 따라 이벤트 허브를 만들고, 이벤트 생성자 애플리케이션을 시작하고, Stream Analytics 작업을 만듭니다. 쿼리 및 출력을 만드는 단계는 건너뜁니다. 대신 다음 섹션을 참조해서 Azure Functions 출력을 설정합니다.

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 만들기

1. [캐시 만들기](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)에서 설명한 단계를 사용하여 캐시를 Azure Cache for Redis에 만듭니다.  

2. 캐시를 만든 다음 **설정** 아래에서 **액세스 키**를 선택합니다. **기본 연결 문자열**을 기록해 둡니다.

   ![Azure Cache for Redis 연결 문자열의 스크린샷](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Azure Functions에서 데이터를 Azure Cache for Redis에 쓸 수 있는 함수 만들기

1. Functions 설명서의 [함수 앱 만들기](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) 섹션을 참조하세요. 이 섹션에서는 CSharp 언어를 사용하여 함수 앱 및 [Azure Functions의 HTTP 트리거 함수](../azure-functions/functions-create-first-azure-function.md#create-function)를 만드는 방법을 안내합니다.  

2. **run.csx** 함수를 찾습니다. 다음 코드로 업데이트합니다. **"\<your Azure Cache for Redis connection string goes here\>"** 를 이전 섹션에서 검색한 Azure Cache for Redis 기본 연결 문자열로 바꿉니다. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Stream Analytics가 함수에서 "HTTP 요청 엔터티가 너무 큼" 예외를 수신할 경우 Functions로 보내는 일괄 처리 크기를 줄입니다. 다음 코드는 Stream Analytics가 너무 큰 일괄 처리를 보내지 않도록 합니다. 함수에 사용되는 최대 일괄 처리 수 및 크기 값이 Stream Analytics 포털에 입력한 값과 일치하는지 확인합니다.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. 원하는 텍스트 편집기에서 이름이 **project.json**인 JSON 파일을 만듭니다. 다음 코드를 붙여넣고 이를 로컬 컴퓨터에 저장합니다. 이 파일에는 C# 함수에 필요한 NuGet 패키지 종속성이 포함됩니다.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Azure 포털로 돌아갑니다. **플랫폼 기능** 탭에서 함수를 찾습니다. **개발 도구** 아래에서 **앱 서비스 편집기**를 선택합니다. 
 
   ![앱 서비스 편집기 스크린샷](./media/stream-analytics-with-azure-functions/image3.png)

5. 앱 서비스 편집기에서 루트 디렉터리를 마우스 오른쪽 단추로 누르고 **project.json** 파일을 업로드합니다. 업로드가 성공한 후 페이지를 새로 고칩니다. 이제 이름이 **project.lock.json**인 자동 생성된 파일이 표시됩니다. 자동 생성된 파일에는 project.json 파일에 지정된 .dll 파일에 대한 참조가 포함됩니다.  

   ![앱 서비스 편집기 스크린샷](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>출력으로 사용할 함수로 Stream Analytics 작업 업데이트

1. Azure 포털에서 Stream Analytics 작업을 엽니다.  

2. 함수를 찾아서 **개요** > **출력** > **추가**를 선택합니다. 새 출력을 추가하려면 싱크 옵션에 대해 **Azure Function**을 선택합니다. Functions 출력 어댑터에는 다음과 같은 속성이 있습니다.  

   |**속성 이름**|**설명**|
   |---|---|
   |출력 별칭| 작업 쿼리에서 출력을 참조하기 위해 사용하는 친숙한 이름입니다. |
   |가져오기 옵션| 현재 구독에서 함수를 사용하거나 함수가 다른 구독에 있는 경우 설정을 수동으로 제공할 수 있습니다. |
   |함수 앱| Functions 앱의 이름입니다. |
   |함수| Functions 앱의 이름(run.csx 함수 이름)입니다.|
   |최대 일괄 처리 크기|함수로 전송되는 각 출력 일괄 처리의 최대 크기(바이트)를 설정합니다. 기본적으로 이 값은 256KB(262,144바이트)로 설정됩니다.|
   |최대 일괄 처리 수|함수로 전송되는 각 일괄 처리에서 최대 이벤트 수를 지정합니다. 기본값은 100입니다. 이 속성은 선택 사항입니다.|
   |키|다른 구독의 함수를 사용할 수 있습니다. 함수에 액세스하기 위한 키 값을 제공합니다. 이 속성은 선택 사항입니다.|

3. 출력 별칭의 이름을 제공합니다. 이 자습서에서는 이름이 **saop1**로 지정되어 있으며 원하는 이름을 사용할 수 있습니다. 기타 세부 정보를 채웁니다.

4. Stream Analytics 작업을 열고 쿼리를 다음과 같이 업데이트합니다. 출력 싱크 이름을 **saop1**로 지정하지 않은 경우 쿼리에서 변경해야 합니다.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. 명령줄에서 다음 명령을 실행하여 telcodatagen.exe 애플리케이션을 시작합니다. 명령은 `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]` 형식을 사용합니다.  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Stream Analytics 작업을 시작합니다.

## <a name="check-azure-cache-for-redis-for-results"></a>Azure Cache for Redis에서 결과 확인

1. Azure Portal로 이동하여 Azure Cache for Redis를 찾습니다. **콘솔**을 선택합니다.  

2. [Azure Cache for Redis 명령](https://redis.io/commands)을 사용하여 데이터가 Azure Cache for Redis에 있는지 확인합니다. (이 명령은 Get {key} 형식을 사용합니다.) 다음은 그 예입니다.

   **Get "12/19/2017 21:32:24 - 123414732"**

   이 명령은 지정된 키에 대해 값을 인쇄합니다.

   ![Azure Cache for Redis 출력의 스크린샷](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>오류 처리 및 재시도

이벤트를 Azure Functions에 전송하는 동안 오류가 발생하면 Stream Analytics는 대부분의 작업을 다시 시도합니다. 모든 http 예외는 http 오류 413(엔터티가 너무 큼) 예외를 제외하고 성공할 때까지 다시 시도됩니다. 엔터티가 너무 큰 오류는 [정책 다시 시도 또는 삭제](stream-analytics-output-error-policy.md)가 적용되는 데이터 오류로 처리됩니다.

> [!NOTE]
> Stream Analytics에서 Azure Functions로의 HTTP 요청에 대한 시간 제한은 100초로 설정됩니다. Azure Functions 앱에서 일괄 처리하는 데 100초 넘게 걸리는 경우 Stream Analytics 오류가 발생합니다.

## <a name="known-issues"></a>알려진 문제

Azure 포털에서 최대 일괄 처리 크기/최대 일괄 처리 수 값을 빈 값(기본값)으로 재설정하려고 시도하면, 저장할 때 값이 이전에 입력된 값으로 다시 변경됩니다. 이 경우 이러한 필드에 대해 기본값을 수동으로 입력하십시오.

Azure Functions에서 [HTTP 라우팅](https://docs.microsoft.com/sandbox/functions-recipes/routes?tabs=csharp)을 사용하는 것은 현재 Stream Analytics에서 지원되지 않습니다.

가상 네트워크에서 호스팅되는 Azure Functions에 연결하기 위한 지원은 사용하도록 설정되지 않습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용된 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 중지하고 나중에 필요할 때 다시 시작할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음 단계를 사용하여 이 빠른 시작에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 클릭한 다음 만든 리소스의 이름을 클릭합니다.  
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 함수를 실행하는 간단한 Stream Analytics 작업을 만들었습니다. Stream Analytics 작업에 대해 자세히 알아보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Stream Analytics 작업 내에서 JavaScript 사용자 정의 함수 실행](stream-analytics-javascript-user-defined-functions.md)
