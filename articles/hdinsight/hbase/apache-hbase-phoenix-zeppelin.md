---
title: Apache Phoenix를 사용하여 Azure HDInsight에서 Apache Base 쿼리 실행
description: Apache Zeppelin을 사용하여 Phoenix로 Apache Base 쿼리를 실행하는 방법을 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: ff963e661a2b258c1eb452ed63f41f4e7d84c6a0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867783"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Apache Zeppelin을 사용하여 Azure HDInsight에서 Apache HBase를 통해 Apache Phoenix 쿼리 실행

Apache Phoenix는 HBase에서 구축되는 오픈 소스 대규모 병렬 관계형 데이터베이스 계층입니다. Phoenix를 사용하면 HBase를 통해 SQL 유사 쿼리를 사용할 수 있습니다. Phoenix는 아래의 JDBC 드라이버를 사용하여 SQL 테이블, 인덱스, 뷰 및 시퀀스를 만들고 삭제하고 변경할 수 있습니다.  Phoenix를 사용하여 개별적으로 또는 대량으로 행을 업데이트할 수도 있습니다. Phoenix는 MapReduce를 사용하여 쿼리를 컴파일하는 대신, NOSQL 네이티브 컴파일을 사용하여 HBase 위에 대기 시간이 짧은 애플리케이션을 만들 수 있도록 합니다.

Apache Zeppelin는 오픈 소스 웹 기반 노트북으로, 대화형 데이터 분석 및 SQL, Scala 등의 언어를 사용하여 데이터 기반 공동 작업 문서를 만들 수 있습니다. 데이터 개발자와 데이터 과학자가 데이터 조작을 위한 코드를 개발, 구성, 실행 및 공유하는 데 도움이 됩니다. 명령줄을 참조하거나 클러스터 세부 정보를 필요로 하지 않고 결과를 시각화할 수 있습니다.

HDInsight 사용자는 Apache Zeppelin를 사용하여 Phoenix 테이블을 쿼리할 수 있습니다. Apache Zeppelin은 HDInsight 클러스터와 통합되며 이를 사용하는 추가 단계가 없습니다. JDBC 인터프리터를 사용하여 Zeppelin Notebook을 만들고 Phoenix SQL 쿼리 작성을 시작하기만 하면 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

HDInsight의 Apache HBase 클러스터. [Apache HBase 시작](./apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin 노트 만들기

1. `CLUSTERNAME`을 다음 URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`에서 클러스터의 이름으로 바꿉니다. 그런 다음, 웹 브라우저에 URL을 입력합니다. 클러스터 로그인 사용자 이름 및 암호를 입력합니다.

1. Zeppelin 페이지에서 **새 메모 만들기** 를 선택합니다.

   :::image type="content" source="./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png" alt-text="HDInsight 대화형 쿼리 zeppelin" border="true":::

1. **새 메모 만들기** 대화 상자에서 다음 값을 입력하거나 선택합니다.

   - 노트 이름: 노트의 이름을 입력합니다.
   - 기본 인터프리터: 드롭다운 목록에서 **jdbc** 를 선택합니다.

   **메모 만들기** 를 선택합니다.

1. 노트북 헤더에 연결된 상태가 표시되는지 확인합니다. 오른쪽 상단 모서리에 녹색 점으로 표시됩니다.

   :::image type="content" source="./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png" alt-text="Zeppelin 노트북 상태" border="true":::

1. HBase 테이블을 만듭니다. 다음 명령을 입력한 후 **Shift+Enter** 키를 누릅니다.

   ```sql
   %jdbc(phoenix)
   CREATE TABLE Company (
       company_id INTEGER PRIMARY KEY,
       name VARCHAR(225)
   );
   ```

   첫 번째 줄의 **%jdbc(phoenix)** 문은 노트북이 Phoenix JDBC 인터프리터를 사용한다는 것을 나타냅니다.

1. 생성된 테이블을 봅니다.

   ```sql
   %jdbc(phoenix)
   SELECT DISTINCT table_name
   FROM SYSTEM.CATALOG
   WHERE table_schem is null or table_schem <> 'SYSTEM';
   ```

1. 테이블에 값을 삽입합니다.

   ```sql
   %jdbc(phoenix)
   UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
   UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
   ```

1. 테이블을 쿼리합니다.

   ```sql
   %jdbc(phoenix)
   SELECT * FROM dbo.Company;
   ```

1. 레코드를 삭제합니다.

   ```sql
   %jdbc(phoenix)
   DELETE FROM dbo.Company WHERE COMPANY_ID=1;
   ```

1. 테이블을 삭제합니다.

   ```sql
   %jdbc(phoenix)
   DROP TABLE dbo.Company;
   ```

## <a name="next-steps"></a>다음 단계

- [이제 Apache Phoenix는 Azure HDInsight에서 Zeppelin을 지원합니다](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix 문법](https://phoenix.apache.org/language/index.html)