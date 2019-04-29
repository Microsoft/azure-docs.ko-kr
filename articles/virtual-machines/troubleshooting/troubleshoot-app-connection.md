---
title: Azure에서 VM 애플리케이션 액세스 문제 해결 | Microsoft Docs
description: 이러한 자세한 문제 해결 단계를 사용하여 Azure의 가상 머신에서 실행되는 애플리케이션에 연결 시 문제를 격리하세요.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: 애플리케이션을 시작할 수 없음, 프로그램이 열리지 않음, 수신 포트가 차단됨, 프로그램을 시작할 수 없음, 수신 포트 차단됨
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 81535d51617a419174331dbf9b18ea558913dfa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922247"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Azure의 가상 머신에서 애플리케이션 연결 문제해결

Azure VM(가상 머신)에서 실행되는 애플리케이션을 시작 또는 연결할 수 없는 다양한 이유가 있습니다. 이유로는 애플리케이션이 실행되지 않거나 예상된 포트에서 수신 대기하지 않거나, 수신 포트가 차단되었거나, 네트워킹 규칙이 애플리케이션에 트래픽을 올바르게 전달하지 않는 등의 경우가 포함됩니다. 이 문서에서는 문제를 찾고 해결하는 체계적인 방법을 설명합니다.

RDP 또는 SSH를 사용하여 VM에 연결하는 데 문제가 있는 경우 먼저 다음 문서 중 하나를 참조하세요.

* [Windows 기반 Azure Virtual Machine에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md)
* [Linux 기반 Azure 가상 머신에 SSH(Secure Shell) 연결 문제 해결](troubleshoot-ssh-connection.md)

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.

## <a name="quick-start-troubleshooting-steps"></a>빠른 시작 문제 해결 단계
애플리케이션에 연결하는 데 문제가 있는 경우 다음과 같은 일반적인 문제 해결 단계를 시도하세요. 각 단계 후 애플리케이션을 다시 연결해 보세요.

