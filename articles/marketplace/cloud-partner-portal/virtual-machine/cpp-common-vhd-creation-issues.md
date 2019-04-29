---
title: 일반적인 Azure Marketplace용 VHD 만들기 문제(FAQ) | Microsoft Docs
description: VHD 만들기 및 관련 문제에 대한 질문과 대답입니다.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 381f88c4641417bceca0f988d4b1a187aedaa642
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744196"
---
# <a name="common-issues-during-vhd-creation-faq"></a>일반적인 VHD 만들기 문제(FAQ)

다음 FAQ(질문과 대답)에서는 VM 제안용 VHD(가상 하드 디스크) 및 VM(가상 머신)을 만드는 동안 발생하는 일반적인 문제를 다룹니다. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Premium Storage에 업로드된 VHD를 사용하여 Azure Portal에서 VM을 만들려면 어떻게 해야 하나요?

현재 Azure Marketplace는 관리되는 스토리지 또는 Azure Premium Storage에 있는 이미지에서 VM 제안을 만들도록 지원하지 않습니다.  이러한 스토리지 옵션에 대한 자세한 내용은 [Azure Managed Disks 개요](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)를 참조하세요.


## <a name="can-you-use-generation-2-vms-for-offers"></a>2세대 VM은 제안에 사용할 수 있나요?

아니요, 1세대 VHD만 지원됩니다.  그러나 현재 Microsoft Azure 플랫폼 팀과 협력하여 2세대 VM에 대한 지원을 조사하고 있습니다.  차이점에 대한 자세한 내용은 [Hyper-V에 1세대 또는 2세대 가상 머신을 만들어야 하나요?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.


## <a name="how-do-you-change-the-name-of-the-host"></a>호스트 이름을 변경하려면 어떻게 해야 하나요?

변경할 수 없습니다.  VM이 만들어지면 사용자(소유자 포함)는 호스트 이름을 업데이트할 수 없습니다.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하려면 어떻게 해야 하나요?

다음 문서에서 Windows 및 Linux 기반 VM에 대해 RDS를 다시 설정하는 방법을 설명하고 있습니다.   

- [Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [VMAccess 확장을 사용하여 Linux VM 암호 또는 SSH 키 재설정, SSH 구성 수정, 디스크 일관성 검사를 수행하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>새 SSH 인증서를 생성하려면 어떻게 해야 하나요?

인증서 생성은 후속 섹션인 [VM 제안에 대한 기술 자산 만들기](./cpp-create-technical-assets.md)의 [VM 이미지에 대한 공유 액세스 서명 URI 가져오기](./cpp-get-sas-uri.md) 문서에서 설명하고 있습니다.


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>내 VM에서 작동하도록 VPN(가상 사설망)을 구성하려면 어떻게 해야 하나요?

Azure Resource Manager 배포 모델을 사용하는 경우 VPN을 설정하는 세 가지 일반적인 옵션은 다음과 같습니다.
- [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [PowerShell을 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure 기반 VM에서 Microsoft 서버 소프트웨어를 실행하기 위한 Microsoft 지원 정책은 무엇인가요?

이러한 지원 정책은 [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 문서에서 자세히 설명하고 있습니다.


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>가상 머신에 연결되는 고유 식별자가 있나요?

예, 가상 머신이 Azure에서 호스팅되는 경우에는 있습니다.  Azure는 만든 새 VM 리소스 각각에 Azure Virtual Machine 고유 ID라는 고유 식별자를 할당합니다.  자세한 내용은 [Azure Virtual Machine 고유 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) 블로그 게시물을 참조하세요.  또한 이 식별자는 [List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)를 통해 프로그래밍 방식으로 가져올 수도 있습니다.


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>VM에서 시작 작업에 있는 사용자 지정 스크립트 확장을 어떻게 관리하나요?

다음 문서에서는 Azure PowerShell 모듈, Azure Resource Manager 템플릿을 사용하여 사용자 지정 스크립트 확장을 사용하는 방법과 Windows 시스템의 자세한 문제 해결 단계를 설명합니다. [Windows용 사용자 지정 스크립트 확장](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Azure Marketplace에서 32비트 애플리케이션 또는 서비스가 지원되나요?

일반적으로 그렇지 않습니다.  Azure VM에 지원되는 운영 체제 및 표준 서비스는 모두 64비트입니다.  그러나 기술적인 관점에서 대부분의 64비트 운영 체제는 이전 버전과의 호환성을 위해 32비트 버전의 애플리케이션을 실행할 수 있습니다.  그러나 32비트 애플리케이션은 VM 솔루션의 일부로 사용하도록 지원되지 않으므로 *매우 권장되지 않습니다*.  대신 애플리케이션을 64비트 프로젝트로 다시 컴파일하세요.

자세한 내용은 다음 문서를 참조하세요.
- [32비트 응용 프로그램 실행](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 가상 머신에 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>내 VHD에서 이미지를 만들려고 할 때마다 PowerShell에서 `.VHD is already registered with image repository as the resource` 오류가 발생합니다. 이미지를 만들지 않았거나 Azure에서 이 이름을 가진 이미지를 찾지 못했습니다. 이 문제를 해결하려면 어떻게 할까요?

이 문제는 일반적으로 사용자가 잠겨 있는 VHD에서 VM을 프로비전한 경우에 발생합니다.  이 VHD에서 할당된 VM이 없는지 확인한 다음, 작업을 다시 시도하세요.  이 문제가 계속되면 [Cloud 파트너 포털 지원](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal)에서 설명한 대로 지원 티켓을 여세요. 

