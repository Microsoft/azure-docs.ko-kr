---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: bc3590e90cacfa4966f0d1f64aa1c8d49483cb1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485461"
---
이 문서에서는 클래식 배포 모델을 사용하여 만든 Azure 가상 머신에 대한 사용자의 일부 일반적인 질문을 해결합니다.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>클래식 배포 모델에서 만든 내 VM을 새 Resource Manager 모델에 마이그레이션할 수 있나요?
예. 마이그레이션 방법에 대한 지침은 다음은 참조하세요.

* [Azure PowerShell을 사용하여 클래식에서 Azure Resource manager로 마이그레이션](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md)
* [Azure CLI를 사용하여 클래식에서 Azure Resource manager로 마이그레이션](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure VM에서 무엇을 실행할 수 있습니까?
모든 구독자는 Azure 가상 머신에서 서버 소프트웨어를 실행할 수 있습니다. 최신 버전의 Windows Server뿐만 아니라 다양한 Linux 배포를 실행할 수 있습니다. 지원 세부 사항은, 다음을 참조하세요:

• Windows VM의 경우 -- [Azure Virtual Machines에 대한 Microsoft 서버 소프트웨어 지원](https://go.microsoft.com/fwlink/p/?LinkId=393550)

• Linux VM의 경우 -- [Azure 인증 배포의 Linux](https://go.microsoft.com/fwlink/p/?LinkId=393551)

Windows 클라이언트 이미지를 사용할 수 있는, Windows 7 및 Windows 8.1의 특정 버전은 MSDN Azure 혜택 구독자와 MSDN 개발 및 테스트 종량제 구독자가 개발 및 테스트 작업을 위해 사용할 수 있습니다. 지침과 제한 사항을 포함한 자세한 내용은 [MSDN 구독자를 위한 Windows 클라이언트 이미지](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)를 참조하세요.

## <a name="why-are-affinity-groups-being-deprecated"></a>왜 선호도 그룹이 사용되지 않나요?
선호도 그룹은 Azure 내 고객의 클라우드 서비스 배포 및 저장소 계정의 지리적 그룹화를 위한 레거시 개념입니다. 선호도 그룹은 초기 Azure 네트워크 설계에서 VM 간 네트워크 성능 향상을 위해 원래 제공되었습니다. 또한 지역에서 작은 집합의 하드웨어로 제한된 가상 네트워크(VNet)의 최초 릴리스를 지원했습니다.

지역 내에서 현재 Azure 네트워크는 선호도 그룹이 더 이상 필요하지 않도록 설계되었습니다. 또한 가상 네트워크는 지역 범위에 있기 때문에 가상 네트워크를 사용하는 경우 선호도 그룹이 필요하지 않습니다. 이러한 성능 향상으로 인해 일부 시나리오에서는 제한될 수 있으므로 고객에게 선호도 그룹을 사용하도록 권하지 않습니다. 선호도 그룹을 사용하면 특정 하드웨어에 VM을 불필요하게 연결하게 되어 선택할 수 있는 VM 크기를 제한합니다. 선호도 그룹과 연결된 특정 하드웨어가 용량에 근접하는 경우 새 VM을 추가하려고 하면 용량 관련 오류가 발생할 수 있습니다.

Azure Resource Manager 배포 모델 및 Azure Portal에서 선호도 그룹 기능을 아직 지원하지 않습니다. 클래식 Azure Portal의 경우 선호도 그룹을 만들고 선호도 그룹에 고정된 저장소 리소스를 만들기 위한 지원을 하지 않고 있습니다. 선호도 그룹을 사용하는 기존 클라우드 서비스를 수정할 필요가 없습니다. 그러나 Azure 기술 지원 엔지니어가 권장하지 않는 한 새 클라우드 서비스에 대한 선호도 그룹을 사용하지 않도록 합니다.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>가상 컴퓨터에 얼마나 많은 용량의 저장소를 사용할 수 있습니까?
각 데이터 디스크의 최대 용량은 1 TB 입니다. 사용할 수 있는 데이터 디스크의 수는 가상 머신의 크기에 따라 달라집니다. 자세한 내용은 [Virtual Machines의 크기](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

Azure 저장소 계정은 운영 체제 디스크 및 모든 데이터 디스크에 대한 저장소를 제공합니다. 각 디스크는 페이지 blob으로 저장된 .vhd 파일입니다. 가격 책정에 대한 자세한 내용은 [Storage 가격 세부 정보](https://go.microsoft.com/fwlink/p/?LinkId=396819)를 참조하세요.

## <a name="which-virtual-hard-disk-types-can-i-use"></a>어떤 가상 하드 디스크 유형을 사용할 수 있습니까?
Azure는 고정된 VHD 형식 가상 하드 디스크를 지원합니다. VHDX가 있고 Azure에서 사용하려는 경우 먼저 Hyper-V 관리자 또는 [convert-VHD](https://go.microsoft.com/fwlink/p/?LinkId=393656) cmdlet을 사용하여 변환해야 합니다. 이를 수행한 후, 가상 머신과 사용할 수 있도록 Azure의 저장소 계정에 VHD를 업로드하기 위해 [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet(서비스 관리 모드)를 사용합니다.

* Linux 지침에 대한 내용은 [Linux 운영 체제를 포함하는 가상 하드 디스크 만들기 및 업로드](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)를 참조하세요.

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>이 가상 머신들은 HYPER-V 가상 머신들과 동일합니까?
많은 측면에서, “1 세대” Hyper-V VM과 유사하지만, 정확히 동일하지는 않습니다. 두 형식 모두 가상화된 하드웨어를 제공하지만, VHD-형식 가상 하드 디스크는 호환이 가능합니다. 이 의미는 사용자가 Hyper-V 및 Azure 사이를 이동할 수 있다는 것입니다. Hyper-V 사용자에게 중요한 세 가지 차이점이 있습니다.

* Azure에서는 가상 머신에 대한 콘솔 액세스를 제공하지 않습니다. 부팅이 완료될 때까지 VM에 액세스할 수 없습니다.
* 대부분의 [크기](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 가진 Azure VM에는 가상 네트워크 어댑터가 하나만 있습니다. 따라서 외부 IP 주소가 하나만 지정될 수 있습니다. A8 및 A9 크기는 제한된 시나리오에서 인스턴스 간의 애플리케이션 커뮤니케이션을 위해 두 번째 네트워크 어댑터를 사용합니다.
* Azure VM은 2세대 Hyper-V VM 기능을 지원하지 않습니다. 이러한 기능에 대한 자세한 내용은 [Hyper-v에 대한 Virtual Machine 사양](https://technet.microsoft.com/library/dn592184.aspx) 및 [2세대 가상 머신 개요](https://technet.microsoft.com/library/dn282285.aspx)를 참조하세요.

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>이러한 가상 머신은 현존하는 온-프레미스 네트워킹 인프라를 사용할 수 있습니까?
클래식 배포 모델에서 만든 가상 머신의 경우 Azure Virtual Network를 사용하여 기존 인프라를 확장할 수 있습니다. 접근법은 지점 사무실을 설치와 유사합니다. 사용자는 Azure에서 VPN(가상 사설망)을 프로비전하고 관리하며 온-프레미스 IT 인프라에 안전하게 연결할 수 있습니다. 자세한 내용은 [Virtual Network 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.

가상 머신을 만들 때, 사용자가 원하는 가상 머신이 소속될 네트워크를 지정해야 합니다. 가상 네트워크에 기존 가상 머신을 가입할 수 없습니다. 그러나 기본 가상 머신에서 가상 하드 디스크(VHD)를 분리한 후, 사용자가 원하는 네트워킹 구성으로 새로운 가상 머신을 만들어서 이 문제를 해결할 수 있습니다.

## <a name="how-can-i-access--my-virtual-machine"></a>나의 가상 머신에 액세스 하려면 어떻게 해야 합니까?
Linux VM에 대한 SSH(Secure Shell) 또는 Windows VM에 대한 원격 데스크톱 연결을 사용하여 가상 머신에 로그온하기 위해서는 원격 연결을 구축해야 합니다. 자세한 내용은 다음을 참조하세요.

* [Windows Server를 실행하는 Virtual Machine에 로그온하는 방법](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 서버가 원격 데스크톱 서비스 세션 호스트로 구성되지 않으면 최대 2개의 동시 연결이 지원됩니다.  
* [Linux를 실행하는 Virtual Machine에 로그온하는 방법](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 기본적으로, SSH는 최대 10개의 동시 연결을 허용합니다. 구성 파일을 편집하여 이 수를 늘릴 수 있습니다.

원격 데스크톱 또는 SSH에 문제가 있는 경우 문제를 해결하는 데 도움이 되는 [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 확장을 설치 및 사용하세요.

Windows VM에 대한 추가 옵션은 다음과 같습니다.

* Azure Portal에서 VM을 찾은 다음 명령 모음에서 **원격 액세스 다시 설정**을 클릭합니다.
* [Windows 기반 Azure Virtual Machine에 대한 원격 데스크톱 연결 문제 해결](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 검토합니다.
* Windows PowerShell 원격을 사용하여 VM에 연결하거나 VM에 연결할 다른 리소스에 대한 추가 엔드포인트를 만듭니다. 자세한 내용은 [Virtual Machine에 엔드포인트를 설정하는 방법](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

Hyper-V에 친숙한 경우 VMConnect와 유사한 도구를 찾을 수 있습니다. 가상 머신에 대한 콘솔 액세스가 지원되지 않으므로 Azure는 유사한 도구를 제공하지 않습니다.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>데이터를 저장하는 데 임시 디스크(Windows의 경우 D: 드라이브 또는 Linux의 경우 /dev/sdb1)를 사용할 수 있나요?
데이터를 저장하는 데 임시 디스크(기본적으로 Windows의 경우 D: 드라이브 또는 Linux의 경우 /dev/sdb1)를 사용할 수 없습니다. 해당 드라이브는 임시 저장소일 뿐이므로 복구할 수 없는 데이터가 손실될 위험이 있습니다. 이는 가상 머신을 다른 호스트로 이동할 때 발생할 수 있습니다. 가상 머신 크기를 조정하고, 호스트를 업데이트 하거나, 호스트의 하드웨어가 실패하는 경우가, 가상 머신이 이동할 수 있는 몇 가지 이유가 됩니다.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>임시 디스크의 드라이브 문자 변경을 어떻게 합니까?
Windows 가상 머신에서, 페이지 파일을 이동하고 드라이브 문자를 다시 할당하여 드라이브 문자를 변경할 수 있지만, 특정 순서에 따라 단계를 수행하고 있는지 확인해야 합니다. 지침에 대한 자세한 내용은 [Windows 임시 디스크의 드라이브 문자 변경](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>게스트 운영 체제를 업그레이드 하려면 어떻게 해야합니까?
용어 업그레이드가 일반적으로 의미하는 것은 동일한 하드웨어를 그대로 사용하면서 사용중인 운영 체제의 최신 릴리스로 이동하는 것을 의미합니다. Azure VM의 경우, 보다 최신 릴리스로의 이동은 Linux 및 Windows에 따라 달라집니다.

* Linux에 대해 배포시 패키지 관리 도구 및 적절한 도구를 사용하세요.
* Windows 가상 컴퓨터의 경우 Windows Server 마이그레이션 도구 등을 사용하여 서버를 마이그레이션해야 합니다. Azure에 상주하는 동안, 게스트 OS를 업그레이드 하려고 시도하지 마십시오. 가상 머신에 대한 액세스를 손실할 수 있는 위험 때문에 해당 업그레이드는 지원되지 않습니다. 업그레이드를 하는 동안 문제가 발생하는 경우, 원격 데스크톱 세션을 시작할 수 있는 기능이 손실되며, 문제를 해결할 수 없습니다.

Windows Server 마이그레이션을 위한 도구 및 프로세스에 대한 자세한 일반 정보는 [Windows Server에 역할 및 기능 마이그레이션](https://go.microsoft.com/fwlink/p/?LinkId=396940)을 참조하세요.

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>가상 머신에 대한 기본 사용자 이름 및 암호는 무엇입니까?
Azure에 의해 제공되는 이미지는 미리 구성된 이름 및 암호가 필요없습니다. 이러한 이미지 중 하나를 사용하여 가상 머신을 만들 때, 가상 머신에 로그온하는 데 사용할 사용자 이름 및 암호를 제공해야 합니다.

사용자 이름 또는 암호를 잊어버리고 VM 에이전트를 설치한 경우 해당 문제를 수정하기 위해 [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 확장을 설치하고 사용할 수 있습니다.

추가 정보:

* Linux 이미지에 대해 Azure Portal을 사용하는 경우 ‘azureuser’가 기본 사용자 이름으로 부여되며 가상 머신을 만들기 위한 방법으로 ‘빨리 만들기’ 대신 ‘갤러리에서’를 사용하여 이 이름을 변경할 수 있습니다. ‘갤러리에서'를 사용하면 로그인을 위해 암호, SSH 키를 사용할 것인지 둘 다 사용할 것인지를 결정할 수 있습니다. 사용자 계정은 권한이 있는 명령을 실행하기 위한 ‘sudo’ 액세스를 할 수 있는 권한이 없는 사용자입니다. ‘root’ 계정은 비활성화 됩니다.
* Windows 이미지에 대해, VM를 만들 때 사용자 이름 및 암호를 제공해야 합니다. 계정은 Administrators 그룹에 추가됩니다.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>Azure가 내 가상 머신에서 바이러스 백신을 실행할 수 있습니까?
Azure는 바이러스 백신 솔루션에 대한 몇 가지 옵션을 제공하지만 관리하는 업무는 사용자의 책임입니다. 예를 들면, 맬웨어 방지 소프트웨어에 대한 별도 구독이 필요할 수 있으며, 검사를 실행하고 업데이트를 설치 시기를 결정해야 합니다. Windows 가상 컴퓨터를 만들 때 또는 나중에 Microsoft 맬웨어 방지 프로그램, Symantec Endpoint Protection 또는 TrendMicro Deep Security Agent에 대한 VM 확장으로 백신 프로그램 지원을 추가할 수 있습니다. Symantec 및 TrendMicro 확장으로 무료 기간 한정 구독 또는 기존 엔터프라이즈 구독을 사용할 수 있습니다. Microsoft 맬웨어 방지 프로그램은 무료입니다. 자세한 내용은 다음을 참조하세요.

* [Azure VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법](https://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Azure VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법](https://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Azure Virtual Machines에 맬웨어 방지 솔루션 배포](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>백업 및 복구에 대한 나의 옵션은 무엇입니까?
Azure Backup은 특정 지역에서 미리 보기로 제공됩니다. 자세한 내용은 [Azure 가상 머신 백업](../articles/backup/backup-azure-arm-vms.md)을 참조하세요. 다른 솔루션은 인증된 파트너에서 사용할 수 있습니다. 무엇이 현재 사용 가능한지 알아보려면, Azure Marketplace를 검색합니다.

추가 옵션은 Blob Storage의 스냅샷 기능을 사용하는 것입니다. 이렇게 하려면, blob 스넵샷에 의존하는 모든 작업을 하기전에 VM을 종료해야 합니다. 이는 보류중인 데이터 쓰기를 저장하고 파일 시스템을 일관된 상태에 넣습니다.

## <a name="how-does-azure-charge-for-my-vm"></a>내 VM에 대한 Azure 청구를 수행하는 방법
Azure는 VM의 크기와 운영 체제에 기반한 시간당 가격을 청구합니다. 부분 시간의 경우 Azure는 사용 시간(분)에 대해서만 청구합니다. 사전 설치된 특정 소프트웨어를 포함하는 VM 이미지로 VM을 만드는 경우, 추가 시간당 소프트웨어 요금이 적용될 수 있습니다. Azure는 VM의 운영 체제 및 데이터 디스크용 저장소에 대해 별도로 요금을 청구합니다. 임시 디스크 저장소는 무료입니다.

VM 상태가 실행 중이거나 정지되면 요금이 부과되지만, VM 상태가 정지됐을 때(할당이 취소됨)는 요금이 부과되지 않습니다. VM을 정지(할당이 취소됨) 상태로 등록하려면, 다음 중 하나를 수행합니다.

* Azure Portal에서 VM을 종료하거나 삭제합니다.
* Azure Powershell 모듈에서 사용할 수 있는 Stop-Azure cmdlet을 사용합니다.
* 서비스 관리 REST API에서 종료 역할 작업을 사용하고 PostShutDownAction 요소에 대한 StoppedDeallocated를 지정합니다.

자세한 내용은 [Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)을 참조하세요.

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>Azure는 유지관리를 위해 내 VM을 다시 부팅합니까?
Azure는 때때로 Azure 데이터 센터에서 일반, 계획된 유지 관리 업데이트의 일부로서 사용자의 VM을 다시 시작합니다.

Azure가 사용자의 VM에 영향을 주는 심각한 하드웨어 문제를 감지할 때 계획되지 않은 유지 관리 이벤트가 발생할 수 있습니다. 계획되지 않은 이벤트에 대해, Azure는 VM을 정상 호스트에 마이그레이션 하고 해당 VM을 다시 시작합니다.

모든 독립 실행형 VM(VM은 가용성 집합의 일부가 아니라는 의미)에 대해, 업데이트를 하는 동안 VM이 다시 시작될 수 있기 때문에, Azure는 계획된 유지 관리 시행 시기 기준 적어도 한 주 전에 구독의 서비스 관리자에게 e-mail로 알립니다. VM에서 실행 중인 애플리케이션에서 가동 중지 시간이 발생할 수 있습니다.

또한 계획된 유지 관리로 인해 다시 부팅할 때 Azure Portal 또는 Azure PowerShell을 사용하여 다시 부팅 로그를 볼 수 있습니다. 자세한 내용은 [VM 다시 부팅 로그 보기](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/)를 참조하세요.

중복성을 제공하려면 동일한 가용성 집합에 둘 이상의 비슷하게 구성된 VM을 넣습니다. 이렇게 하면 계획된 또는 계획되지 않은 유지 관리를 하는 동안 적어도 하나 이상의 VM을 사용할 수 있습니다. Azure는 이 구성에 대해 특정한 수준의 VM 가용성을 보장합니다. 자세한 내용은 [가상 머신의 가용성 관리](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
[Azure Virtual Machines 정보](../articles/virtual-machines/virtual-machines-linux-about.md)

[Azure CLI로 Linux VM 만들기 및 관리](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure PowerShell 사용 하 여 Windows Vm 만들기 및 관리](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

