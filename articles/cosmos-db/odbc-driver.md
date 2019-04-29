---
title: BI 분석 도구를 사용하여 Azure Cosmos DB에 연결
description: BI 및 데이터 분석 소프트웨어에서 정규화된 데이터를 볼 수 있도록 Azure Cosmos DB ODBC 드라이버를 사용하여 테이블 및 뷰를 만드는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: a4cf3a9c19497057d7799be3bec78ad0de97e71a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932113"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>ODBC 드라이버와 함께 BI 분석 도구를 사용하여 Azure Cosmos DB에 연결

Azure Cosmos DB ODBC 드라이버를 사용하면 SQL Server Integration Services, Power BI Desktop 및 Tableau와 같은 BI 분석 도구를 사용하여 Azure Cosmos DB에 연결할 수 있으므로 해당 솔루션에서 Azure Cosmos DB 데이터 시각화를 분석하고 만들 수 있습니다.

Azure Cosmos DB ODBC 드라이버는 ODBC 3.8 규격으로, ANSI SQL-92 구문을 지원합니다. 이 드라이버는 Azure Cosmos DB에서 데이터를 다시 정규화하는 데 도움이 되는 다양한 기능을 제공합니다. 이 드라이버를 사용하여 Azure Cosmos DB의 데이터를 테이블 및 뷰로 나타낼 수 있습니다. 이 드라이버를 사용하면 테이블 및 뷰에 대해 쿼리별 그룹화, 삽입, 업데이트 및 삭제 등의 SQL 작업을 수행할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB와 ODBC 드라이버 연결은 현재 Azure Cosmos DB SQL API 계정에서만 지원됩니다.

## <a name="why-do-i-need-to-normalize-my-data"></a>데이터를 정규화해야 하는 이유는 무엇인가요?
Azure Cosmos DB는 엄격한 스키마로 국한하지 않으면서 신속한 애플리케이션 개발과 데이터 모델 반복 기능을 지원하는 스키마 없는 데이터베이스입니다. 단일 Azure Cosmos DB 데이터베이스에 다양한 구조의 JSON 문서가 포함될 수 있습니다. 이러한 특징은 신속한 애플리케이션 개발에 유용하지만 데이터 분석 및 BI 도구를 사용하여 데이터 보고서를 분석하고 만들려는 경우 데이터를 평면화하고 특정 스키마를 따르도록 할 필요가 있습니다.

바로 이러한 경우 ODBC 드라이버가 사용됩니다. 이제 ODBC 드라이버를 사용하여 Azure Cosmos DB의 데이터를 데이터 분석 및 보고 요구에 맞는 테이블 및 뷰로 다시 정규화할 수 있습니다. 다시 정규화된 스키마는 기본 데이터에 아무런 영향도 미치지 않고 개발자도 이러한 스키마를 준수할 필요가 없습니다. 대신, 사용자는 ODBC 호환 도구를 사용하여 데이터에 액세스할 수 있습니다. 이제 Azure Cosmos DB 데이터베이스는 개발 팀에서 선호될 뿐 아니라 데이터 분석에서도 많이 사용될 것입니다.

ODBC 드라이버를 살펴보겠습니다.

## <a id="install"></a>1단계: Azure Cosmos DB ODBC 드라이버 설치

1. 다음 중에서 환경에 맞는 드라이버를 다운로드합니다.

    | 설치 관리자 | 지원되는 운영 체제| 
    |---|---| 
    |Windows 64비트용 [Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64)| 64비트 버전의 Windows 8.1 이상, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 및 Windows Server 2008 R2.| 
    |Windows 32비트 또는 64비트용 [Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64)| 64비트 버전의 Windows 8.1 이상, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 및 Windows Server 2003| 
    |Windows 32비트용 [Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32)|32비트 버전의 Windows 8.1 이상, Windows 8, Windows 7, Windows XP 및 Windows Vista|

    msi 파일을 로컬로 실행합니다. 그러면 **Microsoft Azure Cosmos DB ODBC 드라이버 설치 마법사**가 시작됩니다. 

1. 기본 입력으로 ODBC 드라이버를 설치하여 설치 마법사를 완료합니다.

