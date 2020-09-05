---
title: Azure 가상 머신 기술 자산 만들기
description: Azure Marketplace에 대한 VM(가상 머신) 제품에 대한 기술 자산을 만들고 구성하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 496cd4aeb96ca1849e950331658014d91dc6d6ba
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89143941"
---
# <a name="create-azure-virtual-machine-technical-assets"></a>Azure 가상 머신 기술 자산 만들기

VM (가상 컴퓨터) 이미지를 Azure Marketplace에 게시 하는 경우 Azure 팀은 VM 이미지의 유효성을 검사 하 여 bootability, 보안 및 Azure 호환성을 보장 합니다. 고품질 테스트 중 하나라도 실패 하면 게시는 오류 및 가능한 [수정 단계](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)를 포함 하는 메시지와 함께 실패 합니다.

이 문서에서는 Azure Marketplace에 대한 VM(가상 머신) 제품에 대한 기술 자산을 만들고 구성하는 방법을 설명합니다. VM에는 운영 체제 VHD(가상 하드 디스크)와 선택적인 연결된 데이터 디스크 VHD의 두 가지 구성 요소가 있습니다.

1. **운영 체제 VHD** – 제품과 함께 배포하는 솔루션과 운영 체제가 포함됩니다. VHD를 준비 하는 프로세스는 Linux 기반, Windows 기반 또는 사용자 지정 기반 VM 인지에 따라 달라 집니다.

2. **데이터 디스크 vhd** – VM에 대 한 전용 영구 저장소입니다. 운영 체제 VHD(예: C: 드라이브)를 사용하여 영구 정보를 저장하지 마세요.

VM 이미지는 하나의 운영 체제 디스크와 최대 16개의 데이터 디스크를 포함합니다. 디스크가 비어 있는 경우에도 데이터 디스크당 하나의 VHD를 사용합니다.

> [!NOTE]
> 사용 중인 운영 체제에 상관없이 솔루션에 필요한 최소 개수의 데이터 디스크만 추가합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만, 배포 중 또는 배포 후에 언제든지 디스크를 추가할 수 있습니다.

