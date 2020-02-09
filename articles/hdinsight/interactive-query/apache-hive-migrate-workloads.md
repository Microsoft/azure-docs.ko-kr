---
title: HDInsight 4.0로 Azure HDInsight 3.6 Hive 워크 로드 마이그레이션
description: HDInsight 3.6에서 Apache Hive 작업을 HDInsight 4.0로 마이그레이션하는 방법에 대해 알아봅니다.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: eceb4b312476d701ec8ce4eb0ce4886621824b3a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841594"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>HDInsight 4.0로 Azure HDInsight 3.6 Hive 워크 로드 마이그레이션

이 문서에서는 HDInsight 3.6에서 Apache Hive 및 LLAP 워크 로드를 HDInsight 4.0로 마이그레이션하는 방법을 보여 줍니다. HDInsight 4.0은 구체화 된 뷰 및 쿼리 결과 캐싱과 같은 새로운 Hive 및 LLAP 기능을 제공 합니다. 작업을 HDInsight 4.0로 마이그레이션하는 경우 HDInsight 3.6에서 사용할 수 없는 Hive 3의 최신 기능을 많이 사용할 수 있습니다.

이 문서에서는 다음 주제를 다룹니다.

* HDInsight 4.0로 Hive 메타 데이터 마이그레이션
* ACID 및 비 ACID 테이블의 안전 마이그레이션
* HDInsight 버전 간 Hive 보안 정책 유지
* HDInsight 3.6에서 HDInsight 4.0로 쿼리 실행 및 디버깅

Hive의 이점 중 하나는 외부 데이터베이스 (Hive Metastore 이라고 함)로 메타 데이터를 내보내는 기능입니다. **Hive Metastore** 는 테이블 저장소 위치, 열 이름 및 테이블 인덱스 정보를 포함 하 여 테이블 통계를 저장 하는 일을 담당 합니다. Metastore 데이터베이스 스키마는 Hive 버전 마다 다릅니다. Hive metastore를 안전 하 게 업그레이드 하는 권장 방법은 복사본을 만들고 현재 프로덕션 환경 대신 복사본을 업그레이드 하는 것입니다.

## <a name="copy-metastore"></a>Metastore 복사

HDInsight 3.6 및 HDInsight 4.0에는 다른 metastore 스키마가 필요 하며 단일 metastore를 공유할 수 없습니다.

### <a name="external-metastore"></a>외부 metastore

외부 metastore의 새 복사본을 만듭니다. 외부 metastore를 사용 하는 경우 metastore의 복사본을 만드는 안전 하 고 쉬운 방법 중 하나는 SQL Database restore 함수를 사용 하 여 다른 이름으로 [데이터베이스를 복원](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) 하는 것입니다.  외부 metastore를 HDInsight 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [Azure hdinsight에서 외부 메타 데이터 저장소 사용](../hdinsight-use-external-metadata-stores.md) 을 참조 하세요.

### <a name="internal-metastore"></a>내부 metastore

내부 metastore를 사용 하는 경우 쿼리를 사용 하 여 Hive metastore에서 개체 정의를 내보내고 새 데이터베이스로 가져올 수 있습니다.

1. [Secure Shell (SSH) 클라이언트](../hdinsight-hadoop-linux-use-ssh-unix.md)를 사용 하 여 HDInsight 클러스터에 연결 합니다.

1. 다음 명령을 입력 하 여 열려 있는 SSH 세션에서 [Beeline 클라이언트](../hadoop/apache-hadoop-use-hive-beeline.md) 를 사용 하 여 HiveServer2에 연결 합니다.

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    이 명령은 **alltables.sql**이라는 파일을 생성 합니다. 기본 데이터베이스를 삭제 하거나 다시 만들 수 없으므로 **alltables.sql**에서 `create database default;` 문을 제거 하십시오.

1. SSH 세션을 종료 합니다. 그런 다음 scp 명령을 입력 하 여 **alltables.sql** 를 로컬로 다운로드 합니다.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. *새* HDInsight 클러스터에 **alltables.sql** 를 업로드 합니다.

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 그런 다음 SSH를 사용 하 여 *새* HDInsight 클러스터에 연결 합니다. SSH 세션에서 다음 코드를 실행 합니다.

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>업그레이드 metastore

