---
title: 온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 저장소 모범 사례
description: 온-프레미스 Hadoop 클러스터를 Azure HDInsight로 마이그레이션하는 저장소 모범 사례를 알아봅니다.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: c62a5384edf66fd9309bc7afcb50ada48e3fca7d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095288"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 저장소 모범 사례

이 문서에서는 Azure HDInsight 시스템에서 데이터 저장소에 대한 권장 사항을 제공합니다. 온-프레미스 Apache Hadoop 시스템을 Azure HDInsight로 마이그레이션하는 데 도움을 주는 모범 사례를 제공하는 시리즈의 일부입니다.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>HDInsight 클러스터에 대한 올바른 스토리지 시스템 선택

Azure Storage 또는 Azure Data Lake Storage에 온-프레미스 HDFS(Apache Hadoop 파일 시스템) 디렉터리 구조를 다시 만들 수 있습니다. 그런 다음, 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다. 두 서비스는 HDInsight 클러스터에 대한 기본 파일 시스템 및 추가 파일 시스템으로 사용할 수 있습니다. HDInsight 클러스터와 저장소 계정은 동일한 지역에서 호스트되어야 합니다.

### <a name="azure-storage"></a>Azure 저장소

HDInsight 클러스터는 기본 파일 시스템 또는 추가 파일 시스템으로 Azure Storage에서 Blob 컨테이너를 사용할 수 있습니다. 표준 계층 저장소 계정은 HDInsight 클러스터와 함께 사용하도록 지원됩니다. 프리미어 계층은 지원되지 않습니다. 기본 Blob 컨테이너는 작업 기록 및 로그와 같은 클러스터 특정 정보를 저장합니다. 여러 클러스터에 대해 하나의 Blob 컨테이너를 기본 파일 시스템으로 공유하는 것은 지원되지 않습니다.

만들기 프로세스에서 정의된 저장소 계정과 해당 키는 클러스터 노드의 `%HADOOP_HOME%/conf/core-site.xml`에 저장됩니다. 또한 Ambari UI에서 HDFS 구성의 "사용자 지정 핵심 사이트" 섹션 아래에서 액세스할 수 있습니다. 저장소 계정 키는 기본적으로 암호화되며 Hadoop 디먼에 전달되기 전에 키를 암호 해독하는 데 사용자 지정 암호 해독 스크립트가 사용됩니다. Hive, MapReduce, Hadoop 스트리밍 및 Pig를 비롯한 작업은 저장소 계정 및 메타데이터 설명을 전달합니다.

Azure 저장소를 지역에서 복제할 수 있습니다. 지역에서 복제는 지리적 복구와 데이터 중복 기능을 제공하지만, 지역에서 복제된 위치에 대한 장애 조치(failover)는 성능에 심각한 영향을 미치게 되며 추가 비용을 발생시킬 수 있습니다. 지역에서 복제 기능은 추가 비용을 감수할 만큼 가치 있는 데이터에 한해서만 현명하게 사용하는 것이 좋습니다.

Azure Storage에 저장된 데이터에 액세스하는 데 다음 형식 중 하나를 사용할 수 있습니다.

|데이터 액세스 형식 |설명 |
|---|---|
|`wasb:///`|암호화되지 않은 통신을 사용하여 기본 스토리지에 액세스합니다.|
|`wasbs:///`|암호화된 통신을 사용하여 기본 스토리지에 액세스합니다.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|기본이 아닌 스토리지 계정과 통신할 때 사용됩니다. |


[Azure Storage 확장성 및 성능 목표](../../storage/common/storage-scalability-targets.md)는 Azure스토리지 계정에 대한 현재 제한을 나열합니다. 애플리케이션의 요구가 단일 저장소 계정의 확장성 목표를 초과하는 경우 여러 저장소 계정을 사용하도록 애플리케이션을 빌드한 다음, 데이터 개체를 이러한 저장소 계정에 분할할 수 있습니다.

[Azure 저장소 분석](../../storage/storage-analytics.md)은 모든 저장소 서비스에 대한 메트릭을 제공하며 Azure Portal은 차트를 통해 시각화되도록 수집 메트릭을 구성할 수 있습니다. 저장소 리소스 메트릭의 임계값에 도달하면 사용자에게 알리도록 경고를 만들 수 있습니다.

Azure Storage는 응용 프로그램 또는 다른 스토리지 계정 사용자에 의해 실수로 수정되거나 삭제될 때 데이터를 복구할 수 있도록 [Blob 개체에 대한 일시 삭제](../../storage/blobs/storage-blob-soft-delete.md)를 제공합니다.

