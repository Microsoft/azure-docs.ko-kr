---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: bca885f9b19086302fd91d29b8ff6553b7205f28
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86544509"
---
## <a name="c-program-example"></a>C# 프로그램 예

이 문서의 다음 섹션에는 ADO.NET을 사용하여 T-SQL(Transact-SQL) 문을 SQL Database로 보내는 C# 프로그램이 나와 있습니다. C# 프로그램은 다음 작업을 보여줍니다.

- [ADO.NET을 사용하여 SQL Database에 연결](#cs_1_connect)
- [T-SQL 명령문을 반환하는 메서드](#cs_2_return)
    - 테이블 만들기
    - 데이터로 테이블 채우기
    - 데이터 업데이트, 삭제 및 선택
- [T-SQL을 데이터베이스에 제출](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>엔터티 관계 다이어그램(ERD)

`CREATE TABLE` 명령문에는 두 테이블 간의 *외래 키*(FK) 관계를 만들기 위해 **REFERENCES** 키워드가 있습니다. *tempdb*를 사용하는 경우 선행 대시의 쌍을 사용하여 `--REFERENCES` 키워드를 주석으로 처리합니다.

ERD는 두 테이블 간의 관계를 표시합니다. **tabEmployee.DepartmentCode** *자식* 열의 값은 **tabDepartment.DepartmentCode** *부모* 열의 값으로 제한됩니다.

![외래 키를 표시하는 ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> *tempdb*에서 `#`를 테이블 이름 앞에 추가하여 임시 테이블로 만드는 T-SQL 편집 옵션이 있습니다. 이는 사용할 수 있는 테스트 데이터베이스가 없는 경우 데모용으로 유용합니다. 외래 키에 대한 참조는 사용하는 동안 적용되지 않으며 프로그램 실행이 완료된 후 연결을 닫으면 임시 테이블은 자동으로 삭제됩니다.

### <a name="to-compile-and-run"></a>컴파일 및 실행

C# 프로그램은 논리적으로 하나의 .cs 파일이며, 물리적으로 여러 코드 블록으로 분할되어 각 블록을 보다 쉽게 이해할 수 있습니다. 프로그램을 컴파일하고 실행하려면 다음 단계를 수행합니다.

1. Visual Studio에서 C# 프로젝트를 만듭니다. 프로젝트 형식은 **템플릿** > **Visual C#**  > **Windows데스크톱** > **콘솔 앱(.NET Framework)** 에 있는 *콘솔*이어야 합니다.

1. 파일 *Program.cs*에서 다음 단계를 사용하여 코드의 시작 줄을 바꿉니다.

    1. 표시되는 동일한 순서로 다음 코드 블록을 복사 및 붙여넣기합니다. [데이터베이스에 연결](#cs_1_connect), [T-SQL 생성](#cs_2_return) 및 [데이터베이스에 전송](#cs_3_submit)을 참조하세요.

    1. `Main` 메서드에서 다음 값을 변경합니다.

        - *cb.DataSource*
        - *cb.UserID*
        - *cb.Password*
        - *cb.InitialCatalog*

1. *System.Data.dll* 어셈블리가 참조되는지 확인합니다. 확인하려면 **솔루션 탐색기** 창에서 **참조** 노드를 확장합니다.

1. Visual Studio의 프로그램을 빌드 및 실행하려면 **시작** 단추를 선택합니다. 보고서 출력이 프로그램 창에 표시됩니다. GUID 값은 테스트 실행 간에 달라질 수 있습니다.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"></a>

### <a name="connect-to-sql-database-using-adonet"></a>ADO.NET을 사용하여 SQL Database에 연결

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"></a>

### <a name="methods-that-return-t-sql-statements"></a>T-SQL 명령문을 반환하는 메서드

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"></a>

### <a name="submit-t-sql-to-the-database"></a>T-SQL을 데이터베이스에 제출

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```