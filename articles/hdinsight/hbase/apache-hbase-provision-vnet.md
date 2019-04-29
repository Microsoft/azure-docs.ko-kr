---
title: Virtual Network에 HBase 클러스터 만들기 - Azure
description: Azure HDInsight에서 HBase 사용 시작 Azure Virtual Network에 HDInsight HBase 클러스터를 만드는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 85eaa81a0cfd7ccfe8ad3ae818f89966280d279e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123113"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Azure Virtual Network의 HDInsight에서 Apache HBase 클러스터 만들기
[Azure Virtual Network][1]에서 Azure HDInsight Apache HBase 클러스터를 만드는 방법을 알아봅니다.

가상 네트워크 통합을 사용하면 애플리케이션이 HBase와 직접 통신할 수 있도록 애플리케이션과 동일한 가상 네트워크에 Apache HBase 클러스터를 배포할 수 있습니다. 이점은 다음과 같습니다.

* 웹 애플리케이션을 HBase 클러스터의 노드에 직접 연결하므로 HBase Java RPC(원격 프로시저 호출) API를 통해 통신할 수 있습니다.
* 트래픽이 여러 게이트웨이 및 부하 분산 장치를 통과하지 않도록 하여 성능을 향상시킵니다.
* 공용 엔드포인트를 노출하지 않고 중요한 정보를 보다 안전한 방식으로 처리할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>가상 네트워크에 Apache HBase 클러스터 만들기
이 섹션에서는 [Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-template-deploy.md)을 사용하여 Azure 가상 네트워크에서 종속 Azure Storage 계정으로 Linux 기반 Apache HBase 클러스터를 만듭니다. 기타 클러스터 생성 방법 및 설정에 대한 이해는 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

> [!NOTE]  
> 일부 속성이 템플릿에 하드 코딩되었습니다. 예를 들면 다음과 같습니다.
>
> * **위치**: 미국 동부 2
> * **클러스터 버전**: 3.6
> * **클러스터 작업자 노드 수**: 2
> * **기본 저장소 계정**: 고유한 문자열
> * **가상 네트워크 이름**: &lt;클러스터 이름>-vnet
> * **가상 네트워크 주소 공간**: 10.0.0.0/16
> * **서브넷 이름**: subnet1
> * **서브넷 주소 범위**: 10.0.0.0/24
>
> &lt;클러스터 이름>은 템플릿을 사용할 때 제공하는 클러스터 이름으로 대체됩니다.
>
>

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다. 서식 파일에 위치한 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **사용자 지정 배포** 블레이드에서 다음 속성을 입력합니다.

   * **구독**: HDInsight 클러스터, 종속 Storage 계정 및 Azure 가상 네트워크를 만드는 데 사용한 Azure 구독을 선택합니다.
   * **리소스 그룹**: **새로 만들기**를 선택하고 새 리소스 그룹 이름을 지정합니다.
   * **위치**: 리소스 그룹의 위치를 선택합니다.
   * **ClusterName**: 만들려는 Hadoop 클러스터의 이름을 입력합니다.
   * **클러스터 로그인 이름 및 암호**: 기본 로그인 이름은 **admin**입니다.
   * **SSH 사용자 이름 및 암호**: 기본 사용자 이름은 **sshuser**입니다.  이름은 변경할 수 있습니다.
   * **위에 명시된 사용 약관에 동의함**: (선택)
3. **구매**를 클릭합니다. 클러스터를 만들려면 20분 정도가 걸립니다. 클러스터가 만들어졌으면 포털에서 클러스터 블레이드를 클릭하면 열립니다.