1. 컴퓨터에서 **ODBC 데이터 원본 관리자** 앱을 엽니다. Windows 검색 상자에 **ODBC 데이터 원본**을 입력하여 이 작업을 수행할 수 있습니다. 
    **드라이버** 탭을 클릭하고 **Microsoft Azure Cosmos DB ODBC 드라이버**가 표시되는지 확인하여 이 드라이버가 설치되어 있는지 확인할 수 있습니다.

    ![Azure Cosmos DB ODBC 데이터 원본 관리자](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>2단계: Azure Cosmos DB 데이터베이스에 연결

1. [Azure Cosmos DB ODBC 드라이버를 설치](#install)한 후 **ODBC 데이터 원본 관리자** 창에서 **추가**를 클릭합니다. 사용자 또는 시스템 DSN을 만들 수 있습니다. 이 예제에서는 사용자 DSN을 만듭니다.

1. **새 데이터 원본 만들기** 창에서 **Microsoft Azure Cosmos DB ODBC 드라이버**를 선택하고 **마침**을 클릭합니다.

1. **Azure Cosmos DB ODBC 드라이버 SDN 설정** 창에서 다음 정보를 입력합니다. 

    ![Azure Cosmos DB ODBC 드라이버 DSN 설정 창](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **데이터 원본 이름**: ODBC DSN에 대한 고유한 이름입니다 이 이름은 Azure Cosmos DB 계정에 고유하므로 여러 계정이 있는 경우 적절히 이름을 지정합니다.
    - **설명**: 데이터 원본에 대한 짧은 설명입니다.
    - **호스트**: Azure Cosmos DB 계정에 대한 URI입니다. 다음 스크린샷처럼 Azure Portal의 Azure Cosmos DB 키 페이지에서 이 URI를 검색할 수 있습니다. 
    - **액세스 키**: 다음 스크린샷처럼 Azure Portal의 Azure Cosmos DB 키 페이지에 있는 기본 또는 보조 읽기-쓰기/읽기 전용 키입니다. DSN가 읽기 전용 데이터 처리 및 보고에 사용되는 경우 읽기 전용 키를 사용하는 것이 좋습니다.
    ![Azure Cosmos DB 키 페이지](./media/odbc-driver/odbc-driver-keys.png)
    - **액세스 키 암호화**: 이 컴퓨터의 사용자 수에 따라 가장 적합한 옵션을 선택합니다. 
    
1. Azure Cosmos DB 계정에 연결할 수 있는지 확인하려면 **테스트** 단추를 클릭합니다. 

1. **고급 옵션**을 클릭하고 다음 값을 설정합니다.
    - **쿼리 일관성**: 작업에 대해 [일관성 수준](consistency-levels.md)을 선택합니다. 기본값은 세션입니다.
    - **재시도 횟수**: 초기 요청이 서비스 속도 제한으로 인해 완료되지 않은 경우 작업을 다시 시도할 횟수를 입력합니다.
    - **스키마 파일**: 다양한 옵션이 있습니다.
        - 기본적으로 이 항목을 있는 그대로(비어 있음) 두면 드라이버는 첫 번째 페이지 데이터에서 모든 컬렉션을 검색하여 각 컬렉션의 스키마를 확인합니다. 이 작업을 컬렉션 매핑이라고 합니다. 정의된 스키마 파일이 없이 경우 이 드라이버는 각 드라이버 세션을 검색하므로, DSN을 사용하는 애플리케이션의 시작 시간이 더 늘어날 수 있습니다. 따라서 DSN에 대한 스키마 파일을 항상 연결하는 것이 좋습니다.
        - 스키마 파일이 이미 있는 경우(스키마 편집기를 사용하여 만들었을 수 있음) **찾아보기**를 클릭하고 해당 파일로 이동한 후 **저장**을 클릭하고 **확인**을 클릭합니다.
        - 새 스키마를 만들려면 **확인**을 클릭하고 주 창에서 **스키마 편집기**를 클릭합니다. 그런 다음, 스키마 편집기 정보로 이동합니다. 새 스키마 파일을 만든 후 **고급 옵션** 창으로 돌아가 새로 만든 스키마 파일을 포함해야 합니다.

1. 작업을 완료하고 **Azure Cosmos DB ODBC 드라이버 DSN 설정** 창을 닫으면 새 사용자 DSN이 사용자 DSN 탭에 추가됩니다.

    ![사용자 DSN 탭의 새 Azure Cosmos DB ODBC DSN](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>3단계: 컬렉션 매핑 방법을 사용하여 스키마 정의 만들기

**컬렉션 매핑** 또는 **테이블 구분 기호**의 두 가지 유형의 샘플링 방법을 사용할 수 있습니다. 샘플링 세션은 두 가지 샘플링 방법을 모두 사용할 수 있지만 각 컬렉션은 특정 샘플링 방법만 사용할 수 있습니다. 아래 단계는 컬렉션 매핑 방법을 사용하여 하나 이상의 컬렉션에서 데이터에 대한 스키마를 만듭니다. 이 샘플링 방법은 컬렉션 페이지에서 데이터를 검색하여 데이터의 구조를 확인합니다. 그런 후 컬렉션을 ODBC 쪽의 테이블로 바꿉니다. 이 샘플링 방법은 컬렉션의 데이터가 동일한 형식일 때 효율적이고 빠릅니다. 컬렉션에 형식이 다른 데이터가 포함되어 있을 때는 [테이블 구분 기호 매핑 방법](#table-mapping)을 사용하는 것이 좋습니다. 이 방법이 컬렉션의 데이터 구조를 확인하는 보다 강력한 샘플링 방법을 제공하기 때문입니다. 

1. [Azure Cosmos DB 데이터베이스에 연결](#connect)의 1-4단계를 완료한 후 **Azure Cosmos DB ODBC 드라이버 DSN 설정** 창에서 **스키마 편집기**를 클릭합니다.

    ![Azure Cosmos DB ODBC 드라이버 DSN 설정 창의 스키마 편집기 단추](./media/odbc-driver/odbc-driver-schema-editor.png)
1. **스키마 편집기** 창에서 **새로 만들기**를 클릭합니다.
    **스키마 생성** 창에 Azure Cosmos DB 계정의 모든 컬렉션이 표시됩니다. 

1. 샘플링할 하나 이상의 컬렉션을 선택하고 **샘플**을 클릭합니다. 

1. **디자인 보기** 탭에 데이터베이스, 스키마 및 테이블이 표시됩니다. 테이블 보기에서 검색을 수행하면 열 이름(예: SQL 이름, 원본 이름 등)과 관련된 속성 집합이 표시됩니다.
    각 열에 대해 SQL 열 이름, SQL 형식, SQL 길이(해당되는 경우), 소수 자릿수(해당되는 경우), 전체 자릿수(해당되는 경우) 및 null 허용을 수정할 수 있습니다.
    - 쿼리 결과에서 해당 열을 제외하려면 **Hide Column**을 **true**로 설정합니다. Hide Column = true로 표시된 열은 스키마의 일부이지만 선택 및 프로젝션의 경우에는 반환되지 않습니다. 예를 들어 “_”로 시작하는 모든 Azure Cosmos DB 시스템 필수 속성을 숨길 수 있습니다.
    - **id** 열은 정규화된 스키마에서 기본 키로 사용되기 때문에 숨길 수 없는 유일한 필드입니다. 

1. 스키마 정의를 끝낸 후에 **파일** | **저장**을 클릭한 후 스키마를 저장할 디렉터리로 이동하고 **저장**을 클릭합니다.

1. DSN을 사용 하 여이 스키마를 사용 하려면 엽니다는 **Azure Cosmos DB ODBC 드라이버 DSN 설정 창** (통해 ODBC 데이터 원본 관리자 사용)를 클릭 **고급 옵션**를 선택한 다음는 **스키마 파일** 상자에서 저장 된 스키마로 이동 합니다. 스키마 파일을 기존 DSN에 저장하면 해당 스키마로 정의된 데이터 및 구조로 범위가 지정되도록 DSN 연결이 수정됩니다.

## <a id="table-mapping"></a>4단계: 테이블 구분 기호 매핑 방법을 사용하여 스키마 정의 만들기

**컬렉션 매핑** 또는 **테이블 구분 기호**의 두 가지 유형의 샘플링 방법을 사용할 수 있습니다. 샘플링 세션은 두 가지 샘플링 방법을 모두 사용할 수 있지만 각 컬렉션은 특정 샘플링 방법만 사용할 수 있습니다. 

다음 단계는 **테이블 구분 기호** 매핑 방법을 사용하여 하나 이상의 컬렉션에 있는 데이터에 대한 스키마를 만듭니다. 컬렉션에 형식이 다른 데이터가 포함되어 있으면 이 샘플링 방법을 사용하는 것이 좋습니다. 이 방법을 사용하여 샘플링 범위를 특성 집합 및 해당 값으로 지정할 수 있습니다. 예를 들어 문서에 “Type” 속성이 포함된 경우 샘플링 범위를 이 속성의 값으로 지정할 수 있습니다. 샘플링의 최종 결과는 지정한 Type의 각 값에 대한 테이블 집합입니다. 예를 들어, Type = Car를 지정하면 Car 테이블이 생성되지만 Type = Plane을 지정하면 Plane 테이블이 생성됩니다.

1. [Azure Cosmos DB 데이터베이스에 연결](#connect)의 1-4단계를 완료한 후 Azure Cosmos DB ODBC 드라이버 DSN 설정 창에서 **스키마 편집기**를 클릭합니다.

1. **스키마 편집기** 창에서 **새로 만들기**를 클릭합니다.
    **스키마 생성** 창에 Azure Cosmos DB 계정의 모든 컬렉션이 표시됩니다. 

1. **샘플 보기** 탭에서 컬렉션을 선택하고 컬렉션에 대한 **매핑 정의** 열에서 **편집**을 클릭합니다. 그런 다음 **매핑 정의** 창에서 **테이블 구분 기호** 방법을 선택합니다. 그런 다음 아래 작업을 수행합니다.

    a. **특성** 상자에 구분 기호 속성의 이름을 입력합니다. 이것은 문서에서 샘플링 범위로 지정하려는 속성(예: City)입니다. 그런 후 Enter 키를 누릅니다. 

    b. 위에서 입력한 특성에 대한 특정 값으로 샘플링 범위를 지정하려는 경우 선택 상자에서 해당 특성을 선택하고 **값** 상자에 값(예: Seattle)을 입력하고 Enter 키를 누릅니다. 특성에 대해 여러 값을 계속 추가할 수 있습니다. 값을 입력할 때 올바른 속성이 선택되어 있는지 확인하기만 하면 됩니다.

    예를 들어 **특성** 값으로 City를 포함하고 city 값이 New York과 Dubai인 행만 포함하도록 테이블을 제한하려면 특성 상자에 City를 입력하고 **값** 상자에 New York을 입력한 후 Dubai를 입력합니다.

1. **확인**을 클릭합니다. 

1. 샘플링하려는 컬렉션에 대한 매핑 정의를 완료한 후 **스키마 편집기** 창에서 **샘플**을 클릭합니다.
     각 열에 대해 SQL 열 이름, SQL 형식, SQL 길이(해당되는 경우), 소수 자릿수(해당되는 경우), 전체 자릿수(해당되는 경우) 및 null 허용을 수정할 수 있습니다.
    - 쿼리 결과에서 해당 열을 제외하려면 **Hide Column**을 **true**로 설정합니다. Hide Column = true로 표시된 열은 스키마의 일부이지만 선택 및 프로젝션의 경우에는 반환되지 않습니다. 예를 들어 `_`로 시작하는 모든 Azure Cosmos DB 시스템 필수 속성을 숨길 수 있습니다.
    - **id** 열은 정규화된 스키마에서 기본 키로 사용되기 때문에 숨길 수 없는 유일한 필드입니다. 

1. 스키마 정의를 끝낸 후에 **파일** | **저장**을 클릭한 후 스키마를 저장할 디렉터리로 이동하고 **저장**을 클릭합니다.

1. **Azure Cosmos DB ODBC 드라이버 DSN 설정** 창으로 돌아가 **고급 옵션**을 클릭합니다. 그런 다음 **스키마 파일** 상자에서 저장된 스키마 파일로 이동한 후 **확인**을 클릭합니다. **확인**을 다시 클릭하여 DSN을 저장합니다. 이렇게 하면 만든 스키마가 DSN에 저장됩니다. 

## <a name="optional-set-up-linked-server-connection"></a>(선택 사항) 연결된 서버 연결 설정

연결된 서버 연결을 설정하여 SSMS(SQL Server Management Studio)에서 Azure Cosmos DB를 쿼리할 수 있습니다.

1. 예를 들어 `SDS Name`으로 명명된 [2단계](#connect)에서 설명된 대로 시스템 데이터 원본을 만듭니다.

1. [SQL Server Management Studio를 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)하고 서버에 연결합니다. 

1. SSMS 쿼리 편집기에서 다음 명령을 사용하여 데이터 원본에 대한 연결된 서버 개체 `DEMOCOSMOS`을 만듭니다. `DEMOCOSMOS`을 연결된 서버의 이름으로, `SDS Name`는 시스템 데이터 원본 이름으로 바꿉니다.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
새 연결된 서버 이름을 보려면 연결된 서버 목록을 새로 고칩니다.

![SSMS의 연결된 서버](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>연결된 데이터베이스 쿼리

연결된 데이터베이스를 쿼리하려면 SSMS 쿼리를 입력합니다. 이 예제에서는 쿼리가 `customers`이라 명명된 컬렉션의 테이블에서 선택합니다.

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

쿼리를 실행합니다. 결과는 다음과 비슷합니다.

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> 연결된 Cosmos DB 서버는 네 부분으로 된 이름 지정을 지원하지 않습니다. 오류는 다음 메시지와 비슷하게 반환됩니다.

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(선택 사항) 뷰 만들기
샘플링 프로세스의 일부로 뷰를 정의하고 만들 수 있습니다. 이러한 뷰는 SQL 뷰와 비슷합니다. 읽기 전용이며, 정의된 Azure Cosmos DB SQL 쿼리의 선택 및 프로젝션으로 범위가 지정됩니다. 

데이터에 대한 뷰를 만들려면 **스키마 편집기** 창의 **뷰 정의** 열에서 샘플링할 컬렉션 행에 대해 **추가**를 클릭합니다. 
    ![데이터 뷰 만들기](./media/odbc-driver/odbc-driver-create-view.png)


그런 다음 **뷰 정의** 창에서 다음을 수행합니다.

1. **새로 만들기**를 클릭하고 뷰 이름(예: EmployeesfromSeattleView)을 입력하고 **확인**을 클릭합니다.

1. **뷰 편집** 창에서 Azure Cosmos DB 쿼리를 입력합니다. [Azure Cosmos DB SQL 쿼리](how-to-sql-query.md)(예: `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`)여야 합니다. 그런 후 **확인**을 클릭합니다.

    ![뷰를 만들 때 쿼리 추가](./media/odbc-driver/odbc-driver-create-view-2.png)


원하는 수만큼 뷰를 만들 수 있습니다. 뷰 정의가 끝나면 데이터를 샘플링할 수 있습니다. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5단계: Power BI Desktop과 같은 BI 도구에서 데이터 보기

새 DSN을 사용하여 ODBC 호환 도구를 통해 Azure Cosmos DB에 연결할 수 있습니다. 이 단계에서는 Power BI Desktop에 연결하고 Power BI 시각화를 만드는 방법을 보여 줍니다.

1. Power BI Desktop을 엽니다.

1. **데이터 가져오기**를 클릭합니다.

    ![Power BI Desktop에서 데이터 가져오기](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. **데이터 가져오기** 창에서 **기타** | **ODBC** | **연결**을 클릭합니다.

    ![Power BI 데이터 가져오기에서 ODBC 데이터 원본 선택](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. **ODBC에서** 창에서 만든 데이터 원본 이름을 선택하고 **확인**을 클릭합니다. **고급 옵션** 항목은 빈 상태로 두어도 됩니다.

    ![Power BI 데이터 가져오기에서 DSN(데이터 원본 이름) 선택](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. **ODBC 드라이버를 사용하여 데이터 원본에 액세스합니다.** 창에서 **기본 또는 사용자 지정**을 클릭하고 **연결**을 클릭합니다. **자격 증명 연결 문자열 속성**은 포함하지 않아도 됩니다.

1. **탐색 창**의 왼쪽 분할 창에서 데이터베이스, 스키마를 확장한 다음 테이블을 선택합니다. 결과 분할 창에는 만든 스키마를 사용하여 해당 데이터가 포함됩니다.

    ![Power BI 데이터 가져오기에서 테이블 선택](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Power BI desktop의 데이터를 시각화하려면 테이블 이름 앞의 확인란을 선택하고 **로드**를 클릭합니다.

1. Power BI Desktop의 맨 왼쪽에 있는 데이터 탭을 선택하여 ![Power BI Desktop의 데이터 탭](./media/odbc-driver/odbc-driver-data-tab.png) 데이터를 가져왔는지 확인합니다.

1. 이제 보고서 탭 ![Power BI Desktop의 보고서 탭](./media/odbc-driver/odbc-driver-report-tab.png)을 클릭한 다음 **새 시각적 개체**를 클릭하고 타일을 사용자 지정함으로써 Power BI를 통해 시각적 개체를 만들 수 있습니다. Power BI Desktop에서 시각화를 만드는 방법에 대한 자세한 내용은 [Power BI의 시각화 유형](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

다음 오류가 표시되면 [2단계](#connect)에서 Azure Portal로부터 복사한 **호스트** 및 **액세스 키** 값이 올바른지 확인하고 다시 시도하세요. Azure Portal의 **호스트** 및 **액세스 키** 값 오른쪽에 있는 복사 단추를 사용하여 올바른 값을 복사합니다.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 시작](introduction.md)을 참조하세요.
