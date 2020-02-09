---
title: 컨트롤 테이블을 사용 하 여 데이터베이스에서 대량 복사
description: 솔루션 템플릿을 사용 하 여 데이터베이스에서 대량 데이터를 복사 하는 방법에 대해 알아봅니다 .이를 사용 하 여 원본 Azure Data Factory 테이블의 파티션 목록을 저장 하려면 외부 컨트롤 테이블을 사용 합니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439976"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>컨트롤 테이블이 있는 데이터베이스에서 대량 복사

Oracle Server, Netezza, Teradata 또는 SQL Server 데이터 웨어하우스의 데이터를 Azure SQL Data Warehouse에 복사 하려면 여러 테이블에서 대량의 데이터를 로드 해야 합니다. 일반적으로 단일 테이블에서 여러 스레드가 동시에 행을 로드할 수 있도록 각 테이블에서 데이터를 분할 해야 합니다. 이 문서에서는 이러한 시나리오에서 사용할 템플릿을 설명 합니다.

 >! 참고 비교적 작은 데이터 볼륨이 포함 된 적은 수의 테이블에서 데이터를 SQL Data Warehouse 복사 하려는 경우 [Azure Data Factory 데이터 복사 도구](copy-data-tool.md)를 사용 하는 것이 더 효율적입니다. 이 문서에서 설명 하는 템플릿은 해당 시나리오에 필요한 것 보다 더 많은 것입니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 외부 컨트롤 테이블에서 복사할 원본 데이터베이스 파티션의 목록을 검색 합니다. 그런 다음 원본 데이터베이스의 각 파티션을 반복 하 고 대상에 데이터를 복사 합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **Lookup** 은 외부 컨트롤 테이블의 데이터베이스 파티션 목록을 검색 합니다.
- **ForEach** 조회 작업에서 파티션 목록을 가져오고 각 파티션을 복사 작업으로 반복 합니다.
- **복사를 복사** 하면 원본 데이터베이스 저장소의 각 파티션이 대상 저장소로 복사 됩니다.

템플릿은 다음 매개 변수를 정의 합니다.
- *Control_Table_Name* 은 원본 데이터베이스에 대한 파티션 목록을 저장 하는 외부 제어 테이블입니다.
- *Control_Table_Schema_PartitionID* 는 외부 제어 테이블에서 각 파티션 ID를 저장 하는 열 이름 이름입니다. 원본 데이터베이스의 각 파티션에 대해 파티션 ID가 고유한 지 확인 합니다.
- *Control_Table_Schema_SourceTableName* 은 원본 데이터베이스의 각 테이블 이름을 저장 하는 외부 제어 테이블입니다.
- *Control_Table_Schema_FilterQuery* 은 원본 데이터베이스의 각 파티션에서 데이터를 가져오기 위해 필터 쿼리를 저장 하는 외부 컨트롤 테이블의 열 이름입니다. 예를 들어 데이터를 연도별로 분할 한 경우 각 행에 저장 된 쿼리는 ' select * from datasource where LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' 및 LastModifytime < = ' ' 2015-12-31 23:59:59.999 까지의 ' ' '와 유사할 수 있습니다.
- *Data_Destination_Folder_Path* 은 데이터를 대상 저장소에 복사 하는 경로 (선택한 대상이 "파일 시스템" 또는 "Azure Data Lake Storage Gen1" 인 경우에 해당)입니다. 
- *Data_Destination_Container* 은 대상 저장소에서 데이터가 복사 되는 루트 폴더 경로입니다. 
- *Data_Destination_Directory* 은 대상 저장소에 데이터가 복사 되는 루트 아래의 디렉터리 경로입니다. 

대상 저장소의 경로를 정의 하는 마지막 세 매개 변수는 선택한 대상이 파일 기반 저장소 인 경우에만 표시 됩니다. "Azure Synapse Analytics (이전의 SQL DW)"를 대상 저장소로 선택 하는 경우 이러한 매개 변수는 필요 하지 않습니다. 그러나 SQL Data Warehouse의 테이블 이름과 스키마는 원본 데이터베이스의 이름과 동일 해야 합니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 대량 복사를 위해 원본 데이터베이스 파티션 목록을 저장할 SQL Server 또는 Azure SQL Database에 컨트롤 테이블을 만듭니다. 다음 예에서는 원본 데이터베이스에 5 개의 파티션이 있습니다. *Datasource_table*에 대한 파티션 3 개와 *project_table*에 대한 두 개의 파티션이 있습니다. *LastModifytime* 열은 원본 데이터베이스에서 테이블 *datasource_table* 의 데이터를 분할 하는 데 사용 됩니다. 첫 번째 파티션을 읽는 데 사용 되는 쿼리는 ' select * from datasource_table where LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' 및 LastModifytime < = ' ' 2015-12-31 23:59:59.999 까지의 ' ' '입니다. 유사한 쿼리를 사용 하 여 다른 파티션에서 데이터를 읽을 수 있습니다.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. **데이터베이스 템플릿에서 대량 복사** 로 이동 합니다. 1 단계에서 만든 외부 컨트롤 테이블에 대한 **새** 연결을 만듭니다.

    ![제어 테이블에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 데이터를 복사 하는 원본 데이터베이스에 대한 **새** 연결을 만듭니다.

    ![원본 데이터베이스에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 데이터를 복사 하는 대상 데이터 저장소에 대한 **새** 연결을 만듭니다.

    ![대상 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. **이 템플릿 사용**을 선택합니다.

6. 다음 예제와 같이 파이프라인이 표시 됩니다.

    ![파이프라인 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    ![\* * 디버그 * *를 클릭 합니다.](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 다음 예제와 유사한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. 필드 "Azure Synapse Analytics (이전의 SQL DW)"를 데이터 대상으로 선택한 경우 Polybase SQL Data Warehouse에서 필요에 따라 스테이징을 위해 Azure Blob 저장소에 대한 연결을 입력 해야 합니다. 템플릿은 Blob 저장소에 대한 컨테이너 경로를 자동으로 생성 합니다. 파이프라인이 실행 된 후 컨테이너를 만들었는지 확인 합니다.
    
    ![Polybase 설정](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
