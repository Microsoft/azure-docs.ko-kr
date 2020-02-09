---
title: SQL Server 또는 Azure SQL Database에 연결
description: Azure Logic Apps를 사용 하 여 온-프레미스 또는 클라우드에서 SQL database에 대한 작업 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789201"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 SQL Server 또는 Azure SQL Database에 대한 워크플로 자동화

이 문서에서는 SQL Server 커넥터가 있는 논리 앱 내에서 SQL 데이터베이스의 데이터에 액세스하는 방법에 대해 설명합니다. 이렇게 하면 논리 앱을 만들어 SQL 데이터 및 리소스를 관리 하는 작업, 프로세스 또는 워크플로를 자동화할 수 있습니다. SQL Server 커넥터는 [온-프레미스 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) 와 [클라우드 기반 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)모두에 대해 작동 합니다.

SQL 데이터베이스 또는 다른 시스템(예: Dynamics CRM Online)의 이벤트로 트리거될 때 실행할 수 있는 논리 앱을 만들 수 있습니다. 논리 앱은 SQL 쿼리 및 저장 프로시저 실행과 함께 데이터를 가져오고 삽입 하 고 삭제할 수도 있습니다. 예를 들어 Dynamics CRM Online에서 새 레코드를 자동으로 확인 하는 논리 앱을 빌드하고, 새 레코드의 SQL 데이터베이스에 항목을 추가한 다음, 추가 된 항목에 대한 전자 메일 경고를 보낼 수 있습니다.

