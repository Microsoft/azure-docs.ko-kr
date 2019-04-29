---
title: Azure Data Factory를 사용 하 여 제어 테이블을 사용 하 여 대량 데이터베이스 복사 | Microsoft Docs
description: 솔루션 템플릿을 사용 하 여 Azure Data Factory를 사용 하 여 원본 테이블의 파티션 목록을 저장할 테이블을 외부 컨트롤을 사용 하 여 데이터베이스에서 데이터를 대량으로 복사 하는 방법에 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635448"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>제어 테이블이 있는 데이터베이스에서 대량 복사

Oracle Server 나 Netezza, Teradata, SQL Server에서 데이터 웨어하우스 로부터 Azure SQL Data Warehouse로 데이터를 복사, 여러 테이블에서 엄청난 양의 데이터를 로드 해야 합니다. 일반적으로 데이터를 단일 테이블에서 병렬로 여러 스레드를 사용 하 여 행을 로드할 수 있도록 각 테이블에 분할할 수 해야 합니다. 이 문서에서는 이러한 시나리오에서 사용할 템플릿을 설명 합니다.

 >! 소수의 비교적 작은 데이터 볼륨을 사용 하 여 테이블에서 SQL Data Warehouse로 데이터 복사 하려는 경우 것이 보다 효율적으로 사용할 합니다 [Azure Data Factory 데이터 복사 도구](copy-data-tool.md)합니다. 이 문서에 설명 된 파일은 해당 시나리오에 필요한 것 보다 더입니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 외부 제어 테이블에서 복사할 원본 데이터베이스 파티션의 목록을 검색 합니다. 원본 데이터베이스의 각 파티션에 대해 반복 하 고 대상에 데이터를 복사 합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **조회** 외부 제어 테이블에서 있는지 데이터베이스 파티션의 목록을 검색 합니다.
- **ForEach** 파티션 목록 조회 작업에서 가져오고 각 파티션을 복사 작업을 반복 합니다.
- **복사** 대상 저장소를 원본 데이터베이스 저장소의 각 파티션에 복사 합니다.

이 템플릿은 다음 5개의 매개 변수를 정의합니다.
- *Control_Table_Name* 가 원본 데이터베이스에 대 한 파티션 목록 저장 하는 외부 컨트롤 테이블입니다.
- *Control_Table_Schema_PartitionID* 각 파티션 ID를 저장 하는 외부 제어 테이블에서 열 이름의 이름 파티션 ID 원본 데이터베이스의 각 파티션에 대해 고유한 지 확인 합니다.
- *Control_Table_Schema_SourceTableName* 가 원본 데이터베이스에서 각 테이블 이름을 저장 하는 외부 컨트롤 테이블입니다.
- *Control_Table_Schema_FilterQuery* 원본 데이터베이스의 각 파티션에 데이터를 활용 하려면 필터 쿼리를 저장 하는 컨트롤 외부 테이블의 열 이름입니다. 예를 들어 연도별로 데이터를 분할 하는 경우 각 행에 저장 된 쿼리 유사할 수 있습니다 ' 선택 * datasource에서 여기서 LastModifytime > = ' 2015-01-01 00시: 00 '과 LastModifytime < = ' 2015-12-31 23:59:59.999 ' '.
- *Data_Destination_Folder_Path* 를 대상 저장소에 데이터가 복사 되는 위치 경로입니다. 이 매개 변수에 대상이 있는 경우 선택 하는 파일 기반 저장소에 표시 됩니다. 대상 저장소로 SQL Data Warehouse를 선택 하면이 매개 변수가 필요 하지 않습니다. 있지만 테이블 이름 및 SQL Data Warehouse에서 스키마를 원본 데이터베이스에서 항목 동일 이어야 합니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 대량 복사의 원본 데이터베이스 파티션 목록을 저장 하는 SQL Server 또는 Azure SQL Database에서 제어 테이블을 만듭니다. 다음 예제에서는 5 개의 파티션을의 경우 원본 데이터베이스 에 대 한 3 개 파티션은 합니다 *datasource_table*, 고 두 개는 합니다 *project_table*합니다. 열 *LastModifytime* 테이블의 데이터를 분할 하는 데 사용 됩니다 *datasource_table* 원본 데이터베이스에서. 첫 번째 파티션에 읽는 데 사용 되는 쿼리는 ' 선택 * datasource_table에서 여기서 LastModifytime > = ' 2015-01-01 00시: 00 '과 LastModifytime < = ' 2015-12-31 23:59:59.999 ' '입니다. 다른 파티션에서 데이터를 읽을 수는 비슷한 쿼리를 사용할 수 있습니다.

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

2. 로 이동 합니다 **데이터베이스에서 대량 복사** 템플릿. 만들기는 **새로 만들기** 1 단계에서 만든 컨트롤 외부 테이블에 연결 합니다.

    ![제어 테이블에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. 만들기는 **새로 만들기** 데이터를 복사 하는 원본 데이터베이스에 연결 합니다.

     ![원본 데이터베이스에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. 만들기는 **새로 만들기** 데이터를 복사 하는 대상 데이터 연결을 저장 합니다.

    ![대상 저장소에 대한 새 연결 만들기](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. 선택 **이 템플릿을 사용 하 여**입니다.

    ![이 템플릿 사용](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. 다음 예제에서와 같이 파이프라인에 표시 됩니다.

    ![파이프라인 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. 선택 **디버그**를 입력 합니다 **매개 변수**를 선택한 후 **마침**합니다.

    ![클릭 * * 디버그 * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. 다음 예제와 유사한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (선택 사항) 데이터 대상으로 SQL Data Warehouse를 선택한 경우 SQL Data Warehouse Polybase에서 필요에 따라 스테이징을 위해 Azure Blob storage에 연결을 입력 해야 합니다. Blob storage에 컨테이너를 만든 이미 있는지 확인 합니다.
    
    ![Polybase 설정](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
