---
title: Azure Data Lake Storage 쿼리 가속 (미리 보기)을 사용 하 여 데이터 필터링 | Microsoft Docs
description: 쿼리 가속 (미리 보기)을 사용 하 여 저장소 계정에서 데이터 하위 집합을 검색 합니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 6de6661e5c970c7c3cbfc944b8539060b8844a36
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005227"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Azure Data Lake Storage 쿼리 가속 (미리 보기)을 사용 하 여 데이터 필터링

이 문서에서는 쿼리 가속 (미리 보기)을 사용 하 여 저장소 계정에서 데이터 하위 집합을 검색 하는 방법을 보여 줍니다. 

쿼리 가속 (미리 보기)은 응용 프로그램 및 분석 프레임 워크에서 지정 된 작업을 수행 하는 데 필요한 데이터만 검색 하 여 데이터 처리를 현저 하 게 최적화할 수 있도록 하는 Azure Data Lake Storage에 대 한 새로운 기능입니다. 자세히 알아보려면 [Azure Data Lake Storage 쿼리 가속 (미리 보기)](data-lake-storage-query-acceleration.md)을 참조 하세요.

> [!NOTE]
> 쿼리 가속 기능은 공개 미리 보기로 제공 되며 캐나다 중부 및 프랑스 중부 지역에서 사용할 수 있습니다. 제한 사항을 검토 하려면 [알려진 문제](data-lake-storage-known-issues.md) 문서를 참조 하세요. 미리 보기에 등록 하려면 [이 양식을](https://aka.ms/adls/qa-preview-signup)참조 하세요.  

## <a name="prerequisites"></a>필수 구성 요소

### <a name="net"></a>[.NET](#tab/dotnet)

- Azure Storage에 액세스하려면 Azure 구독이 있어야 합니다. 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 범용 **v2** 저장소 계정입니다. [저장소 계정 만들기를](../common/storage-quickstart-create-account.md)참조 하세요.

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Azure Storage에 액세스하려면 Azure 구독이 있어야 합니다. 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 범용 **v2** 저장소 계정입니다. [저장소 계정 만들기를](../common/storage-quickstart-create-account.md)참조 하세요.

- [JDK(Java Development Kit)](/java/azure/jdk/?view=azure-java-stable), 버전 8 이상.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > 이 문서에서는 Apache Maven를 사용 하 여 Java 프로젝트를 만들었다고 가정 합니다. Apache Maven를 사용 하 여 프로젝트를 만드는 방법에 대 한 예제는 [설정](storage-quickstart-blobs-java.md#setting-up)을 참조 하세요.
  
---

## <a name="install-packages"></a>패키지 설치 

### <a name="net"></a>[.NET](#tab/dotnet)

1. 쿼리 가속 패키지를 다운로드 합니다. 다음 링크를 사용 하 여 이러한 패키지를 포함 하는 압축 된 .zip 파일을 가져올 수 있습니다 [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. 프로젝트 디렉터리에이 파일의 콘텐츠를 추출 합니다.

3. 텍스트 편집기에서 프로젝트 파일 (*.csproj*)을 열고 이러한 패키지 참조를 요소 내에 추가 \<Project\> 합니다.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Preview SDK 패키지를 복원 합니다. 이 예제 명령은 명령을 사용 하 여 미리 보기 SDK 패키지를 복원 합니다 `dotnet restore` . 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. 공용 NuGet 리포지토리에서 다른 모든 종속성을 복원 합니다.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. 프로젝트의 루트에 디렉터리를 만듭니다. 루트 디렉터리는 **pom.xml** 파일을 포함 하는 디렉터리입니다.

   > [!NOTE]
   > 이 문서의 예제에서는 디렉터리 이름이 **lib**라고 가정 합니다.

2. 쿼리 가속 패키지를 다운로드 합니다. 다음 링크를 사용 하 여 이러한 패키지를 포함 하는 압축 된 .zip 파일을 가져올 수 있습니다 [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. 이 .zip 파일의 파일을 만든 디렉터리에 추출 합니다. 이 예제에서는 해당 디렉터리 이름이 **lib**입니다. 

4. 텍스트 편집기에서 *pom.xml* 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>문 추가


### <a name="net"></a>[.NET](#tab/dotnet)

이러한 `using` 문을 코드 파일의 맨 위에 추가 합니다.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

쿼리 가속은 CSV 및 Json 형식의 데이터를 검색 합니다. 따라서 사용 하도록 선택한 모든 CSV 또는 Json 구문 분석 라이브러리에 대해 using 문을 추가 해야 합니다. 이 문서에 표시 된 예제에서는 NuGet에서 사용할 수 있는 [CsvHelper](https://www.nuget.org/packages/CsvHelper/) 라이브러리를 사용 하 여 CSV 파일을 구문 분석 합니다. 따라서 `using` 코드 파일의 맨 위에 이러한 문을 추가 합니다.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

이 문서에 나와 있는 예제를 컴파일하려면 이러한 문도 추가 해야 `using` 합니다.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

이러한 `import` 문을 코드 파일의 맨 위에 추가 합니다.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>필터를 사용 하 여 데이터 검색

SQL을 사용 하 여 쿼리 가속 요청에서 행 필터 조건자 및 열 프로젝션을 지정할 수 있습니다. 다음 코드는 저장소의 CSV 파일을 쿼리하고 세 번째 열이 값과 일치 하는 모든 데이터 행을 반환 합니다 `Hemingway, Ernest` . 

- SQL 쿼리에서 키워드는 `BlobStorage` 쿼리 중인 파일을 나타내는 데 사용 됩니다.

- 열 참조는 `_N` 첫 번째 열이 인 위치로 지정 됩니다 `_1` . 원본 파일에 머리글 행이 포함 된 경우 머리글 행에 지정 된 이름으로 열을 참조할 수 있습니다. 

### <a name="net"></a>[.NET](#tab/dotnet)

비동기 메서드는 쿼리 `BlobQuickQueryClient.QueryAsync` 가속 API로 쿼리를 전송 하 고 결과를 [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) 개체로 응용 프로그램에 다시 스트리밍합니다.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

메서드는 쿼리 `BlobQuickQueryClient.openInputStream()` 가속 API로 쿼리를 보낸 다음 그 결과를 `InputStream` 다른 InputStream 개체와 같이 읽을 수 있는 개체로 응용 프로그램에 다시 스트리밍합니다.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>특정 열 검색

열의 하위 집합으로 결과의 범위를 지정할 수 있습니다. 이렇게 하면 지정 된 계산을 수행 하는 데 필요한 열만 검색할 수 있습니다. 따라서 네트워크를 통해 전송 되는 데이터가 줄어들기 때문에 응용 프로그램 성능을 향상 시키고 비용을 절감할 수 있습니다. 

이 코드는 `PublicationYear` 데이터 집합의 모든 책에 대 한 열만 검색 합니다. 또한 원본 파일의 머리글 행에 있는 정보를 사용 하 여 쿼리의 열을 참조 합니다.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

다음 코드에서는 행 필터링 및 열 프로젝션을 동일한 쿼리로 결합 합니다. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>다음 단계

- [쿼리 가속 등록 양식](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage 쿼리 가속 (미리 보기)](data-lake-storage-query-acceleration.md)
- [쿼리 가속 SQL 언어 참조 (미리 보기)](query-acceleration-sql-reference.md)