이 자습서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 클러스터 삭제에 대한 내용은 [Azure Portal을 사용하여 HDInsight에서 Apache Hadoop 클러스터 관리](../hdinsight-administer-use-portal-linux.md#delete-clusters)를 참조하세요.

새 HBase 클러스터 사용을 시작하려는 경우 [HDInsight에서 Apache Hadoop을 통해 Apache HBase 사용 시작](./apache-hbase-tutorial-get-started-linux.md)의 절차를 따르면 됩니다.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Apache HBase Java RPC API를 사용하여 Apache HBase 클러스터에 연결
1. 동일한 Azure 가상 네트워크와 동일한 서브넷에 IaaS(infrastructure as a service) 가상 머신을 만듭니다. 새 IaaS 가상 머신을 만드는 지침은 [Windows Server를 실행하는 Virtual Machine 만들기](../../virtual-machines/windows/quick-create-portal.md)를 참조하세요. 이 문서의 단계를 수행할 때 다음과 같은 네트워크 구성 값을 사용해야 합니다.

   * **가상 네트워크**: &lt;클러스터 이름>-vnet
   * **서브넷**: subnet1

   > [!IMPORTANT]  
   > &lt;클러스터 이름>을 이전 단계에서 HDInsight 클러스터를 만들 때 사용한 이름으로 변경합니다.

   이러한 값을 사용하면 가상 머신이 HDInsight 클러스터와 동일한 가상 네트워크와 서브넷에 배치됩니다. 이 구성을 사용하면 서로 직접 통신할 수 있습니다. 빈 에지 노드가 있는 HDInsight 클러스터를 만드는 방법이 있습니다. 에지 노드를 사용하여 클러스터를 관리할 수 있습니다.  자세한 내용은 [HDInsight에서 빈 에지 노드 사용](../hdinsight-apps-use-edge-node.md)을 참조하세요.

2. Java 애플리케이션을 사용하여 HBase에 원격으로 연결할 때는 FQDN(정규화된 도메인 이름)을 사용해야 합니다. FQDN을 확인하려면 HBase 클러스터의 연결별 DNS 접미사를 가져와야 합니다. 이렇게 하려면 다음 방법 중 하나를 사용합니다.

   * 웹 브라우저를 사용하여 [Apache Ambari](https://ambari.apache.org/)를 호출합니다.

     https://&lt;ClusterName>.azurehdinsight.net/api/v1/clusters/&lt;ClusterName>/hosts?minimal_response=true로 이동합니다. JSON 파일의 DNS 접미사를 설정합니다.
   * Ambari 웹 사이트를 사용합니다.

     1. https://&lt;ClusterName>.azurehdinsight.net으로 이동합니다.
     2. 상위 메뉴에서 **호스트** 를 클릭합니다.
   * Curl을 사용하여 REST를 호출합니다.

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     반환되는 JSON(JavaScript Object Notation) 데이터에서 "host_name" 항목을 찾습니다. 이 항목에는 클러스터의 노드에 대한 FQDN이 포함되어 있습니다. 예를 들면 다음과 같습니다.

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     클러스터 이름으로 시작하는 도메인 이름 부분이 DNS 접미사입니다. 예를 들면 mycluster.b1.cloudapp.net과 같습니다.
   * Azure PowerShell 사용

     다음 Azure PowerShell 스크립트를 사용하여 DNS 접미사를 반환하는 데 사용할 수 있는 **Get-ClusterDetail** 함수를 등록합니다.

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Azure PowerShell 스크립트를 실행한 후 다음 명령을 사용하여 **Get-ClusterDetail** 함수를 통해 DNS 접미사를 반환합니다. 이 명령을 사용할 때는 HDInsight HBase 클러스터 이름, 관리자 이름 및 관리자 암호를 지정합니다.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     이 명령은 DNS 접미사를 반환합니다. 예를 들면 **yourclustername.b4.internal.cloudapp.net**과 같습니다.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

가상 머신이 HBase 클러스터와 통신할 수 있는지 확인하려면 가상 머신에서 `ping headnode0.<dns suffix>` 명령을 사용합니다. 예를 들면 ping headnode0.mycluster.b1.cloudapp.net을 사용합니다.

Java 애플리케이션에서 이 정보를 사용하려는 경우 [Apache Maven을 통해 HDInsight(Hadoop)와 함께 Apache HBase를 사용하는 Java 애플리케이션 작성](./apache-hbase-build-java-maven-linux.md) 의 단계에 따라 애플리케이션을 만들 수 있습니다. 애플리케이션이 원격 HBase 서버에 연결하도록 하려면 이 예제의 **hbase-site.xml** 파일이 ZooKeeper의 FQDN을 사용하도록 수정합니다. 예를 들면 다음과 같습니다.

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> 자체 DNS 서버를 사용하는 방법을 포함한 Azure Virtual Network의 이름 확인에 대한 자세한 내용은 [이름 확인(DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Apache HBase 클러스터를 만드는 방법을 배웠습니다. 자세한 내용은 다음을 참조하세요.

* [HDInsight 시작](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight에서 빈 에지 노드 사용](../hdinsight-apps-use-edge-node.md)
* [HDInsight에서 Apache HBase 복제 구성](apache-hbase-replication.md)
* [HDInsight에 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight에서 Hadoop을 통해 Apache HBase 사용 시작](./apache-hbase-tutorial-get-started-linux.md)
* [Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

