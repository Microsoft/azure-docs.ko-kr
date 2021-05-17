---
title: Azure Stream Analytics 작업에서 SQL Database 참조 데이터 사용
description: 이 문서에서는 Azure Portal 및 Visual Studio에서 Azure Stream Analytics 작업에 대한 참조 데이터 입력으로 SQL Database를 사용하는 방법을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016408"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Azure Stream Analytics 작업에 SQL Database의 참조 데이터 사용

Azure Stream Analytics는 참조 데이터 입력 원본으로 Azure SQL Database를 지원합니다. Azure Portal 및 Stream Analytics 도구가 있는 Visual Studio에서 Stream Analytics 작업에 대한 참조 데이터로 SQL Database를 사용할 수 있습니다. 이 문서에서는 두 방법을 수행하는 방법을 모두 보여 줍니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 Azure SQL Database를 참조 입력 원본으로 추가하려면 다음 단계를 따릅니다.

### <a name="portal-prerequisites"></a>포털의 필수 구성 요소

1. Stream Analytics 작업을 만듭니다.

2. Stream Analytics 작업에서 사용할 스토리지 계정을 만듭니다.

3. Stream Analytics 작업에서 참조 데이터로 사용할 데이터 세트를 사용하여 Azure SQL Database를 만듭니다.

### <a name="define-sql-database-reference-data-input"></a>SQL Database 참조 데이터 입력 정의

