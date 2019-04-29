---
title: Azure Database for MySQL의 가져오기 및 내보내기
description: 이 문서에서는 MySQL Workbench와 같은 도구를 사용하여 Azure Database for MySQL에서 데이터베이스를 가져오고 내보내는 일반적인 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838122"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>가져오기 및 내보내기를 사용하여 MySQL 데이터베이스 마이그레이션
이 문서에서는 MySQL Workbench를 사용하여 Azure Database for MySQL 서버로 데이터를 가져오고 내보내는 두 가지 일반적인 방법을 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Portal을 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md)를 따르는 Azure Database for MySQL 서버
- [다운로드된](https://dev.mysql.com/downloads/workbench/) MySQL Workbench 또는 가져오기 및 내보내기를 위한 다른 MySQL 도구

## <a name="use-common-tools"></a>일반 도구 사용
MySQL Workbench, Toad 또는 Navicat과 같은 일반 도구를 사용하여 원격으로 연결하고 MySQL용 Azure 데이터베이스에서 데이터를 가져오거나 내보냅니다. 

인터넷에 연결된 클라이언트 컴퓨터에서 이러한 도구를 사용하여 Azure Database for MySQL에 연결합니다. [Azure Database for MySQL에서 SSL 연결 구성](concepts-ssl-connection-security.md)에 설명된 대로 최상의 보안을 위해 SSL 암호화 연결을 사용합니다.

MySQL용 Azure 데이터베이스로 마이그레이션할 때 가져오기 및 내보내기 파일을 특수한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버에서 데이터베이스 만들기
데이터를 마이그레이션하려는 Azure Database for MySQL 서버에서 빈 데이터베이스를 만듭니다. MySQL Workbench, Toad 또는 Navicat과 같은 도구를 사용하여 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결하려면 Azure Database for MySQL의 **개요**에서 연결 정보를 찾습니다.

![Azure Portal에서 연결 정보 찾기](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

MySQL Workbench에 연결 정보를 추가합니다.

![MySQL Workbench 연결 문자열](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>덤프 및 복원 대신 가져오기 및 내보내기 기술을 사용할 시기 결정
MySQL 도구를 사용하여 다음과 같은 시나리오에서 Azure MySQL 데이터베이스에 데이터베이스를 가져오고 내보낼 수 있습니다. 다른 시나리오에서는 [덤프 및 복원](concepts-migrate-dump-restore.md) 방법을 대신 사용하는 것이 유용할 수 있습니다. 

- 기존 MySQL 데이터베이스에서 Azure MySQL 데이터베이스로 가져오도록 몇 가지 테이블을 선택적으로 선택해야 할 때 가져오기 및 내보내기 기술을 사용하는 것이 좋습니다.  이렇게 하면 시간과 리소스를 절약하도록 마이그레이션에서 불필요한 테이블을 생략할 수 있습니다. 예를 들어 [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)로 `--include-tables` 또는 `--exclude-tables` 스위치를 사용하고 [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables) 덤프로 `--tables` 스위치를 사용합니다.
- 테이블 이외의 다른 데이터베이스 개체를 이동할 때 해당 개체를 명시적으로 만듭니다. 마이그레이션하려는 제약 조건(기본 키, 외래 키, 인덱스), 뷰, 함수, 프로시저, 트리거 및 다른 모든 데이터베이스 개체를 포함합니다.
- MySQL 데이터베이스 이외의 외부 데이터 원본에서 데이터를 마이그레이션하는 경우 플랫 파일을 만들고 [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)를 사용하여 가져옵니다.

데이터베이스의 모든 테이블이 데이터를 Azure Database for MySQL로 로드할 때 InnoDB 저장소 엔진을 사용하도록 합니다. Azure Database for MySQL은 InnoDB 저장소 엔진만을 지원하므로 대체 저장소 엔진을 지원하지 않습니다. 테이블에 대체 저장소 엔진이 필요한 경우 Azure Database for MySQL로 마이그레이션 전에 InnoDB 엔진 형식으로 사용하도록 변환해야 합니다. 

예를 들어 MyISAM 엔진을 사용하는 WordPress 또는 웹앱이 있는 경우 먼저 데이터를 InnoDB 테이블로 마이그레이션하여 테이블을 변환합니다. 그런 다음 Azure Database for MySQL로 복원합니다. `ENGINE=INNODB` 절을 사용하여 테이블을 만들기 위한 엔진을 설정한 다음 마이그레이션 전에 데이터를 호환되는 테이블로 전송합니다. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>가져오기 및 내보내기를 위한 성능 권장 사항
-   데이터를 로드하기 전에 클러스터형 인덱스 및 기본 키를 만듭니다. 기본 키 순서로 데이터를 로드합니다. 
-   데이터가 로드될 때까지 보조 인덱스의 생성을 지연합니다. 로드한 후 모든 보조 인덱스를 만듭니다. 
-   로드하기 전에 외래 키 제약 조건을 비활성화합니다. 외래 키 검사 비활성화는 상당한 성능 향상을 제공합니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
-   병렬로 데이터를 로드합니다. 리소스 제한에 도달하도록 하는 너무 많은 병렬 처리를 피하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다. 
-   적절한 경우 분할된 테이블을 사용합니다.

## <a name="import-and-export-by-using-mysql-workbench"></a>MySQL Workbench를 사용하여 내보내기 및 가져오기
MySQL Workbench에는 데이터 내보내기 및 가져오기 방법이 2가지 있습니다. 각각 다른 용도로 사용됩니다. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>개체 브라우저의 바로 가기 메뉴에서 테이블 데이터 내보내기 및 가져오기 마법사
![개체 브라우저 바로 가기 메뉴의 MySQL Workbench 마법사](./media/concepts-migrate-import-export/p1.png)

테이블 데이터에 대한 마법사는 CSV 및 JSON 파일을 사용하여 가져오기 및 내보내기 작업을 지원합니다. 구분 기호, 열 선택 및 인코딩 선택과 같은 여러 가지 구성 옵션을 포함합니다. 로컬 또는 원격으로 연결된 MySQL 서버에 대해 각 마법사를 수행할 수 있습니다. 가져오기 작업은 테이블, 열 및 형식 매핑을 포함합니다. 

테이블을 마우스 오른쪽 단추로 클릭하여 개체 브라우저의 바로 가기 메뉴에서 이러한 마법사에 액세스할 수 있습니다. 그런 다음 **테이블 데이터 내보내기 마법사** 또는 **테이블 데이터 가져오기 마법사**를 선택합니다. 

#### <a name="table-data-export-wizard"></a>테이블 데이터 내보내기 마법사
다음 예제에서는 테이블을 CSV 파일로 내보냅니다. 
1. 내보낼 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다. 
2. **테이블 데이터 내보내기 마법사**를 선택합니다. 내보낼 열, 행 오프셋(있는 경우) 및 개수(있는 경우)를 선택합니다. 
3. **내보낼 데이터 선택** 페이지에서 **다음**을 클릭합니다. 파일 경로, CSV 또는 JSON 파일 형식을 선택합니다. 또한 선 구분 기호, 문자열을 묶는 방법 및 필드 구분 기호를 선택합니다. 
4. **출력 파일 위치 선택** 페이지에서 **다음**을 클릭합니다. 
5. **데이터 내보내기** 페이지에서 **다음**을 클릭합니다.

#### <a name="table-data-import-wizard"></a>테이블 데이터 가져오기 마법사
다음 예제에서는 CSV 파일에서 테이블을 가져옵니다.
1. 가져올 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다. 
2. 가져올 CSV 파일을 찾아서 선택한 후 **다음**을 클릭합니다. 
3. 대상 테이블(신규 또는 기존)을 선택하고 **가져오기 전에 테이블 자르기** 확인란을 선택하거나 선택 취소합니다. **다음**을 클릭합니다.
4. 가져올 인코딩 및 열을 선택한 후 **다음**을 클릭합니다. 
5. **데이터 가져오기** 페이지에서 **다음**을 클릭합니다. 마법사는 그에 따라 데이터를 가져옵니다.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>탐색기 창의 SQL 데이터 내보내기 및 가져오기 마법사
마법사를 사용하여 MySQL Workbench 또는 mysqldump 명령에서 생성된 SQL을 내보내거나 가져옵니다. **탐색기** 창에서 또는 주 메뉴에서 **서버**를 선택하여 이러한 마법사에 액세스합니다. 그런 다음 **데이터 내보내기** 또는 **데이터 가져오기**를 선택합니다. 

#### <a name="data-export"></a>데이터 내보내기
![탐색기 창을 사용하여 MySQL Workbench 데이터 내보내기](./media/concepts-migrate-import-export/p2.png)

**데이터 내보내기** 탭을 사용하여 MySQL 데이터를 내보낼 수 있습니다. 
1. 내보내려는 각 스키마를 선택하고, 필요에 따라 각 스키마에서 특정 스키마 개체/테이블을 선택하고, 내보내기를 생성합니다. 구성 옵션에는 프로젝트 폴더 또는 자체 포함된 SQL 파일로 내보내기가 포함되고, 저장된 루틴 및 이벤트를 덤프하거나 테이블 데이터를 건너뛰는 작업도 포함됩니다. 
 
   또는 **결과 집합 내보내기**를 사용하여 SQL 편집기에서 CSV, JSON, HTML 및 XML 등의 다른 형식으로 특정 결과 집합을 내보냅니다. 
3. 내보낼 데이터베이스 개체를 선택하고 관련 옵션을 구성합니다.
4. **새로 고침**을 클릭하여 현재 개체를 로드합니다.
5. 필요에 따라 내보내기 작업을 미세 조정하는 **고급 옵션** 탭을 엽니다. 예를 들어 테이블 잠금을 추가하고, insert 문 대신 replace를 사용하고, 억음 부호로 식별자를 묶는 작업을 수행할 수 있습니다.
6. **내보내기 시작**을 클릭하여 내보내기 프로세스를 시작합니다.


#### <a name="data-import"></a>데이터 가져오기
![관리 탐색 창을 사용하여 MySQL Workbench 데이터 가져오기](./media/concepts-migrate-import-export/p3.png)

**데이터 가져오기** 탭을 사용하여 데이터 내보내기 작업에서 또는 mysqldump 명령에서 내보낸 데이터를 가져오거나 복원할 수 있습니다. 
1. 프로젝트 폴더 또는 자체 포함된 SQL 파일을 선택하거나, 가져올 스키마를 선택하거나, **새로 만들기**를 선택하여 새 스키마를 정의합니다. 
2. **가져오기 시작**을 클릭하여 가져오기 프로세스를 시작합니다.

## <a name="next-steps"></a>다음 단계
- 다른 마이그레이션 방법으로 [Azure Database for MySQL에서 덤프 및 복원을 사용하여 MySQL 데이터베이스 마이그레이션](concepts-migrate-dump-restore.md)을 참조하세요.
- Azure Database for MySQL로 데이터베이스 마이그레이션에 대한 자세한 내용은 [데이터베이스 마이그레이션 가이드](https://aka.ms/datamigration)을 참조합니다. 