논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다. 커넥터 관련 기술 정보, 제한 사항 및 알려진 문제에 대해서는 [SQL Server 커넥터 참조 페이지](https://docs.microsoft.com/connectors/sql/)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [SQL Server 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 또는 [Azure SQL 데이터베이스](../sql-database/sql-database-get-started-portal.md)

  작업을 호출할 때 논리 앱에서 결과를 반환할 수 있도록 테이블에 데이터가 있어야 합니다. Azure SQL Database를 만드는 경우 포함된 샘플 데이터베이스를 사용할 수 있습니다.

* SQL 서버 이름, 데이터베이스 이름, 사용자 이름 및 암호. 이러한 자격 증명은 SQL 서버에 액세스할 수 있는 권한을 논리에 부여하는 데 필요합니다.

  * SQL Server의 경우 연결 문자열에서 다음과 같은 세부 정보를 찾을 수 있습니다.

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Azure SQL Database의 경우 연결 문자열 또는 SQL Database 속성 아래의 Azure Portal에서 다음과 같은 세부 정보를 찾을 수 있습니다.

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 이러한 시나리오의 경우, 로컬 컴퓨터에 설치 된 [온-프레미스 데이터 게이트웨이와](../logic-apps/logic-apps-gateway-install.md) [Azure Portal에서 만든 Azure 데이터 게이트웨이 리소스가](../logic-apps/logic-apps-gateway-connection.md) 있습니다.

  * 논리 앱은 [ISE (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행 되지 않습니다.

  * 논리 *앱은* 통합 서비스 환경에서 실행 되지만 SQL Server 연결에는 Windows 인증을 사용 해야 합니다. 이 시나리오에서는 ISE 버전이 Windows 인증을 지원 하지 않기 때문에 데이터 게이트웨이와 함께 SQL Server 커넥터의 비 ISE 버전을 사용 합니다.

* SQL 데이터베이스에 액세스해야 하는 논리 앱. SQL 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 발생할 때마다 Logic Apps 엔진은 논리 앱 인스턴스를 만들고 논리 앱의 워크플로를 실행 하기 시작 합니다.

1. Azure Portal 또는 Visual Studio에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 이 예에서는 Azure Portal을 사용합니다.

1. 디자이너의 검색 상자에 "sql server"를 필터로 입력 합니다. 트리거 목록에서 원하는 SQL 트리거를 선택합니다.

   이 예제에서는 **항목을 만들 때** 트리거를 사용 합니다.

   !["항목을 만들 때" 트리거를 선택 합니다.](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 연결을 만들지 묻는 메시지가 표시 되 면 [이제 SQL 연결을 만듭니다](#create-connection). 연결이 존재 하는 경우 **테이블 이름을**선택 합니다.

   ![원하는 테이블을 선택 합니다.](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 논리 앱에서 테이블을 확인하는 빈도를 지정하는 **간격** 및 **빈도** 속성을 설정합니다.

   이 트리거는 선택한 테이블에서 행을 하나만 반환 합니다. 다른 작업을 수행 하려면 원하는 작업을 수행 하는 다른 작업을 추가 합니다. 예를 들어이 행의 데이터를 보려면 반환 된 행에서 필드를 포함 하는 파일을 만드는 다른 작업을 추가 하 고 전자 메일 알림을 보낼 수 있습니다. 이 커넥터에 대해 사용할 수 있는 다른 작업에 대해 알아보려면 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/sql/)를 참조 하세요.

1. 작업이 완료 되 면 디자이너 도구 모음에서 **저장**을 선택 합니다.

   이 단계에서는 Azure에서 사용할 수 있는 논리 앱을 자동으로 활성화하고 게시합니다.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 이 예에서는 논리 앱은 [되풀이 트리거](../connectors/connectors-native-recurrence.md)를 통해 시작하고 SQL 데이터베이스에서 행을 가져오는 작업을 호출합니다.

1. Azure Portal 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 사용합니다.

1. SQL 작업을 추가 하려는 트리거 또는 작업에서 **새 단계**를 선택 합니다.

   ![논리 앱에 새 단계 추가](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

1. **작업 선택**아래의 검색 상자에 "sql server"를 필터로 입력 합니다. 작업 목록에서 원하는 SQL 작업을 선택 합니다.

   이 예에서는 단일 레코드를 가져오는 **행 가져오기** 작업을 사용 합니다.

   ![SQL "행 가져오기" 작업을 찾아 선택 합니다.](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   이 작업을 수행 하면 선택한 테이블에서 한 행만 반환 되 고 다른 행은 반환 되지 않습니다. 이 행의 데이터를 보려면 반환 된 행에서 필드를 포함 하는 파일을 만드는 다른 작업을 추가 하 고 클라우드 저장소 계정에 해당 파일을 저장 합니다. 이 커넥터에 대해 사용할 수 있는 다른 작업에 대해 알아보려면 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/sql/)를 참조 하세요.

1. 연결을 만들지 묻는 메시지가 표시 되 면 [이제 SQL 연결을 만듭니다](#create-connection). 연결이 존재 하는 경우 **테이블 이름을**선택 하 고 원하는 레코드의 **행 ID** 를 입력 합니다.

   ![테이블 이름 및 행 ID 입력](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 작업이 완료 되 면 디자이너 도구 모음에서 **저장**을 선택 합니다.

   이 단계에서는 Azure에서 사용할 수 있는 논리 앱을 자동으로 활성화하고 게시합니다.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>데이터베이스 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>대량 데이터 처리

경우에 따라, 커넥터가 동시에 모든 결과를 반환 하지 않거나 결과 집합의 크기와 구조를 더 효율적으로 제어 하는 결과 집합으로 작업 해야 합니다. 이러한 많은 결과 집합을 처리할 수 있는 몇 가지 방법은 다음과 같습니다.

* 결과를 더 작은 집합으로 관리할 수 있도록 *페이지 매김을*켭니다. 자세한 내용은 [페이지 매김을 사용 하 여 대량 데이터, 레코드 및 항목 가져오기](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)를 참조 하세요.

* 원하는 방식으로 결과를 구성 하는 저장 프로시저를 만듭니다.

  여러 행을 가져오거나 삽입할 때 논리 앱은 이러한 [제한](../logic-apps/logic-apps-limits-and-config.md)내에서 [*until 루프*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 를 사용 하 여 이러한 행을 반복할 수 있습니다. 그러나 논리 앱에서 레코드 집합을 사용 하 여 대량으로 작업 해야 하는 경우 (예: 수천 또는 수백만 개의 행) 데이터베이스에 대한 호출로 인해 발생 하는 비용을 최소화 하려고 합니다.

  결과를 원하는 방식으로 구성 하기 위해 SQL 인스턴스에서 실행 되는 [*저장 프로시저*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 를 만들고 **select ORDER by** 문을 사용할 수 있습니다. 이 솔루션을 사용하면 결과의 크기와 구조를 더 많이 제어할 수 있습니다. 논리 앱은 SQL Server 커넥터의 **저장 프로시저 실행** 작업을 사용하여 저장 프로시저를 호출합니다.

  자세한 솔루션 정보는 다음 문서를 참조 하세요.

  * [Logic Apps를 사용한 대량 데이터 전송에 대한 SQL 페이지 매김](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 절](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

이 커넥터의 트리거, 작업 및 제한에 대한 기술 정보는 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/sql/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대한 다른 커넥터](../connectors/apis-list.md) 에 대해 알아보기
