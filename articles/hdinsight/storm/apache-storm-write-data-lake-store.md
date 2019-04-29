---
title: Apache Storm에서 Storage/Data Lake Storage에 쓰기 - Azure HDInsight
description: Apache Storm을 사용하여 HDInsight용 HDFS 호환 저장소에 쓰는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 4ba0c861674eb2308cf1f96c33d0792f3e1a0f94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109466"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>HDInsight의 Apache Storm에서 Apache Hadoop HDFS에 쓰기

[Apache Storm](https://storm.apache.org/)을 사용하여 HDInsight의 Apache Storm에서 사용하는 HDFS 호환 스토리지에 데이터를 쓰는 방법에 대해 알아봅니다. HDInsight는 Azure Storage 및 Azure Data Lake Storage를 모두 HDFS 호환 스토리지로 사용할 수 있습니다. Storm은 HDFS에 데이터를 쓰는 [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 구성 요소를 제공합니다. 이 문서는 HdfsBolt에서 두 가지 유형의 저장소에 쓰는 방법에 대한 정보를 제공합니다. 

> [!IMPORTANT]  
> 이 문서에서 사용되는 예제 토폴로지는 HDInsight의 Storm에 포함된 구성 요소를 사용합니다. 다른 Apache Storm 클러스터와 함께 Azure Data Lake Storage를 사용하려면 수정해야 할 수도 있습니다.

## <a name="get-the-code"></a>코드 가져오기

이 토폴로지를 포함하는 프로젝트는 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)에서 다운로드할 수 있습니다.

이 프로젝트를 컴파일하기 위해 개발 환경에 필요한 구성 요소는 다음과 같습니다.