[Blob 스냅숏](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)을 만들 수 있습니다. 스냅숏은 특정 시점에 생성된 Blob의 읽기 전용 버전이며 Blob을 백업하는 방법을 제공합니다. 스냅숏이 생성된 후에는 읽거나 복사하거나 삭제할 수 있지만 수정할 수는 없습니다.

> [!Note]
> "Wasbs" 인증서가 없는 온-프레미스 Hadoop 배포판의 이전 버전의 경우 Java 트러스트 저장소로 가져와야 합니다.

Java 트러스트 저장소로 인증서를 가져오는 데 다음 메서드를 사용할 수 있습니다.

파일에 Azure Blob ssl 인증서 다운로드

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

모든 노드에서 Java 트러스트 저장소로 위의 파일 가져오기

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

추가된 인증서가 신뢰 저장소에 있는지 확인

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

자세한 내용은 다음 문서를 참조하세요.

- [Azure HDInsight 클러스터에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)
- [Azure Storage 확장성 및 성능 목표](../../storage/common/storage-scalability-targets.md)
- [Microsoft Azure Storage 성능 및 확장성 검사 목록](../../storage/common/storage-performance-checklist.md)
- [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Azure Portal에서 저장소 계정 모니터링](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage는 HDFS 및 POSIX 스타일 액세스 제어 모델을 구현합니다. 세밀하게 세분화된 액세스 제어에 대해 AAD를 사용하여 최고 수준의 통합을 제공합니다. 저장할 수 있는 데이터 크기 또는 대규모 병렬 분석을 실행하는 기능에 제한이 없습니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure Portal을 사용하여 Data Lake Storage가 있는 HDInsight 클러스터 만들기](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Azure HDInsight 클러스터에 Data Lake Storage 사용](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake 저장소 Gen2는 최신 저장소를 제공 합니다. Azure Data Lake Storage의 1세대에서 핵심 기능을 Azure Blob Storage로 직접 통합된 Hadoop 호환 파일 시스템 엔드포인트와 통합합니다. 이 향상된 기능은 개체 저장소의 크기 조정 및 비용 혜택을 일반적으로 온-프레미스 파일 시스템에만 연결된 안정성 및 성능과 결합합니다.

ADLS Gen 2는  [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md)를 기반으로 빌드되었으며 이를 사용하면 파일 시스템 및 개체 스토리지 패러다임을 모두 사용하여 데이터를 조작할 수 있습니다. 파일 시스템 의미 체계, 파일 수준 보안 및 확장과 같은  [Azure Data Lake Storage Gen1](../../data-lake-store/index.md)의 기능은  [Azure Blob 스토리지](../../storage/blobs/storage-blobs-introduction.md)의 낮은 비용, 계층화된 스토리지, 높은 가용성/재해 복구 기능 및 대규모 SDK/도구 에코시스템과 결합됩니다. Data Lake Storage Gen2에서 분석 워크로드에 맞게 최적화된 파일 시스템 인터페이스의 이점이 추가되는 동안 개체 스토리지의 모든 특성이 유지됩니다.

Data Lake Storage Gen2의 기본 기능은 데이터 액세스 성능을 개선하기 위해 개체/파일을 디렉터리 계층 구조로 구성하는  [계층 구조 네임스페이스](../../storage/data-lake-storage/namespace.md) 를 Blob 스토리지 서비스에 추가한 것입니다. 계층 구조를 통해 디렉터리 이름 바꾸기 또는 삭제와 같은 작업은 디렉터리의 이름 접두사를 공유하는 모든 개체를 열거 및 처리하는 대신 디렉터리에 대한 단일 원자성 메타데이터 작업이 됩니다.

과거 클라우드 기반 분석은 성능, 관리 및 보안 영역을 양보해야 했습니다. ADLS(Azure Data Lake Storage) Gen2의 주요 기능은 아래와 같습니다.

- **Hadoop 호환 액세스**: Azure Data Lake Storage Gen2를 사용하면  [HDFS(Hadoop 분산 파일 시스템)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)를 사용하는 것처럼 데이터를 관리하고 액세스할 수 있습니다. 새 [ABFS 드라이버](../../storage/data-lake-storage/abfs-driver.md)를  [Azure HDInsight](../index.yml)에 포함된 모든 Apache Hadoop 환경 내에서 사용할 수 있습니다. 이 드라이버를 사용하면 Data Lake Storage Gen2에 저장된 데이터에 액세스할 수 있습니다.

- **POSIX 권한 상위 세트**: Data Lake Gen2의 보안 모델은 Data Lake Storage Gen2와 관련된 몇 가지 추가 세분성과 함께 ACL 및 POSIX 권한을 완벽하게 지원합니다. 설정은 관리 도구 또는 Hive 및 Spark 같은 프레임워크를 통해 구성할 수 있습니다.

- **비용 효율성**: Data Lake Storage Gen2는 낮은 비용의 스토리지 용량 및 트랜잭션을 특징으로 합니다. 데이터가 전체 수명 주기를 통해 전환됨에 따라  [Azure Blob Storage 수명 주기](../../storage/common/storage-lifecycle-management-concepts.md)와 같은 기본 제공 기능을 통해 비용을 최소화하도록 청구 요금을 변경합니다.

- **Blob Storage 도구, 프레임 워크 및 앱 사용**: Data Lake Storage Gen2는 현재 Blob Storage용으로 제공되는 다양한 도구, 프레임워크 및 애플리케이션을 계속 사용합니다.

- **최적화된 드라이버**: ABFS(Azure Blob 파일 시스템 드라이버)는 빅 데이터 분석에  [특별히 최적화](../../storage/data-lake-storage/abfs-driver.md) 되었습니다. 해당 REST API는 dfs 엔드포인트, dfs.core.windows.net을 통해 표시됩니다.

ADLS Gen2에 저장된 데이터에 액세스하는 데 다음 형식 중 하나를 사용할 수 있습니다.
- `abfs:///`: 클러스터의 기본 Data Lake Storage에 액세스합니다.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: 기본이 아닌 Data Lake Storage와 통신할 때 사용됩니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure Data Lake Storage Gen2 소개](../../storage/data-lake-storage/introduction.md)
- [Azure Blob FileSystem 드라이버(ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>온-프레미스 Hadoop 클러스터 구성 내에서 Azure Storage 키 보안

Hadoop 구성 파일에 추가되는 Azure 스토리지 키는 온-프레미스 HDFS 및 Azure Blob Storage 간의 연결을 설정합니다. Hadoop 자격 증명 공급자 프레임워크로 암호화하여 이러한 키를 보호할 수 있습니다. 암호화되면 안전하게 저장하고 액세스할 수 있습니다.

**자격 증명을 프로비전하려면:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**위의 공급자 경로를 core-site.xml 또는 사용자 지정 core-site의 Ambari 구성에 추가하려면:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> 다음과 같이 클러스터 수준에서 core-site.xml에 키를 저장하는 대신 distcp 명령줄에 공급자 경로 속성을 추가할 수 있습니다.

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>SAS를 사용하여 Azure Storage 데이터 액세스 제한

HDInsight는 기본적으로 클러스터와 연결된 Azure Storage 계정의 데이터에 대해 모든 액세스 권한을 갖습니다. 사용자에게 데이터에 대한 읽기 전용 액세스 제공과 같은 데이터에 대한 액세스를 제한하는 데 Blob 컨테이너의 SAS(공유 액세스 서명)를 사용할 수 있습니다.

### <a name="using-the-sas-token-created-with-python"></a>python으로 생성된 SAS 토큰 사용

1. [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) 파일을 열고 다음 값을 변경합니다.

    |토큰 속성|설명|
    |---|---|
    |policy_name|만들 저장된 정책에 대해 사용할 이름입니다.|
    |storage_account_name|사용자 스토리지 계정의 이름입니다.|
    |storage_account_key|저장소 계정의 키입니다.|
    |storage_container_name|액세스를 제한하려는 스토리지 계정의 컨테이너입니다.|
    |example_file_path|컨테이너에 업로드되는 파일에 대한 경로입니다.|

2. SASToken.py 파일은 `ContainerPermissions.READ + ContainerPermissions.LIST` 권한과 함께 제공되며 사용 사례에 따라 조정할 수 있습니다.

3. 스크립트를 다음과 같이 실행합니다. `python SASToken.py`

4. 스크립트가 완료되면 다음 텍스트와 유사한 SAS 토큰이 표시됩니다. `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. 공유 액세스 서명을 사용하여 컨테이너에 대한 액세스를 제한하려면 Ambari HDFS Configs 고급 사용자 지정 core-site 추가 속성 아래의 클러스터에 대한 core-site 구성에 사용자 지정 항목을 추가합니다.

6. **Key** 및 **Value** 필드에 다음 값을 사용합니다.

    **키**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **값**: 위의 4단계에서 Python 애플리케이션에 의해 반환되는 SAS 키입니다.

7. **Add** 단추를 클릭하여 이 키 및 값을 저장한 후 **Save** 단추를 클릭하여 구성 변경을 저장합니다. 메시지가 나타나면 변경에 대한 설명(예: "SAS 저장소 액세스 추가")을 추가하고 **저장**을 클릭합니다.

8. Ambari 웹 UI의 왼쪽 목록에서 HDFS를 선택한 다음, 오른쪽의 Service Actions 드롭다운 목록에서  **영향을 받은 모든 항목 다시 시작**을 선택합니다. 메시지가 나타나면  **모두 다시 시작 확인**을 선택합니다.

9. MapReduce2 및 YARN에 대해 이 프로세스를 반복합니다.

Azure에서 SAS 토큰 사용에 관해 기억해야 할 세 가지 중요한 사항이 있습니다.

1. "읽기 + 나열" 권한이 있는 SAS 토큰이 생성되면 해당 SAS 토큰을 사용하여 Blob 컨테이너에 액세스하는 사용자는 데이터를 "쓰고 삭제"할 수 없습니다. 해당 SAS 토큰을 사용하여 Blob 컨테이너에 액세스하고 쓰기 또는 삭제 작업을 시도하는 사용자는 `"This request is not authorized to perform this operation"`과 같은 메시지를 받습니다.

2. SAS 토큰이 `READ + LIST + WRITE` 권한을 사용하여 생성되면(`DELETE`만 제한하도록) `hadoop fs -put`과 같은 명령은 먼저 `\_COPYING\_` 파일에 작성한 다음, 파일 이름을 변경하려고 합니다. 이 HDFS 작업은 WASB에 대한 `copy+delete`에 매핑합니다. `DELETE` 권한이 제공되지 않았으므로 "put"이 실패합니다. `\_COPYING\_` 작업은 일부 동시성 제어를 제공하기 위한 Hadoop 기능입니다. 현재 "WRITE" 작업에도 영향을 주지 않고 "DELETE" 작업만을 제한하는 방법이 없습니다.

3. 아쉽게도 hadoop 자격 증명 공급자 및 암호 해독 키 공급자(ShellDecryptionKeyProvider)는 현재 SAS 토큰을 사용하여 작동하지 않으므로 현재 표시 유형에서 보호될 수 없습니다.

자세한 내용은 [Azure Storage 공유 액세스 서명을 사용하여 HDInsight에서 데이터 액세스 제한](../hdinsight-storage-sharedaccesssignature-permissions.md)을 참조하세요.

## <a name="use-data-encryption-and-replication"></a>데이터 암호화 및 복제 사용

Azure Storage에 쓴 모든 데이터는  [SSE(스토리지 서비스 암호화)](../../storage/common/storage-service-encryption.md)를 사용하여 자동으로 암호화됩니다. Azure 저장소 계정의 데이터는 항상 고가용성을 위해 복제됩니다. 저장소 계정을 만들 때 다음 복제 옵션 중 하나를 선택할 수 있습니다.

- [LRS(로컬 중복 저장소)](../../storage/common/storage-redundancy-lrs.md)
- [ZRS(영역 중복 저장소)](../../storage/common/storage-redundancy-zrs.md)
- [GRS(지역 중복 저장소)](../../storage/common/storage-redundancy-grs.md)
- [RA-GRS(읽기 액세스 지역 중복 저장소)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Azure Data Lake Storage는 LRS(로컬 중복 스토리지)를 제공하지만 재해 복구 계획의 요구에 맞게 정렬된 주파수를 사용하여 다른 지역의 다른 Data Lake Storage 계정에 중요한 데이터를 복사해야 합니다.  [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), DistCp, [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md) 또는  [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)를 포함하여 데이터를 복사하는 다양한 메서드가 있습니다. 또한 실수로 인한 삭제를 방지하도록 Data Lake Storage 계정에 대한 액세스 정책을 적용하는 것이 좋습니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure Storage 복제](../../storage/common/storage-redundancy.md)
- [ADLS(Azure Data Lake Storage)에 대한 재해 지침](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>클러스터에 추가로 Azure Storage 계정 연결

HDInsight를 만드는 과정에서 Azure Storage 계정 또는 Azure Data Lake 스토리지 계정이 기본 파일 시스템으로 선택됩니다. 클러스터 만들기 프로세스 중이나 클러스터를 만든 후에 이 기본 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다.

다음과 같은 방법으로 추가 저장소 계정을 추가할 수 있습니다.
- 저장소 계정 이름 및 서비스를 다시 시작하는 키를 추가하는 Ambari HDFS 구성 고급 사용자 지정 core-site
- 저장소 계정 이름 및 키를 전달하여 [스크립트 동작](../hdinsight-hadoop-add-storage.md) 사용

> [!Note]
> 유효한 사용 사례에서 Azure 저장소에 대한 제한은  [Azure 지원](https://azure.microsoft.com/support/faq/)에 만들어진 요청을 통해 늘릴 수 있습니다.

자세한 내용은 다음 문서를 참조하세요.
- [HDInsight에 추가 저장소 계정 추가](../hdinsight-hadoop-add-storage.md)

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서를 읽어보세요.

- [온-프레미스에서 Azure HDInsight Hadoop 마이그레이션에 대한 데이터 마이그레이션 모범 사례](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
