---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.openlocfilehash: 276ddf0a70fa450451cd3ddc78c7610c4ab1edc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60326116"
---
가용성 그룹 수신기는 SQL Server 가용성 그룹에서 수신하는 IP 주소 및 네트워크 이름입니다. 가용성 그룹 수신기를 만들려면 다음을 수행합니다.

1. <a name="getnet"></a>클러스터 네트워크 리소스의 이름을 가져옵니다.

    a. RDP를 사용하여 주 복제본을 호스트하는 Azure 가상 머신에 연결합니다. 

    b. 장애 조치(failover) 클러스터 관리자를 엽니다.

    다. **네트워크** 노드를 선택하고 클러스터 네트워크 이름을 확인합니다. 이 이름을 PowerShell 스크립트에서 `$ClusterNetworkName` 변수에 사용합니다. 다음 이미지에서 클러스터 네트워크 이름은 **클러스터 네트워크 1**입니다.

   ![클러스터 네트워크 이름](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>클라이언트 액세스 지점을 추가합니다.  
    클라이언트 액세스 지점은 애플리케이션이 가용성 그룹의 데이터베이스에 연결하는 데 사용하는 네트워크 이름입니다. 장애 조치(Failover) 클러스터 관리자에서 클라이언트 액세스 지점을 만듭니다.

    a. 클러스터 이름을 확장한 다음 **역할**을 클릭합니다.

    b. **역할** 창에서 가용성 그룹 이름을 마우스 오른쪽 단추로 클릭한 다음 **리소스 추가** > **클라이언트 액세스 지점**을 차례로 선택합니다.

   ![클라이언트 액세스 지점](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    다. **이름** 상자에서 이 새 수신기의 이름을 만듭니다. 
   새 수신기의 이름은 애플리케이션에서 SQL Server 가용성 그룹의 데이터베이스에 연결하는 데 사용하는 네트워크 이름입니다.

    d. 수신기 만들기를 완료하려면 **다음**을 두 번 클릭한 다음 **마침**을 클릭합니다. 현재 온라인 상태에서 수신기 또는 리소스를 가져오지 마세요.

3. 가용성 그룹 클러스터 역할을 오프라인으로 전환합니다. **역할**에 있는 **장애 조치(Failover) 클러스터 관리자**에서 역할을 마우스 오른쪽 단추로 클릭하고 **역할 중지**를 선택합니다.

4. <a name="congroup"></a>가용성 그룹에 대한 IP 리소스를 구성합니다.

    a. **리소스** 탭을 클릭한 다음 만든 클라이언트 액세스 지점을 펼칩니다.  
    클라이언트 액세스 지점은 오프라인입니다.

   ![클라이언트 액세스 지점](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. IP 리소스를 마우스 오른쪽 단추로 클릭한 다음 [속성]을 클릭합니다. IP 주소의 이름을 적어두고 PowerShell 스크립트의 `$IPResourceName` 변수에 사용합니다.

    다. **IP 주소**에서 **고정 IP 주소**를 클릭합니다. Azure Portal에서 부하 분산 장치 주소를 설정할 때 사용한 주소와 동일한 주소로 IP 주소를 설정합니다.

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

5. <a name = "dependencyGroup"></a>SQL Server 가용성 그룹 리소스가 클라이언트 액세스 지점에 종속되게 합니다.

    a. [장애 조치(Failover) 클러스터 관리자]에서 **역할**을 클릭한 다음 가용성 그룹을 클릭합니다.

    b. **리소스** 탭의 **기타 리소스**에서 가용성 리소스 그룹을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다. 

    다. [종속성] 탭에서 클라이언트 액세스 지점(수신기) 리소스의 이름을 추가합니다.

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. **확인**을 클릭합니다.

6. <a name="listname"></a>클라이언트 액세스 지점 리소스가 IP 주소에 종속되게 합니다.

    a. [장애 조치(Failover) 클러스터 관리자]에서 **역할**을 클릭한 다음 가용성 그룹을 클릭합니다. 

    b. **리소스** 탭에서 **서버 이름** 아래의 클라이언트 액세스 지점 리소스를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다. 

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    다. **종속성** 탭을 클릭합니다. IP 주소가 종속성인지 확인합니다. 그렇지 않으면 IP 주소에 대한 종속성을 설정합니다. 여러 리소스가 나열되어 있으면 IP 주소에 AND가 아닌 OR 종속성이 있는지 확인합니다. **확인**을 클릭합니다. 

   ![IP 리소스](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >종속성이 올바르게 구성되었는지 확인할 수 있습니다. [장애 조치(Failover) 클러스터 관리자]에서 [역할]로 이동하고, 가용성 그룹을 마우스 오른쪽 단추로 클릭하며, **기타 작업**을 클릭한 다음 **종속성 보고서 표시**를 클릭합니다. 종속성이 올바르게 구성되면 가용성 그룹은 네트워크 이름에 종속되고 네트워크 이름은 IP 주소에 종속됩니다. 

7. <a name="setparam"></a>PowerShell에서 클러스터 매개 변수를 설정합니다.

   a. 다음 PowerShell 스크립트를 SQL Server 인스턴스 중 하나에 복사합니다. 사용자 환경에 맞게 변수를 업데이트합니다.

   - `$ListenerILBIP`는 가용성 그룹 수신기에 대해 Azure 부하 분산 장치에서 만든 IP 주소입니다.

   - `$ListenerProbePort`는 가용성 그룹 수신기에 대해 Azure 부하 분산 장치에서 구성한 포트입니다.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. 클러스터 노드 중 하나에서 PowerShell 스크립트를 실행하여 클러스터 매개 변수를 설정합니다.  

   > [!NOTE]
   > SQL Server 인스턴스가 별도의 지역에 있는 경우 PowerShell 스크립트를 두 번 실행해야 합니다. 처음으로 첫 번째 지역에서 `$ListenerILBIP` 및 `$ListenerProbePort`를 사용합니다. 다음으로 두 번째 지역에서 `$ListenerILBIP` 및 `$ListenerProbePort`를 사용합니다. 클러스터 네트워크 이름과 클러스터 IP 리소스 이름은 각 지역에 마다 다릅니다.

8. 가용성 그룹 클러스터 역할을 오프라인으로 전환합니다. **역할**에 있는 **장애 조치(Failover) 클러스터 관리자**에서 역할을 마우스 오른쪽 단추로 클릭하고 **역할 시작**을 선택합니다.

필요한 경우 WSFC 클러스터 IP 주소에 대한 클러스터 매개 변수를 설정하려면 위의 단계를 반복합니다.

1. WSFC 클러스터 IP 주소의 IP 주소 이름을 가져옵니다. **클러스터 코어 리소스**의 **장애 조치(Failover) 클러스터 관리자**에서 **서버 이름**을 찾습니다.

2. **IP 주소**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

3. IP 주소의 **이름**을 복사합니다. `Cluster IP Address`일 수 있습니다. 

4. <a name="setwsfcparam"></a>PowerShell에서 클러스터 매개 변수를 설정합니다.

   a. 다음 PowerShell 스크립트를 SQL Server 인스턴스 중 하나에 복사합니다. 사용자 환경에 맞게 변수를 업데이트합니다.

   - `$ClusterCoreIP`는 WSFC 코어 클러스터 리소스에 대해 Azure 부하 분산 장치에서 만든 IP 주소입니다. 가용성 그룹 수신기의 IP 주소와는 다릅니다.

   - `$ClusterProbePort`는 WSFC 상태 프로브에 대해 Azure 부하 분산 장치에서 구성한 포트입니다. 가용성 그룹 수신기의 프로브와는 다릅니다.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. 클러스터 노드 중 하나에서 PowerShell 스크립트를 실행하여 클러스터 매개 변수를 설정합니다.  

>[!WARNING]
>가용성 그룹 수신기 상태 프로브 포트는 클러스터 코어 IP 주소 상태 프로브 포트와 달라야 합니다. 이러한 예제에서 수신기 포트는 59999이며 클러스터 코어 IP 주소는 58888입니다. 두 포트는 모두 인바운드 방화벽 규칙을 허용해야 합니다.

<!-- Update_Description: update meta propreties, wording update -->