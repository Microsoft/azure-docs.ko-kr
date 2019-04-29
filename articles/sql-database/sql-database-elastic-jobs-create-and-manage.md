---
title: Azure SQL 데이터베이스의 그룹 관리 | Microsoft Docs
description: 탄력적 작업 만들기 및 관리 과정을 단계별로 안내합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: a95b62ab8f639ad38ee3ac9ace4f30b62bd852bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476064"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>탄력적 작업을 사용하여 확장된 Azure SQL 데이터베이스 만들기 및 관리(미리 보기)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Elastic Database 작업** 은 스키마 변경, 자격 증명 관리, 참조 데이터 업데이트, 성능 데이터 수집 또는 테넌트(고객) 원격 분석 수집 등의 관리 작업을 실행하여 데이터베이스 그룹의 관리를 간소화합니다. Elastic Database 작업은 현재 Azure Portal 및 PowerShell cmdlet을 통해 사용할 수 있습니다. 그러나 Azure Portal 화면은 [탄력적 풀](sql-database-elastic-pool.md)의 모든 데이터베이스에서 실행되도록 기능이 제한됩니다. [Elastic Database 클라이언트 라이브러리](sql-database-elastic-scale-introduction.md)를 사용하여 만든 분할된 데이터베이스 집합 또는 사용자 정의 컬렉션을 포함하여 데이터베이스의 그룹에서 스크립트의 추가 기능 및 실행에 액세스하려면 [PowerShell을 사용하여 작업 만들기 및 관리](sql-database-elastic-jobs-powershell.md)를 참조하세요. 작업에 대한 자세한 내용은 [Elastic Database 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건
* Azure 구독. 무료 평가판에 대해서는 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 탄력적 풀. [탄력적 풀에 대한 정보](sql-database-elastic-pool.md)를 참조하세요.
* 탄력적 데이터베이스 작업 서비스 구성 요소의 설치 [탄력적 데이터베이스 작업 서비스](sql-database-elastic-jobs-service-installation.md) 설치를 참조하세요.

## <a name="creating-jobs"></a>작업 만들기
1. [Azure 포털](https://portal.azure.com)을 사용하여 기존의 탄력적 데이터베이스 작업 풀에서 **작업 만들기**를 클릭합니다.
2. 작업 제어 데이터베이스(작업에 대한 메타데이터 저장소)에 대한 데이터베이스 관리자(작업의 설치 시 생성됨)의 사용자 이름과 암호를 입력합니다.
   
    ![작업 이름을 지정하고 코드에 입력하거나 붙여넣은 다음 실행을 클릭합니다.][1]
3. **작업 만들기** 블레이드에 작업 이름을 입력합니다.
4. 스크립트 실행이 성공하기에 충분한 권한이 있는 대상 데이터베이스에 연결하려면 사용자 이름과 암호를 입력합니다.
5. T-SQL 스크립트를 붙여넣거나 입력합니다.
6. **저장**을 클릭한 다음 **실행**을 클릭합니다.
   
    ![작업 만들기 및 실행][5]

## <a name="run-idempotent-jobs"></a>멱등원 작업 실행
데이터베이스 집합에 대해 스크립트를 실행할 때는 해당 스크립트가 멱등원인지 확인해야 합니다. 즉, 스크립트가 이전에 불완전한 상태에서 실패한 경우에도 여러 번 실행될 수 있어야 합니다. 예를 들어, 스크립트가 실패하면 성공할 때까지 작업이 자동으로 다시 시도됩니다(한도 내에서, 결국은 재시도 논리가 재시도를 중단하게 됨). 이 작업을 수행하는 방법은 "IF EXISTS" 절을 사용하여 새 개체를 만들기 전에 발견된 모든 인스턴스를 삭제하는 것입니다. 아래에 예가 나와 있습니다.

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

또는 새 인스턴스를 만들기 전에 "IF NOT EXISTS" 절을 사용합니다.

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

그러면 스크립트에서 이전에 만든 테이블을 업데이트합니다.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>작업 상태 확인
작업이 시작된 후에 진행 상태를 확인할 수 있습니다.

1. 탄력적 풀 페이지에서 **작업 관리**를 클릭합니다.
   
    !["작업 관리"를 클릭합니다.][2]
2. 작업 이름을 클릭합니다. **상태** 는 "완료" 또는 "실패"일 수 있습니다. 생성 및 실행 날짜와 시간을 포함한 작업 세부 정보가 표시됩니다. 아래 목록(c)은 날짜와 시간 정보를 포함하여 풀에 있는 데이터베이스에 대한 스크립트의 진행 상태를 보여줍니다.
   
    ![완료된 작업 확인][3]

## <a name="checking-failed-jobs"></a>실패한 작업 확인
작업이 실패할 경우 실행 로그가 생깁니다. 실패한 작업의 이름을 클릭하면 세부 정보를 볼 수 있습니다.

![실패한 작업 확인][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


