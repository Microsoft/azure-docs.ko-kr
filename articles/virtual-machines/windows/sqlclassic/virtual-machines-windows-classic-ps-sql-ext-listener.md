---
title: Always On 가용성 그룹에 대한 외부 수신기 구성 | Microsoft Docs
description: 이 자습서에서는 연결된 클라우드 서비스의 공용 가상 IP 주소를 사용하여 외부에서 액세스 가능한 Azure의 Always On 가용성 그룹 수신기를 만드는 과정을 안내합니다.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 89623adbddce07cbc3c3ead811f5174d108c9b0e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101628"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Azure에서 Always On 가용성 그룹에 대한 외부 수신기 구성
> [!div class="op_single_selector"]
> * [내부 수신기](../classic/ps-sql-int-listener.md)
> * [외부 수신기](../classic/ps-sql-ext-listener.md)
> 
> 

이 항목에서는 외부에서 인터넷에 액세스할 수 있는 Always On 가용성 그룹에 대해 수신기를 구성하는 방법을 보여줍니다. 수신기를 구성하려면 클라우드 서비스의 **공용 VIP(가상 IP)** 주소를 수신기와 연결해야 합니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

가용성 그룹은 온-프레미스 전용, Azure 전용 또는 하이브리드 구성에 대한 온-프레미스와 Azure 모두에 걸쳐 있는 복제본을 포함할 수 있습니다. Azure 복제본은 동일한 지역 내 또는 여러 Vnet(가상 네트워크)을 사용하 여 여러 지역에 걸쳐 있을 수 있습니다. 다음 단계에서는 [가용성 그룹을 구성](../classic/portal-sql-alwayson-availability-groups.md)했지만 수신기는 구성하지 않았다고 가정합니다.

## <a name="guidelines-and-limitations-for-external-listeners"></a>외부 수신기에 대한 지침 및 제한 사항
클라우드 서비스 공용 VIP 주소를 사용하여 배포할 경우 Azure의 가용성 그룹 수신기에 다음과 같은 지침이 적용됩니다.

* 가용성 그룹 수신기는 Windows Server 2008 R2, Windows Server 2012 및 Windows Server 2012 R2에서 지원됩니다.
* 클라이언트 애플리케이션은 가용성 그룹 VM이 포함된 것과는 다른 클라우드 서비스에 있어야 합니다. Azure는 동일한 클라우드 서비스에 있는 클라이언트 및 서버에서의 직접 서버 반환을 지원하지 않습니다.
* 이 문서의 단계는 기본적으로 클라우드 서비스 VIP(가상 IP) 주소를 사용하도록 하나의 수신기를 구성하는 방법을 보여 줍니다. 그러나 클라우드 서비스에 대한 여러 VIP 주소를 예약하고 만들 수 있습니다. 이렇게 하면 이 문서의 단계를 사용하여 각각 다른 VIP와 연결된 여러 수신기를 만들 수 있습니다. 여러 VIP 주소를 만드는 방법에 대한 자세한 내용은 [클라우드 서비스당 여러 VIP](../../../load-balancer/load-balancer-multivip.md)를 참조하세요.
* 하이브리드 환경에 대한 수신기를 만드는 경우 온-프레미스 네트워크에 Azure 가상 네트워크와 사이트-사이트 VPN뿐 아니라 공용 인터넷에 대한 연결이 있어야 합니다. Azure 서브넷에 있을 때 가용성 그룹 수신기는 해당 클라우드 서비스의 공용 IP 주소를 통해서만 연결할 수 있습니다.
* ILB(내부 부하 분산 장치)를 사용하여 내부 수신기도 있는 동일한 클라우드 서비스에서 외부 수신기를 만들 수는 없습니다.

## <a name="determine-the-accessibility-of-the-listener"></a>수신기의 액세스 가능 여부 확인
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

이 문서에서는 **외부 부하 분산**을 사용하는 수신기를 만드는 데 중점을 둡니다. 가상 네트워크에 대한 개인 수신기를 만들려면 [ILB를 사용하여 수신기](../classic/ps-sql-int-listener.md)를 설정하는 단계를 설명하는 이 문서의 다른 버전을 참조하세요.

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>직접 서버 반환이 있는 부하 분산 VM 엔드포인트 만들기
외부 부하 분산에서는 VM을 호스팅하는 클라우드 서비스의 공용 가상 IP 주소를 사용합니다. 따라서 이 경우에는 부하 분산 장치를 만들거나 구성할 필요가 없습니다.

