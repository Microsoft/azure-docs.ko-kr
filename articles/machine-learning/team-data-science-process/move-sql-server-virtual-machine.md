---
title: SQL Server 가상 머신으로 데이터 이동 - Team Data Science Process
description: 플랫 파일 또는 온-프레미스 SQL Server에서 Azure VM의 SQL Server로 데이터를 이동합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 47a77def43a9577e5a3506899da47db2f684b495
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429521"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Azure 가상 머신에서 SQL Server로 데이터 이동

이 문서에서는 플랫 파일(CSV 또는 TSV 형식) 또는 온-프레미스 SQL Server에서 Azure 가상 머신의 SQL Server로 데이터를 이동하기 위한 옵션에 대해 간략히 설명합니다. 클라우드로 데이터를 이동하는 이러한 작업은 팀 데이터 과학 프로세스의 일부입니다.

Machine Learning을 위해 Azure SQL Database로 데이터를 이동하기 위한 옵션을 설명하는 항목은 [Azure Machine Learning을 위해 Azure SQL Database로 데이터 이동](move-sql-azure.md)을 참조하세요.

다음 표에서는 Azure 가상 머신에서 SQL Server로 데이터를 이동하는 옵션을 요약합니다.

| <b>원본</b> | <b>대상: Azure VM의 SQL Server</b> |
| --- | --- |
| <b>플랫 파일</b> |1. <a href="#insert-tables-bcp">명령줄 BCP(대량 복사 유틸리티)</a><br> 2. <a href="#insert-tables-bulkquery">대량 삽입 SQL 쿼리</a><br> 3. <a href="#sql-builtin-utilities">SQL Server의 기본 제공 그래픽 유틸리티</a> |
| <b>온-프레미스 SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사</a><br> 2. <a href="#export-flat-file">플랫 파일로 내보내기</a><br> 3. <a href="#sql-migration">SQL Database 마이그레이션 마법사</a> <br> 4. <a href="#sql-backup">데이터베이스 백업 및 복원</a><br> |

이 문서는 SQL Server Management Studio 또는 Visual Studio 데이터베이스 탐색기에서 SQL 명령을 실행하는 것으로 가정합니다.

