---
title: 가상 네트워크를 사용하여 Kafka에 연결 - Azure HDInsight
description: Azure Virtual Network를 통해 HDInsight에서 Kafka에 직접 연결하는 방법을 알아봅니다. VPN Gateway를 사용하여 개발 클라이언트에서 또는 VPN 게이트웨이 디바이스를 사용하여 온-프레미스 네트워크의 클라이언트에서 Kafka에 연결하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 93b5aeafafdc6ab7ee233f6360bb5e09f45b387f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115337"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Azure Virtual Network를 통해 HDInsight의 Apache Kafka에 연결

Azure Virtual Network를 통해 HDInsight에서 Apache Kafka에 직접 연결하는 방법을 알아봅니다. 이 문서는 다음 구성을 사용하여 Kafka에 연결하는 것에 관한 정보를 제공합니다.

* 온-프레미스 네트워크 리소스에서. 이 연결은 로컬 네트워크에서 VPN 디바이스(소프트웨어 또는 하드웨어)를 사용하여 설정됩니다.
* VPN 소프트웨어 클라이언트를 사용한 개발 환경에서.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>아키텍처 및 계획

HDInsight는 공용 인터넷을 통해 Kafka에 직접 연결하는 것을 허용하지 않습니다. 대신, Kafka 클라이언트(생산자와 소비자)는 다음 연결 방법 중 하나를 사용해야 합니다.

* 클라이언트를 동일한 가상 네트워크에서 Kafka로 HDInsight 상에서 실행합니다. 이 구성은 [HDInsight의 Apache Kafka에서 시작](apache-kafka-get-started.md) 문서에서 사용됩니다. 클라이언트는 동일한 네트워크 내의 HDInsight 클러스터 노드나 또 다른 가상 머신에서 직접 실행합니다.

