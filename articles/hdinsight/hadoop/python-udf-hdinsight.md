---
title: Apache Hive 및 Apache Pig에서의 Python UDF - Azure HDInsight
description: HDInsight에서 Azure의 Apache Hadoop 기술 스택인 Apache Hive 및 Apache Pig에서 Python UDF(사용자 정의 함수)를 사용하는 방법에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6f3140f412f9d36ca36cef440bd4e60f1a9197d4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114607"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>HDInsight의 Apache Hive 및 Apache Pig에서 Python UDF(사용자 정의 함수) 사용

Azure HDInsight의 Apache Hadoop에서 Apache Hive 및 Apache Pig에서 Python UDF(사용자 정의 함수)를 사용하는 방법에 대해 알아봅니다.

## <a name="python"></a>HDInsight의 Python

Python2.7은 기본적으로 HDInsight 3.0 이상에 설치됩니다. 스트림 처리를 위해 이 버전의 Python에서 Apache Hive를 사용할 수 있습니다. 스트림 처리는 STDOUT 및 STDIN을 사용하여 Hive와 UDF 간에 데이터를 전달합니다.

HDInsight에는 Java로 작성된 Python 구현인 Jython도 포함되어 있습니다. Jython은 Java Virtual Machine에서 직접 실행되며 스트리밍을 사용하지 않습니다. Jython는 Pig와 함께 Python을 사용할 때 권장되는 Python 인터프리터입니다.

## <a name="prerequisites"></a>필수 조건

