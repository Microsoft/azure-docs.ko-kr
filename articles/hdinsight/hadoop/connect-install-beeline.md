---
title: Apache Beeline에 연결 또는 설치-Azure HDInsight
description: Apache Beeline 클라이언트에 연결 하 여 HDInsight에서 Hadoop으로 Hive 쿼리를 실행 하는 방법에 대해 알아봅니다. Beeline은 JDBC를 통한 HiveServer2 작업을 위한 유틸리티입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperfq1
ms.date: 05/27/2020
ms.openlocfilehash: 5495e6c6392ba2e824a0a70717bd19747db9b754
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754959"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>HDInsight의 Apache Beeline에 연결 하거나 로컬로 설치

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 는 HDInsight 클러스터의 헤드 노드에 포함 된 Hive 클라이언트입니다. 이 문서에서는 다양 한 유형의 연결에서 HDInsight 클러스터에 설치 된 Beeline 클라이언트에 연결 하는 방법을 설명 합니다. [Beeline 클라이언트를 로컬로 설치](#install-beeline-client)하는 방법에 대해서도 설명 합니다. 

## <a name="types-of-connections"></a>연결 유형

### <a name="from-an-ssh-session"></a>SSH 세션에서

SSH 세션에서 클러스터 헤드 노드에 연결 하는 경우 포트에서 주소에 연결할 수 있습니다 `headnodehost` `10001` .

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>Azure Virtual Network에서

Azure Virtual Network를 통해 클라이언트에서 HDInsight로 연결 하는 경우 클러스터 헤드 노드의 FQDN (정규화 된 도메인 이름)을 제공 해야 합니다. 이 연결은 클러스터 노드로 직접 설정되므로 연결은 포트 `10001`을 사용합니다.

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

을 `<headnode-FQDN>` 클러스터 헤드 노드의 정규화 된 도메인 이름으로 바꿉니다. 헤드 노드의 정규화된 도메인 이름을 찾으려면 [Apache Ambari REST API를 사용하여 HDInsight 관리](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) 문서의 정보를 사용합니다.

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Kerberos를 사용 하는 HDInsight Enterprise Security Package (ESP) 클러스터

클라이언트에서 클러스터의 동일한 영역에 있는 컴퓨터의 Azure Active Directory (AAD)-DS에 연결 된 Enterprise Security Package (ESP) 클러스터에 연결 하는 경우 `<AAD-Domain>` 클러스터에 액세스할 수 있는 권한이 있는 도메인 사용자 계정의 이름 및 도메인 이름도 지정 해야 합니다 `<username>` .

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>`을 클러스터에 액세스할 수 있는 권한이 있는 도메인의 계정 이름으로 바꿉니다. `<AAD-DOMAIN>`을 클러스터가 조인된 AAD(Azure Active Directory)의 이름으로 바꿉니다. `<AAD-DOMAIN>` 값에 대문자 문자열을 사용합니다. 그렇지 않으면 자격 증명을 찾을 수 없습니다. 필요하다면 영역 이름에 대한 `/etc/krb5.conf`를 확인합니다.

Ambari에서 JDBC URL을 찾으려면 다음을 수행 합니다.

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다. HiveServer2이 실행 중인지 확인 합니다.

1. 클립보드를 사용 하 여 HiveServer2 JDBC URL을 복사 합니다.

### <a name="over-public-or-private-endpoints"></a>공용 또는 개인 끝점을 통해

공용 또는 개인 끝점을 사용 하 여 클러스터에 연결 하는 경우 클러스터 로그인 계정 이름 (기본값) 및 암호를 제공 해야 합니다 `admin` . 예를 들어 클라이언트 시스템에서 Beeline을 사용하여 `clustername.azurehdinsight.net` 주소에 연결합니다. 이 연결은 포트를 통해 이루어지며 `443` TLS/SSL을 사용 하 여 암호화 됩니다.

`clustername`을 HDInsight 클러스터 이름으로 바꿉니다. `admin`을 클러스터의 클러스터 로그인 계정으로 바꿉니다. ESP 클러스터의 경우 전체 UPN (예:)을 사용 user@domain.com 합니다. `password`를 클러스터 로그인 계정의 암호로 바꿉니다.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

또는 개인 끝점의 경우:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

개인 끝점은 동일한 지역의 Vnet 피어 링 에서만 액세스할 수 있는 기본 부하 분산 장치를 가리킵니다. 자세한 정보는 [글로벌 VNet 피어 링 및 부하 분산 장치에 대 한 제약 조건](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 을 참조 하세요. `curl`Beeline를 사용 하기 전에 옵션과 함께 명령을 사용 하 여 `-v` 공용 또는 개인 끝점의 연결 문제를 해결할 수 있습니다.

### <a name="use-beeline-with-apache-spark"></a>Apache Spark와 함께 Beeline 사용

Apache Spark는 자체적으로 HiveServer2를 구현하며, HiveServer2는 종종 Spark Thrift 서버라고 합니다. 이 서비스는 Spark SQL을 사용 하 여 Hive 대신 쿼리를 확인 합니다. 및는 쿼리에 따라 더 나은 성능을 제공할 수 있습니다.

#### <a name="through-public-or-private-endpoints"></a>공용 또는 개인 끝점을 통해

사용 된 연결 문자열은 약간 다릅니다. 를 포함 하는 대신를 `httpPath=/hive2` 사용 `httpPath/sparkhive2` 합니다. `clustername`을 HDInsight 클러스터 이름으로 바꿉니다. `admin`을 클러스터의 클러스터 로그인 계정으로 바꿉니다. ESP 클러스터의 경우 전체 UPN (예:)을 사용 user@domain.com 합니다. `password`를 클러스터 로그인 계정의 암호로 바꿉니다.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

또는 개인 끝점의 경우:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

개인 끝점은 동일한 지역의 Vnet 피어 링 에서만 액세스할 수 있는 기본 부하 분산 장치를 가리킵니다. 자세한 정보는 [글로벌 VNet 피어 링 및 부하 분산 장치에 대 한 제약 조건](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 을 참조 하세요. `curl`Beeline를 사용 하기 전에 옵션과 함께 명령을 사용 하 여 `-v` 공용 또는 개인 끝점의 연결 문제를 해결할 수 있습니다.

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Apache Spark를 사용 하 여 클러스터 헤드 또는 Azure Virtual Network 내에서

클러스터 헤드 노드에서 직접 연결하거나 HDInsight 클러스터와 동일한 Azure Virtual Network 내부의 리소스에서 연결하는 경우 `10001` 포트 대신 `10002` 포트를 Spark Thrift 서버에 사용해야 합니다. 다음 예제에서는 헤드 노드에 직접 연결 하는 방법을 보여 줍니다.

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>Beeline client 설치

Beeline는 헤드 노드에 포함 되지만 로컬에서 설치 하는 것이 좋습니다.  로컬 컴퓨터의 설치 단계는 [Linux 용 Windows 하위 시스템](https://docs.microsoft.com/windows/wsl/install-win10)을 기반으로 합니다.

1. 패키지 목록을 업데이트 합니다. Bash 셸에서 다음 명령을 입력 합니다.

    ```bash
    sudo apt-get update
    ```

1. 설치 되지 않은 경우 Java를 설치 합니다. 명령을 사용 하 여 확인할 수 있습니다 `which java` .

    1. Java 패키지를 설치 하지 않은 경우 다음 명령을 입력 합니다.

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. .Bashrc 파일을 엽니다 (종종 ~/.bashrc에 있음 `nano ~/.bashrc` ).

    1. .Bashrc 파일을 수정 합니다. 파일 끝에 다음 줄을 추가합니다.

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        그런 다음 **ctrl + X**, **Y**를 차례로 누른 다음를 입력 합니다.

1. Hadoop 및 Beeline 보관 파일을 다운로드 하 고 다음 명령을 입력 합니다.

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 보관 파일의 압축을 풀고 다음 명령을 입력 합니다.

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. .Bashrc 파일을 추가로 수정 합니다. 보관 파일의 압축을 푼 경로를 확인 해야 합니다. [Linux 용 Windows 하위 시스템](https://docs.microsoft.com/windows/wsl/install-win10)을 사용 하 고 정확한 단계를 수행한 경우 경로는 `/mnt/c/Users/user/` `user` 입니다. 여기서는 사용자 이름입니다.

    1. 다음 파일을 엽니다. `nano ~/.bashrc`

    1. 적절 한 경로를 사용 하 여 아래 명령을 수정한 후 .bashrc 파일의 끝에 입력 합니다.

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 그런 다음 **ctrl + X**, **Y**를 차례로 누른 다음를 입력 합니다.

1. Bash 세션을 닫았다가 다시 엽니다.

1. 연결을 테스트 합니다. 위의 [공용 또는 개인 끝점](#over-public-or-private-endpoints)에서 연결 형식을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* Apache Hive와 함께 Beeline client를 사용 하는 예제는 [Apache Hive Apache Beeline 사용](apache-hadoop-use-hive-beeline.md) 을 참조 하세요.
* HDInsight의 Hive에 대 한 일반적인 정보는 [hdinsight에서 Apache Hadoop와 Apache Hive 사용](hdinsight-use-hive.md) 을 참조 하세요.
