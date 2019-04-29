---
title: '빠른 시작: Azure SQL Data Warehouse 만들기 및 쿼리 - Azure Portal | Microsoft Docs'
description: Azure Portal에서 Azure SQL Data Warehouse로 데이터 웨어하우스를 만들고 쿼리합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 08/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 053dccb4ba5798fabd4426d5741d9238af579449
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732402"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure SQL 데이터 웨어하우스 생성 및 쿼리

Azure Portal을 사용하여 Azure SQL 데이터 웨어하우스를 신속하게 만들고 쿼리합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!NOTE]
> SQL Data Warehouse를 만들면 새로운 유료 서비스가 발생할 수 있습니다. 자세한 내용은 [SQL Data Warehouse 가격](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

최신 버전의 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)를 다운로드하여 설치합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-data-warehouse"></a>데이터 웨어하우스 만들기

Azure SQL Database가 정의된 [계산 리소스](memory-and-concurrency-limits.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 및 [Azure SQL 논리 서버](../sql-database/sql-database-logical-servers.md)에 생성됩니다. 

다음 단계에 따라 AdventureWorksDW 샘플 데이터가 포함된 SQL 데이터 웨어하우스를 만듭니다. 

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **새로 만들기**페이지의 **추천**에서 **SQL Data Warehouse**를 선택합니다.

    ![빈 데이터 웨어하우스 만들기](media/load-data-from-azure-blob-storage-using-polybase/create-empty-data-warehouse.png)

3. 다음 정보로 SQL Data Warehouse 양식을 작성합니다.

    | 설정 | 제안 값 | 설명 |
    | :------ | :-------------- | :---------- |
    | **데이터베이스 이름** | mySampleDataWarehouse | 유효한 데이터베이스 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. 데이터 웨어하우스는 데이터베이스의 한 종류입니다.|
    | **구독** | 사용자의 구독 | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
    | **리소스 그룹** | myResourceGroup | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
    | **원본 선택** | 샘플 | 샘플 데이터베이스를 로드하도록 지정합니다. 데이터 웨어하우스는 데이터베이스의 한 종류입니다. |
    | **샘플 선택** | AdventureWorksDW | AdventureWorksDW 샘플 데이터베이스를 로드하도록 지정합니다. |
    ||||

    ![데이터 웨어하우스 만들기](media/create-data-warehouse-portal/select-sample.png)

4. **서버**를 클릭하여 새 데이터베이스에 새 서버를 만들고 구성합니다. 다음 정보로 **새 서버 폼**을 작성합니다. 

    | 설정 | 제안 값 | 설명 |
    | :------ | :-------------- | :---------- |
    | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. |
    | **서버 관리자 로그인** | 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요.|
    | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 포함해야 합니다. |
    | **위치**: | 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |
    ||||

    ![데이터베이스 서버 만들기](media/load-data-from-azure-blob-storage-using-polybase/create-database-server.png)

5. **선택**을 클릭합니다.

6. **성능 수준**을 클릭하여 데이터 웨어하우스에 대한 성능 구성을 지정합니다.

7. 이 자습서에서는 **Gen2**를 선택합니다. 기본적으로 슬라이더는 **DW1000c**로 설정되어 있습니다. 위아래로 이동하면서 작동 방식을 확인하세요. 

    ![성능 구성](media/load-data-from-azure-blob-storage-using-polybase/configure-performance.png)

8. **적용**을 클릭합니다.

9. 이제 SQL Data Warehouse 폼을 완료했으므로 **만들기**를 클릭하여 데이터베이스를 프로비전합니다. 프로비전하는 데 몇 분이 걸립니다. 

    ![만들기 클릭](media/load-data-from-azure-blob-storage-using-polybase/click-create.png)

10. 도구 모음에서 **알림**을 클릭하여 배포 프로세스를 모니터링합니다.
    
     ![알림](media/load-data-from-azure-blob-storage-using-polybase/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 만들기

SQL Data Warehouse 서비스는 외부 애플리케이션 및 도구가 서버 또는 서버의 데이터베이스에 연결되지 않도록 방지하는 서버 수준에 방화벽을 만듭니다. 연결을 사용하려면 특정 IP 주소에 대한 연결을 사용하도록 설정하는 방화벽 규칙을 추가할 수 있습니다. 다음 단계에 따라 클라이언트의 IP 주소에 대해 [서버 수준 방화벽 규칙](../sql-database/sql-database-firewall-configure.md)을 만듭니다. 

> [!NOTE]
> SQL Data Warehouse는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 포트 1433을 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.

1. 배포가 완료되면 왼쪽 메뉴에서 **SQL Data Warehouses**를 클릭한 다음, **SQL Data Warehouses** 페이지에서 **mySampleDatabase**를 클릭합니다. 데이터베이스에 대한 개요 페이지가 열려 정규화된 서버 이름(예: **mynewserver-20180430.database.windows.net**)을 표시하고 추가 구성을 위한 옵션을 제공합니다. 

2. 후속 빠른 시작에서 서버 및 해당 데이터베이스에 연결하는 데 사용하기 위해 이 정규화된 서버 이름을 복사합니다. 서버 설정을 열려면 서버 이름을 클릭합니다.

   ![서버 이름 찾기](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png) 

3. 서버 설정을 열려면 
4. 서버 이름을 클릭합니다.

   ![서버 설정](media/load-data-from-azure-blob-storage-using-polybase/server-settings.png) 

5. **방화벽 설정 표시**를 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

   ![서버 방화벽 규칙](media/load-data-from-azure-blob-storage-using-polybase/server-firewall-rule.png) 

4. 현재 IP 주소를 새 방화벽 규칙에 추가하려면 도구 모음에서 **클라이언트 IP 추가**를 클릭합니다. 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

5. **저장**을 클릭합니다. 논리 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 방화벽 규칙이 생성됩니다.

6. **확인**을 클릭한 후 **방화벽 설정** 페이지를 닫습니다.

이제 이 IP 주소를 사용하여 SQL 서버 및 해당 데이터 웨어하우스에 연결할 수 있습니다. SQL Server Management Studio 또는 원하는 다른 도구에서 연결이 제대로 작동합니다. 연결할 때 이전에 만든 ServerAdmin 계정을 사용합니다.

> [!IMPORTANT]
> SQL Database 방화벽을 통한 액세스는 기본적으로 모든 Azure 서비스에 대해 사용됩니다. 이 페이지에서 **꺼짐**을 클릭한 다음 **저장**을 클릭하여 모든 Azure 서비스에 대한 방화벽을 사용하지 않도록 설정합니다.

## <a name="get-the-fully-qualified-server-name"></a>정규화된 서버 이름 확인

Azure Portal에서 SQL 서버의 정규화된 서버 이름을 확인합니다. 나중에 서버에 연결할 때 이 정규화된 이름을 사용합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Data Warehouse**를 선택하고, **SQL Data Warehouse** 페이지에서 사용자의 데이터 웨어하우스를 클릭합니다. 
3. 데이터베이스의 경우 Azure Portal의 **개요** 창에서 **서버 이름**을 찾고 복사합니다. 이 예제에서 정규화된 이름은 mynewserver-20180430.database.windows.net입니다. 

    ![연결 정보](media/load-data-from-azure-blob-storage-using-polybase/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>서버 관리자 권한으로 서버에 연결

이 섹션에서는 SSMS([SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms))를 사용하여 Azure SQL 서버에 연결합니다.

1. SQL Server Management Studio를 엽니다.

2. **서버에 연결** 대화 상자에서 다음 정보를 입력합니다.

   | 설정 | 제안 값 | 설명 |
   | :------ | :-------------- | :---------- |
   | 서버 유형 | 데이터베이스 엔진 | 이 값은 필수입니다. |
   | 서버 이름 | 정규화된 서버 이름 | 다음은 예제 **mynewserver-20180430.database.windows.net**입니다. |
   | 인증 | SQL Server 인증 | SQL 인증은 이 자습서에서 구성되어 있는 유일한 인증 유형입니다. |
   | 로그인 | 서버 관리자 계정 | 서버를 만들 때 지정한 계정입니다. |
   | 암호 | 서버 관리자 계정의 암호 | 서버를 만들 때 지정한 암호입니다. |
   ||||

    ![서버 연결](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

3. **연결**을 클릭합니다. SSMS에서 개체 탐색기 창이 열립니다. 

4. 개체 탐색기에서 **데이터베이스**를 확장합니다. 그런 다음 **mySampleDatabase**를 확장하여 새 데이터베이스의 개체를 표시합니다.

    ![데이터베이스 개체](media/create-data-warehouse-portal/connected.png) 

## <a name="run-some-queries"></a>일부 쿼리 실행

SQL Data Warehouse는 쿼리 언어로 T-SQL을 사용합니다. 쿼리 창을 열고 T-SQL 쿼리 몇 개를 실행하려면 다음 단계를 수행합니다.

1. 마우스 오른쪽 단추로 **mySampleDataWarehouse**를 클릭하고 **새 쿼리**를 선택합니다. 새 쿼리 창이 열립니다.
2. 쿼리 창에서 다음 명령을 입력하여 데이터베이스 목록을 표시합니다.

    ```sql
    SELECT * FROM sys.databases
    ```

3. **실행**을 클릭합니다. 쿼리 결과로 **master** 및 **mySampleDataWarehouse** 데이터베이스가 표시됩니다.

    ![데이터베이스 쿼리](media/create-data-warehouse-portal/query-databases.png)

4. 일부 데이터를 살펴보려면 다음 명령을 사용하여 성이 Adams이고 자녀 세 명을 둔 고객의 수를 확인합니다. 결과에 6명의 고객이 나열됩니다. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

    ![dbo.dimCustomer 쿼리](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>리소스 정리

데이터 웨어하우스 단위 및 데이터 웨어하우스에 저장된 데이터에 대해 요금이 청구됩니다. 이러한 계산 및 스토리지 리소스에 대한 요금이 별도로 청구됩니다. 

- 데이터를 스토리지에 보관하려는 경우 데이터 웨어하우스를 사용하지 않을 때 계산을 일시 중지할 수 있습니다. 계산을 일시 중지하면 데이터 스토리지 요금만 부과됩니다. 데이터를 작업할 준비가 완료되면 언제든지 계산을 다시 시작할 수 있습니다.
- 앞으로 요금이 부과되지 않게 하려면 데이터 웨어하우스를 삭제하면 됩니다. 

필요에 따라 다음 단계에 따라 리소스를 정리합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 데이터 웨어하우스를 클릭합니다.

    ![리소스 정리](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 계산을 일시 중지하려면 **일시 중지** 단추를 클릭합니다. 데이터 웨어하우스가 일시 중지되면 **시작** 단추가 표시됩니다. 계산을 재개하려면 **시작**을 클릭합니다.

3. 계산 또는 스토리지에 대한 요금이 청구되지 않도록 데이터 웨어하우스를 제거하려면 **삭제**를 클릭합니다.

4. 만든 SQL 서버를 제거하려면 이전 이미지에 있는 **mynewserver-20180430.database.windows.net**을 클릭한 다음, **삭제**를 클릭합니다. 서버를 삭제하면 해당 서버에 할당된 모든 데이터베이스가 삭제되므로 주의해서 실행해야 합니다.

5. 리소스 그룹을 제거하려면 **myResourceGroup**을 클릭하고 **리소스 그룹 삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

지금까지 데이터 웨어하우스를 만들고, 방화벽 규칙을 만들고, 데이터 웨어하우스에 연결하고, 몇 가지 쿼리를 실행했습니다. Azure SQL Data Warehouse에 대해 자세히 알아보려면 데이터 로드에 대한 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [SQL 데이터 웨어하우스로 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)
