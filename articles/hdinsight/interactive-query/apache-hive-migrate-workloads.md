---
title: HDInsight 4.0로 Azure HDInsight 3.6 Hive 워크 로드 마이그레이션
description: HDInsight 4.0 HDInsight 3.6의 Apache Hive 워크 로드를 마이그레이션하는 방법에 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 144e0ada0ce3a15a5dcd13b31f46f50162750a74
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126565"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>HDInsight 4.0로 Azure HDInsight 3.6 Hive 워크 로드 마이그레이션

이 문서에서는 HDInsight 4.0 HDInsight 3.6의 Apache Hive 및 LLAP 워크 로드를 마이그레이션하는 방법을 보여 줍니다. HDInsight 4.0 구체화 된 뷰 및 쿼리 결과 캐싱 같은 최신 Hive 및 LLAP 기능을 제공 합니다. HDInsight 4.0 워크 로드를 마이그레이션하는 경우에 HDInsight 3.6에서 사용할 수 없는 Hive 3의 많은 새로운 기능을 사용할 수 있습니다.

이 문서에서는 다음 주제를 다룹니다.

* 4.0 HDInsight Hive 메타 데이터의 마이그레이션
* ACID 및 비 ACID 테이블의 안전 하 게 마이그레이션
* HDInsight 버전에서 Hive 보안 정책의 유지
* 쿼리 실행 및 HDInsight 4.0 HDInsight 3.6에서 디버깅

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Apache Hive 메타 데이터를 HDInsight 4.0으로 마이그레이션

Hive의 장점 중 하나는 외부 데이터베이스 (Hive Metastore 라고도 함)에 메타 데이터를 내보내는 기능입니다. 합니다 **Hive Metastore** 테이블 통계를 테이블 저장소 위치, 열 이름 및 테이블 인덱스 정보를 포함 하 여 저장을 담당 합니다. Metastore 데이터베이스 스키마를 Hive 버전 간에 다릅니다. HDInsight 3.6 Hive Metastore를 HDInsight 4.0과 호환 되도록 업그레이드 하려면 다음을 수행 합니다.

1. 외부 metastore의 새 복사본을 만듭니다. HDInsight 3.6 및 HDInsight 4.0 다른 metastore 스키마가 필요 하 고 단일 metastore를 공유할 수 없습니다.
1. Metastore의 새 복사본을)를 기존 HDInsight 4.0 클러스터 또는 사용자가 처음으로 만드는 b) 클러스터에 연결 합니다. 참조 [Azure HDInsight에서 외부 메타 데이터 저장소를 사용 하 여](../hdinsight-use-external-metadata-stores.md) 외부 metastore를 HDInsight 클러스터에 연결 하는 방법에 대 한 자세한 내용을 보려면. Metastore 연결 되 면 자동으로 4.0 호환 metastore로 변환 됩니다.

> [!Warning]
> HDInsight 3.6 메타 데이터 스키마는 HDInsight 4.0 스키마로 변환 하는 업그레이드를 취소할 수 없습니다.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>4.0 HDInsight로 Hive 테이블 마이그레이션

