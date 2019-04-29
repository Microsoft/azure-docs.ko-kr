---
title: Azure에서 SQL Server Virtual Machine에 연결(클래식) | Microsoft Docs
description: Azure의 Virtual Machine에서 실행되는 SQL Server에 연결하는 방법을 알아봅니다. 이 항목에서는 클래식 배포 모드를 사용합니다. 시나리오는 네트워킹 구성 및 클라이언트의 위치에 따라 다릅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: b8994d4c1eabf4381bf8364c76f7328d225f7e1a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111530"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Azure에서 SQL Server Virtual Machine 연결(클래식 배포)
> [!div class="op_single_selector"]
> * [리소스 관리자](../sql/virtual-machines-windows-sql-connect.md)
> * [클래식](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>개요
이 항목에서는 Azure 가상 머신에서 실행되는 SQL Server 인스턴스에 연결하는 방법을 설명합니다. 여기서는 몇 가지 [일반 연결 시나리오](#connection-scenarios)를 다룬 다음 [Azure VM에서 SQL Server 연결을 구성하기 위한 상세 단계](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)를 제공합니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager VM을 사용하는 경우 [Azure에서 Resource Manager를 사용하여 SQL Server Virtual Machine에 연결](../sql/virtual-machines-windows-sql-connect.md)을 참조하세요.

## <a name="connection-scenarios"></a>연결 시나리오
클라이언트가 Virtual Machine을 실행 중인 SQL Server에 연결하는 방법은 클라이언트의 위치 및 컴퓨터/네트워킹 구성에 따라 달라집니다. 이 시나리오에는 다음이 포함됩니다.

* [동일한 클라우드 서비스의 SQL Server에 연결](#connect-to-sql-server-in-the-same-cloud-service)
* [인터넷을 통해 SQL Server에 연결](#connect-to-sql-server-over-the-internet)
* [동일한 가상 네트워크의 SQL Server에 연결](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> 이러한 방법으로 연결하기 전에 [이 문서의 단계에 따라 연결을 구성](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)해야 합니다.
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>동일한 클라우드 서비스의 SQL Server에 연결
한 클라우드 서비스에 여러 가상 머신을 만들 수 있습니다. 가상 머신 시나리오를 이해하려면 [가상 머신을 가상 네트워크 또는 클라우드 서비스와 연결하는 방법](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service)을 참조하세요. 이 시나리오는 한 가상 컴퓨터의 클라이언트가 동일한 클라우드 서비스의 다른 가상 컴퓨터에서 실행 중인 SQL Server에 연결하려고 하는 경우입니다.

이 시나리오에서는 VM **이름**(포털에 **컴퓨터 이름** 또는 **호스트 이름**으로도 표시됨)을 사용하여 연결할 수 있습니다. 이 이름은 VM을 만들 때 제공된 이름입니다. 예를 들어 SQL VM 이름을 **mysqlvm**으로 지정하면 동일한 클라우드 서비스의 클라이언트 VM은 다음 연결 문자열을 사용하여 연결할 수 있습니다.

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>인터넷을 통해 SQL Server에 연결 
인터넷을 통해 SQL Server 데이터베이스 엔진에 연결할 경우 들어오는 TCP 통신에 대해 가상 머신 엔드포인트를 만들어야 합니다. 이 Azure 구성 단계에서는 들어오는 TCP 포트 트래픽을 가상 머신에 액세스 가능한 TCP 포트로 보냅니다.

인터넷을 통해 연결하려면 VM의 DNS 이름 및 VM 엔드포인트 포트 번호(이 문서 뒷부분에서 구성)를 사용해야 합니다. DNS 이름을 찾으려면 Azure Portal로 이동하고 **가상 머신(클래식)** 를 선택합니다. 그런 다음 가상 머신을 선택합니다. **DNS 이름**이 **개요** 섹션에 표시됩니다.

예를 들어 이름이 **mysqlvm**, DNS 이름이 **mysqlvm7777.cloudapp.net**이고 VM 엔드포인트가 **57500**인 클래식 가상 머신을 고려해보세요. 올바르게 구성된 연결이라고 가정할 경우 다음 연결 문자열을 사용하여 인터넷의 어디에서든지 가상 머신에 액세스할 수 있습니다.

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

이를 통해 인터넷을 통한 클라이언트의 연결이 활성화되지만 누구나 SQL Server에 연결할 수 있다는 뜻은 아닙니다. 외부 클라이언트는 정확한 사용자 이름과 암호가 있어야 합니다. 보안을 높이기 위해 잘 알려진 포트 1433은 공용 가상 머신 엔드포인트로 사용하지 않는 것이 좋습니다. 또한 가능하다면 엔드포인트에 ACL을 추가하여 트래픽을 허용한 클라이언트로 한정하는 것이 좋습니다. 엔드포인트에서 ACL을 사용하는 방법에 대한 지침은 [엔드포인트에 대한 ACL 관리](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)를 참조하세요.

> [!NOTE]
> 이 방법을 사용하여 SQL Server와 통신하는 경우 Azure 데이터 센터에서 보내는 모든 데이터에는 일반적으로 [아웃바운드 데이터 전송 가격](https://azure.microsoft.com/pricing/details/data-transfers/)이 적용됩니다.
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>동일한 가상 네트워크의 SQL Server에 연결
[Virtual Network](../../../virtual-network/virtual-networks-overview.md) 에서는 추가적인 시나리오가 가능합니다. VM이 다른 클라우드 서비스에 있더라도 동일한 가상 네트워크의 VM에 연결할 수 있습니다. 또한 [사이트 간 VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)을 통해 온-프레미스 네트워크와 컴퓨터에 VM을 연결하는 하이브리드 아키텍처를 만들 수 있습니다.

가상 네트워크를 사용하면 Azure VM을 도메인에 연결할 수 있습니다. 이것이 SQL Server에 Windows 인증을 사용하는 유일한 방법입니다. 다른 연결 시나리오의 경우 사용자 이름과 암호가 있는 SQL 인증이 필요합니다.

도메인 환경과 Windows 인증을 구성하려는 경우 이 글의 단계를 사용하여 공용 엔드포인트나 SQL 인증 및 로그인을 구성할 필요가 없습니다. 이 시나리오에서는 연결 문자열에 SQL Server VM 이름을 지정하여 SQL Server에 연결할 수 있습니다. 다음 예에서는 Windows 인증도 구성되었고 사용자에게 SQL Server 인스턴스에 대한 액세스 권한이 있다고 가정합니다.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Azure VM에서 SQL Server 연결을 구성하기 위한 단계
다음 단계는 SQL Server Management Studio (SSMS)를 사용하여 인터넷을 통해 SQL Server 인스턴스에 연결하는 방법을 보여줍니다. 그러나 동일한 단계는 온-프레미스 및 Azure에서 실행중인 애플리케이션에 대해 SQL Server 가상 머신을 액세스할 수 있게 만들도록 적용합니다.

인터넷 또는 다른 VM에서 SQL Server의 인스턴스에 연결하기 전에 먼저 아래의 섹션에 설명된 대로 다음 작업을 완료해야 합니다.

* [가상 컴퓨터에 대한 TCP 엔드포인트 만들기](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Windows 방화벽에서 TCP 포트 열기](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [TCP 프로토콜에서 수신하도록 SQL Server 구성](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [혼합된 모드 인증에 대한 SQL Server 구성](#configure-sql-server-for-mixed-mode-authentication)
* [SQL Server 인증 로그인 만들기](#create-sql-server-authentication-logins)
* [가상 컴퓨터의 DNS 이름 확인](#determine-the-dns-name-of-the-virtual-machine)
* [다른 컴퓨터에서 데이터베이스 엔진에 연결](#connect-to-the-database-engine-from-another-computer)

연결 경로는 다음 다이어그램에 요약되어 있습니다.

![SQL Server 가상 머신에 연결](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>다음 단계
고가용성 및 재해 복구를 위해 AlwaysOn 가용성 그룹도 사용하려는 경우 수신기의 구현을 고려해야 합니다. 데이터베이스 클라이언트는 SQL Server 인스턴스 중 하나에 직접 연결하기 보다는 수신기에 연결합니다. 수신기는 가용성 그룹의 주 복제본에 클라이언트를 라우팅합니다. 자세한 내용은 [Azure에서 AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성](../classic/ps-sql-int-listener.md)을 참조하세요.

Azure 가상 머신에서 실행되는 SQL Server에 대한 모든 보안 모범 사례를 반드시 검토해야 합니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](../sql/virtual-machines-windows-sql-security.md)을 참조하세요.

[학습 경로를 탐색](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)합니다. 

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

