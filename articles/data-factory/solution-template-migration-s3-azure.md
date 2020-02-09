---
title: Amazon s 3에서 Azure Data Lake Storage Gen2로 데이터 마이그레이션
description: 솔루션 템플릿을 사용 하 여 AWS S3의 파티션 목록을 Azure Data Factory와 함께 저장 하 여 Amazon s 3에서 데이터를 마이그레이션하는 방법에 대해 알아봅니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: e918fe01426202746f0225d25304b9c1b26cb74b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927320"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Amazon s 3에서 Azure Data Lake Storage Gen2로 데이터 마이그레이션

이 템플릿을 사용 하 여 Amazon s 3에서 수백만 개의 파일로 구성 된 페타바이트 데이터를 Azure Data Lake Storage Gen2로 마이그레이션합니다. 

 > [!NOTE]
 > AWS s 3에서 Azure로 작은 데이터 볼륨을 복사 하려는 경우 (예: 10TB 미만) [Azure Data Factory 데이터 복사 도구](copy-data-tool.md)를 사용 하는 것이 더 효율적이 고 사용 하기 쉽습니다. 이 문서에서 설명 하는 템플릿은 필요한 것 보다 더 많은 정보를 제공 합니다.

## <a name="about-the-solution-templates"></a>솔루션 템플릿 정보

데이터 파티션은 10TB를 초과 하는 데이터를 마이그레이션할 때 특히 권장 됩니다. 데이터를 분할 하려면 ' 접두사 ' 설정을 활용 하 여 Amazon s 3의 폴더와 파일을 이름별로 필터링 한 다음 각 ADF 복사 작업에서 한 번에 하나의 파티션을 복사할 수 있습니다. 더 나은 처리량을 위해 여러 ADF 복사 작업을 동시에 실행할 수 있습니다.

데이터 마이그레이션에는 일반적으로 일회성 기록 데이터 마이그레이션과 AWS s 3에서 Azure로의 변경 내용이 정기적으로 동기화 되어야 합니다. 아래에는 두 가지 템플릿이 있습니다. 여기서 한 템플릿은 일회성 기록 데이터 마이그레이션을 처리 하 고 다른 템플릿은 AWS s 3에서 Azure로 변경 내용을 동기화 하는 과정을 다룹니다.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 Amazon s 3에서로 기록 데이터를 마이그레이션하기 위한 템플릿

이 템플릿 (*템플릿 이름: AWS s 3에서 Azure Data Lake Storage Gen2 기록 데이터 마이그레이션*)은 Azure SQL Database의 외부 컨트롤 테이블에 파티션 목록을 작성 했다고 가정 합니다. 따라서 *조회* 작업을 사용 하 여 외부 제어 테이블에서 파티션 목록을 검색 하 고, 각 파티션을 반복 하며, 각 ADF 복사 작업에서 한 번에 하나의 파티션을 복사 하도록 합니다. 복사 작업이 완료 되 면 *저장 프로시저* 작업을 사용 하 여 컨트롤 테이블의 각 파티션 복사 상태를 업데이트 합니다.

템플릿에는 5 가지 작업이 포함 됩니다.
- **Lookup** 은 외부 컨트롤 테이블에서 Azure Data Lake Storage Gen2로 복사 되지 않은 파티션을 검색 합니다. 테이블 이름은 *s3_partition_control_table* 이 고 테이블에서 데이터를 로드 하는 쿼리는 *"SELECT 파티션 접두사 From s3_partition_control_table WHERE SuccessOrFailure = 0"* 입니다.
- **ForEach** *조회* 작업에서 파티션 목록을 가져오고 각 파티션을 *triggercopy* 작업으로 반복 합니다. 여러 ADF 복사 작업을 동시에 실행 하도록 *Batchcount* 를 설정할 수 있습니다. 이 템플릿에는 2가 설정 되어 있습니다.
- **ExecutePipeline** 는 *CopyFolderPartitionFromS3* 파이프라인을 실행 합니다. 각 복사 작업에서 파티션을 복사 하도록 다른 파이프라인을 만드는 이유는 실패 한 복사 작업을 다시 실행 하 여 AWS S3에서 특정 파티션을 다시 다시 로드 하는 것 이기 때문입니다. 다른 파티션을 로드 하는 다른 모든 복사 작업에는 영향을 주지 않습니다.
- **COPY** AWS S3의 각 파티션을 Azure Data Lake Storage Gen2로 복사 합니다.
- **SqlServerStoredProcedure** 컨트롤 테이블의 각 파티션 복사 상태를 업데이트 합니다.

템플릿에는 두 개의 매개 변수가 포함 되어 있습니다.
- **AWS_S3_bucketName** 은 데이터를 마이그레이션할 AWS s 3의 버킷 이름입니다. AWS s 3에서 여러 버킷의 데이터를 마이그레이션하려면 외부 제어 테이블에 열을 하나 더 추가 하 여 각 파티션에 대한 버킷 이름을 저장 하 고 파이프라인을 업데이트 하 여 그에 따라 해당 열에서 데이터를 검색할 수도 있습니다.
- **Azure_Storage_fileSystem** 은 데이터를 마이그레이션할 대상 Azure Data Lake Storage Gen2의 파일 시스템 이름입니다.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>이 템플릿이 Amazon s 3에서 변경 된 파일만 복사 하려면 Azure Data Lake Storage Gen2

