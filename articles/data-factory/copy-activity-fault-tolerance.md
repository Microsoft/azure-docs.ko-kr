---
title: Azure Data Factory의 복사 작업 내결함성 | Microsoft Docs
description: 복사 중에 호환되지 않는 행을 건너뛰어 Azure Data Factory 복사 작업에 내결함성을 추가하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: ef0bb3716a32a0f25b90e74bc44d7291c146b431
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808820"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory의 복사 작업 내결함성
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [현재 버전](copy-activity-fault-tolerance.md)

Azure Data Factory의 복사 작업은 원본 및 싱크 데이터 저장소 간에 데이터를 복사할 때 호환되지 않는 행을 처리하는 2가지 방법을 제공합니다.

- 호환되지 않는 데이터가 나타날 때 복사 작업을 중단하고 실패한 것으로 처리할 수 있습니다(기본 동작).
- 내결함성을 추가하고 호환되지 않는 데이터 행을 건너뛰어 모든 데이터를 계속 복사할 수 있습니다. 또한 Azure Blob Storage 또는 Azure Data Lake Store에 호환되지 않는 행을 기록할 수 있습니다. 그런 후 로그를 검토하여 실패의 원인을 파악하고, 데이터 원본에서 데이터를 수정하고, 복사 작업을 다시 시도할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
복사 작업은 호환되지 않는 데이터의 검색, 건너뛰기 및 로깅에 대한 다음 세 가지 시나리오를 지원합니다.

- **원본 데이터 형식 및 싱크 원시 형식 간의 비호환성** 

    예를 들면 다음과 같습니다. 3개의 INT 형식 열을 포함하는 스키마 정의를 사용하여 Blob Storage에 있는 CSV 파일의 데이터를 SQL Database로 복사합니다. 123,456,789와 같은 숫자 데이터를 포함하는 CSV 파일 행은 싱크 저장소에 성공적으로 복사됩니다. 그러나 123,456, abc와 같은 숫자가 아닌 값을 포함하는 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

- **원본과 싱크 간의 열 수 불일치**

    예를 들면 다음과 같습니다. 6개의 열이 포함된 스키마 정의를 사용하여 Blob Storage에 있는 CSV 파일의 데이터를 SQL Database로 복사합니다. 6개의 열이 포함된 CSV 파일 행은 싱크 저장소에 성공적으로 복사됩니다. 6개보다 많거나 적은 열을 포함하는 CSV 파일 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

- **SQL Server/Azure SQL 데이터베이스/Azure Cosmos DB에 쓸 때 기본 키 위반**.

    예를 들면 다음과 같습니다. SQL Server에서 SQL Database로 데이터를 복사합니다. 기본 키가 싱크 SQL Database에 정의되어 있지만 이러한 기본 키가 원본 SQL Server에 정의되어 있지 않습니다. 원본에 있는 중복된 행을 싱크로 복사할 수 없습니다. 복사 작업은 원본 데이터의 첫 번째 행만 싱크에 복사합니다. 중복된 기본 키 값을 포함하는 후속 원본 행을 호환되지 않는 것으로 감지하고 건너뜁니다.

>[!NOTE]
>- PolyBase를 사용하여 SQL Data Warehouse로 데이터를 로드하려면 복사 작업에서 "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)"를 통해 거부 정책을 지정하여 PolyBase의 네이티브 내결함성 설정을 구성하세요. PolyBase와 호환되지 않는 행을 계속해서 아래와 같이 정상적으로 Blob 또는 ADLS로 리디렉션할 수 있습니다.
>- [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)를 호출하도록 복사 작업이 구성된 경우 이 기능이 적용되지 않습니다.


## <a name="configuration"></a>구성
다음 예제에서는 복사 작업에서 호환되지 않는 행을 건너뛰도록 구성하기 위한 JSON 정의를 제공합니다.

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 복사 중에 호환되지 않는 행을 건너뛸지 지정합니다. | True <br/>False(기본값) | 아닙니다.
redirectIncompatibleRowSettings | 호환되지 않는 행을 기록하려는 경우 지정할 수 있는 속성 그룹입니다. | &nbsp; | 아닙니다.
linkedServiceName | 건너뛰는 행을 포함하는 로그를 저장하는 [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties)의 연결된 서비스입니다. | `AzureStorage` 또는 `AzureDataLakeStore` 형식 연결된 서비스의 이름은 로그 파일을 저장하는 데 사용하려는 인스턴스를 참조합니다. | 아닙니다.
경로 | 건너뛴 행을 포함하는 로그 파일의 경로입니다. | 호환되지 않는 데이터를 기록하는 데 사용하려는 경로를 지정합니다. 경로를 지정하지 않으면 서비스가 대신 컨테이너를 만듭니다. | 아닙니다.

## <a name="monitor-skipped-rows"></a>건너뛴 행 모니터링
복사 작업 실행이 완료되면 복사 작업 출력에서 건너뛴 행의 수를 볼 수 있습니다.

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
호환되지 않는 행을 기록하도록 구성할 경우 `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` 경로에 로그 파일이 생깁니다. 

로그 파일은 csv 파일만 될 수 있습니다. 건너뛴 원래 데이터는 필요한 경우 쉼표를 열 구분 기호로 사용하여 기록됩니다. 로그 파일에서 원래의 원본 데이터에 "ErrorCode" 및 "ErrorMessage" 등의 두 열을 더할 수 있습니다. 여기서 비호환의 근본 원인을 파악할 수 있습니다. ErrorCode 및 ErrorMessage는 큰따옴표를 사용하여 인용됩니다. 

로그 파일 내용의 예는 다음과 같습니다.

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능](copy-activity-performance.md)