* [Java JDK 1.8](https://aka.ms/azure-jdks) 이상 - HDInsight 3.5 이상에는 Java 8이 필요합니다.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* `JAVA_HOME` - JDK가 설치된 디렉터리를 가리켜야 합니다.
* `PATH` - 다음 경로를 포함해야 합니다.
  
    * `JAVA_HOME`(또는 이와 동등한 경로)
    * `JAVA_HOME\bin`(또는 이와 동등한 경로)
    * Maven이 설치된 디렉터리

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>HDInsight에서 HdfsBolt를 사용하는 방법

> [!IMPORTANT]  
> HDInsight의 Storm에서 HdfsBolt를 사용하려면 먼저 스크립트 동작을 사용하여 필요한 jar 파일에 Storm에 대한 `extpath`를 복사해야 합니다. 자세한 내용은 클러스터 구성 섹션을 참조하세요.

HdfsBolt는 사용자가 제공하는 파일 구성표를 사용하여 HDFS에 쓰는 방법을 인식합니다. HDInsight를 사용하는 경우 다음 구성표 중 하나를 사용합니다.

* `wasb://`: Azure Storage 계정과 함께 사용
* `abfs://`: Azure Data Lake Storage Gen2에서 사용됩니다.
* `adl://`: Azure Data Lake Storage Gen1에서 사용됩니다.

다음 표에서는 여러 시나리오에 대한 파일 구성표를 사용하는 경우의 예를 제공합니다.

| 구성표 | 메모 |
| ----- | ----- |
| `wasb:///` | 기본 스토리지 계정은 Azure Storage 계정의 Blob 컨테이너입니다. |
| `abfs:///` | 기본 스토리지 계정이 Azure Data Lake Storage Gen2 계정의 디렉터리입니다. |
| `adl:///` | 기본 스토리지 계정이 Azure Data Lake Storage Gen1의 디렉터리입니다. 클러스터를 만드는 동안 클러스터의 HDFS 루트인 Data Lake Storage의 디렉터리를 지정합니다. 예를 들어 `/clusters/myclustername/` 디렉터리가 있습니다. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | 클러스터와 연결된 기본이 아닌 추가 Azure Storage 계정입니다. |
| `abfs://CONTAINER@ACCOUNT.dfs.core.windows.net/` | 클러스터와 연결된 기본이 아닌 추가 Azure Storage 계정입니다. |
| `adl://STORENAME/` | 클러스터에서 사용하는 Data Lake Storage의 루트입니다. 이 구성표를 사용하면 클러스터 파일 시스템이 포함된 디렉터리 외부에 있는 데이터에 액세스할 수 있습니다. |

자세한 내용은 Apache.org의 [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 참조를 참조하세요.

### <a name="example-configuration"></a>예제 구성

다음 YAML은 예제에 포함된 `resources/writetohdfs.yaml` 파일에서 인용한 부분입니다. 이 파일에서는 Apache Storm의 [Flux](https://storm.apache.org/releases/1.1.2/flux.html) 프레임워크를 사용하여 Storm 토폴로지를 정의합니다.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

이 YAML에서 정의하는 항목은 다음과 같습니다.

* `syncPolicy`: 파일 시스템에 파일이 동기화/플러시되는 시기를 정의합니다. 이 예제에서는 1,000개 튜플마다 동기화/플러시되도록 정의했습니다.
* `fileNameFormat`: 파일을 쓸 때 사용할 경로 및 파일 이름 패턴을 정의합니다. 이 예제에서 경로는 필터를 사용하여 런타임에 제공되며 파일 확장명은 `.txt`입니다.
* `recordFormat`: 기록된 파일의 내부 형식을 정의합니다. 이 예제에서 필드는 `|` 문자로 구분되었습니다.
* `rotationPolicy`: 파일을 회전하는 시기를 정의합니다. 이 예제에서는 회전이 수행되지 않습니다.
* `hdfs-bolt`: 이전 구성 요소를 `HdfsBolt` 클래스의 구성 매개 변수로 사용합니다.

Flux 프레임워크에 대한 자세한 내용은 [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)을 참조하세요.

## <a name="configure-the-cluster"></a>클러스터 구성

기본적으로 HDInsight의 Storm에는 HdfsBolt에서 Storm의 클래스 경로에 있는 Azure Storage 또는 Data Lake Storage와 통신하는 데 사용하는 구성 요소가 포함되지 않습니다. 다음 스크립트 동작을 사용하여 클러스터의 Storm에 대한 `extlib` 디렉터리에 이러한 구성 요소를 추가합니다.

* 스크립트 URI: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* 다음 적용할 노드: Nimbus, Supervisor
* 매개 변수 없음

HDInsight에서 이 스크립트를 사용하는 방법에 대한 자세한 내용은 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](./../hdinsight-hadoop-customize-cluster-linux.md) 문서를 참조하세요.

## <a name="build-and-package-the-topology"></a>토폴로지 빌드 및 패키지

1. 예제 프로젝트를 다운로드 [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) 개발 환경입니다.

2. 명령 프롬프트, 터미널 또는 셸 세션에서 다운로드한 디렉터리를 변경합니다. 토폴로지를 빌드하고 패키지하려면 다음 명령을 사용합니다.
   
        mvn compile package
   
    빌드 및 패키징이 완료되면 `StormToHdfs-1.0-SNAPSHOT.jar`이라는 파일이 포함된 `target`이라는 새 디렉터리가 있습니다. 이 파일에는 컴파일된 토폴로지가 포함되어 있습니다.

## <a name="deploy-and-run-the-topology"></a>토폴로지 배포 및 실행

1. 다음 명령을 사용하여 HDInsight 클러스터에 토폴로지를 복사합니다. **USER** 를 클러스터를 만들 때 사용한 SSH 사용자 이름으로 대체합니다. **CLUSTERNAME** 을 클러스터 이름으로 바꿉니다.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    메시지가 표시되면 클러스터에 대한 SSH 사용자를 만들 때 사용한 암호를 입력합니다. 암호 대신 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하여 개인 키와 일치하는 경로를 지정합니다.
   
   > [!NOTE]  
   > HDInsight에서의 `scp` 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결하도록 다음을 사용합니다. **USER** 를 클러스터를 만들 때 사용한 SSH 사용자 이름으로 대체합니다. **CLUSTERNAME** 을 클러스터 이름으로 바꿉니다.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    메시지가 표시되면 클러스터에 대한 SSH 사용자를 만들 때 사용한 암호를 입력합니다. 암호 대신 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하여 개인 키와 일치하는 경로를 지정합니다.
   
   자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

3. 연결되면 다음 명령을 사용하여 `dev.properties`라는 파일을 만듭니다.

        nano dev.properties

4. `dev.properties` 파일의 내용으로 다음 텍스트를 사용합니다.

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > 이 예제에서는 클러스터에서 Azure Storage 계정을 기본 스토리지로 사용한다고 가정합니다. 클러스터에서 Azure Data Lake Storage Gen2를 사용하는 경우 `hdfs.url: abfs:///`를 대신 사용합니다. 클러스터에서 Azure Data Lake Storage Gen1을 사용하는 경우 `hdfs.url: adl:///`를 대신 사용합니다.
    
    파일을 저장하려면 __Ctrl + X__를 사용한 다음 __Y__를 입력하고 마지막으로 __Enter__ 키를 누릅니다. 이 파일의 값은 데이터가 기록되는 Data Lake Storage URL 및 디렉터리 이름을 설정합니다.

3. 다음 명령을 사용하여 토폴로지를 시작합니다.
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    이 명령은 클러스터의 Nimbus 노드에 제출하여 Flux 프레임워크를 사용하는 토폴로지를 시작합니다. 토폴로지를 jar에 포함된 `writetohdfs.yaml` 파일에 의해 정의됩니다. `dev.properties` 파일은 필터로 전달되고 파일에 포함된 값은 토폴로지에서 읽힙니다.

## <a name="view-output-data"></a>출력 데이터 보기

데이터를 보려면 다음 명령을 사용합니다.

    hdfs dfs -ls /stormdata/

이 토폴로지로 만든 파일 목록이 표시됩니다.

다음 목록은 이전 명령에서 반환된 데이터 예제입니다.

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>토폴로지 중지

Storm 토폴로지가 중지될 때까지 실행되거나 클러스터가 삭제됩니다. 토폴로지를 중지하려면 다음 명령을 사용합니다.

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

Apache Storm을 사용하여 Azure Storage 및 Azure Data Lake Storage에 쓰는 방법을 알아보았으면, 다른 [HDInsight용 Apache Storm 예제](apache-storm-example-topology.md)를 알아보세요.

## <a name="see-also"></a>참고 항목
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