* 온-프레미스 네트워크와 같은 개인 네트워크를 가상 네트워크에 연결합니다. 이 구성을 통해 온-프레미스 네트워크의 클라이언트들은 직접 Kafka를 사용할 수 있습니다. 이 구성을 사용하도록 설정하려면 다음 작업을 수행합니다.

  1. 가상 네트워크를 만듭니다.
  2. 사이트 간 구성을 사용하는 VPN 게이트웨이를 만듭니다. 이 문서에 사용된 구성을 통해 온-프레미스 네트워크의 VPN 게이트웨이 디바이스에 연결합니다.
  3. 가상 네트워크에 DNS 서버를 만듭니다.
  4. 각 네트워크의 DNS 서버 간에 전달을 구성합니다.
  5. 가상 네트워크에 HDInsight의 Kafka 클러스터를 만듭니다.

     자세한 내용은 [온-프레미스 네트워크에서 Apache Kafka에 연결](#on-premises) 섹션을 참조하세요. 

* VPN 게이트웨이와 VPN 클라이언트를 사용하여 개별 컴퓨터를 가상 네트워크에 연결합니다. 이 구성을 사용하도록 설정하려면 다음 작업을 수행합니다.

  1. 가상 네트워크를 만듭니다.
  2. 지점-사이트 간 구성을 사용하는 VPN 게이트웨이를 만듭니다. 이 구성은 Windows 및 MacOS 클라이언트와 함께 사용할 수 있습니다.
  3. 가상 네트워크에 HDInsight의 Kafka 클러스터를 만듭니다.
  4. IP 보급을 위한 Kafka 구성. 이 구성을 사용하면 클라이언트에서 도메인 이름 대신 broker IP 주소를 사용하여 연결할 수 있습니다.
  5. VPN 클라이언트를 개발 시스템에 다운로드하여 사용합니다.

     자세한 내용은 [VPN 클라이언트와 함께 Apache Kafka 연결](#vpnclient) 섹션을 참조하세요.

     > [!WARNING]  
     > 이 구성은 다음과 같은 제한 사항 때문에 개발 용도로만 사용하는 것이 좋습니다.
     >
     > * 각 클라이언트는 VPN 소프트웨어 클라이언트를 사용하여 연결해야 합니다.
     > * VPN 클라이언트는 가상 네트워크에 이름 확인 요청을 전달하지 않으므로, Kafka와 통신하기 위해 IP 주소 지정을 사용해야 합니다. IP 통신을 하려면 Kafka 클러스터에 추가 구성을 해야 합니다.

가상 네트워크에서 HDInsight를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 HDInsight 확장](../hdinsight-extend-hadoop-virtual-network.md)을 참조하세요.

## <a id="on-premises"></a> 온-프레미스 네트워크에서 Apache Kafka에 연결

온-프레미스 네트워크와 통신하는 Kafka 클러스터를 만들려면 [온-프레미스 네트워크에 HDInsight 연결](./../connect-on-premises-network.md) 문서에 나오는 단계를 수행합니다.

> [!IMPORTANT]  
> HDInsight 클러스터를 만들 때 __Kafka__ 클러스터 유형을 선택합니다.

이러한 단계를 거쳐 다음 구성이 생성됩니다.

* Azure Virtual Network
* 사이트 간 VPN 게이트웨이
* Azure Storage 계정(HDInsight에서 사용)
* HDInsight의 Kafka

Kafka 클라이언트가 온-프레미스에서 클러스터로 연결할 수 있는지 확인하려면 [예: Python 클라이언트](#python-client) 섹션에 나오는 단계를 사용합니다.

## <a id="vpnclient"></a> VPN 클라이언트에 Apache Kafka 연결

다음 구성을 만들려면 이 섹션에 나오는 단계를 사용합니다.

* Azure Virtual Network
* 지점-사이트 간 VPN Gateway
* Azure Storage 계정(HDInsight에서 사용)
* HDInsight의 Kafka

1. [지점 및 사이트 간 연결에 대한 자체 서명된 인증서로 작업](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) 문서에 나오는 단계를 수행합니다. 이 문서는 게이트웨이에 필요한 인증서를 만듭니다.

2. PowerShell 프롬프트를 열고 다음 코드를 사용하여 Azure 구독에 로그인합니다.

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. 다음 코드를 사용하여 구성 정보를 포함하는 변수를 만듭니다.

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. 다음 코드를 사용하여 Azure 리소스 그룹 및 가상 네트워크를 만듭니다.

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > 이 프로세스는 완료하는 데 몇 분 정도 걸릴 수 있습니다.

5. 다음 코드를 사용하여 Azure Storage 계정 및 Blob 컨테이너를 만듭니다.

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. 다음 코드를 사용하여 HDInsight 클러스터를 만듭니다.

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > 이 프로세스를 완료하는 데 약 15분이 걸립니다.

### <a name="configure-kafka-for-ip-advertising"></a>IP 보급을 위해 Kafka 구성

기본적으로 Apache Zookeeper는 Kafka 브로커의 도메인 이름을 클라이언트에 반환합니다. 이 구성은 가상 네트워크의 엔터티에 대해 이름 확인을 사용할 수 없으므로 VPN 소프트웨어 클라이언트에 작동하지 않습니다. 이 구성의 경우, 다음 단계에 따라 도메인 이름 대신 IP 주소를 보급하도록 Kafka를 구성합니다.

1. 웹 브라우저를 사용하여 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. __CLUSTERNAME__을 HDInsight 클러스터의 Kafka 이름으로 바꿉니다.

    메시지가 표시되면, 클러스터의 HTTPS 사용자 이름 및 암호를 입력합니다. 클러스터에 대한 Ambari Web UI가 표시됩니다.

2. Kafka에 대한 정보를 보려면 왼쪽 목록에서 __Kafka__를 선택합니다.

    ![Kafka가 강조 표시된 서비스 목록](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Kafka 구성을 보려면 위쪽 가운데에서 __Configs__를 선택합니다.

    ![Kafka에 대한 링크 구성](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. __kafka-env__ 구성을 찾으려면 오른쪽 위에 있는 __필터__ 필드에 `kafka-env`를 입력합니다.

    ![kafka-env의 Kafka 구성](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. IP 주소를 보급하도록 Kafka를 구성하려면 __kafka-env-template__ 맨 아래에 다음 텍스트를 추가합니다.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka에서 수신 대기하는 인터페이스를 구성하려면 오른쪽 위의 __필터__ 필드에 `listeners`를 입력합니다.

7. 모든 네트워크 인터페이스에서 수신 대기하도록 Kafka를 구성하려면 __수신기__ 필드의 값을 `PLAINTEXT://0.0.0.0:9092`로 변경합니다.

8. 구성 변경 내용을 저장하려면 __저장__ 단추를 사용합니다. 변경 내용을 설명하는 텍스트 메시지를 입력합니다. 변경 내용이 저장되면 __확인__을 선택합니다.

    ![구성 저장 단추](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Kafka를 다시 시작할 때 오류를 방지하려면 __서비스 작업__ 단추를 사용하여 __유지 관리 모드 켜기__를 선택합니다. 확인을 선택하여 이 작업을 완료합니다.

    ![유지 관리 모드 켜기가 강조 표시된 서비스 작업](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Kafka를 다시 시작하려면 __다시 시작__ 단추를 사용하고 __영향 받은 모든 항목 다시 시작__을 선택합니다. 다시 시작을 확인하고 작업이 완료되면 __확인__ 단추를 사용합니다.

    ![영향 받은 모든 항목 다시 시작이 강조 표시된 다시 시작 단추](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. 유지 관리 모드를 사용하지 않도록 설정하려면 __서비스 작업__ 단추를 사용하고 __유지 관리 모드 끄기__를 선택합니다. **확인**을 선택하여 이 작업을 완료합니다.

### <a name="connect-to-the-vpn-gateway"></a>VPN Gateway에 연결

VPN 게이트웨이에 연결하려면 [지점-사이트 간 연결 구성](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) 문서의 __Azure에 연결__ 섹션을 사용합니다.

## <a id="python-client"></a> 예: Python 클라이언트

Kafka에 대한 연결 유효성 검사를 하려면, 다음 단계를 사용하여 Python 생산자와 소비자를 만들고 실행합니다.

1. 다음 방법 중 하나를 사용하여 Kafka 클러스터에서 정규화된 도메인 이름(FQDN) 및 노드 IP 주소를 검색합니다.

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    이 스크립트에서는 `$resourceGroupName`을 가상 네트워크가 포함된 Azure 리소스 그룹의 이름으로 가정합니다.

    다음 단계에서의 사용을 위해 반환된 정보를 저장합니다.

2. 다음을 사용하여 [kafka-python](https://kafka-python.readthedocs.io/) 클라이언트를 설치합니다.

        pip install kafka-python

3. Kafka로 데이터를 전송하려면 다음 Python 코드를 사용합니다.

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    `'kafka_broker'` 항목을 이 섹션의 1단계에서 반환된 주소로 바꿉니다.

   * __소프트웨어 VPN 클라이언트__를 사용하는 경우, `kafka_broker` 항목을 작업자 노드의 IP 주소와 바꿉니다.

   * __사용자 지정 DNS 서버를 통해 이름 확인을 할 수 있게__ 한 경우, `kafka_broker` 항목을 작업자 노드의 FQDN과 바꿉니다.

     > [!NOTE]
     > 이 코드는 문자열 `test message`를 토픽 `testtopic`으로 전송합니다. HDInsight에서 Kafka의 기본 구성은 토픽이 없을 때 만드는 것입니다.

4. Kafka에서 메시지를 검색하려면 다음 Python 코드를 사용합니다.

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    `'kafka_broker'` 항목을 이 섹션의 1단계에서 반환된 주소로 바꿉니다.

    * __소프트웨어 VPN 클라이언트__를 사용하는 경우, `kafka_broker` 항목을 작업자 노드의 IP 주소와 바꿉니다.

    * __사용자 지정 DNS 서버를 통해 이름 확인을 할 수 있게__ 한 경우, `kafka_broker` 항목을 작업자 노드의 FQDN과 바꿉니다.

## <a name="next-steps"></a>다음 단계

HDInsight와 함께 가상 네트워크를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight-extend-hadoop-virtual-network.md) 문서를 참조하세요.

지점-사이트 간 VPN Gateway를 사용하여 Azure Virtual Network를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Portal을 사용하여 지점 및 사이트 간 연결 구성](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Azure PowerShell을 사용하여 지점 및 사이트 간 연결 구성](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

HDInsight에서 Apache Kafka를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Kafka 시작](apache-kafka-get-started.md)
* [HDInsight에서 Apache Kafka 미러링 사용](apache-kafka-mirroring.md)
