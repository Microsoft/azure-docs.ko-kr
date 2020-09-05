---
title: 일반적인 VHD 만들기 문제(FAQ)
description: VHD(가상 하드 디스크)를 만들 때 발생하는 일반적인 문제에 대한 질문과 대답입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653531"
---
# <a name="common-issues-during-vhd-creation"></a>VHD를 만들 때 발생하는 일반적인 문제

FAQ(질문과 대답)는 Azure Virtual Machine 제품의 VHD(가상 하드 디스크)를 만들 때 발생할 수 있는 일반적인 문제를 다룹니다.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Premium Storage의 VHD를 사용하여 Azure Portal에서 VM을 만들려면 어떻게 해야 하나요?

현재 Azure Marketplace는 관리형 스토리지 또는 Azure Premium Storage의 이미지에서 VM 제품을 만들도록 지원하지 않습니다. 자세한 내용은 [Azure Managed Disks 개요](../../virtual-machines/managed-disks-overview.md)를 참조하세요.

## <a name="can-i-use-generation-2-vms-for-offers"></a>2세대 VM을 제품에 사용할 수 있나요?

아니요, 1세대 VHD만 지원됩니다. 그러나 현재 Microsoft Azure 플랫폼 팀과 협력하여 2세대 VM에 대한 지원을 연구하고 있습니다. 자세한 내용은 [Hyper-V에 1 또는 2세대 가상 머신을 만들어야 하나요?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.

## <a name="how-do-i-change-the-name-of-the-host"></a>호스트의 이름을 변경하려면 어떻게 해야 하나요?

불가능합니다. VM이 생성된 후에는 사용자(소유자 포함)가 호스트 이름을 업데이트할 수 없습니다.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하려면 어떻게 해야 하나요?

Windows 및 Linux 기반 VM에 대해 RDS를 다시 설정하는 방법을 설명하는 문서는 다음과 같습니다.

* [Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법](/azure/virtual-machines/troubleshooting/reset-rdp)
* [VMAccess 확장을 사용하여 Linux VM 암호 또는 SSH 키 재설정, SSH 구성 수정, 디스크 일관성 검사를 수행하는 방법](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>새 SSH 인증서를 생성하려면 어떻게 해야 하나요?

인증서 생성 방법은 [Azure VM 이미지 인증](https://aks.ms/CertifyVMimage)에 설명되어 있습니다.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>내 VM에서 작동하도록 VPN(가상 사설망)을 구성하려면 어떻게 해야 하나요?

Azure Resource Manager 배포 모델을 사용하는 경우 세 가지 옵션은 다음과 같습니다.

* [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Azure PowerShell을 사용하여 경로 기반 VPN 게이트웨이 만들기](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure 기반 VM에서 Microsoft 서버 소프트웨어를 실행하기 위한 Microsoft 지원 정책은 무엇인가요?

[Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)에서 자세한 내용을 확인할 수 있습니다.

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>가상 머신에 연결되는 고유 식별자가 있나요?

예, 가상 머신이 Azure에서 호스팅되는 경우에는 있습니다. Azure는 생성된 새 VM 리소스 각각에 [Azure Virtual Machine 고유 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)라는 고유 식별자를 할당합니다. 자세한 내용은 Azure Virtual Machine 고유 ID를 참조하세요. [List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)를 통해 이 식별자를 가져올 수도 있습니다.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM에서 시작 태스크에 있는 사용자 지정 스크립트 확장을 어떻게 관리하나요?

Azure PowerShell 모듈, Azure Resource Manager 템플릿을 통해 사용자 지정 스크립트 확장을 사용하는 방법과 Windows 시스템의 문제 해결 단계에 대한 자세한 내용은 [Windows용 사용자 지정 스크립트 확장](/azure/virtual-machines/extensions/custom-script-windows)을 참조하세요.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace에서 32비트 애플리케이션 또는 서비스가 지원되나요?

일반적으로 그렇지 않습니다. Azure VM에 지원되는 운영 체제 및 표준 서비스는 모두 64비트입니다. 대부분의 64비트 운영 체제는 이전 버전과의 호환성을 위해 32비트 버전의 애플리케이션을 지원하지만, VM 솔루션의 일부로 32비트 애플리케이션을 사용하는 것은 지원되지 않으며 권장되지 않습니다. 애플리케이션을 64비트 프로젝트로 다시 만드세요.

자세한 내용은 다음 문서를 참조하세요.

* [32비트 애플리케이션 실행](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 가상 머신에 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>오류: VHD가 이미 리소스로 이미지 리포지토리에 등록됨

내 VHD에서 이미지를 만들려고 할 때마다 Azure PowerShell에서 "리소스인 이미지 리포지토리로 VHD를 이미 등록했습니다." 오류가 표시됩니다. 이전에 이미지를 만들지도 않았고, Azure에서 이 이름을 가진 이미지를 찾지도 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 문제는 일반적으로 사용자가 잠겨 있는 VHD에서 VM을 만든 경우에 발생합니다. 이 VHD에서 할당된 VM이 없는지 확인한 다음, 작업을 다시 시도하세요. 문제가 계속 발생하면 지원 티켓을 여세요. [파트너 센터 지원](support.md)을 참조하세요.
