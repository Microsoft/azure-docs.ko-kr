---
title: Azure VM에 대한 자세한 SSH 문제 해결 | Microsoft Docs
description: Azure 가상 머신에 연결할 때의 문제에 대한 자세한 SSH 문제 해결 단계
keywords: ssh 연결 거부,ssh 오류,azure ssh,SSH 연결 실패
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 3807f713065d16d4c6743c65f6a770d158ac7191
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058497"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Azure에서 Linux VM에 연결할 때의 문제에 대한 자세한 SSH 문제 해결 단계
SSH 클라이언트가 VM의 SSH 서비스에 도달할 수 없는 데에는 여러 원인이 있습니다. 추가적인 [일반 SSH 문제 해결 단계](troubleshoot-ssh-connection.md)를 진행한 경우 연결 문제를 추가적으로 해결해야 합니다. 이 문서에서는 SSH 연결에 문제가 있는지 확인하는 자세한 문제 해결 단계와 해결 방법을 안내합니다.

## <a name="take-preliminary-steps"></a>준비 단계 수행
다음 다이어그램은 관련된 구성 요소를 보여 줍니다.

![SSH 서비스의 구성 요소를 보여주는 다이어그램](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

다음 단계는 실패의 원인을 찾아내고 해결 방안을 알아내는 데 도움이 됩니다.

1. 포털에서 VM의 상태를 확인합니다.
   [Azure Portal](https://portal.azure.com)에서 **가상 머신** > *VM 이름*을 선택합니다.

   VM에 대한 상태 창에 **실행 중**이 표시되어야 합니다. 아래로 스크롤하여 컴퓨팅, 스토리지 및 네트워크 리소스에 대한 최근 활동을 표시합니다.

2. **설정** 을 선택하여 엔드포인트, IP 주소, 네트워크 보안 그룹 및 기타 설정을 검토합니다.

   VM에는 **엔드포인트** 또는 **[네트워크 보안 그룹](../../virtual-network/security-overview.md)** 에서 볼 수 있는 SSH 트래픽에 대해 정의된 엔드포인트가 있어야 합니다. Resource Manager를 사용하여 만든 VM의 엔드포인트는 네트워크 보안 그룹에 저장됩니다. 규칙이 네트워크 보안 그룹에 적용되어 있고 서브넷에서 참조되고 있는지 확인합니다.

네트워크 연결 상태를 확인하려면 구성된 엔드포인트를 점검하고 HTTP와 같은 다른 프로토콜 또는 다른 서비스를 통해 VM에 연결할 수 있는지 확인합니다.

이 단계 후 SSH 연결을 다시 시도해 보세요.

## <a name="find-the-source-of-the-issue"></a>문제의 발생지 찾기
다음 영역의 문제 또는 잘못된 구성으로 인해 컴퓨터의 SSH 클라이언트에서 Azure VM의 SSH 서비스에 연결하지 못할 수 있습니다.

* [SSH 클라이언트 컴퓨터](#source-1-ssh-client-computer)
* [조직 에지 디바이스](#source-2-organization-edge-device)
* [클라우드 서비스 엔드포인트 및 액세스 제어 목록(ACL)](#source-3-cloud-service-endpoint-and-acl)
* [네트워크 보안 그룹](#source-4-network-security-groups)
* [Linux 기반 Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>원본 1: SSH 클라이언트 컴퓨터
문제의 발생지인 사용자의 컴퓨터를 제거하려면 사용자의 컴퓨터가 다른 온-프레미스, Linux 기반 컴퓨터에 SSH 연결을 설정할 수 있는지 확인합니다.

![SSH 클라이언트 컴퓨터 구성 요소를 강조하는 다이어그램](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

연결이 실패하면 컴퓨터에서 다음 문제를 확인합니다.

* 인바운드 또는 아웃 바운드 SSH 트래픽(TCP 22)을 차단하는 로컬 방화벽 설정
* SSH 연결을 방지하는 로컬에 설치된 클라이언트 프록시 소프트웨어
* SSH 연결을 방지하는 로컬에 설치된 네트워크 모니터링 소프트웨어
* 트래픽을 모니터링하거나 특정 유형의 트래픽을 허용하거나 허용하지 않는 다른 유형의 보안 소프트웨어

이러한 조건 중 하나가 적용되면 일시적으로 소프트웨어를 사용하지 않도록 설정하고 온-프레미스 컴퓨터에 SSH 연결을 시도하여 컴퓨터에서 연결을 차단하는 원인을 파악합니다. 그런 다음 네트워크 관리자와 협력하여 SSH 연결을 허용하도록 소프트웨어 설정을 수정합니다.

인증서 인증을 사용하는 경우 홈 디렉터리의 .ssh 폴더에 대해 다음 권한이 있는지 확인하세요.

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa(또는 프라이빗 키가 저장되어 있는 기타 파일)
* Chmod 644 ~/.ssh/known_hosts(SSH를 통해 연결한 호스트 포함)

## <a name="source-2-organization-edge-device"></a>원본 2: 조직 에지 디바이스
문제의 발생지인 조직의 Edge 디바이스를 제거하려면 인터넷에 직접 연결된 컴퓨터가 Azure VM에 SSH 연결을 설정할 수 있는지 확인합니다. 사이트 간 VPN 또는 Azure express 경로 연결을 통해 VM에 액세스 하는 경우 원본 4로 [건너뜁니다. 네트워크 보안 그룹](#nsg).

![조직 에지 디바이스를 강조하는 다이어그램](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

인터넷에 직접 연결된 컴퓨터가 없는 경우 자체 리소스 그룹에 또는 클라우드 서비스에 새 Azure VM을 만든 후 사용합니다. 자세한 내용은 [Azure에서 Linux를 실행하는 가상 머신 만들기](../linux/quick-create-cli.md)를 참조하세요. 테스트가 완료되면 리소스 그룹 또는 VM 및 클라우드 서비스를 삭제합니다.

인터넷에 직접 연결된 컴퓨터에 대한 SSH 연결을 설정할 수 있는 경우 조직 에지 디바이스에서 다음을 확인합니다.

* 인터넷을 사용하여 SSH 트래픽을 차단하는 내부 방화벽
* SSH 연결을 방지하는 프록시 서버
* 경계 네트워크의 디바이스에서 실행되는, SSH 연결을 방지하는 침입 검색 또는 네트워크 모니터링 소프트웨어

네트워크 관리자와 협력하여 인터넷에서 SSH 트래픽을 허용하도록 조직 에지 디바이스 설정을 수정합니다.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>원본 3: 클라우드 서비스 엔드포인트 및 ACL
> [!NOTE]
> 이 발생지는 클래식 배포 모델을 사용하여 만든 VM에만 적용됩니다. 리소스 관리자를 사용 하 여 만든 vm의 경우 원본 4 [로 건너뜁니다. 네트워크 보안 그룹](#nsg).

문제의 발생지인 클라우드 서비스 엔드포인트 및 ACL을 제거하려면 동일한 가상 네트워크의 다른 Azure VM이 SSH를 사용하여 연결할 수 있는지 확인합니다.

![클라우드 서비스 엔드포인트 및 ACL을 강조하는 다이어그램](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

동일한 가상 네트워크에 다른 VM이 없는 경우 새 가상 컴퓨터를 손쉽게 만들 수 있습니다. 자세한 내용은 [CLI를 사용하여 Azure에서 Linux VM 만들기](../linux/quick-create-cli.md)를 참조하세요. 테스트를 마치면 추가한 VM을 삭제합니다.

동일한 가상 네트워크에 있는 VM과 SSH의 연결을 만들 수 있는 경우 다음 영역을 확인합니다.

* **대상 VM의 SSH 트래픽에 대한 엔드포인트 구성.** 엔드포인트의 프라이빗 TCP 포트는 VM에서 SSH 서비스가 수신 대기 중인 TCP 포트와 일치해야 합니다. 기본 포트는 22입니다. **가상 머신** > *VM 이름* > **설정** > **엔드포인트**을 선택하여 Azure Portal에서 SSH TCP 포트 번호를 확인합니다.
* **대상 가상 머신의 SSH 트래픽 엔드포인트에 대한 ACL.** ACL을 통해 인터넷에서 들어오는 트래픽을 원본 IP 주소에 따라 허용 또는 거부하도록 지정할 수 있습니다. ACL이 잘못 구성될 경우 엔드포인트에 SSH 트래픽이 들어오지 못할 수 있습니다. ACL을 확인하고 프록시 또는 다른 에지 서버의 공용 IP 주소에서 들어오는 트래픽이 허용되어 있는지 확인하세요. 자세한 내용은 [네트워크 ACL(액세스 제어 목록) 정보](../../virtual-network/virtual-networks-acl.md)를 참조하세요.

문제의 발생지인 엔드포인트를 제거하려면 현재 엔드포인트를 제거하고 다른 엔드포인트를 만든 다음 SSH 이름(공용 및 프라이빗 포트 번호에 TCP 포트 22)을 지정합니다. 자세한 내용은 [Azure의 가상 컴퓨터에 엔드포인트 설정](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>원본 4: 네트워크 보안 그룹
네트워크 보안 그룹을 사용하면 허용되는 인바운드 및 아웃바운드 트래픽을 더 세부적으로 제어할 수 있습니다. Azure 가상 네트워크의 서브넷 및 클라우드 서비스에 적용되는 규칙을 만들 수 있습니다. 네트워크 보안 그룹 규칙을 확인하고 인터넷으로 나가고 들어오는 SSH 트래픽이 허용되어 있는지 확인합니다.
자세한 내용은 [네트워크 보안 그룹 정보](../../virtual-network/security-overview.md)를 참조하세요.

IP 확인을 사용하여 NSG 구성이 유효한지 검사할 수도 있습니다. 자세한 내용은 [Azure 네트워크 모니터링 개요](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)를 참조하세요. 

## <a name="source-5-linux-based-azure-virtual-machine"></a>원본 5: Linux 기반 Azure 가상 컴퓨터
마지막 가능한 문제 발생지는 Azure 가상 머신 자체입니다.

![Linux 기반 Azure 가상 머신을 강조하는 다이어그램](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

아직 수행하지 않은 경우 지침에 따라 [Linux 기반 가상 머신에 대한 암호를 다시 설정](../linux/reset-password.md)합니다.

컴퓨터에서 다시 연결을 시도하세요. 문제가 계속 발생하면 다음과 같은 문제가 있는 것일 수 있습니다.

* SSH 서비스가 대상 가상 머신에서 실행되고 있지 않습니다.
* SSH 서비스가 TCP 포트 22에서 수신 대기하고 있지 않습니다. 테스트하려면 로컬 컴퓨터에 텔넷 클라이언트를 설치하고 "telnet *cloudServiceName*.cloudapp.net 22"를 실행합니다. 이 단계에서는 가상 머신에서 SSH 엔드포인트에 대한 인바운드 및 아웃바운드 통신을 허용하는지 여부를 결정합니다.
* 대상 가상 머신의 로컬 방화벽에 인바운드 또는 아웃바운드 SSH 트래픽을 방지하는 규칙이 있습니다.
* Azure 가상 머신에서 실행 중인 침입 검색 또는 네트워크 모니터링 소프트웨어가 SSH 연결을 방지하고 있습니다.

## <a name="additional-resources"></a>추가 자료
애플리케이션 액세스 문제를 해결하는 방법에 대한 자세한 내용은 [Azure 가상 머신에서 실행 중인 애플리케이션에 대한 액세스 문제 해결](../linux/troubleshoot-app-connection.md)