Azure 복제본을 호스트하는 각 VM에 대해 부하가 분산된 엔드포인트를 만들어야 합니다. 여러 지역에 복제본이 있는 경우 해당 지역에 대한 각 복제본은 동일한 VNet의 동일한 클라우드 서비스에 있어야 합니다. 여러 Azure 지역에 걸쳐 있는 가용성 그룹 복제본을 만들려면 여러 VNet을 구성해야 합니다. VNet 간 연결 구성에 대한 자세한 내용은 [VNet 간 연결 구성](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요.

1. Azure 포털에서 복제본을 호스트하는 각 VM으로 이동하고 세부 정보를 봅니다.
2. 각 VM에 대한 **엔드포인트** 탭을 클릭합니다.
3. 사용할 수신기 엔드포인트의 **이름** 및 **공용 포트**가 사용 중이지 않은지 확인합니다. 아래 예제에서는 이름이 "MyEndpoint"이고 포트는 "1433"입니다.
4. 로컬 클라이언트에서 [최신 PowerShell 모듈](https://azure.microsoft.com/downloads/)을 다운로드하고 설치합니다.
5. **Azure PowerShell**을 시작합니다. 새 PowerShell 세션이 Azure 관리 모듈이 로드된 상태로 열립니다.
6. **Get-AzurePublishSettingsFile**을 실행합니다. 이 cmdlet은 게시 설정 파일을 로컬 디렉터리에 다운로드하도록 브라우저로 안내합니다. Azure 구독에 대한 로그인 자격 증명을 묻는 메시지가 표시될 수 있습니다.
7. 다운로드한 게시 설정 파일의 경로와 함께 **Import-AzurePublishSettingsFile** 명령을 사용합니다.
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    게시 설정 파일을 가져오면 PowerShell 세션에서 Azure 구독을 관리할 수 있습니다.
    
1. 아래의 PowerShell 스크립트를 텍스트 편집기에 복사하고 사용자 환경에 맞게 변수 값을 설정합니다(일부 매개 변수에 대한 기본값 제공). 가용성 그룹에 Azure 지역에 걸쳐 있는 경우, 데이터센터에 있는 클라우드 서비스 및 노드에 대해 각각의 데이터센터에서 한 번씩 스크립트를 실행해야 합니다.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. 변수를 설정한 후에는 텍스트 편집기에서 해당 스크립트를 Azure PowerShell 세션에 복사하여 실행합니다. 프롬프트에 >>가 계속 표시되면 Enter를 다시 입력하여 스크립트 실행이 시작되도록 합니다.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>필요한 경우 KB2854082가 설치되었는지 확인합니다.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>가용성 그룹 노드에서 방화벽 포트 열기
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>가용성 그룹 수신기 만들기

두 단계로 가용성 그룹 수신기를 만듭니다. 먼저, 클라이언트 액세스 지점 클러스터 리소스를 만들고 종속성을 구성합니다. 두번째로, PowerShell로 클러스터 리소스를 구성합니다.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>클라이언트 액세스 지점을 만들고 클러스터 종속성을 구성합니다.
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>PowerShell로 클러스터 리소스를 구성합니다.
1. 외부 부하 분산에서는 복제본을 포함하는 클라우드 서비스의 공용 가상 IP 주소를 구해야 합니다. Azure Portal에 로그인합니다. 가용성 그룹 VM이 포함된 클라우드 서비스로 이동합니다. **대시보드** 보기를 엽니다.
2. **공용 VIP(가상 IP) 주소**아래에 표시된 주소를 확인합니다. 솔루션이 Vnet에 걸쳐 있으면 복제본을 호스팅하는 VM이 포함된 각 클라우드 서비스에 대해 이 단계를 반복합니다.
3. VM 중 하나에서 아래의 PowerShell 스크립트를 텍스트 편집기에 복사하 고 앞에서 기록한 값으로 변수를 설정합니다.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. 변수를 설정한 후에는 앞으로 온 Windows PowerShell 창을 열고 텍스트 편집기의 스크립트를 복사하여 Azure PowerShell 세션에 붙여넣어 실행합니다. 프롬프트에 >>가 계속 표시되면 Enter를 다시 입력하여 스크립트 실행이 시작되도록 합니다.
5. 각 VM에서 이 작업을 반복합니다. 이 스크립트는 클라우드 서비스의 IP 주소로 IP 주소 리소스를 구성하고 프로프 포트 등의 다른 매개 변수를 설정합니다. IP 주소 리소스를 온라인으로 불러올 때 이 자습서의 앞 부분에서 부하 분산된 엔드포인트로부터 프로브 포트에 대한 폴링에 응답할 수 있습니다.

## <a name="bring-the-listener-online"></a>수신기를 온라인 상태로 만들기
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>추가 작업 항목
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>(동일한 VNet 내)가용성 그룹 수신기 테스트
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>(인터넷을 통해)가용성 그룹 수신기 테스트
가상 네트워크 외부에서 수신기에 액세스 하려면 사용 해야 외부/공용 부하 분산 (이 항목에서 설명) ILB 보다는 동일한 VNet 내에서 액세스할 수만 있습니다. 연결 문자열에서 클라우드 서비스 이름을 지정합니다. 예를 들어 이름이 *mycloudservice*인 클라우드 서비스가 있는 경우 sqlcmd 문은 다음과 같습니다.

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

앞의 예와 달리 호출자가 인터넷을 통해 windows 인증을 사용할 수 없으므로 SQL 인증을 사용해야 합니다. 자세한 내용은 참조 하세요. [Always On 가용성 그룹에 Azure VM: 클라이언트 연결 시나리오](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)합니다. SQL 인증을 사용할 때는 두 복제본에서 동일한 로그인을 만들 수 있는지 확인합니다. 가용성 그룹 로그인 문제 해결에 대한 자세한 내용은 [로그인 매핑 또는 포함된 SQL 데이터베이스 사용자를 통해 다른 복제본에 연결하고 가용성 데이터베이스에 매핑하는 방법](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)을 참조하세요.

Always On 복제본이 다른 서브넷에 있는 경우 클라이언트는 연결 문자열에 **MultisubnetFailover=True** 를 지정해야 합니다. 그러면 다른 서브넷에 있는 복제본에 대한 병렬 연결을 시도합니다. 이 시나리오에는 영역 간 Always On 가용성 그룹 배포가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