> [!TIP]
> 하나의 대안으로, [Azure 데이터 팩터리](https://azure.microsoft.com/services/data-factory/) 를 사용하여 Azure의 SQL Server VM으로 데이터를 이동하는 파이프라인을 만들고 예약할 수 있습니다. 자세한 내용은 [Azure 데이터 팩터리를 사용하여 데이터 복사(복사 작업)](../../data-factory/copy-activity-overview.md)를 참조하세요.
>
>

## <a name="prereqs"></a>필수 조건
이 자습서에서는 사용자가 다음을 보유하고 있다고 가정합니다.

* **Azure 구독**. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
* **Azure 저장소 계정**. 이 자습서에서는 데이터 저장을 위해 Azure 저장소 계정을 사용합니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md) 문서를 참조하세요. 저장소 계정을 만든 후에는 저장소 액세스에 사용되는 계정 키를 확보해야 합니다. [저장소 액세스 키 관리](../../storage/common/storage-account-manage.md#access-keys)를 참조하세요.
* 프로비전된 **Azure VM의 SQL Server**. 자세한 내용은 [고급 분석을 위해 Azure SQL Server 가상 컴퓨터를 IPython Notebook 서버로 설정](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)을 참조하세요.
* 로컬로 설치 및 구성된 **Azure PowerShell** . 자세한 내용은 [Azure PowerShell 설치 및 구성법](/powershell/azure/overview)을 참조하세요.

## <a name="filesource_to_sqlonazurevm"></a> 플랫 파일 원본에서 Azure VM의 SQL Server로 데이터 이동
데이터가 플랫 파일에 있는 경우(행/열 형식으로 정렬됨) 다음 방법을 통해 Azure 기반의 SQL Server VM으로 데이터를 이동할 수 있습니다.

1. [명령줄 BCP(대량 복사 유틸리티)](#insert-tables-bcp)
2. [대량 삽입 SQL 쿼리](#insert-tables-bulkquery)
3. [SQL Server의 기본 제공 그래픽 유틸리티(Import/Export, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>명령줄 BCP(대량 복사 유틸리티)
BCP는 SQL Server와 함께 설치되는 명령줄 유틸리티로, 데이터를 이동하는 가장 빠른 방법 중 하나입니다. 이는 모든 SQL Server 버전(온-프레미스 SQL Server, SQL Azure 및 Azure 기반의 SQL Server VM)에서 작동합니다.

> [!NOTE]
> **BCP를 사용하려면 데이터가 어디에 있어야 하나요?**  
> 필수 사항은 아니지만 원본 데이터가 포함된 파일을 대상 SQL 서버와 같은 컴퓨터에 배치하면 전송 속도가 빨라집니다(네트워크 속도와 로컬 디스크 IO 속도 차이). [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage 탐색기](https://storageexplorer.com/), Windows 복사/붙여넣기, RDP(원격 데스크톱 프로토콜) 등 다양한 파일 복사 도구를 사용하여 데이터가 포함된 플랫 파일을 SQL Server가 설치된 컴퓨터로 이동할 수 있습니다.
>
>

1. 대상 SQL Server 데이터베이스에 데이터베이스 및 테이블이 생성되었는지 확인합니다. 다음은 `Create Database` 및 `Create Table` 명령을 사용하여 이 작업을 수행하는 예제입니다.

```sql
CREATE DATABASE <database_name>

CREATE TABLE <tablename>
(
    <columnname1> <datatype> <constraint>,
    <columnname2> <datatype> <constraint>,
    <columnname3> <datatype> <constraint>
)
```

1. bcp가 설치된 컴퓨터의 명령줄에서 다음 명령을 실행하여 테이블의 스키마를 설명하는 서식 파일을 생성합니다.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. 다음과 같이 bcp 명령을 사용하여 데이터베이스에 데이터를 삽입합니다. SQL Server가 같은 컴퓨터에 설치되었다면 이 명령이 명령줄에서 작동할 것입니다.

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **BCP 삽입 최적화 삽입** 작업을 최적화하는 방법은 ['대량 가져오기를 최적화하기 위한 지침'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) 문서를 참조하세요.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>더 빠른 데이터 이동을 위한 병렬 처리
이동하려는 데이터가 큰 경우 PowerShell 스크립트에서 동시에 여러 BCP 명령을 병렬로 수행하면 작업 속도를 높일 수 있습니다.

> [!NOTE]
> **빅 데이터 수집** 매우 큰 데이터 세트의 데이터 로드 작업을 최적화하려면 여러 파일 그룹 및 파티션 테이블을 사용하여 논리적 및 물리적 데이터베이스 테이블을 분할합니다. 파티션 테이블을 만들어서 데이터를 로드하는 방법에 대한 자세한 내용은 [SQL 파티션 테이블 병렬 로드](parallel-load-sql-partitioned-tables.md)를 참조하세요.
>
>

다음 샘플 PowerShell 스크립트는 bcp를 사용한 병렬 삽입을 보여 줍니다.

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="insert-tables-bulkquery"></a>대량 삽입 SQL 쿼리
[대량 삽입 SQL 쿼리](https://msdn.microsoft.com/library/ms188365)는 데이터를 행/열 기반 파일에서 데이터베이스로 가져오는 데 사용할 수 있습니다(지원되는 형식은 [대량 내보내기 또는 가져오기를 위한 데이터 준비(SQL Server)](https://msdn.microsoft.com/library/ms188609) 항목에서 설명).

아래는 대량 삽입을 위한 몇 가지 샘플 명령입니다.  

1. SQL Server 데이터베이스에서 날짜 같은 특수 필드의 형식이 동일하도록 데이터를 분석하고 사용자 지정 옵션을 설정한 후 데이터를 가져옵니다. 다음은 날짜 형식을 년-월-일로 설정하는 방법의 예입니다(데이터에 년-월-일 형식의 날짜가 포함된 경우).

```sql
SET DATEFORMAT ymd;
```
1. bulk import 문을 사용하여 데이터 가져오기

```sql
BULK INSERT <tablename>
FROM
'<datafilename>'
WITH
(
    FirstRow = 2,
    FIELDTERMINATOR = ',', --this should be column separator in your data
    ROWTERMINATOR = '\n'   --this should be the row separator in your data
)
```

### <a name="sql-builtin-utilities"></a>SQL Server의 기본 제공 그래픽 유틸리티
SSIS(SQL Server Integrations Services)를 사용하여 플랫 파일의 데이터를 Azure 기반의 SQL Server VM으로 가져올 수 있습니다.
SSIS는 두 가지 스튜디오 환경에서 사용할 수 있습니다. 자세한 내용은 [SSIS(Integration Services) 및 스튜디오 환경](https://technet.microsoft.com/library/ms140028.aspx)을 참조하세요.

* SQL Server 데이터 도구에 대한 자세한 내용은 [Microsoft SQL Server 데이터 도구](https://msdn.microsoft.com/data/tools.aspx)  
* Import/Export 마법사에 대한 자세한 내용은 [SQL Server 가져오기 및 내보내기 마법사](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>온-프레미스 SQL Server에서 Azure VM의 SQL Server로 데이터 이동
다음과 같은 마이그레이션 전략을 사용할 수도 있습니다.

1. [Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [플랫 파일로 내보내기](#export-flat-file)
3. [SQL Database 마이그레이션 마법사](#sql-migration)
4. [데이터베이스 백업 및 복원](#sql-backup)

아래는 각 방법에 대한 설명입니다.

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Microsoft Azure 가상 컴퓨터에 SQL Server 데이터베이스 배포 마법사
**Microsoft Azure VM에 SQL Server 데이터베이스 배포 마법사** 는 온-프레미스 SQL Server 인스턴스에서 Azure VM의 SQL Server로 데이터를 이동하는 간단한 권장 방법입니다. 자세한 단계 및 다른 대안에 대한 설명은 [Azure VM의 SQL Server로 데이터베이스 마이그레이션](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)을 참조하세요.

### <a name="export-flat-file"></a>플랫 파일로 내보내기
[데이터의 대량 가져오기 및 내보내기(SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) 토픽에 설명된 대로 온-프레미스 SQL Server에서 데이터를 대량으로 내보내는 데 다양한 방법을 사용할 수 있습니다. 이 문서에서는 그 방법 중 하나로 BCP(대량 복사 프로그램)에 대해 설명합니다. 데이터를 플랫 파일로 내보낸 후에는 대량 삽입을 사용하여 다른 SQL Server로 데이터를 가져올 수 있습니다.

1. 다음과 같이 bcp 유틸리티를 사용하여 온-프레미스 SQL Server에서 파일로 데이터를 내보냅니다.

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. 1단계에서 내보낸 테이블 스키마에 대해 `create database` 및 `create table`을 사용하여 Azure 기반의 SQL Server VM에 데이터베이스 및 테이블을 만듭니다.
3. 내보내는/가져오는 데이터의 테이블 스키마를 설명하는 서식 파일을 만듭니다. 서식 파일의 세부 정보는 [서식 파일 만들기(SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx)에 설명되어 있습니다.

    SQL Server 컴퓨터에서 BCP를 실행하는 경우 서식 파일 생성

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    SQL Server에 대해 원격으로 BCP를 실행하는 경우 서식 파일 생성

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. [파일 원본에서 데이터 이동](#filesource_to_sqlonazurevm) 섹션에 설명된 아무 방법을 사용하여 플랫 파일에서 SQL Server로 데이터를 이동합니다.

### <a name="sql-migration"></a>SQL Database 마이그레이션 마법사
[SQL Server 데이터베이스 마이그레이션 마법사](https://sqlazuremw.codeplex.com/) 는 두 SQL Server 인스턴스 간에 데이터를 이동할 수 있는 사용자에게 편리한 방법을 제공합니다. 사용자가 원본과 대상 테이블 사이에 데이터 스키마를 매핑하고, 열 유형 및 다양한 기타 기능을 선택할 수 있습니다. 사용자에게는 보이지 않지만 SQL Server 데이터베이스 마이그레이션 마법사는 BCP(대량 복사)를 사용합니다. 아래는 SQL Database 마이그레이션 마법사의 시작 화면 스크린샷입니다.  

![SQL Server 마이그레이션 마법사][2]

### <a name="sql-backup"></a>데이터베이스 백업 및 복원
SQL Server는 다음을 지원합니다.

1. [데이터베이스 백업 및 복원 기능](https://msdn.microsoft.com/library/ms187048.aspx)(로컬 파일 백업 또는 blob로 bacpac 내보내기 모두 지원) 및 [데이터 계층 애플리케이션](https://msdn.microsoft.com/library/ee210546.aspx)(bacpac 사용).
2. 복사된 데이터베이스를 사용하여 또는 기존 SQL Azure 데이터베이스에 복사하여 Azure에서 직접 SQL Server VM을 만드는 기능. 자세한 내용은 [데이터베이스 복사 마법사 사용](https://msdn.microsoft.com/library/ms188664.aspx)을 참조하세요.

아래는 SQL Server Management Studio의 데이터베이스 백업/복원 옵션 스크린샷입니다.

![SQL Server 가져오기 도구][1]

## <a name="resources"></a>리소스
[Azure VM에서 SQL Server로 데이터베이스 마이그레이션](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Azure Virtual Machines의 SQL Server 개요](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
