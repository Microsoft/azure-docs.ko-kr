---
title: SQL 데이터 동기화 설정
description: 이 자습서에서는 Azure SQL 데이터 동기화 설정 방법을 보여줍니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 75de7b122bff75ea13e3b66bb0b79452142dc36c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500093"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>자습서: Azure SQL Database의 데이터베이스와 SQL Server 간에 SQL 데이터 동기화 설정
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 자습서에서는 Azure SQL Database와 SQL Server 인스턴스를 모두 포함하는 동기화 그룹을 만들어 SQL 데이터 동기화를 설정하는 방법에 대해 설명합니다. 동기화 그룹은 사용자 지정 방식으로 구성되며 사용자가 설정하는 일정에 따라 동기화됩니다.

자습서에서는 사용자가 이전에 SQL 데이터베이스 및 SQL Server를 어느 정도 사용해 보았다고 가정합니다.

SQL 데이터 동기화의 개요는 [SQL 데이터 동기화를 사용하여 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

SQL 데이터 동기화를 구성하는 방법을 보여 주는 PowerShell 예제는 [SQL Database의 데이터베이스를 동기화하는 방법](scripts/sql-data-sync-sync-data-between-sql-databases.md) 또는 [Azure SQL Database와 SQL Server의 데이터베이스를 동기화하는 방법](scripts/sql-data-sync-sync-data-between-azure-onprem.md)을 참조하세요.

> [!IMPORTANT]
> SQL 데이터 동기화는 현재 Azure SQL Managed Instance를 지원하지 **않습니다**.

## <a name="create-sync-group"></a>동기화 그룹 만들기

1. [Azure Portal](https://portal.azure.com)으로 이동하여 SQL Database의 데이터베이스를 찾습니다. **SQL 데이터베이스** 를 검색하고 선택합니다.

    ![데이터베이스 검색, Microsoft Azure Portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. 데이터 동기화를 위해 허브 데이터베이스로 사용할 데이터베이스를 선택합니다.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > 허브 데이터베이스는 동기화 그룹에 여러 데이터베이스 엔드포인트가 있는 동기화 토폴로지의 중앙 엔드포인트입니다. 동기화 그룹에서 엔드포인트를 포함하는 기타 모든 멤버 데이터베이스는 허브 데이터베이스와 동기화됩니다.

1. 선택한 데이터베이스의 **SQL 데이터베이스** 메뉴에서 **다른 데이터베이스와 동기화** 를 선택합니다.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. **다른 데이터베이스와 동기화** 페이지에서 **새 동기화 그룹** 을 선택합니다. **동기화 그룹 만들기(1단계)** 에서 **새 동기화 그룹** 페이지가 열립니다.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   **데이터 동기화 그룹 만들기** 페이지에서 다음 설정을 변경합니다.

   | 설정                        | 설명 |
   | ------------------------------ | ------------------------------------------------- |
   | **동기화 그룹 이름** | 새 동기화 그룹의 이름을 입력합니다. 이 이름은 데이터베이스 자체의 이름과 구분됩니다. |
   | **메타데이터 데이터베이스 동기화** | 데이터베이스를 만들지(권장) 아니면 기존 데이터베이스를 사용할지를 선택합니다.<br/><br/>**새 데이터베이스** 를 선택하는 경우 **새 데이터베이스 만들기** 를 선택합니다. **SQL 데이터베이스** 페이지에서 새 데이터베이스의 이름을 지정하고 데이터베이스를 구성한 다음 **확인** 을 선택합니다.<br/><br/>**기존 데이터베이스 사용** 을 선택하는 경우 목록에서 데이터베이스를 선택합니다. |
   | **자동 동기화** | **설정** 또는 **해제** 를 선택합니다.<br/><br/>**설정** 을 선택하는 경우 **동기화 빈도** 섹션에서 숫자를 입력하고 **초**, **분**, **시간** 또는 **일** 을 선택합니다.<br/> 첫 번째 동기화는 구성이 저장된 시간부터 선택한 간격 기간이 경과한 후에 시작됩니다.|
   | **충돌 해결** | **허브 획득** 또는 **구성원 획득** 을 선택합니다.<br/><br/>**허브 획득** 을 선택하면 충돌이 발생할 경우 허브 데이터베이스의 데이터가 멤버 데이터베이스에서 충돌하는 데이터를 덮어씁니다.<br/><br/>**구성원 획득 획득** 을 선택하면 충돌이 발생할 경우 구성원 데이터베이스의 데이터가 허브 데이터베이스에서 충돌하는 데이터를 덮어씁니다. |
   | **프라이빗 링크 사용** | 서비스 관리형 프라이빗 엔드포인트를 선택하여 동기화 서비스와 허브 데이터베이스 간에 보안 연결을 설정합니다. |

   > [!NOTE]
   > **동기화 메타데이터 데이터베이스** 로 사용할 비어 있는 새 데이터베이스를 만드는 것이 좋습니다. 데이터 동기화는 이 데이터베이스에서 테이블을 만들고 자주 실행되는 워크로드를 실행합니다. 이 데이터베이스는 선택한 지역 및 구독에서 모든 동기화 그룹의 **동기화 메타데이터 데이터베이스** 로 공유됩니다. 지역의 동기화 그룹 및 동기화 에이전트를 모두 제거해야만 데이터베이스 또는 이름을 변경할 수 있습니다. 또한 탄력적 작업 데이터베이스를 SQL 데이터 동기화 메타데이터 데이터베이스로 사용할 수 없으며 그 반대의 경우도 마찬가지입니다.  

   **확인** 을 선택하고 동기화 그룹이 생성되어 배포될 때까지 기다립니다.
   
1. **새 동기화 그룹** 페이지에서 **프라이빗 링크 사용** 을 선택한 경우 프라이빗 엔드포인트 연결을 승인해야 합니다. 정보 메시지의 링크를 통해 연결을 승인할 수 있는 프라이빗 엔드포인트 연결 환경으로 이동합니다. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>동기화 구성원 추가

새 동기화 그룹이 생성 및 배포되고 나면 **동기화 구성원 추가(2단계)** 가 **새 동기화 그룹** 페이지에서 강조 표시됩니다.

**허브 데이터베이스** 섹션에서 허브 데이터베이스가 있는 서버의 기존 자격 증명을 입력합니다. 이 섹션에서 *새* 자격 증명을 입력하지 않습니다.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Azure SQL Database의 데이터베이스를 추가하려면 다음을 수행합니다.

**구성원 데이터베이스** 섹션에서 필요에 따라 **Azure SQL Database 추가** 를 선택하여 Azure SQL Database의 데이터베이스를 동기화 그룹에 추가합니다. **Azure SQL Database 구성** 페이지가 열립니다.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  **Azure SQL Database 구성** 페이지에서 다음 설정을 변경합니다.

  | 설정                       | 설명 |
  | ----------------------------- | ------------------------------------------------- |
  | **동기화 구성원 이름** | 새 동기화 구성원의 이름을 입력합니다. 이 이름은 데이터베이스 자체의 이름과는 달라야 합니다. |
  | **구독** | 대금 청구용으로 연결된 Azure 구독을 선택합니다. |
  | **Azure SQL Server** | 기존 서버를 선택합니다. |
  | **Azure SQL Database** | SQL Database의 기존 데이터베이스를 선택합니다. |
  | **동기화 방향** | **양방향 동기화**, **허브 수신** 또는 **허브 발신** 을 선택합니다. |
  | **사용자 이름** 및 **암호** | 구성원 데이터베이스가 있는 서버의 기존 자격 증명을 입력합니다. 이 섹션에서 *새* 자격 증명을 입력하지 않습니다. |
  | **프라이빗 링크 사용** | 서비스 관리형 프라이빗 엔드포인트를 선택하여 동기화 서비스와 멤버 데이터베이스 간에 보안 연결을 설정합니다. |

  **확인** 을 선택하고 새 동기화 구성원을 만들고 배포할 때까지 기다립니다.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>SQL Server 데이터베이스를 추가하려면 다음을 수행합니다.

**구성원 데이터베이스** 섹션에서 필요에 따라 **온-프레미스 데이터베이스 추가** 를 선택하여 SQL Server 데이터베이스를 동기화 그룹에 추가합니다. 다음 작업을 수행할 수 있는 **온-프레미스 구성** 페이지가 열립니다.

1. **동기화 에이전트 게이트웨이 선택** 을 선택합니다. **동기화 에이전트 선택** 페이지가 열립니다.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. **동기화 에이전트 선택** 페이지에서 기존 에이전트를 사용할지 아니면 에이전트를 만들지를 선택합니다.

   **기존 에이전트** 를 선택하는 경우 목록에서 기존 에이전트를 선택합니다.

   **새 에이전트 만들기** 를 선택하는 경우 다음 작업을 수행합니다.

   1. 제공된 링크에서 데이터 동기화 에이전트를 다운로드하여 SQL Server가 있는 컴퓨터에 설치합니다. [Azure SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693)에서 에이전트를 직접 다운로드할 수도 있습니다.

      > [!IMPORTANT]
      > 클라이언트 에이전트가 서버와 통신할 수 있도록 방화벽에서 아웃바운드 TCP 포트 1433을 열어야 합니다.

   1. 에이전트의 이름을 입력합니다.

   1. **키 만들기 및 생성** 을 선택하고 에이전트 키를 클립보드에 복사합니다.

   1. **확인** 을 선택하여 **동기화 에이전트 선택** 페이지를 닫습니다.

1. SQL Server 컴퓨터에서 클라이언트 동기화 에이전트 앱을 찾아 실행합니다.

   ![데이터 동기화 클라이언트 에이전트 앱](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. 동기화 에이전트 앱에서 **에이전트 키 전송** 을 선택합니다. **동기화 메타데이터 데이터베이스 구성** 대화 상자가 열립니다.

    1. **동기화 메타데이터 데이터베이스 구성** 대화 상자에 Azure Portal에서 복사된 에이전트 키를 붙여 넣습니다. 또한 메타데이터 데이터베이스가 있는 서버의 기존 자격 증명을 입력합니다. 메타데이터 데이터베이스를 만든 경우 이 데이터베이스는 허브 데이터베이스와 같은 서버에 저장됩니다. **확인** 을 선택하고 구성이 완료되기를 기다립니다.

        ![에이전트 키 및 서버 자격 증명을 입력합니다.](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > 방화벽 오류가 발생하면 SQL Server 컴퓨터에서 들어오는 트래픽을 허용하도록 Azure에서 방화벽 규칙을 만듭니다. SSM(SSQL Server Management Studio) 또는 포털에서 규칙을 수동으로 만들 수 있습니다. SSMS에서 <hub_database_name>.database.windows.net으로 이름을 입력하여 Azure의 허브 데이터베이스에 연결합니다.

    1. **등록** 을 선택하여 에이전트에 SQL Server 데이터베이스를 등록합니다. **SQL Server 구성** 대화 상자가 열립니다.

        ![SQL Server 데이터베이스를 추가하고 구성합니다.](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. **SQL Server 구성** 대화 상자에서 연결에 SQL Server 인증을 사용할지 아니면 Windows 인증을 사용할지를 선택합니다. SQL Server 인증을 선택하는 경우 기존 자격 증명을 입력합니다. 동기화할 SQL Server 이름 및 데이터베이스의 이름을 입력하고 **연결 테스트** 를 선택하여 설정을 테스트합니다. 그런 다음 **저장** 을 선택하면 등록된 데이터베이스가 목록에 나타납니다.

        ![SQL Server 데이터베이스를 지금 등록합니다.](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. 클라이언트 동기화 에이전트 앱을 닫습니다.

1. 포털의 **온-프레미스 구성** 페이지에서 **데이터베이스 선택** 을 선택합니다.

1. **데이터베이스 선택** 페이지의 **동기화 구성원 이름** 필드에서 새 동기화 구성원의 이름을 입력합니다. 이 이름은 데이터베이스 자체의 이름과 구분됩니다. 목록에서 데이터베이스를 선택합니다. **동기화 방향** 필드에서 **양방향 동기화**, **허브 수신** 또는 **허브 발신** 을 선택합니다.

    ![온-프레미스 데이터베이스 선택](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. **확인** 을 선택하여 **데이터베이스 선택** 페이지를 닫습니다. 그런 다음 **확인** 을 선택하여 **온-프레미스 구성** 페이지를 닫고 새 동기화 구성원이 만들어지고 배포될 때까지 기다립니다. 마지막으로 **확인** 을 선택하여 **동기화 구성원 선택** 페이지를 닫습니다.

> [!NOTE]
> SQL 데이터 동기화 및 로컬 에이전트에 연결하려면 역할 *DataSync_Executor* 에 사용자 이름을 추가합니다. 데이터 동기화는 SQL Server 인스턴스에서 이 역할을 만듭니다.

## <a name="configure-sync-group"></a>동기화 그룹 구성

새 동기화 그룹 구성원이 생성 및 배포되고 나면 **동기화 그룹 구성(3단계)** 이 **새 동기화 그룹** 페이지에서 강조 표시됩니다.

![3단계 설정](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. **테이블** 페이지의 동기화 그룹 구성원 목록에서 데이터베이스를 선택한 다음 **스키마 새로 고침** 을 선택합니다.

1. 목록에서 동기화할 테이블을 선택합니다. 기본적으로는 모든 열이 선택되므로 동기화하지 않을 열의 확인란 선택은 취소합니다. 선택한 기본 키 열을 남겨 두어야 합니다.

1. **저장** 을 선택합니다.

1. 데이터베이스는 동기화를 예약하거나 수동으로 실행할 때까지 기본적으로 동기화되지 않습니다. 수동 동기화를 실행하려면 Azure Portal에서 SQL Database의 데이터베이스로 이동하여 **다른 데이터베이스와 동기화** 를 선택하고 동기화 그룹을 선택합니다. **데이터 동기화** 페이지가 열립니다. **동기화** 를 선택합니다.

    ![수동 동기화](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>FAQ

**SQL 데이터 동기화에서는 테이블이 완전히 생성되나요?**

대상 데이터베이스에서 동기화 스키마 테이블이 누락되어 있으면 SQL 데이터 동기화에서는 사용자가 선택한 열로 누락된 테이블을 만듭니다. 그러나 다음과 같은 이유로 인해 이 과정에서 완전히 충실한 스키마가 만들어지지는 않습니다.

- 대상 테이블에서는 사용자가 선택한 열만 생성됩니다. 선택하지 않은 열은 무시됩니다.
- 대상 테이블에서는 선택한 열 인덱스만 생성됩니다. 선택하지 않은 열의 인덱스는 무시됩니다.
- XML 형식 열의 인덱스는 생성되지 않습니다.
- CHECK 제약 조건은 생성되지 않습니다.
- 원본 테이블의 트리거는 생성되지 않습니다.
- 보기 및 저장 프로시저는 생성되지 않습니다.

이러한 제한 때문에 다음이 권장됩니다.

- 프로덕션 환경에서는 완전히 충실한 스키마를 직접 생성합니다.
- 서비스로 실험을 할 때는 자동 프로비저닝 기능을 사용합니다.

**생성하지 않은 테이블이 표시되는 이유는 무엇인가요?**

데이터 동기화는 변경 내용 추적을 위해 데이터베이스에 추가 테이블을 만듭니다. 해당 테이블을 삭제하지 마세요. 삭제하면 데이터 동기화의 작동이 중지됩니다.

**동기화 후 내 데이터가 일치하나요?**

그럴 필요는 없습니다. 허브 하나와 스포크 3개(A, B, C)가 포함된 동기화 그룹에서 허브->A/B/C로 동기화가 진행되는 경우 허브->A로의 동기화 *후* 에 데이터베이스 A를 변경하면 다음 동기화 작업이 진행될 때까지 해당 변경 내용이 데이터베이스 B나 C에 기록되지 않습니다.

**동기화 그룹에 스키마 변경 내용은 어떻게 가져오나요?**

스키마를 변경하고 모든 변경 내용을 수동으로 전파합니다.

1. 스키마 변경 내용을 모든 허브 및 모든 동기화 멤버에 수동으로 복제합니다.
1. 동기화 스키마를 업데이트합니다.

새 테이블 및 열 추가:

새 테이블과 열은 현재 동기화에 영향을 주지 않으며, 이러한 테이블과 열을 동기화 스키마에 추가할 때까지는 데이터 동기화에서 해당 테이블/열을 무시합니다. 새 데이터베이스 개체를 추가할 때는 다음 순서를 따르세요.

1. 허브 및 모든 동기화 멤버에 새 테이블이나 열을 추가합니다.
1. 동기화 스키마에 새 테이블이나 열을 추가합니다.
1. 새 테이블 및 열에 값을 삽입하기 시작합니다.

열의 데이터 형식 변경:

기존 열의 데이터 형식을 변경하면 데이터 동기화는 새 값이 동기화 스키마에 정의된 원본 데이터 형식과 잘 맞을 경우 계속 작동합니다. 예를 들어 원본 데이터베이스에서 데이터 형식을 **int** 에서 **bigint** 로 변경하면 **int** 데이터 형식에 사용하기에 너무 큰 값을 삽입하기 전까지는 데이터 동기화가 계속 작동합니다. 변경을 완료하려면 스키마 변경 내용을 허브와 모든 동기화 멤버에 수동으로 복제한 다음 동기화 스키마를 업데이트합니다.

**데이터 동기화를 사용하여 데이터베이스를 내보내고 가져오려면 어떻게 해야 하나요?**

데이터베이스를 *.bacpac* 파일로 내보내고 데이터베이스를 만들기 위해 파일을 가져온 후에 새 데이터베이스에서 데이터 동기화를 사용하도록 다음 작업을 수행합니다.

1. [이 스크립트](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql)를 사용하여 새 데이터베이스에서 데이터 동기화 개체와 추가 테이블을 정리합니다. 이 스크립트는 데이터베이스에서 필요한 데이터 동기화 개체를 모두 삭제합니다.
1. 새 데이터베이스를 통해 동기화 그룹을 다시 만듭니다. 더 이상 기존 동기화 그룹이 필요하지 않으면 삭제합니다.

**클라이언트 에이전트에 대한 정보는 어디서 확인할 수 있나요?**

클라이언트 에이전트에 대한 질문과 대답은 [에이전트 FAQ](sql-data-sync-agent-overview.md#agent-faq)를 참조하세요.

**프라이빗 링크를 사용하기 전에 수동으로 승인해야 하나요?**

예, 동기화 그룹 배포 중에 또는 PowerShell을 사용하여 Azure Portal의 프라이빗 엔드포인트 연결 페이지에서 서비스 관리형 프라이빗 엔드포인트를 수동으로 승인해야 합니다.

**동기화 작업에서 Azure 데이터베이스를 프로비저닝할 때 방화벽 오류가 발생하는 이유는 무엇인가요?**

이는 Azure 리소스가 서버에 액세스할 수 없기 때문에 발생할 수 있습니다. Azure 데이터베이스의 방화벽에 "이 서버에 액세스할 수 있는 Azure 서비스 및 리소스 허용" 설정이 "예"로 설정되어 있는지 확인합니다.


## <a name="next-steps"></a>다음 단계

축하합니다. SQL Database 인스턴스와 SQL Server 데이터베이스가 모두 포함된 동기화 그룹을 만들었습니다.

SQL 데이터 동기화에 대한 자세한 내용은 다음을 참조하세요.

- [Azure SQL 데이터 동기화용 데이터 동기화 에이전트](sql-data-sync-agent-overview.md)
- [모범 사례](sql-data-sync-best-practices.md) 및 [Azure SQL 데이터 동기화 관련 문제를 해결하는 방법](sql-data-sync-troubleshoot.md)
- [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](./monitor-tune-overview.md)
- [Transact-SQL](sql-data-sync-update-sync-schema.md) 또는 [PowerShell](scripts/update-sync-schema-in-sync-group.md)을 사용하여 동기화 스키마 업데이트

SQL Database에 대한 자세한 내용은 다음을 참조하세요.

- [SQL Database 개요](sql-database-paas-overview.md)
- [데이터베이스 수명 주기 관리](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
