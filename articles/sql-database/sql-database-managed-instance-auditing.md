---
title: Azure SQL Database 관리형 인스턴스 감사 | Microsoft Docs
description: T-SQL을 사용하여 Azure SQL Database 관리형 인스턴스 감사를 시작하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 6ada2a5e505bfe37f4f9a956570d8b6f38f55e55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702867"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Azure SQL Database 관리형 인스턴스 감사 시작

[관리되는 인스턴스](sql-database-managed-instance.md) 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 기록합니다. 또한

- 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.
- 감사를 사용하면 규정을 완전히 준수한다고 보장할 수는 없지만 규정 표준을 보다 쉽게 준수할 수 있습니다. Azure에 대 한 자세한 내용은 지원 표준 규정 준수 프로그램에 대 한 참조를 [Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 있는 SQL Database 규정 준수 인증의 최신 목록을 찾을 수 있습니다.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Azure Storage로 서버에 대한 감사 설정

다음 섹션에서는 관리되는 인스턴스에 대한 감사 구성을 설명합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
1. 감사 로그가 저장되는 Azure Storage **컨테이너**를 만듭니다.

   1. 감사 로그를 저장할 Azure Storage로 이동합니다.

      > [!IMPORTANT]
      > 동일한 지역의 스토리지 계정을 관리되는 인스턴스로 사용하여 지역 간 읽기/쓰기를 방지합니다.

   1. 저장소 계정에서 **개요**로 이동한 다음, **Blob**을 클릭합니다.

      ![Azure Blob 위젯](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. 최상위 메뉴에서 **+ 컨테이너**를 클릭하여 새 컨테이너를 만듭니다.

      ![Blob 컨테이너 만들기 아이콘](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. 컨테이너 **이름**을 지정하고 공용 액세스 수준을 **개인**으로 설정한 다음, **확인**을 클릭합니다.

      ![Blob 컨테이너 구성 만들기](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. 감사 로그용 컨테이너를 만든 후 이 컨테이너를 감사 로그의 대상으로 구성하려면 [T-SQL을 사용](#blobtsql)하거나 [SSMS(SQL Server Management Studio) UI를 사용](#blobssms)할 수 있습니다.

   - <a id="blobtsql"></a>T-SQL을 사용하여 감사 로그용 Blog 스토리지 구성:

     1. 컨테이너 목록에서 새로 만든 컨테이너를 클릭한 다음, **컨테이너 속성**을 클릭합니다.

        ![Blob 컨테이너 속성 단추](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. 복사 아이콘을 클릭하여 컨테이너 URL을 복사하고 나중에 사용할 수 있도록 메모장 등에서 URL을 저장합니다. 컨테이너 URL 형식은 `https://<StorageName>.blob.core.windows.net/<ContainerName>`이어야 합니다.

        ![Blob 컨테이너 URL 복사](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. 스토리지 계정에 관리되는 인스턴스 감사 액세스 권한을 부여하기 위한 Azure Storage **SAS 토큰**을 생성합니다.

        - 이전 단계에서 컨테이너를 만든 Azure Storage 계정으로 이동합니다.

        - 저장소 설정 메뉴에서 **공유 액세스 서명**을 클릭합니다.

          ![스토리지 설정 메뉴의 공유 액세스 서명 아이콘](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - 다음과 같이 SAS를 구성합니다.

          - **허용된 서비스**: Blob

          - **시작 날짜**: 표준 시간대 관련 문제를 방지하려면 어제 날짜를 사용하는 것이 좋습니다.

          - **종료 날짜**: 이 SAS 토큰이 만료되는 날짜를 선택합니다.

            > [!NOTE]
            > 감사 오류를 방지하려면 만료 시 토큰을 갱신합니다.

          - **SAS 생성**을 클릭합니다.
            
            ![SAS 구성](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - SAS 생성을 클릭하면 SAS 토큰이 맨 아래에 표시됩니다. 복사 아이콘을 클릭하여 토큰을 복사하고 나중에 사용할 수 있도록 메모장 등에서 토큰을 저장합니다.

          ![SAS 토큰 복사](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > 토큰 시작 부분에서 물음표(“?”) 문자를 제거합니다.

     1. SSMS(SQL Server Management Studio) 또는 기타 지원되는 도구를 통해 관리되는 인스턴스에 연결합니다.

     1. 다음 T-SQL 문을 실행하여 이전 단계에서 만든 컨테이너 URL 및 SAS 토큰으로 **새 자격 증명을 만듭니다**.

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 다음 T-SQL 문을 실행하여 새 서버 감사를 만듭니다(고유한 감사 이름을 선택하고 이전 단계에서 만든 컨테이너 URL 사용). 지정되지 않은 경우 `RETENTION_DAYS` 기본값은 0(무제한 보존)입니다.

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. [서버 감사 사양 또는 데이터베이스 감사 사양](#createspec)을 만들어 계속 진행합니다.

   - <a id="blobssms"></a>SSMS(SQL Server Management Studio) 18(미리 보기)을 사용하여 감사 로그에 대한 blob 스토리지를 구성합니다.

     1. SSMS(SQL Server Management Studio) UI를 사용하여 Managed Instance에 연결합니다.

     1. 개체 탐색기의 루트 노드를 확장합니다.

     1. **보안** 노드를 확장하고 마우스 오른쪽 단추로 **감사** 노드를 클릭한 후 "새 감사"를 클릭합니다.

        ![보안 및 감사 노드 확장](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. **감사 대상**에서 "URL"이 선택되어 있는지 확인하고 **찾아보기**를 클릭합니다.

        ![Azure Storage 찾아보기](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (선택 사항) Azure 계정에 로그인합니다.

        ![Azure에 로그인](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. 드롭다운에서 구독, 스토리지 계정 및 Blob 컨테이너를 선택하거나 **만들기**를 클릭하여 고유한 컨테이너를 만듭니다. 완료되면 **확인**을 클릭합니다.

        ![Azure 구독, 저장소 계정 및 blob 컨테이너를 선택 합니다.](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. "감사 만들기" 대화 상자에서 **확인**을 클릭합니다.

1. <a id="createspec"></a>Blob 컨테이너를 감사 로그의 대상으로 구성한 후 SQL Server의 경우와 마찬가지로, 서버 감사 사양 또는 데이터베이스 감사 사양을 만듭니다.

   - [서버 감사 사양 만들기 T-SQL 가이드](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [데이터베이스 감사 사양 만들기 T-SQL 가이드](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. 6단계에서 만든 서버 감사를 사용하도록 설정합니다.

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

추가 정보는 다음을 참조하세요.

- [Azure SQL Database 및 SQL Server의 데이터베이스에서 단일 데이터베이스, 탄력적 풀 및 관리형 인스턴스 간의 감사 차이점](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>이벤트 허브 또는 Azure Monitor 로그로 서버에 대 한 감사 설정

Even Hubs 또는 Azure Monitor 로그에서 관리 되는 인스턴스 감사 로그를 보낼 수 있습니다. 이 섹션에서는 이렇게 구성하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com/)에서 관리되는 인스턴스로 이동합니다.

2. **진단 설정**을 클릭합니다.

3. **진단 켜기**를 클릭합니다. 진단을 이미 사용하도록 설정한 경우 *+진단 설정 추가*가 대신 표시됩니다.

4. 로그 목록에서 **SQLSecurityAuditEvents**를 선택합니다.

5. 감사 이벤트-이벤트 허브, Azure Monitor 로그 중 하나 또는 둘 다에 대 한 대상을 선택 합니다. 각 대상에 대해 필수 매개 변수(예: Log Analytics 작업 영역)를 구성합니다.

6. **저장**을 클릭합니다.

    ![진단 설정 구성](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. **SSMS(SQL Server Management Studio)** 또는 기타 지원되는 클라이언트를 사용하여 관리되는 인스턴스에 연결합니다.

8. 다음 T-SQL 문을 실행하여 서버 감사를 만듭니다.

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. SQL Server의 경우와 마찬가지로, 서버 감사 사양 또는 데이터베이스 감사 사양을 만듭니다.

   - [서버 감사 사양 만들기 T-SQL 가이드](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [데이터베이스 감사 사양 만들기 T-SQL 가이드](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 8 단계에서 만든 서버 감사를 사용 하도록 설정 합니다.
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>감사 로그 사용

### <a name="consume-logs-stored-in-azure-storage"></a>Azure Storage에 저장된 로그 사용

여러 방법으로 Blob 감사 로그를 볼 수 있습니다.

- 시스템 함수 `sys.fn_get_audit_file`(T-SQL)을 사용하여 테이블 형식의 감사 로그 데이터를 반환할 수 있습니다. 이 함수 사용에 대한 자세한 내용은 [sys.fn_get_audit_file 설명서](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)를 참조하세요.

- [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 등의 도구를 사용하여 감사 로그를 탐색할 수 있습니다. Azure Storage에서 감사 로그는 감사 로그를 저장하도록 정의된 컨테이너 내부에 Blob 파일 컬렉션으로 저장됩니다. 저장소 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대한 자세한 내용은 [BLOB 감사 로그 형식 참조](https://go.microsoft.com/fwlink/?linkid=829599)를 참조하세요.

- 감사 로그 사용 방법의 전체 목록은 [SQL 데이터베이스 감사 시작](sql-database-auditing.md)을 참조하세요.

### <a name="consume-logs-stored-in-event-hub"></a>Event Hubs에 저장된 로그 사용

이벤트 허브에서 감사 로그 데이터를 사용하려면 이벤트를 사용하고 이벤트를 대상에 작성하도록 스트림을 설정해야 합니다. 자세한 내용은 Azure Event Hubs 설명서를 참조하세요.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>사용 하 고 Azure Monitor 로그에 저장 된 로그를 분석 합니다.

감사 로그는 Azure Monitor 로그에 기록에 있는 경우 사용할 수 있는 감사 데이터에 대해 고급 검색을 실행할 수 있는 Log Analytics 작업 영역에서 합니다. 시작 지점으로 고 Log Analytics 작업 영역으로 이동 *일반적인* 섹션 클릭 *로그* 와 같은 간단한 쿼리를 입력 하 고: `search "SQLSecurityAuditEvents"` 로그를 보려면 감사 합니다.  

Azure Monitor 로그 통합된 검색 및 사용자 지정 대시보드를 사용 하 여 모든 워크 로드 및 서버에서 수백만 개의 레코드를 쉽게 분석할 실시간 작업 통찰력을 제공 합니다. Azure Monitor 로그 검색 언어 및 명령에 대 한 유용한 정보를 추가, 참조 [Azure Monitor 로그 검색 참조](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Azure SQL Database의 데이터베이스 및 SQL Server의 데이터베이스 간 감사 차이점

Azure SQL Database의 데이터베이스 및 SQL Server의 데이터베이스에서 감사 간의 주요 차이점은 다음과 같습니다.

- Azure SQL Database의 관리형 인스턴스 배포 옵션을 사용하면 감사는 서버 수준에서 작동하며 `.xel` 로그 파일이 Azure Blob 스토리지에 저장됩니다.
- Azure SQL Database의 단일 데이터베이스 및 탄력적 풀 배포 옵션을 사용하면 감사는 데이터베이스 수준에서 작동합니다.
- SQL Server 온-프레미스/가상 머신에서 감사는 서버 수준에서 작동하지만 파일 시스템/Windows 이벤트 로그에 이벤트를 저장합니다.

관리되는 인스턴스의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그는 **지원되지 않습니다**.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 새 `TO URL` 구문이 제공되고 `.xel` 파일이 배치되는 Azure Blob Storage 컨테이너의 URL을 지정할 수 있습니다.
- 새로운 구문 `TO EXTERNAL MONITOR` 도 허브 및 Azure Monitor 로그 대상으로 사용할 수 있도록 제공 됩니다.
- SQL Database에서 Windows 파일 공유에 액세스할 수 없으므로 `TO FILE` 구문은 **지원되지 않습니다**.
- 종료 옵션은 **지원되지 않습니다**.
- `queue_delay` 0은 **지원되지 않습니다**.

## <a name="next-steps"></a>다음 단계

- 감사 로그 사용 방법의 전체 목록은 [SQL 데이터베이스 감사 시작](sql-database-auditing.md)을 참조하세요.
- Azure에 대 한 자세한 내용은 지원 표준 규정 준수 프로그램에 대 한 참조를 [Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 있는 SQL Database 규정 준수 인증의 최신 목록을 찾을 수 있습니다.

<!--Image references-->









