---
title: 온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 데이터 마이그레이션 모범 사례
description: 온-프레미스 Hadoop 클러스터를 Azure HDInsight로 마이그레이션하는 데이터 마이그레이션 모범 사례를 알아봅니다.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 732cb118b7a0eebdbf28c7d7fe6ced435ce7920e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129249"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 데이터 마이그레이션 모범 사례

이 문서에서는 데이터를 Azure HDInsight로 마이그레이션하기 위한 권장 사항을 제공합니다. 온-프레미스 Apache Hadoop 시스템을 Azure HDInsight로 마이그레이션하는 데 도움을 주는 모범 사례를 제공하는 시리즈의 일부입니다.

## <a name="migrate-on-premises-data-to-azure"></a>온-프레미스 데이터를 Azure로 마이그레이션

온-프레미스에서 Azure 환경으로 데이터를 마이그레이션하는 두 가지 주요 옵션이 있습니다.

1.  TLS를 사용하여 네트워크를 통해 데이터 전송
    1. 인터넷 - Azure Storage 탐색기, AzCopy, Azure Powershell, Azure CLI 등의 여러 도구 중 하나를 사용하여 일반 인터넷 연결을 통해 데이터를 Azure Storage로 전송할 수 있습니다.  자세한 내용은 [Azure Storage의 데이터 이동](../../storage/common/storage-moving-data.md)을 참조하세요.
    2. ExpressRoute - ExpressRoute는 온-프레미스 또는 공동 장소 시설에 있는 인프라와 Microsoft 데이터 센터 간에 비공개 연결을 만들어 주는 Azure 서비스입니다. ExpressRoute 연결은 공용 인터넷을 사용하지 않으며 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다. 자세한 내용은 [ExpressRoute 회로 만들기 및 수정](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)을 참조하세요.
    1. Data Box 온라인 데이터 전송 - Data Box Edge 및 Data Box Gateway는 사용자 사이트와 Azure 간에 데이터를 관리하는 네트워크 스토리지 게이트웨이로 작동하는 온라인 데이터 전송 제품입니다. 온-프레미스 네트워크 디바이스인 Data Box Edge는 Azure와 데이터를 주고받으며 AI(인공 지능) 지원 에지 컴퓨팅을 사용하여 데이터를 처리합니다. Data Box Gateway는 저장소 게이트웨이 기능이 포함된 가상 어플라이언스입니다. 자세한 내용은 [Azure Data Box 설명서 - 온라인 전송](https://docs.microsoft.com/azure/databox-online/)을 참조하세요.
1.  오프라인 데이터 배송
    1. Data Box 오프라인 데이터 전송 - 네트워크를 사용할 수 없는 경우 Data Box, Data Box Disk 및 Data Box Heavy 디바이스를 통해 대량 데이터를 Azure로 전송할 수 있습니다. 이러한 오프라인 데이터 전송 디바이스는 사용자 조직과 Azure 데이터 센터 간에 배송됩니다. 이러한 디바이스는 AES 암호화를 사용하여 전송 중인 데이터를 보호할 수 있고, 철저한 업로드 후 삭제 프로세스를 진행하여 디바이스에서 데이터를 삭제합니다. 오프 라인 전송 Data Box 장치에 대 한 자세한 내용은 참조 하세요. [Azure Data Box 설명서-오프 라인 전송](https://docs.microsoft.com/azure/databox/)합니다. Hadoop 클러스터의 마이그레이션에 대 한 자세한 내용은 [온-프레미스 HDFS 저장소에서 Azure Storage로 마이그레이션에 사용 하 여 Azure Data Box](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)합니다.

다음 표에는 데이터 볼륨 및 네트워크 대역폭에 따른 대략적인 데이터 전송 기간이 정리되어 있습니다. 데이터 마이그레이션 예상 시간이 3주를 초과하는 경우 Data Box를 사용합니다.

|**데이터 수량**|**네트워크 대역폭**||||
|---|---|---|---|---|
|| **45Mbps(T3)**|**100Mbps**|**1Gbps**|**10Gbps**|
|1TB|2일|1일| 2시간|14분|
|10TB|22일|10일|1일|2시간|
|35TB|76일|34일|3일|8시간|
|80TB|173일|78일|8일|19시간|
|100TB|216일|97일|10일|1일|
|200TB|1년|194일|19일|2일|
|500TB|3년|1년|49일|5일|
|1PB|6년|3년|97일|10일|
|2PB|12년|5년|194일|19일|

Apache Hadoop DistCp, Azure Data Factory, AzureCp 같은 Azure 기본 도구를 사용하여 네트워크를 통해 데이터를 전송할 수 있습니다. 타사 도구 WANDisco를 같은 용도로 사용할 수도 있습니다. Apache Kafka Mirrormaker 및 Apache Sqoop는 온-프레미스에서 Azure Storage 시스템으로 데이터를 지속적으로 전송하는 데 사용할 수 있습니다.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Apache Hadoop DistCp 사용 시 성능 고려 사항


DistCp는 MapReduce 맵 작업을 사용하여 데이터를 전송하고 오류를 처리하고 오류를 복구하는 Apache 프로젝트입니다. DistCp는 원본 파일 목록을 각 맵 작업에 할당합니다. 그러면 맵 작업에서는 할당된 모든 파일을 대상에 복사합니다. DistCp 성능을 향상할 수 있는 여러 기술이 있습니다.

### <a name="increase-the-number-of-mappers"></a>매퍼 수 늘리기

DistCp는 각 복사본의 바이트 수가 거의 비슷하도록 맵 작업을 만듭니다. 기본적으로 DistCp 작업은 매퍼 20개를 사용합니다. Distcp에 더 많은 매퍼를 사용하면(명령줄에서 'm' 매개 변수 사용) 데이터 전송 중에 프로세스 병렬 처리가 증가하고 데이터 전송 시간이 감소합니다. 그러나 매퍼 수를 늘릴 때 두 가지 사항을 고려해야 합니다.

1. DistCp의 가장 낮은 세분성 단위는 단일 파일입니다. 원본 파일보다 많은 매퍼를 지정하면 가용 클러스터 리소스가 낭비됩니다.
1. 클러스터에서 사용 가능한 Yarn 메모리를 고려하여 매퍼 수를 결정해야 합니다. 각 맵 작업은 Yarn 컨테이너로 시작됩니다. 클러스터에서 다른 헤비 워크로드가 실행되지 않는다고 가정할 때, m = (작업자 노드 수 \* 작업자 노드당 YARN 메모리) / YARN 컨테이너 크기 공식을 사용하여 매퍼 수를 결정할 수 있습니다. 그러나 다른 애플리케이션에서 메모리를 사용하는 경우 YARN 메모리에서 DistCp 작업에 관련된 부분만 사용하도록 선택해야 합니다.

### <a name="use-more-than-one-distcp-job"></a>2개 이상의 DistCp 작업 사용

이동할 데이터 세트의 크기가 1TB를 초과하는 경우 2개 이상의 DistCp 작업을 사용합니다. 2개 이상의 작업을 사용하면 오류의 영향이 제한됩니다. 작업이 실패하면 모든 작업이 아닌 해당 작업만 다시 시작하면 됩니다.

### <a name="consider-splitting-files"></a>파일 분할을 고려

소수의 대용량 파일이 있는 경우 256MB 파일 청크로 분할하여 더 많은 매퍼로 동시성을 높이는 방안을 고려해볼 수 있습니다.

### <a name="use-the-strategy-command-line-parameter"></a>'strategy' 명령줄 매개 변수를 사용합니다.

명령줄에서 `strategy = dynamic` 매개 변수 사용을 고려합니다. `strategy` 매개 변수의 기본값은 각 맵 복사본의 바이트 수가 거의 동일한 `uniform size`입니다. 이 매개 변수를 `dynamic`으로 변경하면 목록 파일이 여러 "청크 파일"로 분할됩니다. 청크 파일의 수는 맵 수의 배수입니다. 맵 작업마다 청크 파일 하나가 할당됩니다. 청크의 모든 경로가 처리되면 현재 청크가 삭제되고 새 청크를 가져옵니다. 청크가 더 이상 없을 때까지 프로세스가 계속 진행됩니다. 이 "동적" 접근 방식은 빠른 맵 작업을 통해 느린 맵 작업보다 더 많은 경로를 사용하므로 전체 DistCp 작업 속도가 빨라집니다.

### <a name="increase-the-number-of-threads"></a>스레드 수 늘리기

`-numListstatusThreads` 매개 변수를 늘리면 성능이 향상되는지 알아보세요. 이 매개 변수는 파일 목록 작성에 사용할 스레드 수를 제어하며 최댓값은 40입니다.

### <a name="use-the-output-committer-algorithm"></a>출력 커밋터 알고리즘 사용

`-Dmapreduce.fileoutputcommitter.algorithm.version=2` 매개 변수를 전달하면 DistCp 성능이 향상되는지 알아보세요. 이 출력 커밋터 알고리즘은 대상에 출력 파일을 쓰는 기능과 관련하여 최적화되었습니다. 다음 명령은 여러 매개 변수의 사용 방법을 보여주는 예제입니다.

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>메타데이터 마이그레이션

### <a name="apache-hive"></a>Apache Hive

Hive metastore는 스크립트를 사용하여 또는 DB 복제를 사용하여 마이그레이션할 수 있습니다.

#### <a name="hive-metastore-migration-using-scripts"></a>스크립트를 사용하여 Hive metastore 마이그레이션

1. 온-프레미스 Hive metastore에서 Hive Ddl을 생성 합니다. 이 단계는 [래퍼 Bash 스크립트](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)를 사용하여 수행할 수 있습니다.
1. 생성된 DDL을 편집하여 HDFS url을 WASB/ADLS/ABFS URL로 바꿉니다.
1. HDInsight 클러스터의 metastore에서 업데이트된 DDL을 실행합니다.
1. Hive metastore 버전이 온-프레미스와 클라우드 간에 호환되는지 확인합니다.

#### <a name="hive-metastore-migration-using-db-replication"></a>DB 복제를 사용하여 Hive metastore 마이그레이션

- 온-프레미스 Hive metastore DB와 HDInsight metastore DB 간에 데이터베이스 복제를 설정합니다.
- "Hive MetaTool"을 사용하여 HDFS url을 WASB/ADLS/ABFS url로 바꿉니다. 예:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- 온-프레미스 Ranger 정책을 xml 파일로 내보냅니다.
- 온-프레미스 HDFS 기반 특정 경로 XSLT와 같은 도구를 사용 하 여 WASB/ADLS를 변환 합니다.
- HDInsight에서 실행되는 Ranger로 정책을 가져옵니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서를 읽어보세요.

- [온-프레미스에서 Azure HDInsight Hadoop으로 데이터 마이그레이션 보안 및 DevOps 모범 사례](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