* 가상 머신 다시 시작
* 엔드포인트 / 방화벽 규칙 / NSG(네트워크 보안 그룹) 규칙 다시 만들기
  * [Resource Manager 모델 - 네트워크 보안 그룹 관리](../../virtual-network/manage-network-security-group.md)
  * [클래식 모델 - Cloud Services 엔드포인트 관리](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* 다른 Azure 가상 네트워크 등 다른 위치에서 연결
* 가상 머신 다시 배포
  * [Windows VM 다시 배포](redeploy-to-new-node-windows.md)
  * [Linux VM 다시 배포](redeploy-to-new-node-linux.md)
* 가상 머신 다시 만들기

자세한 내용은 [엔드포인트 연결 문제 해결(RDP/SSH/HTTP 등의 오류)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)를 참조하세요.

## <a name="detailed-troubleshooting-overview"></a>자세한 문제 해결 개요
Azure 가상 머신에서 실행되는 애플리케이션의 액세스 문제 해결에 대한 4개의 주요 영역이 있습니다.

![애플리케이션을 시작할 수 없는 문제 해결](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Azure 가상 머신에서 실행되는 애플리케이션
   * 애플리케이션 자체가 제대로 실행되나요?
2. Azure 가상 머신.
   * VM 자체가 제대로 실행되고 요청에 응답하나요?
3. Azure 네트워크 엔드포인트.
   * 클래식 배포 모델의 가상 머신용 클라우드 서비스 엔드포인트.
   * Resource Manager 배포 모델의 가상 머신용 네트워크 보안 그룹 및 인바운드 NAT 규칙.
   * 트래픽이 사용자에서 VM/응용 프로그램으로 예상되는 포트에서 흐를 수 있나요?
4. 인터넷 에지 디바이스
   * 트래픽 흐름을 방지하도록 방화벽 규칙이 제대로 적용되고 있나요?

사이트-사이트 VPN 또는 ExpressRoute 연결을 통해 애플리케이션에 액세스하는 클라이언트 컴퓨터에 대해, 문제를 일으키는 주요 영역은 애플리케이션 및 Azure 가상 컴퓨터입니다.

문제 및 해당 보정의 원인을 확인하려면 다음 단계를 수행합니다.

## <a name="step-1-access-application-from-target-vm"></a>1단계: 대상 VM에서에서 응용 프로그램 액세스
애플리케이션이 실행 중인 VM에서 해당 클라이언트 프로그램으로 애플리케이션에 액세스합니다. 로컬 호스트 이름, 로컬 IP 주소 또는 루프백 주소(127.0.0.1)를 사용합니다.

![VM에서 직접 애플리케이션 시작](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

예를 들어 애플리케이션이 웹 서버인 경우, VM에서 브라우저를 열고 해당 VM에서 호스트되는 웹 페이지에 액세스를 시도합니다.

애플리케이션에 액세스할 수 있는 경우 [2단계](#step2)로 이동합니다.

애플리케이션에 액세스할 수 없는 경우, 다음 설정을 확인합니다.

* 애플리케이션이 목표 가상 머신에서 실행 중입니다.
* 애플리케이션이 예상된 TCP 및 UDP 포트에서 수신 중입니다.

Windows 및 Linux 기반 가상 머신 둘 다에서 **netstat -a** 명령을 사용하여 활성 수신 포트를 표시합니다. 애플리케이션이 수신해야 할 예상되는 포트에 대한 출력을 검토하세요. 애플리케이션을 다시 시작하거나 필요에 따라 예상되는 포트를 사용하도록 구성하여 다시 로컬로 애플리케이션에 액세스해 보세요.

## <a id="step2"></a>2단계: 동일한 가상 네트워크의 다른 VM에서 응용 프로그램 액세스
VM의 호스트 이름 또는 Azure 할당 공용, 개인 또는 공급자 IP 주소를 사용하여 다른 VM이지만 동일한 가상 네트워크에서 애플리케이션에 액세스합니다. 클래식 배포 모델을 사용하여 만든 가상 머신의 경우 클라우드 서비스의 공용 IP 주소를 사용하지 않습니다.

![다른 VM에서 애플리케이션 시작](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

예를 들어 애플리케이션이 웹 서버인 경우, 동일한 가상 네트워크에 있는 다른 VM의 브라우저에서 웹 페이지 액세스를 시도하세요.

애플리케이션에 액세스할 수 있는 경우 [3단계](#step3)로 이동합니다.

애플리케이션에 액세스할 수 없는 경우, 다음 설정을 확인합니다.

* 대상 VM의 호스트 방화벽이 인바운드 요청 및 아웃 바운드 응답 트래픽을 허용 중입니다.
* 대상 VM에서 실행되는 침입 탐지 또는 네트워크 모니터링 소프트웨어가 트래픽을 허용 중입니다.
* Cloud Services 엔드포인트 또는 네트워크 보안 그룹은 다음과 같이 트래픽을 허용하고 있습니다.
  * [클래식 모델 - Cloud Services 엔드포인트 관리](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager 모델 - 네트워크 보안 그룹 관리](../../virtual-network/manage-network-security-group.md)
* 부하 분산 장치 또는 방화벽과 같은 테스트 VM 및 VM 간의 경로에서 사용자의 VM에서 실행 중인 개별 구성 요소가 트래픽을 허용 중입니다.

Windows 기반 가상 컴퓨터에서, 방화벽 규칙이 사용자의 애플리케이션의 인바운드 및 아웃 바운드 트래픽을 제외할지 여부를 확인하려면 고급 보안이 포함된 Windows 방화벽을 사용하세요.

## <a id="step3"></a>3단계: 가상 네트워크 외부에서 액세스 응용 프로그램
VM에서 애플리케이션이 실행되고 있는 경우 가상 네트워크 외부의 컴퓨터에서 애플리케이션에 대한 액세스를 시도하세요. 다른 네트워크를 원래의 클라이언트 컴퓨터로 사용합니다.

![가상 네트워크 외부의 컴퓨터에서 애플리케이션 시작](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

예를 들면, 애플리케이션이 웹 서버인 경우, 가상 네트워크에 있지 않은 컴퓨터에서 실행 중인 브라우져에서 웹페이지 액세스를 시도합니다.

애플리케이션에 액세스할 수 없는 경우, 다음 설정을 확인합니다.

* 클래식 배포 모델을 사용하여 만든 VM
  
  * VM의 엔드포인트 구성에서 수신 트래픽을 허용하는지, 특히 프로토콜(TCP 또는 UDP), 공용 및 개인 포트 번호를 허용하는지 확인합니다.
  * 엔드포인트의 ACL(액세스 제어 목록)이 인트라넷에서 들어오는 트래픽을 차단하지 않는지 확인합니다.
  * 자세한 내용은 [Virtual Machine으로 엔드포인트를 설정하는 방법](../windows/classic/setup-endpoints.md)을 참조하세요.
* Resource Manager 배포 모델을 사용하여 만든 VM
  
  * VM의 인바운드 NAT 규칙 구성에서 수신 트래픽을 허용하는지, 특히 프로토콜(TCP 또는 UDP), 공용 및 개인 포트 번호를 허용하는지 확인합니다.
  * 네트워크 보안 그룹이 인바운드 요청 및 아웃바운드 요청 트래픽을 허용하는지 확인합니다.
  * 자세한 내용은 [NSG(네트워크 보안 그룹)란?](../../virtual-network/security-overview.md)을 참조하세요.

가상 머신 또는 엔드포인트가 부하 분산 집합의 구성원인 경우:

* 프로브 프로토콜(TCP 또는 UDP) 및 포트 번호가 올바른지 확인합니다.
* 프로브 프로토콜 및 포트가 부하 분산 집합 프로토콜 및 포트와 다른 경우:
  * 애플리케이션이 프로브 프로토콜(TCP 또는 UDP) 및 포트 번호에서 수신 대기 중인지 확인합니다(대상 VM에서 **netstat –a** 사용).
  * 대상 VM의 호스트 방화벽이 인바운드 프로브 요청 및 아웃바운드 프로브 응답 트래픽을 허용하는지 확인합니다.

애플리케이션에 액세스 할 수 있는 경우, 인터넷 에지 장치가 다음을 허용하는지 확인합니다.

* 아웃 바운드 애플리케이션이 클라이언트 컴퓨터에서 부터 Azure 가상 머신에 도달하는 트래픽 요청
* Azure 가상 머신에서 발생하는 인바운드 애플리케이션 응답 트래픽

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>4단계: 애플리케이션에 액세스할 수 없는 경우 IP 확인을 사용하여 설정을 확인합니다. 

자세한 내용은 [Azure 네트워크 모니터링 개요](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스
[Windows 기반 Azure Virtual Machine에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md)

[Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결](troubleshoot-ssh-connection.md)


