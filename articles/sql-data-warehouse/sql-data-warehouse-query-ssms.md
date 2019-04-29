---
title: Azure SQL Data Warehouse에 연결 - SSMS | Microsoft Docs
description: SSMS(SQL Server Management Studio)를 사용하여 Azure SQL Data Warehouse에 연결하고 쿼리합니다.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 809802bc34a6cdc45f4b018d35895939e4b8f667
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476556"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>SSM(SQL Server Management Studio)를 사용하여 SQL Data Warehouse에 연결
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

SSMS(SQL Server Management Studio)를 사용하여 Azure SQL Data Warehouse에 연결하고 쿼리합니다. 

## <a name="prerequisites"></a>필수 조건
이 자습서를 사용하려면 다음이 필요합니다.

* 기존 SQL 데이터 웨어하우스. 만들려면 [SQL Data Warehouse 만들기][Create a SQL Data Warehouse]를 참조하세요.
* SSMS(SQL Server Management Studio) 설치됨. 아직 없는 경우 무료로 [SSMS를 설치][Install SSMS]합니다.
* 정규화된 SQL 서버 이름. 이를 찾으려면 [SQL Data Warehouse에 연결][Connect to SQL Data Warehouse]을 참조하세요.

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. SQL Data Warehouse에 연결
1. SSMS를 엽니다.
2. 개체 탐색기를 엽니다. 이를 수행하려면 **파일** > **개체 탐색기 연결**을 선택합니다.
   
    ![SQL Server 개체 탐색기][1]
3. 서버 창에 연결에서 필드를 입력합니다.
   
    ![서버에 연결][2]
   
   * **서버 이름**. 이전에 식별한 **서버 이름** 을 입력합니다.
   * **인증**. **SQL Server 인증** 또는 **Active Directory 통합 인증**을 선택합니다.
   * **사용자 이름** 및 **암호**. 위에서 SQL Server 인증을 선택한 경우 사용자 이름 및 암호를 입력합니다.
   * **Connect**를 클릭합니다.
4. 탐색하려면 SQL Azure Server를 확장합니다. 서버와 연결된 데이터베이스를 볼 수 있습니다. AdventureWorksDW를 확장하여 샘플 데이터베이스의 테이블을 확인합니다.
   
    ![AdventureWorksDW 탐색하기][3]

## <a name="2-run-a-sample-query"></a>2. 샘플 쿼리 실행
데이터베이스에 대한 연결을 설정했으므로 쿼리를 작성합니다.

1. SQL Server 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭합니다.
2. **새 쿼리**를 선택합니다. 새 쿼리 창이 열립니다.
   
    ![새 쿼리][4]
3. 이 TSQL 쿼리를 쿼리 창에 복사합니다.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 쿼리를 실행합니다. 이렇게 하려면 `Execute`를 클릭하거나 다음 바로 가기(`F5`)를 사용합니다.
   
    ![쿼리 실행][5]
5. 쿼리 결과를 봅니다. 이 예에서 FactInternetSales 테이블에는 60398 행이 있습니다.
   
    ![쿼리 결과][6]

## <a name="next-steps"></a>다음 단계
이제 연결 및 쿼리할 수 있으므로 [PowerBI로 데이터 시각화][visualizing the data with PowerBI]를 시도해 보세요.

Azure Active Directory 인증을 위한 환경을 구성하려면 [SQL Data Warehouse에 대한 인증][Authenticate to SQL Data Warehouse]을 참조하세요.

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