> [!IMPORTANT]
> 계획의 모든 VM 이미지에는 동일한 수의 데이터 디스크가 있어야 합니다.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다. 엔지니어링 팀은 솔루션 영역 외에도 다음 Microsoft 기술에 대한 지식을 갖추고 있어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking)에 대한 실무 지식
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
- [JSON](https://www.json.org/)에 대한 실무 지식

### <a name="optional-suggested-tools"></a>선택적 제안 도구

VM 및 VHD 관리를 위해 다음 스크립팅 환경 중 하나를 사용하는 것이 좋습니다.

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

또한 개발 환경에 다음 도구를 추가하는 것이 좋습니다.

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>승인된 기본 이미지를 사용하여 VM 이미지 만들기

사용자 고유의 프레미스에서 빌드된 이미지를 사용 하 여 가상 머신 기술 자산을 만들려면 아래의 [승인 된 기본을 사용 하 여 VM 이미지 만들기](#create-a-vm-image-using-an-approved-base) 를 참조 하세요.

이 섹션에서는 RDP(원격 데스크톱 프로토콜) 사용, VM 크기 선택, 최신 Windows 업데이트 설치 및 VHD 이미지 일반화와 같이 승인된 기본 이미지 사용 방법을 다양한 측면으로 설명합니다.

이 문서의 지침에 따라 Azure를 사용 하 여 미리 구성 된 보증 운영 체제를 포함 하는 VM을 만듭니다. 솔루션과 호환되지 않는 경우 승인된 운영 체제를 사용하여 온-프레미스 VM을 만들고 구성할 수 있습니다. 그런 다음, [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)에서 설명한 대로 업로드할 수 있도록 구성하고 준비할 수 있습니다.

### <a name="select-an-approved-base-image"></a>승인 된 기본 이미지 선택

Windows 운영 체제 또는 Linux를 기본으로 선택합니다.

VM 이미지용 운영 체제 VHD는 Windows Server 또는 SQL Server를 포함하는 Azure 승인 기본 이미지를 기반으로 해야 합니다.

업데이트를 사용 하 여 이미지를 다시 게시 하는 요청을 제출 하면 파트 번호 확인 테스트 사례가 실패할 수 있습니다. 해당 인스턴스에서 이미지는 승인 되지 않습니다.

이 오류는 다른 게시자에 속하는 기본 이미지를 사용 하 고 이미지를 업데이트 한 경우에 발생 합니다. 이 경우 이미지를 게시할 수 없습니다.

이 문제를 해결 하려면 Azure Marketplace에서 최신 이미지를 검색 하 고 해당 이미지를 변경 합니다. 이미지를 검색할 수 있는 승인 된 기본 이미지를 보려면 다음을 참조 하세요.

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Azure에서 승인된 Linux 배포판을 다양하게 제공합니다. 현재 목록은 [Azure 보증 배포판의 Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)를 참조하세요.

### <a name="create-vm-on-the-azure-portal"></a>Azure Portal에서 VM 만들기

[Azure Portal](https://ms.portal.azure.com/)에서 기본 VM 이미지를 만들려면 다음 단계를 수행 합니다.

1. VM 제품을 게시하는 데 사용하려는 Azure 구독과 연결된 Microsoft 계정으로 [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
2. 새 리소스 그룹을 만들고, **리소스 그룹 이름**, **구독** 및 **리소스 그룹 위치**를 제공합니다. 자세한 내용은 [리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)를 참조하세요.

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="리소스 그룹 만들기를 시작 하는 방법을 보여 줍니다.":::

3. 왼쪽 탐색 창에서 **virtual machines** 를 선택 하 여 가상 머신 세부 정보 페이지를 표시 합니다.
4. **+ 추가**를 선택하여 **가상 머신 환경 만들기**를 엽니다.
5. 드롭다운 목록에서 이미지를 선택 하거나 **모든 공용 및 개인 이미지 찾아보기** 를 선택 하 여 사용 가능한 모든 가상 머신 이미지를 검색 하거나 검색 합니다. 예제:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="샘플 VM 이미지를 표시 합니다.":::

6. 다음 권장 사항을 사용하여 배포할 VM의 크기를 선택합니다.
    1. 온-프레미스에서 VHD를 개발하려는 경우 크기는 중요하지 않습니다. 더 작은 VM 중 하나를 사용하는 것이 좋습니다.
    2. Azure에서 이미지를 개발하려는 경우 선택된 이미지에 대한 권장 VM 크기 중 하나를 사용하는 것이 좋습니다.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="VM 크기의 선택 항목을 표시 합니다.":::

7. **디스크** 섹션에서 **고급** 섹션을 확장하고 **관리 디스크 사용** 옵션을 **아니요**로 설정합니다.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="관리 디스크를 사용 하는 옵션을 표시 합니다.":::

8. VM을 만드는 데 필요한 다른 세부 정보를 입력합니다.
9. **검토 + 만들기**를 선택하여 선택 사항을 검토합니다. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

Azure에서 지정한 가상 머신의 프로비저닝을 시작합니다. 왼쪽의 **가상 머신** 탭을 선택하여 진행 상황을 추적할 수 있습니다. VM이 만들어지면 상태가 **실행 중**으로 변경됩니다.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Azure Portal에서 2 세대 VM 만들기

Azure Portal에서 2 세대 (Gen2) VM을 만듭니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.
2. **리소스 만들기**를 선택합니다.
3. 왼쪽의 Azure Marketplace에서 **모두 보기** 를 선택 합니다.
4. Gen2를 지 원하는 이미지를 선택 합니다.
5. **만들기**를 선택합니다.
6. **고급** 탭의 **VM 생성** 섹션에서 **Gen 2** 옵션을 선택합니다.
7. **기본 사항** 탭의 **인스턴스 세부 정보**에서 **크기**로 이동하여 **VM 크기 선택** 블레이드를 엽니다.
8. [지원 되는 Gen 2 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) 및 크기의 권장 크기를 선택 합니다.
9. [Azure Portal 만들기 흐름](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)을 진행하여 VM 만들기를 완료합니다.

    :::image type="content" source="media/vm/vm-generation.png" alt-text="VM 생성을 선택 하는 옵션을 보여 줍니다.":::

## <a name="connect-to-your-azure-vm"></a>Azure VM에 연결

이 섹션에서는 Azure에서 만든 VM에 연결 하 고 로그인 하는 방법을 설명 합니다. 성공적으로 연결되면 호스트 서버에 로컬로 로그온한 것처럼 VM을 사용하여 작업할 수 있습니다.

### <a name="connect-to-a-windows-based-vm"></a>Windows 기반 VM에 연결

원격 데스크톱 클라이언트를 사용하여 Azure에서 호스팅되는 Windows 기반 VM에 연결합니다. 대부분의 Windows 버전에는 기본적으로 RDP(원격 데스크톱 프로토콜)에 대한 지원이 포함되어 있습니다. 다른 운영 체제의 경우 [원격 데스크톱 클라이언트](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)에서 클라이언트에 대한 자세한 정보를 찾을 수 있습니다.

이 문서에서는 기본 제공 Windows RDP를 사용 하 여 VM에 연결 하는 방법: Windows를 실행 하는 [Azure 가상 머신에 연결 하 고](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)로그온 하는 방법에 대해 자세히 설명 합니다.

> [!TIP]
> 프로세스 중에 보안 경고가 표시될 수 있습니다. 예를 들어 "알 수 없는 게시자의 .rdp 파일입니다" 또는 "사용자 자격 증명을 확인할 수 없습니다"와 같은 경고가 표시됩니다. 이러한 경고는 무시해도 됩니다.

### <a name="connect-to-a-linux-based-vm"></a>Linux 기반 VM에 연결

Linux 기반 VM을 연결하려면 SSH(Secure Shell 프로토콜) 클라이언트가 필요합니다. 다음 단계에서는 무료 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 터미널을 사용합니다.

1. [Azure 포털](https://ms.portal.azure.com/)로 이동합니다.
2. 가상 머신을 검색하여 선택합니다.
3. 연결하려는 VM을 선택합니다.
4. VM이 아직 실행되지 않은 경우 VM을 시작합니다.
5. VM의 이름을 선택하여 개요 페이지를 엽니다.
6. VM의 공용 IP 주소 및 DNS 이름을 적어둡니다 (이러한 값이 설정 되지 않은 경우 [네트워크 인터페이스를 만들어야](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)함).
7. PuTTY 애플리케이션을 엽니다.
8. [PuTTY 구성] 대화 상자에서 VM의 IP 주소 또는 DNS 이름을 입력합니다.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="호스트 이름 및 포트 필드를 강조 표시 하는 PuTTY 터미널 설정을 보여 줍니다.":::

9. **열기**를 선택하여 PuTTY 터미널을 엽니다.
10. 메시지가 표시되면 Linux VM 계정의 계정 이름과 암호를 입력합니다.

## <a name="configure-the-virtual-machine"></a>가상 머신 구성

이 섹션에서는 Azure VM의 크기를 조정하고, 업데이트하고, 일반화하는 방법을 설명합니다. 이러한 단계는 Azure Marketplace에서 배포할 VM을 준비하는 데 필요합니다.

### <a name="sizing-the-vhds"></a>VHD 크기 조정

다음 규칙은 OS 디스크 크기에 대 한 제한 사항입니다. 요청을 제출할 때 OS 디스크 크기가 Linux 또는 Windows에 대 한 제한 범위 내에 있는지 확인 합니다.

| OS | 권장 VHD 크기 |
| --- | --- |
| Linux | 30-1023 GB |
| Windows | 30-250 GB |

Vm에서 기본 운영 체제에 대 한 액세스를 허용 하므로 vhd 크기가 VHD에 대해 충분히 큰지 확인 합니다. 디스크가 가동 중지 시간 없이 확장 되지 않기 때문에 30에서 50 GB 사이의 디스크 크기를 사용 &nbsp; 합니다.

| VHD 크기 | 실제 차지 크기 | 솔루션 |
| --- | --- | --- |
| >500 TB | 해당 없음 | 예외 승인에 대해서는 지원 팀에 문의 하세요. |
| 250-500 TB | >200 GB는 blob 크기와 다릅니다. | 예외 승인에 대해서는 지원 팀에 문의 하세요. |

### <a name="install-the-most-current-updates"></a>최신 업데이트 설치

운영 체제 VM의 기본 이미지에는 게시한 날짜까지의 최신 업데이트가 포함되어야 합니다. 만든 운영 체제 VHD를 게시하기 전에 OS 및 설치된 모든 서비스를 모든 최신 보안 및 유지 관리 패치로 업데이트해야 합니다.

- Windows Server의 경우 업데이트 확인 명령을 실행합니다.
- Linux 배포판의 경우 일반적으로 명령줄 도구 또는 그래픽 유틸리티를 통해 업데이트를 다운로드하고 설치합니다. 예를 들어 Ubuntu Linux는 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 명령과 [업데이트 관리자](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) 도구를 제공하여 OS를 업데이트합니다.

#### <a name="perform-additional-security-checks"></a>추가 보안 검사 수행

Azure Marketplace에서 솔루션 이미지에 대한 높은 수준의 보안을 유지해야 합니다. 보안 구성 및 절차에 대 한 검사 목록은 [Azure Marketplace 이미지에 대 한 보안 권장 사항](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)을 참조 하세요. 이러한 권장 사항 중 일부는 Linux 기반 이미지에만 적용되지만, 대부분은 모든 VM 이미지에 적용됩니다.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>사용자 지정 구성 및 예약된 작업 수행

추가 구성이 필요한 경우 시작 시 실행 되는 예약 된 작업을 사용 하 여 VM을 배포한 후 VM에 대 한 최종 변경을 수행 합니다. 또한 다음 사항도 고려합니다.

- 일회성 작업인 경우 작업이 성공적으로 완료된 후 이 작업을 삭제해야 합니다.
- 이러한 두 드라이브만 항상 존재하는 것이 보장되므로 구성이 C 또는 D 이외의 드라이브에 따라 달라져서는 안 됩니다(C 드라이브는 운영 체제 디스크이고 D 드라이브는 임시 로컬 디스크입니다).

Linux 사용자 지정에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)을 참조하세요.

## <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 이를 달성하려면 운영 체제 VHD가 일반화되어야 합니다. 이 작업은 VM에서 인스턴스 관련 식별자와 소프트웨어 드라이버를 모두 제거합니다.

### <a name="for-windows"></a>Windows의 경우

Windows OS 디스크는 [sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 도구를 사용 하 여 일반화 됩니다. 나중에 OS를 업데이트 하거나 다시 구성 하는 경우 sysprep을 다시 실행 해야 합니다.

> [!WARNING]
> Sysprep을 실행 한 후 업데이트가 자동으로 실행 될 수 있기 때문에 배포 될 때까지 VM을 해제 합니다. 이렇게 종료하면 이후 업데이트에서 운영 체제 또는 설치된 서비스에 대한 인스턴스 관련 변경이 수행되지 않습니다. sysprep 실행에 대한 자세한 내용은 [VHD를 일반화하는 단계](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)를 참조하세요.

### <a name="for-linux"></a>Linux의 경우

다음 프로세스에서 Linux VM을 일반화하여 별도의 VM으로 다시 배포합니다. 자세한 내용은 [가상 머신 또는 VHD의 이미지를 만드는 방법](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)을 참조하세요. "캡처된 이미지에서 VM 만들기" 라는 섹션에 도달 하면를 중지할 수 있습니다.

1. Azure Linux 에이전트 제거

    1. SSH 클라이언트를 사용 하 여 Linux VM에 연결
    2. SSH 창에서 다음 명령을 입력합니다. `sudo waagent –deprovision+user`
    3. Y를 입력하여 계속합니다(확인 단계를 방지하려면 이전 명령에 -force 매개 변수를 추가할 수 있음).
    4. 명령이 완료되면 Exit를 입력하여 SSH 클라이언트를 닫습니다.

2. 가상 머신 중지

    1. Azure Portal에서 RG(리소스 그룹)를 선택하고, VM을 할당 취소합니다.
    2. 이제 VHD가 일반화되어 이 VHD를 사용하여 새 VM을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우 [일반적인 VHD 만들기 문제](common-issues-during-vhd-creation.md)를 참조하세요.
- 그렇지 않은 경우 [VHD에서 배포 된 테스트 vm (가상 컴퓨터)](azure-vm-image-certification.md) 은 Azure 인증 도구에 대 한 인증 테스트 도구를 가져올 위치와 vm 이미지를 인증 하는 데 사용 하는 방법을 비롯 하 여 Azure Marketplace 인증을 위해 vm 이미지를 테스트 하 고 제출 하는 방법을 설명 합니다.
