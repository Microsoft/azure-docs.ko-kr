---
title: HBase .NET SDK 사용 - Azure HDInsight
description: HBase.NET SDK를 사용하여 테이블을 만들고 삭제하며 데이터 읽기 및 쓰기를 할 수 있습니다.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: 707869880c5df619def2d707264b59e22e03c521
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123096"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Apache HBase 용.NET SDK를 사용 합니다.

[Apache HBase](apache-hbase-overview.md) 데이터와 함께 작동 하려면 두 가지 기본 옵션을 제공 합니다. [Apache Hive 쿼리와 HBase's RESTful API에 대 한 호출](apache-hbase-tutorial-get-started-linux.md)합니다. `curl`명령 또는 이와 유사한 유틸리티를 사용하여 REST API로 직접 작업할 수 있습니다.

C# 및 .NET 애플리케이션의 경우 [.NET용 Microsoft HBase REST 클라이언트 라이브러리](https://www.nuget.org/packages/Microsoft.HBase.Client/)가 HBase REST API 기반 클라이언트 라이브러리를 제공합니다.

## <a name="install-the-sdk"></a>SDK 설치

HBase .NET SDK는 다음 명령으로 Visual Studio **NuGet 패키지 관리자 콘솔**에서 설치할 수 있는 NuGet 패키지로 제공됩니다.

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>새 HBaseClient 개체 인스턴스화

SDK를 사용하려면 `Uri`로 구성된 `ClusterCredentials`를 클러스터와 Hadoop 사용자 이름 및 암호로 전달해 새 `HBaseClient` 개체를 인스턴스화합니다.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

CLUSTERNAME은 HDInsight HBase 클러스터 이름으로, 그리고 USERNAME 및 PASSWORD는 클러스터 만들기에 지정된 Apache Hadoop 자격 증명으로 바꿉니다. 기본 Hadoop 사용자 이름은 **admin**입니다.

## <a name="create-a-new-table"></a>새 테이블 만들기

HBase는 테이블에 데이터를 저장합니다. 테이블은 *Rowkey*, 기본 키 및 *열 패밀리*라는 하나 이상의 열 그룹으로 구성됩니다. 각 테이블의 데이터는 Rowkey 범위에 의해 *영역*에 수평으로 배포됩니다. 각 영역에는 시작 및 종료 키가 있습니다. 테이블은 하나 이상의 영역을 포함할 수 있습니다. 테이블의 데이터가 확장하면서 HBase는 큰 영역을 더 작은 영역으로 분할합니다. 영역은 한 지역 서버가 여러 영역을 저장할 수 있는 *지역 서버*에 저장됩니다.

데이터는 물리적으로 *HFiles*에 저장됩니다. 단일 HFile은 한 테이블과 한 영역, 하나의 열 패밀리용 데이터를 포함합니다. HFile의 행은 Rowkey를 기준으로 정렬 저장됩니다. 각 HFile에는 행의 빠른 검색용 *B+트리* 인덱스가 있습니다.

새 테이블을 만들려면 `TableSchema` 및 열을 지정하세요. 다음 코드는 'RestSDKTable' 테이블이 이미 존재하는 지 여부와 존재하지 않는 경우 테이블을 만들지 여부를 확인합니다.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

새 테이블에는 두 개의 열 패밀리, T1과 T2가 있습니다. 열 패밀리는 다른 HFiles에 별도로 저장되므로 자주 쿼리된 데이터에 대한 별도의 열 패밀리가 있는 것이 합리적입니다. 다음의 [데이터 삽입](#insert-data) 예제에서는 열이 T1 열 패밀리에 추가됩니다.

## <a name="delete-a-table"></a>테이블 삭제

테이블을 삭제하려면

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>데이터 삽입

데이터를 삽입하려면 고유한 행 키를 행 식별자로 지정합니다. 모든 데이터는 `byte[]` 배열에 저장됩니다. 다음 코드는 `title`, `director` 및 `release_date` 열이 가장 자주 액세스되기 때문에 이러한 열을 정의하고 T1 열 패밀리에 추가합니다. `description` 및 `tagline` 열은 T2 열 패밀리에 추가됩니다. 필요에 따라 열 패밀리에 데이터를 분할할 수 있습니다.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase는 데이터 형식이 다음처럼 표시되도록 [Cloud BigTable](https://cloud.google.com/bigtable/)을 구현합니다.

![클러스터 사용자 역할이 있는 사용자](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>데이터를 선택합니다.

HBase 테이블에서 데이터를 읽으려면 테이블 이름과 행 키를 `CellSet`를 반환하는 `GetCellsAsync` 메서드로 전달합니다.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

이 경우 코드는 고유 키 하나에 한 행만 있어야 하므로 처음 일치하는 행만을 반환합니다. 반환된 값은 `byte[]` 배열로부터 `string` 형식으로 변경됩니다. 또한 해당 값을 영화 출시 날짜에 대한 정수처럼 다른 형식으로 변환할 수 있습니다.

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>행 검사

HBase는 `scan`을 사용해 하나 이상의 행을 검색합니다. 이 예제에서는 10개 행 일괄 처리에서 여러 행을 요청하고 25-35 사이의 키 값을 갖는 데이터를 검색합니다. 모든 행을 검색한 후 스캐너를 삭제하여 리소스를 정리합니다.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>다음 단계

* [HDInsight에서 Apache HBase 예제 시작](apache-hbase-tutorial-get-started-linux.md)
* [Apache HBase를 사용하여 실시간 Twitter 감정 분석](../hdinsight-hbase-analyze-twitter-sentiment.md)을 통해 엔드투엔드 애플리케이션 빌드