이 템플릿 (*템플릿 이름: AWS s 3에서 Azure Data Lake Storage Gen2로 델타 데이터 복사*)는 각 파일의 LastModifiedTime을 사용 하 여 AWS s 3에서 Azure로 새로운 파일 또는 업데이트 된 파일만 복사 합니다. 파일이 나 폴더가 AWS s 3의 파일 또는 폴더 이름 (예:/yyyy/mm/dd/file.csv)의 일부로 timeslice 정보를 사용 하 여 이미 분할 된 경우에는이 [자습서](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) 로 이동 하 여 새 파일을 증분 로드 하는 더 뛰어난 방법을 얻을 수 있습니다. 이 템플릿은 Azure SQL Database의 외부 컨트롤 테이블에 파티션 목록을 작성 했다고 가정 합니다. 따라서 *조회* 작업을 사용 하 여 외부 제어 테이블에서 파티션 목록을 검색 하 고, 각 파티션을 반복 하며, 각 ADF 복사 작업에서 한 번에 하나의 파티션을 복사 하도록 합니다. 각 복사 작업이 AWS S3에서 파일 복사를 시작 하면 LastModifiedTime 속성을 사용 하 여 새 파일 또는 업데이트 된 파일만 식별 하 고 복사 합니다. 복사 작업이 완료 되 면 *저장 프로시저* 작업을 사용 하 여 컨트롤 테이블의 각 파티션 복사 상태를 업데이트 합니다.

템플릿에는 7 가지 작업이 포함 됩니다.
- **조회** 는 외부 제어 테이블에서 파티션을 검색 합니다. 테이블 이름은 *s3_partition_delta_control_table* 이 고 테이블에서 데이터를 로드 하는 쿼리는 *"select distinct 전치 prefix from s3_partition_delta_control_table"* 입니다.
- **ForEach** 는 *조회* 활동에서 파티션 목록을 가져오고 각 파티션을 *TriggerDeltaCopy* 활동으로 반복 합니다. 여러 ADF 복사 작업을 동시에 실행 하도록 *Batchcount* 를 설정할 수 있습니다. 이 템플릿에는 2가 설정 되어 있습니다.
- **ExecutePipeline** 는 *DeltaCopyFolderPartitionFromS3* 파이프라인을 실행 합니다. 각 복사 작업에서 파티션을 복사 하도록 다른 파이프라인을 만드는 이유는 실패 한 복사 작업을 다시 실행 하 여 AWS S3에서 특정 파티션을 다시 다시 로드 하는 것 이기 때문입니다. 다른 파티션을 로드 하는 다른 모든 복사 작업에는 영향을 주지 않습니다.
- **Lookup** 은 LastModifiedTime를 통해 새 파일 또는 업데이트 된 파일을 식별할 수 있도록 외부 컨트롤 테이블에서 마지막 복사 작업 실행 시간을 검색 합니다. 테이블 이름은 *s3_partition_delta_control_table* 이 고 테이블에서 데이터를 로드 하는 쿼리는 *"Select Max (jobruntime) From s3_partition_delta_control_table from LastModifiedTime (여기서 파티션 = ' @ {Pipeline () prefixStr} ' 및 SuccessOrFailure = 1")* 입니다.
- **Copy** 는 AWS s 3의 각 파티션에 대한 새 파일 또는 변경 된 파일을 Azure Data Lake Storage Gen2로 복사 합니다. *ModifiedDatetimeStart* 의 속성은 마지막 복사 작업 실행 시간으로 설정 됩니다. *ModifiedDatetimeEnd* 의 속성은 현재 복사 작업 실행 시간으로 설정 됩니다. 시간은 UTC 표준 시간대에 적용 됩니다.
- **SqlServerStoredProcedure** 각 파티션 복사 상태와 컨트롤 테이블이 성공할 때 실행 시간을 업데이트 합니다. SuccessOrFailure의 열은 1로 설정 됩니다.
- **SqlServerStoredProcedure** 실패할 경우 각 파티션 및 복사 실행 시간을 제어 테이블에 업데이트 합니다. SuccessOrFailure의 열은 0으로 설정 됩니다.

템플릿에는 두 개의 매개 변수가 포함 되어 있습니다.
- **AWS_S3_bucketName** 은 데이터를 마이그레이션할 AWS s 3의 버킷 이름입니다. AWS s 3에서 여러 버킷의 데이터를 마이그레이션하려면 외부 제어 테이블에 열을 하나 더 추가 하 여 각 파티션에 대한 버킷 이름을 저장 하 고 파이프라인을 업데이트 하 여 그에 따라 해당 열에서 데이터를 검색할 수도 있습니다.
- **Azure_Storage_fileSystem** 은 데이터를 마이그레이션할 대상 Azure Data Lake Storage Gen2의 파일 시스템 이름입니다.