* **HDInsight에서 Hadoop 클러스터를**입니다. 참조 [Linux에서 HDInsight 시작](apache-hadoop-linux-tutorial-get-started.md)합니다.
* **SSH 클라이언트** 자세한 내용은 [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.
* 합니다 [URI 체계](../hdinsight-hadoop-linux-information.md#URI-and-scheme) 클러스터 기본 저장소에 대 한 합니다. Wasb 하는 것: / / Azure storage의 경우 abfs: / / Azure Data Lake 저장소 Gen2 또는 adl: / / Azure Data Lake 저장소 Gen1에 대 한 합니다. URI wasbs 것에 대해 Azure Storage 또는 Data Lake 저장소 Gen2 전송 보안을 사용 하는 경우: / / 또는 abfss: / / 각각도 참조 하세요 [보안 전송](../../storage/common/storage-require-secure-transfer.md)합니다.
* **저장소 구성에 대 한 변경 되었을 수 있습니다.**  참조 [저장소 구성](#storage-configuration) 저장소 계정 종류를 사용 하는 경우 `BlobStorage`합니다.
* 선택 사항입니다.  PowerShell을 사용 하도록 계획 해야 합니다 [AZ 모듈](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) 설치 합니다.

> [!NOTE]  
> 이 문서에 사용 된 저장소 계정은 Azure Storage 사용 되었습니다 [보안 전송](../../storage/common/storage-require-secure-transfer.md) 사용 하도록 설정 되므로 `wasbs` 문서 전체에서 사용 됩니다.

## <a name="storage-configuration"></a>Storage 구성
조치가 필요한 경우 사용 된 저장소 계정의 종류입니다 `Storage (general purpose v1)` 또는 `StorageV2 (general purpose v2)`합니다.  이 문서의 프로세스는 출력에 적어도 `/tezstaging`합니다.  기본 hadoop 구성이 포함 됩니다 `/tezstaging` 에 `fs.azure.page.blob.dir` 에서 구성 변수 `core-site.xml` 서비스용 `HDFS`합니다.  이 구성에는 저장소 계정 종류에 대 한 지원 되지 않는 페이지 blob를 디렉터리에 출력 하면 `BlobStorage`합니다.  사용 하도록 `BlobStorage` 이 문서에 대 한 제거 `/tezstaging` 에서 `fs.azure.page.blob.dir` 구성 변수입니다.  구성에서 액세스할 수 합니다 [Ambari UI](../hdinsight-hadoop-manage-ambari.md)합니다.  그렇지 않으면 오류 메시지가 표시 됩니다. `Page blob is not supported for this account type.`

> [!WARNING]  
> 이 문서의 단계에서는 다음과 같이 가정합니다.  
>
> * 로컬 개발 환경에서 Python 스크립트를 만듭니다.
> * 중 하나를 사용 하 여 HDInsight에 스크립트를 업로드 합니다 `scp` 명령 또는 PowerShell 스크립트를 제공된 합니다.
>
> 사용 하려는 경우는 [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) HDInsight를 사용 하려면 다음을 수행 해야 합니다.
>
> * Cloud Shell 환경 내에서 스크립트를 만듭니다.
> * `scp`를 사용하여 Cloud Shell에서 HDInsight로 파일을 업로드합니다.
> * Cloud Shell에서 `ssh`를 사용하여 HDInsight에 연결하고 예제를 실행합니다.

## <a name="hivepython"></a>Apache Hive UDF

Python은 HiveQL `TRANSFORM` 문을 통해 Hive의 UDF로 사용할 수 있습니다. 예를 들어 다음 HiveQL은 클러스터의 기본 Azure Storage 계정에 저장된 `hiveudf.py` 파일을 호출합니다.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

다음은 이 예제에서 수행하는 작업입니다.

1. 파일의 시작 부분에 있는 `add file` 문이 분산 캐시에 `hiveudf.py` 파일을 추가하므로, 클러스터의 모든 노드에서 액세스할 수 있습니다.
2. `SELECT TRANSFORM ... USING` 문은 `hivesampletable`에서 데이터를 선택합니다. 또한 clientid, devicemake 및 devicemodel 값을 `hiveudf.py` 스크립트에 전달합니다.
3. `AS` 절은 `hiveudf.py`에서 반환된 필드를 설명합니다.

<a name="streamingpy"></a>

### <a name="create-file"></a>파일 만들기

개발 환경에서 `hiveudf.py`라는 텍스트 파일을 만듭니다. 이 파일의 내용으로 다음 코드를 사용합니다.

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

이 스크립트는 다음 작업을 수행합니다.

1. STDIN에서 데이터 줄을 읽습니다.
2. `string.strip(line, "\n ")`를 사용하여 후행 줄 바꿈 문자를 제거합니다.
3. 스트림 처리를 할 때 모든 값과 각 값 사이의 탭 문자가 한 줄에 포함됩니다. 따라서 `string.split(line, "\t")` 를 사용하여 각 탭의 입력을 분할하여 필드만 반환할 수 있습니다.
4. 처리가 완료되면 출력을 단일 행(각 필드 사이에 탭 포함)으로 STDOUT에 작성해야 합니다. 예: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. `while` 루프는 `line`이 읽히지 않을 때까지 반복됩니다.

스크립트 출력은 `devicemake` 및 `devicemodel`의 입력 값과 연결된 값의 해시를 연결합니다.

### <a name="upload-file-shell"></a>(셸) 파일 업로드
아래 명령에서 대체 `sshuser` 와 다른 경우 실제 사용자 이름입니다.  대체 `mycluster` 실제 클러스터 이름입니다.  작업 디렉터리는 파일의 위치를 확인 합니다.

1. `scp` 를 사용하여 파일을 HDInsight 클러스터에 복사합니다. 편집 하 고 아래 명령을 입력 합니다.

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH를 사용하여 클러스터에 연결합니다.  편집 하 고 아래 명령을 입력 합니다.

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. SSH 세션에서 이전 클러스터의 저장소에 업로드 된 python 파일을 추가 합니다.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive UDF (셸)를 사용 합니다.

1. Hive에 연결 하려면 열린 SSH 세션에서 다음 명령을 사용 합니다.

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    이 명령은 Beeline 클라이언트를 시작합니다.

2. `0: jdbc:hive2://headnodehost:10001/>` 프롬프트에서 다음 쿼리를 입력합니다.

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. 마지막 줄을 입력하면 작업이 시작됩니다. 작업이 완료되면 다음 예제와 유사한 출력을 반환합니다.

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Beeline을 종료 하려면 다음 명령을 입력 합니다.

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>업로드 (PowerShell)

> [!IMPORTANT]  
> 경우에 이러한 PowerShell 스크립트 작동 하지 것입니다 [보안 전송](../../storage/common/storage-require-secure-transfer.md) 사용 가능 합니다.  셸 명령을 사용 하거나 보안 전송이 사용 하지 않도록 설정 합니다.

PowerShell을 사용하여 Hive 쿼리를 원격으로 실행할 수도 있습니다. 작업 디렉터리에는 확인 `hiveudf.py` 위치한 합니다.  다음 PowerShell 스크립트를 사용 하 여 사용 하는 Hive 쿼리를 실행 합니다 `hiveudf.py` 스크립트:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> 파일 업로드에 대한 자세한 내용은 [HDInsight에서 Apache Hadoop 작업용 데이터 업로드](../hdinsight-upload-data.md) 문서를 참조하세요.


#### <a name="use-hive-udf"></a>사용 하 여 Hive UDF


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Hive** 작업의 출력은 다음 예제와 유사하게 표시됩니다.

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

`GENERATE` 문을 통해 Python 스크립트를 Pig의 UDF로 사용할 수 있습니다. Jython 또는 C Python을 사용하여 스크립트를 실행할 수 있습니다.

* Jython은 JVM에서 실행되고, 기본적으로 Pig에서 호출될 수 있습니다.
* C Python은 외부 프로세스이므로 JVM에서 Pig의 데이터는 Python 프로세스에서 실행 중인 스크립트로 전송됩니다. Python 스크립트의 출력이 Pig로 다시 전송됩니다.

Python 인터프리터를 지정하려면 Python 스크립트를 참조할 때 `register`를 사용합니다. 다음 예제에서는 Pig as `myfuncs`를 사용하여 스크립트를 등록합니다.

* **Jython 사용**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python 사용**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Jython을 사용할 경우 pig_jython 파일 경로일 수 있습니다 로컬 경로 또는 WASBS: / / 경로입니다. 그러나 C Python을 사용할 경우에는 Pig 작업을 제출하는 데 사용하는 노드의 로컬 파일 시스템에 있는 파일을 참조해야 합니다.

등록하기 전이라면 두 경우에 대한 이 예제의 Pig Latin은 다음과 같이 모두 동일합니다.

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

다음은 이 예제에서 수행하는 작업입니다.

1. 첫 번째 줄은 `LOGS`에 `sample.log` 샘플 데이터 파일을 로드합니다. 각 레코드를 `chararray`로도 정의합니다.
2. 다음 줄에서는 모든 Null 값을 필터링하여 `LOG`에 작업 결과를 저장합니다.
3. 그런 다음 `LOG`의 레코드에 대해 반복하고 `GENERATE`를 사용하여 `create_structure` 메서드를 호출합니다. 이 메서드는 `myfuncs`로 로드된 Python/Jython 스크립트에 포함되어 있습니다. `LINE`은 현재 레코드를 함수에 전달하는 데 사용됩니다.
4. 마지막으로 출력은 `DUMP` 명령을 사용하여 STDOUT로 덤프됩니다. 이 명령은 작업이 완료된 후 결과를 표시합니다.

### <a name="create-file"></a>파일 만들기

개발 환경에서 `pigudf.py`라는 텍스트 파일을 만듭니다. 이 파일의 내용으로 다음 코드를 사용합니다.

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Pig Latin 예제에서는 입력에 대한 일관된 스키마가 없으므로 `LINE` 입력이 chararray로 정의되었습니다. Python 스크립트는 데이터를 출력에 대한 일관된 스키마로 변환합니다.

1. `@outputSchema` 문은 Pig에 반환되는 데이터의 형식을 정의합니다. 이 경우 Pig 데이터 형식은 **데이터 모음**입니다. 모음에는 모두 chararray(문자열)인 다음과 같은 필드가 포함됩니다.

   * date - 로그 항목이 생성된 날짜
   * time - 로그 항목이 생성된 시간
   * classname - 항목이 생성된 클래스 이름
   * level - 로그 수준
   * detail - 로그 항목에 대한 세부 정보

2. 그런 다음 `def create_structure(input)`가 Pig에서 줄 항목을 전달할 함수를 정의합니다.

3. 예제 데이터인 `sample.log`는 대개 date, time, classname, level 및 detail 스키마를 준수합니다. 그러나 `*java.lang.Exception*`으로 시작하는 몇 개의 줄이 포함됩니다. 이러한 줄은 스키마와 일치하도록 수정해야 합니다. `if` 문이 이러한 줄을 확인한 후 `*java.lang.Exception*` 문자열을 끝으로 이동하고, 원하는 출력 스키마에 따라 인라인 데이터를 가져오는 입력 데이터를 전달합니다.

4. 그런 다음 `split` 명령을 사용하여 첫 4개 공백 문자에서 데이터를 분할합니다. 출력은 `date`, `time`, `classname`, `level` 및 `detail`에 할당됩니다.

5. 마지막으로 값은 Pig로 반환됩니다.

데이터가 Pig로 반환되면 `@outputSchema` 문에 정의된 것과 일관된 스키마를 포함합니다.



### <a name="upload-file-shell"></a>(셸) 파일 업로드

아래 명령에서 대체 `sshuser` 와 다른 경우 실제 사용자 이름입니다.  대체 `mycluster` 실제 클러스터 이름입니다.  작업 디렉터리는 파일의 위치를 확인 합니다.

1. `scp` 를 사용하여 파일을 HDInsight 클러스터에 복사합니다. 편집 하 고 아래 명령을 입력 합니다.

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH를 사용하여 클러스터에 연결합니다.  편집 하 고 아래 명령을 입력 합니다.

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. SSH 세션에서 이전 클러스터의 저장소에 업로드 된 python 파일을 추가 합니다.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Pig UDF (셸)를 사용 합니다.

1. Pig에 연결 하려면 열린 SSH 세션에서 다음 명령을 사용 합니다.

    ```bash
    pig
    ```

2. `grunt>` 프롬프트에 다음 문을 입력합니다.

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. 다음 줄을 입력하면 작업이 시작됩니다. 작업이 완료되면 다음 데이터와 유사한 출력이 반환됩니다.

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. `quit`를 사용하여 Grunt 셸을 종료한 후 다음을 사용하여 로컬 파일 시스템에 있는 pigudf.py 파일을 편집합니다.

    ```bash
    nano pigudf.py
    ```

5. 편집기에서 다음 줄의 시작 부분에 있는 `#` 문자를 제거하여 해당 줄의 주석 처리를 제거합니다.

    ```bash
    #from pig_util import outputSchema
    ```

    이 줄은 Jython 대신 C Python과 함께 작동하도록 Python 스크립트를 수정합니다. 변경했으면 **Ctrl+X**를 사용하여 편집기를 종료합니다. **Y**를 선택한 다음 **Enter** 키를 눌러 변경 내용을 저장합니다.

6. `pig` 명령을 사용하여 셸을 다시 시작합니다. `grunt>` 프롬프트에서 다음 문을 사용하여 Jython 인터프리터를 사용하는 Python 스크립트를 실행합니다.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    이 작업이 완료되면 이전에 Jython을 사용하여 스크립트를 실행한 때와 같은 출력이 표시됩니다.


### <a name="upload-file-powershell"></a>업로드 (PowerShell)

> [!IMPORTANT]  
> 경우에 이러한 PowerShell 스크립트 작동 하지 것입니다 [보안 전송](../../storage/common/storage-require-secure-transfer.md) 사용 가능 합니다.  셸 명령을 사용 하거나 보안 전송이 사용 하지 않도록 설정 합니다.

PowerShell을 사용하여 Hive 쿼리를 원격으로 실행할 수도 있습니다. 작업 디렉터리에는 확인 `pigudf.py` 위치한 합니다.  다음 PowerShell 스크립트를 사용 하 여 사용 하는 Hive 쿼리를 실행 합니다 `pigudf.py` 스크립트:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Pig UDF (PowerShell)

> [!NOTE]  
> PowerShell을 사용하는 작업을 원격으로 제출하는 경우 C Python을 인터프리터로사용할 수 없습니다.

PowerShell을 사용하여 Pig Latin 작업을 실행할 수도 있습니다. 사용 하는 Pig Latin 작업을 실행 하는 `pigudf.py` 스크립트에서 다음 PowerShell 스크립트를 사용 합니다.

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Pig** 작업의 출력은 다음 데이터와 유사하게 표시됩니다.

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>문제 해결

### <a name="errors-when-running-jobs"></a>작업 실행 중 오류 발생

하이브 작업 실행 중 다음 텍스트와 유사한 오류가 발생할 수 있습니다.

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

이 문제는 Python 파일의 줄 끝 때문에 발생할 수 있습니다. 많은 Windows 편집기에서는 기본적으로 CRLF를 줄 끝으로 사용하지만 Linux 애플리케이션에서는 보통 LF를 사용합니다.

파일을 HDInsight로 업로드하기 전에 CR 문자를 제거하기 위해 다음 PowerShell 문을 사용할 수 있습니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell 스크립트

이 예제를 실행하는 데 사용된 두 가지 예제 PowerShell 스크립트는 작업의 오류 출력을 표시하는 주석 처리된 줄을 포함합니다. 작업의 필요한 출력이 표시되지 않으면 다음 줄의 주석 처리를 제거하고 오류 정보가 문제를 나타내는지 확인합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

오류 정보(STDERR) 및 작업의 결과(STDOUT)도 HDInsight 저장소에 로깅됩니다.

| 이 작업의 경우 | Blob 컨테이너에서 이러한 파일을 찾습니다. |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>다음 단계

기본적으로 제공되지 않는 Python 모듈을 로드해야 하는 경우 [Azure HDInsight에 모듈을 배포하는 방법](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)(영문)을 참조하세요.

Pig 및 Hive를 사용하고 MapReduce 사용에 대해 배우는 다른 방법은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)