1. Stream Analytics 작업의 **작업 토폴로지** 아래에서 **입력** 을 선택합니다. **참조 입력 추가** 를 클릭하고 **SQL Database** 를 선택합니다.

   ![왼쪽 탐색 창에서 입력이 선택됩니다. 입력에서 + 참조 입력 추가가 선택되고, Blob Storage 및 SQL Database 값을 표시하는 드롭다운 목록이 나타납니다.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Stream Analytics 입력 구성을 입력합니다. 데이터베이스 이름, 서버 이름, 사용자 이름 및 암호를 선택합니다. 참조 데이터 입력을 주기적으로 새로 고치려면 “설정”을 선택하여 DD:HH:MM로 새로 고침 빈도를 지정합니다. 새로 고침 빈도가 짧은 큰 데이터 세트가 있는 경우 [델타 쿼리](sql-reference-data.md#delta-query)를 사용할 수 있습니다.

   ![SQL Database를 선택하면 SQL Database의 새 입력 페이지가 나타납니다. 왼쪽 창에는 구성 양식이 있고 오른쪽 창에는 스냅샷 쿼리가 있습니다.](./media/sql-reference-data/sql-input-config.png)

3. SQL 쿼리 편집기에서 스냅샷 쿼리를 테스트합니다. 자세한 내용은 [Azure Portal의 SQL 쿼리 편집기를 사용하여 데이터 연결 및 쿼리](../azure-sql/database/connect-query-portal.md)를 참조하세요.

### <a name="specify-storage-account-in-job-config"></a>작업 구성에서 스토리지 계정 지정

**구성** 아래의 **스토리지 계정 설정** 으로 이동한 후 **스토리지 계정 추가** 를 선택합니다.

   ![왼쪽 창에서 스토리지 계정 설정이 선택됩니다. 오른쪽 창에는 스토리지 계정 추가 단추가 있습니다.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>작업 시작

다른 입력, 출력 및 쿼리를 구성한 경우 Stream Analytics 작업을 시작할 수 있습니다.

## <a name="tools-for-visual-studio"></a>Visual Studio용 도구

Visual Studio를 사용하여 Azure SQL Database를 참조 입력 원본으로 추가하려면 다음 단계를 따릅니다.

### <a name="visual-studio-prerequisites"></a>Visual Studio의 필수 구성 요소

1. [Stream Analytics Tools for Visual Studio를 설치](stream-analytics-tools-for-visual-studio-install.md)합니다. 다음 Visual Studio 버전이 지원됩니다.

   * Visual Studio 2015
   * Visual Studio 2019

2. [Stream Analytics Tools for Visual Studio](stream-analytics-quick-create-vs.md) 빠른 시작을 숙지합니다.

3. 스토리지 계정을 만듭니다.

### <a name="create-a-sql-database-table"></a>SQL Database 테이블 만들기

SQL Server Management Studio를 사용하여 참조 데이터를 저장할 테이블을 만듭니다. 자세한 내용은 [SSMS를 사용하여 첫 번째 Azure SQL Database 디자인](../azure-sql/database/design-first-database-tutorial.md)을 참조하세요.

다음 예제에서 사용하는 예제 테이블은 다음 문에서 만들었습니다.

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>구독 선택

1. Visual Studio의 **보기** 메뉴에서 **서버 탐색기** 를 선택합니다.

2. **Azure** 를 마우스 오른쪽 단추로 클릭하고 **Microsoft Azure 구독에 연결** 을 선택한 다음, Azure 계정으로 로그인합니다.

### <a name="create-a-stream-analytics-project"></a>Stream Analytics 프로젝트 만들기

1. **파일 > 새 프로젝트 만들기** 를 선택합니다. 

2. 왼쪽의 템플릿 목록에서 **Stream Analytics** 를 선택한 다음, **Azure Stream Analytics 애플리케이션** 을 선택합니다. 

3. 프로젝트 **이름**, **위치** 및 **솔루션 이름** 을 입력하고, **확인** 을 선택합니다.

   ![Stream Analytics 템플릿이 선택되고 Azure Stream Analytics 애플리케이션이 선택되며 이름, 위치 및 솔루션 이름 상자가 강조 표시됩니다.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>SQL Database 참조 데이터 입력 정의

1. 새 입력을 만듭니다.

   ![새 항목 추가에서 입력이 선택됩니다.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. **솔루션 탐색기** 에서 **Input.json** 을 두 번 클릭합니다.

3. **Stream Analytics 입력 구성** 을 입력합니다. 데이터베이스 이름, 서버 이름, 새로 고침 유형 및 새로 고침 빈도를 선택합니다. `DD:HH:MM` 형식으로 새로 고침 빈도를 지정합니다.

   ![Stream Analytics 입력 구성에서는 드롭다운 목록에서 값이 입력되거나 선택됩니다.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   "한 번만 실행" 또는 "주기적으로 실행"을 선택하면 **[입력 별칭].snapshot.sql** 이라는 하나의 SQL CodeBehind 파일이 **Input.json** 파일 노드 아래의 프로젝트에 생성됩니다.

   ![SQL CodeBehind 파일 Chemicals.snapshot.sql이 강조 표시됩니다.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   "덱타로 주기적으로 새로 고침"을 선택하면 두 개의 SQL CodeBehind 파일 **[입력 별칭].snapshot.sql** 및 **[입력 별칭].delta.sql** 이 생성됩니다.

   ![SQL CodeBehind 파일 Chemicals.delta.sql 및 Chemicals.snapshot.sql이 강조 표시됩니다.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. 편집기에서 SQL 파일을 열고 SQL 쿼리를 작성합니다.

5. Visual Studio 2019를 사용하며 SQL Server Data Tools를 설치한 경우 **실행** 을 클릭하여 쿼리를 테스트할 수 있습니다. SQL Database에 연결하는 데 도움이 되는 마법사 창이 팝업되고 쿼리 결과가 아래쪽 창에 나타납니다.

### <a name="specify-storage-account"></a>스토리지 계정 지정

**JobConfig.json** 을 열어 SQL 참조 스냅샷을 저장하기 위한 스토리지 계정을 지정합니다.

   ![Stream Analytics 작업 구성이 기본값으로 표시됩니다. 전역 스토리지 설정이 강조 표시됩니다.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>로컬로 테스트하고 Azure에 배포

작업을 Azure에 배포하기 전에 실시간 입력 데이터에 대해 로컬로 쿼리 논리를 테스트할 수 있습니다. 이 기능에 대한 자세한 내용은 [Azure Stream Analytics Tools for Visual Studio(미리 보기)를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md)를 참조하세요. 테스트가 완료되면 **Azure에 제출** 을 클릭합니다. 작업 시작 방법을 알아보려면 [Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 만들기](stream-analytics-quick-create-vs.md) 빠른 시작을 참조하세요.

## <a name="delta-query"></a>델타 쿼리

델타 쿼리를 사용하는 경우 [Azure SQL Database의 temporal 테이블](../azure-sql/temporal-tables.md)을 사용하는 것이 좋습니다.

1. Azure SQL Database에서 임시 테이블 만들기
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. 스냅샷 쿼리를 작성합니다. 

   **\@snapshotTime** 매개 변수를 사용하여 시스템 타임에 유효한 SQL Database temporal 테이블에서 참조 데이터 세트를 가져오도록 Stream Analytics 런타임에 지시합니다. 이 매개 변수를 제공하지 않으면 클럭 오차로 인해 부정확한 기본 참조 데이터 세트를 가져올 수 있습니다. 전체 스냅샷 쿼리 예제는 아래에 나와 있습니다.
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. 델타 쿼리를 작성합니다. 
   
   이 쿼리는 시작 시간, **\@deltaStartTime** 및 종료 시간 **\@deltaEndTime** 내에 삽입되었거나 삭제된 SQL Database의 모든 행을 검색합니다. 델타 쿼리는 스냅샷 쿼리와 동일한 열뿐만 아니라 **_opdration_** 열도 반환해야 합니다. 이 열은 행이 **\@deltaStartTime** 과 **\@deltaEndTime** 사이에 삽입되었는지 또는 삭제되었는지 정의합니다. 결과 행에는 레코드가 삽입되면 **1**, 삭제되면 **2** 가 태그로 지정됩니다. 또한 이 쿼리는 SQL Server 쪽에서 **워터마크** 를 추가하여 델타 기간의 모든 업데이트가 적절하게 캡처되고 있는지 확인해야 합니다. **워터마크** 없이 델타 쿼리를 사용하면 잘못된 참조 데이터 세트가 사용될 수 있습니다.  

   업데이트된 레코드의 경우 temporal 테이블은 삽입 및 삭제 작업을 캡처하여 목록을 만듭니다. 그러면 Stream Analytics 런타임은 이전 스냅샷에 델타 쿼리 결과를 적용하여 참조 데이터를 최신 상태로 유지합니다. 델타 쿼리 예제는 다음과 같습니다.

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Stream Analytics 런타임은 검사점을 저장하는 델타 쿼리 외에, 스냅샷 쿼리를 주기적으로 실행할 수 있습니다.

## <a name="test-your-query"></a>쿼리 테스트
   쿼리가 Stream Analytics 작업에서 참조 데이터로 사용할 예상 데이터 세트를 반환하는지 확인하는 것이 중요합니다. 쿼리를 테스트하려면 포털의 작업 토폴로지 섹션 아래의 입력으로 이동합니다. 그런 다음, SQL Database 참조 입력에서 샘플 데이터를 선택할 수 있습니다. 샘플을 사용할 수 있게 되면 파일을 다운로드하고 반환되는 데이터가 예상대로 작동하는지 확인할 수 있습니다. 개발 및 테스트 반복을 최적화하려면 [Visual Studio용 Stream Analytics 도구](./stream-analytics-tools-for-visual-studio-install.md)를 사용하는 것이 좋습니다. 또한 기본 설정의 다른 도구를 사용하여 먼저 쿼리가 Azure SQL Database에서 올바른 결과를 반환하는지 확인한 다음, Stream Analytics 작업에서 해당 결과를 사용할 수 있습니다. 

### <a name="test-your-query-with-visual-studio-code"></a>Visual Studio Code를 사용하여 쿼리 테스트

   Visual Studio Code에 [Azure Stream Analytics 도구](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) 및 [SQL Server(mssql)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)를 설치하고, ASA 프로젝트를 설정합니다. 자세한 내용은 [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](./quick-create-visual-studio-code.md) 및 [SQL Server(mssql) 확장 자습서](/sql/tools/visual-studio-code/sql-server-develop-use-vscode)를 참조하세요.

1. SQL 참조 데이터 입력을 구성합니다.
   
   ![Visual Studio Code 편집기(탭)에 ReferenceSQLDatabase.json이 표시됩니다.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. SQL Server 아이콘을 선택하고 **연결 추가** 를 클릭합니다.
   
   ![왼쪽 창에 + 연결 추가가 나타나고 강조 표시됩니다.](./media/sql-reference-data/add-sql-connection.png)

3. 연결 정보를 입력합니다.
   
   ![데이터베이스 및 서버 정보에 대한 두 확인란이 강조 표시됩니다.](./media/sql-reference-data/fill-connection-information.png)

4. 참조 SQL을 마우스 오른쪽 단추로 클릭하고 **쿼리 실행** 을 선택합니다.
   
   ![쿼리 실행이 상황에 맞는 메뉴에 강조 표시됩니다.](./media/sql-reference-data/execute-query.png)

5. 연결을 선택합니다.
   
   ![대화 상자에는 "아래 목록에서 연결 프로필을 만들기"라는 메시지가 표시되고 목록의 항목이 하나 강조 표시됩니다.](./media/sql-reference-data/choose-connection.png)

6. 쿼리 결과를 검토하고 확인합니다.
   
   ![쿼리 검색 결과는 VS Code 편집기 탭에 있습니다.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>FAQ

**Azure Stream Analytics에서 SQL 참조 데이터 입력을 사용하면 추가 비용이 부과되나요?**

Stream Analytics 작업에 [스트리밍 단위당 비용](https://azure.microsoft.com/pricing/details/stream-analytics/)이 추가로 부과되지 않습니다. 그러나 Stream Analytics 작업이 연결된 Azure Storage 계정이 있어야 합니다. Stream Analytics 작업은 SQL DB를 쿼리하여(작업 시작 및 새로 고침 간격 동안) 참조 데이터 세트를 검색하고, 해당 스냅샷을 스토리지 계정에 저장합니다. 이러한 스냅샷을 저장하면 Azure Storage 계정의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/)에서 자세히 설명하는 추가 요금이 발생합니다.

**참조 데이터 스냅샷이 SQL DB에서 쿼리되고 Azure Stream Analytics 작업에 사용되는지를 어떻게 알 수 있나요?**

SQL Database 참조 데이터 입력의 상태를 모니터링하는 데 사용할 수 있는 2개의 메트릭이 메트릭 Azure Portal 아래에 논리 이름별로 필터링되어 표시됩니다.

   * InputEvents: 이 메트릭은 SQL Database 참조 데이터 세트에서 로드된 레코드 수를 측정합니다.
   * InputEventBytes: 이 메트릭은 Stream Analytics 작업의 메모리에 로드된 참조 데이터 스냅샷의 크기를 측정합니다. 

이러한 두 메트릭을 함께 사용하여 작업이 SQL Database를 쿼리하여 참조 데이터 세트를 가져온 후 메모리에 로드하는지를 유추할 수 있습니다.

**특수한 유형의 Azure SQL Database가 필요한가요?**

Azure Stream Analytics는 모든 유형의 Azure SQL Database에서 작동합니다. 그러나 참조 데이터 입력에 대해 설정된 새로 고침 빈도가 쿼리 로드에 영향을 미칠 수 있는지 이해해야 합니다. 델타 쿼리 옵션을 사용하려면 Azure SQL Database의 temporal 테이블을 사용하는 것이 좋습니다.

**Azure Stream Analytics가 Azure Storage 계정에 스냅샷을 저장하는 이유는 무엇인가요?**

Stream Analytics는 정확히 한 번의 이벤트 처리 및 한 번 이상의 이벤트 배달을 보장합니다. 일시적 문제가 작업에 영향을 미치는 경우 상태 복원을 위해 약간의 재생이 필요합니다. 재생을 사용하도록 설정하려면 이러한 스냅샷이 Azure Storage 계정에 저장되어 있어야 합니다. 검사점 재생에 대한 자세한 내용은 [Azure Stream Analytics 작업의 검사점 및 재생 개념](stream-analytics-concepts-checkpoint-replay.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Stream Analytics에서 조회에 대한 참조 데이터 사용](stream-analytics-use-reference-data.md)
* [빠른 시작: Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트(미리 보기)](stream-analytics-live-data-local-testing.md)