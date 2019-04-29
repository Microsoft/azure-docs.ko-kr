---
title: Azure HDInsight 클러스터 사용자 지정 스크립트 작업 개발
description: Bash 스크립트를 사용 하 여 HDInsight 클러스터 사용자 지정 하는 방법에 알아봅니다. 스크립트 작업을 허용 하는 동안 또는 클러스터 구성 설정을 변경 하거나 추가 소프트웨어를 설치 하려면 클러스터를 만든 후 스크립트를 실행할 수 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: 66132a2a6a7b5b89bca0767efe7c194ca3dec051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590791"
---
# <a name="script-action-development-with-hdinsight"></a>HDInsight를 사용하여 스크립트 작업 개발

Bash 스크립트를 사용하여 HDInsight 클러스터를 사용자 지정하는 방법에 대해 알아봅니다. 스크립트 작업은 클러스터를 만드는 동안 또는 만든 후에 HDInsight를 사용자 지정하는 방법입니다.

## <a name="what-are-script-actions"></a>스크립트 작업 정의

스크립트 작업은 Azure가 구성을 변경하거나 소프트웨어를 설치하기 위해 클러스터 노드에서 실행하는 Bash 스크립트입니다. 스크립트 작업은 루트로 실행되며 클러스터 노드에 대한 모든 액세스 권한을 제공합니다.

스크립트 작업은 다음 방법을 통해 적용될 수 있습니다.

| 이 방법을 사용하여 스크립트를 적용... | 클러스터를 생성하는 동안... | 실행 중인 클러스터에서... |
| --- |:---:|:---:|
| Azure portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure 클래식 CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager 템플릿 |✓ |&nbsp; |

이러한 방법을 사용하여 스크립트 작업을 적용하는 데 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="bestPracticeScripting"></a>스크립트 개발을 위한 모범 사례

HDInsight 클러스터용으로 사용자 지정 스크립트를 개발할 때 유의해야 하는 몇 가지 모범 사례는 다음과 같습니다.

