---
title: Azure Functions를 사용하여 데이터베이스 정리 작업 수행 | Microsoft Docs
description: Azure Functions를 사용하여 Azure SQL Database에 연결하여 행을 주기적으로 정리하는 작업을 예약합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 4ec2e9b931e6405aca5b4237bc044647af3b8bb3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120673"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions를 사용하여 Azure SQL Database에 연결

이 문서에서는 Azure Functions를 사용하여 Azure SQL Database 인스턴스에 연결되는 예약된 작업을 만드는 방법을 보여줍니다. 함수 코드는 데이터베이스의 테이블에 있는 행을 정리합니다. 새 C# 함수는 Visual Studio 2017에서 미리 정의된 타이머 트리거 템플릿을 기반으로 생성됩니다. 이 시나리오를 지원하려면 함수 앱에서 데이터베이스 연결 문자열을 앱 설정으로 설정해야 합니다. 이 시나리오는 데이터베이스에 대한 대량 작업을 사용합니다. 

C# 함수를 처음 사용하는 경우 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)를 참고해야 합니다.

## <a name="prerequisites"></a>필수 조건

+ [Visual Studio를 사용하여 첫 번째 함수 만들기](functions-create-your-first-function-visual-studio.md) 문서의 단계를 완료하여 버전 2.x 런타임을 대상으로 하는 로컬 함수 앱을 만듭니다. 또한 프로젝트를 Azure의 함수 앱에 게시했어야 합니다.

+ 이 문서은 AdventureWorksLT 샘플 데이터베이스의 **SalesOrderHeader** 테이블에서 대량 정리 작업을 실행하는 Transact-SQL 명령을 보여줍니다. AdventureWorksLT 샘플 데이터베이스를 만들려면 [Azure Portal에서 Azure SQL Database 만들기](../sql-database/sql-database-get-started-portal.md) 문서의 단계를 완료합니다.

+ 이 빠른 시작 에서 사용하는 컴퓨터의 공용 IP 주소에 대해 [서버 수준 방화벽 규칙](../sql-database/sql-database-get-started-portal-firewall.md)을 추가해야 합니다. 이 규칙은 로컬 컴퓨터에서 SQL 데이터베이스 인스턴스에 액세스할 수 있어야 합니다.  

## <a name="get-connection-information"></a>연결 정보 가져오기

[Azure Portal에서 Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)를 완료하면 만든 데이터베이스에 대한 연결 문자열을 가져와야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 선택합니다.

