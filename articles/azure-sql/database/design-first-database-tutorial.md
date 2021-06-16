---
title: '자습서: SSMS를 사용하여 첫 번째 관계형 데이터베이스 디자인'
description: SQL Server Management Studio를 사용하여 Azure SQL Database에서 첫 번째 관계형 데이터베이스를 디자인하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma, v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: edbac11d17547ac58523559c2dfcf49967b77a1d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110699744"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>자습서: SSMS를 사용하여 Azure SQL Database에서 관계형 데이터베이스 디자인
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL 데이터베이스는 Microsoft Cloud(Azure)의 관계형 DBaaS(Database-As-A-Service)입니다. 이 자습서에서는 Azure Portal 및 SSMS[(SQL Server Management Studio)](/sql/ssms/sql-server-management-studio-ssms)를 사용하는 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Azure Portal을 사용하여 데이터베이스 만들기*
> - Azure Portal을 사용하여 서버 수준 IP 방화벽 규칙 설정
> - SSMS로 데이터베이스에 연결
> - SSMS를 사용하여 테이블 만들기
> - BCP를 사용하여 데이터 대량 로드
> - SSMS를 사용하여 데이터 쿼리

*Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

> [!TIP]
> 다음 Microsoft Learn 모듈을 사용하면 간단한 데이터베이스 생성을 포함하여 [Azure SQL Database를 쿼리하는 ASP.NET 애플리케이션을 개발하고 구성](/learn/modules/develop-app-that-queries-azure-sql/)하는 방법을 무료로 배울 수 있습니다.
> [!NOTE]
> 이 자습서에서는 Azure SQL Database를 사용하고 있습니다. 탄력적 풀 또는 SQL Managed Instance에서 풀링된 데이터베이스를 사용할 수도 있습니다. SQL Managed Instance에 대한 연결은 SQL Managed Instance 빠른 시작을 참조하세요. [빠른 시작: Azure SQL Managed Instance에 연결하도록 Azure VM 구성](../managed-instance/connect-vm-instance-configure.md) 및 [빠른 시작: 온-프레미스에서 Azure SQL Managed Instance로의 지점 및 사이트 간 연결 구성](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 항목이 설치되어 있어야 합니다.

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)(최신 버전)
- [BCP 및 SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433)(최신 버전)

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Azure SQL Database에서 빈 데이터베이스 만들기

Azure SQL Database의 데이터베이스는 정의된 컴퓨팅 및 스토리지 리소스 세트를 사용하여 생성됩니다. 데이터베이스는 [Azure 리소스 그룹](../../active-directory-b2c/overview.md) 내에 생성되고 [논리 SQL 서버](logical-servers.md)를 사용하여 관리됩니다.

다음 단계에 따라 빈 데이터베이스를 만듭니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.
2. **새로 만들기** 페이지의 Azure Marketplace 섹션에서 **데이터베이스** 를 선택한 다음, **추천** 섹션에서 **SQL Database** 를 클릭합니다.

   ![빈 데이터베이스 만들기](./media/design-first-database-tutorial/create-empty-database.png)

