---
title: 컨트롤 테이블을 사용 하 여 데이터베이스에서 델타 복사
description: 솔루션 템플릿을 사용하여 증분 방식으로 Azure Data Factory의 데이터베이스에서 새 행 또는 업데이트된 행만 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 3c077e2c04cae94d2e1a2a84ccd7d09c7a0829b4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439631"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>컨트롤 테이블이 있는 데이터베이스에서 델타 복사

이 문서에서는 상위 워터 마크 값을 저장 하는 외부 제어 테이블을 사용 하 여 데이터베이스 테이블에서 Azure로 새 행 또는 업데이트 된 행을 증분 로드 하는 데 사용할 수 있는 템플릿을 설명 합니다.

이 템플릿에서는 원본 데이터베이스의 스키마에 타임 스탬프 열 또는 증분 키가 포함 되어 있어야 새 행 또는 업데이트 된 행을 식별할 수 있습니다.

>[!NOTE]
> 원본 데이터베이스에 새 행 또는 업데이트 된 행을 식별 하는 타임 스탬프 열이 있지만 델타 복사에 사용할 외부 컨트롤 테이블을 만들지 않으려는 경우, 대신 [Azure Data Factory 데이터 복사 도구](copy-data-tool.md) 를 사용 하 여 파이프라인을 가져올 수 있습니다. 이 도구는 트리거 예약 시간을 변수로 사용 하 여 원본 데이터베이스에서 새 행을 읽습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 먼저 이전 워터 마크 값을 검색 하 여 현재 워터 마크 값과 비교 합니다. 그런 다음 두 워터 마크 값 간의 비교에 따라 원본 데이터베이스의 변경 내용만 복사 합니다. 마지막으로 다음에 델타 데이터를 로드 하기 위해 새 상위 워터 마크 값을 외부 컨트롤 테이블에 저장 합니다.

이 템플릿은 다음 4가지 작업을 포함합니다.
- **Lookup** 은 외부 컨트롤 테이블에 저장 된 이전의 상위 워터 마크 값을 검색 합니다.
- 다른 **조회** 작업은 원본 데이터베이스에서 현재 상위 워터 마크 값을 검색 합니다.
- 원본 데이터베이스에서 대상 저장소로의 변경 **내용만 복사 합니다** . 원본 데이터베이스의 변경 내용을 식별 하는 쿼리는 ' SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > "last high 워터 마크" 및 TIMESTAMP_Column < = "current high 워터 마크" '와 비슷합니다.
- **SqlServerStoredProcedure** 다음에 델타 복사를 위해 현재 높은 워터 마크 값을 외부 컨트롤 테이블에 씁니다.

템플릿은 다음 매개 변수를 정의 합니다.
- *Data_Source_Table_Name* 은 데이터를 로드 하려는 원본 데이터베이스의 테이블입니다.
- *Data_Source_WaterMarkColumn* 는 새 행 또는 업데이트 된 행을 식별 하는 데 사용 되는 원본 테이블의 열 이름입니다. 이 열의 형식은 일반적으로 *datetime*, *INT*또는 유사 합니다.
- *Data_Destination_Container* 은 대상 저장소에서 데이터가 복사 되는 위치의 루트 경로입니다.
- *Data_Destination_Directory* 은 대상 저장소에서 데이터가 복사 되는 위치의 루트 아래에 있는 디렉터리 경로입니다.
- *Data_Destination_Table_Name* 대상 저장소의 데이터가 복사 되는 위치입니다. "Azure Synapse Analytics (이전의 SQL DW)"가 데이터 대상으로 선택 된 경우에 해당 합니다.
- *Data_Destination_Folder_Path* 대상 저장소의 데이터가 복사 되는 위치입니다. "파일 시스템" 또는 "Azure Data Lake Storage Gen1"를 데이터 대상으로 선택한 경우에 해당 합니다.
- *Control_Table_Table_Name* 는 상위 워터 마크 값을 저장 하는 외부 컨트롤 테이블입니다.
- *Control_Table_Column_Name* 은 상위 워터 마크 값을 저장 하는 외부 컨트롤 테이블의 열입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 로드 하려는 원본 테이블을 탐색 하 고 새 행 또는 업데이트 된 행을 식별 하는 데 사용할 수 있는 상위 워터 마크 열을 정의 합니다. 이 열의 형식은 *datetime*, *INT*또는 유사할 수 있습니다. 새 행이 추가 되 면이 열의 값이 늘어납니다. 다음 샘플 원본 테이블 (data_source_table)에서 *LastModifytime* 열을 상위 워터 마크 열로 사용할 수 있습니다.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. 델타 데이터 로드에 대한 높은 워터 마크 값을 저장 하기 위해 SQL Server 또는 Azure SQL Database에서 컨트롤 테이블을 만듭니다. 다음 예제에서 컨트롤 테이블의 이름은 *watermarktable*입니다. 이 테이블에서 *WatermarkValue* 는 상위 워터 마크 값을 저장 하는 열이 고, 해당 형식은 *datetime*입니다.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. 컨트롤 테이블을 만드는 데 사용한 것과 동일한 SQL Server 또는 Azure SQL Database 인스턴스에 저장 프로시저를 만듭니다. 저장 프로시저는 다음에 델타 데이터를 로드 하기 위해 외부 컨트롤 테이블에 새 상위 워터 마크 값을 쓰는 데 사용 됩니다.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. **데이터베이스 템플릿에서 델타 복사** 로 이동 합니다. 데이터를 복사 하려는 원본 데이터베이스에 대한 **새** 연결을 만듭니다.

    ![원본 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. 데이터를 복사 하려는 대상 데이터 저장소에 대한 **새** 연결을 만듭니다.

    ![대상 테이블에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 2 단계와 3 단계에서 만든 외부 제어 테이블 및 저장 프로시저에 대한 **새** 연결을 만듭니다.

    ![제어 테이블 데이터 저장소에 대한 새 연결 만들기](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. **이 템플릿 사용**을 선택합니다.
    
8. 다음 예제와 같이 사용 가능한 파이프라인이 표시 됩니다.
  
    ![파이프라인 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. **저장 프로시저**를 선택 합니다. **저장 프로시저 이름**에 대해 **[dbo]를 선택 합니다. update_watermark]** . **가져오기 매개 변수**를 선택 하 고 **동적 콘텐츠 추가**를 선택 합니다.  

    ![저장 프로시저 작업 설정](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. 콘텐츠 **\@{activity (' LookupCurrentWaterMark 마크 '). NewWatermarkValue}** 를 작성 한 후 **마침**을 선택 합니다.  

    ![저장 프로시저의 매개 변수에 대한 내용 작성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    ![\* * 디버그 * *를 선택 합니다.](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. 다음 예제와 유사한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. 원본 테이블에 새 행을 만들 수 있습니다. 새 행을 만드는 샘플 SQL 언어는 다음과 같습니다.

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. 파이프라인을 다시 실행 하려면 **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    새 행만 대상으로 복사 된 것을 볼 수 있습니다.

15. 필드 Azure Synapse Analytics (이전의 SQL DW)를 데이터 대상으로 선택 하는 경우에는 Polybase를 SQL Data Warehouse 하는 데 필요한 Azure Blob storage에 대한 연결도 준비 해야 합니다. 템플릿이 컨테이너 경로를 생성 합니다. 파이프라인이 실행 된 후 컨테이너를 Blob 저장소에서 만들었는지 확인 합니다.
    
    ![PolyBase 구성](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 있는 컨트롤 테이블을 사용 하 여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)
- [Azure Data Factory를 사용 하 여 여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)
