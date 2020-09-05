---
title: '자습서: 데이터 레이크 캡처 패턴을 구현하여 Azure Databricks Delta 테이블 업데이트 | Microsoft Docs'
description: 이 자습서에서는 Event Grid 구독, Azure Function 및 Azure Databricks 작업을 사용하여 Azure Data Lake Storage Gen2에 저장된 테이블에 데이터 행을 삽입하는 방법을 보여 줍니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: 8df4de01750de92222bfa9021b66828927804e85
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005482"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>자습서: 데이터 레이크 캡처 패턴을 구현하여 Databricks Delta 테이블 업데이트

이 자습서에서는 계층 구조 네임스페이스가 있는 스토리지 계정에서 이벤트를 처리하는 방법을 보여 줍니다.

판매 주문을 설명하는 csv(쉼표로 구분된 값) 파일을 업로드하여 사용자가 Databricks 델타 테이블을 채울 수 있도록 하는 작은 솔루션을 구축합니다. 이 솔루션은 Azure Databricks에서 Event Grid 구독, Azure Function 및 [작업](https://docs.azuredatabricks.net/user-guide/jobs.html)을 함께 연결하여 구축됩니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Function을 호출하는 Event Grid 구독을 만듭니다.
> * 이벤트에서 알림을 받은 후 Azure Databricks에서 작업을 실행하는 Azure Function을 만듭니다.
> * 고객 주문을 삽입하는 Databricks 작업을 스토리지 계정에 있는 Databricks 델타 테이블에 만듭니다.

Azure Databricks 작업 영역에서 시작하여 이 솔루션을 역순으로 구축합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* 계층 구조 네임스페이스(Azure Data Lake Storage Gen2)가 있는 스토리지 계정을 만듭니다. 이 자습서에서는 `contosoorders`라는 스토리지 계정을 사용합니다. 사용자 계정에 [Storage Blob 데이터 기여자 역할](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)이 할당되었는지 확인합니다.

  [Azure Data Lake Storage Gen2 계정 만들기](data-lake-storage-quickstart-create-account.md)를 참조하세요.

* 서비스 주체를 생성합니다. [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)를 참조하세요.

  해당 문서의 단계를 수행할 때 해야 하는 두어 가지 항목이 있습니다.

  :heavy_check_mark: 문서의 [애플리케이션을 역할에 할당](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) 섹션에 있는 단계를 수행할 때 **Storage Blob 데이터 참가자** 역할을 서비스 주체에 할당해야 합니다.

  > [!IMPORTANT]
  > 역할을 Data Lake Storage Gen2 스토리지 계정의 범위에 할당해야 합니다. 역할은 부모 리소스 그룹 또는 구독에 할당할 수 있지만, 이러한 역할 할당이 스토리지 계정에 전파될 때까지 권한 관련 오류가 발생합니다.

  :heavy_check_mark: 문서의 [로그인을 위한 값 가져오기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 섹션에 있는 단계를 수행하는 경우 테넌트 ID, 앱 ID 및 암호 값을 텍스트 파일에 붙여넣습니다. 나중에 이러한 값이 필요합니다.

## <a name="create-a-sales-order"></a>판매 주문 만들기

먼저 판매 주문을 설명하는 csv 파일을 만든 다음, 해당 파일을 스토리지 계정에 업로드합니다. 나중에 이 파일의 데이터를 사용하여 Databricks 델타 테이블의 첫 번째 행을 채웁니다.

1. Azure Storage Explorer를 엽니다. 그런 다음, 스토리지 계정으로 이동하고 **Blob 컨테이너** 섹션에서 **data**라는 새 컨테이너를 만듭니다.

   ![데이터 폴더](./media/data-lake-storage-events/data-container.png "데이터 폴더")

   Storage Explorer를 사용하는 방법에 대한 자세한 내용은 [Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2 계정에서 데이터 관리](data-lake-storage-explorer.md)를 참조하세요.

2. **data** 컨테이너에서 **input**이라는 폴더를 만듭니다.

3. 다음 텍스트를 텍스트 편집기에 붙여넣습니다.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. 이 파일을 로컬 컴퓨터에 저장하고, **data.csv**라는 이름을 지정합니다.

5. Storage Explorer에서 이 파일을 **input** 폴더에 업로드합니다.  

## <a name="create-a-job-in-azure-databricks"></a>Azure Databricks에 작업 만들기

이 섹션에서 수행하는 작업은 다음과 같습니다.

* Azure Databricks 작업 영역 만들기
* Notebook을 만듭니다.
* Databricks 델타 테이블을 만들고 채웁니다.
* 행을 Databricks 델타 테이블에 삽입하는 코드를 추가합니다.
* 작업을 만듭니다.

### <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 Azure Portal을 사용하여 Azure Databricks 작업 영역을 만듭니다.

1. Azure Portal에서 **리소스 만들기** > **분석** > **Azure Databricks**를 차례로 선택합니다.

    ![Azure Portal의 Databricks](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Azure Portal의 Databricks")

2. **Azure Databricks 서비스** 아래에서 Databricks 작업 영역을 만들기 위한 값을 제공합니다.

    ![Azure Databricks 작업 영역 만들기](./media/data-lake-storage-events/new-databricks-service.png "Azure Databricks 작업 영역 만들기")

    작업 영역 생성에는 몇 분 정도가 소요됩니다. 작업 상태를 모니터링하려면 맨 위에 있는 진행률 표시줄을 확인합니다.

### <a name="create-a-spark-cluster-in-databricks"></a>Databricks에서 Spark 클러스터 만들기

1. [Azure Portal](https://portal.azure.com)에서 만든 Azure Databricks 작업 영역으로 이동한 다음, **작업 영역 시작**을 선택합니다.

2. Azure Databricks 포털로 리디렉션됩니다. 포털에서 **새로 만들기** > **클러스터**를 차례로 선택합니다.

    ![Azure의 Databricks](./media/data-lake-storage-events/databricks-on-azure.png "Azure의 Databricks")

3. **새 클러스터** 페이지에서 값을 제공하여 클러스터를 만듭니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

    다음 항목 이외의 다른 모든 기본값을 허용합니다.

    * 클러스터의 이름을 입력합니다.
    * **Terminate after 120 minutes of inactivity**(비활성 120분 후 종료) 확인란을 선택했는지 확인합니다. 클러스터를 사용하지 않는 경우 클러스터를 종료하는 기간(분)을 제공합니다.

4. **클러스터 만들기**를 선택합니다. 클러스터가 실행되면 노트북을 클러스터에 첨부하고 Spark 작업을 실행할 수 있습니다.

클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Databricks에서 Spark 클러스터 만들기](https://docs.azuredatabricks.net/user-guide/clusters/create.html)를 참조하세요.

### <a name="create-a-notebook"></a>Notebook 만들기

1. 왼쪽 창에서 **작업 영역**을 선택합니다. **작업 영역** 드롭다운에서 **만들기** > **Notebook**을 차례로 선택합니다.

    ![Databricks에서 Notebook 만들기](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Databricks에서 Notebook 만들기")

2. **노트북 만들기** 대화 상자에서 노트북 이름을 입력합니다. 언어로 **Python**을 선택한 다음, 앞에서 만든 Spark 클러스터를 선택합니다.

    ![Databricks에서 Notebook 만들기](./media/data-lake-storage-events/new-databricks-notebook.png "Databricks에서 Notebook 만들기")

    **만들기**를 선택합니다.

### <a name="create-and-populate-a-databricks-delta-table"></a>Databricks 델타 테이블 만들기 및 채우기

1. 만든 Notebook에서 다음 코드 블록을 복사하여 첫 번째 셀에 붙여넣습니다. 하지만 이 코드는 아직 실행하지 마세요.  

   `appId`, `password`, `tenant` 자리 표시자 값을 이 자습서의 필수 조건을 수행하는 동안 수집한 값으로 바꿉니다.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    이 코드는 **source_file**이라는 위젯을 만듭니다. 나중에 이 코드를 호출하고 파일 경로를 해당 위젯에 전달하는 Azure Function을 만듭니다.  또한 이 코드는 스토리지 계정을 사용하여 서비스 주체를 인증하고, 다른 셀에서 사용할 몇 가지 변수를 만듭니다.

    > [!NOTE]
    > 프로덕션 설정에서 Azure Databricks에서 인증 키를 저장하는 것이 좋습니다. 그런 다음, 인증 키 대신 코드 블록에 조회 키를 추가합니다. <br><br>예를 들어 `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` 코드 줄을 사용하는 대신 `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))` 코드 줄을 사용합니다. <br><br>이 빠른 시작을 완료했으면 Azure Databricks 웹 사이트의 [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) 문서에서 이 방법에 대한 예제를 살펴보세요.

2. 이 블록에서 코드를 실행하려면 **SHIFT + ENTER** 키를 누릅니다.

3. 다음 코드 블록을 복사하여 다른 셀에 붙여넣은 다음, **Shift+Enter** 키를 눌러 이 블록에서 해당 코드를 실행합니다.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   이 코드는 Databricks 델타 테이블을 스토리지 계정에 만든 다음, 이전에 업로드한 csv 파일에서 몇 가지 초기 데이터를 로드합니다.

4. 이 코드 블록이 성공적으로 실행되면 Notebook에서 이 코드 블록을 제거합니다.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>행을 Databricks 델타 테이블에 삽입하는 코드 추가

1. 다음 코드 블록을 복사하여 다른 셀에 붙여넣습니다. 하지만 이 코드는 실행하지 마세요.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   이 코드는 csv 파일의 데이터를 사용하여 데이터를 임시 테이블 보기에 삽입합니다. 해당 csv 파일의 경로는 이전 단계에서 만든 입력 위젯에서 가져옵니다.

2. 다음 코드를 추가하여 임시 테이블 보기의 내용을 Databricks 델타 테이블과 병합합니다.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>작업 만들기

이전에 만든 Notebook을 실행하는 작업을 만듭니다. 나중에 이벤트가 발생할 때 이 작업을 실행하는 Azure Function을 만듭니다.

1. **작업**을 클릭합니다.

2. **작업** 페이지에서 **작업 만들기**를 클릭합니다.

3. 작업 이름을 지정한 다음, `upsert-order-data` 통합 문서를 선택합니다.

   ![작업 만들기](./media/data-lake-storage-events/create-spark-job.png "작업 만들기")

## <a name="create-an-azure-function"></a>Azure Function 만들기

작업을 실행하는 Azure Function을 만듭니다.

1. Databricks 작업 영역의 위쪽 모서리에서 사용자 아이콘을 선택한 다음, **사용자 설정**을 선택합니다.

   ![계정 관리](./media/data-lake-storage-events/generate-token.png "사용자 설정")

2. **새 토큰 생성** 단추, **생성** 단추를 차례로 클릭합니다.

   토큰은 안전한 장소에 복사해야 합니다. Azure Function에는 작업을 실행할 수 있도록 Databricks를 인증하기 위해 이 토큰이 필요합니다.
  
3. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 단추를 선택한 다음, **컴퓨팅 > 함수 앱**을 차례로 선택합니다.

   ![Azure Function 만들기](./media/data-lake-storage-events/function-app-create-flow.png "Azure 함수 만들기")

4. 함수 앱의 **만들기** 페이지에서 런타임 스택으로 **.NET Core**를 선택하고, Application Insights 인스턴스를 구성해야 합니다.

   ![함수 앱 구성](./media/data-lake-storage-events/new-function-app.png "함수 앱 구성")

5. 함수 앱의 **개요** 페이지에서 **구성**을 클릭합니다.

   ![함수 앱 구성](./media/data-lake-storage-events/configure-function-app.png "함수 앱 구성")

6. **애플리케이션 설정** 페이지에서 **새 애플리케이션 설정** 단추를 선택하여 각 설정을 추가합니다.

   ![구성 설정 추가](./media/data-lake-storage-events/add-application-setting.png "구성 설정 추가")

   다음 설정을 추가합니다.

   |설정 이름 | 값 |
   |----|----|
   |**DBX_INSTANCE**| Databricks 작업 영역의 지역입니다. 예: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| 이전에 생성한 개인 액세스 토큰입니다. |
   |**DBX_JOB_ID**|실행 중인 작업의 식별자입니다. 여기서는 이 값이 `1`입니다.|
7. 함수 앱의 개요 페이지에서 **새 함수** 단추를 클릭합니다.

   ![새 함수](./media/data-lake-storage-events/new-function.png "새 함수")

8. **Azure Event Grid 트리거**를 선택합니다.

   **Microsoft.Azure.WebJobs.Extensions.EventGrid**확장을 설치하라는 메시지가 표시되면 이 확장을 설치합니다. 설치해야 하는 경우 **Azure Event Grid 트리거**를 다시 선택하여 함수를 만들어야 합니다.

   **새 함수** 창이 표시됩니다.

9. **새 함수** 창에서 함수 이름을 **UpsertOrder**로 지정한 다음,  **만들기** 단추를 클릭합니다.

10. 코드 파일의 내용을 다음 코드로 바꾼 다음, **저장** 단추를 클릭합니다.

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   이 코드는 발생된 스토리지 이벤트에 대한 정보를 구문 분석한 다음, 이벤트를 트리거한 파일의 URL을 사용하여 요청 메시지를 만듭니다. 메시지의 일부인 이 함수는 이전에 만든 **source_file** 위젯에 값을 전달합니다. 함수 코드는 메시지를 Databricks 작업으로 보내고, 이전에 획득한 토큰을 인증으로 사용합니다.

## <a name="create-an-event-grid-subscription"></a>Event Grid 구독 만들기

이 섹션에서는 파일이 스토리지 계정에 업로드되면 Azure Function을 호출하는 Event Grid 구독을 만듭니다.

1. 함수 코드 페이지에서 **Event Grid 구독 추가** 단추를 클릭합니다.

   ![새 이벤트 구독](./media/data-lake-storage-events/new-event-subscription.png "새 이벤트 구독")

2. **이벤트 구독 만들기** 페이지에서 구독 이름을 지정한 다음, 페이지의 필드를 사용하여 스토리지 계정을 선택합니다.

   ![새 이벤트 구독](./media/data-lake-storage-events/new-event-subscription-2.png "새 이벤트 구독")

3. **이벤트 유형으로 필터링** 드롭다운 목록에서 **Blob Created**(Blob 만들어짐) 및 **Blob Deleted**(Blob 삭제됨) 이벤트를 선택한 다음, **만들기** 단추를 클릭합니다.

## <a name="test-the-event-grid-subscription"></a>Event Grid 구독 테스트

1. `customer-order.csv`라는 파일을 만들고, 다음 정보를 해당 파일에 붙여넣고, 로컬 컴퓨터에 이를 저장합니다.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Storage Explorer에서 이 파일을 스토리지 계정의 **input** 폴더에 업로드합니다.

   파일이 업로드되면 **Microsoft.Storage.BlobCreated** 이벤트가 발생합니다. Event Grid는 모든 구독자에게 해당 이벤트를 알립니다. 여기서는 Azure Function이 유일한 구독자입니다. Azure Function은 이벤트 매개 변수를 구문 분석하여 발생한 이벤트를 확인합니다. 그런 다음, 파일의 URL을 Databricks 작업에 전달합니다. Databricks 작업에서 파일을 읽고, 스토리지 계정에 있는 Databricks 델타 테이블에 행을 추가합니다.

3. 작업이 성공했는지 확인하려면 Databricks 작업 영역을 열고, **작업** 단추를 클릭한 다음, 작업을 엽니다.

4. 작업을 선택하여 작업 페이지를 엽니다.

   ![Spark 작업](./media/data-lake-storage-events/spark-job.png "Spark 작업")

   작업이 완료되면 완료 상태가 표시됩니다.

   ![성공적으로 작업이 완료됨](./media/data-lake-storage-events/spark-job-completed.png "성공적으로 작업이 완료됨")

5. 새 통합 문서 셀의 셀에서 이 쿼리를 실행하여 업데이트된 델타 테이블을 확인합니다.

   ```
   %sql select * from customer_data
   ```

   반환된 테이블에서 최신 레코드를 보여 줍니다.

   ![최신 레코드가 테이블에 표시됨](./media/data-lake-storage-events/final_query.png "최신 레코드가 테이블에 표시됨")

6. 이 레코드를 업데이트하려면 `customer-order-update.csv`라는 파일을 만들고, 다음 정보를 해당 파일에 붙여넣은 후, 로컬 컴퓨터에 이를 저장합니다.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   이 csv 파일은 주문 수량이 `228`에서 `22`로 변경되는 것을 제외하고 이전 파일과 거의 동일합니다.

7. Storage Explorer에서 이 파일을 스토리지 계정의 **input** 폴더에 업로드합니다.

8. `select` 쿼리를 다시 실행하여 업데이트된 델타 테이블을 확인합니다.

   ```
   %sql select * from customer_data
   ```

   반환된 테이블에 최신 레코드가 표시됩니다.

   ![업데이트된 레코드가 테이블에 표시됨](./media/data-lake-storage-events/final_query-2.png "업데이트된 레코드가 테이블에 표시됨")

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 스토리지 계정에 대한 리소스 그룹을 선택하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Blob Storage 이벤트에 응답](storage-blob-event-overview.md)
