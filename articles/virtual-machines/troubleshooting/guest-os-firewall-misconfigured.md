---
title: Azure VM 게스트 OS 방화벽이 잘못 구성됨 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711019"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM 게스트 OS 방화벽이 잘못 구성됨

이 문서에서는 Azure VM에서 잘못 구성된 게스트 운영 체제 방화벽을 수정하는 방법을 소개합니다.

## <a name="symptoms"></a>증상

1.  VM(가상 머신) 시작 화면에 VM이 완전히 로드된 것이 표시됩니다.

2.  게스트 운영 체제가 구성되는 방식에 따라 VM에 도달하는 네트워크 트래픽이 없거나 일부 있을 수 있습니다.

## <a name="cause"></a>원인

게스트 시스템 방화벽의 잘못된 구성으로 인해 일부 또는 모든 종류의 네트워크 트래픽이 VM에 도달하는 것이 차단될 수 있습니다.

## <a name="solution"></a>해결 방법

다음 단계를 따르기 전에 영향을 받는 VM의 시스템 디스크 스냅숏을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅숏](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

이 문제를 해결하려면 직렬 콘솔을 사용하거나 VM의 시스템 디스크를 복구 VM에 연결하여 [오프라인으로 VM을 복구](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)합니다.

## <a name="online-mitigations"></a>온라인 해결 방법

[직렬 콘솔에 연결한 다음, PowerShell 인스턴스를 엽니다](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). VM에서 직렬 콘솔을 사용하지 않도록 설정한 경우 다음 Azure 문서의 “오프라인으로 VM 복구” 섹션으로 이동합니다.

 [원격 데스크톱을 통해 Azure VM에 연결하려고 할 때 내부 오류 발생](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

다음 규칙을 편집하여 RDP를 통해 VM에 액세스하도록 설정하거나 간편한 문제 해결 환경을 제공할 수 있습니다.

*   원격 데스크톱(TCP-In): 이 규칙은 Azure에서 RDP를 허용하여 VM에 기본 액세스 권한을 제공하는 표준 규칙입니다.

*   Windows 원격 관리(HTTP-In): 이 규칙을 사용하면 PowerShell을 통해 VM에 연결할 수 있습니다. Azure에서는 이러한 유형의 액세스를 통해 스크립팅 측면의 원격 스크립팅 및 문제 해결을 사용할 수 있습니다.

*   파일 및 프린터 공유(SMB-In): 이 규칙을 통해 문제 해결 옵션으로 네트워크 공유 액세스를 사용할 수 있습니다.

*   파일 및 프린터 공유(에코 요청 - ICMPv4-In): 이 규칙을 사용하면 VM을 ping할 수 있습니다.

직렬 콘솔 액세스 인스턴스에서 방화벽 규칙의 현재 상태를 쿼리할 수 있습니다.

*   표시 이름을 매개 변수로 사용하여 쿼리합니다.

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   애플리케이션에서 사용하는 로컬 포트를 사용하여 쿼리합니다.

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   애플리케이션에서 사용하는 로컬 IP 주소를 사용하여 쿼리합니다.

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   규칙을 사용하지 않도록 설정된 것을 확인하는 경우 다음 명령을 실행하여 사용하도록 설정할 수 있습니다.

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   문제 해결을 위해 방화벽 프로필을 OFF로 설정할 수 있습니다.

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    이 작업을 수행하여 방화벽을 올바르게 설정하는 경우 문제 해결을 완료한 후 방화벽을 다시 사용하도록 설정합니다.

    > [!Note]
    > 이 변경 사항을 적용하려면 VM을 다시 시작할 필요는 없습니다.

*   RDP를 통해 VM에 다시 연결을 시도하세요.

### <a name="offline-mitigations"></a>오프라인 해결 방법

1.  참조를 사용 하도록 설정 하거나 방화벽 규칙을 사용 하지 않도록 설정 하려면 [Azure VM 게스트 OS에 대 한 방화벽 규칙을 사용할지](enable-disable-firewall-rule-guest-os.md)합니다.

2.  [인바운드 트래픽을 차단하는 게스트 OS 방화벽 시나리오](guest-os-firewall-blocking-inbound-traffic.md)에 있는지 여부를 확인합니다.

3.  방화벽이 사용자의 액세스를 차단하는지 여부가 여전히 의문인 경우 [Azure VM에서 게스트 OS 방화벽 사용하지 않도록 설정](disable-guest-os-firewall-windows.md)을 참조한 다음, 올바른 규칙을 사용하여 게스트 시스템 방화벽을 다시 사용하도록 설정합니다.

