---
title: Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure VM에 대해 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure 지역 간에 재해 복구를 설정하기 위해 컴퓨터를 준비하는 방법을 설명합니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 019c6ec776277a9102cb95cd685bbae0fc660d66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789691"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Azure로 마이그레이션한 후 Azure VM에 대해 재해 복구 설정 


설정한 경우이 문서를 따릅니다 [Azure Vm에 온-프레미스 컴퓨터를 마이그레이션할](tutorial-migrate-on-premises-to-azure.md) 사용 하 여를 [Site Recovery](site-recovery-overview.md) 서비스, 이제 가져오려는 Vm 재해 복구용 보조 Azure 지역으로 설정 합니다. 문서에는 마이그레이션된 Vm에서 Azure VM 에이전트가 설치 되어 있는지 확인 하는 방법 및 마이그레이션 후 더 이상 필요에 Site Recovery 모바일 서비스를 제거 하는 방법을 설명 합니다.



## <a name="verify-migration"></a>마이그레이션 확인

재해 복구를 설정하기 전에 마이그레이션이 예상대로 완료되었는지 확인합니다. 마이그레이션을 성공적으로 완료하려면 마이그레이션할 각 컴퓨터에 대해 **마이그레이션 완료** 옵션을 선택해야 합니다. 

## <a name="verify-the-azure-vm-agent"></a>Azure VM 에이전트를 확인 합니다.

각 Azure VM에 있어야 합니다 [Azure VM 에이전트](../virtual-machines/extensions/agent-windows.md) 설치 합니다. Azure Vm 복제를 Site Recovery 에이전트에서 확장을 설치 합니다.

- 컴퓨터는 9.7.0.0 버전을 실행 중인 경우 또는 나중에 Site Recovery 모바일 서비스의 Azure VM 에이전트는 Windows Vm에 모바일 서비스에서 자동으로 설치 됩니다. 모바일 서비스의 이전 버전에서 에이전트를 자동으로 설치 해야 합니다.
- Linux Vm에 대 한 Azure VM 에이전트를 수동으로 설치 해야 합니다. 마이그레이션된 컴퓨터에 설치 된 모바일 서비스가 v9.6 경우 Azure VM 에이전트를 설치 해야 또는 이전 버전입니다.


### <a name="install-the-agent-on-windows-vms"></a>Windows Vm 에이전트 설치

9.7.0.0 이전의 Site Recovery 모바일 서비스의 버전을 실행 하는 경우 에이전트를 수동으로 설치 하려면 다른 필요한 경우 다음을 수행 합니다.  

1. VM에 대 한 관리자 권한이 있는지 확인 합니다.
2. 다운로드 합니다 [VM 에이전트 설치 관리자](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다.
3. 설치 관리자 파일을 실행 합니다.

#### <a name="validate-the-installation"></a>설치 유효성 검사
에 에이전트가 설치 되어 있는지 확인 합니다.

1. Azure VM의 C:\WindowsAzure\Packages 폴더에 WaAppAgent.exe 파일이 표시되어야 합니다.
2. 파일을 마우스 오른쪽 단추로 클릭하고 **속성**에서 **세부 정보** 탭을 선택합니다.
3. **제품 버전** 필드에 2.6.1198.718 이상이 표시되는지 확인합니다.

[자세한](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) Windows 에이전트 설치에 대 한 합니다.

### <a name="install-the-agent-on-linux-vms"></a>Linux Vm 에이전트 설치

설치 합니다 [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) 같이 수동으로 에이전트:

1. 컴퓨터에 대 한 관리자 권한이 있는지 확인 합니다.
2. 배포 패키지 리포지토리에서 RPM 또는 DEB 패키지를 사용 하 여 Linux VM 에이전트를 설치 하는 것이 좋습니다. 모든 [인증 배포 공급자](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다.
    - 배포 리포지토리를 사용할 때만 에이전트를 업데이트 하는 것이 좋습니다.
    - GitHub에서 직접 Linux VM 에이전트를 설치 하 고 업데이트 권장 하지 않습니다.
    -  최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 

#### <a name="validate-the-installation"></a>설치 유효성 검사 

1. 이 명령을 실행 합니다. **ps-e** Azure 에이전트는 Linux VM에서 실행 되 고 있는지 확인 합니다.
2. 이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.
    - Ubuntu: **walinuxagent 시작 서비스**
    - 다른 배포에 대 한: **waagent 시작 서비스**


## <a name="uninstall-the-mobility-service"></a>모바일 서비스 설치 제거

1. 다음 방법 중 하나를 사용 하 여 Azure VM에서 모바일 서비스를 수동으로 제거 합니다. 
    - Windows의 경우 제어판 > **프로그램 추가/제거**에서 **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**를 제거합니다. 관리자 권한의 명령 프롬프트에서 다음을 실행합니다.
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux의 경우 루트 사용자로 로그인합니다. 터미널에서 **/user/local/ASR**로 이동하여 다음 명령을 실행합니다.
        ```
        ./uninstall.sh -Y
        ```
2. 복제를 구성하기 전에 VM을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

[검토 문제 해결](site-recovery-extension-troubleshoot.md) Azure VM 에이전트에서 Site Recovery 확장에 대 한 합니다.
Azure VM을 보조 지역에 [신속하게 복제](azure-to-azure-quickstart.md)합니다.