3. 위의 이미지에 표시된 대로 다음과 같은 정보를 사용하여 **SQL Database** 형식을 작성합니다.

    | 설정       | 제안 값 | Description |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **데이터베이스 이름** | *yourDatabase* | 유효한 데이터베이스 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
    | **구독** | *yourSubscription*  | 구독에 대한 자세한 내용은 [구독](https://account.windowsazure.com/Subscriptions)을 참조하세요. |
    | **리소스 그룹** | *yourResourceGroup* | 유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
    | **원본 선택** | 빈 데이터베이스 | 빈 데이터베이스를 만들도록 지정합니다. |

4. **서버** 를 클릭하여 기존 서버를 사용하거나 새 서버를 만들고 구성합니다. 기존 서버를 선택하거나 **새 서버 만들기** 를 클릭하고 **새 서버** 양식에 다음 정보를 입력합니다.

    | 설정       | 제안 값 | Description |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
    | **서버 관리자 로그인** | 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
    | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 사용해야 합니다. |
    | **위치** | 유효한 위치 | 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요. |

    ![create database-server](./media/design-first-database-tutorial/create-database-server.png)

5. **선택** 을 클릭합니다.
6. **가격 책정 계층** 을 클릭하여 서비스 계층, DTU나 vCore 개수 및 스토리지 크기를 지정합니다. 각 서비스 계층에 대해 사용할 수 있는 DTU/vCore 및 스토리지 수에 대한 옵션을 살펴볼 수 있습니다.

    서버 계층, DTU 또는 vCore 수, 스토리지 양을 선택한 후 **적용** 을 클릭합니다.

7. 빈 데이터베이스에 대한 **데이터 정렬** 을 입력합니다(이 자습서의 경우 기본값 사용). 데이터 정렬에 대한 자세한 내용은 [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

8. 이제 **SQL Database** 양식을 완료했으므로 **만들기** 를 클릭하여 데이터베이스를 프로비전합니다. 이 단계를 완료하는 데 몇 분이 걸릴 수 있습니다.

9. 도구 모음에서 **알림** 을 클릭하여 배포 프로세스를 모니터링합니다.

   ![스크린샷은 배포가 진행 중인 알림 메뉴를 보여줍니다.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>서버 수준 IP 방화벽 규칙 만들기

Azure SQL Database는 서버 수준에서 IP 방화벽을 만듭니다. 방화벽 규칙에서 특정 IP가 방화벽을 통과하도록 허용하지 않는 한 이 방화벽은 외부 애플리케이션과 도구가 서버 및 서버의 데이터베이스에 연결하지 못하게 차단합니다. 데이터베이스에 대한 외부 연결을 사용하려면 먼저 IP 주소(또는 IP 주소 범위)에 대한 IP 방화벽 규칙을 추가해야 합니다. 다음 단계에 따라 [서버 수준 IP 방화벽 규칙](firewall-configure.md)을 만듭니다.

> [!IMPORTANT]
> Azure SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 이 서비스에 연결을 시도하면 포트 1433을 통한 아웃바운드 트래픽을 네트워크 방화벽에서 허용하지 않을 수 있습니다. 이 경우 관리자가 1433 포트를 열지 않으면 데이터베이스에 연결할 수 없습니다.

1. 배포가 완료되면 Azure Portal 메뉴에서 **SQL 데이터베이스** 를 선택하거나아무 페이지에서 *SQL 데이터베이스* 를 선택합니다.  

1. **SQL 데이터베이스** 페이지에서 *데이터베이스* 를 선택합니다. 데이터베이스에 대한 개요 페이지가 열리고, 정규화된 **서버 이름**(예: `contosodatabaseserver01.database.windows.net`)을 표시하고, 추가 구성 옵션을 제공합니다.

   ![서버 이름](./media/design-first-database-tutorial/server-name.png)

1. SQL Server Management Studio에서 서버 및 데이터베이스에 연결하는 데 사용할 수 있도록 이 정규화된 서버 이름을 복사합니다.

1. 도구 모음에서 **서버 방화벽 설정** 을 클릭합니다. 서버에 대한 **방화벽 설정** 페이지가 열립니다.

   ![서버 수준 IP 방화벽 규칙](./media/design-first-database-tutorial/server-firewall-rule.png)

1. 도구 모음에서 **클라이언트 IP 추가** 를 클릭하여 현재 IP 주소를 새 IP 방화벽 규칙에 추가합니다. IP 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

1. **저장** 을 클릭합니다. 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 IP 방화벽 규칙이 생성됩니다.

1. **확인** 을 클릭한 후 **방화벽 설정** 페이지를 닫습니다.

이제 IP 주소가 IP 방화벽을 통과할 수 있습니다. 이제 SQL Server Management Studio 또는 원하는 다른 도구를 사용하여 데이터베이스에 연결할 수 있습니다. 이전에 만든 서버 관리자 계정을 사용해야 합니다.

> [!IMPORTANT]
> 기본적으로 모든 Azure 서비스에는 SQL Database IP 방화벽을 통한 액세스가 사용됩니다. 이 페이지에서 **끄기** 를 클릭하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.

## <a name="connect-to-the-database"></a>데이터베이스에 연결

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)를 사용하여 데이터베이스에 대한 연결을 설정합니다.

1. SQL Server Management Studio를 엽니다.
2. **서버에 연결** 대화 상자에 다음 정보를 입력합니다.

   | 설정       | 제안 값 | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **서버 유형** | 데이터베이스 엔진 | 이 값은 필수입니다. |
   | **서버 이름** | 정규화된 서버 이름 | 예: *yourserver.database.windows.net*. |
   | **인증** | SQL Server 인증 | SQL 인증은 이 자습서에서 구성한 유일한 인증 유형입니다. |
   | **로그인** | 서버 관리자 계정 | 서버를 만들 때 지정한 계정입니다. |
   | **암호** | 서버 관리자 계정의 암호 | 서버를 만들 때 지정한 암호입니다. |

   ![서버에 연결](./media/design-first-database-tutorial/connect.png)

3. **서버에 연결** 대화 상자에서 **옵션** 을 클릭합니다. **데이터베이스에 연결** 섹션에서 *yourDatabase* 를 입력하여 이 데이터베이스에 연결합니다.

    ![서버에서 db에 연결](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. **연결** 을 클릭합니다. SSMS에서 **개체 탐색기** 창이 열립니다.

5. **개체 탐색기** 에서 **데이터베이스** 를 확장한 다음, *yourDatabase* 를 확장하여 샘플 데이터베이스에 있는 개체를 봅니다.

   ![데이터베이스 개체](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>데이터베이스에 테이블 만들기

[Transact-SQL](/sql/t-sql/language-reference)을 사용하여 대학의 학생 관리 시스템을 모델링하는 네 개의 테이블이 있는 데이터베이스 스키마 만들기

- Person
- 과정
- 학생
- 크레딧

다음 다이어그램에서는 이러한 테이블 간의 관계를 보여 줍니다. 이러한 테이블 중 일부는 다른 테이블의 열을 참조합니다. 예를 들어 *Student* 테이블은 *Person* 테이블의 *PersonId* 열을 참조합니다. 다이어그램에 대해 학습하여 이 자습서에서 테이블 간의 관계를 이해합니다. 효과적인 데이터베이스 테이블을 만드는 방법에 대한 자세한 내용은 [효과적인 데이터베이스 테이블 만들기](/previous-versions/tn-archive/cc505842(v=technet.10))를 참조하세요. 데이터 형식을 선택하는 방법은 [데이터 형식](/sql/t-sql/data-types/data-types-transact-sql)을 참조하세요.

> [!NOTE]
> 또한 [SQL Server Management Studio의 테이블 디자이너](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools)를 사용하여 테이블을 만들고 디자인할 수도 있습니다.

![테이블 관계](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. **개체 탐색기** 에서 *yourDatabase* 를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.

2. 쿼리 창에서 다음 쿼리를 실행하여 데이터베이스에 4개의 테이블을 만듭니다.

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![테이블 만들기](./media/design-first-database-tutorial/create-tables.png)

3. 만든 테이블을 보려면 **개체 탐색기** 에서 *yourDatabase* 아래에 있는 **테이블** 노드를 확장합니다.

   ![ssms 테이블 생성](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드

1. 다운로드 폴더에 *sampleData* 라는 폴더를 만들어 데이터베이스의 샘플 데이터를 저장합니다.

2. 다음 링크를 마우스 오른쪽 단추로 클릭하고 *sampleData* 폴더에 샘플 데이터를 저장합니다.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. 명령 프롬프트 창을 열고 *sampleData* 폴더로 이동합니다.

4. 다음 명령을 실행하여 테이블에 샘플 데이터를 삽입하고 *서버*, *데이터베이스*, *사용자* 및 *암호* 값을 해당 환경에 맞는 값으로 바꿉니다.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

이제 앞에서 만든 테이블에 샘플 데이터가 로드되었습니다.

## <a name="query-data"></a>쿼리 데이터

다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다. SQL 쿼리 작성에 대한 자세한 내용은 [SQL 쿼리 작성](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90))을 참조하세요. 첫 번째 쿼리는 4개 테이블을 모두 조인하여 'Dominick Pope' 선생님의 학생 중에 성적이 75%보다 높은 학생을 찾습니다. 두 번째 쿼리는 4개 테이블을 모두 조인하여 'Noe Coleman'이 등록한 적이 있는 과정을 찾습니다.

1. SQL Server Management Studio 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. 쿼리 창에서 다음 쿼리를 실행합니다.

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 여러 가지 기본적인 데이터베이스 작업에 대해 알아보았습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> - Azure Portal을 사용하여 데이터베이스 만들기*
> - Azure Portal을 사용하여 서버 수준 IP 방화벽 규칙 설정
> - SSMS로 데이터베이스에 연결
> - SSMS를 사용하여 테이블 만들기
> - BCP를 사용하여 데이터 대량 로드
> - SSMS를 사용하여 데이터 쿼리

Visual Studio 및 C#을 사용하여 데이터베이스를 설계하는 방법에 대한 자세한 내용을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Azure SQL Database C# 및 ADO.NET 내에서 관계형 데이터베이스 디자인](design-first-database-csharp-tutorial.md)