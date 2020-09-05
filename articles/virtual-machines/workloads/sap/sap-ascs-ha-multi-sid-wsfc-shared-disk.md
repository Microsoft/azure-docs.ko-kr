---
title: Azure의 WSFC&공유 디스크를 사용 하는 SAP ASCS/SCS 다중 SID HA | Microsoft Docs
description: Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스를 위한 다중 SID 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 332c81c8502dac6f057c6ea41c7662e1edde1599
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855184"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성

> ![Windows OS][Logo_Windows] Windows
>

SAP를 배포한 경우 내부 부하 분산 장치를 사용하여 SAP 중앙 서비스(ASCS/SCS) 인스턴스에 대한 Windows 클러스터 구성을 만들어야 합니다.

이 문서에서는 SIOS를 사용 하 여 공유 디스크를 시뮬레이션 하는 기존 WSFC (Windows Server 장애 조치 (Failover) 클러스터링) 클러스터에 추가 SAP ASCS/scs 클러스터형 인스턴스를 설치 하 여 단일 ASCS/SCS 설치에서 SAP 다중 SID 구성으로 이동 하는 방법을 중점적으로 설명 합니다. 이 프로세스가 완료되면 SAP 다중 SID 클러스터가 구성됩니다.

> [!NOTE]
> 이 기능은 Azure Resource Manager 배포 모델에만 사용할 수 있습니다.
>
>각 Azure 내부 부하 분산 장치에 대한 프라이빗 프런트 엔드 IP의 수에 제한이 있습니다.
>
>하나의 WSFC 클러스터에서 SAP ASCS/SCS 인스턴스의 최대수는 각 Azure 내부 부하 분산 장치에 대한 개인 프런트 엔드 IP의 최대수와 같습니다.
>

부하 분산 장치 제한에 대한 자세한 내용은 [네트워킹 제한 - Azure Resource Manager][networking-limits-azure-resource-manager]에서 "부하 분산 장치당 프라이빗 프런트 엔드 IP" 섹션을 참조하세요.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 준비 사항

이 다이어그램처럼 **파일 공유**를 사용하는 한 SAP ASCS/SCS 인스턴스에 사용되는 WSFC 클러스터가 이미 구성되어 있어야 합니다.

