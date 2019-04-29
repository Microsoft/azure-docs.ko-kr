---
title: Azure VM 간의 연결 문제 해결 | Microsoft Docs
description: Azure VM 간의 연결 문제를 해결하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036394"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure VM 간의 연결 문제 해결

Azure VM(Virtual Machines) 간에 연결 문제가 발생할 수 있습니다. 이 문서에서는 이 문제를 해결하는 데 도움이 되는 문제 해결 단계를 제공합니다. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>증상

하나의 Azure VM은 다른 Azure VM에 연결할 수 없습니다.

## <a name="troubleshooting-guidance"></a>문제 해결 지침 

1. [NIC가 잘못 구성되었는지 확인](#step-1-check-whether-nic-is-misconfigured)
2. [네트워크 트래픽이 NSG에 의해 차단되는지 또는 UDR에 의해 차단되는지 확인](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [네트워크 트래픽이 VM 방화벽에 의해 차단되는지 여부 확인](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [VM 앱 또는 서비스가 포트에서 수신 대기하는지 여부 확인](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [문제가 SNAT에 의해 발생하는지 여부 확인](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [트래픽이 클래식 VM의 ACL에 의해 차단되는지 여부 ](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [클래식 VM에 대해 엔드포인트가 생성되는지 여부 확인](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [VM 네트워크 공유에 연결해보기](#step-8-try-to-connect-to-a-vm-network-share)
9. [vNET 간 연결 확인](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>문제 해결 단계

문제를 해결하려면 다음 단계를 수행합니다. 각 단계를 완료한 후 문제가 해결되었는지 확인합니다. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>1단계: NIC가 잘못 구성되었는지 확인

[Microsoft Azure VM에 대한 네트워크 인터페이스를 다시 설정하는 방법](../virtual-machines/windows/reset-network-interface.md)의 단계를 따릅니다. 

네트워크 인터페이스(NIC)를 수정한 후에도 문제가 발생하는 경우 다음 단계를 따릅니다.

**다중 NIC VM**

1. NIC를 추가합니다.
2. 잘못된 NIC에서 문제를 수정하거나 잘못된 NIC를 제거합니다.  그런 다음 NIC를 다시 추가합니다.

자세한 내용은 [가상 머신에서 네트워크 인터페이스 추가 또는 제거](virtual-network-network-interface-vm.md)를 참조하세요.

**단일 NIC VM** 

- [Windows VM 다시 배포](../virtual-machines/windows/redeploy-to-new-node.md)
- [Linux VM 다시 배포](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>2단계: 네트워크 트래픽이 NSG에 의해 차단되는지 또는 UDR에 의해 차단되는지 확인

[Network Watcher IP 흐름 확인](../network-watcher/network-watcher-ip-flow-verify-overview.md) 및 [NSG 흐름 로깅](../network-watcher/network-watcher-nsg-flow-logging-overview.md)을 사용하여 트래픽 흐름을 간섭하는 NSG(네트워크 보안 그룹) 또는 UDR(사용자 정의 경로)이 있는지를 확인합니다.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>3단계: 네트워크 트래픽이 VM 방화벽에 의해 차단되는지 여부 확인

방화벽을 사용하지 않도록 설정하고 결과를 테스트합니다. 문제가 해결되면 방화벽 설정을 확인한 후 방화벽을 다시 사용하도록 설정합니다.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>4단계: VM 앱 또는 서비스가 포트에서 수신 대기하는지 여부 확인

VM 앱 또는 서비스가 포트에서 수신 대기하는지 여부를 확인하려면 다음 방법 중 하나를 사용할 수 있습니다.

- 다음 명령을 실행하여 서버가 해당 포트에서 수신 대기하는지 여부를 확인합니다.

**Windows VM**

    netstat –ano

**Linux VM**

    netstat -l

- 가상 머신 자체에서 **telnet** 명령을 실행하여 포트를 테스트합니다. 테스트가 실패하는 경우 애플리케이션 또는 서비스가 해당 포트에서 수신 대기하도록 구성되지 않은 것입니다.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>5단계: 문제가 SNAT에 의해 발생하는지 여부 확인

일부 시나리오에서 VM은 Azure 외부의 리소스에 종속되어 있는 부하 분산 솔루션 뒤에 배치됩니다. 이러한 시나리오에서 간헐적인 연결 문제가 있는 경우 [SNAT 포트 소모](../load-balancer/load-balancer-outbound-connections.md) 때문일 수 있습니다. 이 문제를 해결하려면 부하 분산 장치 뒤에 있고 NSG 또는 ACL을 사용하여 보안이 유지되는 각 VM에 대해 VIP(또는 클래식의 경우 ILPIP)를 만듭니다. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>6단계: 트래픽이 클래식 VM의 ACL에 의해 차단되는지 여부 확인

ACL(액세스 제어 목록)은 가상 머신 엔드포인트의 트래픽을 선택적으로 허용하거나 거부하는 기능을 제공합니다. 자세한 내용은 [엔드포인트에서의 ACL 관리](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)를 참조하세요.

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>7단계: 클래식 VM에 대해 엔드포인트가 생성되는지 여부 확인

클래식 배포 모델을 사용하여 Azure에서 만든 모든 VM은 개인 네트워크 채널을 통해 동일한 클라우드 서비스 또는 가상 네트워크에 있는 다른 가상 머신과 자동으로 통신할 수 있습니다. 그러나 다른 가상 네트워크의 컴퓨터가 가상 컴퓨터로 인바운드 네트워크 트래픽을 전달하려면 엔드포인트가 필요합니다. 자세한 내용은 [엔드포인트를 설정하는 방법](../virtual-machines/windows/classic/setup-endpoints.md)을 참조하세요.

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>8단계: VM 네트워크 공유에 연결해 보기

VM 네트워크 공유에 연결할 수 없으면 VM의 사용할 수 없는 NIC가 문제의 원인일 수 있습니다. 사용할 수 없는 NIC를 삭제하려면 [사용할 수 없는 NIC를 삭제하는 방법](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)을 참조하세요.

### <a name="step-9-check-inter-vnet-connectivity"></a>9단계: Vnet 간 연결 확인

[Network Watcher IP 흐름 확인](../network-watcher/network-watcher-ip-flow-verify-overview.md) 및 [NSG 흐름 로깅](../network-watcher/network-watcher-nsg-flow-logging-overview.md)을 사용하여 트래픽 흐름을 간섭하는 NSG 또는 UDR이 있는지를 확인합니다. Vnet 간 구성을 [여기](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)에서 확인할 수 있습니다.

### <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.