Metastore **복사가** 완료 되 면 기존 HDInsight 3.6 클러스터의 [스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md) 에서 스키마 업그레이드 스크립트를 실행 하 여 새 metastore을 Hive 3 스키마로 업그레이드 합니다. 이를 통해 데이터베이스를 HDInsight 4.0 metastore에 연결할 수 있습니다.

아래 표에 있는 값을 사용 합니다. `SQLSERVERNAME DATABASENAME USERNAME PASSWORD`를 **복사** 된 Hive metastore에 대 한 적절 한 값으로 바꾸고 공백으로 구분 합니다. SQL server 이름을 지정할 때 ". database.windows.net"를 포함 하지 마십시오.

|속성 | 값 |
|---|---|
|스크립트 유형|- 사용자 지정|
|이름|Hive 업그레이드|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|노드 유형|Head|
|매개 변수|SQLSERVERNAME DATABASENAME 사용자 이름 암호|

> [!Warning]  
> HDInsight 3.6 메타 데이터 스키마를 HDInsight 4.0 스키마로 변환 하는 업그레이드는 되돌릴 수 없습니다.

데이터베이스에 대해 다음 sql 쿼리를 실행 하 여 업그레이드를 확인할 수 있습니다.

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>HDInsight 4.0로 Hive 테이블 마이그레이션