이전 집합이 4.0 HDInsight Hive Metastore를 마이그레이션하는 단계를 완료 한 후 테이블 및 metastore에 기록 하는 데이터베이스에서 볼 수 있게 4.0 HDInsight 클러스터 내에서 실행 하 여 `show tables` 또는 `show databases` 에서 클러스터 내에서 . 참조 [쿼리 실행이 HDInsight 버전 간에](#query-execution-across-hdinsight-versions) 4.0 HDInsight 클러스터에서 쿼리 실행에 대 한 정보에 대 한 합니다.

그러나 클러스터에 필요한 저장소 계정에 액세스할 때까지 테이블의 실제 데이터는 액세스할 수 있는 없습니다. HDInsight 4.0 클러스터 이전 HDInsight 3.6 클러스터와 동일한 데이터에 액세스할 수 있도록 다음 단계를 수행 합니다.

1. Azure 저장소 계정의 테이블을 확인 하거나 사용 하 여 데이터베이스에 설명 포맷 합니다.
2. 4.0 HDInsight 클러스터에 이미 실행 중인 경우 Ambari 통해 클러스터에 Azure storage 계정을 연결 합니다. 4.0 HDInsight 클러스터를 아직 만들지 않은 경우 Azure storage 계정의 기본 또는 보조 클러스터 저장소 계정으로 지정 되어 있는지 확인 합니다. HDInsight 클러스터에 저장소 계정을 추가 하는 방법에 대 한 자세한 내용은 참조 하세요. [HDInsight에 추가 저장소 계정을 추가](../hdinsight-hadoop-add-storage.md)합니다.

> [!Note]
> 테이블은 HDInsight 3.6 및 HDInsight 4.0에서 다르게 처리 됩니다. 따라서 서로 다른 버전의 클러스터에 대 한 동일한 테이블을 공유할 수 없습니다. HDInsight 3.6 HDInsight 4.0으로 동시에 사용 하려는 경우 각 버전에 대 한 데이터를 별도 복사본이 있어야 합니다.

Hive 작업에는 다양 한 ACID 및 ACID이 아닌 테이블을 포함할 수 있습니다. 테이블에 대 한 ACID 규정 준수를가 하는 HDInsight 3.6 (Hive 2)에서 Hive 및 HDInsight (Hive 3) 4.0에는 Hive 간의 주요 차이점 중 하나입니다. HDInsight 3.6에서 추가 구성이 필요 Hive ACID 규정 준수를 사용 하도록 설정 되지만 HDInsight 4.0에서 테이블 ACID 규격 기본적입니다. 3.6 클러스터의 주요 압축 ACID 테이블에 대해 실행 되기 전에 필요한 유일한 작업입니다. Hive 보기에서 또는 Beeline에서 다음 쿼리를 실행 합니다.

```bash
alter table myacidtable compact 'major';
```

이 압축 HDInsight 3.6 및 HDInsight 4.0 ACID 테이블 ACID 델타를 다르게 이해 하기 때문에 필요 합니다. 압축은 일관성을 보장 하는 초기 상태를 적용 합니다. 섹션 4 개를 [마이그레이션 설명서를 Hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) HDInsight 3.6 ACID 테이블 대량 압축에 대 한 지침이 포함 되어 있습니다.

Metastore 마이그레이션 및 압축 단계를 완료 한 후에 실제 웨어하우스를 마이그레이션할 수 있습니다. Hive 웨어하우스 마이그레이션이 완료 HDInsight 4.0 웨어하우스 다음 속성을 갖습니다.

* HDInsight 3.6에서 외부 테이블은 HDInsight 4.0의 외부 테이블을 됩니다.
* HDInsight 3.6에서 관리 되는 테이블을 비트랜잭션 HDInsight 4.0의 외부 테이블 수 있습니다.
* HDInsight 3.6에서 트랜잭션 관리 되는 테이블은 HDInsight 4.0에서 관리 되는 테이블을 됩니다.

마이그레이션을 실행 하기 전에 웨어하우스가의 속성을 조정 해야 합니다. 예를 들어, 일부 테이블 (예:는 HDInsight 3.6 클러스터) 제 3 자에서 액세스할 수는 예상 되는 경우 해당 테이블 이어야 합니다 외부 마이그레이션이 완료 되 면 합니다. HDInsight 4.0에서는 모든 관리 되는 테이블은 트랜잭션. 따라서 HDInsight 4.0에서 관리 되는 테이블은 4.0 HDInsight 클러스터에만 액세스 해야 합니다.

테이블 속성 올바르게 설정 되 면 SSH 셸을 사용 하 여 클러스터 헤드 노드 중 하나에서 Hive 웨어하우스 마이그레이션 도구를 실행 합니다.

1. 클러스터 헤드 노드에 SSH를 사용 하 여에 연결 합니다. 자세한 내용은 [SSH를 사용 하 여 HDInsight에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 실행 하 여 Hive 사용자로 로그인 셸 열기 `sudo su - hive`
1. Hortonworks Data Platform 스택 버전을 실행 하 여 확인 `ls /usr/hdp`합니다. 다음 명령에서 사용 해야 하는 버전 문자열을 표시 됩니다.
1. 셸에서 다음 명령을 실행 합니다. 대체 `${{STACK_VERSION}}` 이전 단계에서 버전 문자열을 사용 하 여:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

마이그레이션 도구에는 다음이 완료 되 면 Hive 웨어하우스가 HDInsight 4.0에 대 한 준비 됩니다. 

> [!Important]
> HDInsight 4.0 (3.6에서 비롯 한 테이블 마이그레이션)에서 관리 되는 테이블에서 다른 서비스 또는 응용 프로그램을 HDInsight 3.6 클러스터를 포함 하 여 액세스할 수 없습니다.

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight 버전 간에 보안 Hive

HDInsight 3.6의 경우 이후 HDInsight는 HDInsight 엔터프라이즈 보안 패키지 (ESP)를 사용 하 여 Azure Active Directory와 통합 됩니다. ESP은 Kerberos 및 Apache Ranger를 사용 하 여 클러스터 내에서 특정 리소스의 사용 권한을 관리할 수 있습니다. 다음 단계를 사용 하 여 HDInsight 4.0 ranger 정책은 HDInsight 3.6에서 Hive에 대 한 배포를 마이그레이션할 수 있습니다.

1. HDInsight 3.6 클러스터에서 Service Manager Ranger 패널로 이동 합니다.
2. 명명 된 정책의 이동할 **HIVE** 및 json 파일로 정책 내보내기.
3. 내보낸된 정책 json에서 참조 하는 모든 사용자가 새 클러스터에 있는지 확인 합니다. 사용자 정책 json에서 참조 되는 새 클러스터에 존재 하지 않습니다 하지만 경우에 새 클러스터로 사용자를 추가 하거나 정책에서 참조를 제거 합니다.
4. 로 이동 합니다 **Ranger Service Manager** 4.0 HDInsight 클러스터에서 패널입니다.
5. 명명 된 정책으로 이동 **HIVE** 2 단계의 ranger 정책 json을 가져오고 있습니다.

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight 버전에서 쿼리 실행

실행 및 Hive/LLAP 쿼리는 HDInsight 3.6 클러스터 내에서 디버그 하는 방법은 두 가지입니다. HiveCLI 명령줄 환경을 제공 하 고 Tez 보기/Hive 뷰 GUI 기반 워크플로 제공 합니다.

HDInsight 4.0에서는 HiveCLI Beeline을 사용 하 여 대체 되었습니다. HiveCLI Hiveserver 1에 대 한 thrift 클라이언트 이며 Beeline은 JDBC 클라이언트 Hiveserver 2에 액세스할 수입니다. Beeline는 다른 JDBC 호환 데이터베이스 끝점에 연결할 데도 사용할 수 있습니다. Beeline은 사용할 수 있는의 기본 HDInsight 4.0에서 필요한 설치 없이 합니다.

HDInsight 3.6의 경우 Hive 서버와 상호 작용에 대 한 GUI 클라이언트에서는 Ambari Hive 보기입니다. 4.0 HDInsight Hive 보기 사용 하 여 Hortonworks Analytics Studio DAS (Data)를 대체합니다. DAS는 HDInsight 클러스터의 기본 제공 하지 않습니다 하 고 공식적으로 지원 되는 패키지를 않습니다. 그러나 DAS 설치할 수 있습니다 클러스터에서 다음과 같습니다.

실행에 대 한 노드 형식으로 "헤드 노드"를 사용 하 여 클러스터에 대 한 스크립트 작업을 시작 합니다. 다음 URI는 "Bash 스크립트 URI"로 표시 된 텍스트 상자에 붙여 넣습니다. https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh



DAS를 설치한 후 쿼리 뷰어에서 실행 한 쿼리에 표시 되지 않는 경우, 다음 단계를 수행 합니다.

1. 에 설명 된 대로 Hive 및 Tez에 DAS 구성을 설정할 [DAS 설치 문제 해결에 대 한이 가이드](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)합니다.
2. 다음 Azure storage 디렉터리 configs 페이지 blob는 있는지는 아래 나열 된 `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 두 헤드 노드에서 HDFS, Hive, Tez, 및 DAS를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight 4.0 알림](../hdinsight-version-release.md)
* [HDInsight 4.0 심층 분석](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 테이블](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
