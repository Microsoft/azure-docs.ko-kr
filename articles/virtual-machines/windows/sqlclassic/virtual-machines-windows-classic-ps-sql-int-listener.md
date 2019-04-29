---
title: Azure에서 Always On 가용성 그룹에 대한 ILB 수신기 구성 | Microsoft Docs
description: 이 자습서에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Azure에서 내부 부하 분산 장치를 사용하는 Always On 가용성 그룹 수신기를 만듭니다.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0e6a52ea2fdd05546a4da9f8cd1165b41ed27944
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097741"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Azure에서 Always On 가용성 그룹에 대한 ILB 수신기 구성
> [!div class="op_single_selector"]
> * [내부 수신기](../classic/ps-sql-int-listener.md)
> * [외부 수신기](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>개요

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 [Azure Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있다는 것을 이해해야 합니다. 이 문서에서는 클래식 배포 모델의 사용에 대해 설명합니다. 대부분의 새로운 배포에서는 Azure Resource Manager 모델을 사용하는 것이 좋습니다.

Resource Manager 모델에서 Always On 가용성 그룹에 대한 수신기를 구성하려면 [Azure에서 Always On 가용성 그룹에 대한 부하 분산 장치 구성](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)을 참조하세요.

가용성 그룹은 온-프레미스 전용, Azure 전용 또는 하이브리드 구성에 대한 온-프레미스와 Azure 모두에 걸쳐 있는 복제본을 포함할 수 있습니다. Azure 복제본은 동일한 지역 내 또는 여러 가상 네트워크를 사용하는 여러 지역에 걸쳐 있을 수 있습니다. 이 문서의 절차에서는 [가용성 그룹을 구성](../classic/portal-sql-alwayson-availability-groups.md)했지만 수신기는 아직 구성하지 않았다고 가정합니다.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>내부 수신기에 대한 지침 및 제한 사항
Azure에서 가용성 그룹 수신기에 ILB(내부 부하 분산 장치)를 사용하는 경우 다음과 같은 지침이 적용됩니다.

* 가용성 그룹 수신기는 Windows Server 2008 R2, Windows Server 2012 및 Windows Server 2012 R2에서 지원됩니다.
* 수신기는 ILB로 구성되고 ILB는 클라우드 서비스당 하나만 있기 때문에 클라우드 서비스마다 하나의 내부 가용성 그룹 수신기만 지원됩니다. 그러나 외부 수신기는 여러 개를 만들 수 있습니다. 자세한 내용은 [Azure에서 Always On 가용성 그룹에 대한 외부 수신기 구성](../classic/ps-sql-ext-listener.md)을 참조하세요.

## <a name="determine-the-accessibility-of-the-listener"></a>수신기의 액세스 가능 여부 확인
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

이 문서에서는 ILB를 사용하는 수신기를 만드는 데 중점을 둡니다. 공용 또는 외부 수신기가 필요한 경우 [외부 수신기](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 설정에 대해 설명하는 이 문서의 다른 버전을 참조하세요.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>직접 서버 반환이 있는 부하 분산 VM 엔드포인트 만들기
이 섹션에서는 스크립트를 실행하여 ILB를 먼저 만듭니다.

Azure 복제본을 호스트하는 각 VM에 대해 부하가 분산된 엔드포인트를 만듭니다. 여러 지역에 복제본이 있는 경우 해당 지역에 대한 각 복제본은 동일한 Azure 가상 네트워크의 동일한 클라우드 서비스에 있어야 합니다. 여러 Azure 지역에 걸쳐 있는 가용성 그룹 복제본을 만들려면 여러 가상 네트워크를 구성해야 합니다. 가상 네트워크 간의 연결을 구성하는 방법에 대한 정보는 [가상 네트워크 연결에 가상 네트워크 구성](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요.

1. Azure Portal에서 세부 정보를 보려면 복제본을 호스팅하는 각 VM으로 이동합니다.

2. 각 VM에 대한 **엔드포인트** 탭을 클릭합니다.

3. 사용할 수신기 엔드포인트의 **이름** 및 **공용 포트**가 사용 중이지 않은지 확인합니다. 이 섹션의 예제에서는 이름이 *MyEndpoint*이고 포트는 *1433*입니다.

4. 로컬 클라이언트에서 최신 [PowerShell 모듈](https://azure.microsoft.com/downloads/)을 다운로드하고 설치합니다.

5. Azure PowerShell을 시작합니다.  
    새 PowerShell 세션이 Azure 관리 모듈이 로드된 상태로 열립니다.

6. `Get-AzurePublishSettingsFile`을 실행합니다. 이 cmdlet은 게시 설정 파일을 로컬 디렉터리에 다운로드하도록 브라우저로 안내합니다. Azure 구독에 대한 로그인 자격 증명을 묻는 메시지가 표시될 수 있습니다.

7. 다운로드한 게시 설정 파일의 경로와 함께 다음 `Import-AzurePublishSettingsFile` 명령을 실행합니다.

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    게시 설정 파일을 가져오면 PowerShell 세션에서 Azure 구독을 관리할 수 있습니다.

8. *ILB*에 대해 고정 IP 주소를 할당합니다. 다음 명령을 실행하여 현재 가상 네트워크 구성을 검사합니다.

        (Get-AzureVNetConfig).XMLConfiguration
9. 복제본을 호스트하는 VM이 포함된 서브넷의 *서브넷* 이름을 적어 둡니다. 이 이름은 스크립트의 $SubnetName 매개 변수에서 사용됩니다.

10. 복제본을 호스트하는 VM이 포함된 서브넷의 *VirtualNetworkSite* 이름과 시작 *AddressPrefix*를 적어 둡니다. 두 값을 `Test-AzureStaticVNetIP` 명령에 전달하고 *AvailableAddresses*를 검사하여 사용 가능한 IP 주소를 찾습니다. 예를 들어 가상 네트워크 이름이 *MyVNet*이고 서브넷 주소 범위가 *172.16.0.128*에서 시작한다면 다음 명령을 통해 사용 가능한 주소를 나열할 수 있습니다.

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. 사용 가능한 주소 중 하나를 선택하고 다음 단계에 있는 스크립트의 $ILBStaticIP 매개 변수에 사용합니다.

12. 다음 PowerShell 스크립트를 텍스트 편집기에 복사하고 사용자 환경에 맞게 변수 값을 설정합니다. 일부 매개 변수에 대해 기본값이 제공되었습니다.  

    선호도 그룹을 사용하는 기존 배포는 ILB를 추가할 수 없습니다. ILB 요구 사항에 대한 자세한 내용은 [내부 부하 분산 장치 개요](../../../load-balancer/load-balancer-internal-overview.md)를 참조하세요.

    또한 가용성 그룹에 Azure 지역에 걸쳐 있는 경우, 데이터센터에 있는 클라우드 서비스 및 노드에 대해 각각의 데이터센터에서 한 번씩 스크립트를 실행해야 합니다.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. 변수를 설정한 후에는 텍스트 편집기에서 해당 스크립트를 PowerShell 세션에 복사하여 실행합니다. 프롬프트에 **>>** 가 계속 표시되면 Enter를 다시 입력하여 스크립트 실행이 시작되도록 합니다.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>필요한 경우 KB2854082가 설치되었는지 확인합니다.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>가용성 그룹 노드에서 방화벽 포트 열기
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>가용성 그룹 수신기 만들기

두 단계로 가용성 그룹 수신기를 만듭니다. 먼저, 클라이언트 액세스 지점 클러스터 리소스를 만들고 종속성을 구성합니다. 두 번째로, PowerShell에서 클러스터 리소스를 구성합니다.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>클라이언트 액세스 지점을 만들고 클러스터 종속성을 구성합니다.
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>PowerShell로 클러스터 리소스를 구성합니다.
1. ILB의 경우 앞서 만든 ILB의 IP 주소를 사용해야 합니다. PowerShell에서 이 IP 주소를 가져오려면 다음 스크립트를 사용합니다.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. VM 중 하나에서 사용 중인 운영 체제의 PowerShell 스크립트를 텍스트 편집기에 복사하고 앞에서 기록한 값으로 변수를 설정합니다.

    Windows Server 2012 이상에서는 다음 스크립트를 사용합니다.

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Windows Server 2008 R2에서는 다음 스크립트를 사용합니다.

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. 변수를 설정한 후에는 앞으로 온 Windows PowerShell 창을 열고 텍스트 편집기의 스크립트를 복사하여 PowerShell 세션에 붙여넣어 실행합니다. 프롬프트에 **>>** 가 계속 표시되면 Enter를 다시 입력하여 스크립트 실행이 시작되도록 합니다.

4. 각 VM에 대해 이전 단계를 반복합니다.  
    이 스크립트는 클라우드 서비스의 IP 주소로 IP 주소 리소스를 구성하고 프로브 포트 등의 다른 매개 변수를 설정합니다. IP 주소 리소스를 온라인으로 불러올 때 앞 부분에서 부하가 분산된 엔드포인트로부터 프로브 포트에 대한 폴링에 응답할 수 있습니다.

## <a name="bring-the-listener-online"></a>수신기를 온라인 상태로 만들기
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>추가 작업 항목
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>(동일한 가상 네트워크 내)가용성 그룹 수신기 테스트
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>다음 단계
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
