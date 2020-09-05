---
title: 관리 id (미리 보기)를 사용 하 여 Azure SQL Database에 대 한 연결 설정
titleSuffix: Azure Cognitive Search
description: 관리 id (미리 보기)를 사용 하 여 Azure SQL Database에 대 한 인덱서 연결을 설정 하는 방법을 알아봅니다.
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 8dabf69af8628bb0b168bfea94af5333df341423
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924132"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity-preview"></a>관리 id (미리 보기)를 사용 하 여 Azure SQL Database에 대 한 인덱서 연결 설정

> [!IMPORTANT] 
> 관리 id를 사용 하 여 데이터 원본에 대 한 연결을 설정 하는 기능은 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다.

이 페이지에서는 데이터 원본 개체 연결 문자열에 자격 증명을 제공 하는 대신 관리 되는 id를 사용 하 여 Azure SQL Database에 대 한 인덱서 연결을 설정 하는 방법을 설명 합니다.

이 기능을 학습하기 전에 인덱서가 무엇인지와 데이터 원본에 대해 인덱서를 설정하는 방법을 이해하는 것이 좋습니다. 자세한 내용은 다음 링크에서 확인할 수 있습니다.

* [인덱서 개요](search-indexer-overview.md)
* [Azure SQL 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>관리 ID를 사용하여 연결 설정

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 시스템 할당 관리 ID 켜기

시스템 할당 관리 ID 사용이 설정되면 Azure는 동일한 테넌트 및 구독 내에서 다른 Azure 서비스에 인증하는 데 사용할 수 있는 검색 서비스 ID를 만듭니다. 그런 다음 인덱싱 중에 데이터 액세스를 허용하는 RBAC(역할 기반 액세스 제어) 할당에서 이 ID를 사용할 수 있습니다.

![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

**저장**을 선택한 후 검색 서비스에 할당된 개체 ID가 표시됩니다.

![개체 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "개체 ID입니다.")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - SQL Server의 Azure Active Directory 관리자 프로비저닝

다음 단계에서 데이터베이스에 연결하는 경우 데이터베이스에 대한 액세스 권한을 검색 서비스에 부여하기 위해 데이터베이스에 대한 관리자 액세스 권한이 있는 Azure AD(Active Directory) 계정에 연결해야 합니다.

[여기](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) 지침에 따라 Azure AD 계정 관리자에게 데이터베이스에 대한 액세스 권한을 부여합니다.

### <a name="3---assign-the-search-service-permissions"></a>3 - 검색 서비스 사용 권한 할당

아래 단계에 따라 검색 서비스에 데이터베이스를 읽을 수 있는 사용 권한을 할당합니다.

1. Visual Studio에 연결

    ![Visual Studio에 연결](./media/search-managed-identities/connect-with-visual-studio.png "Visual Studio에 연결")

2. Azure AD 계정을 사용하여 인증

    ![인증](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. 다음 명령을 실행합니다.

    검색 서비스 이름 주위에 대괄호를 포함합니다.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![새 쿼리](./media/search-managed-identities/visual-studio-new-query.png "새 쿼리")

    ![쿼리 실행](./media/search-managed-identities/visual-studio-execute-query.png "쿼리 실행")

>[!NOTE]
> 이 단계를 완료한 후 1단계의 검색 서비스 ID가 변경된 경우에는 역할 멤버 자격을 제거하고 SQL 데이터베이스에서 해당 사용자를 제거한 다음 3단계를 다시 수행하여 사용 권한을 다시 추가해야 합니다.
> 다음 명령을 실행하면 역할 멤버 자격 및 사용자를 제거할 수 있습니다.
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - 역할 할당 추가

이 단계에서는 Azure Cognitive Search 서비스에 SQL Server에서 데이터를 읽을 수 있는 사용 권한을 부여합니다.

1. Azure Portal에서 Azure SQL Server 페이지로 이동합니다.
2. **액세스 제어(IAM)** 를 선택합니다.
3. **추가**를 선택한 다음 **역할 할당 추가**를 선택합니다.

    ![역할 할당 추가](./media/search-managed-identities/add-role-assignment-sql-server.png "역할 할당 추가")

4. 적절한 **읽기 권한자** 역할을 선택합니다.
5. **액세스 할당**을 **Azure AD 사용자, 그룹 또는 서비스 사용자**로 둡니다.
6. 검색 서비스를 검색하고 선택한 다음 **저장**을 선택합니다.

    ![읽기 권한자 역할 할당 추가](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "읽기 권한자 역할 할당 추가")

### <a name="5---create-the-data-source"></a>5 - 데이터 원본 만들기

[REST API](/rest/api/searchservice/create-data-source), Azure Portal 및 [.net SDK](/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) 는 관리 되는 id 연결 문자열을 지원 합니다. 다음은 [REST API](/rest/api/searchservice/create-data-source) 및 관리 되는 id 연결 문자열을 사용 하 여 Azure SQL Database에서 데이터를 인덱싱하는 데이터 원본을 만드는 방법에 대 한 예입니다. 관리 되는 id 연결 문자열 형식은 REST API, .NET SDK 및 Azure Portal에 대해 동일 합니다.

[REST API](/rest/api/searchservice/create-data-source)를 사용 하 여 데이터 원본을 만들 때 데이터 원본에는 다음과 같은 필수 속성이 있어야 합니다.

* **name**은 검색 서비스 내 데이터 원본의 고유 이름입니다.
* **type**은 `azuresql`입니다.
* **credentials**
    * 관리 ID를 사용하여 인증하는 경우 **자격 증명** 형식이 관리 ID를 사용하지 않는 경우와 다릅니다. 여기서는 초기 카탈로그 또는 데이터베이스 이름 및 계정 키 또는 암호가 없는 ResourceID를 제공합니다. ResourceId는 Azure SQL Database 구독 ID, SQL Database 리소스 그룹 및 SQL 데이터베이스의 이름을 포함 해야 합니다. 
    * 관리 ID 연결 문자열 형식:
        * *Initial Catalog|Database=**database name**;ResourceId=/subscriptions/**구독 ID**/resourceGroups/**리소스 그룹 이름**/providers/Microsoft.Sql/servers/**SQL Server 이름**/;Connection Timeout=**connection timeout length**;*
* **container**에는 인덱싱할 테이블 또는 뷰의 이름을 지정합니다.

다음은 [REST API](/rest/api/searchservice/create-data-source)를 사용하여 Azure SQL 데이터 원본 개체를 만드는 방법의 예입니다.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 - 인덱스 만들기

인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

검색 가능한 `booktitle` 필드를 사용하여 인덱스를 만드는 방법은 다음과 같습니다.   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

인덱스 만들기에 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="7---create-the-indexer"></a>7 - 인덱서 만들기

인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

Azure SQL 인덱서에 대한 인덱서 정의 예:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서를 예약하는 방법](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

인덱서가 클라이언트에서 서버에 액세스할 수 없다고 표시하는 데이터 원본에 연결하려고 할 때 오류가 발생하는 경우 [일반적인 인덱서 오류](./search-indexer-troubleshooting.md)를 살펴봅니다.

## <a name="see-also"></a>참고 항목

Azure SQL 인덱서에 대해 자세히 알아봅니다.
* [Azure SQL 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)