이전 단계를 완료 하 여 Hive Metastore를 HDInsight 4.0로 마이그레이션한 후에는 클러스터 내에서 `show tables` 또는 `show databases`를 실행 하 여 HDInsight 4.0 클러스터 내에서 Metastore에 기록 된 테이블 및 데이터베이스를 볼 수 있습니다. HDInsight 4.0 클러스터에서 쿼리 실행에 대 한 자세한 내용은 [hdinsight 버전 간 쿼리 실행](#query-execution-across-hdinsight-versions) 을 참조 하세요.

그러나 클러스터에 필요한 저장소 계정에 대 한 액세스 권한이 있는 경우에는 테이블의 실제 데이터에 액세스할 수 없습니다. HDInsight 4.0 클러스터가 이전 HDInsight 3.6 클러스터와 동일한 데이터에 액세스할 수 있도록 하려면 다음 단계를 완료 합니다.

1. 테이블이 나 데이터베이스의 Azure storage 계정을 확인 합니다.

1. HDInsight 4.0 클러스터가 이미 실행 중인 경우 Ambari를 통해 Azure storage 계정을 클러스터에 연결 합니다. HDInsight 4.0 클러스터를 아직 만들지 않은 경우 Azure storage 계정이 기본 또는 보조 클러스터 저장소 계정으로 지정 되어 있는지 확인 합니다. HDInsight 클러스터에 저장소 계정을 추가 하는 방법에 대 한 자세한 내용은 [hdinsight에 추가 저장소 계정 추가](../hdinsight-hadoop-add-storage.md)를 참조 하세요.

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>새 HDInsight 4.0을 배포 하 고 새 metastore에 연결

스키마 업그레이드가 완료 되 면 새 HDInsight 4.0 클러스터를 배포 하 고 업그레이드 된 metastore를 연결 합니다. 4\.0을 이미 배포한 경우 Ambari에서 metastore에 연결할 수 있도록 설정 합니다.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>HDInsight 4.0에서 스키마 마이그레이션 스크립트 실행

테이블은 HDInsight 3.6 및 HDInsight 4.0에서 다르게 처리 됩니다. 따라서 서로 다른 버전의 클러스터에 대해 동일한 테이블을 공유할 수 없습니다. Hdinsight 4.0와 동시에 HDInsight 3.6을 사용 하려면 각 버전에 대 한 별도의 데이터 복사본이 있어야 합니다.

Hive 워크 로드에 ACID 및 비 ACID 테이블을 혼합 하 여 포함할 수 있습니다. Hdinsight 3.6 (Hive 2)의 hive와 HDInsight 4.0 (Hive 3)의 Hive 간의 한 가지 주요 차이점은 테이블에 대 한 ACID 규정 준수입니다. HDInsight 3.6에서 Hive ACID 규정 준수를 사용 하도록 설정 하려면 추가 구성이 필요 하지만 HDInsight 4.0 테이블은 기본적으로 ACID 규격입니다. 마이그레이션 전에 유일 하 게 필요한 작업은 3.6 클러스터의 ACID 테이블에 대해 주요 압축을 실행 하는 것입니다. Hive 보기 또는 Beeline에서 다음 쿼리를 실행 합니다.

```sql
alter table myacidtable compact 'major';
```

이러한 압축은 HDInsight 3.6 및 HDInsight 4.0 ACID 테이블이 ACID 델타를 다르게 인식 하기 때문에 필요 합니다. 압축은 일관성을 보장 하는 깨끗 한 슬레이트를 적용 합니다. [Hive 마이그레이션 설명서](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) 의 4 단원에는 HDINSIGHT 3.6 ACID 테이블을 대량 압축 하는 방법에 대 한 지침이 포함 되어 있습니다.

Metastore 마이그레이션과 압축 단계를 완료 한 후에는 실제 웨어하우스를 마이그레이션할 수 있습니다. Hive 웨어하우스 마이그레이션을 완료 한 후 HDInsight 4.0 웨어하우스에는 다음과 같은 속성이 있습니다.

|3.6 |4.0 |
|---|---|
|외부 테이블|외부 테이블|
|비트랜잭션 관리 테이블|외부 테이블|
|트랜잭션 관리 테이블|관리 되는 테이블|

마이그레이션을 실행 하기 전에 웨어하우스의 속성을 조정 해야 할 수 있습니다. 예를 들어 타사에서 일부 테이블에 액세스 하는 경우 (예: HDInsight 3.6 클러스터) 마이그레이션이 완료 되 면 해당 테이블은 외부에 있어야 합니다. HDInsight 4.0에서 관리 되는 모든 테이블은 트랜잭션입니다. 따라서 HDInsight 4.0의 관리 되는 테이블은 HDInsight 4.0 클러스터 에서만 액세스 해야 합니다.

테이블 속성이 올바르게 설정 되 면 SSH 셸을 사용 하 여 클러스터 헤드 노드 중 하나에서 Hive 웨어하우스 마이그레이션 도구를 실행 합니다.

1. SSH를 사용 하 여 클러스터 헤드 노드에 연결 합니다. 지침은 [SSH를 사용 하 여 HDInsight에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md) 을 참조 하세요.
1. `sudo su - hive`를 실행 하 여 Hive 사용자로 로그인 셸을 엽니다.
1. `ls /usr/hdp`를 실행 하 여 데이터 플랫폼 스택 버전을 확인 합니다. 그러면 다음 명령에서 사용 해야 하는 버전 문자열이 표시 됩니다.
1. 셸에서 다음 명령을 실행 합니다. `STACK_VERSION`을 이전 단계의 버전 문자열로 바꿉니다.

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

마이그레이션 도구가 완료 되 면 Hive 웨어하우스가 HDInsight 4.0에 대해 준비 됩니다.

> [!Important]  
> Hdinsight 3.6 클러스터를 비롯 한 다른 서비스 또는 응용 프로그램에서는 HDInsight 4.0 (3.6에서 마이그레이션된 테이블 포함)의 관리 되는 테이블에 액세스 하지 않아야 합니다.

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight 버전 간 Hive 보안

Hdinsight 3.6부터 hdinsight는 Enterprise Security Package (ESP)를 사용 하 여 Azure Active Directory와 통합 됩니다. ESP는 Kerberos 및 Apache 레인저를 사용 하 여 클러스터 내의 특정 리소스에 대 한 권한을 관리 합니다. 다음 단계를 수행 하 여 HDInsight 3.6의 Hive에 대해 배포 된 레인저 정책을 HDInsight 4.0로 마이그레이션할 수 있습니다.

1. HDInsight 3.6 클러스터의 레인저 Service Manager 패널로 이동 합니다.
2. **HIVE** 라는 정책으로 이동 하 여 정책을 json 파일로 내보냅니다.
3. 내보낸 정책 json에서 참조 하는 모든 사용자가 새 클러스터에 존재 하는지 확인 합니다. 사용자가 정책 json에서 참조 되지만 새 클러스터에 존재 하지 않는 경우 새 클러스터에 사용자를 추가 하거나 정책에서 참조를 제거 합니다.
4. HDInsight 4.0 클러스터의 **레인저 Service Manager** 패널로 이동 합니다.
5. **HIVE** 라는 정책으로 이동 하 고 2 단계에서 레인저 정책 json을 가져옵니다.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>테스트 앱에서 필요에 따라 호환성 및 수정 코드 확인

기존 프로그램 및 쿼리와 같은 작업을 마이그레이션하는 경우 릴리스 정보 및 설명서에서 변경 내용을 확인 하 고 필요에 따라 변경 내용을 적용 하세요. HDInsight 3.6 클러스터가 공유 Spark 및 Hive metastore를 사용 하는 경우 [Hive 웨어하우스 커넥터를 사용 하 여 추가 구성이](./apache-hive-warehouse-connector.md) 필요 합니다.

## <a name="deploy-new-app-for-production"></a>프로덕션을 위해 새 앱 배포

새 클러스터로 전환 하려는 경우, 예를 들어 새 클라이언트 응용 프로그램을 설치 하 여 새 프로덕션 환경으로 사용 하거나 기존 클라이언트 응용 프로그램을 업그레이드 하 고 HDInsight 4.0로 전환할 수 있습니다.

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4.0을 프로덕션 환경으로 전환

테스트 하는 동안 metastore에서 차이점이 생성 된 경우 전환 하기 전에 변경 내용을 업데이트 해야 합니다. 이 경우 metastore를 내보낸 & 가져온 다음 다시 업그레이드할 수 있습니다.

## <a name="remove-the-old-production"></a>이전 프로덕션 제거

릴리스가 완료 되 고 완벽 하 게 작동 하는지 확인 한 후 버전 3.6 및 이전 metastore를 제거할 수 있습니다. 환경을 삭제 하기 전에 모든 항목이 마이그레이션 되었는지 확인 하세요.

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight 버전 간 쿼리 실행

HDInsight 3.6 클러스터 내에서 Hive/LLAP 쿼리를 실행 하 고 디버그 하는 방법에는 두 가지가 있습니다. HiveCLI는 명령줄 환경을 제공 하며 Tez 뷰/Hive 보기는 GUI 기반 워크플로를 제공 합니다.

HDInsight 4.0에서 HiveCLI는 Beeline로 대체 되었습니다. HiveCLI은 Hiveserver 1의 thrift 클라이언트 이며, Beeline는 Hiveserver 2에 대 한 액세스를 제공 하는 JDBC 클라이언트입니다. Beeline를 사용 하 여 다른 JDBC 호환 데이터베이스 엔드포인트에 연결할 수도 있습니다. Beeline는 설치 하지 않아도 HDInsight 4.0에서 기본 제공 됩니다.

HDInsight 3.6에서 Hive 서버와 상호 작용 하는 GUI 클라이언트는 Ambari Hive 뷰입니다. HDInsight 4.0은 Ambari View와 함께 제공 되지 않습니다. 고객이 핵심 HDInsight 서비스가 아닌 DAS (Data Analytics Studio)를 사용할 수 있는 방법을 제공 하 고 있습니다. DAS는 HDInsight 클러스터에 기본 제공 되지 않으며 공식적으로 지원 되는 패키지가 아닙니다. 그러나 다음과 같이 [스크립트 작업](../hdinsight-hadoop-customize-cluster-linux.md) 을 사용 하 여 클러스터에 DAS를 설치할 수 있습니다.

|속성 | 값 |
|---|---|
|스크립트 유형|- 사용자 지정|
|이름|DAS|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|노드 유형|Head|

10 ~ 15 분 정도 기다린 다음이 URL을 사용 하 여 데이터 분석 스튜디오를 시작 합니다. `https://CLUSTERNAME.azurehdinsight.net/das/`.

DAS에 액세스 하기 전에 Ambari UI를 새로 고치거 나 모든 Ambari 구성 요소를 다시 시작 해야 할 수 있습니다.

DAS가 설치 되 면 쿼리 뷰어에서 실행 한 쿼리가 표시 되지 않으면 다음 단계를 수행 합니다.

1. [Das 설치 문제 해결에 대 한이 가이드](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)에 설명 된 대로 Hive, TEZ 및 das에 대 한 구성을 설정 합니다.
2. 다음 Azure storage 디렉터리 configs가 페이지 blob이 고 `fs.azure.page.blob.dirs`아래에 나열 되어 있는지 확인 합니다.
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 두 헤드 노드에서 HDFS, Hive, Tez 및 DAS를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight 4.0 공지](../hdinsight-version-release.md)
* [HDInsight 4.0 심층 이해](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 테이블](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