![고가용성 SAP ASCS/SCS 인스턴스][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> 설치 프로그램은 다음 조건을 충족해야 합니다.
> * SAP ASCS/SCS 인스턴스는 동일한 WSFC 클러스터를 공유해야 합니다.  
> * 각 DBMS(데이터베이스 관리 시스템) SID에는 해당하는 고유한 전용 WSFC 클러스터가 있어야 합니다.  
> * 하나의 SAP 시스템 SID에 속하는 SAP 애플리케이션 서버에는 고유한 전용 VM이 있어야 합니다.  
> * 동일한 클러스터에서 큐에 넣기 복제 서버 1과 큐에 넣기 복제 서버 2를 함께 사용할 수 없습니다.  

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>공유 디스크를 사용하는 SAP ASCS/SCS 다중 SID 아키텍처

목적은 여기에서 설명한 것처럼 동일한 WSAFC 클러스터에서 여러 SAP ABAP ASCS 또는 SAP Java SCS 클러스터링된 인스턴스를 설치하는 것입니다.

![Azure에서 여러 SAP ASCS/SCS 클러스터링된 인스턴스][sap-ha-guide-figure-6002]

부하 분산 장치 제한에 대한 자세한 내용은 [네트워킹 제한 - Azure Resource Manager][networking-limits-azure-resource-manager]에서 "부하 분산 장치당 프라이빗 프런트 엔드 IP" 섹션을 참조하세요.

두 가지 고가용성 SAP 시스템을 포함한 전체 그림은 다음과 같습니다.

![두 개의 SAP 시스템 SID를 포함한 SAP 고가용성 다중 SID 설치 프로그램][sap-ha-guide-figure-6003]

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a><a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a> SAP 다중 SID 시나리오에 대한 인프라 준비

인프라를 준비하기 위해 다음 매개 변수를 사용하여 추가 SAP ASCS/SCS 인스턴스를 설치할 수 있습니다.

| 매개 변수 이름 | 값 |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| SAP DBMS 내부 부하 분산 장치 | PR5 |
| SAP 가상 호스트 이름 | pr5-sap-cl |
| SAP ASCS/SCS 가상 호스트 IP 주소(추가 Azure Load Balancer IP 주소) | 10.0.0.50 |
| SAP ASCS/SCS 인스턴스 번호 | 50 |
| 추가 SAP ASCS/SCS 인스턴스의 ILB 프로브 포트 | 62350 |

> [!NOTE]
> SAP ASCS/SCS 클러스터 인스턴스의 경우 각 IP 주소에는 고유한 프로브 포트가 필요합니다. 예를 들어 Azure 내부 부하 분산 장치에 있는 하나의 IP 주소가 프로브 포트 62300을 사용하는 경우 해당 부하 분산 장치의 다른 IP 주소는 프로브 포트 62300을 사용할 수 없습니다.
>
>프로브 포트 62300이 이미 예약되어 있으므로 여기서는 프로브 포트 62350을 사용하고 있습니다.

두 개의 노드를 포함한 기존 WSFC 클러스터에서 추가 SAP ASCS/SCS 인스턴스를 설치할 수 있습니다.

| 가상 머신 역할 | 가상 머신 호스트 이름 | 고정 IP 주소 |
| --- | --- | --- |
| ASCS/SCS 인스턴스의 첫 번째 클러스터 노드 |pr1-ascs-0 |10.0.0.10 |
| ASCS/SCS 인스턴스의 두 번째 클러스터 노드 |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>DNS 서버에서 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

다음 매개 변수를 사용하여 ASCS/SCS 인스턴스의 가상 호스트 이름에 대한 DNS 항목을 만들 수 있습니다.

| 새 SAP ASCS/SCS 가상 호스트 이름 | 연결된 IP 주소 |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

새 호스트 이름 및 IP 주소는 다음 스크린샷에 표시된 것처럼 DNS 관리자에 표시됩니다.

![새로운 SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 정의된 DNS 항목을 강조 표시는 DNS 관리자 목록][sap-ha-guide-figure-6004]

> [!NOTE]
> 추가 ASCS/SCS 인스턴스의 가상 호스트 이름에 할당하는 새 IP 주소는 SAP Azure Load Balancer에 할당한 새 IP 주소와 동일해야 합니다.
>
>이 시나리오에서 IP 주소는 10.0.0.50입니다.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>PowerShell을 사용하여 기존 Azure 내부 부하 분산 장치에 IP 주소 추가

동일한 WSFC 클러스터에서 둘 이상의 SAP ASCS/SCS 인스턴스를 만들려면 PowerShell을 사용하여 IP 주소를 기존 Azure 내부 부하 분산 장치에 추가합니다. 각 IP 주소에는 고유한 부하 분산 규칙, 프로브 포트, 프런트 엔드 IP 풀 및 백 엔드 풀이 필요합니다.

다음 스크립트는 기존 부하 분산 장치에 새 IP 주소를 추가합니다. 사용자 환경에 맞게 PowerShell 변수를 업데이트합니다. 이 스크립트는 모든 SAP ASCS /SCS 포트에 필요한 모든 부하 분산 규칙을 만듭니다.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure virtual network and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get a new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get an updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
스크립트를 실행한 후 다음 스크린샷에 표시된 것처럼 결과가 Azure Portal에 표시됩니다.

![Azure Portal에서 새 프런트 엔드 IP 풀][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>클러스터 컴퓨터에 디스크 추가 및 SIOS 클러스터 공유 디스크 구성

각 추가 SAP ASCS/SCS 인스턴스에 새 클러스터 공유 디스크를 추가해야 합니다. Windows Server 2012 R2의 경우 현재 사용 중인 WSFC 클러스터 공유 디스크는 SIOS DataKeeper 소프트웨어 솔루션입니다.

다음을 수행합니다.
1. 각 클러스터 노드에 추가 디스크 또는 동일한 크기의 디스크(스트라이프해야 하는)를 추가하고 서식을 지정합니다.
2. SIOS DataKeeper를 사용하여 스토리지 복제를 구성합니다.

이 절차는 WSFC 클러스터 컴퓨터에 SIOS DataKeeper를 이미 설치했다고 가정합니다. 설치한 경우 이제 컴퓨터 간의 복제를 구성해야 합니다. 자세한 프로세스는 [SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]에 설명되어 있습니다.  

![새 SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>SAP 애플리케이션 서버 및 DBMS 클러스터에 대한 VM 배포

두 번째 SAP 시스템에 대한 인프라 준비를 완료하려면 다음을 수행합니다.

1. SAP 애플리케이션 서버에 대한 전용 VM을 배포하고 각각을 고유한 전용 가용성 그룹에 배치합니다.
2. DBMS 클러스터에 대한 전용 VM을 배포하고 각각을 고유한 전용 가용성 그룹에 배치합니다.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>SAP NetWeaver 다중 SID 시스템 설치

두 번째 SAP SID2 시스템을 설치하는 방법에 대한 전체 프로세스는 [SAP ASCS/SCS 인스턴스에 대한 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP NetWeaver HA 설치][sap-high-availability-installation-wsfc-shared-disk]에 설명되어 있습니다.

고급 절차는 다음과 같습니다.

1. 고가용성 [ASCS/SCS 인스턴스를 사용 하 여 SAP를 설치][sap-high-availability-installation-wsfc-shared-disk-install-ascs]합니다.  
 이 단계에서는 기존 WSFC 클러스터 노드 1에 고가용성 ASCS/SCS 인스턴스를 포함한 SAP를 설치하고 있습니다.

2. [ASCS/SCS 인스턴스의 SAP 프로필을 수정합니다][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile].

3. [프로브 포트를 구성합니다][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].  
 이 단계에서는 PowerShell을 사용하여 SAP 클러스터 리소스 SAP-SID2-IP 프로브 포트를 구성하고 있습니다. SAP ASCS/SCS 클러스터 노드 중 하나에서 이 구성을 실행합니다.

4. 데이터베이스 인스턴스 설치.  
 두 번째 클러스터 노드를 설치하려면 SAP 설치 가이드의 단계를 따릅니다.

5. 두 번째 클러스터 노드 설치.  
 이 단계에서는 기존 WSFC 클러스터 노드 2에 고가용성 ASCS/SCS 인스턴스를 포함한 SAP를 설치하고 있습니다. 두 번째 클러스터 노드를 설치하려면 SAP 설치 가이드의 단계를 따릅니다.

6. SAP ASCS /SCS 인스턴스 및 ProbePort의 Windows 방화벽 포트를 엽니다.  
    SAP ASCS/SCS 인스턴스에 사용되는 두 클러스터 노드에서 SAP ASCS/SCS에서 사용하는 모든 Windows 방화벽 포트를 열고 있습니다. 이러한 SAP ASCS/SCS 인스턴스 포트는 [SAP ASCS/SCS 포트][sap-net-weaver-ports-ascs-scs-ports] 챕터에 나열되어 있습니다.

    그 외의 모든 SAP 포트 목록은 [모든 SAP 제품의 TCP/IP 포트][sap-net-weaver-ports]를 참조하세요.  

    또한 62350 시나리오에서와 같이 Azure 내부 부하 분산 장치 프로브 포트를 엽니다. 이 내용은 [이 문서][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]에 설명되어 있습니다.

8. SAP 설치 가이드의 설명에 따라 새로운 전용 VM에 SAP 기본 애플리케이션 서버를 설치합니다.  

9. SAP 설치 가이드의 설명에 따라 새로운 전용 VM에 SAP 추가 애플리케이션 서버를 설치합니다.

10. [SAP ASCS/SCS 인스턴스 장애 조치(failover) 및 SIOS 복제를 테스트합니다][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl].

## <a name="next-steps"></a>다음 단계

- [네트워킹 제한: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure Load Balancer에 대한 다중 VIP][load-balancer-multivip-overview]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png



[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md