## <a name="how-to-use-these-two-solution-templates"></a>이러한 두 솔루션 템플릿을 사용 하는 방법

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 Amazon s 3에서로 기록 데이터를 마이그레이션하기 위한 템플릿 

1. Azure SQL Database에 컨트롤 테이블을 만들어 AWS S3의 파티션 목록을 저장 합니다. 

    > [!NOTE]
    > 테이블 이름이 s3_partition_control_table입니다.
    > 컨트롤 테이블의 스키마는 전치 접두사 및 SuccessOrFailure입니다. 여기서 partition Prefix는 이름으로 Amazon s 3의 폴더와 파일을 필터링 하 고 SuccessOrFailure는 각 파티션 복사의 상태입니다. 0은이 파티션에는 Azure에 복사 되지 않았습니다. 1은이 파티션이 Azure에 성공적으로 복사 되었음을 의미 합니다.
    > 제어 테이블에는 5 개의 파티션이 정의 되 고 각 파티션 복사의 기본 상태는 0입니다.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. 제어 테이블에 대해 동일한 Azure SQL Database에서 저장 프로시저를 만듭니다. 

    > [!NOTE]
    > 저장 프로시저의 이름이 sp_update_partition_success입니다. ADF 파이프라인에서 SqlServerStoredProcedure 활동에 의해 호출 됩니다.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. **AWS S3에서 기록 데이터 마이그레이션 Azure Data Lake Storage Gen2** 템플릿으로 이동 합니다. 외부 컨트롤 테이블에 대한 연결을 입력 하 고, 데이터 원본 저장소로 S3을 AWS 하 고, 대상 저장소로 Azure Data Lake Storage Gen2 합니다. 외부 제어 테이블 및 저장 프로시저는 동일한 연결에 대한 참조입니다.

    ![새 연결 만들기](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. **이 템플릿 사용**을 선택합니다.

    ![이 템플릿 사용](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. 다음 예제와 같이 2 개의 파이프라인과 3 개의 데이터 집합이 생성 된 것을 볼 수 있습니다.

    ![파이프라인 검토](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    ![\* * 디버그 * *를 클릭 합니다.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. 다음 예제와 유사한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>이 템플릿이 Amazon s 3에서 변경 된 파일만 복사 하려면 Azure Data Lake Storage Gen2

1. Azure SQL Database에 컨트롤 테이블을 만들어 AWS S3의 파티션 목록을 저장 합니다. 

    > [!NOTE]
    > 테이블 이름이 s3_partition_delta_control_table입니다.
    > 컨트롤 테이블의 스키마는 파티션, JobRunTime 및 SuccessOrFailure입니다. 여기서 전치 접두사는 이름으로 Amazon s 3의 폴더와 파일을 필터링 하는 데 사용할 수 있습니다. JobRunTime은 복사 작업을 실행할 때의 datetime 값이 고, SuccessOrFailure는입니다. 각 파티션 복사 상태: 0은이 파티션이 Azure로 복사 되지 않았음을 의미 하 고 1은이 파티션이 Azure로 복사 되었음을 나타냅니다.
    > 제어 테이블에는 5 개의 파티션이 정의 되어 있습니다. JobRunTime의 기본값은 일회성 기록 데이터 마이그레이션이 시작 되는 시간 일 수 있습니다. ADF 복사 작업은 해당 시간 이후 마지막으로 수정 된 AWS S3의 파일을 복사 합니다. 각 파티션 복사의 기본 상태는 1입니다.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. 제어 테이블에 대해 동일한 Azure SQL Database에서 저장 프로시저를 만듭니다. 

    > [!NOTE]
    > 저장 프로시저의 이름이 sp_insert_partition_JobRunTime_success입니다. ADF 파이프라인에서 SqlServerStoredProcedure 활동에 의해 호출 됩니다.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. **AWS S3에서 델타 데이터 복사 Azure Data Lake Storage Gen2** 템플릿으로 이동 합니다. 외부 컨트롤 테이블에 대한 연결을 입력 하 고, 데이터 원본 저장소로 S3을 AWS 하 고, 대상 저장소로 Azure Data Lake Storage Gen2 합니다. 외부 제어 테이블 및 저장 프로시저는 동일한 연결에 대한 참조입니다.

    ![새 연결 만들기](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. **이 템플릿 사용**을 선택합니다.

    ![이 템플릿 사용](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. 다음 예제와 같이 2 개의 파이프라인과 3 개의 데이터 집합이 생성 된 것을 볼 수 있습니다.

    ![파이프라인 검토](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. **디버그**를 선택 하 고 **매개 변수**를 입력 한 다음 **마침**을 선택 합니다.

    ![\* * 디버그 * *를 클릭 합니다.](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. 다음 예제와 유사한 결과가 표시 됩니다.

    ![결과 검토](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. *"Select * from s3_partition_delta_control_table"* 쿼리를 통해 컨트롤 테이블의 결과를 확인할 수도 있습니다. 다음 예제와 유사한 출력이 표시 됩니다.

    ![결과 검토](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>다음 단계

- [여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
- [파일 이동](solution-template-move-files.md)