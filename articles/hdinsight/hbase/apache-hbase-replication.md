---
title: Azure 가상 네트워크에서 HBase 클러스터 복제 설정 - Azure HDInsight
description: 부하 분산, 고가용성, 무중단 마이그레이션/업데이트 및 재해 복구를 위해 한 HDInsight 버전에서 다른 HDInsight 버전으로 HBase 복제를 설정하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: 95a1055df283765b24322f6f8efe3efcb9b19022
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123085"
---
# <a name="set-up-apache-hbase-cluster-replication-in-azure-virtual-networks"></a>Azure Virtual Network에서 Apache HBase 클러스터 복제 설정

Azure에서 한 가상 네트워크 내에 또는 두 가상 네트워크 간에 [Apache HBase](https://hbase.apache.org/) 복제를 설정하는 방법을 알아봅니다.

클러스터 복제에서는 원본-푸시 방법을 사용합니다. HBase 클러스터는 원본 또는 대상이 될 수도 있고, 한 번에 두 가지 역할을 모두 수행할 수도 있습니다. 복제는 비동기적이며, 그 목표는 결과적으로 일관성에 맞춰져 있습니다. 복제를 사용하도록 설정된 경우 원본에서 열 패밀리에 대한 편집 내용을 받으면 해당 편집 내용이 모든 대상 클러스터로 전파됩니다. 한 클러스터에서 다른 클러스터로 데이터를 복제하는 경우 복제 루프를 방지하기 위해 데이터를 이미 사용한 원본 클러스터 및 모든 클러스터가 추적됩니다.

이 자습서에서는 원본-대상 복제를 설정합니다. 다른 클러스터 토폴로지는 [Apache HBase 참조 가이드(영문)](https://hbase.apache.org/book.html#_cluster_replication)를 참조하세요.

단일 가상 네트워크에 대한 HBase 복제 사용 사례는 다음과 같습니다.

* 부하 분산 - 예를 들어 대상 클러스터에서 검색 또는 MapReduce 작업을 실행하고 원본 클러스터에서 데이터를 수집할 수 있습니다.
* 고가용성 추가
* 하나의 HBase 클러스터에서 다른 HBase 클러스터로 데이터 마이그레이션
* 한 버전에서 다른 버전으로 Azure HDInsight 클러스터 업그레이드

두 가상 네트워크에 대한 HBase 복제 사용 사례는 다음과 같습니다.

* 재해 복구 설정
* 애플리케이션 부하 분산 및 분할
* 고가용성 추가

[GitHub](https://github.com/Azure/hbase-utils/tree/master/replication)에 있는 [스크립트 동작](../hdinsight-hadoop-customize-cluster-linux.md) 스크립트를 사용하여 클러스터를 복제할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. [Azure 평가판 받기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

## <a name="set-up-the-environments"></a>환경 설정

세 가지 구성 옵션이 있습니다.

- 1개 Azure Virtual Network에 2개 Apache HBase 클러스터 구성
- 동일한 지역에 있는 2개 가상 네트워크에 2개 Apache HBase 클러스터 구성
- 2개 지역에 있는 2개 가상 네트워크에 2개 Apache HBase 클러스터 구성(지역에서 복제)

이 문서에서는 지역에서 복제 시나리오에 대해 설명합니다.

환경 설정을 지원하기 위해 몇 가지 [Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-overview.md)을 만들었습니다. 다른 방법을 사용하여 환경을 설정하려면 다음을 참조하세요.

- [HDInsight에 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)
- [Azure Virtual Network에 Apache HBase 클러스터 만들기](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>별도의 2개 지역에 2개 가상 네트워크 설정

두 개의 서로 다른 지역에 두 개의 가상 네트워크를 만들고 VNet 간에 VPN 연결을 만드는 템플릿을 사용하려면 다음 **Azure에 배포** 단추를 선택합니다. 템플릿 정의는 [공용 Blob Storage](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)에 저장됩니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

템플릿의 하드 코드된 일부 값:

**VNet 1**

| 자산 | 값 |
|----------|-------|
| Location | 미국 서부 |
| VNet 이름 | &lt;ClusterNamePrevix>-vnet1 |
| 주소 공간 접두사 | 10.1.0.0/16 |
| 서브넷 이름 | 서브넷 1 |
| 서브넷 접두사 | 10.1.0.0/24 |
| 서브넷(게이트웨이) 이름 | GatewaySubnet(변경할 수 없음) |
| 서브넷(게이트웨이) 접두사 | 10.1.255.0/27 |
| 게이트웨이 이름 | vnet1gw |
| 게이트웨이 유형 | Vpn |
| 게이트웨이 VPN 유형 | 경로 기반 |
| 게이트웨이 SKU | Basic |
| 게이트웨이 IP | vnet1gwip |

**VNet 2**

| 자산 | 값 |
|----------|-------|
| Location | 미국 동부 |
| VNet 이름 | &lt;ClusterNamePrevix>-vnet2 |
| 주소 공간 접두사 | 10.2.0.0/16 |
| 서브넷 이름 | 서브넷 1 |
| 서브넷 접두사 | 10.2.0.0/24 |
| 서브넷(게이트웨이) 이름 | GatewaySubnet(변경할 수 없음) |
| 서브넷(게이트웨이) 접두사 | 10.2.255.0/27 |
| 게이트웨이 이름 | vnet2gw |
| 게이트웨이 유형 | Vpn |
| 게이트웨이 VPN 유형 | 경로 기반 |
| 게이트웨이 SKU | Basic |
| 게이트웨이 IP | vnet1gwip |

## <a name="setup-dns"></a>DNS 설정

마지막 섹션에서 템플릿은 두 가상 네트워크 각각에서 Ubuntu 가상 머신을 만듭니다.  이 섹션에서는 두 DNS 가상 머신에서 바인딩을 설치한 다음, 두 가상 머신에서 DNS 전달을 구성합니다.

바인딩을 설치하려면 두 DNS 가상 머신의 공용 IP 주소를 찾아야 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. **리소스 그룹 > [리소스 그룹 이름] > [vnet1DNS]** 를 선택하여 DNS 가상 머신을 엽니다.  리소스 그룹 이름은 마지막 절차에서 만든 이름입니다. 기본 DNS 가상 머신 이름은 *vnet1DNS* 및 *vnet2NDS*입니다.
3. **속성**을 선택하여 가상 네트워크의 속성 페이지를 엽니다.
4. **공용 IP 주소**를 적어 두고 **개인 IP 주소**를 확인합니다.  개인 IP 주소는 vnet1DNS에 대해 **10.1.0.4**이고 vnet2DNS에 대해 **10.2.0.4**여야 합니다.  
5. 기본(Azure 제공) DNS 서버를 사용하도록 두 가상 네트워크에 대한 DNS 서버를 변경합니다. 그러면 다음 단계에서 바인딩을 설치하기 위한 패키지를 다운로드하도록 인바운드 및 아웃바운드 액세스가 허용됩니다.

바인딩을 설치하려면 다음 절차를 따릅니다.

1. SSH를 사용하여 DNS 가상 머신의 __공용 IP 주소__에 연결합니다. 다음 예제에서는 40.68.254.142에서 가상 머신에 연결합니다.

    ```bash
    ssh sshuser@40.68.254.142
    ```

    `sshuser`을 DNS 가상 머신을 만들 때 지정한 SSH 사용자 계정으로 바꿉니다.

    > [!NOTE]  
    > 다양한 방법으로 `ssh` 유틸리티를 가져올 수 있습니다. Linux, Unix 및 macOS에서 운영 체제의 일부분으로 제공됩니다. Windows를 사용하는 경우 다음 옵션 중 하나를 고려하세요.
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Windows 10에서 Ubuntu의 Bash](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git(https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH(https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Bind를 설치하려면 SSH세션에서 다음 명령을 사용합니다.

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 온-프레미스 DNS 서버로 이름 확인 요청을 전달 하도록 바인딩을 구성 합니다. 이를 수행하려면 다음 텍스트를 `/etc/bind/named.conf.options` 파일의 콘텐츠로 사용합니다.

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16; #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]  
    > `goodclients` 섹션의 값을 두 가상 네트워크의 IP 주소 범위로 바꿉니다. 이 섹션에서는 이 DNS 서버가 요청을 수락하는 주소를 정의합니다.

    이 파일을 편집하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    파일을 저장하려면 __Ctrl+X__, __Y__ 및 __Enter__ 키를 사용합니다.

4. SSH 세션에서 다음 명령을 사용합니다.

    ```bash
    hostname -f
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 텍스트는 이 가상 네트워크에 대한 __DNS 접미사__입니다. 나중에 사용하므로 이 값을 저장합니다.

    다른 DNS 서버에서 DNS 접미사도 확인해야 합니다. 다음 단계에서 필요합니다.

5. 가상 네트워크 내에서 리소스의 DNS 이름을 확인하도록 바인딩을 구성하려면 `/etc/bind/named.conf.local` 파일의 내용에 다음과 같은 텍스트를 사용합니다.

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net`을 다른 가상 네트워크의 DNS 접미사로 바꿔야 합니다. 전달자 IP는 다른 가상 네트워크에서 DNS 서버의 개인 IP 주소입니다.

    이 파일을 편집하려면 다음 명령을 사용합니다.

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    파일을 저장하려면 __Ctrl+X__, __Y__ 및 __Enter__ 키를 사용합니다.

6. Bind를 시작하려면 다음 명령을 사용합니다.

    ```bash
    sudo service bind9 restart
    ```

7. 바인딩이 다른 가상 네트워크에서 리소스 이름을 해결할 수 있는지 확인하려면 다음 명령을 사용합니다.

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    ```

    > [!IMPORTANT]  
    > `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net`를 다른 네트워크의 DNS 가상 머신의 FQDN(정규화된 도메인 이름)으로 바꿉니다.
    >
    > `10.2.0.4`를 다른 가상 네트워크에 있는 사용자 지정 DNS 서버의 __내부 IP 주소__로 바꿉니다.

    응답은 다음 텍스트와 유사합니다.

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    지금까지 지정된 DNS 서버 IP 주소 없이는 다른 네트워크에서 IP 주소를 찾을 수 없습니다.

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>사용자 지정 DNS 서버를 사용하도록 가상 네트워크 구성

Azure Recursive Resolver 대신 사용자 지정 DNS 서버를 사용하도록 가상 네트워크를 구성하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 네트워크를 선택한 다음 __DNS 서버__를 선택합니다.

2. __사용자 지정__을 선택하고 사용자 지정 DNS 서버의 __내부 IP 주소__를 입력합니다. 마지막으로 __저장__을 선택합니다.

6. vnet1에서 DNS 서버 가상 머신을 열고 **다시 시작**을 클릭합니다.  DNS 구성을 적용하려면 가상 네트워크에서 모든 가상 머신을 다시 시작해야 합니다.
7. vnet2에 대한 사용자 지정 DNS 서버를 구성하는 단계를 반복합니다.

DNS 구성을 테스트하려면 SSH를 사용하여 두 DNS 가상 머신에 연결하고, 해당 호스트 이름을 사용하여 다른 가상 네트워크의 DNS 서버를 ping할 수 있습니다. 작동하지 않으면 다음 명령을 사용하여 DNS 상태를 확인합니다.

```bash
sudo service bind9 status
```

## <a name="create-apache-hbase-clusters"></a>Apache HBase 클러스터 만들기

다음 구성을 사용하여 두 가상 네트워크에 각각 [Apache HBase](https://hbase.apache.org/) 클러스터를 만듭니다.

- **리소스 그룹 이름**: 가상 네트워크를 만들 때 동일한 리소스 그룹 이름을 사용합니다.
- **클러스터 유형**: HBase
- **버전**: HBase 1.1.2(HDI 3.6)
- **위치**: 가상 네트워크와 동일한 위치를 사용합니다.  기본적으로 vnet1은 *미국 서부*이고, vnet2는 *미국 동부*입니다.
- **저장소**: 클러스터에 대한 새 스토리지 계정을 만듭니다.
- **가상 네트워크**(포털에 대한 고급 설정에서): 마지막 프로시저에서 만든 vnet1를 선택합니다.
- **서브넷**: 템플릿에서 사용하는 기본 이름은 **subnet1**입니다.

환경을 올바르게 구성했는지 확인하려면 두 클러스터 간 헤드 노드의 FQDN을 ping할 수 있어야 합니다.

## <a name="load-test-data"></a>테스트 데이터 로드

클러스터를 복제할 때 복제하려는 테이블을 지정해야 합니다. 이 섹션에서는 일부 데이터를 원본 클러스터에 로드합니다. 다음 섹션에서는 두 클러스터 간에 복제를 사용하도록 설정합니다.

**Contacts**(연락처) 테이블을 만들고 이 테이블에 데이터를 삽입하려면 [HBase 자습서: HDInsight의 Apache HBase 사용 시작](apache-hbase-tutorial-get-started-linux.md)의 지침을 따릅니다.

## <a name="enable-replication"></a>복제 사용

다음 단계에서는 Azure Portal에서 스크립트 동작 스크립트를 호출하는 방법을 설명합니다. Azure PowerShell과 Azure 클래식 CLI를 사용하여 스크립트 동작을 실행하는 방법에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](../hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

**Azure Portal에서 HBase 복제를 사용하도록 설정하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원본 HBase 클러스터를 엽니다.
3. 클러스터 메뉴에서 **스크립트 동작**을 선택합니다.
4. 페이지의 위쪽에서 **새로운 항목 제출**을 선택합니다.
5. 다음 정보를 선택하거나 입력합니다.

   1. **이름**: **복제 사용**을 입력합니다.
   2. **Bash 스크립트 URL**: **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**을 입력합니다.
   3. **헤드**: 선택되어 있는지 확인합니다. 다른 노드 형식은 선택 취소합니다.
   4. **매개 변수**: 다음 샘플 매개 변수를 사용하면 기존의 모든 테이블을 복제하도록 설정한 다음, 모든 데이터를 원본 클러스터에서 대상 클러스터로 복사합니다.

          -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
      > [!NOTE]
      > 원본 및 대상 클러스터 DNS 이름에 대한 FQDN 대신 호스트 이름을 사용합니다.

6. **만들기**를 선택합니다. 특히 **-copydata** 인수를 사용하는 경우 스크립트를 실행하는 데 약간의 시간이 걸릴 수 있습니다.

필수 인수:

|이름|설명|
|----|-----------|
|-s, --src-cluster | HBase 원본 클러스터의 DNS 이름을 지정합니다. 예: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | HBase 대상(복제본) 클러스터의 DNS 이름을 지정합니다. 예: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | HBase 원본 클러스터에서 Ambari의 관리자 암호를 지정합니다. |
|-dp, --dst-ambari-password | HBase 대상 클러스터에서 Ambari의 관리자 암호를 지정합니다.|

선택적 인수:

|이름|설명|
|----|-----------|
|-su, --src-ambari-user | HBase 원본 클러스터에서 Ambari에 대한 관리 사용자 이름을 지정합니다. 기본값은 **admin**입니다. |
|-du, --dst-ambari-user | HBase 대상 클러스터에서 Ambari에 대한 관리 사용자 이름을 지정합니다. 기본값은 **admin**입니다. |
|-t, --table-list | 복제할 테이블을 지정합니다. 예: --table-list="table1;table2;table3". 테이블을 지정하지 않으면 기존의 모든 HBase 테이블을 복제합니다.|
|-m, --machine | 스크립트 동작이 실행되는 헤드 노드를 지정합니다. 값은 **hn0** 또는 **hn1**이며 활성 헤드 노드를 기준으로 선택되어야 합니다. HDInsight 포털 또는 Azure PowerShell에서 $0 스크립트를 스크립트 동작으로 실행하는 경우 이 옵션을 사용합니다.|
|-cp, -copydata | 복제가 사용되는 테이블에서 기존 데이터의 마이그레이션을 사용하도록 설정합니다. |
|-rpm, -replicate-phoenix-meta | Phoenix 시스템 테이블에서 복제를 사용하도록 설정합니다. <br><br>*이 옵션은 주의해서 사용해야 합니다.* 이 스크립트를 사용하기 전에 복제본 클러스터에서 Phoenix 테이블을 다시 만드는 것이 좋습니다. |
|-h, --help | 사용 정보를 표시합니다. |

[스크립트](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh)의 `print_usage()` 섹션에는 매개 변수에 대한 자세한 설명이 있습니다.

스크립트 동작이 성공적으로 배포된 후에 SSH를 사용하여 HBase 대상 클러스터에 연결한 다음 데이터가 복제되었는지 확인할 수 있습니다.

### <a name="replication-scenarios"></a>복제 시나리오

다음 목록에서는 일반적인 사용 사례 및 해당 매개 변수 설정을 보여 줍니다.

- **두 클러스터 간의 모든 테이블에서 복제를 사용하도록 설정** - 이 시나리오에서는 테이블의 기존 데이터를 복사하거나 마이그레이션할 필요가 없으며 Phoenix 테이블을 사용하지 않습니다. 다음 매개 변수를 사용합니다.

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **특정 테이블에서 복제를 사용하도록 설정** - table1, table2 및 table3에서 복제를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **특정 테이블에서 복제를 사용하도록 설정하고 기존 데이터 복사** - table1, table2 및 table3에서 복제를 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **모든 테이블에서 복제를 사용하도록 설정하고 원본에서 대상으로 Phoenix 메타데이터 복제** - Phoenix 메타데이터 복제는 완벽하지 않으므로 주의해서 사용하세요. 다음 매개 변수를 사용합니다.

        -m hn1 -s <source hbase cluster name> -d <destination hbase cluster name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>데이터 복사 및 마이그레이션

복제를 사용하도록 설정한 후에 데이터를 복사하거나 마이그레이션하는 데 사용할 수 있는 별도의 두 가지 스크립트 동작 스크립트가 있습니다.

- [소형 테이블용 스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh)(크기가 수 GB이고, 전체 복사를 완료하는 데 1시간 미만이 걸릴 것으로 예상되는 테이블)

- [대형 테이블용 스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh)(복사하는 데 1시간 이상 걸릴 것으로 예상되는 테이블)

[복제 사용](#enable-replication)에서 설명한 것과 동일한 절차에 따라 스크립트 동작을 호출할 수 있습니다. 다음 매개 변수를 사용합니다.

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

[스크립트](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh)의 `print_usage()` 섹션에는 매개 변수에 대한 자세한 설명이 있습니다.

### <a name="scenarios"></a>시나리오

- **지금(현재 타임스탬프)까지 편집된 모든 행에 대한 특정 테이블(test1, test2 및 test3) 복사**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  또는

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **지정된 시간 범위의 특정 테이블 복사**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>복제 사용 안 함

복제를 사용하지 않도록 설정하려면 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)에 있는 다른 스크립트 동작 스크립트를 사용합니다. [복제 사용](#enable-replication)에서 설명한 것과 동일한 절차에 따라 스크립트 동작을 호출할 수 있습니다. 다음 매개 변수를 사용합니다.

    -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

[스크립트](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)의 `print_usage()` 섹션에는 매개 변수에 대한 자세한 설명이 있습니다.

### <a name="scenarios"></a>시나리오

- **모든 테이블에서 복제를 사용하지 않도록 설정**:

        -m hn1 -s <source hbase cluster name> -sp Mypassword\!789 -all
  또는

        --src-cluster=<source hbase cluster name> --dst-cluster=<destination hbase cluster name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **지정된 테이블(table1, table2 및 table3)에서 복제를 사용하지 않도록 설정**:

        -m hn1 -s <source hbase cluster name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 한 가상 네트워크 내에 또는 두 가상 네트워크 간에 Apache HBase 복제를 설정하는 방법을 알아보았습니다. HDInsight 및 Apache HBase에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache HBase 시작](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight Apache HBase 개요](./apache-hbase-overview.md)
* [Azure Virtual Network에 Apache HBase 클러스터 만들기](./apache-hbase-provision-vnet.md)

