---
title: Virtual Network로 HDInsight 확장 - Azure
description: Azure Virtual Network를 사용하여 HDInsight 다른 클라우드 리소스 또는 데이터 센터에서 리소스에 연결하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/29/2019
ms.openlocfilehash: f97c07c522dfb22818aca84d41d30c023f564d84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097340"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Azure Virtual Network를 사용하여 Azure HDInsight 확장

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 HDInsight를 사용하는 방법을 알아봅니다. Azure Virtual Network를 사용하면 다음 시나리오가 가능합니다.

* 온-프레미스 네트워크에서 HDInsight에 직접 연결합니다.

* Azure Virtual Network에서 데이터 저장소에 HDInsight를 연결합니다.

* 인터넷을 통해 공개적으로 사용할 수 없는 [Apache Hadoop](https://hadoop.apache.org/) 서비스에 직접 액세스합니다. 예: [Apache Kafka](https://kafka.apache.org/) API 또는 [Apache HBase](https://hbase.apache.org/) Java API.

> [!IMPORTANT]  
> 2019 년 2 월 28 일 후 VNET에서 만든 새 클러스터에 대 한 네트워킹 리소스 (예: Nic, 무게, 등) 동일한 HDInsight 클러스터 리소스 그룹에 프로 비전 됩니다. 이전에 이러한 리소스는 VNET 리소스 그룹에 프로 비전 된 합니다. 현재 실행 중인 클러스터 및 해당 클러스터에 VNET을 선택 하지 않고 만든 변하지가 않습니다.

## <a name="prerequisites-for-code-samples-and-examples"></a>코드 샘플 및 예제에 대 한 필수 구성 요소

* TCP/IP 네트워킹 이해 합니다. TCP/IP 네트워킹에 대해 잘 모르는 경우 이전에 프로덕션 네트워크를 수정한 사람과 협력하여 작업해야 합니다.
* PowerShell을 사용 하는 경우는 [AZ 모듈](https://docs.microsoft.com/powershell/azure/overview)합니다.
* 사용 하려는 경우 Azure CLI를 아직 설치 하지 않은 것을 참조 하십시오 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

> [!IMPORTANT]  
> Azure Virtual Network를 사용하여 HDInsight를 온-프레미스 네트워크에 연결하는 단계별 지침을 찾으려면 [온-프레미스 네트워크에 HDInsight 연결](connect-on-premises-network.md) 문서를 참조하세요.

## <a name="planning"></a>계획

다음은 가상 네트워크에 HDInsight를 설치하려고 할 때 대답해야 하는 질문입니다.

* 기존 가상 네트워크에 HDInsight 설치해야 하나요? 아니면 새 네트워크를 만드나요?

    기존 가상 네트워크를 사용하는 경우 HDInsight를 설치하기 전에 네트워크 구성을 수정해야 할 수 있습니다. 자세한 내용은 [기존 가상 네트워크에 HDInsight 추가](#existingvnet) 섹션을 참조하세요.

* HDInsight가 들어 있는 가상 네트워크를 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결하시겠습니까?

    네트워크 간의 리소스로 쉽게 작업하려면 사용자 지정 DNS를 만들고 DNS 전달을 구성해야 할 수 있습니다. 자세한 내용은 [다중 네트워크 연결](#multinet) 섹션을 참조하세요.

* HDInsight로의 인바운드 또는 아웃바운드 트래픽을 제한/리디렉션하시겠습니까?

    HDInsight는 Azure 데이터 센터에서 특정 IP 주소와 무제한 통신을 포함해야 합니다. 또한 클라이언트 통신에 방화벽을 통해 허용해야 하는 여러 포트가 있습니다. 자세한 내용은 [네트워크 트래픽 제어](#networktraffic) 섹션을 참조하세요.

## <a id="existingvnet"></a>기존 가상 네트워크에 HDInsight 추가

이 섹션의 단계를 사용하여 새 HDInsight를 기존 Azure Virtual Network에 추가하는 방법을 알아봅니다.

> [!NOTE]  
> 기존 HDInsight 클러스터를 가상 네트워크에 추가할 수 없습니다.

1. 가상 네트워크에 클래식 또는 리소스 관리자 배포 모델을 사용하나요?

    HDInsight 3.4 이상에는 리소스 관리자 가상 네트워크가 필요합니다. 이전 버전의 HDInsightㅇ는 클래식 가상 네트워크가 필요했습니다.

    기존 네트워크가 클래식 가상 네트워크인 경우 리소스 관리자 가상 네트워크를 만든 후 둘을 연결해야 합니다. [새 VNet에 클래식 VNet 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    조인된 후 리소스 관리자 네트워크에 설치된 HDInsight는 클래식 네트워크의 리소스와 상호 작용할 수 있습니다.

2. 강제 터널링을 사용하나요? 강제 터널링은 검사 및 로깅을 위해 디바이스에 아웃바운드 인터넷 트래픽을 적용하는 서브넷 설정입니다. HDInsight는 강제 터널링을 지원하지 않습니다. 기존 서브넷에 HDInsight를 배포하기 전에 강제 터널링을 제거하거나 HDInsight에 대한 강제 터널링이 없는 새 서브넷을 만듭니다.

3. 가상 네트워크 내부 또는 외부로 트래픽을 제한하기 위해 네트워크 보안 그룹, 사용자 정의 경로 또는 Virtual Network 어플라이언스를 사용하나요?

    관리 서비스로 HDInsight를 사용하려면 Azure 데이터 센터에서 여러 IP 주소에 대한 무제한 액세스가 필요합니다. 이러한 IP 주소와의 통신을 허용하려면 기존의 모든 네트워크 보안 그룹 또는 사용자 정의 경로를 업데이트합니다.
    
    HDInsight는 다양한 포트를 사용하는 여러 서비스를 호스팅합니다. 이 포트로의 트래픽을 차단하지 마세요. 가상 어플라이언스 방화벽을 통과하도록 허용할 포트 목록은 보안 섹션을 참조하세요.
    
    기존 보안 구성을 찾으려면 다음 Azure PowerShell 또는 Azure CLI 명령을 사용합니다.

    * 네트워크 보안 그룹

        대체 `RESOURCEGROUP` virtual network를 포함 하 고 다음 명령을 입력 하는 리소스 그룹의 이름:
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        자세한 내용은 [네트워크 보안 그룹 문제 해결](../virtual-network/diagnose-network-traffic-filter-problem.md) 문서를 참조하세요.

        > [!IMPORTANT]  
        > 네트워크 보안 그룹 규칙은 규칙 우선 순위에 따라 적용됩니다. 트래픽 패턴과 일치하는 첫 번째 규칙이 적용되고 해당 트래픽에 대해서는 다른 규칙이 적용되지 않습니다. 가장 허용적인 것부터 가장 허용적이지 않은 것 순서로 규칙을 정렬합니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/security-overview.md) 문서를 참조하세요.

    * 사용자 정의 경로

        대체 `RESOURCEGROUP` virtual network를 포함 하 고 다음 명령을 입력 하는 리소스 그룹의 이름:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        자세한 내용은 [문제 해결 경로](../virtual-network/diagnose-network-routing-problem.md) 문서를 참조하세요.

4. HDInsight 클러스터를 만들고 구성 중 Azure Virtual Network를 선택합니다. 클러스터 만들기 프로세스를 이해하려면 다음 문서의 단계를 사용하세요.

    * [Azure Portal을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Azure PowerShell을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Azure 클래식 CLI를 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Azure Resource Manager 템플릿을 사용하여 HDInsight 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 가상 네트워크에 HDInsight 추가하기는 선택적 구성 단계입니다. 클러스터를 구성할 때 가상 네트워크를 선택해야 합니다.

## <a id="multinet"></a>다중 네트워크 연결

다중 네트워크 구성에서 가장 큰 문제는 네트워크 간 이름 확인입니다.

Azure는 가상 네트워크에 설치된 Azure 서비스에 대한 이름 확인을 제공합니다. 기본 제공 이름 확인을 통해 HDInsight는 FQDN(정규화된 도메인 이름)을 사용하여 다음 리소스에 연결할 수 있습니다.

* 인터넷에서 사용할 수 있는 모든 리소스. 예: microsoft.com, windowsupdate.com.

* 리소스의 __내부 DNS 이름__을 사용하여 동일한 Azure Virtual Network에 있는 모든 리소스. 예를 들어 기본 이름 확인 기능을 사용할 경우, 다음은 HDInsight 작업자 노드에 할당된 내부 DNS 이름에 대한 예입니다.

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    이러한 두 노드는 내부 DNS 이름을 사용하여 서로 직접 통신하고 HDInsight의 다른 노드와 통신할 수 있습니다.

기본 이름 확인에서는 HDInsight가 가상 네트워크에 조인된 네트워크에 있는 리소스 이름을 확인하도록 허용하지 __않습니다__. 예를 들어 온-프레미스 네트워크를 가상 네트워크에 조인하는 것이 일반적입니다. 기본 이름 확인만으로는, HDInsight가 이름으로 온-프레미스 네트워크의 리소스에 액세스할 수 없습니다. 반대도 마찬가지입니다. 온-프레미스 네트워크의 리소스는 이름으로 가상 네트워크의 리소스에 액세스할 수 없습니다.

> [!WARNING]  
> 사용자 지정 DNS 서버를 만들고 이 서버를 사용하도록 가상 네트워크를 구성한 후 HDInsight 클러스터를 만들어야 합니다.

가상 네트워크 및 조인된 네트워크의 리소스 간에 이름 확인을 사용하려면 다음 작업을 수행해야 합니다.

1. HDInsight를 설치할 계획인 Azure Virtual Network에서 사용자 지정 DNS 서버를 만듭니다.

2. 사용자 지정 DNS 서버를 사용하도록 가상 네트워크를 구성합니다.

3. 가상 네트워크에 대해 Azure에서 할당한 DNS 접미사를 찾습니다. 이 값은 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`과 유사합니다. DNS 접미사를 찾는 방법에 대한 자세한 내용은 [예제: 사용자 지정 DNS](#example-dns) 섹션을 참조하세요.

4. DNS 서버 간에 전달을 구성합니다. 구성은 원격 네트워크의 유형에 따라 달라집니다.

   * 원격 네트워크가 온-프레미스 네트워크인 경우 다음과 같이 DNS를 구성합니다.
        
     * __사용자 지정 DNS(가상 네트워크에서)__:

         * 가상 네트워크의 DNS 접미사에 대한 요청을 Azure 재귀 확인자(168.63.129.16)에 전달합니다. Azure에서 가상 네트워크의 리소스에 대한 요청을 처리합니다.

         * 다른 모든 요청을 온-프레미스 DNS 서버에 전달합니다. 온-프레미스 DNS가 Microsoft.com과 같은 인터넷 리소스에 대한 요청을 비롯한 기타 모든 이름 확인 요청을 처리합니다.

     * __온-프레미스 DNS__: 가상 네트워크 DNS 접미사에 대한 요청을 사용자 지정 DNS 서버에 전달합니다. 그러면 사용자 지정 DNS 서버에서 Azure 재귀 확인자로 전달합니다.

       이 구성은 가상 네트워크에 대한 DNS 접미사를 포함하는 정규화된 도메인 이름 요청을 사용자 지정 DNS 서버로 라우팅합니다. 공용 인터넷 주소를 포함한 모든 다른 요청은 온-프레미스 DNS 서버에서 처리됩니다.

   * 원격 네트워크가 다른 Azure Virtual Network인 경우 다음과 같이 DNS를 구성합니다.

     * __사용자 지정 DNS(각 가상 네트워크에서)__:

         * 가상 네트워크 DNS 접미사에 대한 요청은 사용자 지정 DNS 서버에 전달됩니다. 각 가상 네트워크에 있는 DNS는 해당 네트워크 내에서 리소스를 확인하는 역할을 합니다.

         * 다른 모든 요청은 Azure 재귀 확인자에 전달합니다. 재귀 확인자는 로컬 및 인터넷 리소스를 확인하는 역할을 합니다.

       각 네트워크의 DNS 서버는 DNS 접미사에 따라 요청을 다른 곳으로 전달합니다. 다른 요청은 Azure 재귀 확인자를 사용하여 확인됩니다.

     각 구성에 대한 예는 [예제: 사용자 지정 DNS](#example-dns) 섹션을 참조하세요.

자세한 내용은 [VM 및 역할 인스턴스의 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

## <a name="directly-connect-to-apache-hadoop-services"></a>Apache Hadoop 서비스에 직접 연결

`https://CLUSTERNAME.azurehdinsight.net`에 있는 클러스터에 연결할 수 있습니다. 이 주소는 공용 IP를 사용하며, NSG를 사용하여 인터넷에서 들어오는 트래픽을 제한한 경우 액세스할 수 없습니다. 또한 클러스터를 VNet에 배포하는 경우 개인 엔드포인트 `https://CLUSTERNAME-int.azurehdinsight.net`을 사용하여 액세스할 수 있습니다. 이 엔드포인트는 클러스터 액세스를 위한 VNet 내부의 사설 IP로 확인됩니다.

가상 네트워크를 통해 Apache Ambari 및 다른 웹 페이지에 연결하려면 다음 단계를 사용합니다.

1. HDInsight 클러스터 노드의 내부 FQDN(정규화된 도메인 이름)을 검색하려면 다음 방법 중 하나를 사용합니다.

    대체 `RESOURCEGROUP` virtual network를 포함 하 고 다음 명령을 입력 하는 리소스 그룹의 이름:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    반환된 노드 목록에서 헤드 노드에 대한 FQDN을 찾아 Ambari 및 기타 웹 서비스에 연결하는 데 사용합니다. 예를 들어 `http://<headnode-fqdn>:8080`을 사용하여 Ambari에 액세스합니다.

    > [!IMPORTANT]  
    > 헤드 노드에서 호스팅되는 일부 서비스는 한 번에 한 노드에서만 활성화됩니다. 한 헤드 노드에서 서비스에 액세스하려고 하고 404 오류가 반환되면 다른 헤드 노드로 전환합니다.

2. 서비스가 제공되는 노드 및 포트를 확인하려면 [HDInsight의 Hadoop 서비스에서 사용하는 포트](./hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

## <a id="networktraffic"></a> 네트워크 트래픽 제어

Azure Virtual Networks의 네트워크 트래픽은 다음 방법을 사용하여 제어할 수 있습니다.

* **NSG(네트워크 보안 그룹)** 를 통해 네트워크로의 인바운드 및 아웃바운드 트래픽을 필터링할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/security-overview.md) 문서를 참조하세요.

    > [!WARNING]  
    > HDInsight는 아웃바운드 트래픽을 제한하도록 지원하지 않습니다. 모든 아웃바운드 트래픽을 허용해야 합니다.

* **UDR(사용자 정의 경로)** 은 네트워크에 있는 리소스 간에 트래픽이 흐르는 방식을 정의합니다. 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md) 문서를 참조하세요.

* **네트워크 가상 어플라이언스**는 방화벽 및 라우터와 같은 디바이스 기능을 복제합니다. 자세한 내용은 [네트워크 어플라이언스](https://azure.microsoft.com/solutions/network-appliances) 문서를 참조하세요.

관리 서비스로 HDInsight 필요 HDInsight 상태에 대 한 무제한 액세스 및 관리 VNET에서 들어오고 나가는 트래픽에 대 한 서비스. NSG 및 UDR을 사용하는 경우 이러한 서비스가 HDInsight와 계속 통신할 수 있는지 확인해야 합니다.

### <a id="hdinsight-ip"></a> 네트워크 보안 그룹 및 사용자 정의 경로가 있는 HDInsight

네트워크 트래픽을 제어하는 데 **네트워크 보안 그룹** 또는 **사용자 정의 경로**를 사용할 계획인 경우 HDInsight를 설치하기 전에 다음 작업을 수행합니다.

1. HDInsight에 대해 사용할 Azure 지역을 식별합니다.

2. HDInsight에 필요한 IP 주소를 식별합니다. 자세한 내용은 [HDInsight에 필요한 IP 주소](#hdinsight-ip) 섹션을 참조하세요.

3. HDInsight을 설치하려는 서브넷에 대한 사용자 정의 경로 또는 네트워크 보안 그룹을 만들거나 수정합니다.

    * __네트워크 보안 그룹__: IP 주소에서 포트 __443__에 __인바운드__ 트래픽을 허용합니다. 이렇게 하면 HDI 관리 서비스가 VNET 외부에서 클러스터에 도달할 수 있습니다.
    * __사용자 정의 경로__: UDR을 사용할 계획인 경우 각 IP 주소에 대한 경로를 만들고 __다음 홉 유형__을 __인터넷__으로 설정합니다. VNET에서 오는 다른 아웃바운드 트래픽도 제한 없이 허용해야 합니다. 예를 들어 Azure 방화벽 또는 네트워크 가상 어플라이언스로 (Azure에서 호스팅됨) 모니터링을 위해 다른 모든 트래픽을 라우팅할 수 있지만 나가는 트래픽을 차단 되지 않아야 합니다.

네트워크 보안 그룹 또는 사용자 정의 경로에 대한 자세한 내용은 다음 설명서를 참조하세요.

* [네트워크 보안 그룹](../virtual-network/security-overview.md)

* [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling-to-on-premise"></a>온-프레미스에 강제 터널링

강제 터널링은 서브넷의 모든 트래픽이 특정 네트워크 또는 위치(예: 온-프레미스 네트워크)로 적용되는 사용자 정의 라우팅 구성입니다. HDInsight 않습니다 __되지__ 강제 터널링을 온-프레미스 네트워크에 지원 합니다. Azure 방화벽이 나 Azure에서 호스트 되는 네트워크 가상 어플라이언스를 사용 하는 경우에 모니터링을 위해 트래픽을 라우팅하고 나가는 모든 트래픽이 허용 Udr을 사용할 수 있습니다.

## <a id="hdinsight-ip"></a> 필수 IP 주소

> [!IMPORTANT]  
> Azure 상태 및 관리 서비스는 HDInsight와 통신할 수 있어야 합니다. 네트워크 보안 그룹 또는 사용자 정의 경로를 사용하는 경우 이러한 서비스의 IP 주소에서 HDInsight로 트래픽이 전송되도록 할 수 있습니다.
>
> 트래픽을 제어하는 네트워크 보안 그룹 또는 사용자 정의 경로를 사용하지 않는 경우 이 섹션을 무시할 수 있습니다.

네트워크 보안 그룹을 사용하는 경우 Azure 상태 및 관리 서비스의 트래픽이 포트 443의 HDInsight 클러스터에 도달하도록 해야 합니다. 서브넷 내에서 VM 간 트래픽도 허용해야 합니다. 다음 단계를 사용하여 다음을 허용해야 하는 IP 주소를 찾을 수 있습니다.

1. 다음 IP 주소에서 트래픽을 항상 허용해야 합니다.

    | 원본 IP 주소 | 대상 포트 | Direction |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | 인바운드 |
    | 23.99.5.239 | 443 | 인바운드 |
    | 168.61.48.131 | 443 | 인바운드 |
    | 138.91.141.162 | 443 | 인바운드 |

2. HDInsight 클러스터가 다음 지역 중에 있으면 해당 지역에 대해 나열된 IP 주소에서 트래픽을 허용해야 합니다.

    > [!IMPORTANT]  
    > 사용하는 Azure 지역이 나열되지 않으면 1단계의 4가지 IP 주소만 사용합니다.

    | 국가 | 지역 | 허용된 원본 IP 주소 | 상위 대상 포트 | Direction |
    | ---- | ---- | ---- | ---- | ----- |
    | 아시아 | 동아시아 | 23.102.235.122</br>52.175.38.134 | 443 | 인바운드 |
    | &nbsp; | 동남아시아 | 13.76.245.160</br>13.76.136.249 | 443 | 인바운드 |
    | 오스트레일리아 | 오스트레일리아 동부 | 104.210.84.115</br>13.75.152.195 | 443 | 인바운드 |
    | &nbsp; | 오스트레일리아 남동부 | 13.77.2.56</br>13.77.2.94 | 443 | 인바운드 |
    | 브라질 | 브라질 남부 | 191.235.84.104</br>191.235.87.113 | 443 | 인바운드 |
    | 캐나다 | 캐나다 동부 | 52.229.127.96</br>52.229.123.172 | 443 | 인바운드 |
    | &nbsp; | 캐나다 중부 | 52.228.37.66</br>52.228.45.222 | 443 | 인바운드 |
    | 중국 | 중국 북부 | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | 443 | 인바운드 |
    | &nbsp; | 중국 동부 | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | 443 | 인바운드 |
    | &nbsp; | 중국 북부 2 | 40.73.37.141</br>40.73.38.172 | 443 | 인바운드 |
    | &nbsp; | 중국 동부 2 | 139.217.227.106</br>139.217.228.187 | 443 | 인바운드 |
    | 유럽 | 유럽 북부 | 52.164.210.96</br>13.74.153.132 | 443 | 인바운드 |
    | &nbsp; | 서유럽| 52.166.243.90</br>52.174.36.244 | 443 | 인바운드 |
    | 프랑스 | 프랑스 중부| 20.188.39.64</br>40.89.157.135 | 443 | 인바운드 |
    | 독일 | 독일 중부 | 51.4.146.68</br>51.4.146.80 | 443 | 인바운드 |
    | &nbsp; | 독일 북동부 | 51.5.150.132</br>51.5.144.101 | 443 | 인바운드 |
    | 인도 | 중앙 인도 | 52.172.153.209</br>52.172.152.49 | 443 | 인바운드 |
    | &nbsp; | 인도 남부 | 104.211.223.67<br/>104.211.216.210 | 443 | 인바운드 |
    | 일본 | 일본 동부 | 13.78.125.90</br>13.78.89.60 | 443 | 인바운드 |
    | &nbsp; | 일본 서부 | 40.74.125.69</br>138.91.29.150 | 443 | 인바운드 |
    | 한국 | 한국 중부 | 52.231.39.142</br>52.231.36.209 | 433 | 인바운드 |
    | &nbsp; | 한국 남부 | 52.231.203.16</br>52.231.205.214 | 443 | 인바운드
    | 영국 | 영국 서부 | 51.141.13.110</br>51.141.7.20 | 443 | 인바운드 |
    | &nbsp; | 영국 남부 | 51.140.47.39</br>51.140.52.16 | 443 | 인바운드 |
    | 미국 | 미국 중부 | 13.67.223.215</br>40.86.83.253 | 443 | 인바운드 |
    | &nbsp; | 미국 동부 | 13.82.225.233</br>40.71.175.99 | 443 | 인바운드 |
    | &nbsp; | 미국 중북부 | 157.56.8.38</br>157.55.213.99 | 443 | 인바운드 |
    | &nbsp; | 미국 중서부 | 52.161.23.15</br>52.161.10.167 | 443 | 인바운드 |
    | &nbsp; | 미국 서부 | 13.64.254.98</br>23.101.196.19 | 443 | 인바운드 |
    | &nbsp; | 미국 서부 2 | 52.175.211.210</br>52.175.222.222 | 443 | 인바운드 |

    Azure Government에 사용할 IP 주소에 대한 자세한 내용은 [Azure Government 인텔리전스 + 분석](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) 문서를 참조하세요.

3. __168.63.129.16__에서 액세스도 허용해야 합니다. 이 주소는 Azure 재귀 확인자입니다. 자세한 내용은 [VM 및 역할 인스턴스의 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

자세한 내용은 [네트워크 트래픽 제어](#networktraffic) 섹션을 참조하세요.

UDR(사용자 정의 경로)을 사용 중인 경우 경로를 지정하고 “인터넷”으로 설정된 다음 홉을 통해 VNET에서 위의 IP로의 아웃바운드 트래픽을 허용해야 합니다.
    
## <a id="hdinsight-ports"></a> 필수 포트

**방화벽**을 사용하여 특정 포트 외부에서 클러스터에 액세스하려는 경우 시나리오에 필요한 포트에서 트래픽을 허용해야 합니다. 기본적으로 이전 섹션에서 설명한 대로 Azure 관리 트래픽이 443 포트의 클러스터에 도달하도록 허용되어 있는 한 특별한 포트를 허용 목록에 추가할 필요는 없습니다.

특정 서비스에 대한 포트 목록은 [HDInsight의 Apache Hadoop 서비스에서 사용되는 포트](hdinsight-hadoop-port-settings-for-services.md) 문서를 참조하세요.

가상 어플라이언스의 방화벽 규칙에 대한 자세한 내용은 [가상 어플라이언스 시나리오](../virtual-network/virtual-network-scenario-udr-gw-nva.md) 문서를 참조하세요.

## <a id="hdinsight-nsg"></a>예제: HDInsight에서 네트워크 보안 그룹

이 섹션의 예제는 HDInsight가 Azure 관리 서비스와 통신할 수 있도록 하는 네트워크 보안 그룹 규칙을 만드는 방법을 보여 줍니다. 예제를 사용하기 전에 사용 중인 Azure 지역과 일치하도록 IP 주소를 조정합니다. 이 정보는 [네트워크 보안 그룹 및 사용자 정의 경로가 있는 HDInsight](#hdinsight-ip) 섹션에서 확인할 수 있습니다.

### <a name="azure-resource-management-template"></a>Azure Resource Management 템플릿

다음 리소스 관리 템플릿은 인바운드 트래픽을 제한하지만 HDInsight에 필요한 IP 주소에서의 트래픽은 허용하는 가상 네트워크를 만듭니다. 또한 이 템플릿은 가상 네트워크에 HDInsight 클러스터를 만듭니다.

* [보안 Azure Virtual Network 및 HDInsight Hadoop 클러스터 배포](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]  
> 이 예제에 사용된 IP 주소를 사용 중인 Azure 지역에 맞게 변경합니다. 이 정보는 [네트워크 보안 그룹 및 사용자 정의 경로가 있는 HDInsight](#hdinsight-ip) 섹션에서 확인할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

다음 PowerShell 스크립트를 사용하여 인바운드 트래픽을 제한하며 북유럽 지역의 IP 주소에서 전송되는 트래픽을 허용하는 가상 네트워크를 만듭니다.

> [!IMPORTANT]  
> 이 예제에 사용된 IP 주소를 사용 중인 Azure 지역에 맞게 변경합니다. 이 정보는 [네트워크 보안 그룹 및 사용자 정의 경로가 있는 HDInsight](#hdinsight-ip) 섹션에서 확인할 수 있습니다.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

> [!IMPORTANT]  
> 이 예제에서는 필요한 IP 주소에서 인바운드 트래픽을 허용하도록 규칙을 추가하는 방법을 보여 줍니다. 다른 소스에서 인바운드 액세스를 제한하는 규칙은 포함하지 않습니다.
>
> 다음 예제는 인터넷에서 SSH 액세스를 사용 설정하는 방법을 보여 줍니다.
>
> ```powershell
> Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Azure CLI

다음 단계에 따라 인바운드 트래픽을 제한하지만 HDInsight에 필요한 IP 주소에서의 트래픽은 허용하는 가상 네트워크를 만듭니다.

1. 다음 명령을 사용하여 `hdisecure`이라는 새 네트워크 보안 그룹을 만듭니다. 대체 `RESOURCEGROUP` Azure Virtual Network를 포함 하는 리소스 그룹을 사용 하 여 합니다. 대체 `LOCATION` 그룹에 생성 된 위치 (지역).

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    그룹을 만들면 새 그룹에 대한 정보를 받습니다.

2. 다음을 사용하여 Azure HDInsight 상태 및 관리 서비스에서 포트 443에 대한 인바운드 통신을 허용하는 새 네트워크 보안 그룹에 규칙을 추가합니다. 대체 `RESOURCEGROUP` Azure Virtual Network를 포함 하는 리소스 그룹의 이름입니다.

    > [!IMPORTANT]  
    > 이 예제에 사용된 IP 주소를 사용 중인 Azure 지역에 맞게 변경합니다. 이 정보는 [네트워크 보안 그룹 및 사용자 정의 경로가 있는 HDInsight](#hdinsight-ip) 섹션에서 확인할 수 있습니다.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. 이 네트워크 보안 그룹에 대한 고유 식별자를 검색하려면 다음 명령을 사용합니다.

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query 'id'
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    따옴표를 사용 하 여 `id` 예상된 결과 얻지 못한 경우 명령에서.

4. 다음 명령을 사용하여 네트워크 보안 그룹을 서브넷에 적용합니다. 대체는 `GUID` 고 `RESOURCEGROUP` 이전 단계에서 값을 반환 합니다. 대체 `VNETNAME` 고 `SUBNETNAME` 가상 네트워크 이름 및 만들려는 서브넷 이름입니다.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    이 명령이 완료되면 Virtual Network에 HDInsight를 설치할 수 있습니다.

> [!IMPORTANT]  
> 이러한 단계를 사용하면 Azure 클라우드의 HDInsight 상태 및 관리 서비스에 대한 액세스만 열립니다. Virtual Network 외부에서 HDInsight 클러스터에 대한 기타 액세스는 차단됩니다. 가상 네트워크 외부에서 액세스할 수 있도록 하려는 경우 네트워크 보안 그룹 규칙을 추가해야 합니다.
>
> 다음 예제는 인터넷에서 SSH 액세스를 사용 설정하는 방법을 보여 줍니다.
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> 예제: DNS 구성

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>가상 네트워크와 연결된 온-프레미스 네트워크 간에 이름 확인

이 예제에서는 다음과 같이 가정합니다.

* VPN 게이트웨이를 사용하여 온-프레미스 네트워크에 연결된 Azure Virtual Network가 있습니다.

* 가상 네트워크의 사용자 지정 DNS 서버는 운영 체제로 Linux 또는 Unix 실행합니다.

* [바인딩](https://www.isc.org/downloads/bind/)이 사용자 지정 DNS 서버에 설치됩니다.

가상 네트워크의 사용자 지정 DNS 서버에서:

1. Azure PowerShell 또는 Azure CLI를 사용하여 가상 네트워크의 DNS 접미사를 찾습니다.

    대체 `RESOURCEGROUP` virtual network를 포함 하 고 다음 명령을 입력 하는 리소스 그룹의 이름:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 가상 네트워크에 대한 사용자 지정 DNS 서버에서 `/etc/bind/named.conf.local` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 값을 가상 네트워크의 DNS 접미사로 바꿉니다.

    이 구성은 가상 네트워크의 DNS 접미사에 대한 모든 DNS 요청을 Azure 재귀 확인자에 전달합니다.

2. 가상 네트워크에 대한 사용자 지정 DNS 서버에서 `/etc/bind/named.conf.options` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * `10.0.0.0/16` 값을 가상 네트워크의 IP 주소 범위로 바꿉니다. 이 항목으로 이 범위 내의 주소로 이름 확인 요청이 가능합니다.

    * 온-프레미스 네트워크의 IP 주소 범위를 `acl goodclients { ... }` 섹션에 추가합니다.  이 항목으로 온-프레미스 네트워크의 리소스에서 이름 확인 요청이 가능합니다.
    
    * `192.168.0.1` 값을 온-프레미스 DNS 서버의 IP 주소로 바꿉니다. 이 항목은 다른 모든 DNS 요청을 온-프레미스 DNS 서버에 라우팅합니다.

3. 구성을 사용하려면 바인딩을 다시 시작합니다. 예: `sudo service bind9 restart`.

4. 온-프레미스 DNS 서버에 조건부 전달자를 추가합니다. 1단계에서 DNS 접미사에 대한 요청을 사용자 지정 DNS 서버에 보내도록 조건부 전달자를 구성합니다.

    > [!NOTE]  
    > 조건부 전달자를 추가하는 방법에 대한 구체적인 정보는 DNS 소프트웨어에 대한 설명서를 참조하세요.

다음 단계를 완료한 후 FQDN(정규화된 도메인 이름)을 사용하여 네트워크의 리소스에 연결할 수 있습니다. 이제 HDInsight를 가상 네트워크에 설치할 수 있습니다.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>두 개의 연결된 가상 네트워크 간의 이름 확인

이 예제에서는 다음과 같이 가정합니다.

* VPN 게이트웨이 또는 피어링을 사용하여 연결된 두 Azure Virtual Networks가 있습니다.

* 두 네트워크의 사용자 지정 DNS 서버는 운영 체제로 Linux 또는 Unix를 실행합니다.

* [바인딩](https://www.isc.org/downloads/bind/)이 사용자 지정 DNS 서버에 설치됩니다.

1. Azure PowerShell 또는 Azure CLI를 사용하여 두 가상 네트워크의 DNS 접미사를 찾습니다.

    대체 `RESOURCEGROUP` virtual network를 포함 하 고 다음 명령을 입력 하는 리소스 그룹의 이름:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 사용자 지정 DNS 서버에서 `/etc/bind/named.config.local` 파일의 내용으로 다음 텍스트를 사용합니다. 두 가상 네트워크의 사용자 지정 DNS 서버에서 다음과 같이 변경합니다.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 값을 __다른__ 가상 네트워크의 DNS 접미사로 바꿉니다. 이 항목은 원격 네트워크의 DNS 접미사 요청을 해당 네트워크의 사용자 지정 DNS로 라우팅합니다.

3. 두 가상 네트워크의 사용자 지정 DNS 서버에서 `/etc/bind/named.conf.options` 파일의 내용으로 다음 텍스트를 사용합니다.

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   `10.0.0.0/16` 및 `10.1.0.0/16` 값을 가상 네트워크의 IP 주소 범위로 바꿉니다. 이 항목으로 각 네트워크의 리소스가 DNS 서버에 요청할 수 있습니다.

    가상 네트워크의 DNS 접미사에 해당하지 않는 모든 요청(예: microsoft.com)은 Azure 재귀 확인자에 의해 처리됩니다.

4. 구성을 사용하려면 바인딩을 다시 시작합니다. 예를 들어, 두 DNS 서버에서 `sudo service bind9 restart`입니다.

다음 단계를 완료한 후 FQDN(정규화된 도메인 이름)을 사용하여 가상 네트워크의 리소스에 연결할 수 있습니다. 이제 HDInsight를 가상 네트워크에 설치할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 온-프레미스 네트워크에 연결하기 위해 HDInsight를 구성하는 종단 간 예제는 [HDInsight를 온-프레미스 네트워크에 연결](./connect-on-premises-network.md)을 참조하세요.
* Azure 가상 네트워크에서 Apache Hbase 클러스터를 구성하려면 [Azure Virtual Network의 HDInsight에서 Apache HBase 클러스터 만들기](hbase/apache-hbase-provision-vnet.md)를 참조하세요.
* Apache HBase 지리적 복제를 구성하려면 [Azure 가상 네트워크에서 Apache HBase 클러스터 복제 설정](hbase/apache-hbase-replication.md)을 참조하세요.
* Azure 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

* 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 참조하세요.

* 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.