* [Apache Hadoop 버전 대상](#bPS1)
* [OS 버전 대상](#bps10)
* [스크립트 리소스에 대한 안정적인 링크 제공](#bPS2)
* [사전 컴파일한 리소스 사용](#bPS4)
* [클러스터 사용자 지정 스크립트가 멱등원인지 확인](#bPS3)
* [클러스터 아키텍처의 고가용성 확인](#bPS5)
* [Azure Blob Storage를 사용하도록 사용자 지정 구성 요소 구성](#bPS6)
* [STDOUT 및 STDERR에 정보 쓰기](#bPS7)
* [줄 끝을 LF인 파일을 ASCII로 저장](#bps8)
* [다시 시도 논리를 사용하여 일시적 오류에서 복구](#bps9)

> [!IMPORTANT]  
> 스크립트 작업은 60분 이내에 완료하지 않으면 프로세스에 실패합니다. 노드 프로비전 중에는 스크립트가 다른 설정 및 구성 프로세스와 동시에 실행됩니다. CPU 시간 또는 네트워크 대역폭 등의 리소스에 대한 경합으로 인해 스크립트 실행이 개발 환경에서보다 더 오래 걸릴 수 있습니다.

### <a name="bPS1"></a>Apache Hadoop 버전 대상

HDInsight의 서로 다른 버전에는 설치된 Hadoop 서비스 및 구성 요소의 서로 다른 버전이 있습니다. 스크립트가 특정 버전의 서비스 또는 구성 요소를 기대하는 경우 필수 구성 요소를 포함하는 HDInsight의 버전으로 스크립트를 사용해야 합니다. [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md) 문서를 사용하여 HDInsight가 포함된 구성 요소 버전에 대한 정보를 찾을 수 있습니다.

### <a name="checking-the-operating-system-version"></a>운영 체제 버전 검사

HDInsight의 다른 버전들은 Ubuntu의 특정 버전에 의존합니다. 스크립트에서 확인해야 하는 OS 버전 간의 차이가 있을 수 있습니다. 예를 들어 Ubuntu의 버전에 연결된 이진 파일을 설치해야 할 수 있습니다.

OS 버전을 확인하려면 `lsb_release`을 사용합니다. 예를 들어, 다음 스크립트에서 OS 버전에 따라 특정 tar 파일을 참조하는 방법을 보여 줍니다.

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="bps10"></a> 대상 운영 체제 버전

Linux 기반 HDInsight는 Ubuntu Linux 배포를 기반으로 합니다. HDInsight는 버전이 다르면 다른 버전의 Ubuntu에 의존하는데 이는 스크립트 동작 방식을 변경할 수 있습니다. 예를 들어 HDInsight 3.4 이전 버전은 Upstart를 사용하는 Ubuntu 버전을 기반으로 합니다. 버전 3.5 이상은 Systemd를 사용하는 Ubuntu 16.04를 기반으로 합니다. Systemd 및 Upstart는 다른 명령에 의존하기 때문에 이를 사용하여 작업하기 위해 스크립트가 작성되어야 합니다.

HDInsight 3.4와 3.5 간의 또 다른 중요한 차이는 현재 `JAVA_HOME`이 Java 8을 가리킨다는 것입니다. 다음 코드는 Ubuntu 14 또는 16에서 스크립트가 실행되고 있는지 확인하는 방법을 보여 줍니다.

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

이 코드 조각이 포함된 전체 스크립트는 https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh에서 찾을 수 있습니다.

HDInsight에서 사용되는 Ubuntu 버전은 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md) 문서를 참조하세요.

Systemd와 Upstart 간의 차이점을 이해하려면 [Upstart 사용자에 대한 Systemd](https://wiki.ubuntu.com/SystemdForUpstartUsers)를 참조하세요.

### <a name="bPS2"></a>스크립트 리소스에 대한 안정적인 링크 제공

스크립트 및 연결된 리소스는 클러스터의 수명 내내 사용할 수 있어야 합니다. 이러한 리소스는 크기 조정 작업 중 새 노드가 클러스터에 추가되는 경우에 필요합니다.

구독의 Azure Storage 계정에 모든 것을 다운로드하고 보관하는 것이 좋습니다.

> [!IMPORTANT]  
> 사용된 저장소 계정은 클러스터의 기본 저장소 계정 또는 다른 모든 저장소 계정의 공용 읽기 전용 컨테이너에 있어야 합니다.

예를 들어 Microsoft에서 제공하는 샘플은 [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) 저장소 계정에 저장됩니다. 이 위치는 HDInsight 팀에서 유지 관리하는 공용, 읽기 전용 컨테이너입니다.

### <a name="bPS4"></a>사전 컴파일한 리소스 사용

스크립트 실행 시간을 줄이려면 소스 코드로부터 리소스를 컴파일하는 작업은 실행하지 않습니다. 예를 들어 리소스를 미리 컴파일하고 HDInsight와 동일한 데이터 센터에서 Azure Storage 계정 Blob에 저장합니다.

### <a name="bPS3"></a>클러스터 사용자 지정 스크립트가 멱등원인지 확인

스크립트는 idempotent여야 합니다. 스크립트를 여러 번 실행하는 경우 매번 동일한 상태로 클러스터를 반환해야 합니다.

예를 들어 구성 파일을 수정하는 스크립트는 여러 번 실행하는 경우 중복 항목을 추가해서는 안됩니다.

### <a name="bPS5"></a>클러스터 아키텍처의 고가용성 확인

Linux 기반 HDInsight 클러스터는 클러스터 내에서 활성화 되는 두 헤드 노드를 제공하며 스크립트 작업은 두 노드에서 실행됩니다. 설치하는 구성 요소가 하나의 헤드 노드만 예상하는 경우 두 헤드 노드에 구성 요소를 설치하지 마십시오.

> [!IMPORTANT]  
> HDInsight의 일부로 제공되는 서비스는 두 헤드 노드 간에 필요에 따라 장애 조치하도록 설계되었습니다. 이 기능은 스크립트 작업을 통해 설치된 구성 요소를 사용자 지정하도록 확장되지 않습니다. 사용자 지정 구성 요소에 대한 고가용성이 필요한 경우 사용자 고유의 장애 조치 메커니즘을 구현해야 합니다.

### <a name="bPS6"></a>Azure Blob Storage를 사용하도록 사용자 지정 구성 요소 구성

클러스터에 설치하는 구성 요소에는 Apache HDFS(Hadoop 분산 파일 시스템) 스토리지를 사용하기 위한 기본 구성이 있을 수 있습니다. HDInsight는 Azure Storage 또는 Data Lake Storage를 기본 스토리지로 사용합니다. 클러스터가 삭제되는 경우에도 데이터가 지속되는 HDFS 호환 가능 파일 시스템을 제공합니다. 설치하는 구성 요소가 HDFS 대신 WASB 또는 ADL을 사용하도록 구성해야 할 수 있습니다.

대부분의 작업의 경우 파일 시스템을 지정할 필요가 없습니다. 예를 들어 다음 복사 hadoop common.jar 파일을 로컬 파일 시스템에서 클러스터 저장소에:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

이 예제에서 `hdfs` 명령은 기본 클러스터 저장소를 투명하게 사용합니다. 일부 작업의 경우 URI를 지정해야 할 수도 있습니다. 예를 들어 Azure Data Lake Storage Gen1은 `adl:///example/jars`, Data Lake Storage Gen2는 `abfs:///example/jars`, Azure Storage는 `wasb:///example/jars`입니다.

### <a name="bPS7"></a>STDOUT 및 STDERR에 정보 쓰기

HDInsight는 STDOUT 및 STDERR로 작성된 스크립트 출력을 기록합니다. Ambari 웹 UI를 사용하여 이 정보를 볼 수 있습니다.

> [!NOTE]  
> Apache Ambari는 클러스터를 정상적으로 만든 경우에만 사용할 수 있습니다. 클러스터를 만드는 동안 스크립트 작업을 사용하며 만들기에 실패하는 경우 문제 해결 섹션 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) 에서 로깅된 정보에 액세스하는 다른 방법을 확인해보세요.

대부분의 유틸리티 및 설치 패키지는 STDOUT 및 STDERR에 정보를 쓰지만 추가 로깅을 추가하려 할 수도 있습니다. 텍스트를 STDOUT에 보내려면 `echo`를 사용합니다. 예를 들면 다음과 같습니다.

```bash
echo "Getting ready to install Foo"
```

기본적으로 `echo`는 STDOUT에 문자열을 보냅니다. STDERR에 전달하려면 `echo` 앞에 `>&2`를 추가합니다. 예를 들면 다음과 같습니다.

```bash
>&2 echo "An error occurred installing Foo"
```

이는 STDOUT에 작성된 정보를 STDERR(2)로 대신 리디렉션합니다. IO 리디렉션에 대한 자세한 내용은 [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)을 참조하세요.

스크립트 동작에서 기록된 정보 보기에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> 줄 끝을 LF인 파일을 ASCII로 저장

Bash 스크립트는 LF에서 종료한 줄을 사용하여 ASCII 형식으로 저장되어야 합니다. UTF-8로 저장되거나 줄 끝으로 CRLF를 사용하는 파일은 다음 오류와 함께 실패할 수 있습니다.

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> 다시 시도 논리를 사용하여 일시적 오류에서 복구

파일을 다운로드할 때 apt-get 또는 인터넷을 통해 데이터를 전송하는 기타 작업을 사용하여 패키지를 설치하면 일시적인 네트워킹 오류로 인해 작업이 실패할 수 있습니다. 예를 들어 통신하는 원격 리소스가 백업 노드로의 장애 조치(failover) 중일 수 있습니다.

스크립트를 일시적인 오류에 대해 탄력적으로 만들려면 다시 시도 논리를 구현할 수 있습니다. 다음 함수는 재시도 논리를 구현하는 방법을 보여 줍니다. 실패하기 전에 작업을 세 번 다시 시도합니다.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

다음 예제에서는 이 함수를 사용하는 방법을 보여 줍니다.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>사용자 지정 스크립트에 대한 도우미 메서드

스크립트 작업 도우미 메서드는 사용자 지정 스크립트를 쓰는 동안 사용할 수 있는 유틸리티입니다. 이러한 메서드는 [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) 스크립트에 포함되어 있습니다. 다음을 사용하여 다운로드하고 스크립트의 일부로 사용합니다.

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

스크립트에서 사용하기 위해 다음 도우미를 사용할 수 있습니다.

| 도우미 사용 | 설명 |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |원본 URI에서 지정된 파일 경로로 파일을 다운로드합니다. 기본적으로 기존 파일을 덮어쓰지 않습니다. |
| `untar_file TARFILE DESTDIR` |(`-xf`를 사용하여) 대상 디렉터리에 tar 파일을 추출합니다. |
| `test_is_headnode` |클러스터 헤드 노드에서 실행한 경우 1을 반환하고 그렇지 않으면 0을 반환합니다. |
| `test_is_datanode` |현재 노드가 데이터(작업자) 노드인 경우 1을 반환하고 그렇지 않으면 0을 반환합니다. |
| `test_is_first_datanode` |현재 노드가 첫 번째 데이터(작업자) 노드(workernode0이라는 이름)인 경우 1을 반환하고 그렇지 않으면 0을 반환합니다. |
| `get_headnodes` |클러스터에서 헤드 노드의 정규화된 도메인 이름을 반환합니다. 이름은 쉼표로 구분됩니다. 빈 문자열이 오류에 반환됩니다. |
| `get_primary_headnode` |기본 헤드 노드의 정규화된 도메인 이름을 가져옵니다. 빈 문자열이 오류에 반환됩니다. |
| `get_secondary_headnode` |보조 헤드 노드의 정규화된 도메인 이름을 가져옵니다. 빈 문자열이 오류에 반환됩니다. |
| `get_primary_headnode_number` |기본 헤드 노드의 숫자 접미사를 가져옵니다. 빈 문자열이 오류에 반환됩니다. |
| `get_secondary_headnode_number` |보조 헤드 노드의 숫자 접미사를 가져옵니다. 빈 문자열이 오류에 반환됩니다. |

## <a name="commonusage"></a>일반적인 사용 패턴

이 섹션에서는 사용자 고유의 사용자 지정 스크립트를 작성하는 동안 실행할 수 있는 일반적인 사용 패턴 중 일부를 구현하는 방법에 대한 지침을 제공합니다.

### <a name="passing-parameters-to-a-script"></a>스크립트에 매개 변수 전달

경우에 따라 스크립트에 매개 변수가 필요할 수 있습니다. 예를 들어 Ambari REST API를 사용하는 경우 클러스터에 대한 관리자 암호가 필요할 수 있습니다.

스크립트에 전달된 매개 변수는 *위치 매개 변수*로 알려져 있으며 첫 번째 매개 변수의 경우 `$1`, 두 번째는 `$2` 등에 할당됩니다. `$0` 는 스크립트 자체의 이름을 포함합니다.

매개 변수로 스크립트에 전달되는 값은 작은따옴표(')로 묶여야 합니다. 이렇게 하면 전달된 값이 리터럴로 처리됩니다.

### <a name="setting-environment-variables"></a>환경 변수 설정

환경 변수의 설정은 다음 문으로 수행됩니다.

    VARIABLENAME=value

여기서 VARIABLENAME은 변수의 이름입니다. 변수에 액세스하려면 `$VARIABLENAME`을 사용합니다. 예를 들어 위치 매개 변수에서 제공하는 값을 PASSWORD라는 환경 변수로 할당하려면 다음 문을 사용합니다.

    PASSWORD=$1

이후 정보에 액세스하려면 `$PASSWORD`를 사용합니다.

스크립트 내에서 설정된 환경 변수는 스크립트의 범위 내에만 존재합니다. 경우에 따라 스크립트가 완료된 후에 유지되는 시스템 수준 환경 변수를 추가해야 할 수 있습니다. 시스템 수준 환경 변수를 추가하려면 `/etc/environment`에 변수를 추가합니다. 예를 들어 다음 문은 `HADOOP_CONF_DIR`을 추가합니다.

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>사용자 지정 스크립트가 저장된 위치 액세스

클러스터를 사용자 지정하는 데 사용되는 스크립트는 다음 위치 중 하나에 저장해야 합니다.

* 클러스터와 연결된 __Azure Storage 계정__

* 클러스터와 연결된 __추가 저장소 계정__

* __공개적으로 읽을 수 있는 URI__ 예를 들어 OneDrive, Dropbox 또는 다른 파일 호스팅 서비스에 저장된 데이터에 대한 URL

* HDInsight 클러스터와 연결된 __Azure Data Lake Storage 계정__ HDInsight에서 Azure Data Lake Storage를 사용하는 방법에 대한 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.

    > [!NOTE]  
    > HDInsight에서 Data Lake Storage에 액세스하는 데 사용하는 서비스 주체에는 스크립트에 대한 읽기 권한이 있어야 합니다.

스크립트에서 사용되는 리소스는 공개적으로도 사용할 수 있어야 합니다.

Azure Storage 계정 또는 Azure Data Lake Storage에서 파일을 저장하면 Azure 네트워크 내에서 두 가지 모두 빠른 액세스를 제공합니다.

> [!NOTE]  
> 스크립트를 참조하는 데 사용되는 URI 형식은 사용 중인 서비스에 따라 다릅니다. HDInsight 클러스터와 연결된 저장소 계정의 경우 `wasb://` 또는 `wasbs://`를 사용합니다. 공개적으로 읽을 수 있는 URI의 경우 `http://` 또는 `https://`를 사용합니다. Data Lake Storage의 경우 `adl://`을 사용합니다.

## <a name="deployScript"></a>스크립트 작업 배포를 위한 검사 목록

이러한 스크립트 배포를 준비할 때 수행하는 단계는 다음과 같습니다.

* 사용자 지정 스크립트가 포함된 파일을 배포 중 클러스터 노드에서 액세스할 수 있는 위치에 배치합니다. 예를 들어 클러스터의 기본 저장소입니다. 공개적으로 읽을 수 있는 호스팅 서비스에 파일을 저장할 수도 있습니다.
* 스크립트가 idempotent인지 확인합니다. 이렇게 하면 스크립트가 동일한 노드에서 여러 번 실행될 수 있습니다.
* 임시 파일 디렉터리 /tmp를 사용하여 스크립트에서 사용되는 다운로드된 파일을 보관하고 스크립트가 실행된 후 이 파일을 정리합니다.
* OS 수준 설정 또는 Hadoop 서비스 구성 파일이 변경되면 HDInsight 서비스를 다시 시작할 수 있습니다.

## <a name="runScriptAction"></a>스크립트 작업을 실행하는 방법

스크립트 작업을 사용하여 다음 메서드를 사용하여 HDInsight 클러스터를 사용자 지정할 수 있습니다.

* Azure portal
* Azure PowerShell
* Azure 리소스 관리자 템플릿
* HDInsight .NET SDK

각 메서드 사용에 대한 자세한 내용은 [스크립트 작업을 사용하는 방법](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="sampleScripts"></a>사용자 지정 스크립트 샘플

Microsoft에서는 HDInsight 클러스터에 구성 요소를 설치하는 샘플 스크립트를 제공합니다. 더 많은 예제 스크립트 작업은 다음 링크를 참조하세요.

* [HDInsight에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)
* [HDInsight 클러스터에 Apache Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)

## <a name="troubleshooting"></a>문제 해결

다음은 개발한 스크립트를 사용할 때 발생할 수 있는 오류입니다.

**오류**: `$'\r': command not found`. 때로는 `syntax error: unexpected end of file`이 이어집니다.

*원인*: 이 오류는 스크립트에서 줄이 CRLF로 끝날 때 발생합니다. Unix 시스템은 줄 끝에 LF이 필요합니다.

스크립트가 Windows 환경에서 작성된 경우 CRLF은 Windows의 많은 텍스트 편집기에서 줄 끝에 흔하게 쓰이기 때문에 이 문제가 가장 자주 발생합니다.

*해결 방법*: 텍스트 편집기에서 옵션인 경우 줄 끝에 LF 또는 Unix 형식을 선택합니다. 또한 Unix 시스템에서 다음 명령을 사용하여 CRLF는 LF로 변경할 수 있습니다.

> [!NOTE]  
> 다음 명령은 CRLF 줄 끝을 LF으로 변경해야 하는 것과 거의 동일합니다. 시스템에서 사용할 수 있는 유틸리티에 따라 하나를 선택합니다.

| 명령 | 메모 |
| --- | --- |
| `unix2dos -b INFILE` |원본 파일이 .BAK 확장으로 백업됩니다. |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE은 끝이 LF인 버전만 포함합니다. |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | 파일을 직접 수정 |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE은 끝이 LF인 버전만 포함합니다. |

**오류**: `line 1: #!/usr/bin/env: No such file or directory`.

*원인*: 스크립트가 바이트 순서 표시(BOM)를 사용하여 UTF-8로 저장될 때 이 오류가 발생합니다.

*해결 방법*: ASCII로 또는 BOM을 사용하지 않고 UTF-8로 파일을 저장합니다. Linux 또는 Unix 시스템에서 다음 명령을 사용하여 BOM을 사용하지 않고 파일을 만들 수도 있습니다.

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

`INFILE`을 BOM을 포함하는 파일로 대체합니다. `OUTFILE`은 BOM을 사용하지 않고 스크립트를 포함하는 새 파일 이름이어야 합니다.

## <a name="seeAlso"></a>다음 단계

* [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)
* [HDInsight.NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)를 사용하여 HDInsight를 관리하는 .NET 애플리케이션을 만드는 방법을 알아봅니다.
* [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) 를 사용하여 REST를 통해 HDInsight 클러스터에서 관리 작업을 수행하는 방법을 알아봅니다.