1. **설정** 아래에서 **연결 문자열**을 선택하고, 전체 **ADO.NET** 연결 문자열을 복사합니다.

    ![ADO.NET 연결 문자열을 복사합니다.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>연결 문자열 설정

함수 앱은 Azure에서 함수 실행을 호스트합니다. 보안 연습으로 함수 앱 설정에 연결 문자열 및 다른 비밀을 저장합니다. 애플리케이션 설정을 사용하여 코드로 연결 문자열이 실수로 노출되는 것을 방지합니다. Visual Studio에서 직접 함수 앱의 앱 설정에 액세스할 수 있습니다.

이전에 Azure에 앱을 게시했어야 합니다. 아직 이렇게 수행하지 않은 경우 [함수 앱을 Azure에 게시](functions-develop-vs.md#publish-to-azure)합니다.

1. 솔루션 탐색기에서 함수 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고, **게시** > **애플리케이션 설정 관리...** 를 선택합니다. **설정 추가**를 선택하고, **새 앱 설정 이름**에서 `sqldb_connection`을 입력하고, **확인**을 선택합니다.

    ![함수 앱에 대한 애플리케이션 설정입니다.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. 새로운 **sqldb_connection** 설정에서는 이전 섹션에서 복사한 연결 문자열을 **로컬** 필드에 붙여넣고, `{your_username}` 및 `{your_password}` 자리 표시자를 실제 값으로 바꿉니다. **로컬에서 값 삽입**을 선택하여 업데이트된 값을 **원격** 필드에 복사한 다음, **확인**을 선택합니다.

    ![SQL 연결 문자열 설정을 추가합니다.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    연결 문자열은 Azure에 암호화되어 저장됩니다(**원격**). 비밀 유출을 방지하기 위해 local.settings.json 프로젝트 파일(**로컬**)은 .gitignore 파일을 사용하는 등 원본 제어에서 제외되어야 합니다.

## <a name="add-the-sqlclient-package-to-the-project"></a>프로젝트에 SqlClient 패키지 추가

SqlClient 라이브러리를 포함하는 NuGet 패키지를 추가해야 합니다. 이 데이터 액세스 라이브러리는 SQL 데이터베이스에 연결하는 데 필요합니다.

1. Visual Studio 2017에서 로컬 함수 앱 프로젝트를 엽니다.

1. 솔루션 탐색기에서 함수 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고, **NuGet 패키지 관리**를 선택합니다.

1. **찾아보기** 탭에서 ```System.Data.SqlClient```를 검색하고 찾은 항목을 선택합니다.

1. **System.Data.SqlClient** 페이지에서 `4.5.1` 버전을 선택한 다음, **설치**를 클릭합니다.

1. 설치가 완료되면 변경 내용을 검토한 다음 **확인**을 클릭하여 **미리 보기** 창을 닫습니다.

1. **라이선스 승인** 창이 표시되면 **동의**를 클릭합니다.

이제 SQL Database와 연결하는 C# 함수 코드를 추가할 수 있습니다.

## <a name="add-a-timer-triggered-function"></a>타이머 트리거 함수 추가

1. 솔루션 탐색기에서 함수 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고, **추가** > **새 Azure 함수**를 선택합니다.

1. **Azure Functions** 템플릿을 선택하여 새 항목의 이름을 `DatabaseCleanup.cs`와 같이 지정하고, **추가**를 선택합니다.

1. **새 Azure 함수** 대화 상자에서 **타이머 트리거** 및 **확인**을 차례로 선택합니다. 이 대화 상자에서는 타이머 트리거 함수에 대한 코드 파일을 만듭니다.

1. 새 코드 파일을 열고, 파일 맨 위에 다음 using 문을 추가합니다.

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. 기존 `Run` 함수를 다음 코드로 바꿉니다.

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    이 함수는 15초마다 실행되어 운송 날짜를 기준으로 `Status` 열을 업데이트합니다. 타이머 트리거에 대한 자세한 정보는 [Azure Functions의 타이머 트리거](functions-bindings-timer.md)를 참조하세요.

1. **F5** 키를 눌러 함수 앱을 시작합니다. Visual Studio 뒤에 [Azure Functions Core Tools](functions-develop-local.md) 실행 창이 열립니다.

1. 시작 후 15초에 함수가 실행됩니다. 출력을 보고 **SalesOrderHeader** 테이블에서 업데이트된 행 수를 적어둡니다.

    ![함수 로그를 확인합니다.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    첫 번째 실행에서 32개 행의 데이터를 업데이트해야 합니다. 다음 실행은 데이터 행을 업데이트하지 않습니다. 따라서 SalesOrderHeader 테이블 데이터를 변경하지 않으면 `UPDATE` 문에 의해 더 많은 행이 선택됩니다.

[이 함수를 게시](functions-develop-vs.md#publish-to-azure)하려는 경우 `TimerTrigger` 특성을 15초 간격보다 적절한 [cron 일정](functions-bindings-timer.md#cron-expressions)으로 변경해야 합니다.

## <a name="next-steps"></a>다음 단계

다음으로, Logic Apps와 함께 함수를 사용하여 다른 서비스와 통합하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Logic Apps와 통합되는 함수 만들기](functions-twitter-email.md)

Functions에 대한 자세한 내용은 다음 문서를 참조하세요.

+ [Azure Functions 개발자 참조](functions-reference.md)  
   함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.
+ [Azure Functions 테스트](functions-test-a-function.md)  
  함수를 테스트하는 다양한 도구와 기법을 설명합니다.  
